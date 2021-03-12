---
title: ASP.NET Core에서 세계화 및 지역화
author: rick-anderson
description: ASP.NET Core에서 다른 언어와 문화권으로의 콘텐츠 지역화를 위한 서비스 및 미들웨어를 제공하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 8212025bbf778334045b8fbe7a5dae312524b426
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588024"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="b636e-103">ASP.NET Core에서 세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b636e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="b636e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="b636e-105">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="b636e-106">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="b636e-107">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="b636e-108">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="b636e-109">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="b636e-110">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="b636e-111">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="b636e-112">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-112">App localization involves the following:</span></span>

1. <span data-ttu-id="b636e-113">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="b636e-114">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="b636e-115">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="b636e-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b636e-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="b636e-117">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-117">Make the app's content localizable</span></span>

<span data-ttu-id="b636e-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="b636e-119">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="b636e-120">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="b636e-121">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="b636e-122">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="b636e-123">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="b636e-124">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="b636e-125">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="b636e-126">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="b636e-127">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="b636e-128">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="b636e-129">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="b636e-130">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="b636e-131">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="b636e-132">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="b636e-133">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="b636e-134">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-135">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="b636e-136">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="b636e-137">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="b636e-138">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="b636e-139">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="b636e-140">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="b636e-141">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="b636e-141">View localization</span></span>

<span data-ttu-id="b636e-142">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="b636e-143">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="b636e-144">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="b636e-145">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="b636e-146">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="b636e-147">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="b636e-148">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="b636e-149">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="b636e-150">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="b636e-151">Key</span><span class="sxs-lookup"><span data-stu-id="b636e-151">Key</span></span> | <span data-ttu-id="b636e-152">값</span><span class="sxs-lookup"><span data-stu-id="b636e-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="b636e-153">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="b636e-154">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-155">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="b636e-156">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-156">DataAnnotations localization</span></span>

<span data-ttu-id="b636e-157">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="b636e-158">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="b636e-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="b636e-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="b636e-161">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="b636e-162">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b636e-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="b636e-163">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="b636e-164">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="b636e-165">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="b636e-166">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="b636e-167">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="b636e-168">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="b636e-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="b636e-169">ASP.NET Core를 사용하면 두 문화권 값 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> 및 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-169">ASP.NET Core allows you to specify two culture values, <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> and <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>.</span></span> <span data-ttu-id="b636e-170">`SupportedCultures`에 대한 <xref:System.Globalization.CultureInfo> 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-170">The <xref:System.Globalization.CultureInfo> object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="b636e-171">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="b636e-172">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> 및 <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-172">For more information on how the server obtains the culture, see <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b636e-173">`SupportedUICultures`는 <xref:System.Resources.ResourceManager>에서 조회하는 번역된 문자열( `.resx` 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-173">The `SupportedUICultures` determines which translated strings (from `.resx` files) are looked up by the <xref:System.Resources.ResourceManager>.</span></span> <span data-ttu-id="b636e-174">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-174">The `ResourceManager` looks up culture-specific strings that are determined by `CurrentUICulture`.</span></span> <span data-ttu-id="b636e-175">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="b636e-176">프레임워크는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-176">The framework inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="b636e-177">현재 스레드의 문화권이 `en-US`(영어, 미국)로 설정된 경우, `DateTime.Now.ToLongDateString()`에는 `Thursday, February 18, 2016`이 표시되지만 `CurrentCulture`가 `es-ES`(스페인어, 스페인)로 설정된 경우 출력은 `jueves, 18 de febrero de 2016`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-177">If the current thread's culture is set to `en-US` (English, United States), `DateTime.Now.ToLongDateString()` displays `Thursday, February 18, 2016`; but if `CurrentCulture` is set to `es-ES` (Spanish, Spain), the output is `jueves, 18 de febrero de 2016`.</span></span>

## <a name="resource-files"></a><span data-ttu-id="b636e-178">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="b636e-178">Resource files</span></span>

<span data-ttu-id="b636e-179">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="b636e-180">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="b636e-181">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="b636e-182">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="b636e-183">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="b636e-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="b636e-184">**솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

