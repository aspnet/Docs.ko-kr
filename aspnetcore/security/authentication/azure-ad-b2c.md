---
title: ASP.NET Core에서 Azure Active Directory B2C를 사용 하는 클라우드 인증
author: camsoper
description: ASP.NET Core를 사용 하 여 Azure Active Directory B2C 인증을 설정 하는 방법을 알아봅니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061394"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="f4dd2-103">ASP.NET Core에서 Azure Active Directory B2C를 사용 하는 클라우드 인증</span><span class="sxs-lookup"><span data-stu-id="f4dd2-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="f4dd2-104">작성자: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="f4dd2-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="f4dd2-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C)은 웹 및 모바일 앱에 대 한 클라우드 id 관리 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="f4dd2-106">서비스는 클라우드 및 온-프레미스에서 호스트 되는 앱에 대 한 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="f4dd2-107">인증 유형에는 개별 계정, 소셜 네트워크 계정 및 페더레이션된 엔터프라이즈 계정이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="f4dd2-108">또한 Azure AD B2C는 최소 구성으로 다단계 인증을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="f4dd2-109">Azure Active Directory (Azure AD) 및 Azure AD B2C는 별도 제품 제품입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="f4dd2-110">Azure AD 테 넌 트는 조직을 나타내며 Azure AD B2C 테 넌 트는 신뢰 당사자 응용 프로그램에 사용할 id의 컬렉션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="f4dd2-111">자세히 알아보려면 [Azure AD B2C: faq (질문과 대답)](/azure/active-directory-b2c/active-directory-b2c-faqs)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="f4dd2-112">이 자습서에서는 다음 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f4dd2-113">Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="f4dd2-114">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="f4dd2-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="f4dd2-115">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="f4dd2-116">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="f4dd2-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4dd2-117">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f4dd2-117">Prerequisites</span></span>

<span data-ttu-id="f4dd2-118">이 연습을 수행 하려면 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="f4dd2-119">Microsoft Azure 구독</span><span class="sxs-lookup"><span data-stu-id="f4dd2-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="f4dd2-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="f4dd2-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="f4dd2-121">Azure Active Directory B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="f4dd2-122">[설명서에 설명 된 대로](/azure/active-directory-b2c/active-directory-b2c-get-started)Azure Active Directory B2C 테 넌 트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="f4dd2-123">메시지가 표시 되 면이 자습서에서는 테 넌 트와 Azure 구독을 연결 하는 것이 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="f4dd2-124">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="f4dd2-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="f4dd2-125">새로 만든 Azure AD B2C 테 넌 트에서 설명서의 **웹 앱 등록** 섹션 아래 [에 있는 단계](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) 를 사용 하 여 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="f4dd2-126">**웹 앱 클라이언트 암호 만들기** 섹션에서 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="f4dd2-127">이 자습서에서는 클라이언트 암호가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="f4dd2-128">다음 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-128">Use the following values:</span></span>

