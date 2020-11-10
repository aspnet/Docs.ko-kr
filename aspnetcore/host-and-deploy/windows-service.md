---
title: Windows 서비스에서 ASP.NET Core 호스트
author: rick-anderson
description: Windows 서비스에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 31a738e7aa8779171dfa09a5678d7240b8f62343
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057234"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="db839-103">Windows 서비스에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="db839-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db839-104">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="db839-105">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="db839-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db839-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="db839-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="db839-107">Prerequisites</span></span>

* [<span data-ttu-id="db839-108">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="db839-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="db839-109">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="db839-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="db839-110">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="db839-110">Worker Service template</span></span>

<span data-ttu-id="db839-111">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="db839-112">템플릿을 Windows Service 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="db839-113">.NET Core 템플릿에서 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="db839-114">[앱 구성](#app-configuration) 섹션의 지침에 따라 Windows Service로 실행할 수 있도록 Worker Service 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="db839-115">앱 구성</span><span class="sxs-lookup"><span data-stu-id="db839-115">App configuration</span></span>

<span data-ttu-id="db839-116">앱에는 [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="db839-117">`IHostBuilder.UseWindowsService`는 호스트를 빌드할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="db839-118">앱이 Windows 서비스로 실행되는 경우 이 메서드는 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="db839-119">호스트 수명을 `WindowsServiceLifetime`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="db839-120">[콘텐츠 루트](xref:fundamentals/index#content-root)를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="db839-121">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="db839-122">이벤트 로그에 대한 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="db839-123">애플리케이션 이름이 기본 소스 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="db839-124">호스트를 빌드하기 위해 `CreateDefaultBuilder`를 호출하는 ASP.NET Core 템플릿 기반 앱에 대한 기본 로그 수준은 *경고* 이상입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="db839-125">*:::no-loc(appsettings.json):::* /*appsettings.{Environment}.json* 의 `Logging:EventLog:LogLevel:Default` 키 또는 다른 구성 공급자로 기본 로그 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *:::no-loc(appsettings.json):::*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="db839-126">관리자만 새 이벤트 소스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="db839-127">애플리케이션 이름을 사용하여 이벤트 소스를 만들 수 없는 경우 *Application* 소스에 경고가 기록되고 이벤트 로그가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="db839-128">*Program.cs* 의 `CreateHostBuilder`에서:</span><span class="sxs-lookup"><span data-stu-id="db839-128">In `CreateHostBuilder` of *Program.cs* :</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="db839-129">다음 샘플 앱은 이 항목과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="db839-130">백그라운드 작업자 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하는 [작업자 서비스 템플릿](#worker-service-template)을 기반으로 하는 비 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="db839-131">웹앱 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하여 Windows 서비스로 실행되는 :::no-loc(Razor)::: Pages 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-131">Web App Service Sample: A :::no-loc(Razor)::: Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="db839-132">MVC 지침은 <xref:mvc/overview> 및 <xref:migration/22-to-30>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="db839-133">배포 유형</span><span class="sxs-lookup"><span data-stu-id="db839-133">Deployment type</span></span>

<span data-ttu-id="db839-134">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="db839-135">SDK</span><span class="sxs-lookup"><span data-stu-id="db839-135">SDK</span></span>

<span data-ttu-id="db839-136">:::no-loc(Razor)::: Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-136">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="db839-137">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="db839-138">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="db839-139">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="db839-140">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe* )을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="db839-141">[Web SDK](#sdk)를 사용할 경우 ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="db839-142">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="db839-143">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="db839-144">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="db839-145">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="db839-146">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="db839-147">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="db839-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="db839-148">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="db839-149">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="db839-150">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="db839-151">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="db839-151">Service user account</span></span>

<span data-ttu-id="db839-152">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="db839-153">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="db839-154">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="db839-155">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="db839-156">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="db839-157">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="db839-158">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="db839-159">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="db839-160">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="db839-160">Log on as a service rights</span></span>

<span data-ttu-id="db839-161">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="db839-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="db839-162">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="db839-163">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="db839-164">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="db839-165">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="db839-166">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="db839-167">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="db839-168">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-168">Select **Advanced**.</span></span> <span data-ttu-id="db839-169">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-169">Select **Find Now**.</span></span> <span data-ttu-id="db839-170">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-170">Select the user account from the list.</span></span> <span data-ttu-id="db839-171">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-171">Select **OK**.</span></span> <span data-ttu-id="db839-172">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="db839-173">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="db839-174">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="db839-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="db839-175">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="db839-175">Create a service</span></span>

<span data-ttu-id="db839-176">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="db839-177">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="db839-178">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="db839-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="db839-179">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="db839-180">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="db839-181">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="db839-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="db839-182">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="db839-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="db839-183">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="db839-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="db839-184">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="db839-185">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="db839-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="db839-186">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="db839-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="db839-187">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="db839-187">Start a service</span></span>

<span data-ttu-id="db839-188">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-189">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="db839-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="db839-190">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="db839-190">Determine a service's status</span></span>

<span data-ttu-id="db839-191">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-192">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="db839-193">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="db839-193">Stop a service</span></span>

<span data-ttu-id="db839-194">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="db839-195">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="db839-195">Remove a service</span></span>

<span data-ttu-id="db839-196">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="db839-197">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="db839-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="db839-198">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="db839-199">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="db839-200">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="db839-200">Configure endpoints</span></span>

<span data-ttu-id="db839-201">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="db839-202">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="db839-203">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="db839-204">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="db839-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="db839-205">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="db839-206">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="db839-207">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="db839-207">Current directory and content root</span></span>

<span data-ttu-id="db839-208">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="db839-209">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="db839-210">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="db839-211">ContentRootPath 또는 ContentRootFileProvider 사용</span><span class="sxs-lookup"><span data-stu-id="db839-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="db839-212">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 또는 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>를 사용하여 앱의 리소스를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="db839-213">앱이 서비스로 실행되면 <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*>가 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath>를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="db839-214">앱의 기본 설정 파일인 *:::no-loc(appsettings.json):::* 및 *appsettings.{Environment}.json* 은 [호스트 생성 중에 CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host)를 호출하여 앱의 콘텐츠 루트에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-214">The app's default settings files, *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* , are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="db839-215"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에서 개발자 코드로 로드되는 다른 설정 파일의 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="db839-216">다음 예제에서 *custom_settings.json* 파일은 앱의 콘텐츠 루트에 있으며 기본 경로를 명시적으로 설정하지 않고 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="db839-217">Windows Service 앱은 *C:\\WINDOWS\\system32* 폴더를 현재 디렉터리로 반환하기 때문에 <xref:System.IO.Directory.GetCurrentDirectory*>를 사용하여 리소스 경로를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="db839-218">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="db839-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="db839-219">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db839-220">문제 해결</span><span class="sxs-lookup"><span data-stu-id="db839-220">Troubleshoot</span></span>

<span data-ttu-id="db839-221">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="db839-222">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="db839-222">Common errors</span></span>

* <span data-ttu-id="db839-223">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="db839-224">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="db839-225">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="db839-226">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="db839-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="db839-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="db839-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="db839-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="db839-229">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="db839-230">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="db839-231">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="db839-232">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="db839-233">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="db839-234">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="db839-235">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="db839-236">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="db839-236">System and Application Event Logs</span></span>

<span data-ttu-id="db839-237">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="db839-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="db839-238">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-238">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="db839-239">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-239">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="db839-240">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="db839-241">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="db839-242">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="db839-243">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="db839-243">Run the app at a command prompt</span></span>

<span data-ttu-id="db839-244">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="db839-245">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="db839-246">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="db839-247">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="db839-247">Clear package caches</span></span>

<span data-ttu-id="db839-248">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="db839-249">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="db839-250">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="db839-251">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="db839-252">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="db839-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="db839-253">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="db839-254">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="db839-255">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="db839-256">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="db839-257">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="db839-257">Slow or hanging app</span></span>

<span data-ttu-id="db839-258">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="db839-259">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="db839-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="db839-260">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="db839-261">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="db839-262">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="db839-263">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="db839-264">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="db839-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="db839-265">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="db839-266">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="db839-267">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="db839-268">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="db839-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="db839-269">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="db839-270">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="db839-270">Analyze the dump</span></span>

<span data-ttu-id="db839-271">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="db839-272">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db839-273">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db839-273">Additional resources</span></span>

* <span data-ttu-id="db839-274">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="db839-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="db839-275">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="db839-276">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="db839-277">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db839-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="db839-278">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="db839-278">Prerequisites</span></span>

* [<span data-ttu-id="db839-279">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="db839-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="db839-280">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="db839-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="db839-281">앱 구성</span><span class="sxs-lookup"><span data-stu-id="db839-281">App configuration</span></span>

<span data-ttu-id="db839-282">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="db839-283">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="db839-284">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="db839-285">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="db839-286">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="db839-287"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="db839-288"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="db839-289">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="db839-290">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="db839-291"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="db839-292">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="db839-293">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="db839-294">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="db839-295">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="db839-296">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="db839-297">배포 유형</span><span class="sxs-lookup"><span data-stu-id="db839-297">Deployment type</span></span>

<span data-ttu-id="db839-298">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="db839-299">SDK</span><span class="sxs-lookup"><span data-stu-id="db839-299">SDK</span></span>

<span data-ttu-id="db839-300">:::no-loc(Razor)::: Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-300">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="db839-301">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="db839-302">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="db839-303">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="db839-304">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe* )을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="db839-305">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="db839-306">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="db839-307">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="db839-308">해당 속성은 SDK에 Windows용 실행 파일( *.exe* )과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-308">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="db839-309">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="db839-310">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="db839-311">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="db839-312">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="db839-313">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="db839-314">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="db839-315">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="db839-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="db839-316">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="db839-317">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="db839-318">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="db839-319">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="db839-320">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="db839-320">Service user account</span></span>

<span data-ttu-id="db839-321">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="db839-322">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="db839-323">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="db839-324">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="db839-325">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="db839-326">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="db839-327">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="db839-328">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="db839-329">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="db839-329">Log on as a service rights</span></span>

<span data-ttu-id="db839-330">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="db839-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="db839-331">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="db839-332">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="db839-333">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="db839-334">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="db839-335">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="db839-336">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="db839-337">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-337">Select **Advanced**.</span></span> <span data-ttu-id="db839-338">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-338">Select **Find Now**.</span></span> <span data-ttu-id="db839-339">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-339">Select the user account from the list.</span></span> <span data-ttu-id="db839-340">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-340">Select **OK**.</span></span> <span data-ttu-id="db839-341">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="db839-342">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="db839-343">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="db839-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="db839-344">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="db839-344">Create a service</span></span>

<span data-ttu-id="db839-345">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="db839-346">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="db839-347">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="db839-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="db839-348">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="db839-349">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="db839-350">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="db839-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="db839-351">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="db839-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="db839-352">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="db839-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="db839-353">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="db839-354">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="db839-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="db839-355">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="db839-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="db839-356">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="db839-356">Start a service</span></span>

<span data-ttu-id="db839-357">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-358">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="db839-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="db839-359">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="db839-359">Determine a service's status</span></span>

<span data-ttu-id="db839-360">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-361">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="db839-362">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="db839-362">Stop a service</span></span>

<span data-ttu-id="db839-363">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="db839-364">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="db839-364">Remove a service</span></span>

<span data-ttu-id="db839-365">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="db839-366">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="db839-366">Handle starting and stopping events</span></span>

<span data-ttu-id="db839-367"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="db839-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="db839-368">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="db839-369">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="db839-370">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="db839-371">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="db839-372">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="db839-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="db839-373">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="db839-374">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="db839-375">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="db839-375">Configure endpoints</span></span>

<span data-ttu-id="db839-376">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="db839-377">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="db839-378">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="db839-379">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="db839-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="db839-380">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="db839-381">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="db839-382">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="db839-382">Current directory and content root</span></span>

<span data-ttu-id="db839-383">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="db839-384">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="db839-385">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="db839-386">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="db839-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="db839-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="db839-388">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="db839-389">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="db839-390">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="db839-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="db839-391">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db839-392">문제 해결</span><span class="sxs-lookup"><span data-stu-id="db839-392">Troubleshoot</span></span>

<span data-ttu-id="db839-393">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="db839-394">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="db839-394">Common errors</span></span>

* <span data-ttu-id="db839-395">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="db839-396">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="db839-397">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="db839-398">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="db839-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="db839-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="db839-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="db839-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="db839-401">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="db839-402">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="db839-403">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="db839-404">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="db839-405">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="db839-406">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="db839-407">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="db839-408">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="db839-408">System and Application Event Logs</span></span>

<span data-ttu-id="db839-409">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="db839-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="db839-410">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-410">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="db839-411">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-411">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="db839-412">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="db839-413">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="db839-414">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="db839-415">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="db839-415">Run the app at a command prompt</span></span>

<span data-ttu-id="db839-416">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="db839-417">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="db839-418">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="db839-419">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="db839-419">Clear package caches</span></span>

<span data-ttu-id="db839-420">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="db839-421">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="db839-422">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="db839-423">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="db839-424">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="db839-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="db839-425">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="db839-426">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="db839-427">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="db839-428">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="db839-429">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="db839-429">Slow or hanging app</span></span>

<span data-ttu-id="db839-430">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="db839-431">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="db839-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="db839-432">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="db839-433">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="db839-434">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="db839-435">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="db839-436">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="db839-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="db839-437">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="db839-438">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="db839-439">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="db839-440">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="db839-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="db839-441">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="db839-442">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="db839-442">Analyze the dump</span></span>

<span data-ttu-id="db839-443">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="db839-444">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db839-445">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db839-445">Additional resources</span></span>

* <span data-ttu-id="db839-446">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="db839-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="db839-447">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="db839-448">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="db839-449">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db839-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="db839-450">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="db839-450">Prerequisites</span></span>

* [<span data-ttu-id="db839-451">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="db839-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="db839-452">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="db839-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="db839-453">앱 구성</span><span class="sxs-lookup"><span data-stu-id="db839-453">App configuration</span></span>

<span data-ttu-id="db839-454">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="db839-455">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="db839-456">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="db839-457">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="db839-458">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="db839-459"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="db839-460"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="db839-461">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="db839-462">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="db839-463"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="db839-464">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="db839-465">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="db839-466">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="db839-467">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="db839-468">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="db839-469">배포 유형</span><span class="sxs-lookup"><span data-stu-id="db839-469">Deployment type</span></span>

<span data-ttu-id="db839-470">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="db839-471">SDK</span><span class="sxs-lookup"><span data-stu-id="db839-471">SDK</span></span>

<span data-ttu-id="db839-472">:::no-loc(Razor)::: Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-472">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="db839-473">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="db839-474">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="db839-475">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="db839-476">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe* )을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="db839-477">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="db839-478">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="db839-479">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="db839-480">해당 속성은 SDK에 Windows용 실행 파일( *.exe* )과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-480">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="db839-481">`<UseAppHost>` 속성은 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="db839-482">이 속성은 서비스에 FDD의 활성화 경로(실행 파일, *.exe* )를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-482">This property provides the service with an activation path (an executable, *.exe* ) for an FDD.</span></span>

<span data-ttu-id="db839-483">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="db839-484">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="db839-485">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="db839-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="db839-486">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="db839-487">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="db839-488">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="db839-489">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="db839-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="db839-490">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="db839-491">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="db839-492">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="db839-493">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="db839-494">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="db839-494">Service user account</span></span>

<span data-ttu-id="db839-495">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="db839-496">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="db839-497">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="db839-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="db839-498">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="db839-499">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="db839-500">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="db839-501">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="db839-502">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="db839-503">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="db839-503">Log on as a service rights</span></span>

<span data-ttu-id="db839-504">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="db839-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="db839-505">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="db839-506">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="db839-507">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="db839-508">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="db839-509">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="db839-510">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="db839-511">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-511">Select **Advanced**.</span></span> <span data-ttu-id="db839-512">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-512">Select **Find Now**.</span></span> <span data-ttu-id="db839-513">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-513">Select the user account from the list.</span></span> <span data-ttu-id="db839-514">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-514">Select **OK**.</span></span> <span data-ttu-id="db839-515">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="db839-516">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="db839-517">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="db839-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="db839-518">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="db839-518">Create a service</span></span>

<span data-ttu-id="db839-519">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="db839-520">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="db839-521">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="db839-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="db839-522">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="db839-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="db839-523">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="db839-524">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="db839-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="db839-525">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="db839-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="db839-526">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="db839-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="db839-527">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="db839-528">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="db839-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="db839-529">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="db839-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="db839-530">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="db839-530">Start a service</span></span>

<span data-ttu-id="db839-531">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-532">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="db839-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="db839-533">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="db839-533">Determine a service's status</span></span>

<span data-ttu-id="db839-534">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="db839-535">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="db839-536">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="db839-536">Stop a service</span></span>

<span data-ttu-id="db839-537">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="db839-538">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="db839-538">Remove a service</span></span>

<span data-ttu-id="db839-539">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="db839-540">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="db839-540">Handle starting and stopping events</span></span>

<span data-ttu-id="db839-541"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="db839-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="db839-542">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="db839-543">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="db839-544">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="db839-545">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="db839-546">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="db839-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="db839-547">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="db839-548">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="db839-549">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="db839-549">Configure endpoints</span></span>

<span data-ttu-id="db839-550">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="db839-551">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="db839-552">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="db839-553">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="db839-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="db839-554">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="db839-555">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="db839-556">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="db839-556">Current directory and content root</span></span>

<span data-ttu-id="db839-557">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="db839-558">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="db839-559">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="db839-560">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="db839-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="db839-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="db839-562">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="db839-563">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="db839-564">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="db839-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="db839-565">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db839-566">문제 해결</span><span class="sxs-lookup"><span data-stu-id="db839-566">Troubleshoot</span></span>

<span data-ttu-id="db839-567">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="db839-568">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="db839-568">Common errors</span></span>

* <span data-ttu-id="db839-569">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="db839-570">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="db839-571">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="db839-572">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="db839-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="db839-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="db839-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="db839-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="db839-575">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db839-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="db839-576">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="db839-577">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="db839-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="db839-578">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="db839-579">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="db839-580">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="db839-581">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="db839-582">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="db839-582">System and Application Event Logs</span></span>

<span data-ttu-id="db839-583">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="db839-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="db839-584">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-584">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="db839-585">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-585">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="db839-586">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="db839-587">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="db839-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="db839-588">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="db839-589">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="db839-589">Run the app at a command prompt</span></span>

<span data-ttu-id="db839-590">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="db839-591">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="db839-592">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="db839-593">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="db839-593">Clear package caches</span></span>

<span data-ttu-id="db839-594">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="db839-595">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="db839-596">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="db839-597">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="db839-598">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="db839-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="db839-599">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="db839-600">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="db839-601">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="db839-602">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="db839-603">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="db839-603">Slow or hanging app</span></span>

<span data-ttu-id="db839-604">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db839-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="db839-605">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="db839-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="db839-606">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="db839-607">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db839-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="db839-608">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="db839-609">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="db839-610">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="db839-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="db839-611">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="db839-612">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="db839-613">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="db839-614">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="db839-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="db839-615">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="db839-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="db839-616">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="db839-616">Analyze the dump</span></span>

<span data-ttu-id="db839-617">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db839-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="db839-618">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db839-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db839-619">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db839-619">Additional resources</span></span>

* <span data-ttu-id="db839-620">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="db839-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