1. <span data-ttu-id="b636e-187">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. <span data-ttu-id="b636e-189">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   <span data-ttu-id="b636e-191">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="b636e-193">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="b636e-193">Resource file naming</span></span>

<span data-ttu-id="b636e-194">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="b636e-195">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="b636e-196">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-197">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="b636e-198">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="b636e-199">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-200">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="b636e-201">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-202">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="b636e-203">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-204">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="b636e-205">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="b636e-205">Resource name</span></span> | <span data-ttu-id="b636e-206">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="b636e-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="b636e-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="b636e-208">점</span><span class="sxs-lookup"><span data-stu-id="b636e-208">Dot</span></span>  |
| <span data-ttu-id="b636e-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="b636e-210">경로</span><span class="sxs-lookup"><span data-stu-id="b636e-210">Path</span></span> |

<span data-ttu-id="b636e-211">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="b636e-212">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="b636e-213">Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="b636e-214">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="b636e-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="b636e-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="b636e-217">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="b636e-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="b636e-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="b636e-219"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-219">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="b636e-220">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="b636e-221">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="b636e-222">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="b636e-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="b636e-223">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-223">Localization does not work by default.</span></span>
* <span data-ttu-id="b636e-224">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="b636e-225">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="b636e-226">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="b636e-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="b636e-227">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="b636e-228">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="b636e-228">Culture fallback behavior</span></span>

<span data-ttu-id="b636e-229">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="b636e-230">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="b636e-231">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="b636e-232">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="b636e-233">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="b636e-234">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="b636e-235">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="b636e-236">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="b636e-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="b636e-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="b636e-239">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="b636e-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="b636e-240">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="b636e-241">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="b636e-242">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="b636e-243">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="b636e-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="b636e-244">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="b636e-245">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="b636e-246">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="b636e-247">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="b636e-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="b636e-248">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="b636e-249">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="b636e-249">Add other cultures</span></span>

<span data-ttu-id="b636e-250">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="b636e-251">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="b636e-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="b636e-252">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="b636e-253">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="b636e-254">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="b636e-254">Configure localization</span></span>

<span data-ttu-id="b636e-255">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="b636e-256">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="b636e-257">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="b636e-258">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="b636e-259">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="b636e-260">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="b636e-261">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="b636e-262">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="b636e-262">Localization middleware</span></span>

<span data-ttu-id="b636e-263">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="b636e-264">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="b636e-265">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="b636e-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b636e-266">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="b636e-267">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="b636e-268">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="b636e-269">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="b636e-270">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="b636e-271">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="b636e-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="b636e-273">일부 앱은 쿼리 문자열을 사용하여 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-273">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="b636e-274">cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="b636e-275">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="b636e-276">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="b636e-277">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="b636e-278">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="b636e-279">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="b636e-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="b636e-281">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="b636e-282">`MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="b636e-283">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="b636e-284">기본 cookie 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="b636e-285">cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="b636e-286">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="b636e-287">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="b636e-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="b636e-288">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="b636e-289">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="b636e-290">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="b636e-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="b636e-291">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="b636e-292">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="b636e-293">기어 아이콘에서 **인터넷 옵션** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="b636e-294">**언어** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-294">Tap **Languages**.</span></span>

   ![인터넷 옵션](localization/_static/lang.png)

1. <span data-ttu-id="b636e-296">**언어 기본 설정 설정** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="b636e-297">**언어 추가** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="b636e-298">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-298">Add the language.</span></span>

1. <span data-ttu-id="b636e-299">언어를 누른 다음, **위로 이동** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="b636e-300">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-300">Use a custom provider</span></span>

<span data-ttu-id="b636e-301">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="b636e-302">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="b636e-303">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="b636e-304">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="b636e-305">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-305">Set the culture programmatically</span></span>

<span data-ttu-id="b636e-306">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="b636e-307">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="b636e-308">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="b636e-309">`SetLanguage` 메서드는 문화권 cookie를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="b636e-310">*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="b636e-311">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="b636e-312">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="b636e-312">Model binding route data and query strings</span></span>

<span data-ttu-id="b636e-313">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="b636e-314">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="b636e-314">Globalization and localization terms</span></span>

