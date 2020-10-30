---
title: ASP.NET Core의 키 불변성 및 키 설정
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 불변성 Api의 구현 세부 정보에 대해 알아봅니다.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061251"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="fe994-103">ASP.NET Core의 키 불변성 및 키 설정</span><span class="sxs-lookup"><span data-stu-id="fe994-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="fe994-104">개체가 백업 저장소에 유지 되 면 해당 표현은 영구적으로 수정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="fe994-105">새 데이터는 백업 저장소에 추가할 수 있지만 기존 데이터는 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="fe994-106">이 동작의 주요 목적은 데이터 손상을 방지 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="fe994-107">이 동작의 한 가지 결과는 키를 백업 저장소에 기록 하 고 나면 변경할 수 없다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="fe994-108">를 사용 하 여 취소할 수 있지만 생성, 활성화 및 만료 날짜를 변경할 수는 없습니다 `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="fe994-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="fe994-109">또한 기본 알고리즘 정보, 마스터 키 자료 및 휴지 상태의 암호화 속성도 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="fe994-110">개발자가 키 지 속성에 영향을 주는 설정을 변경 하는 경우 이러한 변경 내용은에 대 한 명시적 호출 `IKeyManager.CreateNewKey` 또는 데이터 보호 시스템의 자체 [자동 키 생성](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 동작을 통해 다음에 키를 생성할 때까지 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="fe994-111">키 지 속성에 영향을 주는 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="fe994-112">기본 키 수명</span><span class="sxs-lookup"><span data-stu-id="fe994-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="fe994-113">휴지 상태의 키 암호화 메커니즘</span><span class="sxs-lookup"><span data-stu-id="fe994-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="fe994-114">키에 포함 된 알고리즘 정보</span><span class="sxs-lookup"><span data-stu-id="fe994-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="fe994-115">이러한 설정이 다음 자동 키 롤링 시간 보다 먼저 시작 해야 하는 경우에 대 한 명시적 호출을 수행 하 여 `IKeyManager.CreateNewKey` 새 키를 강제로 만들도록 하십시오.</span><span class="sxs-lookup"><span data-stu-id="fe994-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="fe994-116">명시적인 활성화 날짜 ({now + 2 일})는 호출에서 변경 내용이 전파 되는 시간을 허용 하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="fe994-117">리포지토리에 접촉 하는 모든 응용 프로그램은 확장명 메서드를 사용 하 여 동일한 설정을 지정 해야 합니다 `IDataProtectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="fe994-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="fe994-118">그렇지 않으면 지속형 키의 속성이 키 생성 루틴을 호출한 특정 응용 프로그램에 종속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe994-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
