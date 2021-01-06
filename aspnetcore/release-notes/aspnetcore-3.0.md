---
title: ASP.NET Core 3.0의 새로운 기능
author: rick-anderson
description: ASP.NET Core 3.0의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 235daac5c08248ca2052de6b44e66a8162ce23ad
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051241"
---
# <a name="whats-new-in-aspnet-core-30"></a>ASP.NET Core 3.0의 새로운 기능

이 문서에서는 ASP.NET Core 3.0의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## Blazor

Blazor는 .NET을 사용하여 대화형 클라이언트 쪽 웹 UI를 빌드하기 위한 ASP.NET Core 내의 새로운 프레임워크입니다.

* JavaScript 대신 C#을 사용하여 풍부한 대화형 UI를 만듭니다.
* .NET에서 작성된 서버 쪽 및 클라이언트 쪽 앱 논리를 공유합니다.
* 모바일 브라우저를 포함한 광범위한 브라우저 지원을 위해 UI를 HTML 및 CSS로 렌더링합니다.

Blazor 프레임워크 지원 시나리오:

* 재사용 가능한 UI 구성 요소(Razor 구성 요소)
* 클라이언트 쪽 라우팅
* 구성 요소 레이아웃
* 종속성 주입을 위한 지원
* 양식 및 유효성 검사
* Razor 클래스 라이브러리를 사용하여 구성 요소 라이브러리 빌드
* JavaScript interop

자세한 내용은 <xref:blazor/index>를 참조하세요.

### Blazor Server

Blazor는 UI 업데이트 적용 방법에서 구성 요소 렌더링 논리를 분리합니다. Blazor Server에서는 ASP.NET Core 앱의 서버에서 Razor 구성 요소를 호스팅할 수 있도록 지원합니다. UI 업데이트는 SignalR 연결을 통해 처리됩니다. Blazor Server는 ASP.NET Core 3.0에서 지원됩니다.

### <a name="no-locblazor-webassembly-preview"></a>Blazor WebAssembly(미리 보기)

Blazor 앱은 WebAssembly 기반 .NET 런타임을 사용하여 브라우저에서 직접 실행할 수도 있습니다. Blazor WebAssembly는 미리 보기로 제공되며, ASP.NET Core 3.0에서 지원되지 ‘않습니다’. Blazor WebAssembly는 ASP.NET Core의 이후 릴리스에서 지원될 예정입니다.

### <a name="no-locrazor-components"></a>Razor 구성 요소

Blazor 앱은 구성 요소에서 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 UI 렌더링 논리 및 클라이언트 쪽 이벤트 처리기를 정의하는 일반적인 .NET 클래스입니다. JavaScript 없이 풍부한 대화형 웹앱을 만들 수 있습니다.

Blazor의 구성 요소는 일반적으로 HTML과 C#의 자연 혼합인 Razor 구문을 사용하여 작성됩니다. Razor 구성 요소는 둘 다 Razor를 사용한다는 점에서 Razor Pages 및 MVC 뷰와 비슷합니다. 요청-응답 모델 기반의 페이지와 보기와는 달리, 구성 요소는 특별히 UI 컴퍼지션을 처리하는 데 사용됩니다.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* 인기 있는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다.
* API 개발에 독자적인 계약 중심 접근 방식을 제공합니다.
* 다음과 같은 최신 기술을 사용합니다.

  * 전송용 HTTP/2
  * 인터페이스 설명 언어인 프로토콜 버퍼
  * 이진 직렬화 형식
* 다음과 같은 기능을 제공합니다.

  * 인증
  * 양방향 스트리밍 및 흐름 제어
  * 취소 및 시간 제한

ASP.NET Core 3.0의 gRPC 기능에는 다음이 포함됩니다.

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): gRPC 서비스 호스팅을 위한 ASP.NET Core 프레임워크입니다. ASP.NET의 gRPC는 로깅, DI(종속성 주입), 인증 및 권한 부여와 같은 표준 ASP.NET Core 기능과 통합됩니다.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): 친숙한 `HttpClient`를 기반으로 하는 .NET Core용 gRPC 클라이언트입니다.
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC 클라이언트와 `HttpClientFactory`를 통합합니다.

자세한 내용은 <xref:grpc/index>를 참조하세요.

## SignalR

