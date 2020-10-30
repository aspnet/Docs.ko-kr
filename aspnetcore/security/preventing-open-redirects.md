---
title: ASP.NET Core에서 열기 리디렉션 공격 방지
author: ardalis
description: ASP.NET Core 앱에 대해 열린 리디렉션 공격을 방지 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058391"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core에서 열기 리디렉션 공격 방지

Querystring 또는 양식 데이터와 같은 요청을 통해 지정 된 URL로 리디렉션하는 웹 앱은 사용자를 안전 하 고 악의적인 URL로 리디렉션하는 변조 될 수 있습니다. 이러한 변조를 개방 리디렉션 공격 이라고 합니다.

응용 프로그램 논리가 지정 된 URL로 리디렉션되는 경우 항상 리디렉션 URL이 변조 되지 않았는지 확인 해야 합니다. ASP.NET Core에는 개방형 리디렉션 (개방형 리디렉션) 공격 으로부터 앱을 보호 하는 데 도움이 되는 기본 제공 기능이 있습니다.

## <a name="what-is-an-open-redirect-attack"></a>개방형 리디렉션 공격 이란?

웹 응용 프로그램은 인증을 요구 하는 리소스에 액세스할 때 사용자를 로그인 페이지로 리디렉션합니다. 일반적으로 리디렉션에는 `returnUrl` 사용자가 성공적으로 로그인 한 후 원래 요청 된 URL로 반환 될 수 있도록 querystring 매개 변수가 포함 됩니다. 사용자가 인증 되 면 원래 요청한 URL로 리디렉션됩니다.

요청의 querystring에 대상 URL이 지정 되어 있으므로 악의적인 사용자가 querystring을 조작할 수 있습니다. 변조 된 querystring을 사용 하면 사이트가 사용자를 외부 악성 사이트로 리디렉션할 수 있습니다. 이 기술을 개방형 리디렉션 (또는 리디렉션) 공격 이라고 합니다.

### <a name="an-example-attack"></a>예제 공격

악의적인 사용자는 악의적인 사용자가 사용자의 자격 증명이 나 중요 한 정보에 액세스할 수 있도록 하는 공격을 개발할 수 있습니다. 공격을 시작 하기 위해 악의적인 사용자는 `returnUrl` URL에 추가 된 querystring 값을 사용 하 여 사이트의 로그인 페이지에 대 한 링크를 클릭 하도록 사용자를 유도 합니다. 예를 들어에서 `contoso.com` 로그인 페이지가 포함 된 앱이 있다고 가정 `http://contoso.com/Account/LogOn?returnUrl=/Home/About` 합니다. 이 공격은 다음 단계를 따릅니다.

1. 사용자가에 대 한 악의적인 링크를 클릭 합니다 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (두 번째 URL은 "contoso.com"이 아닌 "contoso **1** .com").
2. 사용자가 로그인 했습니다.
3. 사용자가 (사이트에서)로 리디렉션됩니다 `http://contoso1.com/Account/LogOn` (실제 사이트와 똑같이 보이는 악의적인 사이트).
4. 사용자가 다시 로그인 하 여 악의적인 사이트에 자격 증명을 제공 하 고 실제 사이트로 다시 리디렉션됩니다.

사용자는 첫 번째 로그인 시도가 실패 하 고 두 번째 시도가 성공 했음을 판단할 수 있습니다. 사용자는 자격 증명이 손상 되었음을 인식 하지 못할 가능성이 높습니다.

![리디렉션 공격 프로세스 열기](preventing-open-redirects/_static/open-redirection-attack-process.png)

로그인 페이지 외에도 일부 사이트는 리디렉션 페이지 또는 끝점을 제공 합니다. 응용 프로그램에 열린 리디렉션이 있는 페이지가 있다고 가정 `/Home/Redirect` 합니다. 예를 들어 공격자는로 이동 하는 전자 메일에 링크를 만들 수 있습니다 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` . 일반 사용자는 URL을 확인 하 고 사이트 이름으로 시작 하는 것을 확인 합니다. 이를 신뢰 하면 링크를 클릭 합니다. 그러면 열린 리디렉션이 사용자를 피싱 사이트로 보내고,이 사이트는 사용자와 동일 하 게 표시 되 고 사용자는 사용자가 생각 하는 사이트에 로그인 할 가능성이 높습니다.

## <a name="protecting-against-open-redirect-attacks"></a>오픈 리디렉션 공격 으로부터 보호

웹 응용 프로그램을 개발 하는 경우 모든 사용자 제공 데이터를 신뢰할 만큼의 데이터로 처리 합니다. 응용 프로그램에 URL의 내용에 따라 사용자를 리디렉션하는 기능이 있는 경우 이러한 리디렉션은 응용 프로그램 내에서 로컬로 또는 querystring에 제공 될 수 있는 URL이 아닌 알려진 URL로만 수행 해야 합니다.

### <a name="localredirect"></a>LocalRedirect

`LocalRedirect`기본 클래스의 도우미 메서드를 사용 합니다 `Controller` .

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` 로컬이 아닌 URL을 지정 하면에서 예외를 throw 합니다. 그렇지 않으면 메서드와 마찬가지로 동작 `Redirect` 합니다.

### <a name="islocalurl"></a>IsLocalUrl

다음을 리디렉션하기 전에 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 메서드를 사용 하 여 url을 테스트 합니다.

다음 예에서는 리디렉션하기 전에 URL이 로컬 인지 여부를 확인 하는 방법을 보여 줍니다.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl`메서드는 사용자가 실수로 악의적인 사이트로 리디렉션되는 것을 방지 합니다. 로컬 URL이 필요한 상황에서 로컬이 아닌 URL을 제공 하는 경우 제공 된 URL의 세부 정보를 기록할 수 있습니다. 리디렉션 Url 로깅은 리디렉션 공격을 진단 하는 데 도움이 될 수 있습니다.
