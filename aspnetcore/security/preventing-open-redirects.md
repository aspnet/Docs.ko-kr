---
title: ASP.NET Core에서 열기 리디렉션 공격 방지
author: ardalis
description: ASP.NET Core 앱에 대해 열린 리디렉션 공격을 방지 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 07/07/2017
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058391"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="f1b2b-103">ASP.NET Core에서 열기 리디렉션 공격 방지</span><span class="sxs-lookup"><span data-stu-id="f1b2b-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="f1b2b-104">Querystring 또는 양식 데이터와 같은 요청을 통해 지정 된 URL로 리디렉션하는 웹 앱은 사용자를 안전 하 고 악의적인 URL로 리디렉션하는 변조 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="f1b2b-105">이러한 변조를 개방 리디렉션 공격 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="f1b2b-106">응용 프로그램 논리가 지정 된 URL로 리디렉션되는 경우 항상 리디렉션 URL이 변조 되지 않았는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="f1b2b-107">ASP.NET Core에는 개방형 리디렉션 (개방형 리디렉션) 공격 으로부터 앱을 보호 하는 데 도움이 되는 기본 제공 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="f1b2b-108">개방형 리디렉션 공격 이란?</span><span class="sxs-lookup"><span data-stu-id="f1b2b-108">What is an open redirect attack?</span></span>

<span data-ttu-id="f1b2b-109">웹 응용 프로그램은 인증을 요구 하는 리소스에 액세스할 때 사용자를 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="f1b2b-110">일반적으로 리디렉션에는 `returnUrl` 사용자가 성공적으로 로그인 한 후 원래 요청 된 URL로 반환 될 수 있도록 querystring 매개 변수가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="f1b2b-111">사용자가 인증 되 면 원래 요청한 URL로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="f1b2b-112">요청의 querystring에 대상 URL이 지정 되어 있으므로 악의적인 사용자가 querystring을 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="f1b2b-113">변조 된 querystring을 사용 하면 사이트가 사용자를 외부 악성 사이트로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="f1b2b-114">이 기술을 개방형 리디렉션 (또는 리디렉션) 공격 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="f1b2b-115">예제 공격</span><span class="sxs-lookup"><span data-stu-id="f1b2b-115">An example attack</span></span>

<span data-ttu-id="f1b2b-116">악의적인 사용자는 악의적인 사용자가 사용자의 자격 증명이 나 중요 한 정보에 액세스할 수 있도록 하는 공격을 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="f1b2b-117">공격을 시작 하기 위해 악의적인 사용자는 `returnUrl` URL에 추가 된 querystring 값을 사용 하 여 사이트의 로그인 페이지에 대 한 링크를 클릭 하도록 사용자를 유도 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="f1b2b-118">예를 들어에서 `contoso.com` 로그인 페이지가 포함 된 앱이 있다고 가정 `http://contoso.com/Account/LogOn?returnUrl=/Home/About` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="f1b2b-119">이 공격은 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="f1b2b-120">사용자가에 대 한 악의적인 링크를 클릭 합니다 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (두 번째 URL은 "contoso.com"이 아닌 "contoso **1** .com").</span><span class="sxs-lookup"><span data-stu-id="f1b2b-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso **1** .com", not "contoso.com").</span></span>
2. <span data-ttu-id="f1b2b-121">사용자가 로그인 했습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="f1b2b-122">사용자가 (사이트에서)로 리디렉션됩니다 `http://contoso1.com/Account/LogOn` (실제 사이트와 똑같이 보이는 악의적인 사이트).</span><span class="sxs-lookup"><span data-stu-id="f1b2b-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="f1b2b-123">사용자가 다시 로그인 하 여 악의적인 사이트에 자격 증명을 제공 하 고 실제 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="f1b2b-124">사용자는 첫 번째 로그인 시도가 실패 하 고 두 번째 시도가 성공 했음을 판단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="f1b2b-125">사용자는 자격 증명이 손상 되었음을 인식 하지 못할 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![리디렉션 공격 프로세스 열기](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="f1b2b-127">로그인 페이지 외에도 일부 사이트는 리디렉션 페이지 또는 끝점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="f1b2b-128">응용 프로그램에 열린 리디렉션이 있는 페이지가 있다고 가정 `/Home/Redirect` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="f1b2b-129">예를 들어 공격자는로 이동 하는 전자 메일에 링크를 만들 수 있습니다 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="f1b2b-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="f1b2b-130">일반 사용자는 URL을 확인 하 고 사이트 이름으로 시작 하는 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="f1b2b-131">이를 신뢰 하면 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="f1b2b-132">그러면 열린 리디렉션이 사용자를 피싱 사이트로 보내고,이 사이트는 사용자와 동일 하 게 표시 되 고 사용자는 사용자가 생각 하는 사이트에 로그인 할 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="f1b2b-133">오픈 리디렉션 공격 으로부터 보호</span><span class="sxs-lookup"><span data-stu-id="f1b2b-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="f1b2b-134">웹 응용 프로그램을 개발 하는 경우 모든 사용자 제공 데이터를 신뢰할 만큼의 데이터로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="f1b2b-135">응용 프로그램에 URL의 내용에 따라 사용자를 리디렉션하는 기능이 있는 경우 이러한 리디렉션은 응용 프로그램 내에서 로컬로 또는 querystring에 제공 될 수 있는 URL이 아닌 알려진 URL로만 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="f1b2b-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="f1b2b-136">LocalRedirect</span></span>

<span data-ttu-id="f1b2b-137">`LocalRedirect`기본 클래스의 도우미 메서드를 사용 합니다 `Controller` .</span><span class="sxs-lookup"><span data-stu-id="f1b2b-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="f1b2b-138">`LocalRedirect` 로컬이 아닌 URL을 지정 하면에서 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="f1b2b-139">그렇지 않으면 메서드와 마찬가지로 동작 `Redirect` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="f1b2b-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="f1b2b-140">IsLocalUrl</span></span>

<span data-ttu-id="f1b2b-141">다음을 리디렉션하기 전에 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 메서드를 사용 하 여 url을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="f1b2b-142">다음 예에서는 리디렉션하기 전에 URL이 로컬 인지 여부를 확인 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="f1b2b-143">`IsLocalUrl`메서드는 사용자가 실수로 악의적인 사이트로 리디렉션되는 것을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="f1b2b-144">로컬 URL이 필요한 상황에서 로컬이 아닌 URL을 제공 하는 경우 제공 된 URL의 세부 정보를 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="f1b2b-145">리디렉션 Url 로깅은 리디렉션 공격을 진단 하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b2b-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
