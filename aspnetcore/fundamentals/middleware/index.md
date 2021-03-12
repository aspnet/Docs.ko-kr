---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 15d011e88ab291173668a0b6dc5f46e97fdfeff0
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605713"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core 미들웨어

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)

미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다. 각 구성 요소는 다음과 같습니다.

* 요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.
* 파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.

요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다. 요청 대리자는 각 HTTP 요청을 처리합니다.

요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다. 개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다. 이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다. 요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다. 미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.

<xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder로 미들웨어 파이프라인 만들기

ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다. 다음 다이어그램은 그 개념을 보여줍니다. 실행 스레드는 검은색 화살표를 따릅니다.

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다. 각 미들웨어는 해당 논리를 실행하고 next() 문에서 다음 미들웨어로 요청을 전달합니다. 세 번째 미들웨어가 요청을 처리한 후 요청은 클라이언트에 대한 응답으로 앱을 떠나기 전에 해당 next() 문 후에 추가 처리를 위해 반대 순서로 이전의 두 개 미들웨어를 통해 다시 전달됩니다.](index/_static/request-delegate-pipeline.png)

각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다. 예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.

가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다. 이 경우 실제 요청 파이프라인은 포함되지 않습니다. 대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다. `next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다. *next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다. 다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다. 단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다. 예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다. 추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다. 그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.

> [!WARNING]
> 클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오. 응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다. 예를 들어 [헤더 및 상태 코드를 설정하면 예외를 throw합니다](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). `next`를 호출한 후 응답 본문에 작성할 경우:
>
> * 프로토콜 위반이 발생할 수 있습니다. 예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.
> * 본문 형식을 손상시킬 수 있습니다. 예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 `next` 매개 변수를 받지 않습니다. 첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다. `Run`이 규칙입니다. 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다. `Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.

<a name="order"></a>

## <a name="middleware-order"></a>미들웨어 순서

다음 다이어그램은 ASP.NET Core MVC 및 Razor Pages 앱의 전체 요청 처리 파이프라인을 보여 줍니다. 일반적인 앱에서 기존 미들웨어의 순서가 지정되는 방식과 사용자 지정 미들웨어가 추가되는 위치를 볼 수 있습니다. 사용자는 원하는 대로 기존 미들웨어의 순서를 바꾸거나 시나리오의 필요에 따라 새 사용자 지정 미들웨어를 주입할 수 있습니다.

![ASP.NET Core 미들웨어 파이프라인](index/_static/middleware-pipeline.svg)

앞에 나온 다이어그램의 **엔드포인트** 미들웨어는 해당 앱 형식&mdash;MVC 또는 Razor Pages에 따라 필터 파이프라인을 실행합니다.

![ASP.NET Core 필터 파이프라인](index/_static/mvc-endpoint.svg)

미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다. 순서는 보안, 성능 및 기능에 **중요** 합니다.

다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 일반적인 권장 순서에 따라 추가합니다.

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

위의 코드에서

