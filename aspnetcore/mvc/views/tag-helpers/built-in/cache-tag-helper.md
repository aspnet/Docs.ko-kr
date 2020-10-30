---
title: ASP.NET Core MVC의 캐시 태그 도우미
author: pkellner
description: 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: a87f91255bd1f280b1567f522423a6f4e88a6dd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060887"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>ASP.NET Core MVC의 캐시 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net)

캐시 태그 도우미는 ASP.NET Core 앱의 콘텐츠를 내부 ASP.NET Core 캐시 공급자에 캐시하여 성능을 개선하는 기능을 제공합니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

Razor현재 날짜를 캐시 하는 태그는 다음과 같습니다.

```cshtml
<cache>@DateTime.Now</cache>
```

이 태그 도우미를 포함하고 있는 페이지에 대한 첫 번째 요청은 현재 날짜를 표시합니다. 추가적인 요청은 캐시가 만료될 때까지(기본값 20분) 또는 캐시된 날짜가 캐시에서 제거될 때까지 캐시된 값을 표시합니다.

## <a name="cache-tag-helper-attributes"></a>캐시 태그 도우미 특성

### <a name="enabled"></a>사용

| 특성 유형  | 예제        | 기본값 |
| --------------- | --------------- | ------- |
| 부울         | `true`, `false` | `true`  |

`enabled`는 캐시 태그 도우미로 묶인 콘텐츠를 캐시할지 여부를 결정합니다. 기본값은 `true`입니다. `false`로 설정하면 렌더링된 출력이 캐시되지 **않습니다** .

예제:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| 특성 유형   | 예제                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on`은 캐시된 항목의 절대 만료 날짜를 설정합니다.

다음 예제는 2025년 1월 29일 오후 5시 2분까지 캐시 태그 도우미의 콘텐츠를 캐시합니다.

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| 특성 유형 | 예제                      | 기본값    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20분 |

`expires-after`는 첫 번째 요청 시간으로부터 콘텐츠를 캐시할 때까지의 시간 길이를 설정합니다.

예제:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Razor뷰 엔진은 기본값 `expires-after` 을 20 분으로 설정 합니다.

### <a name="expires-sliding"></a>expires-sliding

| 특성 유형 | 예제                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

캐시 항목 값이 액세스되지 않을 경우 캐시 항목을 제거해야 하는 시간을 설정합니다.

예제:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| 특성 유형 | 예                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header`는 값이 변경될 때 캐시 새로 고침을 트리거할 쉼표로 구분된 헤더 값 목록을 허용합니다.

다음 예제는 `User-Agent` 헤더 값을 모니터링합니다. 이 예제는 웹 서버에 제공된 서로 다른 모든 `User-Agent` 마다 콘텐츠를 캐시합니다.

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| 특성 유형 | 예             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query`는 나열된 키 값 중 하나가 변경될 때 캐시 새로 고침을 트리거할 쉼표로 구분된 쿼리 문자열(<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>)의 <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> 목록을 허용합니다.

다음 예제는 `Make` 및 `Model` 값을 모니터링합니다. 이 예제는 웹 서버에 제공된 서로 다른 모든 `Make` 및 `Model`마다 콘텐츠를 캐시합니다.

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| 특성 유형 | 예             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route`는 경로 데이터 매개 변수 값이 변경될 때 캐시 새로 고침을 트리거할 쉼표로 구분된 경로 매개 변수 이름 목록을 허용합니다.

예제:

*Startup.cs* :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*인덱스 cshtml* :

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a>다름cookie

| 특성 유형 | 예                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie`cookie값이 변경 될 때 캐시 새로 고침을 트리거하는 쉼표로 구분 된 이름 목록을 허용 cookie 합니다.

다음 예에서는와 연결 된를 모니터링 합니다 cookie ASP.NET Core Identity . 사용자가 인증 되 면에서 변경 내용이 Identity cookie 캐시 새로 고침을 트리거합니다.

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| 특성 유형  | 예제        | 기본값 |
| --------------- | --------------- | ------- |
| 부울         | `true`, `false` | `true`  |

`vary-by-user`는 로그인한 사용자(또는 컨텍스트 보안 주체)가 변경될 때 캐시를 다시 설정할지 여부를 지정합니다. 현재 사용자를 요청 컨텍스트 보안 주체 라고도 하며를 Razor 참조 하 여 뷰에서 볼 수 있습니다 `@User.Identity.Name` .

다음 예제는 현재 로그인한 사용자를 모니터링하여 캐시 새로 고침을 트리거합니다.

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

이 특성을 사용하면 로그인 및 로그아웃 주기 동안 콘텐츠를 캐시에 유지합니다. 값이 `true`로 설정되면 인증 주기가 인증된 사용자에 대한 캐시를 무효화시킵니다. cookie사용자가 인증 될 때 새 고유 값이 생성 되므로 캐시가 무효화 됩니다. 가 없거나가 만료 된 경우 캐시는 익명 상태에 대해 유지 관리 됩니다 cookie cookie . 사용자가 인증되지 **않으면** 캐시가 유지됩니다.

### <a name="vary-by"></a>vary-by

| 특성 유형 | 예제  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by`를 사용하면 캐시될 데이터를 사용자 지정할 수 있습니다. 특성의 문자열 값에서 참조하는 개체가 변경되면 캐시 태그 도우미의 콘텐츠가 갱신됩니다. 흔히 모델 값들의 문자열 연결을 이 특성에 할당합니다. 사실상 이는 연결된 값들 중 하나를 업데이트하면 캐시가 무효화되는 시나리오로 이어집니다.

다음 예제는 보기를 렌더링하는 컨트롤러 메서드가 두 경로 매개 변수 `myParam1` 및 `myParam2`의 정수 값을 합산하고, 합계를 단일 모델 속성으로 반환한다고 가정합니다. 합계가 변경되면 캐시 태그 도우미의 콘텐츠가 다시 렌더링 및 캐시됩니다.  

작업:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*인덱스 cshtml* :

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| 특성 유형      | 예제                               | 기본값  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority`는 기본 제공 캐시 공급자에게 캐시 제거 지침을 제공합니다. 웹 서버는 메모리가 부족할 경우 가장 먼저 `Low` 캐시 항목부터 제거합니다.

예제:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

`priority` 특성은 특정 수준의 캐시 보존을 보장하지 않습니다. `CacheItemPriority`는 제안 사항일 뿐입니다. 이 특성을 `NeverRemove`로 설정하더라도 캐시된 항목이 항상 보존된다고 보장할 수 없습니다. 자세한 내용은 [추가 리소스](#additional-resources) 섹션의 항목을 참조하세요.

캐시 태그 도우미는 [메모리 캐시 서비스](xref:performance/caching/memory)에 의존합니다. 해당 서비스가 추가되지 않은 경우 캐시 태그 도우미가 서비스를 추가합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