<span data-ttu-id="b636e-315">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="b636e-316">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="b636e-317">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="b636e-318">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="b636e-319">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="b636e-320">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="b636e-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="b636e-322"><https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-322">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="b636e-323">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="b636e-324">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="b636e-325">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="b636e-326">용어:</span><span class="sxs-lookup"><span data-stu-id="b636e-326">Terms:</span></span>

* <span data-ttu-id="b636e-327">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="b636e-328">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="b636e-329">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="b636e-330">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="b636e-331">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="b636e-332">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="b636e-332">(for example "en", "es")</span></span>
* <span data-ttu-id="b636e-333">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="b636e-334">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="b636e-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="b636e-335">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="b636e-336">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="b636e-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="b636e-337">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="b636e-338">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b636e-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="b636e-339">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="b636e-340">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="b636e-341">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="b636e-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="b636e-342">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="b636e-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="b636e-343">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="b636e-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b636e-344">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="b636e-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="b636e-345">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="b636e-346">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="b636e-347">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="b636e-348">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="b636e-349">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="b636e-350">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="b636e-351">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="b636e-352">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-352">App localization involves the following:</span></span>

1. <span data-ttu-id="b636e-353">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="b636e-354">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="b636e-355">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="b636e-356">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b636e-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="b636e-357">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-357">Make the app's content localizable</span></span>

<span data-ttu-id="b636e-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="b636e-359">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="b636e-360">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="b636e-361">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="b636e-362">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="b636e-363">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="b636e-364">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="b636e-365">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="b636e-366">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="b636e-367">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="b636e-368">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="b636e-369">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="b636e-370">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="b636e-371">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="b636e-372">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="b636e-373">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="b636e-374">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-375">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="b636e-376">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="b636e-377">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="b636e-378">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="b636e-379">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="b636e-380">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="b636e-381">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="b636e-381">View localization</span></span>

<span data-ttu-id="b636e-382">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="b636e-383">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="b636e-384">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="b636e-385">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="b636e-386">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="b636e-387">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="b636e-388">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="b636e-389">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="b636e-390">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="b636e-391">Key</span><span class="sxs-lookup"><span data-stu-id="b636e-391">Key</span></span> | <span data-ttu-id="b636e-392">값</span><span class="sxs-lookup"><span data-stu-id="b636e-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="b636e-393">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="b636e-394">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-395">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="b636e-396">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-396">DataAnnotations localization</span></span>

<span data-ttu-id="b636e-397">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="b636e-398">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="b636e-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="b636e-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="b636e-401">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="b636e-402">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b636e-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="b636e-403">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="b636e-404">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="b636e-405">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="b636e-406">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="b636e-407">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="b636e-408">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="b636e-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="b636e-409">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="b636e-410">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="b636e-411">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="b636e-412">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="b636e-413">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="b636e-414">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="b636e-415">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="b636e-416">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="b636e-417">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="b636e-418">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="b636e-418">Resource files</span></span>

<span data-ttu-id="b636e-419">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="b636e-420">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="b636e-421">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="b636e-422">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="b636e-423">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="b636e-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="b636e-424">**솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

1. <span data-ttu-id="b636e-427">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. <span data-ttu-id="b636e-429">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   <span data-ttu-id="b636e-431">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="b636e-433">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="b636e-433">Resource file naming</span></span>

<span data-ttu-id="b636e-434">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="b636e-435">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="b636e-436">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-437">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="b636e-438">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="b636e-439">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-440">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="b636e-441">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-442">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="b636e-443">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-444">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="b636e-445">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="b636e-445">Resource name</span></span> | <span data-ttu-id="b636e-446">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="b636e-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="b636e-447">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="b636e-448">점</span><span class="sxs-lookup"><span data-stu-id="b636e-448">Dot</span></span>  |
| <span data-ttu-id="b636e-449">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="b636e-450">경로</span><span class="sxs-lookup"><span data-stu-id="b636e-450">Path</span></span> |

<span data-ttu-id="b636e-451">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="b636e-452">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="b636e-453">Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="b636e-454">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="b636e-455">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="b636e-456">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="b636e-457">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="b636e-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="b636e-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="b636e-459"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-459">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="b636e-460">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="b636e-461">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="b636e-462">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="b636e-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="b636e-463">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-463">Localization does not work by default.</span></span>
* <span data-ttu-id="b636e-464">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="b636e-465">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="b636e-466">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="b636e-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="b636e-467">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="b636e-468">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="b636e-468">Culture fallback behavior</span></span>

