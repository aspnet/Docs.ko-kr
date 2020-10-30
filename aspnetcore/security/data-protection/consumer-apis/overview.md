---
title: ASP.NET Core에 대 한 소비자 Api 개요
author: rick-anderson
description: ASP.NET Core 데이터 보호 라이브러리에서 사용할 수 있는 다양 한 소비자 Api에 대 한 간략 한 개요를 수신 합니다.
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051878"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a><span data-ttu-id="5b8d2-103">ASP.NET Core에 대 한 소비자 Api 개요</span><span class="sxs-lookup"><span data-stu-id="5b8d2-103">Consumer APIs overview for ASP.NET Core</span></span>

<span data-ttu-id="5b8d2-104">`IDataProtectionProvider`및 `IDataProtector` 인터페이스는 소비자가 데이터 보호 시스템을 사용 하는 기본 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-104">The `IDataProtectionProvider` and `IDataProtector` interfaces are the basic interfaces through which consumers use the data protection system.</span></span> <span data-ttu-id="5b8d2-105">이러한 데이터는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-105">They're located in the [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.</span></span>

## <a name="idataprotectionprovider"></a><span data-ttu-id="5b8d2-106">IDataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="5b8d2-106">IDataProtectionProvider</span></span>

<span data-ttu-id="5b8d2-107">공급자 인터페이스는 데이터 보호 시스템의 루트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-107">The provider interface represents the root of the data protection system.</span></span> <span data-ttu-id="5b8d2-108">데이터를 보호 하거나 보호 해제 하는 데 직접 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-108">It cannot directly be used to protect or unprotect data.</span></span> <span data-ttu-id="5b8d2-109">대신 소비자는를 호출 하 여에 대 한 참조를 가져와야 합니다 `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` . 여기서 용도는 의도 된 소비자 사용 사례를 설명 하는 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-109">Instead, the consumer must get a reference to an `IDataProtector` by calling `IDataProtectionProvider.CreateProtector(purpose)`, where purpose is a string that describes the intended consumer use case.</span></span> <span data-ttu-id="5b8d2-110">이 매개 변수의 의도 및 적절 한 값을 선택 하는 방법에 대 한 자세한 내용은 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-110">See [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings) for much more information on the intent of this parameter and how to choose an appropriate value.</span></span>

## <a name="idataprotector"></a><span data-ttu-id="5b8d2-111">IDataProtector</span><span class="sxs-lookup"><span data-stu-id="5b8d2-111">IDataProtector</span></span>

<span data-ttu-id="5b8d2-112">보호기 인터페이스는를 호출 하 여 반환 `CreateProtector` 되며, 소비자는이 인터페이스를 사용 하 여 보호 및 보호 해제 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-112">The protector interface is returned by a call to `CreateProtector`, and it's this interface which consumers can use to perform protect and unprotect operations.</span></span>

<span data-ttu-id="5b8d2-113">데이터를 보호 하려면 메서드에 데이터를 전달 `Protect` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-113">To protect a piece of data, pass the data to the `Protect` method.</span></span> <span data-ttu-id="5b8d2-114">기본 인터페이스는 byte []-> byte []를 변환 하는 메서드를 정의 하지만 문자열 > 문자열을 변환 하는 오버 로드 (확장 메서드로 제공 됨)도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-114">The basic interface defines a method which converts byte[] -> byte[], but there's also an overload (provided as an extension method) which converts string -> string.</span></span> <span data-ttu-id="5b8d2-115">두 메서드에서 제공 하는 보안은 동일 합니다. 개발자는 사용 사례에 가장 편리한 오버 로드를 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-115">The security offered by the two methods is identical; the developer should choose whichever overload is most convenient for their use case.</span></span> <span data-ttu-id="5b8d2-116">선택한 오버 로드에 관계 없이 Protect 메서드에서 반환 된 값은 이제 보호 되 고 (암호화 된 읽거나 및 proofed) 응용 프로그램은 신뢰할 수 없는 클라이언트에 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-116">Irrespective of the overload chosen, the value returned by the Protect method is now protected (enciphered and tamper-proofed), and the application can send it to an untrusted client.</span></span>

