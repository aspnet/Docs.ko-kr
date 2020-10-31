---
title: :::no-loc(Identity):::ASP.NET Core 프로젝트의 스 캐 폴드
author: rick-anderson
description: 'ASP.NET Core 프로젝트에서 스 캐 폴드 하는 방법에 대해 알아봅니다 :::no-loc(Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141497"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="0b1b6-103">:::no-loc(Identity):::ASP.NET Core 프로젝트의 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="0b1b6-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0b1b6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b1b6-105">ASP.NET Core은 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: 클래스 라이브러리로](xref:razor-pages/ui-class)제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0b1b6-106">를 포함 하는 응용 프로그램 :::no-loc(Identity)::: 은 스 캐 폴더를 적용 하 여 :::no-loc(Identity)::: :::no-loc(Razor)::: 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="0b1b6-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="0b1b6-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="0b1b6-109">생성된 코드는 :::no-loc(Identity)::: RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="0b1b6-110">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 :::no-loc(Identity)::: Ui 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="0b1b6-111">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="0b1b6-112">선택한 :::no-loc(Identity)::: 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="0b1b6-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="0b1b6-114">이 문서에서는 :::no-loc(Identity)::: 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="0b1b6-115">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="0b1b6-116">스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="0b1b6-117">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 :::no-loc(Identity)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-118">스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-119">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="0b1b6-120">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="0b1b6-121">:::no-loc(Identity):::기존 개별 계정이 있는 프로젝트에 새 데이터 컨텍스트를 사용 하 여 스 캐 폴딩 할 때:</span><span class="sxs-lookup"><span data-stu-id="0b1b6-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="0b1b6-122">에서 `Startup.ConfigureServices` 에 대 한 호출을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="0b1b6-123">예를 들어 `AddDbContext` 및 `AddDefault:::no-loc(Identity):::` 는 다음 코드에서 주석 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="0b1b6-124">앞 코드는 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 에서 중복 된 코드를 주석으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="0b1b6-125">일반적으로 개별 계정을 사용 하 여 만든 앱은 새 데이터 컨텍스트를 **만들지 않아야 합니다** .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="0b1b6-126">:::no-loc(Identity):::빈 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-127">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="0b1b6-128">:::no-loc(Identity)::: :::no-loc(Razor)::: 기존 권한 부여 없이 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-129">:::no-loc(Identity):::는 _Areas/HostingStartup.cs \*에서 구성 됩니다 :::no-loc(Identity)::: / :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="0b1b6-130">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="0b1b6-131">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="0b1b6-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="0b1b6-132">인증 사용</span><span class="sxs-lookup"><span data-stu-id="0b1b6-132">Enable authentication</span></span>

<span data-ttu-id="0b1b6-133">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="0b1b6-134">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="0b1b6-134">Layout changes</span></span>

