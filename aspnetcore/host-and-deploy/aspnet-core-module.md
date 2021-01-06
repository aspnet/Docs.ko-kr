---
title: ASP.NET Core 모듈
author: rick-anderson
description: IIS를 사용하여 ASP.NET Core 앱을 호스트하기 위한 ASP.NET Core 모듈에 관해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901238"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="b0af3-103">ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b0af3-103">ASP.NET Core Module</span></span>

<span data-ttu-id="b0af3-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="b0af3-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b0af3-105">ASP.NET Core 모듈은 IIS 파이프라인에 연결되어 ASP.NET Core 애플리케이션이 IIS에서 작동할 수 있도록 하는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="b0af3-106">다음 중 하나를 사용하여 IIS에서 ASP.NET Core 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="b0af3-107">IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In Process 호스팅 모델](xref:host-and-deploy/iis/in-process-hosting)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="b0af3-108">Kestrel 서버를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out of Process 호스팅 모델](xref:host-and-deploy/iis/out-of-process-hosting)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="b0af3-109">각 호스팅 모델에는 장단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="b0af3-110">기본적으로 더 나은 성능 및 진단 기능으로 인해 In Process 호스팅 모델이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="b0af3-111">ASP.NET Core 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="b0af3-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="b0af3-112">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="b0af3-113">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="b0af3-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="b0af3-114">ASP.NET Core 모듈을 설치하거나 다양한 버전의 모듈을 설치하는 방법에 관한 자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="b0af3-115">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b0af3-116">ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="b0af3-117">IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="b0af3-118">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="b0af3-119">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="b0af3-119">Supported Windows versions:</span></span>

* <span data-ttu-id="b0af3-120">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-120">Windows 7 or later</span></span>
* <span data-ttu-id="b0af3-121">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="b0af3-122">In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b0af3-123">Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="b0af3-124">이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b0af3-125">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b0af3-126">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-126">In-process hosting model</span></span>

<span data-ttu-id="b0af3-127">ASP.NET Core 앱의 기본값은 In-process 호스팅 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="b0af3-128">다음 특성은 In-Process로 호스팅할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="b0af3-129">IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b0af3-130">In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="b0af3-131">`IISHttpServer`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="b0af3-132">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="b0af3-133">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="b0af3-134">[requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="b0af3-135">앱 간의 앱 풀 공유는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="b0af3-136">앱당 하나의 앱 풀을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-136">Use one app pool per app.</span></span>

