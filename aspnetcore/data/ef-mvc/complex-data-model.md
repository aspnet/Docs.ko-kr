---
title: '자습서: 복잡한 데이터 모델 만들기 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587790"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="78593-103">자습서: 복잡한 데이터 모델 만들기 - ASP.NET MVC 및 EF Core 사용</span><span class="sxs-lookup"><span data-stu-id="78593-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="78593-104">이전 자습서에서는 세 가지 엔터티로 구성된 간단한 데이터 모델을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="78593-105">이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="78593-106">완료되면 엔터티 클래스는 다음 그림에 표시된 완성된 데이터 모델을 구성하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="78593-108">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="78593-109">데이터 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="78593-109">Customize the Data model</span></span>
> * <span data-ttu-id="78593-110">Student 엔터티 변경</span><span class="sxs-lookup"><span data-stu-id="78593-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="78593-111">Instructor 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-111">Create Instructor entity</span></span>
> * <span data-ttu-id="78593-112">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="78593-113">Course 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-113">Modify Course entity</span></span>
> * <span data-ttu-id="78593-114">Department 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-114">Create Department entity</span></span>
> * <span data-ttu-id="78593-115">Enrollment 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="78593-116">데이터베이스 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-116">Update the database context</span></span>
> * <span data-ttu-id="78593-117">테스트 데이터로 데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="78593-117">Seed database with test data</span></span>
> * <span data-ttu-id="78593-118">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="78593-118">Add a migration</span></span>
> * <span data-ttu-id="78593-119">연결 문자열 변경</span><span class="sxs-lookup"><span data-stu-id="78593-119">Change the connection string</span></span>
> * <span data-ttu-id="78593-120">데이터베이스 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78593-121">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="78593-121">Prerequisites</span></span>

* [<span data-ttu-id="78593-122">EF Core 마이그레이션 사용</span><span class="sxs-lookup"><span data-stu-id="78593-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="78593-123">데이터 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="78593-123">Customize the Data model</span></span>

<span data-ttu-id="78593-124">이 섹션에서는 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하는 특성을 사용하여 데이터 모델을 사용자 지정하는 방법을 배웁니다.</span><span class="sxs-lookup"><span data-stu-id="78593-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="78593-125">그런 다음, 이어지는 몇 개 섹션에서는 특성을 이미 만든 클래스에 추가하고, 모델에 나머지 엔터티 형식에 대한 새 클래스를 만들어 완벽한 학교 데이터 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="78593-126">DataType 특성</span><span class="sxs-lookup"><span data-stu-id="78593-126">The DataType attribute</span></span>

<span data-ttu-id="78593-127">학생 등록 날짜의 경우, 이 필드에서 필요한 것은 날짜이지만 현재 모든 웹 페이지는 날짜와 함께 시간을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="78593-128">데이터 주석 특성을 사용하면 데이터를 표시하는 모든 보기에서 표시 형식을 해결하는 하나의 코드 변경을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="78593-129">수행 방법의 예제를 보려면 특성을 `Student` 클래스의 `EnrollmentDate` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="78593-130">*Models/Student.cs* 에서 다음 예제와 같이 `System.ComponentModel.DataAnnotations` 네임스페이스에 대한 `using` 문을 추가하고 `DataType` 및 `DisplayFormat` 특성을 `EnrollmentDate` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="78593-131">`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="78593-132">이 경우에는 날짜 및 시간이 아닌 날짜만 추적하고자 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="78593-133">`DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 다양한 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="78593-134">`DataType` 특성을 통해 응용 프로그램에서 자동으로 형식별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="78593-135">예를 들어, `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들고 HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="78593-136">`DataType` 특성은 HTML 5 브라우저가 인식할 수 있는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="78593-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="78593-137">`DataType` 특성은 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="78593-138">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="78593-139">기본적으로 데이터 필드는 서버의 CultureInfo의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="78593-140">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="78593-141">`ApplyFormatInEditMode` 설정은 값이 편집을 위해 텍스트 상자에 표시될 때 서식 지정도 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="78593-142">(통화 값의 경우 편집을 위한 텍스트 상자에 통화 기호를 표시하지 않는 등, 특정 필드에 대해서 이것이 필요하지 않을 수 있습니다.)</span><span class="sxs-lookup"><span data-stu-id="78593-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="78593-143">`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성도 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="78593-144">`DataType` 특성은 데이터를 화면에 렌더링하는 방법과 반대로 데이터의 의미 체계를 전달하고 `DisplayFormat`으로 가져올 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="78593-145">브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크, 일부 클라이언트 쪽 입력 유효성 검사 등을 표시하기 위해).</span><span class="sxs-lookup"><span data-stu-id="78593-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="78593-146">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 서식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="78593-147">자세한 내용은 [\<input> 태그 도우미 설명서](../../mvc/views/working-with-forms.md#the-input-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78593-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="78593-148">앱을 실행하고 학생 인덱스 페이지로 이동하여 등록 날짜에 대해 시간이 더 이상 표시되지 않음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="78593-149">학생 모델을 사용하는 다른 보기에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-149">The same will be true for any view that uses the Student model.</span></span>

