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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="0916a-103">ASP.NET Core에서 호스팅되는 서비스를 사용하는 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="0916a-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="0916a-104">작성자: [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="0916a-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0916a-105">ASP.NET Core에서 백그라운드 작업은 *호스팅되는 서비스* 로 구현될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="0916a-106">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0916a-107">이 토픽에서는 세 가지 호스팅되는 서비스 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="0916a-108">타이머에서 실행되는 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="0916a-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="0916a-109">[범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 활성화하는 호스팅되는 서비스.</span><span class="sxs-lookup"><span data-stu-id="0916a-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0916a-110">범위가 지정된 서비스는 [종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="0916a-111">순차적으로 실행되는 대기 중인 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="0916a-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="0916a-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0916a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="0916a-113">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="0916a-113">Worker Service template</span></span>

<span data-ttu-id="0916a-114">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="0916a-115">Worker Service 템플릿에서 만든 앱은 프로젝트 파일에 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="0916a-116">템플릿을 호스팅되는 서비스 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="0916a-117">패키지</span><span class="sxs-lookup"><span data-stu-id="0916a-117">Package</span></span>

<span data-ttu-id="0916a-118">Worker Service 템플릿을 기반으로 하는 앱은 `Microsoft.NET.Sdk.Worker` SDK를 사용하고 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지에 대한 명시적 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="0916a-119">예를 들어 샘플 앱의 프로젝트 파일(*BackgroundTasksSample*)을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="0916a-120">`Microsoft.NET.Sdk.Web` SDK를 사용하는 웹앱의 경우, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지는 공유 프레임워크에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="0916a-121">앱의 프로젝트 파일에 있는 명시적 패키지 참조는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="0916a-122">IHostedService 인터페이스</span><span class="sxs-lookup"><span data-stu-id="0916a-122">IHostedService interface</span></span>

<span data-ttu-id="0916a-123"><xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스는 호스트에 의해 관리되는 개체에 대한 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="0916a-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync`에는 백그라운드 작업을 시작하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="0916a-125">다음 상황 *이전* 에 `StartAsync`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="0916a-126">앱의 요청 처리 파이프라인이 구성됨(`Startup.Configure`)</span><span class="sxs-lookup"><span data-stu-id="0916a-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="0916a-127">서버가 시작되고 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)가 트리거됨</span><span class="sxs-lookup"><span data-stu-id="0916a-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="0916a-128">앱의 파이프라인이 구성되고 `ApplicationStarted`가 호출된 후에 호스팅된 서비스의 `StartAsync`가 실행되도록 기본 동작을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="0916a-129">기본 동작을 변경하려면 `ConfigureWebHostDefaults`를 호출한 후에 호스팅된 서비스(다음 예제에서 `VideosWatcher`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="0916a-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): 호스트가 정상적으로 종료될 때 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="0916a-131">`StopAsync`에는 백그라운드 작업을 종료하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="0916a-132">관리되지 않는 리소스를 삭제하려면 <xref:System.IDisposable> 및 [종료자(소멸자)](/dotnet/csharp/programming-guide/classes-and-structs/destructors)를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="0916a-133">취소 토큰에는 종료 프로세스가 더 이상 정상화되지 않아야 함을 나타내는 기본 5초 시간 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="0916a-134">토큰에 취소가 요청된 경우:</span><span class="sxs-lookup"><span data-stu-id="0916a-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="0916a-135">앱이 수행하는 나머지 백그라운드 작업은 중단해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="0916a-136">`StopAsync`에 호출된 모든 메서드는 즉시 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="0916a-137">그러나 취소가 요청된 후 &mdash; 호출자가 모든 작업을 완료될 때까지 작업을 취소하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="0916a-138">앱이 예기치 않게 종료된 경우(예: 앱의 프로세스가 실패한 경우), `StopAsync`가 호출되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="0916a-139">따라서 메서드 호출이나 `StopAsync`에서 수행된 작업이 발생하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="0916a-140">기본 5초 시스템 종료 시간 제한을 연장하려면 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="0916a-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 일반 호스트를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="0916a-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="0916a-142">자세한 내용은 <xref:fundamentals/host/generic-host#shutdown-timeout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="0916a-143">웹 호스트를 사용하는 경우 시스템 종료 시간 제한 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="0916a-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="0916a-144">자세한 내용은 <xref:fundamentals/host/web-host#shutdown-timeout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="0916a-145">호스팅되는 서비스는 앱 시작 시 한 번 활성화되고 앱 종료 시 정상적으로 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="0916a-146">백그라운드 작업을 실행하는 동안 오류가 발생하면 `StopAsync`가 호출되지 않아도 `Dispose`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="0916a-147">BackgroundService 기본 클래스</span><span class="sxs-lookup"><span data-stu-id="0916a-147">BackgroundService base class</span></span>

