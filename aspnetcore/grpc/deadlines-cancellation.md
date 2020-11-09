---
title: 최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스
author: jamesnk
description: .NET에서 최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059925"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="5718b-103">최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="5718b-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="5718b-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5718b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5718b-105">최종 기한 및 취소는 gRPC 클라이언트에서 진행 중인 호출을 중단하는 데 사용하는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="5718b-106">이 문서에서는 최종 기한 및 취소가 중요한 이유와 .NET gRPC 앱에서 사용하는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="5718b-107">최종 기한</span><span class="sxs-lookup"><span data-stu-id="5718b-107">Deadlines</span></span>

<span data-ttu-id="5718b-108">최종 기한을 사용하면 gRPC 클라이언트에서 호출이 완료될 때까지 대기하는 기간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="5718b-109">최종 기한이 초과하면 호출이 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="5718b-110">최종 기한 설정은 호출을 실행할 수 있는 기간에 대한 상한을 제공하기 때문에 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="5718b-111">또한 오작동하는 서비스가 계속 실행되어 서버 리소스가 소진되는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="5718b-112">최종 기한은 안정적인 앱을 빌드하는 데 유용한 도구이므로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="5718b-113">최종 기한 구성:</span><span class="sxs-lookup"><span data-stu-id="5718b-113">Deadline configuration:</span></span>

* <span data-ttu-id="5718b-114">최종 기한은 호출할 때 `CallOptions.Deadline`을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="5718b-115">기본 최종 기한 값은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-115">There is no default deadline value.</span></span> <span data-ttu-id="5718b-116">gRPC 호출은 최종 기한을 지정하지 않는 한 시간이 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="5718b-117">최종 기한은 최종 기한이 초과하는 시점의 UTC 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="5718b-118">예를 들어 `DateTime.UtcNow.AddSeconds(5)`는 최종 기한이 지금부터 5초임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="5718b-119">이전 또는 현재 시간을 사용하면 호출이 바로 최종 기한을 초과하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="5718b-120">최종 기한은 gRPC 호출을 통해 서비스에 전송되고 클라이언트와 서비스 모두에서 독립적으로 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="5718b-121">한 머신에서는 gRPC 호출이 완료되었지만 응답이 클라이언트에 반환된 시간까지 최종 기한이 초과하였을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="5718b-122">최종 기한을 초과하는 경우 클라이언트와 서비스의 동작이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="5718b-123">클라이언트는 기본 HTTP 요청을 즉시 중단하고 `DeadlineExceeded` 오류를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="5718b-124">클라이언트 앱에서는 오류를 catch하고 사용자에게 시간 제한 메시지를 표시하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="5718b-125">서버에서는 실행 중인 HTTP 요청이 중단되고 [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="5718b-126">HTTP 요청은 중단되지만 gRPC 호출은 메서드가 완료될 때까지 서버에서 계속 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="5718b-127">취소 토큰을 비동기 메서드에 전달하여 호출과 함께 메서드가 취소되도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="5718b-128">예를 들어 비동기 데이터베이스 쿼리와 HTTP 요청에 취소 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="5718b-129">취소 토큰을 전달하면 취소된 호출이 서버에서 신속하게 완료되어 다른 호출을 위한 리소스를 확보할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="5718b-130">`CallOptions.Deadline`을 구성하여 gRPC 호출의 최종 기한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="5718b-131">gRPC 서비스에서 `ServerCallContext.CancellationToken`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="5718b-132">최종 기한 전파</span><span class="sxs-lookup"><span data-stu-id="5718b-132">Propagating deadlines</span></span>

<span data-ttu-id="5718b-133">실행 중인 gRPC 서비스에서 gRPC를 호출하는 경우 최종 기한을 전파해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="5718b-134">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5718b-134">For example:</span></span>

1. <span data-ttu-id="5718b-135">클라이언트 앱에서 최종 기한이 있는 `FrontendService.GetUser`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="5718b-136">`FrontendService`은 `UserService.GetUser`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="5718b-137">클라이언트에 지정된 최종 기한을 새 gRPC 호출에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="5718b-138">`UserService.GetUser`에서 최종 기한을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="5718b-139">클라이언트 앱의 최종 기한이 초과하면 올바르게 시간 초과됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="5718b-140">호출 컨텍스트에서는 `ServerCallContext.Deadline`에 최종 기한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="5718b-141">수동으로 최종 기한을 전파하면 복잡할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="5718b-142">최종 기한을 모든 호출에 전달해야 하므로 실수로 누락하기가 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="5718b-143">자동 솔루션은 gRPC 클라이언트 팩터리에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="5718b-144">`EnableCallContextPropagation`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="5718b-145">최종 기한 및 취소 토큰을 자식 호출에 자동으로 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="5718b-146">복잡한 중첩 gRPC 시나리오에서 최종 기한 및 취소를 항상 전파하도록 하는 가장 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="5718b-147">자세한 내용은 <xref:grpc/clientfactory#deadline-and-cancellation-propagation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5718b-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="5718b-148">취소</span><span class="sxs-lookup"><span data-stu-id="5718b-148">Cancellation</span></span>

<span data-ttu-id="5718b-149">취소를 사용하면 gRPC 클라이언트에서 더 이상 필요 없는 장기 실행 호출을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="5718b-150">예를 들어 사용자가 웹 사이트의 페이지를 방문하면 실시간 업데이트를 스트리밍하는 gRPC 호출이 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="5718b-151">사용자가 페이지를 벗어나면 이 스트림을 취소해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="5718b-152">호출 시 [CallOptions.CancellationToken](xref:System.Threading.CancellationToken)으로 취소 토큰을 전달하거나 `Dispose`를 호출하여 클라이언트에서 gRPC 호출을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="5718b-153">취소할 수 있는 gRPC 서비스는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="5718b-154">`ServerCallContext.CancellationToken`을 비동기 메서드에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="5718b-155">비동기 메서드를 취소하면 서버 호출을 신속하게 완료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="5718b-156">취소 토큰을 자식 호출에 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="5718b-157">취소 토큰을 전파하면 자식 호출이 부모와 함께 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="5718b-158">[gRPC 클라이언트 팩터리](xref:grpc/clientfactory) 및 `EnableCallContextPropagation()`은 취소 토큰을 자동으로 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="5718b-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5718b-159">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5718b-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
