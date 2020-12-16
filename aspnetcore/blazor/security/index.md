---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: 8a61472da556db48a8572b0a59075beb7737a547
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506827"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a>ASP.NET Core Blazor 인증 및 권한 부여

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS) 및 [Luke Latham](https://github.com/guardrex)

ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.

Blazor Server와 Blazor WebAssembly 앱은 보안 시나리오가 서로 다릅니다. Blazor Server 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.

* 사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)
* 앱 영역과 구성 요소의 액세스 규칙

Blazor WebAssembly 앱이 클라이언트에서 실행됩니다. 권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다. 사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에 Blazor WebAssembly 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.

[Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization)은 라우팅 가능한 Razor 구성 요소에 적용되지 않습니다. 라우팅할 수 없는 Razor 구성 요소가 [페이지에 포함](xref:blazor/components/prerendering-and-integration)된 경우 페이지의 권한 부여 규칙은 페이지 콘텐츠의 나머지 부분과 함께 Razor 구성 요소에 간접적으로 영향을 미칩니다.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 및 <xref:Microsoft.AspNetCore.Identity.UserManager%601>는 Razor 구성 요소에서 지원되지 않습니다.

## <a name="authentication"></a>인증

Blazor는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다. 정확한 메커니즘은 Blazor 앱이 호스트되는 방법, Blazor WebAssembly 또는 Blazor Server에 따라 달라집니다.

### <a name="no-locblazor-webassembly-authentication"></a>Blazor WebAssembly 인증

Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다. JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.

다음을 추가합니다.

* 앱의 프로젝트 파일에 대한 [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) 패키지 참조
* 앱의 `_Imports.razor` 파일에 대한 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스

인증을 처리하려면 이어지는 섹션에서 설명하는 기본 제공 또는 사용자 지정 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스의 사용 방법을 참조하세요.

앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:blazor/security/webassembly/index> 문서를 참조하세요.

### <a name="no-locblazor-server-authentication"></a>Blazor Server 인증

Blazor Server 앱은 SignalR를 사용하여 생성된 실시간 연결을 통해 작동합니다. [SignalR 기반 앱](xref:signalr/authn-and-authz)의 인증은 연결 시 처리됩니다. 인증은 cookie 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.

Blazor Server 앱용 기본 제공 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스는 ASP.NET Core의 `HttpContext.User`에서 인증 상태 데이터를 가져옵니다. 이것이 바로 인증 상태가 기존의 ASP.NET Core 인증 메커니즘과 통합되는 방식입니다.

앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:blazor/security/server/index> 문서를 참조하세요.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider 서비스

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소와 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.

일반적으로 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 직접 사용하지는 않습니다. 이 문서의 뒷부분에서 설명하는 [`AuthorizeView` 구성 요소](#authorizeview-component) 또는 [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용합니다. <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.

DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/fundamentals/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.

## <a name="implement-a-custom-authenticationstateprovider"></a>사용자 지정 AuthenticationStateProvider 구현

앱에 사용자 지정 공급자가 필요한 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

Blazor WebAssembly 앱에서 `CustomAuthStateProvider` 서비스는 `Program.cs`의 `Main`에 등록됩니다.

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Blazor Server 앱에서 `CustomAuthStateProvider` 서비스는 `Startup.ConfigureServices`에 등록됩니다.

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

모든 사용자는 앞에 나온 예제의 `CustomAuthStateProvider`를 사용하여 사용자 이름 `mrfibuli`로 인증되었습니다.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>인증 상태를 연계 매개 변수로 공개

사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.

`App` 구성 요소(`App.razor`)에서 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 및 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 구성 요소를 사용하여 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 연계 매개 변수를 설정합니다.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Blazor WebAssembly 앱에서 옵션 및 권한 부여 서비스를 `Program.Main`에 추가합니다.

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Blazor Server 앱에는 옵션 및 권한 부여 서비스가 이미 있으므로 추가 작업이 필요하지 않습니다.

## <a name="authorization"></a>권한 부여

사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다.

일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.

* 사용자가 인증(로그인)되었는지 여부
* 사용자가 ‘역할’에 속하는지 여부
* 사용자에게 ‘클레임’이 있는지 여부
* ‘정책’이 충족되었는지 여부

이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다. ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 Identity](xref:security/index)의 문서를 참조하세요.

## <a name="authorizeview-component"></a>AuthorizeView 구성 요소

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 사용자에게 권한이 있는지에 따라 선택적으로 UI 콘텐츠를 표시합니다. 이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다.

이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 형식의 `context` 변수를 공개합니다.

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

사용자가 권한이 없는 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

