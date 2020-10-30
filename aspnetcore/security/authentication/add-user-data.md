---
title: 'ASP.NET Core 프로젝트에서 사용자 데이터 추가, 다운로드 및 삭제 :::no-loc(Identity):::'
author: rick-anderson
description: 'ASP.NET Core 프로젝트에서에 사용자 지정 사용자 데이터를 추가 하는 방법에 대해 알아봅니다 :::no-loc(Identity)::: . GDPR 당 데이터를 삭제 합니다.'
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061420"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="e223b-104">ASP.NET Core 프로젝트에서 사용자 지정 사용자 데이터 추가, 다운로드 및 삭제 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="e223b-104">Add, download, and delete custom user data to :::no-loc(Identity)::: in an ASP.NET Core project</span></span>

<span data-ttu-id="e223b-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e223b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e223b-106">이 문서는 다음 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-106">This article shows how to:</span></span>

* <span data-ttu-id="e223b-107">ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="e223b-108">사용자 지정 사용자 데이터 모델을 특성으로 표시 하 여 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> 자동으로 다운로드 및 삭제에 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="e223b-109">데이터를 다운로드 하 고 삭제할 수 있도록 하는 것은 [Gdpr](xref:security/gdpr) 요구 사항을 충족 하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="e223b-110">프로젝트 샘플은 :::no-loc(Razor)::: 페이지 웹 앱에서 생성 되지만 지침은 ASP.NET CORE MVC 웹 앱과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-110">The project sample is created from a :::no-loc(Razor)::: Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="e223b-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e223b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e223b-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e223b-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="e223b-113">:::no-loc(Razor):::웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e223b-113">Create a :::no-loc(Razor)::: web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e223b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e223b-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e223b-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-115">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="e223b-116">[다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e223b-117">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e223b-118">드롭다운에서 **ASP.NET Core 3.0** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="e223b-119">**웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e223b-120">프로젝트를 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="e223b-121">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-121">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="e223b-122">[다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e223b-123">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e223b-124">드롭다운에서 **ASP.NET Core 2.2** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="e223b-125">**웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e223b-126">프로젝트를 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="e223b-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e223b-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="e223b-128">스 캐 폴더 실행 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="e223b-128">Run the :::no-loc(Identity)::: scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e223b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e223b-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e223b-130">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **Add**  >  **새 스 캐 폴드 항목** 추가를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-130">From **Solution Explorer** , right-click on the project > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="e223b-131">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 추가를 선택 **:::no-loc(Identity):::**  >  **Add** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-131">From the left pane of the **Add Scaffold** dialog, select **:::no-loc(Identity):::** > **Add** .</span></span>
* <span data-ttu-id="e223b-132">**추가 :::no-loc(Identity):::** 대화 상자에서 다음 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-132">In the **Add :::no-loc(Identity):::** dialog, the following options:</span></span>
  * <span data-ttu-id="e223b-133">기존 레이아웃 파일  *~/Pages/Shared/_Layout를 선택 합니다.*</span><span class="sxs-lookup"><span data-stu-id="e223b-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="e223b-134">재정의할 다음 파일 선택:</span><span class="sxs-lookup"><span data-stu-id="e223b-134">Select the following files to override:</span></span>
    * <span data-ttu-id="e223b-135">**계정/등록**</span><span class="sxs-lookup"><span data-stu-id="e223b-135">**Account/Register**</span></span>
    * <span data-ttu-id="e223b-136">**계정/관리/인덱스**</span><span class="sxs-lookup"><span data-stu-id="e223b-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="e223b-137">단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스** 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-137">Select the **+** button to create a new **Data context class** .</span></span> <span data-ttu-id="e223b-138">프로젝트 이름이 **WebApp1** 인 경우 형식 ( **WebApp1. WebApp1Context** )을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-138">Accept the type ( **WebApp1.Models.WebApp1Context** if the project is named **WebApp1** ).</span></span>
  * <span data-ttu-id="e223b-139">단추를 선택 **+** 하 여 새 **사용자 클래스** 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-139">Select the **+** button to create a new **User class** .</span></span> <span data-ttu-id="e223b-140">형식 (프로젝트 이름이 **WebApp1** 인 경우 **WebApp1User** )을 적용 > **추가** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-140">Accept the type ( **WebApp1User** if the project is named **WebApp1** ) > **Add** .</span></span>
* <span data-ttu-id="e223b-141">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-141">Select **Add** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e223b-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e223b-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e223b-143">이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e223b-144">프로젝트 (.csproj) 파일에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="e223b-145">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e223b-146">다음 명령을 실행 하 여 스 캐 폴더 옵션을 나열 합니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="e223b-146">Run the following command to list the :::no-loc(Identity)::: scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e223b-147">프로젝트 폴더에서 스 캐 폴더를 실행 합니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="e223b-147">In the project folder, run the :::no-loc(Identity)::: scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="e223b-148">[마이그레이션, UseAuthentication 및 레이아웃](xref:security/authentication/scaffold-identity#efm) 의 지침에 따라 다음 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="e223b-149">마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="e223b-150">`UseAuthentication`을 `Startup.Configure`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="e223b-151">`<partial name="_LoginPartial" />`레이아웃 파일에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="e223b-152">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-152">Test the app:</span></span>
  * <span data-ttu-id="e223b-153">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="e223b-153">Register a user</span></span>
  * <span data-ttu-id="e223b-154">새 사용자 이름 ( **로그 아웃** 링크 옆에 있는)을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="e223b-155">창을 확장 하거나 탐색 모음 아이콘을 선택 하 여 사용자 이름 및 기타 링크를 표시 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="e223b-156">**개인 데이터** 탭을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="e223b-157">**다운로드** 단추를 선택 하 고 파일 *의PersonalData.js* 를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="e223b-158">로그온 한 사용자를 삭제 하는 **삭제** 단추를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="e223b-159">DB에 사용자 지정 사용자 데이터 추가 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="e223b-159">Add custom user data to the :::no-loc(Identity)::: DB</span></span>

<span data-ttu-id="e223b-160">`:::no-loc(Identity):::User`사용자 지정 속성을 사용 하 여 파생 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-160">Update the `:::no-loc(Identity):::User` derived class with custom properties.</span></span> <span data-ttu-id="e223b-161">WebApp1 프로젝트의 이름을 지정 하는 경우 파일의 이름은 *Areas/ :::no-loc(Identity)::: /Data/WebApp1User.cs* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-161">If you named the project WebApp1, the file is named *Areas/:::no-loc(Identity):::/Data/WebApp1User.cs* .</span></span> <span data-ttu-id="e223b-162">다음 코드를 사용 하 여 파일을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="e223b-163">[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성이 있는 속성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="e223b-164">*Areas/ :::no-loc(Identity)::: /Pages/Account/Manage/DeletePersonalData.cshtml* :::no-loc(Razor)::: 페이지가 호출 하면 삭제 `UserManager.Delete` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-164">Deleted when the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DeletePersonalData.cshtml* :::no-loc(Razor)::: Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="e223b-165">*영역/ :::no-loc(Identity)::: /Pages/Account/Manage/DownloadPersonalData.cshtml* 페이지에서 다운로드 한 데이터에 포함 :::no-loc(Razor)::: 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-165">Included in the downloaded data by the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DownloadPersonalData.cshtml* :::no-loc(Razor)::: Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="e223b-166">계정/관리/인덱스를 업데이트 합니다. cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="e223b-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="e223b-167">`InputModel`다음 강조 표시 된 코드를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Manage/Index.cshtml.cs* 의를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-167">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="e223b-168">다음 강조 표시 된 태그를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Manage/Index.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-168">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="e223b-169">다음 강조 표시 된 태그를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Manage/Index.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-169">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="e223b-170">계정/레지스터를 업데이트 합니다. cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="e223b-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="e223b-171">`InputModel`다음 강조 표시 된 코드를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* 의를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-171">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="e223b-172">다음 강조 표시 된 태그를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-172">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="e223b-173">다음 강조 표시 된 태그를 사용 하 여 *영역/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-173">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="e223b-174">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="e223b-175">사용자 지정 사용자 데이터에 대 한 마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="e223b-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e223b-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e223b-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e223b-177">Visual Studio **패키지 관리자 콘솔** 에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-177">In the Visual Studio **Package Manager Console** :</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e223b-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e223b-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="e223b-179">사용자 지정 사용자 데이터 만들기, 보기, 다운로드, 삭제 테스트</span><span class="sxs-lookup"><span data-stu-id="e223b-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="e223b-180">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-180">Test the app:</span></span>

* <span data-ttu-id="e223b-181">새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-181">Register a new user.</span></span>
* <span data-ttu-id="e223b-182">페이지에서 사용자 지정 사용자 데이터를 봅니다 `/:::no-loc(Identity):::/Account/Manage` .</span><span class="sxs-lookup"><span data-stu-id="e223b-182">View the custom user data on the `/:::no-loc(Identity):::/Account/Manage` page.</span></span>
* <span data-ttu-id="e223b-183">페이지에서 사용자 개인 데이터를 다운로드 하 여 봅니다 `/:::no-loc(Identity):::/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="e223b-183">Download and view the users personal data from the `/:::no-loc(Identity):::/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="e223b-184">IUserClaimsPrincipalFactory를 사용 하 여 클레임 추가 :::no-loc(Identity):::<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="e223b-184">Add claims to :::no-loc(Identity)::: using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="e223b-185">이 섹션은 이전 자습서의 확장이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="e223b-186">자습서를 사용 하 여 빌드한 앱에 다음 단계를 적용 하려면 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/18797)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e223b-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="e223b-187">인터페이스를 사용 하 여 추가 클레임을에 추가할 수 있습니다 :::no-loc(ASP.NET Core Identity)::: `IUserClaimsPrincipalFactory<T>` .</span><span class="sxs-lookup"><span data-stu-id="e223b-187">Additional claims can be added to :::no-loc(ASP.NET Core Identity)::: by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="e223b-188">이 클래스는 메서드의 응용 프로그램에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e223b-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e223b-189">다음과 같이 클래스의 사용자 지정 구현을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="e223b-190">데모 코드는 클래스를 사용 합니다 `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="e223b-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="e223b-191">이 클래스는 `IsAdmin` 추가 클레임을 추가 하는 데 사용 되는 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="e223b-192">`AdditionalUserClaimsPrincipalFactory`는 `UserClaimsPrincipalFactory` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="e223b-193">새 역할 클레임이에 추가 됩니다 `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="e223b-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, :::no-loc(Identity):::Role>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<:::no-loc(Identity):::Role> roleManager, 
        IOptions<:::no-loc(Identity):::Options> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (Claims:::no-loc(Identity):::)principal.:::no-loc(Identity):::;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="e223b-194">그러면 앱에서 추가 클레임을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="e223b-195">페이지에서 :::no-loc(Razor)::: `IAuthorizationService` 인스턴스를 사용 하 여 클레임 값에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e223b-195">In a :::no-loc(Razor)::: Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
