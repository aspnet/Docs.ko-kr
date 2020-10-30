---
title: ':::no-loc(SignalR)::: API 디자인 고려 사항'
author: anurse
description: :::no-loc(SignalR):::앱 버전 간 호환성을 위해 api를 설계 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059652"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="c6d93-103">:::no-loc(SignalR)::: API 디자인 고려 사항</span><span class="sxs-lookup"><span data-stu-id="c6d93-103">:::no-loc(SignalR)::: API design considerations</span></span>

<span data-ttu-id="c6d93-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="c6d93-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="c6d93-105">이 문서에서는 기반 Api를 빌드하기 위한 지침을 제공 :::no-loc(SignalR)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-105">This article provides guidance for building :::no-loc(SignalR):::-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="c6d93-106">사용자 지정 개체 매개 변수를 사용 하 여 이전 버전과의 호환성 보장</span><span class="sxs-lookup"><span data-stu-id="c6d93-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="c6d93-107">:::no-loc(SignalR):::클라이언트 또는 서버에서 허브 메서드에 매개 변수를 추가 하는 것은 *주요 변경 사항* 입니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-107">Adding parameters to a :::no-loc(SignalR)::: hub method (on either the client or the server) is a *breaking change* .</span></span> <span data-ttu-id="c6d93-108">즉, 이전 클라이언트/서버에서 적절 한 수의 매개 변수를 사용 하지 않고 메서드를 호출 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="c6d93-109">그러나 사용자 지정 개체 매개 변수에 속성을 추가 하는 것은 주요 변경 사항이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="c6d93-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="c6d93-110">이를 사용 하 여 클라이언트 또는 서버의 변경 내용에 대해 복원 력이 있는 호환 Api를 디자인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="c6d93-111">예를 들어 다음과 같은 서버 쪽 API를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="c6d93-112">JavaScript 클라이언트는 다음과 같이를 사용 하 여이 메서드를 호출 합니다 `invoke` .</span><span class="sxs-lookup"><span data-stu-id="c6d93-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="c6d93-113">나중에 서버 메서드에 두 번째 매개 변수를 추가 하는 경우 이전 클라이언트는이 매개 변수 값을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="c6d93-114">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="c6d93-115">이전 클라이언트에서이 메서드를 호출 하려고 하면 다음과 같은 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.:::no-loc(SignalR):::.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="c6d93-116">서버에 다음과 같은 로그 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="c6d93-117">이전 클라이언트는 매개 변수를 하나만 보냈지만 최신 서버 API에는 두 개의 매개 변수가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="c6d93-118">사용자 지정 개체를 매개 변수로 사용 하면 더 많은 유연성이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="c6d93-119">사용자 지정 개체를 사용 하도록 원래 API를 다시 디자인 해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="c6d93-120">이제 클라이언트는 개체를 사용 하 여 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="c6d93-121">매개 변수를 추가 하는 대신 개체에 속성을 추가 합니다 `TotalLengthRequest` .</span><span class="sxs-lookup"><span data-stu-id="c6d93-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="c6d93-122">이전 클라이언트에서 단일 매개 변수를 보낼 때 추가 `Param2` 속성은 그대로 유지 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="c6d93-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="c6d93-123">를 확인 하 `Param2` `null` 고 기본값을 적용 하 여 이전 클라이언트에서 보낸 메시지를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="c6d93-124">새 클라이언트는 두 매개 변수를 모두 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="c6d93-125">클라이언트에 정의 된 메서드에 대해 동일한 기술이 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="c6d93-126">서버 쪽에서 사용자 지정 개체를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="c6d93-127">클라이언트 쪽에서 `Message` 매개 변수를 사용 하는 대신 속성에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="c6d93-128">나중에 메시지 보낸 사람을 페이로드에 추가 하기로 결정 한 경우에는 개체에 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="c6d93-129">이전 클라이언트에는 값이 필요 하지 않으므로 `Sender` 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="c6d93-130">새 클라이언트는 새 속성을 읽도록 업데이트 하 여이를 수락할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="c6d93-131">이 경우 새 클라이언트도 값을 제공 하지 않는 이전 서버를 허용 합니다 `Sender` .</span><span class="sxs-lookup"><span data-stu-id="c6d93-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="c6d93-132">이전 서버는 값을 제공 하지 않으므로 `Sender` 클라이언트는 액세스 하기 전에 존재 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6d93-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
