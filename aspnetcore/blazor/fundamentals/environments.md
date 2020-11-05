---
title: 'ASP.NET Core :::no-loc(Blazor)::: 환경'
author: guardrex
description: ':::no-loc(Blazor WebAssembly)::: 앱의 환경을 설정하는 방법을 포함하여 :::no-loc(Blazor):::의 환경에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056038"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="cc0aa-103">ASP.NET Core :::no-loc(Blazor)::: 환경</span><span class="sxs-lookup"><span data-stu-id="cc0aa-103">ASP.NET Core :::no-loc(Blazor)::: environments</span></span>

> [!NOTE]
> <span data-ttu-id="cc0aa-104">이 항목은 :::no-loc(Blazor WebAssembly):::에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="cc0aa-105">ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/environments>의 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cc0aa-106">앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="cc0aa-107">앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="cc0aa-108">호스트된 :::no-loc(Blazor WebAssembly)::: 앱은 `blazor-environment` 헤더를 추가하여 브라우저에 환경을 전달하는 미들웨어를 통해 서버에서 환경을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-108">A hosted :::no-loc(Blazor WebAssembly)::: app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="cc0aa-109">헤더의 값은 해당 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-109">The value of the header is the environment.</span></span> <span data-ttu-id="cc0aa-110">호스트된 :::no-loc(Blazor)::: 앱과 서버 앱은 동일한 환경을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-110">The hosted :::no-loc(Blazor)::: app and the server app share the same environment.</span></span> <span data-ttu-id="cc0aa-111">환경을 구성하는 방법을 비롯한 자세한 내용은 <xref:fundamentals/environments> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cc0aa-112">로컬에서 실행되는 독립 실행형 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="cc0aa-113">다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="cc0aa-114">다음 IIS 예제에서는 게시된 `web.config` 파일에 사용자 지정 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="cc0aa-115">`web.config` 파일은 `bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="cc0aa-116">앱이 `publish` 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 `web.config` 파일을 사용하려면 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="cc0aa-117"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 삽입하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="cc0aa-118">시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 개발자가 환경 관련 논리를 코드에 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="cc0aa-119">다음과 같은 편리한 확장 메서드를 통해 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="cc0aa-120"><xref:Microsoft.AspNetCore.Components.NavigationManager> 서비스를 사용할 수 없는 경우 시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc0aa-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc0aa-121">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cc0aa-121">Additional resources</span></span>

* <xref:fundamentals/environments>
