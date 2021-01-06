---
title: '8부: 유효성 검사 추가'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486124"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="266f9-103">Razor Pages에 대한 자습서 시리즈의 8부입니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="266f9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="266f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="266f9-105">이 섹션에서 유효성 검사 논리는 `Movie` 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="266f9-106">유효성 검사 규칙은 사용자가 동영상을 만들거나 편집할 때 언제든지 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="266f9-107">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="266f9-107">Validation</span></span>

<span data-ttu-id="266f9-108">소프트웨어 개발의 주요 개념은 [반복 금지](https://wikipedia.org/wiki/Don%27t_repeat_yourself)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="266f9-109">Razor Pages는 기능이 한 번 정의된 곳의 개발을 원활하게 하고 앱 전체에서 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="266f9-110">DRY는 다음 작업에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-110">DRY can help:</span></span>

* <span data-ttu-id="266f9-111">앱에서 코드의 양 감소</span><span class="sxs-lookup"><span data-stu-id="266f9-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="266f9-112">코드에 오류 감소 및 쉽게 테스트 및 유지 관리</span><span class="sxs-lookup"><span data-stu-id="266f9-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="266f9-113">Razor Pages와 Entity Framework에서 제공하는 유효성 검사 지원은 반복 금지 원칙의 좋은 예입니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="266f9-114">유효성 검사 규칙은 모델 클래스의 한 위치에서 선언적으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="266f9-115">규칙은 앱의 모든 위치에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="266f9-116">영화 모델에 유효성 검사 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="266f9-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="266f9-117">`DataAnnotations` 네임스페이스는 다음을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="266f9-118">클래스 또는 속성에 선언적으로 적용되는 기본 제공 유효성 검사 특성 집합.</span><span class="sxs-lookup"><span data-stu-id="266f9-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="266f9-119">서식 지정을 돕고 유효성 검사를 제공하지 않는 `[DataType]`과 같은 서식 지정 특성.</span><span class="sxs-lookup"><span data-stu-id="266f9-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="266f9-120">기본 제공되는 `[Required]`, `[StringLength]`, `[RegularExpression]` 및 `[Range]` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="266f9-121">이 유효성 검사 특성은 적용되는 모델 속성에 시행하려는 동작을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="266f9-122">`[Required]` 및 `[MinimumLength]` 특성은 속성에 값이 있어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="266f9-123">사용자는 공백을 입력하여 이 유효성 검사를 충족할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="266f9-124">`[RegularExpression]` 특성은 입력할 수 있는 문자를 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="266f9-125">위의 코드에서 `Genre`는</span><span class="sxs-lookup"><span data-stu-id="266f9-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="266f9-126">문자만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-126">Must only use letters.</span></span>
  * <span data-ttu-id="266f9-127">첫 번째 문자는 대문자여야 합니다</span><span class="sxs-lookup"><span data-stu-id="266f9-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="266f9-128">공백, 숫자 및 특수 문자는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="266f9-129">`RegularExpression` `Rating`:</span><span class="sxs-lookup"><span data-stu-id="266f9-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="266f9-130">첫 번째 문자는 대문자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="266f9-131">이어진 공백에서는 특수 문자와 숫자가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="266f9-132">"PG-13"은 등급으로 유효하지만 `Genre`에서는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="266f9-133">`[Range]` 특성은 값을 지정된 범위 내로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="266f9-134">`[StringLength]` 특성은 문자열 속성의 최대 길이와, 필요에 따라 최소 길이를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="266f9-135">값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필수이며, `[Required]` 특성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="266f9-136">위의 유효성 검사 규칙은 데모에 사용되며 프로덕션 시스템에는 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="266f9-137">예를 들어 위의 규칙에서는 문자가 두 개뿐인 영화를 입력할 수 없으며, `Genre`에 특수 문자를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="266f9-138">유효성 검사 규칙을 ASP.NET Core가 자동으로 적용하도록 하면 다음에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="266f9-139">앱을 보다 강력하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="266f9-140">잘못된 데이터를 데이터베이스에 저장할 가능성이 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="266f9-141">Razor Pages에서 유효성 검사 오류 UI</span><span class="sxs-lookup"><span data-stu-id="266f9-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="266f9-142">앱을 실행하고 Pages/Movies로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="266f9-143">**새로 만들기** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-143">Select the **Create New** link.</span></span> <span data-ttu-id="266f9-144">일부 잘못된 값으로 양식을 완성합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="266f9-145">jQuery 클라이언트 쪽 유효성 검사에서 오류를 발견하면 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="266f9-147">양식에서 잘못된 값을 포함하는 각 필드에 유효성 검사 오류 메시지를 자동으로 렌더링하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="266f9-148">오류는 클라이언트 쪽(JavaScript 및 jQuery 사용) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="266f9-149">중요한 이점은 만들기 또는 편집 페이지에서 코드 변경이 필요하지 **않았다는** 점입니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="266f9-150">데이터 주석이 모델에 적용된 후 유효성 검사 UI가 활성화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="266f9-151">이 자습서에서 만든 Razor Pages는 `Movie` 모델 클래스의 속성에서 유효성 검사 특성을 사용하여 자동으로 유효성 검사 규칙을 선택했습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="266f9-152">편집 페이지를 사용하는 테스트 유효성 검사는 동일한 유효성 검사가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="266f9-153">양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="266f9-154">양식 데이터가 다음 방법 중 하나 이상으로 게시되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="266f9-155">`OnPostAsync` 메서드에 중단점을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="266f9-156">**만들기** 또는 **저장** 을 선택하여 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="266f9-157">중단점은 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-157">The break point is never hit.</span></span>
* <span data-ttu-id="266f9-158">[Fiddler 도구](https://www.telerik.com/fiddler)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="266f9-159">브라우저 개발자 도구를 사용하여 네트워크 트래픽을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="266f9-160">서버 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="266f9-160">Server-side validation</span></span>

<span data-ttu-id="266f9-161">브라우저에서 JavaScript가 비활성화된 경우 오류가 있는 양식 제출에서 서버에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="266f9-162">선택 가능한 서버 쪽 유효성 검사 테스트:</span><span class="sxs-lookup"><span data-stu-id="266f9-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="266f9-163">브라우저에서 JavaScript를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="266f9-164">브라우저의 개발자 도구를 사용하여 JavaScript를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="266f9-165">브라우저에서 JavaScript를 사용하지 않도록 설정할 수 없는 경우 다른 브라우저를 시도하세요.</span><span class="sxs-lookup"><span data-stu-id="266f9-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="266f9-166">만들기 또는 편집 페이지의 `OnPostAsync` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="266f9-167">잘못된 데이터가 포함된 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="266f9-168">모델 상태가 유효하지 않은 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="266f9-169">또는 [서버에서 클라이언트 쪽 유효성 검사를 사용하지 않도록 설정](xref:mvc/models/validation#disable-client-side-validation)합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="266f9-170">다음 코드는 자습서의 앞부분에서 스캐폴드한 *Create.cshtml* 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="266f9-171">만들기 및 편집 페이지에서 다음을 수행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="266f9-172">초기 양식을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-172">Display the initial form.</span></span>
* <span data-ttu-id="266f9-173">오류가 발생한 경우 양식을 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="266f9-174">[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="266f9-175">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="266f9-176">자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="266f9-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="266f9-177">만들기 및 편집 페이지에 유효성 검사 규칙이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="266f9-178">유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="266f9-179">이러한 유효성 검사 규칙은 `Movie` 모델을 편집하는 Razor Pages에 자동으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="266f9-180">유효성 검사 논리를 변경해야 하는 경우 모델에서만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="266f9-181">유효성 검사는 애플리케이션에 걸쳐 일관되게 적용되고, 유효성 검사 논리는 한 위치에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="266f9-182">한 위치의 유효성 검사를 통해 코드를 깨끗이 유지하고 쉽게 유지 관리하고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="266f9-183">DataType 특성 사용</span><span class="sxs-lookup"><span data-stu-id="266f9-183">Use DataType Attributes</span></span>

<span data-ttu-id="266f9-184">`Movie` 클래스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-184">Examine the `Movie` class.</span></span> <span data-ttu-id="266f9-185">`System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성 모음 외에도 서식 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="266f9-186">`[DataType]` 특성은 `ReleaseDate` 및 `Price` 속성에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="266f9-187">`[DataType]` 특성은 다음을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="266f9-188">데이터의 서식을 지정하는 뷰 엔진에 대한 힌트.</span><span class="sxs-lookup"><span data-stu-id="266f9-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="266f9-189">URL의 `<a>` 및 이메일의 `<a href="mailto:EmailAddress.com">` 같은 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="266f9-190">`[RegularExpression]` 특성을 사용하여 데이터 형식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="266f9-191">`[DataType]` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="266f9-192">`[DataType]` 특성은 유효성 검사 특성이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="266f9-193">샘플 애플리케이션에서 날짜는 시간 없이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="266f9-194">`DataType` 열거형은 `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress` 등과 같은 다양한 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="266f9-195">`[DataType]` 특성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="266f9-196">애플리케이션이 자동으로 형식별 기능을 제공하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="266f9-197">예를 들어 `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="266f9-198">HTML5를 지원하는 브라우저에서 날짜 선택기 `DataType.Date`를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="266f9-199">HTML 5 브라우저가 사용하는 HTML 5 `data-`("데이터 대시"로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="266f9-200">유효성 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="266f9-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="266f9-201">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="266f9-202">기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="266f9-203">Entity Framework Core가 `Price`를 데이터베이스의 통화에 올바르게 매핑하기 위해서는 `[Column(TypeName = "decimal(18, 2)")]` 데이터 주석이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="266f9-204">자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="266f9-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="266f9-205">`[DisplayFormat]` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="266f9-206">`ApplyFormatInEditMode` 설정은 편집을 위해 값이 표시될 때 서식 지정이 적용된다고 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="266f9-207">일부 필드에서는 이 동작이 바람직하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="266f9-208">예를 들어 통화 값의 편집 UI에서는 통화 기호가 일반적으로 바람직하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="266f9-209">`[DisplayFormat]` 특성은 단독으로 사용될 수 있지만 일반적으로 `[DataType]` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="266f9-210">`[DataType]` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="266f9-211">`[DataType]` 특성은 `[DisplayFormat]`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="266f9-212">브라우저에서 HTML5 기능을 사용하도록 설정하여 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크 등을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="266f9-213">기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="266f9-214">`[DataType]` 특성은 ASP.NET Core 프레임워크를 활성화하여 데이터를 렌더링할 올바른 필드 템플릿을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="266f9-215">자체적으로 사용되는 경우 `DisplayFormat`은 문자열 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="266f9-216">**참고:** jQuery 유효성 검사는 `[Range]` 특성 및 `DateTime`을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="266f9-217">예를 들어 다음 코드는 날짜가 지정된 범위에 있을 경우에도 클라이언트 쪽 유효성 검사 오류를 항상 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="266f9-218">모델에서 고정된 날짜를 컴파일하는 방식은 좋지 않으므로 `[Range]` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="266f9-219">날짜 범위 및 자주 변경되는 그 밖의 값은 코드로 지정하는 대신 [구성](xref:fundamentals/configuration/index)을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="266f9-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="266f9-220">다음 코드는 특성을 한 줄로 결합하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="266f9-221">[Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)에서는 Razor Pages를 사용한 고급 EF Core 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="266f9-222">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="266f9-222">Apply migrations</span></span>

<span data-ttu-id="266f9-223">클래스에 적용된 DataAnnotations는 스키마를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="266f9-224">예를 들어 `Title` 필드에 적용되는 DataAnnotations는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="266f9-225">문자를 60자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="266f9-226">`null` 값은 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="266f9-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="266f9-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="266f9-228">`Movie` 테이블에는 현재 다음과 같은 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="266f9-229">위의 스키마 변경으로 인해 EF가 예외를 throw하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="266f9-230">그러나 스키마가 모델과 일치하도록 마이그레이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="266f9-231">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="266f9-232">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="266f9-233">`Update-Database`는 `New_DataAnnotations` 클래스의 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="266f9-234">다음과 같이 `Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="266f9-235">업데이트된 `Movie` 테이블에는 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="266f9-236">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="266f9-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="266f9-237">SQLite에는 마이그레이션이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="266f9-238">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="266f9-238">Publish to Azure</span></span>

<span data-ttu-id="266f9-239">Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: SQL Database가 포함된 Azure에서 ASP.NET Core 앱 빌드](/azure/app-service/tutorial-dotnetcore-sqldb-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="266f9-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="266f9-240">Razor Pages에 관한 이 소개를 완료해 주셔서 감사합니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="266f9-241">[Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)은 이 자습서의 유용한 후속편입니다.</span><span class="sxs-lookup"><span data-stu-id="266f9-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="266f9-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="266f9-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="266f9-243">이전: 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="266f9-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
