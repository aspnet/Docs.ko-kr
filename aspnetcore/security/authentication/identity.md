---
title: ASP.NET Core 소개 Identity
author: rick-anderson
description: IdentityASP.NET Core 앱과 함께 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 7141808b3190db2c882d562d753cd68fe3f985ff
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589740"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="7801e-104">ASP.NET Core 소개 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7801e-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7801e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7801e-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="7801e-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="7801e-107">는 UI (사용자 인터페이스) 로그인 기능을 지 원하는 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="7801e-108">사용자, 암호, 프로필 데이터, 역할, 클레임, 토큰, 전자 메일 확인 등을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="7801e-109">사용자는에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 Identity 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="7801e-110">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="7801e-111">[ Identity 소스 코드](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) 는 GitHub에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="7801e-112">[스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity) 및는 생성 된 파일을 확인 하 여와의 템플릿 상호 작용을 검토 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="7801e-113">Identity 는 일반적으로 사용자 이름, 암호 및 프로필 데이터를 저장 하기 위해 SQL Server 데이터베이스를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="7801e-114">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="7801e-115">이 항목에서는를 사용 하 여 사용자를 Identity 등록 하 고 로그인 하 고 로그 아웃 하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="7801e-116">참고: 템플릿은 사용자에 대해 사용자 이름 및 전자 메일을 동일 하 게 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="7801e-117">를 사용 하는 앱을 만드는 방법에 대 한 자세한 지침은 Identity [다음 단계](#next)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="7801e-118">[Microsoft id 플랫폼](/azure/active-directory/develop/) 은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="7801e-119">Azure Active Directory (Azure AD) 개발자 플랫폼의 진화</span><span class="sxs-lookup"><span data-stu-id="7801e-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="7801e-120">와 관련이 ASP.NET Core Identity 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="7801e-121">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7801e-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="7801e-122">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7801e-122">Create a Web app with authentication</span></span>

<span data-ttu-id="7801e-123">개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7801e-125">**파일** > **새로 만들기** > **프로젝트** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="7801e-126">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7801e-127">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="7801e-128">**확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-128">Click **OK**.</span></span>
* <span data-ttu-id="7801e-129">ASP.NET Core **웹 응용 프로그램** 을 선택한 다음 **인증 변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="7801e-130">**개별 사용자 계정을** 선택 하 고 **확인** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="7801e-132">이전 명령은 Razor SQLite를 사용 하 여 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="7801e-133">LocalDB를 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="7801e-134">생성 된 프로젝트는을 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7801e-135">Identity Razor 클래스 라이브러리는 영역을 사용 하 여 끝점을 노출 `Identity` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="7801e-136">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-136">For example:</span></span>

* <span data-ttu-id="7801e-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="7801e-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="7801e-138">/Identity/계정/로그 아웃</span><span class="sxs-lookup"><span data-stu-id="7801e-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="7801e-139">/Identity/계정/관리</span><span class="sxs-lookup"><span data-stu-id="7801e-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7801e-140">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7801e-140">Apply migrations</span></span>

<span data-ttu-id="7801e-141">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7801e-143">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7801e-145">SQLite를 사용 하는 경우이 단계에서는 마이그레이션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="7801e-146">LocalDB의 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="7801e-147">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="7801e-147">Test Register and Login</span></span>

<span data-ttu-id="7801e-148">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-148">Run the app and register a user.</span></span> <span data-ttu-id="7801e-149">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="7801e-150">Identity서비스 구성</span><span class="sxs-lookup"><span data-stu-id="7801e-150">Configure Identity services</span></span>

<span data-ttu-id="7801e-151">서비스는에 추가 됩니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7801e-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="7801e-152">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="7801e-153">위의 강조 표시 된 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="7801e-154">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7801e-155">Identity 는를 호출 하 여 사용할 수 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="7801e-156">`UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="7801e-157">위의 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="7801e-158">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7801e-159">Identity 는 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="7801e-160">`UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7801e-161">템플릿에서 생성 된 앱은 [권한 부여](xref:security/authorization/secure-data)를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="7801e-162">`app.UseAuthorization` 는 앱이 권한 부여를 추가 하는 올바른 순서로 추가 되었는지 확인 하기 위해 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="7801e-163">`UseRouting`, `UseAuthentication` , `UseAuthorization` 및 `UseEndpoints` 는 앞의 코드에 표시 된 순서 대로 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="7801e-164">및에 대 한 자세한 내용은 `IdentityOptions` `Startup` <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="7801e-165">스 캐 폴드 Register, Login, LogOut 및 RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="7801e-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7801e-167">,, `Register` `Login` 및 파일을 추가 `LogOut` `RegisterConfirmation` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="7801e-168">이 섹션에 표시 된 코드를 생성 하려면 [ Razor 권한 부여 지침을 포함 하는 스 캐 폴드 id를 프로젝트에](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-169">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7801e-170">**WebApp1** 이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="7801e-171">그렇지 않으면에 대해 올바른 네임 스페이스를 사용 합니다 `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="7801e-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="7801e-172">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="7801e-173">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="7801e-174">스 캐 폴딩에 대 한 자세한 내용은 Identity [스 캐 폴드 identity to a Razor project to authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="7801e-175">등록 검사</span><span class="sxs-lookup"><span data-stu-id="7801e-175">Examine Register</span></span>

<span data-ttu-id="7801e-176">사용자가 페이지에서 **등록** 단추를 클릭 하면 `Register` `RegisterModel.OnPostAsync` 동작이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="7801e-177">사용자는 개체에 대해 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 를 사용 하 여 만들어집니다 `_userManager` .</span><span class="sxs-lookup"><span data-stu-id="7801e-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="7801e-178">로그인</span><span class="sxs-lookup"><span data-stu-id="7801e-178">Log in</span></span>

<span data-ttu-id="7801e-179">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="7801e-180">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="7801e-181">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="7801e-182">로그인 페이지의 폼이 제출 되 면 `OnPostAsync` 동작이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="7801e-183">`PasswordSignInAsync` 는 개체에서 호출 됩니다 `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="7801e-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="7801e-184">권한 부여 결정을 내리는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="7801e-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="7801e-185">로그아웃</span><span class="sxs-lookup"><span data-stu-id="7801e-185">Log out</span></span>

<span data-ttu-id="7801e-186">**로그 아웃** 링크는 작업을 호출 합니다 `LogoutModel.OnPost` .</span><span class="sxs-lookup"><span data-stu-id="7801e-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="7801e-187">위의 코드에서는 `return RedirectToPage();` 브라우저가 새 요청을 수행 하 고 사용자에 대 한 id가 업데이트 되도록 코드를 리디렉션 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="7801e-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 에 저장 된 사용자의 클레임을 지웁니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="7801e-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="7801e-189">Post는 *Pages/Shared/_LoginPartial* 에서 지정 됩니다. cshtml:</span><span class="sxs-lookup"><span data-stu-id="7801e-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="7801e-190">테스트할 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-190">Test Identity</span></span>

<span data-ttu-id="7801e-191">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="7801e-192">테스트 하려면 Identity 다음을 추가 합니다 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="7801e-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="7801e-193">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="7801e-194">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="7801e-195">탐험 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-195">Explore Identity</span></span>

<span data-ttu-id="7801e-196">자세히 살펴보기 Identity :</span><span class="sxs-lookup"><span data-stu-id="7801e-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="7801e-197">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="7801e-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="7801e-198">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="7801e-199">Identity 요소도</span><span class="sxs-lookup"><span data-stu-id="7801e-199">Identity Components</span></span>

<span data-ttu-id="7801e-200">모든 Identity 종속 NuGet 패키지는 [ASP.NET Core 공유 프레임 워크](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="7801e-201">의 기본 패키지는 Identity [AspNetCore입니다. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="7801e-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="7801e-202">이 패키지에는에 대 한 핵심 인터페이스 집합이 포함 되어 ASP.NET Core Identity 있으며,에 포함 되어 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="7801e-203">로 마이그레이션 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="7801e-204">기존 저장소 마이그레이션에 대 한 자세한 내용 및 지침은 Identity [인증 및 Identity 마이그레이션 ](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="7801e-205">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="7801e-205">Setting password strength</span></span>

<span data-ttu-id="7801e-206">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="7801e-207">AddDefault Identity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="7801e-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="7801e-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 는 ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="7801e-209">호출은 `AddDefaultIdentity` 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="7801e-210">자세한 내용은 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="7801e-211">정적 자산 게시 방지 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="7801e-212">IdentityUI에 대 한 정적 자산 (스타일 시트 및 JavaScript 파일 Identity )을 웹 루트에 게시 하지 않으려면 다음 `ResolveStaticWebAssetsInputsDependsOn` 속성과 `RemoveIdentityAssets` 대상을 응용 프로그램의 프로젝트 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="7801e-213">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7801e-213">Next Steps</span></span>

* <span data-ttu-id="7801e-214">[ASP.NET Core Identity 소스 코드](https://github.com/dotnet/aspnetcore/tree/main/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="7801e-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/main/src/Identity)</span></span>
* <span data-ttu-id="7801e-215">SQLite를 사용 하 여 구성 하는 방법에 대 한 자세한 내용은 [GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/5131) 를 참조 하세요 Identity .</span><span class="sxs-lookup"><span data-stu-id="7801e-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="7801e-216">Identity 구성</span><span class="sxs-lookup"><span data-stu-id="7801e-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7801e-217">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7801e-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7801e-218">ASP.NET Core Identity 는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="7801e-219">사용자는에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 Identity 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="7801e-220">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7801e-221">Identity SQL Server 데이터베이스를 사용 하 여 사용자 이름, 암호 및 프로필 데이터를 저장 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="7801e-222">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="7801e-223">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7801e-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7801e-224">이 항목에서는를 사용 하 여 사용자를 Identity 등록 하 고 로그인 하 고 로그 아웃 하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="7801e-225">를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은 Identity 이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="7801e-226">AddDefault Identity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="7801e-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="7801e-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 는 ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="7801e-228">호출은 `AddDefaultIdentity` 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="7801e-229">자세한 내용은 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="7801e-230">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7801e-230">Create a Web app with authentication</span></span>

<span data-ttu-id="7801e-231">개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7801e-233">**파일** > **새로 만들기** > **프로젝트** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="7801e-234">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7801e-235">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="7801e-236">**확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-236">Click **OK**.</span></span>
* <span data-ttu-id="7801e-237">ASP.NET Core **웹 응용 프로그램** 을 선택한 다음 **인증 변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="7801e-238">**개별 사용자 계정을** 선택 하 고 **확인** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="7801e-240">생성 된 프로젝트는을 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7801e-241">Identity Razor 클래스 라이브러리는 영역을 사용 하 여 끝점을 노출 `Identity` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="7801e-242">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-242">For example:</span></span>

* <span data-ttu-id="7801e-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="7801e-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="7801e-244">/Identity/계정/로그 아웃</span><span class="sxs-lookup"><span data-stu-id="7801e-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="7801e-245">/Identity/계정/관리</span><span class="sxs-lookup"><span data-stu-id="7801e-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7801e-246">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7801e-246">Apply migrations</span></span>

<span data-ttu-id="7801e-247">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7801e-249">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="7801e-251">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="7801e-251">Test Register and Login</span></span>

<span data-ttu-id="7801e-252">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-252">Run the app and register a user.</span></span> <span data-ttu-id="7801e-253">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="7801e-254">Identity서비스 구성</span><span class="sxs-lookup"><span data-stu-id="7801e-254">Configure Identity services</span></span>

<span data-ttu-id="7801e-255">서비스는에 추가 됩니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7801e-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="7801e-256">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="7801e-257">위의 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="7801e-258">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7801e-259">Identity 는 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="7801e-260">`UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="7801e-261">자세한 내용은 [ Identity 옵션 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="7801e-262">스 캐 폴드 Register, Login 및 LogOut</span><span class="sxs-lookup"><span data-stu-id="7801e-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="7801e-263">이 섹션에 표시 된 코드를 생성 하려면 [ Razor 권한 부여 지침을 포함 하는 스 캐 폴드 id를 프로젝트에](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7801e-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7801e-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7801e-265">Register, Login 및 LogOut 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7801e-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7801e-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7801e-267">**WebApp1** 이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="7801e-268">그렇지 않으면에 대해 올바른 네임 스페이스를 사용 합니다 `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="7801e-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

<span data-ttu-id="7801e-269">SQLite를 사용 하는 경우 `--useSqLite` 다음을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-269">When using SQLite, `--useSqLite` must be specified:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

<span data-ttu-id="7801e-270">SQL Express를 사용 하 여 다음 명령을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-270">With SQL Express, use the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="7801e-271">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-271">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="7801e-272">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-272">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="7801e-273">등록 검사</span><span class="sxs-lookup"><span data-stu-id="7801e-273">Examine Register</span></span>

<span data-ttu-id="7801e-274">사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-274">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="7801e-275">사용자는 개체에 대해 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 를 사용 하 여 만들어집니다 `_userManager` .</span><span class="sxs-lookup"><span data-stu-id="7801e-275">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="7801e-276">사용자가 성공적으로 만들어진 경우에는에 대 한 호출을 통해 사용자가 로그인 됩니다 `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="7801e-276">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="7801e-277">**참고:** 등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-277">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="7801e-278">로그인</span><span class="sxs-lookup"><span data-stu-id="7801e-278">Log in</span></span>

<span data-ttu-id="7801e-279">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-279">The Login form is displayed when:</span></span>

* <span data-ttu-id="7801e-280">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-280">The **Log in** link is selected.</span></span>
* <span data-ttu-id="7801e-281">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-281">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="7801e-282">로그인 페이지의 폼이 제출 되 면 `OnPostAsync` 동작이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-282">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="7801e-283">`PasswordSignInAsync` 는 개체에서 호출 됩니다 `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="7801e-283">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="7801e-284">권한 부여 결정을 내리는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="7801e-284">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="7801e-285">로그아웃</span><span class="sxs-lookup"><span data-stu-id="7801e-285">Log out</span></span>

<span data-ttu-id="7801e-286">**로그 아웃** 링크는 작업을 호출 합니다 `LogoutModel.OnPost` .</span><span class="sxs-lookup"><span data-stu-id="7801e-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="7801e-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 에 저장 된 사용자의 클레임을 지웁니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="7801e-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="7801e-288">Post는 *Pages/Shared/_LoginPartial* 에서 지정 됩니다. cshtml:</span><span class="sxs-lookup"><span data-stu-id="7801e-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="7801e-289">테스트할 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-289">Test Identity</span></span>

<span data-ttu-id="7801e-290">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="7801e-291">테스트 하려면 Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 개인 정보 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="7801e-292">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="7801e-293">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="7801e-294">탐험 Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-294">Explore Identity</span></span>

<span data-ttu-id="7801e-295">자세히 살펴보기 Identity :</span><span class="sxs-lookup"><span data-stu-id="7801e-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="7801e-296">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="7801e-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="7801e-297">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="7801e-298">Identity 요소도</span><span class="sxs-lookup"><span data-stu-id="7801e-298">Identity Components</span></span>

<span data-ttu-id="7801e-299">모든 Identity 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7801e-300">의 기본 패키지는 Identity [AspNetCore입니다. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="7801e-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="7801e-301">이 패키지에는에 대 한 핵심 인터페이스 집합이 포함 되어 ASP.NET Core Identity 있으며,에 포함 되어 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7801e-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="7801e-302">로 마이그레이션 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="7801e-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="7801e-303">기존 저장소 마이그레이션에 대 한 자세한 내용 및 지침은 Identity [인증 및 Identity 마이그레이션 ](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="7801e-304">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="7801e-304">Setting password strength</span></span>

<span data-ttu-id="7801e-305">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7801e-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7801e-306">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7801e-306">Next Steps</span></span>

* <span data-ttu-id="7801e-307">SQLite를 사용 하 여 구성 하는 방법에 대 한 자세한 내용은 [GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/5131) 를 참조 하세요 Identity .</span><span class="sxs-lookup"><span data-stu-id="7801e-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="7801e-308">Identity 구성</span><span class="sxs-lookup"><span data-stu-id="7801e-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
