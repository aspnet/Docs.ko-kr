---
title: ASP.NET Core Blazor 상태 관리
author: guardrex
description: Blazor Server 앱에서 상태를 유지하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 5b7a6fefd13d7255fbe3ae7010df48c6211fa755
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854338"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="de240-103">ASP.NET Core Blazor 상태 관리</span><span class="sxs-lookup"><span data-stu-id="de240-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="de240-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de240-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="de240-105">Blazor WebAssembly 앱에서 만든 사용자 상태는 브라우저의 메모리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="de240-106">브라우저 메모리에 저장되는 사용자 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="de240-107">구성 요소 인스턴스의 계층 구조 및 렌더링된 UI의 가장 최근 렌더링 출력.</span><span class="sxs-lookup"><span data-stu-id="de240-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="de240-108">구성 요소 인스턴스의 필드 및 속성 값.</span><span class="sxs-lookup"><span data-stu-id="de240-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="de240-109">[DI(종속성 주입)](xref:fundamentals/dependency-injection) 서비스 인스턴스에 저장된 데이터.</span><span class="sxs-lookup"><span data-stu-id="de240-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="de240-110">[JavaScript interop](xref:blazor/call-javascript-from-dotnet) 호출을 통해 설정된 값.</span><span class="sxs-lookup"><span data-stu-id="de240-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="de240-111">사용자가 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드하면 브라우저의 메모리에 저장된 사용자 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="de240-112">[보호된 브라우저 스토리지](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage)(<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> 네임스페이스)는 ASP.NET Core 데이터 보호를 사용하며, Blazor Server 앱에서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="de240-113">브라우저 세션 간 상태 유지</span><span class="sxs-lookup"><span data-stu-id="de240-113">Persist state across browser sessions</span></span>

<span data-ttu-id="de240-114">일반적으로 사용자가 이미 존재하는 데이터를 단순히 읽는 것이 아니라 데이터를 적극적으로 만드는 브라우저 세션 간에 상태를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="de240-115">브라우저 세션 간에 상태를 유지하려면 앱에서 브라우저의 메모리 외의 스토리지 위치에 데이터를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="de240-116">상태 지속성은 자동이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="de240-116">State persistence isn't automatic.</span></span> <span data-ttu-id="de240-117">상태 저장 데이터 지속성을 구현하려면 앱을 개발할 때 일정 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="de240-118">데이터 지속성은 일반적으로 사용자가 특별히 노력해서 만든 높은 가치의 상태에만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="de240-119">다음 예제에서 상태를 유지하면 상업 활동의 시간 또는 지원을 절약할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="de240-120">다중 단계 웹 양식: 상태가 손실된 경우 다단계 웹 양식의 완료된 여러 단계에 대한 데이터를 사용자가 다시 입력하려면 오랜 시간이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="de240-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="de240-121">이 시나리오에서는 사용자가 양식을 벗어났다가 나중에 돌아올 경우 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="de240-122">쇼핑 카트: 잠재적 수익을 나타내는, 상업적으로 중요한 앱 구성 요소를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="de240-123">상태와 쇼핑 카트가 손실된 사용자는 나중에 사이트로 돌아올 때 제품 또는 서비스를 더 적게 구매할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="de240-124">앱은 ‘앱 상태’만 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-124">An app can only persist *app state*.</span></span> <span data-ttu-id="de240-125">구성 요소 인스턴스 및 렌더링 트리와 같은 UI는 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="de240-126">구성 요소 및 렌더링 트리는 일반적으로 직렬화할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="de240-127">트리 뷰 컨트롤의 펼친 노드와 같은 UI 상태를 유지하려면 앱이 사용자 지정 코드를 사용하여 UI 상태의 동작을 직렬화 가능한 앱 상태로 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="de240-128">상태를 유지할 위치</span><span class="sxs-lookup"><span data-stu-id="de240-128">Where to persist state</span></span>

<span data-ttu-id="de240-129">상태를 유지하기 위한 공통 위치는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="de240-130">서버 쪽 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="de240-131">URL</span><span class="sxs-lookup"><span data-stu-id="de240-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="de240-132">브라우저 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="de240-133">메모리 내 상태 컨테이너 서비스</span><span class="sxs-lookup"><span data-stu-id="de240-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="de240-134">서버 쪽 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-134">Server-side storage</span></span></h2>

<span data-ttu-id="de240-135">여러 사용자 및 디바이스에 걸친 영구적 데이터 지속성을 위해 웹 API를 통해 액세스하는 독립적인 서버 쪽 스토리지를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="de240-136">표시되는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-136">Options include:</span></span>

