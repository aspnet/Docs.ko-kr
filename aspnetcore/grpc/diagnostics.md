---
title: .NET의 gRPC에서 로깅 및 진단
author: jamesnk
description: .NET의 gRPC 앱에서 진단을 수집하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060445"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET의 gRPC에서 로깅 및 진단

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 문제 해결을 위해 gRPC 앱에서 진단 정보를 수집하는 방법에 대한 지침을 제공합니다. 다음 내용을 다룹니다.

* **로깅** - [.NET Core 로깅](xref:fundamentals/logging/index)에 기록되는 구조적 로그입니다. <xref:Microsoft.Extensions.Logging.ILogger>는 앱 프레임워크에서 로그를 기록하고 사용자가 앱에서 자체 로깅을 유지하는 데 사용됩니다.
* **추적** - `DiaganosticSource` 및 `Activity`를 사용하여 기록되는 작업 관련 이벤트입니다. 진단 소스에서의 추적은 일반적으로 [Application Insights](/azure/azure-monitor/app/asp-net-core) 및 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 라이브러리에서 앱 원격 분석을 수집하는 데 사용됩니다.
* **메트릭** - 시간 간격에 따른 데이터 측정값 표시입니다(예: 초당 요청 수). `EventCounter`를 사용하여 메트릭을 내보내고, [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) 명령줄 도구 또는 [Application Insights](/azure/azure-monitor/app/eventcounters)를 사용하여 확인할 수 있습니다.

## <a name="logging"></a>로깅

gRPC 서비스와 gRPC 클라이언트는 [.NET Core 로깅](xref:fundamentals/logging/index)을 사용하여 로그를 기록합니다. 앱의 예기치 않은 동작을 디버그해야 하는 경우 로그에서 시작하는 것이 좋습니다.

### <a name="grpc-services-logging"></a>gRPC 서비스 로깅

> [!WARNING]
> 서버 쪽 로그에는 앱의 중요한 정보가 포함될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 퍼블릭 포럼에 게시하면 **안 됩니다**.

gRPC 서비스는 ASP.NET Core에서 호스트되므로 ASP.NET Core 로깅 시스템을 사용합니다. 기본 구성에서 gRPC는 매우 적은 정보를 기록하지만, 이 동작을 구성할 수 있습니다. ASP.NET Core 로깅을 구성하는 방법에 대한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 문서를 참조하세요.

gRPC는 `Grpc` 범주에 로그를 추가합니다. gRPC의 자세한 로그를 사용하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가하여 *appsettings.json* 파일의 `Debug` 수준에 `Grpc` 접두사를 구성합니다.

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

`ConfigureLogging`을 사용하여 *Startup.cs* 에서 이 동작을 구성할 수도 있습니다.

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON 기반 구성을 사용하지 않는 경우, 구성 시스템에서 다음 구성 값을 설정합니다.

* `Logging:LogLevel:Grpc` = `Debug`

구성 시스템 설명서에서 중첩된 구성 값을 지정하는 방법을 확인합니다. 예를 들어 환경 변수를 사용하는 경우, `:` 대신 두 개의 `_` 문자가 사용됩니다(예: `Logging__LogLevel__Grpc`).

앱에 대한 자세한 진단을 수집하는 경우 `Debug` 수준을 사용하는 것이 좋습니다. `Trace` 수준은 매우 낮은 수준의 진단을 생성하며, 앱의 문제를 진단하는 데 거의 필요하지 않습니다.

#### <a name="sample-logging-output"></a>샘플 로깅 출력

다음은 gRPC 서비스의 `Debug` 수준에서 표시되는 콘솔 출력의 예입니다.

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>서버 쪽 로그 액세스

서버 쪽 로그에 액세스하는 방법은 실행 환경에 따라 다릅니다.

#### <a name="as-a-console-app"></a>콘솔 앱으로

