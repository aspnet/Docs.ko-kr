---
title: ASP.NET Core Blazor 양식 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: a8bbcbd6ac13ec064350a5b885423835baa4c4cc
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870375"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor 양식 및 유효성 검사

작성자: [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), [Luke Latham](https://github.com/guardrex)

양식 및 유효성 검사는 [데이터 주석](xref:mvc/models/validation)을 사용하여 Blazor에서 지원됩니다.

다음 `ExampleModel` 형식은 데이터 주석을 사용하여 유효성 검사 논리를 정의합니다.

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

양식은 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 구성 요소를 사용하여 정의합니다. 다음 양식은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

앞의 예제에서:

* 양식은 `ExampleModel` 형식에서 정의된 유효성 검사를 사용하여 `name` 필드에서 사용자 입력의 유효성을 검사합니다. 모델은 구성 요소의 `@code` 블록에 생성되고 프라이빗 필드(`exampleModel`)에 저장됩니다. 필드는 `<EditForm>` 요소의 `Model` 특성에 할당됩니다.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `@bind-Value`는 다음과 같이 바인딩합니다.
  * 모델 속성(`exampleModel.Name`)을 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `Value` 속성에 바인딩합니다. 속성 바인딩에 대한 자세한 내용은 <xref:blazor/components/data-binding#binding-with-component-parameters>를 참조하세요.
  * 변경 이벤트 대리자를 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `ValueChanged` 속성에 바인딩합니다.
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [유효성 검사기 구성 요소](#validator-components)는 데이터 주석을 사용하여 유효성 검사 지원을 연결합니다.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 유효성 검사 메시지를 요약합니다.
* 양식이 성공적으로 제출되면(유효성 검사 통과) `HandleValidSubmit`가 트리거됩니다.

## <a name="built-in-forms-components"></a>기본 제공 양식 구성 요소

기본 제공 구성 요소 세트를 사용하여 사용자 입력을 수신하고 유효성을 검사할 수 있습니다. 입력을 변경할 때와 양식을 제출할 때 입력의 유효성이 검사됩니다. 사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.

::: moniker range=">= aspnetcore-5.0"

| 입력 구성 요소 | 렌더링 형식&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| 입력 구성 요소 | 렌더링 형식&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> `InputRadio` 및 `InputRadioGroup` 구성 요소는 ASP.NET Core 5.0 이상에서 사용할 수 있습니다. 자세한 내용을 보려면 이 문서의 5.0 이상 버전을 선택하세요.

::: moniker-end

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>을 비롯한 모든 입력 구성 요소는 임의 특성을 지원합니다. 구성 요소 매개 변수와 일치하지 않는 특성은 렌더링된 HTML 요소에 추가됩니다.

입력 구성 요소는 필드 상태를 반영하기 위해 필드 CSS 클래스를 업데이트하는 작업을 포함하여 필드 변경이 발생하는 경우 유효성 검사의 기본 동작을 제공합니다. 일부 구성 요소는 유용한 구문 분석 논리를 포함합니다. 예를 들어 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>는 구문 분석할 수 없는 값을 유효성 검사 오류로 등록하여 구문 분석 불가능한 값을 정상적으로 처리합니다. Null 값을 허용할 수 있는 형식은 대상 필드의 Null 허용 여부도 지원합니다(예: `int?`).

다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석 집합을 사용하여 유효성 검사 논리를 정의합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

위의 예제에서 `Description`은 데이터 주석이 없으므로 선택 사항입니다.

다음 양식은 `Starship` 모델에서 정의된 유효성 검사를 사용하여 사용자 입력의 유효성을 검사합니다.

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 수정된 필드와 현재 유효성 검사 메시지를 포함하여 편집 프로세스에 대한 메타데이터를 추적하는 [계단식 값](xref:blazor/components/cascading-values-and-parameters)으로 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 만듭니다.

<xref:Microsoft.AspNetCore.Components.Forms.EditContext> **또는** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType>을 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 할당합니다. 두 항목을 모두 할당할 수 없으며, 그러면 **런타임 오류** 가 발생합니다.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 유효한 양식 제출과 잘못된 양식 제출에 대한 편리한 이벤트를 제공합니다.

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

사용자 지정 코드를 사용하여 유효성 검사를 트리거하고 필드 값을 확인하려면 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>을 사용합니다.

다음 예제에서는

* **`Submit`** 단추를 선택하면 `HandleSubmit` 메서드가 실행됩니다.
* <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>를 호출하여 양식의 유효성을 검사합니다.
* 유효성 검사 결과에 따라 추가 코드가 실행됩니다. <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>에 할당된 메서드에 비즈니스 논리를 삽입합니다.

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 직접 유효성 검사 메시지를 지울 수 있는 프레임워크 API는 없습니다. 따라서 일반적으로 양식의 새 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 유효성 검사 메시지를 추가하지 않는 것이 좋습니다. 유효성 검사 메시지를 관리하려면 이 문서에 설명된 대로 [비즈니스 논리 유효성 검사 코드](#business-logic-validation)에서 [유효성 검사기 구성 요소](#validator-components)를 사용합니다.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>표시 이름 지원

‘이 섹션은 .NET 5 RC1(릴리스 후보 1) 이상의 ASP.NET Core에 적용됩니다.’

다음 기본 제공 구성 요소는 `DisplayName` 매개 변수를 사용하여 표시 이름을 지원합니다.

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

다음 `InputDate` 구성 요소 예제에서

* 표시 이름(`DisplayName`)이 `birthday`로 설정되어 있습니다.
* 구성 요소는 `BirthDate` 속성에 `DateTime` 형식으로 바인딩됩니다.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

사용자가 날짜 값을 제공하지 않는 경우 다음과 같은 유효성 검사 오류가 표시됩니다.

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>유효성 검사기 구성 요소

유효성 검사기 구성 요소는 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에 대한 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>를 관리하여 양식 유효성 검사를 지원합니다.

Blazor 프레임워크는 [유효성 검사 특성(데이터 주석)](xref:mvc/models/validation#validation-attributes)을 기반으로 양식에 유효성 검사 지원을 연결하는 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 제공합니다. 사용자 지정 유효성 검사기 구성 요소를 만들어 동일한 페이지의 다른 양식에 대한 유효성 검사 메시지를 처리하거나, 다양한 양식 처리 단계에서 동일한 양식에 대한 유효성 검사 메시지를 처리할 수 있습니다. 예를 들어 클라이언트 쪽 유효성 검사 후 서버 쪽 유효성 검사를 수행합니다. 이 섹션에 표시된 유효성 검사기 구성 요소 예제(`CustomValidator`)는 이 문서의 다음 섹션에서 사용됩니다.

* [비즈니스 논리 유효성 검사](#business-logic-validation)
* [서버 유효성 검사](#server-validation)

> [!NOTE]
> 대부분의 경우 사용자 지정 유효성 검사기 구성 요소 대신 사용자 지정 데이터 주석 유효성 검사 특성을 사용할 수 있습니다. 양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다. 서버 쪽 유효성 검사에 사용하는 경우 모델에 적용된 사용자 지정 특성을 서버에서 실행 가능해야 합니다. 자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.

<xref:Microsoft.AspNetCore.Components.ComponentBase>에서 유효성 검사기 구성 요소를 만듭니다.

* 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>는 구성 요소의 [연계 매개 변수](xref:blazor/components/cascading-values-and-parameters)입니다.
* 유효성 검사기 구성 요소가 초기화되면 현재 양식 오류 목록을 유지하기 위해 새 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>가 만들어집니다.
* 양식의 구성 요소에 있는 개발자 코드에서 `DisplayErrors` 메서드를 호출하면 메시지 저장소에 오류가 수신됩니다. 오류는 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)의 `DisplayErrors` 메서드에 전달됩니다. 사전에서 키는 하나 이상의 오류가 발생한 양식 필드의 이름입니다. 값은 오류 목록입니다.
* 다음 중 하나가 발생하면 메시지가 지워집니다.
  * <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> 이벤트가 발생하면 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 유효성 검사가 요청됩니다. 모든 오류는 지워집니다.
  * <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 이벤트가 발생하면 양식의 필드가 변경됩니다. 해당 필드에 대한 오류만 지워집니다.
  * `ClearErrors` 메서드는 개발자 코드에 의해 호출됩니다. 모든 오류는 지워집니다.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a>비즈니스 논리 유효성 검사

비즈니스 논리 유효성 검사는 사전에서 양식 오류를 수신하는 [유효성 검사기 구성 요소](#validator-components)를 사용하여 수행할 수 있습니다.

다음 예제에서는

* 이 문서의 [유효성 검사기 구성 요소](#validator-components) 섹션에서 `CustomValidator` 구성 요소가 사용됩니다.
* 사용자가 `Defense` 배송 분류(`Classification`)를 선택하는 경우 유효성 검사에는 배송 설명(`Description`)에 대한 값이 필요합니다.

유효성 검사 메시지가 구성 요소에 설정되어 있으면 해당 메시지가 유효성 검사기의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 추가되고 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 표시됩니다.

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> [유효성 검사 구성 요소](#validator-components)를 사용하는 대신 데이터 주석 유효성 검사 특성을 사용할 수 있습니다. 양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다. 서버 쪽 유효성 검사에 사용하는 경우 특성을 서버에서 실행 가능해야 합니다. 자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.

## <a name="server-validation"></a>서버 유효성 검사

서버 [유효성 검사기 구성 요소](#validator-components)를 사용하여 서버 유효성 검사를 수행할 수 있습니다.

* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 양식에서 클라이언트 쪽 유효성 검사를 처리합니다.
* 양식이 클라이언트 쪽 유효성 검사를 통과하면(<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>이 호출됨) 양식 처리를 위해 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType>을 백 엔드 서버 API로 보냅니다.
* 서버에서 모델 유효성 검사를 처리합니다.
* 서버 API는 개발자가 제공하는 기본 제공 프레임워크 데이터 주석 유효성 검사와 사용자 지정 유효성 검사 논리를 모두 포함합니다. 서버에서 유효성 검사를 통과하면 양식을 처리하고 성공 상태 코드(‘200 - 정상’)를 다시 보냅니다. 유효성 검사에 실패하면 실패 상태 코드(‘400 - 잘못된 요청’) 및 필드 유효성 검사 오류가 반환됩니다.
* 성공 시 양식을 사용하지 않도록 설정하거나 오류를 표시합니다.

다음 예제는 다음을 기반으로 합니다.

* [Blazor 호스트된 프로젝트 템플릿](xref:blazor/hosting-models#blazor-webassembly)에서 만든 호스트된 Blazor 솔루션. 이 예제는 [보안 및 Identity 설명서](xref:blazor/security/webassembly/index#implementation-guidance)에 설명된 보안 호스트된 Blazor 솔루션과 함께 사용할 수 있습니다.
* 이전 [기본 제공 양식 구성 요소](#built-in-forms-components) 섹션의 ‘Starfleet Starship 데이터베이스’ 양식 예제.
* Blazor 프레임워크의 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소.
* [유효성 검사기 구성 요소](#validator-components) 섹션에 표시된 `CustomValidator` 구성 요소.

다음 예에서 사용자가 `Defense` 배송 분류(`Classification`)를 선택하는 경우 서버 API는 배송 설명(`Description`)에 대한 값이 제공되는지 확인합니다.

클라이언트 앱과 서버 앱이 모두 모델을 사용할 수 있도록 `Starship` 모델을 솔루션의 `Shared` 프로젝트에 배치합니다. 모델에 데이터 주석이 필요하므로 [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations)에 대한 패키지 참조를 `Shared` 프로젝트의 프로젝트 파일에 추가합니다.

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

미리 보기가 아닌 최신 버전의 패키지를 확인하려면 [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)에서 패키지 **버전 기록** 을 참조하세요.

서버 API 프로젝트에서 starship 유효성 검사 요청(`Controllers/StarshipValidation.cs`)을 처리하고 실패한 유효성 검사 메시지를 반환하는 컨트롤러를 추가합니다.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

앞의 예제에서 자리 표시자 `{ASSEMBLY NAME}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.Server`).

서버에서 모델 바인딩 유효성 검사 오류가 발생하는 경우 [`ApiController`](xref:web-api/index)(<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>)는 일반적으로 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>를 사용하여 [기본 잘못된 요청 응답](xref:web-api/index#default-badrequest-response)을 반환합니다. ‘Starfleet Starship 데이터베이스’ 양식의 모든 필드가 제출되지 않고 양식에서 유효성 검사가 실패하는 경우, 다음 예제에 표시된 것처럼 응답에는 유효성 검사 오류 외에 추가 데이터가 포함되어 있습니다.

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

서버 API가 이전의 기본 JSON 응답을 반환하는 경우 클라이언트는 응답을 구문 분석하여 `errors` 노드의 자식을 가져올 수 있습니다. 하지만 파일을 구문 분석하는 것은 불편할 수 있습니다. JSON을 구문 분석하려면 양식 유효성 검사 오류 처리를 위한 오류 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)을 생성하기 위해 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>를 호출한 후 추가 코드가 필요합니다. 서버 API는 유효성 검사 오류만 반환하는 것이 가장 좋습니다.

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

유효성 검사 오류만 반환하도록 서버 API의 응답을 수정하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 주석 처리된 작업에 대해 호출되는 대리자를 변경합니다. API 엔드포인트(`/StarshipValidation`)의 경우 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>를 사용하여 <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult>를 반환합니다. 다른 API 엔드포인트의 경우 새 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>를 통해 개체 결과를 반환하여 기본 동작을 유지합니다.

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

자세한 내용은 <xref:web-api/handle-errors#validation-failure-error-response>를 참조하세요.

클라이언트 프로젝트에서 [유효성 검사기 구성 요소](#validator-components) 섹션에 표시된 유효성 검사기 구성 요소를 추가합니다.

클라이언트 프로젝트에서 `CustomValidator` 구성 요소에 대한 도움말과 함께 서버 유효성 검사 오류를 표시하도록 ‘Starfleet Starship 데이터베이스’ 양식이 업데이트됩니다. 서버 API에서 반환되는 유효성 검사 메시지는 `CustomValidator` 구성 요소의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 추가됩니다. 오류는 양식의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>에서 표시하기 위해 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 사용할 수 있습니다.

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> [유효성 검사 구성 요소](#validator-components) 대신 데이터 주석 유효성 검사 특성을 사용할 수 있습니다. 양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다. 서버 쪽 유효성 검사에 사용하는 경우 특성을 서버에서 실행 가능해야 합니다. 자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.

> [!NOTE]
> 이 섹션의 서버 쪽 유효성 검사 방법은 이 설명서 집합의 Blazor WebAssembly 호스트된 솔루션 예제에 적합합니다.
>
> * [AAD(Azure Active Directory)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [AAD(Azure Active Directory) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity서버](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>입력 이벤트를 기반으로 하는 InputText

<xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소를 사용하여 `change` 이벤트 대신 `input` 이벤트를 사용하는 사용자 지정 구성 요소를 만들 수 있습니다.

다음 예제에서 `CustomInputText` 구성 요소는 프레임워크의 `InputText` 구성 요소를 상속하고 이벤트 바인딩(<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>)을 `oninput` 이벤트로 설정합니다.

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

`CustomInputText` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Forms.InputText>가 사용되는 모든 위치에서 사용할 수 있습니다.

`Pages/TestForm.razor`:

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>라디오 단추

::: moniker range=">= aspnetcore-5.0"

`InputRadioGroup` 구성 요소와 함께 `InputRadio` 구성 요소를 사용하여 라디오 단추 그룹을 만듭니다. 다음 예제에서는 [기본 제공 양식 구성 요소](#built-in-forms-components) 단원에 설명된 `Starship` 모델에 속성을 추가합니다.

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

앱에 다음 `enums`를 추가합니다. `enums`를 저장할 새 파일을 만들거나 `Starship.cs` 파일에 `enums`를 추가합니다. `Starship` 모델 및 ‘Starfleet Starship Database’ 양식에서 `enums`에 액세스할 수 있도록 만듭니다.

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

[기본 제공 양식 구성 요소](#built-in-forms-components) 섹션에 설명된 ‘Starfleet Starship Database’ 양식을 업데이트합니다. 생성할 구성 요소를 추가합니다.

* 선박 제조업체의 라디오 단추 그룹.
* 선박 색 및 엔진에 해당하는 중첩된 라디오 단추 그룹.

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> `Name`이 생략되면 `InputRadio` 구성 요소가 가장 최근 상위 항목을 기준으로 그룹화됩니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

양식에서 라디오 단추를 사용하는 경우, 라디오 단추는 그룹으로 평가되기 때문에 데이터 바인딩이 다른 요소와 다르게 처리됩니다. 각 라디오 단추의 값은 고정되어 있지만 라디오 단추 그룹의 값은 선택한 라디오 단추의 값입니다. 아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 라디오 단추 그룹의 데이터 바인딩을 처리합니다.
* 사용자 지정 `InputRadio` 구성 요소를 사용하여 유효성 검사를 지원합니다.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

다음 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 위의 `InputRadio` 구성 요소를 사용하여 사용자의 평가를 가져오고 유효성을 검사합니다.

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a>C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩

다음과 같은 이유로 `<select>` 요소 옵션 값을 C# 개체 `null` 값으로 나타낼 수 있는 적절한 방법은 없습니다.

* HTML 특성에는 `null` 값을 사용할 수 없습니다. HTML에서 `null`에 가장 근사한 값은 `<option>` 요소에서 HTML `value` 특성이 없는 상태입니다.
* `value` 특성이 없는 `<option>`을 선택하면 브라우저가 해당 `<option>` 요소의 텍스트 내용을 값으로 처리합니다.

Blazor 프레임워크는 기본 동작에 다음을 포함하므로 기본 동작을 억제하려고 시도하지 않습니다.

* 프레임워크에서 일련의 특수 경우 해결 방법 만들기
* 현재 프레임워크 동작에 대한 호환성이 손상되는 변경

::: moniker range=">= aspnetcore-5.0"

HTML에서 가장 타당한 `null` 근사값은 빈 문자열 `value`입니다. Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 위해 `null`을 빈 문자열 변환으로 처리합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 시도할 때 자동으로 `null`을 빈 문자열 변환으로 처리하지 않습니다. 자세한 내용은 [Null 값에 대한 `<select>` 바인딩 수정(dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)을 참조하세요.

::: moniker-end

## <a name="validation-support"></a>유효성 검사 지원

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 계단식 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에 연결합니다. 데이터 주석을 통해 유효성 검사 지원을 사용하도록 설정하려면 이 명시적 제스처가 필요합니다. 데이터 주석이 아닌 다른 유효성 검사 시스템을 사용하려면 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>를 사용자 지정 구현으로 바꿉니다. 참조 원본 [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 ASP.NET Core 구현을 검사할 수 있습니다. 위의 참조 원본 링크에서는 ASP.NET Core의 향후 릴리스를 위한 제품 단위의 최신 개발을 나타내는 리포지토리 `master` 분기의 코드를 제공합니다. 다른 릴리스의 분기를 선택하려면 GitHub 분기 선택기를 사용합니다(예: `release/3.1`).

Blazor는 다음 두 가지 유형의 유효성 검사를 수행합니다.

* ‘필드 유효성 검사’는 사용자가 Tab 키를 눌러 필드를 벗어날 때 수행됩니다. 필드 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 보고된 모든 유효성 검사 결과를 필드에 연결합니다.
* ‘모델 유효성 검사’는 사용자가 양식을 제출할 때 수행됩니다. 모델 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 유효성 검사 결과에 보고된 멤버 이름을 기준으로 필드를 확인합니다. 개별 멤버와 연결되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결됩니다.

### <a name="validation-summary-and-validation-message-components"></a>유효성 검사 요약 및 유효성 검사 메시지 구성 요소

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 유사하게, 모든 유효성 검사 메시지를 요약합니다.

```razor
<ValidationSummary />
```

`Model` 매개 변수를 사용하여 특정 모델의 유효성 검사 메시지를 출력합니다.
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 유사하게, 특정 필드의 유효성 검사 메시지를 표시합니다. 모델 속성 이름을 지정하는 람다 식과 `For` 특성을 사용하여 유효성을 검사할 필드를 지정합니다.

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 임의 특성을 지원합니다. 구성 요소 매개 변수와 일치하지 않는 특성은 생성된 `<div>` 또는 `<ul>` 요소에 추가됩니다.

앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에서 유효성 검사 메시지 스타일을 제어합니다. 기본 `validation-message` 클래스는 유효성 검사 메시지의 텍스트 색을 빨간색으로 설정합니다.

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>사용자 지정 유효성 검사 특성

[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용할 때 유효성 검사 결과가 필드와 올바르게 연결되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>을 전달합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>이(가) `null`인 경우 `IsValid` 메서드에서 유효성 검사를 위한 서비스 삽입은 지원되지 않습니다.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>사용자 지정 유효성 검사 클래스 특성

사용자 지정 유효성 검사 클래스 이름은 [부트스트랩](https://getbootstrap.com/) 같은 CSS 프레임워크와 통합하는 경우에 유용합니다. 사용자 지정 유효성 검사 클래스 이름을 지정하려면 `FieldCssClassProvider`에서 파생된 클래스를 만들고 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 인스턴스에서 클래스를 설정합니다.

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor 데이터 주석 유효성 검사 패키지

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)는 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 유효성 검사 환경 차이를 완화하는 패키지입니다. 이 패키지는 현재 ‘실험적’입니다.

> [!NOTE]
> [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지는 [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)에 최신 버전의 ‘릴리스 후보’가 있습니다. 여기서는 ‘실험적’ 릴리스 후보 패키지를 계속 사용합니다. 이후 릴리스에서 패키지의 어셈블리가 프레임워크 또는 런타임으로 이동될 수 있습니다. [알림 GitHub 리포지토리](https://github.com/aspnet/Announcements), [dotnet/aspnetcore GitHub 리포지토리](https://github.com/dotnet/aspnetcore) 또는 이 항목 섹션에서 추가 업데이트를 확인하세요.

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a>`[CompareProperty]` 특성

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>는 유효성 검사 결과를 특정 멤버에 연결하지 않으므로 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소에서 제대로 작동하지 않습니다. 이로 인해 제출 시 전체 모델의 유효성을 검사하는 경우와 필드 수준 유효성 검사 간에 동작이 일치하지 않을 수 있습니다. [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ‘실험적’ 패키지는 해당 제한 사항을 해결하는 추가 유효성 검사 특성인 `ComparePropertyAttribute`를 도입합니다. Blazor 앱에서 `[CompareProperty]`는 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 특성을 직접 대체합니다.

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a>중첩된 모델, 컬렉션 형식 및 복합 형식

Blazor는 기본 제공 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>와 함께 데이터 주석을 사용하여 양식 입력의 유효성 검사를 지원합니다. 그러나 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>는 컬렉션 형식 또는 복합 형식 속성이 아닌, 양식에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사합니다.

컬렉션 형식 및 복합 형식 속성을 포함한 바인딩된 모델의 전체 개체 그래프에 대해 유효성을 검사하려면 다음과 같이 ‘실험적’ [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지에서 제공하는 `ObjectGraphDataAnnotationsValidator`를 사용합니다.

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

`[ValidateComplexType]`을 사용하여 모델 속성에 주석을 답니다. 다음 모델 클래스에서 `ShipDescription` 클래스는 모델이 양식에 바인딩된 경우 유효성을 검사할 추가 데이터 주석을 포함합니다.

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

`ShipDescription.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>양식 유효성 검사에 따라 제출 단추 사용

양식 유효성 검사에 따라 제출 단추를 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.

* 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하여 구성 요소가 초기화될 때 모델을 할당할 수 있습니다.
* 컨텍스트의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 콜백에서 양식의 유효성을 검사하여 제출 단추를 사용하거나 사용하지 않도록 설정합니다.
* `Dispose` 메서드에서 이벤트 처리기를 언후크합니다. 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>를 참조하세요.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하는 경우 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>을 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 할당하지 마세요.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

위의 예제에서는 다음과 같은 경우 `formInvalid`를 `false`로 설정합니다.

* 양식에 유효한 기본값이 미리 로드된 경우
* 양식을 로드할 때 제출 단추를 사용할 수 있게 하려는 경우

이전 방법의 부작용으로, 사용자가 아무 필드나 하나를 조작하고 나면 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소에 잘못된 필드가 채워집니다. 이 시나리오는 다음 방법 중 하나로 해결할 수 있습니다.

* 양식에서 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소를 사용하지 않습니다.
* 제출 단추를 선택할 때 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소가 표시되도록 설정합니다(예: `HandleValidSubmit` 메서드에서).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>문제 해결

> InvalidOperationException: EditForm에는 모델 매개 변수 또는 EditContext 매개 변수를 사용해야 합니다(둘 다 사용할 필요는 없음).

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **또는** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>가 있는지 확인합니다. 동일한 양식에 대해 둘 다 사용하지 마세요.

양식에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>을 할당하는 경우 다음 예제와 같이 모델 형식이 인스턴스화되는지 확인합니다.

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/file-uploads>

::: moniker-end
