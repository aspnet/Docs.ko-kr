---
title: ASP.NET Core 5.0의 새로운 기능
author: rick-anderson
description: ASP.NET Core 5.0의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: d7ffcb67637593ab2909885a9e1f6de74a78361b
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855497"
---
# <a name="whats-new-in-aspnet-core-50"></a>ASP.NET Core 5.0의 새로운 기능

이 문서에서는 ASP.NET Core 5.0의 가장 중요한 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC 및 Razor 개선 사항

### <a name="model-binding-datetime-as-utc"></a>모델 바인딩 DateTime(UTC)

이제 모델 바인딩에서 UTC 시간 문자열을 `DateTime`에 바인딩하는 것을 지원합니다. 요청에 UTC 시간 문자열이 포함된 경우 모델 바인딩은 이를 UTC `DateTime`에 바인딩합니다. 예를 들어 다음 시간 문자열은 UTC `DateTime`에 바인딩됩니다. `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>C# 9 레코드 형식으로 모델 바인딩 및 유효성 검사

MVC 컨트롤러 또는 Razor 페이지에서 [C# 9 레코드 형식](/dotnet/csharp/whats-new/csharp-9#record-types)을 모델 바인딩에 사용할 수 있습니다. 레코드 형식은 네트워크를 통해 전송되는 데이터를 모델링하는 좋은 방법입니다.

예를 들어 다음 `PersonController`는 모델 바인딩과 양식 유효성 검사에 `Person` 레코드 형식을 사용합니다.

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

`Person/Index.cshtml` 파일은 다음과 같습니다.

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>DynamicRouteValueTransformer 개선 사항

ASP.NET Core 3.1에 사용자 지정 엔드포인트를 사용하여 MVC 컨트롤러 작업 또는 Razor 페이지를 동적으로 선택하는 방법으로 <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer>가 도입되었습니다. ASP.NET Core 5.0 앱은 `DynamicRouteValueTransformer`에 상태를 전달하고, 선택한 엔드포인트 집합을 필터링할 수 있습니다.

### <a name="miscellaneous"></a>기타

* [Compare](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 특성은 Razor 페이지 모델에서 속성에 적용될 수 있습니다.
* 본문에서 바인딩된 매개 변수와 속성은 기본적으로 필수 요소로 간주됩니다. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web API

### <a name="openapi-specification-on-by-default"></a>기본적으로 설정된 OpenAPI 사양

[OpenAPI 사양](http://spec.openapis.org/oas/v3.0.3)은 HTTP API를 설명하고 복잡한 비즈니스 프로세스에 통합하거나 타사와 통합하기 위한 산업 표준입니다. OpenAPI는 모든 클라우드 공급자와 많은 API 레지스트리에서 광범위하게 지원됩니다. 웹 API에서 OpenAPI 문서를 내보내는 앱에는 이러한 API를 사용할 수 있는 새로운 기회가 많습니다. 오픈 소스 프로젝트 [Swashbuckle AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)의 유지 관리자와 제휴 관계를 맺은 ASP.NET Core API 템플릿에는 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)에 대한 NuGet 종속성이 포함되어 있습니다. Swashbuckle은 OpenAPI 문서를 동적으로 내보내는 인기 있는 오픈 소스 NuGet 패키지입니다. Swashbuckle은 API 컨트롤러를 자기 분석하고 런타임에 또는 Swashbuckle CLI를 사용하여 빌드 시 OpenAPI 문서를 생성하여 이를 수행합니다.

ASP.NET Core 5.0에서 웹 API 템플릿은 기본적으로 OpenAPI 지원을 사용하도록 설정합니다. OpenAPI를 사용하지 않으려면:

* 명령줄에서:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Visual Studio에서: **OpenAPI 지원 사용** 선택을 취소합니다.

웹 API 프로젝트용으로 만들어진 모든 *.csproj* 파일에는 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet 패키지 참조가 포함되어 있습니다.

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

생성된 템플릿 코드에는 OpenAPI 문서 생성을 활성화하는 `Startup.ConfigureServices`의 코드가 포함되어 있습니다.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

`Startup.Configure` 메서드는 다음을 사용하도록 설정하는 Swashbuckle 미들웨어를 추가합니다.

* 문서 생성 프로세스.
* 기본적으로 개발 모드의 Swagger UI 페이지.

템플릿에서 생성된 코드는 프로덕션 환경에 게시할 때 실수로 API 설명을 노출하지 않습니다.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Azure API Management 가져오기

ASP.NET Core API 프로젝트에서 OpenAPI를 사용하도록 설정하는 경우 Visual Studio 2019 버전 16.8 이상 게시는 게시 흐름에서 추가 단계를 자동으로 제공합니다. [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs)를 사용하는 개발자는 게시 흐름 중에 API를 Azure API Management로 자동으로 가져올 수 있습니다.

![Azure API Management 가져오기와 게시 비교](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>웹 API 프로젝트의 시작 환경 향상

기본적으로 OpenAPI를 사용하도록 설정되어 웹 API 개발자의 앱 시작 환경(F5)이 크게 향상되었습니다. ASP.NET Core 5.0에서는 Swagger UI 페이지를 로드하도록 웹 API 템플릿이 미리 구성되어 있습니다. Swagger UI 페이지는 게시된 API에 추가된 설명서를 모두 제공하며, 한 번의 클릭으로 API를 테스트할 수 있습니다.

![swagger/index.html 보기](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>성능 개선

.NET 5의 경우 복잡한 UI 렌더링 및 JSON serialization에 특히 중점을 두어 Blazor WebAssembly 런타임 성능이 크게 향상되었습니다. 성능 테스트에서 .NET 5의 Blazor WebAssembly는 대부분의 시나리오에서 2~3배 더 빠릅니다. 자세한 내용은 [ASP.NET 블로그: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)을 참조하세요.

### <a name="css-isolation"></a>CSS 격리

Blazor는 이제 지정된 구성 요소로 범위가 지정된 CSS 스타일의 정의를 지원합니다. 구성 요소별 CSS 스타일을 사용하면 앱의 스타일을 쉽게 추론하고 전역 스타일의 의도하지 않은 부작용을 방지할 수 있습니다. 자세한 내용은 <xref:blazor/components/css-isolation>를 참조하세요.

### <a name="new-inputfile-component"></a>새 `InputFile` 구성 요소

`InputFile` 구성 요소를 사용하면 업로드를 위해 사용자가 선택한 하나 이상의 파일을 읽을 수 있습니다. 자세한 내용은 <xref:blazor/file-uploads>를 참조하세요.

### <a name="new-inputradio-and-inputradiogroup-components"></a>새 `InputRadio` 및 `InputRadioGroup` 구성 요소

Blazor에는 통합 유효성 검사를 통해 라디오 단추 그룹에 대한 데이터 바인딩을 간소화하는 기본 제공 `InputRadio` 및 `InputRadioGroup` 구성 요소가 있습니다. 자세한 내용은 <xref:blazor/forms-validation>를 참조하세요.

### <a name="component-virtualization"></a>구성 요소 가상화

Blazor 프레임워크의 기본 제공 가상화 지원을 사용하여 구성 요소 렌더링의 인식된 성능을 향상합니다. 자세한 내용은 <xref:blazor/components/virtualization>를 참조하세요.

### <a name="ontoggle-event-support"></a>`ontoggle` 이벤트 지원

Blazor 이벤트는 이제 `ontoggle` DOM 이벤트를 지원합니다. 자세한 내용은 <xref:blazor/components/event-handling#event-argument-types>를 참조하세요.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Blazor 앱에서 UI 포커스 설정

요소 참조에서 `FocusAsync` 편의 메서드를 사용하여 UI 포커스를 해당 요소로 설정합니다. 자세한 내용은 <xref:blazor/components/event-handling#focus-an-element>를 참조하세요.

### <a name="custom-validation-class-attributes"></a>사용자 지정 유효성 검사 클래스 특성

사용자 지정 유효성 검사 클래스 이름은 부트스트랩 같은 CSS 프레임워크와 통합하는 경우에 유용합니다. 자세한 내용은 <xref:blazor/forms-validation#custom-validation-class-attributes>를 참조하세요.

### <a name="iasyncdisposable-support"></a>IAsyncDisposable 지원

이제 Blazor 구성 요소가 할당된 리소스의 비동기 릴리스를 위한 <xref:System.IAsyncDisposable> 인터페이스를 지원합니다.

### <a name="javascript-isolation-and-object-references"></a> JavaScript 격리 및 개체 참조

Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다. 자세한 내용은 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>를 참조하세요.

### <a name="form-components-support-display-name"></a>양식 구성 요소가 표시 이름 지원

다음 기본 제공 구성 요소는 `DisplayName` 매개 변수를 사용하여 표시 이름을 지원합니다.

* `InputDate`
* `InputNumber`
* `InputSelect`

자세한 내용은 <xref:blazor/forms-validation#display-name-support>를 참조하세요.

### <a name="catch-all-route-parameters"></a>모든 경로 매개 변수 catch

여러 폴더 경계에서 경로를 캡처하는 catch-all 경로 매개 변수는 구성 요소에서 지원됩니다. 자세한 내용은 <xref:blazor/fundamentals/routing#catch-all-route-parameters>를 참조하세요.

### <a name="debugging-improvements"></a>디버깅 기능 향상

ASP.NET Core 5.0에서 Blazor WebAssembly 앱 디버깅이 개선되었습니다. 또한 이제 Mac용 Visual Studio에서 디버깅이 지원됩니다. 자세한 내용은 <xref:blazor/debug>를 참조하세요.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v2.0 및 MSAL v2.0

Blazor 보안이 이제 Microsoft Identity v2.0([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) 및 [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI))과 MSAL v2.0을 사용합니다. 자세한 내용은 [Blazor 보안 및 Identity 노드](xref:blazor/security/index) 토픽을 참조하세요.

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Blazor Server의 보호된 브라우저 스토리지

이제 Blazor Server 앱이 ASP.NET Core 데이터 보호를 사용하여 변조로부터 보호된 브라우저에 앱 상태 저장에 대한 기본 제공 지원을 사용할 수 있습니다. 로컬 브라우저 스토리지 또는 세션 스토리지에 데이터를 저장할 수 있습니다. 자세한 내용은 <xref:blazor/state-management>를 참조하세요.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly 미리 렌더링

여러 호스팅 모델에서 구성 요소 통합이 개선되고, 이제 Blazor WebAssembly 앱이 서버에서 출력을 미리 렌더링할 수 있습니다. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>자르기/연결 향상

Blazor WebAssembly는 빌드 중에 IL(중간 언어) 자르기/연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 잘라냅니다. ASP.NET Core 5.0 릴리스에서 Blazor WebAssembly는 추가 구성 옵션을 사용하여 향상된 자르기를 수행합니다. 자세한 내용은 <xref:blazor/host-and-deploy/configure-trimmer> 및 [자르기 옵션](/dotnet/core/deploying/trimming-options)을 참조하세요.

### <a name="browser-compatibility-analyzer"></a>브라우저 호환성 분석기

Blazor WebAssembly 앱은 전체 .NET API 노출 영역을 대상으로 하지만 일부 .NET API는 브라우저 샌드박스 제약 조건으로 인해 WebAssembly에서 지원되지 않습니다. 지원되지 않는 API는 WebAssembly에서 실행될 때 <xref:System.PlatformNotSupportedException>을 throw합니다. 플랫폼 호환성 분석기는 앱이 앱의 대상 플랫폼에서 지원하지 않는 API를 사용하는 경우 개발자에게 경고합니다. 자세한 내용은 <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>를 참조하세요.

### <a name="lazy-load-assemblies"></a>어셈블리 지연 로드

필요할 때까지 일부 앱 어셈블리의 로드를 지연하여 Blazor WebAssembly 앱 시작 성능을 향상시킬 수 있습니다. 자세한 내용은 <xref:blazor/webassembly-lazy-load-assemblies>를 참조하세요.

### <a name="updated-globalization-support"></a>세계화 지원 업데이트

ICU(International Components for Unicode)를 기반으로 하는 세계화 지원을 Blazor WebAssembly에 사용할 수 있습니다. 자세한 내용은 <xref:blazor/globalization-localization>를 참조하세요.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/)에서 많은 성능 향상이 있었습니다. 자세한 내용은 [.NET 5의 gRPC 성능 향상](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)을 참조하세요.

추가 gRPC 정보는 <xref:grpc/index>를 참조하세요.

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR 허브 필터

ASP.NET SignalR에서 허브 파이프라인이라고 하는 SignalR 허브 필터는 허브 메서드가 호출되기 전과 후에 코드를 실행할 수 있는 기능입니다. 허브 메서드가 호출되기 전과 후에 코드를 실행하는 것은 미들웨어가 HTTP 요청 전후에 코드를 실행할 수 있는 것과 유사합니다. 일반적인 용도로는 로깅, 오류 처리, 인수 유효성 검사 등이 있습니다.

자세한 내용은 [ASP.NET Core에서 허브 필터 사용SignalR](xref:signalr/hub-filters)을 참조하세요.

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR 병렬 허브 호출

ASP.NET Core SignalR이 이제 병렬 허브 호출을 처리할 수 있습니다. 클라이언트가 한 번에 둘 이상의 허브 메서드를 호출할 수 있도록 기본 동작을 변경할 수 있습니다.

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>SignalR Java 클라이언트에 Messagepack 지원 추가

새 패키지인 [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)은 SignalR Java 클라이언트에 MessagePack 지원을 추가합니다. MessagePack 허브 프로토콜을 사용하려면 연결 작성기에 `.withHubProtocol(new MessagePackHubProtocol())`을 추가하세요.

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* 구성을 통해 다시 로드할 수 있는 엔드포인트: Kestrel은 [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A)에 전달된 구성 변경 내용을 검색하고, `reloadOnChange` 매개 변수가 `true`일 때 애플리케이션을 다시 시작할 필요 없이 기존 엔드포인트에 대한 바인딩을 해제하고 새 엔드포인트에 바인딩할 수 있습니다. 기본적으로 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> 또는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 사용할 경우 Kestrel은 `reloadOnChange`가 사용하도록 설정된 ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) 구성 하위 섹션에 바인딩됩니다. `KestrelServerOptions.Configure`를 수동으로 호출할 때 앱이 `reloadOnChange: true`를 전달해야 다시 로드할 수 있는 엔드포인트를 가져올 수 있습니다.
* HTTP/2 응답 헤더 향상. 자세한 내용은 [성능 개선](#performance-improvements)을 참조하세요.
* 소켓 전송에서 추가 엔드포인트 형식 지원: <xref:System.Net.Sockets?displayProperty=nameWithType>에 도입된 새 API에 더해 [Kestrel](xref:fundamentals/servers/kestrel)의 소켓 기본 전송은 기존 파일 핸들과 Unix 도메인 소켓에 대한 바인딩을 모두 허용합니다. 기존 파일 핸들에 대한 바인딩 지원으로 `libuv` 전송 없이 기존 `Systemd` 통합을 사용할 수 있습니다.
* Kestrel의 사용자 지정 헤더 디코딩: 앱은 UTF-8을 기본값으로 설정하는 대신 헤더 이름을 기반으로 들어오는 헤더를 해석하는 데 사용할 <xref:System.Text.Encoding>을 지정할 수 있습니다. 인증 체계, 계정 이름 및 인증 서명을 포함하는 문자열로 <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` 속성을 설정하여 사용할 인코딩을 지정합니다.
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>구성을 통한 Kestrel 엔드포인트별 옵션

