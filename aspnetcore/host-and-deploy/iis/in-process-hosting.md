---
title: IIS 및 ASP.NET Core를 사용하는 In Process 호스팅
author: rick-anderson
description: IIS 및 ASP.NET Core 모듈을 사용하는 In Process 호스팅에 관해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755172"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>IIS 및 ASP.NET Core를 사용하는 In Process 호스팅 

In Process 호스팅은 IIS 작업자 프로세스와 동일한 프로세스에서 ASP.NET Core 앱을 실행합니다. 요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.

다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>In Process 호스팅 사용

ASP.NET Core 3.0 이후 In Process 호스팅은 IIS에 배포된 모든 앱에서 기본적으로 사용되었습니다.

In Process 호스팅을 위해 앱을 명시적으로 구성하려면 프로젝트 파일(`.csproj`)에서 `<AspNetCoreHostingModel>` 속성의 값을 `InProcess`로 설정합니다.

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>일반 아키텍처

요청의 일반적인 흐름은 다음과 같습니다.

1. 요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.
1. 드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.
1. ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다. IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.

IIS HTTP 서버에서 요청을 처리한 후:

1. 해당 요청은 ASP.NET Core 미들웨어 파이프라인으로 전송됩니다.
1. 미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.
1. 앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.
1. IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.

`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(`w3wp.exe` 또는 `iisexpress.exe`) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다. 성능 테스트의 결과 .NET Core 앱 in-process를 호스팅하는 것이 앱 out-of-process 및 [Kestrel](xref:fundamentals/servers/kestrel)에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.

단일 실행 파일로 게시된 앱은 In Process 호스팅 모델을 통해 로드할 수 없습니다.

## <a name="application-configuration"></a>애플리케이션 구성

IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다. 다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| 옵션 | 기본값 | 설정 |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | `true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다. `false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다. IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다. 자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요. |
| `AuthenticationDisplayName` | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다. |
| `AllowSynchronousIO` | `false` | `HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다. |
| `MaxRequestBodySize` | `30000000` | `HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다. IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다. `MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다. `maxAllowedContentLength`를 늘리려면 `web.config`에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다. 자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요. |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>In Process 호스트와 Out of Process의 차이점

다음 특성은 In-Process로 호스팅할 때 적용됩니다.

* IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다. In Process인 경우 [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A>를 호출하여 다음을 수행합니다.

  * `IISHttpServer`를 등록합니다.
  * ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
  * 시작 오류를 캡처하도록 호스트를 구성합니다.

* [`requestTimeout` 특성](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element)은 In Process 호스팅에 적용되지 않습니다.

* 앱 간의 앱 풀 공유는 지원되지 않습니다. 앱당 하나의 앱 풀을 사용합니다.

* 앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다. 예를 들어 32비트(x86)용으로 게시된 앱은 해당 IIS 애플리케이션 풀에 대해 32비트를 사용하도록 설정해야 합니다. 자세한 내용은 [IIS 사이트 만들기](xref:tutorials/publish-to-iis#create-the-iis-site) 섹션을 참조하세요.

* 클라이언트의 연결 끊김이 검색되었습니다. 클라이언트의 연결이 끊어지면 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) 취소 토큰이 취소됩니다.

* In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A>를 내부적으로 호출하지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>을 호출하여 인증 서비스를 추가합니다.

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [웹 패키지(단일 파일) 배포](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)는 지원되지 않습니다.
