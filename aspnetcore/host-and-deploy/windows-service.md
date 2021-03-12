---
title: Windows 서비스에서 ASP.NET Core 호스트
author: rick-anderson
description: Windows 서비스에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588817"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="65051-103">Windows 서비스에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="65051-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65051-104">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65051-105">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65051-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65051-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65051-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="65051-107">Prerequisites</span></span>

* [<span data-ttu-id="65051-108">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="65051-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65051-109">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="65051-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="65051-110">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="65051-110">Worker Service template</span></span>

<span data-ttu-id="65051-111">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="65051-112">템플릿을 Windows Service 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="65051-113">.NET Core 템플릿에서 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="65051-114">[앱 구성](#app-configuration) 섹션의 지침에 따라 Windows Service로 실행할 수 있도록 Worker Service 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="65051-115">앱 구성</span><span class="sxs-lookup"><span data-stu-id="65051-115">App configuration</span></span>

<span data-ttu-id="65051-116">앱에는 [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="65051-117">`IHostBuilder.UseWindowsService`는 호스트를 빌드할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="65051-118">앱이 Windows 서비스로 실행되는 경우 이 메서드는 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="65051-119">호스트 수명을 `WindowsServiceLifetime`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="65051-120">[콘텐츠 루트](xref:fundamentals/index#content-root)를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="65051-121">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="65051-122">이벤트 로그에 대한 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="65051-123">애플리케이션 이름이 기본 소스 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="65051-124">호스트를 빌드하기 위해 `CreateDefaultBuilder`를 호출하는 ASP.NET Core 템플릿 기반 앱에 대한 기본 로그 수준은 *경고* 이상입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="65051-125">*appsettings.json* /*appsettings.{Environment}.json* 의 `Logging:EventLog:LogLevel:Default` 키 또는 다른 구성 공급자로 기본 로그 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="65051-126">관리자만 새 이벤트 소스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="65051-127">애플리케이션 이름을 사용하여 이벤트 소스를 만들 수 없는 경우 *Application* 소스에 경고가 기록되고 이벤트 로그가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="65051-128">*Program.cs* 의 `CreateHostBuilder`에서:</span><span class="sxs-lookup"><span data-stu-id="65051-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="65051-129">다음 샘플 앱은 이 항목과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="65051-130">백그라운드 작업자 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하는 [작업자 서비스 템플릿](#worker-service-template)을 기반으로 하는 비 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="65051-131">웹앱 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하여 Windows 서비스로 실행되는 Razor Pages 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="65051-132">MVC 지침은 <xref:mvc/overview> 및 <xref:migration/22-to-30>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="65051-133">배포 유형</span><span class="sxs-lookup"><span data-stu-id="65051-133">Deployment type</span></span>

<span data-ttu-id="65051-134">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65051-135">SDK</span><span class="sxs-lookup"><span data-stu-id="65051-135">SDK</span></span>

<span data-ttu-id="65051-136">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65051-137">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65051-138">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65051-139">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65051-140">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65051-141">[Web SDK](#sdk)를 사용할 경우 ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65051-142">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65051-143">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65051-144">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65051-145">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65051-146">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65051-147">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="65051-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65051-148">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65051-149">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65051-150">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="65051-151">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="65051-151">Service user account</span></span>

<span data-ttu-id="65051-152">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65051-153">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65051-154">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65051-155">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65051-156">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65051-157">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65051-158">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65051-159">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65051-160">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="65051-160">Log on as a service rights</span></span>

<span data-ttu-id="65051-161">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="65051-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65051-162">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65051-163">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65051-164">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65051-165">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65051-166">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65051-167">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65051-168">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-168">Select **Advanced**.</span></span> <span data-ttu-id="65051-169">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-169">Select **Find Now**.</span></span> <span data-ttu-id="65051-170">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-170">Select the user account from the list.</span></span> <span data-ttu-id="65051-171">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-171">Select **OK**.</span></span> <span data-ttu-id="65051-172">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65051-173">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65051-174">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="65051-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65051-175">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="65051-175">Create a service</span></span>

<span data-ttu-id="65051-176">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65051-177">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65051-178">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="65051-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65051-179">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65051-180">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65051-181">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="65051-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65051-182">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="65051-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65051-183">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="65051-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65051-184">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65051-185">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="65051-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65051-186">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="65051-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65051-187">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="65051-187">Start a service</span></span>

<span data-ttu-id="65051-188">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-189">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="65051-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65051-190">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="65051-190">Determine a service's status</span></span>

<span data-ttu-id="65051-191">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-192">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65051-193">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="65051-193">Stop a service</span></span>

<span data-ttu-id="65051-194">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65051-195">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="65051-195">Remove a service</span></span>

<span data-ttu-id="65051-196">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65051-197">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="65051-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65051-198">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65051-199">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65051-200">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="65051-200">Configure endpoints</span></span>

<span data-ttu-id="65051-201">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65051-202">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65051-203">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65051-204">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="65051-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65051-205">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65051-206">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65051-207">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="65051-207">Current directory and content root</span></span>

<span data-ttu-id="65051-208">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory%2A>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65051-209">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65051-210">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="65051-211">ContentRootPath 또는 ContentRootFileProvider 사용</span><span class="sxs-lookup"><span data-stu-id="65051-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="65051-212">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 또는 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>를 사용하여 앱의 리소스를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="65051-213">앱이 서비스로 실행되면 <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A>가 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath>를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="65051-214">앱의 기본 설정 파일인 *appsettings.json* 및 *appsettings.{Environment}.json* 은 [호스트 생성 중에 CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host)를 호출하여 앱의 콘텐츠 루트에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="65051-215"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>에서 개발자 코드로 로드되는 다른 설정 파일의 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="65051-216">다음 예제에서 *custom_settings.json* 파일은 앱의 콘텐츠 루트에 있으며 기본 경로를 명시적으로 설정하지 않고 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="65051-217">Windows Service 앱은 *C:\\WINDOWS\\system32* 폴더를 현재 디렉터리로 반환하기 때문에 <xref:System.IO.Directory.GetCurrentDirectory%2A>를 사용하여 리소스 경로를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65051-218">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="65051-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65051-219">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65051-220">문제 해결</span><span class="sxs-lookup"><span data-stu-id="65051-220">Troubleshoot</span></span>

<span data-ttu-id="65051-221">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65051-222">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="65051-222">Common errors</span></span>

* <span data-ttu-id="65051-223">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65051-224">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65051-225">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65051-226">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65051-227">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="65051-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65051-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="65051-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65051-229">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65051-230">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65051-231">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65051-232">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65051-233">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65051-234">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65051-235">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65051-236">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="65051-236">System and Application Event Logs</span></span>

<span data-ttu-id="65051-237">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="65051-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65051-238">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65051-239">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65051-240">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65051-241">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65051-242">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65051-243">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="65051-243">Run the app at a command prompt</span></span>

<span data-ttu-id="65051-244">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65051-245">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65051-246">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65051-247">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="65051-247">Clear package caches</span></span>

<span data-ttu-id="65051-248">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65051-249">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65051-250">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65051-251">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65051-252">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="65051-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65051-253">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65051-254">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65051-255">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65051-256">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65051-257">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="65051-257">Slow or hanging app</span></span>

<span data-ttu-id="65051-258">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65051-259">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="65051-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="65051-260">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65051-261">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65051-262">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65051-263">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65051-264">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="65051-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65051-265">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65051-266">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65051-267">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65051-268">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="65051-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65051-269">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65051-270">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="65051-270">Analyze the dump</span></span>

<span data-ttu-id="65051-271">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65051-272">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65051-273">추가 자료</span><span class="sxs-lookup"><span data-stu-id="65051-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="65051-274">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="65051-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="65051-275">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="65051-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="65051-276">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65051-277">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65051-278">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65051-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65051-279">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="65051-279">Prerequisites</span></span>

* [<span data-ttu-id="65051-280">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="65051-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65051-281">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="65051-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="65051-282">앱 구성</span><span class="sxs-lookup"><span data-stu-id="65051-282">App configuration</span></span>

<span data-ttu-id="65051-283">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="65051-284">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="65051-285">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="65051-286">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="65051-287">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="65051-288"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="65051-289"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="65051-290">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="65051-291">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="65051-292"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="65051-293">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="65051-294">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="65051-295">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="65051-296">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="65051-297">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="65051-298">배포 유형</span><span class="sxs-lookup"><span data-stu-id="65051-298">Deployment type</span></span>

<span data-ttu-id="65051-299">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65051-300">SDK</span><span class="sxs-lookup"><span data-stu-id="65051-300">SDK</span></span>

<span data-ttu-id="65051-301">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65051-302">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65051-303">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65051-304">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65051-305">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65051-306">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="65051-307">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="65051-308">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="65051-309">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="65051-310">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65051-311">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65051-312">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65051-313">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65051-314">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65051-315">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65051-316">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="65051-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65051-317">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65051-318">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65051-319">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="65051-320">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="65051-321">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="65051-321">Service user account</span></span>

<span data-ttu-id="65051-322">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65051-323">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65051-324">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65051-325">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65051-326">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65051-327">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65051-328">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65051-329">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65051-330">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="65051-330">Log on as a service rights</span></span>

<span data-ttu-id="65051-331">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="65051-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65051-332">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65051-333">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65051-334">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65051-335">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65051-336">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65051-337">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65051-338">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-338">Select **Advanced**.</span></span> <span data-ttu-id="65051-339">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-339">Select **Find Now**.</span></span> <span data-ttu-id="65051-340">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-340">Select the user account from the list.</span></span> <span data-ttu-id="65051-341">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-341">Select **OK**.</span></span> <span data-ttu-id="65051-342">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65051-343">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65051-344">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="65051-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65051-345">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="65051-345">Create a service</span></span>

<span data-ttu-id="65051-346">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65051-347">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65051-348">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="65051-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65051-349">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65051-350">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65051-351">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="65051-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65051-352">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="65051-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65051-353">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="65051-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65051-354">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65051-355">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="65051-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65051-356">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="65051-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65051-357">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="65051-357">Start a service</span></span>

<span data-ttu-id="65051-358">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-359">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="65051-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65051-360">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="65051-360">Determine a service's status</span></span>

<span data-ttu-id="65051-361">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-362">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65051-363">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="65051-363">Stop a service</span></span>

<span data-ttu-id="65051-364">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65051-365">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="65051-365">Remove a service</span></span>

<span data-ttu-id="65051-366">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="65051-367">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="65051-367">Handle starting and stopping events</span></span>

<span data-ttu-id="65051-368"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="65051-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="65051-369">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="65051-370">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="65051-371">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="65051-372">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65051-373">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="65051-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65051-374">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65051-375">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65051-376">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="65051-376">Configure endpoints</span></span>

<span data-ttu-id="65051-377">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65051-378">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65051-379">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65051-380">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="65051-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65051-381">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65051-382">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65051-383">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="65051-383">Current directory and content root</span></span>

<span data-ttu-id="65051-384">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65051-385">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65051-386">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="65051-387">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="65051-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="65051-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="65051-389">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="65051-390">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65051-391">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="65051-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65051-392">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65051-393">문제 해결</span><span class="sxs-lookup"><span data-stu-id="65051-393">Troubleshoot</span></span>

<span data-ttu-id="65051-394">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65051-395">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="65051-395">Common errors</span></span>

* <span data-ttu-id="65051-396">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65051-397">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65051-398">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65051-399">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65051-400">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="65051-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65051-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="65051-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65051-402">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65051-403">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65051-404">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65051-405">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65051-406">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65051-407">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65051-408">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65051-409">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="65051-409">System and Application Event Logs</span></span>

<span data-ttu-id="65051-410">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="65051-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65051-411">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65051-412">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65051-413">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65051-414">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65051-415">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65051-416">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="65051-416">Run the app at a command prompt</span></span>

<span data-ttu-id="65051-417">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65051-418">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65051-419">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65051-420">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="65051-420">Clear package caches</span></span>

<span data-ttu-id="65051-421">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65051-422">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65051-423">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65051-424">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65051-425">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="65051-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65051-426">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65051-427">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65051-428">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65051-429">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65051-430">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="65051-430">Slow or hanging app</span></span>

<span data-ttu-id="65051-431">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65051-432">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="65051-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="65051-433">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65051-434">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65051-435">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65051-436">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65051-437">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="65051-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65051-438">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65051-439">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65051-440">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65051-441">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="65051-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65051-442">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65051-443">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="65051-443">Analyze the dump</span></span>

<span data-ttu-id="65051-444">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65051-445">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65051-446">추가 자료</span><span class="sxs-lookup"><span data-stu-id="65051-446">Additional resources</span></span>

* <span data-ttu-id="65051-447">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="65051-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="65051-448">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65051-449">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65051-450">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65051-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65051-451">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="65051-451">Prerequisites</span></span>

* [<span data-ttu-id="65051-452">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="65051-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65051-453">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="65051-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="65051-454">앱 구성</span><span class="sxs-lookup"><span data-stu-id="65051-454">App configuration</span></span>

<span data-ttu-id="65051-455">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="65051-456">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="65051-457">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="65051-458">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="65051-459">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="65051-460"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="65051-461"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="65051-462">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="65051-463">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="65051-464"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="65051-465">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="65051-466">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="65051-467">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="65051-468">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="65051-469">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="65051-470">배포 유형</span><span class="sxs-lookup"><span data-stu-id="65051-470">Deployment type</span></span>

<span data-ttu-id="65051-471">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65051-472">SDK</span><span class="sxs-lookup"><span data-stu-id="65051-472">SDK</span></span>

<span data-ttu-id="65051-473">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65051-474">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65051-475">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65051-476">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65051-477">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일* 이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65051-478">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="65051-479">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="65051-480">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="65051-481">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="65051-482">`<UseAppHost>` 속성은 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="65051-483">이 속성은 서비스에 FDD의 활성화 경로(실행 파일, *.exe*)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="65051-484">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65051-485">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65051-486">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="65051-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65051-487">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65051-488">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65051-489">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65051-490">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="65051-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65051-491">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65051-492">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65051-493">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="65051-494">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="65051-495">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="65051-495">Service user account</span></span>

<span data-ttu-id="65051-496">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65051-497">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65051-498">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="65051-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65051-499">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65051-500">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65051-501">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65051-502">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65051-503">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65051-504">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="65051-504">Log on as a service rights</span></span>

<span data-ttu-id="65051-505">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="65051-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65051-506">*secpol.msc* 를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65051-507">**로컬 정책** 노드를 확장하고 **사용자 권한 할당** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65051-508">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65051-509">**사용자 또는 그룹 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65051-510">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65051-511">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65051-512">**고급** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-512">Select **Advanced**.</span></span> <span data-ttu-id="65051-513">**지금 찾기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-513">Select **Find Now**.</span></span> <span data-ttu-id="65051-514">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-514">Select the user account from the list.</span></span> <span data-ttu-id="65051-515">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-515">Select **OK**.</span></span> <span data-ttu-id="65051-516">다시 **확인** 을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65051-517">**확인** 또는 **적용** 을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65051-518">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="65051-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65051-519">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="65051-519">Create a service</span></span>

<span data-ttu-id="65051-520">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65051-521">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65051-522">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="65051-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65051-523">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65051-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65051-524">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65051-525">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="65051-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65051-526">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="65051-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65051-527">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="65051-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65051-528">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65051-529">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="65051-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65051-530">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="65051-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65051-531">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="65051-531">Start a service</span></span>

<span data-ttu-id="65051-532">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-533">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="65051-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65051-534">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="65051-534">Determine a service's status</span></span>

<span data-ttu-id="65051-535">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65051-536">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65051-537">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="65051-537">Stop a service</span></span>

<span data-ttu-id="65051-538">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65051-539">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="65051-539">Remove a service</span></span>

<span data-ttu-id="65051-540">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="65051-541">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="65051-541">Handle starting and stopping events</span></span>

<span data-ttu-id="65051-542"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="65051-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="65051-543">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="65051-544">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="65051-545">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="65051-546">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65051-547">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="65051-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65051-548">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65051-549">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65051-550">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="65051-550">Configure endpoints</span></span>

<span data-ttu-id="65051-551">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65051-552">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65051-553">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65051-554">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="65051-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65051-555">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65051-556">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65051-557">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="65051-557">Current directory and content root</span></span>

<span data-ttu-id="65051-558">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65051-559">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65051-560">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="65051-561">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="65051-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="65051-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="65051-563">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="65051-564">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65051-565">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="65051-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65051-566">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65051-567">문제 해결</span><span class="sxs-lookup"><span data-stu-id="65051-567">Troubleshoot</span></span>

<span data-ttu-id="65051-568">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65051-569">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="65051-569">Common errors</span></span>

* <span data-ttu-id="65051-570">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65051-571">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65051-572">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65051-573">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65051-574">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="65051-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65051-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="65051-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65051-576">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65051-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65051-577">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65051-578">Windows Service의 기본 경로는 *c:\\Windows\\System32* 입니다.</span><span class="sxs-lookup"><span data-stu-id="65051-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65051-579">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65051-580">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65051-581">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65051-582">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65051-583">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="65051-583">System and Application Event Logs</span></span>

<span data-ttu-id="65051-584">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="65051-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65051-585">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65051-586">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65051-587">**시스템** 을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65051-588">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="65051-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65051-589">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65051-590">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="65051-590">Run the app at a command prompt</span></span>

<span data-ttu-id="65051-591">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65051-592">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65051-593">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65051-594">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="65051-594">Clear package caches</span></span>

<span data-ttu-id="65051-595">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65051-596">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65051-597">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65051-598">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65051-599">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="65051-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65051-600">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65051-601">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65051-602">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65051-603">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65051-604">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="65051-604">Slow or hanging app</span></span>

<span data-ttu-id="65051-605">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65051-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65051-606">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="65051-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="65051-607">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65051-608">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65051-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65051-609">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65051-610">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65051-611">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="65051-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65051-612">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65051-613">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65051-614">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65051-615">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="65051-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65051-616">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="65051-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65051-617">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="65051-617">Analyze the dump</span></span>

<span data-ttu-id="65051-618">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65051-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65051-619">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65051-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65051-620">추가 자료</span><span class="sxs-lookup"><span data-stu-id="65051-620">Additional resources</span></span>

* <span data-ttu-id="65051-621">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="65051-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
