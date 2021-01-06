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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060445"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="a5708-103">.NET의 gRPC에서 로깅 및 진단</span><span class="sxs-lookup"><span data-stu-id="a5708-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="a5708-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a5708-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a5708-105">이 문서에서는 문제 해결을 위해 gRPC 앱에서 진단 정보를 수집하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="a5708-106">다음 내용을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-106">Topics covered include:</span></span>

* <span data-ttu-id="a5708-107">**로깅** - [.NET Core 로깅](xref:fundamentals/logging/index)에 기록되는 구조적 로그입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a5708-108"><xref:Microsoft.Extensions.Logging.ILogger>는 앱 프레임워크에서 로그를 기록하고 사용자가 앱에서 자체 로깅을 유지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="a5708-109">**추적** - `DiaganosticSource` 및 `Activity`를 사용하여 기록되는 작업 관련 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="a5708-110">진단 소스에서의 추적은 일반적으로 [Application Insights](/azure/azure-monitor/app/asp-net-core) 및 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 라이브러리에서 앱 원격 분석을 수집하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="a5708-111">**메트릭** - 시간 간격에 따른 데이터 측정값 표시입니다(예: 초당 요청 수).</span><span class="sxs-lookup"><span data-stu-id="a5708-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="a5708-112">`EventCounter`를 사용하여 메트릭을 내보내고, [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) 명령줄 도구 또는 [Application Insights](/azure/azure-monitor/app/eventcounters)를 사용하여 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="a5708-113">로깅</span><span class="sxs-lookup"><span data-stu-id="a5708-113">Logging</span></span>

<span data-ttu-id="a5708-114">gRPC 서비스와 gRPC 클라이언트는 [.NET Core 로깅](xref:fundamentals/logging/index)을 사용하여 로그를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a5708-115">앱의 예기치 않은 동작을 디버그해야 하는 경우 로그에서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="a5708-116">gRPC 서비스 로깅</span><span class="sxs-lookup"><span data-stu-id="a5708-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="a5708-117">서버 쪽 로그에는 앱의 중요한 정보가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="a5708-118">프로덕션 앱의 원시 로그를 GitHub와 같은 퍼블릭 포럼에 게시하면 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="a5708-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="a5708-119">gRPC 서비스는 ASP.NET Core에서 호스트되므로 ASP.NET Core 로깅 시스템을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="a5708-120">기본 구성에서 gRPC는 매우 적은 정보를 기록하지만, 이 동작을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="a5708-121">ASP.NET Core 로깅을 구성하는 방법에 대한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5708-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="a5708-122">gRPC는 `Grpc` 범주에 로그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="a5708-123">gRPC의 자세한 로그를 사용하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가하여 *appsettings.json* 파일의 `Debug` 수준에 `Grpc` 접두사를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="a5708-124">`ConfigureLogging`을 사용하여 *Startup.cs* 에서 이 동작을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="a5708-125">JSON 기반 구성을 사용하지 않는 경우, 구성 시스템에서 다음 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="a5708-126">구성 시스템 설명서에서 중첩된 구성 값을 지정하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="a5708-127">예를 들어 환경 변수를 사용하는 경우, `:` 대신 두 개의 `_` 문자가 사용됩니다(예: `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="a5708-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="a5708-128">앱에 대한 자세한 진단을 수집하는 경우 `Debug` 수준을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="a5708-129">`Trace` 수준은 매우 낮은 수준의 진단을 생성하며, 앱의 문제를 진단하는 데 거의 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="a5708-130">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="a5708-130">Sample logging output</span></span>

<span data-ttu-id="a5708-131">다음은 gRPC 서비스의 `Debug` 수준에서 표시되는 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="a5708-132">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="a5708-132">Access server-side logs</span></span>

<span data-ttu-id="a5708-133">서버 쪽 로그에 액세스하는 방법은 실행 환경에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="a5708-134">콘솔 앱으로</span><span class="sxs-lookup"><span data-stu-id="a5708-134">As a console app</span></span>

