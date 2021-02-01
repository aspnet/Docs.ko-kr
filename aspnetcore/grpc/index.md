---
title: .NET에서 gRPC 소개
author: juntaoluo
description: Kestrel 서버 및 ASP.NET Core 스택을 사용하는 gRPC 서비스에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 5820049aba90a2fbd06a23756b12ac9656c3b2c4
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057514"
---
# <a name="introduction-to-grpc-on-net"></a>.NET에서 gRPC 소개

작성자: [John Luo](https://github.com/juntaoluo) 및 [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/)는 언어에 제약 받지 않는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다.

gRPC의 주요 이점은 다음과 같습니다.
* 최신 고성능 경량 RPC 프레임워크.
* 기본적으로 프로토콜 버퍼를 사용하는 계약 중심 API 개발로 언어에 구애받지 않는 구현이 가능합니다.
* 여러 언어에서 강력한 형식의 서버 및 클라이언트를 생성할 수 있는 도구입니다.
* 클라이언트, 서버 및 양방향 스트리밍 호출을 지원합니다.
* Protobuf 이진 직렬화를 사용하여 네트워크 사용량이 감소합니다.

이러한 이점으로 인해 gRPC는 다음과 같은 분야에 이상적입니다.
* 효율성이 중요한 경량 마이크로 서비스.
* 개발을 위해 여러 언어가 필요한 다중 언어 시스템.
* 스트리밍 요청 또는 응답을 처리해야 하는 지점 간 실시간 서비스.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>.proto 파일에 대한 C# 도구 지원

gRPC는 API 개발에 계약 중심 접근 방식을 사용합니다. 서비스 및 메시지는 다음과 같은 *\*.proto* 파일에 정의됩니다.

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

프로젝트에 *\*.proto* 파일을 포함하면 서비스, 클라이언트 및 메시지에 대한 .NET 형식이 자동으로 생성됩니다.

* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 패키지에 대한 패키지 참조를 추가합니다.
* `<Protobuf>` 항목 그룹에 *\*.proto* 파일을 추가합니다.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

gRPC 도구 지원에 대한 자세한 내용은 <xref:grpc/basics> 문서를 참조하세요.

## <a name="grpc-services-on-aspnet-core"></a>ASP.NET Core의 gRPC 서비스

gRPC 서비스는 ASP.NET Core에서 호스팅될 수 있습니다. 서비스는 로깅, DI(종속성 주입), 인증 및 권한 부여와 같은 널리 사용되는 ASP.NET Core 기능과 완벽하게 통합됩니다.

gRPC 서비스 프로젝트 템플릿은 다음과 같은 시작 서비스를 제공합니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`는 *\*.proto* 파일의 `Greeter` 서비스에서 생성되는 `GreeterBase` 형식에서 상속됩니다. 이 서비스는 *Startup.cs* 에서 클라이언트가 액세스할 수 있게 구성됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

ASP.NET Core의 gRPC 서비스에 대한 자세한 내용은 <xref:grpc/aspnetcore> 문서를 참조하세요.

## <a name="call-grpc-services-with-a-net-client"></a>.NET 클라이언트로 gRPC 서비스 호출

gRPC 클라이언트는 [ *\*.proto* 파일에서 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 형식입니다. 구체적인 gRPC 클라이언트에는 *\*.proto* 파일에서 gRPC 서비스로 변환되는 메서드가 있습니다.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

gRPC 클라이언트는 gRPC 서비스에 대한 수명이 긴 연결을 나타내는 채널을 사용하여 만들어집니다. `GrpcChannel.ForAddress`를 사용하여 채널을 만들 수 있습니다.

클라이언트를 만들고 다른 서비스 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:grpc/client> 문서를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