`<Authorized>` 및 `<NotAuthorized>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.

이전 예제의 `<button>` 요소에 대한 `SecureMethod` 메서드와 같이 권한 있는 요소에 대한 기본 이벤트 처리기는 권한 있는 사용자만 호출할 수 있습니다.

UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.

권한 부여 조건을 지정하지 않으면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본 정책을 사용하고 다음과 같이 처리합니다.

* 인증(로그인)된 사용자를 권한 있는 사용자로 처리
* 인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 `NavMenu` 구성 요소(`Shared/NavMenu.razor`)에서 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)에 대한 목록 항목(`<li>...</li>`)을 표시하는 데 사용할 수 있지만, 이 방법은 렌더링된 출력에서 목록 항목을 제거할 뿐입니다. 사용자가 해당 구성 요소로 이동하는 것을 방지하지는 않습니다.

### <a name="role-based-and-policy-based-authorization"></a>역할 기반 및 정책 기반 권한 부여

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다. 

역할 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 매개 변수를 사용합니다.

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

자세한 내용은 <xref:security/authorization/roles>를 참조하세요.

정책 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 매개 변수를 사용합니다.

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다. 예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다. 자세한 내용은 <xref:security/authorization/policies>를 참조하세요.

이러한 API는 Blazor Server 또는 Blazor WebAssembly 앱에서 사용할 수 있습니다.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 또는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>를 지정하지 않으면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본 정책을 사용합니다.

### <a name="content-displayed-during-asynchronous-authentication"></a>비동기 인증 중에 표시되는 콘텐츠

Blazor에서는 인증 상태를 *비동기적으로* 확인할 수 있습니다. 이 접근 방식의 주요 시나리오는 Blazor WebAssembly 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.

인증이 진행되는 동안 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>는 기본적으로 아무 콘텐츠도 표시하지 않습니다. 인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 태그를 사용합니다.

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

이 접근 방식은 일반적으로 Blazor Server 앱에 적용할 수 없습니다. Blazor Server 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다. Blazor Server 앱의 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 구성 요소에 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.

## <a name="authorize-attribute"></a>[Authorize] 특성

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 Razor 구성 요소에서 사용될 수 있습니다.

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)를 사용합니다. 권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’. 페이지 내의 특정 파트 표시 권한을 부여하려면 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>를 대신 사용합니다.

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다. 역할 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 매개 변수를 사용합니다.

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

정책 기반 권한 부여의 경우 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 매개 변수를 사용합니다.

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 또는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>를 지정하지 않으면 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.

* 인증(로그인)된 사용자를 권한 있는 사용자로 처리
* 인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리

## <a name="customize-unauthorized-content-with-the-router-component"></a>Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 구성 요소와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.

* 콘텐츠를 찾을 수 없는 경우
* 사용자가 구성 요소에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 조건을 충족하지 못하는 경우 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 [`[Authorize]` 속성](#authorize-attribute) 섹션에서 설명합니다.
* 비동기 인증이 진행 중인 경우

기본 Blazor Server 프로젝트 템플릿에서 `App` 구성 요소(`App.razor`)는 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.

`<NotAuthorized>` 태그를 지정하지 않으면, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>는 다음 대체 메시지를 사용합니다.

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>인증 상태 변경 알림

예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, [사용자 지정 `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider)는 필요에 따라 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 기본 클래스에서 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> 메서드를 호출할 수 있습니다. 그러면 인증 상태 데이터의 소비자(예: <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.

## <a name="procedural-logic"></a>절차적 논리

앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다. `IAuthorizationService` 등의 다른 서비스와 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`를 결합하여 정책을 평가할 수 있습니다.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Blazor WebAssembly 앱 구성 요소에서 <xref:Microsoft.AspNetCore.Authorization> 및 <xref:Microsoft.AspNetCore.Components.Authorization> 네임스페이스를 추가합니다.
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> 이러한 네임스페이스는 앱의 `_Imports.razor` 파일에 추가하여 전역적으로 제공할 수 있습니다.

## <a name="troubleshoot-errors"></a>오류 문제 해결

일반적인 오류:

* **권한을 부여하려면 `Task\<AuthenticationState>` 형식의 연계 매개 변수가 필요합니다. `CascadingAuthenticationState`를 사용하여 이 변수를 제공하는 방법을 고려하세요.**

* **`authenticationStateTask`에 대해 `null` 값을 받았습니다.**

인증을 사용할 수 있는 Blazor Server 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다. UI 트리의 일부를 `<CascadingAuthenticationState>`로 래핑합니다. 다음은 `App` 구성 요소(`App.razor`)를 래핑한 예제입니다.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>는 기본 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI 서비스로부터 받은 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 연계 매개 변수를 제공합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Awesome Blazor: 인증](https://github.com/AdrienTorris/awesome-blazor#authentication) 커뮤니티 샘플 링크
