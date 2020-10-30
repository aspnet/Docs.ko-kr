---
title: ASP.NET Core MVC에서 보기 기반 권한 부여
author: rick-anderson
description: 이 문서에서는 ASP.NET Core 뷰 내에서 권한 부여 서비스를 삽입 하 고 활용 하는 방법을 보여 줍니다 Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061329"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 보기 기반 권한 부여

개발자는 종종 현재 사용자 id를 기준으로 UI를 표시 하거나 숨기 거 나 수정 하려고 합니다. [종속성 주입](xref:fundamentals/dependency-injection)을 통해 MVC 뷰 내에서 권한 부여 서비스에 액세스할 수 있습니다. 권한 부여 서비스를 뷰에 삽입 하려면 Razor 다음 지시문을 사용 합니다 `@inject` .

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

모든 보기에서 권한 부여 서비스를 원하는 경우에는 `@inject` *Views* 디렉터리의 *_ViewImports cshtml* 파일에 지시문을 추가 합니다. 자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.

삽입 된 권한 부여 서비스를 사용 하 여 `AuthorizeAsync` [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)중에 확인 하는 것과 동일한 방식으로를 호출 합니다.

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

경우에 따라 리소스가 보기 모델이 됩니다. `AuthorizeAsync` [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)중에 확인 하는 것과 정확히 동일한 방법으로를 호출 합니다.

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

위의 코드에서 모델은 정책 평가를 고려해 야 하는 리소스로 전달 됩니다.

> [!WARNING]
> 앱의 UI 요소에 대 한 표시 여부를 단독 권한 부여 확인으로 전환 하는 것을 사용 하지 마세요. UI 요소를 숨기면 연결 된 컨트롤러 작업에 대 한 액세스를 완전히 방지할 수 없습니다. 예를 들어 위의 코드 조각에서 단추를 살펴보겠습니다. 사용자는 `Edit` 상대 리소스 URL이 */Document/Edit/1* 인 것으로 알고 있는 경우 작업 메서드를 호출할 수 있습니다. 이러한 이유로 `Edit` 작업 메서드는 자체 권한 부여 검사를 수행 해야 합니다.
