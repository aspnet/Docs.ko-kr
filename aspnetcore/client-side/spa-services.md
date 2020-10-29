---
title: JavaScript Services를 사용하여 ASP.NET Core에서 단일 페이지 애플리케이션 만들기
author: scottaddie
description: JavaScript Services를 사용하여 ASP.NET Core에서 지원하는 SPA(단일 페이지 애플리케이션)를 만드는 경우의 이점에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
no-loc:
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
uid: client-side/spa-services
ms.openlocfilehash: 33ae16c033142aa1c0f4cea53ca746e8a6aaf052
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690663"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="9d2be-103">JavaScript Services를 사용하여 ASP.NET Core에서 단일 페이지 애플리케이션 만들기</span><span class="sxs-lookup"><span data-stu-id="9d2be-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="9d2be-104">작성자: [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="9d2be-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="9d2be-105">SPA(단일 페이지 애플리케이션)는 기본적으로 풍부한 사용자 환경을 제공하므로 널리 사용되는 웹 애플리케이션 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="9d2be-106">클라이언트 쪽 SPA 프레임워크 또는 라이브러리(예: [Angular](https://angular.io/) 또는 [React](https://facebook.github.io/react/))를 ASP.NET Core와 같은 서버 쪽 프레임워크와 통합하는 것은 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="9d2be-107">JavaScript Services는 통합 프로세스의 마찰을 줄이기 위해 개발되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="9d2be-108">이를 통해 다른 클라이언트 및 서버 기술 스택 간에 원활한 작업이 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="9d2be-109">이 문서에서 설명하는 기능은 ASP.NET Core 3.0에서 더 이상 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="9d2be-110">더 간단한 SPA 프레임워크 통합 메커니즘을 [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="9d2be-111">자세한 내용은 [[알림] Microsoft.AspNetCore.SpaServices 및 Microsoft.AspNetCore.NodeServices가 더 이상 사용되지 않음](https://github.com/dotnet/AspNetCore/issues/12890)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="9d2be-112">JavaScript Services란?</span><span class="sxs-lookup"><span data-stu-id="9d2be-112">What is JavaScript Services</span></span>

<span data-ttu-id="9d2be-113">JavaScript Services는 ASP.NET Core에 대한 클라이언트 쪽 기술 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="9d2be-114">해당 목표는 ASP.NET Core를 SPA를 빌드하기 위한 개발자의 기본 서버 쪽 플랫폼으로 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="9d2be-115">JavaScript 서비스는 다음 두 개의 고유한 NuGet 패키지로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="9d2be-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/)(NodeServices)</span><span class="sxs-lookup"><span data-stu-id="9d2be-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="9d2be-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)(SpaServices)</span><span class="sxs-lookup"><span data-stu-id="9d2be-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="9d2be-118">이러한 패키지는 다음과 같은 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="9d2be-119">서버에서 JavaScript 실행</span><span class="sxs-lookup"><span data-stu-id="9d2be-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="9d2be-120">SPA 프레임워크 또는 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="9d2be-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="9d2be-121">Webpack을 사용하여 클라이언트 쪽 자산 작성</span><span class="sxs-lookup"><span data-stu-id="9d2be-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="9d2be-122">이 문서에 포함된 대부분의 핵심 내용은 SpaServices 패키지를 사용해서 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="9d2be-123">SpaServices란?</span><span class="sxs-lookup"><span data-stu-id="9d2be-123">What is SpaServices</span></span>

<span data-ttu-id="9d2be-124">SpaServices는 ASP.NET Core를 SPA를 빌드하기 위한 개발자의 기본 서버 쪽 플랫폼으로 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="9d2be-125">SpaServices는 ASP.NET Core에서 SPA를 개발하는 데 필요하지 않으며, 특정 클라이언트 프레임워크로 개발자가 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="9d2be-126">SpaServices는 다음과 같은 유용한 인프라를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="9d2be-127">서버 쪽 사전 렌더링</span><span class="sxs-lookup"><span data-stu-id="9d2be-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="9d2be-128">Webpack 개발 미들웨어</span><span class="sxs-lookup"><span data-stu-id="9d2be-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="9d2be-129">핫 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="9d2be-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="9d2be-130">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="9d2be-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="9d2be-131">전체적으로, 이러한 인프라 구성 요소는 개발 워크플로와 런타임 환경을 모두 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="9d2be-132">구성 요소는 개별적으로 채택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="9d2be-133">SpaServices 사용을 위한 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9d2be-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="9d2be-134">SpaServices를 사용하려면 다음을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="9d2be-135">[Node.js](https://nodejs.org/)(버전 6 이상), npm 포함</span><span class="sxs-lookup"><span data-stu-id="9d2be-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="9d2be-136">이러한 구성 요소가 설치되어 있고 찾을 수 있는지 확인하려면 명령줄에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="9d2be-137">Azure 웹 사이트에 배포하는 경우 아무런 작업도 필요하지 않습니다. 서버 환경에서 Node.js가 설치되며 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="9d2be-138">Visual Studio 2017을 사용하는 Windows에서는 **.NET Core 플랫폼 간 개발** 워크로드를 선택하여 SDK가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="9d2be-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="9d2be-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="9d2be-140">서버 쪽 사전 렌더링</span><span class="sxs-lookup"><span data-stu-id="9d2be-140">Server-side prerendering</span></span>

<span data-ttu-id="9d2be-141">유니버설(같은 구조) 애플리케이션은 서버와 클라이언트 둘 다에서 실행될 수 있는 JavaScript 애플리케이션입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="9d2be-142">Angular, React 및 기타 인기 있는 프레임워크는 이 애플리케이션 개발 스타일에 대한 범용 플랫폼을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="9d2be-143">즉, 먼저 Node.js를 통해 서버에서 프레임워크 구성 요소를 렌더링한 다음, 클라이언트에 추가 실행을 위임하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="9d2be-144">SpaServices에서 제공하는 ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro)는 서버에서 JavaScript 함수를 호출하여 서버 쪽 렌더링의 구현을 단순화합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="9d2be-145">서버 쪽 사전 렌더링 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9d2be-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="9d2be-146">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="9d2be-147">서버 쪽 사전 렌더링 구성</span><span class="sxs-lookup"><span data-stu-id="9d2be-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="9d2be-148">태그 도우미는 프로젝트의 *_ViewImports.cshtml* 파일에서 네임스페이스 등록을 통해 검색할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="9d2be-149">이 태그 도우미는 :::no-loc(Razor)::: 뷰 내에서 HTML 유사 구문을 활용하여 하위 수준 API와 직접 통신할 때의 복잡성을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the :::no-loc(Razor)::: view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="9d2be-150">asp-prerender-module 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="9d2be-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="9d2be-151">위의 코드 예제에서 사용되는 `asp-prerender-module` 태그 도우미는 Node.js를 통해 서버에서 *ClientApp/dist/main-server.js* 를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="9d2be-152">명확성을 위해 *main-server.js* 파일은 [Webpack](https://webpack.github.io/) 빌드 프로세스에서 TypeScript에서 JavaScript로의 소스 간 컴파일 태스크의 아티팩트입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="9d2be-153">Webpack은 `main-server`의 진입점 별칭을 정의합니다. 이 별칭에 대한 종속성 그래프 이동은 *ClientApp/boot-server.ts* 파일에서 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="9d2be-154">다음 Angular 예제에서 *ClientApp/boot-server.ts* 파일은 `createServerRenderer` 함수 및 `aspnet-prerendering` npm 패키지의 `RenderResult` 형식을 활용하여 Node.js를 통해 서버 렌더링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="9d2be-155">서버 쪽 렌더링을 대상으로 하는 HTML 태그는 강력한 형식의 JavaScript `Promise` 개체로 래핑된 resolve 함수 호출에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="9d2be-156">`Promise` 개체의 중요성은 DOM의 자리 표시자 요소에 주입하기 위해 페이지에 HTML 태그를 비동기적으로 제공한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="9d2be-157">asp-prerender-data 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="9d2be-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="9d2be-158">`asp-prerender-module` 태그 도우미와 함께 `asp-prerender-data` 태그 도우미를 사용하여 :::no-loc(Razor)::: 뷰에서 서버 쪽 JavaScript로 컨텍스트 정보를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the :::no-loc(Razor)::: view to the server-side JavaScript.</span></span> <span data-ttu-id="9d2be-159">예를 들어, 다음 태그는 사용자 데이터를 `main-server` 모듈에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="9d2be-160">수신된 `UserName` 인수는 기본 제공 JSON serializer를 사용하여 serialize되고 `params.data` 개체에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="9d2be-161">다음 Angular 예제에서 데이터는 `h1` 요소 내에서 맞춤형 인사말을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="9d2be-162">태그 도우미에 전달된 속성 이름은 **PascalCase** 표기법으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="9d2be-163">동일한 속성 이름이 **camelCase** 로 표시되는 JavaScript와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-163">Contrast that to JavaScript, where the same property names are represented with **camelCase** .</span></span> <span data-ttu-id="9d2be-164">기본 JSON serialization 구성은 이러한 차이를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="9d2be-165">위의 코드 예제를 확장하기 위해 `resolve` 함수에 제공 된 `globals` 속성을 하이드레이션하여 서버에서 뷰로 데이터를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="9d2be-166">`globals` 개체 내에 정의된 `postList` 배열은 브라우저의 전역 `window` 개체에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="9d2be-167">이와 같이 변수를 전역 범위로 올리면 불필요한 작업이 줄어듭니다. 특히 서버에서 동일한 데이터를 한 번 로드하고 클라이언트에서 다시 로드하는 것과 관련이 있는 경우에 이러한 효과가 더욱 두드러집니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![window 개체에 연결된 전역 postList 변수](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="9d2be-169">Webpack 개발 미들웨어</span><span class="sxs-lookup"><span data-stu-id="9d2be-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="9d2be-170">[Webpack 개발 미들웨어](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)에는 Webpack에서 필요 시 리소스를 작성하는 간소화된 개발 워크플로가 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="9d2be-171">미들웨어는 페이지가 브라우저에서 다시 로드될 때 클라이언트 쪽 리소스를 자동으로 컴파일하고 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="9d2be-172">또 다른 방법은 타사 종속성 또는 사용자 지정 코드가 변경될 때 프로젝트의 npm 빌드 스크립트를 통해 Webpack을 수동으로 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="9d2be-173">*package.json* 파일의 npm 빌드 스크립트는 다음 예제에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="9d2be-174">Webpack 개발 미들웨어 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9d2be-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="9d2be-175">다음과 같이 [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="9d2be-176">Webpack 개발 미들웨어 구성</span><span class="sxs-lookup"><span data-stu-id="9d2be-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="9d2be-177">Webpack 개발 미들웨어는 *Startup.cs* 파일의 `Configure` 메서드에서 다음 코드를 통해 HTTP 요청 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="9d2be-178">`UseStaticFiles` 확장 메서드를 통해 [정적 파일 호스팅을 등록](xref:fundamentals/static-files)하기 전에 `UseWebpackDevMiddleware` 확장 메서드를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="9d2be-179">보안상의 이유로 앱이 개발 모드에서 실행되는 경우에만 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="9d2be-180">*webpack.config.js* 파일의 `output.publicPath` 속성은 미들웨어에 `dist` 폴더의 변경 내용을 감시하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="9d2be-181">핫 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="9d2be-181">Hot Module Replacement</span></span>

<span data-ttu-id="9d2be-182">Webpack의 HMR([핫 모듈 교체](https://webpack.js.org/concepts/hot-module-replacement/)) 기능을 [Webpack 개발 미들웨어](#webpack-dev-middleware)가 진화된 결과로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="9d2be-183">HMR은 모든 동일한 혜택을 제공하지만 변경 내용을 컴파일한 후 페이지 콘텐츠를 자동으로 업데이트하여 개발 워크플로를 더욱 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="9d2be-184">이것을 SPA의 현재 메모리 내 상태와 디버깅 세션을 방해하는 브라우저의 새로 고침과 혼동하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="9d2be-185">Webpack 개발 미들웨어 서비스와 브라우저 간에 라이브 링크가 있습니다. 따라서 변경 내용이 브라우저에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="9d2be-186">핫 모듈 교체 필수 조건</span><span class="sxs-lookup"><span data-stu-id="9d2be-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="9d2be-187">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="9d2be-188">핫 모듈 교체 구성</span><span class="sxs-lookup"><span data-stu-id="9d2be-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="9d2be-189">HMR 구성 요소는 `Configure` 메서드에서 MVC의 HTTP 요청 파이프라인에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="9d2be-190">[Webpack 개발 미들웨어](#webpack-dev-middleware)와 마찬가지로 `UseWebpackDevMiddleware` 확장 메서드를 `UseStaticFiles` 확장 메서드보다 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="9d2be-191">보안상의 이유로 앱이 개발 모드에서 실행되는 경우에만 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="9d2be-192">*webpack.config.js* 파일은 비어 있는 경우에도 `plugins` 배열을 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="9d2be-193">브라우저에서 앱을 로드한 후 개발자 도구의 콘솔 탭에서 HMR 활성화 확인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![핫 모듈 교체 연결된 메시지](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="9d2be-195">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="9d2be-195">Routing helpers</span></span>

<span data-ttu-id="9d2be-196">대부분의 ASP.NET Core 기반 SPA에서 서버 쪽 라우팅 외에 클라이언트 쪽 라우팅이 필요한 경우도 많습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="9d2be-197">SPA 및 MVC 라우팅 시스템은 간섭 없이 독립적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="9d2be-198">그러나 문제를 유발하는 한 가지 극단적인 사례가 있습니다. 404 HTTP 응답이 발생하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="9d2be-199">`/some/page`의 확장 없는 경로가 사용되는 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="9d2be-200">요청이 서버 쪽 경로와 패턴이 일치하지 않지만 해당 패턴은 클라이언트 쪽 경로와 일치하는 경우를 가정해 보세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="9d2be-201">이제, 서버에서 이미지 파일을 찾게 되는 `/images/user-512.png`에 대한 들어오는 요청을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="9d2be-202">요청된 리소스 경로가 서버 쪽 경로 또는 정적 파일과 일치하지 않을 경우 클라이언트 쪽 애플리케이션에서 이를 처리할 가능성이 거의 없습니다. 따라서 일반적으로 404 HTTP 상태 코드를 반환하는 것이 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="9d2be-203">라우팅 도우미 필수 조건</span><span class="sxs-lookup"><span data-stu-id="9d2be-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="9d2be-204">클라이언트 쪽 라우팅 npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="9d2be-205">Angular를 예로 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="9d2be-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="9d2be-206">라우팅 도우미 구성</span><span class="sxs-lookup"><span data-stu-id="9d2be-206">Routing helpers configuration</span></span>

<span data-ttu-id="9d2be-207">`MapSpaFallbackRoute`라는 확장 메서드는 `Configure` 메서드에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="9d2be-208">경로는 구성된 순서대로 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="9d2be-209">따라서 앞의 코드 예제의 `default` 경로는 처음에는 패턴 일치에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="9d2be-210">새 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="9d2be-210">Create a new project</span></span>

<span data-ttu-id="9d2be-211">JavaScript Services는 미리 구성된 애플리케이션 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="9d2be-212">SpaServices는 이러한 템플릿에서 다른 프레임워크 및 라이브러리(예: Angular, React 및 Redux)와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="9d2be-213">다음 명령을 실행하여 .NET Core CLI를 통해 이러한 템플릿을 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="9d2be-214">사용 가능한 SPA 템플릿 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="9d2be-215">템플릿</span><span class="sxs-lookup"><span data-stu-id="9d2be-215">Templates</span></span>                                 | <span data-ttu-id="9d2be-216">짧은 이름</span><span class="sxs-lookup"><span data-stu-id="9d2be-216">Short Name</span></span> | <span data-ttu-id="9d2be-217">언어</span><span class="sxs-lookup"><span data-stu-id="9d2be-217">Language</span></span> | <span data-ttu-id="9d2be-218">Tags</span><span class="sxs-lookup"><span data-stu-id="9d2be-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="9d2be-219">MVC ASP.NET Core(Angular 사용)</span><span class="sxs-lookup"><span data-stu-id="9d2be-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="9d2be-220">angular</span><span class="sxs-lookup"><span data-stu-id="9d2be-220">angular</span></span>    | <span data-ttu-id="9d2be-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="9d2be-221">[C#]</span></span>     | <span data-ttu-id="9d2be-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="9d2be-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="9d2be-223">MVC ASP.NET Core(React.js 사용)</span><span class="sxs-lookup"><span data-stu-id="9d2be-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="9d2be-224">react</span><span class="sxs-lookup"><span data-stu-id="9d2be-224">react</span></span>      | <span data-ttu-id="9d2be-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="9d2be-225">[C#]</span></span>     | <span data-ttu-id="9d2be-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="9d2be-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="9d2be-227">MVC ASP.NET Core(React.js 및 Redux 사용)</span><span class="sxs-lookup"><span data-stu-id="9d2be-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="9d2be-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="9d2be-228">reactredux</span></span> | <span data-ttu-id="9d2be-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="9d2be-229">[C#]</span></span>     | <span data-ttu-id="9d2be-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="9d2be-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="9d2be-231">SPA 템플릿 중 하나를 사용하여 새 프로젝트를 만들려면 [dotnet new](/dotnet/core/tools/dotnet-new) 명령에 템플릿의 **짧은 이름** 을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="9d2be-232">다음 명령은 서버 쪽에 대해 구성된 ASP.NET Core MVC를 사용하여 Angular 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="9d2be-233">런타임 구성 모드 설정</span><span class="sxs-lookup"><span data-stu-id="9d2be-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="9d2be-234">다음과 같은 두 가지 기본 런타임 구성 모드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="9d2be-235">**개발** :</span><span class="sxs-lookup"><span data-stu-id="9d2be-235">**Development** :</span></span>
  * <span data-ttu-id="9d2be-236">디버깅을 용이하게 하는 소스 맵을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="9d2be-237">성능을 위해 클라이언트 쪽 코드를 최적화하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="9d2be-238">**프로덕션** :</span><span class="sxs-lookup"><span data-stu-id="9d2be-238">**Production** :</span></span>
  * <span data-ttu-id="9d2be-239">소스 맵을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-239">Excludes source maps.</span></span>
  * <span data-ttu-id="9d2be-240">묶음 및 축소를 통해 클라이언트 쪽 코드를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="9d2be-241">ASP.NET Core는 `ASPNETCORE_ENVIRONMENT`라는 환경 변수를 사용하여 구성 모드를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="9d2be-242">자세한 내용은 [환경 설정](xref:fundamentals/environments#set-the-environment)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="9d2be-243">.NET Core CLI를 사용하여 실행</span><span class="sxs-lookup"><span data-stu-id="9d2be-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="9d2be-244">프로젝트 루트에서 다음 명령을 실행하여 필요한 NuGet 및 npm 패키지를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="9d2be-245">다음과 같이 애플리케이션을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="9d2be-246">애플리케이션은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="9d2be-247">브라우저에서 `http://localhost:5000`으로 이동하면 방문 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="9d2be-248">Visual Studio 2017로 열기</span><span class="sxs-lookup"><span data-stu-id="9d2be-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="9d2be-249">[dotnet new](/dotnet/core/tools/dotnet-new) 명령으로 생성된 *.csproj* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="9d2be-250">필요한 NuGet 및 npm 패키지는 프로젝트를 열 때 자동으로 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="9d2be-251">이 복원 프로세스는 몇 분 정도 걸릴 수 있으며 완료되면 애플리케이션을 실행할 준비가 된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="9d2be-252">녹색 실행 단추를 클릭하거나 `Ctrl + F5`를 누르면 브라우저가 열리고 애플리케이션의 방문 페이지가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="9d2be-253">애플리케이션은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="9d2be-254">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-254">Test the app</span></span>

<span data-ttu-id="9d2be-255">SpaServices 템플릿은 [Karma](https://karma-runner.github.io/1.0/index.html) 및 [Jasmine](https://jasmine.github.io/)을 사용하여 클라이언트 쪽 테스트를 실행하도록 미리 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="9d2be-256">Jasmine는 인기 있는 JavaScript용 단위 테스트 프레임워크이지만 Karma는 해당 테스트용 Test Runner입니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="9d2be-257">Karma는 [Webpack 개발 미들웨어](#webpack-dev-middleware)와 함께 작동하도록 구성되므로 개발자는 변경이 수행될 때마다 테스트를 중지했다가 다시 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="9d2be-258">코드가 테스트 사례 또는 테스트 사례 자체에 대해 실행되는지에 관계없이 테스트는 자동으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="9d2be-259">한 예로 Angular 애플리케이션을 사용할 경우 *counter.component.spec.ts* 파일의 `CounterComponent`에 대해 두 개의 테스트 사례가 이미 제공되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="9d2be-260">*ClientApp* 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="9d2be-261">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="9d2be-262">이 스크립트는 *karma.conf.js* 파일에 정의된 설정을 읽는 Karma Test Runner를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="9d2be-263">다른 설정 중에 *karma.conf.js* 는 `files` 배열을 통해 실행할 테스트 파일을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="9d2be-264">앱 게시</span><span class="sxs-lookup"><span data-stu-id="9d2be-264">Publish the app</span></span>

<span data-ttu-id="9d2be-265">Azure에 게시하는 방법에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/12474)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9d2be-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="9d2be-266">생성된 클라이언트 쪽 자산과 게시된 ASP.NET Core 아티팩트를 배포용 패키지에 결합하는 작업은 복잡할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="9d2be-267">다행히 SpaServices는 `RunWebpack`이라는 사용자 지정 MSBuild 대상을 사용하여 전체 게시 프로세스를 오케스트레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="9d2be-268">MSBuild 대상은 다음과 같은 기능을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="9d2be-269">npm 패키지를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="9d2be-270">타사 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="9d2be-271">사용자 지정 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="9d2be-272">Webpack에서 생성된 자산을 게시 폴더에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="9d2be-273">MSBuild 대상은 다음을 실행할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d2be-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="9d2be-274">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9d2be-274">Additional resources</span></span>

* [<span data-ttu-id="9d2be-275">Angular 설명서</span><span class="sxs-lookup"><span data-stu-id="9d2be-275">Angular Docs</span></span>](https://angular.io/docs)
