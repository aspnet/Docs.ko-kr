---
title: 'ASP.NET Core :::no-loc(Blazor)::: 고급 시나리오'
author: guardrex
description: 수동 RenderTreeBuilder 논리를 앱에 통합하는 방법을 포함하여 :::no-loc(Blazor):::의 고급 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056558"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="576e8-103">ASP.NET Core :::no-loc(Blazor)::: 고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="576e8-103">ASP.NET Core :::no-loc(Blazor)::: advanced scenarios</span></span>

<span data-ttu-id="576e8-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="576e8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="576e8-105">:::no-loc(Blazor Server)::: 회로 처리기</span><span class="sxs-lookup"><span data-stu-id="576e8-105">:::no-loc(Blazor Server)::: circuit handler</span></span>

<span data-ttu-id="576e8-106">:::no-loc(Blazor Server):::에서는 코드를 통해 사용자 회로 상태 변경 시 코드를 실행할 수 있게 해주는 ‘회로 처리기’를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-106">:::no-loc(Blazor Server)::: allows code to define a *circuit handler* , which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="576e8-107">회로 처리기는 `CircuitHandler`에서 파생되고 앱의 서비스 컨테이너에 클래스를 등록하여 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="576e8-108">다음 회로 처리기 예제에서는 열린 :::no-loc(SignalR)::: 연결을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-108">The following example of a circuit handler tracks open :::no-loc(SignalR)::: connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="576e8-109">회로 처리기는 DI를 사용하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="576e8-110">회로 인스턴스별로 범위가 지정된 인스턴스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="576e8-111">위의 예제에서 `TrackingCircuitHandler`를 사용할 경우, 모든 회로의 상태를 추적해야 하기 때문에 singleton 서비스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="576e8-112">사용자 지정 회로 처리기의 메서드에서 처리되지 않은 예외가 throw될 경우 이 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="576e8-113">처리기의 코드 또는 호출된 메서드에서 예외를 허용하려면 오류 처리 및 로깅 기능을 사용하여 하나 이상의 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 코드를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="576e8-114">사용자가 연결을 끊었으며 프레임워크에서 회로 상태를 정리하고 있어서 회로가 종료될 경우 프레임워크는 회로의 DI 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="576e8-115">범위를 삭제하면 <xref:System.IDisposable?displayProperty=fullName>을 구현하는, 회로 범위가 지정된 DI 서비스가 모두 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="576e8-116">삭제하는 동안 처리되지 않은 예외를 throw하는 DI 서비스가 있을 경우 프레임워크에서 예외를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="576e8-117">수동 RenderTreeBuilder 논리</span><span class="sxs-lookup"><span data-stu-id="576e8-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="576e8-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>는 C# 코드를 사용한 구성 요소 수동 빌드를 포함하여 구성 요소와 요소를 조작하기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="576e8-119"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>를 사용하여 구성 요소를 만드는 것은 고급 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="576e8-120">구성 요소의 형식이 잘못된 경우(예: 닫히지 않은 태그) 정의되지 않은 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="576e8-121">다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="576e8-122">다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 구성 요소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="576e8-123">시퀀스 번호를 사용하는 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 메서드에서 시퀀스 번호는 소스 코드 줄 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="576e8-124">:::no-loc(Blazor)::: diff 알고리즘은 개별 호출이 아니라 개별 코드 줄에 해당하는 시퀀스 번호를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-124">The :::no-loc(Blazor)::: difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="576e8-125"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 메서드를 사용하여 구성 요소를 만드는 경우 시퀀스 번호의 인수를 하드 코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="576e8-126">**계산 또는 카운터를 사용하여 시퀀스 번호를 생성하면 성능이 저하될 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="576e8-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="576e8-127">자세한 내용은 [시퀀스 번호는 실행 순서가 아니라 코드 줄 번호와 관련이 있음](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="576e8-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="576e8-128">`BuiltContent` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="576e8-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="576e8-129"><xref:Microsoft.AspNetCore.Components.RenderTree>의 형식을 사용하여 렌더링 작업 ‘결과’를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="576e8-130">해당 형식은 :::no-loc(Blazor)::: 프레임워크 구현의 내부 세부 정보이며,</span><span class="sxs-lookup"><span data-stu-id="576e8-130">These are internal details of the :::no-loc(Blazor)::: framework implementation.</span></span> <span data-ttu-id="576e8-131">‘불안정’하고 이후 릴리스에서 변경될 수 있는 것으로 간주되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="576e8-132">시퀀스 번호는 실행 순서가 아니라 코드 줄 번호와 관련이 있음</span><span class="sxs-lookup"><span data-stu-id="576e8-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="576e8-133">:::no-loc(Razor)::: 구성 요소 파일(`.razor`)은 항상 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-133">:::no-loc(Razor)::: component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="576e8-134">컴파일 단계를 사용하여 런타임에 앱 성능을 개선하는 정보를 삽입할 수 있으므로 컴파일은 코드 해석보다 잠재적 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="576e8-135">해당 개선 사항의 주요 예로 ‘시퀀스 번호’가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="576e8-136">시퀀스 번호는 정렬된 개별 코드 줄에서 생성된 해당 출력을 런타임에 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="576e8-137">런타임은 이 정보를 사용하여 선형 시간으로 효율적인 트리 diff를 생성하며, 일반적인 트리 diff 알고리즘에서 가능한 속도보다 훨씬 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="576e8-138">다음 :::no-loc(Razor)::: 구성 요소(`.razor`) 파일을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="576e8-138">Consider the following :::no-loc(Razor)::: component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="576e8-139">위의 코드는 다음과 같이 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="576e8-140">코드를 처음 실행할 때 `someFlag`가 `true`인 경우 작성기는 다음을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="576e8-141">순서</span><span class="sxs-lookup"><span data-stu-id="576e8-141">Sequence</span></span> | <span data-ttu-id="576e8-142">형식</span><span class="sxs-lookup"><span data-stu-id="576e8-142">Type</span></span>      | <span data-ttu-id="576e8-143">데이터</span><span class="sxs-lookup"><span data-stu-id="576e8-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="576e8-144">0</span><span class="sxs-lookup"><span data-stu-id="576e8-144">0</span></span>        | <span data-ttu-id="576e8-145">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-145">Text node</span></span> | <span data-ttu-id="576e8-146">첫째</span><span class="sxs-lookup"><span data-stu-id="576e8-146">First</span></span>  |
| <span data-ttu-id="576e8-147">1</span><span class="sxs-lookup"><span data-stu-id="576e8-147">1</span></span>        | <span data-ttu-id="576e8-148">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-148">Text node</span></span> | <span data-ttu-id="576e8-149">Second</span><span class="sxs-lookup"><span data-stu-id="576e8-149">Second</span></span> |

