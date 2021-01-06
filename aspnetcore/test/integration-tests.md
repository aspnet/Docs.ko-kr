---
title: ASP.NET Core의 통합 테스트
author: rick-anderson
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: f1ce6a209ef3ca85abe0a6f1ac61d85bec52d17a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050825"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core의 통합 테스트

작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) 및 [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다. ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.

이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다. 테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다. Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.

* [Razor Pages 소개](xref:razor-pages/index)
* [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages 단위 테스트](xref:test/razor-pages-tests)

> [!NOTE]
> SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.

## <a name="introduction-to-integration-tests"></a>통합 테스트 소개

통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다. 단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다. 통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.

이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.

* 데이터베이스
* 파일 시스템
* 네트워크 어플라이언스
* 요청-응답 파이프라인

단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체* 로 알려져 있는 제작된 구성 요소를 사용합니다.

단위 테스트와 달리, 통합 테스트는 다음과 같습니다.

* 앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.
* 더 많은 코드와 데이터 처리가 필요합니다.
* 실행하는 데 시간이 더 오래 걸립니다.

따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다. 단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.

> [!TIP]
> 데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요. 앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다. 이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다. 단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.

> [!NOTE]
> 통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.
>
> *이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core의 통합 테스트

ASP.NET Core의 통합 테스트에는 다음이 필요합니다.

* 테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다. 테스트 프로젝트에는 SUT에 대한 참조가 있습니다.
* 테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.
* Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.

통합 테스트는 일반적인 *정렬*, *실행* 및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.

1. SUT의 웹 호스트가 구성되어 있습니다.
1. 앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.
1. *정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.
1. *실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.
1. *어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패* 로 검증됩니다.
1. 모든 테스트가 실행될 때까지 프로세스가 계속됩니다.
1. 테스트 결과가 보고됩니다.

일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다. 예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.

테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다. 이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.

`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.

* SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.
* 테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.
* `TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.

[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.

> [!NOTE]
> 앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다. 이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다. 또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.

Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이가 거의 없습니다. 유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다. Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`). MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).

## <a name="test-app-prerequisites"></a>테스트 앱 필수 조건

테스트 프로젝트는 다음을 수행해야 합니다.

* [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 참조합니다.
* 프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다.

이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다. *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다. 샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core도 테스트에 사용됩니다. 앱 참조:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT 환경

SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>기본 WebApplicationFactory를 사용하는 기본 테스트

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다. `TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.

테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.

다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다. 메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 cookie를 처리하는 `HttpClient`의 인스턴스를 만듭니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 cookie가 요청에서 유지되지 않습니다. TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 cookie를 유지하려면 테스트에서 필수로 표시합니다. cookie를 필수로 표시하는 방법에 대한 지침은 [필수 cookie](xref:security/gdpr#essential-cookies)를 참조하세요.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 사용자 지정

웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.

1. `WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있도록 합니다.

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다. 이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.

   SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다. 테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후* 에 실행됩니다. 실행 순서는 ASP.NET Core 3.0의 릴리스를 포함하는 [일반 호스트](xref:fundamentals/host/generic-host)의 호환성이 손상되는 변경에 해당합니다. 앱의 데이터베이스와는 다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다.

   여전히 [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 SUT의 경우, SUT의 `Startup.ConfigureServices` 코드 이전에 테스트 앱의 `builder.ConfigureServices` 콜백이 실행됩니다. 테스트 앱의 `builder.ConfigureTestServices` 콜백은 *이후에* 실행됩니다.

   샘플 앱은 데이터베이스 컨텍스트에 대한 서비스 설명자를 찾고, 이 설명자를 사용하여 서비스 등록을 제거합니다. 그런 다음, 팩터리는 테스트를 위해 메모리 내 데이터베이스를 사용하는 새 `ApplicationDbContext`를 추가합니다.

   메모리 내 데이터베이스가 아닌 다른 데이터베이스에 연결하려면 `UseInMemoryDatabase` 호출을 변경하여 컨텍스트를 다른 데이터베이스에 연결합니다. SQL Server 테스트 데이터베이스를 사용하려면 다음을 수행합니다.

   * 프로젝트 파일에서 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 패키지를 참조합니다.
   * 데이터베이스에 대한 연결 문자열을 사용하여 `UseSqlServer`를 호출합니다.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. 테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다. 다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다. [모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다. 첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.

3. 일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다. 테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.

1. 페이지에 대한 요청을 만듭니다.
1. 위조 방지 cookie를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.
1. 위조 방지 cookie 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.

* `GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다. `GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답* 을 준비하는 팩터리를 사용합니다. 자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.
* `HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다. `SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.
  * 양식(`IHtmlElement`)의 제출 단추
  * 양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)
  * 제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다. AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다. [HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다. 또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 cookie를 직접 처리하는 코드를 작성하는 것입니다.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder를 사용하여 클라이언트 사용자 지정

테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다. 이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.

`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다. SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>클라이언트 옵션

다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.

| 옵션 | 설명 | 기본값 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정합니다. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` 인스턴스에서 cookie를 처리할지 여부를 가져오거나 설정합니다. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다. | 7 |

`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>모의 서비스 주입

호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다. **모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**

샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다. 인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

다음 태그는 SUT 앱이 실행될 때 생성됩니다.

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다. 모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>모의 인증

`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.

* 인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.
* 인증된 사용자에 대한 콘텐츠를 반환합니다.

SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다. 자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.

* SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.
* 응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.

테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다. 최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler`는 인증 체계가 `Test`로 설정된 경우 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다. `Test` 체계가 앱에 필요한 체계와 일치하는 것이 중요합니다. 그렇지 않으면 인증이 작동하지 않습니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.

## <a name="set-the-environment"></a>환경 설정

기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다. `IHostBuilder`를 사용하여 SUT의 환경을 재정의하는 방법은 다음과 같습니다.

* `ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.
* `ASPNETCORE`를 접두사로 사용하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의합니다.

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

SUT에서 웹 호스트(`IWebHostBuilder`)를 사용하는 경우 `CreateWebHostBuilder`를 재정의합니다.

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법

`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다. 올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다. 앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.

## <a name="disable-shadow-copying"></a>섀도 복사 사용 안 함

섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다. 테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다. 자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.

다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>개체 삭제

`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다. 개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다. 자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.

## <a name="integration-tests-sample"></a>통합 테스트 샘플

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.

| 앱 | 프로젝트 디렉터리 | 설명 |
| --- | ----------------- | ----------- |
| 메시지 앱(SUT) | *src/RazorPagesProject* | 사용자가 메시지를 추가, 메시지 1개 또는 모든 메시지를 삭제, 메시지를 분석할 수 있도록 허용합니다. |
| 앱 테스트 | *tests/RazorPagesProject.Tests* | SUT에 대해 통합 테스트를 수행하는 데 사용됩니다. |

[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>메시지 앱(SUT) 조직

SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).
* 메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다. `Text` 속성은 필수이며 200자로 제한됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.
* 앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.
* 앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.

EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다. 이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다. 여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.

앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다. 자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](../mvc/controllers/testing.md)(샘플에서 리포지토리 패턴 구현)를 참조하세요.

### <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리에 있는 콘솔 앱입니다.

| 테스트 앱 디렉터리 | 설명 |
| ------------------ | ----------- |
| *AuthTests* | 다음에 대한 테스트 메서드를 포함합니다.<ul><li>인증되지 않은 사용자가 보안 페이지에 액세스</li><li>모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</li><li>GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</li></ul> |
| *BasicTests* | 라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다. |
| *IntegrationTests* | 사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</li><li>*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</li><li>*HttpClientExtensions.cs* 는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</li></ul> |

테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다. [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.

**테스트를 위해 데이터베이스 시드**

통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다. 예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.

샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지로 데이터베이스를 시드합니다.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다. 테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후* 에 실행됩니다. 다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다. 자세한 내용은 [WebApplicationFactory 사용자 지정](#customize-webapplicationfactory) 섹션을 참조하세요.

여전히 [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 SUT의 경우, SUT의 `Startup.ConfigureServices` 코드 이전에 테스트 앱의 `builder.ConfigureServices` 콜백이 실행됩니다. 테스트 앱의 `builder.ConfigureTestServices` 콜백은 *이후에* 실행됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다. ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.

이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다. 테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다. Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.

* [Razor Pages 소개](xref:razor-pages/index)
* [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages 단위 테스트](xref:test/razor-pages-tests)

> [!NOTE]
> SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.

## <a name="introduction-to-integration-tests"></a>통합 테스트 소개

통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다. 단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다. 통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.

이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.

* 데이터베이스
* 파일 시스템
* 네트워크 어플라이언스
* 요청-응답 파이프라인

단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체* 로 알려져 있는 제작된 구성 요소를 사용합니다.

단위 테스트와 달리, 통합 테스트는 다음과 같습니다.

* 앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.
* 더 많은 코드와 데이터 처리가 필요합니다.
* 실행하는 데 시간이 더 오래 걸립니다.

따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다. 단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.

> [!TIP]
> 데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요. 앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다. 이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다. 단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.

> [!NOTE]
> 통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.
>
> *이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core의 통합 테스트

ASP.NET Core의 통합 테스트에는 다음이 필요합니다.

* 테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다. 테스트 프로젝트에는 SUT에 대한 참조가 있습니다.
* 테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.
* Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.

통합 테스트는 일반적인 *정렬*, *실행* 및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.

1. SUT의 웹 호스트가 구성되어 있습니다.
1. 앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.
1. *정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.
1. *실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.
1. *어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패* 로 검증됩니다.
1. 모든 테스트가 실행될 때까지 프로세스가 계속됩니다.
1. 테스트 결과가 보고됩니다.

일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다. 예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.

테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다. 이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.

`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.

* SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.
* 테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.
* `TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.

[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.

> [!NOTE]
> 앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다. 이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다. 또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.

Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이가 거의 없습니다. 유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다. Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`). MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).

## <a name="test-app-prerequisites"></a>테스트 앱 필수 조건

테스트 프로젝트는 다음을 수행해야 합니다.

* 다음 패키지를 참조합니다.
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다. 웹 SDK는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요합니다.

이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다. *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다. 샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 환경

SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>기본 WebApplicationFactory를 사용하는 기본 테스트

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다. `TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.

테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.

다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다. 메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 cookie를 처리하는 `HttpClient`의 인스턴스를 만듭니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 cookie가 요청에서 유지되지 않습니다. TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 cookie를 유지하려면 테스트에서 필수로 표시합니다. cookie를 필수로 표시하는 방법에 대한 지침은 [필수 cookie](xref:security/gdpr#essential-cookies)를 참조하세요.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 사용자 지정

웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.

1. `WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하면 앱의 `Startup.ConfigureServices` 이전에 실행되는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있습니다. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하면 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)를 사용하여 서비스 컬렉션에서 등록된 서비스를 재정의하고 수정할 수 있습니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다. 이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.

