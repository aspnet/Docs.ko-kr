---
title: gRPC 재시도를 통해 일시적인 오류 처리
author: jamesnk
description: .NET에서 재시도로 복원력과 내결함성이 있는 gRPC 호출을 수행하는 방법에 관해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/25/2021
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
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589791"
---
# <a name="transient-fault-handling-with-grpc-retries"></a>gRPC 재시도를 통해 일시적인 오류 처리

작성자: [James Newton-King](https://twitter.com/jamesnk)

gRPC 재시도는 gRPC 클라이언트가 실패한 호출을 자동으로 다시 시도하는 기능입니다. 이 문서에서는 .NET에서 복원력과 내결함성이 있는 gRPC 앱을 만들기 위해 재시도 정책을 구성하는 방법에 관해 설명합니다.

gRPC 다시 시도에는 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 버전 2.36.0-pre1 이상이 필요합니다.

## <a name="transient-fault-handling"></a>일시적인 오류 처리

gRPC 호출은 일시적인 오류로 인해 중단될 수 있습니다. 일시적인 오류에는 다음이 포함됩니다.

* 네트워크 연결이 일시적으로 끊김
* 서비스를 일시적으로 사용할 수 없음
* 서버 로드로 인한 시간 초과

gRPC 호출이 중단되면 클라이언트는 오류에 대한 세부 정보와 함께 `RpcException`을 throw합니다. 클라이언트 앱은 예외를 catch하고 오류를 처리하는 방법을 선택해야 합니다.

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

앱 전체에서 다시 시도 논리를 중복하는 것은 장황한 일이며 오류를 일으킬 수 있습니다. 다행히 .NET gRPC 클라이언트는 자동 다시 시도를 기본적으로 지원합니다.

## <a name="configure-a-grpc-retry-policy"></a>gRPC 재시도 정책 구성

gRPC 채널을 만들 때 재시도 정책이 한 번 구성됩니다.

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

위의 코드는

* `MethodConfig`을 만듭니다. 다시 시도 정책은 메서드당 구성할 수 있으며 `Names` 속성을 사용하여 메서드를 일치시킵니다. 이 메서드는 `MethodName.Default`로 구성되므로 이 채널에서 호출하는 모든 gRPC 메서드에 적용됩니다.
* 재시도 정책을 구성합니다. 이 정책은 상태 코드 `Unavailable`로 실패한 gRPC 호출을 자동으로 다시 시도하도록 클라이언트에 지시합니다.
* `GrpcChannelOptions.ServiceConfig`를 설정하여 만든 채널이 재시도 정책을 사용하도록 구성합니다.

채널로 만든 gRPC 클라이언트는 실패한 호출을 자동으로 다시 시도합니다.

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a>gRPC 다시 시도 옵션

다음 표에서는 gRPC 다시 시도 정책을 구성하기 위한 옵션을 설명합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| `MaxAttempts` | 원래 시도를 포함한 최대 호출 시도 횟수입니다. 이 값은 `GrpcChannelOptions.MaxRetryAttempts`에 의해 제한되며 기본값은 5입니다. 값은 필수이며 1보다 커야 합니다. |
| `InitialBackoff` | 다시 시도 사이의 초기 백오프 지연입니다. 0과 현재 백오프 사이의 임의 지연은 언제 다음 다시 시도가 수행될지를 결정합니다. 각 시도 후에는 현재 백오프에 `BackoffMultiplier`를 곱합니다. 값은 필수이며 0보다 커야 합니다. |
| `MaxBackoff` | 최대 백오프는 지수 백오프 증가에 대한 상한을 설정합니다. 값은 필수이며 0보다 커야 합니다. |
| `BackoffMultiplier` | 다시 시도할 때마다 백오프에 이 값을 곱하게 되며 승수가 1보다 크면 백오프가 기하급수적으로 증가합니다. 값은 필수이며 0보다 커야 합니다. |
| `RetryableStatusCodes` | 상태 코드의 컬렉션입니다. 일치 상태와 함께 실패하는 gRPC 호출을 자동으로 다시 시도합니다. 상태 코드에 대한 자세한 내용은 [상태 코드 및 gRPC에서의 사용](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)을 참조하세요. 다시 시도 가능한 상태 코드가 하나 이상 필요합니다. |

## <a name="hedging"></a>Hedging

Hedging은 대체 다시 시도 전략입니다. Hedging은 응답을 기다리지 않고 단일 gRPC 호출의 여러 복사본을 적극적으로 전송할 수 있습니다. Hedged gRPC 호출은 서버에서 여러 번 실행될 수 있으며 처음 성공한 결과가 사용됩니다. Hedging은 부정적인 영향 없이 여러 번 실행해도 안전한 메서드에만 사용하도록 설정하는 것이 중요합니다.

Hedging은 다시 시도와 비교했을 때 장단점이 있습니다. 

* Hedging의 장점은 성공적인 결과를 더 빠르게 반환할 수 있다는 것입니다. 여러 동시 gRPC 호출을 허용하고 첫 번째 성공적인 결과를 사용할 수 있을 때 완료됩니다. 
* Hedging의 단점은 낭비가 될 수 있다는 것입니다. 여러 번 호출할 수 있으며 모두 성공할 수 있습니다. 첫 번째 결과만 사용되고 나머지는 삭제됩니다.

## <a name="configure-a-grpc-hedging-policy"></a>gRPC hedging 정책 구성

Hedging 정책은 재시도 정책처럼 구성됩니다. Hedging 정책은 재시도 정책과 함께 사용할 수 없습니다.

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a>gRPC hedging 옵션

다음 표에서는 gRPC hedging 정책을 구성하기 위한 옵션을 설명합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| `MaxAttempts` | Hedging 정책은 이 호출 수만큼 보냅니다. `MaxAttempts`는 원래 시도를 포함한 모든 시도의 총횟수를 나타냅니다. 이 값은 `GrpcChannelOptions.MaxRetryAttempts`에 의해 제한되며 기본값은 5입니다. 값은 필수이며 2 이상이어야 합니다. |
| `HedgingDelay` | 첫 번째 호출은 즉시 전송되지만 후속 hedging 호출은 이 값으로 지연됩니다. 지연을 0 또는 `null`로 설정하면 모든 hedged 호출이 즉시 전송됩니다. 기본값은 0입니다. |
| `NonFatalStatusCodes` | 다른 hedge 호출이 성공할 수 있음을 나타내는 상태 코드의 컬렉션입니다. 서버에서 심각하지 않은 상태 코드를 반환하는 경우에는 hedged 호출이 계속됩니다. 아니면 처리되지 않은 요청이 취소되고 오류가 앱으로 반환됩니다. 상태 코드에 대한 자세한 내용은 [상태 코드 및 gRPC에서의 사용](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)을 참조하세요. |

## <a name="additional-resources"></a>추가 리소스

* <xref:grpc/client>
* [다시 시도 일반 지침 - 클라우드 애플리케이션에 대한 모범 사례](/azure/architecture/best-practices/transient-faults)