<span data-ttu-id="576e8-150">`someFlag`가 `false`로 전환되었으며, 태그를 다시 렌더링했다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="576e8-151">이번에는 작성기가 다음을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-151">This time, the builder receives:</span></span>

| <span data-ttu-id="576e8-152">순서</span><span class="sxs-lookup"><span data-stu-id="576e8-152">Sequence</span></span> | <span data-ttu-id="576e8-153">형식</span><span class="sxs-lookup"><span data-stu-id="576e8-153">Type</span></span>       | <span data-ttu-id="576e8-154">데이터</span><span class="sxs-lookup"><span data-stu-id="576e8-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="576e8-155">1</span><span class="sxs-lookup"><span data-stu-id="576e8-155">1</span></span>        | <span data-ttu-id="576e8-156">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-156">Text node</span></span>  | <span data-ttu-id="576e8-157">Second</span><span class="sxs-lookup"><span data-stu-id="576e8-157">Second</span></span> |

<span data-ttu-id="576e8-158">런타임은 diff를 통해 시퀀스 `0`의 항목이 제거된 것을 확인하고 다음과 같은 간단한 *편집 스크립트* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* :</span></span>

* <span data-ttu-id="576e8-159">첫 번째 텍스트 노드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="576e8-160">프로그래밍 방식으로 시퀀스 번호를 생성할 경우 발생하는 문제</span><span class="sxs-lookup"><span data-stu-id="576e8-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="576e8-161">대신, 다음과 같은 렌더링 트리 작성기 논리를 작성했다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="576e8-162">이제 첫 번째 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-162">Now, the first output is:</span></span>

| <span data-ttu-id="576e8-163">순서</span><span class="sxs-lookup"><span data-stu-id="576e8-163">Sequence</span></span> | <span data-ttu-id="576e8-164">형식</span><span class="sxs-lookup"><span data-stu-id="576e8-164">Type</span></span>      | <span data-ttu-id="576e8-165">데이터</span><span class="sxs-lookup"><span data-stu-id="576e8-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="576e8-166">0</span><span class="sxs-lookup"><span data-stu-id="576e8-166">0</span></span>        | <span data-ttu-id="576e8-167">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-167">Text node</span></span> | <span data-ttu-id="576e8-168">첫째</span><span class="sxs-lookup"><span data-stu-id="576e8-168">First</span></span>  |
| <span data-ttu-id="576e8-169">1</span><span class="sxs-lookup"><span data-stu-id="576e8-169">1</span></span>        | <span data-ttu-id="576e8-170">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-170">Text node</span></span> | <span data-ttu-id="576e8-171">Second</span><span class="sxs-lookup"><span data-stu-id="576e8-171">Second</span></span> |

