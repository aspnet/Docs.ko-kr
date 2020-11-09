---
title: web.config 파일
author: rick-anderson
description: web.config 파일의 내부에 있는 내용과 다양한 ASP.NET Core 모듈 옵션을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057286"
---
# <a name="webconfig-file"></a><span data-ttu-id="8595a-103">`web.config` 파일:</span><span class="sxs-lookup"><span data-stu-id="8595a-103">`web.config` file</span></span>

<span data-ttu-id="8595a-104">`web.config`는 IIS에서 호스트되는 앱을 구성하기 위해 IIS 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 읽는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-104">The `web.config` is a file that is read by IIS and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to configure an app hosted with IIS.</span></span>

## <a name="webconfig-file-location"></a><span data-ttu-id="8595a-105">`web.config` 파일 위치</span><span class="sxs-lookup"><span data-stu-id="8595a-105">`web.config` file location</span></span>

<span data-ttu-id="8595a-106">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-106">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="8595a-107">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-107">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="8595a-108">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-108">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="8595a-109">중요한 파일은 `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml`(XML 문서 주석) 및 `{ASSEMBLY}.deps.json`과 같은 앱의 실제 경로에 있습니다. 여기서 자리 표시자 `{ASSEMBLY}`는 어셈플리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-109">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="8595a-110">`web.config` 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-110">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="8595a-111">`web.config` 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-111">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="8595a-112">**`web.config` 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 `web.config` 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="8595a-112">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

<span data-ttu-id="8595a-113">프로젝트에 `web.config` 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments` 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-113">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="8595a-114">프로젝트에 `web.config` 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments`로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-114">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="8595a-115">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-115">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="8595a-116">`web.config` 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-116">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="8595a-117">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-117">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="8595a-118">`web.config` 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-118">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="8595a-119">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-119">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="8595a-120">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-120">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="8595a-121">웹 SDK가 `web.config` 파일을 변환하지 못하도록 하려면 프로젝트 파일의 `<IsTransformWebConfigDisabled>` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-121">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="8595a-122">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 `processPath` 및 `arguments`를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-122">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="8595a-123">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-123">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a><span data-ttu-id="8595a-124">`web.config`를 사용하여 ASP.NET Core 모듈 구성</span><span class="sxs-lookup"><span data-stu-id="8595a-124">Configuration of ASP.NET Core Module with `web.config`</span></span>

<span data-ttu-id="8595a-125">ASP.NET Core 모듈은 사이트의 `web.config` 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-125">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's `web.config` file.</span></span>

<span data-ttu-id="8595a-126">다음 `web.config` 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-126">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8595a-127">다음 `web.config`는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-127">The following `web.config` is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8595a-128"><xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> 속성이 `false`로 설정되어 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-128">The <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="8595a-129">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-129">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8595a-130">이 경로는 서비스에서 자동으로 만들어진 위치인 `LogFiles` 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-130">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8595a-131">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-131">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8595a-132">`aspNetCore` 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="8595a-132">Attributes of the `aspNetCore` element</span></span>

