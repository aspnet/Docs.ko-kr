---
title: ASP.NET Core 보호 된 페이로드의 수명 제한
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 보호 된 페이로드의 수명을 제한 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060796"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="46c36-103">ASP.NET Core 보호 된 페이로드의 수명 제한</span><span class="sxs-lookup"><span data-stu-id="46c36-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="46c36-104">응용 프로그램 개발자가 설정 된 시간 후에 만료 되는 보호 된 페이로드를 만들려고 하는 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="46c36-105">예를 들어 보호 된 페이로드는 1 시간 동안만 유효 해야 하는 암호 다시 설정 토큰을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="46c36-106">개발자는 포함 된 만료 날짜가 포함 된 자체 페이로드 형식을 만들 수 있으며, 고급 개발자는이 작업을 수행할 수 있지만 이러한 만료를 관리 하는 대부분의 개발자는 지루한 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="46c36-107">개발자 대상에 게 더 쉽게 사용할 수 있도록 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지에는 설정 된 시간 후 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 api가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="46c36-108">이러한 Api는 해당 유형의 작동이 중단 `ITimeLimitedDataProtector` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="46c36-109">API 사용</span><span class="sxs-lookup"><span data-stu-id="46c36-109">API usage</span></span>

<span data-ttu-id="46c36-110">`ITimeLimitedDataProtector`인터페이스는 시간 제한/자체 만료 페이로드를 보호 하 고 보호 해제 하는 핵심 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="46c36-111">인스턴스 `ITimeLimitedDataProtector` 를 만들려면 먼저 특정 용도를 사용 하 여 생성 된 일반 [IDataProtector](xref:security/data-protection/consumer-apis/overview) 인스턴스가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="46c36-112">인스턴스를 `IDataProtector` 사용할 수 있게 되 면 `IDataProtector.ToTimeLimitedDataProtector` 확장 메서드를 호출 하 여 기본 제공 만료 기능이 포함 된 보호기를 다시 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="46c36-113">`ITimeLimitedDataProtector` 는 다음과 같은 API 노출 및 확장 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="46c36-114">CreateProtector (문자열 용도): ITimeLimitedDataProtector-이 API는 `IDataProtectionProvider.CreateProtector` 루트 시간 제한 보호기에서 [용도 체인](xref:security/data-protection/consumer-apis/purpose-strings) 을 만드는 데 사용할 수 있다는 점에서 기존와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="46c36-115">Protect (byte [] plaintext, DateTimeOffset 만료): byte []</span><span class="sxs-lookup"><span data-stu-id="46c36-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="46c36-116">Protect (byte [] plaintext, TimeSpan 수명): byte []</span><span class="sxs-lookup"><span data-stu-id="46c36-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="46c36-117">Protect (byte [] plaintext): byte []</span><span class="sxs-lookup"><span data-stu-id="46c36-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="46c36-118">보호 (문자열 일반 텍스트, DateTimeOffset 만료): 문자열</span><span class="sxs-lookup"><span data-stu-id="46c36-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="46c36-119">보호 (문자열 일반 텍스트, TimeSpan 수명): 문자열</span><span class="sxs-lookup"><span data-stu-id="46c36-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="46c36-120">보호 (일반 텍스트 문자열): 문자열</span><span class="sxs-lookup"><span data-stu-id="46c36-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="46c36-121">일반 텍스트를 사용 하는 핵심 방법 외에 `Protect` 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="46c36-122">만료 날짜는 절대 날짜 (를 통해 `DateTimeOffset` ) 또는 상대 시간 (를 통해 현재 시스템 시간에서)으로 지정할 수 있습니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="46c36-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="46c36-123">만료 되지 않는 오버 로드를 호출 하면 페이로드가 만료 되지 않는다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="46c36-124">보호 해제 (byte [] protectedData, out DateTimeOffset 만료): byte []</span><span class="sxs-lookup"><span data-stu-id="46c36-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="46c36-125">보호 해제 (byte [] protectedData): byte []</span><span class="sxs-lookup"><span data-stu-id="46c36-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="46c36-126">보호 해제 (string protectedData, out DateTimeOffset 만료): 문자열</span><span class="sxs-lookup"><span data-stu-id="46c36-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="46c36-127">보호 해제 (문자열 protectedData): 문자열</span><span class="sxs-lookup"><span data-stu-id="46c36-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="46c36-128">`Unprotect`메서드는 보호 되지 않는 원래 데이터를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="46c36-129">페이로드가 아직 만료 되지 않은 경우 절대 만료는 원래의 보호 되지 않는 데이터와 함께 선택적 out 매개 변수로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="46c36-130">페이로드가 만료 된 경우 보호 해제 메서드의 모든 오버 로드가 System.security.cryptography.cryptographicexception을 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="46c36-131">이러한 Api를 사용 하 여 장기 또는 무한 지 속성을 필요로 하는 페이로드를 보호 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="46c36-132">"보호 된 페이로드가 한 달 후 영구적으로 복구할 수 있습니다."</span><span class="sxs-lookup"><span data-stu-id="46c36-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="46c36-133">좋은 방법으로 사용할 수 있습니다. 대답이 아니요 인 경우 개발자는 대체 Api를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="46c36-134">아래 샘플에서는 데이터 보호 시스템을 인스턴스화하기 위해 [DI 이외의 코드 경로](xref:security/data-protection/configuration/non-di-scenarios) 를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="46c36-135">이 샘플을 실행 하려면 먼저 AspNetCore 패키지에 대 한 참조를 추가 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="46c36-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
