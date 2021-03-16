---
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
ms.openlocfilehash: 1aa36c8d91dbd92485e85f223f2391303bebac42
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109713"
---
<span data-ttu-id="5fb6a-101">Blazor는 SPA(단일 페이지 애플리케이션) 클라이언트 쪽 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-101">Blazor is a single-page application (SPA) client-side framework.</span></span> <span data-ttu-id="5fb6a-102">브라우저는 앱의 호스트로 사용되므로 탐색 및 정적 자산에 대한 URI 요청을 기반으로 개별 Razor 구성 요소에 대한 처리 파이프라인 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-102">The browser serves as the app's host and thus acts as the processing pipeline for individual Razor components based on URI requests for navigation and static assets.</span></span> <span data-ttu-id="5fb6a-103">미들웨어 처리 파이프라인이 있는 서버에서 실행되는 ASP.NET Core 앱과 달리, 전역 오류 처리에 사용할 수 있는 Razor 구성 요소에 대한 요청을 처리하는 미들웨어 파이프라인이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-103">Unlike ASP.NET Core apps that run on the server with a middleware processing pipeline, there is no middleware pipeline that processes requests for Razor components that can be leveraged for global error handling.</span></span> <span data-ttu-id="5fb6a-104">그러나 앱은 오류 처리 구성 요소를 연계 값으로 사용하여 중앙 집중식으로 오류를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-104">However, an app can use an error processing component as a cascading value to process errors in a centralized way.</span></span>

<span data-ttu-id="5fb6a-105">다음 `Error` 구성 요소는 자신을 [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component)로서 자식 구성 요소에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-105">The following `Error` component passes itself as a [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) to child components.</span></span> <span data-ttu-id="5fb6a-106">다음 예제는 단지 오류를 로깅하지만 구성 요소의 메서드는 앱에서 요구하는 방식으로 오류를 처리할 수 있으며 여기에는 여러 오류 처리 메서드를 사용하는 것도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-106">The following example merely logs the error, but methods of the component can process errors in any way required by the app, including through the use of multiple error processing methods.</span></span> <span data-ttu-id="5fb6a-107">[삽입된 서비스](xref:blazor/fundamentals/dependency-injection) 또는 사용자 지정 로거 구현을 사용하는 것에 비해 구성 요소를 사용하는 것의 이점은 연계된 구성 요소가 오류 발생 시 콘텐츠를 렌더링하고 CSS 스타일을 적용할 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-107">An advantage of using a component over using an [injected service](xref:blazor/fundamentals/dependency-injection) or a custom logger implementation is that a cascaded component can render content and apply CSS styles when an error occurs.</span></span>

<span data-ttu-id="5fb6a-108">`Shared/Error.razor`:</span><span class="sxs-lookup"><span data-stu-id="5fb6a-108">`Shared/Error.razor`:</span></span>

```razor
@using Microsoft.Extensions.Logging
@inject ILogger<Error> Logger

<CascadingValue Value=this>
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public void ProcessError(Exception ex)
    {
        Logger.LogError("Error:ProcessError - Type: {Type} Message: {Message}", 
            ex.GetType(), ex.Message);
    }
}
```

<span data-ttu-id="5fb6a-109">`App` 구성 요소에서 `Router` 구성 요소를 `Error` 구성 요소로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-109">In the `App` component, wrap the `Router` component with the `Error` component.</span></span> <span data-ttu-id="5fb6a-110">그러면 `Error` 구성 요소는 `Error` 구성 요소가 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute)로 수신되는 앱의 구성 요소로 아래로 연계될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-110">This permits the `Error` component to cascade down to any component of the app where the `Error` component is received as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute).</span></span>

<span data-ttu-id="5fb6a-111">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="5fb6a-111">`App.razor`:</span></span>

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

<span data-ttu-id="5fb6a-112">구성 요소에서 오류를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="5fb6a-112">To process errors in a component:</span></span>

* <span data-ttu-id="5fb6a-113">[`@code`](xref:mvc/views/razor#code) 블록에서 `Error` 구성 요소를 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-113">Designate the `Error` component as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) in the [`@code`](xref:mvc/views/razor#code) block:</span></span>

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* <span data-ttu-id="5fb6a-114">적절한 예외 형식이 있는 `catch` 블록에서 오류 처리 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-114">Call an error processing method in any `catch` block with an appropriate exception type.</span></span> <span data-ttu-id="5fb6a-115">예제 `Error` 구성 요소는 단일 `ProcessError` 메서드만 제공하지만, 오류 처리 구성 요소는 앱 전체에서 대체 오류 처리 요구 사항을 해결할 수 있도록 여러 오류 처리 메서드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-115">The example `Error` component only offers a single `ProcessError` method, but the error processing component can provide any number of error processing methods to address alternative error processing requirements throughout the app.</span></span>

  ```csharp
  try
  {
      ...
  }
  catch (Exception ex)
  {
      Error.ProcessError(ex);
  }
  ```

<span data-ttu-id="5fb6a-116">위 예제의 `Error` 구성 요소 및 `ProcessError` 메서드를 사용하여 브라우저의 개발자 도구 콘솔은 트랩되고 로깅된 오류를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-116">Using the preceding example `Error` component and `ProcessError` method, the browser's developer tools console indicates the trapped, logged error:</span></span>

> <span data-ttu-id="5fb6a-117">fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException 메시지: 개체의 인스턴스에 대해 개체 참조가 설정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-117">fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException Message: Object reference not set to an instance of an object.</span></span>

<span data-ttu-id="5fb6a-118">사용자 지정 오류 메시지 표시줄을 표시하거나 렌더링된 요소의 CSS 스타일을 변경하는 등 `ProcessError` 메서드가 렌더링에 직접 참여하는 경우, UI를 다시 렌더링하기 위해 `ProcessErrors` 메서드의 끝에서 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5fb6a-118">If the `ProcessError` method directly participates in rendering, such as showing a custom error message bar or changing the CSS styles of the rendered elements, call [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) at the end of the `ProcessErrors` method to rerender the UI.</span></span>
