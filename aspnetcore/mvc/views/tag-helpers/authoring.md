---
title: ASP.NET Core의 작성자 태그 도우미
author: rick-anderson
description: ASP.NET Core에서 태그 도우미를 작성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586828"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="a2661-103">ASP.NET Core의 작성자 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a2661-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="a2661-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a2661-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a2661-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2661-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="a2661-106">태그 도우미 시작</span><span class="sxs-lookup"><span data-stu-id="a2661-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="a2661-107">이 자습서는 태그 도우미 프로그래밍에 대한 소개를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="a2661-108">[태그 도우미 소개](intro.md)에서는 태그 도우미가 제공하는 이점에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="a2661-109">태그 도우미는 `ITagHelper` 인터페이스를 구현하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="a2661-110">그러나 태그 도우미를 작성할 때 일반적으로 `TagHelper`에서 파생되므로 `Process` 메서드에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="a2661-111">**AuthoringTagHelpers** 라는 새로운 ASP.NET Core 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="a2661-112">이 프로젝트에 대한 인증이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="a2661-113">*TagHelpers* 라는 태그 도우미를 보관할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="a2661-114">*TagHelpers* 폴더는 필수는 *아니지만* 있는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="a2661-115">이제 몇 가지 간단한 태그 도우미를 작성하는 것으로 시작해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="a2661-116">최소한의 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a2661-116">A minimal Tag Helper</span></span>

