---
title: ASP.NET Core의 임시 데이터 보호 공급자
author: rick-anderson
description: ASP.NET Core 임시 데이터 보호 공급자의 구현 세부 정보에 대해 알아봅니다.
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052701"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="089eb-103">ASP.NET Core의 임시 데이터 보호 공급자</span><span class="sxs-lookup"><span data-stu-id="089eb-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="089eb-104">응용 프로그램에 throwaway 필요한 시나리오가 있습니다 `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="089eb-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="089eb-105">예를 들어, 개발자는 일회용 콘솔 응용 프로그램에서 실험 하거나 응용 프로그램 자체가 임시 (스크립팅된 또는 단위 테스트 프로젝트) 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="089eb-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="089eb-106">이러한 시나리오를 지원 하기 위해 [AspNetCore 보호](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 패키지에는 형식이 포함 되어 있습니다 `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="089eb-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="089eb-107">이 형식은 `IDataProtectionProvider` 키 리포지토리가 메모리 내에만 유지 되 고 백업 저장소에 기록 되지 않는의 기본 구현을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="089eb-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="089eb-108">각 인스턴스는 고유한 `EphemeralDataProtectionProvider` 마스터 키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="089eb-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="089eb-109">따라서에서 루 팅 된가 보호 된 페이로드를 생성 하는 경우 동일한 인스턴스를 기반으로 하는 `IDataProtector` `EphemeralDataProtectionProvider` 동등 `IDataProtector` (동일한 [용도](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) 체인)에 의해서만 해당 페이로드를 보호 하지 않을 수 있습니다 `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="089eb-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="089eb-110">다음 샘플에서는를 인스턴스화하고 `EphemeralDataProtectionProvider` 이를 사용 하 여 데이터를 보호 하 고 보호를 해제 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="089eb-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
