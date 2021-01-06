---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
uid: fundamentals/http-requests
ms.openlocfilehash: 34c35daac3da845bac9156fe96078df7902a4cd0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059496"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기

::: moniker range=">= aspnetcore-3.0"

작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://github.com/serpent5)

앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다. `IHttpClientFactory`는 다음과 같은 이점을 제공합니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다. 예를 들어, *github* 라는 클라이언트를 [GitHub](https://github.com/)에 액세스하도록 등록 및 구성할 수 있습니다. 일반적인 액세스를 위한 기본 클라이언트를 등록할 수 있습니다.
* `HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화합니다. Polly 기반 미들웨어에 대한 확장을 제공하여 `HttpClient`에서의 핸들러 위임을 활용합니다.
* 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다. 자동 관리가 `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS(Domain Name System) 문제를 방지해 줍니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

이 항목 버전의 샘플 코드는 <xref:System.Text.Json>을 사용하여 HTTP 응답으로 반환된 JSON 콘텐츠를 역직렬화합니다. `Json.NET` 및 `ReadAsAsync<T>`를 사용하는 샘플의 경우, 버전 선택기를 사용하여 이 항목의 2.x 버전을 선택하세요.

## <a name="consumption-patterns"></a>사용 패턴

앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.

* [기본적인 사용 방법](#basic-usage)
* [명명된 클라이언트](#named-clients)
* [형식화된 클라이언트](#typed-clients)
* [생성된 클라이언트](#generated-clients)

가장 좋은 방법은 앱의 요구 사항에 따라서 달라집니다.

### <a name="basic-usage"></a>기본적인 사용 방법

`AddHttpClient`를 호출하여 `IHttpClientFactory`를 등록할 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

[종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용하여 `IHttpClientFactory`를 요청할 수 있습니다. 다음 코드는 `IHttpClientFactory`를 사용하여 `HttpClient` 인스턴스를 만듭니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

앞서 나온 예제에서와 같이 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링하는 좋은 방법입니다. `HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다. 기존 앱에서 `HttpClient` 인스턴스가 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.

### <a name="named-clients"></a>명명된 클라이언트

명명된 클라이언트는 다음과 같은 경우에 적합합니다.

* 앱에서 `HttpClient`를 서로 다른 곳에서 여러 번 사용해야 합니다.
* 여러 `HttpClient`가 서로 다른 구성을 갖습니다.

명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

위의 코드에서 클라이언트는 다음을 사용하여 구성됩니다.

* 기본 주소 `https://api.github.com/`.
* GitHub API를 사용하는 데 필요한 헤더 2개.

#### <a name="createclient"></a>CreateClient

<xref:System.Net.Http.IHttpClientFactory.CreateClient*>가 호출될 때마다

* `HttpClient`의 새 인스턴스가 만들어집니다.
* 구성 작업이 호출됩니다.

명명된 클라이언트를 만들려면 `CreateClient`로 이름을 전달합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다. 클라이언트에 대해 구성된 기본 주소가 사용되었므로 코드는 경로만 전달할 수 있습니다.

### <a name="typed-clients"></a>형식화된 클라이언트

형식화된 클라이언트:

* 문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.
* 클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.
* 특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다. 예를 들어, 다음과 같은 경우에 단일 형식화된 클라이언트를 사용할 수 있습니다.
  * 단일 백 엔드 엔드포인트에 대해.
  * 엔드포인트를 처리하는 모든 로직을 캡슐화하기 위해.
* DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.

형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

위의 코드에서

* 구성이 형식화된 클라이언트로 이동되었습니다.
* `HttpClient` 개체는 공용 속성으로 노출됩니다.

`HttpClient` 기능을 노출하는 API 특정 메서드를 만들 수 있습니다. 예를 들어, `GetAspNetDocsIssues` 메서드는 열린 문제를 검색하는 코드를 캡슐화합니다.

다음 코드는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*>를 호출하여 형식화된 클라이언트 클래스를 등록합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다. 위의 코드에서 `AddHttpClient`는 `GitHubService`를 임시 서비스로 등록합니다. 이 등록에서는 팩터리 메서드를 사용하여 다음을 수행합니다.

1. `HttpClient`의 인스턴스를 만듭니다.
1. `HttpClient`의 인스턴스를 생성자에 전달하여 `GitHubService`의 인스턴스를 만듭니다.

형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

형식화된 클라이언트 내에서 `HttpClient`를 캡슐화할 수 있습니다. 속성으로 노출하는 대신 내부적으로 `HttpClient` 인스턴스를 호출하는 메서드를 정의합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

위의 코드에서는 `HttpClient`가 private 필드로 저장됩니다. `HttpClient`에 대한 액세스는 public `GetRepos` 메서드에 의해 이루어집니다.

### <a name="generated-clients"></a>생성된 클라이언트

`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 타사 라이브러리와 함께 사용할 수 있습니다. Refit은 .NET용 REST 라이브러리입니다. REST API를 라이브 인터페이스로 변환합니다. 인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.

외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a>POST, PUT 및 DELETE 요청 수행

위의 예제에서 모든 HTTP 요청은 GET HTTP 동사를 사용합니다. `HttpClient`는 다음을 비롯한 다른 HTTP 동사도 지원합니다.

* POST
* PUT
* Delete
* 패치

지원되는 HTTP 동사의 전체 목록은 <xref:System.Net.Http.HttpMethod>를 참조하세요.

다음 예제에서는 HTTP POST 요청을 수행하는 방법을 보여 줍니다.

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

위의 코드에서 `CreateItemAsync` 메서드는 다음을 수행합니다.

* `System.Text.Json`을 사용하여 `TodoItem` 매개 변수를 JSON으로 serialize합니다. <xref:System.Text.Json.JsonSerializerOptions>의 인스턴스를 사용하여 serialization 프로세스를 구성합니다.
* HTTP 요청의 본문에서 전송하기 위해 serialize된 JSON을 패키지할 <xref:System.Net.Http.StringContent>의 인스턴스를 만듭니다.
* <xref:System.Net.Http.HttpClient.PostAsync%2A>를 호출하여 JSON 콘텐츠를 지정된 URL로 보냅니다. [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)에 추가되는 상대 URL입니다.
* 응답 상태 코드가 성공을 나타내지 않을 경우 <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>를 호출하여 예외를 throw합니다.

`HttpClient`는 다른 형식의 콘텐츠도 지원합니다. 예를 들어 <xref:System.Net.Http.MultipartContent> 및 <xref:System.Net.Http.StreamContent>를 지정합니다. 지원되는 콘텐츠의 전체 목록은 <xref:System.Net.Http.HttpContent>를 참조하세요.

다음 예제에서는 HTTP PUT 요청을 보여 줍니다.

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

앞의 코드는 POST 예제와 매우 비슷합니다. `SaveItemAsync` 메서드는 `PostAsync` 대신 <xref:System.Net.Http.HttpClient.PutAsync%2A>를 호출합니다.

다음 예제에서는 HTTP DELETE 요청을 보여 줍니다.

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

위의 코드에서 `DeleteItemAsync` 메서드는 <xref:System.Net.Http.HttpClient.DeleteAsync%2A>를 호출합니다. HTTP DELETE 요청은 일반적으로 본문을 포함하지 않기 때문에 `DeleteAsync` 메서드는 `HttpContent`의 인스턴스를 허용하는 오버로드를 제공하지 않습니다.

`HttpClient`에 다른 HTTP 동사를 사용하는 방법에 대한 자세한 내용은 <xref:System.Net.Http.HttpClient>를 참조하세요.

## <a name="outgoing-request-middleware"></a>나가는 요청 미들웨어

`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 있습니다. `IHttpClientFactory`:

* 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.
* 나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다. 이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다. 이 패턴은

  * ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.
  * 다음과 같은 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.

    * 캐싱
    * 오류 처리
    * 직렬화
    * 로깅

위임 처리기를 만들려면 다음을 수행합니다.

* <xref:System.Net.Http.DelegatingHandler>를 파생시킵니다.
* <xref:System.Net.Http.DelegatingHandler.SendAsync*>을 재정의합니다. 파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

위의 코드는 `X-API-KEY` 헤더가 요청에 있는지 여부를 확인합니다. `X-API-KEY`가 누락된 경우 <xref:System.Net.HttpStatusCode.BadRequest>가 반환됩니다.

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>가 있는 `HttpClient`의 구성에는 둘 이상의 핸들러가 추가될 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다. `IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다. 처리기는 모든 범위의 서비스에서 사용할 수 있습니다. 처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.

일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.

여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다. 각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.

* [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties)를 사용하여 데이터를 처리기로 전달합니다.
* <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>를 사용하여 현재 요청에 액세스합니다.
* 사용자 지정 <xref:System.Threading.AsyncLocal`1> 스토리지 개체를 만들어 데이터를 전달합니다.

## <a name="use-polly-based-handlers"></a>Polly 기반 처리기 사용

`IHttpClientFactory`는 타사 라이브러리 [Polly](https://github.com/App-vNext/Polly)와 통합됩니다. Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다. 개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.

구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다. Polly 확장은 클라이언트에 Polly 기반 처리기를 추가하는 것을 지원합니다. Polly를 사용하려면 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지가 필요합니다.

### <a name="handle-transient-faults"></a>일시적인 오류 처리

오류는 외부 HTTP 호출이 일시적인 경우 발생합니다. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용하면 정책에서 일시적인 오류를 처리하도록 정의할 수 있습니다. `AddTransientHttpErrorPolicy`를 사용하여 구성한 정책은 다음과 같은 응답을 처리합니다.

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy`는 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다. 실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.

### <a name="dynamically-select-policies"></a>동적으로 정책 선택

Polly 기반 처리기를 추가하는 데 사용할 수 있는 확장 메서드(예: <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>)가 제공됩니다. 다음 `AddPolicyHandler` 오버로드는 요청을 검사하여 어느 정책을 적용할지 결정합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다. 다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.

### <a name="add-multiple-polly-handlers"></a>여러 Polly 처리기 추가

Polly 정책을 중첩하는 것은 일반적입니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

앞의 예제에서:

* 두 개의 처리기가 추가됩니다.
* 첫 번째 처리기는 재시도 정책을 추가하기 위해 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용합니다. 실패한 요청은 최대 세 번까지 다시 시도됩니다.
* 두 번째 호출 `AddTransientHttpErrorPolicy`는 회로 차단기 정책을 추가합니다. 5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다. 회로 차단기 정책은 상태를 저장합니다. 이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.

### <a name="add-policies-from-the-polly-registry"></a>Polly 레지스트리로부터 정책 추가

정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.

다음 코드에서:

* "regular" 정책과 "long" 정책이 추가됩니다.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>가 레지스트리로부터 "regular" 정책과 "long" 정책을 추가합니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

`IHttpClientFactory` 및 Polly 통합에 대한 자세한 내용은 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)를 참조하세요.

## <a name="httpclient-and-lifetime-management"></a>HttpClient 및 수명 관리

`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다. 명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 생성됩니다. 팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.

`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다. 수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.

일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다. 필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다. 또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS(Domain Name System) 변경에 대응하는 것을 막을 수 있습니다.

기본 처리기 수명은 2분입니다. 명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 **않은** .NET 개체로 간주할 수 있습니다. 삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다. `IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.

장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다. 이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory의 대안

DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.

* `HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.
* 정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.

수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.

- 앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.
- DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.
- 필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.

위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.

- `SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다. 이와 같이 공유하면 소켓이 소모되지 않도록 합니다.
- 오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.

### <a name="no-loccookies"></a>Cookies

풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다. 예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다. cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.

 - 자동 cookie 처리 사용 안 함
 - `IHttpClientFactory` 방지

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>로깅

`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다. 기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다. 요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.

각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다. 예를 들어, *MyNamedClient* 라는 클라이언트는 “System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler”의 범주를 사용하여 메시지를 기록합니다. *LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다. 요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다. 응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.

로깅은 요청 처리기 파이프라인 내부에서도 발생합니다. *MyNamedClient* 예제에서 해당 메시지는 로그 범주 “System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler”에 대해 기록됩니다. 요청의 경우 이는 요청이 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다. 응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.

파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다. 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.

로그 범주에 클라이언트의 이름을 포함하면 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 구성

클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.

`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다. 대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>콘솔 앱에서 IHttpClientFactory 사용

콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

다음 예제에서는

* <xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.
* `MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다. `HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.
* `Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>헤더 전파 미들웨어

헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 ASP.NET Core 미들웨어입니다. 헤더 전파를 사용하려면 다음을 수행합니다.

* [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 패키지를 참조합니다.
* `Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* 클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>추가 자료

* [HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [회로 차단기 패턴 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [.NET에서 JSON을 직렬화 및 역직렬화하는 방법](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)

앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다. 이는 다음과 같은 이점을 제공합니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다. 예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다. 기본 클라이언트는 다른 용도로 등록할 수 있습니다.
* `HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.
* `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>사용 패턴

앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.

* [기본적인 사용 방법](#basic-usage)
* [명명된 클라이언트](#named-clients)
* [형식화된 클라이언트](#typed-clients)
* [생성된 클라이언트](#generated-clients)

어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다. 가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.

### <a name="basic-usage"></a>기본적인 사용 방법

`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다. `IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다. `HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다. `HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.

### <a name="named-clients"></a>명명된 클라이언트

앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트** 를 사용하는 것입니다. 명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이라는 이름을 제공합니다. 이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.

`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.

명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다. 만들 클라이언트의 이름을 지정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다. 클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.

### <a name="typed-clients"></a>형식화된 클라이언트

형식화된 클라이언트:

* 문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.
* 클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.
* 특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다. 예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.
* DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.

형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다. `HttpClient` 개체는 공용 속성으로 노출됩니다. `HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다. `GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.

형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다. 형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다. 속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다. 외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.

### <a name="generated-clients"></a>생성된 클라이언트

`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다. Refit은 .NET용 REST 라이브러리입니다. REST API를 라이브 인터페이스로 변환합니다. 인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.

외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>나가는 요청 미들웨어

`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다. `IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다. 나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다. 이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다. 이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다. 이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.

처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다. 파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

위의 코드에서는 기본 처리기를 정의합니다. `X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다. 헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.

등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다. 이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다. `IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다. 처리기는 모든 범위의 서비스에서 사용할 수 있습니다. 처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.

일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.

여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다. 각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.

* `HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.
* `IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.
* 사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.

## <a name="use-polly-based-handlers"></a>Polly 기반 처리기 사용

`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다. Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다. 개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.

구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다. Polly 확장은:

* 클라이언트에 Polly 기반 처리기 추가를 지원합니다.
* [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다. 이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.

### <a name="handle-transient-faults"></a>일시적인 오류 처리

가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다. 일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다. 이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.

`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다. 이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다. 실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.

### <a name="dynamically-select-policies"></a>동적으로 정책 선택

Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다. 이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다. 한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다. 다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.

### <a name="add-multiple-polly-handlers"></a>여러 Polly 처리기 추가

향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

위의 예제에서는 두 개의 처리기가 추가됩니다. 첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다. 실패한 요청은 최대 세 번까지 다시 시도됩니다. `AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다. 5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다. 회로 차단기 정책은 상태를 저장합니다. 이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.

### <a name="add-policies-from-the-polly-registry"></a>Polly 레지스트리로부터 정책 추가

정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다. 레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다. 레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.

`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.

## <a name="httpclient-and-lifetime-management"></a>HttpClient 및 수명 관리

`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다. 명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다. 팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.

`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다. 수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.

일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다. 필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다. 또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.

기본 처리기 수명은 2분입니다. 명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다. 이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

클라이언트의 삭제는 불필요합니다. 삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다. `IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다. `HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.

장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다. 이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory의 대안

DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.

* `HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.
* 정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.

수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.

- 앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.
- DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.
- 필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.

위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.

- `SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다. 이와 같이 공유하면 소켓이 소모되지 않도록 합니다.
- 오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.

### <a name="no-loccookies"></a>Cookies

풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다. 예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다. cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.

 - 자동 cookie 처리 사용 안 함
 - `IHttpClientFactory` 방지

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>로깅

`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다. 기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다. 요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.

각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다. 예를 들어, *MyNamedClient* 라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다. *LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다. 요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다. 응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.

로깅은 요청 처리기 파이프라인 내부에서도 발생합니다. *MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다. 요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다. 응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.

파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다. 예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.

로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 구성

클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.

`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다. 대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>콘솔 앱에서 IHttpClientFactory 사용

콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

다음 예제에서는

* <xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.
* `MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다. `HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.
* `Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>추가 자료

* [HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [회로 차단기 패턴 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)

앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다. 이는 다음과 같은 이점을 제공합니다.

* 논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다. 예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다. 기본 클라이언트는 다른 용도로 등록할 수 있습니다.
* `HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.
* `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.
* 팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

.NET Framework를 대상으로 하는 프로젝트는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다. .NET Core를 대상으로 하고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트는 이미 `Microsoft.Extensions.Http` 패키지를 포함하고 있습니다.

## <a name="consumption-patterns"></a>사용 패턴

앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.

* [기본적인 사용 방법](#basic-usage)
* [명명된 클라이언트](#named-clients)
* [형식화된 클라이언트](#typed-clients)
* [생성된 클라이언트](#generated-clients)

어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다. 가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.

### <a name="basic-usage"></a>기본적인 사용 방법

`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다. `IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다. `HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다. `HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.

### <a name="named-clients"></a>명명된 클라이언트

앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트** 를 사용하는 것입니다. 명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이라는 이름을 제공합니다. 이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.

`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.

명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다. 만들 클라이언트의 이름을 지정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다. 클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.

### <a name="typed-clients"></a>형식화된 클라이언트

형식화된 클라이언트:

* 문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.
* 클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.
* 특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다. 예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.
* DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.

형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다. `HttpClient` 개체는 공용 속성으로 노출됩니다. `HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다. `GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.

형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다. 형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다. 속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다. 외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.

### <a name="generated-clients"></a>생성된 클라이언트

`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다. Refit은 .NET용 REST 라이브러리입니다. REST API를 라이브 인터페이스로 변환합니다. 인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.

외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>나가는 요청 미들웨어

`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다. `IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다. 나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다. 이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다. 이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다. 이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.

처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다. 파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

위의 코드에서는 기본 처리기를 정의합니다. `X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다. 헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.

등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다. 이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다. 처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록 **되어야** 합니다. 처리기가 범위 지정 서비스로 등록되고 처리기가 종속된 모든 서비스는 삭제 가능합니다.

* 처리기가 범위를 벗어나기 전에 처리기의 서비스를 삭제할 수 있습니다.
* 처리기 서비스를 삭제하면 처리기가 실패합니다.

일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.

실행해야 하는 순서에 따라 여러 처리기를 등록할 수 있습니다. 각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.

* `HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.
* `IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.
* 사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.

## <a name="use-polly-based-handlers"></a>Polly 기반 처리기 사용

`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다. Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다. 개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.

구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다. Polly 확장은:

* 클라이언트에 Polly 기반 처리기 추가를 지원합니다.
* [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다. 이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.

### <a name="handle-transient-faults"></a>일시적인 오류 처리

가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다. 일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다. 이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.

`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다. 이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다. 실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.

### <a name="dynamically-select-policies"></a>동적으로 정책 선택

Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다. 이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다. 한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다. 다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.

### <a name="add-multiple-polly-handlers"></a>여러 Polly 처리기 추가

향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

위의 예제에서는 두 개의 처리기가 추가됩니다. 첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다. 실패한 요청은 최대 세 번까지 다시 시도됩니다. `AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다. 5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다. 회로 차단기 정책은 상태를 저장합니다. 이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.

### <a name="add-policies-from-the-polly-registry"></a>Polly 레지스트리로부터 정책 추가

정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다. 레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다. 레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.

`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.

## <a name="httpclient-and-lifetime-management"></a>HttpClient 및 수명 관리

`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다. 명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다. 팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.

`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다. 수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.

일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다. 필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다. 또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.

기본 처리기 수명은 2분입니다. 명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다. 이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

클라이언트의 삭제는 불필요합니다. 삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다. `IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다. `HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.

장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다. 이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory의 대안

DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.

* `HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.
* 정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.

수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.

- 앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.
- DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.
- 필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.

위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.

- `SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다. 이와 같이 공유하면 소켓이 소모되지 않도록 합니다.
- 오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.

### <a name="no-loccookies"></a>Cookies

풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다. 예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다. cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.

 - 자동 cookie 처리 사용 안 함
 - `IHttpClientFactory` 방지

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>로깅

`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다. 기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다. 요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.

각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다. 예를 들어, *MyNamedClient* 라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다. *LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다. 요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다. 응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.

로깅은 요청 처리기 파이프라인 내부에서도 발생합니다. *MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다. 요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다. 응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.

파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다. 예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.

로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 구성

클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.

`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다. 대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>콘솔 앱에서 IHttpClientFactory 사용

콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

다음 예제에서는

* <xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.
* `MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다. `HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.
* `Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>헤더 전파 미들웨어

헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 커뮤니티 지원 미들웨어입니다. 헤더 전파를 사용하려면 다음을 수행합니다.

* [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 패키지의 커뮤니티 지원 포트를 참조합니다. ASP.NET Core 3.1 이상은 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)을 지원합니다.

* `Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* 클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>추가 자료

* [HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [회로 차단기 패턴 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