* <span data-ttu-id="de240-137">Blob 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-137">Blob storage</span></span>
* <span data-ttu-id="de240-138">키-값 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-138">Key-value storage</span></span>
* <span data-ttu-id="de240-139">관계형 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="de240-139">Relational database</span></span>
* <span data-ttu-id="de240-140">Table Storage</span><span class="sxs-lookup"><span data-stu-id="de240-140">Table storage</span></span>

<span data-ttu-id="de240-141">데이터가 저장된 후 사용자의 상태가 유지되고 모든 새 브라우저 세션에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="de240-142">Blazor WebAssembly 앱은 전적으로 사용자의 브라우저에서 실행되므로 스토리지 서비스 및 데이터베이스와 같은 안전한 외부 시스템에 액세스하기 위한 추가 조치가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="de240-143">Blazor WebAssembly 앱은 SPA(단일 페이지 애플리케이션)와 동일한 방식으로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="de240-144">일반적으로 앱은 [OAuth](https://oauth.net)/[OpenID Connect(OIDC)](https://openid.net/connect/)를 통해 사용자를 인증한 다음 서버 쪽 앱에 대한 웹 API 호출을 통해 스토리지 서비스 및 데이터베이스와 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="de240-145">서버 쪽 앱은 Blazor WebAssembly 앱과 스토리지 서비스 또는 데이터베이스 간 데이터 전송을 중재합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="de240-146">Blazor WebAssembly 앱은 서버 쪽 앱에 대한 임시 연결을 유지하고, 서버 쪽 앱은 스토리지에 대한 영구 연결을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="de240-147">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="de240-148">보안 및Blazor  *Identity* 문서</span><span class="sxs-lookup"><span data-stu-id="de240-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="de240-149">Azure 데이터 스토리지 옵션에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="de240-150">Azure 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="de240-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="de240-151">Azure Storage 설명서</span><span class="sxs-lookup"><span data-stu-id="de240-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="de240-152">URL</span><span class="sxs-lookup"><span data-stu-id="de240-152">URL</span></span></h2>

<span data-ttu-id="de240-153">탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL 일부로 모델링합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="de240-154">URL에 모델링된 사용자 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="de240-155">표시된 엔터티의 ID</span><span class="sxs-lookup"><span data-stu-id="de240-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="de240-156">페이징 그리드의 현재 페이지 번호</span><span class="sxs-lookup"><span data-stu-id="de240-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="de240-157">사용자가 페이지를 수동으로 다시 로드하는 경우 브라우저 주소 표시줄의 내용이 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="de240-158">[`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 URL 패턴을 정의하는 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="de240-159">브라우저 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-159">Browser storage</span></span></h2>

<span data-ttu-id="de240-160">사용자가 적극적으로 만드는 임시 데이터의 경우, 일반적으로 사용되는 스토리지 위치는 브라우저의 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 및 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="de240-161">`localStorage`는 브라우저의 창으로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="de240-162">사용자가 페이지를 다시 로드하거나 브라우저를 닫았다가 다시 열면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="de240-163">사용자가 여러 개의 브라우저 탭을 여는 경우 탭 간에 상태가 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="de240-164">명시적으로 지울 때까지 데이터가 `localStorage`에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="de240-165">`sessionStorage`는 브라우저 탭으로 범위가 지정됩니다. 사용자가 탭을 다시 로드하면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="de240-166">사용자가 탭 또는 브라우저를 닫으면 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="de240-167">사용자가 여러 개의 브라우저 탭을 여는 경우 각 탭에 독립적인 고유한 버전의 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="de240-168">Blazor WebAssembly 앱에서는 `localStorage` 및 `sessionStorage`를 사용할 수 있지만, 사용자 지정 코드를 작성하거나 타사 패키지를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="de240-169">일반적으로 `sessionStorage`를 사용하는 것이 더 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="de240-170">`sessionStorage`를 사용하면 사용자가 여러 탭을 열 때 다음과 같은 경우가 발생하는 위험을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="de240-171">탭 간에 상태 스토리지의 버그</span><span class="sxs-lookup"><span data-stu-id="de240-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="de240-172">한 탭에서 다른 탭의 상태를 덮어쓸 때 혼동되는 동작</span><span class="sxs-lookup"><span data-stu-id="de240-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="de240-173">앱이 브라우저를 닫았다가 다시 열 때 상태를 유지해야 하는 경우 `localStorage`를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="de240-174">사용자는 `localStorage` 및 `sessionStorage`에 저장된 데이터를 보거나 변조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="de240-175">메모리 내 상태 컨테이너 서비스</span><span class="sxs-lookup"><span data-stu-id="de240-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="de240-176">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="de240-176">Additional resources</span></span>

* [<span data-ttu-id="de240-177">인증 작업 전에 앱 상태 저장</span><span class="sxs-lookup"><span data-stu-id="de240-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="de240-178">Blazor Server는 상태 저장 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="de240-179">대체로 앱은 서버와의 연결을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="de240-180">사용자 상태는 서버 메모리의 ‘회로’에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="de240-181">회로에 저장되는 사용자 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="de240-182">구성 요소 인스턴스의 계층 구조 및 렌더링된 UI의 가장 최근 렌더링 출력.</span><span class="sxs-lookup"><span data-stu-id="de240-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="de240-183">구성 요소 인스턴스의 필드 및 속성 값.</span><span class="sxs-lookup"><span data-stu-id="de240-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="de240-184">회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터</span><span class="sxs-lookup"><span data-stu-id="de240-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="de240-185">사용자 상태는 [JavaScript interop](xref:blazor/call-javascript-from-dotnet) 호출을 통해 브라우저의 메모리 집합에 있는 JavaScript 변수에서 찾을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="de240-186">사용자에게 임시 네트워크 연결 손실이 발생하는 경우, Blazor는 사용자의 원래 상태로 사용자를 원래 회로에 다시 연결하려 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="de240-187">그러나 사용자를 서버 메모리의 원래 회로에 다시 연결할 수 없는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="de240-188">서버는 연결이 끊어진 회로를 계속 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="de240-189">시간 제한 이후 또는 서버에 메모리 압력이 발생할 경우 서버는 연결이 끊어진 회로를 해제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="de240-190">부하가 분산된 다중 서버 배포 환경에서 개별 서버는 오류가 발생하거나 전체 요청 볼륨을 더 이상 처리할 필요가 없으면 자동으로 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="de240-191">사용자가 다시 연결하려고 할 때 사용자에 대한 원래 서버 처리 요청을 사용할 수 없게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="de240-192">사용자는 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드할 수 있으며, 그러면 브라우저의 메모리에 저장된 모든 상태가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="de240-193">예를 들어 JavaScript interop 호출을 통해 설정된 JavaScript 변수 값이 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="de240-194">사용자를 원래 회로에 다시 연결할 수 없는 경우, 사용자는 빈 상태로 새 회로를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="de240-195">이 경우 데스크톱 앱을 닫았다가 다시 여는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="de240-196">회로 간 상태 유지</span><span class="sxs-lookup"><span data-stu-id="de240-196">Persist state across circuits</span></span>

<span data-ttu-id="de240-197">일반적으로 사용자가 이미 존재하는 데이터를 단순히 읽는 것이 아니라 데이터를 적극적으로 만드는 회로 간에 상태를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="de240-198">회로 간에 상태를 유지하려면 앱이 브라우저 메모리 외의 스토리지 위치에 데이터를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="de240-199">상태 지속성은 자동이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="de240-199">State persistence isn't automatic.</span></span> <span data-ttu-id="de240-200">상태 저장 데이터 지속성을 구현하려면 앱을 개발할 때 일정 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="de240-201">데이터 지속성은 일반적으로 사용자가 특별히 노력해서 만든 높은 가치의 상태에만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="de240-202">다음 예제에서 상태를 유지하면 상업 활동의 시간 또는 지원을 절약할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="de240-203">다중 단계 웹 양식: 상태가 손실된 경우 다단계 웹 양식의 완료된 여러 단계에 대한 데이터를 사용자가 다시 입력하려면 오랜 시간이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="de240-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="de240-204">이 시나리오에서는 사용자가 양식을 벗어났다가 나중에 돌아올 경우 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="de240-205">쇼핑 카트: 잠재적 수익을 나타내는, 상업적으로 중요한 앱 구성 요소를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="de240-206">상태와 쇼핑 카트가 손실된 사용자는 나중에 사이트로 돌아올 때 제품 또는 서비스를 더 적게 구매할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="de240-207">앱은 ‘앱 상태’만 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-207">An app can only persist *app state*.</span></span> <span data-ttu-id="de240-208">구성 요소 인스턴스 및 렌더링 트리와 같은 UI는 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="de240-209">구성 요소 및 렌더링 트리는 일반적으로 직렬화할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="de240-210">트리 뷰 컨트롤의 펼친 노드와 같은 UI 상태를 유지하려면 앱이 사용자 지정 코드를 사용하여 UI 상태의 동작을 직렬화 가능한 앱 상태로 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="de240-211">상태를 유지할 위치</span><span class="sxs-lookup"><span data-stu-id="de240-211">Where to persist state</span></span>

<span data-ttu-id="de240-212">상태를 유지하기 위한 공통 위치는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="de240-213">서버 쪽 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="de240-214">URL</span><span class="sxs-lookup"><span data-stu-id="de240-214">URL</span></span>](#url-server)
* [<span data-ttu-id="de240-215">브라우저 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="de240-216">메모리 내 상태 컨테이너 서비스</span><span class="sxs-lookup"><span data-stu-id="de240-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="de240-217">서버 쪽 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-217">Server-side storage</span></span></h2>

<span data-ttu-id="de240-218">여러 사용자 및 디바이스에 걸친 영구적 데이터 지속성을 위해 앱이 서버 쪽 스토리지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="de240-219">표시되는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-219">Options include:</span></span>

* <span data-ttu-id="de240-220">Blob 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-220">Blob storage</span></span>
* <span data-ttu-id="de240-221">키-값 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-221">Key-value storage</span></span>
* <span data-ttu-id="de240-222">관계형 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="de240-222">Relational database</span></span>
* <span data-ttu-id="de240-223">Table Storage</span><span class="sxs-lookup"><span data-stu-id="de240-223">Table storage</span></span>

<span data-ttu-id="de240-224">데이터가 저장된 후 사용자의 상태가 유지되고 모든 새 회로에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="de240-225">Azure 데이터 스토리지 옵션에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="de240-226">Azure 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="de240-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="de240-227">Azure Storage 설명서</span><span class="sxs-lookup"><span data-stu-id="de240-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="de240-228">URL</span><span class="sxs-lookup"><span data-stu-id="de240-228">URL</span></span></h2>

<span data-ttu-id="de240-229">탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL 일부로 모델링합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="de240-230">URL에 모델링된 사용자 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="de240-231">표시된 엔터티의 ID</span><span class="sxs-lookup"><span data-stu-id="de240-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="de240-232">페이징 그리드의 현재 페이지 번호</span><span class="sxs-lookup"><span data-stu-id="de240-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="de240-233">브라우저의 주소 표시줄 내용은 다음과 같은 경우에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="de240-234">사용자가 페이지를 수동으로 다시 로드하는 경우</span><span class="sxs-lookup"><span data-stu-id="de240-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="de240-235">웹 서버를 사용할 수 없게 되고 사용자가 다른 서버에 연결하기 위해 페이지를 강제로 다시 로드해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="de240-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="de240-236">[`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 URL 패턴을 정의하는 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="de240-237">브라우저 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-237">Browser storage</span></span></h2>

<span data-ttu-id="de240-238">사용자가 적극적으로 만드는 임시 데이터의 경우, 일반적으로 사용되는 스토리지 위치는 브라우저의 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 및 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="de240-239">`localStorage`는 브라우저의 창으로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="de240-240">사용자가 페이지를 다시 로드하거나 브라우저를 닫았다가 다시 열면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="de240-241">사용자가 여러 개의 브라우저 탭을 여는 경우 탭 간에 상태가 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="de240-242">명시적으로 지울 때까지 데이터가 `localStorage`에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="de240-243">`sessionStorage`는 브라우저 탭으로 범위가 지정됩니다. 사용자가 탭을 다시 로드하면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="de240-244">사용자가 탭 또는 브라우저를 닫으면 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="de240-245">사용자가 여러 개의 브라우저 탭을 여는 경우 각 탭에 독립적인 고유한 버전의 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="de240-246">일반적으로 `sessionStorage`를 사용하는 것이 더 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="de240-247">`sessionStorage`를 사용하면 사용자가 여러 탭을 열 때 다음과 같은 경우가 발생하는 위험을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="de240-248">탭 간에 상태 스토리지의 버그</span><span class="sxs-lookup"><span data-stu-id="de240-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="de240-249">한 탭에서 다른 탭의 상태를 덮어쓸 때 혼동되는 동작</span><span class="sxs-lookup"><span data-stu-id="de240-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="de240-250">앱이 브라우저를 닫았다가 다시 열 때 상태를 유지해야 하는 경우 `localStorage`를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="de240-251">브라우저 스토리지를 사용하는 경우의 주의 사항:</span><span class="sxs-lookup"><span data-stu-id="de240-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="de240-252">서버 쪽 데이터베이스를 사용하는 경우와 유사하게, 데이터 로드 및 저장은 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="de240-253">서버 쪽 데이터베이스와 달리, 미리 렌더링 단계에서는 요청한 페이지가 브라우저에 없기 때문에 미리 렌더링하는 동안 스토리지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="de240-254">Blazor Server 앱의 경우 몇 킬로바이트의 데이터 스토리지를 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="de240-255">몇 킬로바이트를 넘을 경우, 네트워크를 통해 데이터를 로드하고 저장하기 때문에 성능에 미치는 영향을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="de240-256">사용자가 데이터를 보거나 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="de240-257">[ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="de240-258">예를 들어 [ASP.NET Core 보호된 브라우저 스토리지](#aspnet-core-protected-browser-storage)는 ASP.NET Core 데이터 보호를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="de240-259">타사 NuGet 패키지는 `localStorage` 및 `sessionStorage` 작업을 위한 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="de240-260">[ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 투명하게 사용하는 패키지를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="de240-261">데이터 보호는 저장된 데이터를 암호화하고, 저장된 데이터의 잠재적 변조 위험을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="de240-262">JSON 직렬화된 데이터를 일반 텍스트로 저장한 경우, 사용자는 브라우저 개발자 도구를 사용하여 데이터를 확인할 수 있으며 저장된 데이터를 수정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="de240-263">본질적으로 데이터가 중요하지 않을 수도 있으므로 데이터 보호가 항상 문제가 되는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="de240-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="de240-264">예를 들어 저장된 UI 요소 색을 읽거나 수정하는 경우 사용자 또는 조직에 중요한 보안 위험이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="de240-265">사용자가 ‘중요한 데이터’를 검사하거나 변조할 수 있도록 허용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="de240-266">ASP.NET Core 보호된 브라우저 스토리지</span><span class="sxs-lookup"><span data-stu-id="de240-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="de240-267">ASP.NET Core 보호된 브라우저 스토리지는 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 및 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="de240-268">보호된 브라우저 스토리지는 ASP.NET Core 데이터 보호에 의존하며 Blazor Server 앱에 대해서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="de240-269">구성 요소 내에서 데이터 저장 및 로드</span><span class="sxs-lookup"><span data-stu-id="de240-269">Save and load data within a component</span></span>

<span data-ttu-id="de240-270">브라우저 스토리지에 데이터를 로드하거나 저장해야 하는 구성 요소에서 [`@inject`](xref:mvc/views/razor#inject) 지시문을 사용하여 다음 중 하나의 인스턴스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="de240-271">선택은 사용하려는 브라우저 스토리지 위치에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="de240-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="de240-272">다음 예제에서는 `sessionStorage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="de240-273">구성 요소 대신 앱의 `_Imports.razor` 파일에 `@using` 지시문을 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="de240-274">`_Imports.razor` 파일을 사용하면 더 큰 앱 세그먼트나 전체 앱에서 네임스페이스를 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="de240-275">Blazor Server 프로젝트 템플릿에 기반한 앱의 `Counter` 구성 요소에서 `currentCount` 값을 유지하려면 `ProtectedSessionStore.SetAsync`를 사용하도록 `IncrementCount` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="de240-276">더 크고 현실적인 앱에서 개별 필드 스토리지는 가능성이 거의 없는 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="de240-277">앱에서 복합 상태를 포함하는 전체 모델 개체를 저장할 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="de240-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="de240-278">`ProtectedSessionStore`는 JSON 데이터를 자동으로 직렬화 및 역직렬화하여 복잡한 상태 개체를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="de240-279">위의 코드 예제에서 `currentCount` 데이터는 사용자 브라우저에 `sessionStorage['count']`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="de240-280">데이터는 일반 텍스트로 저장되지 않고 ASP.NET Core의 데이터 보호를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="de240-281">브라우저의 개발자 콘솔에서 `sessionStorage['count']`를 평가하는 경우 암호화된 데이터를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="de240-282">사용자가 새로운 회로에 있는 경우를 포함하여 사용자가 나중에 `Counter` 구성 요소로 돌아오는 경우 `currentCount` 데이터를 복구하려면 `ProtectedSessionStore.GetAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="de240-283">구성 요소의 매개 변수에 탐색 상태가 포함된 경우, `ProtectedSessionStore.GetAsync`를 호출하고 `null`이 아닌 결과를 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>가 아니라 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="de240-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>는 구성 요소를 처음 인스턴스화할 때 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="de240-285">나중에 사용자가 동일한 페이지를 유지하면서 다른 URL로 이동하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>가 다시 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="de240-286">자세한 내용은 <xref:blazor/components/lifecycle>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="de240-287">이 섹션의 예제는 서버에서 미리 렌더링을 사용하지 않는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="de240-288">미리 렌더링을 사용하도록 설정하면 구성 요소가 미리 렌더링되고 있기 때문에 JavaScript interop 호출을 실행할 수 없음을 설명하는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="de240-289">미리 렌더링을 사용하지 않도록 설정하거나, 미리 렌더링 작업을 위한 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="de240-290">미리 렌더링 작업을 위한 코드 작성 방법에 대한 자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="de240-291">로드 상태 처리</span><span class="sxs-lookup"><span data-stu-id="de240-291">Handle the loading state</span></span>

<span data-ttu-id="de240-292">브라우저 스토리지는 네트워크 연결을 통해 비동기로 액세스되므로 데이터가 로드되고 구성 요소에서 사용할 수 있으려면 항상 일정 시간이 지나야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="de240-293">최상의 결과를 얻으려면 빈 데이터나 기본 데이터를 표시하는 대신, 로드가 진행되는 동안 로드 상태 메시지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="de240-294">한 가지 방법은 데이터가 아직 로드 중임을 뜻하는 `null`인지 여부를 추적하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="de240-295">기본 `Counter` 구성 요소에서 개수는 `int`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="de240-296">[형식(`int`)에 물음표(`?`)를 추가하여 `currentCount`를 null 허용](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="de240-297">개수 및 **`Increment`** 단추를 무조건 표시하는 대신 <xref:System.Nullable%601.HasValue%2A>를 선택하여 데이터가 로드된 경우에만 해당 요소를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="de240-298">미리 렌더링 처리</span><span class="sxs-lookup"><span data-stu-id="de240-298">Handle prerendering</span></span>

<span data-ttu-id="de240-299">미리 렌더링 과정과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-299">During prerendering:</span></span>

* <span data-ttu-id="de240-300">사용자 브라우저에 대한 대화형 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="de240-301">브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="de240-302">미리 렌더링하는 동안 `localStorage` 또는 `sessionStorage`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="de240-303">구성 요소가 스토리지와 상호 작용하려고 하면 구성 요소가 미리 렌더링되고 있기 때문에 JavaScript interop 호출을 실행할 수 없음을 설명하는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="de240-304">오류를 해결하는 한 가지 방법은 미리 렌더링을 사용하지 않도록 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="de240-305">앱에서 브라우저 기반 스토리지를 많이 사용하는 경우, 일반적으로 이 옵션을 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="de240-306">`localStorage` 또는 `sessionStorage`를 사용할 수 있어야 앱에서 유용한 콘텐츠를 미리 렌더링할 수 있기 때문에 미리 렌더링은 복잡성만 추가하고 앱에 도움이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="de240-307">미리 렌더링을 사용하지 않도록 설정하려면 `Pages/_Host.cshtml` 파일을 열고 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode` 특성을 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="de240-308">`localStorage` 또는 `sessionStorage`를 사용하지 않는 다른 페이지에는 미리 렌더링이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="de240-309">미리 렌더링을 유지하려면 브라우저가 회로에 연결될 때까지 로드 작업을 연기합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="de240-310">다음은 카운터 값을 저장하기 위한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="de240-311">상태 유지를 공통 위치로 추출</span><span class="sxs-lookup"><span data-stu-id="de240-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="de240-312">많은 구성 요소가 브라우저 기반 스토리지를 사용하는 경우 상태 제공자 코드를 여러 번 다시 구현하면 코드 중복이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="de240-313">코드 중복을 방지하는 한 가지 옵션은 상태 제공자 논리를 캡슐화하는 ‘상태 제공자 부모 구성 요소’를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="de240-314">자식 구성 요소는 상태 지속성 메커니즘과 관계없이 영구 데이터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="de240-315">다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 `sessionStorage`에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="de240-316">`CounterStateProvider` 구성 요소는 로드가 완료될 때까지 자식 콘텐츠를 렌더링하지 않고 로드 단계를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="de240-317">`CounterStateProvider` 구성 요소를 사용하려면 카운터 상태에 액세스해야 하는 다른 모든 구성 요소를 이 구성 요소 인스턴스로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="de240-318">앱의 모든 구성 요소가 상태에 액세스할 수 있게 하려면 `App` 구성 요소(`App.razor`)의 <xref:Microsoft.AspNetCore.Components.Routing.Router>를 `CounterStateProvider` 구성 요소로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="de240-319">래핑된 구성 요소는 영구 카운터 상태를 받고 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="de240-320">다음 `Counter` 구성 요소는 패턴을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="de240-321">위의 구성 요소는 `ProtectedBrowserStorage`와 상호 작용하는 데 필요하지 않으며, “로드” 단계를 처리하지도 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="de240-322">앞에서 설명한 대로 미리 렌더링을 처리하기 위해, 카운터 데이터를 사용하는 모든 구성 요소가 자동으로 미리 렌더링을 사용하도록 `CounterStateProvider`를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="de240-323">자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="de240-324">일반적으로 다음과 같은 경우 상태 제공자 부모 구성 요소 패턴을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="de240-325">여러 구성 요소에서 상태를 사용하려는 경우</span><span class="sxs-lookup"><span data-stu-id="de240-325">To consume state across many components.</span></span>
* <span data-ttu-id="de240-326">유지할 최상위 상태 개체가 하나뿐인 경우</span><span class="sxs-lookup"><span data-stu-id="de240-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="de240-327">여러 다른 상태 개체를 유지하고 서로 다른 위치에 있는 여러 개체 하위 집합을 사용하려면 전역적으로 상태를 유지하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="de240-328">보호된 브라우저 스토리지 실험적 NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="de240-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="de240-329">ASP.NET Core 보호된 브라우저 스토리지는 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 및 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="de240-330">`Microsoft.AspNetCore.ProtectedBrowserStorage`는 지원되지 않는 실험적 패키지로, 프로덕션 사용에는 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="de240-331">이 패키지는 ASP.NET Core 3.1 Blazor Server 앱에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="de240-332">구성</span><span class="sxs-lookup"><span data-stu-id="de240-332">Configuration</span></span>

1. <span data-ttu-id="de240-333">[`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="de240-334">`Pages/_Host.cshtml` 파일에서 닫는 `</body>` 태그 안에 다음 스크립트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="de240-335">`Startup.ConfigureServices`에서 `AddProtectedBrowserStorage`를 호출하여 서비스 컬렉션에 `localStorage` 및 `sessionStorage` 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="de240-336">구성 요소 내에서 데이터 저장 및 로드</span><span class="sxs-lookup"><span data-stu-id="de240-336">Save and load data within a component</span></span>

<span data-ttu-id="de240-337">브라우저 스토리지에 데이터를 로드하거나 저장해야 하는 구성 요소에서 [`@inject`](xref:mvc/views/razor#inject) 지시문을 사용하여 다음 중 하나의 인스턴스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="de240-338">선택은 사용하려는 브라우저 스토리지 위치에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="de240-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="de240-339">다음 예제에서는 `sessionStorage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="de240-340">구성 요소 대신 `_Imports.razor` 파일에 `@using` 문을 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="de240-341">`_Imports.razor` 파일을 사용하면 더 큰 앱 세그먼트나 전체 앱에서 네임스페이스를 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="de240-342">Blazor Server 프로젝트 템플릿에 기반한 앱의 `Counter` 구성 요소에서 `currentCount` 값을 유지하려면 `ProtectedSessionStore.SetAsync`를 사용하도록 `IncrementCount` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="de240-343">더 크고 현실적인 앱에서 개별 필드 스토리지는 가능성이 거의 없는 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="de240-344">앱에서 복합 상태를 포함하는 전체 모델 개체를 저장할 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="de240-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="de240-345">`ProtectedSessionStore`는 JSON 데이터를 자동으로 직렬화 및 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="de240-346">위의 코드 예제에서 `currentCount` 데이터는 사용자 브라우저에 `sessionStorage['count']`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="de240-347">데이터는 일반 텍스트로 저장되지 않고 ASP.NET Core의 데이터 보호를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="de240-348">브라우저의 개발자 콘솔에서 `sessionStorage['count']`를 평가하는 경우 암호화된 데이터를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="de240-349">사용자가 완전히 새로운 회로에 있는 경우를 포함하여 사용자가 나중에 `Counter` 구성 요소로 돌아오는 경우 `currentCount` 데이터를 복구하려면 `ProtectedSessionStore.GetAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="de240-350">구성 요소의 매개 변수에 탐색 상태가 포함된 경우, `ProtectedSessionStore.GetAsync`를 호출하고 결과를 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>가 아닌 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="de240-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>는 구성 요소를 처음 인스턴스화할 때 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="de240-352">나중에 사용자가 동일한 페이지를 유지하면서 다른 URL로 이동하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>가 다시 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="de240-353">자세한 내용은 <xref:blazor/components/lifecycle>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="de240-354">이 섹션의 예제는 서버에서 미리 렌더링을 사용하지 않는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="de240-355">미리 렌더링을 사용하도록 설정하면 구성 요소가 미리 렌더링되고 있기 때문에 JavaScript interop 호출을 실행할 수 없음을 설명하는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="de240-356">미리 렌더링을 사용하지 않도록 설정하거나, 미리 렌더링 작업을 위한 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="de240-357">미리 렌더링 작업을 위한 코드 작성 방법에 대한 자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="de240-358">로드 상태 처리</span><span class="sxs-lookup"><span data-stu-id="de240-358">Handle the loading state</span></span>

<span data-ttu-id="de240-359">브라우저 스토리지는 네트워크 연결을 통해 비동기로 액세스되므로 데이터가 로드되고 구성 요소에서 사용할 수 있으려면 항상 일정 시간이 지나야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="de240-360">최상의 결과를 얻으려면 빈 데이터나 기본 데이터를 표시하는 대신, 로드가 진행되는 동안 로드 상태 메시지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="de240-361">한 가지 방법은 데이터가 아직 로드 중임을 뜻하는 `null`인지 여부를 추적하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="de240-362">기본 `Counter` 구성 요소에서 개수는 `int`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="de240-363">[형식(`int`)에 물음표(`?`)를 추가하여 `currentCount`를 null 허용](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="de240-364">개수 및 **`Increment`** 단추를 무조건 표시하는 대신, 데이터가 로드된 경우에만 해당 요소를 표시하도록 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="de240-365">미리 렌더링 처리</span><span class="sxs-lookup"><span data-stu-id="de240-365">Handle prerendering</span></span>

<span data-ttu-id="de240-366">미리 렌더링 과정과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-366">During prerendering:</span></span>

* <span data-ttu-id="de240-367">사용자 브라우저에 대한 대화형 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="de240-368">브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="de240-369">미리 렌더링하는 동안 `localStorage` 또는 `sessionStorage`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="de240-370">구성 요소가 스토리지와 상호 작용하려고 하면 구성 요소가 미리 렌더링되고 있기 때문에 JavaScript interop 호출을 실행할 수 없음을 설명하는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="de240-371">오류를 해결하는 한 가지 방법은 미리 렌더링을 사용하지 않도록 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="de240-372">앱에서 브라우저 기반 스토리지를 많이 사용하는 경우, 일반적으로 이 옵션을 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="de240-373">`localStorage` 또는 `sessionStorage`를 사용할 수 있어야 앱에서 유용한 콘텐츠를 미리 렌더링할 수 있기 때문에 미리 렌더링은 복잡성만 추가하고 앱에 도움이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="de240-374">미리 렌더링을 사용하지 않도록 설정하려면 `Pages/_Host.cshtml` 파일을 열고 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode` 특성을 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="de240-375">`localStorage` 또는 `sessionStorage`를 사용하지 않는 다른 페이지에는 미리 렌더링이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="de240-376">미리 렌더링을 유지하려면 브라우저가 회로에 연결될 때까지 로드 작업을 연기합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="de240-377">다음은 카운터 값을 저장하기 위한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="de240-378">상태 유지를 공통 위치로 추출</span><span class="sxs-lookup"><span data-stu-id="de240-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="de240-379">많은 구성 요소가 브라우저 기반 스토리지를 사용하는 경우 상태 제공자 코드를 여러 번 다시 구현하면 코드 중복이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="de240-380">코드 중복을 방지하는 한 가지 옵션은 상태 제공자 논리를 캡슐화하는 ‘상태 제공자 부모 구성 요소’를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de240-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="de240-381">자식 구성 요소는 상태 지속성 메커니즘과 관계없이 영구 데이터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="de240-382">다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 `sessionStorage`에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="de240-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="de240-383">`CounterStateProvider` 구성 요소는 로드가 완료될 때까지 자식 콘텐츠를 렌더링하지 않고 로드 단계를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="de240-384">`CounterStateProvider` 구성 요소를 사용하려면 카운터 상태에 액세스해야 하는 다른 모든 구성 요소를 이 구성 요소 인스턴스로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="de240-385">앱의 모든 구성 요소가 상태에 액세스할 수 있게 하려면 `App` 구성 요소(`App.razor`)의 <xref:Microsoft.AspNetCore.Components.Routing.Router>를 `CounterStateProvider` 구성 요소로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="de240-386">래핑된 구성 요소는 영구 카운터 상태를 받고 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="de240-387">다음 `Counter` 구성 요소는 패턴을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="de240-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="de240-388">위의 구성 요소는 `ProtectedBrowserStorage`와 상호 작용하는 데 필요하지 않으며, “로드” 단계를 처리하지도 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="de240-389">앞에서 설명한 대로 미리 렌더링을 처리하기 위해, 카운터 데이터를 사용하는 모든 구성 요소가 자동으로 미리 렌더링을 사용하도록 `CounterStateProvider`를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="de240-390">자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="de240-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="de240-391">일반적으로 다음과 같은 경우 ‘상태 제공자 부모 구성 요소’ 패턴을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="de240-392">여러 구성 요소에서 상태를 사용하려는 경우</span><span class="sxs-lookup"><span data-stu-id="de240-392">To consume state across many components.</span></span>
* <span data-ttu-id="de240-393">유지할 최상위 상태 개체가 하나뿐인 경우</span><span class="sxs-lookup"><span data-stu-id="de240-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="de240-394">여러 다른 상태 개체를 유지하고 서로 다른 위치에 있는 여러 개체 하위 집합을 사용하려면 전역적으로 상태를 유지하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="de240-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="de240-395">메모리 내 상태 컨테이너 서비스</span><span class="sxs-lookup"><span data-stu-id="de240-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