[구성](xref:fundamentals/configuration/index)을 통한 Kestrel 엔드포인트별 옵션 구성에 대한 지원이 추가되었습니다. 엔드포인트별 구성에는 다음이 포함됩니다.

* HTTP 프로토콜 사용됨
* TLS 프로토콜 사용됨
* 인증서 선택됨
* 클라이언트 인증서 모드

구성에서는 지정된 서버 이름을 기준으로 선택되는 인증서를 지정할 수 있습니다. 서버 이름은 클라이언트에서 표시되는 TLS 프로토콜에 대한 SNI(서버 이름 표시) 확장의 일부입니다. Kestrel의 구성은 호스트 이름의 와일드카드 접두사도 지원합니다.

다음 예제는 구성 파일을 사용하여 엔드포인트별 옵션을 지정하는 방법을 보여 줍니다.

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

SNI(서버 이름 표시)는 SSL 협상의 일부로 가상 도메인을 포함하기 위한 TLS 확장입니다. 따라서 가상 도메인 이름, 즉 호스트 이름을 네트워크 엔드포인트 식별에 사용할 수 있습니다.

## <a name="performance-improvements"></a>성능 개선

### <a name="http2"></a>HTTP/2

* HTTP/2 코드 경로에서 할당 크게 축소.
* [Kestrel](xref:fundamentals/servers/kestrel)에서 HTTP/2 응답 헤더의 [HPack 동적 압축](https://tools.ietf.org/html/rfc7541) 지원. 자세한 내용은 [헤더 테이블 크기](xref:fundamentals/servers/kestrel#header-table-size) 및 [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)를 참조하세요.
* HTTP/2 PING 프레임 전송: HTTP/2에는 PING 프레임을 전송하여 유휴 연결이 계속해서 작동하는지 확인하는 메커니즘이 있습니다. 사용 가능한 연결을 보장하는 것은 gRPC 스트림과 같이 대체로 유휴 상태이면서 간헐적으로 활동을 보이는 수명이 긴 스트림 작업 시 특히 유용합니다. 앱은 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>에 제한을 설정하여 [Kestrel](xref:fundamentals/servers/kestrel)에서 주기적 PING 프레임을 보낼 수 있습니다.

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>컨테이너

.NET 5.0 이전에는 ASP.NET Core 앱용 *Dockerfile* 을 빌드하고 게시하려면 전체 .NET Core SDK 및 ASP.NET Core 이미지를 끌어와야 했습니다. 이번 릴리스에서는 SDK 이미지 끌어오기가 줄어들고, ASP.NET Core 이미지를 위해 끌어오는 바이트가 대부분 제거되었습니다. 자세한 내용은 [이 GitHub 문제 설명](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)을 참조하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Microsoft.Identity.Web을 사용한 Azure Active Directory 인증

이제 ASP.NET Core 프로젝트 템플릿이 <xref:Microsoft.Identity.Web?displayProperty=fullName>과 통합되어 [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)(Azure Active Directory)를 사용하여 인증을 처리합니다. [Microsoft.Identity.Web 패키지](https://www.nuget.org/packages/Microsoft.Identity.Web/)는 다음을 제공합니다.

* Azure AD를 통한 향상된 인증 환경.
* [Microsoft Graph](/graph/overview) 등 사용자를 대신하여 Azure 리소스에 액세스하는 더 쉬운 방법. 기본 로그인에서 시작하여 다중 테넌시, Azure API 사용, Microsoft Graph 사용, 자체 API 보호를 차례로 다루는 [Microsoft.Identity.Web 샘플](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)을 참조하세요. `Microsoft.Identity.Web`은 .NET 5와 함께 사용할 수 있습니다.

### <a name="allow-anonymous-access-to-an-endpoint"></a>엔드포인트에 대한 익명 액세스 허용

`AllowAnonymous` 확장 메서드는 엔드포인트에 대한 익명 액세스를 허용합니다.

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>권한 부여 오류의 사용자 지정 처리

이제 [권한 부여](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [미들웨어](xref:fundamentals/middleware/index)에 의해 호출되는 [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) 인터페이스로 인증 오류의 사용자 지정 처리가 더 쉬워집니다. 기본 구현은 동일하게 유지되지만 사용자 지정 처리기를 종속성 주입에 등록하면 권한 부여에 실패한 이유를 기준으로 사용자 지정 HTTP 응답을 허용할 수 있습니다. `IAuthorizationMiddlewareResultHandler` 사용을 보여 주는 [이 샘플](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs)을 참조하세요.

### <a name="authorization-when-using-endpoint-routing"></a>엔드포인트 라우팅 사용 시 권한 부여

이제 엔드포인트 라우팅을 사용할 때 권한 부여가 엔드포인트 인스턴스가 아닌 `HttpContext`를 수신합니다. 이를 통해 권한 부여 미들웨어는 <xref:Microsoft.AspNetCore.Http.Endpoint> 클래스를 통해 액세스할 수 없었던 `HttpContext`의 `RouteData` 및 기타 속성에 액세스할 수 있습니다. [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A)를 사용하여 컨텍스트에서 엔드포인트를 가져올 수 있습니다.

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Linux에서 Kerberos 인증 및 LDAP를 사용하여 역할 기반 액세스 제어

[Kerberos 인증 및 RBAC(역할 기반 액세스 제어)](xref:security/authentication/windowsauth#rbac)를 참조하세요.

## <a name="api-improvements"></a>API 개선 사항

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>HttpRequest 및 HttpResponse용 JSON 확장 메서드

새로운 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 및 `WriteAsJsonAsync` 확장 메서드를 사용하여 `HttpRequest` 및 `HttpResponse`에서 JSON 데이터를 읽고 쓸 수 있습니다. 이러한 확장 메서드는 [System.Text.Json](xref:System.Text.Json) 직렬 변환기를 사용하여 JSON 데이터를 처리합니다. 새 `HasJsonContentType` 확장 메서드는 요청에 JSON 콘텐츠 형식이 있는지 여부도 확인할 수 있습니다.

JSON 확장 메서드를 [엔드포인트 라우팅](xref:fundamentals/routing)과 결합하여 **라우팅 대상 코드** 라고 하는 프로그래밍 스타일로 JSON API를 만들 수 있습니다. 이것은 경량 방식으로 기본 JSON API를 만들려는 개발자에게 새롭게 제공되는 옵션입니다. 예를 들어 엔드포인트가 몇 개만 있는 웹앱은 ASP.NET Core MVC의 전체 기능이 아니라 라우팅 대상 코드를 사용하도록 선택할 수 있습니다.

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

새로운 JSON 확장 메서드와 라우팅 대상 코드에 대한 자세한 내용은 [이 .NET 쇼](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)를 참조하세요.

### <a name="systemdiagnosticsactivity"></a>System.Diagnostics.Activity

이제 <xref:System.Diagnostics.Activity?displayProperty=fullName>의 기본 형식은 W3C 형식입니다. 이렇게 하면 ASP.NET Core에서 분산 추적 지원이 기본적으로 상호 작용할 수 있는 프레임워크가 늘어납니다.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute>는 이제 이러한 매개 변수 또는 속성을 선택적인 것으로 간주할 수 있는 옵션 구성을 지원합니다.

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>기타 개선 사항

ASP.NET Core 어셈블리에 [null 허용 주석](/dotnet/csharp/nullable-references#attributes-describe-apis)이 적용되기 시작했습니다. .NET 5 프레임워크의 일반적인 공용 API 표면 대부분에 주석을 달 계획입니다. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>시작 클래스 활성화 제어

앱이 `Startup` 클래스 활성화 제어를 위한 팩터리 메서드를 제공할 수 있도록 하는 추가 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> 오버로드가 추가되었습니다. `Startup` 클래스 활성화 제어는 호스트와 함께 초기화되는 추가 매개 변수를 `Startup`에 전달하는 데 유용합니다.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>dotnet watch를 사용하여 자동 새로 고침

.NET 5에서는 [dotnet watch](xref:tutorials/dotnet-watch)를 ASP.NET Core 프로젝트에서 실행하면 기본 브라우저가 시작되고 코드를 변경할 때 브라우저가 자동으로 새로 고침됩니다. 즉, 다음이 가능합니다.

텍스트 편집기에서 ASP.NET Core 프로젝트를 엽니다.
* `dotnet watch`을 실행합니다.
* 도구가 앱 다시 빌드, 다시 시작, 다시 로드를 처리하는 동안 코드 변경에 집중하세요.

### <a name="console-logger-formatter"></a>콘솔 로거 포맷터

`Microsoft.Extensions.Logging` 라이브러리의 콘솔 로그 공급자가 개선되었습니다. 이제 개발자는 사용자 지정 `ConsoleFormatter`를 구현하여 콘솔 출력의 서식 및 색 지정을 완전히 제어할 수 있습니다. 포맷터 API는 VT-100 이스케이프 시퀀스의 하위 집합을 구현하여 다양한 서식 지정이 가능합니다. VT-100은 대부분의 최신 터미널에서 지원됩니다. 콘솔 로거는 지원되지 않는 터미널에서 이스케이프 시퀀스를 구문 분석할 수 있으므로 개발자는 모든 터미널의 단일 포맷터를 작성할 수 있습니다.

### <a name="json-console-logger"></a>JSON 콘솔 로거

사용자 지정 포맷터 지원 외에 구조화된 JSON 로그를 콘솔로 내보내는 기본 제공 JSON 포맷터도 추가했습니다. 다음 코드는 기본 로거에서 JSON으로 전환하는 방법을 보여 줍니다.

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

콘솔로 내보내는 로그 메시지는 JSON 형식입니다.

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