<span data-ttu-id="5b8d2-117">이전에 보호 된 데이터 부분의 보호를 해제 하려면 보호 된 데이터를 메서드에 전달 합니다 `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="5b8d2-117">To unprotect a previously-protected piece of data, pass the protected data to the `Unprotect` method.</span></span> <span data-ttu-id="5b8d2-118">개발자 편의를 위해 바이트 [] 기반 및 문자열 기반 오버 로드가 있습니다. 이에 대 한 이전 호출에 의해 보호 된 페이로드가 생성 된 경우 `Protect` 이 `IDataProtector` `Unprotect` 메서드는 보호 되지 않는 원래 페이로드를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-118">(There are byte[]-based and string-based overloads for developer convenience.) If the protected payload was generated by an earlier call to `Protect` on this same `IDataProtector`, the `Unprotect` method will return the original unprotected payload.</span></span> <span data-ttu-id="5b8d2-119">보호 된 페이로드가 변조 되었거나 다른에 의해 생성 된 경우이 `IDataProtector` 메서드는 system.security.cryptography.cryptographicexception을 `Unprotect` throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-119">If the protected payload has been tampered with or was produced by a different `IDataProtector`, the `Unprotect` method will throw CryptographicException.</span></span>

<span data-ttu-id="5b8d2-120">동일한 개념과 다른의 개념은 `IDataProtector` 목적 개념에 다시 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-120">The concept of same vs. different `IDataProtector` ties back to the concept of purpose.</span></span> <span data-ttu-id="5b8d2-121">두 `IDataProtector` 인스턴스가 동일한 루트에서 생성 `IDataProtectionProvider` 되었지만에 대 한 호출에서 다른 용도의 문자열을 통해 생성 된 경우 `IDataProtectionProvider.CreateProtector` [다른 보호기](xref:security/data-protection/consumer-apis/purpose-strings)로 간주 되 고 다른 인스턴스가 생성 한 페이로드를 보호 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-121">If two `IDataProtector` instances were generated from the same root `IDataProtectionProvider` but via different purpose strings in the call to `IDataProtectionProvider.CreateProtector`, then they're considered [different protectors](xref:security/data-protection/consumer-apis/purpose-strings), and one won't be able to unprotect payloads generated by the other.</span></span>

## <a name="consuming-these-interfaces"></a><span data-ttu-id="5b8d2-122">이러한 인터페이스 사용</span><span class="sxs-lookup"><span data-stu-id="5b8d2-122">Consuming these interfaces</span></span>

<span data-ttu-id="5b8d2-123">DI 인식 구성 요소의 경우 구성 요소가 `IDataProtectionProvider` 생성자에서 매개 변수를 사용 하 고 구성 요소가 인스턴스화될 때 di 시스템이이 서비스를 자동으로 제공 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-123">For a DI-aware component, the intended usage is that the component takes an `IDataProtectionProvider` parameter in its constructor and that the DI system automatically provides this service when the component is instantiated.</span></span>

> [!NOTE]
> <span data-ttu-id="5b8d2-124">일부 응용 프로그램 (예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램)은 DI를 인식 하지 못할 수 있으므로 여기에 설명 된 메커니즘을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-124">Some applications (such as console applications or ASP.NET 4.x applications) might not be DI-aware so cannot use the mechanism described here.</span></span> <span data-ttu-id="5b8d2-125">이러한 시나리오에서는 DI를 거치지 않고 공급자의 인스턴스를 가져오는 방법에 대 한 자세한 내용은 [비 Di 인식 시나리오](xref:security/data-protection/configuration/non-di-scenarios) 문서를 참조 하세요 `IDataProtection` .</span><span class="sxs-lookup"><span data-stu-id="5b8d2-125">For these scenarios consult the [Non DI Aware Scenarios](xref:security/data-protection/configuration/non-di-scenarios) document for more information on getting an instance of an `IDataProtection` provider without going through DI.</span></span>

<span data-ttu-id="5b8d2-126">다음 샘플에서는 세 가지 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-126">The following sample demonstrates three concepts:</span></span>

1. <span data-ttu-id="5b8d2-127">서비스 컨테이너에 [데이터 보호 시스템 추가](xref:security/data-protection/configuration/overview)</span><span class="sxs-lookup"><span data-stu-id="5b8d2-127">[Add the data protection system](xref:security/data-protection/configuration/overview) to the service container,</span></span>

2. <span data-ttu-id="5b8d2-128">DI를 사용 하 여 인스턴스 받기 `IDataProtectionProvider` 및</span><span class="sxs-lookup"><span data-stu-id="5b8d2-128">Using DI to receive an instance of an `IDataProtectionProvider`, and</span></span>

3. <span data-ttu-id="5b8d2-129">에서을 만들고 `IDataProtector` `IDataProtectionProvider` 이를 사용 하 여 데이터 보호 및 보호 해제</span><span class="sxs-lookup"><span data-stu-id="5b8d2-129">Creating an `IDataProtector` from an `IDataProtectionProvider` and using it to protect and unprotect data.</span></span>

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="5b8d2-130">AspNetCore 패키지에는 개발자 편의를 위한 확장 메서드가 포함 되어 있습니다. `IServiceProvider.GetDataProtector`</span><span class="sxs-lookup"><span data-stu-id="5b8d2-130">The package Microsoft.AspNetCore.DataProtection.Abstractions contains an extension method `IServiceProvider.GetDataProtector` as a developer convenience.</span></span> <span data-ttu-id="5b8d2-131">서비스 공급자에서를 검색 하 고를 호출 하는 단일 작업으로 캡슐화 `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-131">It encapsulates as a single operation both retrieving an `IDataProtectionProvider` from the service provider and calling `IDataProtectionProvider.CreateProtector`.</span></span> <span data-ttu-id="5b8d2-132">다음 샘플에서는 사용 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-132">The following sample demonstrates its usage.</span></span>

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> <span data-ttu-id="5b8d2-133">및 인스턴스 `IDataProtectionProvider` `IDataProtector` 는 여러 호출자가 스레드로부터 안전 하 게 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-133">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="5b8d2-134">구성 요소가 호출을 통해에 대 한 참조를 가져온 후에 `IDataProtector` `CreateProtector` 는 및에 대 한 여러 호출에 해당 참조를 사용 합니다 `Protect` `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="5b8d2-134">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span> <span data-ttu-id="5b8d2-135">`Unprotect`보호 된 페이로드를 확인 하거나 해독할 수 없는 경우에 대 한 호출은 system.security.cryptography.cryptographicexception을 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-135">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="5b8d2-136">일부 구성 요소는 보호 되지 않는 작업 중에 오류를 무시 하려고 할 수 있습니다. 인증을 읽는 구성 요소가 :::no-loc(cookie)::: 이 오류를 처리 하 고 요청을 완전히 실패 하는 것이 아닌 모든 요청을 처리 하는 것 처럼 처리할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5b8d2-136">Some components may wish to ignore errors during unprotect operations; a component which reads authentication :::no-loc(cookie):::s might handle this error and treat the request as if it had no :::no-loc(cookie)::: at all rather than fail the request outright.</span></span> <span data-ttu-id="5b8d2-137">이 동작을 원하는 구성 요소는 모든 예외를 swallowing 하는 대신 System.security.cryptography.cryptographicexception를 명확 하 게 catch 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b8d2-137">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
