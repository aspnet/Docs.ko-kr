---
title: 웹 API 규칙 사용
author: pranavkm
description: ASP.NET Core에서 웹 API 규칙에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
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
uid: web-api/advanced/conventions
ms.openlocfilehash: 0c5ea8ba69e4c6287afce1771ac9cee65bb188a8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052541"
---
# <a name="use-web-api-conventions"></a>웹 API 규칙 사용

작성자: [Pranav Krishnamoorthy](https://github.com/pranavkm) 및 [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2.2 이상에는 일반적인 [API 설명서](xref:tutorials/web-api-help-pages-using-swagger)를 추출하여 여러 작업, 컨트롤러 또는 어셈블리 내 모든 컨트롤러에 적용하는 방법이 포함되어 있습니다. Web API 규칙은 개별 작업을로 데코레이팅하는 대체 방법 [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 입니다.

규칙을 통해 다음을 사용할 수 있습니다.

* 특정 유형의 작업에서 반환되는 가장 일반적인 반환 형식 및 상태 코드를 정의합니다.
* 정의된 표준에서 벗어나는 작업을 식별합니다.

ASP.NET Core MVC 2.2 이상에는 <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>의 기본 규칙 집합이 포함되어 있습니다. 규칙은 ASP.NET Core **API** 프로젝트 템플릿에서 제공되는 컨트롤러( *ValuesController.cs* )를 기반으로 합니다. 작업이 템플릿의 패턴을 따르는 경우, 기본 규칙을 성공적으로 사용해야 합니다. 기본 규칙이 요구 사항을 충족하지 못하는 경우 [웹 API 규칙 만들기](#create-web-api-conventions)를 참조하세요.

런타임 시 <xref:Microsoft.AspNetCore.Mvc.ApiExplorer>가 규칙을 이해합니다. `ApiExplorer`는 [OpenAPI](https://www.openapis.org/)(Swagger라고도 함) 문서 생성기와 통신하기 위한 MVC의 추상화입니다. 적용된 규칙의 특성은 작업과 연결되며 작업의 OpenAPI 설명서에 포함됩니다. [API 분석기](xref:web-api/advanced/analyzers)도 규칙을 이해합니다. 작업이 색다른 경우(예를 들어 적용된 규칙에 의해 문서화되지 않은 상태 코드를 반환하는 경우) 경고는 상태 코드를 문서화할 것을 권장합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>웹 API 규칙 적용

규칙이 구성되지 않으면 각 작업은 정확히 하나의 규칙과 연결될 수 있습니다. 더 구체적인 규칙은 덜 구체적인 규칙보다 우선합니다. 동일한 우선순위의 둘 이상의 규칙이 작업에 적용되는 경우 선택은 명확하지 않습니다. 다음 옵션은 가장 구체적인 것에서 가장 덜 구체적인 것까지 작업에 규칙을 적용할 수 있습니다.

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;개별 작업에 적용 되며, 적용 되는 규칙 형식 및 규칙 메서드를 지정 합니다.

    다음 예제에서는 기본 규칙 유형의 `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 규칙 메서드가 `Update` 작업에 적용됩니다.

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 규칙 메서드는 다음 특성을 작업에 적용합니다.

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    `[ProducesDefaultResponseType]`에 대한 자세한 내용은 [Default Response](https://swagger.io/docs/specification/describing-responses/#default)(기본 응답)를 참조하세요.

1. &mdash; 컨트롤러에 `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`를 적용하면 컨트롤러에서 모든 작업에 지정된 규칙 유형을 적용합니다. 규칙 메서드는 규칙 메서드가 적용되는 작업을 결정하는 힌트로 표시됩니다. 힌트에 대한 자세한 내용은 [웹 API 규칙 만들기](#create-web-api-conventions)를 참조하세요).

    다음 예제에서는 *ContactsConventionController* 의 모든 작업에 기본 규칙 집합이 적용됩니다.

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. &mdash; 어셈블리에 적용된 `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`는 현재 어셈블리의 모든 컨트롤러에 지정된 규칙 유형을 적용합니다. 권장 사항으로 어셈블리 수준 특성을 *Startup.cs* 파일에 적용합니다.

    다음 예제에서는 기본 규칙 집합이 어셈블리의 모든 컨트롤러에 적용됩니다.

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>웹 API 규칙 만들기

기본 API 규칙이 요구 사항을 충족하지 못하는 경우 고유한 규칙을 만듭니다. 규칙은 다음과 같습니다.

* 메서드가 있는 정적 형식입니다.
* 작업에 [응답 형식](#response-types) 및 [명명 요구 사항](#naming-requirements)을 정의하는 데 사용할 수 있습니다.

### <a name="response-types"></a>응답 형식

이러한 메서드에는 `[ProducesResponseType]` 또는 `[ProducesDefaultResponseType]` 특성으로 주석이 추가됩니다. 다음은 그 예입니다.

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

보다 구체적인 메타데이터 특성이 없는 경우 이 규칙을 어셈블리에 적용하면 다음과 같이 적용됩니다.

* 규칙 메서드는 `Find`라는 작업에 적용됩니다.
* `id`라는 매개 변수는 `Find` 작업에 있습니다.

### <a name="naming-requirements"></a>명명 요구 사항

`[ApiConventionNameMatch]` 및 `[ApiConventionTypeMatch]` 특성은 적용할 작업을 결정하는 규칙 메서드에 적용될 수 있습니다. 다음은 그 예입니다.

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

앞의 예제에서:

* 메서드에 적용된 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` 옵션은 규칙이 "찾기"로 접두사가 지정된 모든 작업과 일치함을 나타냅니다. 일치하는 작업의 예로는 `Find`, `FindPet` 및 `FindById`가 있습니다.
* 매개 변수에 적용된 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix`는 규칙이 접미사 식별자로 끝나는 정확히 하나의 매개 변수와 메서드가 일치함을 나타냅니다. 예제에는 `id` 또는 `petId`와 같은 매개 변수가 포함됩니다. `ApiConventionTypeMatch`는 매개 변수 유형을 제한하는 형식에 유사하게 적용할 수 있습니다. `params[]` 인수는 명시적으로 일치할 필요가 없는 나머지 매개 변수를 나타냅니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