마이그레이션 지침은 [SignalR 코드 업데이트](xref:migration/22-to-30#signalr)를 참조하세요. 이제 SignalR이 `System.Text.Json`을 사용하여 JSON 메시지를 직렬화 및 역직렬화합니다. `Newtonsoft.Json` 기반 직렬 변환기를 복원하려면 [Newtonsoft.Json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)에서 지침을 참조하세요.

SignalR용 JavaScript 및 .NET Clients에서 자동 다시 연결을 위해 지원이 추가되었습니다. 기본적으로 클라이언트는 즉시 다시 연결을 시도하고 필요한 경우 2, 10, 30초 후에 다시 시도합니다. 클라이언트는 다시 연결되면 새로운 연결 ID를 수신합니다. 자동 다시 연결 옵트인(opt-in:):

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

밀리초 기반 기간의 배열을 전달하여 다시 연결 간격을 지정할 수 있습니다.

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

사용자 지정 구현을 전달하여 다시 연결 간격을 완전히 컨트롤할 수 있습니다.

마지막 다시 연결 간격 후 다시 연결이 실패하는 경우:

* 클라이언트는 연결이 오프라인 상태인 것으로 간주합니다.
* 클라이언트에서 다시 연결 시도를 중지합니다.

다시 연결을 시도하는 동안 다시 연결을 시도하고 있음을 사용자에게 알리기 위해 앱 UI를 업데이트합니다.

연결이 중단될 때 UI 피드백을 제공하기 위해 SignalR 클라이언트 API는 다음과 같은 이벤트 처리기를 포함하도록 확장되었습니다.

* `onreconnecting`:  개발자에게 UI를 사용하지 않도록 설정하거나 사용자에게 해당 앱이 오프라인 상태임을 알릴 수 있는 기회를 제공합니다.
* `onreconnected`: 개발자에게 연결이 다시 구축되면 UI를 업데이트할 수 있는 기회를 제공합니다.

다음 코드는 `onreconnecting`을 사용하여 연결을 시도하는 동안 UI를 업데이트합니다.

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

다음 코드는 `onreconnected`를 사용하여 연결 시 UI를 업데이트합니다.

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3.0 이상에서는 허브 메서드에 권한 부여가 필요할 때 권한 부여 처리기에 사용자 지정 리소스를 제공합니다. 리소스가 `HubInvocationContext`의 인스턴스인 경우 `HubInvocationContext`는 다음을 포함합니다.

* `HubCallerContext`
* 호출되는 허브 메서드의 이름
* 허브 메서드에 대한 인수

여러 조직에서 Azure Active Directory를 통해 로그인할 수 있도록 지원하는 대화방 앱의 다음 예를 살펴보세요. Microsoft 계정을 가진 사용자는 채팅에 로그인할 수 있지만 소유 조직의 멤버만 사용자를 금지하거나 사용자의 채팅 기록을 볼 수 있습니다. 앱이 특정 사용자의 특정 기능을 제한할 수 있습니다.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

위의 코드에서 `DomainRestrictedRequirement`는 사용자 지정 `IAuthorizationRequirement` 역할을 합니다. `HubInvocationContext` 리소스 매개 변수가 전달되기 때문에 내부 로직은 다음이 가능합니다.

* 허브가 호출되는 컨텍스트를 검사할 수 있습니다.
* 사용자가 개별 허브 메서드를 실행할 수 있도록 하는 결정을 내립니다.

개별 허브 메서드는 런타임에 코드가 확인하는 정책의 이름을 사용하여 표시할 수 있습니다. 클라이언트에서 개별 허브 메서드를 호출하려고 하면 `DomainRestrictedRequirement` 처리기가 실행되고 메서드에 대한 액세스를 컨트롤합니다. `DomainRestrictedRequirement`가 액세스를 컨트롤하는 방식에 따라 다음이 가능합니다.

* 로그인된 모든 사용자는 `SendMessage` 메서드를 호출할 수 있습니다.
* `@jabbr.net` 전자 메일 주소로 로그인한 사용자들만 사용자의 기록을 볼 수 있습니다.
* `bob42@jabbr.net`만 사용자들을 대화방에서 차단할 수 있습니다.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

`DomainRestricted` 정책을 생성하려면 다음이 필요할 수 있습니다.

* *Startup.cs* 에서 새 정책 추가
* 사용자 지정 `DomainRestrictedRequirement` 요구 사항을 매개 변수로 제공합니다.
* 권한 부여 미들웨어로 `DomainRestricted` 등록

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR 허브는 [엔드포인트 라우팅](xref:fundamentals/routing)을 사용합니다. SignalR 허브 연결은 이전에 명시적으로 수행되었습니다.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

이전 버전에서는 개발자가 다양한 위치에서 컨트롤러, Razor Pages 및 허브를 연결해야 했습니다. 명시적 연결로 인해 다음과 같이 거의 동일한 일련의 라우팅 세그먼트가 생성됩니다.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR 3.0 허브는 엔드포인트 라우팅을 통해 라우팅할 수 있습니다. 엔드포인트 라우팅을 사용하면 일반적으로 모든 라우팅을 `UseRouting`에서 구성할 수 있습니다.

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR에는 다음 기능이 추가되었습니다.

클라이언트-서버 스트리밍 클라이언트-서버 스트리밍을 사용하는 경우 서버 쪽 메서드는 `IAsyncEnumerable<T>` 또는 `ChannelReader<T>`의 인스턴스를 사용할 수 있습니다. 다음 C# 샘플에서 허브에 있는 `UploadStream` 메서드는 다음 클라이언트로부터 문자열 스트림을 받게 됩니다.

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET 클라이언트 앱은 위 `UploadStream` 허브 메서드의 `stream` 인수로 `IAsyncEnumerable<T>` 또는 `ChannelReader<T>` 인스턴스를 전달할 수 있습니다.

`for` 루프가 완료되고 로컬 함수가 종료되면 스트림 완료가 전송됩니다.

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

JavaScript 클라이언트 앱은 위의 `UploadStream` 허브 메서드의 `stream` 인수에 대해 SignalR `Subject`(또는 [RxJS Subject](https://rxjs.dev/api/index/class/Subject))를 사용합니다.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

JavaScript 코드는 `subject.next` 메서드를 사용하여 캡처되고 서버에 보낼 준비가 된 문자열을 처리할 수 있습니다.

```javascript
subject.next("example");
subject.complete();
```

위의 두 코드 조각과 같은 코드를 사용하여 실시간 스트리밍 환경을 만들 수 있습니다.

## <a name="new-json-serialization"></a>새로운 JSON 직렬화

ASP.NET Core 3.0은 이제 JSON 직렬화를 위해 기본적으로 <xref:System.Text.Json>을 사용합니다.

* JSON을 비동기적으로 읽고 씁니다.
* UTF-8 텍스트에 최적화되어 있습니다.
* 일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.

Json.NET을 ASP.NET Core 3.0에 추가하려면 [Newtonsoft.Json 기반 JSON 형식 지원](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)을 참조하세요.

## <a name="new-no-locrazor-directives"></a>새 Razor 지시문

다음 목록에는 새 Razor 지시문이 포함되어 있습니다.

* [`@attribute`](xref:mvc/views/razor#attribute): `@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다. 예: `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements): `@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다. 예: `@implements IDisposable`.

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4의 웹 API 및 SPA에 대한 인증 및 권한 부여 지원

ASP.NET Core 3.0에서는 웹 API 권한 부여에 대한 지원을 사용하여 SPA(단일 페이지 앱)의 인증을 제공합니다. 사용자를 인증하고 저장하기 위한 ASP.NET Core Identity는 OpenID Connect 구현을 위해 [IdentityServer4](https://identityserver.io/)와 통합됩니다.

IdentityServer4는 ASP.NET Core 3.0용 OpenID Connect 및 OAuth 2.0 프레임워크입니다. 다음과 같은 보안 기능을 제공합니다.

* AaaS(Authentication as a Service)
* 여러 응용 프로그램 유형에 대한 SSO(Single Sign-On/Off)
* API에 대한 액세스 제어
* 페더레이션 게이트웨이

자세한 내용은 [IdentityServer4 설명서](http://docs.identityserver.io/en/latest/index.html) 또는 [SPA에 대한 인증 및 권한 부여](xref:security/authentication/identity/spa)를 참조하세요.

## <a name="certificate-and-kerberos-authentication"></a>인증서 및 Kerberos 인증

인증서 인증은 다음을 필요로 합니다.

* 인증서를 허용하도록 서버 구성
* `Startup.Configure`에서 인증 미들웨어 추가
* `Startup.ConfigureServices`에서 인증서 인증 서비스 추가

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

인증서 인증에 대한 옵션에는 다음 기능이 포함됩니다.

* 자체 서명된 인증서 허용
* 인증서 해지 확인
* 제공된 인증서에 올바른 사용량 플래그가 있는지 확인

본 사용자 계정은 인증서 속성으로부터 생성됩니다. 사용자 계정에는 해당 계정을 보완하거나 대체할 수 있게 해주는 이벤트가 포함되어 있습니다. 자세한 내용은 <xref:security/authentication/certauth>를 참조하세요.

[Windows 인증](/windows-server/security/windows-authentication/windows-authentication-overview)이 Linux 및 macOS로 확장되었습니다. 이전 버전에서 Windows 인증은 [IIS](xref:host-and-deploy/iis/index) 및 [HttpSys](xref:fundamentals/servers/httpsys)로 제한되었습니다. ASP.NET Core 3.0에서 [Kestrel](xref:fundamentals/servers/kestrel)은 Windows 도메인 가입 호스트로 Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux 및 macOS를 사용할 수 있습니다. 이러한 인증 체계의 Kestrel 지원은 [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지에서 제공합니다. 다른 인증 서비스와 마찬가지로 인증 앱 전체를 구성한 다음 해당 서비스를 구성합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

호스트 요구 사항:

* Windows 호스트에는 해당 앱을 호스트하는 사용자 계정에 [서비스 사용자 이름](/windows/win32/ad/service-principal-names)(SPN)을 추가해야 합니다.
* Linux 및 macOS 컴퓨터는 해당 도메인에 가입되어 있어야 합니다.
  * 해당 웹 프로세스에 대한 SPN을 만들어야 합니다.
  * 호스트 컴퓨터에서 [Keytab 파일](/archive/blogs/pie/all-you-need-to-know-about-keytab-files)을 생성하고 구성해야 합니다.

자세한 내용은 <xref:security/authentication/windowsauth>를 참조하세요.

## <a name="template-changes"></a>템플릿 변경 사항

웹 UI 템플릿(컨트롤러 및 뷰를 사용하는 Razor Pages, MVC)에서 다음이 제거되었습니다.

* cookie 동의 UI가 더 이상 포함되지 않습니다. ASP.NET Core 3.0 템플릿에서 생성한 앱에서 cookie 동의 기능을 사용하려면 <xref:security/gdpr>을 참조하세요.
* 스크립트 및 관련 정적 자산은 이제 CDN을 사용하는 대신 로컬 파일로 참조됩니다. 자세한 내용은 [스크립트 및 관련 정적 자산은 현재 환경을 기반으로 CDN을 사용하는 대신 로컬 파일로 참조됩니다(aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350)를 참조하세요.

Angular 템플릿은 Angular 8을 사용하는 것으로 업데이트되었습니다.

RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다. Visual Studio의 새 템플릿 옵션은 페이지 및 보기에 대한 템플릿 지원을 제공합니다. 명령 셸의 템플릿으로부터 RCL을 생성할 때 `--support-pages-and-views` 옵션(`dotnet new razorclasslib --support-pages-and-views`)을 전달합니다.

## <a name="generic-host"></a>제네릭 호스트

ASP.NET Core 3.0 템플릿은 <xref:fundamentals/host/generic-host>를 사용합니다. 이전 버전은 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 사용했습니다. .NET Core 제네릭 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 사용하면 ASP.NET Core 앱과 웹에 고유하지 않은 다른 서버 시나리오의 통합을 더 효율적으로 개선할 수 있습니다. 자세한 내용은 [HostBuilder가 WebHostBuilder 대체](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)를 참조하세요.

### <a name="host-configuration"></a>호스트 구성

ASP.NET Core 3.0 릴리스 전에는 웹 호스트의 호스트 구성에 대해 `ASPNETCORE_`가 접두사로 추가된 환경 변수가 로드되었습니다. 3\.0에서 `AddEnvironmentVariables`는 `CreateDefaultBuilder`와의 호스트 구성을 위해 `DOTNET_`가 접두사로 추가된 환경 변수를 로드하는 데 사용됩니다.

### <a name="changes-to-startup-constructor-injection"></a>Startup 생성자 주입의 변경 내용

제네릭 호스트는 `Startup` 생성자 주입에 대해서만 다음 형식을 지원합니다.

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

모든 서비스는 여전히 `Startup.Configure` 메서드에 대한 인수로 직접 삽입할 수 있습니다. 자세한 내용은 [제네릭 호스트가 시작 생성자 삽입 제한(aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353)을 참조하세요.

## <a name="kestrel"></a>Kestrel

* 제네릭 호스트로 마이그레이션하기 위해 Kestrel 구성이 업데이트되었습니다. 3\.0에서 Kestrel은 `ConfigureWebHostDefaults`가 제공하는 웹 호스트 작성기에서 구성됩니다.
* 연결 어댑터는 Kestrel에서 제거되었고 연결 미들웨어로 대체되었습니다. 이는 ASP.NET Core 파이프라인의 HTTP 미들웨어와 비슷하며, 하위 수준 연결을 위한 것입니다.
* Kestrel 전송 계층이 `Connections.Abstractions`에서 공용 인터페이스로 공개되었습니다.
* 후행 헤더를 새 컬렉션으로 이동하여 헤더와 트레일러 사이의 모호성을 해결했습니다.
* `HttpRequest.Body.Read`와 같은 동기 I/O API는 앱 크래시로 이어지는 일반적인 스레드 굶주림 현상(starvation)의 원인입니다. 3\.0에서 `AllowSynchronousIO`는 기본적으로 사용하지 않도록 설정됩니다.

자세한 내용은 <xref:migration/22-to-30#kestrel>를 참조하세요.

## <a name="http2-enabled-by-default"></a>HTTP/2는 기본적으로 사용하도록 설정됨

HTTP/2는 HTTPS 엔드포인트에 대해 Kestrel에서 기본적으로 사용하도록 설정되어 있습니다. 운영 체제에서 지원되는 경우 IIS 또는 HTTP.sys에 대한 HTTP/2 지원이 사용하도록 설정되어 있습니다.

## <a name="eventcounters-on-request"></a>EventCounters 요청

호스팅 EventSource인 `Microsoft.AspNetCore.Hosting`은 들어오는 요청과 관련된 다음과 같은 새 <xref:System.Diagnostics.Tracing.EventCounter> 형식을 내보냅니다.

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>엔드포인트 라우팅

프레임워크(예: MVC)가 미들웨어와 잘 작동하도록 허용하는 엔드포인트 라우팅이 다음과 같이 향상되었습니다.

* 미들웨어 및 엔드포인트의 순서는 `Startup.Configure`의 요청 처리 파이프라인에서 구성할 수 있습니다.
* 엔드포인트 및 미들웨어는 상태 검사와 같은 다른 ASP.NET Core 기반 기술과 함께 잘 구성됩니다.
* 엔드포인트는 CORS 또는 권한 부여와 같은 정책을 미들웨어와 MVC에서 모두 구현할 수 있습니다.
* 컨트롤러의 메서드에 필터 및 특성을 배치할 수 있습니다.

자세한 내용은 <xref:fundamentals/routing#routing-basics>를 참조하세요.

## <a name="health-checks"></a>상태 검사

상태 검사는 제네릭 호스트와의 엔드포인트 라우팅을 사용합니다. `Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 엔드포인트 작성기에 `MapHealthChecks`를 호출합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

상태 검사 엔드포인트는 다음을 수행할 수 있습니다.

* 허용되는 호스트/포트를 하나 이상 지정합니다.
* 권한 부여가 필요합니다.
* CORS가 필요합니다.

자세한 내용은 다음 항목을 참조하세요.

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>HttpContext의 파이프

이제 <xref:System.IO.Pipelines> API를 사용하여 요청 본문을 읽고 응답 본문을 쓸 수 있습니다. Component <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader` 속성은 요청 본문을 읽는 데 사용할 수 있는 <xref:System.IO.Pipelines.PipeReader>를 제공합니다. Component <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter` 속성은 응답 본문을 쓰는 데 사용할 수 있는 <xref:System.IO.Pipelines.PipeWriter>를 제공합니다. `HttpRequest.BodyReader`는 `HttpRequest.Body` 스트림의 아날로그입니다. `HttpResponse.BodyWriter`는 `HttpResponse.Body` 스트림의 아날로그입니다.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>IIS에서 오류 보고 개선

IIS에서 ASP.NET Core 앱을 호스팅할 때 시작 오류가 발생하면 더 많은 진단 데이터가 생성됩니다. 이러한 오류는 해당될 때마다 스택 추적을 사용하여 Windows 이벤트 로그에 보고됩니다. 또한 모든 경고, 오류 및 처리되지 않은 예외는 Windows 이벤트 로그에 기록됩니다.

## <a name="worker-service-and-worker-sdk"></a>작업자 서비스 및 작업자 SDK

.NET Core 3.0에는 새로운 작업자 서비스 앱 템플릿이 도입되었습니다. 이 템플릿은 .NET Core에서 장기간 실행되는 서비스를 작성하기 위한 시작점을 제공합니다.

자세한 내용은 다음을 참조하세요.

* [Windows 서비스로서 .NET Core 작업자](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>전달된 헤더 미들웨어 개선

이전 버전의 ASP.NET Core에서는 Azure Linux 또는 IIS 이외의 역방향 프록시 뒤에 배포 할 때 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 및 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>을 호출하는 데 문제가 있었습니다. 이전 버전에 대한 수정은 [Linux용 스키마 및 비 IIS 역방향 프록시 전달](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)에 설명되어 있습니다.

이 시나리오는 ASP.NET Core 3.0에서 해결되었습니다. `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 호스트는 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options)를 사용하도록 설정합니다. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`는 컨테이너 이미지에서 `true`로 설정됩니다.

## <a name="performance-improvements"></a>성능 향상

ASP.NET Core 3.0에는 메모리 사용을 줄이고 처리량을 향상시키는 다음과 같은 여러 개선 사항이 포함되어 있습니다.

* 범위가 지정된 서비스에 기본 제공 종속성 주입 컨테이너를 사용하는 경우 메모리 사용량 감소
* 미들웨어 시나리오 및 라우팅을 포함하여 프레임워크에서 할당 감소
* WebSocket 연결에 대한 메모리 사용량 감소
* HTTPS 연결에 대한 메모리 감소 및 처리량 향상
* 새롭게 최적화된 완전한 비동기 JSON 직렬 변환기
* 양식 구문 분석에서 처리량 향상 및 메모리 사용량 감소

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0은 .NET Core 3.0에서만 실행됩니다

ASP.NET Core 3.0에서 .NET Framework는 더 이상 지원 되는 대상 프레임워크가 아닙니다. .NET Framework를 대상으로 하는 프로젝트는 [.NET Core 2.1 LTS 릴리스](https://dotnet.microsoft.com/download/dotnet-core/2.1)를 사용하여 완전히 지원되는 방식으로 계속할 수 있습니다. 대부분의 ASP.NET Core 2.1.x 관련 패키지는 .NET Core 2.1의 3년 LTS 기간을 초과하여 무기한 지원됩니다.

마이그레이션 정보는 [.NET Framework에서 .NET Core로 코드 포팅](/dotnet/core/porting/)을 참조하세요.

## <a name="use-the-aspnet-core-shared-framework"></a>.NET Core 공유 프레임워크 사용

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함된 ASP.NET Core 3.0 공유 프레임워크는 더 이상 프로젝트 파일에서 명시적인 `<PackageReference />` 요소가 필요하지 않습니다. 프로젝트 파일에서 `Microsoft.NET.Sdk.Web` SDK를 사용할 때 공유 프레임워크가 자동으로 참조됩니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>ASP.NET Core 공유 프레임워크에서 제거된 어셈블리

ASP.NET Core 3.0 공유 프레임워크에서 제거된 가장 주목할 만한 어셈블리는 다음과 같습니다.

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(Json.NET). Json.NET을 ASP.NET Core 3.0에 추가하려면 [Newtonsoft.Json 기반 JSON 형식 지원](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)을 참조하세요. ASP.NET Core 3.0에서는 JSON을 읽고 쓰는 `System.Text.Json`을 제공합니다. 자세한 내용은 이 문서에서 [새 JSON 직렬화](#new-json-serialization)를 참조하세요.
* [Entity Framework Core](/ef/core/)

공유 프레임워크에서 제거되는 어셈블리의 전체 목록은 [Microsoft.AspNetCore.App 3.0에서 제거되는 어셈블리](https://github.com/dotnet/AspNetCore/issues/3755)를 참조하세요. 이러한 변경을 하는 동기에 관한 자세한 내용은 [Microsoft.AspNetCore.App 3.0에서 호환성이 손상되는 변경](https://github.com/aspnet/Announcements/issues/325) 및 [ASP.NET Core 3.0의 변경 내용 처음 보기](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)를 참조하세요.

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