* [개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.
* 모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다. 예를 들어:
  * `UseCors`, `UseAuthentication`, `UseAuthorization`은 표시된 순서를 지켜야 합니다.
  * 현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.

일부 시나리오의 경우 미들웨어는 다른 순서를 갖습니다. 예를 들어 캐싱 및 압축 순서는 시나리오마다 다르며 유효한 순서가 여러 개 있습니다. 예를 들어:

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

위의 코드를 사용하면 압축된 응답을 캐싱하여 CPU를 저장할 수 있지만 gzip 또는 brotli와 같은 다른 압축 알고리즘을 사용하여 리소스의 여러 표현을 캐싱할 수도 있습니다.

다음 순서는 정적 파일을 결합하여 압축된 정적 파일 캐싱을 허용합니다.

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
app.UseStaticFiles();
```

다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.

1. 예외/오류 처리
   * 앱이 개발 환경에서 실행되는 경우:
     * 개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.
     * 데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.
   * 프로덕션 환경에서 앱을 실행하는 경우:
     * 예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.
     * HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.
1. HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.
1. 정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.
1. Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.
1. 요청을 라우팅하도록 미들웨어(<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 라우팅.
1. 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.
1. 권한 부여 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.
1. 세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다. 앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.
1. 엔드포인트 라우팅 미들웨어(<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>를 포함하는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다. 따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.

정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다. 정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**. *wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다. 정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.

요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다. 인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다. 인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.

다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다. 정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다. Razor Pages 응답을 압축할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseRouting();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

SPA(단일 페이지 애플리케이션)의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다. SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.

* 다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.
* 클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.

SPA에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref:spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.

### <a name="forwarded-headers-middleware-order"></a>전달된 헤더 미들웨어 순서

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>미들웨어 파이프라인 분기

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다. `Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다. 요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.

| 요청             | 응답                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.

`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다. `Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다. 다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.

| 요청                       | 응답                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=main | Branch used = main         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다. `MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하지 않는 경우 기본 파이프라인에 다시 연결됩니다.

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=18-19)]

위 예제에서는 “Hello from main pipeline.” 응답이 모든 요청에 대해 기록됩니다. 요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.

## <a name="built-in-middleware"></a>기본 제공 미들웨어

ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다. *순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다. 미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다. 단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.

| 미들웨어 | 설명 | 순서 |
| ---------- | ----------- | ----- |
| [인증](xref:security/authentication/identity) | 인증 지원을 제공합니다. | `HttpContext.User`가 필요하기 전에. OAuth 콜백에 대한 터미널. |
| [권한 부여](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | 권한 부여 지원을 제공합니다. | 인증 미들웨어 바로 뒤에 있습니다. |
| [Cookie 정책](xref:security/gdpr) | 개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다. | cookie를 발행하는 미들웨어 전에. 예: 인증, 세션, MVC(TempData). |
| [CORS](xref:security/cors) | 원본 간 리소스 공유를 구성합니다. | CORS를 사용하는 구성 요소 이전. 현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.|
| [진단](xref:fundamentals/error-handling) | 개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어. | 오류를 생성하는 구성 요소 이전. 예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다. |
| [전달된 헤더](xref:host-and-deploy/proxy-load-balancer) | 프록시된 헤더를 현재 요청에 전달합니다. | 업데이트된 필드를 사용하는 구성 요소 전에. 예: 체계, 호스트, 클라이언트 IP, 메서드. |
| [상태 검사](xref:host-and-deploy/health-checks) | ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다. | 요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다. |
| [헤더 전파](xref:fundamentals/http-requests#header-propagation-middleware) | 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다. |
| [HTTP 메서드 재정의](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | 들어오는 POST 요청이 메서드를 재정의하도록 허용합니다. | 업데이트된 메서드를 사용하는 구성 요소 앞입니다. |
| [HTTPS 리디렉션](xref:security/enforcing-ssl#require-https) | 모든 HTTP 요청을 HTTPS로 리디렉션합니다. | URL을 사용하는 구성 요소 이전. |
| [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | 특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다. | 응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에. 예: 전달된 헤더, URL 재작성. |
| [MVC](xref:mvc/overview) | MVC/Razor Pages를 사용하여 요청을 처리합니다. | 요청이 경로와 일치하는 경우 터미널입니다. |
| [OWIN](xref:fundamentals/owin) | OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다. | OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다. |
| [응답 캐싱](xref:performance/caching/middleware) | 응답 캐시에 대한 지원을 제공합니다. | 캐싱이 필요한 구성 요소 이전. `UseCORS`는 `UseResponseCaching` 앞에 와야 합니다.|
| [응답 압축](xref:performance/response-compression) | 응답 압축에 대한 지원을 제공합니다. | 압축이 필요한 구성 요소 이전. |
| [요청 지역화](xref:fundamentals/localization) | 지역화 지원을 제공합니다. | 지역화 구분 구성 요소 이전. |
| [엔드포인트 라우팅](xref:fundamentals/routing) | 요청 경로를 정의하고 제한합니다. | 경로 일치에 대한 터미널. |
| [SPA](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다. | 정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.|
| [세션](xref:fundamentals/app-state) | 사용자 세션 관리에 대한 지원을 제공합니다. | 세션이 필요한 구성 요소 이전. | 
| [정적 파일](xref:fundamentals/static-files) | 정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다. | 요청이 파일과 일치하는 경우 터미널입니다. |
| [URL 재작성](xref:fundamentals/url-rewriting) | URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다. | URL을 사용하는 구성 요소 이전. |
| [WebSockets](xref:fundamentals/websockets) | WebSocket 프로토콜을 활성화합니다. | WebSocket 요청을 수락하는 데 필요한 구성 요소 이전. |

## <a name="additional-resources"></a>추가 자료

* [수명 및 등록 옵션](xref:fundamentals/dependency-injection#lifetime-and-registration-options) ‘범위가 지정되고’, ‘일시적인’ *singleton* 수명 서비스를 사용하는 미들웨어의 전체 샘플이 포함되어 있습니다. 
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)

미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다. 각 구성 요소는 다음과 같습니다.

* 요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.
* 파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.

요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다. 요청 대리자는 각 HTTP 요청을 처리합니다.

요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다. 개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다. 이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다. 요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다. 미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.

<xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder로 미들웨어 파이프라인 만들기

ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다. 다음 다이어그램은 그 개념을 보여줍니다. 실행 스레드는 검은색 화살표를 따릅니다.

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다. 각 미들웨어는 해당 논리를 실행하고 next() 문에서 다음 미들웨어로 요청을 전달합니다. 세 번째 미들웨어가 요청을 처리한 후 요청은 클라이언트에 대한 응답으로 앱을 떠나기 전에 해당 next() 문 후에 추가 처리를 위해 반대 순서로 이전의 두 개 미들웨어를 통해 다시 전달됩니다.](index/_static/request-delegate-pipeline.png)

각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다. 예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.

가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다. 이 경우 실제 요청 파이프라인은 포함되지 않습니다. 대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 파이프라인을 종료합니다.

<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다. `next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다. *next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다. 다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다. 단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다. 예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다. 추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다. 그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.

> [!WARNING]
> 클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오. 응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다. 예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다. `next`를 호출한 후 응답 본문에 작성할 경우:
>
> * 프로토콜 위반이 발생할 수 있습니다. 예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.
> * 본문 형식을 손상시킬 수 있습니다. 예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.

<a name="order"></a>

## <a name="middleware-order"></a>미들웨어 순서

미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다. 순서는 보안, 성능 및 기능에 **중요** 합니다.

다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

위의 코드에서

* [개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.
* 모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다. 예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.

다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.

1. 예외/오류 처리
   * 앱이 개발 환경에서 실행되는 경우:
     * 개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.
     * 데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.
   * 프로덕션 환경에서 앱을 실행하는 경우:
     * 예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.
     * HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.
1. HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.
1. 정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.
1. Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.
1. 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.
1. 세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다. 앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.
1. MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>)는 MVC를 요청 파이프라인에 추가합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다. 따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.

정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다. 정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**. *wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다. 정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.

요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다. 인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다. 인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.

다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다. 정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다. <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>의 MVC 응답은 압축할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Use, Run 및 Map

<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>을 사용하여 HTTP 파이프라인을 구성합니다. `Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우). `Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다. `Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다. 요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.

| 요청             | 응답                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다. `Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다. 다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.

| 요청                       | 응답                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=main | Branch used = main         |

`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>기본 제공 미들웨어

ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다. *순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다. 미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다. 단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.

| 미들웨어 | 설명 | 순서 |
| ---------- | ----------- | ----- |
| [인증](xref:security/authentication/identity) | 인증 지원을 제공합니다. | `HttpContext.User`가 필요하기 전에. OAuth 콜백에 대한 터미널. |
| [Cookie 정책](xref:security/gdpr) | 개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다. | cookie를 발행하는 미들웨어 전에. 예: 인증, 세션, MVC(TempData). |
| [CORS](xref:security/cors) | 원본 간 리소스 공유를 구성합니다. | CORS를 사용하는 구성 요소 이전. |
| [진단](xref:fundamentals/error-handling) | 개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어. | 오류를 생성하는 구성 요소 이전. 예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다. |
| [전달된 헤더](xref:host-and-deploy/proxy-load-balancer) | 프록시된 헤더를 현재 요청에 전달합니다. | 업데이트된 필드를 사용하는 구성 요소 전에. 예: 체계, 호스트, 클라이언트 IP, 메서드. |
| [상태 검사](xref:host-and-deploy/health-checks) | ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다. | 요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다. |
| [HTTP 메서드 재정의](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | 들어오는 POST 요청이 메서드를 재정의하도록 허용합니다. | 업데이트된 메서드를 사용하는 구성 요소 앞입니다. |
| [HTTPS 리디렉션](xref:security/enforcing-ssl#require-https) | 모든 HTTP 요청을 HTTPS로 리디렉션합니다. | URL을 사용하는 구성 요소 이전. |
| [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | 특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다. | 응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에. 예: 전달된 헤더, URL 재작성. |
| [MVC](xref:mvc/overview) | MVC/Razor Pages를 사용하여 요청을 처리합니다. | 요청이 경로와 일치하는 경우 터미널입니다. |
| [OWIN](xref:fundamentals/owin) | OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다. | OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다. |
| [응답 캐싱](xref:performance/caching/middleware) | 응답 캐시에 대한 지원을 제공합니다. | 캐싱이 필요한 구성 요소 이전. |
| [응답 압축](xref:performance/response-compression) | 응답 압축에 대한 지원을 제공합니다. | 압축이 필요한 구성 요소 이전. |
| [요청 지역화](xref:fundamentals/localization) | 지역화 지원을 제공합니다. | 지역화 구분 구성 요소 이전. |
| [엔드포인트 라우팅](xref:fundamentals/routing) | 요청 경로를 정의하고 제한합니다. | 경로 일치에 대한 터미널. |
| [세션](xref:fundamentals/app-state) | 사용자 세션 관리에 대한 지원을 제공합니다. | 세션이 필요한 구성 요소 이전. |
| [정적 파일](xref:fundamentals/static-files) | 정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다. | 요청이 파일과 일치하는 경우 터미널입니다. |
| [URL 재작성](xref:fundamentals/url-rewriting) | URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다. | URL을 사용하는 구성 요소 이전. |
| [WebSockets](xref:fundamentals/websockets) | WebSocket 프로토콜을 활성화합니다. | WebSocket 요청을 수락하는 데 필요한 구성 요소 이전. |

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
