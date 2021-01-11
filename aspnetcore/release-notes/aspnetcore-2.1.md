---
title: ASP.NET Core 2.1의 새로운 기능
author: isaac2004
description: ASP.NET Core 2.1의 새로운 기능에 대해 알아봅니다.
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
uid: aspnetcore-2.1
ms.openlocfilehash: 199ca62f71df5c0ee5ca2f78caf53ad8d6be1179
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854563"
---
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2.1의 새로운 기능

이 아티클에서는 ASP.NET Core 2.1의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## SignalR

SignalR은 ASP.NET Core 2.1에 대해 다시 작성되었습니다. ASP.NET Core SignalR에는 여러 가지 향상된 기능이 포함됩니다.

* 간소화된 스케일 아웃 모델
* JQuery에 종속되지 않는 새 JavaScript 클라이언트
* MessagePack에 기반한 새로운 초소형 이진 프로토콜
* 사용자 지정 프로토콜에 대한 지원
* 새 스트리밍 응답 모델
* 기본 Websocket에 기반한 클라이언트에 대한 지원

자세한 내용은 [ASP.NET Core SignalR](xref:signalr/introduction)을 참조하세요.

## <a name="no-locrazor-class-libraries"></a>Razor 클래스 라이브러리

ASP.NET Core 2.1을 통해 Razor 기반 UI를 빌드하고 라이브러리에 포함하고 여러 프로젝트 간에 공유합니다. 새로운 Razor SDK를 사용하면 NuGet 패키지에 포함될 수 있는 클래스 라이브러리 프로젝트에 Razor 파일을 빌드할 수 있습니다. 라이브러리의 보기 및 페이지는 자동으로 검색되고 앱에서 재정의할 수 있습니다. Razor 컴파일을 빌드에 통합하여 다음을 수행합니다.

* 앱 시작 시간이 훨씬 더 빠릅니다.
* 런타임 시 Razor 뷰 및 Pages에 대한 빠른 업데이트는 반복 개발 워크플로의 일부로 사용할 수 있습니다.

자세한 내용은 [Razor 클래스 라이브러리 프로젝트를 사용하여 재사용 가능한 UI 만들기](xref:razor-pages/ui-class)를 참조하세요.

## <a name="no-locidentity-ui-library--scaffolding"></a>Identity UI 라이브러리 및 스캐폴딩

ASP.NET Core 2.1에서는 [ASP.NET Core Identity](xref:security/authentication/identity)[를 Razor 클래스 라이브러리](xref:razor-pages/ui-class)로 제공합니다. Identity가 포함되는 앱은 선택적으로 Identity RCL(Razor 클래스 라이브러리)에 포함된 소스 코드를 추가하기 위해 새 Identity 스캐폴더를 적용할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 Identity RCL의 동일한 코드보다 우선합니다.

인증을 포함하지 **않는** 앱은 RCL Identity 패키지를 추가하기 위해 Identity 스캐폴더를 적용할 수 있습니다. 선택한 Identity 코드의 옵션이 생성되어야 합니다.

자세한 내용은 [ASP.NET Core 프로젝트에서 Identity 스캐폴드](xref:security/authentication/scaffold-identity)를 참조하세요.

## <a name="https"></a>HTTPS

보안 및 개인 정보에 더 중점을 두어 웹앱에서 HTTPS를 활성화하는 것이 중요합니다. 웹에서 HTTPS를 점점 더 많이 사용하고 있습니다. HTTPS를 사용하지 않는 사이트는 안전하지 않은 것으로 간주됩니다. 브라우저(Chromium, Mozilla)는 보안 컨텍스트에서 웹 기능을 사용하도록 강제 적용하기 시작합니다. [GDPR](xref:security/gdpr)은 사용자의 개인 정보를 보호하기 위해 HTTPS를 사용해야 합니다. 프로덕션에서 HTTPS를 사용하는 것이 중요한 반면 개발에서 HTTPS를 사용하면 배포의 문제(예: 안전하지 않은 링크)를 방지할 수 있습니다. ASP.NET Core 2.1에는 개발에서 HTTPS를 쉽게 사용하고 프로덕션에서 HTTPS를 쉽게 구성하는 다양한 향상된 기능이 포함되어 있습니다. 자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl)을 참조하세요.

### <a name="on-by-default"></a>기본적으로 설정

보안 웹 사이트 개발을 위해 HTTPS는 이제 기본적으로 사용됩니다. 2\.1부터는 Kestrel이 로컬 개발 인증서가 제공되는 경우 `https://localhost:5001`을 수신 대기합니다. 개발 인증서를 만듭니다.

* 처음에 SDK를 사용하면 .NET Core SDK 첫 실행 환경의 일부입니다.
* 수동으로 새 `dev-certs` 도구를 사용합니다.

인증서를 신뢰하려면 `dotnet dev-certs https --trust`를 실행합니다.

### <a name="https-redirection-and-enforcement"></a>HTTPS 리디렉션 및 적용

웹앱은 일반적으로 HTTP 및 HTTPS를 모두 수신 대기해야 하지만 그런 다음, 모든 HTTP 트래픽을 HTTPS로 리디렉션합니다. 2\.1에서는 구성 또는 바인딩된 서버 포트의 유무에 따라 지능적으로 리디렉션되는 특수한 HTTPS 리디렉션 미들웨어가 도입되었습니다.

HTTPS는 [HSTS(HTTP 엄격한 전송 보안 프로토콜)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)를 사용하여 더 적용될 수 있습니다. HSTS는 항상 HTTPS를 통해 사이트에 액세스하도록 브라우저에 지시합니다. ASP.NET Core 2.1은 최대 기간, 하위 도메인 및 HSTS 미리 로드 목록에 대한 몇 가지 옵션을 지원하는 HSTS 미들웨어를 추가합니다.

