---
title: ASP.NET Core ASP.NET machineKey 바꾸기
author: rick-anderson
description: ASP.NET에서 machineKey를 대체 하 여 새롭고 안전한 데이터 보호 시스템을 사용할 수 있도록 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053061"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="28f75-103">ASP.NET Core ASP.NET machineKey 바꾸기</span><span class="sxs-lookup"><span data-stu-id="28f75-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="28f75-104">`<machineKey>`ASP.NET에서 요소를 구현할 [수](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="28f75-105">이를 통해 대부분의 호출은 ASP.NET 암호화 루틴을 새 데이터 보호 시스템을 비롯 한 대체 데이터 보호 메커니즘을 통해 라우팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="28f75-106">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="28f75-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="28f75-107">새 데이터 보호 시스템은 .NET 4.5.1 이상을 대상으로 하는 기존 ASP.NET 응용 프로그램에만 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="28f75-108">응용 프로그램에서 .NET 4.5이 하를 대상으로 하는 경우 설치가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="28f75-109">새 데이터 보호 시스템을 기존 ASP.NET 4.5.1 + 프로젝트에 설치 하려면 temWeb Microsoft.AspNetCore.DataProtection.Sys패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="28f75-110">그러면 [기본 구성](xref:security/data-protection/configuration/default-settings) 설정을 사용 하 여 데이터 보호 시스템이 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="28f75-111">패키지를 설치 하면 폼 인증, 뷰 상태, ASP.NET에 대 한 호출을 포함 하 여 [대부분의 암호화 작업](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)에 사용 하도록 지시 하는 줄을 *Web.config* 에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="28f75-112">다음과 같이 삽입 되는 읽기 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="28f75-113">`__VIEWSTATE`아래 예제와 같이 "CfDJ8"로 시작 하는와 같은 필드를 검사 하 여 새 데이터 보호 시스템이 활성 상태 인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="28f75-114">"CfDJ8"는 데이터 보호 시스템으로 보호 되는 페이로드를 식별 하는 매직 "09 F0 C9 F0" 헤더의 base64 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="28f75-115">패키지 구성</span><span class="sxs-lookup"><span data-stu-id="28f75-115">Package configuration</span></span>

<span data-ttu-id="28f75-116">데이터 보호 시스템은 기본 제로 설치 구성으로 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="28f75-117">그러나 기본적으로 키가 로컬 파일 시스템에 저장 되므로 팜에 배포 된 응용 프로그램에서는 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="28f75-118">이 문제를 해결 하려면 하위 클래스를 DataProtectionStartup 하 고 ConfigureServices 메서드를 재정의 하는 형식을 만들어 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="28f75-119">다음은 키가 유지 되는 위치와 미사용 데이터를 암호화 하는 방법을 모두 구성 하는 사용자 지정 데이터 보호 시작 유형의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="28f75-120">또한 자체 응용 프로그램 이름을 제공 하 여 기본 앱 격리 정책을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="28f75-121">`<machineKey applicationName="my-app" ... />`SetApplicationName을 명시적으로 호출 하는 대신를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="28f75-122">이것은 개발자가 응용 프로그램 이름을 설정 하기 위해 구성 하려는 경우 DataProtectionStartup 파생 형식을 강제로 만들도록 방지 하는 편리한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="28f75-123">이 사용자 지정 구성을 사용 하도록 설정 하려면 Web.config로 돌아가서 `<appSettings>` 패키지 설치가 구성 파일에 추가 된 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="28f75-124">이 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="28f75-125">빈 값을 방금 만든 DataProtectionStartup 파생 형식의 어셈블리 정규화 된 이름으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="28f75-126">응용 프로그램의 이름이 DataProtectionDemo 인 경우에는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="28f75-127">이제 응용 프로그램 내에서 새로 구성 된 데이터 보호 시스템을 사용할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28f75-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
