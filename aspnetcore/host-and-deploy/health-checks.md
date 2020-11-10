---
title: ASP.NET Core의 상태 검사
author: rick-anderson
description: 앱 및 데이터베이스와 같은 ASP.NET Core 인프라의 상태 검사를 설정하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 32b7a4c6722ba45ba998f9430f5d6da6ddca53f9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058664"
---
# <a name="health-checks-in-aspnet-core"></a>ASP.NET Core의 상태 검사

작성자: [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.

상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다. 상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.

* 상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다. 예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다. 부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.
* 메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.
* 상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다. 지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다. 샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다. 앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다. 모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.

[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지는 ASP.NET Core 앱에 대해 암시적으로 참조됩니다. Entity Framework Core를 사용하여 상태 검사를 수행하려면 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 패키지에 패키지 참조를 추가합니다.

샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다. [데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다. [DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다. 데이터베이스 시나리오를 탐색하려면 샘플 앱:

* 데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.
* 해당 프로젝트 파일에 다음 패키지 참조가 있습니다.
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다. 샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다. 자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.

## <a name="basic-health-probe"></a>기본 상태 프로브

많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성( *활동성* )을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.

기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다. 기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다. 상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다. 기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. `Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.

샘플 앱에서 상태 검사 엔드포인트는 `/health`( *BasicStartup.cs* )에 만들어집니다.

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker 예

[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>상태 검사 만들기

상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다. 결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.

다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다. 상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다. 다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다. `healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>상태 검사 서비스 등록

`ExampleHealthCheck` 형식은 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>으로 상태 검사 서비스에 추가됩니다.

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다. 오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다. 이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.

*태그* 를 사용하여 상태 검사를 필터링할 수 있습니다( [필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다. 다음 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*>를 호출하여 상태 검사 구현에 인수를 전달합니다. 다음 예제에서 `TestHealthCheckWithArgs`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>가 호출될 때 사용할 정수 및 문자열을 허용합니다.

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs`는 구현에 전달된 정수 및 문자열로 `AddTypeActivatedCheck`를 호출하여 등록됩니다.

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>상태 검사 라우팅 사용

`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 엔드포인트 작성기에 `MapHealthChecks`를 호출합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>호스트 필요

`RequireHost`를 호출하여 상태 검사 엔드포인트에 대해 허용된 호스트를 하나 이상 지정합니다. 호스트는 punycode가 아닌 유니코드여야 하고 포트를 포함할 수 있습니다. 컬렉션을 제공하지 않으면 모든 호스트가 허용됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.

### <a name="require-authorization"></a>권한 부여 필요

`RequireAuthorization`을 호출하여 상태 검사 요청 엔드포인트에서 권한 부여 미들웨어를 실행합니다. `RequireAuthorization` 오버로드는 하나 이상의 권한 부여 정책을 허용합니다. 정책을 제공하지 않으면 기본 권한 부여 정책이 사용됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>원본 간 요청(CORS) 사용

브라우저에서 수동으로 상태 검사를 수행하는 것이 일반적이지는 않지만, 상태 검사 엔드포인트에 `RequireCors`를 호출하여 CORS 미들웨어를 사용하도록 설정할 수 있습니다. `RequireCors` 오버로드는 CORS 정책 작성기 대리자(`CorsPolicyBuilder`) 또는 정책 이름을 허용합니다. 정책을 제공하지 않으면 기본 CORS 정책이 사용됩니다. 자세한 내용은 <xref:security/cors>를 참조하세요.

## <a name="health-check-options"></a>상태 검사 옵션

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.

* [상태 검사 필터링](#filter-health-checks)
* [HTTP 상태 코드 사용자 지정](#customize-the-http-status-code)
* [캐시 헤더 표시 안 함](#suppress-cache-headers)
* [출력 사용자 지정](#customize-output)

### <a name="filter-health-checks"></a>상태 검사 필터링

기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다. 상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다. 다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.

`Startup.ConfigureServices`의 경우

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

`Startup.Configure`에서 `Predicate`는 'Bar' 상태 검사를 필터링합니다. Foo 및 Baz만 실행됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>HTTP 상태 코드 사용자 지정

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다. 다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다. 요구 사항에 맞게 상태 코드 값을 변경합니다.

`Startup.Configure`의 경우

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>캐시 헤더 표시 안 함

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다. 값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다. 값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.

`Startup.Configure`의 경우

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>출력 사용자 지정

`Startup.Configure`에서 [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 옵션을 응답을 쓰기 위한 대리자로 설정합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다. 다음 사용자 지정 대리자는 사용자 지정 JSON 응답을 출력합니다.

샘플 앱의 첫 번째 예제에서는 <xref:System.Text.Json?displayProperty=fullName>를 사용하는 방법을 보여 줍니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

두 번째 예제에서는 [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json/)을 사용하는 방법을 보여 줍니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

샘플 앱에서 `WriteResponse` 버전의 `Newtonsoft.Json`을 사용할 수 있도록 *CustomWriterStartup.cs* 의 `SYSTEM_TEXT_JSON` [전처리기 지시문](xref:index#preprocessor-directives-in-sample-code)을 주석 처리합니다.

상태 검사 API는 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다. 앞의 예제에서 응답을 필요에 따라 사용자 지정합니다. `System.Text.Json`을 사용한 역직렬화에 대한 자세한 내용은 [.NET에서 JSON을 직렬화 및 역직렬화하는 방법](/dotnet/standard/serialization/system-text-json-how-to)을 참조하세요.

## <a name="database-probe"></a>데이터베이스 프로브

상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.

샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다. `AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.

> [!WARNING]
> 쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다. 쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다. 대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다. 간단히 데이터베이스에 연결하는 정도로도 충분합니다. 쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.

[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.

샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다. 앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. 샘플 앱은 데이터베이스의 연결 문자열( *DbHealthStartup.cs* )을 사용하여 `AddSqlServer` 메서드를 호출합니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="entity-framework-core-dbcontext-probe"></a>Entity Framework Core DbContext 프로브

`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다. `DbContext` 검사는 다음과 같은 앱에서 지원됩니다.

* [EF(Entity Framework) Core](/ef/core/)를 사용합니다.
* [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.

`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다. `DbContext`는 메서드에 `TContext`로 제공됩니다. 오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.

기본적으로 다음과 같습니다.

* `DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다. `AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.
* 상태 검사의 이름은 `TContext` 형식의 이름입니다.

샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다( *DbContextHealthStartup.cs* ).

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다. 데이터베이스가 있으면 삭제합니다.

명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario dbcontext
```

앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다. 데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.

```
Unhealthy
```

데이터베이스를 만드는 샘플 앱을 트리거합니다. `/createdatabase`에 요청합니다. 앱은 다음과 같이 응답합니다.

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` 엔드포인트에 요청합니다. 데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.

```
Healthy
```

데이터베이스를 삭제하는 샘플 앱을 트리거합니다. `/deletedatabase`에 요청합니다. 앱은 다음과 같이 응답합니다.

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` 엔드포인트에 요청합니다. 앱은 다음과 같이 비정상 응답을 제공합니다.

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>별도의 준비 상태 및 활동성 프로브

일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.

* ‘준비 상태’는 앱이 정상적으로 실행 중이지만 요청을 받을 준비가 되지 않았음을 나타냅니다.
* ‘활동성’은 앱의 작동이 중단되어 다시 시작해야 함을 나타냅니다.

다음 예제를 참조하세요. 앱이 요청을 처리할 준비가 되려면 먼저 대용량 구성 파일을 다운로드해야 합니다. 앱에서 파일 다운로드를 여러 번 다시 시도할 수 있으므로 초기 다운로드가 실패하더라도 앱을 다시 시작하지 않는 것이 좋습니다. ‘활동성 프로브’를 사용하여 프로세스의 활동성을 설명하며, 추가 검사를 수행하지 않습니다. 또한 구성 파일이 다운로드되기 전에는 앱에 요청을 보내지 못하게 합니다. ‘준비 상태 프로브’를 사용하여 다운로드에 성공하여 앱이 요청을 받을 준비가 될 때까지 “준비되지 않음” 상태를 표시합니다.

샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다. `StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면( *StartupHostedServiceHealthCheck.cs* ) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)( *Services/StartupHostedService* )에 의해 시작됩니다. 작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다. 호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너( *LivenessProbeStartup.cs* )에 등록됩니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다. 샘플 앱에서 상태 검사 엔드포인트는 다음 위치에 만들어집니다.

* 준비 상태 검사를 위해 `/health/ready`. 준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다.
* 활동성 검사를 위해 `/health/live`. 활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).

다음 예제 코드에서:

* 준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.
* `Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario liveness
```

브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다. 상태 검사는 첫 15초 동안 *Unhealthy* 를 보고합니다. 15초 후, 엔드포인트는 *Healthy* 를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.

이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다. 자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.

### <a name="kubernetes-example"></a>Kubernetes 예제

별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다. Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다. 별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다. Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.

다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>사용자 지정 응답 기록기가 있는 메트릭 기반 프로브

샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.

앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 성능이 저하된 상태를 보고합니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다( *MemoryHealthCheck.cs* ).

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다. 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다. 상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.

샘플 앱의 *CustomWriterStartup.cs* 에서:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다. 상태 검사를 실행하는 경우 `WriteResponse` 대리자가 사용자 지정 JSON 응답을 출력하는 출력에 대한 <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 속성에 제공됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

`WriteResponse` 대리자는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다. 자세한 내용은 [출력 사용자 지정](#customize-output) 섹션을 참조하세요.

샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="filter-by-port"></a>포트별 필터링

지정된 포트에 대한 상태 검사 요청을 제한하는 포트를 지정하는 URL 패턴을 사용하여 `MapHealthChecks`에 `RequireHost`를 호출합니다. 이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.

샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables)를 사용하여 포트를 구성합니다. 포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다. 또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.

샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.

샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. `Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.

샘플 앱에서 `Startup.Configure`의 엔드포인트에 대한 `RequireHost` 호출은 구성의 관리 포트를 지정합니다.

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

샘플 앱에서 엔드포인트는 `Startup.Configure`에서 만들어집니다. 다음 예제 코드에서:

* 준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.
* `Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> 코드에서 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다. <xref:Microsoft.Extensions.Hosting.HostBuilder>가 만들어진 *Program.cs* 에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*>에 대한 호출을 추가하고 앱의 관리 포트 엔드포인트를 제공합니다. *ManagementPortStartup.cs* 의 `Configure`에서 `RequireHost`로 관리 포트를 지정합니다.
>
> *Program.cs* :
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>상태 검사 라이브러리 배포

상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다. 클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.

   `CheckHealthAsync`의 상태 검사 논리에서:

   * `data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.
   * `AccessViolationException`이 처리됩니다.

   <xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. 사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다. 다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다. 상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다. 다음 예제에서 호출자는 선택 사항을 지정합니다.

   * 상태 검사 이름(`name`). `null`인 경우 `example_health_check`가 사용됩니다.
   * 상태 검사를 위한 문자열 데이터 요소입니다(`data1`).
   * 상태 검사를 위한 정수 데이터 요소입니다(`data2`). `null`인 경우 `1`이 사용됩니다.
   * 실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). 기본값은 `null`입니다. `null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.
   * 태그(`IEnumerable<string>`)입니다.

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>상태 검사 게시자

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다. 이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다. 지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다. 기본값은 5초입니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다. 기본값은 30초입니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다. 상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다. 조건자는 기간마다 평가됩니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다. 시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다. 기본값은 30초입니다.

샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다. 상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.

* 상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)
* 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="restrict-health-checks-with-mapwhen"></a>MapWhen으로 상태 검사 제한

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.

다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

자세한 내용은 <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.

상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다. 상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.

* 상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다. 예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다. 부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.
* 메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.
* 상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다. 지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다. 샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다. 앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다. 모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.

[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지에 대한 패키지 참조를 추가합니다.

샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다. [데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다. [DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다. 데이터베이스 시나리오를 탐색하려면 샘플 앱:

* 데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.
* 해당 프로젝트 파일에 다음 패키지 참조가 있습니다.
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다. 샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다. 자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.

## <a name="basic-health-probe"></a>기본 상태 프로브

많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성( *활동성* )을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.

기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다. 기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다. 상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다. 기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. `Startup.Configure`의 요청 처리 파이프라인에 있는 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>로 상태 검사 미들웨어용 엔드포인트를 추가합니다.

샘플 앱에서 상태 검사 엔드포인트는 `/health`( *BasicStartup.cs* )에 만들어집니다.

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker 예

[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>상태 검사 만들기

상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다. 결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.

### <a name="example-health-check"></a>상태 검사 예

다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다. 상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다. 다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다. `healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>상태 검사 서비스 등록

`ExampleHealthCheck` 형식은 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>로 `Startup.ConfigureServices`의 상태 검사 서비스에 추가됩니다.

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다. 오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다. 이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.

*태그* 를 사용하여 상태 검사를 필터링할 수 있습니다( [필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다. 다음 `Startup.ConfigureServices` 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>상태 검사 미들웨어 사용

`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 처리 파이프라인에서 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출합니다.

```csharp
app.UseHealthChecks("/health");
```

상태 검사가 특정 포트에서 수신 대기해야 하는 경우 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>의 오버로드를 사용하여 포트를 설정합니다([포트별 필터링 ](#filter-by-port) 섹션에 자세히 설명되어 있음).

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>상태 검사 옵션

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.

* [상태 검사 필터링](#filter-health-checks)
* [HTTP 상태 코드 사용자 지정](#customize-the-http-status-code)
* [캐시 헤더 표시 안 함](#suppress-cache-headers)
* [출력 사용자 지정](#customize-output)

### <a name="filter-health-checks"></a>상태 검사 필터링

기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다. 상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다. 다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>HTTP 상태 코드 사용자 지정

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다. 다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다. 요구 사항에 맞게 상태 코드 값을 변경합니다.

`Startup.Configure`의 경우

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>캐시 헤더 표시 안 함

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다. 값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다. 값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.

`Startup.Configure`의 경우

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>출력 사용자 지정

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 옵션은 응답을 기록하는 데 사용되는 대리자를 가져오거나 설정합니다. 기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.

`Startup.Configure`의 경우

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다. 다음 사용자 지정 대리자 `WriteResponse`는 사용자 지정 JSON 응답을 출력합니다.

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

상태 검사 시스템은 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다. 필요에 따라 앞의 예제에서 `JObject`를 자유롭게 사용자 지정할 수 있습니다.

## <a name="database-probe"></a>데이터베이스 프로브

상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.

샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다. `AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.

> [!WARNING]
> 쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다. 쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다. 대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다. 간단히 데이터베이스에 연결하는 정도로도 충분합니다. 쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.

[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.

샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다. 앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. 샘플 앱은 데이터베이스의 연결 문자열( *DbHealthStartup.cs* )을 사용하여 `AddSqlServer` 메서드를 호출합니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.

```csharp
app.UseHealthChecks("/health");
```

샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="entity-framework-core-dbcontext-probe"></a>Entity Framework Core DbContext 프로브

`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다. `DbContext` 검사는 다음과 같은 앱에서 지원됩니다.

* [EF(Entity Framework) Core](/ef/core/)를 사용합니다.
* [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.

`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다. `DbContext`는 메서드에 `TContext`로 제공됩니다. 오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.

기본적으로 다음과 같습니다.

* `DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다. `AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.
* 상태 검사의 이름은 `TContext` 형식의 이름입니다.

샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다( *DbContextHealthStartup.cs* ).

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

샘플 앱에서 `UseHealthChecks`는 `Startup.Configure`에서 상태 검사 미들웨어를 추가합니다.

```csharp
app.UseHealthChecks("/health");
```

샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다. 데이터베이스가 있으면 삭제합니다.

명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario dbcontext
```

앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다. 데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.

```
Unhealthy
```

데이터베이스를 만드는 샘플 앱을 트리거합니다. `/createdatabase`에 요청합니다. 앱은 다음과 같이 응답합니다.

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` 엔드포인트에 요청합니다. 데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.

```
Healthy
```

데이터베이스를 삭제하는 샘플 앱을 트리거합니다. `/deletedatabase`에 요청합니다. 앱은 다음과 같이 응답합니다.

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` 엔드포인트에 요청합니다. 앱은 다음과 같이 비정상 응답을 제공합니다.

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>별도의 준비 상태 및 활동성 프로브

일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.

* ‘준비 상태’는 앱이 정상적으로 실행 중이지만 요청을 받을 준비가 되지 않았음을 나타냅니다.
* ‘활동성’은 앱의 작동이 중단되어 다시 시작해야 함을 나타냅니다.

다음 예제를 참조하세요. 앱이 요청을 처리할 준비가 되려면 먼저 대용량 구성 파일을 다운로드해야 합니다. 앱에서 파일 다운로드를 여러 번 다시 시도할 수 있으므로 초기 다운로드가 실패하더라도 앱을 다시 시작하지 않는 것이 좋습니다. ‘활동성 프로브’를 사용하여 프로세스의 활동성을 설명하며, 추가 검사를 수행하지 않습니다. 또한 구성 파일이 다운로드되기 전에는 앱에 요청을 보내지 못하게 합니다. ‘준비 상태 프로브’를 사용하여 다운로드에 성공하여 앱이 요청을 받을 준비가 될 때까지 “준비되지 않음” 상태를 표시합니다.

샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다. `StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면( *StartupHostedServiceHealthCheck.cs* ) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)( *Services/StartupHostedService* )에 의해 시작됩니다. 작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다. 호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너( *LivenessProbeStartup.cs* )에 등록됩니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다. 샘플 앱에서 상태 검사 엔드포인트는 활동성 검사의 경우 `/health/ready`에, 준비 상태 검사의 경우 `/health/live`에 만들어집니다. 준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다. 활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario liveness
```

브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다. 상태 검사는 첫 15초 동안 *Unhealthy* 를 보고합니다. 15초 후, 엔드포인트는 *Healthy* 를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.

이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다. 자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.

### <a name="kubernetes-example"></a>Kubernetes 예제

별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다. Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다. 별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다. Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.

다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>사용자 지정 응답 기록기가 있는 메트릭 기반 프로브

샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.

앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 비정상적인 상태를 보고합니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다( *MemoryHealthCheck.cs* ).

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다. 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다. 상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.

샘플 앱( *CustomWriterStartup.cs* )에서:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다. 상태 검사가 실행될 때 사용자 정의 JSON 응답을 출력하기 위해 `ResponseWriter` 속성에 `WriteResponse` 대리자가 제공됩니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

`WriteResponse` 메서드는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="filter-by-port"></a>포트별 필터링

포트와 함께 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출하면 지정된 포트에 대한 상태 검사 요청을 제한합니다. 이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.

샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 사용하여 포트를 구성합니다. 포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다. 또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.

샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.

샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다. <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>에 대한 호출은 관리 포트를 지정합니다( *ManagementPortStartup.cs* ).

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> 코드에서 URL과 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>가 만들어진 *Program.cs* 에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>에 호출을 추가하고 앱의 정상적인 응답 엔드포인트와 관리 포트 엔드포인트를 제공합니다. <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>가 호출되는 *ManagementPortStartup.cs* 에서 관리 포트를 명시적으로 지정합니다.
>
> *Program.cs* :
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>상태 검사 라이브러리 배포

상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.

1. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다. 클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.

   `CheckHealthAsync`의 상태 검사 논리에서:

   * `data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.
   * `AccessViolationException`이 처리됩니다.

   <xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. 사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다. 다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다. 상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다. 다음 예제에서 호출자는 선택 사항을 지정합니다.

   * 상태 검사 이름(`name`). `null`인 경우 `example_health_check`가 사용됩니다.
   * 상태 검사를 위한 문자열 데이터 요소입니다(`data1`).
   * 상태 검사를 위한 정수 데이터 요소입니다(`data2`). `null`인 경우 `1`이 사용됩니다.
   * 실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). 기본값은 `null`입니다. `null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.
   * 태그(`IEnumerable<string>`)입니다.

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>상태 검사 게시자

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다. 이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다. 지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다. 기본값은 5초입니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다. 기본값은 30초입니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다. 상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다. 조건자는 기간마다 평가됩니다.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다. 시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다. 기본값은 30초입니다.

> [!WARNING]
> ASP.NET Core 2.2 릴리스에서 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현은 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> 설정을 적용하지 않으며, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 값을 설정합니다. 이 문제는 ASP.NET Core 3.0에서 해결되었습니다.

샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다. 상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.

* 상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)
* 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> 다음 해결 방법에서는 하나 이상의 다른 호스트 서비스가 이미 앱에 추가되었을 때 서비스 컨테이너에 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 추가하는 것을 허용합니다. ASP.NET Core 3.0에는 이 해결 방법이 필요하지 않습니다.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.

## <a name="restrict-health-checks-with-mapwhen"></a>MapWhen으로 상태 검사 제한

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.

다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

자세한 내용은 <xref:fundamentals/middleware/index#use-run-and-map>를 참조하세요.

::: moniker-end
