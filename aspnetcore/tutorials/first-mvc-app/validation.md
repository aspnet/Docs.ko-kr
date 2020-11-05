---
title: 9부. ASP.NET Core MVC 앱에 유효성 검사 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 9부입니다.
ms.author: riande
ms.date: 04/13/2017
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
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 340a66c4a561c6e00bf6f38bcf51abc795aa649c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059093"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="6639e-103">9부. ASP.NET Core MVC 앱에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="6639e-103">Part 9, add validation to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="6639e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6639e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6639e-105">이 섹션에서는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-105">In this section:</span></span>

* <span data-ttu-id="6639e-106">`Movie` 모델에 유효성 검사 논리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-106">Validation logic is added to the `Movie` model.</span></span>
* <span data-ttu-id="6639e-107">사용자가 영화를 만들거나 편집할 때마다 유효성 검사 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-107">You ensure that the validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="keeping-things-dry"></a><span data-ttu-id="6639e-108">반복 금지 준수</span><span class="sxs-lookup"><span data-stu-id="6639e-108">Keeping things DRY</span></span>

<span data-ttu-id="6639e-109">MVC의 설계 원칙 중 하나는 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)(반복 금지, Don't Repeat Yourself)입니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-109">One of the design tenets of MVC is [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Don't Repeat Yourself").</span></span> <span data-ttu-id="6639e-110">ASP.NET Core MVC는 기능 또는 동작을 한 번만 지정한 다음, 앱의 모든 곳에 반영하는 것을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-110">ASP.NET Core MVC encourages you to specify functionality or behavior only once, and then have it be reflected everywhere in an app.</span></span> <span data-ttu-id="6639e-111">이렇게 하면 작성할 코드의 양이 줄어들고 작성 코드에서 오류가 발생할 가능성이 낮아지며 테스트 및 유지 관리가 더 쉬워집니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-111">This reduces the amount of code you need to write and makes the code you do write less error prone, easier to test, and easier to maintain.</span></span>

<span data-ttu-id="6639e-112">MVC 및 Entity Framework Core Code First가 제공하는 유효성 검사 지원은 반복 금지 원칙의 좋은 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-112">The validation support provided by MVC and Entity Framework Core Code First is a good example of the DRY principle in action.</span></span> <span data-ttu-id="6639e-113">유효성 검사 규칙을 한 위치(모델 클래스에서)에서 선언적으로 지정하고 앱의 모든 위치에서 해당 규칙을 시행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-113">You can declaratively specify validation rules in one place (in the model class) and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a><span data-ttu-id="6639e-114">유효성 검사 오류 UI</span><span class="sxs-lookup"><span data-stu-id="6639e-114">Validation Error UI</span></span>

<span data-ttu-id="6639e-115">앱을 실행하고 Movies 컨트롤러로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-115">Run the app and navigate to the Movies controller.</span></span>

<span data-ttu-id="6639e-116">**Create New** 링크를 눌러 새 영화를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-116">Tap the **Create New** link to add a new movie.</span></span> <span data-ttu-id="6639e-117">일부 잘못된 값으로 양식을 기입합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-117">Fill out the form with some invalid values.</span></span> <span data-ttu-id="6639e-118">jQuery 클라이언트 쪽 유효성 검사가 오류를 감지하자마자 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-118">As soon as jQuery client side validation detects the error, it displays an error message.</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 표시된 영화 보기 양식](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="6639e-120">양식에서 잘못된 값을 포함하고 있는 각 필드에 적절한 유효성 검사 오류 메시지가 자동으로 렌더링되는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-120">Notice how the form has automatically rendered an appropriate validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="6639e-121">오류는 클라이언트 쪽(JavaScript 및 jQuery 사용 시) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-121">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (in case a user has JavaScript disabled).</span></span>

<span data-ttu-id="6639e-122">가장 큰 이점은 이 유효성 검사 UI를 사용하기 위해 `MoviesController` 클래스 또는 *Create.cshtml* 의 코드를 한 줄도 변경할 필요가 없다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-122">A significant benefit is that you didn't need to change a single line of code in the `MoviesController` class or in the *Create.cshtml* view in order to enable this validation UI.</span></span> <span data-ttu-id="6639e-123">이 자습서에서 이전에 만든 컨트롤러 및 보기는 `Movie` 모델 클래스의 속성에 유효성 검사 특성을 사용하여 지정한 유효성 검사 규칙을 자동으로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-123">The controller and views you created earlier in this tutorial automatically picked up the validation rules that you specified by using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="6639e-124">`Edit` 작업 메서드로 유효성 검사를 테스트하여 동일한 유효성 검사가 적용되는지 확인해보세요.</span><span class="sxs-lookup"><span data-stu-id="6639e-124">Test validation using the `Edit` action method, and the same validation is applied.</span></span>

<span data-ttu-id="6639e-125">양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-125">The form data isn't sent to the server until there are no client side validation errors.</span></span> <span data-ttu-id="6639e-126">[Fiddler 도구](https://www.telerik.com/fiddler) 또는 [F12 개발자 도구](/microsoft-edge/devtools-guide)를 사용하여 `HTTP Post` 메서드에 중단점을 넣어 이를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-126">You can verify this by putting a break point in the `HTTP Post` method, by using the [Fiddler tool](https://www.telerik.com/fiddler) , or the [F12 Developer tools](/microsoft-edge/devtools-guide).</span></span>

## <a name="how-validation-works"></a><span data-ttu-id="6639e-127">유효성 검사 작동 방식</span><span class="sxs-lookup"><span data-stu-id="6639e-127">How validation works</span></span>

<span data-ttu-id="6639e-128">컨트롤러나 보기의 코드를 전혀 수정하지 않고도 어떻게 유효성 검사 UI가 생성되는지 궁금할 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-128">You might wonder how the validation UI was generated without any updates to the code in the controller or views.</span></span> <span data-ttu-id="6639e-129">다음 코드는 두 `Create` 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-129">The following code shows the two `Create` methods.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

<span data-ttu-id="6639e-130">첫 번째(HTTP GET) `Create` 작업 메서드는 최초 Create 양식을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-130">The first (HTTP GET) `Create` action method displays the initial Create form.</span></span> <span data-ttu-id="6639e-131">두 번째(`[HttpPost]`) 버전은 양식 게시를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-131">The second (`[HttpPost]`) version handles the form post.</span></span> <span data-ttu-id="6639e-132">두 번째 `Create` 메서드(`[HttpPost]` 버전)는 `ModelState.IsValid`를 호출하여 영화의 유효성 검사 오류 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-132">The second `Create` method (The `[HttpPost]` version) calls `ModelState.IsValid` to check whether the movie has any validation errors.</span></span> <span data-ttu-id="6639e-133">이 메서드를 호출하면 개체에 적용된 모든 유효성 검사 특성이 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-133">Calling this method evaluates any validation attributes that have been applied to the object.</span></span> <span data-ttu-id="6639e-134">개체에 유효성 검사 오류가 있으면 `Create` 메서드는 양식을 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-134">If the object has validation errors, the `Create` method re-displays the form.</span></span> <span data-ttu-id="6639e-135">오류가 없으면 메서드가 데이터베이스에 새 영화를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-135">If there are no errors, the method saves the new movie in the database.</span></span> <span data-ttu-id="6639e-136">이 영화 예제에서는 클라이언트 쪽에서 유효성 검사 오류가 감지되면 서버에 양식이 게시되지 않으며, 두 번째 `Create` 메서드가 절대 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-136">In our movie example, the form isn't posted to the server when there are validation errors detected on the client side; the second `Create` method is never called when there are client side validation errors.</span></span> <span data-ttu-id="6639e-137">브라우저에서 JavaScript를 사용하지 않으면 클라이언트 유효성 검사가 비활성화되며 모든 유효성 검사 오류를 감지하는 HTTP POST `Create` 메서드 `ModelState.IsValid`를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-137">If you disable JavaScript in your browser, client validation is disabled and you can test the HTTP POST `Create` method `ModelState.IsValid` detecting any validation errors.</span></span>

<span data-ttu-id="6639e-138">`[HttpPost] Create` 메서드에서 중단점을 설정하고 메서드가 호출되지 않았는지 확인할 수 있으며, 유효성 검사 오류가 감지되면 클라이언트 쪽 유효성 검사가 양식 데이터를 제출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-138">You can set a break point in the `[HttpPost] Create` method and verify the method is never called, client side validation won't submit the form data when validation errors are detected.</span></span> <span data-ttu-id="6639e-139">브라우저에서 JavaScript를 사용하지 않고 오류가 있는 상태로 양식을 제출하면 중단점에 이르게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-139">If you disable JavaScript in your browser, then submit the form with errors, the break point will be hit.</span></span> <span data-ttu-id="6639e-140">JavaScript 없이도 여전히 완전한 유효성 검사가 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-140">You still get full validation without JavaScript.</span></span> 

<span data-ttu-id="6639e-141">다음 이미지는 Firefox 브라우저에서 JavaScript를 사용하지 않도록 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-141">The following image shows how to disable JavaScript in the Firefox browser.</span></span>

![Firefox: 옵션의 콘텐츠 탭에서 Javascript 사용 확인란의 선택을 취소합니다.](~/tutorials/first-mvc-app/validation/_static/ff.png)

<span data-ttu-id="6639e-143">다음 이미지는 Chrome 브라우저에서 JavaScript를 사용하지 않도록 설정하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-143">The following image shows how to disable JavaScript in the Chrome browser.</span></span>

![Google Chrome: 콘텐츠 설정의 Javascript에서 모든 사이트의 JavaScript 실행 허용 안 함을 선택합니다.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

<span data-ttu-id="6639e-145">JavaScript를 사용하지 않도록 설정한 뒤에 잘못된 데이터를 게시하고 디버거를 단계별로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-145">After you disable JavaScript, post invalid data and step through the debugger.</span></span>

![잘못된 데이터 게시에 대한 디버깅 중에는 ModelState.IsValid의 Intellisense에 해당 값이 false로 표시됩니다.](~/tutorials/first-mvc-app/validation/_static/ms.png)

<span data-ttu-id="6639e-147">*Create.cshtml* 보기 템플릿의 일부분이 다음 태그에 나타나 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-147">The portion of the *Create.cshtml* view template is shown in the following markup:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

<span data-ttu-id="6639e-148">위의 태크는 최초 양식을 표시하고 오류 발생 시 양식을 다시 표시하기 위해 작업 메서드에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-148">The preceding markup is used by the action methods to display the initial form and to redisplay it in the event of an error.</span></span>

<span data-ttu-id="6639e-149">[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하여 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-149">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client side.</span></span> <span data-ttu-id="6639e-150">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-150">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="6639e-151">자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6639e-151">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="6639e-152">이 방식의 가장 멋진 장점은 컨트롤러나 `Create` 보기 템플릿이 실제 적용되는 유효성 검사 규칙이나 표시되는 특정 오류 메시지에 대해 전혀 알지 못한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-152">What's really nice about this approach is that neither the controller nor the `Create` view template knows anything about the actual validation rules being enforced or about the specific error messages displayed.</span></span> <span data-ttu-id="6639e-153">유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-153">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="6639e-154">동일한 유효성 검사 규칙이 `Edit` 보기 및 모델을 편집하는 만들 수 있는 모든 다른 보기 템플릿에 자동으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-154">These same validation rules are automatically applied to the `Edit` view and any other views templates you might create that edit your model.</span></span>

<span data-ttu-id="6639e-155">유효성 검사 논리를 변경해야 할 경우 모델에 유효성 검사 특성을 추가하여(이 예제에서는 `Movie` 클래스) 정확히 한 곳에서 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-155">When you need to change validation logic, you can do so in exactly one place by adding validation attributes to the model (in this example, the `Movie` class).</span></span> <span data-ttu-id="6639e-156">모든 유효성 검사 논리가 한 곳에서 정의되어 모든 곳에서 사용되므로 애플리케이션의 서로 다른 부분이 규칙 적용 방법에 부합하는지 우려하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-156">You won't have to worry about different parts of the application being inconsistent with how the rules are enforced — all validation logic will be defined in one place and used everywhere.</span></span> <span data-ttu-id="6639e-157">이렇게 하면 코드가 매우 깔끔해지고 유지 관리 및 확장이 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-157">This keeps the code very clean, and makes it easy to maintain and evolve.</span></span> <span data-ttu-id="6639e-158">또한 반복 금지 원칙에 완전히 부합하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-158">And it means that you'll be fully honoring the DRY principle.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="6639e-159">DataType 특성 사용</span><span class="sxs-lookup"><span data-stu-id="6639e-159">Using DataType Attributes</span></span>

<span data-ttu-id="6639e-160">*Movie.cs* 파일을 열고 `Movie` 클래스를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-160">Open the *Movie.cs* file and examine the `Movie` class.</span></span> <span data-ttu-id="6639e-161">`System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성 모음 외에도 서식 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-161">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="6639e-162">이미 `DataType` 열거 값을 출시일 밑 가격 필드에 적용했습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-162">We've already applied a `DataType` enumeration value to the release date and to the price fields.</span></span> <span data-ttu-id="6639e-163">다음 코드는 적절한 `DataType` 특성이 적용된 `ReleaseDate` 및 `Price` 속성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-163">The following code shows the `ReleaseDate` and `Price` properties with the appropriate `DataType` attribute.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="6639e-164">`DataType` 특성은 데이터에 서식을 지정하도록(그리고 URL에 대한 `<a>` 및 이메일에 대한 `<a href="mailto:EmailAddress.com">` 과 같은 요소/특성을 제공하도록) 보기 엔진에 대한 힌트만 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-164">The `DataType` attributes only provide hints for the view engine to format the data (and supplies elements/attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span> <span data-ttu-id="6639e-165">`RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-165">You can use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="6639e-166">`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용되며 유효성 검사 특성이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-166">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type, they're not validation attributes.</span></span> <span data-ttu-id="6639e-167">이 예제에서는 시간을 제외한 날짜만 추적하고자 합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-167">In this case we only want to keep track of the date, not the time.</span></span> <span data-ttu-id="6639e-168">`DataType` 열거형은 Date, Time, PhoneNumber, Currency, EmailAddress를 비롯한 많은 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-168">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress and more.</span></span> <span data-ttu-id="6639e-169">`DataType` 특성을 통해 응용 프로그램에서 자동으로 형식별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-169">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="6639e-170">예를 들어, `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들고 HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-170">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="6639e-171">`DataType` 특성은 HTML 5 브라우저가 인식할 수 있는 HTML 5 `data-`(데이터 대시라고 발음합니다) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-171">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="6639e-172">`DataType` 특성은 유효성 검사를 전혀 제공하지 **않습니다**</span><span class="sxs-lookup"><span data-stu-id="6639e-172">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="6639e-173">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-173">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="6639e-174">기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-174">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="6639e-175">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-175">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="6639e-176">`ApplyFormatInEditMode` 설정은 값이 편집을 위해 텍스트 상자에 표시될 때 서식 지정도 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-176">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="6639e-177">(예를 들어 통화 값의 경우 편집을 위한 텍스트 상자에 통화 기호를 사용하지 않는 등, 특정 필드에 대해서는 이 기능이 필요없을 수도 있습니다.)</span><span class="sxs-lookup"><span data-stu-id="6639e-177">(You might not want that for some fields — for example, for currency values, you probably don't want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="6639e-178">`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-178">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="6639e-179">`DataType` 특성은 데이터를 화면에 렌더링하는 방법이 아닌 데이터의 의미 체계를 전달하고 DisplayFormat으로는 얻을 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-179">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="6639e-180">브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크 등을 표시합니다).</span><span class="sxs-lookup"><span data-stu-id="6639e-180">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>

* <span data-ttu-id="6639e-181">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 서식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-181">By default, the browser will render data using the correct format based on your locale.</span></span>

* <span data-ttu-id="6639e-182">`DataType` 특성을 사용하면 MVC가 데이터 렌더링에 적합한 필드 템플릿을 선택할 수 있습니다(`DisplayFormat`만 사용할 경우 문자열 템플릿을 사용합니다).</span><span class="sxs-lookup"><span data-stu-id="6639e-182">The `DataType` attribute can enable MVC to choose the right field template to render the data (the `DisplayFormat` if used by itself uses the string template).</span></span>

> [!NOTE]
> <span data-ttu-id="6639e-183">jQuery 유효성 검사는 `Range` 특성 및 `DateTime`에서 동작하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-183">jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="6639e-184">예를 들어 다음 코드는 날짜가 지정된 범위에 존재할 경우에도 항상 클라이언트 쪽 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-184">For example, the following code will always display a client side validation error, even when the date is in the specified range:</span></span>
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

<span data-ttu-id="6639e-185">`DateTime`에 `Range` 특성을 사용하려면 jQuery 날짜 유효성 검사를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-185">You will need to disable jQuery date validation to use the `Range` attribute with `DateTime`.</span></span> <span data-ttu-id="6639e-186">일반적으로 모델에서 고정된 날짜를 컴파일하는 방식은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-186">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="6639e-187">다음 코드는 특성을 한 줄로 결합하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-187">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="6639e-188">시리즈의 다음 부분에서는 앱을 검토하고 자동으로 생성된 `Details` 및 `Delete` 메서드를 일부 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="6639e-188">In the next part of the series, we review the app and make some improvements to the automatically generated `Details` and `Delete` methods.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6639e-189">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6639e-189">Additional resources</span></span>

* [<span data-ttu-id="6639e-190">양식 사용</span><span class="sxs-lookup"><span data-stu-id="6639e-190">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="6639e-191">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="6639e-191">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="6639e-192">태그 도우미 소개</span><span class="sxs-lookup"><span data-stu-id="6639e-192">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6639e-193">태그 도우미 작성</span><span class="sxs-lookup"><span data-stu-id="6639e-193">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> <span data-ttu-id="6639e-194">[이전](new-field.md)
> [다음](details.md)</span><span class="sxs-lookup"><span data-stu-id="6639e-194">[Previous](new-field.md)
[Next](details.md)</span></span>  
