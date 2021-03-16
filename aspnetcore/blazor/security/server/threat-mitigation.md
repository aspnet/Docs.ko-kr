---
title: ASP.NET Core Blazor Server를 위한 위협 완화 지침
author: guardrex
description: Blazor Server 앱의 보안 위협을 완화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 2a5add79d60ddcbb1afdb9489fa16da692a7d6bd
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109665"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a><span data-ttu-id="fc284-103">ASP.NET Core Blazor Server를 위한 위협 완화 지침</span><span class="sxs-lookup"><span data-stu-id="fc284-103">Threat mitigation guidance for ASP.NET Core Blazor Server</span></span>

<span data-ttu-id="fc284-104">Blazor Server 앱은 ‘상태 저장’ 데이터 처리 모델을 사용합니다. 이 모델에서는 서버와 클라이언트가 장기적으로 지속되는 관계를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-104">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="fc284-105">영구 상태는 [회로](xref:blazor/state-management)에 의해 유지 관리되는데, 회로는 역시 장기적으로 지속되는 여러 연결을 포괄할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-105">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="fc284-106">사용자가 Blazor Server 사이트를 방문하면 서버는 서버의 메모리에 회로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-106">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="fc284-107">회로는 어떤 콘텐츠를 렌더링해야 하는지를 브라우저에 알려 주고 사용자가 UI에서 단추를 선택한 경우와 같은 이벤트에 대응합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-107">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="fc284-108">이러한 동작을 수행하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출하고 서버에서 .NET 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-108">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="fc284-109">이와 같은 양방향 JavaScript 기반 상호 작용을 [JS interop(JavaScript interop)](xref:blazor/call-javascript-from-dotnet)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-109">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="fc284-110">JS interop은 인터넷을 통해 실행되고 클라이언트가 원격 브라우저를 사용하기 때문에 Blazor Server 앱은 대부분의 웹앱 보안 문제를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-110">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="fc284-111">이 항목에서는 Blazor Server 앱에 가해지는 일반적인 위협을 살펴보고 인터넷에 연결된 앱을 위한 위협 완화 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-111">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="fc284-112">회사 내부 네트워크와 인트라넷과 같은 제한된 환경에서는 이러한 완화 지침이</span><span class="sxs-lookup"><span data-stu-id="fc284-112">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="fc284-113">제한된 환경에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-113">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="fc284-114">제한된 환경의 보안 위험이 낮기 때문에 구현 비용을 투입할 가치가 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-114">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-and-shared-state"></a><span data-ttu-id="fc284-115">Blazor 및 공유 상태</span><span class="sxs-lookup"><span data-stu-id="fc284-115">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a><span data-ttu-id="fc284-116">리소스 소모</span><span class="sxs-lookup"><span data-stu-id="fc284-116">Resource exhaustion</span></span>

