---
title: ASP.NET Core의 해시 암호
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 암호를 해시 하는 방법을 알아봅니다.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051865"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="28518-103">ASP.NET Core의 해시 암호</span><span class="sxs-lookup"><span data-stu-id="28518-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="28518-104">데이터 보호 코드 베이스에는 암호화 키 파생 함수를 포함 하는 *AspNetCore* 패키지가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28518-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="28518-105">이 패키지는 독립 실행형 구성 요소 이며 나머지 데이터 보호 시스템에 대 한 종속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="28518-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="28518-106">완전히 독립적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28518-106">It can be used completely independently.</span></span> <span data-ttu-id="28518-107">원본은 데이터 보호 코드 베이스와 함께 편의를 위해 존재 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="28518-108">패키지는 현재 `KeyDerivation.Pbkdf2` [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)을 사용 하 여 암호를 해시할 수 있는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="28518-109">이 API는 .NET Framework의 기존 [Rfc2898DeriveBytes 형식과](/dotnet/api/system.security.cryptography.rfc2898derivebytes)매우 유사 하지만 세 가지 중요 한 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28518-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="28518-110">`KeyDerivation.Pbkdf2`메서드는 여러 PRFs (현재 `HMACSHA1` , 및)를 사용 하도록 지원 `HMACSHA256` `HMACSHA512` 하지만 형식은를 `Rfc2898DeriveBytes` 지원 `HMACSHA1` 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="28518-111">`KeyDerivation.Pbkdf2`메서드는 현재 운영 체제를 검색 하 고 가장 최적화 된 루틴 구현을 선택 하려고 시도 하 여 특정 경우에 훨씬 더 나은 성능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="28518-112">Windows 8에서는의 처리량을 10 배 하는 것을 제공 `Rfc2898DeriveBytes` 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="28518-113">`KeyDerivation.Pbkdf2`메서드에는 호출자가 모든 매개 변수 (솔트, PRF 및 반복 횟수)를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="28518-114">`Rfc2898DeriveBytes`형식은 이러한에 대 한 기본값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28518-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="28518-115">[source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity `PasswordHasher` 실제 사용 사례는의 형식에 대 한 소스 코드를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="28518-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
