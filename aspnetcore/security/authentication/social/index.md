---
title: ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증
author: rick-anderson
description: 이 자습서에는 외부 인증 공급자에 OAuth 2.0을 사용하여 ASP.NET Core 앱을 빌드하는 방법을 보여줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: 1f7c8cd0716f1ada3517add0d37a09e419f38774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053308"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="7926c-103">ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증</span><span class="sxs-lookup"><span data-stu-id="7926c-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="7926c-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7926c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7926c-105">이 자습서에서는 사용자가 외부 인증 공급 기업의 자격 증명으로 OAuth 2.0을 사용하여 로그인할 수 있도록 ASP.NET Core 3.0 앱을 빌드하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="7926c-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) 및 [Microsoft](xref:security/authentication/microsoft-logins) 공급자는 다음 섹션에서 다루고 이 문서에서 만든 시작 프로젝트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="7926c-107">다른 공급자는 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 및 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)와 같은 타사 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="7926c-108">사용자가 기존 자격 증명으로 로그인할 수 있도록 설정:</span><span class="sxs-lookup"><span data-stu-id="7926c-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="7926c-109">사용자에게 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-109">Is convenient for the users.</span></span>
* <span data-ttu-id="7926c-110">로그인 프로세스를 관리하는 많은 복잡한 과정을 타사로 이전합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="7926c-111">소셜 로그인이 트래픽 및 고객 변환을 제공할 수 있는 방법에 대한 예제는 [Facebook](https://www.facebook.com/unsupportedbrowser) 및 [Twitter](https://dev.twitter.com/resources/case-studies)의 사례 연구를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7926c-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="7926c-112">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="7926c-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7926c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7926c-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7926c-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-114">Create a new project.</span></span>
* <span data-ttu-id="7926c-115">**ASP.NET Core 웹 애플리케이션** 및 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-115">Select **ASP.NET Core Web Application** and **Next** .</span></span>
* <span data-ttu-id="7926c-116">**프로젝트 이름** 을 제공하고 **위치** 를 확인하거나 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-116">Provide a **Project name** and confirm or change the **Location** .</span></span> <span data-ttu-id="7926c-117">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-117">Select **Create** .</span></span>
* <span data-ttu-id="7926c-118">드롭다운( **ASP.NET Core {x.y}** )에서 최신 버전의 ASP.NET Core를 선택한 다음 **웹 애플리케이션** 을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="7926c-118">Select the latest version of ASP.NET Core in the drop-down ( **ASP.NET Core {X.Y}** ), and then select **Web Application** .</span></span>
* <span data-ttu-id="7926c-119">**인증** 에서 **변경** 을 선택하고 인증을 **개별 사용자 계정** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-119">Under **Authentication** , select **Change** and set the authentication to **Individual User Accounts** .</span></span> <span data-ttu-id="7926c-120">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-120">Select **OK** .</span></span>
* <span data-ttu-id="7926c-121">**새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-121">In the **Create a new ASP.NET Core Web Application** window, select **Create** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7926c-122">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7926c-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7926c-123">터미널을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-123">Open the terminal.</span></span>  <span data-ttu-id="7926c-124">Visual Studio Code에서 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="7926c-125">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="7926c-126">Windows의 경우 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="7926c-127">macOS 및 Linux의 경우 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="7926c-128">`dotnet new` 명령은 *WebApp1* 폴더에서 새 :::no-loc(Razor)::: Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-128">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="7926c-129">`-au Individual`은 개별 인증을 위한 코드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="7926c-130">`-uld`에서는 Windows용 SQL Server Express의 경량 버전인 LocalDB를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="7926c-131">SQLite를 사용하려면 `-uld`를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="7926c-132">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *WebApp1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="7926c-133">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7926c-133">Apply migrations</span></span>

* <span data-ttu-id="7926c-134">앱을 실행하고 **레지스터** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="7926c-135">새 계정의 메일과 암호를 입력한 다음 **등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-135">Enter the email and password for the new account, and then select **Register** .</span></span>
* <span data-ttu-id="7926c-136">지침에 따라 마이그레이션을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="7926c-137">SecretManager를 사용하여 로그인 공급자에 의해 할당된 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="7926c-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="7926c-138">소셜 로그인 공급자는 등록 프로세스 중에 **애플리케이션 ID** 및 **애플리케이션 암호** 토큰을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="7926c-139">정확한 토큰 이름은 공급자에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-139">The exact token names vary by provider.</span></span> <span data-ttu-id="7926c-140">이러한 토큰은 앱이 API에 액세스하는 데 사용하는 자격 증명을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="7926c-141">토큰은 [Secret Manager](xref:security/app-secrets#secret-manager)의 도움으로 앱 구성에 연결할 수 있는 "암호"를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="7926c-142">Secret Manager는 *:::no-loc(appsettings.json):::* 같은 구성 파일에 토큰을 저장하는 보다 안전한 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *:::no-loc(appsettings.json):::* .</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7926c-143">Secret Manager는 개발 목적으로만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="7926c-144">[Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="7926c-145">[ASP.NET Core에서 개발 중인 앱 암호의 안전한 스토리지](xref:security/app-secrets) 항목의 단계에 따라 아래의 각 로그인 공급자에 의해 할당된 토큰을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="7926c-146">애플리케이션에 필요한 로그인 공급자 설정</span><span class="sxs-lookup"><span data-stu-id="7926c-146">Setup login providers required by your application</span></span>

<span data-ttu-id="7926c-147">다음 항목을 사용하여 해당 공급자를 사용하도록 애플리케이션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="7926c-148">[Facebook](xref:security/authentication/facebook-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="7926c-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="7926c-149">[Twitter](xref:security/authentication/twitter-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="7926c-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="7926c-150">[Google](xref:security/authentication/google-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="7926c-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="7926c-151">[Microsoft](xref:security/authentication/microsoft-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="7926c-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="7926c-152">[다른 공급자](xref:security/authentication/otherlogins) 지침</span><span class="sxs-lookup"><span data-stu-id="7926c-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="7926c-153">필요에 따라 암호 설정</span><span class="sxs-lookup"><span data-stu-id="7926c-153">Optionally set password</span></span>

<span data-ttu-id="7926c-154">외부 로그인 공급자를 등록하는 경우 앱에 암호를 등록하지 않은 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="7926c-155">그러면 사이트에 암호를 만들고 기억하지 않아도 되지만 외부 로그인 공급자에 따라 다르게 적용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="7926c-156">외부 로그인 공급자를 사용할 수 없는 경우 웹 사이트에 로그인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="7926c-157">외부 공급자로 로그인하는 프로세스 중에 설정한 전자 메일을 사용하여 암호를 만들고 로그인하려면:</span><span class="sxs-lookup"><span data-stu-id="7926c-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="7926c-158">오른쪽 위 모서리에 있는 **Hello &lt;이메일 별칭&gt;** 링크를 선택하여 **관리** 보기로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![웹 애플리케이션 관리 뷰](index/_static/pass1a.png)

* <span data-ttu-id="7926c-160">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-160">Select **Create**</span></span>

![암호 페이지 설정](index/_static/pass2a.png)

* <span data-ttu-id="7926c-162">올바른 암호를 설정하고 사용자의 전자 메일을 사용하여 로그인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7926c-163">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7926c-163">Next steps</span></span>

* <span data-ttu-id="7926c-164">로그인 단추를 사용자 지정하는 방법에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/10563)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7926c-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="7926c-165">이 문서는 외부 인증을 고새하고 ASP.NET Core 앱에 외부 로그인을 추가하는 데 필요한 필수 구성 요소를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="7926c-166">앱에 필요한 공급자에 대한 로그인을 구성하기 위해 공급자 관련 페이지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="7926c-167">사용자와 해당 액세스 및 새로 고침 토큰에 대한 추가 데이터를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7926c-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="7926c-168">자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7926c-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