<span data-ttu-id="a5708-135">콘솔 앱에서 실행하는 경우 [콘솔 로거](xref:fundamentals/logging/index#console)가 기본적으로 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="a5708-136">gRPC 로그가 콘솔에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="a5708-137">기타 환경</span><span class="sxs-lookup"><span data-stu-id="a5708-137">Other environments</span></span>

<span data-ttu-id="a5708-138">다른 환경(예: Docker, Kubernetes 또는 Windows 서비스)에 앱을 배포하는 경우, 환경에 적합한 로깅 공급자를 구성하는 방법에 대한 자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5708-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="a5708-139">gRPC 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="a5708-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="a5708-140">클라이언트 쪽 로그에는 앱의 중요한 정보가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="a5708-141">프로덕션 앱의 원시 로그를 GitHub와 같은 퍼블릭 포럼에 게시하면 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="a5708-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="a5708-142">.NET 클라이언트에서 로그를 가져오려면 클라이언트 채널을 만들 때 `GrpcChannelOptions.LoggerFactory` 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="a5708-143">ASP.NET Core 앱에서 gRPC 서비스를 호출하는 경우, DI(종속성 주입)에서 로거 팩터리를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="a5708-144">클라이언트 로깅을 사용하도록 설정하는 대체 방법은 [gRPC 클라이언트 팩터리](xref:grpc/clientfactory)를 사용하여 클라이언트를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="a5708-145">클라이언트 팩터리에 등록되고 DI에서 확인된 gRPC 클라이언트는 자동으로 앱의 구성된 로깅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="a5708-146">앱에서 DI를 사용하지 않는 경우, [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용하여 새 `ILoggerFactory` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="a5708-147">이 메서드에 액세스하려면 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="a5708-148">gRPC 클라이언트 로그 범위</span><span class="sxs-lookup"><span data-stu-id="a5708-148">gRPC client log scopes</span></span>

<span data-ttu-id="a5708-149">gRPC 클라이언트는 gRPC 호출 중에 생성된 로그에 [로깅 범위](../fundamentals/logging/index.md#log-scopes)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="a5708-150">범위에는 gRPC 호출과 관련된 메타데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="a5708-151">**GrpcMethodType** - gRPC 메서드 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="a5708-152">가능한 값은 `Grpc.Core.MethodType` 열거형의 이름입니다(예: 단항).</span><span class="sxs-lookup"><span data-stu-id="a5708-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="a5708-153">**GrpcUri** - gRPC 메서드의 상대 URI입니다(예: /greet.Greeter/SayHellos).</span><span class="sxs-lookup"><span data-stu-id="a5708-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="a5708-154">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="a5708-154">Sample logging output</span></span>

<span data-ttu-id="a5708-155">다음은 gRPC 클라이언트의 `Debug` 수준에서 표시되는 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="a5708-156">추적</span><span class="sxs-lookup"><span data-stu-id="a5708-156">Tracing</span></span>

<span data-ttu-id="a5708-157">gRPC 서비스와 gRPC 클라이언트는 [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) 및 [Activity](/dotnet/api/system.diagnostics.activity)를 사용하여 gRPC 호출에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="a5708-158">.NET gRPC는 작업을 사용하여 gRPC 호출을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="a5708-159">추적 이벤트는 gRPC 호출 작업을 시작하고 중지할 때 진단 소스에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="a5708-160">추적은 gRPC 스트리밍 호출의 수명 동안 메시지가 전송된 시기에 대한 정보를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="a5708-161">gRPC 서비스 추적</span><span class="sxs-lookup"><span data-stu-id="a5708-161">gRPC service tracing</span></span>

<span data-ttu-id="a5708-162">gRPC 서비스는 들어오는 HTTP 요청에 대한 이벤트를 보고하는 ASP.NET Core에서 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="a5708-163">ASP.NET Core에서 제공하는 기존 HTTP 요청 진단에 gRPC 특정 메타데이터가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="a5708-164">진단 소스 이름은 `Microsoft.AspNetCore`입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="a5708-165">작업 이름은 `Microsoft.AspNetCore.Hosting.HttpRequestIn`입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="a5708-166">gRPC 호출을 통해 호출된 gRPC 메서드 이름은 이름이 `grpc.method`인 태그로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="a5708-167">완료 시 gRPC 호출의 상태 코드는 이름이 `grpc.status_code`인 태그로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="a5708-168">gRPC 클라이언트 추적</span><span class="sxs-lookup"><span data-stu-id="a5708-168">gRPC client tracing</span></span>

<span data-ttu-id="a5708-169">.NET gRPC 클라이언트는 `HttpClient`를 사용하여 gRPC 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="a5708-170">`HttpClient`가 진단 이벤트를 기록하지만, .NET gRPC 클라이언트는 gRPC 호출에 대한 전체 정보를 수집할 수 있도록 사용자 지정 진단 소스, 작업 및 이벤트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="a5708-171">진단 소스 이름은 `Grpc.Net.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="a5708-172">작업 이름은 `Grpc.Net.Client.GrpcOut`입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="a5708-173">gRPC 호출을 통해 호출된 gRPC 메서드 이름은 이름이 `grpc.method`인 태그로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="a5708-174">완료 시 gRPC 호출의 상태 코드는 이름이 `grpc.status_code`인 태그로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="a5708-175">추적 수집</span><span class="sxs-lookup"><span data-stu-id="a5708-175">Collecting tracing</span></span>

<span data-ttu-id="a5708-176">`DiagnosticSource`를 사용하는 가장 간편한 방법은 앱에서 [Application Insights](/azure/azure-monitor/app/asp-net-core) 또는 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 원격 분석 라이브러리를 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="a5708-177">라이브러리는 다른 앱 원격 분석과 함께 gRPC 호출 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="a5708-178">Application Insights와 같은 관리형 서비스에서 추적을 볼 수 있으며, 고유한 분산 추적 시스템을 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="a5708-179">OpenTelemetry는 추적 데이터를 [Jaeger](https://www.jaegertracing.io/) 및 [Zipkin](https://zipkin.io/)으로 내보내는 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="a5708-180">`DiagnosticSource`는 `DiagnosticListener`를 통해 코드에서 추적 이벤트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="a5708-181">코드를 사용하여 진단 소스를 수신 대기하는 방법에 대한 자세한 내용은 [DiagnosticSource 사용자 가이드](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5708-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="a5708-182">원격 분석 라이브러리는 현재 gRPC 특정 `Grpc.Net.Client.GrpcOut` 원격 분석을 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="a5708-183">이 추적을 캡처하도록 원격 분석 라이브러리를 개선하기 위해 노력하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="a5708-184">메트릭</span><span class="sxs-lookup"><span data-stu-id="a5708-184">Metrics</span></span>

<span data-ttu-id="a5708-185">메트릭은 시간 간격에 따른 데이터 측정값 표시입니다(예: 초당 요청 수).</span><span class="sxs-lookup"><span data-stu-id="a5708-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="a5708-186">메트릭 데이터를 통해 앱 상태를 상위 수준에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="a5708-187">`EventCounter`를 사용하여 .NET gRPC 메트릭을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="a5708-188">gRPC 서비스 메트릭</span><span class="sxs-lookup"><span data-stu-id="a5708-188">gRPC service metrics</span></span>

<span data-ttu-id="a5708-189">gRPC 서버 메트릭은 `Grpc.AspNetCore.Server` 이벤트 원본에 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="a5708-190">이름</span><span class="sxs-lookup"><span data-stu-id="a5708-190">Name</span></span>                      | <span data-ttu-id="a5708-191">설명</span><span class="sxs-lookup"><span data-stu-id="a5708-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="a5708-192">총 호출</span><span class="sxs-lookup"><span data-stu-id="a5708-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="a5708-193">현재 호출 수</span><span class="sxs-lookup"><span data-stu-id="a5708-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="a5708-194">실패한 호출의 총수</span><span class="sxs-lookup"><span data-stu-id="a5708-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="a5708-195">최종 기한을 넘긴 호출의 총수</span><span class="sxs-lookup"><span data-stu-id="a5708-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="a5708-196">총 보낸 메시지</span><span class="sxs-lookup"><span data-stu-id="a5708-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="a5708-197">받은 총 메시지 수</span><span class="sxs-lookup"><span data-stu-id="a5708-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="a5708-198">구현되지 않은 호출의 총수</span><span class="sxs-lookup"><span data-stu-id="a5708-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="a5708-199">ASP.NET Core는 `Microsoft.AspNetCore.Hosting` 이벤트 원본에 대한 고유한 메트릭도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="a5708-200">gRPC 클라이언트 메트릭</span><span class="sxs-lookup"><span data-stu-id="a5708-200">gRPC client metrics</span></span>

<span data-ttu-id="a5708-201">gRPC 클라이언트 메트릭은 `Grpc.Net.Client` 이벤트 원본에 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="a5708-202">이름</span><span class="sxs-lookup"><span data-stu-id="a5708-202">Name</span></span>                      | <span data-ttu-id="a5708-203">설명</span><span class="sxs-lookup"><span data-stu-id="a5708-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="a5708-204">총 호출</span><span class="sxs-lookup"><span data-stu-id="a5708-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="a5708-205">현재 호출 수</span><span class="sxs-lookup"><span data-stu-id="a5708-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="a5708-206">실패한 호출의 총수</span><span class="sxs-lookup"><span data-stu-id="a5708-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="a5708-207">최종 기한을 넘긴 호출의 총수</span><span class="sxs-lookup"><span data-stu-id="a5708-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="a5708-208">총 보낸 메시지</span><span class="sxs-lookup"><span data-stu-id="a5708-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="a5708-209">받은 총 메시지 수</span><span class="sxs-lookup"><span data-stu-id="a5708-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="a5708-210">메트릭 확인</span><span class="sxs-lookup"><span data-stu-id="a5708-210">Observe metrics</span></span>

<span data-ttu-id="a5708-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters)는 임시 상태 모니터링 및 1단계 수준 성능 조사를 위한 성능 모니터링 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="a5708-212">공급자 이름이 `Grpc.AspNetCore.Server` 또는 `Grpc.Net.Client`인 .NET 앱을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="a5708-213">gRPC 메트릭을 확인하는 또 다른 방법은 Application Insights의 [Microsoft.ApplicationInsights.EventCounterCollector 패키지](/azure/azure-monitor/app/eventcounters)를 사용하여 카운터 데이터를 캡처하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="a5708-214">설정이 완료되면 Application Insights는 런타임에 일반적인 .NET 카운터를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="a5708-215">gRPC 카운터는 기본적으로 수집되지 않지만, [추가 카운터를 포함하도록 App Insights를 사용자 지정](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="a5708-216">*Startup.cs* 에서 수집할 Application Insight의 gRPC 카운터를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5708-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a5708-217">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5708-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>