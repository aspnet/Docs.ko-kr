---
title: ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션하는 방법 알아보기
author: wadepickett
description: ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 마이그레이션하기 시작 하는 방법에 대해 알아봅니다.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326634"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="8f79c-103">ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8f79c-104">이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview)로 마이그레이션하기 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="8f79c-105">이 프로세스에서는 ASP.NET MVC와 관련 된 변경 내용을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="8f79c-106">ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="8f79c-107">이 문서에서는 다음 내용을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-107">This article covers:</span></span>

* <span data-ttu-id="8f79c-108">초기 설정.</span><span class="sxs-lookup"><span data-stu-id="8f79c-108">Initial setup.</span></span>
* <span data-ttu-id="8f79c-109">기본 컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-109">Basic controllers and views.</span></span>
* <span data-ttu-id="8f79c-110">정적 콘텐츠.</span><span class="sxs-lookup"><span data-stu-id="8f79c-110">Static content.</span></span>
* <span data-ttu-id="8f79c-111">클라이언트 쪽 종속성.</span><span class="sxs-lookup"><span data-stu-id="8f79c-111">Client-side dependencies.</span></span>

<span data-ttu-id="8f79c-112">구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 [ASP.NET Core 구성 마이그레이션](xref:migration/configuration) 및 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8f79c-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="8f79c-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="8f79c-114">스타터 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="8f79c-115">마이그레이션할 Visual Studio의 예제 ASP.NET MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="8f79c-116">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="8f79c-117">**ASP.NET 웹 응용 프로그램 (.NET Framework)** 을 선택 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="8f79c-118">다음 단계에서 만든 ASP.NET Core 프로젝트와 네임 스페이스가 일치 하도록 프로젝트 이름을 *WebApp1* 로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="8f79c-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-119">Select **Create**.</span></span>
1. <span data-ttu-id="8f79c-120">**MVC**를 선택 하 고 **만들기**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="8f79c-121">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="8f79c-122">마이그레이션할 새 ASP.NET Core 프로젝트를 사용 하 여 새 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="8f79c-123">Visual Studio의 두 번째 인스턴스를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="8f79c-124">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="8f79c-125">**ASP.NET Core 웹 응용 프로그램**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-125">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="8f79c-126">**새 프로젝트 구성** 대화 상자에서 프로젝트 이름을 *WebApp1*로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="8f79c-127">동일한 프로젝트 이름을 사용 하려면 위치를 이전 프로젝트가 아닌 다른 디렉터리로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="8f79c-128">동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="8f79c-129">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-129">Select **Create**.</span></span>
1. <span data-ttu-id="8f79c-130">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="8f79c-131">**웹 응용 프로그램 (모델-뷰-컨트롤러)** 프로젝트 템플릿을 선택 하 고 **만들기**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="8f79c-132">MVC를 사용 하도록 ASP.NET Core 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="8f79c-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="8f79c-133">ASP.NET Core 3.0 이상 프로젝트에서 .NET Framework는 더 이상 지원 되는 대상 프레임 워크가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="8f79c-134">프로젝트는 .NET Core를 대상으로 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-134">Your project must target .NET Core.</span></span> <span data-ttu-id="8f79c-135">MVC를 포함 하는 ASP.NET Core 공유 프레임 워크는 .NET Core 런타임 설치의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="8f79c-136">프로젝트 파일에서 `Microsoft.NET.Sdk.Web` SDK를 사용할 때 공유 프레임워크가 자동으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="8f79c-137">자세한 내용은 [프레임 워크 참조](xref:migration/22-to-30#framework-reference)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="8f79c-138">ASP.NET Core에서 클래스는 `Startup` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="8f79c-139">*Global.asax*를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="8f79c-140">모든 앱 시작 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="8f79c-141">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="8f79c-142">ASP.NET Core 프로젝트에서 *Startup.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="8f79c-143">ASP.NET Core 앱은 미들웨어를 사용 하 여 프레임 워크 기능을 옵트인 (opt in) 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="8f79c-144">이전 템플릿에서 생성 된 코드는 다음 서비스 및 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="8f79c-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>확장 메서드는 컨트롤러, API 관련 기능 및 뷰에 대 한 MVC 서비스 지원을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="8f79c-146">MVC 서비스 등록 옵션에 대 한 자세한 내용은 [mvc 서비스 등록](xref:migration/22-to-30#mvc-service-registration) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="8f79c-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>확장 메서드는 정적 파일 처리기를 추가 합니다 `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="8f79c-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="8f79c-148">`UseStaticFiles`확장 메서드는 이전에 호출 해야 합니다 `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="8f79c-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="8f79c-149">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="8f79c-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>확장 메서드는 라우팅을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="8f79c-151">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="8f79c-152">이 기존 구성에는 예제 ASP.NET MVC 프로젝트를 마이그레이션하는 데 필요한 항목이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="8f79c-153">미들웨어 옵션 ASP.NET Core에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="8f79c-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="8f79c-154">컨트롤러 및 뷰 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-154">Migrate controllers and views</span></span>

<span data-ttu-id="8f79c-155">ASP.NET Core 프로젝트에서 마이그레이션할 ASP.NET MVC 프로젝트의 컨트롤러 및 뷰 클래스와 동일한 이름을 사용 하 여 자리 표시자 역할을 하는 새로운 빈 컨트롤러 클래스 및 뷰 클래스가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="8f79c-156">ASP.NET Core *WebApp1* 프로젝트에는 ASP.NET MVC 프로젝트와 동일한 이름의 최소 예제 컨트롤러 및 뷰가 이미 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="8f79c-157">ASP.NET MVC 컨트롤러에 대 한 자리 표시자와 ASP.NET MVC *WebApp1* 프로젝트에서 마이그레이션할 뷰를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="8f79c-158">ASP.NET MVC에서 메서드를 복사 `HomeController` 하 여 새 ASP.NET Core 메서드를 `HomeController` 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="8f79c-159">작업 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="8f79c-160">ASP.NET MVC 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> . MVC ASP.NET Core 작업 메서드는 대신을 반환 합니다 `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="8f79c-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="8f79c-161">`ActionResult`는 `IActionResult`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="8f79c-162">ASP.NET Core 프로젝트에서 *Views/Home* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목**추가를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="8f79c-163">**기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views/Home* 디렉터리로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="8f79c-164">*About. cshtml*및 *Contact*를 선택 하 고,이 파일을 *인덱스* 를 선택 하 고, Razor **추가**를 선택 하 고 기존 파일을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="8f79c-165">자세한 내용은 <xref:mvc/controllers/actions> 및 <xref:mvc/views/overview>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="8f79c-166">각 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="8f79c-166">Test each method</span></span>

<span data-ttu-id="8f79c-167">그러나 각 컨트롤러 끝점을 테스트할 수 있지만 레이아웃 및 스타일은 문서의 뒷부분에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="8f79c-168">ASP.NET Core 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="8f79c-169">현재 포트 번호를 ASP.NET Core 프로젝트에 사용 되는 포트 번호로 바꿔서 실행 중인 ASP.NET Core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="8f79c-170">`https://localhost:44375/home/about`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="8f79c-171">정적 콘텐츠 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-171">Migrate static content</span></span>

<span data-ttu-id="8f79c-172">ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트 디렉터리에서 호스팅되며 서버 쪽 파일과 혼합 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="8f79c-173">ASP.NET Core에서 정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="8f79c-174">기본 디렉터리는 *{content root}/wwwroot*이지만 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="8f79c-175">자세한 내용은 [ASP.NET Core의 정적 파일](xref:fundamentals/static-files#serve-static-files)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="8f79c-176">ASP.NET MVC *WebApp1* 프로젝트의 정적 콘텐츠를 ASP.NET Core *WebApp1* 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="8f79c-177">ASP.NET Core 프로젝트에서 *wwwroot* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목**추가를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="8f79c-178">**기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="8f79c-179">*Favicon* 파일을 선택한 다음 **추가**를 선택 하 고 기존 파일을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="8f79c-180">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-180">Migrate the layout files</span></span>

<span data-ttu-id="8f79c-181">ASP.NET MVC 프로젝트 레이아웃 파일을 ASP.NET Core 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="8f79c-182">ASP.NET Core 프로젝트에서 *Views* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목**추가를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="8f79c-183">**기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views* 디렉터리로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="8f79c-184">*_ViewStart* 파일을 선택한 다음 **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="8f79c-185">ASP.NET MVC 프로젝트 공유 레이아웃 파일을 ASP.NET Core 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="8f79c-186">ASP.NET Core 프로젝트에서 *Views/Shared* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목**추가를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="8f79c-187">**기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views/Shared* 디렉터리로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="8f79c-188">*_Layout* 파일을 선택한 다음 **추가**를 선택 하 여 기존 파일을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="8f79c-189">ASP.NET Core 프로젝트에서 *_Layout* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="8f79c-190">아래에 표시 된 완료 된 코드와 일치 하도록 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="8f79c-191">아래 완료 된 코드와 일치 하도록 부트스트랩 CSS 포함을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="8f79c-192">`@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="8f79c-193">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="8f79c-194">부트스트랩 CSS 포함에 대해 완료 된 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="8f79c-195">다음 완성 된 코드와 일치 하도록 jQuery 및 부트스트랩 JavaScript 포함을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="8f79c-196">`@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="8f79c-197">`@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="8f79c-198">JQuery 및 부트스트랩 JavaScript를 포함 하는 완성 된 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="8f79c-199">업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="8f79c-200">브라우저에서 사이트를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-200">View the site in the browser.</span></span> <span data-ttu-id="8f79c-201">예상 되는 스타일을 사용 하 여 렌더링 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="8f79c-202">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="8f79c-202">Configure bundling and minification</span></span>

<span data-ttu-id="8f79c-203">ASP.NET Core는 [WebOptimizer](https://github.com/ligershark/WebOptimizer) 및 기타 유사한 라이브러리와 같은 여러 오픈 소스 번들 및 축소 솔루션과 호환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="8f79c-204">ASP.NET Core는 네이티브 묶음 및 축소 솔루션을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="8f79c-205">묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="8f79c-206">HTTP 500 오류 해결</span><span class="sxs-lookup"><span data-stu-id="8f79c-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="8f79c-207">문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="8f79c-208">예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="8f79c-209">기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 환경에서 *개발*중일 때 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="8f79c-210">다음 코드에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="8f79c-211">ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="8f79c-212">일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="8f79c-213">자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f79c-214">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8f79c-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="8f79c-215">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8f79c-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8f79c-216">이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview) 2.2로 마이그레이션하기 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="8f79c-217">이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="8f79c-218">ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="8f79c-219">이 문서에서는 다음 내용을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-219">This article covers:</span></span>

* <span data-ttu-id="8f79c-220">초기 설정</span><span class="sxs-lookup"><span data-stu-id="8f79c-220">Initial setup</span></span>
* <span data-ttu-id="8f79c-221">기본 컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-221">Basic controllers and views</span></span>
* <span data-ttu-id="8f79c-222">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="8f79c-222">Static content</span></span>
* <span data-ttu-id="8f79c-223">클라이언트 쪽 종속성.</span><span class="sxs-lookup"><span data-stu-id="8f79c-223">Client-side dependencies.</span></span>

<span data-ttu-id="8f79c-224">구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 및을 참조 하십시오 <xref:migration/configuration> <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="8f79c-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="8f79c-225">샘플의 버전 번호가 최신이 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="8f79c-226">스타터 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="8f79c-227">업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="8f79c-228">*WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만든 ASP.NET Core 프로젝트와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="8f79c-231">*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5*로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="8f79c-232">Visual Studio에서 새 솔루션 이름 (*Mvc5*)을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="8f79c-233">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="8f79c-234">두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 (*WebApp1*)을 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="8f79c-235">동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="8f79c-236">같은 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="8f79c-239">*선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="8f79c-240">프로젝트 이름을 *WebApp1*로 하 고 **개별 사용자 계정**에 대 한 인증 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="8f79c-241">이 앱의 이름을 *FullAspNetCore*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="8f79c-242">이 프로젝트를 만들면 변환 시 시간이 절약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="8f79c-243">템플릿 생성 코드에서 최종 결과를 볼 수 있습니다. 변환 프로젝트에 코드를 복사 하거나 템플릿 생성 프로젝트와 비교할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="8f79c-244">MVC를 사용 하도록 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="8f79c-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="8f79c-245">.NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="8f79c-246">이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="8f79c-247">.NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="8f79c-248">`Microsoft.AspNetCore.Mvc` 는 ASP.NET Core MVC 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="8f79c-249">`Microsoft.AspNetCore.StaticFiles` 는 정적 파일 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="8f79c-250">ASP.NET Core apps는 정적 파일을 제공 하는 등 미들웨어에 대해 명시적으로 옵트인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="8f79c-251">자세한 내용은 [정적 파일](xref:fundamentals/static-files)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="8f79c-252">*Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="8f79c-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>확장 메서드는 정적 파일 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="8f79c-254">자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="8f79c-255">컨트롤러 및 뷰 추가</span><span class="sxs-lookup"><span data-stu-id="8f79c-255">Add a controller and view</span></span>

<span data-ttu-id="8f79c-256">이 섹션에서는 다음 섹션에서 마이그레이션된 ASP.NET MVC 컨트롤러 및 뷰에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="8f79c-257">*Controller* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="8f79c-258">*HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="8f79c-260">*Views* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="8f79c-261">*뷰/홈* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="8f79c-262">뷰 */홈* 디렉터리에 *Index. cshtml* 이라는 \*\* Razor 뷰\*\* 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![새 항목 추가 대화 상자](mvc/_static/view.png)

<span data-ttu-id="8f79c-264">프로젝트 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-264">The project structure is shown below:</span></span>

![WebApp1의 파일 및 디렉터리를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="8f79c-266">*Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="8f79c-267">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-267">Run the app.</span></span>

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

<span data-ttu-id="8f79c-269">자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="8f79c-270">다음 기능을 사용 하려면 예제 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트로 마이그레이션해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="8f79c-271">클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)</span><span class="sxs-lookup"><span data-stu-id="8f79c-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="8f79c-272">controllers</span><span class="sxs-lookup"><span data-stu-id="8f79c-272">controllers</span></span>

* <span data-ttu-id="8f79c-273">뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-273">views</span></span>

* <span data-ttu-id="8f79c-274">모델</span><span class="sxs-lookup"><span data-stu-id="8f79c-274">models</span></span>

* <span data-ttu-id="8f79c-275">묶음</span><span class="sxs-lookup"><span data-stu-id="8f79c-275">bundling</span></span>

* <span data-ttu-id="8f79c-276">filters</span><span class="sxs-lookup"><span data-stu-id="8f79c-276">filters</span></span>

* <span data-ttu-id="8f79c-277">로그인/로그 아웃 합니다 Identity (다음 자습서에서 수행 됨).</span><span class="sxs-lookup"><span data-stu-id="8f79c-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="8f79c-278">컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-278">Controllers and views</span></span>

* <span data-ttu-id="8f79c-279">ASP.NET MVC의 각 메서드 `HomeController` 를 새로 복사 `HomeController` 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="8f79c-280">ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다. <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ASP.NET CORE mvc에서는 작업 메서드가 대신을 반환 합니다 `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="8f79c-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="8f79c-281">`ActionResult` 는 `IActionResult` 를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="8f79c-282">*Contact.cshtml*ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한*파일을 *복사 합니다.* Razor</span><span class="sxs-lookup"><span data-stu-id="8f79c-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="8f79c-283">각 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="8f79c-283">Test each method</span></span>

<span data-ttu-id="8f79c-284">레이아웃 파일 및 스타일은 아직 마이그레이션되지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="8f79c-285">및 뷰에 대해 생성 된 레이아웃 파일 링크를 `About` `Contact` 아직 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="8f79c-286">현재 포트 번호를 ASP.NET core 프로젝트에서 사용 되는 포트 번호로 바꿔 실행 중인 ASP.NET core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="8f79c-287">예: `https://localhost:44375/home/about`</span><span class="sxs-lookup"><span data-stu-id="8f79c-287">For example: `https://localhost:44375/home/about`.</span></span>

![연락처 페이지](mvc/_static/contact-page.png)

<span data-ttu-id="8f79c-289">스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오.</span><span class="sxs-lookup"><span data-stu-id="8f79c-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="8f79c-290">다음 섹션에서 스타일을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="8f79c-291">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="8f79c-291">Static content</span></span>

<span data-ttu-id="8f79c-292">ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과는 결합 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="8f79c-293">ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 디렉터리에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="8f79c-294">ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="8f79c-295">이 샘플 변환에서:</span><span class="sxs-lookup"><span data-stu-id="8f79c-295">In this sample conversion:</span></span>

* <span data-ttu-id="8f79c-296">ASP.NET MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="8f79c-297">ASP.NET MVC 프로젝트는 해당 스타일에 [부트스트랩](https://getbootstrap.com/) 을 사용 하 여 *콘텐츠* 및 *스크립트* 디렉터리에 부트스트랩 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="8f79c-298">ASP.NET MVC 프로젝트를 생성 한 템플릿은 레이아웃 파일 (*Views/Shared/_Layout cshtml*)의 부트스트랩을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="8f79c-299">*bootstrap.js* 및 *부트스트랩 .css* 파일은 ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 디렉터리로 복사 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="8f79c-300">대신이 문서는 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="8f79c-301">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-301">Migrate the layout file</span></span>

* <span data-ttu-id="8f79c-302">ASP.NET MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewStart.* s a s 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="8f79c-303">*_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="8f79c-304">*뷰/공유* 디렉터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="8f79c-305">*선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewImports* 를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="8f79c-306">*_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8f79c-307">*_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="8f79c-308">태그 도우미는 새 레이아웃 파일에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="8f79c-309">*_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="8f79c-310">ASP.NET MVC 프로젝트의 *views/shared* 디렉터리에서 ASP.NET Core 프로젝트의 *views/shared* 디렉터리로 *_Layout cshtml* 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="8f79c-311">*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="8f79c-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="8f79c-312">`@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="8f79c-313">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="8f79c-314">줄을 주석 `@Html.Partial("_LoginPartial")` 으로 처리 `@*...*@` 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="8f79c-315">자세한 내용은 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="8f79c-316">`@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="8f79c-317">`@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="8f79c-318">부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="8f79c-319">JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="8f79c-320">업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="8f79c-321">브라우저에서 사이트를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-321">View the site in the browser.</span></span> <span data-ttu-id="8f79c-322">이제 올바른 스타일을 적절히 로드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="8f79c-323">*선택 사항:* 새 레이아웃 파일을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="8f79c-324">*FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="8f79c-325">새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="8f79c-326">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="8f79c-326">Configure bundling and minification</span></span>

<span data-ttu-id="8f79c-327">묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="8f79c-328">HTTP 500 오류 해결</span><span class="sxs-lookup"><span data-stu-id="8f79c-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="8f79c-329">문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="8f79c-330">예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="8f79c-331">기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 구성이 *개발*될 때 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="8f79c-332">다음 코드의 예제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="8f79c-333">ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="8f79c-334">일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="8f79c-335">자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f79c-336">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8f79c-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="8f79c-337">이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview) 2.1로 마이그레이션하기 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="8f79c-338">이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="8f79c-339">ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="8f79c-340">이 문서에서는 다음 내용을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-340">This article covers:</span></span>

* <span data-ttu-id="8f79c-341">초기 설정</span><span class="sxs-lookup"><span data-stu-id="8f79c-341">Initial setup</span></span>
* <span data-ttu-id="8f79c-342">기본 컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-342">Basic controllers and views</span></span>
* <span data-ttu-id="8f79c-343">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="8f79c-343">Static content</span></span>
* <span data-ttu-id="8f79c-344">클라이언트 쪽 종속성.</span><span class="sxs-lookup"><span data-stu-id="8f79c-344">Client-side dependencies.</span></span>

<span data-ttu-id="8f79c-345">구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 [ASP.NET Core 구성 마이그레이션](xref:migration/configuration) 및 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="8f79c-346">샘플의 버전 번호가 최신이 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="8f79c-347">스타터 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="8f79c-348">업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="8f79c-349">*WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만든 ASP.NET Core 프로젝트와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="8f79c-352">*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5*로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="8f79c-353">Visual Studio에서 새 솔루션 이름 (*Mvc5*)을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="8f79c-354">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8f79c-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="8f79c-355">두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 (*WebApp1*)을 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="8f79c-356">동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="8f79c-357">같은 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="8f79c-360">*선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="8f79c-361">프로젝트 이름을 *WebApp1*로 하 고 **개별 사용자 계정**에 대 한 인증 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="8f79c-362">이 앱의 이름을 *FullAspNetCore*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="8f79c-363">이 프로젝트를 만들면 변환 시 시간이 절약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="8f79c-364">템플릿 생성 코드에서 최종 결과를 볼 수 있습니다. 변환 프로젝트에 코드를 복사 하거나 템플릿 생성 프로젝트와 비교할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="8f79c-365">MVC를 사용 하도록 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="8f79c-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="8f79c-366">.NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="8f79c-367">이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="8f79c-368">.NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="8f79c-369">`Microsoft.AspNetCore.Mvc` 는 ASP.NET Core MVC 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="8f79c-370">`Microsoft.AspNetCore.StaticFiles` 는 정적 파일 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="8f79c-371">ASP.NET Core apps는 정적 파일을 제공 하는 등 미들웨어에 대해 명시적으로 옵트인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="8f79c-372">자세한 내용은 [정적 파일](xref:fundamentals/static-files)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="8f79c-373">*Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="8f79c-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>확장 메서드는 정적 파일 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="8f79c-375">`UseMvc`확장 메서드는 라우팅을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="8f79c-376">자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="8f79c-377">컨트롤러 및 뷰 추가</span><span class="sxs-lookup"><span data-stu-id="8f79c-377">Add a controller and view</span></span>

<span data-ttu-id="8f79c-378">이 섹션에서는 다음 섹션에서 마이그레이션된 ASP.NET MVC 컨트롤러 및 뷰에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="8f79c-379">*Controller* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="8f79c-380">*HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="8f79c-382">*Views* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="8f79c-383">*뷰/홈* 디렉터리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="8f79c-384">뷰 */홈* 디렉터리에 *Index. cshtml* 이라는 \*\* Razor 뷰\*\* 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![새 항목 추가 대화 상자](mvc/_static/view.png)

<span data-ttu-id="8f79c-386">프로젝트 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-386">The project structure is shown below:</span></span>

![WebApp1의 파일 및 디렉터리를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="8f79c-388">*Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="8f79c-389">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-389">Run the app.</span></span>

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

<span data-ttu-id="8f79c-391">자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="8f79c-392">다음 기능을 사용 하려면 예제 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트로 마이그레이션해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="8f79c-393">클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)</span><span class="sxs-lookup"><span data-stu-id="8f79c-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="8f79c-394">controllers</span><span class="sxs-lookup"><span data-stu-id="8f79c-394">controllers</span></span>

* <span data-ttu-id="8f79c-395">뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-395">views</span></span>

* <span data-ttu-id="8f79c-396">모델</span><span class="sxs-lookup"><span data-stu-id="8f79c-396">models</span></span>

* <span data-ttu-id="8f79c-397">묶음</span><span class="sxs-lookup"><span data-stu-id="8f79c-397">bundling</span></span>

* <span data-ttu-id="8f79c-398">filters</span><span class="sxs-lookup"><span data-stu-id="8f79c-398">filters</span></span>

* <span data-ttu-id="8f79c-399">로그인/로그 아웃 합니다 Identity (다음 자습서에서 수행 됨).</span><span class="sxs-lookup"><span data-stu-id="8f79c-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="8f79c-400">컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="8f79c-400">Controllers and views</span></span>

* <span data-ttu-id="8f79c-401">ASP.NET MVC의 각 메서드 `HomeController` 를 새로 복사 `HomeController` 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="8f79c-402">ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다. <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ASP.NET CORE mvc에서는 작업 메서드가 대신을 반환 합니다 `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="8f79c-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="8f79c-403">`ActionResult` 는 `IActionResult` 를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="8f79c-404">*Contact.cshtml*ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한*파일을 *복사 합니다.* Razor</span><span class="sxs-lookup"><span data-stu-id="8f79c-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="8f79c-405">각 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="8f79c-405">Test each method</span></span>

<span data-ttu-id="8f79c-406">레이아웃 파일 및 스타일은 아직 마이그레이션되지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="8f79c-407">및 뷰에 대해 생성 된 레이아웃 파일 링크를 `About` `Contact` 아직 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="8f79c-408">현재 포트 번호를 ASP.NET core 프로젝트에서 사용 되는 포트 번호로 바꿔 실행 중인 ASP.NET core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="8f79c-409">예: `https://localhost:44375/home/about`</span><span class="sxs-lookup"><span data-stu-id="8f79c-409">For example: `https://localhost:44375/home/about`.</span></span>

![연락처 페이지](mvc/_static/contact-page.png)

<span data-ttu-id="8f79c-411">스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오.</span><span class="sxs-lookup"><span data-stu-id="8f79c-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="8f79c-412">다음 섹션에서 스타일을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="8f79c-413">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="8f79c-413">Static content</span></span>

<span data-ttu-id="8f79c-414">ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과는 결합 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="8f79c-415">ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 디렉터리에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="8f79c-416">ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="8f79c-417">이 샘플 변환에서:</span><span class="sxs-lookup"><span data-stu-id="8f79c-417">In this sample conversion:</span></span>

* <span data-ttu-id="8f79c-418">ASP.NET MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="8f79c-419">ASP.NET MVC 프로젝트는 해당 스타일에 [부트스트랩](https://getbootstrap.com/) 을 사용 하 여 *콘텐츠* 및 *스크립트* 디렉터리에 부트스트랩 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="8f79c-420">ASP.NET MVC 프로젝트를 생성 한 템플릿은 레이아웃 파일 (*Views/Shared/_Layout cshtml*)의 부트스트랩을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="8f79c-421">*bootstrap.js* 및 *부트스트랩 .css* 파일은 ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 디렉터리로 복사 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="8f79c-422">대신이 문서는 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="8f79c-423">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f79c-423">Migrate the layout file</span></span>

* <span data-ttu-id="8f79c-424">ASP.NET MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewStart.* s a s 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="8f79c-425">*_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="8f79c-426">*뷰/공유* 디렉터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="8f79c-427">*선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewImports* 를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="8f79c-428">*_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8f79c-429">*_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="8f79c-430">태그 도우미는 새 레이아웃 파일에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="8f79c-431">*_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="8f79c-432">ASP.NET MVC 프로젝트의 *views/shared* 디렉터리에서 ASP.NET Core 프로젝트의 *views/shared* 디렉터리로 *_Layout cshtml* 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="8f79c-433">*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="8f79c-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="8f79c-434">`@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="8f79c-435">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="8f79c-436">줄을 주석 `@Html.Partial("_LoginPartial")` 으로 처리 `@*...*@` 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="8f79c-437">자세한 내용은 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="8f79c-438">`@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="8f79c-439">`@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="8f79c-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="8f79c-440">부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="8f79c-441">JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="8f79c-442">업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="8f79c-443">브라우저에서 사이트를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-443">View the site in the browser.</span></span> <span data-ttu-id="8f79c-444">이제 올바른 스타일을 적절히 로드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="8f79c-445">*선택 사항:* 새 레이아웃 파일을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="8f79c-446">*FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="8f79c-447">새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="8f79c-448">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="8f79c-448">Configure bundling and minification</span></span>

<span data-ttu-id="8f79c-449">묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="8f79c-450">HTTP 500 오류 해결</span><span class="sxs-lookup"><span data-stu-id="8f79c-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="8f79c-451">문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="8f79c-452">예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="8f79c-453">기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 구성이 *개발*될 때 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="8f79c-454">다음 코드의 예제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="8f79c-455">ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="8f79c-456">일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f79c-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="8f79c-457">자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f79c-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f79c-458">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8f79c-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
