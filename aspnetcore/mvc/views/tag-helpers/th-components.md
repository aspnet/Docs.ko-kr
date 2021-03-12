---
title: ASP.NET Core의 태그 도우미 구성 요소
author: scottaddie
description: 태그 도우미 구성 요소란 무엇이며 ASP.NET Core에서 어떻게 사용하는지 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fb0bda0cf8d225df4c58ae43f81ed0dce10c1adc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587153"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="800d1-103">ASP.NET Core의 태그 도우미 구성 요소</span><span class="sxs-lookup"><span data-stu-id="800d1-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="800d1-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie), [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="800d1-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="800d1-105">태그 도우미 구성 요소는 서버 쪽 코드에서 HTML 요소를 조건부로 수정하거나 추가할 수 있도록 하는 태그 도우미입니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="800d1-106">이 기능은 ASP.NET Core 2.0 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="800d1-107">ASP.NET Core에는 두 개의 기본 제공 태그 도우미 구성 요소, 즉 `head` 및 `body`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="800d1-108"><xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers>네임 스페이스는 네임 스페이스에 있으며 MVC와 페이지에서 모두 사용할 수 있습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="800d1-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="800d1-109">태그 도우미 구성 요소는 *_ViewImports.cshtml* 에서 앱을 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="800d1-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="800d1-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="800d1-111">사용 사례</span><span class="sxs-lookup"><span data-stu-id="800d1-111">Use cases</span></span>

<span data-ttu-id="800d1-112">태그 도우미 구성 요소의 두 가지 일반적인 사용 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="800d1-113">에를 삽입 `<link>` `<head>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="800d1-114">에를 삽입 `<script>` `<body>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="800d1-115">다음 섹션에서는 이 사용 사례를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="800d1-116">HTML 헤드 요소에 주입</span><span class="sxs-lookup"><span data-stu-id="800d1-116">Inject into HTML head element</span></span>

<span data-ttu-id="800d1-117">HTML `<head>` 요소 내에서 CSS 파일은 일반적으로 HTML `<link>` 요소로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="800d1-118">다음 코드는 `head` 태그 도우미 구성 요소를 사용하여 `<head>` 요소에 `<link>` 요소를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="800d1-119">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="800d1-119">In the preceding code:</span></span>

* <span data-ttu-id="800d1-120">`AddressStyleTagHelperComponent`는 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="800d1-121">추상은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-121">The abstraction:</span></span>
  * <span data-ttu-id="800d1-122"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>를 사용한 클래스 초기화를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="800d1-123">태그 도우미 구성 요소를 사용하여 HTML 요소를 추가하거나 수정할 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="800d1-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> 속성은 구성 요소가 렌더링되는 순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="800d1-125">앱에서 태그 도우미 구성 요소가 여러 번 사용되는 경우 `Order`가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="800d1-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>는 실행 컨텍스트의 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> 속성 값을 `head`와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="800d1-127">비교가 true로 평가되면 `_style` 필드의 내용이 HTML `<head>` 요소에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="800d1-128">HTML 본문 요소에 주입</span><span class="sxs-lookup"><span data-stu-id="800d1-128">Inject into HTML body element</span></span>

