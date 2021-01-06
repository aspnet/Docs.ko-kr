---
title: ASP.NET Core 및 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 이상은 ASP.NET Core 3.1 이상에서 작동합니다.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059457"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="2cf0e-103">ASP.NET Core 및 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="2cf0e-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2cf0e-104">작성자: [Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="2cf0e-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="2cf0e-105">ASP.NET Core에서 Entity Framework 6 사용</span><span class="sxs-lookup"><span data-stu-id="2cf0e-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="2cf0e-106">새로 개발하는 경우 [Entity Framework Core](/ef/)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="2cf0e-107">[다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample)에서는 종료된 앱을 ASP.NET Core로 마이그레이션하는 데 사용할 수 있는 [EF6(Entity Framework 6)](/ef/ef6)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cf0e-108">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2cf0e-108">Additional resources</span></span>

* [<span data-ttu-id="2cf0e-109">Entity Framework - 코드 기반 구성</span><span class="sxs-lookup"><span data-stu-id="2cf0e-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2cf0e-110">작성자: [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="2cf0e-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="2cf0e-111">이 문서에는 ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="2cf0e-112">개요</span><span class="sxs-lookup"><span data-stu-id="2cf0e-112">Overview</span></span> 

<span data-ttu-id="2cf0e-113">Entity Framework 6을 사용하려면 Entity Framework 6에서 .NET Core를 지원하지 않으므로 .NET Framework에 대해 프로젝트를 컴파일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="2cf0e-114">플랫폼 간 기능이 필요한 경우 [Entity Framework Core](/ef/)로 업그레이드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="2cf0e-115">ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 권장되는 방법은 EF6 컨텍스트 및 모델 클래스를 .NET Framework를 대상으로 하는 클래스 라이브러리 프로젝트에 넣는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="2cf0e-116">ASP.NET Core 프로젝트에서 클래스 라이브러리에 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="2cf0e-117">샘플 [EF6 및 ASP.NET Core 프로젝트가 있는 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="2cf0e-118">.NET Core 프로젝트는 *Enable-Migrations* 와 같은 EF6 명령이 요구하는 모든 기능을 지원하지 않으므로 ASP.NET Core 프로젝트에 EF6 컨텍스트를 배치할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="2cf0e-119">EF6 컨텍스트를 찾는 프로젝트 형식에 관계없이 EF6 명령줄 도구만 EF6 컨텍스트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="2cf0e-120">예를 들어 `Scaffold-DbContext`는 Entity Framework Core에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="2cf0e-121">데이터베이스를 EF6 모델로 리버스 엔지니어링해야 하는 경우 <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="2cf0e-122">ASP.NET Core 프로젝트에서 전체 프레임워크 및 EF6 참조</span><span class="sxs-lookup"><span data-stu-id="2cf0e-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="2cf0e-123">ASP.NET Core 프로젝트는 .NET Framework를 대상으로 하고 EF6을 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="2cf0e-124">예를 들어 ASP.NET Core 프로젝트의 *.csproj* 파일은 다음 예제와 유사합니다(파일의 관련 부분만 표시됨).</span><span class="sxs-lookup"><span data-stu-id="2cf0e-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="2cf0e-125">새 프로젝트를 만들 때 **ASP.NET Core 웹 애플리케이션(.NET Framework)** 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="2cf0e-126">연결 문자열 처리</span><span class="sxs-lookup"><span data-stu-id="2cf0e-126">Handle connection strings</span></span>    

<span data-ttu-id="2cf0e-127">EF6 클래스 라이브러리 프로젝트에서 사용할 EF6 명령줄 도구에는 컨텍스트를 인스턴스화할 수 있도록 기본 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="2cf0e-128">그러나 ASP.NET Core 프로젝트에서 사용할 연결 문자열을 지정하려는 경우 컨텍스트 생성자에 연결 문자열로 전달할 수 있는 매개 변수가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="2cf0e-129">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="2cf0e-130">EF6 컨텍스트에는 매개 변수가 없는 생성자가 없으므로 EF6 프로젝트는 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>의 구현을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="2cf0e-131">EF6 명령줄 도구는 해당 구현을 찾아 사용하므로 컨텍스트를 인스턴스화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="2cf0e-132">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="2cf0e-133">이 샘플 코드에서 `IDbContextFactory` 구현은 하드 코딩된 연결 문자열을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="2cf0e-134">이것은 명령줄 도구에서 사용할 연결 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="2cf0e-135">클래스 라이브러리가 호출 애플리케이션이 사용하는 것과 동일한 연결 문자열을 사용하도록 하는 전략을 구현하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="2cf0e-136">예를 들어, 두 프로젝트의 환경 변수에서 값을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="2cf0e-137">ASP.NET Core 프로젝트에서 종속성 주입 설정</span><span class="sxs-lookup"><span data-stu-id="2cf0e-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="2cf0e-138">Core 프로젝트의 *Startup.cs* 파일에서 `ConfigureServices`에 종속성 주입(DI)에 대한 EF6 컨텍스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="2cf0e-139">EF 컨텍스트 개체는 요청당 수명에 대해 범위가 지정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="2cf0e-140">그러면 DI를 사용하여 컨트롤러에서 컨텍스트의 인스턴스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="2cf0e-141">코드는 EF Core 컨텍스트에 대해 작성한 코드와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="2cf0e-142">애플리케이션 예제</span><span class="sxs-lookup"><span data-stu-id="2cf0e-142">Sample application</span></span>   

<span data-ttu-id="2cf0e-143">작업 중인 애플리케이션 예제는 이 문서와 함께 제공되는 [샘플 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="2cf0e-144">Visual Studio에서 다음 단계에 따라 이 샘플을 처음부터 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="2cf0e-145">솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-145">Create a solution.</span></span>    

* <span data-ttu-id="2cf0e-146">**추가** > **새 프로젝트** > **웹** > **ASP.NET Core 웹 애플리케이션**</span><span class="sxs-lookup"><span data-stu-id="2cf0e-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="2cf0e-147">프로젝트 템플릿 선택 대화 상자의 드롭다운에서 API 및 .NET Framework 선택</span><span class="sxs-lookup"><span data-stu-id="2cf0e-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="2cf0e-148">**추가** > **새 프로젝트** > **Windows 데스크톱** > **클래스 라이브러리(.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="2cf0e-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="2cf0e-149">두 프로젝트에 대한 **PMC(패키지 관리자 콘솔)** 에서 `Install-Package Entityframework` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="2cf0e-150">클래스 라이브러리 프로젝트에서 데이터 모델 클래스 및 컨텍스트 클래스와 `IDbContextFactory`의 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="2cf0e-151">클래스 라이브러리 프로젝트에 대한 PMC에서 `Enable-Migrations` 및 `Add-Migration Initial` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="2cf0e-152">ASP.NET Core 프로젝트를 시작 프로젝트로 설정한 경우 `-StartupProjectName EF6`을 이러한 명령에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="2cf0e-153">Core 프로젝트에서 클래스 라이브러리 프로젝트에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="2cf0e-154">Core 프로젝트의 *Startup.cs* 에서 DI에 대한 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-154">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="2cf0e-155">Core 프로젝트의 *appsettings.json* 에서 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-155">In the Core project, in *appsettings.json*, add the connection string.</span></span>  

* <span data-ttu-id="2cf0e-156">Core 프로젝트에서 데이터를 읽고 쓸 수 있는지 확인하는 컨트롤러 및 뷰를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2cf0e-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="2cf0e-157">(ASP.NET Core MVC 스캐폴딩은 클래스 라이브러리에서 참조된 EF6 컨텍스트와 작동하지 않습니다.)</span><span class="sxs-lookup"><span data-stu-id="2cf0e-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
