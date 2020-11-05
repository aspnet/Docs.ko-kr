---
title: C Core에서 ASP.NET Core로 gRPC 서비스 마이그레이션
author: juntaoluo
description: ASP.NET Core 스택을 토대로 실행되도록 기존 C Core 기반 gRPC 앱을 이동하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059834"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>C Core에서 ASP.NET Core로 gRPC 서비스 마이그레이션

작성자: [John Luo](https://github.com/juntaoluo)

기본 스택의 구현 때문에 일부 기능은 [C Core 기반 gRPC](https://grpc.io/blog/grpc-stacks) 앱과 ASP.NET Core 기반 앱에서 동일한 방식으로 작동하지 않습니다. 이 문서에서는 두 스택 간의 마이그레이션을 위해 주요 차이점을 설명합니다.

## <a name="grpc-service-implementation-lifetime"></a>gRPC 서비스 구현 수명

ASP.NET Core 스택에서 gRPC 서비스는 기본적으로 [범위가 지정된 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 사용하여 생성됩니다. 반면, gRPC C Core는 기본적으로 [singleton 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 사용하여 서비스에 바인딩됩니다.

범위가 지정된 수명을 사용하면 서비스 구현에서 범위가 지정된 수명을 사용하는 다른 서비스를 확인할 수 있습니다. 예를 들어 범위가 지정된 수명이 생성자 주입을 통해 DI 컨테이너에서 `DbContext`를 확인할 수도 있습니다. 범위가 지정된 수명 사용과 관련해서 다음 사항을 확인합니다.

* 각 요청에 대해 서비스 구현의 새 인스턴스가 생성됩니다.
* 구현 형식의 인스턴스 멤버를 통해 요청 간에 상태를 공유할 수 없습니다.
* DI 컨테이너의 단일 서비스에 공유 상태를 저장해야 합니다. 저장된 공유 상태는 gRPC 서비스 구현의 생성자에서 확인됩니다.

서비스 수명에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#service-lifetimes>를 참조하세요.

### <a name="add-a-singleton-service"></a>singleton 서비스 추가

gRPC C Core 구현에서 ASP.NET Core로 전환하기 쉽도록 서비스 구현의 서비스 수명을 범위가 지정된 수명에서 singleton 수명으로 변경할 수 있습니다. 여기에는 서비스 구현 인스턴스를 DI 컨테이너에 추가하는 작업이 포함됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

그러나 singleton 수명을 사용하는 서비스 구현은 이제 생성자 주입을 통해 범위가 지정된 서비스를 확인할 수 없습니다.

## <a name="configure-grpc-services-options"></a>gRPC 서비스 옵션 구성

C Core 기반 앱에서 `grpc.max_receive_message_length`, `grpc.max_send_message_length` 등의 설정은 [서버 인스턴스를 생성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)할 때 `ChannelOption`을 사용하여 구성합니다.

ASP.NET Core에서 gRPC는 `GrpcServiceOptions` 형식을 통해 구성을 제공합니다. 예를 들어 gRPC 서비스에서 들어오는 메시지의 최대 크기는 `AddGrpc`를 통해 구성할 수 있습니다. 다음 예제에서는 기본 `MaxReceiveMessageSize`인 4MB를 16MB로 변경합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

구성에 대한 자세한 내용은 <xref:grpc/configuration>을 참조하세요.

## <a name="logging"></a>로깅

C Core 기반 앱은 `GrpcEnvironment`를 사용하여 디버깅 용도의 [로거를 구성](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)합니다. ASP.NET Core 스택은 [로깅 API](xref:fundamentals/logging/index)를 통해 이 기능을 제공합니다. 예를 들어 생성자 주입을 통해 gRPC 서비스에 로거를 추가할 수 있습니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C Core 기반 앱은 [Server.Ports 속성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)을 통해 HTTPS를 구성합니다. ASP.NET Core에서 서버를 구성하는 경우에도 유사한 개념이 사용됩니다. 예를 들어 Kestrel은 이 기능을 위해 [엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 사용합니다.

## <a name="grpc-interceptors-vs-middleware"></a>gRPC 인터셉터 대 미들웨어

ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)는 C Core 기반 gRPC 앱의 인터셉터와 유사한 기능을 제공합니다. ASP.NET Core 미들웨어와 인터셉터는 개념적으로 유사합니다. 둘 다:

* gRPC 요청을 처리하는 파이프라인을 생성하는 데 사용됩니다.
* 파이프라인의 다음 구성 요소 이전이나 이후에 작업을 수행할 수 있습니다.
* `HttpContext`에 대한 액세스를 제공합니다.
  * 미들웨어에서 `HttpContext`는 매개 변수입니다.
  * 인터셉터에서는 `ServerCallContext.GetHttpContext` 확장 메서드와 함께 `ServerCallContext` 매개 변수를 사용하여 `HttpContext`에 액세스할 수 있습니다. 이 기능은 ASP.NET Core에서 실행되는 인터셉터와 관련이 있습니다.

ASP.NET Core 미들웨어와 gRPC 인터셉터의 차이점:

* 인터셉터:
  * [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)를 사용하여 추상화의 gRPC 계층에서 작동합니다.
  * 다음에 대한 액세스를 제공합니다.
    * 호출에 전송되는 역직렬화된 메시지
    * 직렬화되기 전에 호출에서 반환되는 메시지
  * gRPC 서비스에서 throw된 예외를 catch하고 처리할 수 있습니다.
* 미들웨어:
  * gRPC 인터셉터 이전에 실행됩니다.
  * 기본 HTTP/2 메시지에서 작동합니다.
  * 요청 및 응답 스트림의 바이트에만 액세스할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
