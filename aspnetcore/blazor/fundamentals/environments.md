---
title: ASP.NET Core Blazor 환경
author: guardrex
description: Blazor WebAssembly 앱의 환경을 설정하는 방법을 포함하여 Blazor의 환경에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101050"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="69489-103">ASP.NET Core Blazor 환경</span><span class="sxs-lookup"><span data-stu-id="69489-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="69489-104">이 항목은 Blazor WebAssembly에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="69489-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="69489-105">Blazor Server 앱에 사용할 접근 방식을 설명하는 ASP.NET Core 앱 구성에 관한 일반 지침은 <xref:fundamentals/environments>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="69489-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="69489-106">앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="69489-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="69489-107">앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="69489-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="69489-108">호스트된 Blazor WebAssembly 솔루션의 클라이언트 쪽 Blazor 앱( *`Client`* )은 브라우저에 환경을 전달하는 미들웨어를 통해 솔루션의 *`Server`* 앱에서 환경을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="69489-109">*`Server`* 앱은 환경이 포함된 `blazor-environment`라는 헤더를 헤더 값으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="69489-110">*`Client`* 앱은 헤더를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="69489-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="69489-111">솔루션의 *`Server`* 앱은 ASP.NET Core 앱이므로 환경을 구성하는 방법에 대한 자세한 내용은 <xref:fundamentals/environments>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="69489-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="69489-112">로컬에서 실행되는 독립 실행형 Blazor WebAssembly 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="69489-113">다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="69489-114">다음 IIS 예제에서는 게시된 `web.config` 파일에 사용자 지정 헤더(`blazor-environment`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="69489-115">`web.config` 파일은 `bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 있습니다. 여기서 자리 표시자 `{TARGET FRAMEWORK}`는 대상 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="69489-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="69489-116">앱이 `publish` 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 `web.config` 파일을 사용하려면 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="69489-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="69489-117"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 삽입하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="69489-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="69489-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="69489-118">`Pages/ReadEnvironment.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

<span data-ttu-id="69489-119">시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 호스트 빌더 코드에 환경 관련 논리를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69489-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="69489-120">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="69489-121"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>를 통해 제공되는 다음과 같은 편리한 확장 메서드로 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69489-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="69489-122">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="69489-122">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="69489-123"><xref:Microsoft.AspNetCore.Components.NavigationManager> 서비스를 사용할 수 없는 경우 시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69489-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69489-124">추가 자료</span><span class="sxs-lookup"><span data-stu-id="69489-124">Additional resources</span></span>

* <xref:fundamentals/environments>