<span data-ttu-id="0916a-148"><xref:Microsoft.Extensions.Hosting.BackgroundService>는 장기 실행 <xref:Microsoft.Extensions.Hosting.IHostedService>를 구현하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="0916a-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*)은 백그라운드 서비스를 실행하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="0916a-150">구현하면 백그라운드 서비스의 전체 수명을 나타내는 <xref:System.Threading.Tasks.Task>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="0916a-151">`await`를 호출하는 것처럼 [ExecuteAsync가 비동기가 될 때까지](https://github.com/dotnet/extensions/issues/2149) 추가 서비스가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="0916a-152">`ExecuteAsync`에서 긴 초기화 작업을 차단하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="0916a-153">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*)의 호스트 블록은 `ExecuteAsync`가 완료될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="0916a-154">취소 토큰은 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)가 호출되면 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="0916a-155">서비스를 정상적으로 종료하기 위해 취소 토큰이 발생하면 `ExecuteAsync` 구현이 즉시 완료되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="0916a-156">그렇지 않으면 서비스가 종료 시간 제한에 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="0916a-157">자세한 내용은 [IHostedService 인터페이스](#ihostedservice-interface) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="0916a-158">시간이 지정된 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="0916a-158">Timed background tasks</span></span>

<span data-ttu-id="0916a-159">시간이 지정된 백그라운드 작업은 [System.Threading.Timer](xref:System.Threading.Timer) 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="0916a-160">타이머가 작업의 `DoWork` 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="0916a-161">서비스 컨테이너가 `Dispose`에서 삭제될 때 `StopAsync`에서 타이머가 비활성화되고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="0916a-162"><xref:System.Threading.Timer>는 이전에 실행된 `DoWork`를 마칠 때까지 기다리지 않으므로 제시된 방법이 모든 시나리오에 적합한 것은 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="0916a-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*)는 여러 스레드가 동시에 `executionCount`를 업데이트하지 않도록 하는 원자성 작업으로 실행 카운터를 증가시키는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="0916a-164">서비스는 `AddHostedService` 확장 메서드를 사용하여 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="0916a-165">백그라운드 작업에서 범위가 지정된 서비스 사용</span><span class="sxs-lookup"><span data-stu-id="0916a-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="0916a-166">[BackgroundService](#backgroundservice-base-class) 내에서 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 사용하려면 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="0916a-167">기본적으로 호스팅되는 서비스에 대한 범위는 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="0916a-168">범위가 지정된 백그라운드 작업 서비스에는 백그라운드 작업의 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="0916a-169">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="0916a-169">In the following example:</span></span>

* <span data-ttu-id="0916a-170">서비스는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-170">The service is asynchronous.</span></span> <span data-ttu-id="0916a-171">`DoWork` 메서드는 `Task`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="0916a-172">데모용으로 `DoWork` 메서드에서 10초의 지연이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="0916a-173"><xref:Microsoft.Extensions.Logging.ILogger>가 서비스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="0916a-174">호스팅되는 서비스는 범위가 지정된 백그라운드 작업 서비스를 해결하여 `DoWork` 메서드를 호출하는 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="0916a-175">`DoWork`는 `ExecuteAsync`에서 대기하고 있는 `Task`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="0916a-176">서비스는 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="0916a-177">호스팅된 서비스는 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="0916a-178">대기 중인 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="0916a-178">Queued background tasks</span></span>

<span data-ttu-id="0916a-179">백그라운드 작업 큐는 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="0916a-180">다음 `QueueHostedService` 예제에서는</span><span class="sxs-lookup"><span data-stu-id="0916a-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="0916a-181">`BackgroundProcessing` 메서드는 `ExecuteAsync`에서 대기하고 있는 `Task`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="0916a-182">`BackgroundProcessing`에서 큐의 백그라운드 작업이 큐에서 제거되고 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="0916a-183">작업 항목은 `StopAsync`에서 서비스가 중지되기 전에 대기 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="0916a-184">입력 장치에서 `w` 키가 선택될 때마다 `MonitorLoop` 서비스가 호스팅된 서비스에 대해 큐에 넣는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="0916a-185">`IBackgroundTaskQueue`가 `MonitorLoop` 서비스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="0916a-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem`이 호출되어 작업 항목을 큐에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="0916a-187">작업 항목은 장기 실행 백그라운드 작업을 시뮬레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="0916a-188">세 번의 5초 지연이 실행됩니다(`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="0916a-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="0916a-189">작업이 취소되면 `try-catch` 문이 <xref:System.OperationCanceledException>을 트래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="0916a-190">서비스는 `IHostBuilder.ConfigureServices`(*Program.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="0916a-191">호스팅된 서비스는 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="0916a-192">`MonitorLoop`는 `Program.Main`에서 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-192">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0916a-193">ASP.NET Core에서 백그라운드 작업은 *호스팅되는 서비스* 로 구현될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="0916a-194">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0916a-195">이 토픽에서는 세 가지 호스팅되는 서비스 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="0916a-196">타이머에서 실행되는 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="0916a-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="0916a-197">[범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 활성화하는 호스팅되는 서비스.</span><span class="sxs-lookup"><span data-stu-id="0916a-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0916a-198">범위가 지정된 서비스는 [종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="0916a-199">순차적으로 실행되는 대기 중인 백그라운드 작업.</span><span class="sxs-lookup"><span data-stu-id="0916a-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="0916a-200">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0916a-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="0916a-201">패키지</span><span class="sxs-lookup"><span data-stu-id="0916a-201">Package</span></span>

<span data-ttu-id="0916a-202">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="0916a-203">IHostedService 인터페이스</span><span class="sxs-lookup"><span data-stu-id="0916a-203">IHostedService interface</span></span>

<span data-ttu-id="0916a-204">호스팅되는 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0916a-205">인터페이스는 호스트에 의해 관리되는 개체에 대한 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="0916a-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync`에는 백그라운드 작업을 시작하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="0916a-207">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 `StartAsync`는 서버가 시작되고 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)가 트리거된 후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="0916a-208">[제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우 `ApplicationStarted`가 트리거되기 전에 `StartAsync`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="0916a-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): 호스트가 정상적으로 종료될 때 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="0916a-210">`StopAsync`에는 백그라운드 작업을 종료하는 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="0916a-211">관리되지 않는 리소스를 삭제하려면 <xref:System.IDisposable> 및 [종료자(소멸자)](/dotnet/csharp/programming-guide/classes-and-structs/destructors)를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="0916a-212">취소 토큰에는 종료 프로세스가 더 이상 정상화되지 않아야 함을 나타내는 기본 5초 시간 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="0916a-213">토큰에 취소가 요청된 경우:</span><span class="sxs-lookup"><span data-stu-id="0916a-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="0916a-214">앱이 수행하는 나머지 백그라운드 작업은 중단해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="0916a-215">`StopAsync`에 호출된 모든 메서드는 즉시 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="0916a-216">그러나 취소가 요청된 후 &mdash; 호출자가 모든 작업을 완료될 때까지 작업을 취소하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="0916a-217">앱이 예기치 않게 종료된 경우(예: 앱의 프로세스가 실패한 경우), `StopAsync`가 호출되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="0916a-218">따라서 메서드 호출이나 `StopAsync`에서 수행된 작업이 발생하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="0916a-219">기본 5초 시스템 종료 시간 제한을 연장하려면 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="0916a-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 일반 호스트를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="0916a-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="0916a-221">자세한 내용은 <xref:fundamentals/host/generic-host#shutdown-timeout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="0916a-222">웹 호스트를 사용하는 경우 시스템 종료 시간 제한 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="0916a-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="0916a-223">자세한 내용은 <xref:fundamentals/host/web-host#shutdown-timeout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0916a-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="0916a-224">호스팅되는 서비스는 앱 시작 시 한 번 활성화되고 앱 종료 시 정상적으로 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="0916a-225">백그라운드 작업을 실행하는 동안 오류가 발생하면 `StopAsync`가 호출되지 않아도 `Dispose`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="0916a-226">시간이 지정된 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="0916a-226">Timed background tasks</span></span>

<span data-ttu-id="0916a-227">시간이 지정된 백그라운드 작업은 [System.Threading.Timer](xref:System.Threading.Timer) 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="0916a-228">타이머가 작업의 `DoWork` 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="0916a-229">서비스 컨테이너가 `Dispose`에서 삭제될 때 `StopAsync`에서 타이머가 비활성화되고 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="0916a-230"><xref:System.Threading.Timer>는 이전에 실행된 `DoWork`를 마칠 때까지 기다리지 않으므로 제시된 방법이 모든 시나리오에 적합한 것은 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="0916a-231">서비스는 `AddHostedService` 확장 메서드를 사용하여 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="0916a-232">백그라운드 작업에서 범위가 지정된 서비스 사용</span><span class="sxs-lookup"><span data-stu-id="0916a-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="0916a-233">`IHostedService` 내에서 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes)를 사용하려면 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="0916a-234">기본적으로 호스팅되는 서비스에 대한 범위는 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="0916a-235">범위가 지정된 백그라운드 작업 서비스에는 백그라운드 작업의 논리가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="0916a-236">다음 예제에서는 <xref:Microsoft.Extensions.Logging.ILogger>가 서비스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="0916a-237">호스팅되는 서비스는 범위가 지정된 백그라운드 작업 서비스를 해결하여 `DoWork` 메서드를 호출하는 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="0916a-238">서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0916a-239">`IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="0916a-240">대기 중인 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="0916a-240">Queued background tasks</span></span>

<span data-ttu-id="0916a-241">백그라운드 작업 큐는 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>([ASP.NET Core용으로 기본 제공될 예정](https://github.com/aspnet/Hosting/issues/1280))을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="0916a-242">`QueueHostedService`에서 큐의 백그라운드 작업은 큐에서 제거되고 장기 실행 `IHostedService`를 구현하기 위한 기본 클래스인 [BackgroundService](#backgroundservice-base-class)로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="0916a-243">서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0916a-244">`IHostedService` 구현은 `AddHostedService` 확장 메서드를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="0916a-245">인덱스 페이지 모델 클래스에서:</span><span class="sxs-lookup"><span data-stu-id="0916a-245">In the Index page model class:</span></span>

* <span data-ttu-id="0916a-246">`IBackgroundTaskQueue`는 생성자에 삽입되고 `Queue`에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="0916a-247"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>가 삽입되어 `_serviceScopeFactory`에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="0916a-248">팩터리는 범위 내에서 서비스를 만드는 데 사용되는 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>의 인스턴스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="0916a-249">앱의 `AppDbContext`([범위가 지정된 서비스](xref:fundamentals/dependency-injection#service-lifetimes))를 사용하여 `IBackgroundTaskQueue`(싱글톤 서비스)에 데이터베이스 레코드를 작성하는 범위가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0916a-250">인덱스 페이지에서 **작업 추가** 단추를 선택하면 `OnPostAddTask` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="0916a-251">`QueueBackgroundWorkItem`이 호출되어 작업 항목을 큐에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="0916a-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0916a-252">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="0916a-252">Additional resources</span></span>

* [<span data-ttu-id="0916a-253">IHostedService 및 BackgroundService 클래스를 사용하여 마이크로 서비스에서 백그라운드 작업 구현</span><span class="sxs-lookup"><span data-stu-id="0916a-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="0916a-254">Azure App Service에서 WebJobs로 백그라운드 작업 실행</span><span class="sxs-lookup"><span data-stu-id="0916a-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
