---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 구성 요소에 서비스를 주입할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 30edffedf1faf96ed54d5380762c8558e478966c
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106923"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 종속성 주입

작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Mike Rousos](https://github.com/mjrousos)

[DI(종속성 주입)](xref:fundamentals/dependency-injection)는 중앙 위치에 구성된 서비스에 액세스하기 위한 기술입니다.

* 프레임워크 등록 서비스는 Blazor 앱의 구성 요소에 직접 주입할 수 있습니다.
* Blazor 앱은 사용자 지정 서비스를 정의 및 등록하고 DI를 통해 앱 전체에서 사용하도록 할 수 있습니다.

## <a name="default-services"></a>기본 서비스

다음 표에 표시된 서비스는 일반적으로 Blazor 앱에서 사용됩니다.

| 서비스 | 수명 | 설명 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Scoped | <p>URI로 식별되는 리소스에서 HTTP 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공합니다.</p><p>Blazor WebAssembly 앱의 <xref:System.Net.Http.HttpClient> 인스턴스는 브라우저를 사용하여 백그라운드에서 HTTP 트래픽을 처리합니다.</p><p>Blazor Server 앱에는 기본적으로 서비스로 구성된 <xref:System.Net.Http.HttpClient>는 포함되지 않습니다. Blazor Server 앱에 <xref:System.Net.Http.HttpClient>를 제공합니다.</p><p>자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</p><p><xref:System.Net.Http.HttpClient>가 싱글톤이 아닌 범위가 지정된 서비스로 등록됩니다. 자세한 내용은 [서비스 수명](#service-lifetime) 섹션을 참조하세요.</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Scoped</p> | JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다. 자세한 내용은 <xref:blazor/call-javascript-from-dotnet>를 참조하세요. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Scoped</p> | URI 및 탐색 상태를 사용하기 위한 도우미를 포함합니다. 자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)를 참조하세요. |

사용자 지정 서비스 공급자는 테이블에 나열된 기본 서비스를 자동으로 제공하지 않습니다. 사용자 지정 서비스 공급자를 사용하고 표에 표시된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가합니다.

## <a name="add-services-to-an-app"></a>앱에 서비스 추가

::: zone pivot="webassembly"

`Program.cs`의 `Program.Main` 메서드에서 앱의 서비스 컬렉션용 서비스를 구성합니다. 다음 예제에서는 `MyDependency` 구현을 `IMyDependency`에 등록합니다.

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

호스트를 빌드한 후 구성 요소를 렌더링하기 전에 루트 DI 범위에서 서비스를 사용할 수 있습니다. 이러한 기능은 콘텐츠를 렌더링하기 전에 초기화 논리를 실행하는 데 유용할 수 있습니다.

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

호스트는 앱의 중앙 구성 인스턴스를 제공합니다. 이전 예제를 기준으로 작성한 날씨 서비스 URL은 기본 구성 원본(예: `appsettings.json`)에서 `InitializeWeatherAsync`로 전달됩니다.

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

새 앱을 만든 후 `Startup.ConfigureServices`에서 `Startup.cs` 메서드를 검사합니다.

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 메서드는 [서비스 설명자](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) 개체 목록에 해당하는 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 전달됩니다. 서비스 설명자를 서비스 컬렉션에 제공함으로써 서비스가 `ConfigureServices` 메서드에 추가됩니다. 다음 예제에서는 `IDataAccess` 인터페이스와 해당 구체적 구현 `DataAccess`를 통해 이러한 개념을 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>서비스 수명

다음 표에 표시된 수명으로 서비스를 구성할 수 있습니다.

| 수명 | 설명 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly 앱에는 현재, DI 범위에 대한 개념이 없습니다. `Scoped` 등록 서비스는 `Singleton` 서비스처럼 동작합니다.</p><p>Blazor Server 호스팅 모델은 HTTP 요청에서 `Scoped` 수명을 지원하지만 클라이언트에서 로드되는 구성 요소 간의 SignalR 연결/회로 메시지에서는 지원하지 않습니다. Razor Pages 또는 앱의 MVC 부분은 범위가 지정된 서비스를 정상적으로 처리하고, 페이지 또는 뷰 사이를 이동하거나 페이지 또는 뷰에서 구성 요소로 이동할 때 각 HTTP 요청에서 서비스를 다시 만듭니다. 클라이언트의 구성 요소 사이를 이동할 때는 범위가 지정된 서비스가 다시 구성되지 않습니다. 이 경우 서버와의 통신은 HTTP 요청을 통하지 않고 사용자 회로의 SignalR 연결을 통해 이루어집니다. 클라이언트에서 다음과 같은 구성 요소 시나리오에서는 사용자를 위해 새 회로가 만들어지므로 범위가 지정된 서비스가 다시 구성됩니다.</p><ul><li>사용자가 브라우저의 창을 닫습니다. 사용자가 새 창을 열고 앱으로 다시 이동합니다.</li><li>사용자가 브라우저 창에서 앱의 마지막 탭을 닫습니다. 사용자가 새 탭을 열고 앱으로 다시 이동합니다.</li><li>사용자가 브라우저의 다시 로드/새로 고침 단추를 선택합니다.</li></ul><p>Blazor Server 앱의 범위가 지정된 서비스에서 사용자 상태를 보존하는 방법에 대한 자세한 내용은 <xref:blazor/hosting-models?pivots=server>를 참조하세요.</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI는 서비스의 *단일 인스턴스* 를 만듭니다. `Singleton` 서비스가 필요한 모든 구성 요소는 동일한 서비스의 인스턴스를 수신합니다. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | 구성 요소는 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 을 받습니다. |

DI 시스템은 ASP.NET Core에서 DI 시스템을 기준으로 합니다. 자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.

## <a name="request-a-service-in-a-component"></a>구성 요소에서 서비스 요청

서비스 컬렉션에 서비스를 추가한 후에는 다음 두 개의 매개 변수를 포함하는 [`@inject`](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 서비스를 구성 요소에 주입합니다.

* 유형: 주입할 서비스의 유형입니다.
* 속성: 주입된 앱 서비스를 받는 속성의 이름입니다. 이 속성은 수동으로 만들 필요가 없습니다. 컴파일러에서 속성을 만들기 때문입니다.

자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.

여러 [`@inject`](xref:mvc/views/razor#inject) 문을 사용하여 여러 서비스를 주입합니다.

다음 예제에서는 [`@inject`](xref:mvc/views/razor#inject)를 사용하는 방법을 보여 줍니다. `Services.IDataAccess`를 구현하는 서비스는 구성 요소의 속성 `DataRepository`에 주입됩니다. 코드가 `IDataAccess` 추상화만 사용하는 방식에 유의하세요.

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

내부적으로 생성된 속성(`DataRepository`)은 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 사용합니다. 일반적으로 이 특성은 직접 사용되지 않습니다. 구성 요소에 기본 클래스가 필요하고 기본 클래스에 주입된 속성도 필요하면 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 수동으로 추가합니다.

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

기본 클래스에서 파생된 구성 요소에서는 [`@inject`](xref:mvc/views/razor#inject) 지시문이 필요하지 않습니다. 기본 클래스의 <xref:Microsoft.AspNetCore.Components.InjectAttribute>만 있으면 충분합니다.

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>서비스에서 DI 사용

복잡한 서비스에는 추가 서비스가 필요할 수 있습니다. 다음 예제에서는 `DataAccess`에 <xref:System.Net.Http.HttpClient> 기본 서비스가 필요합니다. [`@inject`](xref:mvc/views/razor#inject)(또는 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute))는 서비스에서 사용할 수 없습니다. 대신 *생성자 주입* 을 사용해야 합니다. 서비스의 생성자에 매개 변수를 추가하여 필요한 서비스를 추가합니다. DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식하고 적절히 제공합니다. 다음 예제에서 생성자는 DI를 통해 <xref:System.Net.Http.HttpClient>를 받습니다. <xref:System.Net.Http.HttpClient>는 기본 서비스입니다.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

생성자 주입의 필수 조건:

* 모든 인수를 DI에서 처리할 수 있는 생성자가 하나 있어야 합니다. DI에서 다루지 않는 추가 매개 변수는 기본값이 지정되면 허용됩니다.
* 적용 가능한 생성자는 `public`이어야 합니다.
* 적용 가능한 생성자가 하나 있어야 합니다. 모호한 경우 시 DI는 예외를 throw합니다.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스

ASP.NET Core 앱에서 범위가 지정된 서비스는 일반적으로 현재 요청으로 범위가 지정됩니다. 요청이 완료된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에서 삭제됩니다. Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간에 지속되므로 임시 및 범위가 지정된 서비스가 예상보다 훨씬 오래 지속될 수 있습니다. Blazor WebAssembly 앱에서, 범위가 지정된 수명으로 등록된 서비스는 singleton으로 처리되므로 일반적인 ASP.NET Core 앱의 범위가 지정된 서비스보다 오래 지속됩니다.

> [!NOTE]
> 앱에서 삭제 가능한 임시 서비스를 검색하려면 [임시 삭제 가능 항목 검색](#detect-transient-disposables) 섹션을 참조하세요.

Blazor 앱에서 서비스 수명을 제한하는 방법은 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용하는 것입니다. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 구성 요소의 수명에 해당하는 DI 범위를 만드는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생된 추상 형식입니다. 이 범위를 사용하는 경우 수명 범위를 지정한 DI 서비스를 사용하고 해당 구성 요소만큼 지속되도록 할 수 있습니다. 구성 요소가 제거되면 구성 요소 범위 지정 서비스 공급자의 서비스도 삭제됩니다. 이 기능은 다음과 같은 서비스에 유용할 수 있습니다.

* 일시적 수명은 부적절하므로 구성 요소 내에서 다시 사용해야 합니다.
* 싱글톤 수명은 부적절하므로 구성 요소에서 공유할 수 없습니다.

두 가지 버전의 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용할 수 있습니다.

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 <xref:System.IServiceProvider> 형식의 보호된 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase> 형식의 삭제 가능한 추상 자식입니다. 이 공급자는 구성 요소의 수명으로 범위가 지정된 서비스를 확인하는 데 사용할 수 있습니다.

  [`@inject`](xref:mvc/views/razor#inject) 또는 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 사용하여 구성 요소에 주입된 DI 서비스는 구성 요소의 범위에 만들어지지 않습니다. 구성 요소의 범위를 사용하려면 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 또는 <xref:System.IServiceProvider.GetService%2A>를 사용하여 서비스를 확인해야 합니다. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 공급자를 사용하여 확인된 모든 서비스에는 동일한 범위에서 종속성이 제공됩니다.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>에서 파생되고 범위가 지정된 DI 공급자에서 `T`의 인스턴스를 반환하는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 속성을 추가합니다. 이 형식은 구성 요소의 범위를 사용하여 DI 컨테이너에서 앱에 필요한 기본 서비스가 하나 있는 경우 <xref:System.IServiceProvider> 인스턴스를 사용하지 않고 범위 지정 서비스에 액세스할 수 있는 편리한 방법입니다. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용할 수 있으므로 필요한 경우 앱에서 다른 형식의 서비스를 가져올 수 있습니다.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>DI에서 EF Core(Entity Framework Core) DbContext 사용

자세한 내용은 <xref:blazor/blazor-server-ef-core>를 참조하세요.

## <a name="detect-transient-disposables"></a>임시 삭제 가능 항목 검색

다음 예제에서는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>를 사용해야 하는 앱에서 삭제 가능한 임시 서비스를 검색하는 방법을 보여 줍니다. 자세한 내용은 [DI 범위를 관리하는 유틸리티 기본 구성 요소 클래스](#utility-base-component-classes-to-manage-a-di-scope) 섹션을 참조하세요.

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

다음 예제에서 `TransientDisposable`이 검색되었습니다(`Program.cs`).

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.

```csharp
using Microsoft.Extensions.DependencyInjection;
```

`Program.cs`의 `Program.CreateHostBuilder`에서 다음을 수행합니다.

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

다음 예제에서 `TransientDependency`가 검색되었습니다(`Startup.cs`).

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

앱은 예외를 throw하지 않고 삭제 가능한 임시 서비스를 등록할 수 있습니다. 그러나 다음 예제에 나와 있는 것처럼 삭제 가능한 임시 서비스를 확인하려는 경우 <xref:System.InvalidOperationException>이 발생합니다.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

`/transient-disposable`에 있는 `TransientDisposable` 구성 요소로 이동하면 프레임워크가 `TransientDisposable`의 인스턴스를 생성하려고 할 때 <xref:System.InvalidOperationException>이 throw됩니다.

> System.InvalidOperationException: 잘못된 범위에서 삭제 가능한 임시 서비스 TransientDisposable을 확인하려고 합니다. 확인하려는 서비스 'T'에 'OwningComponentBase\<T>' 구성 요소 기본 클래스를 사용합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/dependency-injection>
* [임시 및 공유 인스턴스에 대한 `IDisposable` 지침](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
