---
title: ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업
author: rick-anderson
description: ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업을 구현하는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c0492c0c5b660e1387b0d0a4f6be405ded49ee92
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587478"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업

작성자: [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core에서 백그라운드 작업은 *호스팅되는 서비스* 로 구현될 수 있습니다. 호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다. 이 토픽에서는 세 가지 호스팅되는 서비스 예제를 제공합니다.

* 타이머에서 실행되는 백그라운드 작업.
* [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 활성화하는 호스팅되는 서비스. 범위가 지정된 서비스는 [종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용할 수 있습니다.
* 순차적으로 실행되는 대기 중인 백그라운드 작업.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Worker Service 템플릿

ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다. Worker Service 템플릿에서 만든 앱은 프로젝트 파일에 작업자 SDK를 지정합니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

템플릿을 호스팅되는 서비스 앱의 기반으로 사용하려면 다음을 수행합니다.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>패키지

Worker Service 템플릿을 기반으로 하는 앱은 `Microsoft.NET.Sdk.Worker` SDK를 사용하고 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지에 대한 명시적 패키지 참조가 있습니다. 예를 들어 샘플 앱의 프로젝트 파일(*BackgroundTasksSample*)을 확인하세요.

`Microsoft.NET.Sdk.Web` SDK를 사용하는 웹앱의 경우, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지는 공유 프레임워크에서 암시적으로 참조됩니다. 앱의 프로젝트 파일에 있는 명시적 패키지 참조는 필요하지 않습니다.

## <a name="ihostedservice-interface"></a>IHostedService 인터페이스

<xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스는 호스트에 의해 관리되는 개체에 대한 두 가지 메서드를 정의합니다.

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync`에는 백그라운드 작업을 시작하는 논리가 포함됩니다. 다음 상황 *이전* 에 `StartAsync`가 호출됩니다.

  * 앱의 요청 처리 파이프라인이 구성됨(`Startup.Configure`)
  * 서버가 시작되고 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)가 트리거됨

  앱의 파이프라인이 구성되고 `ApplicationStarted`가 호출된 후에 호스팅된 서비스의 `StartAsync`가 실행되도록 기본 동작을 변경할 수 있습니다. 기본 동작을 변경하려면 `ConfigureWebHostDefaults`를 호출한 후에 호스팅된 서비스(다음 예제에서 `VideosWatcher`)를 추가합니다.

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): 호스트가 정상적으로 종료될 때 트리거됩니다. `StopAsync`에는 백그라운드 작업을 종료하는 논리가 포함됩니다. 관리되지 않는 리소스를 삭제하려면 <xref:System.IDisposable> 및 [종료자(소멸자)](/dotnet/csharp/programming-guide/classes-and-structs/destructors)를 구현합니다.

  취소 토큰에는 종료 프로세스가 더 이상 정상화되지 않아야 함을 나타내는 기본 5초 시간 제한이 있습니다. 토큰에 취소가 요청된 경우:

  * 앱이 수행하는 나머지 백그라운드 작업은 중단해야 합니다.
  * `StopAsync`에 호출된 모든 메서드는 즉시 반환해야 합니다.

  그러나 취소가 요청된 후 &mdash; 호출자가 모든 작업을 완료될 때까지 작업을 취소하지 않습니다.

  앱이 예기치 않게 종료된 경우(예: 앱의 프로세스가 실패한 경우), `StopAsync`가 호출되지 않을 수 있습니다. 따라서 메서드 호출이나 `StopAsync`에서 수행된 작업이 발생하지 않을 수 있습니다.

  기본 5초 시스템 종료 시간 제한을 연장하려면 다음을 설정합니다.

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 일반 호스트를 사용하는 경우 자세한 내용은 <xref:fundamentals/host/generic-host#shutdown-timeout>를 참조하세요.
  * 웹 호스트를 사용하는 경우 시스템 종료 시간 제한 호스트 구성 설정. 자세한 내용은 <xref:fundamentals/host/web-host#shutdown-timeout>를 참조하세요.

호스팅되는 서비스는 앱 시작 시 한 번 활성화되고 앱 종료 시 정상적으로 종료됩니다. 백그라운드 작업을 실행하는 동안 오류가 발생하면 `StopAsync`가 호출되지 않아도 `Dispose`를 호출해야 합니다.

## <a name="backgroundservice-base-class"></a>BackgroundService 기본 클래스

<xref:Microsoft.Extensions.Hosting.BackgroundService>는 장기 실행 <xref:Microsoft.Extensions.Hosting.IHostedService>를 구현하기 위한 기본 클래스입니다.

[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*)은 백그라운드 서비스를 실행하기 위해 호출됩니다. 구현하면 백그라운드 서비스의 전체 수명을 나타내는 <xref:System.Threading.Tasks.Task>가 반환됩니다. `await`를 호출하는 것처럼 [ExecuteAsync가 비동기가 될 때까지](https://github.com/dotnet/extensions/issues/2149) 추가 서비스가 시작되지 않습니다. `ExecuteAsync`에서 긴 초기화 작업을 차단하는 것을 방지합니다. [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*)의 호스트 블록은 `ExecuteAsync`가 완료될 때까지 대기합니다.

취소 토큰은 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)가 호출되면 트리거됩니다. 서비스를 정상적으로 종료하기 위해 취소 토큰이 발생하면 `ExecuteAsync` 구현이 즉시 완료되어야 합니다. 그렇지 않으면 서비스가 종료 시간 제한에 종료됩니다. 자세한 내용은 [IHostedService 인터페이스](#ihostedservice-interface) 섹션을 참조하세요.

## <a name="timed-background-tasks"></a>시간이 지정된 백그라운드 작업

시간이 지정된 백그라운드 작업은 [System.Threading.Timer](xref:System.Threading.Timer) 클래스를 사용합니다. 타이머가 작업의 `DoWork` 메서드를 트리거합니다. 서비스 컨테이너가 `Dispose`에서 삭제될 때 `StopAsync`에서 타이머가 비활성화되고 삭제됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer>는 이전에 실행된 `DoWork`를 마칠 때까지 기다리지 않으므로 제시된 방법이 모든 시나리오에 적합한 것은 아닐 수 있습니다. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*)는 여러 스레드가 동시에 `executionCount`를 업데이트하지 않도록 하는 원자성 작업으로 실행 카운터를 증가시키는 데 사용됩니다.

서비스는 `AddHostedService` 확장 메서드를 사용하여 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>백그라운드 작업에서 범위가 지정된 서비스 사용

[BackgroundService](#backgroundservice-base-class) 내에서 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 사용하려면 범위를 만듭니다. 기본적으로 호스팅되는 서비스에 대한 범위는 생성되지 않습니다.

범위가 지정된 백그라운드 작업 서비스에는 백그라운드 작업의 논리가 포함됩니다. 다음 예제에서는

* 서비스는 비동기입니다. `DoWork` 메서드는 `Task`를 반환합니다. 데모용으로 `DoWork` 메서드에서 10초의 지연이 있습니다.
* <xref:Microsoft.Extensions.Logging.ILogger>가 서비스에 삽입됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

호스팅되는 서비스는 범위가 지정된 백그라운드 작업 서비스를 해결하여 `DoWork` 메서드를 호출하는 범위를 만듭니다. `DoWork`는 `ExecuteAsync`에서 대기하고 있는 `Task`를 반환합니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

서비스는 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다. 호스팅된 서비스는 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>대기 중인 백그라운드 작업

백그라운드 작업 큐는 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>을 기반으로 합니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

다음 `QueueHostedService` 예제에서는

* `BackgroundProcessing` 메서드는 `ExecuteAsync`에서 대기하고 있는 `Task`를 반환합니다.
* `BackgroundProcessing`에서 큐의 백그라운드 작업이 큐에서 제거되고 실행됩니다.
* 작업 항목은 `StopAsync`에서 서비스가 중지되기 전에 대기 상태입니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

입력 장치에서 `w` 키가 선택될 때마다 `MonitorLoop` 서비스가 호스팅된 서비스에 대해 큐에 넣는 작업을 처리합니다.

* `IBackgroundTaskQueue`가 `MonitorLoop` 서비스에 삽입됩니다.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`이 호출되어 작업 항목을 큐에 넣습니다.
* 작업 항목은 장기 실행 백그라운드 작업을 시뮬레이션합니다.
  * 세 번의 5초 지연이 실행됩니다(`Task.Delay`).
  * 작업이 취소되면 `try-catch` 문이 <xref:System.OperationCanceledException>을 트래핑합니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

서비스는 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다. 호스팅된 서비스는 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop`는 `Program.Main`에서 시작됩니다.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core에서 백그라운드 작업은 *호스팅되는 서비스* 로 구현될 수 있습니다. 호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다. 이 토픽에서는 세 가지 호스팅되는 서비스 예제를 제공합니다.

* 타이머에서 실행되는 백그라운드 작업.
* [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 활성화하는 호스팅되는 서비스. 범위가 지정된 서비스는 [종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용할 수 있습니다.
* 순차적으로 실행되는 대기 중인 백그라운드 작업.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="package"></a>패키지

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지에 대한 패키지 참조를 추가합니다.

## <a name="ihostedservice-interface"></a>IHostedService 인터페이스

호스팅되는 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현합니다. 인터페이스는 호스트에 의해 관리되는 개체에 대한 두 가지 메서드를 정의합니다.

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync`에는 백그라운드 작업을 시작하는 논리가 포함됩니다. [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 `StartAsync`는 서버가 시작되고 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)가 트리거된 후에 호출됩니다. [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우 `ApplicationStarted`가 트리거되기 전에 `StartAsync`가 호출됩니다.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): 호스트가 정상적으로 종료될 때 트리거됩니다. `StopAsync`에는 백그라운드 작업을 종료하는 논리가 포함됩니다. 관리되지 않는 리소스를 삭제하려면 <xref:System.IDisposable> 및 [종료자(소멸자)](/dotnet/csharp/programming-guide/classes-and-structs/destructors)를 구현합니다.

  취소 토큰에는 종료 프로세스가 더 이상 정상화되지 않아야 함을 나타내는 기본 5초 시간 제한이 있습니다. 토큰에 취소가 요청된 경우:

  * 앱이 수행하는 나머지 백그라운드 작업은 중단해야 합니다.
  * `StopAsync`에 호출된 모든 메서드는 즉시 반환해야 합니다.

  그러나 취소가 요청된 후 &mdash; 호출자가 모든 작업을 완료될 때까지 작업을 취소하지 않습니다.

  앱이 예기치 않게 종료된 경우(예: 앱의 프로세스가 실패한 경우), `StopAsync`가 호출되지 않을 수 있습니다. 따라서 메서드 호출이나 `StopAsync`에서 수행된 작업이 발생하지 않을 수 있습니다.

  기본 5초 시스템 종료 시간 제한을 연장하려면 다음을 설정합니다.

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 일반 호스트를 사용하는 경우 자세한 내용은 <xref:fundamentals/host/generic-host#shutdown-timeout>를 참조하세요.
  * 웹 호스트를 사용하는 경우 시스템 종료 시간 제한 호스트 구성 설정. 자세한 내용은 <xref:fundamentals/host/web-host#shutdown-timeout>를 참조하세요.

호스팅되는 서비스는 앱 시작 시 한 번 활성화되고 앱 종료 시 정상적으로 종료됩니다. 백그라운드 작업을 실행하는 동안 오류가 발생하면 `StopAsync`가 호출되지 않아도 `Dispose`를 호출해야 합니다.

## <a name="timed-background-tasks"></a>시간이 지정된 백그라운드 작업

시간이 지정된 백그라운드 작업은 [System.Threading.Timer](xref:System.Threading.Timer) 클래스를 사용합니다. 타이머가 작업의 `DoWork` 메서드를 트리거합니다. 서비스 컨테이너가 `Dispose`에서 삭제될 때 `StopAsync`에서 타이머가 비활성화되고 삭제됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer>는 이전에 실행된 `DoWork`를 마칠 때까지 기다리지 않으므로 제시된 방법이 모든 시나리오에 적합한 것은 아닐 수 있습니다.

서비스는 `AddHostedService` 확장 메서드를 사용하여 `Startup.ConfigureServices`에 등록됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>백그라운드 작업에서 범위가 지정된 서비스 사용

`IHostedService` 내에서 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 사용하려면 범위를 만듭니다. 기본적으로 호스팅되는 서비스에 대한 범위는 생성되지 않습니다.

범위가 지정된 백그라운드 작업 서비스에는 백그라운드 작업의 논리가 포함됩니다. 다음 예제에서는 <xref:Microsoft.Extensions.Logging.ILogger>가 서비스에 삽입됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

호스팅되는 서비스는 범위가 지정된 백그라운드 작업 서비스를 해결하여 `DoWork` 메서드를 호출하는 범위를 만듭니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

서비스는 `Startup.ConfigureServices`에 등록됩니다. `IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>대기 중인 백그라운드 작업

백그라운드 작업 큐는 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>([ASP.NET Core용으로 기본 제공될 예정](https://github.com/aspnet/Hosting/issues/1280))을 기반으로 합니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

`QueueHostedService`에서 큐의 백그라운드 작업은 큐에서 제거되고 장기 실행 `IHostedService`를 구현하기 위한 기본 클래스인 [BackgroundService](#backgroundservice-base-class)로 실행됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

서비스는 `Startup.ConfigureServices`에 등록됩니다. `IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

인덱스 페이지 모델 클래스에서:

* `IBackgroundTaskQueue`는 생성자에 삽입되고 `Queue`에 할당됩니다.
* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>가 삽입되어 `_serviceScopeFactory`에 할당됩니다. 팩터리는 범위 내에서 서비스를 만드는 데 사용되는 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>의 인스턴스를 만드는 데 사용됩니다. 앱의 `AppDbContext`([범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes))를 사용하여 `IBackgroundTaskQueue`(싱글톤 서비스)에 데이터베이스 레코드를 작성하는 범위가 생성됩니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

인덱스 페이지에서 **작업 추가** 단추를 선택하면 `OnPostAddTask` 메서드가 실행됩니다. `QueueBackgroundWorkItem`이 호출되어 작업 항목을 큐에 넣습니다.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [IHostedService 및 BackgroundService 클래스를 사용하여 마이크로 서비스에서 백그라운드 작업 구현](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Azure App Service에서 WebJobs로 백그라운드 작업 실행](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
