---
title: ASP.NET Core Blazor Server 앱 보호
author: guardrex
description: Blazor Server 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280320"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="153bd-103">ASP.NET Core Blazor Server 앱 보호</span><span class="sxs-lookup"><span data-stu-id="153bd-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="153bd-104">Blazor Server 앱은 ASP.NET Core 앱과 동일한 방식으로 보안이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="153bd-105">자세한 내용은 <xref:security/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="153bd-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="153bd-106">이 개요의 항목은 Blazor Server에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="153bd-107">Blazor Server 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="153bd-107">Blazor Server project template</span></span>

<span data-ttu-id="153bd-108">프로젝트를 만들 때 인증을 위해 Blazor Server 프로젝트 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="153bd-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="153bd-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="153bd-110"><xref:blazor/tooling>의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="153bd-111">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor Server 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="153bd-112">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="153bd-113">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="153bd-113">**No Authentication**</span></span>
* <span data-ttu-id="153bd-114">**개별 사용자 계정**: 사용자 계정은 다음과 같이 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="153bd-115">ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장</span><span class="sxs-lookup"><span data-stu-id="153bd-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="153bd-116">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="153bd-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="153bd-117">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="153bd-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="153bd-118">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="153bd-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="153bd-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="153bd-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="153bd-120"><xref:blazor/tooling>의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="153bd-121">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="153bd-122">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="153bd-122">Authentication mechanism</span></span> | <span data-ttu-id="153bd-123">설명</span><span class="sxs-lookup"><span data-stu-id="153bd-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="153bd-124">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="153bd-124">`None` (default)</span></span>         | <span data-ttu-id="153bd-125">인증 없음</span><span class="sxs-lookup"><span data-stu-id="153bd-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="153bd-126">ASP.NET Core Identity를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="153bd-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="153bd-127">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="153bd-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="153bd-128">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="153bd-129">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="153bd-130">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-130">Windows Authentication</span></span> |

<span data-ttu-id="153bd-131">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="153bd-132">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-132">Create a folder for the project.</span></span>
* <span data-ttu-id="153bd-133">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-133">Name the project.</span></span>

<span data-ttu-id="153bd-134">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="153bd-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="153bd-135">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="153bd-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="153bd-136"><xref:blazor/tooling>의 Mac용 Visual Studio 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="153bd-137">**새 Blazor Server 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="153bd-138">ASP.NET Core Identity를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="153bd-139">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="153bd-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="153bd-140">명령 셸에서 다음 명령을 사용해 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="153bd-141">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="153bd-142">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="153bd-142">Authentication mechanism</span></span> | <span data-ttu-id="153bd-143">설명</span><span class="sxs-lookup"><span data-stu-id="153bd-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="153bd-144">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="153bd-144">`None` (default)</span></span>         | <span data-ttu-id="153bd-145">인증 없음</span><span class="sxs-lookup"><span data-stu-id="153bd-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="153bd-146">ASP.NET Core Identity를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="153bd-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="153bd-147">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="153bd-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="153bd-148">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="153bd-149">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="153bd-150">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="153bd-150">Windows Authentication</span></span> |

<span data-ttu-id="153bd-151">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="153bd-152">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-152">Create a folder for the project.</span></span>
* <span data-ttu-id="153bd-153">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-153">Name the project.</span></span>

<span data-ttu-id="153bd-154">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="153bd-154">For more information:</span></span>

* <span data-ttu-id="153bd-155">.NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="153bd-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="153bd-156">명령 셸에서 Blazor Server 템플릿(`blazorserver`)에 대한 도움말 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="153bd-157">스캐폴드 Identity</span><span class="sxs-lookup"><span data-stu-id="153bd-157">Scaffold Identity</span></span>

<span data-ttu-id="153bd-158">Blazor Server 프로젝트에 Identity를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="153bd-159">[기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="153bd-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="153bd-160">[권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="153bd-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="153bd-161">Identity Server를 사용한 Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="153bd-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="153bd-162">Identity Server를 사용하여 Azure App Service on Linux에 배포할 때 발급자를 명시적으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="153bd-163">자세한 내용은 <xref:security/authentication/identity/spa#azure-app-service-on-linux>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="153bd-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="153bd-164">추가 자료</span><span class="sxs-lookup"><span data-stu-id="153bd-164">Additional resources</span></span>

* [<span data-ttu-id="153bd-165">빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가</span><span class="sxs-lookup"><span data-stu-id="153bd-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="153bd-166">빠른 시작: Microsoft ID 플랫폼을 사용하여 ASP.NET Core 웹 API 보호</span><span class="sxs-lookup"><span data-stu-id="153bd-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="153bd-167"><xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="153bd-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="153bd-168">전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.</span><span class="sxs-lookup"><span data-stu-id="153bd-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="153bd-169">수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.</span><span class="sxs-lookup"><span data-stu-id="153bd-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
