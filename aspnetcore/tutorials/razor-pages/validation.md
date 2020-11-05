---
title: '8부. ASP.NET Core :::no-loc(Razor)::: Page에 유효성 검사 추가'
author: rick-anderson
description: ':::no-loc(Razor)::: Pages에 대한 자습서 시리즈의 8부입니다.'
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 991a0f29c0edc5a220dfde69bd22dc4ed758394d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060731"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="4fd85-103">8부. ASP.NET Core :::no-loc(Razor)::: Page에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="4fd85-103">Part 8, add validation to an ASP.NET Core :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="4fd85-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4fd85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4fd85-105">이 섹션에서 유효성 검사 논리는 `Movie` 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="4fd85-106">유효성 검사 규칙은 사용자가 동영상을 만들거나 편집할 때 언제든지 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="4fd85-107">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="4fd85-107">Validation</span></span>

<span data-ttu-id="4fd85-108">소프트웨어 개발의 주요 개념은 [반복 금지](https://wikipedia.org/wiki/Don%27t_repeat_yourself)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="4fd85-109">:::no-loc(Razor)::: Pages는 기능이 한 번 정의된 곳의 개발을 원활하게 하고 앱 전체에서 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-109">:::no-loc(Razor)::: Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="4fd85-110">DRY는 다음 작업에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-110">DRY can help:</span></span>

* <span data-ttu-id="4fd85-111">앱에서 코드의 양 감소</span><span class="sxs-lookup"><span data-stu-id="4fd85-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="4fd85-112">코드에 오류 감소 및 쉽게 테스트 및 유지 관리</span><span class="sxs-lookup"><span data-stu-id="4fd85-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="4fd85-113">:::no-loc(Razor)::: Pages에서 제공하는 유효성 검사 지원 및 Entity Framework는 반복 금지 원칙의 좋은 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-113">The validation support provided by :::no-loc(Razor)::: Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="4fd85-114">유효성 검사 규칙은 한 위치(모델 클래스에서)에서 선언적으로 지정되고 규칙은 앱의 모든 위치에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="4fd85-115">영화 모델에 유효성 검사 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="4fd85-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="4fd85-116">DataAnnotations 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 모음을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="4fd85-117">또한 DataAnnotations는 서식 지정을 돕고 아무런 유효성 검사도 제공하지 않는 `DataType` 같은 서식 지정 특성도 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="4fd85-118">기본 제공되는 `Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="4fd85-119">유효성 검사 특성은 해당 특성이 적용되는 모델 속성에 시행하려는 동작을 지정합니다</span><span class="sxs-lookup"><span data-stu-id="4fd85-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="4fd85-120">`Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 함을 지정합니다. 그러나 사용자가 이 유효성 검사를 만족시키기 위해 공백을 입력하는 것까지 막을 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="4fd85-121">`RegularExpression` 특성은 입력할 수 있는 문자를 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="4fd85-122">이전 코드에서 “Genre”는 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="4fd85-123">문자만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-123">Must only use letters.</span></span>
  * <span data-ttu-id="4fd85-124">첫 번째 문자는 대문자여야 합니다</span><span class="sxs-lookup"><span data-stu-id="4fd85-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="4fd85-125">공백, 숫자 및 특수 문자는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="4fd85-126">`RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="4fd85-127">첫 번째 문자는 대문자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="4fd85-128">이어지는 자리에서는 특수 문자와 숫자가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="4fd85-129">“PG-13”은 등급에서는 유효하지만 “Genre”에서는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="4fd85-130">`Range` 특성은 지정한 범위 내로 값을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="4fd85-131">`StringLength` 특성을 사용하면 문자열 속성의 최대 길이와, 그리고 필요에 따라 최소 길이를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="4fd85-132">값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필수적이며 `[Required]` 특성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="4fd85-133">ASP.NET Core가 자동으로 적용하는 유효성 검사 규칙을 사용하면 앱을 더욱 강력하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="4fd85-134">또한 무언가의 유효성 검사를 잊거나, 실수로 데이터베이스에 불량 데이터가 들어가지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="4fd85-135">:::no-loc(Razor)::: Pages에서 유효성 검사 오류 UI</span><span class="sxs-lookup"><span data-stu-id="4fd85-135">Validation Error UI in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="4fd85-136">앱을 실행하고 Pages/Movies로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="4fd85-137">**새로 만들기** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-137">Select the **Create New** link.</span></span> <span data-ttu-id="4fd85-138">일부 잘못된 값으로 양식을 완성합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="4fd85-139">jQuery 클라이언트 쪽 유효성 검사에서 오류를 발견하면 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="4fd85-141">양식에서 잘못된 값을 포함하는 각 필드에 유효성 검사 오류 메시지를 자동으로 렌더링하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="4fd85-142">오류는 클라이언트 쪽(JavaScript 및 jQuery 사용) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="4fd85-143">중요한 이점은 만들기 또는 편집 페이지에서 코드 변경이 필요하지 **않았다는** 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="4fd85-144">DataAnnotations가 모델에 적용된 후 유효성 검사 UI가 활성화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="4fd85-145">이 자습서에서 만든 :::no-loc(Razor)::: Pages는 자동으로 유효성 검사 규칙을 선택했습니다(`Movie` 모델 클래스의 속성에서 유효성 검사 특성 사용).</span><span class="sxs-lookup"><span data-stu-id="4fd85-145">The :::no-loc(Razor)::: Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="4fd85-146">편집 페이지를 사용하는 테스트 유효성 검사는 동일한 유효성 검사가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="4fd85-147">양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="4fd85-148">양식 데이터가 다음 방법 중 하나 이상으로 게시되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="4fd85-149">`OnPostAsync` 메서드에 중단점을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="4fd85-150">양식을 제출합니다( **만들기** 또는 **저장** 선택).</span><span class="sxs-lookup"><span data-stu-id="4fd85-150">Submit the form (select **Create** or **Save** ).</span></span> <span data-ttu-id="4fd85-151">중단점은 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-151">The break point is never hit.</span></span>
* <span data-ttu-id="4fd85-152">[Fiddler 도구](https://www.telerik.com/fiddler)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="4fd85-153">브라우저 개발자 도구를 사용하여 네트워크 트래픽을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="4fd85-154">서버 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="4fd85-154">Server-side validation</span></span>

<span data-ttu-id="4fd85-155">브라우저에서 JavaScript가 비활성화된 경우 오류가 있는 양식 제출에서 서버에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="4fd85-156">선택 가능한 서버 쪽 유효성 검사 테스트:</span><span class="sxs-lookup"><span data-stu-id="4fd85-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="4fd85-157">브라우저에서 JavaScript를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="4fd85-158">브라우저의 개발자 도구를 사용하여 JavaScript를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="4fd85-159">브라우저에서 JavaScript를 비활성화할 수 없는 경우 다른 브라우저를 시도하세요.</span><span class="sxs-lookup"><span data-stu-id="4fd85-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="4fd85-160">만들기 또는 편집 페이지의 `OnPostAsync` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="4fd85-161">잘못된 데이터가 포함된 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="4fd85-162">모델 상태가 유효하지 않은 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="4fd85-163">또는 [서버에서 클라이언트 쪽 유효성 검사를 사용하지 않도록 설정](xref:mvc/models/validation#disable-client-side-validation)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="4fd85-164">다음 코드는 자습서의 앞부분에서 스캐폴드한 *Create.cshtml* 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="4fd85-165">만들기 및 편집 페이지에서 초기 양식을 표시하고 오류가 발생한 경우 양식을 다시 표시하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="4fd85-166">[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="4fd85-167">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="4fd85-168">자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fd85-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="4fd85-169">만들기 및 편집 페이지에 유효성 검사 규칙이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="4fd85-170">유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="4fd85-171">이러한 유효성 검사 규칙은 `Movie` 모델을 편집하는 :::no-loc(Razor)::: Pages에 자동으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-171">These validation rules are automatically applied to :::no-loc(Razor)::: Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="4fd85-172">유효성 검사 논리를 변경해야 하는 경우 모델에서만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="4fd85-173">유효성 검사는 애플리케이션에 걸쳐 일관되게 적용됩니다(유효성 검사 논리는 한 위치에서 정의됨).</span><span class="sxs-lookup"><span data-stu-id="4fd85-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="4fd85-174">한 위치의 유효성 검사를 통해 코드를 깨끗이 유지하고 쉽게 유지 관리하고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="4fd85-175">데이터 형식 특성 사용</span><span class="sxs-lookup"><span data-stu-id="4fd85-175">Using DataType Attributes</span></span>

<span data-ttu-id="4fd85-176">`Movie` 클래스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-176">Examine the `Movie` class.</span></span> <span data-ttu-id="4fd85-177">`System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성 모음 외에도 서식 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="4fd85-178">`DataType` 특성은 `ReleaseDate` 및 `Price` 속성에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="4fd85-179">`DataType` 특성은 데이터의 서식을 지정하도록 뷰 엔진에 대한 힌트만을 제공합니다(그리고 URL에 대한 `<a>` 및 전자 메일에 대한 `<a href="mailto:EmailAddress.com">`과 같은 특성 제공).</span><span class="sxs-lookup"><span data-stu-id="4fd85-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="4fd85-180">`RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="4fd85-181">`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="4fd85-182">`DataType` 특성은 유효성 검사 특성이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="4fd85-183">샘플 애플리케이션에서 날짜는 시간 없이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="4fd85-184">`DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 전자 메일 주소 등과 같은 많은 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="4fd85-185">`DataType` 특성을 통해 애플리케이션에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="4fd85-186">예를 들어 `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="4fd85-187">HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기가 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="4fd85-188">`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-188">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="4fd85-189">`DataType` 특성은 유효성 검사를 전혀 제공하지 **않습니다**</span><span class="sxs-lookup"><span data-stu-id="4fd85-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="4fd85-190">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="4fd85-191">기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="4fd85-192">Entity Framework Core가 `Price`를 데이터베이스의 통화에 올바르게 매핑하기 위해서는 `[Column(TypeName = "decimal(18, 2)")]` 데이터 주석이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="4fd85-193">자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fd85-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="4fd85-194">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="4fd85-195">`ApplyFormatInEditMode` 설정은 값이 편집을 위해 표시될 때 서식 지정이 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="4fd85-196">일부 필드에 대해 해당 동작을 원하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="4fd85-197">예를 들어 통화 값의 편집 UI에서 통화 기호를 원하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="4fd85-198">`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="4fd85-199">`DataType` 특성은 데이터를 화면에 렌더링하는 방법이 아닌 데이터의 의미 체계를 전달하고 DisplayFormat으로는 얻을 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="4fd85-200">브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크 등을 표시합니다).</span><span class="sxs-lookup"><span data-stu-id="4fd85-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="4fd85-201">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="4fd85-202">`DataType` 특성은 ASP.NET Core 프레임워크를 활성화하여 데이터를 렌더링할 올바른 필드 템플릿을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="4fd85-203">자체적으로 사용되는 경우 `DisplayFormat`은 문자열 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-203">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="4fd85-204">**참고:** jQuery 유효성 검사는 `Range` 특성 및 `DateTime`을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-204">**Note:** jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="4fd85-205">예를 들어 다음 코드는 날짜가 지정된 범위에 있을 경우에도 클라이언트 쪽 유효성 검사 오류를 항상 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="4fd85-206">일반적으로 모델에서 고정된 날짜를 컴파일하는 방식은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="4fd85-207">다음 코드는 특성을 한 줄로 결합하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="4fd85-208">[:::no-loc(Razor)::: Pages 및 EF Core 시작](xref:data/ef-rp/intro)에서는 :::no-loc(Razor)::: Pages를 사용한 고급 EF Core 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-208">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with :::no-loc(Razor)::: Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="4fd85-209">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="4fd85-209">Apply migrations</span></span>

<span data-ttu-id="4fd85-210">클래스에 적용된 DataAnnotations는 스키마를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-210">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="4fd85-211">예를 들어 `Title` 필드에 적용되는 DataAnnotations는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="4fd85-212">문자를 60자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="4fd85-213">`null` 값은 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fd85-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fd85-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fd85-215">`Movie` 테이블에는 현재 다음과 같은 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-215">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="4fd85-216">위의 스키마 변경으로 인해 EF가 예외를 throw하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="4fd85-217">그러나 스키마가 모델과 일치하도록 마이그레이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="4fd85-218">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="4fd85-219">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="4fd85-220">`Update-Database`는 `New_DataAnnotations` 클래스의 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="4fd85-221">다음과 같이 `Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="4fd85-222">업데이트된 `Movie` 테이블에는 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-222">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4fd85-223">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fd85-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4fd85-224">SQLite에는 마이그레이션이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="4fd85-225">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="4fd85-225">Publish to Azure</span></span>

<span data-ttu-id="4fd85-226">Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: SQL Database가 포함된 Azure에서 ASP.NET Core 앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fd85-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="4fd85-227">:::no-loc(Razor)::: Pages에 관한 이 소개를 완료해 주셔서 감사합니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-227">Thanks for completing this introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="4fd85-228">[:::no-loc(Razor)::: Pages 및 EF Core 시작](xref:data/ef-rp/intro)은 이 자습서의 유용한 후속편입니다.</span><span class="sxs-lookup"><span data-stu-id="4fd85-228">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4fd85-229">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4fd85-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="4fd85-230">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4fd85-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="4fd85-231">이전: 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="4fd85-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