콘솔 앱에서 실행하는 경우 [콘솔 로거](xref:fundamentals/logging/index#console)가 기본적으로 사용되어야 합니다. gRPC 로그가 콘솔에 표시됩니다.

#### <a name="other-environments"></a>기타 환경

다른 환경(예: Docker, Kubernetes 또는 Windows 서비스)에 앱을 배포하는 경우, 환경에 적합한 로깅 공급자를 구성하는 방법에 대한 자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.

### <a name="grpc-client-logging"></a>gRPC 클라이언트 로깅

> [!WARNING]
> 클라이언트 쪽 로그에는 앱의 중요한 정보가 포함될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 퍼블릭 포럼에 게시하면 **안 됩니다**.

.NET 클라이언트에서 로그를 가져오려면 클라이언트 채널을 만들 때 `GrpcChannelOptions.LoggerFactory` 속성을 설정합니다. ASP.NET Core 앱에서 gRPC 서비스를 호출하는 경우, DI(종속성 주입)에서 로거 팩터리를 확인할 수 있습니다.

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

클라이언트 로깅을 사용하도록 설정하는 대체 방법은 [gRPC 클라이언트 팩터리](xref:grpc/clientfactory)를 사용하여 클라이언트를 만드는 것입니다. 클라이언트 팩터리에 등록되고 DI에서 확인된 gRPC 클라이언트는 자동으로 앱의 구성된 로깅을 사용합니다.

앱에서 DI를 사용하지 않는 경우, [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용하여 새 `ILoggerFactory` 인스턴스를 만들 수 있습니다. 이 메서드에 액세스하려면 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 패키지를 앱에 추가합니다.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC 클라이언트 로그 범위

gRPC 클라이언트는 gRPC 호출 중에 생성된 로그에 [로깅 범위](../fundamentals/logging/index.md#log-scopes)를 추가합니다. 범위에는 gRPC 호출과 관련된 메타데이터가 있습니다.

* **GrpcMethodType** - gRPC 메서드 형식입니다. 가능한 값은 `Grpc.Core.MethodType` 열거형의 이름입니다(예: 단항).
* **GrpcUri** - gRPC 메서드의 상대 URI입니다(예: /greet.Greeter/SayHellos).

#### <a name="sample-logging-output"></a>샘플 로깅 출력

다음은 gRPC 클라이언트의 `Debug` 수준에서 표시되는 콘솔 출력의 예입니다.

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>추적

gRPC 서비스와 gRPC 클라이언트는 [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) 및 [Activity](/dotnet/api/system.diagnostics.activity)를 사용하여 gRPC 호출에 대한 정보를 제공합니다.

* .NET gRPC는 작업을 사용하여 gRPC 호출을 나타냅니다.
* 추적 이벤트는 gRPC 호출 작업을 시작하고 중지할 때 진단 소스에 기록됩니다.
* 추적은 gRPC 스트리밍 호출의 수명 동안 메시지가 전송된 시기에 대한 정보를 캡처하지 않습니다.

### <a name="grpc-service-tracing"></a>gRPC 서비스 추적

gRPC 서비스는 들어오는 HTTP 요청에 대한 이벤트를 보고하는 ASP.NET Core에서 호스트됩니다. ASP.NET Core에서 제공하는 기존 HTTP 요청 진단에 gRPC 특정 메타데이터가 추가됩니다.

* 진단 소스 이름은 `Microsoft.AspNetCore`입니다.
* 작업 이름은 `Microsoft.AspNetCore.Hosting.HttpRequestIn`입니다.
  * gRPC 호출을 통해 호출된 gRPC 메서드 이름은 이름이 `grpc.method`인 태그로 추가됩니다.
  * 완료 시 gRPC 호출의 상태 코드는 이름이 `grpc.status_code`인 태그로 추가됩니다.

### <a name="grpc-client-tracing"></a>gRPC 클라이언트 추적

.NET gRPC 클라이언트는 `HttpClient`를 사용하여 gRPC 호출을 수행합니다. `HttpClient`가 진단 이벤트를 기록하지만, .NET gRPC 클라이언트는 gRPC 호출에 대한 전체 정보를 수집할 수 있도록 사용자 지정 진단 소스, 작업 및 이벤트를 제공합니다.

* 진단 소스 이름은 `Grpc.Net.Client`입니다.
* 작업 이름은 `Grpc.Net.Client.GrpcOut`입니다.
  * gRPC 호출을 통해 호출된 gRPC 메서드 이름은 이름이 `grpc.method`인 태그로 추가됩니다.
  * 완료 시 gRPC 호출의 상태 코드는 이름이 `grpc.status_code`인 태그로 추가됩니다.

### <a name="collecting-tracing"></a>추적 수집

`DiagnosticSource`를 사용하는 가장 간편한 방법은 앱에서 [Application Insights](/azure/azure-monitor/app/asp-net-core) 또는 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 원격 분석 라이브러리를 구성하는 것입니다. 라이브러리는 다른 앱 원격 분석과 함께 gRPC 호출 정보를 처리합니다.

Application Insights와 같은 관리형 서비스에서 추적을 볼 수 있으며, 고유한 분산 추적 시스템을 실행할 수도 있습니다. OpenTelemetry는 추적 데이터를 [Jaeger](https://www.jaegertracing.io/) 및 [Zipkin](https://zipkin.io/)으로 내보내는 기능을 지원합니다.

`DiagnosticSource`는 `DiagnosticListener`를 통해 코드에서 추적 이벤트를 사용할 수 있습니다. 코드를 사용하여 진단 소스를 수신 대기하는 방법에 대한 자세한 내용은 [DiagnosticSource 사용자 가이드](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)를 참조하세요.

> [!NOTE]
> 원격 분석 라이브러리는 현재 gRPC 특정 `Grpc.Net.Client.GrpcOut` 원격 분석을 캡처하지 않습니다. 이 추적을 캡처하도록 원격 분석 라이브러리를 개선하기 위해 노력하고 있습니다.

## <a name="metrics"></a>메트릭

메트릭은 시간 간격에 따른 데이터 측정값 표시입니다(예: 초당 요청 수). 메트릭 데이터를 통해 앱 상태를 상위 수준에서 확인할 수 있습니다. `EventCounter`를 사용하여 .NET gRPC 메트릭을 내보냅니다.

### <a name="grpc-service-metrics"></a>gRPC 서비스 메트릭

gRPC 서버 메트릭은 `Grpc.AspNetCore.Server` 이벤트 원본에 보고됩니다.

| 이름                      | 설명                   |
| --------------------------|-------------------------------|
| `total-calls`             | 총 호출                   |
| `current-calls`           | 현재 호출 수                 |
| `calls-failed`            | 실패한 호출의 총수            |
| `calls-deadline-exceeded` | 최종 기한을 넘긴 호출의 총수 |
| `messages-sent`           | 총 보낸 메시지           |
| `messages-received`       | 받은 총 메시지 수       |
| `calls-unimplemented`     | 구현되지 않은 호출의 총수     |

ASP.NET Core는 `Microsoft.AspNetCore.Hosting` 이벤트 원본에 대한 고유한 메트릭도 제공합니다.

### <a name="grpc-client-metrics"></a>gRPC 클라이언트 메트릭

gRPC 클라이언트 메트릭은 `Grpc.Net.Client` 이벤트 원본에 보고됩니다.

| 이름                      | 설명                   |
| --------------------------|-------------------------------|
| `total-calls`             | 총 호출                   |
| `current-calls`           | 현재 호출 수                 |
| `calls-failed`            | 실패한 호출의 총수            |
| `calls-deadline-exceeded` | 최종 기한을 넘긴 호출의 총수 |
| `messages-sent`           | 총 보낸 메시지           |
| `messages-received`       | 받은 총 메시지 수       |

### <a name="observe-metrics"></a>메트릭 확인

[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters)는 임시 상태 모니터링 및 1단계 수준 성능 조사를 위한 성능 모니터링 도구입니다. 공급자 이름이 `Grpc.AspNetCore.Server` 또는 `Grpc.Net.Client`인 .NET 앱을 모니터링합니다.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

gRPC 메트릭을 확인하는 또 다른 방법은 Application Insights의 [Microsoft.ApplicationInsights.EventCounterCollector 패키지](/azure/azure-monitor/app/eventcounters)를 사용하여 카운터 데이터를 캡처하는 것입니다. 설정이 완료되면 Application Insights는 런타임에 일반적인 .NET 카운터를 수집합니다. gRPC 카운터는 기본적으로 수집되지 않지만, [추가 카운터를 포함하도록 App Insights를 사용자 지정](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)할 수 있습니다.

*Startup.cs* 에서 수집할 Application Insight의 gRPC 카운터를 지정합니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>