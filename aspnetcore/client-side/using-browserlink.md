---
title: ASP.NET Core의 브라우저 링크
author: ncarandini
description: 브라우저 링크가 하나 이상의 웹 브라우저와 개발 환경을 연결하는 Visual Studio 기능이 되는 방법을 설명합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054491"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="ca456-103">ASP.NET Core의 브라우저 링크</span><span class="sxs-lookup"><span data-stu-id="ca456-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="ca456-104">작성자: [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ca456-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ca456-105">브라우저 링크는 Visual Studio 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="ca456-106">개발 환경과 하나 이상의 웹 브라우저 간에 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="ca456-107">브라우저 링크를 사용하여 여러 브라우저에서 웹앱을 한 번에 새로 고칠 수 있으며, 이 기능은 브라우저 간 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="ca456-108">브라우저 링크 설정</span><span class="sxs-lookup"><span data-stu-id="ca456-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca456-109">[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="ca456-110">또한 ASP.NET Core :::no-loc(Razor)::: Pages 또는 MVC 프로젝트의 경우, <xref:mvc/views/view-compilation>에 설명된 대로 :::no-loc(Razor):::( *.cshtml* ) 파일의 런타임 컴파일을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-110">For ASP.NET Core :::no-loc(Razor)::: Pages or MVC projects, also enable runtime compilation of :::no-loc(Razor)::: ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="ca456-111">:::no-loc(Razor)::: 구문 변경 내용은 런타임 컴파일을 사용하도록 설정한 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-111">:::no-loc(Razor)::: syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="ca456-112">ASP.NET Core 2.0 프로젝트를 ASP.NET Core 2.1로 변환하고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)로 전환하는 경우, 브라우저 링크 기능을 위해 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="ca456-113">ASP.NET Core 2.1 프로젝트 템플릿은 기본적으로 `Microsoft.AspNetCore.App` 메타패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="ca456-114">ASP.NET Core 2.0 **웹 애플리케이션** , **비어 있음** 및 **웹 API** 프로젝트 템플릿은 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)의 패키지 참조를 포함하는 [Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="ca456-115">따라서 `Microsoft.AspNetCore.All` 메타패키지를 사용하는 경우, 브라우저 링크를 사용할 수 있도록 설정하는 추가 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="ca456-116">ASP.NET Core 1.x **웹 애플리케이션** 프로젝트 템플릿에는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지의 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="ca456-117">다른 프로젝트 형식의 경우, `Microsoft.VisualStudio.Web.BrowserLink`의 패키지 참조를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="ca456-118">Configuration</span><span class="sxs-lookup"><span data-stu-id="ca456-118">Configuration</span></span>

<span data-ttu-id="ca456-119">`Startup.Configure` 메서드에서 `UseBrowserLink`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="ca456-120">`UseBrowserLink` 호출은 일반적으로 개발 환경에서만 브라우저 링크를 사용하도록 설정하는 `if` 블록 내에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="ca456-121">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="ca456-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="ca456-122">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca456-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="ca456-123">브라우저 링크 사용 방법</span><span class="sxs-lookup"><span data-stu-id="ca456-123">How to use Browser Link</span></span>

<span data-ttu-id="ca456-124">ASP.NET Core 프로젝트를 열면 Visual Studio에서 **디버그 대상** 도구 모음 컨트롤 옆에 브라우저 링크 도구 모음 컨트롤이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![브라우저 링크 드롭다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="ca456-126">브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="ca456-127">한 번에 여러 브라우저에서 웹앱을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="ca456-128">**브라우저 링크 대시보드** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="ca456-129">**브라우저 링크** 를 사용하거나 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="ca456-130">참고: Visual Studio에서 브라우저 링크는 기본적으로 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="ca456-131">[CSS 자동 동기화](#enable-or-disable-css-auto-sync)를 사용하거나 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="ca456-132">한 번에 여러 브라우저에서 웹앱 새로 고침</span><span class="sxs-lookup"><span data-stu-id="ca456-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="ca456-133">프로젝트를 시작할 때 실행할 단일 웹 브라우저를 선택하려면 **디버그 대상** 도구 모음 컨트롤의 드롭다운 메뉴를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="ca456-135">한 번에 여러 브라우저를 열려면 동일한 드롭다운에서 **브라우저 선택...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="ca456-136"><kbd>Ctrl</kbd> 키를 누른 채 원하는 브라우저를 선택하고 **찾아보기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![한 번에 많은 브라우저 열기](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="ca456-138">다음 스크린샷은 인덱스 뷰가 열려 있는 Visual Studio와 두 개의 열린 브라우저를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![두 브라우저와 동기화 예제](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="ca456-140">브라우저 링크 도구 모음 컨트롤을 마우스로 가리켜 프로젝트에 연결된 브라우저를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![가리키기 팁](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="ca456-142">인덱스 뷰를 변경하고 브라우저 링크 새로 고침 단추를 클릭하면 연결된 모든 브라우저가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="ca456-144">브라우저 링크는 Visual Studio 외부에서 시작하여 앱 URL로 이동하는 브라우저에서도 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="ca456-145">브라우저 링크 대시보드</span><span class="sxs-lookup"><span data-stu-id="ca456-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="ca456-146">브라우저 링크 드롭다운 메뉴에서 **브라우저 링크 대시보드** 창을 열고 열린 브라우저와의 연결을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="ca456-148">연결된 브라우저가 없는 경우, **브라우저에서 보기** 링크를 선택하여 디버깅하지 않는 세션을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="ca456-150">그러지 않으면 연결된 브라우저가 각 브라우저에 표시되는 페이지의 경로와 함께 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="ca456-152">개별 브라우저 이름을 클릭하여 해당 브라우저만 새로 고칠 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="ca456-153">브라우저 링크 사용 또는 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="ca456-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="ca456-154">브라우저 링크를 사용하지 않도록 설정한 후 다시 사용하도록 설정하는 경우, 브라우저를 새로 고쳐 다시 연결해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="ca456-155">CSS 자동 동기화 사용 또는 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="ca456-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="ca456-156">CSS 자동 동기화를 사용하도록 설정한 경우, CSS 파일을 변경하면 연결된 브라우저가 자동으로 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="ca456-157">작동 방법</span><span class="sxs-lookup"><span data-stu-id="ca456-157">How it works</span></span>

<span data-ttu-id="ca456-158">브라우저 링크는 [:::no-loc(SignalR):::](xref:signalr/introduction)를 사용하여 Visual Studio와 브라우저 간에 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-158">Browser Link uses [:::no-loc(SignalR):::](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="ca456-159">브라우저 링크를 사용하도록 설정한 경우, Visual Studio는 여러 클라이언트(브라우저)가 연결할 수 있는 :::no-loc(SignalR)::: 서버 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-159">When Browser Link is enabled, Visual Studio acts as a :::no-loc(SignalR)::: server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="ca456-160">또한 브라우저 링크는 ASP.NET Core 요청 파이프라인에 미들웨어 구성 요소를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="ca456-161">이 구성 요소는 서버의 모든 페이지 요청에 특수 `<script>` 참조를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="ca456-162">브라우저에서 **소스 보기** 를 선택하고 `<body>` 태그 콘텐츠의 끝으로 스크롤하면 스크립트 참조를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="ca456-163">소스 파일은 수정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-163">Your source files aren't modified.</span></span> <span data-ttu-id="ca456-164">미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="ca456-165">브라우저 쪽 코드는 모두 JavaScript이므로, 브라우저 플러그 인을 요구하지 않고 :::no-loc(SignalR):::를 지원하는 모든 브라우저에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ca456-165">Because the browser-side code is all JavaScript, it works on all browsers that :::no-loc(SignalR)::: supports without requiring a browser plug-in.</span></span>