<span data-ttu-id="800d1-129">`body` 태그 도우미 구성 요소는 `<body>` 요소에 `<script>` 요소를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="800d1-130">다음 코드에서는 이 기술을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="800d1-131">별도의 HTML 파일이 `<script>` 요소를 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="800d1-132">HTML 파일은 코드를 더 깔끔하고 유지 관리가 가능하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="800d1-133">위의 코드는 *TagHelpers/Templates/AddressToolTipScript.html* 의 내용을 읽고 태그 도우미 출력으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="800d1-134">*AddressToolTipScript.html* 파일에는 다음 태그가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="800d1-135">위의 코드는 [부트스트랩 도구 설명 위젯](https://getbootstrap.com/docs/3.3/javascript/#tooltips)을 `printable` 특성을 포함하는 `<address>` 요소에 바인드합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="800d1-136">요소 위에 마우스 포인터를 놓으면 효과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="800d1-137">구성 요소 등록</span><span class="sxs-lookup"><span data-stu-id="800d1-137">Register a Component</span></span>

<span data-ttu-id="800d1-138">태그 도우미 구성 요소를 앱의 태그 도우미 구성 요소 컬렉션에 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="800d1-139">컬렉션에 추가하는 세 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="800d1-140">서비스 컨테이너를 통한 등록</span><span class="sxs-lookup"><span data-stu-id="800d1-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="800d1-141">File via 등록 Razor</span><span class="sxs-lookup"><span data-stu-id="800d1-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="800d1-142">페이지 모델 또는 컨트롤러를 통한 등록</span><span class="sxs-lookup"><span data-stu-id="800d1-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="800d1-143">서비스 컨테이너를 통한 등록</span><span class="sxs-lookup"><span data-stu-id="800d1-143">Registration via services container</span></span>

<span data-ttu-id="800d1-144">태그 도우미 구성 요소 클래스가 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>로 관리되지 않는 경우 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 시스템에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="800d1-145">다음 `Startup.ConfigureServices` 코드는 [임시 수명](xref:fundamentals/dependency-injection#lifetime-and-registration-options)이 있는 `AddressStyleTagHelperComponent` 및 `AddressScriptTagHelperComponent` 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="800d1-146">File via 등록 Razor</span><span class="sxs-lookup"><span data-stu-id="800d1-146">Registration via Razor file</span></span>

<span data-ttu-id="800d1-147">태그 도우미 구성 요소가 DI를 사용 하 여 등록 되지 않은 경우 Razor 페이지 페이지나 MVC 뷰에서 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="800d1-148">이 기법은 파일에서 삽입 된 태그와 구성 요소 실행 순서를 제어 하는 데 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="800d1-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="800d1-149">`ITagHelperComponentManager`는 태그 도우미 구성 요소를 추가하거나 앱에서 제거하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="800d1-150">다음 코드에서는 `AddressTagHelperComponent`를 사용한 이 기술을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="800d1-151">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="800d1-151">In the preceding code:</span></span>

* <span data-ttu-id="800d1-152">`@inject` 지시문은 `ITagHelperComponentManager`의 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="800d1-153">인스턴스는 `manager` 파일의 access 다운스트림에 대해 라는 변수에 할당 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="800d1-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="800d1-154">`AddressTagHelperComponent` 인스턴스가 앱의 태그 도우미 구성 요소 컬렉션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="800d1-155">`AddressTagHelperComponent`는 `markup` 및 `order` 매개 변수를 허용하는 생성자를 수용하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="800d1-156">제공된 `markup` 매개 변수는 다음과 같이 `ProcessAsync`에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="800d1-157">페이지 모델 또는 컨트롤러를 통한 등록</span><span class="sxs-lookup"><span data-stu-id="800d1-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="800d1-158">태그 도우미 구성 요소가 DI를 사용 하 여 등록 되지 않은 경우 Razor 페이지 페이지 모델 또는 MVC 컨트롤러에서 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="800d1-159">이 기법은 c # 논리를 파일에서 분리 하는 데 유용 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="800d1-160">생성자 주입은 `ITagHelperComponentManager`의 인스턴스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="800d1-161">태그 도우미 구성 요소를 인스턴스의 태그 도우미 구성 요소 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="800d1-162">다음 페이지 Razor 페이지 모델에서는이 기법을 보여 줍니다 `AddressTagHelperComponent` .</span><span class="sxs-lookup"><span data-stu-id="800d1-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="800d1-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="800d1-163">In the preceding code:</span></span>

* <span data-ttu-id="800d1-164">생성자 주입은 `ITagHelperComponentManager`의 인스턴스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="800d1-165">`AddressTagHelperComponent` 인스턴스가 앱의 태그 도우미 구성 요소 컬렉션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="800d1-166">구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="800d1-166">Create a Component</span></span>

<span data-ttu-id="800d1-167">사용자 지정 태그 도우미 구성 요소를 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="800d1-168"><xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>에서 파생된 공용 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="800d1-169">[`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute)클래스에 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="800d1-170">대상 HTML 요소의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="800d1-171">*선택 사항*: [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) 클래스에 특성을 적용 하 여 IntelliSense에서 형식의 표시를 표시 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="800d1-172">다음 코드는 `<address>` HTML 요소를 대상으로 하는 사용자 지정 태그 도우미 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="800d1-173">다음과 같이 사용자 지정 `address` 태그 도우미 구성 요소를 사용하여 HTML 태그를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="800d1-174">위의 `ProcessAsync` 메서드는 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*>에 제공된 HTML을 일치하는 `<address>` 요소에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="800d1-175">주입은 다음과 같은 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-175">The injection occurs when:</span></span>

* <span data-ttu-id="800d1-176">실행 컨텍스트의 `TagName` 속성 값이 `address`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="800d1-177">해당 `<address>` 요소에 `printable` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="800d1-178">예를 들어 `if` 문은 다음 `<address>` 요소를 처리할 때 true로 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="800d1-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="800d1-179">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="800d1-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
