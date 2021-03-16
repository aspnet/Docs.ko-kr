---
title: ASP.NET Core에서 구성 요소 테스트Blazor
author: guardrex
description: Blazor 앱에서 구성 요소를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 1a7b1114934f4fe7006d60bdbd0f06792d2c6935
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394553"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="f3f1b-103">ASP.NET Core에서 구성 요소 테스트Blazor</span><span class="sxs-lookup"><span data-stu-id="f3f1b-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="f3f1b-104">작성자: [Egil Hansen](https://egilhansen.com/)</span><span class="sxs-lookup"><span data-stu-id="f3f1b-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="f3f1b-105">테스트는 안정적이고 유지 관리 가능한 소프트웨어를 구축하는 데 있어 중요한 측면입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="f3f1b-106">Blazor 구성 요소를 테스트하기 위해 CUT(‘테스트 중인 구성 요소’)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="f3f1b-107">테스트에 관련된 입력으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="f3f1b-108">수행되는 테스트 형식에 따라 상호 작용 또는 수정이 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="f3f1b-109">예를 들어 단추에 대한 `onclick` 이벤트와 같은 이벤트 처리기를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="f3f1b-110">예상 값이 검사되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="f3f1b-111">테스트 접근 방식</span><span class="sxs-lookup"><span data-stu-id="f3f1b-111">Test approaches</span></span>

<span data-ttu-id="f3f1b-112">Blazor 구성 요소를 테스트하는 두 가지 일반적인 접근 방식은 E2E(엔드투엔드) 테스트와 유닛 테스트입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="f3f1b-113">**유닛 테스트**: [단위 테스트](/dotnet/core/testing/)는 다음을 제공하는 유닛 테스트 라이브러리를 사용하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="f3f1b-114">구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="f3f1b-114">Component rendering.</span></span>
  * <span data-ttu-id="f3f1b-115">구성 요소 출력 및 상태 검사</span><span class="sxs-lookup"><span data-stu-id="f3f1b-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="f3f1b-116">이벤트 처리기 및 수명 주기 메서드 트리거</span><span class="sxs-lookup"><span data-stu-id="f3f1b-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="f3f1b-117">구성 요소 동작이 올바른 어설션</span><span class="sxs-lookup"><span data-stu-id="f3f1b-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="f3f1b-118">[bUnit](https://github.com/egil/bUnit)은 Razor 구성 요소 유닛 테스트를 사용하도록 지정하는 라이브러리의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="f3f1b-119">**E2E 테스트**: Test Runner는 CUT를 포함하는 Blazor 앱을 실행하고 브라우저 인스턴스를 자동화합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="f3f1b-120">테스트 도구는 브라우저를 통해 CUT를 검사하고 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="f3f1b-121">[Selenium](https://github.com/SeleniumHQ/selenium)은 Blazor 앱에서 사용할 수 있는 E2E 테스트 프레임워크의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="f3f1b-122">유닛 테스트에는 Blazor 구성 요소(Razor/C#)만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="f3f1b-123">서비스 및 JS interop과 같은 외부 종속성은 모의되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="f3f1b-124">E2E 테스트에서 Blazor 구성 요소와 모든 보조 인프라는 CSS, JS, DOM, 브라우저 API를 비롯한 테스트의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="f3f1b-125">‘테스트 범위’에서는 테스트의 범위를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="f3f1b-126">테스트 범위는 일반적으로 테스트의 속도에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="f3f1b-127">단위 테스트는 앱 하위 시스템의 하위 집합에서 실행되며 일반적으로 몇 밀리초 내에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="f3f1b-128">광범위한 앱 하위 시스템 그룹을 테스트하는 E2E 테스트는 완료하는 데 몇 초 정도 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="f3f1b-129">또한 유닛 테스트를 사용하면 CUT의 인스턴스에 액세스하여 구성 요소의 내부 상태를 검사 및 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="f3f1b-130">일반적으로 E2E 테스트에서는 이렇게 할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="f3f1b-131">구성 요소의 환경과 관련하여 E2E 테스트에서는 확인이 시작되기 전에 필요한 환경 상태에 도달했는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="f3f1b-132">그렇지 않으면 결과를 예측할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="f3f1b-133">유닛 테스트에서는 CUT 렌더링과 테스트의 수명 주기가 통합되어 테스트 안정성이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="f3f1b-134">E2E 테스트는 여러 프로세스, 네트워크 및 디스크 I/O, 기타 하위 시스템 작업을 포함하여 테스트 안정성이 저하되는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="f3f1b-135">단위 테스트는 일반적으로 이러한 종류의 문제로부터 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="f3f1b-136">다음 표에서는 두 테스트 접근 방식의 차이점을 요약하여 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="f3f1b-137">기능</span><span class="sxs-lookup"><span data-stu-id="f3f1b-137">Capability</span></span>                       | <span data-ttu-id="f3f1b-138">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-138">Unit testing</span></span>                     | <span data-ttu-id="f3f1b-139">E2E 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="f3f1b-140">테스트 범위</span><span class="sxs-lookup"><span data-stu-id="f3f1b-140">Test scope</span></span>                       | <span data-ttu-id="f3f1b-141">Blazor 구성 요소(Razor/C#)만</span><span class="sxs-lookup"><span data-stu-id="f3f1b-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="f3f1b-142">Blazor 구성 요소(Razor/C#) 및 CSS/JS</span><span class="sxs-lookup"><span data-stu-id="f3f1b-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="f3f1b-143">테스트 실행 시간</span><span class="sxs-lookup"><span data-stu-id="f3f1b-143">Test execution time</span></span>              | <span data-ttu-id="f3f1b-144">밀리초</span><span class="sxs-lookup"><span data-stu-id="f3f1b-144">Milliseconds</span></span>                     | <span data-ttu-id="f3f1b-145">초</span><span class="sxs-lookup"><span data-stu-id="f3f1b-145">Seconds</span></span>                                 |
| <span data-ttu-id="f3f1b-146">구성 요소 인스턴스에 대한 액세스</span><span class="sxs-lookup"><span data-stu-id="f3f1b-146">Access to the component instance</span></span> | <span data-ttu-id="f3f1b-147">예</span><span class="sxs-lookup"><span data-stu-id="f3f1b-147">Yes</span></span>                              | <span data-ttu-id="f3f1b-148">아니요</span><span class="sxs-lookup"><span data-stu-id="f3f1b-148">No</span></span>                                      |
| <span data-ttu-id="f3f1b-149">환경에 민감</span><span class="sxs-lookup"><span data-stu-id="f3f1b-149">Sensitive to the environment</span></span>     | <span data-ttu-id="f3f1b-150">아니요</span><span class="sxs-lookup"><span data-stu-id="f3f1b-150">No</span></span>                               | <span data-ttu-id="f3f1b-151">예</span><span class="sxs-lookup"><span data-stu-id="f3f1b-151">Yes</span></span>                                     |
| <span data-ttu-id="f3f1b-152">안정성</span><span class="sxs-lookup"><span data-stu-id="f3f1b-152">Reliability</span></span>                      | <span data-ttu-id="f3f1b-153">안정성 향상</span><span class="sxs-lookup"><span data-stu-id="f3f1b-153">More reliable</span></span>                    | <span data-ttu-id="f3f1b-154">안정성 감소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="f3f1b-155">가장 적절한 테스트 접근 방식 선택</span><span class="sxs-lookup"><span data-stu-id="f3f1b-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="f3f1b-156">수행할 테스트 형식을 선택할 때 시나리오를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="f3f1b-157">다음 표에서는 몇 가지 고려 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="f3f1b-158">시나리오</span><span class="sxs-lookup"><span data-stu-id="f3f1b-158">Scenario</span></span> | <span data-ttu-id="f3f1b-159">제안된 방법</span><span class="sxs-lookup"><span data-stu-id="f3f1b-159">Suggested approach</span></span> | <span data-ttu-id="f3f1b-160">설명</span><span class="sxs-lookup"><span data-stu-id="f3f1b-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="f3f1b-161">JS interop 논리가 없는 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-161">Component without JS interop logic</span></span> | <span data-ttu-id="f3f1b-162">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-162">Unit testing</span></span> | <span data-ttu-id="f3f1b-163">Blazor 구성 요소에서 JS interop에 대한 종속성이 없는 경우 JS 또는 DOM API에 액세스하지 않고 구성 요소를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="f3f1b-164">이 시나리오에서는 유닛 테스트를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="f3f1b-165">간단한 JS interop 논리를 포함하는 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="f3f1b-166">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-166">Unit testing</span></span> | <span data-ttu-id="f3f1b-167">구성 요소에서 DOM을 쿼리하거나 JS interop을 통해 애니메이션을 트리거하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="f3f1b-168"><xref:Microsoft.JSInterop.IJSRuntime> 인터페이스를 통해 JS를 모의하는 것이 간단하므로 이 시나리오에서는 일반적으로 유닛 테스트를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="f3f1b-169">복잡한 JS 코드에 종속되는 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="f3f1b-170">유닛 테스트 및 개별 JS 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="f3f1b-171">구성 요소에서 JS interop을 사용하여 크거나 복잡한 JS 라이브러리를 호출하지만 Blazor 구성 요소와 JS 라이브러리 간의 상호 작용이 간단한 경우, 구성 요소와 JS 라이브러리 또는 코드를 두 개별 부분으로 처리하고 각 부분을 개별적으로 테스트하는 것이 가장 좋은 접근 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="f3f1b-172">유닛 테스트 라이브러리를 사용하여 Blazor 구성 요소를 테스트하고 JS 테스트 라이브러리를 사용하여 JS를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="f3f1b-173">브라우저 DOM의 JS 조작에 종속되는 논리를 포함하는 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="f3f1b-174">E2E 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-174">E2E testing</span></span> | <span data-ttu-id="f3f1b-175">구성 요소의 기능이 JS와 해당 DOM 조작에 종속되는 경우 E2E 테스트에서 JS와 Blazor 코드를 함께 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="f3f1b-176">이 방법은 Blazor 프레임워크 개발자가 긴밀하게 결합된 C# 및 JS 코드를 포함하는 Blazor의 브라우저 렌더링 논리를 사용하여 수행하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="f3f1b-177">C# 및 JS 코드는 함께 작동하여 브라우저에서 Blazor 구성 요소를 올바르게 렌더링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="f3f1b-178">모의하기 어려운 종속 항목을 포함하며 타사 구성 요소 라이브러리에 종속되는 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f3f1b-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="f3f1b-179">E2E 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-179">E2E testing</span></span> | <span data-ttu-id="f3f1b-180">구성 요소의 기능이 JS interop과 같은 모의하기 어려운 종속 항목을 포함하는 타사 구성 요소 라이브러리에 종속되는 경우 E2E 테스트는 구성 요소를 테스트하는 유일한 옵션일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="f3f1b-181">bUnit을 사용하여 구성 요소 테스트</span><span class="sxs-lookup"><span data-stu-id="f3f1b-181">Test components with bUnit</span></span>

<span data-ttu-id="f3f1b-182">Blazor에 대한 공식적인 Microsoft 테스트 프레임워크는 없지만 커뮤니티 기반 프로젝트 [bUnit](https://github.com/egil/bUnit)은 Blazor 구성 요소를 단위 테스트하는 편리한 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="f3f1b-183">bUnit은 타사 테스트 라이브러리이며 Microsoft에서 지원하거나 유지 관리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="f3f1b-184">bUnit은 [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), [xUnit](https://xunit.github.io/)과 같은 범용 테스트 프레임워크에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="f3f1b-185">이러한 테스트 프레임워크는 bUnit 테스트가 일반적인 단위 테스트처럼 보이고 느껴지도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="f3f1b-186">범용 테스트 프레임워크와 통합된 bUnit 테스트는 일반적으로 다음을 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="f3f1b-187">[Visual Studio 테스트 탐색기](/visualstudio/test/run-unit-tests-with-test-explorer)</span><span class="sxs-lookup"><span data-stu-id="f3f1b-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="f3f1b-188">명령 셸의 [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI 명령</span><span class="sxs-lookup"><span data-stu-id="f3f1b-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="f3f1b-189">자동화된 DevOps 테스트 파이프라인</span><span class="sxs-lookup"><span data-stu-id="f3f1b-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="f3f1b-190">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="f3f1b-191">지침은 테스트 프레임워크 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="f3f1b-192">다음은 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)을 기반으로 하는 앱의 `Counter` 구성 요소에 대한 bUnit 테스트 구조를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a [Blazor project template](xref:blazor/project-structure).</span></span> <span data-ttu-id="f3f1b-193">`Counter` 구성 요소는 페이지에서 단추를 선택하는 사용자를 기반으로 카운터를 표시하고 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="f3f1b-194">다음 bUnit 테스트에서는 단추를 선택할 때 CUT의 카운터가 올바르게 증가하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="f3f1b-195">다음은 테스트의 각 단계에서 수행되는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="f3f1b-196">‘정렬’: `Counter` 구성 요소는 bUnit의 `TestContext`를 사용하여 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="f3f1b-197">CUT의 단락 요소(`<p>`)를 찾아서 `paraElm`에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="f3f1b-198">‘실행’: 단추의 요소(`<button>`)를 찾은 다음 `Click`을 호출하여 선택합니다. 그러면 카운터가 증가하고 단락 태그(`<p>`)의 내용이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="f3f1b-199">단락 요소 텍스트 콘텐츠는 `TextContent`를 호출하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="f3f1b-200">‘어설션’: 텍스트 내용에 대해 `MarkupMatches`를 호출하여 예상 문자열(`Current count: 1`)과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="f3f1b-201">`MarkupMatches` 어설션 메서드는 일반 문자열 비교 어설션(예: `Assert.Equal("Current count: 1", paraElmText);`)과 다르며 `MarkupMatches`는 입력 및 예상 HTML 태그에 대한 의미 체계를 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="f3f1b-202">의미 체계 비교에서는 HTML 의미 체계를 인식하므로 중요하지 않은 공백과 같은 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="f3f1b-203">따라서 더 안정적으로 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-203">This results in more stable tests.</span></span> <span data-ttu-id="f3f1b-204">자세한 내용은 [의미 체계 HTML 비교 사용자 지정](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3f1b-205">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f3f1b-205">Additional resources</span></span>

* <span data-ttu-id="f3f1b-206">[bUnit 시작하기](https://bunit.egilhansen.com/docs/getting-started/): bUnit 지침에는 테스트 프로젝트를 만들고, 프레임워크 패키지를 참조하고, 테스트를 빌드 및 실행하는 방법에 대한 지침이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3f1b-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