<span data-ttu-id="b636e-469">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="b636e-470">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="b636e-471">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="b636e-472">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="b636e-473">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="b636e-474">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="b636e-475">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="b636e-476">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="b636e-477">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="b636e-478">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="b636e-479">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="b636e-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="b636e-480">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="b636e-481">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="b636e-482">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="b636e-483">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="b636e-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="b636e-484">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="b636e-485">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="b636e-486">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="b636e-487">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="b636e-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="b636e-488">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="b636e-489">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="b636e-489">Add other cultures</span></span>

<span data-ttu-id="b636e-490">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="b636e-491">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="b636e-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="b636e-492">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="b636e-493">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="b636e-494">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="b636e-494">Configure localization</span></span>

<span data-ttu-id="b636e-495">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="b636e-496">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="b636e-497">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="b636e-498">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="b636e-499">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="b636e-500">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="b636e-501">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="b636e-502">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="b636e-502">Localization middleware</span></span>

<span data-ttu-id="b636e-503">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="b636e-504">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="b636e-505">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="b636e-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b636e-506">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="b636e-507">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="b636e-508">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="b636e-509">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="b636e-510">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="b636e-511">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="b636e-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="b636e-513">일부 앱은 쿼리 문자열을 사용하여 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-513">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="b636e-514">cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="b636e-515">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="b636e-516">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="b636e-517">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="b636e-518">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="b636e-519">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="b636e-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="b636e-521">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="b636e-522">`MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="b636e-523">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="b636e-524">기본 cookie 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="b636e-525">cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="b636e-526">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="b636e-527">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="b636e-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="b636e-528">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="b636e-529">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="b636e-530">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="b636e-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="b636e-531">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="b636e-532">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="b636e-533">기어 아이콘에서 **인터넷 옵션** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="b636e-534">**언어** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-534">Tap **Languages**.</span></span>

   ![인터넷 옵션](localization/_static/lang.png)

1. <span data-ttu-id="b636e-536">**언어 기본 설정 설정** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="b636e-537">**언어 추가** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="b636e-538">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-538">Add the language.</span></span>

1. <span data-ttu-id="b636e-539">언어를 누른 다음, **위로 이동** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="b636e-540">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-540">Use a custom provider</span></span>

<span data-ttu-id="b636e-541">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="b636e-542">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="b636e-543">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="b636e-544">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="b636e-545">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-545">Set the culture programmatically</span></span>

<span data-ttu-id="b636e-546">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="b636e-547">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="b636e-548">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="b636e-549">`SetLanguage` 메서드는 문화권 cookie를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="b636e-550">*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="b636e-551">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="b636e-552">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="b636e-552">Model binding route data and query strings</span></span>

<span data-ttu-id="b636e-553">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="b636e-554">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="b636e-554">Globalization and localization terms</span></span>

<span data-ttu-id="b636e-555">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="b636e-556">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="b636e-557">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="b636e-558">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="b636e-559">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="b636e-560">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="b636e-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="b636e-562"><https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-562">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="b636e-563">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="b636e-564">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="b636e-565">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="b636e-566">용어:</span><span class="sxs-lookup"><span data-stu-id="b636e-566">Terms:</span></span>

* <span data-ttu-id="b636e-567">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="b636e-568">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="b636e-569">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="b636e-570">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="b636e-571">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="b636e-572">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="b636e-572">(for example "en", "es")</span></span>
* <span data-ttu-id="b636e-573">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="b636e-574">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="b636e-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="b636e-575">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="b636e-576">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="b636e-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="b636e-577">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="b636e-578">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b636e-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="b636e-579">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="b636e-580">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="b636e-581">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="b636e-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="b636e-582">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="b636e-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="b636e-583">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="b636e-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="b636e-584">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="b636e-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="b636e-585">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="b636e-586">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="b636e-587">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="b636e-588">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="b636e-589">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="b636e-590">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="b636e-591">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="b636e-592">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-592">App localization involves the following:</span></span>

