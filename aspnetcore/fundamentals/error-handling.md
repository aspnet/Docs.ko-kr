---
title: ASP.NET Core에서 오류 처리
author: rick-anderson
description: ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753116"
---
# <a name="handle-errors-in-aspnet-core"></a>ASP.NET Core에서 오류 처리

::: moniker range=">= aspnetcore-5.0"

작성자: [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)

이 항목에서는 ASP.NET Core 웹앱에서 오류를 처리하는 일반적인 접근법을 다룹니다. 웹 API는 <xref:web-api/handle-errors>를 참조하세요.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([다운로드하는 방법](xref:index#how-to-download-a-sample)) F12 브라우저 개발자 도구의 네트워크 탭은 샘플 앱을 테스트할 때 유용합니다.

## <a name="developer-exception-page"></a>개발자 예외 페이지

개발자 예외 페이지에는 요청 예외에 대한 자세한 정보가 표시됩니다. ASP.NET Core 템플릿에서 다음 코드를 생성합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

위의 강조 표시된 코드는 앱이 [개발 환경](xref:fundamentals/environments)에서 실행 중인 경우 개발자 예외 페이지를 사용하도록 설정합니다.

템플릿은 미들웨어 파이프라인의 앞부분에 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>를 배치하여 다음에 오는 미들웨어에서 throw된 예외를 catch할 수 있도록 합니다.

위의 코드는 앱이 개발 환경에서 실행되는 경우에 **만** 개발자 예외 페이지를 사용하도록 설정합니다. 앱이 프로덕션 환경에서 실행되는 경우에는 자세한 예외 정보를 공개적으로 표시해서는 안 됩니다. 환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

개발자 예외 페이지에는 예외 및 요청에 대한 다음 정보가 포함됩니다.

_ 스택 추적
* 쿼리 문자열 매개 변수(있는 경우)
* Cookie(있는 경우)
* 헤더

## <a name="exception-handler-page"></a>예외 처리기 페이지

[프로덕션 환경](xref:fundamentals/environments)의 사용자 지정 오류 처리 페이지를 구성하려면 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>를 호출합니다. 이 예외 처리 미들웨어에서 다음을 수행합니다.

* 예외를 잡고 기록합니다.
* 대체 파이프라인에서 표시된 경로를 사용하여 요청을 다시 실행합니다. 응답이 시작된 경우에는 요청이 다시 실행되지 않습니다. 템플릿 생성 코드는 `/Error` 경로를 사용하여 요청을 다시 실행합니다.

다음 예제에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 개발 환경이 아닌 환경에서 예외 처리 미들웨어를 추가합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Razor Pages 앱 템플릿은 *Pages* 폴더에 오류 페이지( *.cshtml*) 및 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스(`ErrorModel`)를 제공합니다. MVC 앱의 프로젝트 템플릿에는 홈 컨트롤러에 대한 `Error` 작업 메서드와 오류 보기가 포함됩니다.

오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요. 명시적 동사는 일부 요청이 작업 메서드에 도달하지 못하도록 합니다. 인증되지 않은 사용자에게 오류 보기를 표시해야 하는 경우 메서드에 대한 익명 액세스를 허용합니다.

### <a name="access-the-exception"></a>예외에 액세스

<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>를 사용하여 오류 처리기에서 예외 및 원래 요청 경로에 액세스합니다. 다음 코드에서는 ASP.NET Core 템플릿에서 생성된 기본 *Pages/Error.cshtml.cs* 에 `ExceptionMessage`를 추가합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> 클라이언트에 중요한 오류 정보를 **제공하지 마세요**. 오류 제공은 보안 위험입니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 예외를 테스트하려면 다음을 수행합니다.

* 환경을 프로덕션으로 설정합니다.
* *Program.cs* 의 `webBuilder.UseStartup<Startup>();`에서 주석을 제거합니다.
* 홈페이지에서 **예외 트리거** 를 선택합니다.

## <a name="exception-handler-lambda"></a>예외 처리기 람다

[사용자 지정 예외 처리기 페이지](#exception-handler-page)의 대안은 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>에 람다를 제공하는 것입니다. 람다를 사용하면 응답을 반환하기 전에 오류에 액세스할 수 있습니다.

다음 코드에서는 예외 처리에 람다를 사용합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 또는 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>에서 클라이언트에 중요한 오류 정보를 **제공하지 마세요**. 오류 제공은 보안 위험입니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 예외 처리 람다를 테스트하려면 다음을 수행합니다.

* 환경을 프로덕션으로 설정합니다.
* *Program.cs* 의 `webBuilder.UseStartup<StartupLambda>();`에서 주석을 제거합니다.
* 홈페이지에서 **예외 트리거** 를 선택합니다.

## <a name="usestatuscodepages"></a>UseStatusCodePages

기본적으로 ASP.NET Core 앱은 ‘404 - 찾을 수 없음’과 같은 HTTP 오류 상태 코드에 대한 상태 코드 페이지를 제공하지 않습니다. 앱에서 본문이 없는 HTTP 400-599 오류 상태 코드가 발생하면 상태 코드와 빈 응답 본문이 반환됩니다. 상태 코드 페이지를 제공하려면 상태 코드 페이지 미들웨어를 사용합니다. 일반적인 오류 상태 코드에 대해 기본 텍스트 전용 처리기를 사용하려면 `Startup.Configure` 메서드에서 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>를 호출합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

요청 처리 미들웨어보다 먼저 `UseStatusCodePages`를 호출합니다. 예를 들어 정적 파일 미들웨어 및 엔드포인트 미들웨어보다 먼저 `UseStatusCodePages`를 호출합니다.

`UseStatusCodePages`를 사용하지 않는 경우 엔드포인트가 없는 URL로 이동하면 엔드포인트를 찾을 수 없다는 브라우저 종속 오류 메시지가 반환됩니다. 예를 들어 `Home/Privacy2`로 이동하는 경우 `UseStatusCodePages`를 호출하면 브라우저에서 다음을 반환합니다.

```html
Status Code: 404; Not Found
```

`UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 다음을 수행합니다.

* 환경을 프로덕션으로 설정합니다.
* *Program.cs* 의 `webBuilder.UseStartup<StartupUseStatusCodePages>();`에서 주석을 제거합니다.
* 홈페이지에서 링크를 선택합니다.

> [!NOTE]
> 상태 코드 페이지 미들웨어는 예외를 catch하지 **않습니다**. 사용자 지정 오류 처리 페이지를 제공하려면 [예외 처리기 페이지](#exception-handler-page)를 사용하세요.

### <a name="usestatuscodepages-with-format-string"></a>형식 문자열을 사용하는 UseStatusCodePages

응답 콘텐츠 형식 및 텍스트를 사용자 지정하려면 콘텐츠 형식 및 형식 문자열을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

위의 코드에서 `{0}`은 오류 코드에 대한 자리 표시자입니다.

형식 문자열이 있는 `UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs* 의 `webBuilder.UseStartup<StartupFormat>();`에서 주석을 제거합니다.

### <a name="usestatuscodepages-with-lambda"></a>람다를 사용하는 UseStatusCodePages

사용자 지정 오류 처리 및 응답 쓰기 코드를 지정하려면 람다 식을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

람다를 사용하는 `UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs* 의 `webBuilder.UseStartup<StartupStatusLambda>();`에서 주석을 제거합니다.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 확장 메서드는:

* ‘302 - 찾음’ 상태 코드를 클라이언트에 보냅니다. 
* URL 템플릿에 제공된 오류 처리 엔드포인트로 클라이언트를 리디렉션합니다. 오류 처리 엔드포인트는 일반적으로 오류 정보를 표시하고 HTTP 200을 반환합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

위의 코드에 표시된 것처럼 URL 템플릿에는 상태 코드에 대한 `{0}` 자리 표시자가 포함될 수 있습니다. URL 템플릿이 `~`(물결표)로 시작하는 경우 `~`는 앱의 `PathBase`로 대체됩니다. 앱에서 엔드포인트를 지정할 때 해당 엔드포인트에 대한 MVC 보기 또는 Razor 페이지를 만듭니다. Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.

이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.

* 일반적으로 다른 앱이 오류를 처리하는 상황에서 앱이 클라이언트를 다른 엔드포인트로 리디렉션해야 하는 경우. 웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 리디렉션된 엔드포인트가 반영됩니다.
* 초기 리디렉션 응답과 함께 원래 상태 코드를 유지하고 반환하면 안 되는 경우.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs* 의 `webBuilder.UseStartup<StartupSCredirect>();`에서 주석을 제거합니다.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 확장 메서드는:

* 원래 상태 코드를 클라이언트에 반환합니다.
* 대체 경로에서 요청 파이프라인을 다시 실행하여 응답 본문을 생성합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

앱 내에 엔드포인트가 지정된 경우 해당 엔드포인트에 대한 MVC 보기 또는 Razor 페이지를 만듭니다. 요청이 상태 페이지로 다시 라우팅될 수 있도록 `UseRouting` 전에 `UseStatusCodePagesWithReExecute`를 배치합니다. Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.

이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.

* 다른 엔드포인트로 리디렉션하지 않고 요청을 처리해야 하는 경우. 웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 원래 요청된 엔드포인트가 반영됩니다.
* 원래 상태 코드를 유지하고 응답과 함께 반환해야 하는 경우.

URL 및 쿼리 문자열 템플릿에는 상태 코드에 대한 자리 표시자(`{0}`)가 포함될 수 있습니다. URL 템플릿은 `/`로 시작해야 합니다.

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

오류를 처리하는 엔드포인트는 다음 예제와 같이 오류를 생성한 원래 URL을 가져올 수 있습니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs* 의 `webBuilder.UseStartup<StartupSCreX>();`에서 주석을 제거합니다.

## <a name="disable-status-code-pages"></a>상태 코드 페이지 사용 안 함

MVC 컨트롤러 또는 작업 메서드에 대한 상태 코드 페이지를 비활성화하려면 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 특성을 사용합니다.

Razor Pages 처리기 메서드 또는 MVC 컨트롤러에서 특정 요청에 대한 상태 코드 페이지를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>를 사용합니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>예외 처리 코드

예외 처리 페이지의 코드도 예외를 throw할 수 있습니다. 프로덕션 오류 페이지는 철저히 테스트하고 자체적으로 예외를 throw하지 않도록 특히 주의해야 합니다.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>응답 헤더

또한 응답의 헤더가 전송되고 나면 다음 제한이 적용됩니다.

* 앱에서 응답의 상태 코드를 변경할 수 없습니다.
* 예외 페이지 또는 처리기를 실행할 수 없습니다. 응답을 완료하거나 연결이 중단되어야 합니다.

## <a name="server-exception-handling"></a>서버 예외 처리

앱의 예외 처리 논리 외에도 [HTTP 서버 구현](xref:fundamentals/servers/index)에서 몇 가지 예외를 처리할 수 있습니다. 응답 헤더가 전송되기 전에 예외를 catch한 서버는 응답 본문 없이 `500 - Internal Server Error` 응답을 보냅니다. 응답 헤더가 전송된 후에 예외를 catch한 서버는 연결을 닫습니다. 앱에서 처리되지 않는 요청은 서버에서 처리됩니다. 서버에서 요청을 처리할 때 발생하는 모든 예외는 서버의 예외 처리에 의해 처리됩니다. 앱의 사용자 지정 오류 페이지, 예외 처리 미들웨어 및 필터는 이 동작에 영향을 미치지 않습니다.

## <a name="startup-exception-handling"></a>시작 예외 처리

호스팅 계층만 앱 시작 시 발생하는 예외를 처리할 수 있습니다. [시작 오류를 캡처](xref:fundamentals/host/web-host#capture-startup-errors)하고 [자세한 오류를 캡처](xref:fundamentals/host/web-host#detailed-errors)하도록 호스트를 구성할 수 있습니다.

호스팅 계층은 호스트 주소/포트 바인딩 후에 오류가 발생하는 경우에만 캡처된 시작 오류에 대한 오류 페이지만 표시할 수 있습니다. 바인딩이 실패하면 결과는 다음과 같습니다.

* 호스팅 계층에서 심각한 예외를 기록합니다.
* dotnet 프로세스의 작동이 중단됩니다.
* HTTP 서버가 [Kestrel](xref:fundamentals/servers/kestrel)인 경우 오류 페이지가 표시되지 않습니다.

[IIS](/iis)(또는 Azure App Service) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)에서 실행 중일 때, 프로세스를 시작할 수 없는 경우 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 ‘502.5 - 프로세스 실패’를 반환합니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

## <a name="database-error-page"></a>데이터베이스 오류 페이지

데이터베이스 개발자 페이지 예외 필터 `AddDatabaseDeveloperPageExceptionFilter`는 Entity Framework Core 마이그레이션을 사용하여 해결할 수 있는 데이터베이스 관련 예외를 캡처합니다. 이 예외가 발생하면 문제 해결을 위해 가능한 작업의 세부 정보가 포함된 HTML 응답이 생성됩니다. 이 페이지는 개발 환경에서만 사용할 수 있습니다. 다음 코드는 개별 사용자 계정을 지정할 때 ASP.NET Core Razor 페이지 템플릿에서 생성되었습니다.

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>예외 필터

MVC 앱에서는 예외 필터를 전역으로 구성하거나 컨트롤러별 또는 작업별로 구성할 수 있습니다. Razor Pages 앱에서는 전역으로 구성하거나 페이지 모델별로 구성할 수 있습니다. 이러한 필터는 컨트롤러 작업 또는 다른 필터를 실행하는 동안 발생하는 처리되지 않은 예외를 처리합니다. 자세한 내용은 <xref:mvc/controllers/filters#exception-filters>를 참조하세요.

예외 필터는 MVC 작업 내에서 발생하는 예외를 트래핑하는 데 유용하지만 기본 제공 [예외 처리 미들웨어](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler`만큼 유연하지는 않습니다. 선택한 MVC 작업에 따라 오류 처리를 다르게 수행해야 하는 경우에만 `UseExceptionHandler`를 사용하는 것이 좋습니다.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>모델 상태 오류

모델 상태 오류를 처리하는 방법에 대한 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 및 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

작성자: [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)

이 항목에서는 ASP.NET Core 웹앱에서 오류를 처리하는 일반적인 접근법을 다룹니다. 웹 API는 <xref:web-api/handle-errors>를 참조하세요.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([다운로드하는 방법](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>개발자 예외 페이지

개발자 예외 페이지에는 요청 예외에 대한 자세한 정보가 표시됩니다. ASP.NET Core 템플릿에서 다음 코드를 생성합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

위의 코드는 앱이 [개발 환경](xref:fundamentals/environments)에서 실행 중인 경우 개발자 예외 페이지를 사용하도록 설정합니다.

템플릿은 미들웨어 앞에 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>를 배치하여 다음에 오는 미들웨어에서 예외를 catch할 수 있도록 합니다.

위의 코드에서는 **앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정합니다. 앱이 프로덕션에서 실행되는 경우에는 자세한 예외 정보를 공개적으로 표시해서는 안 됩니다. 환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

개발자 예외 페이지에는 예외 및 요청에 대한 다음 정보가 포함됩니다.

* 스택 추적
* 쿼리 문자열 매개 변수(있는 경우)
* Cookie(있는 경우)
* 헤더

## <a name="exception-handler-page"></a>예외 처리기 페이지

프로덕션 환경의 사용자 지정 오류 처리 페이지를 구성하려면 예외 처리 미들웨어를 사용합니다. 이 미들웨어는 다음을 수행합니다.

* 예외를 잡고 기록합니다.
* 표시된 페이지 또는 컨트롤러에 대한 대체 파이프라인에서 요청을 다시 실행합니다. 응답이 시작된 경우에는 요청이 다시 실행되지 않습니다. 템플릿 생성 코드는 `/Error`에 대한 요청을 다시 실행합니다.

다음 예제에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 비 개발 환경에서 예외 처리 미들웨어를 추가합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Razor Pages 앱 템플릿은 *Pages* 폴더에 오류 페이지( *.cshtml*) 및 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스(`ErrorModel`)를 제공합니다. MVC 앱의 프로젝트 템플릿에는 홈 컨트롤러에 대한 오류 작업 메서드와 오류 보기가 포함됩니다.

오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요. 명시적 동사는 일부 요청이 메서드에 도달하지 못하게 방해합니다. 인증되지 않은 사용자에게 오류 보기를 표시해야 하는 경우 메서드에 대한 익명 액세스를 허용합니다.

### <a name="access-the-exception"></a>예외에 액세스

<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>를 사용하여 오류 처리기 컨트롤러 또는 페이지에서 예외 및 원래 요청 경로에 액세스합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> 클라이언트에 중요한 오류 정보를 **제공하지 마세요**. 오류 제공은 보안 위험입니다.

위의 예외 처리 페이지를 트리거하려면 환경을 프로덕션으로 설정하고 예외를 적용합니다.

## <a name="exception-handler-lambda"></a>예외 처리기 람다

[사용자 지정 예외 처리기 페이지](#exception-handler-page)의 대안은 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>에 람다를 제공하는 것입니다. 람다를 사용하면 응답을 반환하기 전에 오류에 액세스할 수 있습니다.

다음은 예외 처리를 위해 람다를 사용하는 예제입니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

위의 코드에서 Internet Explorer 브라우저에 IE 오류 메시지가 아닌 오류 메시지가 표시되도록 `await context.Response.WriteAsync(new string(' ', 512));`가 추가됩니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/16144)를 참조하세요.

> [!WARNING]
> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 또는 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>에서 클라이언트에 중요한 오류 정보를 **제공하지 마세요**. 오류 제공은 보안 위험입니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 예외 처리 람다의 결과를 확인하려면 `ProdEnvironment` 및 `ErrorHandlerLambda` 전처리기 지시문을 사용하고 홈페이지에서 **Trigger an exception** 을 선택합니다.

## <a name="usestatuscodepages"></a>UseStatusCodePages

기본적으로 ASP.NET Core 앱은 ‘404 - 찾을 수 없음’과 같은 HTTP 상태 코드에 대한 상태 코드 페이지를 제공하지 않습니다. 앱은 상태 코드와 빈 응답 본문을 반환합니다. 상태 코드 페이지를 제공하려면 상태 코드 페이지 미들웨어를 사용합니다.

미들웨어는 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 패키지를 통해 사용할 수 있습니다.

일반적인 오류 상태 코드에 대해 기본 텍스트 전용 처리기를 사용하려면 `Startup.Configure` 메서드에서 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>를 호출합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

요청 처리 미들웨어(예: 정적 파일 미들웨어 및 MVC 미들웨어) 이전에 `UseStatusCodePages`를 호출합니다.

`UseStatusCodePages`를 사용하지 않는 경우 엔드포인트가 없는 URL로 이동하면 엔드포인트를 찾을 수 없다는 브라우저 종속 오류 메시지가 반환됩니다. 예를 들어 `Home/Privacy2`로 이동하는 경우 `UseStatusCodePages`를 호출하면 브라우저에서 다음을 반환합니다.

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>형식 문자열을 사용하는 UseStatusCodePages

응답 콘텐츠 형식 및 텍스트를 사용자 지정하려면 콘텐츠 형식 및 형식 문자열을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>람다를 사용하는 UseStatusCodePages

사용자 지정 오류 처리 및 응답 쓰기 코드를 지정하려면 람다 식을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 확장 메서드는:

* ‘302 - 찾음’ 상태 코드를 클라이언트에 보냅니다. 
* 클라이언트를 URL 템플릿에 제공된 위치로 리디렉션합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

예제에서 볼 수 있는 것과 같이 URL 템플릿에는 상태 코드에 대한 `{0}` 자리 표시자가 포함될 수 있습니다. URL 템플릿이 `~`(물결표)로 시작하는 경우 `~`는 앱의 `PathBase`로 대체됩니다. 앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다. Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml* 을 참조하세요.

이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.

* 일반적으로 다른 앱이 오류를 처리하는 상황에서 앱이 클라이언트를 다른 엔드포인트로 리디렉션해야 하는 경우. 웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 리디렉션된 엔드포인트가 반영됩니다.
* 초기 리디렉션 응답과 함께 원래 상태 코드를 유지하고 반환하면 안 되는 경우.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 확장 메서드는:

* 원래 상태 코드를 클라이언트에 반환합니다.
* 대체 경로에서 요청 파이프라인을 다시 실행하여 응답 본문을 생성합니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다. 요청이 상태 페이지로 다시 라우팅될 수 있도록 `UseRouting` 전에 `UseStatusCodePagesWithReExecute`를 배치합니다. Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml* 을 참조하세요.

이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.

* 다른 엔드포인트로 리디렉션하지 않고 요청을 처리해야 하는 경우. 웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 원래 요청된 엔드포인트가 반영됩니다.
* 원래 상태 코드를 유지하고 응답과 함께 반환해야 하는 경우.

URL 및 쿼리 문자열 템플릿에는 상태 코드에 대한 자리 표시자(`{0}`)가 포함될 수 있습니다. URL 템플릿은 슬래시(`/`)로 시작해야 합니다. 경로에서 자리 표시자를 사용하는 경우, 엔드포인트(페이지 또는 컨트롤러)가 경로 세그먼트를 처리할 수 있는지 확인하세요. 예를 들어 오류를 위한 Razor 페이지는 `@page` 지시문을 사용하여 선택적 경로 세그먼트 값을 수락해야 합니다.

```cshtml
@page "{code?}"
```

오류를 처리하는 엔드포인트는 다음 예제와 같이 오류를 생성한 원래 URL을 가져올 수 있습니다.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>상태 코드 페이지 사용 안 함

MVC 컨트롤러 또는 작업 메서드에 대한 상태 코드 페이지를 비활성화하려면 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 특성을 사용합니다.

Razor Pages 처리기 메서드 또는 MVC 컨트롤러에서 특정 요청에 대한 상태 코드 페이지를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>를 사용합니다.

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>예외 처리 코드

예외 처리 페이지의 코드도 예외를 던질 수 있습니다. 프로덕션 오류 페이지를 완전한 정적 콘텐츠로 구성하는 것은 종종 좋은 생각입니다.

### <a name="response-headers"></a>응답 헤더

또한 응답의 헤더가 전송되고 나면 다음 제한이 적용됩니다.

* 앱에서 응답의 상태 코드를 변경할 수 없습니다.
* 예외 페이지 또는 처리기를 실행할 수 없습니다. 응답을 완료하거나 연결이 중단되어야 합니다.

## <a name="server-exception-handling"></a>서버 예외 처리

앱의 예외 처리 논리 외에도 [HTTP 서버 구현](xref:fundamentals/servers/index)에서 몇 가지 예외를 처리할 수 있습니다. 응답 헤더가 전송되기 전에 예외를 catch한 서버는 응답 본문 없이 ‘500 - 내부 서버 오류’ 응답을 보냅니다. 응답 헤더가 전송된 후에 예외를 catch한 서버는 연결을 닫습니다. 앱으로 처리되지 않는 요청은 서버에서 처리됩니다. 서버에서 요청을 처리할 때 발생하는 모든 예외는 서버의 예외 처리에 의해 처리됩니다. 앱의 사용자 지정 오류 페이지, 예외 처리 미들웨어 및 필터는 이 동작에 영향을 미치지 않습니다.

## <a name="startup-exception-handling"></a>시작 예외 처리

호스팅 계층만 앱 시작 시 발생하는 예외를 처리할 수 있습니다. [시작 오류를 캡처](xref:fundamentals/host/web-host#capture-startup-errors)하고 [자세한 오류를 캡처](xref:fundamentals/host/web-host#detailed-errors)하도록 호스트를 구성할 수 있습니다.

호스팅 계층은 호스트 주소/포트 바인딩 후에 오류가 발생하는 경우에만 캡처된 시작 오류에 대한 오류 페이지만 표시할 수 있습니다. 바인딩이 실패하면 결과는 다음과 같습니다.

* 호스팅 계층에서 심각한 예외를 기록합니다.
* dotnet 프로세스의 작동이 중단됩니다.
* HTTP 서버가 [Kestrel](xref:fundamentals/servers/kestrel)인 경우 오류 페이지가 표시되지 않습니다.

[IIS](/iis)(또는 Azure App Service) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)에서 실행 중일 때, 프로세스를 시작할 수 없는 경우 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 ‘502.5 - 프로세스 실패’를 반환합니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

## <a name="database-error-page"></a>데이터베이스 오류 페이지

데이터베이스 오류 페이지 미들웨어는 Entity Framework 마이그레이션을 사용하여 해결할 수 있는 데이터베이스 관련 예외를 잡습니다. 이 예외가 발생하면 문제 해결을 위한 가능한 작업의 세부 정보가 포함된 HTML 응답이 생성됩니다. 이 페이지는 개발 환경에서만 사용하도록 설정해야 합니다. `Startup.Configure`에 코드를 추가하여 페이지를 사용하도록 설정합니다.

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>에는 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 패키지가 필요합니다.

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>예외 필터

MVC 앱에서는 예외 필터를 전역으로 구성하거나 컨트롤러별 또는 작업별로 구성할 수 있습니다. Razor Pages 앱에서는 전역으로 구성하거나 페이지 모델별로 구성할 수 있습니다. 이러한 필터는 컨트롤러 작업 또는 다른 필터를 실행하는 동안 발생하는 처리되지 않은 예외를 처리합니다. 자세한 내용은 <xref:mvc/controllers/filters#exception-filters>를 참조하세요.

> [!TIP]
> 예외 필터는 MVC 작업 내에서 발생하는 예외를 트래핑하는 데 유용하지만 예외 처리 미들웨어만큼 유연하지는 않습니다. 미들웨어를 사용하는 것이 좋습니다. 선택한 MVC 작업에 따라 오류 처리를 다르게 수행해야 하는 경우에만 필터를 사용하세요.

## <a name="model-state-errors"></a>모델 상태 오류

모델 상태 오류를 처리하는 방법에 대한 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 및 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
