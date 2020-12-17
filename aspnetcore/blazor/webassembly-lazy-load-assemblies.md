---
title: ASP.NET Core Blazor WebAssembly의 어셈블리 지연 로드
author: guardrex
description: ASP.NET Core Blazor WebAssembly 앱에서 어셈블리를 지연 로드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6e7fa6e231e97793fbf7e1ac1d208bf3013c6fce
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506576"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly의 어셈블리 지연 로드

[Safia Abdalla](https://safia.rocks) 및 [Luke Latham](https://github.com/guardrex)

일부 애플리케이션 어셈블리의 로드를 연기(지연 로드라고 함)하여 Blazor WebAssembly 앱 시작 성능을 향상시킬 수 있습니다. 예를 들어 단일 구성 요소를 렌더링하는 데만 사용되는 어셈블리는 사용자가 해당 구성 요소로 이동하는 경우에만 로드되도록 설정할 수 있습니다. 로드 후 어셈블리는 클라이언트 쪽에서 캐시되며 이후의 모든 탐색에 사용할 수 있습니다.

Blazor의 지연 로드 기능을 사용하면 사용자가 특정 경로로 이동할 때 런타임에 어셈블리를 로드하는 지연 로드로 앱 어셈블리를 표시할 수 있습니다. 이 기능은 프로젝트 파일 변경과 애플리케이션의 라우터 변경으로 구성됩니다.

> [!NOTE]
> 어셈블리가 Blazor Server 앱에서 클라이언트로 다운로드되지 않기 때문에 어셈블리 지연 로드는 Blazor Server 앱에 유용하지 않습니다.

## <a name="project-file"></a>프로젝트 파일

`BlazorWebAssemblyLazyLoad` 항목을 사용하여 앱의 프로젝트 파일(`.csproj`)에서 어셈블리를 지연 로드로 표시합니다. `.dll` 확장명과 함께 어셈블리 이름을 사용합니다. Blazor 프레임워크는 이 항목 그룹이 지정한 어셈블리가 앱 시작 시 로드되지 않도록 합니다. 다음 예제에서는 큰 사용자 지정 어셈블리(`GrantImaharaRobotControls.dll`)를 지연 로드로 표시합니다. 지연 로드로 표시된 어셈블리에 종속성이 있는 경우 프로젝트 파일에서 종속성도 지연 로드로 표시되어야 합니다.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>`Router` 구성 요소

Blazor의 `Router` 구성 요소는 Blazor가 라우팅 가능한 구성 요소를 검색할 어셈블리를 지정합니다. `Router` 구성 요소는 사용자가 탐색하는 경로에 대한 구성 요소 렌더링도 담당합니다. `Router` 구성 요소는 지연 로드와 함께 사용할 수 있는 `OnNavigateAsync` 기능을 지원합니다.

앱의 `Router` 구성 요소(`App.razor`)에서 다음을 수행합니다.

* `OnNavigateAsync` 콜백을 추가합니다. 사용자가 다음을 수행하면 `OnNavigateAsync` 처리기가 호출됩니다.
  * 브라우저에서 직접 경로로 이동하여 처음으로 경로를 방문합니다.
  * 링크 또는 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 호출을 사용하여 새 경로로 이동합니다.
* 지연 로드된 어셈블리에 라우팅 가능한 구성 요소가 포함된 경우 [목록](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>>(예: `lazyLoadedAssemblies`)을 구성 요소에 추가합니다. 어셈블리에 라우팅 가능한 구성 요소가 포함된 경우 어셈블리는 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 컬렉션으로 다시 전달됩니다. 프레임워크는 어셈블리에서 경로를 검색하고 새 경로를 찾으면 경로 컬렉션을 업데이트합니다.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

`OnNavigateAsync` 콜백이 처리되지 않은 예외를 throw하는 경우 [Blazor 오류 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)가 호출됩니다.

### <a name="assembly-load-logic-in-onnavigateasync"></a>`OnNavigateAsync`의 어셈블리 로드 논리

`OnNavigateAsync`에는 대상 경로(`Path`) 및 취소 토큰(`CancellationToken`)을 포함하여 현재 비동기 탐색 이벤트에 대한 정보를 제공하는 `NavigationContext` 매개 변수가 있습니다.

* `Path` 속성은 `/robot`과 같이 애플리케이션의 기본 경로를 기준으로 하는 사용자의 대상 경로입니다.
* `CancellationToken`을 사용하여 비동기 작업의 취소를 관찰할 수 있습니다. `OnNavigateAsync`는 사용자가 다른 페이지로 이동하면 현재 실행 중인 탐색 작업을 자동으로 취소합니다.

`OnNavigateAsync` 내에서 로드할 어셈블리를 결정하는 논리를 구현합니다. 표시되는 옵션은 다음과 같습니다.

* `OnNavigateAsync` 메서드 내의 조건부 검사입니다.
* 구성 요소에 삽입되거나 [`@code`](xref:mvc/views/razor#code) 블록 내에서 구현되는 경로를 어셈블리 이름에 매핑하는 조회 테이블입니다.

`LazyAssemblyLoader`는 어셈블리를 로드하기 위한 프레임워크 제공 싱글톤 서비스입니다. `Router` 구성 요소에 `LazyAssemblyLoader`를 삽입합니다.

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader`는 다음과 같은 `LoadAssembliesAsync` 메서드를 제공합니다.

* JS interop을 사용하여 네트워크 호출을 통해 어셈블리를 가져옵니다.
* 브라우저의 WebAssembly에서 실행되는 런타임에 어셈블리를 로드합니다.

프레임워크의 지연 로드 구현에서는 호스트된 Blazor 솔루션에서 미리 렌더링으로 지연 로드를 지원합니다. 미리 렌더링 중에는 지연 로드로 표시된 어셈블리를 포함한 모든 어셈블리가 로드된다고 가정합니다. *Server* 프로젝트의 `Startup.ConfigureServices` 메서드(`Startup.cs`)에서 `LazyAssemblyLoader`를 수동으로 등록합니다.

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>`<Navigating>` 콘텐츠와의 사용자 상호 작용

몇 초 정도 걸릴 수 있는 어셈블리 로드 동안 `Router` 구성 요소는 페이지 전환 중임을 사용자에게 표시할 수 있습니다.

* <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문을 추가합니다.
* 페이지 전환 이벤트 중 표시할 태그를 사용하여 구성 요소에 `<Navigating>` 태그를 추가합니다.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a>`OnNavigateAsync`에서의 취소 처리

`OnNavigateAsync` 콜백에 전달되는 `NavigationContext` 개체에는 새 탐색 이벤트가 발생할 때 설정되는 `CancellationToken`이 포함됩니다. 오래된 탐색에서 `OnNavigateAsync` 콜백을 계속 실행하지 않도록 이 취소 토큰이 설정된 경우 `OnNavigateAsync` 콜백이 throw해야 합니다.

사용자가 경로 A로 이동한 다음 경로 B로 바로 이동하는 경우 앱은 경로 A에 대해 `OnNavigateAsync` 콜백을 계속 실행해서는 안 됩니다.

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> `NavigationContext`의 취소 토큰이 취소되는 경우 throw하지 않으면 이전 탐색에서 구성 요소를 렌더링하는 것과 같은 의도하지 않은 동작이 발생할 수 있습니다.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` 이벤트 및 이름이 바뀐 어셈블리 파일

리소스 로더는 `blazor.boot.json` 파일에 정의된 어셈블리 이름을 사용합니다. [어셈블리의 이름이 바뀌는](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files) 경우 `OnNavigateAsync` 메서드에 사용되는 어셈블리 이름과 `blazor.boot.json` 파일의 어셈블리 이름이 동기화되지 않습니다.

해결하려면 다음을 수행합니다.

* 사용할 어셈블리 이름을 결정할 때 앱이 프로덕션 환경에서 실행 중인지 확인합니다.
* 바뀐 어셈블리 이름을 별도의 파일에 저장하고 해당 파일에서 읽어서 `LazyLoadAssemblyService` 메서드와 `OnNavigateAsync` 메서드에 사용할 어셈블리 이름을 결정합니다.

### <a name="complete-example"></a>전체 예제

다음 전체 `Router` 구성 요소는 사용자가 `/robot`으로 이동할 때 `GrantImaharaRobotControls.dll` 어셈블리가 로드되는 것을 보여 줍니다. 페이지 전환 중에는 스타일이 지정된 메시지가 사용자에게 표시됩니다.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a>문제 해결

* 이전 탐색의 구성 요소가 렌더링되는 것처럼 예기치 않은 렌더링이 발생하는 경우, 취소 토큰이 설정되면 코드에서 throw하는지 확인합니다.
* 애플리케이션 시작 시 어셈블리가 여전히 로드되는 경우 프로젝트 파일에서 해당 어셈블리가 지연 로드로 표시되는지 확인합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/webassembly-performance-best-practices>