1. <span data-ttu-id="b636e-593">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="b636e-594">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="b636e-595">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="b636e-596">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b636e-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="b636e-597">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="b636e-597">Make the app's content localizable</span></span>

<span data-ttu-id="b636e-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="b636e-599">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="b636e-600">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="b636e-601">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="b636e-602">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="b636e-603">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="b636e-604">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="b636e-605">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="b636e-606">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="b636e-607">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="b636e-608">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="b636e-609">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="b636e-610">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="b636e-611">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="b636e-612">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="b636e-613">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="b636e-614">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-615">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="b636e-616">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="b636e-617">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="b636e-618">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="b636e-619">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="b636e-620">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="b636e-621">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="b636e-621">View localization</span></span>

<span data-ttu-id="b636e-622">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="b636e-623">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="b636e-624">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="b636e-625">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="b636e-626">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="b636e-627">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="b636e-628">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="b636e-629">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="b636e-630">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="b636e-631">Key</span><span class="sxs-lookup"><span data-stu-id="b636e-631">Key</span></span> | <span data-ttu-id="b636e-632">값</span><span class="sxs-lookup"><span data-stu-id="b636e-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="b636e-633">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="b636e-634">일반적으로는 HTML이 아닌 텍스트만 지역화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="b636e-635">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="b636e-636">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-636">DataAnnotations localization</span></span>

<span data-ttu-id="b636e-637">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="b636e-638">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="b636e-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="b636e-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="b636e-641">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="b636e-642">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b636e-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="b636e-643">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="b636e-644">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="b636e-645">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="b636e-646">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="b636e-647">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="b636e-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="b636e-648">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="b636e-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="b636e-649">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="b636e-650">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="b636e-651">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="b636e-652">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="b636e-653">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="b636e-654">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="b636e-655">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="b636e-656">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="b636e-657">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="b636e-658">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="b636e-658">Resource files</span></span>

<span data-ttu-id="b636e-659">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="b636e-660">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="b636e-661">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="b636e-662">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="b636e-663">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="b636e-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="b636e-664">**솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

1. <span data-ttu-id="b636e-667">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. <span data-ttu-id="b636e-669">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   <span data-ttu-id="b636e-671">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="b636e-673">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="b636e-673">Resource file naming</span></span>

<span data-ttu-id="b636e-674">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="b636e-675">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="b636e-676">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-677">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="b636e-678">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="b636e-679">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-680">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="b636e-681">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-682">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="b636e-683">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="b636e-684">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="b636e-685">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="b636e-685">Resource name</span></span> | <span data-ttu-id="b636e-686">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="b636e-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="b636e-687">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="b636e-688">점</span><span class="sxs-lookup"><span data-stu-id="b636e-688">Dot</span></span>  |
| <span data-ttu-id="b636e-689">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="b636e-690">경로</span><span class="sxs-lookup"><span data-stu-id="b636e-690">Path</span></span> |

<span data-ttu-id="b636e-691">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="b636e-692">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="b636e-693">Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="b636e-694">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="b636e-695">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="b636e-696">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="b636e-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="b636e-697">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="b636e-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="b636e-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="b636e-699"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-699">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="b636e-700">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="b636e-701">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="b636e-702">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="b636e-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="b636e-703">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-703">Localization does not work by default.</span></span>
* <span data-ttu-id="b636e-704">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="b636e-705">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="b636e-706">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="b636e-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="b636e-707">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="b636e-708">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="b636e-708">Culture fallback behavior</span></span>

<span data-ttu-id="b636e-709">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="b636e-710">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="b636e-711">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="b636e-712">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="b636e-713">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="b636e-714">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="b636e-715">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="b636e-716">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="b636e-717">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="b636e-718">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="b636e-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="b636e-719">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="b636e-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="b636e-720">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="b636e-721">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="b636e-722">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="b636e-723">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="b636e-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="b636e-724">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="b636e-725">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="b636e-726">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="b636e-727">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="b636e-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="b636e-728">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="b636e-729">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="b636e-729">Add other cultures</span></span>

<span data-ttu-id="b636e-730">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="b636e-731">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="b636e-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="b636e-732">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="b636e-733">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="b636e-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="b636e-734">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="b636e-734">Configure localization</span></span>