| <span data-ttu-id="8595a-133">attribute</span><span class="sxs-lookup"><span data-stu-id="8595a-133">Attribute</span></span> | <span data-ttu-id="8595a-134">설명</span><span class="sxs-lookup"><span data-stu-id="8595a-134">Description</span></span> | <span data-ttu-id="8595a-135">기본값</span><span class="sxs-lookup"><span data-stu-id="8595a-135">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8595a-136">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-136">Optional string attribute.</span></span></p><p><span data-ttu-id="8595a-137">`processPath`에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-137">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8595a-138">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-138">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8595a-139">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 `web.config`에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-139">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the `web.config` takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8595a-140">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-140">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8595a-141">true인 경우 토큰은 `%ASPNETCORE_PORT%`에서 수신 대기하는 자식 프로세스에 요청별 헤더 ‘MS-ASPNETCORE-WINAUTHTOKEN’으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-141">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8595a-142">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-142">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="8595a-143">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-143">Optional string attribute.</span></span></p><p><span data-ttu-id="8595a-144">호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-144">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="8595a-145">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-145">Optional integer attribute.</span></span></p><p><span data-ttu-id="8595a-146">앱별로 스핀 업할 수 있는 `processPath` 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-146">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8595a-147">&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-147">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="8595a-148">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-148">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8595a-149">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-149">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8595a-150">기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="8595a-150">Default: `1`</span></span><br><span data-ttu-id="8595a-151">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="8595a-151">Min: `1`</span></span><br><span data-ttu-id="8595a-152">최대: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="8595a-152">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="8595a-153">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-153">Required string attribute.</span></span></p><p><span data-ttu-id="8595a-154">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-154">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8595a-155">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-155">Relative paths are supported.</span></span> <span data-ttu-id="8595a-156">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-156">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8595a-157">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-157">Optional integer attribute.</span></span></p><p><span data-ttu-id="8595a-158">`processPath`에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-158">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="8595a-159">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-159">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="8595a-160">In-Process 호스팅에서는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-160">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="8595a-161">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="8595a-161">Default: `10`</span></span><br><span data-ttu-id="8595a-162">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="8595a-162">Min: `0`</span></span><br><span data-ttu-id="8595a-163">최대: `100`</span><span class="sxs-lookup"><span data-stu-id="8595a-163">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8595a-164">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-164">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8595a-165">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-165">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8595a-166">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-166">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="8595a-167">In-Process 호스팅에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-167">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="8595a-168">In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-168">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="8595a-169">문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-169">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="8595a-170">분 또는 초의 값에 `60`을 사용하면 *500 - 내부 서버 오류* 가 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-170">Use of `60` in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="8595a-171">기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8595a-171">Default: `00:02:00`</span></span><br><span data-ttu-id="8595a-172">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8595a-172">Min: `00:00:00`</span></span><br><span data-ttu-id="8595a-173">최대: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8595a-173">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8595a-174">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-174">Optional integer attribute.</span></span></p><p><span data-ttu-id="8595a-175">`app_offline.htm` 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-175">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="8595a-176">기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="8595a-176">Default: `10`</span></span><br><span data-ttu-id="8595a-177">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="8595a-177">Min: `0`</span></span><br><span data-ttu-id="8595a-178">최대: `600`</span><span class="sxs-lookup"><span data-stu-id="8595a-178">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8595a-179">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-179">Optional integer attribute.</span></span></p><p><span data-ttu-id="8595a-180">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-180">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8595a-181">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-181">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="8595a-182">*In Process* 를 호스트하는 경우: 프로세스가 다시 시작되지 **않고** `rapidFailsPerMinute` 설정을 사용하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="8595a-182">When hosting *in-process* : The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="8595a-183">*Out-of-Process* 를 호스트하는 경우: 모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 `rapidFailsPerMinute`번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-183">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8595a-184">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="8595a-184">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8595a-185">기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="8595a-185">Default: `120`</span></span><br><span data-ttu-id="8595a-186">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="8595a-186">Min: `0`</span></span><br><span data-ttu-id="8595a-187">최대: `3600`</span><span class="sxs-lookup"><span data-stu-id="8595a-187">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8595a-188">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8595a-189">true인 경우 `processPath`에 지정된 프로세스에 대한 `stdout` 및 `stderr`이 `stdoutLogFile`에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-189">If true, `stdout` and `stderr` for the process specified in `processPath` are redirected to the file specified in `stdoutLogFile`.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8595a-190">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-190">Optional string attribute.</span></span></p><p><span data-ttu-id="8595a-191">`processPath`에 지정된 프로세스에서 `stdout` 및 `stderr`이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-191">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="8595a-192">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-192">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8595a-193">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-193">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8595a-194">경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-194">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="8595a-195">타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)은 밑줄 구분 기호를 사용하여 `stdoutLogFile` 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-195">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="8595a-196">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 `logs` 폴더에 `stdout_20180205194132_1934.log`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-196">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="8595a-197">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="8595a-197">Set environment variables</span></span>

<span data-ttu-id="8595a-198">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-198">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8595a-199">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-199">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="8595a-200">이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-200">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="8595a-201">다음 예제에서는 `web.config`에서 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-201">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="8595a-202">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-202">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8595a-203">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 `web.config` 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-203">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8595a-204">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-204">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8595a-205">`web.config`에서 환경을 직접 설정하는 대안으로 [게시 프로필(`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-205">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="8595a-206">이 방법은 프로젝트가 게시될 때 `web.config`에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-206">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="8595a-207">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-207">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="8595a-208">`web.config`를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="8595a-208">Configuration of IIS with `web.config`</span></span>

<span data-ttu-id="8595a-209">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 `web.config`에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-209">IIS configuration is influenced by the `<system.webServer>` section of `web.config` for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="8595a-210">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-210">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="8595a-211">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 `web.config` 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-211">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's `web.config` file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="8595a-212">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-212">For more information, see the following topics:</span></span>

* [<span data-ttu-id="8595a-213">`<system.webServer>`의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="8595a-213">Configuration reference for `<system.webServer>`</span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="8595a-214">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘`AppCmd.exe` 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-214">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *`AppCmd.exe` command* section of the [Environment Variables `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="8595a-215">`web.config`의 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="8595a-215">Configuration sections of `web.config`</span></span>

<span data-ttu-id="8595a-216">`web.config`에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-216">Configuration sections of ASP.NET 4.x apps in `web.config` aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="8595a-217">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-217">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="8595a-218">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-218">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="8595a-219">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="8595a-219">Transform web.config</span></span>

<span data-ttu-id="8595a-220">게시할 때 `web.config`를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8595a-220">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="8595a-221">구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 `web.config`를 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8595a-221">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8595a-222">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8595a-222">Additional resources</span></span>

* [<span data-ttu-id="8595a-223">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="8595a-223">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