<span data-ttu-id="a2661-117">이 섹션에서는 이메일 태그를 업데이트하는 태그 도우미를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="a2661-118">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="a2661-119">서버는 이메일 태그 도우미를 사용하여 태그를 다음으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="a2661-120">즉, 이메일 링크로 만드는 앵커 태그.</span><span class="sxs-lookup"><span data-stu-id="a2661-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="a2661-121">블로그 엔진을 작성하고 마케팅, 지원 및 기타 연락처에 대한 이메일을 모두 동일한 도메인으로 보내는 데 필요한 경우 이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="a2661-122">다음 `EmailTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="a2661-123">태그 도우미는 루트 클래스 이름의 요소를 대상으로 하는 명명 규칙을 사용합니다(클래스 이름의 *TagHelper* 부분 제외).</span><span class="sxs-lookup"><span data-stu-id="a2661-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="a2661-124">이 예에서 **EmailTagHelper** 의 루트 이름이 *email* 이므로 `<email>` 태그가 대상으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="a2661-125">이 명명 규칙은 대부분의 태그 도우미에서 작동하며, 나중에는 재정의하는 방법에 대해 설명하기로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="a2661-126">`EmailTagHelper` 클래스는 `TagHelper`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="a2661-127">`TagHelper` 클래스는 태그 도우미를 작성하기 위한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="a2661-128">재정의된 `Process` 메서드는 실행될 때 태그 도우미가 수행하는 작업을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="a2661-129">`TagHelper` 클래스는 동일한 매개 변수와 함께 비동기 버전(`ProcessAsync`)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="a2661-130">`Process`(및 `ProcessAsync`)에 대한 컨텍스트 매개 변수는 현재 HTML 태그의 실행과 관련된 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="a2661-131">`Process`(및 `ProcessAsync`)에 대한 출력 매개 변수는 HTML 태그 및 콘텐츠를 생성하는 데 사용된 원본 소스의 상태 저장 HTML 요소 표현을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="a2661-132">클래스 이름에는 **TagHelper** 라는 접미사가 있으며 필수는 *아니지만* 관례적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="a2661-133">다음과 같이 클래스를 선언할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="a2661-134">`EmailTagHelper`모든 보기에서 클래스를 사용할 수 있도록 하려면 Razor `addTagHelper` 다음 지시문을 *views/_ViewImports에 추가 합니다.*</span><span class="sxs-lookup"><span data-stu-id="a2661-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="a2661-135">위의 코드에서는 와일드 카드 구문을 사용하여 사용할 수 있는 모든 태그 도우미를 어셈블리에서 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="a2661-136">`@addTagHelper` 뒤의 첫 번째 문자열은 로드할 태그 도우미(모든 태그 도우미에 "\*" 사용)를 지정하고 두 번째 문자열 "AuthoringTagHelpers"는 태그 도우미가 있는 어셈블리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="a2661-137">또한 두 번째 줄은 와일드 카드 구문을 사용 하 여 ASP.NET Core MVC 태그 도우미에 표시 됩니다. 해당 도우미는 [태그 도우미 소개](intro.md)에 설명 되어 있습니다. 이 지시문은 `@addTagHelper` 태그 도우미를 뷰에서 사용할 수 있도록 하는 지시문입니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="a2661-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="a2661-138">또는 아래 표시된 것처럼 태그 도우미의 FQN(정규화된 이름)을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="a2661-139">FQN을 사용하여 뷰에 태그 도우미를 추가하려면 먼저 FQN(`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)을 추가한 후 **어셈블리 이름**(*AuthoringTagHelpers*, 반드시 `namespace`는 아님)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="a2661-140">대부분의 개발자는 와일드 카드 구문을 사용하는 방법을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="a2661-141">[태그 도우미 소개](intro.md)에서는 태그 도우미 추가, 제거, 계층 구조 및 와일드 카드 구문에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="a2661-142">*Views/Home/Contact.cshtml* 파일에서 태그를 다음 변경 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="a2661-143">앱을 실행하고 선호하는 브라우저를 사용하여 HTML 소스를 보면 이메일 태그가 앵커 태그로 대체되었는지 확인할 수 있습니다(예: `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="a2661-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="a2661-144">*지원* 및 *마케팅* 은 링크로 렌더링되지만 작동하도록 하는 `href` 특성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="a2661-145">다음 섹션에서 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="a2661-146">SetAttribute 및 SetContent</span><span class="sxs-lookup"><span data-stu-id="a2661-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="a2661-147">이 섹션에서는 `EmailTagHelper`를 업데이트하여 이메일에 대한 유효한 앵커 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="a2661-148">이를 업데이트 하 여 Razor 보기 (특성 형식)에서 정보를 가져와 `mail-to` 앵커 생성에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="a2661-149">`EmailTagHelper` 클래스를 다음으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="a2661-150">태그 도우미에 대한 파스칼식 클래스 및 속성 이름이 [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="a2661-151">따라서 `MailTo` 특성을 사용하려면 `<email mail-to="value"/>`를 사용하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="a2661-152">마지막 줄은 최소로 작동하는 태그 도우미에 대한 완성된 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="a2661-153">강조 표시된 줄은 특성 추가를 위한 구문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="a2661-154">이 방법은 특성 컬렉션에 현재 존재하지 않는 한, 속성 "href" 특성에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="a2661-155">또한 `output.Attributes.Add` 메서드를 사용하여 태그 특성 컬렉션 끝에 태그 도우미 특성을 추가할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="a2661-156">*Views/Home/Contact.cshtml* 파일에서 태그를 다음 변경 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="a2661-157">앱을 실행하고 올바른 링크를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="a2661-158">이메일 태그 자체 닫음(`<email mail-to="Rick" />`)을 작성하려는 경우 최종 출력도 자체로 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="a2661-159">시작 태그(`<email mail-to="Rick">`)만 사용하여 태그를 작성하는 기능을 사용하려면 다음을 사용하여 클래스를 표시해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="a2661-160">자체 닫음 이메일 태그 도우미를 사용하면 출력은 `<a href="mailto:Rick@contoso.com" />`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="a2661-161">자체 닫음 앵커 태그가 유효한 HTML이 아니므로 태그를 만들지는 않겠지만 자체 닫음 태그 도우미를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="a2661-162">태그 도우미는 태그를 읽은 후 `TagMode` 속성의 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="a2661-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="a2661-163">ProcessAsync</span></span>

<span data-ttu-id="a2661-164">이 섹션에서는 비동기 이메일 도우미를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="a2661-165">`EmailTagHelper` 클래스를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="a2661-166">**참고:**</span><span class="sxs-lookup"><span data-stu-id="a2661-166">**Notes:**</span></span>

   * <span data-ttu-id="a2661-167">이 버전은 비동기 `ProcessAsync` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="a2661-168">비동기 `GetChildContentAsync`는 `TagHelperContent`가 포함된 `Task`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="a2661-169">`output` 매개 변수를 사용하여 HTML 요소의 내용을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="a2661-170">태그 도우미가 대상 이메일을 가져올 수 있도록 *Views/Home/Contact.cshtml* 파일을 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="a2661-171">앱을 실행하고 유효한 이메일 링크를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="a2661-172">RemoveAll, PreContent.SetHtmlContent 및 PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="a2661-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="a2661-173">다음 `BoldTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="a2661-174">`[HtmlTargetElement]` 특성은 "bold"라는 HTML 특성이 포함된 HTML 요소가 일치하고 클래스에서 `Process` 재정의 메서드가 실행되도록 지정하는 특성 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="a2661-175">이 샘플에서 `Process` 메서드는 "bold" 특성을 제거하고 포함된 태그를 `<strong></strong>`으로 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="a2661-176">기존 태그 내용을 바꾸지 않기 때문에 `PreContent.SetHtmlContent` 메서드로 여는 `<strong>` 태그를 작성하고 `PostContent.SetHtmlContent` 메서드로 닫는 `</strong>` 태그를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="a2661-177">`bold` 특성 값을 포함하도록 *About.cshtml* 뷰를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="a2661-178">완성된 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="a2661-179">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-179">Run the app.</span></span> <span data-ttu-id="a2661-180">선호하는 브라우저를 사용하여 소스를 검사하고 태그를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="a2661-181">위의 `[HtmlTargetElement]` 특성은 "bold" 특성 이름을 제공하는 HTML 태그만 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="a2661-182">`<bold>` 요소는 태그 도우미에 의해 수정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="a2661-183">`[HtmlTargetElement]` 특성 줄을 주석 처리하고 기본적으로 `<bold>` 태그를 대상으로 합니다. 즉, `<bold>` 형식의 HTML 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="a2661-184">기본 명명 규칙은 클래스 이름 **Bold** TagHelper를 `<bold>` 태그와 일치시킨다는 점을 유의합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="a2661-185">앱을 실행하고 `<bold>` 태그가 태그 도우미에 의해 처리되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="a2661-186">클래스를 여러 개의 `[HtmlTargetElement]` 특성으로 데코레이팅하면 그 결과는 대상의 논리 OR입니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="a2661-187">예를 들어 아래 코드를 사용하여 bold 태그 또는 bold 특성을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="a2661-188">동일한 문에 특성이 여러 개 추가된 경우, 런타임에서 논리 AND로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="a2661-189">예를 들어 아래 코드에서는 일치시킬 "bold"(`<bold bold />`) 특성을 사용하여 HTML 요소 이름을 "bold"로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="a2661-190">또는 `[HtmlTargetElement]`를 사용하여 대상 요소의 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="a2661-191">예를 들어 `BoldTagHelper`가 `<MyBold>` 태그를 대상으로 지정하도록 하려면 다음 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="a2661-192">모델을 태그 도우미에 전달</span><span class="sxs-lookup"><span data-stu-id="a2661-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="a2661-193">*모델* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="a2661-194">다음 `WebsiteContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="a2661-195">다음 `WebsiteInformationTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="a2661-196">앞에서 언급했듯이, 태그 도우미는 파스칼식 C# 클래스 이름과 태그 도우미 속성을 [kebab case](https://wiki.c2.com/?KebabCase)로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="a2661-197">따라서에서를 사용 하려면 `WebsiteInformationTagHelper` 를 Razor 작성 `<website-information />` 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="a2661-198">`[HtmlTargetElement]` 특성을 사용하여 대상 요소를 명시적으로 식별하지 않으므로 `website-information`의 기본값이 대상으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="a2661-199">다음 특성을 적용한 경우(kebab 대/소문자는 아니지만 클래스 이름 일치):</span><span class="sxs-lookup"><span data-stu-id="a2661-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="a2661-200">kebab case 태그 `<website-information />`은 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="a2661-201">`[HtmlTargetElement]` 특성을 사용하려는 경우 아래와 같이 kebab 대/소문자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="a2661-202">자체 닫음 요소에는 내용이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="a2661-203">이 예제에서 태그는 Razor 자체 닫는 태그를 사용 하지만, 태그 도우미는 [section](https://www.w3.org/TR/html5/sections.html#the-section-element) 요소를 만듭니다 .이 요소는 자체 닫히지 않으며 요소 내에 콘텐츠를 작성 하는 것입니다 `section` .</span><span class="sxs-lookup"><span data-stu-id="a2661-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="a2661-204">따라서 `TagMode`를 `StartTagAndEndTag`로 설정하여 출력을 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="a2661-205">또는 `TagMode` 설정 줄을 주석 처리하고 닫는 태그로 태그를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="a2661-206">(예제 태그는 이 자습서의 뒷부분에 제공됩니다.)</span><span class="sxs-lookup"><span data-stu-id="a2661-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="a2661-207">다음 줄에서 `$`(달러 기호)는 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="a2661-208">다음 태그를 *About.cshtml* 뷰에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="a2661-209">강조 표시된 태그는 웹 사이트 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="a2661-210">Razor아래 표시 된 태그에서:</span><span class="sxs-lookup"><span data-stu-id="a2661-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="a2661-211">Razor 특성은 `info` 문자열이 아니라 클래스 이며 c # 코드를 작성 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="a2661-212">모든 문자열이 아닌 태그 도우미 특성이 `@` 문자 없이 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="a2661-213">앱을 실행하고 About 뷰로 이동하여 웹 사이트 정보를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a2661-214">닫는 태그로 다음 태그를 사용하고 태그 도우미에서 `TagMode.StartTagAndEndTag`로 해당 줄을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="a2661-215">조건 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a2661-215">Condition Tag Helper</span></span>

<span data-ttu-id="a2661-216">조건 태그 도우미는 true 값을 전달한 경우 출력을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="a2661-217">다음 `ConditionTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="a2661-218">*Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="a2661-219">`Home` 컨트롤러에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="a2661-220">앱을 실행하고 홈 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="a2661-221">조건부 `div`의 태그가 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="a2661-222">쿼리 문자열 `?approved=true`를 URL에 추가합니다(예: `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="a2661-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="a2661-223">`approved`가 true로 설정되고 조건부 태그가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="a2661-224">[nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하여 bold 태그 도우미에서와 마찬가지로 문자열을 지정하지 않고 대상에 특성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="a2661-225">[nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자는 리팩토링해야 하는 코드를 보호합니다(이름을 `RedCondition`으로 변경할 수 있음).</span><span class="sxs-lookup"><span data-stu-id="a2661-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="a2661-226">태그 도우미 충돌 방지</span><span class="sxs-lookup"><span data-stu-id="a2661-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="a2661-227">이 섹션에서는 자동 링크 태그 도우미 쌍을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="a2661-228">첫 번째는 HTTP로 시작하는 URL을 포함하는 태그를 동일한 URL을 포함하는 HTML 앵커 태그로 대체합니다(따라서 URL에 대한 링크를 생성함).</span><span class="sxs-lookup"><span data-stu-id="a2661-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="a2661-229">두 번째는 WWW로 시작하는 URL에 대해 동일한 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="a2661-230">이러한 두 도우미는 밀접하게 관련되어 있으며 향후 리팩터링할 수 있으므로 동일한 파일에 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="a2661-231">다음 `AutoLinkerHttpTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="a2661-232">`AutoLinkerHttpTagHelper` 클래스는 `p` 요소를 대상으로 하고 [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 사용하여 앵커를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="a2661-233">*Views/Home/Contact.cshtml* 파일 끝에 다음 마크업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="a2661-234">앱을 실행하고 태그 도우미가 앵커를 제대로 렌더링하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="a2661-235">www 텍스트를 원본 www 텍스트를 포함하는 앵커 태그로 변환할 `AutoLinkerWwwTagHelper`를 포함하도록 `AutoLinker` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="a2661-236">업데이트된 코드는 아래에 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="a2661-237">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-237">Run the app.</span></span> <span data-ttu-id="a2661-238">www 텍스트는 링크로 렌더링되지만 HTTP 텍스트는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="a2661-239">두 클래스 모두에 중단점을 넣으면 HTTP 태그 도우미 클래스가 먼저 실행되는 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="a2661-240">문제는 태그 도우미 출력이 캐시된 후 WWW 태그 도우미가 실행될 때, HTTP 태그 도우미의 캐시된 출력을 덮어쓴다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="a2661-241">자습서의 뒷부분에서 태그 도우미가 실행되는 순서를 제어하는 방법을 살펴 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="a2661-242">코드를 다음과 같이 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="a2661-243">자동 링크 태그 도우미의 첫 번째 버전에서는 다음 코드를 사용하여 대상의 내용을 얻었습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="a2661-244">즉, `ProcessAsync` 메서드에 전달된 `TagHelperOutput`을 사용하여 `GetChildContentAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="a2661-245">이전에 언급한 것처럼, 출력이 캐시되므로 실행할 마지막 태그 도우미가 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="a2661-246">다음 코드로 문제를 해결했습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="a2661-247">위의 코드는 내용이 수정되었는지 확인하고, 수정되었으면 출력 버퍼에서 내용을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="a2661-248">앱을 실행하고 두 링크가 예상대로 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="a2661-249">자동 링커 태그 도우미가 정확하고 완전한 것으로 보일 수 있지만 미묘한 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="a2661-250">WWW 태그 도우미가 먼저 실행되면 www 링크는 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="a2661-251">태그가 실행되는 순서를 제어하기 위해 `Order` 오버로드를 추가하여 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="a2661-252">`Order` 속성은 동일한 요소를 대상으로 하는 다른 태그 도우미를 기준으로 실행 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="a2661-253">기본 순서 값은 0이고 값이 낮은 인스턴스가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="a2661-254">위의 코드는 HTTP 태그 도우미가 WWW 태그 도우미보다 먼저 실행되도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="a2661-255">`Order`를 `MaxValue`로 변경하고 WWW 태그에 대해 보장된 태그가 잘못되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="a2661-256">자식 콘텐츠 검사 및 검색</span><span class="sxs-lookup"><span data-stu-id="a2661-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="a2661-257">태그 도우미는 콘텐츠를 검색하기 위한 여러 가지 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="a2661-258">`GetChildContentAsync`의 결과는 `output.Content`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="a2661-259">`GetContent`로 `GetChildContentAsync`의 결과를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="a2661-260">`output.Content`를 수정하고 `GetChildContentAsync`를 자동 링커 샘플에서처럼 호출하지 않는 경우, TagHelper 본문이 실행 또는 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="a2661-261">`GetChildContentAsync`를 여러 번 호출해도 같은 값이 반환되며 캐시된 결과를 사용하지 않음을 나타내는 false 매개 변수를 전달하지 않은 경우 `TagHelper` 본문을 다시 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="a2661-262">축소된 부분 보기 TagHelper 로드</span><span class="sxs-lookup"><span data-stu-id="a2661-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="a2661-263">프로덕션 환경에서는 축소된 부분 보기를 로드하여 성능을 향상할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="a2661-264">프로덕션에서 축소된 부분 보기를 활용하려면 다음을 수행하세요.</span><span class="sxs-lookup"><span data-stu-id="a2661-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="a2661-265">부분 보기를 축소하는 사전 빌드 프로세스를 만들거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="a2661-266">다음 코드를 사용하여 개발 환경이 아닌 환경에서 축소된 부분 보기를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a2661-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
