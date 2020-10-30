---
title: ASP.NET Core 데이터 보호에 대한 비-DI 인식 시나리오
author: rick-anderson
description: 종속성 주입에서 제공 하는 서비스를 사용 하지 않거나 사용 하지 않으려는 데이터 보호 시나리오를 지 원하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052827"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a><span data-ttu-id="ccc70-103">ASP.NET Core 데이터 보호에 대한 비-DI 인식 시나리오</span><span class="sxs-lookup"><span data-stu-id="ccc70-103">Non-DI aware scenarios for Data Protection in ASP.NET Core</span></span>

<span data-ttu-id="ccc70-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ccc70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ccc70-105">ASP.NET Core 데이터 보호 시스템은 일반적으로 [서비스 컨테이너에 추가](xref:security/data-protection/consumer-apis/overview) 되 고, DI (종속성 주입)를 통해 종속 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-105">The ASP.NET Core Data Protection system is normally [added to a service container](xref:security/data-protection/consumer-apis/overview) and consumed by dependent components via dependency injection (DI).</span></span> <span data-ttu-id="ccc70-106">그러나 특히 시스템을 기존 응용 프로그램으로 가져오는 경우에는이 방법이 적합 하지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-106">However, there are cases where this isn't feasible or desired, especially when importing the system into an existing app.</span></span>

<span data-ttu-id="ccc70-107">이러한 시나리오를 지원 하기 위해 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지는 DI를 사용 하지 않고 데이터 보호를 사용 하는 간단한 방법을 제공 하는 구체적 유형인 [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-107">To support these scenarios, the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) package provides a concrete type, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), which offers a simple way to use Data Protection without relying on DI.</span></span> <span data-ttu-id="ccc70-108">`DataProtectionProvider`형식은 [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-108">The `DataProtectionProvider` type implements [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider).</span></span> <span data-ttu-id="ccc70-109">`DataProtectionProvider`을 생성 하려면 다음 코드 샘플에 표시 된 것 처럼 공급자의 암호화 키를 저장 해야 하는 위치를 나타내는 [system.io.directoryinfo](/dotnet/api/system.io.directoryinfo) 인스턴스를 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-109">Constructing `DataProtectionProvider` only requires providing a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance to indicate where the provider's cryptographic keys should be stored, as seen in the following code sample:</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

<span data-ttu-id="ccc70-110">기본적으로 `DataProtectionProvider` 구체적 형식은 원시 키 자료를 파일 시스템에 저장 하기 전에 암호화 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-110">By default, the `DataProtectionProvider` concrete type doesn't encrypt raw key material before persisting it to the file system.</span></span> <span data-ttu-id="ccc70-111">이는 개발자가 네트워크 공유를 가리키고 데이터 보호 시스템이 적절 한 rest 키 암호화 메커니즘을 자동으로 추론할 수 없는 시나리오를 지원 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-111">This is to support scenarios where the developer points to a network share and the Data Protection system can't automatically deduce an appropriate at-rest key encryption mechanism.</span></span>

<span data-ttu-id="ccc70-112">또한 `DataProtectionProvider` 구체적인 형식은 기본적으로 [앱을 격리](xref:security/data-protection/configuration/overview#per-application-isolation) 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-112">Additionally, the `DataProtectionProvider` concrete type doesn't [isolate apps](xref:security/data-protection/configuration/overview#per-application-isolation) by default.</span></span> <span data-ttu-id="ccc70-113">동일한 키 디렉터리를 사용 하는 모든 앱은 [용도 매개 변수가](xref:security/data-protection/consumer-apis/purpose-strings) 일치 하는 한 페이로드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-113">All apps using the same key directory can share payloads as long as their [purpose parameters](xref:security/data-protection/consumer-apis/purpose-strings) match.</span></span>

<span data-ttu-id="ccc70-114">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) 생성자는 시스템의 동작을 조정 하는 데 사용할 수 있는 선택적 구성 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-114">The [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) constructor accepts an optional configuration callback that can be used to adjust the behaviors of the system.</span></span> <span data-ttu-id="ccc70-115">아래 샘플에서는 [Setapplicationname](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)에 대 한 명시적 호출을 사용 하 여 격리를 복원 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-115">The sample below demonstrates restoring isolation with an explicit call to [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname).</span></span> <span data-ttu-id="ccc70-116">이 샘플에서는 Windows DPAPI를 사용 하 여 지속형 키를 자동으로 암호화 하도록 시스템을 구성 하는 방법도 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-116">The sample also demonstrates configuring the system to automatically encrypt persisted keys using Windows DPAPI.</span></span> <span data-ttu-id="ccc70-117">디렉터리가 UNC 공유를 가리키는 경우에는 모든 관련 컴퓨터에 공유 인증서를 배포 하 고 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)에 대 한 호출을 사용 하 여 인증서 기반 암호화를 사용 하도록 시스템을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-117">If the directory points to a UNC share, you may wish to distribute a shared certificate across all relevant machines and to configure the system to use certificate-based encryption with a call to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> <span data-ttu-id="ccc70-118">`DataProtectionProvider`구체적 형식의 인스턴스는 만드는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-118">Instances of the `DataProtectionProvider` concrete type are expensive to create.</span></span> <span data-ttu-id="ccc70-119">앱이이 유형의 여러 인스턴스를 유지 관리 하 고 모두 동일한 키 저장소 디렉터리를 사용 하는 경우 앱 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-119">If an app maintains multiple instances of this type and if they're all using the same key storage directory, app performance might degrade.</span></span> <span data-ttu-id="ccc70-120">형식을 사용 하는 경우 `DataProtectionProvider` 이 형식을 한 번 만들고 가능한 한 많이 다시 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-120">If you use the `DataProtectionProvider` type, we recommend that you create this type once and reuse it as much as possible.</span></span> <span data-ttu-id="ccc70-121">`DataProtectionProvider`형식 및이 형식에서 만들어진 모든 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 인스턴스는 여러 호출자에 대해 스레드로부터 안전 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccc70-121">The `DataProtectionProvider` type and all [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instances created from it are thread-safe for multiple callers.</span></span>
