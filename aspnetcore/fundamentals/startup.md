---
title: ASP.NET Core에서 앱 시작
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
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
uid: fundamentals/startup
ms.openlocfilehash: 747b13abb0ce3fed2d1dc018c6dbf82db1ae7130
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052242"
---
# <a name="app-startup-in-aspnet-core"></a>ASP.NET Core에서 앱 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com)

`Startup` 클래스는 서비스와 응용 프로그램의 요청 파이프라인을 구성합니다.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>시작 클래스

ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다. `Startup` 클래스는:

* 선택적으로 앱의 *서비스* 를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다. 서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다. 서비스는 `ConfigureServices`에서 *등록* 되며 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.
* 앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.

`ConfigureServices` 및 `Configure`는 앱 시작 시 ASP.NET Core 런타임에 의해 호출됩니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.


앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 지정됩니다. `Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다. 앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다. 그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.

[제네릭 호스트](xref:fundamentals/host/generic-host)(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.

### <a name="multiple-startup"></a>여러 Startup

앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의하면 런타임에 적절한 `Startup` 클래스가 선택됩니다. 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다. 앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다. 자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)을 참조하세요.

호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요. 시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.

## <a name="the-configureservices-method"></a>ConfigureServices 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.

* 선택 사항입니다.
* `Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.
* 여기서 [구성 옵션](xref:fundamentals/configuration/index)이 규칙에 의해 설정됩니다.

호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다. 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.

실질적인 설정이 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다. 예를 들어 **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores 및 **Add**RazorPages가 있습니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.

## <a name="the-configure-method"></a>Configure 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다. 요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다. `IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다. 호스팅이 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.

[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.

* [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)
* [예외 처리기](xref:fundamentals/error-handling#exception-handler-page)
* [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS 리디렉션](xref:security/enforcing-ssl)
* [정적 파일](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.

각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다. 예를 들어 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>는 [정적 파일](xref:fundamentals/static-files)을 제공하도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.

요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.

`IWebHostEnvironment`, `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다. 이 서비스를 사용할 수 있는 경우 삽입됩니다.

`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Startup을 사용하지 않고 서비스 구성

`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다. `ConfigureServices`에 대한 여러 호출은 서로 추가합니다. 여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>시작 필터를 이용한 Startup 확장

<xref:Microsoft.AspNetCore.Hosting.IStartupFilter>는 다음 용도로 사용합니다.

* `Use{Middleware}`를 명시적으로 호출하지 않고 앱의 [Configure](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝 부분에서 미들웨어를 구성합니다. `IStartupFilter`는 ASP.NET Core에서 앱 작성자가 명시적으로 기본 미들웨어를 등록할 필요 없이 파이프라인의 시작 부분에 기본값을 추가하는 데 사용됩니다. `IStartupFilter`는 앱 작성자를 대신하여 다른 구성 요소 호출 `Use{Middleware}`를 허용합니다.
* `Configure` 메서드의 파이프라인을 만듭니다. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*)는 라이브러리에서 추가된 미들웨어 이전 또는 이후에 실행할 미들웨어를 설정할 수 있습니다.

`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구현합니다. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다. 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.

각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 추가할 수 있습니다. 필터는 서비스 컨테이너에 추가된 순서대로 호출됩니다. 필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.

다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다. `RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록됩니다.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 ASP.NET Core 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.

미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.

* 여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다. 순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.
* 라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다. 라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 다음을 수행합니다.

  * 라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.
  * 나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>시작 시 외부 어셈블리로부터 구성 추가

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [호스트](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Startup 클래스

ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다. `Startup` 클래스는:

* 선택적으로 앱의 *서비스* 를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다. 서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다. 서비스는 `ConfigureServices`에서 *등록* 되며 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.
* 앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.

`ConfigureServices` 및 `Configure`는 앱 시작 시 ASP.NET Core 런타임에 의해 호출됩니다.

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 지정됩니다. `Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다. 앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다. 그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.

`Startup` 클래스에 대한 [종속성 주입](xref:fundamentals/dependency-injection)의 일반적인 용도는 다음을 삽입하는 것입니다.

* 환경별로 서비스를 구성하는 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.
* 구성을 읽는 <xref:Microsoft.Extensions.Configuration.IConfiguration>.
* `Startup.ConfigureServices`에서 로거를 만드는 <xref:Microsoft.Extensions.Logging.ILoggerFactory>.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.

### <a name="multiple-startup"></a>여러 Startup

앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의하면 런타임에 적절한 `Startup` 클래스가 선택됩니다. 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다. 앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다. 자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)을 참조하세요.

호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요. 시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.

## <a name="the-configureservices-method"></a>ConfigureServices 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.

* 선택 사항입니다.
* `Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.
* 여기서 [구성 옵션](xref:fundamentals/configuration/index)이 규칙에 의해 설정됩니다.

호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다. 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.

실질적인 설정이 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다. 예를 들어 **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores 및 **Add**RazorPages가 있습니다.

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.

`SetCompatibilityVersion`에 대한 자세한 내용은 [SetCompatibilityVersion](xref:mvc/compatibility-version)을 참조하세요.

## <a name="the-configure-method"></a>Configure 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다. 요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다. `IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다. 호스팅이 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.

[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.

* [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)
* [예외 처리기](xref:fundamentals/error-handling#exception-handler-page)
* [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS 리디렉션](xref:security/enforcing-ssl)
* [정적 파일](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)
* [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다. 예를 들어 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>는 [정적 파일](xref:fundamentals/static-files)을 제공하도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.

요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.

`IHostingEnvironment` 및 `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다. 이 서비스를 사용할 수 있는 경우 삽입됩니다.

`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Startup을 사용하지 않고 서비스 구성

`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다. `ConfigureServices`에 대한 여러 호출은 서로 추가합니다. 여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>시작 필터를 이용한 Startup 확장

<xref:Microsoft.AspNetCore.Hosting.IStartupFilter>는 다음 용도로 사용합니다.

* `Use{Middleware}`를 명시적으로 호출하지 않고 앱의 [Configure](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝 부분에서 미들웨어를 구성합니다. `IStartupFilter`는 ASP.NET Core에서 앱 작성자가 명시적으로 기본 미들웨어를 등록할 필요 없이 파이프라인의 시작 부분에 기본값을 추가하는 데 사용됩니다. `IStartupFilter`는 앱 작성자를 대신하여 다른 구성 요소 호출 `Use{Middleware}`를 허용합니다.
* `Configure` 메서드의 파이프라인을 만듭니다. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*)는 라이브러리에서 추가된 미들웨어 이전 또는 이후에 실행할 미들웨어를 설정할 수 있습니다.

`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구현합니다. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다. 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.

각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 추가할 수 있습니다. 필터는 서비스 컨테이너에 추가된 순서대로 호출됩니다. 필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.

다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다. `RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록됩니다.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 ASP.NET Core 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.

미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.

* 여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다. 순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.
* 라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다. 라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 다음을 수행합니다.

  * 라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.
  * 나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>시작 시 외부 어셈블리로부터 구성 추가

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [호스트](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end