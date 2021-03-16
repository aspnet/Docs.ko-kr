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
Blazor는 SPA(단일 페이지 애플리케이션) 클라이언트 쪽 프레임워크입니다. 브라우저는 앱의 호스트로 사용되므로 탐색 및 정적 자산에 대한 URI 요청을 기반으로 개별 Razor 구성 요소에 대한 처리 파이프라인 역할을 합니다. 미들웨어 처리 파이프라인이 있는 서버에서 실행되는 ASP.NET Core 앱과 달리, 전역 오류 처리에 사용할 수 있는 Razor 구성 요소에 대한 요청을 처리하는 미들웨어 파이프라인이 없습니다. 그러나 앱은 오류 처리 구성 요소를 연계 값으로 사용하여 중앙 집중식으로 오류를 처리할 수 있습니다.

다음 `Error` 구성 요소는 자신을 [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component)로서 자식 구성 요소에 전달합니다. 다음 예제는 단지 오류를 로깅하지만 구성 요소의 메서드는 앱에서 요구하는 방식으로 오류를 처리할 수 있으며 여기에는 여러 오류 처리 메서드를 사용하는 것도 포함됩니다. [삽입된 서비스](xref:blazor/fundamentals/dependency-injection) 또는 사용자 지정 로거 구현을 사용하는 것에 비해 구성 요소를 사용하는 것의 이점은 연계된 구성 요소가 오류 발생 시 콘텐츠를 렌더링하고 CSS 스타일을 적용할 수 있다는 것입니다.

`Shared/Error.razor`:

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

`App` 구성 요소에서 `Router` 구성 요소를 `Error` 구성 요소로 래핑합니다. 그러면 `Error` 구성 요소는 `Error` 구성 요소가 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute)로 수신되는 앱의 구성 요소로 아래로 연계될 수 있습니다.

`App.razor`:

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

구성 요소에서 오류를 처리하려면:

* [`@code`](xref:mvc/views/razor#code) 블록에서 `Error` 구성 요소를 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute)로 지정합니다.

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* 적절한 예외 형식이 있는 `catch` 블록에서 오류 처리 메서드를 호출합니다. 예제 `Error` 구성 요소는 단일 `ProcessError` 메서드만 제공하지만, 오류 처리 구성 요소는 앱 전체에서 대체 오류 처리 요구 사항을 해결할 수 있도록 여러 오류 처리 메서드를 제공할 수 있습니다.

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

위 예제의 `Error` 구성 요소 및 `ProcessError` 메서드를 사용하여 브라우저의 개발자 도구 콘솔은 트랩되고 로깅된 오류를 나타냅니다.

> fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException 메시지: 개체의 인스턴스에 대해 개체 참조가 설정되지 않았습니다.

사용자 지정 오류 메시지 표시줄을 표시하거나 렌더링된 요소의 CSS 스타일을 변경하는 등 `ProcessError` 메서드가 렌더링에 직접 참여하는 경우, UI를 다시 렌더링하기 위해 `ProcessErrors` 메서드의 끝에서 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)를 호출합니다.
