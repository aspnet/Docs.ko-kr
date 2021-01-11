---
title: ASP.NET Core Blazor 환경
author: guardrex
description: Blazor WebAssembly 앱의 환경을 설정하는 방법을 포함하여 Blazor의 환경에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97678296"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor 환경

> [!NOTE]
> 이 항목은 Blazor WebAssembly에 적용됩니다. Blazor Server 앱에 사용할 접근 방식을 설명하는 ASP.NET Core 앱 구성에 관한 일반 지침은 <xref:fundamentals/environments>을 참조하세요.

앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다. 앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.

호스트된 Blazor WebAssembly 솔루션의 클라이언트 쪽 Blazor 앱( *`Client`* )은 브라우저에 환경을 전달하는 미들웨어를 통해 솔루션의 *`Server`* 앱에서 환경을 결정합니다. *`Server`* 앱은 환경이 포함된 `blazor-environment`라는 헤더를 헤더 값으로 추가합니다. *`Client`* 앱은 헤더를 읽습니다. 솔루션의 *`Server`* 앱은 ASP.NET Core 앱이므로 환경을 구성하는 방법에 대한 자세한 내용은 <xref:fundamentals/environments>을 참조하세요.

로컬에서 실행되는 독립 실행형 Blazor WebAssembly 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다. 다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.

다음 IIS 예제에서는 게시된 `web.config` 파일에 사용자 지정 헤더(`blazor-environment`)를 추가합니다. `web.config` 파일은 `bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 있습니다. 여기서 자리 표시자 `{TARGET FRAMEWORK}`는 대상 프레임워크입니다.

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

`Pages/ReadEnvironment.razor`:

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 호스트 빌더 코드에 환경 관련 논리를 사용할 수 있습니다.

`Program.cs`의 `Program.Main`에서 다음을 수행합니다.

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>를 통해 제공되는 다음과 같은 편리한 확장 메서드로 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

`Program.cs`의 `Program.Main`에서 다음을 수행합니다.

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