![시간 없이 날짜를 표시하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="78593-151">StringLength 특성</span><span class="sxs-lookup"><span data-stu-id="78593-151">The StringLength attribute</span></span>

<span data-ttu-id="78593-152">특성을 사용하여 데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="78593-153">`StringLength` 특성은 데이터베이스의 최대 길이를 설정하고, ASP.NET Core MVC에 대한 클라이언트 쪽 및 서버 쪽 유효성을 검사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="78593-154">이 특성의 최소 문자열 길이를 지정할 수도 있지만, 최소값은 데이터베이스 스키마에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="78593-155">사용자가 이름을 50자 이하로 입력하였는지를 확인한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="78593-156">이 제한 사항을 추가하려면 다음 예제와 같이 `StringLength` 특성을 `LastName` 및 `FirstMidName` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="78593-157">`StringLength` 특성은 이름에 공백을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="78593-158">`RegularExpression` 특성을 사용하여 입력에 제한을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="78593-159">예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 알파벳순이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="78593-160">`MaxLength` 특성은 `StringLength` 특성과 비슷한 기능을 제공하지만, 클라이언트 쪽 유효성 검사는 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="78593-161">이제 데이터베이스 스키마에서 변경이 필요한 방식으로 데이터베이스 모델이 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="78593-162">애플리케이션 UI를 사용하여 데이터베이스에 추가했을 수도 있는 데이터 손실 없이 마이그레이션을 사용하여 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="78593-163">변경 내용을 저장하고 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-163">Save your changes and build the project.</span></span> <span data-ttu-id="78593-164">그런 다음, 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="78593-165">`migrations add` 명령은 변경으로 인해 두 개의 열에 대한 최대 길이가 짧아질 수 있으므로 데이터 손실이 발생할 수 있음에 대해 경고합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="78593-166">마이그레이션은 *\<timeStamp>_MaxLengthOnNames.cs* 라는 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="78593-167">이 파일에는 현재 데이터 모델과 일치하도록 데이터베이스를 업데이트하는 `Up` 메서드의 코드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="78593-168">`database update` 명령은 해당 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="78593-169">timestamp가 접두사로 사용된 마이그레이션 파일 이름이 마이그레이션을 요청하는 Entity Framework에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="78593-170">update-database 명령을 실행하기 전에 여러 마이그레이션을 만들 수 있습니다. 그런 다음, 모든 마이그레이션이 생성된 순서 대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="78593-171">앱을 실행하고, **학생** 탭을 선택하고, **새로 만들기** 를 클릭한 다음, 50자보다 긴 이름을 입력해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="78593-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="78593-172">애플리케이션에서 이 작업을 수행하지 못하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="78593-173">열 특성</span><span class="sxs-lookup"><span data-stu-id="78593-173">The Column attribute</span></span>

<span data-ttu-id="78593-174">또한 특성을 사용하여 클래스 및 속성을 데이터베이스에 매핑하는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="78593-175">필드에 중간 이름이 포함될 수 있으므로 첫 번째 이름 필드에 이름 `FirstMidName`을 사용한 경우를 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="78593-176">하지만 데이터베이스에 임시 쿼리를 작성하는 사용자는 해당 이름이 익숙하기 때문에 데이터베이스 열 이름을 `FirstName`으로 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="78593-177">이 매핑을 수행하기 위해 `Column` 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="78593-178">`Column` 특성은 데이터베이스를 만드는 시기를 지정하고 `FirstMidName` 속성에 매핑하는 `Student` 테이블의 열 이름은 `FirstName`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="78593-179">즉, 코드가 `Student.FirstMidName`을 참조하는 경우 데이터는 `Student` 테이블의 `FirstName` 열에서 가져오거나 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="78593-180">열 이름을 지정하지 않으면 속성 이름과 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="78593-181">다음 강조 표시된 코드에 나와 있는 것처럼 *Student.cs* 파일에서 `System.ComponentModel.DataAnnotations.Schema`에 대한 `using` 문을 추가하고, 열 이름 특성을 `FirstMidName` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="78593-182">`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경되므로 데이터베이스가 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="78593-183">변경 내용을 저장하고 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-183">Save your changes and build the project.</span></span> <span data-ttu-id="78593-184">그런 다음, 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력하여 다른 마이그레이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="78593-185">**SQL Server 개체 탐색기** 에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="78593-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="78593-187">처음 두 마이그레이션을 적용하기 전에 이름 열은 nvarchar(MAX) 형식이었습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="78593-188">이제는 nvarchar(50)이며 열 이름이 FirstMidName에서 FirstName으로 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="78593-189">다음 섹션의 모든 엔터티 클래스 만들기를 완료하기 전에 컴파일을 시도하는 경우 컴파일러 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="78593-190">Student 엔터티 변경</span><span class="sxs-lookup"><span data-stu-id="78593-190">Changes to Student entity</span></span>

![학생 엔터티](complex-data-model/_static/student-entity.png)

<span data-ttu-id="78593-192">*Models/Student.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="78593-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="78593-193">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="78593-194">필수 특성</span><span class="sxs-lookup"><span data-stu-id="78593-194">The Required attribute</span></span>

<span data-ttu-id="78593-195">`Required` 특성에서 이름 속성은 필수 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="78593-196">`Required` 특성은 값 형식(DateTime, int, double, float 등)과 같은 null을 허용하지 않는 형식에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="78593-197">Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="78593-198">`MinimumLength`가 적용되려면 `Required` 특성이 `MinimumLength`와 함께 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="78593-199">표시 특성</span><span class="sxs-lookup"><span data-stu-id="78593-199">The Display attribute</span></span>

<span data-ttu-id="78593-200">`Display` 특성은 텍스트 상자에 대한 캡션이 각 인스턴스의 속성 이름 대신 “First Name”, “Last Name”, “Full Name” 및 “Enrollment Date”여야 함을 지정합니다(단어를 분할하는 공백 없음).</span><span class="sxs-lookup"><span data-stu-id="78593-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="78593-201">FullName 계산된 속성</span><span class="sxs-lookup"><span data-stu-id="78593-201">The FullName calculated property</span></span>

<span data-ttu-id="78593-202">`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="78593-203">따라서 get 접근자만 있으며 `FullName` 열은 데이터베이스에 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="78593-204">Instructor 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-204">Create Instructor entity</span></span>

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="78593-206">템플릿 코드를 다음 코드로 바꾸어 *Models/Instructor.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="78593-207">학생과 강사 엔터티의 여러 속성이 동일하다는 것을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="78593-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="78593-208">이 시리즈의 뒷부분에 나오는 [상속 구현](inheritance.md) 자습서에서는 이 코드를 리팩터링하여 중복을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="78593-209">다음과 같이 `HireDate` 특성을 쓸 수 있도록 여러 특성을 한 줄에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="78593-210">CourseAssignments 및 OfficeAssignment 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="78593-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="78593-211">`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="78593-212">강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="78593-213">탐색 속성이 여러 엔터티를 포함할 수 있는 경우, 해당 형식은 항목이 추가, 삭제 및 업데이트될 수 있는 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="78593-214">`List<T>` 또는 `HashSet<T>`와 같은 형식 또는 `ICollection<T>`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="78593-215">`ICollection<T>`를 지정하는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="78593-216">이러한 항목은 다대다 관계에 대한 섹션의 아래쪽에 설명된 `CourseAssignment` 엔터티이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="78593-217">Contoso University 비즈니스 규칙에 따르면 강사는 최대 하나의 사무실만 가질 수 있으므로 `OfficeAssignment` 속성은 단일 OfficeAssignment 엔터티를 포함합니다(사무실이 할당되지 않은 경우 null일 수 있음).</span><span class="sxs-lookup"><span data-stu-id="78593-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="78593-218">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="78593-220">다음 코드로 *Models/OfficeAssignment.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="78593-221">Key 특성</span><span class="sxs-lookup"><span data-stu-id="78593-221">The Key attribute</span></span>

<span data-ttu-id="78593-222">강사와 OfficeAssignment 엔터티 간의 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="78593-223">사무실 할당은 할당된 강사에 관하여 존재하며, 따라서 해당 기본 키도 강사 엔터티에 대한 해당 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="78593-224">하지만, 해당 이름이 ID 및 classnameID 명명 규칙을 따르지 않으므로 Entity Framework는 자동으로 InstructorID를 이 엔터티의 기본 키로 인식할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="78593-225">따라서 `Key` 특성은 키로 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="78593-226">엔터티에 자체 기본 키가 있지만 classnameID 또는 ID가 아닌 다른 항목 속성의 이름을 지정하려는 경우 `Key` 특성을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="78593-227">기본적으로 EF는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="78593-228">강사 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="78593-228">The Instructor navigation property</span></span>

<span data-ttu-id="78593-229">강사 엔터티에 null 허용 `OfficeAssignment` 탐색 속성이 있으므로(강사에 사무실이 할당되지 않을 수 있으므로), OfficeAssignment 엔터티에는 null 비허용 `Instructor` 탐색 속성이 있습니다(사무실 할당은 강사 없이는 존재할 수 없으므로, `InstructorID`는 null을 허용하지 않음).</span><span class="sxs-lookup"><span data-stu-id="78593-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="78593-230">강사 엔터티에 관련된 OfficeAssignment 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="78593-231">강사 탐색 속성에 `[Required]` 특성을 배치하여 관련된 강사가 반드시 있도록 지정할 수도 있지만, `InstructorID` 외래 키(이 테이블에 대한 키이기도 함)가 null을 허용하지 않으므로 이를 수행하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="78593-232">Course 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-232">Modify Course entity</span></span>

![강좌 엔터티](complex-data-model/_static/course-entity.png)

<span data-ttu-id="78593-234">*Models/Course.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="78593-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="78593-235">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="78593-236">강좌 엔터티에는 관련된 부서 엔터티를 가리키는 외래 키 속성 `DepartmentID`가 있으며, 여기에는 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="78593-237">Entity Framework는 관련된 엔터티에 대한 탐색 속성이 있는 경우 사용자가 외래 키 속성을 데이터 모델에 추가하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="78593-238">EF에서 자동으로 필요할 때마다 데이터베이스에 외래 키를 생성하고, 이에 대한 [그림자 속성](/ef/core/modeling/shadow-properties)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="78593-239">하지만 데이터 모델에 외래 키가 있으면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="78593-240">예를 들어, 편집할 강좌 엔터티를 페치하는 경우 이를 로드하지 않으면 부서 엔터티가 null이므로, 강좌 엔터티를 업데이트할 때 먼저 부서 엔터티를 페치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="78593-241">외래 키 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 부서 엔터티를 페치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="78593-242">DatabaseGenerated 특성</span><span class="sxs-lookup"><span data-stu-id="78593-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="78593-243">`CourseID` 속성의 `None` 매개 변수가 있는 `DatabaseGenerated` 특성은 기본 키 값이 데이터베이스에서 생성되지 않고 사용자에 의해 제공되도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="78593-244">기본적으로 Entity Framework은 기본 키 값이 데이터베이스에서 생성된다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="78593-245">이는 대부분의 시나리오에서 사용자가 원하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="78593-246">그러나 강좌 엔터티의 경우 한 부서에 대해 1000개 시리즈, 다른 부서에 대한 2000개 시리즈 등과 같은 사용자 지정 강좌 수를 사용하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="78593-247">또한 행이 생성되거나 업데이트된 날짜를 기록하는 데 데이터베이스 열이 사용되는 경우에 `DatabaseGenerated` 특성을 사용하여 기본 값을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="78593-248">자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78593-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="78593-249">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="78593-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="78593-250">강좌 엔터티의 외래 키 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="78593-251">강좌는 한 부서에 할당되므로, 위에서 언급한 이유로 `DepartmentID` 외래 키와 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="78593-252">강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="78593-253">여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다(형식 `CourseAssignment`는 [나중에](#many-to-many-relationships) 설명).</span><span class="sxs-lookup"><span data-stu-id="78593-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="78593-254">Department 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-254">Create Department entity</span></span>

![부서 엔터티](complex-data-model/_static/department-entity.png)

<span data-ttu-id="78593-256">다음 코드로 *Models/Department.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="78593-257">열 특성</span><span class="sxs-lookup"><span data-stu-id="78593-257">The Column attribute</span></span>

<span data-ttu-id="78593-258">앞서 `Column` 특성을 사용하여 열 이름 매핑을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="78593-259">부서 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용되었으므로 열은 데이터베이스의 SQL Server 금액 형식을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="78593-260">Entity Framework이 속성에 대해 사용자가 정의한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택하기 때문에 열 매핑은 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="78593-261">CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="78593-262">하지만 이 경우 열에 통화 금액이 포함되므로 금액 데이터 형식이 더 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="78593-263">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="78593-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="78593-264">외래 키 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="78593-265">부서는 관리자가 있을 수도 있고 없을 수도 있으며, 관리자는 항상 강사입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="78593-266">따라서 `InstructorID` 속성은 강사 엔터티에 외래 키로 포함되며, 속성이 null 허용임을 표시하도록 `int` 형식이 지정된 후 물음표가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="78593-267">탐색 속성이 `Administrator`로 명명되나, 강사 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="78593-268">부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="78593-269">규칙에 따라 Entity Framework는 null 비허용 외래 키 및 다대다 관계에 대한 하위 삭제를 사용하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="78593-270">이는 마이그레이션을 추가하려고 하면 예외가 발생하는 순환 하위 삭제 규칙으로 이어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="78593-271">예를 들어, Department.InstructorID 속성을 nullable로 정의하지 않은 경우 EF는 강사를 삭제할 때 부서를 삭제하도록 계단식 삭제 규칙을 구성합니다. 이는 원하는 결과가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="78593-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="78593-272">비즈니스 규칙에 null 비허용인 `InstructorID` 속성이 필요한 경우, 다음 흐름 API 문을 사용하여 관계에서 하위 삭제를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="78593-273">Enrollment 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-273">Modify Enrollment entity</span></span>

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="78593-275">*Models/Enrollment.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="78593-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="78593-276">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="78593-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="78593-277">외래 키 속성 및 탐색 속성은 다음 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="78593-278">등록 레코드는 단일 강좌에 해당하므로 `CourseID` 외래 키 속성 및 `Course` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="78593-279">등록 레코드는 단일 학생에 해당하므로 `StudentID` 외래 키 속성 및 `Student` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="78593-280">다 대 다 관계</span><span class="sxs-lookup"><span data-stu-id="78593-280">Many-to-Many relationships</span></span>

<span data-ttu-id="78593-281">학생과 강좌 엔터티 간의 다대다 관계가 있으며, 등록 엔터티는 데이터베이스에서 *페이로드가 있는* 다대다 조인 테이블로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="78593-282">“페이로드가 있다”는 것은 등록 테이블에 조인된 테이블에 대한 외래 키 외에 추가 데이터가 포함되어 있다는 것을 의미합니다(이 경우 기본 키 및 Grade 속성).</span><span class="sxs-lookup"><span data-stu-id="78593-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="78593-283">다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="78593-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="78593-284">(이 다이어그램은 EF 6.x에 대한 Entity Framework 파워 도구를 사용하여 생성되었습니다. 다이어그램 만들기는 자습서 내용에 해당하지 않으며 그림으로만 사용됩니다.)</span><span class="sxs-lookup"><span data-stu-id="78593-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

<span data-ttu-id="78593-286">각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(\*)가 있으며, 이는 일 대 다 관계를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="78593-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="78593-287">등록 테이블에 등급 정보가 포함되지 않은 경우 두 개의 외래 키 CourseID 및 StudentID를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="78593-288">그런 경우에는 데이터베이스에서 페이로드 없는 다대다 조인 테이블(또는 순수 조인 테이블)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="78593-289">강사 및 강좌 엔터티에 그러한 다대다 관계가 있으며, 다음 단계는 페이로드 없는 조인 테이블로 작동하는 엔터티 클래스를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="78593-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="78593-290">(EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="78593-291">자세한 내용은 [EF Core GitHub 리포지토리에서 논의](https://github.com/aspnet/EntityFramework/issues/1368)를 참조하세요.)</span><span class="sxs-lookup"><span data-stu-id="78593-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="78593-292">CourseAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="78593-292">The CourseAssignment entity</span></span>

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="78593-294">다음 코드로 *Models/CourseAssignment.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78593-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="78593-295">조인 엔터티 이름</span><span class="sxs-lookup"><span data-stu-id="78593-295">Join entity names</span></span>

<span data-ttu-id="78593-296">조인 테이블은 강사-강좌 간 다대다 관계에 대한 데이터베이스에 필요하며, 엔터티 집합으로 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="78593-297">이름은 조인 엔터티 `EntityName1EntityName2`로 지정하는 것이 일반적입니다. 이 경우 `CourseInstructor`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="78593-298">그러나 관계를 설명하는 이름을 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="78593-299">데이터 모델은 단순하게 시작되며 나중에 자주 페이로드를 가져오는 페이로드 없는 조인을 사용하여 증가됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="78593-300">설명이 포함된 엔터티 이름으로 시작하는 경우 나중에 이름을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="78593-301">이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="78593-302">예를 들어 Books 및 Customers는 Ratings를 통해 연결될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="78593-303">이 관계의 경우 `CourseAssignment`가 `CourseInstructor`보다 더 낫습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="78593-304">복합 키</span><span class="sxs-lookup"><span data-stu-id="78593-304">Composite key</span></span>

<span data-ttu-id="78593-305">외래 키가 null을 허용하지 않고 고유하게 테이블의 각 행을 식별하기 때문에 별도 기본 키가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="78593-306">*InstructorID* 및 *CourseID* 속성은 복합 기본 키로 작동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="78593-307">EF에서 복합 기본 키를 식별하는 유일한 방법은 *흐름 API* 를 사용하는 것입니다(특성을 사용해서는 수행할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="78593-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="78593-308">다음 섹션에 복합 기본 키를 구성하는 방법이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="78593-309">복합 키는 한 강좌에 여러 행 및 한 강사에 여러 행을 가질 수 있는 반면, 동일한 강사 및 강좌에는 여러 행을 가질 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="78593-310">`Enrollment` 조인 엔터티는 고유한 기본 키를 정의하므로 이러한 종류의 중복이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="78593-311">그러한 중복을 방지하려면 외래 키 필드에서 고유한 인덱스를 추가하거나 `CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="78593-312">자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78593-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="78593-313">데이터베이스 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-313">Update the database context</span></span>

<span data-ttu-id="78593-314">다음 강조 표시된 코드를 *Data/SchoolContext.cs* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="78593-315">이 코드는 새 엔터티를 추가하고 CourseAssignment 엔터티의 복합 기본 키를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="78593-316">흐름 API 대안 정보</span><span class="sxs-lookup"><span data-stu-id="78593-316">About a fluent API alternative</span></span>

<span data-ttu-id="78593-317">`DbContext` 클래스에서 `OnModelCreating` 메서드의 코드는 *흐름 API* 를 사용하여 EF 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="78593-318">API는 종종 [EF Core 설명서](/ef/core/modeling/#use-fluent-api-to-configure-a-model)의 이 예제와 같이 일련의 메서드 호출을 단일 명령문으로 연결하여 사용되기 때문에 “흐름”이라고 부릅니다.</span><span class="sxs-lookup"><span data-stu-id="78593-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="78593-319">이 자습서에서는 특성으로 작업할 수 없는 데이터베이스 매핑에 대해서만 흐름 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="78593-320">그러나 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정하는 데 흐름 API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="78593-321">`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="78593-322">앞서 설명한 대로 `MinimumLength`는 스키마를 변경하지 않고, 클라이언트와 서버 쪽 유효성 검사만 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="78593-323">일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="78593-324">원하는 경우 특성과 흐름 API를 섞을 수 있으며, 흐름 API를 사용해야만 수행할 수 있는 몇 가지 사용자 지정이 있습니다. 하지만 일반적으로 이러한 두 가지 방법 중 하나를 선택하여 가능한 한 일관되게 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="78593-325">둘 다 사용하는 경우 충돌이 있을 때마다 흐름 API가 특성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="78593-326">특성 대 흐름 API의 비교에 관한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78593-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="78593-327">관계를 보여 주는 엔터티 다이어그램</span><span class="sxs-lookup"><span data-stu-id="78593-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="78593-328">다음 그림에는 Entity Framework 파워 도구가 완벽한 School 모델을 만드는 다이어그램이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="78593-330">여기에서 일대다 관계 줄 외에도(1 ~ \*), 강사 및 OfficeAssignment 엔터티 간 일대영 또는 일 관계 줄(1 ~ 0..1)과 강사 및 부서 간 영 또는 일대다 관계 줄(0..1 ~ \*)을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="78593-331">테스트 데이터로 데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="78593-331">Seed database with test data</span></span>

<span data-ttu-id="78593-332">만든 새 엔터티에 대한 시드 데이터를 제공하기 위해 *Data/DbInitializer.cs* 파일의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="78593-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="78593-333">첫 번째 자습서에서 보았듯이, 이 코드의 대부분은 단순히 새 엔터티 개체를 만들고 테스트를 위해 필요에 따라 속성에 샘플 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="78593-334">어떻게 다대다 관계가 처리되는지 확인하세요. 코드는 `Enrollments` 및 `CourseAssignment` 조인 엔터티 집합에서 엔터티를 만들어서 관계를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="78593-335">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="78593-335">Add a migration</span></span>

<span data-ttu-id="78593-336">변경 내용을 저장하고 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-336">Save your changes and build the project.</span></span> <span data-ttu-id="78593-337">그런 다음, 프로젝트 폴더에서 명령 창을 열고 `migrations add` 명령을 입력합니다. (update-database 명령은 아직 실행하지 마십시오.)</span><span class="sxs-lookup"><span data-stu-id="78593-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="78593-338">가능한 데이터 손실에 대한 경고가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="78593-339">이 시점에서 `database update` 명령 실행을 시도하면(아직 수행하지 말 것) 다음 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="78593-340">ALTER TABLE 문이 FOREIGN KEY 제약 조건 “FK_dbo.Course_dbo.Department_DepartmentID”와 충돌했습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="78593-341">데이터베이스 “ContosoUniversity”, 테이블 “dbo.Department”, 열 ‘DepartmentID’에서 충돌이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="78593-342">종종 기존 데이터와 마이그레이션을 실행하는 경우 외래 키 제약 조건을 만족하도록 데이터베이스에 스텁 데이터를 삽입해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="78593-343">`Up` 메서드의 생성된 코드는 null 비허용 DepartmentID 외래 키를 강좌 테이블에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="78593-344">코드를 실행할 때 강좌 테이블에 이미 행이 있는 경우 SQL Server에서 null일 수 없는 열에 배치하는 값을 알지 못하므로 `AddColumn` 작업이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="78593-345">이 자습서의 경우 새 데이터베이스에서 마이그레이션을 실행하지만, 프로덕션 애플리케이션에서는 마이그레이션이 기존 데이터를 처리하도록 해야 합니다. 따라서 다음 지침에는 수행 방법의 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="78593-346">기존 데이터로 이 마이그레이션을 수행하려면 새 열에 기본 값을 제공하도록 코드를 변경하고 “Temp”라는 이름의 스텁 부서를 만들어 기본 부서로 작동하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="78593-347">결과적으로, `Up` 메서드를 실행한 후에 기존 강좌 행은 모두 “Temp” 부서에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="78593-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="78593-348">*{timestamp}_ComplexDataModel.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="78593-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="78593-349">DepartmentID 열을 강좌 테이블에 추가하는 코드 줄을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="78593-350">부서 테이블을 만드는 코드 뒤에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="78593-351">프로덕션 애플리케이션에서 코드 또는 스크립트를 작성하여 부서 행을 추가하고 강좌 행을 새 부서 행에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="78593-352">그런 다음, “Temp” 부서 또는 기본 값은 Course.DepartmentID 열에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="78593-353">변경 내용을 저장하고 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="78593-354">연결 문자열 변경</span><span class="sxs-lookup"><span data-stu-id="78593-354">Change the connection string</span></span>

<span data-ttu-id="78593-355">이제 새 엔터티에 대한 시드 데이터를 빈 데이터베이스에 추가하는 새 코드가 `DbInitializer` 클래스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="78593-356">EF가 비어 있는 새 데이터베이스를 만들도록 하려면 *appsettings.json* 의 연결 문자열에서 데이터베이스의 이름을 ContosoUniversity3 또는 사용 중인 컴퓨터에서 사용한 적 없는 다른 이름으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="78593-357">변경 내용을 *appsettings.json* 에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="78593-358">데이터베이스 이름을 변경하는 대신, 데이터베이스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78593-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="78593-359">**SSOX(SQL Server 개체 탐색기)** 또는 `database drop` CLI 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="78593-360">데이터베이스 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-360">Update the database</span></span>

<span data-ttu-id="78593-361">데이터베이스 이름을 변경했거나 데이터베이스를 삭제한 후 명령 창에서 `database update` 명령을 실행하여 마이그레이션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="78593-362">`DbInitializer.Initialize` 메서드로 이어지는 앱을 실행하여 새 데이터베이스를 실행하고 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="78593-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="78593-363">이전에 수행한 SSOX에서 데이터베이스를 열고, 만들었던 테이블을 모두 볼 수 있도록 **Tables** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="78593-364">(아직 이전 시점의 SSOX가 열려 있는 경우 **새로 고침** 단추를 클릭합니다.)</span><span class="sxs-lookup"><span data-stu-id="78593-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX에서의 테이블](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="78593-366">앱을 실행하여 데이터베이스를 시드하는 이니셜라이저 코드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="78593-367">**CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택하여 데이터가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="78593-369">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="78593-369">Get the code</span></span>

[<span data-ttu-id="78593-370">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="78593-371">다음 단계</span><span class="sxs-lookup"><span data-stu-id="78593-371">Next steps</span></span>

<span data-ttu-id="78593-372">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="78593-373">데이터 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="78593-373">Customized the Data model</span></span>
> * <span data-ttu-id="78593-374">Student 엔터티 변경</span><span class="sxs-lookup"><span data-stu-id="78593-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="78593-375">Instructor 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-375">Created Instructor entity</span></span>
> * <span data-ttu-id="78593-376">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="78593-377">Course 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-377">Modified Course entity</span></span>
> * <span data-ttu-id="78593-378">Department 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="78593-378">Created Department entity</span></span>
> * <span data-ttu-id="78593-379">Enrollment 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="78593-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="78593-380">데이터베이스 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-380">Updated the database context</span></span>
> * <span data-ttu-id="78593-381">테스트 데이터로 데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="78593-381">Seeded database with test data</span></span>
> * <span data-ttu-id="78593-382">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="78593-382">Added a migration</span></span>
> * <span data-ttu-id="78593-383">연결 문자열 변경</span><span class="sxs-lookup"><span data-stu-id="78593-383">Changed the connection string</span></span>
> * <span data-ttu-id="78593-384">데이터베이스 업데이트</span><span class="sxs-lookup"><span data-stu-id="78593-384">Updated the database</span></span>

<span data-ttu-id="78593-385">관련 데이터에 액세스하는 방법을 자세히 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="78593-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="78593-386">다음: 관련 데이터 액세스</span><span class="sxs-lookup"><span data-stu-id="78593-386">Next: Access related data</span></span>](read-related-data.md)
