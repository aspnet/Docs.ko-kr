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
ms.openlocfilehash: 67f245b7f4e4aa97b30c5318c73732617aea44c7
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217572"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>ASP.NET Core에서 세계화 및 지역화

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다. ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.

국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다. 세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다. 세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.

지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다. 자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.

앱 지역화 과정은 다음과 같습니다.

1. 앱의 콘텐츠를 지역화 가능하도록 만들기
1. 지원하는 언어 및 문화권에 대한 지역화된 리소스 제공
1. 각 요청에 대한 언어/문화권을 선택하는 전략 구현

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>앱의 콘텐츠를 지역화 가능하도록 만들기

<xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다. `IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다. 인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다. `IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다. 지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다. 아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다. "About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다. 앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다. 기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다. 또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다. 대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다. 다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.

HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다. `IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다. 아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.

컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다. 샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다. 아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>지역화 보기

`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다. `ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다. 다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다. 전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다. `ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다. 리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다. 다음 Razor 태그를 살펴보세요.

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

프랑스어 리소스 파일은 다음을 포함할 수 있습니다.

| Key | 값 |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations 지역화

DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다. `ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다. ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>다중 클래스에 대해 하나의 리소스 문자열 사용

다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.

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

위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다. 이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>지원하는 언어 및 문화권에 대한 지역화된 리소스 제공

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures 및 SupportedUICultures

ASP.NET Core를 사용하면 두 문화권 값 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> 및 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>를 지정할 수 있습니다. `SupportedCultures`에 대한 <xref:System.Globalization.CultureInfo> 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다. `SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다. 서버가 문화권을 가져오는 방법에 대한 자세한 내용은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> 및 <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType>를 참조하세요. `SupportedUICultures`는 <xref:System.Resources.ResourceManager>에서 조회하는 번역된 문자열( `.resx` 파일에서)을 결정합니다. `ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 조회합니다. .NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다. 프레임워크는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다. 현재 스레드의 문화권이 `en-US`(영어, 미국)로 설정된 경우, `DateTime.Now.ToLongDateString()`에는 `Thursday, February 18, 2016`이 표시되지만 `CurrentCulture`가 `es-ES`(스페인어, 스페인)로 설정된 경우 출력은 `jueves, 18 de febrero de 2016`입니다.

## <a name="resource-files"></a>리소스 파일

리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다. 기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다. 예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다. "es"는 스페인어 언어 코드입니다. Visual Studio에서 이 리소스 파일을 만들려면:

1. **솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다. 두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.](localization/_static/newi.png)

1. **설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. **이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a>리소스 파일 이름 지정

리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다. 예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다. `LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다. 예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.

샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다. 또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다. 홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다. `ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다. `HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.

| 리소스 이름 | 점 또는 경로 명명 |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | 점  |
| Resources/Controllers/HomeController.fr.resx  | 경로 |

Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다. 보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다. Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다. `ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다. 

> [!WARNING]
> 이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다. 예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다. 

어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:

* 지역화는 기본적으로 작동하지 않습니다.
* 지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다. `RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다. 

`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.

## <a name="culture-fallback-behavior"></a>문화권 대체 동작

리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다. 요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다. 그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다. 이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다. 예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다. 이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.

사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다. 지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)

예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다. 리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다. 요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio를 사용하여 리소스 파일 생성

파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다. 일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다. 일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다. 문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*). 문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.

### <a name="add-other-cultures"></a>다른 문화권 추가

각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다. ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**). 이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>각 요청에 대한 언어/문화권을 선택하는 전략 구현

### <a name="configure-localization"></a>지역화 구성

지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다. 위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.

* `AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다. 이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다. 예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.

* `AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.

### <a name="localization-middleware"></a>지역화 미들웨어

요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다. 지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다. 지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다. 모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다. 기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다. 문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다. 공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

일부 앱은 쿼리 문자열을 사용하여 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>을 설정합니다. cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다. 기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다. `culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다. 다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다. 예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다. `MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.

`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다. 기본 cookie 이름은 `.AspNetCore.Culture`입니다.

cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.

```
c=en-UK|uic=en-US
```

문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.

### <a name="the-accept-language-http-header"></a>수용-언어 HTTP 헤더

[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다. 이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다. 브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조). 프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE에서 수용-언어 HTTP 헤더 설정

1. 기어 아이콘에서 **인터넷 옵션** 을 누릅니다.