<span data-ttu-id="0b1b6-135">선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="0b1b6-136">:::no-loc(Identity)::: :::no-loc(Razor)::: 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="0b1b6-137">일부 :::no-loc(Identity)::: 옵션은 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-138">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="0b1b6-139">:::no-loc(Identity):::기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-140">선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="0b1b6-141">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="0b1b6-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="0b1b6-142">:::no-loc(Identity):::는 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-143">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="0b1b6-144">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="0b1b6-145">:::no-loc(Identity):::권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="0b1b6-146">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 기존 권한 부여 없이 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-147">:::no-loc(Identity):::는 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-148">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="0b1b6-149">마이그레이션</span><span class="sxs-lookup"><span data-stu-id="0b1b6-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="0b1b6-150">앱에 XSRF 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="0b1b6-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="0b1b6-151">토큰은 구성 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="0b1b6-152">인증 토큰이 프로 비전 되 고 인증에 저장 될 때 :::no-loc(cookie)::: 구성 요소에 전달 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="0b1b6-153">:::no-loc(Razor)::: 구성 요소 `HttpContext` 는 직접 사용할 수 없으므로의 로그 아웃 끝점에 게시 하기 위해 [XSRF (요청 방지 위조) 토큰](xref:security/anti-request-forgery) 을 얻을 수 있는 방법이 없습니다 :::no-loc(Identity)::: `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="0b1b6-154">XSRF 토큰을 구성 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="0b1b6-155">자세한 내용은 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="0b1b6-156">*Pages/_Host* 파일에서 및 클래스에 토큰을 추가한 후에 토큰을 설정 합니다 `InitialApplicationState` `TokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="0b1b6-157">`App`를 할당 하기 위해 구성 요소 ( *응용 프로그램 razor* )를 업데이트 합니다 `InitialState.XsrfToken` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="0b1b6-158">항목에서 설명 하는 `TokenProvider` 서비스는 `LoginDisplay` 다음 [레이아웃 및 인증 흐름 변경](#layout-and-authentication-flow-changes) 섹션의 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="0b1b6-159">인증 사용</span><span class="sxs-lookup"><span data-stu-id="0b1b6-159">Enable authentication</span></span>

<span data-ttu-id="0b1b6-160">클래스에서 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="0b1b6-160">In the `Startup` class:</span></span>

* <span data-ttu-id="0b1b6-161">:::no-loc(Razor):::에서 Pages services가 추가 되었는지 확인 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="0b1b6-162">[Tokenprovider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)를 사용 하는 경우 서비스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="0b1b6-163">`UseDatabaseErrorPage` `Startup.Configure` 개발 환경에 대 한의 응용 프로그램 작성기에서를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="0b1b6-164">`UseAuthentication`및 이후를 호출 `UseAuthorization` `UseRouting` 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="0b1b6-165">페이지에 대 한 끝점을 추가 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="0b1b6-166">레이아웃 및 인증 흐름 변경</span><span class="sxs-lookup"><span data-stu-id="0b1b6-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="0b1b6-167">`RedirectToLogin`프로젝트 루트의 앱 *공유* 폴더에 구성 요소 ( *redirecttologin. razor* )를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`앱의 *공유* 폴더에 구성 요소 ( *LoginDisplay* )를 추가 합니다. <span data-ttu-id="0b1b6-169">[Tokenprovider 서비스](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) 는 :::no-loc(Identity)::: 의 로그 아웃 끝점에 게시 하는 HTML 폼에 XSRF 토큰을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="0b1b6-170">`MainLayout`구성 요소 ( *Shared/mainlayout. razor* )에서 `LoginDisplay` 구성 요소를 상위 행 요소 내용에 추가 합니다 `<div>` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="0b1b6-171">스타일 인증 끝점</span><span class="sxs-lookup"><span data-stu-id="0b1b6-171">Style authentication endpoints</span></span>

<span data-ttu-id="0b1b6-172">:::no-loc(Blazor Server):::에서는 페이지 :::no-loc(Razor)::: 페이지를 사용 하므로 :::no-loc(Identity)::: 방문자가 :::no-loc(Identity)::: 페이지와 구성 요소 간을 탐색할 때 UI의 스타일이 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="0b1b6-173">Incongruous 스타일을 해결 하는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="0b1b6-174">빌드 :::no-loc(Identity)::: 구성 요소</span><span class="sxs-lookup"><span data-stu-id="0b1b6-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="0b1b6-175">페이지 대신 구성 요소를 사용 하는 방법은 :::no-loc(Identity)::: 구성 요소를 빌드하는 것입니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="0b1b6-176">`SignInManager`및 `UserManager` 는 구성 요소에서 지원 되지 않으므로 :::no-loc(Razor)::: 앱에서 API 끝점을 사용 :::no-loc(Blazor Server)::: 하 여 사용자 계정 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="0b1b6-177">앱 스타일을 사용 하 여 사용자 지정 레이아웃 사용 :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="0b1b6-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="0b1b6-178">:::no-loc(Identity):::페이지 레이아웃 및 스타일을 수정 하 여 기본 테마를 사용 하는 페이지를 만들 수 있습니다 :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1b6-179">이 섹션의 예제는 단순히 사용자 지정을 위한 출발점입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="0b1b6-180">최상의 사용자 환경을 위해 추가 작업이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="0b1b6-181">새 `NavMenu_:::no-loc(Identity):::Layout` 구성 요소 ( *공유/NavMenu_ :::no-loc(Identity)::: 레이아웃. razor* )를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="0b1b6-182">구성 요소의 태그와 코드에 대해 앱 `NavMenu` 구성 요소의 동일한 콘텐츠 ( *Shared/NavMenu* )를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="0b1b6-183">`NavLink` `RedirectToLogin` 구성 요소의 자동 리디렉션이 인증 또는 권한 부여가 필요한 구성 요소에 대해 실패 하므로 익명으로 연결할 수 없는 구성 요소에 대 한 모든를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="0b1b6-184">*Pages/Shared/Layout. cshtml* 파일에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="0b1b6-185">:::no-loc(Razor):::태그 도우미와 앱의 구성 요소를 *공유* 폴더에 사용 하려면 파일의 맨 위에 지시문을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="0b1b6-186">를 `{APPLICATION ASSEMBLY}` 앱의 어셈블리 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="0b1b6-187">`<base>`콘텐츠에 태그 및 :::no-loc(Blazor)::: 스타일 시트를 추가 `<link>` 합니다 `<head>` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="0b1b6-188">태그의 내용을 `<body>` 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="0b1b6-189">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="0b1b6-190">일부 :::no-loc(Identity)::: 옵션은 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-191">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="0b1b6-192">독립 실행형 또는 호스트 된 :::no-loc(Blazor WebAssembly)::: 앱</span><span class="sxs-lookup"><span data-stu-id="0b1b6-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="0b1b6-193">클라이언트 쪽 :::no-loc(Blazor WebAssembly)::: 앱은 자체 :::no-loc(Identity)::: UI 방법을 사용 하 고 :::no-loc(ASP.NET Core Identity)::: 스 캐 폴딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="0b1b6-194">호스팅된 솔루션의 서버 쪽 ASP.NET Core 앱은 :::no-loc(Blazor)::: :::no-loc(Razor)::: 이 문서의 PAGES/MVC 지침을 따를 수 있으며,에서 지 원하는 다른 유형의 ASP.NET Core 앱과 마찬가지로 구성 됩니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="0b1b6-195">:::no-loc(Blazor):::프레임 워크는 :::no-loc(Razor)::: UI 페이지의 구성 요소 버전을 포함 하지 않습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="0b1b6-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: 구성 요소는 사용자가 작성 하거나 지원 되지 않는 타사 원본에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="0b1b6-197">자세한 내용은 [ :::no-loc(Blazor)::: 보안 및 :::no-loc(Identity)::: 문서](xref:blazor/security/index)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="0b1b6-198">전체 :::no-loc(Identity)::: UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="0b1b6-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="0b1b6-199">UI에 대 한 모든 권한을 유지 하려면 :::no-loc(Identity)::: 스 캐 폴더을 실행 하 :::no-loc(Identity)::: 고 **모든 파일 재정의** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="0b1b6-200">다음 강조 표시 된 코드는 :::no-loc(Identity)::: :::no-loc(Identity)::: ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="0b1b6-201">이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="0b1b6-202">기본값은 :::no-loc(Identity)::: 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="0b1b6-203">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="0b1b6-204">구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="0b1b6-205">암호 구성</span><span class="sxs-lookup"><span data-stu-id="0b1b6-205">Password configuration</span></span>

<span data-ttu-id="0b1b6-206"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions>이에서 구성 된 경우 `Startup.ConfigureServices` 스 캐 폴드 pages의 속성에 대 한 [ `[StringLength]` 특성](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) 구성이 필요할 수 있습니다 `Password` :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="0b1b6-207">`InputModel``Password`속성은 다음 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="0b1b6-208">페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="0b1b6-208">Disable a page</span></span>

<span data-ttu-id="0b1b6-209">이 섹션에서는 등록 페이지를 사용 하지 않도록 설정 하는 방법을 보여 주지만,이 방법을 사용 하 여 페이지를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="0b1b6-210">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="0b1b6-210">To disable user registration:</span></span>

* <span data-ttu-id="0b1b6-211">스 캐 폴드 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-212">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="0b1b6-213">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="0b1b6-214">사용자가이 끝점에서 등록할 수 없도록 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="0b1b6-215">이전 변경 내용과 일치 하도록 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="0b1b6-216">*영역/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="0b1b6-217">*영역/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="0b1b6-218">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="0b1b6-219">에서 확인 코드를 제거 합니다 `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-219">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="0b1b6-220">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="0b1b6-220">Use another app to add users</span></span>

<span data-ttu-id="0b1b6-221">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="0b1b6-222">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-222">Options to add users include:</span></span>

* <span data-ttu-id="0b1b6-223">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="0b1b6-224">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-224">A console app.</span></span>

<span data-ttu-id="0b1b6-225">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="0b1b6-226">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="0b1b6-227">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="0b1b6-228">사용자가 데이터베이스에 추가 됩니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="0b1b6-229">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="0b1b6-230">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="0b1b6-231">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="0b1b6-232">정적 자산 게시 방지 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="0b1b6-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="0b1b6-233">정적 :::no-loc(Identity)::: 자산을 웹 루트에 게시 하지 않으려면를 참조 하십시오 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b1b6-234">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="0b1b6-234">Additional resources</span></span>

* [<span data-ttu-id="0b1b6-235">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="0b1b6-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b1b6-236">ASP.NET Core 2.1 이상에서는 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: 클래스 라이브러리로](xref:razor-pages/ui-class)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0b1b6-237">를 포함 하는 응용 프로그램 :::no-loc(Identity)::: 은 스 캐 폴더를 적용 하 여 :::no-loc(Identity)::: :::no-loc(Razor)::: 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="0b1b6-238">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="0b1b6-239">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="0b1b6-240">생성된 코드는 :::no-loc(Identity)::: RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="0b1b6-241">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="0b1b6-242">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="0b1b6-243">선택한 :::no-loc(Identity)::: 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="0b1b6-244">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="0b1b6-245">이 문서에서는 :::no-loc(Identity)::: 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="0b1b6-246">스 캐 폴더를 :::no-loc(Identity)::: 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme.txt* 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="0b1b6-247">*ScaffoldingReadme.txt* 파일은 :::no-loc(Identity)::: 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="0b1b6-248">이 문서에는 *ScaffoldingReadme.txt* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="0b1b6-249">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="0b1b6-250">스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1b6-251">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 :::no-loc(Identity)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-252">스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-253">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="0b1b6-254">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="0b1b6-255">:::no-loc(Identity):::빈 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-256">클래스에 다음의 강조 표시 된 호출을 추가 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="0b1b6-257">:::no-loc(Identity)::: :::no-loc(Razor)::: 기존 권한 부여 없이 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-258">:::no-loc(Identity):::는 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-259">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="0b1b6-260">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="0b1b6-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="0b1b6-261">인증 사용</span><span class="sxs-lookup"><span data-stu-id="0b1b6-261">Enable authentication</span></span>

<span data-ttu-id="0b1b6-262">`Configure`클래스의 메서드에서 `Startup` 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="0b1b6-263">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="0b1b6-263">Layout changes</span></span>

<span data-ttu-id="0b1b6-264">선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="0b1b6-265">:::no-loc(Identity)::: :::no-loc(Razor)::: 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="0b1b6-266">일부 :::no-loc(Identity)::: 옵션은 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-267">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="0b1b6-268">:::no-loc(Identity):::기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0b1b6-269">선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="0b1b6-270">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="0b1b6-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="0b1b6-271">:::no-loc(Identity):::는 *영역/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="0b1b6-272">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="0b1b6-273">다음 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 이후에 호출 `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="0b1b6-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="0b1b6-274">:::no-loc(Identity):::권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="0b1b6-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="0b1b6-275">*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="0b1b6-276">전체 :::no-loc(Identity)::: UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="0b1b6-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="0b1b6-277">UI에 대 한 모든 권한을 유지 하려면 :::no-loc(Identity)::: 스 캐 폴더을 실행 하 :::no-loc(Identity)::: 고 **모든 파일 재정의** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="0b1b6-278">다음 강조 표시 된 코드는 :::no-loc(Identity)::: :::no-loc(Identity)::: ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="0b1b6-279">이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="0b1b6-280">기본값은 :::no-loc(Identity)::: 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="0b1b6-281">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="0b1b6-282">구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="0b1b6-283">암호 구성</span><span class="sxs-lookup"><span data-stu-id="0b1b6-283">Password configuration</span></span>

<span data-ttu-id="0b1b6-284"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions>이에서 구성 된 경우 `Startup.ConfigureServices` 스 캐 폴드 pages의 속성에 대 한 [ `[StringLength]` 특성](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) 구성이 필요할 수 있습니다 `Password` :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="0b1b6-285">`InputModel``Password`속성은 다음 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="0b1b6-286">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="0b1b6-286">Disable register page</span></span>

<span data-ttu-id="0b1b6-287">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="0b1b6-287">To disable user registration:</span></span>

* <span data-ttu-id="0b1b6-288">스 캐 폴드 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="0b1b6-289">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="0b1b6-290">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="0b1b6-291">사용자가이 끝점에서 등록할 수 없도록 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="0b1b6-292">이전 변경 내용과 일치 하도록 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="0b1b6-293">*영역/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="0b1b6-294">*영역/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="0b1b6-295">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="0b1b6-296">에서 확인 코드를 제거 합니다 `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-296">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="0b1b6-297">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="0b1b6-297">Use another app to add users</span></span>

<span data-ttu-id="0b1b6-298">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="0b1b6-299">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-299">Options to add users include:</span></span>

* <span data-ttu-id="0b1b6-300">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="0b1b6-301">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-301">A console app.</span></span>

<span data-ttu-id="0b1b6-302">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="0b1b6-303">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="0b1b6-304">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="0b1b6-305">사용자가 데이터베이스에 추가 됩니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="0b1b6-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="0b1b6-306">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="0b1b6-307">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="0b1b6-308">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b1b6-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b1b6-309">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="0b1b6-309">Additional resources</span></span>

* [<span data-ttu-id="0b1b6-310">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="0b1b6-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