2. 테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다. 다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다. [모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다. 첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.

3. 일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다. 테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.

1. 페이지에 대한 요청을 만듭니다.
1. 위조 방지 cookie를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.
1. 위조 방지 cookie 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.

* `GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다. `GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답* 을 준비하는 팩터리를 사용합니다. 자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.
* `HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다. `SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.
  * 양식(`IHtmlElement`)의 제출 단추
  * 양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)
  * 제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다. AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다. [HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다. 또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 cookie를 직접 처리하는 코드를 작성하는 것입니다.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder를 사용하여 클라이언트 사용자 지정

테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다. 이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.

`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다. SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>클라이언트 옵션

다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.

| 옵션 | Description | 기본값 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정합니다. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` 인스턴스에서 cookie를 처리할지 여부를 가져오거나 설정합니다. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다. | 7 |

`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>모의 서비스 주입

호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다. **모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**

샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다. 인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

다음 태그는 SUT 앱이 실행될 때 생성됩니다.

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다. 모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>모의 인증

`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.

* 인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.
* 인증된 사용자에 대한 콘텐츠를 반환합니다.

SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다. 자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.

* SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.
* 응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.

테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다. 최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler`는 인증 체계가 `Test`로 설정된 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.

## <a name="set-the-environment"></a>환경 설정

기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다. SUT의 환경을 재정의하려면 다음을 수행합니다.

* `ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.
* 테스트 앱의 `CreateWebHostBuilder`를 재정의하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 읽습니다.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

사용자 지정 <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>에서 호스트 작성기에 대해 직접 환경을 설정할 수도 있습니다.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법

`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다. 올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다. 앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.

## <a name="disable-shadow-copying"></a>섀도 복사 사용 안 함

섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다. 테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다. 자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.

다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.

```json
{
  "shadowCopy": false
}
```

Visual Studio를 사용하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사** 로 설정합니다. Visual Studio를 사용하지 않는 경우 테스트 앱의 프로젝트 파일에 `Content` 대상을 추가합니다.

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>개체 삭제

`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다. 개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다. 자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.

## <a name="integration-tests-sample"></a>통합 테스트 샘플

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.

| 앱 | 프로젝트 디렉터리 | 설명 |
| --- | ----------------- | ----------- |
| 메시지 앱(SUT) | *src/RazorPagesProject* | 사용자가 메시지를 추가, 메시지 1개 또는 모든 메시지를 삭제, 메시지를 분석할 수 있도록 허용합니다. |
| 앱 테스트 | *tests/RazorPagesProject.Tests* | SUT에 대해 통합 테스트를 수행하는 데 사용됩니다. |

[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>메시지 앱(SUT) 조직

SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).
* 메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다. `Text` 속성은 필수이며 200자로 제한됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.
* 앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.
* 앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.

EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다. 이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다. 여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.

앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다. 자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](../mvc/controllers/testing.md)(샘플에서 리포지토리 패턴 구현)를 참조하세요.

### <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리에 있는 콘솔 앱입니다.

| 테스트 앱 디렉터리 | 설명 |
| ------------------ | ----------- |
| *AuthTests* | 다음에 대한 테스트 메서드를 포함합니다.<ul><li>인증되지 않은 사용자가 보안 페이지에 액세스</li><li>모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</li><li>GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</li></ul> |
| *BasicTests* | 라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다. |
| *IntegrationTests* | 사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</li><li>*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</li><li>*HttpClientExtensions.cs* 는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</li></ul> |

테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다. [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.

**테스트를 위해 데이터베이스 시드**

통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다. 예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.

샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지로 데이터베이스를 시드합니다.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>