| <span data-ttu-id="f4dd2-129">설정</span><span class="sxs-lookup"><span data-stu-id="f4dd2-129">Setting</span></span>                       | <span data-ttu-id="f4dd2-130">값</span><span class="sxs-lookup"><span data-stu-id="f4dd2-130">Value</span></span>                     | <span data-ttu-id="f4dd2-131">참고</span><span class="sxs-lookup"><span data-stu-id="f4dd2-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f4dd2-132">**이름**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-132">**Name**</span></span>                      | <span data-ttu-id="f4dd2-133">*&lt;앱 이름&gt;*</span><span class="sxs-lookup"><span data-stu-id="f4dd2-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="f4dd2-134">소비자에 게 앱을 설명 하는 앱의 **이름을** 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="f4dd2-135">**웹앱/웹 API 포함**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-135">**Include web app / web API**</span></span> | <span data-ttu-id="f4dd2-136">Yes</span><span class="sxs-lookup"><span data-stu-id="f4dd2-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="f4dd2-137">**암시적 흐름 허용**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="f4dd2-138">Yes</span><span class="sxs-lookup"><span data-stu-id="f4dd2-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="f4dd2-139">**회신 URL**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="f4dd2-140">회신 URL은 Azure AD B2C에서 앱이 요청한 토큰을 반환하는 엔드포인트입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="f4dd2-141">Visual Studio는 사용할 회신 URL을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="f4dd2-142">이제를 입력 `https://localhost:44300/signin-oidc` 하 여 양식을 완성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="f4dd2-143">**앱 ID URI**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-143">**App ID URI**</span></span>                | <span data-ttu-id="f4dd2-144">비워 둠</span><span class="sxs-lookup"><span data-stu-id="f4dd2-144">Leave blank</span></span>               | <span data-ttu-id="f4dd2-145">이 자습서에서는 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="f4dd2-146">**네이티브 클라이언트 포함**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-146">**Include native client**</span></span>     | <span data-ttu-id="f4dd2-147">아니요</span><span class="sxs-lookup"><span data-stu-id="f4dd2-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="f4dd2-148">Localhost가 아닌 회신 URL을 설정 하는 경우 [회신 url 목록에서 허용 되는 항목에 대 한 제약 조건을](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="f4dd2-149">앱이 등록 되 면 테 넌 트의 앱 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="f4dd2-150">방금 등록 한 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-150">Select the app that was just registered.</span></span> <span data-ttu-id="f4dd2-151">**응용 프로그램 ID** 필드의 오른쪽에 있는 **복사** 아이콘을 선택 하 여 클립보드에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="f4dd2-152">지금은 Azure AD B2C 테 넌 트에서 더 이상 구성할 수 없지만 브라우저 창은 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="f4dd2-153">ASP.NET Core 앱을 만든 후에 더 많은 구성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="f4dd2-154">Visual Studio에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="f4dd2-155">인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 Visual Studio 웹 응용 프로그램 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="f4dd2-156">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-156">In Visual Studio:</span></span>

1. <span data-ttu-id="f4dd2-157">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="f4dd2-158">템플릿 목록에서 **웹 응용 프로그램** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="f4dd2-159">**인증 변경** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-159">Select the **Change Authentication** button.</span></span>
    
    ![인증 변경 단추](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="f4dd2-161">**인증 변경** 대화 상자에서 **개별 사용자 계정** 을 선택한 다음 드롭다운에서 **클라우드의 기존 사용자 저장소에 연결** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![인증 대화 상자 변경](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="f4dd2-163">다음 값을 사용 하 여 양식을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="f4dd2-164">설정</span><span class="sxs-lookup"><span data-stu-id="f4dd2-164">Setting</span></span>                       | <span data-ttu-id="f4dd2-165">값</span><span class="sxs-lookup"><span data-stu-id="f4dd2-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="f4dd2-166">**도메인 이름**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-166">**Domain Name**</span></span>               | <span data-ttu-id="f4dd2-167">*&lt;B2C 테 넌 트의 도메인 이름&gt;*</span><span class="sxs-lookup"><span data-stu-id="f4dd2-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="f4dd2-168">**애플리케이션 ID**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-168">**Application ID**</span></span>            | <span data-ttu-id="f4dd2-169">*&lt;클립보드에서 응용 프로그램 ID 붙여넣기&gt;*</span><span class="sxs-lookup"><span data-stu-id="f4dd2-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="f4dd2-170">**콜백 경로**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-170">**Callback Path**</span></span>             | <span data-ttu-id="f4dd2-171">*&lt;기본값 사용&gt;*</span><span class="sxs-lookup"><span data-stu-id="f4dd2-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="f4dd2-172">**등록 또는 로그인 정책**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="f4dd2-173">**암호 재설정 정책**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="f4dd2-174">**프로필 정책 편집**</span><span class="sxs-lookup"><span data-stu-id="f4dd2-174">**Edit profile policy**</span></span>       | <span data-ttu-id="f4dd2-175">*&lt;비워 둠&gt;*</span><span class="sxs-lookup"><span data-stu-id="f4dd2-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="f4dd2-176">회신 uri 옆의 **복사** 링크를 **선택 하 여** 회신 uri를 클립보드로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="f4dd2-177">**확인** 을 선택 하 여 **인증 변경** 대화 상자를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="f4dd2-178">**확인** 을 선택 하 여 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="f4dd2-179">B2C 앱 등록을 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-179">Finish the B2C app registration</span></span>

<span data-ttu-id="f4dd2-180">B2C 앱 속성이 계속 열려 있는 브라우저 창으로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="f4dd2-181">이전에 지정한 임시 **회신 URL** 을 Visual Studio에서 복사한 값으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="f4dd2-182">창 맨 위에 있는 **저장** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="f4dd2-183">회신 URL을 복사 하지 않은 경우 웹 프로젝트 속성의 디버그 탭에서 HTTPS 주소를 사용 하 고에서 **Callbackpath** 값을 추가 *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="f4dd2-184">정책 구성</span><span class="sxs-lookup"><span data-stu-id="f4dd2-184">Configure policies</span></span>

<span data-ttu-id="f4dd2-185">Azure AD B2C 설명서의 단계를 사용 하 여 [등록 또는 로그인 정책을 만든](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)다음 [암호 재설정 정책을 만듭니다](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="f4dd2-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="f4dd2-186">**Identity 공급자** , **등록 특성** 및 **응용 프로그램 클레임** 에 대 한 설명서에 제공 된 예제 값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-186">Use the example values provided in the documentation for **Identity providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="f4dd2-187">설명서에 설명 된 대로 **지금 실행** 단추를 사용 하 여 정책을 테스트 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="f4dd2-188">Visual Studio의 **인증 변경** 대화 상자에서 정책 이름이 사용 되었으므로 정책 이름이 설명서에 설명 된 것과 정확 하 게 일치 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="f4dd2-189">에서 정책 이름을 확인할 수 있습니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f4dd2-189">The policy names can be verified in *appsettings.json* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="f4dd2-190">기본 OpenIdConnectOptions/JwtBearer/options 구성 Cookie</span><span class="sxs-lookup"><span data-stu-id="f4dd2-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="f4dd2-191">기본 옵션을 직접 구성 하려면에서 적절 한 체계 상수를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f4dd2-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="f4dd2-192">앱 실행</span><span class="sxs-lookup"><span data-stu-id="f4dd2-192">Run the app</span></span>

<span data-ttu-id="f4dd2-193">Visual Studio에서 **f5** 키를 눌러 앱을 빌드하고 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="f4dd2-194">웹 앱이 시작 된 후 **동의 함** 을 선택 하 여 cookie s (메시지가 표시 되는 경우)를 사용 하 고 **로그인** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in** .</span></span>

![앱에 로그인](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="f4dd2-196">브라우저가 Azure AD B2C 테 넌 트로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="f4dd2-197">기존 계정 (정책 테스트를 만든 경우)으로 로그인 하거나 **지금 등록** 을 선택 하 여 새 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="f4dd2-198">**암호를 잊으셨나요?** 링크를 사용 하 여 잊어버린 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C 로그인](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="f4dd2-200">성공적으로 로그인 하면 브라우저가 웹 앱으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-200">After successfully signing in, the browser redirects to the web app.</span></span>

![성공](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="f4dd2-202">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f4dd2-202">Next steps</span></span>

<span data-ttu-id="f4dd2-203">이 자습서에서는 다음 작업 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f4dd2-204">Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="f4dd2-205">Azure AD B2C에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="f4dd2-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="f4dd2-206">Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="f4dd2-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="f4dd2-207">Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성</span><span class="sxs-lookup"><span data-stu-id="f4dd2-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="f4dd2-208">ASP.NET Core 앱이 인증을 위해 Azure AD B2C를 사용 하도록 구성 되었으므로 [권한 부여 특성](xref:security/authorization/simple) 을 사용 하 여 앱을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="f4dd2-209">다음을 학습 하 여 앱을 계속 개발 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="f4dd2-210">[Azure AD B2C 사용자 인터페이스를 사용자 지정](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="f4dd2-211">[암호 복잡성 요구 사항을 구성](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="f4dd2-212">[Multi-factor authentication을 사용 하도록 설정](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="f4dd2-213">[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)등의 추가 id 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="f4dd2-214">[AZURE AD Graph API를 사용](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 하 여 Azure AD B2C 테 넌 트에서 그룹 멤버 자격과 같은 추가 사용자 정보를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="f4dd2-215">[Azure AD B2C를 사용 하 여 ASP.NET Core를 사용 하 여 빌드한 웹 API를 보호 하는 방법](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)입니다.</span><span class="sxs-lookup"><span data-stu-id="f4dd2-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="f4dd2-216">[자습서: Azure Active Directory B2C을 사용 하 여 ASP.NET WEB API에 대 한 액세스 권한 부여](/azure/active-directory-b2c/tutorial-web-api-dotnet)</span><span class="sxs-lookup"><span data-stu-id="f4dd2-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