<span data-ttu-id="fc284-117">리소스 소모는 클라이언트가 서버와 상호 작용하는 과정에서 서버가 지나치게 많은 리소스를 사용하도록 만드는 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-117">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="fc284-118">과도한 리소스 사용은 주로 다음에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-118">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="fc284-119">CPU</span><span class="sxs-lookup"><span data-stu-id="fc284-119">CPU</span></span>](#cpu)
* [<span data-ttu-id="fc284-120">메모리</span><span class="sxs-lookup"><span data-stu-id="fc284-120">Memory</span></span>](#memory)
* [<span data-ttu-id="fc284-121">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="fc284-121">Client connections</span></span>](#client-connections)

<span data-ttu-id="fc284-122">DoS(서비스 거부) 공격은 일반적으로 앱 또는 서버 리소스의 소모를 유발합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-122">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="fc284-123">그러나 리소스 소모가 항상 시스템에 대한 공격의 결과로 발생하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-123">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="fc284-124">예를 들어 사용자 수요가 높을 경우 유한한 리소스가 소모될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-124">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="fc284-125">DoS는 [DoS(서비스 거부) 공격](#denial-of-service-dos-attacks) 섹션에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-125">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="fc284-126">데이터베이스나 (파일 읽기 및 쓰기에 사용되는) 파일 핸들과 같은 Blazor 프레임워크 외부 리소스에서도 리소스 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-126">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="fc284-127">자세한 내용은 <xref:performance/performance-best-practices>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-127">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="fc284-128">CPU</span><span class="sxs-lookup"><span data-stu-id="fc284-128">CPU</span></span>

<span data-ttu-id="fc284-129">하나 이상의 클라이언트에서 서버가 CPU 작업을 많이 수행하도록 강제하는 경우 CPU 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-129">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="fc284-130">예를 들어 ‘피보나치 수’를 계산하는 Blazor Server 앱이 있다고 가정하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-130">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="fc284-131">피보나치 수는 피보나치 수열에서 생성됩니다. 피보나치 수열에서 각 숫자는 앞에 나온 두 개의 숫자의 합입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-131">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="fc284-132">정답을 도출하는 데 필요한 작업량은 수열의 길이와 초기 값의 크기에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-132">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="fc284-133">앱이 클라이언트의 요청에 제한을 두지 않으면 CPU를 많이 사용하는 계산이 CPU의 시간을 차지하여 다른 작업의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-133">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="fc284-134">과도한 리소스 사용은 가용성에 영향을 주는 보안 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-134">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="fc284-135">CPU 소모는 모든 공용 앱의 중요한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-135">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="fc284-136">일반적인 웹앱에서는 보호 기능으로 요청 및 연결의 시간이 초과되지만 Blazor Server 앱에는 이와 같은 보호 기능이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-136">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="fc284-137">Blazor Server 앱의 경우 CPU 사용량이 많은 작업을 수행하기 전에 반드시 적절한 검사와 제한을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-137">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="fc284-138">메모리</span><span class="sxs-lookup"><span data-stu-id="fc284-138">Memory</span></span>

<span data-ttu-id="fc284-139">하나 이상의 클라이언트에서 서버가 많은 양의 메모리를 사용하도록 강제하는 경우 메모리 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-139">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="fc284-140">예를 들어 항목 목록을 받아서 표시하는 구성 요소가 포함된 Blazor서버 쪽 앱이 있다고 가정하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-140">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="fc284-141">Blazor 앱이 허용되는 항목 수 또는 클라이언트로 다시 렌더링되는 항목 수에 제한을 설정하지 않을 경우 메모리를 많이 사용하는 처리 및 렌더링이 서버의 메모리를 차지하여 서버의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-141">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="fc284-142">서버는 크래시가 발생한 것처럼 보일 정도로 느려지거나 실제로 크래시가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-142">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="fc284-143">서버의 잠재적 메모리 소모 시나리오와 관련하여 항목 목록을 유지 관리하고 표시하는 다음 시나리오를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-143">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="fc284-144">`List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-144">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="fc284-145">앱에서 항목 목록이 무제한으로 늘어나도록 허용할 경우 서버의 메모리가 부족해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-145">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="fc284-146">메모리가 부족해지면 현재 세션이 종료되고(크래시) 해당 서버 인스턴스의 모든 동시 세션에서 메모리 부족 예외가 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-146">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="fc284-147">이 시나리오가 발생하지 않도록 하려면 앱에서 동시 사용자에게 항목 제한을 적용하는 데이터 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-147">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="fc284-148">렌더링에 페이지 매기기 스키마가 사용되지 않는 경우 서버는 UI에 표시되지 않는 개체를 위해 추가 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-148">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="fc284-149">항목 수에 제한이 없는 경우 메모리 수요로 인해 사용 가능한 서버 메모리가 소모될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-149">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="fc284-150">이 시나리오를 방지하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-150">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="fc284-151">렌더링 시에 페이지가 매겨진 목록을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-151">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="fc284-152">처음 100개에서 1,000개의 항목만 표시하고, 표시된 항목보다 더 많은 항목을 찾으려면 사용자가 검색 기준을 입력하도록 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-152">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="fc284-153">고급 렌더링 시나리오의 경우 ‘가상화’를 지원하는 목록 또는 그리드를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-153">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="fc284-154">가상화를 사용하면 목록이 현재 사용자에게 표시되는 항목의 하위 집합만 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-154">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="fc284-155">사용자가 UI의 스크롤 막대와 상호 작용하면 구성 요소는 표시하는 데 필요한 항목만 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-155">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="fc284-156">현재 표시하는 데 필요하지 않은 항목은 보조 스토리지에 보관될 수 있습니다. 이렇게 보조 스토리지에 보관하는 것이 이상적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-156">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="fc284-157">표시되지 않은 항목을 메모리에 보관할 수도 있는데, 이것은 덜 이상적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-157">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="fc284-158">Blazor Server 앱은 WPF, Windows Forms, Blazor WebAssembly와 같은 상태 저장의 다른 UI 프레임워크에서도 비슷한 프로그래밍 모델을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-158">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="fc284-159">주된 차이점은 이러한 몇몇 UI 프레임워크에서 앱에 의해 사용되는 메모리는 클라이언트에 속하기 때문에 해당 개별 클라이언트에만 영향을 준다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-159">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="fc284-160">예를 들어 Blazor WebAssembly 앱은 전적으로 클라이언트에서 실행되며 클라이언트 메모리 리소스만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-160">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="fc284-161">Blazor Server 시나리오에서 앱이 사용하는 메모리는 서버에 속하며 서버 인스턴스에 있는 여러 클라이언트 간에 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-161">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="fc284-162">서버 쪽 메모리 수요는 모든 Blazor Server 앱의 고려 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-162">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="fc284-163">그러나 대부분의 웹앱은 상태 비저장이며, 요청을 처리하는 동안 사용되는 메모리는 응답이 반환될 때 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-163">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="fc284-164">일반적으로, 클라이언트 연결이 끊겨도 유지되는 다른 서버 쪽 앱에서와 같이 클라이언트가 무제한으로 늘어나는 메모리를 할당할 수 있도록 허용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-164">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="fc284-165">Blazor Server 앱이 사용하는 메모리는 단일 요청보다 오랜 시간 동안 지속됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-165">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="fc284-166">개발 단계에서 프로파일러를 사용하거나 추적을 캡처하여 클라이언트의 메모리 수요를 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-166">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="fc284-167">프로파일러나 추적은 특정 클라이언트에 할당된 메모리를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-167">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="fc284-168">개발 단계에서 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처한 다음 해당 사용자의 회로에서 루트된 모든 개체의 메모리 수요를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-168">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="fc284-169">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="fc284-169">Client connections</span></span>

<span data-ttu-id="fc284-170">하나 이상의 클라이언트에서 서버에 대한 동시 연결을 너무 많이 열어서 다른 클라이언트가 새 연결을 설정하는 것을 차단하는 경우 연결 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-170">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="fc284-171">Blazor 클라이언트는 세션당 단일 연결을 설정하고 브라우저 창이 열려 있는 한 연결을 계속 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-171">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="fc284-172">서버에 가해지는 모든 연결에 대한 유지 관리 수요는 Blazor 앱에 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-172">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="fc284-173">연결의 지속적인 특성과 Blazor Server 앱의 상태 저장 특성을 고려하면 연결 소모는 앱 가용성에 대한 보다 큰 위험이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-173">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="fc284-174">기본적으로 Blazor Server 앱에 대한 사용자당 연결 수에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-174">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="fc284-175">앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-175">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="fc284-176">인증을 요구합니다. 이렇게 하면 인증되지 않은 사용자가 자연적으로 앱에 연결할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-176">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="fc284-177">이 시나리오가 유효해지려면 사용자가 마음대로 새 사용자를 프로비저닝하는 것을 차단해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-177">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="fc284-178">사용자당 연결 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-178">Limit the number of connections per user.</span></span> <span data-ttu-id="fc284-179">다음 방법을 통해 연결을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-179">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="fc284-180">정상 사용자가 앱에 액세스할 수 있도록 주의를 기울이세요(예: 클라이언트의 IP 주소를 기준으로 연결 제한이 설정된 경우).</span><span class="sxs-lookup"><span data-stu-id="fc284-180">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="fc284-181">앱 수준에서:</span><span class="sxs-lookup"><span data-stu-id="fc284-181">At the app level:</span></span>
    * <span data-ttu-id="fc284-182">엔드포인트 라우팅 확장성.</span><span class="sxs-lookup"><span data-stu-id="fc284-182">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="fc284-183">앱에 연결할 때 인증을 요구하고, 사용자당 활성 세션을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-183">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="fc284-184">제한에 도달하면 새 세션을 거부합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-184">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="fc284-185">프록시를 사용한 앱에 대한 프록시 WebSocket 연결(예: 클라이언트에서 앱으로의 연결을 다중 송신하는 [Azure SignalR 서비스](/azure/azure-signalr/signalr-overview)).</span><span class="sxs-lookup"><span data-stu-id="fc284-185">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="fc284-186">이렇게 하면 앱에 단일 클라이언트에서 설정할 수 있는 것보다 더 많은 연결 용량이 제공되어 하나의 클라이언트가 서버에 대한 연결을 소모하는 일을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-186">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="fc284-187">서버 수준에서: 앱 앞에서 프록시/게이트웨이를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-187">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="fc284-188">예를 들어 [Azure Front Door](/azure/frontdoor/front-door-overview)는 앱으로 가는 웹 트래픽의 전역 라우팅을 정의, 관리, 모니터링할 수 있고, Blazor Server 앱이 Long Polling을 사용하도록 구성된 경우 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-188">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app and works when Blazor Server apps are configured to use Long Polling.</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="fc284-189">Long Polling은 Blazor Server 앱에서 지원되지만 [WebSocket이 권장 전송 프로토콜](xref:blazor/host-and-deploy/server#azure-signalr-service)입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-189">Although Long Polling is supported for Blazor Server apps, [WebSockets is the recommended transport protocol](xref:blazor/host-and-deploy/server#azure-signalr-service).</span></span> <span data-ttu-id="fc284-190">[Azure Front Door](/azure/frontdoor/front-door-overview)는 현재 WebSocket을 지원하지 않지만 향후 서비스 릴리스에서 WebSocket을 지원하는 것을 검토 중입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-190">[Azure Front Door](/azure/frontdoor/front-door-overview) doesn't support WebSockets at this time, but support for WebSockets is under consideration for a future release of the service.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="fc284-191">DoS(서비스 거부) 공격</span><span class="sxs-lookup"><span data-stu-id="fc284-191">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="fc284-192">DoS(서비스 거부) 공격에서는 클라이언트가 서버로 하여금 하나 이상의 리소스를 소모하도록 하여 앱을 사용할 수 없도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-192">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="fc284-193">Blazor Server 앱에는 기본 제한이 있으며 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>에 설정된 다른 ASP.NET Core 및 SignalR 제한을 사용하여 DoS 공격으로부터 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-193">Blazor Server apps include default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks that are set on <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>:</span></span>

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>

<span data-ttu-id="fc284-194">자세한 내용 및 구성 코딩 예제는 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-194">For more information and configuration coding examples, see the following articles:</span></span>

* <xref:blazor/fundamentals/signalr>
* <xref:signalr/configuration>

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="fc284-195">브라우저와의 상호 작용(클라이언트)</span><span class="sxs-lookup"><span data-stu-id="fc284-195">Interactions with the browser (client)</span></span>

<span data-ttu-id="fc284-196">클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-196">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="fc284-197">JS interop 통신은 JavaScript와 .NET 사이에서 양방향으로 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-197">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="fc284-198">브라우저 이벤트는 비동기 방식으로 클라이언트에서 서버로 디스패치됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-198">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="fc284-199">서버는 비동기적으로 응답하며 필요에 따라 UI를 다시 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-199">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="fc284-200">.NET에서 호출되는 JavaScript 함수</span><span class="sxs-lookup"><span data-stu-id="fc284-200">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="fc284-201">.NET 메서드에서 JavaScript로의 호출:</span><span class="sxs-lookup"><span data-stu-id="fc284-201">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="fc284-202">모든 호출에는 구성 가능한 시간 제한이 있습니다. 이 시간이 경과하면 장애가 발생하고 호출자에게 <xref:System.OperationCanceledException>이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-202">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="fc284-203">호출의 기본 시간 제한(<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>)은 1분입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-203">There's a default timeout for the calls (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) of one minute.</span></span> <span data-ttu-id="fc284-204">이 제한을 구성하려면 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>의 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-204">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="fc284-205">취소 토큰을 제공하여 개별 호출별로 취소를 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-205">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="fc284-206">가능한 경우 기본 호출 시간 제한을 사용하고, 취소 토큰을 제공한 경우에는 클라이언트에 대한 호출의 시간을 제한하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-206">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="fc284-207">JavaScript 호출 결과는 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-207">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="fc284-208">브라우저에서 실행되는 Blazor 앱 클라이언트는 호출할 JavaScript 함수를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-208">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="fc284-209">함수가 호출되면 결과 또는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-209">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="fc284-210">악의적인 클라이언트가 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-210">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="fc284-211">JavaScript 함수에서 오류를 반환하여 앱에서 문제를 발생시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-211">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="fc284-212">JavaScript 함수에서 예기치 않은 결과를 반환하여 서버에서 의도치 않은 동작을 유도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-212">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="fc284-213">위와 같은 시나리오를 방지하려면 다음과 같은 예방 조치를 수행하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-213">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="fc284-214">호출 중에 발생할 수 있는 오류를 고려하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문 내에 JS interop 호출을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-214">Wrap JS interop calls within [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="fc284-215">자세한 내용은 <xref:blazor/fundamentals/handle-errors?pivots=server#javascript-interop-server>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-215">For more information, see <xref:blazor/fundamentals/handle-errors?pivots=server#javascript-interop-server>.</span></span>
* <span data-ttu-id="fc284-216">다른 동작을 수행하기 전에 JS interop 호출에서 반환된 데이터(오류 메시지 포함)의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-216">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="fc284-217">브라우저에서 호출된 .NET 메서드</span><span class="sxs-lookup"><span data-stu-id="fc284-217">.NET methods invoked from the browser</span></span>

<span data-ttu-id="fc284-218">JavaScript에서 .NET 메서드로의 호출은 신뢰하지 마시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-218">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="fc284-219">.NET 메서드가 JavaScript에 노출된 경우 .NET 메서드가 어떻게 호출되었는지 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-219">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="fc284-220">JavaScript에 노출된 모든 .NET 메서드는 앱에 대한 공용 엔드포인트를 취급할 때와 마찬가지로 취급합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-220">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="fc284-221">입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-221">Validate input.</span></span>
    * <span data-ttu-id="fc284-222">값이 예상 범위 내에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-222">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="fc284-223">사용자에게 요청된 작업을 수행할 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-223">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="fc284-224">.NET 메서드 호출의 일부로 과도한 리소스를 할당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-224">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="fc284-225">예를 들어 CPU 및 메모리 사용에 대한 검사를 수행하고 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-225">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="fc284-226">정적 메서드와 인스턴스 메서드가 JavaScript 클라이언트에 노출될 수 있음을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-226">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="fc284-227">적절한 제약 조건을 사용하여 상태를 공유하는 것이 필요한 디자인이 아닌 이상 여러 세션 간에 상태를 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-227">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="fc284-228">처음에 DI(종속성 주입)를 통해 생성된 `DotNetReference` 개체를 통해 노출되는 인스턴스 메서드의 경우 해당 개체를 범위가 지정된 개체로 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-228">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="fc284-229">이는 Blazor Server 앱에서 사용하는 모든 DI 서비스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-229">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="fc284-230">정적 메서드의 경우, 앱이 서버 인스턴스의 모든 사용자 간에 명시적으로 상태를 공유하는 디자인이 아닌 이상 클라이언트로 범위를 지정할 수 없는 상태를 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-230">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="fc284-231">사용자가 제공한 데이터를 JavaScript 호출의 매개 변수로 전달하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-231">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="fc284-232">반드시 매개 변수로 데이터를 전달해야 하는 경우에는 JavaScript 코드가 [XSS(교차 사이트 스크립팅)](#cross-site-scripting-xss) 취약성을 유발하지 않으면서 데이터 전달을 처리하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-232">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="fc284-233">예를 들어, 요소의 `innerHTML` 속성을 설정하여 DOM(문서 개체 모델)에 사용자가 제공한 데이터를 쓰지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-233">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="fc284-234">[CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)를 사용하여 `eval` 및 그 밖의 안전하지 않은 JavaScript 기본 형식을 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-234">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="fc284-235">프레임워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-235">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="fc284-236">.NET 메서드를 브라우저에 노출하는 것은 고급 시나리오로, 일반적인 Blazor 개발에서는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-236">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="fc284-237">이벤트</span><span class="sxs-lookup"><span data-stu-id="fc284-237">Events</span></span>

<span data-ttu-id="fc284-238">이벤트는 Blazor Server 앱에 대한 진입점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-238">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="fc284-239">웹앱에서 엔드포인트를 보호하기 위한 규칙이 Blazor Server 앱의 이벤트 처리에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-239">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="fc284-240">악의적인 클라이언트는 이벤트에 대한 페이로드로서 어떤 데이터도 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-240">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="fc284-241">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="fc284-241">For example:</span></span>

* <span data-ttu-id="fc284-242">`<select>`에 대한 변경 이벤트는 앱이 클라이언트에 제공한 옵션에 포함되지 않는 값을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-242">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="fc284-243">`<input>`은 클라이언트 쪽 유효성 검사를 우회하고 서버로 임의의 텍스트 데이터를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-243">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="fc284-244">앱은 해당 앱에서 처리하는 모든 이벤트에 대한 데이터의 유효성을 검사해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-244">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="fc284-245">Blazor 프레임워크 [forms 구성 요소](xref:blazor/forms-validation)는 기본적인 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-245">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="fc284-246">앱에서 사용자 지정 forms 구성 요소를 사용하는 경우, 그에 맞게 이벤트 데이터의 유효성을 검사하는 사용자 지정 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-246">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

<span data-ttu-id="fc284-247">Blazor Server 이벤트는 비동기적이므로 앱이 새 렌더링을 생성하여 대응할 시간을 갖기 전에 서버로 여러 이벤트를 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-247">Blazor Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="fc284-248">이 과정에서 몇 가지 고려해야 할 보안 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-248">This has some security implications to consider.</span></span> <span data-ttu-id="fc284-249">앱에서 클라이언트 작업을 제한하는 작업은 이벤트 처리기 내에서 수행해야 하며 현재 렌더링된 보기 상태에 종속되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-249">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="fc284-250">사용자가 카운터를 최대 세 차례 늘릴 수 있도록 허용하는 counter 구성 요소를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-250">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="fc284-251">카운터를 늘리는 단추는 `count`의 값에 따라 조건부로 정해집니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-251">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="fc284-252">클라이언트는 프레임워크가 이 구성 요소의 새 렌더링을 생성하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-252">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="fc284-253">따라서 단추가 UI에 의해 충분히 빠르게 제거되지 않기 때문에 사용자에 의해 `count`가 ‘세 차례보다 많이’ 늘어날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-253">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="fc284-254">다음 예제에는 세 차례의 `count` 증분이라는 제한을 올바르게 설정하는 방법이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-254">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="fc284-255">처리기 안에 `if (count < 3) { ... }` 검사를 추가한 결과 `count`를 늘린다는 결정이 현재 앱 상태를 기준으로 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-255">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="fc284-256">이 결정은 이전 예제에서처럼 일시적으로 오래되었을 수 있는 UI의 상태를 기준으로 이루어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-256">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="fc284-257">여러 디스패치를 방지하는 가드</span><span class="sxs-lookup"><span data-stu-id="fc284-257">Guard against multiple dispatches</span></span>

<span data-ttu-id="fc284-258">이벤트 콜백이 장기 작업을 비동기적으로 호출하는 경우(예: 외부 서비스 또는 데이터베이스에서 데이터를 페치하는 경우) 가드를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-258">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="fc284-259">가드는 작업이 진행되는 동안 시각적 피드백을 사용하여 사용자가 여러 작업을 큐에 대기시키는 것을 차단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-259">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="fc284-260">다음 구성 요소 코드는 `GetForecastAsync`가 서버에서 데이터를 가져오는 동안 `isLoading`을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-260">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="fc284-261">`isLoading`이 `true`인 동안에는 UI에서 단추가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-261">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="fc284-262">위 예제에서 살펴본 가드 패턴은 백그라운드 작업이 `async`-`await` 패턴으로 비동기적으로 실행될 경우에 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-262">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="fc284-263">조기에 취소하여 use-after-dispose 방지</span><span class="sxs-lookup"><span data-stu-id="fc284-263">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="fc284-264">[여러 디스패치를 방지하는 가드](#guard-against-multiple-dispatches) 섹션에서 설명한 것과 같이 가드를 사용하는 것 외에도, 구성 요소가 폐기된 경우 <xref:System.Threading.CancellationToken>을 사용하여 장기 작업을 취소하는 방안을 고려할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-264">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="fc284-265">이 접근 방식에는 구성 요소에서 *use-after-dispose* 를 방지할 수 있다는 이점도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-265">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="fc284-266">많은 양의 데이터를 생성하는 이벤트 방지</span><span class="sxs-lookup"><span data-stu-id="fc284-266">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="fc284-267">`oninput` 또는 `onscroll`과 같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-267">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="fc284-268">Blazor 서버 앱에서 이러한 이벤트를 사용하는 일이 없도록 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-268">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="fc284-269">추가 보안 지침</span><span class="sxs-lookup"><span data-stu-id="fc284-269">Additional security guidance</span></span>

<span data-ttu-id="fc284-270">ASP.NET Core 앱을 보호하기 위한 지침은 Blazor Server 앱에도 적용되며 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-270">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="fc284-271">로깅 및 중요한 데이터</span><span class="sxs-lookup"><span data-stu-id="fc284-271">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="fc284-272">HTTPS를 사용하여 전송 중인 정보 보호</span><span class="sxs-lookup"><span data-stu-id="fc284-272">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* [<span data-ttu-id="fc284-273">XSS(교차 사이트 스크립팅)</span><span class="sxs-lookup"><span data-stu-id="fc284-273">Cross-site scripting (XSS)</span></span>](#cross-site-scripting-xss)
* [<span data-ttu-id="fc284-274">교차 원본 보호</span><span class="sxs-lookup"><span data-stu-id="fc284-274">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="fc284-275">클릭재킹</span><span class="sxs-lookup"><span data-stu-id="fc284-275">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="fc284-276">오픈 리디렉션</span><span class="sxs-lookup"><span data-stu-id="fc284-276">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="fc284-277">로깅 및 중요한 데이터</span><span class="sxs-lookup"><span data-stu-id="fc284-277">Logging and sensitive data</span></span>

<span data-ttu-id="fc284-278">클라이언트와 서버 간의 JS interop 상호 작용은 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스를 사용하여 서버 로그에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-278">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> <span data-ttu-id="fc284-279">Blazor는 실제 이벤트나 JS interop 입력 및 출력과 같은 중요한 정보를 로깅하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-279">Blazor avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="fc284-280">서버에서 오류가 발생할 경우 프레임워크가 이를 클라이언트에 알리고 세션을 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-280">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="fc284-281">기본적으로 클라이언트는 일반 오류 메시지를 수신하며, 이 메시지는 브라우저의 개발자 도구에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-281">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="fc284-282">클라이언트 쪽 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-282">The client-side error doesn't include the call stack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="fc284-283">개발 단계에서는 [자세한 오류를 사용하도록 설정](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors)하여 클라이언트에서 중요한 오류 정보를 볼 수 있도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-283">For development purposes, sensitive error information can be made available to the client by [enabling detailed errors](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors).</span></span>

> [!WARNING]
> <span data-ttu-id="fc284-284">인터넷에서 사용되는 클라이언트로 오류 정보를 노출하는 것은 항상 피해야 하는 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-284">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="fc284-285">HTTPS를 사용하여 전송 중인 정보 보호</span><span class="sxs-lookup"><span data-stu-id="fc284-285">Protect information in transit with HTTPS</span></span>

<span data-ttu-id="fc284-286">Blazor Server는 클라이언트와 서버 간 통신을 위해 SignalR을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-286">Blazor Server uses SignalR for communication between the client and the server.</span></span> <span data-ttu-id="fc284-287">Blazor Server는 일반적으로 SignalR이 협상하는 전송(일반적으로 WebSocket)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-287">Blazor Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

<span data-ttu-id="fc284-288">Blazor Server는 서버와 클라이언트 간에 전송되는 데이터의 무결성과 기밀성을 보장하지 않으므로</span><span class="sxs-lookup"><span data-stu-id="fc284-288">Blazor Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="fc284-289">항상 HTTPS를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-289">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="fc284-290">XSS(교차 사이트 스크립팅)</span><span class="sxs-lookup"><span data-stu-id="fc284-290">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="fc284-291">XSS(교차 사이트 스크립팅)를 사용하면 권한이 없는 주체가 브라우저의 컨텍스트에서 임의의 논리를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-291">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="fc284-292">손상된 앱은 클라이언트에서 임의의 코드를 실행할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-292">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="fc284-293">이 취약성은 서버에 대해 몇 가지 악의적인 작업을 수행하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-293">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="fc284-294">서버로 가짜/잘못된 이벤트 디스패치.</span><span class="sxs-lookup"><span data-stu-id="fc284-294">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="fc284-295">실패한/잘못된 렌더링 완료 디스패치.</span><span class="sxs-lookup"><span data-stu-id="fc284-295">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="fc284-296">렌더링 완료의 디스패치 차단.</span><span class="sxs-lookup"><span data-stu-id="fc284-296">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="fc284-297">JavaScript에서 .NET으로의 interop 호출 디스패치.</span><span class="sxs-lookup"><span data-stu-id="fc284-297">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="fc284-298">.NET에서 JavaScript로의 interop 호출에 대한 응답 수정.</span><span class="sxs-lookup"><span data-stu-id="fc284-298">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="fc284-299">.NET이 JS interop 결과로 디스패치되는 것을 차단.</span><span class="sxs-lookup"><span data-stu-id="fc284-299">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="fc284-300">Blazor Server 프레임워크는 위와 같은 위협으로부터 보호하기 위한 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-300">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="fc284-301">클라이언트에서 렌더링 일괄 처리를 승인하지 않는 경우 새 UI 업데이트의 생성을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-301">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="fc284-302"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-302">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="fc284-303">클라이언트에서 응답을 받지 않은 상태에서 1분이 지나면 .NET에서 JavaScript로의 호출을 시간 초과 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-303">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="fc284-304"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-304">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="fc284-305">JS interop 중에 브라우저에서 들어오는 모든 입력에 대해 다음과 같은 기본적인 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-305">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="fc284-306">.NET 참조가 유효하며 .NET 메서드에 예상하는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-306">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="fc284-307">데이터 형식이 잘못되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-307">The data isn't malformed.</span></span>
  * <span data-ttu-id="fc284-308">페이로드에 메서드에 대한 올바른 인수 개수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-308">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="fc284-309">메서드를 호출하기 전에 인수나 결과를 올바르게 역직렬화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-309">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="fc284-310">브라우저에서 디스패치된 이벤트로부터 들어오는 모든 입력에 대해 다음과 같은 기본적인 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-310">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="fc284-311">이벤트의 형식이 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-311">The event has a valid type.</span></span>
  * <span data-ttu-id="fc284-312">이벤트의 데이터를 역직렬화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-312">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="fc284-313">이벤트에 연결된 이벤트 처리기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-313">There's an event handler associated with the event.</span></span>

<span data-ttu-id="fc284-314">프레임워크에서 구현하는 보호 기능에 더해, 개발자가 위협으로부터 보호하기 위해 앱을 코딩하고 적절한 조치를 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-314">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="fc284-315">이벤트를 처리할 때 항상 데이터의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-315">Always validate data when handling events.</span></span>
* <span data-ttu-id="fc284-316">잘못된 데이터가 수신되면 적절한 조치를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-316">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="fc284-317">데이터를 무시하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-317">Ignore the data and return.</span></span> <span data-ttu-id="fc284-318">이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-318">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="fc284-319">앱에서 입력이 정상이 아니고 정상 클라이언트에 의해 생성될 수 없는 값임을 확인한 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-319">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="fc284-320">예외를 throw하면 회로가 중단되고 세션이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-320">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="fc284-321">로그에 포함된 렌더링 일괄 처리 완료에서 제공하는 오류 메시지를 신뢰하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-321">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="fc284-322">오류는 클라이언트가 제공하는 것인데, 클라이언트가 손상되었을 수 있으므로 일반적으로 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-322">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="fc284-323">JavaScript와 .NET 메서드 사이의 두 방향 모두에서 JS interop 호출의 입력을 신뢰하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-323">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="fc284-324">앱은 인수나 결과가 올바르지 역직렬화되는 경우에도 인수와 결과의 내용이 올바른지 확인하기 위해 유효성을 검사할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-324">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="fc284-325">XSS 취약성이 존재하기 위해서는 앱이 렌더링된 페이지에 사용자 입력을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-325">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> <span data-ttu-id="fc284-326">Blazor Server 구성 요소는 `.razor` 파일의 태그가 절차적 C# 논리로 변환되는 컴파일 시간 단계를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-326">Blazor Server components execute a compile-time step where the markup in a `.razor` file is transformed into procedural C# logic.</span></span> <span data-ttu-id="fc284-327">C# 논리는 런타임에 요소, 텍스트 및 자식 구성 요소를 설명하는 ‘렌더링 트리’를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-327">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="fc284-328">이는 JavaScript 명령 시퀀스를 통해 브라우저의 DOM에 적용됩니다(또는 사전 렌더링의 경우 HTML로 직렬화됩니다).</span><span class="sxs-lookup"><span data-stu-id="fc284-328">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="fc284-329">Razor 구문은 텍스트만 쓸 수 있는 명령을 통해 DOM에 추가되기 때문에 일반적인 Razor 구문을 통해 렌더링된 사용자 입력(예: `@someStringValue`)은 XSS 취약성을 노출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-329">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="fc284-330">값에 HTML 마크업이 포함된 경우에도 이 값은 정적 텍스트로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-330">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="fc284-331">사전 렌더링을 수행하는 경우 출력은 HTML로 인코딩되며 이 또한 콘텐츠를 정적 텍스트로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-331">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="fc284-332">스크립트 태그는 허용되지 않으며 앱의 구성 요소 렌더링 트리에 포함해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-332">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="fc284-333">스크립트 태그가 구성 요소의 마크업에 포함된 경우 컴파일 시간 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-333">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="fc284-334">구성 요소 작성자는 Razor를 사용하지 않고 C#에서 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-334">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="fc284-335">구성 요소 작성자는 출력을 내보낼 때 올바른 API를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-335">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="fc284-336">예를 들어 XSS 취약성을 생성할 수 있는 `builder.AddMarkupContent(0, someUserSuppliedString)` 대신 `builder.AddContent(0, someUserSuppliedString)`을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-336">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="fc284-337">XSS 공격으로부터 보호하기 위한 조치의 일환으로 [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)와 같은 XSS 위험 완화를 구현하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-337">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="fc284-338">자세한 내용은 <xref:security/cross-site-scripting>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-338">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="fc284-339">교차 원본 보호</span><span class="sxs-lookup"><span data-stu-id="fc284-339">Cross-origin protection</span></span>

<span data-ttu-id="fc284-340">교차 원본 공격에서는 다른 원본의 클라이언트가 서버에 대한 동작을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-340">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="fc284-341">이때 진행되는 악의적인 동작은 보통 GET 요청이나 양식 POST(교차 사이트 요청 위조/CSRF)이며, 악성 WebSocket을 여는 것도 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-341">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> <span data-ttu-id="fc284-342">Blazor Server 앱은 [허브 프로토콜을 사용하는 다른 모든 SignalR 앱에서 제공하는 보장과 동일한 보장](xref:signalr/security)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-342">Blazor Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="fc284-343">Blazor Server 앱은 원본 간 액세스를 차단하기 위한 조치를 취하지 않은 이상 원본 간에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-343">Blazor Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="fc284-344">교차 소스 액세스를 사용하지 않도록 설정하려면 파이프라인에 CORS 미들웨어를 추가하고 Blazor 엔드포인트 메타데이터에 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute>을 추가하여 엔드포인트에서 CORS를 사용하지 않도록 설정하거나 [교차 소스 리소스 공유에 대해 SignalR을 구성](xref:signalr/security#cross-origin-resource-sharing)하여 허용되는 소스 집합을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-344">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="fc284-345">CORS가 사용하도록 설정된 경우에는 CORS 구성에 따라 앱을 보호하는 추가 단계가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-345">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="fc284-346">CORS가 전역적으로 사용하도록 설정된 경우 엔드포인트 경로 빌더에서 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 호출한 후에 엔드포인트 메타데이터에 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 메타데이터를 추가하여 Blazor Server 허브에 대해 CORS를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-346">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadata to the endpoint metadata after calling <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> on the endpoint route builder.</span></span>

<span data-ttu-id="fc284-347">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-347">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="fc284-348">클릭재킹</span><span class="sxs-lookup"><span data-stu-id="fc284-348">Click-jacking</span></span>

<span data-ttu-id="fc284-349">클릭재킹은 특정 사이트를 다른 원본의 사이트 안에서 `<iframe>`으로 렌더링하여 사용자가 공격 대상 사이트에서 동작을 수행하도록 유도하는 공격입니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-349">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="fc284-350">앱이 `<iframe>` 내에서 렌더링되지 않도록 보호하려면 [CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 `X-Frame-Options` 헤더를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-350">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="fc284-351">자세한 내용은 [MDN 웹 문서: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-351">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="fc284-352">오픈 리디렉션</span><span class="sxs-lookup"><span data-stu-id="fc284-352">Open redirects</span></span>

<span data-ttu-id="fc284-353">Blazor Server 앱 세션이 시작되면 서버는 세션 시작 과정의 일환으로 전송된 URL에 대해 기본적인 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-353">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="fc284-354">프레임워크는 기준 URL이 현재 URL의 부모인지 확인한 후에 회로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-354">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="fc284-355">프레임워크에서 추가로 수행하는 검사는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-355">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="fc284-356">사용자가 클라이언트에서 링크를 선택하면 링크의 URL이 서버로 전송되어 이를 바탕으로 수행할 작업이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-356">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="fc284-357">예를 들어 앱이 클라이언트 쪽 탐색을 수행하거나 새 위치로 이동하도록 브라우저에 알려줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-357">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="fc284-358">구성 요소는 <xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 프로그래밍 방식으로 탐색 요청을 트리거할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-358">Components can also trigger navigation requests programatically through the use of <xref:Microsoft.AspNetCore.Components.NavigationManager>.</span></span> <span data-ttu-id="fc284-359">이러한 시나리오에서는 앱이 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-359">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="fc284-360">구성 요소는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-360">Components must:</span></span>

* <span data-ttu-id="fc284-361">탐색 호출 인수의 일부로 사용자 입력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-361">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="fc284-362">앱에서 대상이 허용되는지 확인하기 위해 인수의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-362">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="fc284-363">이렇게 하지 않으면 악의적인 사용자가 공격자가 제어하는 사이트로 이동하도록 브라우저를 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-363">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="fc284-364">이 시나리오에서 공격자는 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 메서드 호출의 일환으로 사용자 입력을 사용하도록 앱을 유도합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-364">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="fc284-365">이 조건은 앱의 일부로 링크를 렌더링하는 경우에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-365">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="fc284-366">가능한 경우 상대 링크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-366">If possible, use relative links.</span></span>
* <span data-ttu-id="fc284-367">절대 링크 대상을 페이지에 포함하기 전에 절대 링크 대상이 유효한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-367">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="fc284-368">자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc284-368">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="fc284-369">보안 검사 목록</span><span class="sxs-lookup"><span data-stu-id="fc284-369">Security checklist</span></span>

<span data-ttu-id="fc284-370">다음 보안 고려 사항 목록은 모든 항목을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-370">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="fc284-371">이벤트 인수의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-371">Validate arguments from events.</span></span>
* <span data-ttu-id="fc284-372">JS interop 호출 입력 및 결과의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-372">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="fc284-373">.NET에서 JS interop로의 호출에 대한 사용자 입력을 사용하지 않습니다(또는 미리 유효성을 검사합니다).</span><span class="sxs-lookup"><span data-stu-id="fc284-373">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="fc284-374">클라이언트가 메모리를 무제한으로 할당하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-374">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="fc284-375">구성 요소 내의 데이터.</span><span class="sxs-lookup"><span data-stu-id="fc284-375">Data within the component.</span></span>
  * <span data-ttu-id="fc284-376">클라이언트로 반환되는 `DotNetObject` 참조.</span><span class="sxs-lookup"><span data-stu-id="fc284-376">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="fc284-377">여러 디스패치를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-377">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="fc284-378">구성 요소가 폐기된 경우 장기 작업을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-378">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="fc284-379">많은 양의 데이터를 생성하는 이벤트를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-379">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="fc284-380"><xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에 대한 호출의 일환으로 사용자 입력을 사용하지 않고, 반드시 사용해야 하는 경우 허용되는 원본 집합을 기준으로 사용자 입력에서 URL의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-380">Avoid using user input as part of calls to <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="fc284-381">UI 상태가 아닌 구성 요소 상태를 기준으로 권한 부여를 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-381">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="fc284-382">[CSP(콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)를 사용하여 XSS 공격으로부터 보호하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-382">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="fc284-383">CSP 및 [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)를 사용하여 클릭재킹으로부터 보호하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-383">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="fc284-384">CORS를 사용하도록 설정하거나 Blazor 앱에 대해 CORS를 명시적으로 사용하지 않도록 설정할 때 CORS 설정이 적절한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-384">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="fc284-385">Blazor 앱의 서버 측 제한이 허용할 수 없는 위험 수준을 유발하지 않으면서 허용할 수 있는 사용자 환경을 제공하는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="fc284-385">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
