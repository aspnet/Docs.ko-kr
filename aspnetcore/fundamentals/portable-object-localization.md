---
title: ASP.NET Core에서 이식 가능 개체 지역화 구성
author: sebastienros
description: 이 문서는 이식 가능 개체 파일을 소개하고 ASP.NET Core 애플리케이션에서 Orchard Core 프레임워크와 사용하는 단계를 간략하게 설명합니다.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: f9c0ecd32f0e5c05782c2db76eff92bb64845db7
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878401"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>ASP.NET Core에서 이식 가능 개체 지역화 구성

::: moniker range=">= aspnetcore-3.0"

작성자: [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) 및 [Hisham Bin Ateya](https://github.com/hishamco)

이 문서는 ASP.NET Core 애플리케이션에서 PO(이식 가능 개체) 파일을 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 프레임워크와 함께 사용하는 단계를 안내합니다.

**참고:** Orchard Core는 Microsoft 제품이 아닙니다. 따라서 Microsoft는 이 기능에 대한 지원을 제공하지 않습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>PO 파일이란?

PO 파일은 지정된 언어에 대한 번역된 문자열을 포함하는 텍스트 파일로 배포됩니다. *.resx* 파일 대신 PO 파일을 사용하면 다음과 같은 몇 가지 이점이 있습니다.
- PO 파일은 복수화를 지원합니다. *.resx* 파일은 복수화를 지원하지 않습니다.
- PO 파일은 *.resx* 파일처럼 컴파일되지 않습니다. 이와 같이 특수화된 도구 및 빌드 단계는 필요하지 않습니다.
- PO 파일은 공동 온라인 편집 도구와 함께 잘 작동합니다.

### <a name="example"></a>예제

복수 형태로 하나를 포함하여 프랑스어로 두 개의 문자열에 대한 번역을 포함하는 샘플 PO 파일은 다음과 같습니다.

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

이 예제에서는 다음 구문을 사용합니다.

- `#:`: 변환되는 문자열의 컨텍스트를 나타내는 주석입니다. 동일한 문자열은 사용되는 위치에 따라 다르게 변환될 수 있습니다.
- `msgid`: 번역되지 않은 문자열입니다.
- `msgstr`: 번역된 문자열입니다.

복수화 지원의 경우 더 많은 항목을 정의할 수 있습니다.

- `msgid_plural`: 번역되지 않은 복수형 문자열입니다.
- `msgstr[0]`: 사례 0의 번역된 문자열입니다.
- `msgstr[N]`: 사례 N의 번역된 문자열입니다.

PO 파일 사양은 [여기](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)에서 찾을 수 있습니다.

## <a name="configuring-po-file-support-in-aspnet-core"></a>ASP.NET Core에서 PO 파일 구성 지원

이 예제는 Visual Studio 2017 프로젝트 템플릿에서 생성된 ASP.NET Core MVC 애플리케이션을 기반으로 합니다.

### <a name="referencing-the-package"></a>패키지 참조

`OrchardCore.Localization.Core` NuGet 패키지에 대한 참조를 추가합니다. 패키지 소스 https://www.myget.org/F/orchardcore-preview/api/v3/index.json 의 [MyGet](https://www.myget.org/)에서 사용할 수 있습니다.

이제 *.csproj* 파일은 다음과 비슷한 줄을 포함합니다(버전 번호가 달라질 수 있음).

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a>서비스 등록

*Startup.cs* 의 `ConfigureServices` 메서드에 필요한 서비스를 추가합니다.

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

*Startup.cs* 의 `Configure` 메서드에 필요한 미들웨어를 추가합니다.

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

선택한 Razor 뷰에 다음 코드를 추가합니다. 이 예제에서는 *About.cshtml* 이 사용됩니다.

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer` 인스턴스가 삽입되고 "Hello world!" 텍스트를 번역하는 데 사용됩니다.

### <a name="creating-a-po-file"></a>PO 파일 만들기

애플리케이션 루트 폴더에 *\<culture code>.po* 라는 파일을 만듭니다. 이 예제에서는 프랑스어가 사용되므로 파일 이름은 *fr.po* 입니다.

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

이 파일은 번역할 문자열 및 프랑스어로 번역된 문자열 모두를 저장합니다. 필요한 경우 번역은 해당 부모 문화권으로 되돌립니다. 이 예제에서 요청된 문화권이 `fr-FR` 또는 `fr-CA`인 경우 *fr.po* 파일이 사용됩니다.

### <a name="testing-the-application"></a>애플리케이션 테스트

애플리케이션을 실행하고 `/Home/About` URL로 이동합니다. 텍스트 **Hello world!** 가 표시됩니다.

`/Home/About?culture=fr-FR` URL로 이동합니다. 텍스트 **Bonjour le monde!** 가 표시됩니다.

## <a name="pluralization"></a>복수화

PO 파일은 동일한 문자열이 카디널리티에 따라 다르게 번역되어야 하는 경우 유용한 복수화 양식을 지원합니다. 이 작업은 각 언어가 카디널리티에 따라 사용할 문자열을 선택하도록 사용자 지정 규칙을 정의한다는 점에서 복잡하게 이루어집니다.

Orchard 지역화 패키지는 이러한 다른 복수 양식을 자동으로 호출하는 API를 제공합니다.

### <a name="creating-pluralization-po-files"></a>복수화 PO 파일 만들기

앞에서 언급한 *fr.po* 파일에 다음 콘텐츠를 추가합니다.

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

이 예제의 각 항목이 나타내는 것의 설명은 [PO 파일이란?](#what-is-a-po-file)을 참조하세요.

### <a name="adding-a-language-using-different-pluralization-forms"></a>다른 복수화 양식을 사용하여 언어 추가

이전 예제에서는 영어와 프랑스어 문자열이 사용되었습니다. 영어와 프랑스어에는 두 개의 복수화 양식만이 있으며 하나의 카디널리티가 첫 번째 복수 양식에 매핑되는 동일한 양식 규칙을 공유합니다. 다른 카디널리티는 두 번째 복수 양식에 매핑됩니다.

모든 언어는 동일한 규칙을 공유하지 않습니다. 세 가지 복수 양식을 가진 체코어로 나와 있습니다.

다음과 같이 `cs.po` 파일을 만들고 복수화에서 세 가지 다른 번역을 요구하는 방법을 확인합니다.

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

체코어 지역화를 허용하려면 `ConfigureServices` 메서드의 지원되는 문화권 목록에 `"cs"`를 추가합니다.

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

*Views/Home/About.cshtml* 파일을 편집하여 여러 카디널리티에 대한 지역화된 복수 문자열을 렌더링합니다.

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**참고:** 실제 시나리오에서 변수는 수를 나타내는 데 사용될 수 있습니다. 여기에서 세 가지 다른 값으로 동일한 코드를 반복하여 매우 특정한 사례를 공개합니다.

문화권이 전환되면 다음이 표시됩니다.

`/Home/About`의 경우:

```html
There is one item.
There are 2 items.
There are 5 items.
```

`/Home/About?culture=fr`의 경우:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

`/Home/About?culture=cs`의 경우:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

체코어 문화권의 경우 세 가지 번역은 다릅니다. 프랑스어 및 영어 문화권은 마지막 두 개의 번역된 문자열에 대해 동일한 구조를 공유합니다.

## <a name="advanced-tasks"></a>고급 작업

### <a name="contextualizing-strings"></a>문자열 맥락화

애플리케이션은 종종 여러 위치에서 번역되는 문자열을 포함합니다. 동일한 문자열은 앱 내의 특정 위치에서 다른 번역을 가질 수 있습니다(Razor 뷰 또는 클래스 파일). PO 파일은 표시되는 문자열을 분류하는 데 사용될 수 있는 파일 컨텍스트의 개념을 지원합니다. 파일 컨텍스트를 사용하여 문자열은 파일 컨텍스트(또는 파일 컨텍스트 부족)에 따라 다르게 번역될 수 있습니다.

PO 지역화 서비스는 전체 클래스 또는 문자열을 변역할 때 사용되는 뷰의 이름을 사용합니다. `msgctxt` 항목의 값을 설정하여 수행됩니다.

이전 *fr.po* 예제에 대한 최소 추가를 고려합니다. *Views/Home/About.cshtml* 에 있는 Razor 뷰는 예약된 `msgctxt` 항목의 값을 설정하여 파일 컨텍스트로 정의될 수 있습니다.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

`msgctxt`를 이와 같이 설정하면 텍스트 번역은 `/Home/About?culture=fr-FR`로 이동할 때 발생합니다. 번역은 `/Home/Contact?culture=fr-FR`로 이동할 때 발생하지 않습니다.

특정 항목이 지정된 파일 컨텍스트와 일치하지 않는 경우 Orchard Core의 대체 메커니즘은 컨텍스트 없이 적절한 PO 파일을 찾습니다. *Views/Home/Contact.cshtml* 에 대해 정의된 특정 파일 컨텍스트가 없다고 가정하는 경우 `/Home/Contact?culture=fr-FR`로 이동하면 다음과 같은 PO 파일이 로드됩니다.

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>PO 파일의 위치 변경

PO 파일의 기본 위치는 `ConfigureServices`에서 변경될 수 있습니다.

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

이 예제에서 PO 파일은 *지역화* 폴더에서 로드됩니다.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>지역화 파일을 찾기 위한 사용자 지정 논리 구현

PO 파일을 배치하는 데 더 복잡한 논리가 필요한 경우 서비스로 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 인터페이스를 구현하고 등록할 수 있습니다. PO 파일을 다양한 위치에 저장할 수 있는 경우 또는 폴더의 계층 구조 내에서 파일을 찾아야 하는 경우에 유용합니다.

### <a name="using-a-different-default-pluralized-language"></a>복수화된 다른 기본 언어 사용

패키지는 두 개의 복수 양식과 관련된 `Plural` 확장 메서드를 포함합니다. 더 많은 복수 양식을 요구하는 언어의 경우 확장 메서드를 만듭니다. 확장 메서드를 사용하면 기본 언어 &mdash;에 대해 지역화 파일을 제공할 필요가 없습니다. 원본 문자열을 코드에서 직접 사용할 수 있습니다.

번역의 문자열 배열을 허용하는 보다 일반적인 `Plural(int count, string[] pluralForms, params object[] arguments)` 오버로드를 사용할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Sébastien Ros](https://github.com/sebastienros) 및 [Scott Addie](https://twitter.com/Scott_Addie)

이 문서는 ASP.NET Core 애플리케이션에서 PO(이식 가능 개체) 파일을 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 프레임워크와 함께 사용하는 단계를 안내합니다.

**참고:** Orchard Core는 Microsoft 제품이 아닙니다. 따라서 Microsoft는 이 기능에 대한 지원을 제공하지 않습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>PO 파일이란?

PO 파일은 지정된 언어에 대한 번역된 문자열을 포함하는 텍스트 파일로 배포됩니다. *.resx* 파일 대신 PO 파일을 사용하는 몇 가지 이점은 다음을 포함합니다.
- PO 파일은 복수화를 지원합니다. *.resx* 파일은 복수화를 지원하지 않습니다.
- PO 파일은 *.resx* 파일처럼 컴파일되지 않습니다. 이와 같이 특수화된 도구 및 빌드 단계는 필요하지 않습니다.
- PO 파일은 공동 온라인 편집 도구와 함께 잘 작동합니다.

### <a name="example"></a>예제

복수 형태로 하나를 포함하여 프랑스어로 두 개의 문자열에 대한 번역을 포함하는 샘플 PO 파일은 다음과 같습니다.

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

이 예제에서는 다음 구문을 사용합니다.

- `#:`: 변환되는 문자열의 컨텍스트를 나타내는 주석입니다. 동일한 문자열은 사용되는 위치에 따라 다르게 변환될 수 있습니다.
- `msgid`: 번역되지 않은 문자열입니다.
- `msgstr`: 번역된 문자열입니다.

복수화 지원의 경우 더 많은 항목을 정의할 수 있습니다.

- `msgid_plural`: 번역되지 않은 복수형 문자열입니다.
- `msgstr[0]`: 사례 0의 번역된 문자열입니다.
- `msgstr[N]`: 사례 N의 번역된 문자열입니다.

PO 파일 사양은 [여기](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)에서 찾을 수 있습니다.

## <a name="configuring-po-file-support-in-aspnet-core"></a>ASP.NET Core에서 PO 파일 구성 지원

이 예제는 Visual Studio 2017 프로젝트 템플릿에서 생성된 ASP.NET Core MVC 애플리케이션을 기반으로 합니다.

### <a name="referencing-the-package"></a>패키지 참조

`OrchardCore.Localization.Core` NuGet 패키지에 대한 참조를 추가합니다. 패키지 소스 https://www.myget.org/F/orchardcore-preview/api/v3/index.json 의 [MyGet](https://www.myget.org/)에서 사용할 수 있습니다.

이제 *.csproj* 파일은 다음과 비슷한 줄을 포함합니다(버전 번호가 달라질 수 있음).

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>서비스 등록

*Startup.cs* 의 `ConfigureServices` 메서드에 필요한 서비스를 추가합니다.

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

*Startup.cs* 의 `Configure` 메서드에 필요한 미들웨어를 추가합니다.

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

선택한 Razor 뷰에 다음 코드를 추가합니다. 이 예제에서는 *About.cshtml* 이 사용됩니다.

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer` 인스턴스가 삽입되고 "Hello world!" 텍스트를 번역하는 데 사용됩니다.

### <a name="creating-a-po-file"></a>PO 파일 만들기

애플리케이션 루트 폴더에 *\<culture code>.po* 라는 파일을 만듭니다. 이 예제에서는 프랑스어가 사용되므로 파일 이름은 *fr.po* 입니다.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

이 파일은 번역할 문자열 및 프랑스어로 번역된 문자열 모두를 저장합니다. 필요한 경우 번역은 해당 부모 문화권으로 되돌립니다. 이 예제에서 요청된 문화권이 `fr-FR` 또는 `fr-CA`인 경우 *fr.po* 파일이 사용됩니다.

### <a name="testing-the-application"></a>애플리케이션 테스트

애플리케이션을 실행하고 `/Home/About` URL로 이동합니다. 텍스트 **Hello world!** 가 표시됩니다.

`/Home/About?culture=fr-FR` URL로 이동합니다. 텍스트 **Bonjour le monde!** 가 표시됩니다.

## <a name="pluralization"></a>복수화

PO 파일은 동일한 문자열이 카디널리티에 따라 다르게 번역되어야 하는 경우 유용한 복수화 양식을 지원합니다. 이 작업은 각 언어가 카디널리티에 따라 사용할 문자열을 선택하도록 사용자 지정 규칙을 정의한다는 점에서 복잡하게 이루어집니다.

Orchard 지역화 패키지는 이러한 다른 복수 양식을 자동으로 호출하는 API를 제공합니다.

### <a name="creating-pluralization-po-files"></a>복수화 PO 파일 만들기

앞에서 언급한 *fr.po* 파일에 다음 콘텐츠를 추가합니다.

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

이 예제의 각 항목이 나타내는 것의 설명은 [PO 파일이란?](#what-is-a-po-file)을 참조하세요.

### <a name="adding-a-language-using-different-pluralization-forms"></a>다른 복수화 양식을 사용하여 언어 추가

이전 예제에서는 영어와 프랑스어 문자열이 사용되었습니다. 영어와 프랑스어에는 두 개의 복수화 양식만이 있으며 하나의 카디널리티가 첫 번째 복수 양식에 매핑되는 동일한 양식 규칙을 공유합니다. 다른 카디널리티는 두 번째 복수 양식에 매핑됩니다.

모든 언어는 동일한 규칙을 공유하지 않습니다. 세 가지 복수 양식을 가진 체코어로 나와 있습니다.

다음과 같이 `cs.po` 파일을 만들고 복수화에서 세 가지 다른 번역을 요구하는 방법을 확인합니다.

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

체코어 지역화를 허용하려면 `ConfigureServices` 메서드의 지원되는 문화권 목록에 `"cs"`를 추가합니다.

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

*Views/Home/About.cshtml* 파일을 편집하여 여러 카디널리티에 대한 지역화된 복수 문자열을 렌더링합니다.

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**참고:** 실제 시나리오에서 변수는 수를 나타내는 데 사용될 수 있습니다. 여기에서 세 가지 다른 값으로 동일한 코드를 반복하여 매우 특정한 사례를 공개합니다.

문화권이 전환되면 다음이 표시됩니다.

`/Home/About`의 경우:

```html
There is one item.
There are 2 items.
There are 5 items.
```

`/Home/About?culture=fr`의 경우:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

`/Home/About?culture=cs`의 경우:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

체코어 문화권의 경우 세 가지 번역은 다릅니다. 프랑스어 및 영어 문화권은 마지막 두 개의 번역된 문자열에 대해 동일한 구조를 공유합니다.

## <a name="advanced-tasks"></a>고급 작업

### <a name="contextualizing-strings"></a>문자열 맥락화

애플리케이션은 종종 여러 위치에서 번역되는 문자열을 포함합니다. 동일한 문자열은 앱 내의 특정 위치에서 다른 번역을 가질 수 있습니다(Razor 뷰 또는 클래스 파일). PO 파일은 표시되는 문자열을 분류하는 데 사용될 수 있는 파일 컨텍스트의 개념을 지원합니다. 파일 컨텍스트를 사용하여 문자열은 파일 컨텍스트(또는 파일 컨텍스트 부족)에 따라 다르게 번역될 수 있습니다.

PO 지역화 서비스는 전체 클래스 또는 문자열을 변역할 때 사용되는 뷰의 이름을 사용합니다. `msgctxt` 항목의 값을 설정하여 수행됩니다.

이전 *fr.po* 예제에 대한 최소 추가를 고려합니다. *Views/Home/About.cshtml* 에 있는 Razor 뷰는 예약된 `msgctxt` 항목의 값을 설정하여 파일 컨텍스트로 정의될 수 있습니다.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

`msgctxt`를 이와 같이 설정하면 텍스트 번역은 `/Home/About?culture=fr-FR`로 이동할 때 발생합니다. 번역은 `/Home/Contact?culture=fr-FR`로 이동할 때 발생하지 않습니다.

특정 항목이 지정된 파일 컨텍스트와 일치하지 않는 경우 Orchard Core의 대체 메커니즘은 컨텍스트 없이 적절한 PO 파일을 찾습니다. *Views/Home/Contact.cshtml* 에 대해 정의된 특정 파일 컨텍스트가 없다고 가정하는 경우 `/Home/Contact?culture=fr-FR`로 이동하면 다음과 같은 PO 파일이 로드됩니다.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>PO 파일의 위치 변경

PO 파일의 기본 위치는 `ConfigureServices`에서 변경될 수 있습니다.

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

이 예제에서 PO 파일은 *지역화* 폴더에서 로드됩니다.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>지역화 파일을 찾기 위한 사용자 지정 논리 구현

PO 파일을 배치하는 데 더 복잡한 논리가 필요한 경우 서비스로 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 인터페이스를 구현하고 등록할 수 있습니다. PO 파일을 다양한 위치에 저장할 수 있는 경우 또는 폴더의 계층 구조 내에서 파일을 찾아야 하는 경우에 유용합니다.

### <a name="using-a-different-default-pluralized-language"></a>복수화된 다른 기본 언어 사용

패키지는 두 개의 복수 양식과 관련된 `Plural` 확장 메서드를 포함합니다. 더 많은 복수 양식을 요구하는 언어의 경우 확장 메서드를 만듭니다. 확장 메서드를 사용하면 기본 언어 &mdash;에 대해 지역화 파일을 제공할 필요가 없습니다. 원본 문자열을 코드에서 직접 사용할 수 있습니다.

번역의 문자열 배열을 허용하는 보다 일반적인 `Plural(int count, string[] pluralForms, params object[] arguments)` 오버로드를 사용할 수 있습니다.

::: moniker-end
