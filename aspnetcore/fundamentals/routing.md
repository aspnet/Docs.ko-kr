---
title: ASP.NET Core에서 라우팅
author: rick-anderson
description: ASP.NET Core 라우팅이 HTTP 요청을 일치시키고 실행 가능 엔드포인트로 디스패치하는 역할을 담당하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
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
uid: fundamentals/routing
ms.openlocfilehash: e3dd7168e6974f63fa963d3732bc5df41814c70e
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491624"
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core에서 라우팅

작성자: [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

라우팅은 들어오는 HTTP 요청을 일치시켜 앱의 실행 가능 엔드포인트로 디스패치하는 역할을 담당합니다. [엔드포인트](#endpoint)는 앱의 실행 가능 요청 처리 코드 단위입니다. 엔드포인트는 앱에서 정의되고 앱 시작 시 구성됩니다. 엔드포인트 일치 프로세스는 요청의 URL에서 값을 추출하고 요청 처리를 위해 이 값을 제공할 수 있습니다. 또한 라우팅은 앱의 엔드포인트 정보를 사용하여 엔드포인트에 매핑되는 URL을 생성할 수도 있습니다.

앱은 다음을 사용하여 라우팅을 구성할 수 있습니다.

- Controllers
- Razor Pages
- SignalR
- gRPC 서비스
- [상태 검사](xref:host-and-deploy/health-checks)와 같은 엔드포인트 지원 [미들웨어](xref:fundamentals/middleware/index)
- 라우팅에 등록된 대리자 및 람다

이 문서에서는 ASP.NET Core 라우팅의 하위 수준 세부 정보를 설명합니다. 라우팅을 구성하는 방법은 다음을 참조하세요.

* 컨트롤러의 경우 <xref:mvc/controllers/routing>을 참조하세요.
* Razor Pages 규칙은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.

이 문서에서 설명하는 엔드포인트 라우팅 시스템은 ASP.NET Core 3.0 이상에 적용됩니다. <xref:Microsoft.AspNetCore.Routing.IRouter> 기반의 이전 라우팅 시스템에 대한 자세한 내용은 다음 접근 방법 중 하나를 사용하여 ASP.NET Core 2.1 버전을 선택합니다.

* 이전 버전의 버전 선택기
* [ASP.NET Core 2.1 라우팅](?view=aspnetcore-2.1) 선택

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서의 다운로드 샘플은 특정 `Startup` 클래스를 통해 사용할 수 있습니다. 특정 샘플을 실행하려면 *Program.cs* 를 수정하여 원하는 `Startup` 클래스를 호출합니다.

## <a name="routing-basics"></a>라우팅 기본 사항

모든 ASP.NET Core 템플릿에는 생성된 코드에서의 라우팅이 포함됩니다. 라우팅은 `Startup.Configure`의 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 등록됩니다.

다음 코드에서는 라우팅의 기본 예제를 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

라우팅은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 및 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>를 통해 등록된 미들웨어 쌍을 사용합니다.

* `UseRouting`은 경로 일치를 미들웨어 파이프라인에 추가합니다. 이 미들웨어는 앱에 정의된 엔드포인트 집합을 확인하고 요청을 기반으로 [가장 일치하는 항목](#urlm)을 선택합니다.
* `UseEndpoints`는 엔드포인트 실행을 미들웨어 파이프라인에 추가합니다. 선택한 엔드포인트와 연결된 대리자를 실행합니다.

앞의 예제에는 [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) 메서드를 사용하는 단일 ‘라우팅 대상 코드’ 엔드포인트가 포함됩니다.

* HTTP `GET` 요청이 루트 URL `/`로 전송되면 다음과 같이 됩니다.
  * 표시된 요청 대리자가 실행됩니다.
  * `Hello World!`가 HTTP 응답에 기록됩니다. 기본적으로 루트 URL `/`는 `https://localhost:5001/`입니다.
* 요청 메서드가 `GET`이 아니거나 루트 URL이 `/`가 아니면 일치하는 경로가 없고 HTTP 404가 반환됩니다.

### <a name="endpoint"></a>엔드포인트

<a name="endpoint"></a>

`MapGet` 메서드는 **엔드포인트** 을 정의하는 데 사용됩니다. 엔드포인트는 다음과 같을 수 있습니다.

* URL 및 HTTP 메서드를 일치시켜 선택됩니다.
* 대리자를 실행하여 실행됩니다.

앱에서 일치시키고 실행할 수 있는 엔드포인트는 `UseEndpoints`에서 구성됩니다. 예를 들어 <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 및 [이들과 유사한 메서드](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions)는 요청 대리자를 라우팅 시스템에 연결합니다.
ASP.NET Core Framework 기능을 라우팅 시스템에 연결하는 데 다음과 같은 추가 메서드를 사용할 수 있습니다.
- Razor Pages의 [MapRazorPages ](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [컨트롤러의 MapControllers](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [SignalR의 MapHub\<THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [gRPC의 MapGrpcService\<TService>](xref:grpc/aspnetcore)

다음 예제에서는 더 복잡한 경로 템플릿을 사용한 라우팅을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

`/hello/{name:alpha}` 문자열은 **경로 템플릿** 이며 엔드포인트가 일치되는 방식을 구성합니다. 이 경우 템플릿은 다음과 일치합니다.

* `/hello/Ryan`과 같은 URL
* `/hello/`로 시작하고 그 다음에 시퀀스 영문자가 오는 URL 경로.  `:alpha`는 영문자와만 일치하는 경로 제약 조건을 적용합니다. [경로 제약 조건](#route-constraint-reference)은 이 문서의 뒷부분에 설명되어 있습니다.

URL 경로의 두 번째 세그먼트 `{name:alpha}`는 다음과 같습니다.

* `name` 매개 변수에 바인딩됩니다.
* [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)에 캡처되고 저장됩니다.

이 문서에서 설명하는 엔드포인트는 라우팅 시스템은 ASP.NET Core 3.0의 새로운 기능입니다. 그러나 모든 버전의 ASP.NET Core에서 동일한 경로 템플릿 기능 및 경로 제약 조건 집합을 지원합니다.

다음 예제에서는 [상태 검사](xref:host-and-deploy/health-checks) 및 권한 부여를 사용한 라우팅을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

앞의 예제에서는 다음을 수행하는 방법을 보여 줍니다.

* 권한 부여 미들웨어를 라우팅에 사용할 수 있습니다.
* 엔드포인트를 사용하여 권한 부여 동작을 구성할 수 있습니다.

<xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> 호출은 상태 검사 엔드포인트를 추가합니다. 이 호출에 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>을 연결하면 권한 부여 정책이 엔드포인트에 연결됩니다.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*>을 호출하면 인증 및 권한 부여 미들웨어가 추가됩니다. 이러한 미들웨어는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*>과 `UseEndpoints` 사이에 배치되므로 다음을 수행할 수 있습니다.

* `UseRouting`에서 선택된 엔드포인트를 확인합니다.
* 엔드포인트로 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>가 디스패치되기 전에 권한 부여 정책을 적용합니다.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>엔드포인트 메타데이터

앞의 예제에는 두 개의 엔드포인트가 있지만 상태 검사 엔드포인트에만 권한 부여 정책이 연결되어 있습니다. 요청이 상태 검사 엔드포인트 `/healthz`와 일치하는 경우 권한 부여 확인이 수행됩니다. 이는 엔드포인트에 추가 데이터가 연결될 수 있음을 보여 줍니다. 이러한 추가 데이터를 엔드포인트 **메타데이터** 라고 합니다.

* 이 메타데이터는 라우팅 인식 미들웨어에서 처리될 수 있으며
* 모든 .NET 형식일 수 있습니다.

## <a name="routing-concepts"></a>라우팅 개념

라우팅 시스템은 강력한 **엔드포인트** 개념을 추가하여 미들웨어 파이프라인을 기반으로 빌드됩니다. 엔드포인트는 라우팅, 권한 부여 및 ASP.NET Core 시스템 수의 측면에서 서로 다른 앱의 기능 단위를 나타냅니다.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>ASP.NET Core 엔드포인트 정의

ASP.NET Core 엔드포인트는 다음과 같습니다.

* 실행 가능: <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>가 있습니다.
* 확장 가능: [메타데이터](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) 컬렉션이 있습니다.
* Selectable: 필요에 따라 [라우팅 정보](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)가 있습니다.
* 열거 가능: [DI](xref:fundamentals/dependency-injection)에서 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>를 검색하여 엔드포인트 컬렉션을 나열할 수 있습니다.

다음 코드에서는 현재 요청과 일치하는 엔드포인트를 검색하고 검사하는 방법을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

엔드포인트는 선택된 경우 `HttpContext`에서 검색할 수 있습니다. 해당 속성을 검사할 수 있습니다. 엔드포인트 개체는 변경할 수 없으며 만든 후 수정할 수 없습니다. 가장 일반적인 형식의 엔드포인트는 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>입니다. `RouteEndpoint`에는 라우팅 시스템에서 선택할 수 있는 정보가 포함됩니다.

앞의 코드에서 [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*)는 인라인 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.

<a name="mt"></a>

다음 코드에서는 파이프라인에서 `app.Use`가 호출되는 위치에 따라 엔드포인트가 없을 수 있음을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

위의 샘플에서는 엔드포인트가 선택되었는지 아닌지를 표시하는 `Console.WriteLine` 문을 추가합니다. 명확하게 하도록 이 샘플에서는 제공된 `/` 엔드포인트에 표시 이름을 할당합니다.

`/`의 URL을 사용하여 이 코드를 실행하면 다음이 표시됩니다.

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

다른 URL을 사용하여 이 코드를 실행하면 다음이 표시됩니다.

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

이 출력은 다음을 나타냅니다.

* `UseRouting`을 호출하기 전에 엔드포인트는 항상 null입니다.
* 일치 항목이 발견되면 `UseRouting`와 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>사이에서 엔드포인트가 null이 아닙니다.
* 일치 항목이 발견되면 `UseEndpoints` 미들웨어가 **터미널** 입니다. [터미널 미들웨어](#tm)는 이 문서의 뒷부분에 정의되어 있습니다.
* `UseEndpoints` 뒤의 미들웨어는 일치 항목이 없는 경우에만 실행됩니다.

`UseRouting` 미들웨어는 [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) 메서드를 사용하여 엔드포인트를 현재 컨텍스트에 연결합니다. `UseRouting` 미들웨어를 사용자 지정 논리로 바꾸어도 엔드포인트를 사용하는 이점을 얻을 수 있습니다. 엔드포인트는 미들웨어와 같은 하위 수준 기본 형식이며 라우팅 구현에 결합되지 않습니다. 대부분의 앱에서는 `UseRouting`을 사용자 지정 논리로 바꿀 필요가 없습니다.

`UseEndpoints` 미들웨어는 `UseRouting` 미들웨어와 함께 사용하기 위한 것입니다. 엔드포인트를 실행하는 핵심 논리는 복잡하지 않습니다. <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>를 사용하여 엔드포인트를 검색한 다음 해당 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 속성을 호출하면 됩니다.

다음 코드에서는 미들웨어가 라우팅에 영향을 주거나 반응하는 방식을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

앞의 예제에서는 다음과 같은 두 가지 중요한 개념을 보여 줍니다.

* 미들웨어는 `UseRouting` 전에 실행되어 라우팅 작동의 기반이 되는 데이터를 수정할 수 있습니다.
    * 일반적으로 라우팅 전에 표시되는 미들웨어는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>또는 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>와 같은 요청의 일부 속성을 수정합니다.
* 미들웨어는 `UseRouting`과 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 사이에서 실행되어 엔드포인트가 실행되기 전에 라우팅의 결과를 처리할 수 있습니다.
    * `UseRouting`과 `UseEndpoints` 사이에서 실행되는 미들웨어는 다음을 수행합니다.
      * 일반적으로 메타데이터를 검사하여 엔드포인트를 이해합니다.
      * `UseAuthorization` 및 `UseCors`에서 하는 것처럼 보안 결정을 내리는 경우가 많습니다.
    * 미들웨어와 메타데이터를 조합하면 엔드포인트별로 정책을 구성할 수 있습니다.

위의 코드에서는 엔드포인트별 정책을 지원하는 사용자 지정 미들웨어의 예를 보여 줍니다. 이 미들웨어는 중요한 데이터에 대한 액세스의 ‘감사 로그’를 콘솔에 기록합니다. `AuditPolicyAttribute` 메타데이터를 사용하여 엔드포인트를 ‘감사’하도록 미들웨어를 구성할 수 있습니다. 이 샘플에서는 중요함으로 표시된 엔드포인트만 감사되는 ‘옵트인 패턴’을 보여 줍니다. 예를 들어 이 논리를 역으로 정의하여 안전한 것으로 표시되지 않은 모든 항목을 감사할 수 있습니다. 엔드포인트 메타데이터 시스템은 유연합니다. 이 논리는 사용 사례에 적합한 방식으로 설계할 수 있습니다.

앞의 샘플 코드는 엔드포인트의 기본 개념을 보여 주기 위한 것입니다. **프로덕션 용도로는 사용하지 않아야 합니다** . ‘감사 로그’ 미들웨어의 전체 버전은 다음과 같습니다.

* 파일이나 데이터베이스에 기록합니다.
* 사용자, IP 주소, 중요한 엔드포인트의 이름 등과 같은 세부 정보를 포함합니다.

감사 정책 메타데이터 `AuditPolicyAttribute`는 컨트롤러 및 SignalR 같은 클래스 기반 프레임워크에서 더욱 쉽게 사용할 수 있도록 `Attribute`로 정의됩니다. ‘라우팅 대상 코드’를 사용하면 다음과 같이.

* 메타데이터가 작성기 API와 연결됩니다.
* 엔드포인트를 만들 때 해당 메서드 및 클래스의 모든 특성이 클래스 기반 프레임워크에 포함됩니다.

메타데이터 형식은 인터페이스나 특성으로 정의하는 것이 가장 좋습니다. 인터페이스 및 특성을 사용하면 코드를 다시 사용할 수 있습니다. 메타데이터 시스템은 유연하며 제한을 적용하지 않습니다.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>터미널 미들웨어와 라우팅 비교

다음 코드 샘플에서는 미들웨어 사용과 라우팅 사용을 비교합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

`Approach 1:`로 표시된 미들웨어 스타일은 **터미널 미들웨어** 입니다. 일치 작업을 수행하기 때문에 터미널 미들웨어라고 합니다.

* 위 샘플의 일치 작업은 미들웨어의 `Path == "/"`와 라우팅의 `Path == "/Movie"`입니다.
* 일치가 성공하면 일부 기능을 실행하고 `next` 미들웨어를 호출하는 대신 반환합니다.

검색을 종료하고 일부 기능을 실행한 다음 반환하기 때문에 터미널 미들웨어라고 합니다.

터미널 미들웨어와 라우팅을 비교하면 다음과 같습니다.
* 두 방법 모두 처리 파이프라인을 종료할 수 있습니다.
    * 미들웨어는 `next`를 호출하는 대신 반환하여 파이프라인을 종료합니다.
    * 엔드포인트는 항상 터미널입니다.
* 터미널 미들웨어를 사용하면 파이프라인에서 미들웨어를 임의로 위치 지정할 수 있습니다.
    * 엔드포인트는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>의 위치에서 실행됩니다.
* 터미널 미들웨어는 임의의 코드를 사용하여 미들웨어가 일치되는 시기를 지정할 수 있습니다.
    * 사용자 지정 경로 일치 코드는 길어져서 올바르게 작성하기 어려울 수 있습니다.
    * 라우팅은 일반적인 앱을 위한 간단한 솔루션을 제공합니다. 앱 대부분에는 사용자 지정 경로 일치 코드가 필요하지 않습니다.
* 엔드포인트는 `UseAuthorization` 및 `UseCors` 같은 미들웨어와 상호 작용합니다.
    * `UseAuthorization` 또는 `UseCors`와 함께 터미널 미들웨어를 사용하려면 권한 부여 시스템을 수동으로 조작해야 합니다.

[엔드포인트](#endpoint)는 다음 두 가지를 모두 정의합니다.

* 요청을 처리할 대리자
* 임의 메타데이터의 컬렉션. 메타데이터는 각 엔드포인트에 연결된 정책과 구성에 따라 횡단 관심사(Cross-Cutting Concerns)를 구현하는 데 사용됩니다.

터미널 미들웨어는 효과적인 도구이지만 다음이 필요할 수 있습니다.

* 상당한 양의 코딩과 테스트
* 원하는 수준의 유연성을 얻기 위한 다른 시스템과의 수동 통합

터미널 미들웨어를 작성하기 전에 라우팅과 통합하는 것이 좋습니다.

[또는](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>과 통합되는 기존 터미널 미들웨어는 일반적으로 라우팅 인식 엔드포인트로 전환될 수 있습니다. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16)는 다음과 같은 라우터 방식의 패턴을 보여 줍니다.
* <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>에 대한 확장 메서드를 작성합니다.
* <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>를 사용하여 중첩된 미들웨어 파이프라인을 만듭니다.
* 새 파이프라인에 미들웨어를 연결합니다. 이 예제의 경우 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>입니다.
* 미들웨어 파이프라인을 <xref:Microsoft.AspNetCore.Http.RequestDelegate>로 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>합니다.
* `Map`을 호출하고 새 미들웨어 파이프라인을 제공합니다.
* 확장 메서드의 `Map`에서 제공하는 작성기 개체를 반환합니다.

다음 코드에서는 [MapHealthChecks](xref:host-and-deploy/health-checks)를 사용하는 방법을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

앞의 샘플에서는 작성기 개체를 반환하는 것이 중요한 이유를 보여 줍니다. 작성기 개체를 반환하면 앱 개발자가 엔드포인트의 권한 부여와 같은 정책을 구성할 수 있습니다. 이 예제에서는 상태 검사 미들웨어가 권한 부여 시스템과 직접 통합되지 않습니다.

메타데이터 시스템은 터미널 미들웨어를 사용하는 확장성 작성자에서 발생한 문제에 대응하여 만들어졌습니다. 미들웨어마다 권한 부여 시스템과의 고유한 통합을 구현하는 것은 문제가 있습니다.

<a name="urlm"></a>

### <a name="url-matching"></a>URL 일치

* 라우팅이 들어오는 요청을 [엔드포인트](#endpoint)와 일치시키는 프로세스입니다.
* URL 경로 및 헤더의 데이터를 기반으로 합니다.
* 요청의 모든 데이터를 고려하도록 확장될 수 있습니다.

라우팅 미들웨어가 실행되면 `Endpoint`를 설정하여 현재 요청에서 <xref:Microsoft.AspNetCore.Http.HttpContext>의 [요청 기능](xref:fundamentals/request-features)으로 값을 라우팅합니다.

* [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>)를 호출하면 엔드포인트를 가져옵니다.
* `HttpRequest.RouteValues`는 경로 값의 컬렉션을 가져옵니다.

라우팅 미들웨어 뒤에 실행되는 [미들웨어](xref:fundamentals/middleware/index)는 엔드포인트를 검사하고 작업을 수행할 수 있습니다. 예를 들어 권한 부여 미들웨어는 엔드포인트의 메타데이터 컬렉션에서 권한 부여 정책을 조사할 수 있습니다. 요청 처리 파이프라인의 미들웨어가 모두 실행된 후에 선택한 엔드포인트의 대리자가 호출됩니다.

엔드포인트 라우팅의 라우팅 시스템은 모든 디스패치를 결정합니다. 미들웨어는 선택된 엔드포인트에 기반으로 하여 정책을 적용하므로 다음이 중요합니다.

* 디스패치나 보안 정책의 애플리케이션에 영향을 줄 수 있는 모든 결정은 라우팅 시스템 내에서 내려야 합니다.

> [!WARNING]
> 이전 버전과의 호환성을 위해 컨트롤러 또는 Razor Pages 엔드포인트 대리자가 실행되면 [RouteContext RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성이 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정됩니다.
>
> `RouteContext` 형식은 이후 릴리스에서 obsolete로 표시됩니다.
>
> * `RouteData.Values`를 `HttpRequest.RouteValues`로 마이그레이션합니다.
> * `RouteData.DataTokens`를 마이그레이션하여 엔드포인트 메타데이터에서 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata)를 검색합니다.

URL 일치는 구성 가능한 일련의 단계로 작동합니다. 각 단계의 출력은 일치 항목 집합입니다. 일치 항목 집합은 다음 단계에서 더욱 좁혀질 수 있습니다. 라우팅 구현에서는 일치하는 엔드포인트의 처리 순서를 보장하지 않습니다. 기능한 **모든** 일치 항목이 한 번에 처리됩니다. URL 일치 단계는 다음 순서로 수행됩니다. ASP.NET Core:

1. 엔드포인트와 해당 경로 템플릿 집합에 대한 URL 경로를 처리하여 일치 항목을 **모두** 수집합니다.
1. 앞의 목록을 사용하여 경로 제약 조건이 적용되지 않는 일치 항목을 제거합니다.
1. 앞의 목록을 사용하여 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 인스턴스 집합에서 실패하는 항목을 제거합니다.
1. [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector)를 사용하여 앞의 목록을 통해 최종 결정을 내립니다.

엔드포인트 목록의 우선 순위는 다음에 따라 지정됩니다.

* [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [경로 템플릿 우선 순위](#rtp)

<xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector>에 도달할 때까지 각 단계에서 일치하는 모든 엔드포인트가 처리됩니다. `EndpointSelector`는 최종 단계이며, 일치 항목에서 가장 높은 우선 순위 엔드포인트를 가장 일치하는 항목으로 선택합니다. 가장 일치하는 항목과 같은 우선 순위의 다른 일치 항목이 있으면 모호한 일치 예외가 throw됩니다.

경로 우선 순위는 **더 구체적인** 경로 템플릿에 높은 우선 순위가 지정되는 기준에 따라 컴퓨팅됩니다. 예를 들어 `/hello` 및 `/{message}` 템플릿을 가정해 보겠습니다.

* 둘 다 URL 경로 `/hello`와 일치합니다.
* `/hello`가 더 구체적이므로 우선 순위가 높습니다.

일반적으로 경로 우선 순위는 실제로 사용되는 URL 체계에 가장 일치하는 항목을 선택하는 데 좋습니다. 모호성을 방지하는 데 필요한 경우에만 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>를 사용합니다.

라우팅에서 제공하는 확장성의 종류 때문에 라우팅 시스템이 모호한 경로를 미리 컴퓨팅할 수는 없습니다. 경로 템플릿 `/{message:alpha}` 및 `/{message:int}`와 같은 예제를 살펴보겠습니다.

* `alpha` 제약 조건은 영문자와만 일치합니다.
* `int` 제약 조건은 숫자와만 일치합니다.
* 이러한 템플릿은 경로 우선 순위가 동일하지만 둘 다와 일치하는 단일 URL은 없습니다.
* 라우팅 시스템에서 시작 시 모호성 오류를 보고한 경우 이 유효한 사용 사례를 차단합니다.

> [!WARNING]
>
> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 내의 작업 순서는 라우팅 동작에 영향을 주지 않지만 한 가지 예외가 있습니다. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>는 호출된 순서를 기준으로 해당 엔드포인트에 순서 값을 자동으로 할당합니다. 이는 이전 라우팅 구현과 동일한 보장을 제공하는 라우팅 시스템이 없이 컨트롤러의 오래된 동작을 시뮬레이트합니다.
>
> 라우팅의 레거시 구현에서는 경로가 처리되는 순서에 종속된 라우팅 확장성을 구현할 수 있습니다. ASP.NET Core 3.0 이상의 엔드포인트 라우팅은 다음과 같습니다.
> 
> * 경로 개념이 없습니다.
> * 순서 지정을 보장하지 않습니다. 모든 엔드포인트가 한 번에 처리됩니다.

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>경로 템플릿 우선 순위 및 엔드포인트 선택 영역 순서

[경로 템플릿 우선 순위](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16)는 얼마나 구체적인지를 기준으로 각 경로 템플릿에 값을 할당하는 시스템입니다. 경로 템플릿 우선 순위의 특징은 다음과 같습니다.

* 일반적인 사례에서 엔드포인트 순서를 조정할 필요가 없게 합니다.
* 라우팅 동작에 관한 일반적인 기대에 맞추려고 합니다.

예를 들어 `/Products/List` 및 `/Products/{id}` 템플릿을 가정해 보겠습니다. URL 경로 `/Products/List`에 대해 `/Products/List`가 `/Products/{id}`보다 더 잘 일치한다고 합리적으로 가정할 수 있습니다. 이유는 리터럴 세그먼트 `/List`가 매개 변수 세그먼트 `/{id}`보다 더 우선 순위가 높다고 간주되기 때문입니다.

우선 순위의 작동 방식에 대한 세부 정보는 경로 템플릿이 정의된 방법과 어느 정도 관련이 있습니다.

* 세그먼트가 더 많은 템플릿은 더 구체적인 것으로 간주됩니다.
* 리터럴 텍스트가 있는 세그먼트가 매개 변수 세그먼트보다 더 구체적인 것으로 간주됩니다.
* 제약 조건이 있는 매개 변수 세그먼트가 제약 조건이 없는 매개 변수 세그먼트보다 더 구체적인 것으로 간주됩니다.
* 복잡한 세그먼트는 제약 조건이 있는 매개 변수 세그먼트만큼 구체적인 것으로 간주됩니다.
* Catch-all 매개 변수가 가장 덜 구체적입니다. Catch-all 경로에 관한 중요한 내용은 [경로 템플릿 참조](#rtr)에서 **catch-all** 을 참조하세요.

정확한 값 참조는 [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189)(GitHub의 소스 코드)을 참조하세요.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>URL 생성 개념

URL 생성은 다음과 같습니다.

* 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.
* 엔드포인트와 이에 액세스하는 URL을 논리적으로 분리할 수 있습니다.

엔드포인트 라우팅에는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API가 포함됩니다. `LinkGenerator`는 [DI](xref:fundamentals/dependency-injection)에서 사용할 수 있는 싱글톤 서비스입니다. `LinkGenerator` API는 실행 중인 요청의 컨텍스트 외부에서 사용할 수 있습니다. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) 및 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 사용하는 시나리오(예: [태그 도우미](xref:mvc/views/tag-helpers/intro), HTML 도우미 및 [작업 결과](xref:mvc/controllers/actions))는 내부적으로 `LinkGenerator` API를 사용하여 링크 생성 기능을 제공합니다.

링크 생성기는 **주소** 및 **주소 체계** 의 개념으로 지원됩니다. 주소 체계는 링크 생성을 위해 고려해야 할 엔드포인트를 결정하는 방법입니다. 예를 들어 컨트롤러 및 Razor Pages에서 많은 사용자에게 친숙한 경로 이름 및 경로 값 시나리오는 주소 체계로 구현됩니다.

링크 생성기는 다음 확장 메서드를 통해 컨트롤러 및 Razor Pages에 연결할 수 있습니다.

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

이러한 메서드의 오버로드에는 `HttpContext`를 포함한 인수가 허용됩니다. 이러한 메서드는 기능적으로 [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) 및 [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)와 동일하지만, 추가적인 유연성과 옵션을 제공합니다.

`GetPath*` 메서드는 절대 경로가 포함된 URI를 생성한다는 점에서 `Url.Action` 및 `Url.Page`와 가장 비슷합니다. `GetUri*` 메서드는 항상 체계와 호스트를 포함한 절대 URI를 생성합니다. `HttpContext`를 허용하는 메서드는 실행 중인 요청의 컨텍스트에서 URI를 생성합니다. 재정의되지 않는 한 실행 중인 요청의 [앰비언트](#ambient) 경로 값, URL 기본 경로, 체계 및 호스트가 사용됩니다.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 주소를 사용하여 호출됩니다. URI 생성은 다음 두 단계로 수행됩니다.

1. 주소는 해당 주소와 일치하는 엔드포인트 목록에 바인딩됩니다.
1. 제공된 값과 일치하는 경로 패턴을 찾을 때까지 각 엔드포인트의 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>이 평가됩니다. 결과 출력은 링크 생성기에 제공된 다른 URI 부분과 결합되어 반환됩니다.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>에서 제공하는 메서드는 모든 유형의 주소에 대해 표준 링크 생성 기능을 지원합니다. 링크 생성기를 사용하는 가장 편리한 방법은 특정 주소 유형에 대한 작업을 수행하는 확장 메서드를 사용하는 것입니다.

| 확장 메서드 | 설명 |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 제공된 값에 기반한 절대 경로의 URI를 생성합니다. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 제공된 값에 기반한 절대 URI를 생성합니다.             |

> [!WARNING]
> <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 메서드 호출 시 다음과 같은 의미에 주의하세요.
>
> * 들어오는 요청의 `GetUri*` 헤더의 유효성을 검사하지 않는 앱 구성에서는 `Host` 확장 메서드를 신중하게 사용하세요. 들어오는 요청의 `Host` 헤더의 유효성을 검사하지 않으면 신뢰할 수 없는 요청 입력이 보기 또는 페이지에 포함된 URI로 클라이언트에 다시 보내질 수 있습니다. 모든 프로덕션 앱은 알려진 유효한 값에 대해 `Host` 헤더의 유효성을 검사하도록 서버를 구성하는 것이 좋습니다.
>
> * 미들웨어에서 `MapWhen` 또는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>과 함께 `Map`를 사용할 때는 신중하게 사용하세요. `Map*`는 실행 중인 요청의 기본 경로를 변경하여 링크 생성의 출력에 영향을 줍니다. 모든 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API는 기본 경로를 지정할 수 있습니다. 링크 생성에 대한 `Map*`의 영향을 실행 취소하려면 빈 기본 경로를 지정합니다.

### <a name="middleware-example"></a>미들웨어 예제

다음 예제에서는 미들웨어에서 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 상점 제품을 나열하는 작업 메서드에 대한 링크를 만듭니다. 링크 생성기를 클래스에 주입하고 `GenerateLink`를 호출하여 앱의 모든 클래스에서 해당 링크 생성기를 사용할 수 있습니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>경로 템플릿 참조

`{}` 내의 토큰은 경로가 일치하는 경우 바인딩될 경로 매개 변수를 정의합니다. 경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 경로 매개 변수를 리터럴 값으로 분리해야 합니다. 예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다.  경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.

경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다. 텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다. 리터럴 경로 매개 변수 구분 기호 `{` 또는 `}`와 일치시키려면 문자를 반복하여(예: `{{` 또는 `}}`) 구분 기호를 이스케이프합니다.

별표 `*` 또는 이중 별표 `**`:

* URI의 나머지 부분에 바인딩하기 위해 경로 매개 변수의 접두사로 사용할 수 있습니다.
* **범용** 매개 변수라고 합니다. 예: `blog/{**slug}`
  * `/blog`로 시작하고 그 다음에 임의의 값이 오는 모든 URI를 찾습니다.
  * `/blog` 다음의 값은 [동적 필드](https://developer.mozilla.org/docs/Glossary/Slug) 경로 값에 할당됩니다.

[!INCLUDE[](~/includes/catchall.md)]

범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.

범용 매개 변수는 경로 구분 기호 `/` 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다. 예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다. 이스케이프된 슬래시에 주의하세요. 경로 구분 기호 문자를 왕복하려면 `**` 경로 매개 변수 접두사를 사용합니다. `{ path = "my/path" }`를 사용하는 경로 `foo/{**path}`는 `foo/my/path`를 생성합니다.

선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다. 예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다. `filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다. URL에 `filename`에 대한 값만 있으면 후행 `.`가 선택 사항이므로 경로가 일치합니다. 다음 URL은 이 경로와 일치합니다.

* `/files/myFile.txt`
* `/files/myFile`

경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 **기본값** 이 있을 수 있습니다. 예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다. URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다. 경로 매개 변수는 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다. 예: `id?`. 선택적 값과 기본 경로 매개 변수의 차이는 다음과 같습니다.

* 기본값이 있는 경로 매개 변수는 항상 값을 생성합니다.
* 선택적 매개 변수는 요청 URL에서 값을 제공한 경우에만 값이 있습니다.

경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다. 경로 매개 변수 이름 뒤에 `:`과 제약 조건 이름을 추가하여 경로 매개 변수에서 인라인 제약 조건을 지정합니다. 제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에서 괄호 `(...)`로 묶입니다. 또 다른 `:` 및 제약 조건 이름을 추가하여 여러 ‘인라인 제약 조건’을 지정할 수 있습니다.

제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다. 예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다. 경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.

경로 매개 변수에는 매개 변수 변환기가 있을 수도 있습니다. 매개 변수 변환기는 링크를 생성하고 URL에 대한 작업 및 페이지와 일치할 때 매개 변수 값을 변환합니다. 제약 조건과 마찬가지로, 매개 변수 변환기는 경로 매개 변수 이름 뒤에 `:`과 변환기 이름을 추가하여 경로 매개 변수에 인라인으로 추가될 수 있습니다. 예를 들어 경로 템플릿 `blog/{article:slugify}`는 `slugify` 변환기를 지정합니다. 매개 변수 변환기에 대한 자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.

다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.

| 경로 템플릿                           | URI 일치 예제    | 요청 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | `/hello` 단일 경로만 일치합니다.                                     |
| `{Page=Home}`                            | `/`                     | 일치하고, `Page`를 `Home`으로 설정합니다.                                         |
| `{Page=Home}`                            | `/Contact`              | 일치하고, `Page`를 `Contact`로 설정합니다.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다. `id`는 무시됩니다.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | `Products` 컨트롤러 및 `Index` 메서드에 매핑합니다. `id`는 무시됩니다.        |

템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다. 제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.

### <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트는 [non-greedy](#greedy) 방식으로 오른쪽에서 왼쪽으로 리터럴 구분 기호를 매칭하여 처리됩니다. 예를 들어 `[Route("/a{b}c{d}")]`는 복잡한 세그먼트입니다.
복잡한 세그먼트는 특정 방식으로 작동하므로 제대로 사용하려면 이 방식을 이해해야 합니다. 이 단원의 예제에서는 매개 변수 내에 구분 기호 텍스트가 표시되지 않는 경우에만 복잡한 세그먼트가 실제로 잘 작동하는 이유를 보여 줍니다. 더욱 복잡한 경우에는 [regex](/dotnet/standard/base-types/regular-expressions)를 사용한 다음 값을 수동으로 추출해야 합니다.

[!INCLUDE[](~/includes/regex.md)]

다음은 라우팅에서 `/a{b}c{d}` 템플릿 및 URL 경로 `/abcd`를 사용하여 수행하는 단계를 요약한 것입니다. `|`는 알고리즘의 작동 방식을 시각화하는 데 사용됩니다.

* 오른쪽에서 왼쪽으로 첫 번째 리터럴은 `c`입니다. 따라서 `/abcd`가 오른쪽에서 검색되고 `/ab|c|d`를 찾습니다.
* 이제 오른쪽의 모든 항목(`d`)이 경로 매개 변수 `{d}`와 일치합니다.
* 오른쪽에서 왼쪽으로 다음 리터럴은 `a`입니다. 따라서 `/ab|c|d`가 중단된 위치부터 검색되고 `a`를 `/|a|b|c|d`와 함께 찾습니다.
* 이제 오른쪽 값(`b`)이 경로 매개 변수 `{b}`와 일치합니다.
* 남은 텍스트가 없고 남은 경로 템플릿도 없으므로 이것이 일치 항목입니다.

다음은 동일한 템플릿 `/a{b}c{d}`와 URL 경로 `/aabcd`를 사용하는 부정적인 사례의 예제입니다. `|`는 알고리즘의 작동 방식을 시각화하는 데 사용됩니다. 이 경우는 동일한 알고리즘으로 설명되는 일치 항목이 아닙니다.
* 오른쪽에서 왼쪽으로 첫 번째 리터럴은 `c`입니다. 따라서 `/aabcd`가 오른쪽에서 검색되고 `/aab|c|d`를 찾습니다.
* 이제 오른쪽의 모든 항목(`d`)이 경로 매개 변수 `{d}`와 일치합니다.
* 오른쪽에서 왼쪽으로 다음 리터럴은 `a`입니다. 따라서 `/aab|c|d`가 중단된 위치부터 검색되고 `a`를 `/a|a|b|c|d`와 함께 찾습니다.
* 이제 오른쪽 값(`b`)이 경로 매개 변수 `{b}`와 일치합니다.
* 이제 남은 텍스트 `a`가 있지만 알고리즘에서 구문 분석할 경로 템플릿이 부족하므로 이것은 일치 항목이 아닙니다.

일치 알고리즘이 [non-greedy](#greedy)이므로 다음과 같습니다.

* 각 단계에서 가능한 최소의 텍스트와 일치합니다.
* 매개 변수 값 내에 구분 기호 값이 표시되는 모든 경우에는 일치하지 않게 됩니다.

정규식을 사용하면 일치 동작을 더 효율적으로 제어할 수 있습니다.

<a name="greedy"></a>

[지연 일치](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)라고도 하는 greedy 일치는 가능한 가장 큰 문자열과 일치합니다. Non-greedy는 가능한 가장 작은 문자열과 일치합니다.

## <a name="route-constraint-reference"></a>경로 제약 조건 참조

경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다. 일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 true 또는 false 결정을 내립니다. 일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다. 예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다. 제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.

> [!WARNING]
> 제약 조건을 입력 유효성 검사에 사용하지 마세요. 입력 유효성 검사에 제약 조건을 사용하면 잘못된 입력으로 인해 `404` 찾을 수 없음 응답이 반환됩니다. 입력이 잘못되면 `400` 잘못된 요청과 해당하는 오류 메시지가 생성됩니다. 경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 명확하게 구분하는 데 사용됩니다.

다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.

| 제약 조건 | 예제 | 일치하는 예제 | 참고 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치 |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` 또는 `false`와 일치. 대/소문자 구분하지 않음 |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 유효한 `DateTime` 값 일치(고정 문화권에서) 위의 경고를 참조하세요. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 유효한 `decimal` 값 일치(고정 문화권에서) 위의 경고를 참조하세요.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 유효한 `double` 값 일치(고정 문화권에서) 위의 경고를 참조하세요.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 유효한 `float` 값 일치(고정 문화권에서) 위의 경고를 참조하세요.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 유효한 `Guid` 값 일치 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 `long` 값 일치 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열은 4자 이상이어야 합니다. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 문자열은 8자 이하여야 합니다. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열은 정확히 12자여야 합니다. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열의 길이는 8자 이상이며 16자 이하여야 합니다. |
| `min(value)` | `{age:min(18)}` | `19` | 정수 값은 18 이상이어야 합니다. |
| `max(value)` | `{age:max(120)}` | `91` | 정수 값은 120 이하여야 합니다. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 정수 값은 18 이상이며 120 이하여야 합니다. |
| `alpha` | `{name:alpha}` | `Rick` | 문자열은 하나 이상의 영문자(`a`-`z`, 대/소문자 구분)로 구성되어야 합니다. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열은 정규식과 일치해야 합니다. 정규식을 정의하는 방법에 대한 팁을 참조하세요. |
| `required` | `{name:required}` | `Rick` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됨 |

[!INCLUDE[](~/includes/regex.md)]

콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다. 예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> CLR 형식으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다(예: CLR 형식 `int` 또는 `DateTime`으로 변환). 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다. 프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다. URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다. 예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.

### <a name="regular-expressions-in-constraints"></a>제약 조건의 정규식

[!INCLUDE[](~/includes/regex.md)]

`regex(...)` 경로 제약 조건을 사용하여 정규식을 인라인 제약 조건으로 지정할 수 있습니다. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 제품군의 메서드에서는 개체 리터럴의 제약 조건도 사용할 수 있습니다. 해당 양식을 사용하는 경우 문자열 값이 정규식으로 해석됩니다.

다음 코드에서는 인라인 regex 제약 조건을 사용합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

다음 코드에서는 개체 리터럴을 사용하여 regex 제약 조건을 지정합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다. 이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.

정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다. 정규식 토큰은 이스케이프되어야 합니다. 인라인 제약 조건에서 정규식 `^\d{3}-\d{2}-\d{4}$`를 사용하려면 다음 중 하나를 사용합니다.

* `\` 문자열 이스케이프 문자를 이스케이프하기 위해 C# 소스 파일에서 문자열에 제공된 `\` 문자를 `\\` 문자로 바꿉니다.
* [축자 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string).

라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(예: `{{`, `}}`, `[[`, `]]`). 다음 표에서는 정규식 및 이스케이프된 버전을 보여 줍니다.

| 정규식    | 이스케이프된 정규식     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

라우팅에 사용되는 정규식은 `^` 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다. 식은 `$` 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다. `^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다. `^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하지만, 이는 종종 원하는 것이 아닙니다. 다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.

| 식   | String    | 일치 | 주석               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | mz        | 예   | 식 일치    |
| `[a-z]{2}`   | MZ        | 예   | 대/소문자 구분하지 않음    |
| `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$` 참조 |
| `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$` 참조 |

정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.

가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다. 예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다. 제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다. 알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전에서 전달되는 제약 조건도 정규식으로 처리됩니다. 알려진 제약 조건 중 하나와 일치하지 않는 템플릿 내에서 전달되는 제약 조건은 정규식으로 처리되지 않습니다.

### <a name="custom-route-constraints"></a>사용자 지정 경로 제약 조건

<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다. `IRouteConstraint` 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 <xref:System.Web.Routing.IRouteConstraint.Match*>가 포함됩니다.

사용자 지정 경로 제약 조건은 거의 필요하지 않습니다. 사용자 지정 경로 제약 조건을 구현하기 전에 모델 바인딩과 같은 다른 방식을 고려해 보세요.

ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 폴더는 제약 조건을 만드는 좋은 예제를 제공합니다. 예를 들어 [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)입니다.

사용자 지정 `IRouteConstraint`를 사용하려면 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다. `ConstraintMap`은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 `IRouteConstraint` 구현과 매핑하는 사전입니다. `Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 `ConstraintMap`을 수정할 수 있습니다. 예를 들어:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

위의 제약 조건은 다음 코드에서 적용됩니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

`MyCustomConstraint`를 구현하면 경로 매개 변수에 `0`이 적용되지 않습니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

위의 코드는

* 경로의 `{id}` 세그먼트에 `0`을 사용하지 못하게 합니다.
* 사용자 지정 제약 조건을 구현하는 기본 예제를 제공하기 위해 표시됩니다. 프로덕션 앱에 사용해서는 안 됩니다.

다음 코드는 `0`이 포함된 `id`가 처리되지 않게 하는 더 나은 방법입니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

위의 코드는 `MyCustomConstraint` 접근 방식과 비교하면 다음과 같은 이점이 있습니다.

* 사용자 지정 제약 조건이 필요하지 않습니다.
* 경로 매개 변수에 `0`이 포함된 경우 더 자세한 설명이 포함된 오류를 반환합니다.

## <a name="parameter-transformer-reference"></a>매개 변수 변환기 참조

매개 변수 변환기는:

* <xref:Microsoft.AspNetCore.Routing.LinkGenerator>를 사용하여 링크를 생성할 때 실행됩니다.
* <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>를 구현해야 합니다.
* <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성됩니다.
* 매개 변수의 경로 값을 가져와서 새 문자열 값으로 변환합니다.
* 생성된 링크에서 변환된 값을 사용합니다.

예를 들어, `Url.Action(new { article = "MyTestArticle" })`을 사용하는 경로 패턴 `blog\{article:slugify}`의 사용자 지정 `slugify` 매개 변수 변환기는 `blog\my-test-article`을 생성합니다.

다음 `IOutboundParameterTransformer` 구현을 생각해 보겠습니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

경로 패턴에서 매개 변수 변환기를 사용하려면 `Startup.ConfigureServices`의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core Framework는 매개 변수 변화기를 사용하여 엔드포인트가 해결되는 URI를 변환합니다. 예를 들어 매개 변수 변환기는 `area`, `controller`, `action` 및 `page`와 일치하도록 사용되는 경로 값을 변환합니다.

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

위의 경로 템플릿을 사용하면 `SubscriptionManagementController.GetAll` 작업이 URI `/subscription-management/get-all`과 일치합니다. 매개 변수 변환기는 링크를 생성하는 데 사용되는 경로 값을 변경하지 않습니다. 예를 들어 `Url.Action("GetAll", "SubscriptionManagement")`는 `/subscription-management/get-all`을 출력합니다.

ASP.NET Core는 생성된 경로와 함께 매개 변수 변환기를 사용하기 위한 API 규칙을 제공합니다.

* 이 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC 규칙은 앱의 모든 특성 경로에 지정된 매개 변수 변환기를 적용합니다. 매개 변수 변환기는 특성 경로 토큰이 교체될 때 변환합니다. 자세한 내용은 [매개 변수 변환기를 사용하여 토큰 교체 사용자 지정](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)을 참조하세요.
* Razor Pages에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 규칙을 사용합니다. 이 규칙은 자동으로 검색된 모든 Razor Pages에 지정된 매개 변수 변환기를 적용합니다. 매개 변수 변환기는 Razor Pages 경로의 폴더와 파일 이름 부분을 변환합니다. 자세한 내용은 [매개 변수 변환기를 사용하여 페이지 경로 사용자 지정](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)을 참조하세요.

<a name="ugr"></a>

## <a name="url-generation-reference"></a>URL 생성 참조

이 단원에는 URL 생성에서 구현하는 알고리즘에 대한 참조가 포함되어 있습니다. 실제로 URL 생성의 가장 복잡한 예제에서는 컨트롤러나 Razor Pages를 사용합니다. 자세한 내용은 [routing in controllers](xref:mvc/controllers/routing)(컨트롤러의 라우팅)를 참조하세요.

URL 생성 프로세스는 [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 또는 이와 유사한 메서드를 호출하여 시작합니다. 메서드에는 주소, 경로 값 집합 및 `HttpContext`의 현재 요청에 관한 정보(선택 사항)를 제공합니다.

첫 번째 단계에서는 주소를 사용하여 주소의 형식과 일치하는 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)을 사용하는 후보 엔드포인트 집합을 확인합니다.

주소 체계에 따라 후보 집합을 찾으면 URL 생성 작업이 성공할 때까지 엔드포인트가 순서 지정되고 반복적으로 처리됩니다. URL 생성에서는 모호성을 확인하지 **않으며** , 반환되는 첫 번째 결과가 최종 결과입니다.

### <a name="troubleshooting-url-generation-with-logging"></a>로깅을 사용하여 URL 생성 문제 해결

URL 생성 문제를 해결하는 첫 번째 단계는 `Microsoft.AspNetCore.Routing`의 로깅 수준을 `TRACE`로 설정하는 것입니다. `LinkGenerator`는 문제 해결에 유용할 수 있는 처리에 관한 여러 세부 정보를 기록합니다.

URL 생성에 대한 자세한 내용은 [URL 생성 참조](#ugr)를 참조하세요.

### <a name="addresses"></a>주소

주소는 URL 생성에서 링크 생성기의 호출을 후보 엔드포인트 집합에 바인딩하는 데 사용되는 개념입니다.

주소는 기본적으로 다음 두 가지 구현이 함께 제공되는 확장 가능한 개념입니다.

* ‘엔드포인트 이름’(`string`)을 주소로 사용:
    * MVC의 경로 이름과 유사한 기능을 제공합니다.
    * <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 메타데이터 형식을 사용합니다.
    * 등록된 모든 엔드포인트의 메타데이터와 제공된 문자열을 비교하여 확인합니다.
    * 여러 엔드포인트에서 같은 이름을 사용하는 경우 시작 시 예외를 throw합니다.
    * 컨트롤러 및 Razor Pages 외의 일반적인 용도에 사용하는 것이 좋습니다.
* ‘경로 값’(<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>)을 주소로 사용:
    * 컨트롤러 및 Razor Pages 레거시 URL 생성과 비슷한 기능을 제공합니다.
    * 확장 및 디버그하기가 매우 복잡합니다.
    * `IUrlHelper`, 태그 도우미, HTML 도우미, 작업 결과 등에서 사용하는 구현을 제공합니다.

주소 체계의 역할은 다음과 같은 임의 조건에 따라 주소와 일치하는 엔드포인트 사이를 연결하는 것입니다.

* 엔드포인트 이름 체계에서 기본 사전 조회를 수행합니다.
* 경로 값 체계에는 알고리즘의 복잡한 최적 하위 집합이 있습니다.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>앰비언트 값 및 명시적 값

현재 요청에서 라우팅은 현재 요청의 경로 값 `HttpContext.Request.RouteValues`에 액세스합니다. 현재 요청과 연결된 값을 **앰비언트 값** 이라고 합니다. 명확하게 하도록 설명서에서는 메서드에 전달된 경로 값을 **명시적 값** 이라고 합니다.

다음 예제에서는 앰비언트 값과 명시적 값을 보여 줍니다. 현재 요청의 앰비언트 값과 명시적 값 `{ id = 17, }`을 제공합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

위의 코드는

* `/Widget/Index/17`를 반환합니다.
* [DI](xref:fundamentals/dependency-injection)를 통해 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>를 가져옵니다.

다음 코드는 앰비언트 값과 `{ controller = "Home", action = "Subscribe", id = 17, }`을 제공하지 않습니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

앞의 메서드는 `/Home/Subscribe/17`을 반환합니다.

`WidgetController`의 다음 코드는 `/Widget/Subscribe/17`을 반환합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

다음 코드는 현재 요청의 앰비언트 값과 명시적 값 `{ action = "Edit", id = 17, }`을 컨트롤러에 제공합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

위의 코드에서

* `/Gadget/Edit/17`이 반환됩니다.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>는 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 가져옵니다.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
작업 메서드의 절대 경로가 포함된 URL을 생성합니다. URL에는 지정된 `action` 이름과 `route` 값이 포함됩니다.

다음 코드에서는 현재 요청의 앰비언트 값과 명시적 값 `{ page = "./Edit, id = 17, }`을 제공합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

앞의 코드에서는 Razor 편집 페이지에 다음 페이지 지시문이 포함된 경우 `url`을 `/Edit/17`로 설정합니다.

 `@page "{id:int}"`

편집 페이지에 `"{id:int}"` 경로 템플릿이 포함되어 있지 않으면 `url`은 `/Edit?id=17`입니다.

MVC의 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 동작은 여기에 설명된 규칙 외에도 복잡성 계층을 추가합니다.

* `IUrlHelper`는 항상 현재 요청의 경로 값을 앰비언트 값으로 제공합니다.
* [IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*)은 개발자가 재정의하는 경우 외에는 항상 현재 `action` 및 `controller` 경로 값을 명시적 값으로 복사합니다.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)는 재정의되는 경우 외에는 항상 현재 `page` 경로 값을 명시적 값으로 복사합니다. <!--by the user-->
* `IUrlHelper.Page`는 재정의되는 경우 외에는 항상 현재 `handler` 경로 값을 명시적 값 `null`로 재정의합니다.

MVC가 자체 규칙을 따르지 않기 때문에 앰비언트 값의 동작 세부 정보에 사용자가 놀라는 경우가 많습니다. 기록 및 호환성을 위해 `action`, `controller`, `page`, `handler` 등의 특정 경로 값에는 고유한 특수 사례 동작이 있습니다.

`LinkGenerator.GetPathByAction` 및 `LinkGenerator.GetPathByPage`에서 제공하는 동일한 기능에서는 호환성을 위해 `IUrlHelper`의 이러한 변칙을 복제합니다.

### <a name="url-generation-process"></a>URL 생성 프로세스

후보 엔드포인트 집합을 찾으면 URL 생성 알고리즘은 다음을 수행합니다.

* 엔드포인트를 반복적으로 처리합니다.
* 첫 번째 성공적인 결과를 반환합니다.

이 프로세스의 첫 번째 단계를 **경로 값 무효화** 라고 합니다.  경로 값 무효화는 라우팅에서 앰비언트 값의 어떤 경로 값을 사용하고 무시할지 결정하는 프로세스입니다. 각 앰비언트 값이 고려되고 명시적 값과 결합되거나 무시됩니다.

앰비언트 값의 역할을 알아보려면 일반적인 사례에서 애플리케이션 개발자 입력을 저장하려고 하는 점을 고려하면 가장 좋습니다. 일반적으로 앰비언트 값이 유용한 시나리오는 MVC와 관련이 있습니다.

* 동일한 컨트롤러의 다른 작업에 연결하는 경우에는 컨트롤러 이름을 지정할 필요가 없습니다.
* 같은 영역의 다른 컨트롤러에 연결하는 경우 영역 이름을 지정할 필요가 없습니다.
* 동일한 작업 메서드에 연결하는 경우 경로 값을 지정할 필요가 없습니다.
* 앱의 다른 부분에 연결하는 경우 앱의 해당 부분에서 의미가 없는 경로 값을 전달하지 않을 수 있습니다.

`null`을 반환하는 `LinkGenerator` 또는 `IUrlHelper`를 호출하면 일반적으로 이해되지 않는 경로 값 무효화가 발생합니다. 경로 값 무효화 문제를 해결하려면 더 많은 경로 값을 명시적으로 지정하여 문제가 해결되는지 확인합니다.

경로 값 무효화는 앱의 URL 체계가 계층이 왼쪽에서 오른쪽으로 형성된 계층 구조라고 가정하고 작동합니다. 기본 컨트롤러 경로 템플릿 `{controller}/{action}/{id?}`를 사용하여 실제 작동 방법을 직관적으로 파악해 보겠습니다. 값을 **변경** 하면 오른쪽에 표시되는 경로 값이 모두 **무효화** 됩니다. 이는 계층 구조에 관한 가정이 반영된 것입니다. 앱에 `id`의 앰비언트 값이 있고 작업에서 `controller`에 대해 다른 값을 지정하는 경우:

* `{controller}`가 `{id?}`의 왼쪽에 있으므로 `id`가 다시 사용되지 않습니다.

이 원칙을 보여 주는 몇 가지 예는 다음과 같습니다.

* 명시적 값에 `id`의 값이 포함된 경우 `id`의 앰비언트 값은 무시됩니다. `controller` 및 `action`의 앰비언트 값이 사용될 수 있습니다.
* 명시적 값에 `action`의 값이 포함된 경우 `action`의 앰비언트 값은 무시됩니다. `controller`의 앰비언트 값이 사용될 수 있습니다. `action`의 명시적 값이 `action`의 앰비언트 값과 다른 경우 `id` 값은 사용되지 않습니다.  `action`의 명시적 값이 `action`의 앰비언트 값과 같으면 `id` 값이 사용될 수 있습니다.
* 명시적 값에 `controller`의 값이 포함된 경우 `controller`의 앰비언트 값은 무시됩니다. `controller`의 명시적 값이 `controller`의 앰비언트 값과 다른 경우 `action` 및 `id` 값은 사용되지 않습니다. `controller`의 명시적 값이 `controller`의 앰비언트 값과 같으면 `action` 및 `id` 값이 사용될 수 있습니다.

이 프로세스는 특성 경로와 전용 규칙 기반 경로가 있으면 더 복잡해집니다. `{controller}/{action}/{id?}`와 같은 컨트롤러 규칙 기반 경로는 경로 매개 변수를 사용하여 계층 구조를 지정합니다. 컨트롤러 및 Razor Pages에 대한 [전용 규칙 기반 경로](xref:mvc/controllers/routing#dcr) 및 [특성 경로](xref:mvc/controllers/routing#ar)의 경우:

* 경로 값의 계층 구조가 있습니다.
* 템플릿에는 표시되지 않습니다.

이러한 경우 URL 생성에서 **필수 값** 개념을 정의합니다. 컨트롤러 및 Razor Pages에서 만든 엔드포인트에는 경로 값 무효화가 작동할 수 있도록 필수 값이 지정되어 있습니다.

경로 값 무효화 알고리즘을 자세히 설명하면 다음과 같습니다.

* 필요 값 이름을 경로 매개 변수와 결합한 다음 왼쪽에서 오른쪽으로 처리합니다.
* 각 매개 변수에 대해 앰비언트 값과 명시적 값을 비교합니다.
    * 앰비언트 값과 명시적 값이 같으면 프로세스가 계속됩니다.
    * 앰비언트 값이 있고 명시적 값이 없으면 URL을 생성할 때 앰비언트 값이 사용됩니다.
    * 앰비언트 값이 없고 명시적 값이 있으면 앰비언트 값과 모든 후속 앰비언트 값을 거부합니다.
    * 앰비언트 값과 명시적 값이 있고 두 값이 다른 경우 앰비언트 값과 모든 후속 앰비언트 값을 거부합니다.

이제 URL 생성 작업에서 경로 제약 조건을 평가할 준비가 되었습니다. 허용되는 값 집합이 제약 조건에 제공되는 매개 변수 기본값과 결합됩니다. 제약 조건이 모두 통과되면 작업이 계속됩니다.

다음으로 **허용되는 값** 을 사용하여 경로 템플릿을 확장할 수 있습니다. 경로 템플릿은 다음과 같이 처리됩니다.

* 왼쪽에서 오른쪽으로
* 각 매개 변수의 허용되는 값이 대체됩니다.
* 다음과 같은 특수 사례의 경우:
  * 허용되는 값에 누락된 값이 있고 매개 변수에 기본값이 있으면 기본값이 사용됩니다.
  * 허용되는 값에 누락된 값이 있고 매개 변수가 선택 사항이면 처리가 계속됩니다.
  * 누락된 선택적 매개 변수 오른쪽의 경로 매개 변수에 값이 있으면 작업이 실패합니다.
  * <!-- review default-valued parameters optional parameters --> 연속된 기본값 매개 변수 및 선택적 매개 변수는 가능하면 축소됩니다.

명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다. 다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.

| 앰비언트 값                     | 명시적 값                        | 결과                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>경로 값 무효화 문제

ASP.NET Core 3.0부터 이전 ASP.NET Core 버전에서 사용되는 일부 URL 생성 체계가 URL 생성에서 잘 작동하지 않습니다. ASP.NET Core 팀은 향후 릴리스에서 이러한 요구를 해결하는 기능을 추가할 계획입니다. 지금은 레거시 라우팅을 사용하는 것이 가장 좋습니다.

다음 코드에서는 라우팅에서 지원하지 않는 URL 생성 체계의 예제를 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

위의 코드에서 `culture` 경로 매개 변수는 지역화에 사용됩니다. `culture` 매개 변수가 앰비언트 값으로 항상 허용되게 하려는 것이 목표입니다. 그러나 `culture` 매개 변수는 필수 값이 작동하는 방식 때문에 앰비언트 값으로 허용되지 않습니다.

* `"default"` 경로 템플릿에서 `culture` 경로 매개 변수는 `controller`의 왼쪽에 있으므로 `controller`를 변경해도 `culture`가 무효화되지 않습니다.
* `"blog"` 경로 템플릿에서 `culture` 경로 매개 변수는 필수 값에 표시되는 `controller`의 오른쪽에 있는 것으로 간주됩니다.

## <a name="configuring-endpoint-metadata"></a>엔드포인트 메타데이터 구성

다음 링크는 엔드포인트 메타데이터를 구성하는 방법에 대한 정보를 제공합니다.

* [엔드포인트 라우팅을 사용하여 Cors 사용](xref:security/cors#enable-cors-with-endpoint-routing)
* 사용자 지정 `[MinimumAgeAuthorize]` 특성을 사용하는 [IAuthorizationPolicyProvider 샘플](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [[권한 부여] 특성으로 인증 테스트](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [[권한 부여] 특성을 갖는 체계 선택](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [[권한 부여] 특성을 사용하여 정책 적용](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>RequireHost가 있는 경로의 호스트 일치

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>는 지정된 호스트가 필요한 경로에 제약 조건을 적용합니다. `RequireHost` 또는 [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) 매개 변수는 다음과 같을 수 있습니다.

* 호스트: `www.domain.com`(아무 포트에서나 `www.domain.com`과 일치)
* 와일드카드가 있는 호스트: `*.domain.com`(아무 포트에서나 `www.domain.com`, `subdomain.domain.com` 또는 `www.subdomain.domain.com`과 일치)
* 포트: `*:5000`(아무 호스트에서나 포트 5000과 일치)
* 호스트 및 포트: `www.domain.com:5000` 또는 `*.domain.com:5000`(호스트 및 포트와 일치)

`RequireHost` 또는 `[Host]`를 사용하여 여러 매개 변수를 지정할 수 있습니다. 제약 조건은 모든 매개 변수에 유효한 호스트와 일치합니다. 예를 들어 `[Host("domain.com", "*.domain.com")]`은 `domain.com`, `www.domain.com` 및 `subdomain.domain.com`과 일치합니다.

다음 코드는 `RequireHost`를 사용하여 경로상에 있는 지정된 호스트를 요구합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

다음 코드는 컨트롤러의 `[Host]` 특성을 사용하여 지정된 호스트를 요구합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

`[Host]` 특성이 컨트롤러 메서드와 작업 메서드에 모두 적용될 경우

* 작업의 특성이 사용됩니다.
* 컨트롤러의 특성은 무시됩니다.

## <a name="performance-guidance-for-routing"></a>라우팅의 성능 지침

ASP.NET Core 3.0에서는 성능 향상을 위해 대부분의 라우팅이 업데이트되었습니다.

앱에 성능 문제가 있는 경우 라우팅이 문제의 원인으로 의심받는 경우가 많습니다. 라우팅이 의심받는 이유는 컨트롤러 및 Razor Pages 같은 프레임워크가 프레임워크 내에서 소요된 시간을 로깅 메시지로 보고하기 때문입니다. 컨트롤러에서 보고하는 시간과 요청의 총 시간 사이에 상당한 차이가 있는 경우:

* 개발자는 문제의 원인인 앱 코드를 제거합니다.
* 일반적으로 라우팅이 원인이라고 가정합니다.

라우팅은 수천 개의 엔드포인트를 사용하여 성능을 테스트했습니다. 일반적인 앱에서는 너무 크다고 성능 문제가 발생할 가능성은 거의 없습니다. 라우팅 성능이 저하되는 가장 일반적인 근본 원인은 일반적으로 잘못 동작하는 사용자 지정 미들웨어입니다.

다음 코드 샘플에서는 지연의 원인을 좁히기 위한 기본 기술을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

종료 시간 라우팅:

* 앞의 코드에 표시된 타이밍 미들웨어의 복사본을 각 미들웨어에 인터리빙합니다.
* 고유 식별자를 추가하여 타이밍 데이터를 코드와 연관 짓습니다.

이것이 예를 들어 `10ms` 이상의 상당한 지연이 발생하는 경우 지연을 좁히는 기본적인 방법입니다.  `Time 1`에서 `Time 2`를 빼면 `UseRouting` 미들웨어 내에서 소요된 시간이 보고됩니다.

다음 코드에서는 앞의 타이밍 코드에 더욱 간결한 방법을 사용합니다.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>비용이 많이 들 수 있는 라우팅 기능

다음 목록에서는 기본 경로 템플릿보다 비교적 비용이 많이 드는 라우팅 기능에 대한 몇 가지 유용한 정보를 제공합니다.

* 정규식: 정규식 작성은 복잡하거나 적은 양의 입력으로 실행 시간이 오래 걸릴 수 있습니다.

* 복잡한 세그먼트(`{x}-{y}-{z}`): 
  * 일반 URL 경로 세그먼트를 구문 분석하는 것보다 훨씬 비용이 많이 듭니다.
  * 더 많은 부분 문자열이 할당됩니다.
  * 복잡한 세그먼트 논리는 ASP.NET Core 3.0 라우팅 성능 업데이트에서 업데이트되지 않았습니다.

* 동기 데이터 액세스: 많은 복잡한 앱에서 라우팅의 일부로 데이터베이스에 액세스합니다. ASP.NET Core 2.2 및 이전 라우팅에서는 데이터베이스 액세스 라우팅을 지원하기 위한 적합한 확장 포인트를 제공할 수 없습니다. 예를 들어 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 및 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>는 동기식입니다. <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> 및 <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> 같은 확장 포인트는 비동기식입니다.

## <a name="guidance-for-library-authors"></a>라이브러리 작성자를 위한 지침

이 단원에는 라우팅을 기반으로 빌드하는 라이브러리 작성자를 위한 지침이 포함되어 있습니다. 이러한 세부 내용은 앱 개발자가 라우팅을 확장하는 라이브러리 및 프레임워크를 사용하는 좋은 환경을 갖추도록 하기 위한 것입니다.

### <a name="define-endpoints"></a>엔드포인트 정의

URL 일치를 위해 라우팅을 사용하는 프레임워크를 만들려면 먼저 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>를 기반으로 빌드되는 사용자 환경을 정의합니다.

<xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>를 기반으로 **빌드하세요** . 이렇게 하면 사용자는 혼동하지 않고 다른 ASP.NET Core 기능을 사용하여 프레임워크를 작성할 수 있습니다. 모든 ASP.NET Core 템플릿에는 라우팅이 포함됩니다. 라우팅이 있고 사용자에게 친숙하다고 간주합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>를 구현하는 `MapMyFramework(...)` 호출에서 봉인된 구체적인 형식을 반환 **하세요** . 대부분의 프레임워크 `Map...` 메서드는 이 패턴을 따릅니다. `IEndpointConventionBuilder` 인터페이스:

* 메타데이터를 작성 가능하게 합니다.
* 다양한 확장 메서드의 대상으로 지정됩니다.

고유의 형식을 선언하면 작성기에 사용자 고유의 프레임워크 관련 기능을 추가할 수 있습니다. 프레임워크 선언된 작성기를 래핑하고 여기에 호출을 전달해도 됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

사용자 고유의 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>를 작성하는 것이 **좋습니다** . `EndpointDataSource`는 엔드포인트 컬렉션을 선언하고 업데이트하기 위한 하위 수준 기본 형식입니다. `EndpointDataSource`는 컨트롤러 및 Razor Pages에서 사용되는 강력한 API입니다.

라우팅 테스트에는 업데이트되지 않는 데이터 원본의 [기본 예제](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17)가 있습니다.

기본적으로 `EndpointDataSource`를 등록하지 **마세요** . 프레임워크를 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>에 등록하도록 사용자에게 요구하세요. 라우팅의 원리에 따르면 기본적으로 아무것도 포함되지 않으며 `UseEndpoints`가 엔드포인트를 등록하는 위치입니다.

### <a name="creating-routing-integrated-middleware"></a>라우팅 통합 미들웨어 만들기

메타데이터 형식을 인터페이스로 정의하는 것이 **좋습니다** .

메타데이터 형식을 클래스 및 메서드의 특성으로 사용할 수 있게 **하세요** .

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

컨트롤러 및 Razor Pages와 같은 프레임워크는 형식 및 메서드에 메타데이터 특성을 적용하도록 지원합니다. 메타데이터 형식을 선언하는 경우:

* 이 형식에 [특성](/dotnet/csharp/programming-guide/concepts/attributes/)으로 액세스할 수 있습니다.
* 사용자 대부분이 특성을 적용하는 데 익숙합니다.

메타데이터 형식을 인터페이스로 선언하면 또 하나의 유연성 계층이 추가됩니다.

* 인터페이스는 구성할 수 있습니다.
* 개발자가 여러 정책을 결합하여 고유한 형식을 선언할 수 있습니다.

다음 예제와 같이 메타데이터를 재정의할 수 있게 **하세요** .

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

이러한 지침을 따르는 가장 좋은 방법은 **마커 메타데이터** 를 정의하지 않는 것입니다.

* 메타데이터 형식이 있는지만 확인하면 안 됩니다.
* 메타데이터의 속성을 정의하고 속성을 확인합니다.

메타데이터 컬렉션은 순서 지정되며 우선 순위별로 재정의할 수 있습니다. 컨트롤러의 경우 작업 메서드의 메타데이터가 가장 구체적입니다.

라우팅이 있는지와 관계없이 미들웨어를 유용하게 사용할 수 있게 **하세요** .

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

이 지침의 예제에서는 `UseAuthorization` 미들웨어를 고려해 보겠습니다. 권한 부여 미들웨어를 사용하면 대체 정책을 전달할 수 있습니다. <!-- shown where?  (shown here) --> 대체 정책(지정된 경우)은 다음 모두에 적용됩니다.

* 지정된 정책이 없는 엔드포인트
* 엔드포인트와 일치하지 않는 요청

따라서 권한 부여 미들웨어는 라우팅 컨텍스트 외에서도 유용합니다. 권한 부여 미들웨어는 기존 미들웨어 프로그래밍에 사용할 수 있습니다.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

라우팅은 요청 URI를 엔드포인트에 매핑하고 들어오는 요청을 이러한 엔드포인트로 디스패치합니다. 경로는 앱에서 정의되고 앱 시작 시 구성됩니다. 경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다. 또한 라우팅은 앱의 경로 정보를 사용하여 엔드포인트에 매핑되는 URL을 생성할 수도 있습니다.

ASP.NET Core 2.2에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 옵션은 라우팅에서 내부적으로 엔드포인트 기반 논리를 사용해야 하는지, 또는 ASP.NET Core 2.1 이하의 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 논리를 사용해야 하는지 여부를 결정합니다. 호환성 버전이 2.2 이상으로 설정된 경우 기본값은 `true`입니다. 이전 라우팅 논리를 사용하려면 값을 `false`로 설정합니다.

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.1 버전](?view=aspnetcore-2.1)을 참조하세요.

> [!IMPORTANT]
> 이 문서에서는 낮은 수준의 ASP.NET Core 라우팅을 설명합니다. ASP.NET Core MVC 라우팅에 대한 내용은 <xref:mvc/controllers/routing>을 참조하세요. Razor Pages의 라우팅 규칙에 대한 내용은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>라우팅 기본 사항

대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다. 기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:

* 기본적이고 서술적인 라우팅 체계를 지원합니다.
* UI 기반 앱에 대한 유용한 시작점입니다.

일반적으로 개발자는 [특성 라우팅](xref:mvc/controllers/routing#attribute-routing) 또는 전용 기존 경로를 사용하여 특수한 상황에서 앱의 트래픽이 높은 영역에 간결한 추가 경로를 추가합니다. 특수한 상황 예제로는 블로그 및 전자 상거래 엔드포인트가 있습니다.

Web API는 특성 라우팅을 사용하여 작업이 HTTP 동사로 표현되는 리소스 집합으로 앱의 기능을 모델링해야 합니다. 이는 동일한 논리 리소스의 많은 작업(예: GET, POST)이 동일한 URL을 사용한다는 뜻입니다. 특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.

Razor Pages 앱은 기본 기존 라우팅을 사용하여 앱의 *Pages* 폴더에서 명명된 리소스를 제공합니다. Razor Pages 라우팅 동작을 사용자 지정할 수 있는 추가 규칙을 사용할 수 있습니다. 자세한 내용은 <xref:razor-pages/index> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.

URL 생성 지원을 사용하면 URL을 하드 코딩하지 않고 앱을 개발하여 앱을 서로 연결할 수 있습니다. 이 지원을 사용하면 기본 라우팅 구성으로 시작하고 앱의 리소스 레이아웃이 결정된 후에 해당 경로를 수정할 수 있습니다.

라우팅은 *엔드포인트* (`Endpoint`)를 사용하여 앱에서 논리적 엔드포인트를 나타냅니다.

엔드포인트는 요청을 처리할 대리자와 임의의 메타데이터 컬렉션을 정의합니다. 메타데이터는 각 엔드포인트에 연결된 정책과 구성에 따라 횡단 관심사(Cross-Cutting Concerns)를 구현하는 데 사용됩니다.

라우팅 시스템에는 다음과 같은 특징이 있습니다.

* 경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.
* 기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.
* MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 엔드포인트를 등록합니다.
* 라우팅 구현은 미들웨어 파이프라인에서 필요한 곳이라면 어디서든지 라우팅 결정을 내립니다.
* 라우팅 미들웨어 이후에 나타나는 미들웨어는 지정된 요청 URI에 대한 라우팅 미들웨어의 엔드포인트 결정 결과를 검사할 수 있습니다.
* 미들웨어 파이프라인의 어느 곳에서든 앱의 모든 엔드포인트를 열거할 수 있습니다.
* 앱은 라우팅을 사용하여 엔드포인트 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.
* URL 생성은 임의의 확장성을 지원하는 주소를 기반으로 합니다.

  * 링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 URL을 생성하기 위해 어디서나 확인할 수 있습니다.
  * DI를 통해 링크 생성기 API를 사용할 수 없는 경우 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>에서 URL을 작성하는 메서드를 제공합니다.

> [!NOTE]
> ASP.NET Core 2.2의 엔드포인트 라우팅이 릴리스되면서 엔드포인트 연결이 MVC/Razor Pages 작업 및 페이지로 제한되었습니다. 이후 릴리스에서는 엔드포인트 연결 기능이 확장될 예정입니다.

라우팅은 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 클래스에 의해 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 연결되어 있습니다. [ASP.NET Core MVC](xref:mvc/overview)는 라우팅을 해당 구성의 일부로 미들웨어 파이프라인에 추가하고, MVC 및 Razor Pages 앱에서 라우팅을 처리합니다. 라우팅을 독립 실행형 구성 요소로 사용하는 방법을 알아보려면 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션을 참조하세요.

### <a name="url-matching"></a>URL 일치

URL 일치는 라우팅이 들어오는 요청을 *엔드포인트* 로 디스패치하는 프로세스입니다. 이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다. 요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.

엔드포인트 라우팅의 라우팅 시스템은 모든 디스패치를 결정합니다. 미들웨어가 선택된 엔드포인트를 기반으로 정책을 적용하므로 디스패치 또는 애플리케이션의 보안 정책에 영향을 미칠 수 있는 모든 결정은 라우팅 시스템 내에서 이루어져야 합니다.

엔드포인트 대리자가 실행되면 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성이 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정됩니다.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*)는 경로에서 생성된 *경로 값* 의 사전입니다. 이러한 값은 일반적으로 URL을 토큰화하여 결정되고, 사용자 입력을 수락하거나 앱 내부의 추가 디스패치 결정을 내리는 데 사용될 수 있습니다.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)는 일치하는 경로와 관련된 추가 데이터의 속성 모음입니다. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>는 앱에서 일치된 경로에 따라 결정할 수 있도록 각 경로와 상태 데이터의 연결을 지원하기 위해 제공됩니다. 이러한 값은 개발자 정의되고 어떤 방식으로든 라우팅의 동작에 영향을 주지 **않습니다** . 또한 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)에 안전하게 배치되는(stashed) 값은 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values)와는 달리 문자열 간에 변환될 수 있어야 하는 모든 형식일 수 있습니다.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers)는 요청을 성공적으로 일치하는 데 참여한 경로의 목록입니다. 경로는 서로 중첩될 수 있습니다. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 속성은 일치한 경로의 논리 트리를 통해 경로를 반영합니다. 일반적으로 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 첫 번째 항목은 경로 컬렉션이며 URL 생성을 위해 사용되어야 합니다. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 마지막 항목은 일치한 경로 처리기입니다.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>LinkGenerator를 사용한 URL 생성

URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다. 이렇게 하면 엔드포인트와 이에 액세스하는 URL을 논리적으로 분리할 수 있습니다.

엔드포인트 라우팅에는 링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)가 포함됩니다. <xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 [DI](xref:fundamentals/dependency-injection)에서 가져올 수 있는 싱글톤 서비스입니다. API는 실행 중인 요청의 컨텍스트 외부에서 사용할 수 있습니다. MVC의 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 및 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 사용하는 시나리오(예: [태그 도우미](xref:mvc/views/tag-helpers/intro), HTML 도우미 및 [작업 결과](xref:mvc/controllers/actions))는 링크 생성기를 사용하여 링크 생성 기능을 제공합니다.

링크 생성기는 *주소* 및 *주소 체계* 의 개념으로 지원됩니다. 주소 체계는 링크 생성을 위해 고려해야 할 엔드포인트를 결정하는 방법입니다. 예를 들어 많은 사용자가 MVC/Razor Pages에서 친숙한 경로 이름 및 경로 값 시나리오는 주소 체계로 구현됩니다.

링크 생성기는 다음 확장 메서드를 통해 MVC/Razor Pages 작업 및 페이지에 연결할 수 있습니다.

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

이러한 메서드의 오버로드에는 `HttpContext`를 포함한 인수가 허용됩니다. 이러한 메서드는 기능적으로 `Url.Action` 및 `Url.Page`와 동일하지만, 추가적인 유연성과 옵션을 제공합니다.

`GetPath*` 메서드는 절대 경로가 포함된 URI를 생성한다는 점에서 `Url.Action` 및 `Url.Page`와 가장 비슷합니다. `GetUri*` 메서드는 항상 체계와 호스트를 포함한 절대 URI를 생성합니다. `HttpContext`를 허용하는 메서드는 실행 중인 요청의 컨텍스트에서 URI를 생성합니다. 재정의되지 않는 한 실행 중인 요청의 앰비언트 경로 값, URL 기본 경로, 체계 및 호스트가 사용됩니다.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 주소를 사용하여 호출됩니다. URI 생성은 다음 두 단계로 수행됩니다.

1. 주소는 해당 주소와 일치하는 엔드포인트 목록에 바인딩됩니다.
1. 제공된 값과 일치하는 경로 패턴을 찾을 때까지 각 엔드포인트의 `RoutePattern`이 평가됩니다. 결과 출력은 링크 생성기에 제공된 다른 URI 부분과 결합되어 반환됩니다.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>에서 제공하는 메서드는 모든 유형의 주소에 대해 표준 링크 생성 기능을 지원합니다. 링크 생성기를 사용하는 가장 편리한 방법은 특정 주소 유형에 대한 작업을 수행하는 확장 메서드를 사용하는 것입니다.

| 확장 메서드   | 설명                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 제공된 값에 기반한 절대 경로의 URI를 생성합니다. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 제공된 값에 기반한 절대 URI를 생성합니다.             |

> [!WARNING]
> <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 메서드 호출 시 다음과 같은 의미에 주의하세요.
>
> * 들어오는 요청의 `GetUri*` 헤더의 유효성을 검사하지 않는 앱 구성에서는 `Host` 확장 메서드를 신중하게 사용하세요. 들어오는 요청의 `Host` 헤더의 유효성을 검사하지 않으면 신뢰할 수 없는 요청 입력이 보기/페이지에 포함된 URI로 클라이언트에 다시 보내질 수 있습니다. 모든 프로덕션 앱은 알려진 유효한 값에 대해 `Host` 헤더의 유효성을 검사하도록 서버를 구성하는 것이 좋습니다.
>
> * 미들웨어에서 `MapWhen` 또는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>과 함께 `Map`를 사용할 때는 신중하게 사용하세요. `Map*`는 실행 중인 요청의 기본 경로를 변경하여 링크 생성의 출력에 영향을 줍니다. 모든 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API는 기본 경로를 지정할 수 있습니다. 링크 생성에 대한 `Map*`의 영향을 취소하려면 항상 빈 기본 경로를 지정합니다.

## <a name="differences-from-earlier-versions-of-routing"></a>이전 버전의 라우팅과의 차이점

ASP.NET Core 2.2 이상의 엔드포인트 라우팅과 ASP.NET Core 이전 버전의 라우팅 간에는 다음과 같은 몇 가지 차이점이 있습니다.

* 엔드포인트 라우팅 시스템은 <xref:Microsoft.AspNetCore.Routing.Route>에서 상속하는 것을 포함하여 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 확장성을 지원하지 않습니다.

* 엔드포인트 라우팅은 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)을 지원하지 않습니다. 호환성 shim을 계속 사용하려면 2.1 [호환성 버전](xref:mvc/compatibility-version)(`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`)을 사용하세요.

* 엔드포인트 라우팅은 기존 경로를 사용하여 생성된 URI의 대/소문자 표기와 다른 동작을 수행합니다.

  다음과 같은 기본 경로 템플릿을 고려합니다.

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  다음 경로를 사용하여 작업에 대한 링크를 생성한다고 가정합니다.

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅을 사용하는 경우 이 코드는 제공된 경로 값의 대/소문자 표기를 고려하여 `/blog/ReadPost/17`이라는 URI를 생성합니다. ASP.NET Core 2.2 이상의 엔드포인트 라우팅에서는 `/Blog/ReadPost/17`을 생성합니다("Blog"의 첫 글자가 대문자로 지정됨). 엔드포인트 라우팅은 이 동작을 전역으로 사용자 지정하거나 URL 매핑에 대해 다른 규칙을 적용하는 데 사용할 수 있는 `IOutboundParameterTransformer` 인터페이스를 제공합니다.

  자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.

* MVC/Razor Pages에서 기존 경로를 통해 사용하는 링크 생성이 존재하지 않는 컨트롤러/작업 또는 페이지에 연결하려고 할 때 다르게 동작합니다.

  다음과 같은 기본 경로 템플릿을 고려합니다.

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  다음과 함께 기본 템플릿을 사용하여 작업에 대한 링크를 생성한다고 가정합니다.

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  `IRouter` 기반 라우팅을 사용하면 `BlogController`가 존재하지 않거나 `ReadPost` 작업 메서드가 존재하지 않는 경우에도 결과는 항상 `/Blog/ReadPost/17`입니다. 예상대로, 작업 메서드가 존재할 경우 ASP.NET Core 2.2 이상의 엔드포인트 라우팅은 `/Blog/ReadPost/17`을 생성합니다. *그러나 작업이 존재하지 않을 경우 엔드포인트 라우팅은 빈 문자열을 생성합니다.* 개념적으로 엔드포인트 라우팅은 작업이 존재하지 않는 경우 엔드포인트가 존재한다고 가정하지 않습니다.

* 링크 생성 *앰비언트 값 무효화 알고리즘* 은 엔드포인트 라우팅에서 사용할 때 다르게 작동합니다.

  *앰비언트 값 무효화* 는 링크 생성 작업에서 사용할 수 있는 현재 실행 중인 요청의 경로 값(앰비언트 값)을 결정하는 알고리즘입니다. 기존 라우팅은 다른 작업에 연결할 때 항상 추가 경로 값을 무효화했습니다. ASP.NET Core 2.2를 릴리스하기 전에는 특성 라우팅에 이 동작이 없었습니다. 이전 버전의 ASP.NET Core에서는 동일한 경로 매개 변수 이름을 사용하는 다른 작업에 연결하면 링크 생성 오류가 발생했습니다. ASP.NET Core 2.2 이상에서는 두 가지 형태의 라우팅 모두에서 다른 작업에 연결하면 값이 무효화됩니다.

  ASP.NET Core 2.1 이하에서 다음 예제를 고려합니다. 다른 작업(또는 다른 페이지)에 연결할 때 경로 값을 원하지 않는 방법으로 다시 사용할 수 있습니다.

  */Pages/Store/Product.cshtml* ,

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  */Pages/Login.cshtml* ,

  ```cshtml
  @page "{id?}"
  ```

  ASP.NET Core 2.1 이하에서 URI가 `/Store/Product/18`인 경우 Store/Info 페이지에서 `@Url.Page("/Login")`로 생성된 링크는 `/Login/18`입니다. 링크 대상이 완전히 다른 앱 부분인 경우에도 18이라는 `id` 값이 다시 사용됩니다. `/Login` 페이지의 컨텍스트에 있는 `id` 경로 값은 상점 제품 ID 값이 아닌 사용자 ID 값일 수 있습니다.

  ASP.NET Core 2.2 이상을 사용하는 엔드포인트 라우팅에서 결과는 `/Login`입니다. 연결된 대상이 다른 작업 또는 페이지인 경우 앰비언트 값은 다시 사용되지 않습니다.

* 라운드트립 경로 매개 변수 구문: 이중 별표(`**`) 범용(catch-all) 매개 변수 구문을 사용하는 경우 슬래시는 인코딩되지 않습니다.

  링크를 생성하는 동안 라우팅 시스템은 슬래시를 제외한 이중 별표(`**`) 범용 매개 변수(예: `{**myparametername}`)에서 캡처된 값을 인코딩합니다. 이중 별표 범용 매개 변수는 ASP.NET Core 2.2 이상의 `IRouter` 기반 라우팅에서 지원됩니다.

  ASP.NET Core 이전 버전의 단일 별표 범용 매개 변수 구문(`{*myparametername}`)은 계속 지원되며, 슬래시가 인코딩됩니다.

  | 경로              | 다음을 사용하여 생성되는 링크<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`(슬래시가 인코딩됨)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>미들웨어 예제

다음 예제에서는 미들웨어에서 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 상점 제품을 나열하는 작업 메서드에 대한 링크를 만듭니다. 링크 생성기를 클래스에 주입하고 `GenerateLink`를 호출하여 앱의 모든 클래스에서 해당 링크 생성기를 사용할 수 있습니다.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>경로 만들기

대부분의 앱은 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 또는 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>에 정의된 유사한 확장 메서드 중 하나를 호출하여 경로를 만듭니다. 모든 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 확장 메서드는 <xref:Microsoft.AspNetCore.Routing.Route>의 인스턴스를 만들고, 경로 컬렉션에 이를 추가합니다.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다. MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.

다음 코드 예제는 일반적인 ASP.NET Core MVC 경로 정의에서 사용되는 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 호출의 예제입니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

이 템플릿은 URL 경로와 일치시키고 경로 값을 추출합니다. 예를 들어 `/Products/Details/17` 경로는 `{ controller = Products, action = Details, id = 17 }` 경로 값을 생성합니다.

경로 값은 URL 경로를 세그먼트로 분할하고 각 세그먼트를 경로 템플릿의 *경로 매개 변수* 이름과 일치시켜 결정됩니다. 경로 매개 변수는 이름이 지정됩니다. 매개 변수는 매개 변수 이름을 `{ ... }` 중괄호로 묶어 정의됩니다.

또한 위의 템플릿은 `/` URL 경로와 일치시키고 `{ controller = Home, action = Index }` 값을 생성할 수도 있습니다. 이는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 있으며 `id` 경로 매개 변수는 선택 사항이기 때문에 발생합니다. 경로 매개 변수 이름 뒤에 있는 값이 뒤따르는 등호(`=`)는 매개 변수에 대한 기본값을 정의합니다. 경로 매개 변수 이름 뒤에 있는 물음표(`?`)는 선택적 매개 변수를 정의합니다.

기본 값을 사용하는 경로 매개 변수는 경로가 일치하는 경우 *항상* 경로 값을 생성합니다. 해당 URL 경로 세그먼트가 없는 경우 선택적 매개 변수는 경로 값을 생성하지 않습니다. 경로 템플릿 시나리오 및 구문에 대한 자세한 설명은 [경로 템플릿 참조](#route-template-reference) 섹션을 참조하세요.

다음 예제에서 `{id:int}` 경로 매개 변수 정의는 `id` 경로 매개 변수에 대한 [경로 제약 조건](#route-constraint-reference)을 정의합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

이 템플릿은 `/Products/Details/Apples`가 아닌 `/Products/Details/17`과 같은 URL 경로와 일치합니다. 경로 제약 조건은 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 구현하고 경로 값을 검사하여 확인합니다. 이 예제에서 경로 값 `id`는 정수로 변환할 수 있어야 합니다. 프레임워크에서 제공하는 경로 제약 조건에 대한 설명은 [경로 제약 조건 참조](#route-constraint-reference)를 참조하세요.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>의 추가 오버로드는 `constraints`, `dataTokens` 및 `defaults`에 대한 값을 허용합니다. 이러한 매개 변수의 일반적인 사용법은 익명 형식의 속성 이름이 경로 매개 변수 이름과 일치하는 익명 형식의 개체를 전달하는 것입니다.

다음 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 예제에서는 동등한 경로를 만듭니다.

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 제약 조건 및 기본값을 정의하기 위한 인라인 구문은 단순 경로에 편리할 수 있습니다. 그러나 데이터 토큰과 같이 인라인 구문에서는 지원되지 않는 시나리오가 있습니다.

다음 예제에서는 몇 가지 추가 시나리오를 보여 줍니다.

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

위의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치하고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다. `controller` 및 `action`에 대한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다. 기본값은 경로 템플릿에서 지정될 수 있습니다. `article` 경로 매개 변수는 경로 매개 변수 이름 앞에 이중 별표(`**`)를 표시하여 *범용* 으로 정의됩니다. 범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과도 일치시킬 수 있습니다.

다음 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

앞의 템플릿은 `/en-US/Products/5`와 같은 URL 경로와 일치하고, `{ controller = Products, action = Details, id = 5 }` 값 및 `{ locale = en-US }` 데이터 토큰을 추출합니다.

![지역 Windows 토큰](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Route 클래스 URL 생성

<xref:Microsoft.AspNetCore.Routing.Route> 클래스는 경로 값의 집합을 해당 경로 템플릿과 결합하여 URL 생성을 수행할 수도 있습니다. 이는 논리적으로 URL 경로와 일치시키는 역방향 프로세스입니다.

> [!TIP]
> URL 생성을 보다 잘 이해하려면 생성하려는 URL을 가정한 다음, 경로 템플릿을 해당 URL과 일치시키는 방법을 생각합니다. 어떤 값이 생성되나요? 이는 URL 생성이 <xref:Microsoft.AspNetCore.Routing.Route> 클래스에서 작동하는 방식과 대략적으로 동일합니다.

다음 예제에서는 일반적인 ASP.NET Core MVC 기본 경로를 사용합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

`{ controller = Products, action = List }` 경로 값을 사용하면 `/Products/List` URL이 생성됩니다. 경로 값은 URL 경로를 구성하기 위해 해당 경로 매개 변수에 대해 대체됩니다. `id`는 선택적 경로 매개 변수이므로 `id`에 대한 값 없이 URL이 성공적으로 생성됩니다.

`{ controller = Home, action = Index }` 경로 값을 사용하면 `/` URL이 생성됩니다. 제공된 경로 값이 기본값과 일치하고, 기본값에 해당하는 세그먼트는 안전하게 생략됩니다.

뒤이어 언급된 경로 정의(`/Home/Index` 및 `/`)를 사용하는 URL 생성 왕복 모두에서는 URL을 생성하기 위해 사용된 것과 동일한 경로 값을 생성합니다.

> [!NOTE]
> ASP.NET Core MVC를 사용하는 앱은 라우팅을 직접 호출하는 대신 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper>를 사용하여 URL을 생성해야 합니다.

URL 생성에 대한 자세한 내용은 [URL 생성 참조](#url-generation-reference) 섹션을 참조하세요.

## <a name="use-routing-middleware"></a>라우팅 미들웨어 사용

앱의 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.

`Startup.ConfigureServices`의 서비스 컨테이너에 라우팅을 추가합니다.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

경로는 `Startup.Configure` 메서드에서 구성되어야 합니다. 샘플 앱에서 사용하는 API는 다음과 같습니다.

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: HTTP GET 요청만 일치합니다.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

다음 표는 지정된 URI에 대한 응답을 보여줍니다.

| URI                    | 응답                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 일치하는 경로가 없으므로, 요청이 실패합니다.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | HTTP GET만 일치하므로, 요청이 실패합니다. |
| `GET /hello/Joe/Smith` | 일치하는 경로가 없으므로, 요청이 실패합니다.              |

프레임워크는 경로를 만드는 확장 메서드 모음(<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)을 제공합니다.

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

`Map[Verb]` 메서드는 제약 조건을 사용하여 메서드 이름에 지정된 HTTP 동사에 대한 경로로 제한합니다. 예제는 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 및 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>을 참조하세요.

## <a name="route-template-reference"></a>경로 템플릿 참조

중괄호(`{ ... }`) 내의 토큰은 경로가 일치하는 경우 바인딩될 *경로 매개 변수* 를 정의합니다. 경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 분리되어 있어야 합니다. 예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다. 이러한 경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.

경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다. 텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다. 리터럴 경로 매개 변수 구분 기호(`{` 또는 `}`)와 일치시키려면 문자를 반복하여(`{{` 또는 `}}`) 구분 기호를 이스케이프합니다.

선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다. 예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다. `filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다. URL에 `filename`에 대한 값만 있으면 후행 마침표(`.`)가 선택 사항이므로 경로가 일치합니다. 다음 URL은 이 경로와 일치합니다.

* `/files/myFile.txt`
* `/files/myFile`

별표(`*`) 또는 이중 별표(`**`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다. 이러한 매개 변수는 *범용* 매개 변수라고 합니다. 예를 들어 `blog/{**slug}`는 `/blog`로 시작하고 모든 값(`slug` 경로 값에 할당된)이 뒤따르는 모든 URI와 일치합니다. 범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.

catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다. 예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다. 이스케이프된 슬래시에 주의하세요. 경로 구분 기호 문자를 왕복하려면 `**` 경로 매개 변수 접두사를 사용합니다. `{ path = "my/path" }`를 사용하는 경로 `foo/{**path}`는 `foo/my/path`를 생성합니다.

경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 *기본값* 이 있을 수 있습니다. 예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다. URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다. 경로 매개 변수는 `id?`와 같이 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다. 선택적 값과 기본 경로 매개 변수 간의 차이점은 기본값이 있는 경로 매개 변수는 항상 값을 생성한다는 것입니다. 선택적 매개 변수에는 요청 URL에서 값을 제공하는 경우에만 값이 있습니다.

경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다. 경로 매개 변수 이름 뒤에 콜론(`:`)과 제약 조건 이름을 추가하여 경로 매개 변수에서 *인라인 제약 조건* 을 지정합니다. 제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에 인수를 괄호(`(...)`)로 묶습니다. 또 다른 콜론(`:`) 및 제약 조건 이름을 추가하여 여러 인라인 제약 조건을 지정할 수 있습니다.

제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다. 예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다. 경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.

또한 경로 매개 변수에는 링크를 생성하고 URL에 대한 작업 및 페이지와 일치할 때 매개 변수 값을 변환하는 매개 변수 변환기가 있을 수도 있습니다. 제약 조건과 마찬가지로, 매개 변수 변환기는 경로 매개 변수 이름 뒤에 콜론(`:`)과 변환기 이름을 추가하여 라우트 매개 변수에 인라인으로 추가될 수 있습니다. 예를 들어 경로 템플릿 `blog/{article:slugify}`는 `slugify` 변환기를 지정합니다. 매개 변수 변환기에 대한 자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.

다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.

| 경로 템플릿                           | URI 일치 예제    | 요청 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | `/hello` 단일 경로만 일치합니다.                                     |
| `{Page=Home}`                            | `/`                     | 일치하고, `Page`를 `Home`으로 설정합니다.                                         |
| `{Page=Home}`                            | `/Contact`              | 일치하고, `Page`를 `Contact`로 설정합니다.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다(`id`가 무시됨).        |

템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다. 제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.

> [!TIP]
> [로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.

## <a name="reserved-routing-names"></a>예약된 라우팅 이름

다음 키워드는 예약된 이름이므로 경로 이름 또는 매개 변수로 사용할 수 없습니다.

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>경로 제약 조건 참조

경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다. 일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 예/아니요 의사 결정을 내립니다. 일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다. 예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다. 제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.

> [!WARNING]
> 제약 조건을 **입력 유효성 검사** 에 사용하지 마세요. 제약 조건이 **입력 유효성 검사** 에 사용되는 경우 잘못된 입력으로 인해 적절한 오류 메시지와 함께 *400 - 잘못된 요청* 대신 *404 - 찾을 수 없음* 응답이 발생합니다. 경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 **명확하게 구분하는 데** 사용됩니다.

다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.

| 제약 조건 | 예제 | 일치하는 예제 | 참고 |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치|
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` 또는 `false`와 일치. 대/소문자 구분하지 않음|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 유효한 `DateTime` 값 일치(고정 문화권에서) 위의 경고를 참조하세요.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 유효한 `decimal` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 유효한 `double` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 유효한 `float` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 유효한 `Guid` 값과 일치|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 `long` 값과 일치|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열은 4자 이상이어야 합니다.|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 문자열의 최대 크기는 8자입니다.|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열은 정확히 12자여야 합니다.|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열은 8자 이상이어야 하며 최대 16자여야 합니다.|
| `min(value)` | `{age:min(18)}` | `19` | 정수 값은 18 이상이어야 합니다.|
| `max(value)` | `{age:max(120)}` | `91` | 정수 값의 최댓값은 120입니다.|
| `range(min,max)` | `{age:range(18,120)}` | `91` | 정수 값은 18 이상이고 최대값은 120이어야 합니다.|
| `alpha` | `{name:alpha}` | `Rick` | 문자열은 하나 이상의 영문자(`a`-`z`)로 구성되어야 합니다. 대/소문자 구분하지 않음|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열은 정규식과 일치해야 합니다. 정규식을 정의하는 방법에 대한 팁을 참조하세요.|
| `required` | `{name:required}` | `Rick` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됨|

콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다. 예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다. 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다. 프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다. URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다. 예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.

## <a name="regular-expressions"></a>정규식

ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다. 이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.

정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다. 정규식 토큰은 이스케이프되어야 합니다. 라우팅에서 정규식 `^\d{3}-\d{2}-\d{4}$`을 사용하려면:

* 식에는 문자열에 제공된 단일 백슬래시 `\` 문자가 소스 코드의 이중 백슬래시 `\\` 문자로 포함되어야 합니다.
* 정규식에서는 `\` 문자열 이스케이프 문자를 이스케이프하기 위해 `\\`를 사용해야 합니다.
* 정규식에서 [축자 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string)을 사용하는 경우 `\\`가 필요하지 않습니다.

라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(`{{`, `}`, `[[`, `]]`). 다음 표는 정규식 및 이스케이프된 버전을 보여 줍니다.

| 정규식    | 이스케이프된 정규식     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

라우팅에 사용되는 정규식은 캐럿(`^`) 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다. 식은 달러 기호(`$`) 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다. `^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다. `^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하는데, 이는 종종 원하는 것이 아닙니다. 다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.

| 식   | String    | 일치 | 주석               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | mz        | 예   | 식 일치    |
| `[a-z]{2}`   | MZ        | 예   | 대/소문자 구분하지 않음    |
| `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$` 참조 |
| `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$` 참조 |

정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.

가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다. 예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다. 제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다. 알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전(템플릿 내 인라인이 아님)에서 전달되는 제약 조건도 정규식으로 처리됩니다.

## <a name="custom-route-constraints"></a>사용자 지정 경로 제약 조건

기본 제공 경로 제약 조건 외에도 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 `Match` 단일 메서드가 포함됩니다.

사용자 지정 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 사용하려면 앱의 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 구현과 매핑하는 사전입니다. `Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 수정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

이제 제약 조건 형식을 등록할 때 지정한 이름을 사용하여 일반적인 방식으로 제약 조건을 경로에 적용할 수 있습니다. 예를 들어:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>매개 변수 변환기 참조

매개 변수 변환기는:

* <xref:Microsoft.AspNetCore.Routing.Route>에 대한 링크를 생성할 때 실행합니다.
* `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`를 구현해야 합니다.
* <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성됩니다.
* 매개 변수의 경로 값을 가져와서 새 문자열 값으로 변환합니다.
* 생성된 링크에서 변환된 값을 사용합니다.

예를 들어, `Url.Action(new { article = "MyTestArticle" })`을 사용하는 경로 패턴 `blog\{article:slugify}`의 사용자 지정 `slugify` 매개 변수 변환기는 `blog\my-test-article`을 생성합니다.

경로 패턴에서 매개 변수 변환기를 사용하려면 먼저 `Startup.ConfigureServices`의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성합니다.

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

매개 변수 변환기는 프레임워크에 의해 사용되어 엔드포인트가 해결되는 URI를 변환합니다. 예를 들어 ASP.NET Core MVC는 매개 변수 변환기를 사용하여 `area` , `controller` , `action` 및 `page`와 일치하도록 사용되는 경로 값을 변환합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

위의 경로를 사용하면 `SubscriptionManagementController.GetAll` 작업이 URI `/subscription-management/get-all`과 일치합니다. 매개 변수 변환기는 링크를 생성하는 데 사용되는 경로 값을 변경하지 않습니다. 예를 들어 `Url.Action("GetAll", "SubscriptionManagement")`는 `/subscription-management/get-all`을 출력합니다.

ASP.NET Core는 생성된 경로와 함께 매개 변수 변환기를 사용하기 위한 API 규칙을 제공합니다.

* ASP.NET Core MVC에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 규칙이 있습니다. 이 규칙은 앱의 모든 특성 경로에 지정된 매개 변수 변환기를 적용합니다. 매개 변수 변환기는 특성 경로 토큰이 교체될 때 변환합니다. 자세한 내용은 [매개 변수 변환기를 사용하여 토큰 교체 사용자 지정](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)을 참조하세요.
* Razor Pages에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 규칙이 있습니다. 이 규칙은 자동으로 검색된 모든 Razor Pages에 지정된 매개 변수 변환기를 적용합니다. 매개 변수 변환기는 Razor Pages 경로의 폴더와 파일 이름 부분을 변환합니다. 자세한 내용은 [매개 변수 변환기를 사용하여 페이지 경로 사용자 지정](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)을 참조하세요.

## <a name="url-generation-reference"></a>URL 생성 참조

다음 예제에서는 경로 값의 사전 및 <xref:Microsoft.AspNetCore.Routing.RouteCollection>이 지정된 경로에 대한 링크를 생성하는 방법을 보여줍니다.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

위의 샘플 끝부분에서 생성된 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>는 `/package/create/123`입니다. 사전은 "Track Package Route" 템플릿인 `package/{operation}/{id}`의 `operation` 및 `id` 경로 값을 제공합니다. 자세한 내용은 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션의 샘플 코드 또는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)을 참조하세요.

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 생성자에 대한 두 번째 매개 변수는 *앰비언트 값* 의 컬렉션입니다. 개발자가 요청 컨텍스트 내에서 지정해야 하는 값의 수를 제한하므로 앰비언트 값은 사용하기 편리합니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. ASP.NET Core MVC 앱의 `HomeController`에 대한 `About` 작업에서는 `Index` 작업에 연결하기 위해 컨트롤러 경로 값을 지정할 필요가 없으며, `Home`이라는 앰비언트 값이 사용됩니다.

매개 변수와 일치하지 않는 앰비언트 값은 무시됩니다. 명시적으로 제공된 값이 앰비언트 값을 재정의하는 경우에도 앰비언트 값이 무시됩니다. URL에서 일치는 왼쪽에서 오른쪽으로 수행됩니다.

명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다. 다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.

| 앰비언트 값                     | 명시적 값                        | 결과                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

경로에 매개 변수에 해당하지 않고 값이 명시적으로 제공된 기본값이 있는 경우 기본값과 일치해야 합니다.

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

`controller` 및 `action`에 대해 일치하는 값이 제공되는 경우에만 링크 생성에서 이 경로에 대한 링크를 생성합니다.

## <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트(예: `[Route("/x{token}y")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다. 복잡한 세그먼트 일치 방법에 대한 자세한 설명은 [이 코드](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)를 참조하세요. [코드 샘플](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)은 ASP.NET Core에서 사용되지 않지만 복잡한 세그먼트에 대한 적절한 설명을 제공합니다.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

라우팅은 요청 URI를 경로 처리기에 매핑하고 들어오는 요청을 디스패치합니다. 경로는 앱에서 정의되고 앱 시작 시 구성됩니다. 경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다. 라우팅은 앱에 구성된 경로를 사용하여 경로 처리기에 매핑되는 URL을 생성할 수 있습니다.

ASP.NET Core 2.1에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> 이 문서에서는 낮은 수준의 ASP.NET Core 라우팅을 설명합니다. ASP.NET Core MVC 라우팅에 대한 내용은 <xref:mvc/controllers/routing>을 참조하세요. Razor Pages의 라우팅 규칙에 대한 내용은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>라우팅 기본 사항

대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다. 기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:

* 기본적이고 서술적인 라우팅 체계를 지원합니다.
* UI 기반 앱에 대한 유용한 시작점입니다.

일반적으로 개발자는 [특성 라우팅](xref:mvc/controllers/routing#attribute-routing) 또는 전용 기존 경로를 사용하여 특수한 상황에서 앱의 트래픽이 높은 영역(예: 블로그 및 전자 상거래 엔드포인트)에 간결한 추가 경로를 추가합니다.

Web API는 특성 라우팅을 사용하여 작업이 HTTP 동사로 표현되는 리소스 집합으로 앱의 기능을 모델링해야 합니다. 이는 동일한 논리 리소스의 많은 작업(예: GET, POST)이 동일한 URL을 사용한다는 뜻입니다. 특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.

Razor Pages 앱은 기본 기존 라우팅을 사용하여 앱의 *Pages* 폴더에서 명명된 리소스를 제공합니다. Razor Pages 라우팅 동작을 사용자 지정할 수 있는 추가 규칙을 사용할 수 있습니다. 자세한 내용은 <xref:razor-pages/index> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.

URL 생성 지원을 사용하면 URL을 하드 코딩하지 않고 앱을 개발하여 앱을 서로 연결할 수 있습니다. 이 지원을 사용하면 기본 라우팅 구성으로 시작하고 앱의 리소스 레이아웃이 결정된 후에 해당 경로를 수정할 수 있습니다.

라우팅은 <xref:Microsoft.AspNetCore.Routing.IRouter>의 경로 구현을 사용하여 다음 작업을 수행합니다.

* 들어오는 요청을 *경로 처리기* 에 매핑합니다.
* 응답에 사용되는 URL을 생성합니다.

기본적으로 앱에는 단일 경로 컬렉션이 있습니다. 요청이 도착하면 컬렉션의 경로가 컬렉션에 존재하는 순서대로 처리됩니다. 프레임워크는 컬렉션의 각 경로에서 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출하여 들어오는 요청 URL을 컬렉션의 경로와 일치시키려고 시도합니다. 응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.

라우팅 시스템에는 다음과 같은 특징이 있습니다.

* 경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.
* 기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.
* MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 경로를 등록합니다.
* 응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.
* URL 생성은 임의의 확장성을 지원하는 경로를 기반으로 합니다. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>는 URL을 작성하는 메서드를 제공합니다.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
라우팅은 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 클래스에 의해 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 연결되어 있습니다. [ASP.NET Core MVC](xref:mvc/overview)는 라우팅을 해당 구성의 일부로 미들웨어 파이프라인에 추가하고, MVC 및 Razor Pages 앱에서 라우팅을 처리합니다. 라우팅을 독립 실행형 구성 요소로 사용하는 방법을 알아보려면 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션을 참조하세요.

### <a name="url-matching"></a>URL 일치

URL 일치는 라우팅이 들어오는 요청을 *처리기* 로 디스패치하는 프로세스입니다. 이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다. 요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.

들어오는 요청이 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>에 진입하면 순차적으로 각 경로의 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출합니다. <xref:Microsoft.AspNetCore.Routing.IRouter> 인스턴스는 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*)를 null이 아닌 <xref:Microsoft.AspNetCore.Http.RequestDelegate>로 설정하여 요청을 *처리* 할지 여부를 선택합니다. 경로가 요청에 대한 처리기를 설정하는 경우 경로 처리가 중지되고 해당 처리기가 요청을 처리하기 위해 호출됩니다. 요청을 처리할 경로 처리기가 없는 경우 미들웨어는 요청을 요청 파이프라인의 다음 미들웨어에 전달합니다.

<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>에 대한 기본 입력은 현재 요청과 연결된 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)입니다. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 및 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*)는 경로가 일치된 후에 설정되는 출력입니다.

<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>를 호출하는 일치는 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성도 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정합니다.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*)는 경로에서 생성된 *경로 값* 의 사전입니다. 이러한 값은 일반적으로 URL을 토큰화하여 결정되고, 사용자 입력을 수락하거나 앱 내부의 추가 디스패치 결정을 내리는 데 사용될 수 있습니다.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)는 일치하는 경로와 관련된 추가 데이터의 속성 모음입니다. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>는 앱에서 일치된 경로에 따라 결정할 수 있도록 각 경로와 상태 데이터의 연결을 지원하기 위해 제공됩니다. 이러한 값은 개발자 정의되고 어떤 방식으로든 라우팅의 동작에 영향을 주지 **않습니다** . 또한 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)에 안전하게 배치되는(stashed) 값은 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values)와는 달리 문자열 간에 변환될 수 있어야 하는 모든 형식일 수 있습니다.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers)는 요청을 성공적으로 일치하는 데 참여한 경로의 목록입니다. 경로는 서로 중첩될 수 있습니다. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 속성은 일치한 경로의 논리 트리를 통해 경로를 반영합니다. 일반적으로 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 첫 번째 항목은 경로 컬렉션이며 URL 생성을 위해 사용되어야 합니다. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 마지막 항목은 일치한 경로 처리기입니다.

<a name="lg"></a>

### <a name="url-generation"></a>URL 생성

URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다. 이렇게 하면 경로 처리기와 이에 액세스하는 URL을 논리적으로 구분할 수 있습니다.

URL 생성은 비슷한 반복적인 프로세스를 따르지만 경로 컬렉션의 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드로 호출하는 사용자 또는 프레임워크 코드로 시작합니다. 각 *경로* 에는 null이 아닌 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>가 반환될 때까지 순차적으로 호출되는 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드가 있습니다.

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>에 대한 기본 입력은 다음과 같습니다.

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

경로는 주로 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 및 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>에서 제공하는 경로 값을 사용하여 URL을 생성할 수 있는지 여부와 포함할 값을 결정합니다. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>는 현재 요청 일치로부터 생성된 경로 값의 세트입니다. 반면, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values>는 현재 작업에 대한 원하는 URL을 생성하는 방법을 지정하는 경로 값입니다. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>는 경로가 서비스 또는 현재 컨텍스트와 연결된 추가 데이터를 가져와야 하는 경우에 제공됩니다.

> [!TIP]
> [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*)를 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)에 대한 재정의 세트로 간주하세요. URL 생성은 동일한 경로 또는 경로 값을 사용하는 링크에 대한 URL을 생성하기 위해 현재 요청의 경로 값을 다시 사용하려고 시도합니다.

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>의 출력은 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>입니다. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 <xref:Microsoft.AspNetCore.Routing.RouteData>와 유사합니다. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 출력 URL 및 경로에 의해 설정되어야 하는 몇 가지 추가 속성에 대한 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>를 포함합니다.

[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 속성은 경로에 의해 생성된 *가상 경로* 를 포함합니다. 필요에 따라 경로를 추가로 처리해야 합니다. HTML에서 생성된 URL을 렌더링하려는 경우 앱의 기본 경로를 추가합니다.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*)는 성공적으로 URL을 생성한 경로에 대한 참조입니다.

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 속성은 URL을 생성한 경로와 관련된 추가 데이터의 사전입니다. 이 속성은 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)와 유사합니다.

### <a name="create-routes"></a>경로 만들기

라우팅은 <xref:Microsoft.AspNetCore.Routing.IRouter>의 표준 구현으로 <xref:Microsoft.AspNetCore.Routing.Route> 클래스를 제공합니다. <xref:Microsoft.AspNetCore.Routing.Route>는 *경로 템플릿* 구문을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>가 호출될 때 URL 경로와 일치하는 패턴을 정의합니다. <xref:Microsoft.AspNetCore.Routing.Route>는 동일한 경로 템플릿을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>가 호출되었을 때 URL을 생성합니다.

대부분의 앱은 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 또는 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>에 정의된 유사한 확장 메서드 중 하나를 호출하여 경로를 만듭니다. 모든 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 확장 메서드는 <xref:Microsoft.AspNetCore.Routing.Route>의 인스턴스를 만들고, 경로 컬렉션에 이를 추가합니다.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다. 기본 처리기는 `IRouter`이며, 처리기에서 요청을 처리하지 못할 수 있습니다. 예를 들어 ASP.NET Core MVC는 일반적으로 사용 가능한 컨트롤러 및 작업과 일치하는 요청만 처리하는 기본 처리기로 구성됩니다. MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.

다음 코드 예제는 일반적인 ASP.NET Core MVC 경로 정의에서 사용되는 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 호출의 예제입니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

이 템플릿은 URL 경로와 일치시키고 경로 값을 추출합니다. 예를 들어 `/Products/Details/17` 경로는 `{ controller = Products, action = Details, id = 17 }` 경로 값을 생성합니다.

경로 값은 URL 경로를 세그먼트로 분할하고 각 세그먼트를 경로 템플릿의 *경로 매개 변수* 이름과 일치시켜 결정됩니다. 경로 매개 변수는 이름이 지정됩니다. 매개 변수는 매개 변수 이름을 `{ ... }` 중괄호로 묶어 정의됩니다.

또한 위의 템플릿은 `/` URL 경로와 일치시키고 `{ controller = Home, action = Index }` 값을 생성할 수도 있습니다. 이는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 있으며 `id` 경로 매개 변수는 선택 사항이기 때문에 발생합니다. 경로 매개 변수 이름 뒤에 있는 값이 뒤따르는 등호(`=`)는 매개 변수에 대한 기본값을 정의합니다. 경로 매개 변수 이름 뒤에 있는 물음표(`?`)는 선택적 매개 변수를 정의합니다.

기본 값을 사용하는 경로 매개 변수는 경로가 일치하는 경우 *항상* 경로 값을 생성합니다. 해당 URL 경로 세그먼트가 없는 경우 선택적 매개 변수는 경로 값을 생성하지 않습니다. 경로 템플릿 시나리오 및 구문에 대한 자세한 설명은 [경로 템플릿 참조](#route-template-reference) 섹션을 참조하세요.

다음 예제에서 `{id:int}` 경로 매개 변수 정의는 `id` 경로 매개 변수에 대한 [경로 제약 조건](#route-constraint-reference)을 정의합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

이 템플릿은 `/Products/Details/Apples`가 아닌 `/Products/Details/17`과 같은 URL 경로와 일치합니다. 경로 제약 조건은 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 구현하고 경로 값을 검사하여 확인합니다. 이 예제에서 경로 값 `id`는 정수로 변환할 수 있어야 합니다. 프레임워크에서 제공하는 경로 제약 조건에 대한 설명은 [경로 제약 조건 참조](#route-constraint-reference)를 참조하세요.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>의 추가 오버로드는 `constraints`, `dataTokens` 및 `defaults`에 대한 값을 허용합니다. 이러한 매개 변수의 일반적인 사용법은 익명 형식의 속성 이름이 경로 매개 변수 이름과 일치하는 익명 형식의 개체를 전달하는 것입니다.

다음 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 예제에서는 동등한 경로를 만듭니다.

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 제약 조건 및 기본값을 정의하기 위한 인라인 구문은 단순 경로에 편리할 수 있습니다. 그러나 데이터 토큰과 같이 인라인 구문에서는 지원되지 않는 시나리오가 있습니다.

다음 예제에서는 몇 가지 추가 시나리오를 보여 줍니다.

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

위의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치하고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다. `controller` 및 `action`에 대한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다. 기본값은 경로 템플릿에서 지정될 수 있습니다. `article` 경로 매개 변수는 경로 매개 변수 이름 앞에 별표(`*`)를 표시하여 *범용* 으로 정의됩니다. 범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과도 일치시킬 수 있습니다.

다음 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

앞의 템플릿은 `/en-US/Products/5`와 같은 URL 경로와 일치하고, `{ controller = Products, action = Details, id = 5 }` 값 및 `{ locale = en-US }` 데이터 토큰을 추출합니다.

![지역 Windows 토큰](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Route 클래스 URL 생성

<xref:Microsoft.AspNetCore.Routing.Route> 클래스는 경로 값의 집합을 해당 경로 템플릿과 결합하여 URL 생성을 수행할 수도 있습니다. 이는 논리적으로 URL 경로와 일치시키는 역방향 프로세스입니다.

> [!TIP]
> URL 생성을 보다 잘 이해하려면 생성하려는 URL을 가정한 다음, 경로 템플릿을 해당 URL과 일치시키는 방법을 생각합니다. 어떤 값이 생성되나요? 이는 URL 생성이 <xref:Microsoft.AspNetCore.Routing.Route> 클래스에서 작동하는 방식과 대략적으로 동일합니다.

다음 예제에서는 일반적인 ASP.NET Core MVC 기본 경로를 사용합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

`{ controller = Products, action = List }` 경로 값을 사용하면 `/Products/List` URL이 생성됩니다. 경로 값은 URL 경로를 구성하기 위해 해당 경로 매개 변수에 대해 대체됩니다. `id`는 선택적 경로 매개 변수이므로 `id`에 대한 값 없이 URL이 성공적으로 생성됩니다.

`{ controller = Home, action = Index }` 경로 값을 사용하면 `/` URL이 생성됩니다. 제공된 경로 값이 기본값과 일치하고, 기본값에 해당하는 세그먼트는 안전하게 생략됩니다.

뒤이어 언급된 경로 정의(`/Home/Index` 및 `/`)를 사용하는 URL 생성 왕복 모두에서는 URL을 생성하기 위해 사용된 것과 동일한 경로 값을 생성합니다.

> [!NOTE]
> ASP.NET Core MVC를 사용하는 앱은 라우팅을 직접 호출하는 대신 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper>를 사용하여 URL을 생성해야 합니다.

URL 생성에 대한 자세한 내용은 [URL 생성 참조](#url-generation-reference) 섹션을 참조하세요.

## <a name="use-routing-middleware"></a>라우팅 미들웨어 사용

앱의 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.

`Startup.ConfigureServices`의 서비스 컨테이너에 라우팅을 추가합니다.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

경로는 `Startup.Configure` 메서드에서 구성되어야 합니다. 샘플 앱에서 사용하는 API는 다음과 같습니다.

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: HTTP GET 요청만 일치합니다.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

다음 표는 지정된 URI에 대한 응답을 보여줍니다.

| URI                    | 응답                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 일치하는 경로가 없으므로, 요청이 실패합니다.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | HTTP GET만 일치하므로, 요청이 실패합니다. |
| `GET /hello/Joe/Smith` | 일치하는 경로가 없으므로, 요청이 실패합니다.              |

단일 경로를 구성하는 경우 `IRouter` 인스턴스를 전달하는 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>를 호출합니다. <xref:Microsoft.AspNetCore.Routing.RouteBuilder>는 사용할 필요가 없습니다.

프레임워크는 경로를 만드는 확장 메서드 모음(<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)을 제공합니다.

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>과 같은 나열된 메서드 중 일부는 <xref:Microsoft.AspNetCore.Http.RequestDelegate>를 필요로 합니다. <xref:Microsoft.AspNetCore.Http.RequestDelegate>는 경로가 일치하는 경우 *경로 처리기* 로 사용됩니다. 이 모음의 다른 메서드를 사용하면 경로 처리기로 사용할 미들웨어 파이프라인을 구성할 수 있습니다. `Map*` 메서드에서 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>와 같은 처리기를 허용하지 않는 경우 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>를 사용합니다.

`Map[Verb]` 메서드는 제약 조건을 사용하여 메서드 이름에 지정된 HTTP 동사에 대한 경로로 제한합니다. 예제는 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 및 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>을 참조하세요.

## <a name="route-template-reference"></a>경로 템플릿 참조

중괄호(`{ ... }`) 내의 토큰은 경로가 일치하는 경우 바인딩될 *경로 매개 변수* 를 정의합니다. 경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 분리되어 있어야 합니다. 예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다. 이러한 경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.

경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다. 텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다. 리터럴 경로 매개 변수 구분 기호(`{` 또는 `}`)와 일치시키려면 문자를 반복하여(`{{` 또는 `}}`) 구분 기호를 이스케이프합니다.

선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다. 예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다. `filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다. URL에 `filename`에 대한 값만 있으면 후행 마침표(`.`)가 선택 사항이므로 경로가 일치합니다. 다음 URL은 이 경로와 일치합니다.

* `/files/myFile.txt`
* `/files/myFile`

별표(`*`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다. 이를 *범용* 매개 변수라고 합니다. 예를 들어 `blog/{*slug}`는 `/blog`로 시작하고 모든 값(`slug` 경로 값에 할당된)이 뒤따르는 모든 URI와 일치합니다. 범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.

catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다. 예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다. 이스케이프된 슬래시에 주의하세요.

경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 *기본값* 이 있을 수 있습니다. 예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다. URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다. 경로 매개 변수는 `id?`와 같이 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다. 선택적 값과 기본 경로 매개 변수 간의 차이점은 기본값이 있는 경로 매개 변수는 항상 값을 생성한다는 것입니다. 선택적 매개 변수에는 요청 URL에서 값을 제공하는 경우에만 값이 있습니다.

경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다. 경로 매개 변수 이름 뒤에 콜론(`:`)과 제약 조건 이름을 추가하여 경로 매개 변수에서 *인라인 제약 조건* 을 지정합니다. 제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에 인수를 괄호(`(...)`)로 묶습니다. 또 다른 콜론(`:`) 및 제약 조건 이름을 추가하여 여러 인라인 제약 조건을 지정할 수 있습니다.

제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다. 예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다. 경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.

다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.

| 경로 템플릿                           | URI 일치 예제    | 요청 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | `/hello` 단일 경로만 일치합니다.                                     |
| `{Page=Home}`                            | `/`                     | 일치하고, `Page`를 `Home`으로 설정합니다.                                         |
| `{Page=Home}`                            | `/Contact`              | 일치하고, `Page`를 `Contact`로 설정합니다.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다(`id`가 무시됨).        |

템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다. 제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.

> [!TIP]
> [로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.

## <a name="route-constraint-reference"></a>경로 제약 조건 참조

경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다. 일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 예/아니요 의사 결정을 내립니다. 일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다. 예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다. 제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.

> [!WARNING]
> 제약 조건을 **입력 유효성 검사** 에 사용하지 마세요. 제약 조건이 **입력 유효성 검사** 에 사용되는 경우 잘못된 입력으로 인해 적절한 오류 메시지와 함께 *400 - 잘못된 요청* 대신 *404 - 찾을 수 없음* 응답이 발생합니다. 경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 **명확하게 구분하는 데** 사용됩니다.

다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.

| 제약 조건 | 예제 | 일치하는 예제 | 참고 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치 |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` 또는 `false` 일치(대/소문자 구분하지 않음) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 유효한 `DateTime` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 유효한 `decimal` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 유효한 `double` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 유효한 `float` 값 일치(고정 문화권에서) 이전 경고를 참조하세요.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 유효한 `Guid` 값 일치 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 `long` 값 일치 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열은 4자 이상이어야 합니다. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 문자열은 8자 이하여야 합니다. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열은 정확히 12자여야 합니다. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열의 길이는 8자 이상이며 16자 이하여야 합니다. |
| `min(value)` | `{age:min(18)}` | `19` | 정수 값은 18 이상이어야 합니다. |
| `max(value)` | `{age:max(120)}` | `91` | 정수 값은 120 이하여야 합니다. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 정수 값은 18 이상이며 120 이하여야 합니다. |
| `alpha` | `{name:alpha}` | `Rick` | 문자열은 하나 이상의 알파벳 문자(`a`-`z`, 대/소문자 구분)로 구성되어야 합니다. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열은 정규식과 일치해야 합니다(정규식 정의에 대한 팁 참조). |
| `required` | `{name:required}` | `Rick` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됨 |

콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다. 예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다. 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다. 프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다. URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다. 예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.

## <a name="regular-expressions"></a>정규식

ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다. 이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.

정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다. 정규식 토큰은 이스케이프되어야 합니다. 라우팅에서 `^\d{3}-\d{2}-\d{4}$`라는 정규식을 사용하려면, C# 소스 파일에서는 `\` 문자열 이스케이프 문자를 이스케이프하기 위해서 식 문자열의 `\`(단일 백슬래시) 문자를 `\\`(이중 백슬래시) 문자로 제공해야 합니다([약어 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string)을 사용하지 않는 한). 라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(`{{`, `}`, `[[`, `]]`). 다음 표는 정규식 및 이스케이프된 버전을 보여 줍니다.

| 정규식    | 이스케이프된 정규식     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

라우팅에 사용되는 정규식은 캐럿(`^`) 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다. 식은 달러 기호(`$`) 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다. `^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다. `^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하는데, 이는 종종 원하는 것이 아닙니다. 다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.

| 식   | String    | 일치 | 주석               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     |
| `[a-z]{2}`   | mz        | 예   | 식 일치    |
| `[a-z]{2}`   | MZ        | 예   | 대/소문자 구분하지 않음    |
| `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$` 참조 |
| `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$` 참조 |

정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.

가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다. 예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다. 제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다. 알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전(템플릿 내 인라인이 아님)에서 전달되는 제약 조건도 정규식으로 처리됩니다.

## <a name="custom-route-constraints"></a>사용자 지정 경로 제약 조건

기본 제공 경로 제약 조건 외에도 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 `Match` 단일 메서드가 포함됩니다.

사용자 지정 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 사용하려면 앱의 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 구현과 매핑하는 사전입니다. `Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 수정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

이제 제약 조건 형식을 등록할 때 지정한 이름을 사용하여 일반적인 방식으로 제약 조건을 경로에 적용할 수 있습니다. 예를 들어:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>URL 생성 참조

다음 예제에서는 경로 값의 사전 및 <xref:Microsoft.AspNetCore.Routing.RouteCollection>이 지정된 경로에 대한 링크를 생성하는 방법을 보여줍니다.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

위의 샘플 끝부분에서 생성된 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>는 `/package/create/123`입니다. 사전은 "Track Package Route" 템플릿인 `package/{operation}/{id}`의 `operation` 및 `id` 경로 값을 제공합니다. 자세한 내용은 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션의 샘플 코드 또는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)을 참조하세요.

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 생성자에 대한 두 번째 매개 변수는 *앰비언트 값* 의 컬렉션입니다. 개발자가 요청 컨텍스트 내에서 지정해야 하는 값의 수를 제한하므로 앰비언트 값은 사용하기 편리합니다. 현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다. ASP.NET Core MVC 앱의 `HomeController`에 대한 `About` 작업에서는 `Index` 작업에 연결하기 위해 컨트롤러 경로 값을 지정할 필요가 없으며, `Home`이라는 앰비언트 값이 사용됩니다.

매개 변수와 일치하지 않는 앰비언트 값은 무시됩니다. 명시적으로 제공된 값이 앰비언트 값을 재정의하는 경우에도 앰비언트 값이 무시됩니다. URL에서 일치는 왼쪽에서 오른쪽으로 수행됩니다.

명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다. 다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.

| 앰비언트 값                     | 명시적 값                        | 결과                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

경로에 매개 변수에 해당하지 않고 값이 명시적으로 제공된 기본값이 있는 경우 기본값과 일치해야 합니다.

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

`controller` 및 `action`에 대해 일치하는 값이 제공되는 경우에만 링크 생성에서 이 경로에 대한 링크를 생성합니다.

## <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트(예: `[Route("/x{token}y")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다. 복잡한 세그먼트 일치 방법에 대한 자세한 설명은 [이 코드](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)를 참조하세요. [코드 샘플](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)은 ASP.NET Core에서 사용되지 않지만 복잡한 세그먼트에 대한 적절한 설명을 제공합니다.

::: moniker-end
