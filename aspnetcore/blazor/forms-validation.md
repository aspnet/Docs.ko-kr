---
title: 'ASP.NET Core Blazor 양식 및 유효성 검사'
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: fe232b40a2255732dd375cc266937576d5b2d5d9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507826"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="3b589-103">ASP.NET Core Blazor 양식 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="3b589-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="3b589-104">작성자: [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3b589-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3b589-105">양식 및 유효성 검사는 [데이터 주석](xref:mvc/models/validation)을 사용하여 Blazor에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="3b589-106">다음 `ExampleModel` 형식은 데이터 주석을 사용하여 유효성 검사 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="3b589-107">양식은 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 구성 요소를 사용하여 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="3b589-108">다음 양식은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="3b589-109">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="3b589-109">In the preceding example:</span></span>

* <span data-ttu-id="3b589-110">양식은 `ExampleModel` 형식에서 정의된 유효성 검사를 사용하여 `name` 필드에서 사용자 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="3b589-111">모델은 구성 요소의 `@code` 블록에 생성되고 프라이빗 필드(`exampleModel`)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="3b589-112">필드는 `<EditForm>` 요소의 `Model` 특성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="3b589-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `@bind-Value`는 다음과 같이 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="3b589-114">모델 속성(`exampleModel.Name`)을 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `Value` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="3b589-115">속성 바인딩에 대한 자세한 내용은 <xref:blazor/components/data-binding#binding-with-component-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-115">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="3b589-116">변경 이벤트 대리자를 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `ValueChanged` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="3b589-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [유효성 검사기 구성 요소](#validator-components)는 데이터 주석을 사용하여 유효성 검사 지원을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="3b589-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 유효성 검사 메시지를 요약합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="3b589-119">양식이 성공적으로 제출되면(유효성 검사 통과) `HandleValidSubmit`가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="3b589-120">기본 제공 양식 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3b589-120">Built-in forms components</span></span>

<span data-ttu-id="3b589-121">기본 제공 구성 요소 세트를 사용하여 사용자 입력을 수신하고 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="3b589-122">입력을 변경할 때와 양식을 제출할 때 입력의 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="3b589-123">사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="3b589-124">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3b589-124">Input component</span></span> | <span data-ttu-id="3b589-125">렌더링 형식&hellip;</span><span class="sxs-lookup"><span data-stu-id="3b589-125">Rendered as&hellip;</span></span> |
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

| <span data-ttu-id="3b589-126">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3b589-126">Input component</span></span> | <span data-ttu-id="3b589-127">렌더링 형식&hellip;</span><span class="sxs-lookup"><span data-stu-id="3b589-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="3b589-128">`InputRadio` 및 `InputRadioGroup` 구성 요소는 ASP.NET Core 5.0 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="3b589-129">자세한 내용을 보려면 이 문서의 5.0 이상 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="3b589-130"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>을 비롯한 모든 입력 구성 요소는 임의 특성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="3b589-131">구성 요소 매개 변수와 일치하지 않는 특성은 렌더링된 HTML 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="3b589-132">입력 구성 요소는 필드 상태를 반영하기 위해 필드 CSS 클래스를 업데이트하는 작업을 포함하여 필드 변경이 발생하는 경우 유효성 검사의 기본 동작을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="3b589-133">일부 구성 요소는 유용한 구문 분석 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="3b589-134">예를 들어 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>는 구문 분석할 수 없는 값을 유효성 검사 오류로 등록하여 구문 분석 불가능한 값을 정상적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="3b589-135">Null 값을 허용할 수 있는 형식은 대상 필드의 Null 허용 여부도 지원합니다(예: `int?`).</span><span class="sxs-lookup"><span data-stu-id="3b589-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="3b589-136">다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석 집합을 사용하여 유효성 검사 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="3b589-137">위의 예제에서 `Description`은 데이터 주석이 없으므로 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="3b589-138">다음 양식은 `Starship` 모델에서 정의된 유효성 검사를 사용하여 사용자 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="3b589-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 수정된 필드와 현재 유효성 검사 메시지를 포함하여 편집 프로세스에 대한 메타데이터를 추적하는 [계단식 값](xref:blazor/components/cascading-values-and-parameters)으로 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="3b589-140"><xref:Microsoft.AspNetCore.Components.Forms.EditContext> **또는** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType>을 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="3b589-141">두 항목을 모두 할당할 수 없으며, 그러면 **런타임 오류** 가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="3b589-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 유효한 양식 제출과 잘못된 양식 제출에 대한 편리한 이벤트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="3b589-143">사용자 지정 코드를 사용하여 유효성 검사를 트리거하고 필드 값을 확인하려면 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="3b589-144">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="3b589-144">In the following example:</span></span>

* <span data-ttu-id="3b589-145">**`Submit`** 단추를 선택하면 `HandleSubmit` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="3b589-146"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>를 호출하여 양식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="3b589-147">유효성 검사 결과에 따라 추가 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="3b589-148"><xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>에 할당된 메서드에 비즈니스 논리를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

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
> <span data-ttu-id="3b589-149"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 직접 유효성 검사 메시지를 지울 수 있는 프레임워크 API는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="3b589-150">따라서 일반적으로 양식의 새 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 유효성 검사 메시지를 추가하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="3b589-151">유효성 검사 메시지를 관리하려면 이 문서에 설명된 대로 [비즈니스 논리 유효성 검사 코드](#business-logic-validation)에서 [유효성 검사기 구성 요소](#validator-components)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="3b589-152">표시 이름 지원</span><span class="sxs-lookup"><span data-stu-id="3b589-152">Display name support</span></span>

<span data-ttu-id="3b589-153">‘이 섹션은 .NET 5 RC1(릴리스 후보 1) 이상의 ASP.NET Core에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="3b589-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="3b589-154">다음 기본 제공 구성 요소는 `DisplayName` 매개 변수를 사용하여 표시 이름을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="3b589-155">다음 `InputDate` 구성 요소 예제에서</span><span class="sxs-lookup"><span data-stu-id="3b589-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="3b589-156">표시 이름(`DisplayName`)이 `birthday`로 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="3b589-157">구성 요소는 `BirthDate` 속성에 `DateTime` 형식으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="3b589-158">사용자가 날짜 값을 제공하지 않는 경우 다음과 같은 유효성 검사 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="3b589-159">유효성 검사기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3b589-159">Validator components</span></span>

<span data-ttu-id="3b589-160">유효성 검사기 구성 요소는 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에 대한 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>를 관리하여 양식 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="3b589-161">Blazor 프레임워크는 [유효성 검사 특성(데이터 주석)](xref:mvc/models/validation#validation-attributes)을 기반으로 양식에 유효성 검사 지원을 연결하는 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="3b589-162">사용자 지정 유효성 검사기 구성 요소를 만들어 동일한 페이지의 다른 양식에 대한 유효성 검사 메시지를 처리하거나, 다양한 양식 처리 단계에서 동일한 양식에 대한 유효성 검사 메시지를 처리할 수 있습니다. 예를 들어 클라이언트 쪽 유효성 검사 후 서버 쪽 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="3b589-163">이 섹션에 표시된 유효성 검사기 구성 요소 예제(`CustomValidator`)는 이 문서의 다음 섹션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="3b589-164">비즈니스 논리 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="3b589-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="3b589-165">서버 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="3b589-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="3b589-166">대부분의 경우 사용자 지정 유효성 검사기 구성 요소 대신 사용자 지정 데이터 주석 유효성 검사 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="3b589-167">양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="3b589-168">서버 쪽 유효성 검사에 사용하는 경우 모델에 적용된 사용자 지정 특성을 서버에서 실행 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="3b589-169">자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="3b589-170"><xref:Microsoft.AspNetCore.Components.ComponentBase>에서 유효성 검사기 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="3b589-171">양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>는 구성 요소의 [연계 매개 변수](xref:blazor/components/cascading-values-and-parameters)입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="3b589-172">유효성 검사기 구성 요소가 초기화되면 현재 양식 오류 목록을 유지하기 위해 새 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="3b589-173">양식의 구성 요소에 있는 개발자 코드에서 `DisplayErrors` 메서드를 호출하면 메시지 저장소에 오류가 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="3b589-174">오류는 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)의 `DisplayErrors` 메서드에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="3b589-175">사전에서 키는 하나 이상의 오류가 발생한 양식 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="3b589-176">값은 오류 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-176">The value is the error list.</span></span>
* <span data-ttu-id="3b589-177">다음 중 하나가 발생하면 메시지가 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="3b589-178"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> 이벤트가 발생하면 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 유효성 검사가 요청됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="3b589-179">모든 오류는 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="3b589-180"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 이벤트가 발생하면 양식의 필드가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="3b589-181">해당 필드에 대한 오류만 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="3b589-182">`ClearErrors` 메서드는 개발자 코드에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="3b589-183">모든 오류는 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
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
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="3b589-184">비즈니스 논리 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="3b589-184">Business logic validation</span></span>

<span data-ttu-id="3b589-185">비즈니스 논리 유효성 검사는 사전에서 양식 오류를 수신하는 [유효성 검사기 구성 요소](#validator-components)를 사용하여 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="3b589-186">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="3b589-186">In the following example:</span></span>

* <span data-ttu-id="3b589-187">이 문서의 [유효성 검사기 구성 요소](#validator-components) 섹션에서 `CustomValidator` 구성 요소가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="3b589-188">사용자가 `Defense` 배송 분류(`Classification`)를 선택하는 경우 유효성 검사에는 배송 설명(`Description`)에 대한 값이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="3b589-189">유효성 검사 메시지가 구성 요소에 설정되어 있으면 해당 메시지가 유효성 검사기의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 추가되고 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

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
> <span data-ttu-id="3b589-190">[유효성 검사 구성 요소](#validator-components)를 사용하는 대신 데이터 주석 유효성 검사 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="3b589-191">양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="3b589-192">서버 쪽 유효성 검사에 사용하는 경우 특성을 서버에서 실행 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="3b589-193">자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="3b589-194">서버 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="3b589-194">Server validation</span></span>

<span data-ttu-id="3b589-195">서버 [유효성 검사기 구성 요소](#validator-components)를 사용하여 서버 유효성 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="3b589-196"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 양식에서 클라이언트 쪽 유효성 검사를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="3b589-197">양식이 클라이언트 쪽 유효성 검사를 통과하면(<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>이 호출됨) 양식 처리를 위해 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType>을 백 엔드 서버 API로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="3b589-198">서버에서 모델 유효성 검사를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-198">Process model validation on the server.</span></span>
* <span data-ttu-id="3b589-199">서버 API는 개발자가 제공하는 기본 제공 프레임워크 데이터 주석 유효성 검사와 사용자 지정 유효성 검사 논리를 모두 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="3b589-200">서버에서 유효성 검사를 통과하면 양식을 처리하고 성공 상태 코드(‘200 - 정상’)를 다시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-200">If validation passes on the server, process the form and send back a success status code ( *200 - OK* ).</span></span> <span data-ttu-id="3b589-201">유효성 검사에 실패하면 실패 상태 코드(‘400 - 잘못된 요청’) 및 필드 유효성 검사 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-201">If validation fails, return a failure status code ( *400 - Bad Request* ) and the field validation errors.</span></span>
* <span data-ttu-id="3b589-202">성공 시 양식을 사용하지 않도록 설정하거나 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="3b589-203">다음 예제는 다음을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-203">The following example is based on:</span></span>

* <span data-ttu-id="3b589-204">[Blazor 호스트된 프로젝트 템플릿](xref:blazor/hosting-models#blazor-webassembly)에서 만든 호스트된 Blazor 솔루션.</span><span class="sxs-lookup"><span data-stu-id="3b589-204">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="3b589-205">이 예제는 [보안 및 Identity 설명서](xref:blazor/security/webassembly/index#implementation-guidance)에 설명된 보안 호스트된 Blazor 솔루션과 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-205">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="3b589-206">이전 [기본 제공 양식 구성 요소](#built-in-forms-components) 섹션의 ‘Starfleet Starship 데이터베이스’ 양식 예제.</span><span class="sxs-lookup"><span data-stu-id="3b589-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="3b589-207">Blazor 프레임워크의 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="3b589-207">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="3b589-208">[유효성 검사기 구성 요소](#validator-components) 섹션에 표시된 `CustomValidator` 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="3b589-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="3b589-209">다음 예에서 사용자가 `Defense` 배송 분류(`Classification`)를 선택하는 경우 서버 API는 배송 설명(`Description`)에 대한 값이 제공되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="3b589-210">클라이언트 앱과 서버 앱이 모두 모델을 사용할 수 있도록 `Starship` 모델을 솔루션의 `Shared` 프로젝트에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="3b589-211">모델에 데이터 주석이 필요하므로 [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations)에 대한 패키지 참조를 `Shared` 프로젝트의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="3b589-212">미리 보기가 아닌 최신 버전의 패키지를 확인하려면 [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)에서 패키지 **버전 기록** 을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="3b589-213">서버 API 프로젝트에서 starship 유효성 검사 요청(`Controllers/StarshipValidation.cs`)을 처리하고 실패한 유효성 검사 메시지를 반환하는 컨트롤러를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
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

<span data-ttu-id="3b589-214">서버에서 모델 바인딩 유효성 검사 오류가 발생하는 경우 [`ApiController`](xref:web-api/index)(<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>)는 일반적으로 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>를 사용하여 [기본 잘못된 요청 응답](xref:web-api/index#default-badrequest-response)을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-214">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="3b589-215">‘Starfleet Starship 데이터베이스’ 양식의 모든 필드가 제출되지 않고 양식에서 유효성 검사가 실패하는 경우, 다음 예제에 표시된 것처럼 응답에는 유효성 검사 오류 외에 추가 데이터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-215">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

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

<span data-ttu-id="3b589-216">서버 API가 이전의 기본 JSON 응답을 반환하는 경우 클라이언트는 응답을 구문 분석하여 `errors` 노드의 자식을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-216">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="3b589-217">하지만 파일을 구문 분석하는 것은 불편할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-217">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="3b589-218">JSON을 구문 분석하려면 양식 유효성 검사 오류 처리를 위한 오류 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)을 생성하기 위해 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>를 호출한 후 추가 코드가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-218">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="3b589-219">서버 API는 유효성 검사 오류만 반환하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-219">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="3b589-220">유효성 검사 오류만 반환하도록 서버 API의 응답을 수정하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 주석 처리된 작업에 대해 호출되는 대리자를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-220">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b589-221">API 엔드포인트(`/StarshipValidation`)의 경우 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>를 사용하여 <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-221">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="3b589-222">다른 API 엔드포인트의 경우 새 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>를 통해 개체 결과를 반환하여 기본 동작을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-222">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

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

<span data-ttu-id="3b589-223">자세한 내용은 <xref:web-api/handle-errors#validation-failure-error-response>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-223">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="3b589-224">클라이언트 프로젝트에서 [유효성 검사기 구성 요소](#validator-components) 섹션에 표시된 유효성 검사기 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-224">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="3b589-225">클라이언트 프로젝트에서 `CustomValidator` 구성 요소에 대한 도움말과 함께 서버 유효성 검사 오류를 표시하도록 ‘Starfleet Starship 데이터베이스’ 양식이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-225">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="3b589-226">서버 API에서 반환되는 유효성 검사 메시지는 `CustomValidator` 구성 요소의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-226">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="3b589-227">오류는 양식의 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>에서 표시하기 위해 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-227">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

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
> <span data-ttu-id="3b589-228">[유효성 검사 구성 요소](#validator-components) 대신 데이터 주석 유효성 검사 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-228">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="3b589-229">양식의 모델에 적용된 사용자 지정 특성은 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-229">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="3b589-230">서버 쪽 유효성 검사에 사용하는 경우 특성을 서버에서 실행 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-230">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="3b589-231">자세한 내용은 <xref:mvc/models/validation#alternatives-to-built-in-attributes>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-231">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="3b589-232">이 섹션의 서버 쪽 유효성 검사 방법은 이 설명서 집합의 Blazor WebAssembly 호스트된 솔루션 예제에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-232">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="3b589-233">AAD(Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="3b589-233">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="3b589-234">AAD(Azure Active Directory) B2C</span><span class="sxs-lookup"><span data-stu-id="3b589-234">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="3b589-235">Identity서버</span><span class="sxs-lookup"><span data-stu-id="3b589-235">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="3b589-236">입력 이벤트를 기반으로 하는 InputText</span><span class="sxs-lookup"><span data-stu-id="3b589-236">InputText based on the input event</span></span>

<span data-ttu-id="3b589-237"><xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소를 사용하여 `change` 이벤트 대신 `input` 이벤트를 사용하는 사용자 지정 구성 요소를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-237">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="3b589-238">다음 예제에서 `CustomInputText` 구성 요소는 프레임워크의 `InputText` 구성 요소를 상속하고 이벤트 바인딩(<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>)을 `oninput` 이벤트로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-238">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="3b589-239">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="3b589-239">`Shared/CustomInputText.razor`:</span></span>

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

<span data-ttu-id="3b589-240">`CustomInputText` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Forms.InputText>가 사용되는 모든 위치에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-240">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="3b589-241">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="3b589-241">`Pages/TestForm.razor`:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="3b589-242">라디오 단추</span><span class="sxs-lookup"><span data-stu-id="3b589-242">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3b589-243">`InputRadioGroup` 구성 요소와 함께 `InputRadio` 구성 요소를 사용하여 라디오 단추 그룹을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-243">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="3b589-244">다음 예제에서는 [기본 제공 양식 구성 요소](#built-in-forms-components) 단원에 설명된 `Starship` 모델에 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-244">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

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

<span data-ttu-id="3b589-245">앱에 다음 `enums`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-245">Add the following `enums` to the app.</span></span> <span data-ttu-id="3b589-246">`enums`를 저장할 새 파일을 만들거나 `Starship.cs` 파일에 `enums`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-246">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="3b589-247">`Starship` 모델 및 ‘Starfleet Starship Database’ 양식에서 `enums`에 액세스할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-247">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="3b589-248">[기본 제공 양식 구성 요소](#built-in-forms-components) 섹션에 설명된 ‘Starfleet Starship Database’ 양식을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-248">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="3b589-249">생성할 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-249">Add the components to produce:</span></span>

* <span data-ttu-id="3b589-250">선박 제조업체의 라디오 단추 그룹.</span><span class="sxs-lookup"><span data-stu-id="3b589-250">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="3b589-251">선박 색 및 엔진에 해당하는 중첩된 라디오 단추 그룹.</span><span class="sxs-lookup"><span data-stu-id="3b589-251">A nested radio button group for ship color and engine.</span></span>

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
> <span data-ttu-id="3b589-252">`Name`이 생략되면 `InputRadio` 구성 요소가 가장 최근 상위 항목을 기준으로 그룹화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-252">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3b589-253">양식에서 라디오 단추를 사용하는 경우, 라디오 단추는 그룹으로 평가되기 때문에 데이터 바인딩이 다른 요소와 다르게 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-253">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="3b589-254">각 라디오 단추의 값은 고정되어 있지만 라디오 단추 그룹의 값은 선택한 라디오 단추의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-254">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="3b589-255">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-255">The following example shows how to:</span></span>

* <span data-ttu-id="3b589-256">라디오 단추 그룹의 데이터 바인딩을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-256">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="3b589-257">사용자 지정 `InputRadio` 구성 요소를 사용하여 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-257">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="3b589-258">다음 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 위의 `InputRadio` 구성 요소를 사용하여 사용자의 평가를 가져오고 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-258">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="3b589-259">C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩</span><span class="sxs-lookup"><span data-stu-id="3b589-259">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="3b589-260">다음과 같은 이유로 `<select>` 요소 옵션 값을 C# 개체 `null` 값으로 나타낼 수 있는 적절한 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-260">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="3b589-261">HTML 특성에는 `null` 값을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-261">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="3b589-262">HTML에서 `null`에 가장 근사한 값은 `<option>` 요소에서 HTML `value` 특성이 없는 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-262">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="3b589-263">`value` 특성이 없는 `<option>`을 선택하면 브라우저가 해당 `<option>` 요소의 텍스트 내용을 값으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-263">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="3b589-264">Blazor 프레임워크는 기본 동작에 다음을 포함하므로 기본 동작을 억제하려고 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-264">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="3b589-265">프레임워크에서 일련의 특수 경우 해결 방법 만들기</span><span class="sxs-lookup"><span data-stu-id="3b589-265">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="3b589-266">현재 프레임워크 동작에 대한 호환성이 손상되는 변경</span><span class="sxs-lookup"><span data-stu-id="3b589-266">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3b589-267">HTML에서 가장 타당한 `null` 근사값은 빈 문자열 `value`입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-267">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="3b589-268">Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 위해 `null`을 빈 문자열 변환으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-268">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3b589-269">Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 시도할 때 자동으로 `null`을 빈 문자열 변환으로 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-269">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="3b589-270">자세한 내용은 [Null 값에 대한 `<select>` 바인딩 수정(dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-270">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="3b589-271">유효성 검사 지원</span><span class="sxs-lookup"><span data-stu-id="3b589-271">Validation support</span></span>

<span data-ttu-id="3b589-272"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 계단식 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-272">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="3b589-273">데이터 주석을 통해 유효성 검사 지원을 사용하도록 설정하려면 이 명시적 제스처가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-273">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="3b589-274">데이터 주석이 아닌 다른 유효성 검사 시스템을 사용하려면 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>를 사용자 지정 구현으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-274">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="3b589-275">참조 원본 [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 ASP.NET Core 구현을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-275">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="3b589-276">위의 참조 원본 링크에서는 ASP.NET Core의 향후 릴리스를 위한 제품 단위의 최신 개발을 나타내는 리포지토리 `master` 분기의 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-276">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="3b589-277">다른 릴리스의 분기를 선택하려면 GitHub 분기 선택기를 사용합니다(예: `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="3b589-277">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="3b589-278">Blazor는 다음 두 가지 유형의 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-278">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="3b589-279">‘필드 유효성 검사’는 사용자가 Tab 키를 눌러 필드를 벗어날 때 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-279">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="3b589-280">필드 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 보고된 모든 유효성 검사 결과를 필드에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-280">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="3b589-281">‘모델 유효성 검사’는 사용자가 양식을 제출할 때 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-281">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="3b589-282">모델 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 유효성 검사 결과에 보고된 멤버 이름을 기준으로 필드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-282">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="3b589-283">개별 멤버와 연결되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-283">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="3b589-284">유효성 검사 요약 및 유효성 검사 메시지 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3b589-284">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="3b589-285"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 유사하게, 모든 유효성 검사 메시지를 요약합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="3b589-286">`Model` 매개 변수를 사용하여 특정 모델의 유효성 검사 메시지를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-286">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="3b589-287"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 유사하게, 특정 필드의 유효성 검사 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="3b589-288">모델 속성 이름을 지정하는 람다 식과 `For` 특성을 사용하여 유효성을 검사할 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-288">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="3b589-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 임의 특성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-289">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="3b589-290">구성 요소 매개 변수와 일치하지 않는 특성은 생성된 `<div>` 또는 `<ul>` 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-290">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="3b589-291">앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에서 유효성 검사 메시지 스타일을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-291">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="3b589-292">기본 `validation-message` 클래스는 유효성 검사 메시지의 텍스트 색을 빨간색으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-292">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="3b589-293">사용자 지정 유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="3b589-293">Custom validation attributes</span></span>

<span data-ttu-id="3b589-294">[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용할 때 유효성 검사 결과가 필드와 올바르게 연결되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-294">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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
> <span data-ttu-id="3b589-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>이(가) `null`인 경우</span><span class="sxs-lookup"><span data-stu-id="3b589-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="3b589-296">`IsValid` 메서드에서 유효성 검사를 위한 서비스 삽입은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-296">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="3b589-297">사용자 지정 유효성 검사 클래스 특성</span><span class="sxs-lookup"><span data-stu-id="3b589-297">Custom validation class attributes</span></span>

<span data-ttu-id="3b589-298">사용자 지정 유효성 검사 클래스 이름은 [부트스트랩](https://getbootstrap.com/) 같은 CSS 프레임워크와 통합하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-298">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="3b589-299">사용자 지정 유효성 검사 클래스 이름을 지정하려면 `FieldCssClassProvider`에서 파생된 클래스를 만들고 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 인스턴스에서 클래스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-299">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

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

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="3b589-300">Blazor 데이터 주석 유효성 검사 패키지</span><span class="sxs-lookup"><span data-stu-id="3b589-300">Blazor data annotations validation package</span></span>

<span data-ttu-id="3b589-301">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)는 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 유효성 검사 환경 차이를 완화하는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-301">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="3b589-302">이 패키지는 현재 ‘실험적’입니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-302">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="3b589-303">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지는 [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)에 최신 버전의 ‘릴리스 후보’가 있습니다. 여기서는 ‘실험적’ 릴리스 후보 패키지를 계속 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-303">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="3b589-304">이후 릴리스에서 패키지의 어셈블리가 프레임워크 또는 런타임으로 이동될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-304">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="3b589-305">[알림 GitHub 리포지토리](https://github.com/aspnet/Announcements), [dotnet/aspnetcore GitHub 리포지토리](https://github.com/dotnet/aspnetcore) 또는 이 항목 섹션에서 추가 업데이트를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-305">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="3b589-306">[CompareProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="3b589-306">[CompareProperty] attribute</span></span>

<span data-ttu-id="3b589-307"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>는 유효성 검사 결과를 특정 멤버에 연결하지 않으므로 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소에서 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-307">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="3b589-308">이로 인해 제출 시 전체 모델의 유효성을 검사하는 경우와 필드 수준 유효성 검사 간에 동작이 일치하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-308">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="3b589-309">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ‘실험적’ 패키지는 해당 제한 사항을 해결하는 추가 유효성 검사 특성인 `ComparePropertyAttribute`를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-309">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="3b589-310">Blazor 앱에서 `[CompareProperty]`는 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 특성을 직접 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-310">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="3b589-311">중첩된 모델, 컬렉션 형식 및 복합 형식</span><span class="sxs-lookup"><span data-stu-id="3b589-311">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="3b589-312">Blazor는 기본 제공 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>와 함께 데이터 주석을 사용하여 양식 입력의 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-312">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="3b589-313">그러나 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>는 컬렉션 형식 또는 복합 형식 속성이 아닌, 양식에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-313">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="3b589-314">컬렉션 형식 및 복합 형식 속성을 포함한 바인딩된 모델의 전체 개체 그래프에 대해 유효성을 검사하려면 다음과 같이 ‘실험적’ [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지에서 제공하는 `ObjectGraphDataAnnotationsValidator`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-314">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="3b589-315">`[ValidateComplexType]`을 사용하여 모델 속성에 주석을 답니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-315">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="3b589-316">다음 모델 클래스에서 `ShipDescription` 클래스는 모델이 양식에 바인딩된 경우 유효성을 검사할 추가 데이터 주석을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-316">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="3b589-317">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="3b589-317">`Starship.cs`:</span></span>

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

<span data-ttu-id="3b589-318">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="3b589-318">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="3b589-319">양식 유효성 검사에 따라 제출 단추 사용</span><span class="sxs-lookup"><span data-stu-id="3b589-319">Enable the submit button based on form validation</span></span>

<span data-ttu-id="3b589-320">양식 유효성 검사에 따라 제출 단추를 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-320">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="3b589-321">양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하여 구성 요소가 초기화될 때 모델을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-321">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="3b589-322">컨텍스트의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 콜백에서 양식의 유효성을 검사하여 제출 단추를 사용하거나 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-322">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="3b589-323">`Dispose` 메서드에서 이벤트 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-323">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="3b589-324">자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-324">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="3b589-325"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하는 경우 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>을 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 할당하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-325">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

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

<span data-ttu-id="3b589-326">위의 예제에서는 다음과 같은 경우 `formInvalid`를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-326">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="3b589-327">양식에 유효한 기본값이 미리 로드된 경우</span><span class="sxs-lookup"><span data-stu-id="3b589-327">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="3b589-328">양식을 로드할 때 제출 단추를 사용할 수 있게 하려는 경우</span><span class="sxs-lookup"><span data-stu-id="3b589-328">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="3b589-329">이전 방법의 부작용으로, 사용자가 아무 필드나 하나를 조작하고 나면 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소에 잘못된 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-329">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="3b589-330">이 시나리오는 다음 방법 중 하나로 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-330">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="3b589-331">양식에서 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-331">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="3b589-332">제출 단추를 선택할 때 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소가 표시되도록 설정합니다(예: `HandleValidSubmit` 메서드에서).</span><span class="sxs-lookup"><span data-stu-id="3b589-332">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="3b589-333">문제 해결</span><span class="sxs-lookup"><span data-stu-id="3b589-333">Troubleshoot</span></span>

> <span data-ttu-id="3b589-334">InvalidOperationException: EditForm에는 모델 매개 변수 또는 EditContext 매개 변수를 사용해야 합니다(둘 다 사용할 필요는 없음).</span><span class="sxs-lookup"><span data-stu-id="3b589-334">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="3b589-335"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **또는** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-335">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="3b589-336">동일한 양식에 대해 둘 다 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="3b589-336">Don't use both for the same form.</span></span>

<span data-ttu-id="3b589-337">양식에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>을 할당하는 경우 다음 예제와 같이 모델 형식이 인스턴스화되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3b589-337">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="3b589-338">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="3b589-338">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
