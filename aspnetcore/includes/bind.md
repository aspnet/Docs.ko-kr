---
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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536308"
---
관련 구성 값을 읽는 기본 방법은 [옵션 패턴](xref:fundamentals/configuration/options)를 사용하는 것입니다. 예를 들어 다음 구성 값을 읽으려면:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

다음 `PositionOptions` 클래스를 만듭니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

옵션 클래스:

* 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.
* 형식의 모든 공용 읽기-쓰기 속성이 바인딩됩니다.
* 필드가 바인딩되지 ***않았습니다***. 위 코드에서 `Position`은 바운딩되지 않습니다. `Position` 속성을 사용하므로 클래스를 구성 공급자에 바인딩할 때 문자열 `"Position"`을 앱에서 하드 코딩하지 않아도 됩니다.

코드는 다음과 같습니다.

* [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)를 호출하여 `PositionOptions` 클래스를 `Position` 섹션에 바인딩합니다.
* `Position` 구성 데이터를 표시합니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다. `ConfigurationBinder.Get<T>`가 `ConfigurationBinder.Bind`를 사용하는 것보다 편리할 수 있습니다. 다음 코드에서는 `ConfigurationBinder.Get<T>`와 `PositionOptions` 클래스를 함께 사용하는 방법을 보여 줍니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.

**옵션 패턴** 을 사용하는 경우 대체 방법은 `Position` 섹션을 바인딩하고 [종속성 주입 서비스 컨테이너](xref:fundamentals/dependency-injection)에 추가하는 것입니다. 다음 코드에서 `PositionOptions`는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

위의 코드를 사용하여 다음 코드는 위치 옵션을 읽습니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

위 코드에서는 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽지 ***않습니다***. 앱을 시작한 후의 변경 사항을 읽으려면 [IOptionsSnapshot](xref:fundamentals/configuration/options#ios)을 사용합니다.