1. **언어** 를 누릅니다.

   ![인터넷 옵션](localization/_static/lang.png)

1. **언어 기본 설정 설정** 을 누릅니다.

1. **언어 추가** 를 누릅니다.

1. 언어를 추가합니다.

1. 언어를 누른 다음, **위로 이동** 을 누릅니다.

### <a name="use-a-custom-provider"></a>사용자 지정 공급자 사용

소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다. 공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다. 다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.

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

`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.

### <a name="set-the-culture-programmatically"></a>프로그래밍 방식으로 문화권 설정

[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다. *Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` 메서드는 문화권 cookie를 설정합니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다. [GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.

## <a name="model-binding-route-data-and-query-strings"></a>모델 바인딩 경로 데이터 및 쿼리 문자열

[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.

## <a name="globalization-and-localization-terms"></a>세계화 및 지역화 용어

또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다. 모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다. 지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.

[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.

문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다. 예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.

국제화는 종종 "I18N"으로 단축됩니다. 약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다. 세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.

용어:

* 세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.
* 지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.
* 국제화(I18N): 세계화와 지역화를 모두 설명합니다.
* 문화권: 언어이며 경우에 따라 지역입니다.
* 중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다. (예: "en", "es")
* 특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다. (예: "en-US", "en-GB", "es-CL")
* 부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다. (예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)
* 로캘: 로캘은 문화권과 동일합니다.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.
* [.NET 애플리케이션 전역화 및 지역화](/dotnet/standard/globalization-localization/index)
* [.resx 파일의 리소스](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft 다국어 앱 도구 키트](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [지역화 및 제네릭](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다. ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.

국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다. 세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다. 세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.

지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다. 자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.

앱 지역화 과정은 다음과 같습니다.

1. 앱의 콘텐츠를 지역화 가능하도록 만들기
1. 지원하는 언어 및 문화권에 대한 지역화된 리소스 제공
1. 각 요청에 대한 언어/문화권을 선택하는 전략 구현

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>앱의 콘텐츠를 지역화 가능하도록 만들기

<xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다. `IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다. 인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다. `IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다. 지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다. 아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다. "About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다. 앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다. 기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다. 또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다. 대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다. 다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.

HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다. `IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다. 아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.

컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다. 샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다. 아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>지역화 보기

`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다. `ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다. 다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다. 전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다. `ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다. 리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다. 다음 Razor 태그를 살펴보세요.

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

프랑스어 리소스 파일은 다음을 포함할 수 있습니다.

| Key | 값 |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations 지역화

DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다. `ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다. ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>다중 클래스에 대해 하나의 리소스 문자열 사용

다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.

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

위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다. 이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>지원하는 언어 및 문화권에 대한 지역화된 리소스 제공

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures 및 SupportedUICultures

ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다. `SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다. `SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다. 서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요. `SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다. `ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다. .NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다. ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다. 예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.

## <a name="resource-files"></a>리소스 파일

리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다. 기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다. 예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다. "es"는 스페인어 언어 코드입니다. Visual Studio에서 이 리소스 파일을 만들려면:

1. **솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다. 두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.](localization/_static/newi.png)

1. **설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. **이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a>리소스 파일 이름 지정

리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다. 예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다. `LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다. 예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.

샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다. 또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다. 홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다. `ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다. `HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.

| 리소스 이름 | 점 또는 경로 명명 |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | 점  |
| Resources/Controllers/HomeController.fr.resx  | 경로 |

Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다. 보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다. Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다. `ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다. 

> [!WARNING]
> 이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다. 예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다. 

어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:

* 지역화는 기본적으로 작동하지 않습니다.
* 지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다. `RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다. 

`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.

## <a name="culture-fallback-behavior"></a>문화권 대체 동작

리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다. 요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다. 그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다. 이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다. 예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다. 이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.

사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다. 지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)

예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다. 리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다. 요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio를 사용하여 리소스 파일 생성

파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다. 일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다. 일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다. 문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*). 문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.

### <a name="add-other-cultures"></a>다른 문화권 추가

각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다. ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**). 이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>각 요청에 대한 언어/문화권을 선택하는 전략 구현

### <a name="configure-localization"></a>지역화 구성

지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다. 위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.

* `AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다. 이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다. 예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.

* `AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.

### <a name="localization-middleware"></a>지역화 미들웨어

요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다. 지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다. 지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다. 모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다. 기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다. 문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다. 공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

일부 앱은 쿼리 문자열을 사용하여 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>을 설정합니다. cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다. 기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다. `culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다. 다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다. 예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다. `MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.

`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다. 기본 cookie 이름은 `.AspNetCore.Culture`입니다.

cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.

```
c=en-UK|uic=en-US
```

문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.

### <a name="the-accept-language-http-header"></a>수용-언어 HTTP 헤더

[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다. 이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다. 브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조). 프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE에서 수용-언어 HTTP 헤더 설정

1. 기어 아이콘에서 **인터넷 옵션** 을 누릅니다.

1. **언어** 를 누릅니다.

   ![인터넷 옵션](localization/_static/lang.png)

1. **언어 기본 설정 설정** 을 누릅니다.

1. **언어 추가** 를 누릅니다.

1. 언어를 추가합니다.

1. 언어를 누른 다음, **위로 이동** 을 누릅니다.

### <a name="use-a-custom-provider"></a>사용자 지정 공급자 사용

소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다. 공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다. 다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.

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

`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.

### <a name="set-the-culture-programmatically"></a>프로그래밍 방식으로 문화권 설정

[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다. *Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` 메서드는 문화권 cookie를 설정합니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다. [GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.

## <a name="model-binding-route-data-and-query-strings"></a>모델 바인딩 경로 데이터 및 쿼리 문자열

[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.

## <a name="globalization-and-localization-terms"></a>세계화 및 지역화 용어

또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다. 모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다. 지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.

[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.

문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다. 예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.

국제화는 종종 "I18N"으로 단축됩니다. 약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다. 세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.

용어:

* 세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.
* 지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.
* 국제화(I18N): 세계화와 지역화를 모두 설명합니다.
* 문화권: 언어이며 경우에 따라 지역입니다.
* 중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다. (예: "en", "es")
* 특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다. (예: "en-US", "en-GB", "es-CL")
* 부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다. (예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)
* 로캘: 로캘은 문화권과 동일합니다.

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.
* [.NET 애플리케이션 전역화 및 지역화](/dotnet/standard/globalization-localization/index)
* [.resx 파일의 리소스](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft 다국어 앱 도구 키트](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [지역화 및 제네릭](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다. ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.

국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다. 세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다. 세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.

지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다. 자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어** 를 참조하세요.

앱 지역화 과정은 다음과 같습니다.

1. 앱의 콘텐츠를 지역화 가능하도록 만들기
1. 지원하는 언어 및 문화권에 대한 지역화된 리소스 제공
1. 각 요청에 대한 언어/문화권을 선택하는 전략 구현

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>앱의 콘텐츠를 지역화 가능하도록 만들기

<xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다. `IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다. 인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다. `IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다. 지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다. 아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다. "About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다. 앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다. 기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다. 또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다. 대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다. 다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.

HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다. `IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다. 아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.

컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다. 샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다. 아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>지역화 보기

`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다. `ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다. 다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다. 전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다. `ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다. 리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다. 다음 Razor 태그를 살펴보세요.

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

프랑스어 리소스 파일은 다음을 포함할 수 있습니다.

| Key | 값 |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.

> [!NOTE]
> 일반적으로는 HTML이 아닌 텍스트만 지역화합니다.

보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations 지역화

DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다. `ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다. ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>다중 클래스에 대해 하나의 리소스 문자열 사용

다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.

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

위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다. 이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>지원하는 언어 및 문화권에 대한 지역화된 리소스 제공

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures 및 SupportedUICultures

ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다. `SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다. `SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다. 서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요. `SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다. `ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다. .NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다. ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다. 예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.

## <a name="resource-files"></a>리소스 파일

리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다. 기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다. 예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx* 라는 스페인어 리소스 파일을 만들 수 있습니다. "es"는 스페인어 언어 코드입니다. Visual Studio에서 이 리소스 파일을 만들려면:

1. **솔루션 탐색기** 에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 클릭합니다.

   ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다. 두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.](localization/_static/newi.png)

1. **설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.

   ![새 항목 추가 대화 상자](localization/_static/res.png)

1. **이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.

   ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

   Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.

   ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a>리소스 파일 이름 지정

리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다. 예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx* 로 이름이 지정됩니다. `LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다. 예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx* 로 이름이 지정됩니다.

샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx* 입니다. 또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다. 홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx* 입니다. `ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다. `HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx* 로 이름이 지정됩니다. 점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.

| 리소스 이름 | 점 또는 경로 명명 |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | 점  |
| Resources/Controllers/HomeController.fr.resx  | 경로 |

Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다. 보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다. Razor 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다. `ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다. 

> [!WARNING]
> 이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다. 예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다. 

어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:

* 지역화는 기본적으로 작동하지 않습니다.
* 지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다. `RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다. 

`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs* 에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.

## <a name="culture-fallback-behavior"></a>문화권 대체 동작

리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다. 요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다. 그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다. 이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다. 예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다. 이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.

사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다. 지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)

예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다. 리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다. 요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio를 사용하여 리소스 파일 생성

파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다. 일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다. 일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다. 문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*). 문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.

### <a name="add-other-cultures"></a>다른 문화권 추가

각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다. ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**). 이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>각 요청에 대한 언어/문화권을 선택하는 전략 구현

### <a name="configure-localization"></a>지역화 구성

지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다. 위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.

* `AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다. 이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다. 예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.

* `AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.

### <a name="localization-middleware"></a>지역화 미들웨어

요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다. 지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다. 지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다. 모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다. 기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다. 문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다. 공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

일부 앱은 쿼리 문자열을 사용하여 <xref:System.Globalization.CultureInfo>을 설정합니다. cookie 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다. 기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다. `culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다. 다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다. 예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 cookie로 문화권을 설정합니다. `MakeCookieValue` 메서드를 사용하여 cookie를 만듭니다.

`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 cookie 이름을 반환합니다. 기본 cookie 이름은 `.AspNetCore.Culture`입니다.

cookie 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며, 여기서 `c`는 `Culture`이고 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.

```
c=en-UK|uic=en-US
```

문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.

### <a name="the-accept-language-http-header"></a>수용-언어 HTTP 헤더

[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다. 이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다. 브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조). 프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE에서 수용-언어 HTTP 헤더 설정

1. 기어 아이콘에서 **인터넷 옵션** 을 누릅니다.

1. **언어** 를 누릅니다.

   ![인터넷 옵션](localization/_static/lang.png)

1. **언어 기본 설정 설정** 을 누릅니다.

1. **언어 추가** 를 누릅니다.

1. 언어를 추가합니다.

1. 언어를 누른 다음, **위로 이동** 을 누릅니다.

### <a name="the-content-language-http-header"></a>콘텐츠-언어 HTTP 헤더

[콘텐츠-언어](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) 엔터티 헤더:

* 대상 그룹을 위한 언어를 설명하는 데 사용됩니다.
* 사용자가 사용자 기본 설정 언어에 따라 구별할 수 있습니다.

엔터티 헤더는 HTTP 요청 및 응답에 모두 사용됩니다.

속성 `ApplyCurrentCultureToResponseHeaders`을(를) 설정하여 `Content-Language` 헤더를 추가할 수 있습니다.

`Content-Language` 헤더 추가:

* RequestLocalizationMiddleware가 `CurrentUICulture`를 사용하여 `Content-Language` 헤더를 설정할 수 있습니다.
* 응답 헤더 `Content-Language`를 명시적으로 설정하지 않아도 됩니다.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>사용자 지정 공급자 사용

소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다. 공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다. 다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.

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

`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.

### <a name="set-the-culture-programmatically"></a>프로그래밍 방식으로 문화권 설정

[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다. *Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` 메서드는 문화권 cookie를 설정합니다.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

*_SelectLanguagePartial.cshtml* 을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다. [GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.

## <a name="model-binding-route-data-and-query-strings"></a>모델 바인딩 경로 데이터 및 쿼리 문자열

[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.

## <a name="globalization-and-localization-terms"></a>세계화 및 지역화 용어

또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다. 모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다. 지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.

[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.

문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다. 예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>을 참조하세요.

국제화는 종종 "I18N"으로 단축됩니다. 약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다. 세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.

용어:

* 세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.
* 지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.
* 국제화(I18N): 세계화와 지역화를 모두 설명합니다.
* 문화권: 언어이며 경우에 따라 지역입니다.
* 중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다. (예: "en", "es")
* 특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다. (예: "en-US", "en-GB", "es-CL")
* 부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다. (예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)
* 로캘: 로캘은 문화권과 동일합니다.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.
* [.NET 애플리케이션 전역화 및 지역화](/dotnet/standard/globalization-localization/index)
* [.resx 파일의 리소스](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft 다국어 앱 도구 키트](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [지역화 및 제네릭](http://hishambinateya.com/localization-and-generics)

::: moniker-end
