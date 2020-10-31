---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: 앱 보호'
author: guardrex
description: ':::no-loc(Blazor Server)::: 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055479"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="9c47d-103">ASP.NET Core :::no-loc(Blazor Server)::: 앱 보호</span><span class="sxs-lookup"><span data-stu-id="9c47d-103">Secure ASP.NET Core :::no-loc(Blazor Server)::: apps</span></span>

<span data-ttu-id="9c47d-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="9c47d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9c47d-105">:::no-loc(Blazor Server)::: 앱은 ASP.NET Core 앱과 동일한 방식으로 보안이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-105">:::no-loc(Blazor Server)::: apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9c47d-106">자세한 내용은 <xref:security/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9c47d-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="9c47d-107">이 개요의 항목은 :::no-loc(Blazor Server):::에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-107">Topics under this overview apply specifically to :::no-loc(Blazor Server):::.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="9c47d-108">:::no-loc(Blazor Server)::: 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="9c47d-108">:::no-loc(Blazor Server)::: project template</span></span>

<span data-ttu-id="9c47d-109">프로젝트를 만들 때 인증을 위해 :::no-loc(Blazor Server)::: 프로젝트 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-109">The :::no-loc(Blazor Server)::: project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9c47d-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c47d-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c47d-111"><xref:blazor/tooling>의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 :::no-loc(Blazor Server)::: 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism.</span></span>

<span data-ttu-id="9c47d-112">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **:::no-loc(Blazor Server)::: 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-112">After choosing the **:::no-loc(Blazor Server)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="9c47d-113">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9c47d-114">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="9c47d-114">**No Authentication**</span></span>
* <span data-ttu-id="9c47d-115">**개별 사용자 계정** : 사용자 계정은 다음과 같이 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="9c47d-116">ASP.NET Core의 [:::no-loc(Identity):::](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장</span><span class="sxs-lookup"><span data-stu-id="9c47d-116">Within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9c47d-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="9c47d-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9c47d-118">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="9c47d-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="9c47d-119">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="9c47d-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9c47d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c47d-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9c47d-121"><xref:blazor/tooling>의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 :::no-loc(Blazor Server)::: 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9c47d-122">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9c47d-123">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="9c47d-123">Authentication mechanism</span></span> | <span data-ttu-id="9c47d-124">설명</span><span class="sxs-lookup"><span data-stu-id="9c47d-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9c47d-125">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="9c47d-125">`None` (default)</span></span>         | <span data-ttu-id="9c47d-126">인증 없음</span><span class="sxs-lookup"><span data-stu-id="9c47d-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9c47d-127">:::no-loc(ASP.NET Core Identity):::를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="9c47d-127">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9c47d-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="9c47d-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9c47d-129">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9c47d-130">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9c47d-131">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-131">Windows Authentication</span></span> |

<span data-ttu-id="9c47d-132">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9c47d-133">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-133">Create a folder for the project.</span></span>
* <span data-ttu-id="9c47d-134">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-134">Name the project.</span></span>

<span data-ttu-id="9c47d-135">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9c47d-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9c47d-136">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c47d-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9c47d-137"><xref:blazor/tooling>의 Mac용 Visual Studio 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9c47d-138">**새 :::no-loc(Blazor Server)::: 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-138">On the **Configure your new :::no-loc(Blazor Server)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9c47d-139">:::no-loc(ASP.NET Core Identity):::를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-139">The app is created for individual users stored in the app with :::no-loc(ASP.NET Core Identity):::.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9c47d-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9c47d-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9c47d-141">명령 셸에서 다음 명령을 사용해 인증 메커니즘이 있는 새 :::no-loc(Blazor Server)::: 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-141">Create a new :::no-loc(Blazor Server)::: project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9c47d-142">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9c47d-143">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="9c47d-143">Authentication mechanism</span></span> | <span data-ttu-id="9c47d-144">설명</span><span class="sxs-lookup"><span data-stu-id="9c47d-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9c47d-145">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="9c47d-145">`None` (default)</span></span>         | <span data-ttu-id="9c47d-146">인증 없음</span><span class="sxs-lookup"><span data-stu-id="9c47d-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9c47d-147">:::no-loc(ASP.NET Core Identity):::를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="9c47d-147">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9c47d-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="9c47d-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9c47d-149">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9c47d-150">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9c47d-151">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="9c47d-151">Windows Authentication</span></span> |

<span data-ttu-id="9c47d-152">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9c47d-153">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-153">Create a folder for the project.</span></span>
* <span data-ttu-id="9c47d-154">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-154">Name the project.</span></span>

<span data-ttu-id="9c47d-155">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9c47d-155">For more information:</span></span>

* <span data-ttu-id="9c47d-156">.NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9c47d-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="9c47d-157">명령 셸에서 :::no-loc(Blazor Server)::: 템플릿(`blazorserver`)에 대한 도움말 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-157">Execute the help command for the :::no-loc(Blazor Server)::: template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="9c47d-158">스캐폴드 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="9c47d-158">Scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="9c47d-159">:::no-loc(Blazor Server)::: 프로젝트에 :::no-loc(Identity):::를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="9c47d-159">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project:</span></span>

* <span data-ttu-id="9c47d-160">[기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="9c47d-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9c47d-161">[권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="9c47d-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c47d-162">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="9c47d-162">Additional resources</span></span>

* [<span data-ttu-id="9c47d-163">빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가</span><span class="sxs-lookup"><span data-stu-id="9c47d-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="9c47d-164">빠른 시작: Microsoft ID 플랫폼을 사용하여 ASP.NET Core 웹 API 보호</span><span class="sxs-lookup"><span data-stu-id="9c47d-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
