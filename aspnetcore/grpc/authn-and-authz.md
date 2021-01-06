---
title: ASP.NET Core용 gRPC의 인증 및 권한 부여
author: jamesnk
description: ASP.NET Core용 gRPC에서 인증 및 권한 부여를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94673980"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>ASP.NET Core용 gRPC의 인증 및 권한 부여

작성자: [James Newton-King](https://twitter.com/jamesnk)

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>gRPC 서비스를 호출하는 사용자 인증

[ASP.NET Core 인증](xref:security/authentication/identity)과 함께 gRPC를 사용하여 각 호출과 사용자를 연결할 수 있습니다.

다음은 gRPC와 ASP.NET Core 인증을 사용하는 `Startup.Configure`의 예입니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> ASP.NET Core 인증 미들웨어를 등록하는 순서가 중요합니다. `UseAuthentication`과 `UseAuthorization`은 항상 `UseRouting` 이후, `UseEndpoints` 이전에 호출합니다.

호출 중에 앱에서 사용하는 인증 메커니즘을 구성해야 합니다. 인증 구성은 `Startup.ConfigureServices`에 추가되며, 앱에서 사용하는 인증 메커니즘에 따라 다릅니다. ASP.NET Core 앱을 보호하는 방법의 예는 [인증 샘플](xref:security/authentication/samples)을 참조하세요.

인증이 설정되면, gRPC 서비스 메서드에서 `ServerCallContext`를 통해 사용자에 액세스할 수 있습니다.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>전달자 토큰 인증

클라이언트는 인증을 위해 액세스 토큰을 제공할 수 있습니다. 서버는 토큰의 유효성을 검사하고 사용자를 확인하는 데 사용합니다.

서버에서 [JWT 전달자 미들웨어](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)를 사용하여 전달자 토큰 인증을 구성합니다.

.NET gRPC 클라이언트에서 `Metadata` 컬렉션을 사용하여 호출과 함께 토큰을 보낼 수 있습니다. `Metadata` 컬렉션의 항목은 gRPC 호출을 통해 HTTP 헤더로 전송됩니다.

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

gRPC 호출과 함께 토큰을 서비스로 보내는 대신, 채널에 `ChannelCredentials`를 구성할 수도 있습니다. `ChannelCredentials`는 `Metadata`를 자동으로 설정하는 방법을 제공하는 `CallCredentials`를 포함할 수 있습니다.

gRPC 호출을 수행할 때마다 `CallCredentials`가 실행되므로, 토큰을 직접 전달하기 위해 여러 위치에 코드를 작성하지 않아도 됩니다. `CallCredentials`는 채널이 TLS로 보호되는 경우에만 적용됩니다. `CallCredentials`는 보안이 설정되지 않은 비 TLS 채널에는 적용되지 않습니다.

다음 예제의 자격 증명은 모든 gRPC 호출과 함께 토큰을 보내도록 채널을 구성합니다.

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

클라이언트에서 인증을 위해 클라이언트 인증서를 제공할 수도 있습니다. [인증서 인증](https://tools.ietf.org/html/rfc5246#section-7.4.4)은 ASP.NET Core에 도달하기 훨씬 전에 TLS 수준에서 수행됩니다. 요청이 ASP.NET Core에 도달하면 [클라이언트 인증서 인증 패키지](xref:security/authentication/certauth)를 사용하여 인증서를 `ClaimsPrincipal`로 확인할 수 있습니다.

> [!NOTE]
> 클라이언트 인증서를 허용하도록 서버를 구성합니다. Kestrel, IIS 및 Azure에서 클라이언트 인증서를 허용하는 방법에 대한 내용은 <xref:security/authentication/certauth#configure-your-server-to-require-certificates>를 참조하세요.

.NET gRPC 클라이언트에서 클라이언트 인증서는 `HttpClientHandler`에 추가되며, gRPC 클라이언트를 만드는 데 사용됩니다.

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>기타 인증 메커니즘

ASP.NET Core를 지원하는 많은 인증 메커니즘은 gRPC에서 작동합니다.

* Azure Active Directory
* 클라이언트 인증서
* Identity서버
* JWT 토큰
* OAuth 2.0
* OpenID Connect
* WS-Federation

서버에서 인증을 구성하는 방법에 대한 자세한 내용은 [ASP.NET Core 인증](xref:security/authentication/identity)을 참조하세요.

인증을 사용하도록 gRPC 클라이언트를 구성하는 방법은 사용하는 인증 메커니즘에 따라 다릅니다. 이전의 전달자 토큰 및 클라이언트 인증서 예제는 gRPC 호출과 함께 인증 메타데이터를 보내도록 gRPC 클라이언트를 구성할 수 있는 몇 가지 방법을 보여 줍니다.

* 강력한 형식의 gRPC 클라이언트는 `HttpClient`를 내부적으로 사용합니다. [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)에 인증을 구성하거나, 사용자 지정 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) 인스턴스를 `HttpClient`에 추가하여 구성할 수 있습니다.
* 각 gRPC 호출에는 선택 사항인 `CallOptions` 인수가 있습니다. 옵션의 헤더 컬렉션을 사용하여 사용자 지정 헤더를 보낼 수 있습니다.

> [!NOTE]
> Windows 인증(NTLM/Kerberos/Negotiate)은 gRPC와 함께 사용할 수 없습니다. gRPC에는 HTTP/2가 필요한데, HTTP/2는 Windows 인증을 지원하지 않습니다.

## <a name="authorize-users-to-access-services-and-service-methods"></a>사용자에게 서비스 및 서비스 메서드에 대한 액세스 권한 부여

기본적으로 인증되지 않은 사용자도 서비스의 모든 메서드를 호출할 수 있습니다. 인증을 요구하려면 서비스에 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 적용합니다.

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

`[Authorize]` 특성의 생성자 인수와 속성을 사용하여 특정 [권한 부여 정책](xref:security/authorization/policies)과 일치하는 사용자로만 액세스를 제한할 수 있습니다. 예를 들어 `MyAuthorizationPolicy`라는 사용자 지정 권한 부여 정책이 있는 경우 다음 코드를 사용하여 해당 정책과 일치하는 사용자만 서비스에 액세스할 수 있도록 합니다.

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

개별 서비스 메서드에도 `[Authorize]` 특성을 적용할 수 있습니다. 현재 사용자가 메서드 및 클래스에 적용된 정책과 **모두** 일치하지 않는 경우 호출자에게 오류가 반환됩니다.

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core의 전달자 토큰 인증](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [ASP.NET Core에서 클라이언트 인증서 인증 구성](xref:security/authentication/certauth)