<span data-ttu-id="576e8-172">이 결과는 이전 사례와 동일하므로 부정적인 이슈가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="576e8-173">두 번째 렌더링에서 `someFlag`는 `false`이고, 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="576e8-174">순서</span><span class="sxs-lookup"><span data-stu-id="576e8-174">Sequence</span></span> | <span data-ttu-id="576e8-175">형식</span><span class="sxs-lookup"><span data-stu-id="576e8-175">Type</span></span>      | <span data-ttu-id="576e8-176">데이터</span><span class="sxs-lookup"><span data-stu-id="576e8-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="576e8-177">0</span><span class="sxs-lookup"><span data-stu-id="576e8-177">0</span></span>        | <span data-ttu-id="576e8-178">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="576e8-178">Text node</span></span> | <span data-ttu-id="576e8-179">Second</span><span class="sxs-lookup"><span data-stu-id="576e8-179">Second</span></span> |

<span data-ttu-id="576e8-180">이번에는 diff 알고리즘에서 ‘두 가지’ 변경 사항이 발생했음을 확인하고, 다음과 같은 편집 스크립트를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="576e8-181">첫 번째 텍스트 노드의 값을 `Second`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="576e8-182">두 번째 텍스트 노드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-182">Remove the second text node.</span></span>

<span data-ttu-id="576e8-183">시퀀스 번호를 생성하면 원본 코드에서 `if/else` 분기 및 루프가 있던 위치에 대한 유용한 정보가 모두 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="576e8-184">이로 인해 diff의 길이가 이전보다 **두 배** 가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="576e8-185">여기서는 간단한 예제를 사용했지만,</span><span class="sxs-lookup"><span data-stu-id="576e8-185">This is a trivial example.</span></span> <span data-ttu-id="576e8-186">복잡하고 깊이 중첩된 구조와 특히 루프를 사용하는 보다 현실적인 사례에서는 일반적으로 성능 비용이 더 높습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="576e8-187">삽입 또는 제거된 루프 블록이나 분기를 즉시 확인하는 대신, diff 알고리즘은 렌더링 트리를 재귀적으로 깊이 반복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="576e8-188">이로 인해 이전 구조와 새 구조 간의 관계에 대한 잘못된 정보가 diff 알고리즘에 제공되기 때문에 일반적으로 긴 편집 스크립트를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="576e8-189">지침 및 결론</span><span class="sxs-lookup"><span data-stu-id="576e8-189">Guidance and conclusions</span></span>

* <span data-ttu-id="576e8-190">시퀀스 번호를 동적으로 생성하면 앱 성능이 저하됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="576e8-191">컴파일 시간에 캡처하지 않는 한, 필요한 정보가 없기 때문에 프레임워크에서 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="576e8-192">수동으로 구현된 긴 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리 블록을 작성하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="576e8-193">`.razor` 파일을 사용하고 컴파일러를 통해 시퀀스 번호를 처리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="576e8-194">수동 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 사용해야 하는 경우, 긴 코드 블록을 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 호출에 래핑된 작은 조각으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="576e8-195">영역마다 고유한 시퀀스 번호 공간이 있으므로, 각 영역 내에서 0(또는 다른 임의 숫자)부터 다시 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="576e8-196">시퀀스 번호를 하드 코딩한 경우, 시퀀스 번호의 값만 증가하면 diff 알고리즘을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="576e8-197">초기 값과 간격은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="576e8-198">한 가지 타당한 옵션은 코드 줄 번호를 시퀀스 번호로 사용하거나 0부터 시작하고 1씩, 100씩 또는 선호하는 간격만큼 늘리는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="576e8-199">:::no-loc(Blazor):::는 시퀀스 번호를 사용하는 반면, 다른 트리 diff UI 프레임워크는 시퀀스 번호를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-199">:::no-loc(Blazor)::: uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="576e8-200">diff는 시퀀스 번호를 사용할 때 훨씬 더 빠르며, :::no-loc(Blazor):::는 `.razor` 파일을 작성하는 개발자를 위해 시퀀스 번호를 자동으로 처리하는 컴파일 단계의 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="576e8-200">Diffing is far faster when sequence numbers are used, and :::no-loc(Blazor)::: has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
