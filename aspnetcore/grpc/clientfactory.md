---
title: .NET Core의 gRPC 클라이언트 팩터리 통합
author: jamesnk
description: 클라이언트 팩터리를 사용하여 gRPC 클라이언트를 만드는 방법을 알아봅니다.
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060952"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core의 gRPC 클라이언트 팩터리 통합

`HttpClientFactory`와 gRPC 통합은 중앙에서 gRPC 클라이언트를 만드는 방법을 제공합니다. [독립 실행형 gRPC 클라이언트 인스턴스를 구성](xref:grpc/client)하는 대신 이 방법을 사용할 수 있습니다. 팩터리 통합은 [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.

팩터리는 다음과 같은 이점을 제공합니다.

* 논리적 gRPC 클라이언트 인스턴스를 구성하기 위한 중앙 위치를 제공합니다.
* 기본 `HttpClientMessageHandler`의 수명을 관리합니다.
* ASP.NET Core gRPC 서비스에서 최종 기한 및 취소 자동 전파

## <a name="register-grpc-clients"></a>gRPC 클라이언트 등록

gRPC 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 gRPC 형식 클라이언트 클래스 및 서비스 주소를 지정하여 제네릭 `AddGrpcClient` 확장 메서드를 사용할 수 있습니다.

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

gRPC 클라이언트 형식은 DI(종속성 주입)를 사용하여 일시적으로 등록됩니다. 이제 클라이언트는 DI를 사용하여 만든 형식으로 직접 주입하고 사용할 수 있습니다. ASP.NET Core MVC 컨트롤러, SignalR 허브 및 gRPC 서비스는 gRPC 클라이언트가 자동으로 주입될 수 있는 위치입니다.

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>HttpClient 구성

`HttpClientFactory`는 gRPC 클라이언트에서 사용하는 `HttpClient`를 만듭니다. 표준 `HttpClientFactory` 메서드는 나가는 요청 미들웨어를 추가하거나 `HttpClient`의 기본 `HttpClientHandler`를 구성하는 데 사용할 수 있습니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

자세한 내용은 [IHttpClientFactory를 사용하여 HTTP 요청 만들기](xref:fundamentals/http-requests)를 참조하세요.

## <a name="configure-channel-and-interceptors"></a>채널 및 인터셉터 구성

gRPC 관련 메서드는 다음에 사용할 수 있습니다.

* gRPC 클라이언트의 기본 채널을 구성합니다.
* 클라이언트에서 gRPC 호출을 만들 때 사용할 `Interceptor` 인스턴스를 추가합니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>최종 기한 및 취소 전파

gRPC 서비스에서 팩터리로 만든 gRPC 클라이언트는 최종 기한 및 취소 토큰을 자식 호출에 자동으로 전파하도록 `EnableCallContextPropagation()`를 사용하여 구성할 수 있습니다. `EnableCallContextPropagation()` 확장 메서드는 [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.

호출 컨텍스트 전파는 현재 gRPC 요청 컨텍스트에서 최종 기한 및 취소 토큰을 읽고 gRPC 클라이언트에서 수행한 나가는 호출에 자동으로 전파하는 방식으로 작동합니다. 호출 컨텍스트 전파는 복잡한 중첩 gRPC 시나리오가 항상 최종 기한 및 취소를 전파하도록 하는 유용한 방법입니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

기본적으로 클라이언트를 gRPC 호출 컨텍스트의 외부에서 사용하는 경우 `EnableCallContextPropagation` 오류가 발생합니다. 이 오류는 전파할 호출 컨텍스트가 없다는 경고를 표시하기 위한 것입니다. 호출 컨텍스트 외부에서 클라이언트를 사용하려는 경우 `SuppressContextNotFoundErrors`를 사용하여 클라이언트를 구성하면 오류가 표시되지 않습니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

최종 기한 및 RPC 취소에 대한 자세한 내용은 [RPC 수명 주기](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