<span data-ttu-id="b636e-735">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="b636e-736">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="b636e-737">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="b636e-738">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="b636e-739">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="b636e-740">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="b636e-741">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="b636e-742">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="b636e-742">Localization middleware</span></span>

<span data-ttu-id="b636e-743">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="b636e-744">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="b636e-745">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="b636e-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b636e-746">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="b636e-747">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="b636e-748">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="b636e-749">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="b636e-750">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="b636e-751">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="b636e-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="b636e-753">일부 앱은 쿼리 문자열을 사용하여 <xref:System.Globalization.CultureInfo>을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-753">Some apps will use a query string to set the <xref:System.Globalization.CultureInfo>.</span></span> <span data-ttu-id="b636e-754">cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="b636e-755">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="b636e-756">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="b636e-757">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="b636e-758">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="b636e-759">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="b636e-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="b636e-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="b636e-761">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="b636e-762">`MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="b636e-763">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="b636e-764">기본 cookie 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="b636e-765">cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="b636e-766">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="b636e-767">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="b636e-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="b636e-768">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="b636e-769">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="b636e-770">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="b636e-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="b636e-771">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="b636e-772">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="b636e-773">기어 아이콘에서 **인터넷 옵션** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="b636e-774">**언어** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-774">Tap **Languages**.</span></span>

   ![인터넷 옵션](localization/_static/lang.png)

1. <span data-ttu-id="b636e-776">**언어 기본 설정 설정** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="b636e-777">**언어 추가** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="b636e-778">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-778">Add the language.</span></span>

1. <span data-ttu-id="b636e-779">언어를 누른 다음, **위로 이동** 을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="b636e-780">콘텐츠-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="b636e-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="b636e-781">[콘텐츠-언어](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) 엔터티 헤더:</span><span class="sxs-lookup"><span data-stu-id="b636e-781">The [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="b636e-782">대상 그룹을 위한 언어를 설명하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="b636e-783">사용자가 사용자 기본 설정 언어에 따라 구별할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="b636e-784">엔터티 헤더는 HTTP 요청 및 응답에 모두 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="b636e-785">속성 `ApplyCurrentCultureToResponseHeaders`을(를) 설정하여 `Content-Language` 헤더를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="b636e-786">`Content-Language` 헤더 추가:</span><span class="sxs-lookup"><span data-stu-id="b636e-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="b636e-787">RequestLocalizationMiddleware가 `CurrentUICulture`를 사용하여 `Content-Language` 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="b636e-788">응답 헤더 `Content-Language`를 명시적으로 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="b636e-789">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="b636e-789">Use a custom provider</span></span>

<span data-ttu-id="b636e-790">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="b636e-791">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="b636e-792">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="b636e-793">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="b636e-794">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="b636e-794">Set the culture programmatically</span></span>

<span data-ttu-id="b636e-795">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="b636e-796">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="b636e-797">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="b636e-798">`SetLanguage` 메서드는 문화권 cookie를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="b636e-799">*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="b636e-800">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="b636e-801">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="b636e-801">Model binding route data and query strings</span></span>

<span data-ttu-id="b636e-802">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="b636e-803">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="b636e-803">Globalization and localization terms</span></span>

<span data-ttu-id="b636e-804">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="b636e-805">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="b636e-806">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="b636e-807">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="b636e-808">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="b636e-809">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="b636e-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="b636e-811"><https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b636e-811">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="b636e-812">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="b636e-813">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="b636e-814">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="b636e-815">용어:</span><span class="sxs-lookup"><span data-stu-id="b636e-815">Terms:</span></span>

* <span data-ttu-id="b636e-816">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="b636e-817">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="b636e-818">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="b636e-819">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="b636e-820">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="b636e-821">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="b636e-821">(for example "en", "es")</span></span>
* <span data-ttu-id="b636e-822">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="b636e-823">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="b636e-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="b636e-824">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="b636e-825">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="b636e-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="b636e-826">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="b636e-827">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b636e-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="b636e-828">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b636e-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="b636e-829">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="b636e-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="b636e-830">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="b636e-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="b636e-831">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="b636e-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="b636e-832">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="b636e-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
