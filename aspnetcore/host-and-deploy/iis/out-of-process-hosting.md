---
title: IIS 및 ASP.NET Core를 사용하는 Out of Process 호스팅
author: rick-anderson
description: IIS 및 ASP.NET Core 모듈을 사용하는 Out of Process 호스팅에 관해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060419"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>IIS 및 ASP.NET Core를 사용하는 Out of Process 호스팅 

ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다. 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다. 이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.

다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

1. 요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.
1. 드라이버는 웹 사이트의 구성된 포트에서 IIS로 요청을 라우팅합니다. 구성된 포트는 일반적으로 80(HTTP) 또는 443(HTTPS)입니다.
1. 모듈은 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다. 임의 포트는 80 또는 443이 아닙니다.

<!-- make this a bullet list -->
ASP.NET Core 모듈은 시작 시 환경 변수를 통해 포트를 지정합니다. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 확장은 `http://localhost:{PORT}`에서 수신하도록 서버를 구성합니다. 추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다. 이 모듈은 HTTPS 전달을 지원하지 않습니다. HTTPS를 통해 IIS에서 수신하는 경우에도 요청은 HTTP를 통해 전달됩니다.

Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 전달됩니다. 미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다. IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다. 앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 전달됩니다.

ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.

호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.

## <a name="application-configuration"></a>애플리케이션 구성

### <a name="enable-the-iisintegration-components"></a>IISIntegration 구성 요소 사용

`CreateHostBuilder`(`Program.cs`)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.


**Out-of-process 호스팅 모델**

IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다. 다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| 옵션                         | 기본값 | 설정 |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다. `false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다. IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다. 자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요. |
| `AuthenticationDisplayName`    | `null`  | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다. |
| `ForwardClientCertificate`     | `true`  | `true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

[IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 다음을 전달하도록 구성되어 있습니다.

* 체계(HTTP/HTTPS).
* 요청이 시작된 원격 IP 주소입니다.

[IIS 통합 미들웨어](#enable-the-iisintegration-components)는 전달된 헤더 미들웨어를 구성합니다.

추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다. 자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.


### <a name="out-of-process-hosting-model"></a>Out-of-Process 호스팅 모델

Out-of-Process 호스팅을 위해 앱을 구성하려면 프로젝트 파일( `.csproj`)에서 `<AspNetCoreHostingModel>` 속성의 값을 `OutOfProcess`로 설정합니다.

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

In-Process 호스팅은 기본값 `InProcess`로 설정됩니다.

`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.

[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.

Out of Process의 경우 [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>을 호출합니다.

* ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.
* 시작 오류를 캡처하도록 호스트를 구성합니다.

### <a name="process-name"></a>프로세스 이름

`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.

Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다. ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.

ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.

* 작업자 프로세스의 환경 변수를 설정합니다.
* 시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.
* Windows 인증 토큰을 전달합니다.