### <a name="configuration-for-production"></a>프로덕션에 대한 구성

프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다. 2\.1에서는 Kestrel에 HTTPS를 구성하는 기본 구성 스키마가 추가되었습니다. 다음을 사용하도록 앱을 구성할 수 있습니다.

* URL을 포함하는 여러 엔드포인트. 자세한 내용은 [Kestrel 웹 서버 구현: 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.
* 디스크의 파일 또는 인증서 저장소에서 HTTPS에 사용할 인증서입니다.

## <a name="gdpr"></a>GDPR

ASP.NET Core에서는 [EU GDPR(일반 데이터 보호 규정)](https://www.eugdpr.org/) 요구 사항의 일부를 충족하는 API 및 템플릿을 제공합니다. 자세한 내용은 [ASP.NET Core에서 GDPR 지원](xref:security/gdpr)을 참조하세요. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)에서는 ASP.NET Core 2.1 템플릿에 추가된 대부분의 GDPR 확장점 및 API를 사용하고 테스트하는 방법을 보여줍니다.

## <a name="integration-tests"></a>통합 테스트

테스트 생성 및 실행을 간소화하는 새 패키지가 도입되었습니다. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) 패키지는 다음과 같은 작업을 처리합니다.

* 종속성 파일( *\*.deps*)을 테스트된 앱에서 테스트 프로젝트의 *bin* 폴더로 복사합니다.
* 테스트를 실행하면 고정 파일 및 페이지/보기를 찾을 수 있도록 루트 콘텐츠를 테스트된 앱의 프로젝트 루트로 설정합니다.
* [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 사용하여 테스트된 앱의 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.

다음 테스트는 [xUnit](https://xunit.github.io/)를 사용하여 성공 상태 코드 및 올바른 콘텐츠 형식 헤더의 인덱스 페이지가 로드되는지 확인합니다.

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

자세한 내용은 [통합 테스트](xref:test/integration-tests) 항목을 참조하세요.

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1은 쉽게 명확하고 설명이 포함된 웹 API를 빌드할 수 있는 새 프로그래밍 규칙을 추가합니다. `ActionResult<T>`를 추가하여 앱이 응답 또는 다른 작업 결과를 반환할 수 있도록 추가된 새 형식(IActionResult와 유사)이지만 응답 형식을 나타냅니다. `[ApiController]` 특성은 Web API 특정 규칙 및 동작에 옵트인하는 방법으로도 추가되었습니다.

자세한 내용은 [ASP.NET Core를 사용하여 Web API 빌드](xref:web-api/index)를 참조하세요.

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1에는 앱에서 `HttpClient`의 인스턴스를 쉽게 구성하고 사용할 수 있는 새로운 `IHttpClientFactory` 서비스가 포함됩니다. `HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 처리기 위임이라는 개념이 이미 있습니다. 팩터리는 다음과 같습니다.

* 보다 직관적인 명명 클라이언트별 `HttpClient` 인스턴스를 등록합니다.
* Polly 정책을 다시 시도, CircuitBreakers 등에 사용할 수 있는 Polly 처리기를 구현합니다.

자세한 내용은 [HTTP 요청 시작](xref:fundamentals/http-requests)을 참조하세요.

## <a name="kestrel-libuv-transport-configuration"></a>Kestrel libuv 전송 구성

ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다. 자세한 내용은 [Kestrel 웹 서버 구현: Libuv 전송 구성](xref:fundamentals/servers/kestrel#libuv-transport-configuration)을 참조하세요.

## <a name="generic-host-builder"></a>제네릭 호스트 작성기

제네릭 호스트 작성기(`HostBuilder`)가 도입되었습니다. HTTP 요청(메시징, 백그라운드 작업 등)을 처리하지 않는 앱에 작성기를 사용할 수 있습니다.

자세한 내용은 [.NET 제네릭 호스트](xref:fundamentals/host/generic-host)를 참조하세요.

## <a name="updated-spa-templates"></a>업데이트된 SPA 템플릿

표준 프로젝트 구조를 사용하고 각 프레임워크에 대한 시스템을 빌드하도록 Angular, React 및 React with Redux에 대한 단일 페이지 애플리케이션 템플릿을 업데이트합니다.

Angular 템플릿은 Angular CLI에 기반하고 React 템플릿은 create-react-app에 기반합니다.

자세한 내용은 다음을 참조하세요.

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Razor 자산에 대한 Razor Pages 검색

2\.1에서는 나열된 순서로 다음 디렉터리에 있는 Razor 자산(예: 레이아웃 및 부분)에 대한 Razor Pages 검색은 다음과 같습니다.

1. 현재 Pages 폴더
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>영역의 Razor Pages

이제 Razor Pages는 [영역](xref:mvc/controllers/areas)을 지원합니다. 영역의 예제를 보려면 개별 사용자 계정을 사용하여 새 Razor Pages 웹앱을 만듭니다. 개별 사용자 계정을 사용하는 Razor Pages 웹앱에는 */Areas/Identity/Pages* 가 포함됩니다.

## <a name="mvc-compatibility-version"></a>MVC 호환성 버전

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 이상에서 도입된 주요 동작 변경 내용을 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다.

자세한 내용은 <xref:mvc/compatibility-version>를 참조하세요.

## <a name="migrate-from-20-to-21"></a>2\.0에서 2.1로 마이그레이션

[ASP.NET Core 2.0에서 2.1로 마이그레이션](xref:migration/20_21)을 참조하세요.

## <a name="additional-information"></a>추가 정보

전체 변경 내용 목록을 보려면 [ASP.NET Core 2.1 릴리스 정보](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)를 참조하세요.
