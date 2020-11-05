---
title: ASP.NET Core에서 정적 자산 번들링 및 축소하기
author: scottaddie
description: 번들링 및 축소 기술을 적용하여 ASP.NET Core 웹 애플리케이션에서 정적 리소스를 최적화하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054842"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="5ebfe-103">ASP.NET Core에서 정적 자산 번들링 및 축소하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="5ebfe-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie) 및 [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="5ebfe-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="5ebfe-105">이 문서에서는 ASP.NET Core 웹 응용 프로그램에서 번들링 및 축소를 사용하는 방법을 비롯해서 이러한 기능을 적용하여 얻을 수 있는 이점을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="5ebfe-106">번들링 및 축소란 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="5ebfe-106">What is bundling and minification</span></span>

<span data-ttu-id="5ebfe-107">번들링 및 축소는 웹 앱에 적용할 수 있는 두 가지 고유한 성능 최적화입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="5ebfe-108">번들링 및 축소를 함께 사용하면 서버 요청 수를 줄이고 요청된 정적 자산의 크기를 줄여서 성능을 향상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="5ebfe-109">번들링 및 축소는 주로 첫 번째 페이지의 요청 로드 시간을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="5ebfe-110">브라우저는 웹 페이지가 요청되면 정적 자산(JavaScript, CSS 및 이미지)을 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="5ebfe-111">따라서 동일한 자산을 요구하는 동일한 사이트에서 동일한 페이지(들)을 요청하는 경우에는 번들링 및 축소로 성능을 개선하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="5ebfe-112">자산에 대한 만료 헤더가 올바르게 설정되지 않고 번들링 및 축소를 사용하지 않는 경우에는 브라우저의 새로 고침 추론이 며칠 지난 후에 자산이 오래된 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="5ebfe-113">게다가 브라우저는 각 자산에 대한 유효성 검사 요청을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="5ebfe-114">이 경우 번들링 및 축소는 첫 번째 페이지 요청 이후라도 성능 개선을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="5ebfe-115">번들링</span><span class="sxs-lookup"><span data-stu-id="5ebfe-115">Bundling</span></span>

<span data-ttu-id="5ebfe-116">번들링은 여러 개의 파일을 단일 파일로 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="5ebfe-117">번들링은 웹 페이지 같은 웹 자산을 렌더링하는데 필요한 서버 요청의 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="5ebfe-118">CSS, JavaScript 등에 대한 여러 개의 개별 번들을 만들 수 있습니다. 파일 수가 적다는 말은 브라우저에서 서버로 보내는 HTTP 요청 수 또는 애플리케이션을 제공하는 서비스의 HTTP 요청 수가 줄어든다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="5ebfe-119">따라서 첫 번째 페이지 로드 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="5ebfe-120">축소</span><span class="sxs-lookup"><span data-stu-id="5ebfe-120">Minification</span></span>

<span data-ttu-id="5ebfe-121">축소는 기능 변경 없이 코드에서 불필요한 문자를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="5ebfe-122">결과적으로 요청된 자산(CSS, 이미지 및 JavaScript 파일 같은)의 크기가 크게 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="5ebfe-123">일반적인 축소의 부수적인 작용은 변수 이름을 한 문자로 줄이고 주석과 불필요한 공백을 제거하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="5ebfe-124">다음 JavaScript 함수를 살펴보시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="5ebfe-125">축소는 이 함수를 다음과 같이 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="5ebfe-126">주석과 불필요한 공백을 제거하는 것 외에도 다음 매개 변수 및 변수 이름이 다음과 같이 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="5ebfe-127">원래 이름</span><span class="sxs-lookup"><span data-stu-id="5ebfe-127">Original</span></span> | <span data-ttu-id="5ebfe-128">변경된 이름</span><span class="sxs-lookup"><span data-stu-id="5ebfe-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="5ebfe-129">번들링 및 축소의 영향</span><span class="sxs-lookup"><span data-stu-id="5ebfe-129">Impact of bundling and minification</span></span>

<span data-ttu-id="5ebfe-130">다음 표는 자산을 개별적으로 로드하는 경우와 번들링 및 축소를 사용하는 경우의 차이를 간략히 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="5ebfe-131">작업</span><span class="sxs-lookup"><span data-stu-id="5ebfe-131">Action</span></span> | <span data-ttu-id="5ebfe-132">B/M 포함</span><span class="sxs-lookup"><span data-stu-id="5ebfe-132">With B/M</span></span> | <span data-ttu-id="5ebfe-133">B/M 제외</span><span class="sxs-lookup"><span data-stu-id="5ebfe-133">Without B/M</span></span> | <span data-ttu-id="5ebfe-134">변화</span><span class="sxs-lookup"><span data-stu-id="5ebfe-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="5ebfe-135">파일 요청</span><span class="sxs-lookup"><span data-stu-id="5ebfe-135">File Requests</span></span>  | <span data-ttu-id="5ebfe-136">7</span><span class="sxs-lookup"><span data-stu-id="5ebfe-136">7</span></span>   | <span data-ttu-id="5ebfe-137">18</span><span class="sxs-lookup"><span data-stu-id="5ebfe-137">18</span></span>     | <span data-ttu-id="5ebfe-138">157%</span><span class="sxs-lookup"><span data-stu-id="5ebfe-138">157%</span></span>
<span data-ttu-id="5ebfe-139">전송(kb)</span><span class="sxs-lookup"><span data-stu-id="5ebfe-139">KB Transferred</span></span> | <span data-ttu-id="5ebfe-140">156</span><span class="sxs-lookup"><span data-stu-id="5ebfe-140">156</span></span> | <span data-ttu-id="5ebfe-141">264.68</span><span class="sxs-lookup"><span data-stu-id="5ebfe-141">264.68</span></span> | <span data-ttu-id="5ebfe-142">70%</span><span class="sxs-lookup"><span data-stu-id="5ebfe-142">70%</span></span>
<span data-ttu-id="5ebfe-143">로드 시간(ms)</span><span class="sxs-lookup"><span data-stu-id="5ebfe-143">Load Time (ms)</span></span> | <span data-ttu-id="5ebfe-144">885</span><span class="sxs-lookup"><span data-stu-id="5ebfe-144">885</span></span> | <span data-ttu-id="5ebfe-145">2360</span><span class="sxs-lookup"><span data-stu-id="5ebfe-145">2360</span></span>   | <span data-ttu-id="5ebfe-146">167%</span><span class="sxs-lookup"><span data-stu-id="5ebfe-146">167%</span></span>

<span data-ttu-id="5ebfe-147">브라우저는 HTTP 요청 헤더에 관해 매우 장황합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="5ebfe-148">번들링 시 전송되는 총 바이트가 상당히 감소하는 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="5ebfe-149">로드 시간이 크게 개선되었지만 이 예제는 로컬에서 실행된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="5ebfe-150">네트워크를 통해서 전송되는 자산에 대해 번들링 및 축소를 사용하면 큰 성능 향상을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="5ebfe-151">번들링 및 축소 전략 선택하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="5ebfe-152">MVC 및 :::no-loc(Razor)::: Pages 프로젝트 템플릿은 JSON 구성 파일로 구성된 묶음 및 축소용 솔루션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-152">The MVC and :::no-loc(Razor)::: Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="5ebfe-153">[Grunt](xref:client-side/using-grunt) 작업 실행기와 같은 타사 도구는 동일한 작업을 약간 더 복잡하게 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="5ebfe-154">타사 도구는 개발 워크플로에 lint 및 이미지 최적화와 같은 묶음 및 축소 이외의 처리가 필요한 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="5ebfe-155">디자인 타임 묶음 및 축소를 사용하여 앱 배포 전에 축소된 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="5ebfe-156">배포 전 묶음 및 축소는 서버 부하 감소라는 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="5ebfe-157">그러나 디자인 타임 묶음 및 축소는 빌드 복잡성을 높이고 정적 파일에만 작동한다는 것을 인식하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="5ebfe-158">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-158">Configure bundling and minification</span></span>

> [!NOTE]
> <span data-ttu-id="5ebfe-159">이 작업을 수행하려면 [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 패키지를 프로젝트에 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-159">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package needs to be added to your project for this to work.</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5ebfe-160">ASP.NET Core 2.0 이전에서는 MVC 및 :::no-loc(Razor)::: 페이지 프로젝트 템플릿에서 각 번들에 대한 옵션을 정의하는 *bundleconfig.json* 구성 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-160">In ASP.NET Core 2.0 or earlier, the MVC and :::no-loc(Razor)::: Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5ebfe-161">ASP.NET Core 2.1 이상에서는 MVC 또는 :::no-loc(Razor)::: Pages 프로젝트 루트에 *bundleconfig.json* 이라는 새로운 JSON 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-161">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json* , to the MVC or :::no-loc(Razor)::: Pages project root.</span></span> <span data-ttu-id="5ebfe-162">다음 JSON을 이 파일의 시작점으로 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-162">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="5ebfe-163">*bundleconfig.json* 파일은 각 번들에 대한 옵션을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-163">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="5ebfe-164">위의 예제에서는 사용자 지정 JavaScript( *wwwroot/js/site.js* ) 및 스타일시트( *wwwroot/css/site.css* ) 파일들에 대해 단일 번들 구성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-164">In the preceding example, a single bundle configuration is defined for the custom JavaScript ( *wwwroot/js/site.js* ) and stylesheet ( *wwwroot/css/site.css* ) files.</span></span>

<span data-ttu-id="5ebfe-165">구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-165">Configuration options include:</span></span>

* <span data-ttu-id="5ebfe-166">`outputFileName`: 출력할 묶음 파일의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-166">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="5ebfe-167">*bundleconfig.json* 파일로부터의 상대 경로를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-167">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="5ebfe-168">**필수**</span><span class="sxs-lookup"><span data-stu-id="5ebfe-168">**required**</span></span>
* <span data-ttu-id="5ebfe-169">`inputFiles`: 묶을 파일의 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-169">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="5ebfe-170">이 배열의 값은 구성 파일에 대한 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="5ebfe-171">**선택적** , \*값이 비어 있으면 빈 출력 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-171">**optional** , \*an empty value results in an empty output file.</span></span> <span data-ttu-id="5ebfe-172">[와일드카드 사용](https://www.tldp.org/LDP/abs/html/globbingref.html) 패턴이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-172">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="5ebfe-173">`minify`: 출력 형식에 대한 축소 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-173">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="5ebfe-174">**선택적** , *기본값 - `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="5ebfe-174">**optional** , *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="5ebfe-175">이 구성 옵션은 출력 파일 형식마다 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="5ebfe-176">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="5ebfe-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="5ebfe-177">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="5ebfe-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="5ebfe-178">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="5ebfe-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="5ebfe-179">`includeInProject`: 생성된 파일을 프로젝트 파일에 추가할지 여부를 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-179">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="5ebfe-180">**선택적** , *기본값 - false*</span><span class="sxs-lookup"><span data-stu-id="5ebfe-180">**optional** , *default - false*</span></span>
* <span data-ttu-id="5ebfe-181">`sourceMap`: 묶은 파일에 대해 소스 맵을 생성할지 여부를 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-181">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="5ebfe-182">**선택적** , *기본값 - false*</span><span class="sxs-lookup"><span data-stu-id="5ebfe-182">**optional** , *default - false*</span></span>
* <span data-ttu-id="5ebfe-183">`sourceMapRootPath`: 생성된 소스 맵 파일을 저장하기 위한 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-183">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="5ebfe-184">워크플로에 파일 추가하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-184">Add files to workflow</span></span>

<span data-ttu-id="5ebfe-185">다음과 비슷한 또 다른 *custom.css* 파일이 추가되는 예제를 가정해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-185">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="5ebfe-186">*custom.css* 를 축소하고 이를 *site.css* 와 함께 *site.min.css* 파일에 번들하려면 다음과 같이 상대 경로를 *bundleconfig.json* 에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-186">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json* :</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="5ebfe-187">또는 다음과 같은 와일드 카드 사용 패턴을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-187">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="5ebfe-188">이 와일드 카드 사용 패턴은 축소된 파일 패턴을 제외한 모든 CSS 파일을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-188">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="5ebfe-189">애플리케이션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-189">Build the application.</span></span> <span data-ttu-id="5ebfe-190">*site.min.css* 를 열고 *custom.css* 의 내용이 파일 끝에 추가되었음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-190">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="5ebfe-191">환경 기반 번들링 및 축소</span><span class="sxs-lookup"><span data-stu-id="5ebfe-191">Environment-based bundling and minification</span></span>

<span data-ttu-id="5ebfe-192">프로덕션 환경에서는 번들링되고 축소된 앱의 파일을 사용하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-192">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="5ebfe-193">개발하는 중에는 원본 파일을 사용해야 앱을 손쉽게 디버깅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-193">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="5ebfe-194">뷰에서 [Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)를 사용하여 페이지에 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-194">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="5ebfe-195">Environment 태그 도우미는 특정 [환경](xref:fundamentals/environments)에서 실행될 때만 자신의 내용을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-195">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5ebfe-196">다음 `environment` 태그는 `Development` 환경에서 실행하는 경우에만 처리되지 않은 CSS 파일을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-196">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="5ebfe-197">다음 `environment` 태그는 `Development`가 아닌 환경에서 실행할 때 번들링되고 축소된 CSS 파일을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-197">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="5ebfe-198">예를 들어 `Production`이나 `Staging`에서 실행하면 다음 스타일시트의 렌더링이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-198">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="5ebfe-199">Gulp에서 bundleconfig.json 사용하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-199">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="5ebfe-200">앱의 번들링 및 축소 워크플로에서 추가적인 처리를 필요로 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-200">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="5ebfe-201">그 예로 이미지 최적화, 캐시 무효화 및 CDN 자산 처리를 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-201">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="5ebfe-202">이러한 요구 사항을 충족하려면 Gulp를 사용하도록 번들링 및 축소 워크플로를 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-202">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="5ebfe-203">Gulp를 사용하도록 묶음 및 축소 워크플로를 수동으로 변환</span><span class="sxs-lookup"><span data-stu-id="5ebfe-203">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="5ebfe-204">프로젝트 루트에 다음 `devDependencies`가 지정된 *package.json* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-204">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="5ebfe-205">`gulp-uglify` 모듈은 ES(ECMAScript) 2015/ES6 이상을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-205">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="5ebfe-206">ES2015/ES6 이상을 사용하려면 [gulp-terser](https://www.npmjs.com/package/gulp-terser)를 `gulp-uglify` 대신 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-206">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="5ebfe-207">*package.json* 과 동일한 수준에서 다음 명령을 실행하여 종속성을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-207">Install the dependencies by running the following command at the same level as *package.json* :</span></span>

```bash
npm i
```

<span data-ttu-id="5ebfe-208">Gulp CLI를 전역 종속성으로 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-208">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="5ebfe-209">*gulpfile.js* 파일을 프로젝트 루트 아래에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-209">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="5ebfe-210">Gulp 작업 실행하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-210">Run Gulp tasks</span></span>

<span data-ttu-id="5ebfe-211">Visual Studio에서 프로젝트를 빌드하기 전에 Gulp 축소 작업을 트리거하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-211">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="5ebfe-212">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-212">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="5ebfe-213">프로젝트 파일에 다음 [MSBuild 대상](/visualstudio/msbuild/msbuild-targets)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-213">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="5ebfe-214">이 예제에서 `MyPreCompileTarget` 대상 내에 정의된 모든 작업은 사전 정의된 `Build` 대상보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-214">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="5ebfe-215">Visual Studio의 출력 창에 다음과 비슷한 출력이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="5ebfe-215">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="5ebfe-216">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5ebfe-216">Additional resources</span></span>

* [<span data-ttu-id="5ebfe-217">Grunt 사용하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-217">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="5ebfe-218">다양한 환경 사용하기</span><span class="sxs-lookup"><span data-stu-id="5ebfe-218">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="5ebfe-219">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5ebfe-219">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