* <span data-ttu-id="b0af3-137">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [`app_offline.htm` 파일을 배포](xref:host-and-deploy/iis/index#locked-deployment-files)에 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b0af3-138">예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="b0af3-139">앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="b0af3-140">클라이언트의 연결 끊김이 검색되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-140">Client disconnects are detected.</span></span> <span data-ttu-id="b0af3-141">클라이언트의 연결이 끊어지면 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="b0af3-142">ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: `w3wp.exe`에 대한 `C:\Windows\System32\inetsrv`).</span><span class="sxs-lookup"><span data-stu-id="b0af3-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="b0af3-143">앱의 현재 디렉터리를 설정하는 샘플 코드는 [`CurrentDirectoryHelpers` 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="b0af3-144">`SetCurrentDirectory` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="b0af3-145"><xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="b0af3-146">In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="b0af3-147">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="b0af3-148"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="b0af3-149">[웹 패키지(단일 파일) 배포](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b0af3-150">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-150">Out-of-process hosting model</span></span>

<span data-ttu-id="b0af3-151">Out-of-Process 호스팅을 위해 앱을 구성하려면 프로젝트 파일( `.csproj`)에서 `<AspNetCoreHostingModel>` 속성의 값을 `OutOfProcess`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b0af3-152">In-Process 호스팅은 기본값 `InProcess`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="b0af3-153">`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b0af3-154">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b0af3-155">Out of Process의 경우 [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="b0af3-156">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="b0af3-157">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="b0af3-158">호스팅 모델 변경</span><span class="sxs-lookup"><span data-stu-id="b0af3-158">Hosting model changes</span></span>

<span data-ttu-id="b0af3-159">`hostingModel` 설정이 `web.config` 파일에서 변경되면([`web.config`로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="b0af3-160">IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="b0af3-161">앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="b0af3-162">프로세스 이름</span><span class="sxs-lookup"><span data-stu-id="b0af3-162">Process name</span></span>

<span data-ttu-id="b0af3-163">`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="b0af3-164">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b0af3-165">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b0af3-166">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b0af3-167">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b0af3-168">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b0af3-169">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b0af3-170">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="b0af3-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b0af3-171">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b0af3-172">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="b0af3-172">Configuration with web.config</span></span>

<span data-ttu-id="b0af3-173">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b0af3-174">다음 `web.config` 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b0af3-175">다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b0af3-176"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="b0af3-177">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-178">이 경로는 서비스에서 자동으로 만들어진 위치인 `LogFiles` 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b0af3-179">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b0af3-180">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b0af3-181">특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-181">Attribute</span></span> | <span data-ttu-id="b0af3-182">설명</span><span class="sxs-lookup"><span data-stu-id="b0af3-182">Description</span></span> | <span data-ttu-id="b0af3-183">기본값</span><span class="sxs-lookup"><span data-stu-id="b0af3-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b0af3-184">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-184">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-185">**processPath** 에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b0af3-186">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-187">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b0af3-188">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-189">true인 경우 토큰은 `%ASPNETCORE_PORT%`에서 수신 대기하는 자식 프로세스에 요청별 헤더 `'MS-ASPNETCORE-WINAUTHTOKEN'`으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="b0af3-190">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="b0af3-191">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-191">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-192">호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="b0af3-193">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-194">앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b0af3-195">&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="b0af3-196">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b0af3-197">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b0af3-198">기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-198">Default: `1`</span></span><br><span data-ttu-id="b0af3-199">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-199">Min: `1`</span></span><br><span data-ttu-id="b0af3-200">최대: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="b0af3-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="b0af3-201">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-201">Required string attribute.</span></span></p><p><span data-ttu-id="b0af3-202">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b0af3-203">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-203">Relative paths are supported.</span></span> <span data-ttu-id="b0af3-204">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b0af3-205">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-206">**processPath** 에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b0af3-207">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="b0af3-208">In-Process 호스팅에서는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="b0af3-209">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-209">Default: `10`</span></span><br><span data-ttu-id="b0af3-210">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-210">Min: `0`</span></span><br><span data-ttu-id="b0af3-211">최대: `100`</span><span class="sxs-lookup"><span data-stu-id="b0af3-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b0af3-212">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b0af3-213">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b0af3-214">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="b0af3-215">In-Process 호스팅에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="b0af3-216">In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="b0af3-217">문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="b0af3-218">분 또는 초의 값에 **60** 을 사용하면 *500 - 내부 서버 오류* 가 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="b0af3-219">기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-219">Default: `00:02:00`</span></span><br><span data-ttu-id="b0af3-220">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-220">Min: `00:00:00`</span></span><br><span data-ttu-id="b0af3-221">최대: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b0af3-222">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-223">*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b0af3-224">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-224">Default: `10`</span></span><br><span data-ttu-id="b0af3-225">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-225">Min: `0`</span></span><br><span data-ttu-id="b0af3-226">최대: `600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b0af3-227">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-228">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b0af3-229">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="b0af3-230">*In Process* 를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** **rapidFailsPerMinute** 설정을 사용하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="b0af3-231">*Out-of-Process* 를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute** 번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b0af3-232">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b0af3-233">기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="b0af3-233">Default: `120`</span></span><br><span data-ttu-id="b0af3-234">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-234">Min: `0`</span></span><br><span data-ttu-id="b0af3-235">최대: `3600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b0af3-236">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-237">true인 경우 **processPath** 에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b0af3-238">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-238">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-239">**processPath** 에 지정된 프로세스에서 **stdout** 및 **stderr** 이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b0af3-240">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b0af3-241">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b0af3-242">경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-243">타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b0af3-244">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="b0af3-245">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="b0af3-245">Set environment variables</span></span>

<span data-ttu-id="b0af3-246">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b0af3-247">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="b0af3-248">이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="b0af3-249">다음 예제에서는 `web.config`에서 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="b0af3-250">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b0af3-251">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 `web.config` 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b0af3-252">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="b0af3-253">`web.config`에서 환경을 직접 설정하는 대안으로 [게시 프로필(`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="b0af3-254">이 방법은 프로젝트가 게시될 때 `web.config`에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="b0af3-255">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="b0af3-256">응용 프로그램의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b0af3-257">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b0af3-258">`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="b0af3-259">`app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="b0af3-260">Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b0af3-261">예를 들어 WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b0af3-262">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="b0af3-262">Start-up error page</span></span>

<span data-ttu-id="b0af3-263">In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="b0af3-264">ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-265">in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-266">out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-267">이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b0af3-268">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류`<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b0af3-269">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="b0af3-269">Log creation and redirection</span></span>

<span data-ttu-id="b0af3-270">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b0af3-271">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-272">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="b0af3-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="b0af3-273">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b0af3-274">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b0af3-275">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b0af3-276">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b0af3-277">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b0af3-278">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b0af3-279">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b0af3-280">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="b0af3-281">`stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="b0af3-282">`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="b0af3-283">시작 후에는 모든 추가 로그가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="b0af3-284">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b0af3-285">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="b0af3-286">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-287">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b0af3-288">로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="b0af3-289">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="b0af3-290">개선된 진단 로그</span><span class="sxs-lookup"><span data-stu-id="b0af3-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="b0af3-291">ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="b0af3-292">`web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="b0af3-293">`debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b0af3-294">경로에 있는 모든 폴더(위 예제의 `logs`)가 로그 파일을 만들 때 모듈에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-295">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).</span><span class="sxs-lookup"><span data-stu-id="b0af3-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="b0af3-296">디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="b0af3-297">수준(최소한에서 가장 자세한 정보까지 순서대로 ):</span><span class="sxs-lookup"><span data-stu-id="b0af3-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="b0af3-298">오류</span><span class="sxs-lookup"><span data-stu-id="b0af3-298">ERROR</span></span>
* <span data-ttu-id="b0af3-299">경고</span><span class="sxs-lookup"><span data-stu-id="b0af3-299">WARNING</span></span>
* <span data-ttu-id="b0af3-300">정보</span><span class="sxs-lookup"><span data-stu-id="b0af3-300">INFO</span></span>
* <span data-ttu-id="b0af3-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="b0af3-301">TRACE</span></span>

<span data-ttu-id="b0af3-302">위치(여러 위치가 허용됨):</span><span class="sxs-lookup"><span data-stu-id="b0af3-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="b0af3-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="b0af3-303">CONSOLE</span></span>
* <span data-ttu-id="b0af3-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="b0af3-304">EVENTLOG</span></span>
* <span data-ttu-id="b0af3-305">FILE</span><span class="sxs-lookup"><span data-stu-id="b0af3-305">FILE</span></span>

<span data-ttu-id="b0af3-306">처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="b0af3-307">`ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="b0af3-308">(기본값: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="b0af3-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="b0af3-309">`ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="b0af3-310">배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="b0af3-311">로그의 크기는 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-311">The size of the log isn't limited.</span></span> <span data-ttu-id="b0af3-312">디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="b0af3-313">`web.config` 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="b0af3-314">스택 크기 수정</span><span class="sxs-lookup"><span data-stu-id="b0af3-314">Modify the stack size</span></span>

<span data-ttu-id="b0af3-315">*In-process 호스팅 모델을 사용하는 경우에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="b0af3-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="b0af3-316">`web.config`에서 `stackSize` 설정을 사용하여 관리형 스택 크기(바이트)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="b0af3-317">기본 크기는 1,048,576 bytes(1MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b0af3-318">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b0af3-319">*호스팅에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="b0af3-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="b0af3-320">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b0af3-321">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b0af3-322">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b0af3-323">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b0af3-324">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b0af3-325">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b0af3-326">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b0af3-327">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b0af3-328">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b0af3-329">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b0af3-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b0af3-330">ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="b0af3-331">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="b0af3-332">IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다</span><span class="sxs-lookup"><span data-stu-id="b0af3-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="b0af3-333">공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="b0af3-334">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b0af3-335">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-335">Run the installer.</span></span>
1. <span data-ttu-id="b0af3-336">업데이트된 `applicationHost.config` 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="b0af3-337">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b0af3-338">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="b0af3-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b0af3-339">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="b0af3-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b0af3-340">호스팅 시스템에서 `%windir%\System32\inetsrv`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="b0af3-341">`aspnetcore.dll` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="b0af3-342">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b0af3-343">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b0af3-344">모듈의 호스팅 번들 설치 관리자 로그는 `C:\Users\%UserName%\AppData\Local\Temp`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="b0af3-345">파일 이름은 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b0af3-346">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="b0af3-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b0af3-347">Module</span><span class="sxs-lookup"><span data-stu-id="b0af3-347">Module</span></span>

<span data-ttu-id="b0af3-348">**IIS(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b0af3-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="b0af3-349">**IIS Express(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b0af3-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="b0af3-350">스키마</span><span class="sxs-lookup"><span data-stu-id="b0af3-350">Schema</span></span>

<span data-ttu-id="b0af3-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="b0af3-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="b0af3-353">Configuration</span><span class="sxs-lookup"><span data-stu-id="b0af3-353">Configuration</span></span>

<span data-ttu-id="b0af3-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="b0af3-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-355">**IIS Express**</span></span>

* <span data-ttu-id="b0af3-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="b0af3-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="b0af3-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="b0af3-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="b0af3-358">`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b0af3-359">ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="b0af3-360">IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="b0af3-361">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="b0af3-362">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="b0af3-362">Supported Windows versions:</span></span>

* <span data-ttu-id="b0af3-363">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-363">Windows 7 or later</span></span>
* <span data-ttu-id="b0af3-364">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b0af3-365">In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b0af3-366">Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="b0af3-367">이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b0af3-368">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b0af3-369">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-369">In-process hosting model</span></span>

<span data-ttu-id="b0af3-370">In-Process 호스팅용 앱을 구성하려면 `<AspNetCoreHostingModel>` 속성을 `InProcess` 값의 앱 프로젝트 파일에 추가합니다(Out-of-Process 호스팅은 `OutOfProcess`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="b0af3-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b0af3-371">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="b0af3-372">`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b0af3-373">파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="b0af3-374">다음 특성은 In-Process로 호스팅할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="b0af3-375">IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b0af3-376">In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="b0af3-377">`IISHttpServer`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="b0af3-378">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="b0af3-379">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="b0af3-380">[requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="b0af3-381">앱 간의 앱 풀 공유는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="b0af3-382">앱당 하나의 앱 풀을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-382">Use one app pool per app.</span></span>

* <span data-ttu-id="b0af3-383">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [app_offline.htm 파일을 배포에](xref:host-and-deploy/iis/index#locked-deployment-files) 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b0af3-384">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="b0af3-385">앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="b0af3-386">클라이언트의 연결 끊김이 검색되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-386">Client disconnects are detected.</span></span> <span data-ttu-id="b0af3-387">클라이언트의 연결이 끊어지면 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="b0af3-388">ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: *w3wp.exe* 에 대한 *C:\Windows\System32\inetsrv*).</span><span class="sxs-lookup"><span data-stu-id="b0af3-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="b0af3-389">앱의 현재 디렉터리를 설정하는 샘플 코드는 [CurrentDirectoryHelpers 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="b0af3-390">`SetCurrentDirectory` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="b0af3-391"><xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="b0af3-392">In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="b0af3-393">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="b0af3-394"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b0af3-395">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="b0af3-395">Out-of-process hosting model</span></span>

<span data-ttu-id="b0af3-396">Out of Process 호스팅을 위한 앱을 구성하려면 프로젝트 파일에서 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="b0af3-397">`<AspNetCoreHostingModel>` 속성을 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="b0af3-398">파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="b0af3-399">`<AspNetCoreHostingModel>` 속성 값을 `OutOfProcess`로 설정합니다(In Process 호스트팅은 `InProcess`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="b0af3-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b0af3-400">이 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b0af3-401">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b0af3-402">Out of Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="b0af3-403">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="b0af3-404">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="b0af3-405">호스팅 모델 변경</span><span class="sxs-lookup"><span data-stu-id="b0af3-405">Hosting model changes</span></span>

<span data-ttu-id="b0af3-406">`hostingModel` 설정이 *web.config* 파일에서 변경되면([web.config로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="b0af3-407">IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="b0af3-408">앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="b0af3-409">프로세스 이름</span><span class="sxs-lookup"><span data-stu-id="b0af3-409">Process name</span></span>

<span data-ttu-id="b0af3-410">`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="b0af3-411">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b0af3-412">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b0af3-413">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b0af3-414">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b0af3-415">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b0af3-416">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b0af3-417">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="b0af3-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b0af3-418">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b0af3-419">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="b0af3-419">Configuration with web.config</span></span>

<span data-ttu-id="b0af3-420">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b0af3-421">다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b0af3-422">다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b0af3-423"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="b0af3-424">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-425">이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b0af3-426">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b0af3-427">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b0af3-428">특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-428">Attribute</span></span> | <span data-ttu-id="b0af3-429">설명</span><span class="sxs-lookup"><span data-stu-id="b0af3-429">Description</span></span> | <span data-ttu-id="b0af3-430">기본값</span><span class="sxs-lookup"><span data-stu-id="b0af3-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b0af3-431">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-431">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-432">`processPath`에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b0af3-433">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-434">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b0af3-435">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-436">true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b0af3-437">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="b0af3-438">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-438">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-439">호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="b0af3-440">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-441">앱별로 스핀 업할 수 있는 `processPath` 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b0af3-442">&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="b0af3-443">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b0af3-444">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b0af3-445">기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-445">Default: `1`</span></span><br><span data-ttu-id="b0af3-446">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-446">Min: `1`</span></span><br><span data-ttu-id="b0af3-447">최대: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="b0af3-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="b0af3-448">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-448">Required string attribute.</span></span></p><p><span data-ttu-id="b0af3-449">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b0af3-450">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-450">Relative paths are supported.</span></span> <span data-ttu-id="b0af3-451">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b0af3-452">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-453">`processPath`에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="b0af3-454">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="b0af3-455">In-Process 호스팅에서는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="b0af3-456">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-456">Default: `10`</span></span><br><span data-ttu-id="b0af3-457">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-457">Min: `0`</span></span><br><span data-ttu-id="b0af3-458">최대: `100`</span><span class="sxs-lookup"><span data-stu-id="b0af3-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b0af3-459">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b0af3-460">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b0af3-461">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="b0af3-462">In-Process 호스팅에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="b0af3-463">In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="b0af3-464">문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="b0af3-465">분 또는 초의 값에 **60** 을 사용하면 *500 - 내부 서버 오류* 가 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="b0af3-466">기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-466">Default: `00:02:00`</span></span><br><span data-ttu-id="b0af3-467">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-467">Min: `00:00:00`</span></span><br><span data-ttu-id="b0af3-468">최대: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b0af3-469">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-470">`app_offline.htm` 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="b0af3-471">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-471">Default: `10`</span></span><br><span data-ttu-id="b0af3-472">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-472">Min: `0`</span></span><br><span data-ttu-id="b0af3-473">최대: `600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b0af3-474">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-475">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b0af3-476">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="b0af3-477">*In Process* 를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** `rapidFailsPerMinute` 설정을 사용하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="b0af3-478">*Out-of-Process* 를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 `rapidFailsPerMinute`번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b0af3-479">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b0af3-480">기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="b0af3-480">Default: `120`</span></span><br><span data-ttu-id="b0af3-481">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-481">Min: `0`</span></span><br><span data-ttu-id="b0af3-482">최대: `3600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b0af3-483">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-484">true인 경우 `processPath`에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b0af3-485">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-485">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-486">`processPath`에 지정된 프로세스에서 `stdout` 및 `stderr`이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="b0af3-487">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b0af3-488">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b0af3-489">경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-490">타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 `stdoutLogFile` 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="b0af3-491">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="b0af3-492">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="b0af3-492">Setting environment variables</span></span>

<span data-ttu-id="b0af3-493">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b0af3-494">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="b0af3-495">이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="b0af3-496">다음 예제에서는 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-496">The following example sets two environment variables.</span></span> <span data-ttu-id="b0af3-497">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b0af3-498">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 `web.config` 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b0af3-499">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="b0af3-500">`web.config`에서 환경을 직접 설정하는 대안으로 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="b0af3-501">이 방법은 프로젝트가 게시될 때 `web.config`에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="b0af3-502">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="b0af3-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="b0af3-503">app_offline.htm</span></span>

<span data-ttu-id="b0af3-504">응용 프로그램의 루트 디렉터리에서 이름이 `app_offline.htm`인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b0af3-505">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b0af3-506">`app_offline.htm` 파일이 있는 동안 ASP.NET Core 모듈은 `app_offline.htm` 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="b0af3-507">`app_offline.htm` 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="b0af3-508">Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b0af3-509">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b0af3-510">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="b0af3-510">Start-up error page</span></span>

<span data-ttu-id="b0af3-511">In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="b0af3-512">ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-513">in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-514">out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="b0af3-515">이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b0af3-516">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b0af3-517">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="b0af3-517">Log creation and redirection</span></span>

<span data-ttu-id="b0af3-518">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b0af3-519">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-520">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).</span><span class="sxs-lookup"><span data-stu-id="b0af3-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="b0af3-521">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b0af3-522">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b0af3-523">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b0af3-524">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b0af3-525">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b0af3-526">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b0af3-527">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b0af3-528">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="b0af3-529">`stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="b0af3-530">`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="b0af3-531">시작 후에는 모든 추가 로그가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="b0af3-532">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b0af3-533">앱 풀 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="b0af3-534">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-535">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b0af3-536">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="b0af3-537">개선된 진단 로그</span><span class="sxs-lookup"><span data-stu-id="b0af3-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="b0af3-538">ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="b0af3-539">`web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="b0af3-540">`debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b0af3-541">`<handlerSetting>` 값에 제공한 경로에 있는 폴더(위 예제의 `logs`)가 모듈에서 자동으로 생성되지 않으며, 배포에 있는 기존 폴더여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="b0af3-542">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).</span><span class="sxs-lookup"><span data-stu-id="b0af3-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="b0af3-543">디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="b0af3-544">수준(최소한에서 가장 자세한 정보까지 순서대로 ):</span><span class="sxs-lookup"><span data-stu-id="b0af3-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="b0af3-545">오류</span><span class="sxs-lookup"><span data-stu-id="b0af3-545">ERROR</span></span>
* <span data-ttu-id="b0af3-546">경고</span><span class="sxs-lookup"><span data-stu-id="b0af3-546">WARNING</span></span>
* <span data-ttu-id="b0af3-547">정보</span><span class="sxs-lookup"><span data-stu-id="b0af3-547">INFO</span></span>
* <span data-ttu-id="b0af3-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="b0af3-548">TRACE</span></span>

<span data-ttu-id="b0af3-549">위치(여러 위치가 허용됨):</span><span class="sxs-lookup"><span data-stu-id="b0af3-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="b0af3-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="b0af3-550">CONSOLE</span></span>
* <span data-ttu-id="b0af3-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="b0af3-551">EVENTLOG</span></span>
* <span data-ttu-id="b0af3-552">FILE</span><span class="sxs-lookup"><span data-stu-id="b0af3-552">FILE</span></span>

<span data-ttu-id="b0af3-553">처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="b0af3-554">`ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="b0af3-555">(기본값: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="b0af3-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="b0af3-556">`ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="b0af3-557">배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="b0af3-558">로그의 크기는 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-558">The size of the log isn't limited.</span></span> <span data-ttu-id="b0af3-559">디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="b0af3-560">`web.config` 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b0af3-561">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b0af3-562">*호스팅에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="b0af3-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="b0af3-563">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b0af3-564">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b0af3-565">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b0af3-566">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b0af3-567">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b0af3-568">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b0af3-569">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b0af3-570">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b0af3-571">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b0af3-572">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b0af3-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b0af3-573">ASP.NET Core 모듈 설치 관리자는 `TrustedInstaller` 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="b0af3-574">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="b0af3-575">IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다</span><span class="sxs-lookup"><span data-stu-id="b0af3-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="b0af3-576">공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="b0af3-577">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b0af3-578">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-578">Run the installer.</span></span>
1. <span data-ttu-id="b0af3-579">업데이트된 `applicationHost.config` 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="b0af3-580">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b0af3-581">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="b0af3-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b0af3-582">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="b0af3-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b0af3-583">호스팅 시스템에서 `%windir%\System32\inetsrv`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="b0af3-584">`aspnetcore.dll` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="b0af3-585">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b0af3-586">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b0af3-587">모듈의 호스팅 번들 설치 관리자 로그는 `C:\\Users\\%UserName%\\AppData\\Local\\Temp`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="b0af3-588">파일 이름은 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`입니다. 여기서 자리 표시자 `{TIMESTAMP}`는 타임스탬프입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b0af3-589">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="b0af3-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b0af3-590">Module</span><span class="sxs-lookup"><span data-stu-id="b0af3-590">Module</span></span>

<span data-ttu-id="b0af3-591">**IIS(x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="b0af3-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="b0af3-592">**IIS Express(x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="b0af3-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="b0af3-593">스키마</span><span class="sxs-lookup"><span data-stu-id="b0af3-593">Schema</span></span>

<span data-ttu-id="b0af3-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="b0af3-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="b0af3-596">Configuration</span><span class="sxs-lookup"><span data-stu-id="b0af3-596">Configuration</span></span>

<span data-ttu-id="b0af3-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="b0af3-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-598">**IIS Express**</span></span>

* <span data-ttu-id="b0af3-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="b0af3-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="b0af3-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="b0af3-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="b0af3-601">`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b0af3-602">ASP.NET Core 모듈은 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하는 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="b0af3-603">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="b0af3-603">Supported Windows versions:</span></span>

* <span data-ttu-id="b0af3-604">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-604">Windows 7 or later</span></span>
* <span data-ttu-id="b0af3-605">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="b0af3-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b0af3-606">모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-606">The module only works with Kestrel.</span></span> <span data-ttu-id="b0af3-607">모듈이 [HTTP.sys](xref:fundamentals/servers/httpsys)와 호환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="b0af3-608">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리도 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="b0af3-609">이 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="b0af3-610">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 IIS에서 In Process로 실행되는 ASP.NET 4.x 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b0af3-611">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 모듈](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="b0af3-613">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b0af3-614">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b0af3-615">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b0af3-616">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="b0af3-617">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b0af3-618">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b0af3-619">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b0af3-620">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b0af3-621">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b0af3-622">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="b0af3-623">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b0af3-624">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b0af3-625">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b0af3-626">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b0af3-627">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b0af3-628">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b0af3-629">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="b0af3-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b0af3-630">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b0af3-631">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="b0af3-631">Configuration with web.config</span></span>

<span data-ttu-id="b0af3-632">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b0af3-633">다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="b0af3-634">다음 *web.config* 는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="b0af3-635">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-636">이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b0af3-637">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b0af3-638">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b0af3-639">특성</span><span class="sxs-lookup"><span data-stu-id="b0af3-639">Attribute</span></span> | <span data-ttu-id="b0af3-640">설명</span><span class="sxs-lookup"><span data-stu-id="b0af3-640">Description</span></span> | <span data-ttu-id="b0af3-641">기본값</span><span class="sxs-lookup"><span data-stu-id="b0af3-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b0af3-642">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-642">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-643">**processPath** 에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b0af3-644">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-645">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config* 에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b0af3-646">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-647">true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b0af3-648">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="b0af3-649">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-650">앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b0af3-651">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b0af3-652">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b0af3-653">기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-653">Default: `1`</span></span><br><span data-ttu-id="b0af3-654">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="b0af3-654">Min: `1`</span></span><br><span data-ttu-id="b0af3-655">최대: `100`</span><span class="sxs-lookup"><span data-stu-id="b0af3-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="b0af3-656">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-656">Required string attribute.</span></span></p><p><span data-ttu-id="b0af3-657">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b0af3-658">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-658">Relative paths are supported.</span></span> <span data-ttu-id="b0af3-659">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b0af3-660">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-661">**processPath** 에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b0af3-662">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="b0af3-663">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-663">Default: `10`</span></span><br><span data-ttu-id="b0af3-664">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-664">Min: `0`</span></span><br><span data-ttu-id="b0af3-665">최대: `100`</span><span class="sxs-lookup"><span data-stu-id="b0af3-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b0af3-666">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b0af3-667">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b0af3-668">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="b0af3-669">기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-669">Default: `00:02:00`</span></span><br><span data-ttu-id="b0af3-670">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-670">Min: `00:00:00`</span></span><br><span data-ttu-id="b0af3-671">최대: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b0af3-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b0af3-672">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-673">*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b0af3-674">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="b0af3-674">Default: `10`</span></span><br><span data-ttu-id="b0af3-675">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-675">Min: `0`</span></span><br><span data-ttu-id="b0af3-676">최대: `600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b0af3-677">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="b0af3-678">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b0af3-679">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="b0af3-680">모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute** 번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b0af3-681">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b0af3-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b0af3-682">기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="b0af3-682">Default: `120`</span></span><br><span data-ttu-id="b0af3-683">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="b0af3-683">Min: `0`</span></span><br><span data-ttu-id="b0af3-684">최대: `3600`</span><span class="sxs-lookup"><span data-stu-id="b0af3-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b0af3-685">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b0af3-686">true인 경우 **processPath** 에 지정된 프로세스에 대한 **stdout** 및 **stderr** 이 **stdoutLogFile** 에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b0af3-687">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-687">Optional string attribute.</span></span></p><p><span data-ttu-id="b0af3-688">**processPath** 에 지정된 프로세스에서 **stdout** 및 **stderr** 이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b0af3-689">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b0af3-690">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b0af3-691">모듈이 로그 파일을 만들려면 경로에 제공된 모든 폴더가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="b0af3-692">타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b0af3-693">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 *logs* 폴더에 *stdout_20180205194132_1934.log* 로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="b0af3-694">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="b0af3-694">Setting environment variables</span></span>

<span data-ttu-id="b0af3-695">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b0af3-696">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="b0af3-697">이 섹션에 설정된 환경 변수가 동일한 이름으로 설정된 시스템 환경 변수와 충돌합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="b0af3-698">환경 변수가 *web.config* 파일과 Windows의 시스템 수준에 모두 설정된 경우 *web.config* 파일의 값은 시스템 환경 변수 값(예: `ASPNETCORE_ENVIRONMENT: Development;Development`)에 추가되어 앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="b0af3-699">다음 예제에서는 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-699">The following example sets two environment variables.</span></span> <span data-ttu-id="b0af3-700">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b0af3-701">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 *web.config* 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b0af3-702">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="b0af3-703">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="b0af3-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="b0af3-704">app_offline.htm</span></span>

<span data-ttu-id="b0af3-705">응용 프로그램의 루트 디렉터리에서 이름이 *app_offline.htm* 인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b0af3-706">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b0af3-707">*app_offline.htm* 파일이 있는 동안 ASP.NET Core 모듈은 *app_offline.htm* 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="b0af3-708">*app_offline.htm* 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b0af3-709">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="b0af3-709">Start-up error page</span></span>

<span data-ttu-id="b0af3-710">ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="b0af3-711">이 페이지를 표시하지 않고 기본 IIS 502 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b0af3-712">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b0af3-713">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="b0af3-713">Log creation and redirection</span></span>

<span data-ttu-id="b0af3-714">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b0af3-715">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b0af3-716">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="b0af3-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="b0af3-717">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b0af3-718">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b0af3-719">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b0af3-720">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b0af3-721">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b0af3-722">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b0af3-723">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b0af3-724">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 *stdout*)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="b0af3-725">`stdoutLogFile` 경로가 *stdout* 으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 *stdout_20180205194132_1934.log* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="b0af3-726">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b0af3-727">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="b0af3-728">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b0af3-729">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b0af3-730">로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="b0af3-731">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0af3-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b0af3-732">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b0af3-733">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b0af3-734">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b0af3-735">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b0af3-736">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b0af3-737">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b0af3-738">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b0af3-739">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b0af3-740">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b0af3-741">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b0af3-742">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b0af3-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b0af3-743">ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="b0af3-744">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 *applicationHost.config* 에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="b0af3-745">IIS 공유 구성을 사용할 경우 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="b0af3-746">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b0af3-747">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-747">Run the installer.</span></span>
1. <span data-ttu-id="b0af3-748">업데이트된 *applicationHost.config* 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="b0af3-749">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b0af3-750">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="b0af3-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b0af3-751">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="b0af3-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b0af3-752">호스팅 시스템에서 *%windir%\System32\inetsrv* 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="b0af3-753">*aspnetcore.dll* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="b0af3-754">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b0af3-755">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b0af3-756">모듈에 대한 호스팅 번들 설치 관리자 로그는 *C:\\Users\\%UserName%\\AppData\\Local\\Temp* 에 있습니다. 파일 이름은 *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* 로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b0af3-757">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="b0af3-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b0af3-758">Module</span><span class="sxs-lookup"><span data-stu-id="b0af3-758">Module</span></span>

<span data-ttu-id="b0af3-759">**IIS(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b0af3-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="b0af3-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b0af3-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="b0af3-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b0af3-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="b0af3-762">**IIS Express(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="b0af3-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="b0af3-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b0af3-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="b0af3-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b0af3-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="b0af3-765">스키마</span><span class="sxs-lookup"><span data-stu-id="b0af3-765">Schema</span></span>

<span data-ttu-id="b0af3-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-766">**IIS**</span></span>

* <span data-ttu-id="b0af3-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b0af3-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="b0af3-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-768">**IIS Express**</span></span>

* <span data-ttu-id="b0af3-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b0af3-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="b0af3-770">Configuration</span><span class="sxs-lookup"><span data-stu-id="b0af3-770">Configuration</span></span>

<span data-ttu-id="b0af3-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b0af3-771">**IIS**</span></span>

* <span data-ttu-id="b0af3-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="b0af3-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="b0af3-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b0af3-773">**IIS Express**</span></span>

* <span data-ttu-id="b0af3-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="b0af3-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="b0af3-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="b0af3-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="b0af3-776">*applicationHost.config* 파일에서 *aspnetcore* 를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0af3-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b0af3-777">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b0af3-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="b0af3-778">[ASP.NET Core 모듈 참조 원본[기본 분기(마스터)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): **분기** 드롭다운 목록을 사용하여 특정 릴리스를 선택할 수 있습니다(예: `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="b0af3-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
