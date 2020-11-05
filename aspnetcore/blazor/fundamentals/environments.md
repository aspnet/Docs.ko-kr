---
title: ASP.NET Core Blazor 환경
author: guardrex
description: Blazor WebAssembly 앱의 환경을 설정하는 방법을 포함하여 Blazor의 환경에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056038"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor 환경

> [!NOTE]
> 이 항목은 Blazor WebAssembly에 적용됩니다. ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/environments>의 내용을 참조하세요.

앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다. 앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.

호스트된 Blazor WebAssembly 앱은 `blazor-environment` 헤더를 추가하여 브라우저에 환경을 전달하는 미들웨어를 통해 서버에서 환경을 선택합니다. 헤더의 값은 해당 환경입니다. 호스트된 Blazor 앱과 서버 앱은 동일한 환경을 공유합니다. 환경을 구성하는 방법을 비롯한 자세한 내용은 <xref:fundamentals/environments> 문서를 참조하세요.

로컬에서 실행되는 독립 실행형 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다. 다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.

다음 IIS 예제에서는 게시된 `web.config` 파일에 사용자 지정 헤더를 추가합니다. `web.config` 파일은 `bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> 앱이 `publish` 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 `web.config` 파일을 사용하려면 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>를 참조하세요.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 삽입하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 개발자가 환경 관련 논리를 코드에 포함할 수 있습니다.

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

다음과 같은 편리한 확장 메서드를 통해 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.NavigationManager> 서비스를 사용할 수 없는 경우 시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 속성을 사용할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/environments>
