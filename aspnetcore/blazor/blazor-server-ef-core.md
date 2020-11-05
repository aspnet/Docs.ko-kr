---
title: EFCore(Entity Framework Core)를 사용한 ASP.NET Core Blazor Server
author: JeremyLikness
description: Blazor Server 앱에서 EF Core를 사용하기 위한 지침입니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: bfc8f334b9229fed54e6b9841e4fb255ed18249a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056623"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>EFCore(Entity Framework Core)를 사용한 ASP.NET Core Blazor Server

기준: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server는 상태 저장 앱 프레임워크입니다. 앱은 서버에 대한 지속적인 연결을 유지하고, 사용자 상태는 ‘회로’의 서버 메모리에 저장됩니다. 사용자 상태의 한 예는 회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터입니다. Blazor Server에서 제공하는 고유한 애플리케이션 모델을 사용하려면 Entity Framework Core를 사용하는 특별한 방법이 필요합니다.

> [!NOTE]
> 이 문서에서는 Blazor Server 앱의 EF Core에 대해 살펴봅니다. Blazor WebAssembly 앱은 대부분 직접 데이터베이스 연결을 방지하는 WebAssembly 샌드박스에서 실행됩니다. Blazor WebAssembly에서 EF Core를 실행하는 것은 이 문서에서 다루지 않습니다.

<h2 id="sample-app-5x">샘플 앱</h2>

샘플 앱은 EF Core를 사용하는 Blazor Server 앱에 대한 참조로 작성되었습니다. 샘플 앱에는 정렬 및 필터링, 삭제, 추가 및 업데이트 작업을 포함하는 표가 포함되어 있습니다. 이 샘플에서는 EF Core를 사용하여 낙관적 동시성을 처리하는 방법을 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 샘플에서는 모든 플랫폼에서 사용할 수 있도록 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스를 사용합니다. 또한 생성되는 SQL 쿼리를 표시하도록 데이터베이스 로깅을 구성합니다. 이는 `appsettings.Development.json`에서 구성됩니다.

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

표, 추가 및 보기 구성 요소에서는 작업별로 하나의 컨텍스트를 생성하는 “context-per-operation” 패턴을 사용합니다. 편집 구성 요소에서는 구성 요소별로 하나의 컨텍스트를 생성하는 “context-per-component” 패턴을 사용합니다.

> [!NOTE]
> 이 항목의 일부 코드 예제에는 표시되지 않은 네임스페이스와 서비스가 필요합니다. Razor 예제에 대한 필수 [`@using`](xref:mvc/views/razor#using) 및 [`@inject`](xref:mvc/views/razor#inject) 지시문을 포함하여 완전히 작동하는 코드를 검사하려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)을 참조하세요.

<h2 id="database-access-5x">데이터베이스 액세스</h2>

EF Core에서는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용하여 [데이터베이스 액세스를 구성](/ef/core/miscellaneous/configuring-dbcontext)하고 [‘작업 단위’](https://martinfowler.com/eaaCatalog/unitOfWork.html) 역할을 합니다. EF Core는 컨텍스트를 기본적으로 ‘범위가 지정된’ 서비스로 등록하는 ASP.NET Core 앱에 대한 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장을 제공합니다. Blazor Server 앱에서는 사용자 회로 내의 구성 요소 간에 인스턴스가 공유되므로 범위가 지정된 서비스 등록을 사용할 경우 문제가 될 수 있습니다. <xref:Microsoft.EntityFrameworkCore.DbContext>는 스레드로부터 안전하지 않고 동시 사용을 위해 설계되지 않았습니다. 기존 수명은 다음과 같은 이유로 적합하지 않습니다.

* **Singleton** 은 앱의 모든 사용자에 대한 상태를 공유하고 부적절한 동시 사용을 초래합니다.
* **범위 지정** (기본값)은 동일한 사용자에 대한 구성 요소 간에 유사한 문제를 초래합니다.
* **임시** 는 요청별로 새 인스턴스를 생성하지만 구성 요소가 오래 지속될 수 있으므로 의도한 것보다 수명이 긴 컨텍스트가 생성됩니다.

다음 권장 사항은 Blazor Server 앱에서 EF Core를 사용하는 일관된 방법을 제공하도록 설계되었습니다.

* 기본적으로 작업당 하나의 컨텍스트를 사용하는 것이 좋습니다. 이 컨텍스트는 빠르고 낮은 오버헤드 인스턴스화를 위해 설계되었습니다.

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 플래그를 사용하여 여러 동시 작업을 방지합니다.

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  `try` 블록의 `Loading = true;` 줄 뒤에 작업을 추가합니다.

* EF Core의 [변경 내용 추적](/ef/core/querying/tracking) 또는 [동시성 제어](/ef/core/saving/concurrency)를 활용하는 장기 작업의 경우 [컨텍스트의 범위를 구성 요소의 수명으로 지정합니다](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">새 DbContext 인스턴스</h3>

새 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스를 만드는 가장 빠른 방법은 `new`를 사용하여 새 인스턴스를 만드는 것입니다. 하지만 일부 시나리오에서는 추가 종속성을 확인해야 합니다. 예를 들어 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions)를 사용하여 컨텍스트를 구성할 수 있습니다.

종속성을 사용하여 새 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만드는 권장 솔루션은 팩터리를 사용하는 것입니다. EF Core 5.0 이상에서는 새 컨텍스트를 만들기 위한 기본 제공 팩터리를 제공합니다.

다음 예에서는 [SQLite](https://www.sqlite.org/index.html)를 구성하고 데이터 로깅을 사용하도록 설정합니다. 이 코드에서는 [확장 메서드(`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs)를 사용하여 DI용 데이터베이스 팩터리를 구성하고 기본 옵션을 제공합니다.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

팩터리는 구성 요소에 삽입되어 새 인스턴스를 만드는 데 사용됩니다. 예를 들어 `Pages/Index.razor`에서는 다음과 같습니다.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper`는 `GridWrapper` 구성 요소에 대한 [구성 요소 참조](xref:blazor/components/index#capture-references-to-components)입니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)의 `Index` 구성 요소(`Pages/Index.razor`)를 참조하세요.

새로운 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스는 [ASP.NET Core의 Identity 모델])(xref:security/authentication/customize_identity_model)을 사용하는 경우와 같이 `DbContext`에 따라 연결 문자열을 구성할 수 있는 팩터리로 생성할 수 있습니다.

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x">구성 요소 수명으로 범위 지정</h3>

구성 요소의 수명 동안 존재하는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만들 수 있습니다. 그러면 해당 인스턴스를 [작업 단위](https://martinfowler.com/eaaCatalog/unitOfWork.html)로 사용하고 변경 내용 추적, 동시성 확인과 같은 기본 제공 기능을 활용할 수 있습니다.
팩터리를 사용하여 컨텍스트를 만든 후 구성 요소의 수명 동안 추적할 수 있습니다. 먼저 <xref:System.IDisposable>을 구현하고 `Pages/EditContact.razor`에 표시된 대로 팩터리를 삽입합니다.

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

샘플 앱이 구성 요소가 삭제될 때 컨텍스트가 삭제되는지 확인합니다.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

마지막으로 [`OnInitializedAsync`](xref:blazor/components/lifecycle)를 재정의하여 새 컨텍스트를 만듭니다. 샘플 앱에서 [`OnInitializedAsync`](xref:blazor/components/lifecycle)는 동일한 방법으로 연락처를 로드합니다.

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging">중요한 데이터 로깅 사용</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>에는 예외 메시지 및 프레임워크 로깅의 애플리케이션 데이터가 포함됩니다. 로깅된 데이터에는 엔터티 인스턴스 속성에 할당된 값과 데이터베이스로 전송된 명령의 매개 변수 값이 포함될 수 있습니다. <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>으로 데이터를 로깅하면 데이터베이스에 대해 실행되는 SQL 문을 로그할 때 암호와 기타 PII(개인 식별 정보)를 노출할 수 있으므로 **보안상 위험** 합니다.

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>은 개발 및 테스트 용도로만 사용하는 것이 좋습니다.

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server는 상태 저장 앱 프레임워크입니다. 앱은 서버에 대한 지속적인 연결을 유지하고, 사용자 상태는 ‘회로’의 서버 메모리에 저장됩니다. 사용자 상태의 한 예는 회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터입니다. Blazor Server에서 제공하는 고유한 애플리케이션 모델을 사용하려면 Entity Framework Core를 사용하는 특별한 방법이 필요합니다.

> [!NOTE]
> 이 문서에서는 Blazor Server 앱의 EF Core에 대해 살펴봅니다. Blazor WebAssembly 앱은 대부분 직접 데이터베이스 연결을 방지하는 WebAssembly 샌드박스에서 실행됩니다. Blazor WebAssembly에서 EF Core를 실행하는 것은 이 문서에서 다루지 않습니다.

<h2 id="sample-app-3x">샘플 앱</h2>

샘플 앱은 EF Core를 사용하는 Blazor Server 앱에 대한 참조로 작성되었습니다. 샘플 앱에는 정렬 및 필터링, 삭제, 추가 및 업데이트 작업을 포함하는 표가 포함되어 있습니다. 이 샘플에서는 EF Core를 사용하여 낙관적 동시성을 처리하는 방법을 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 샘플에서는 모든 플랫폼에서 사용할 수 있도록 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스를 사용합니다. 또한 생성되는 SQL 쿼리를 표시하도록 데이터베이스 로깅을 구성합니다. 이는 `appsettings.Development.json`에서 구성됩니다.

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

표, 추가 및 보기 구성 요소에서는 작업별로 하나의 컨텍스트를 생성하는 “context-per-operation” 패턴을 사용합니다. 편집 구성 요소에서는 구성 요소별로 하나의 컨텍스트를 생성하는 “context-per-component” 패턴을 사용합니다.

> [!NOTE]
> 이 항목의 일부 코드 예제에는 표시되지 않은 네임스페이스와 서비스가 필요합니다. Razor 예제에 대한 필수 [`@using`](xref:mvc/views/razor#using) 및 [`@inject`](xref:mvc/views/razor#inject) 지시문을 포함하여 완전히 작동하는 코드를 검사하려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)을 참조하세요.

<h2 id="database-access-3x">데이터베이스 액세스</h2>

EF Core에서는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용하여 [데이터베이스 액세스를 구성](/ef/core/miscellaneous/configuring-dbcontext)하고 [‘작업 단위’](https://martinfowler.com/eaaCatalog/unitOfWork.html) 역할을 합니다. EF Core는 컨텍스트를 기본적으로 ‘범위가 지정된’ 서비스로 등록하는 ASP.NET Core 앱에 대한 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장을 제공합니다. Blazor Server 앱에서는 사용자 회로 내의 구성 요소 간에 인스턴스가 공유되므로 이 경우 문제가 될 수 있습니다. <xref:Microsoft.EntityFrameworkCore.DbContext>는 스레드로부터 안전하지 않고 동시 사용을 위해 설계되지 않았습니다. 기존 수명은 다음과 같은 이유로 적합하지 않습니다.

* **Singleton** 은 앱의 모든 사용자에 대한 상태를 공유하고 부적절한 동시 사용을 초래합니다.
* **범위 지정** (기본값)은 동일한 사용자에 대한 구성 요소 간에 유사한 문제를 초래합니다.
* **임시** 는 요청별로 새 인스턴스를 생성하지만 구성 요소가 오래 지속될 수 있으므로 의도한 것보다 수명이 긴 컨텍스트가 생성됩니다.

다음 권장 사항은 Blazor Server 앱에서 EF Core를 사용하는 일관된 방법을 제공하도록 설계되었습니다.

* 기본적으로 작업당 하나의 컨텍스트를 사용하는 것이 좋습니다. 이 컨텍스트는 빠르고 낮은 오버헤드 인스턴스화를 위해 설계되었습니다.

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 플래그를 사용하여 여러 동시 작업을 방지합니다.

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  `try` 블록의 `Loading = true;` 줄 뒤에 작업을 추가합니다.

* EF Core의 [변경 내용 추적](/ef/core/querying/tracking) 또는 [동시성 제어](/ef/core/saving/concurrency)를 활용하는 장기 작업의 경우 [컨텍스트의 범위를 구성 요소의 수명으로 지정합니다](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">새 DbContext 인스턴스</h3>

새 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스를 만드는 가장 빠른 방법은 `new`를 사용하여 새 인스턴스를 만드는 것입니다. 하지만 일부 시나리오에서는 추가 종속성을 확인해야 합니다. 예를 들어 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions)를 사용하여 컨텍스트를 구성할 수 있습니다.

종속성을 사용하여 새 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만드는 권장 솔루션은 팩터리를 사용하는 것입니다. 샘플 앱은 `Data/DbContextFactory.cs`에서 자체 팩터리를 구현합니다.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

이전 팩터리에서

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>은 서비스 공급자를 통해 종속 항목을 충족합니다.
* `IDbContextFactory`는 EF Core ASP.NET Core 5.0 이상에서 사용할 수 있으므로 [ASP.NET Core 3.x에 대한 샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)에서 인터페이스가 구현됩니다.

다음 예에서는 [SQLite](https://www.sqlite.org/index.html)를 구성하고 데이터 로깅을 사용하도록 설정합니다. 이 코드에서는 확장 메서드를 사용하여 DI용 데이터베이스 팩터리를 구성하고 기본 옵션을 제공합니다.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

팩터리는 구성 요소에 삽입되어 새 인스턴스를 만드는 데 사용됩니다. 예를 들어 `Pages/Index.razor`에서는 다음과 같습니다.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper`는 `GridWrapper` 구성 요소에 대한 [구성 요소 참조](xref:blazor/components/index#capture-references-to-components)입니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)의 `Index` 구성 요소(`Pages/Index.razor`)를 참조하세요.

새로운 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스는 [ASP.NET Core의 Identity 모델])(xref:security/authentication/customize_identity_model)을 사용하는 경우와 같이 `DbContext`에 따라 연결 문자열을 구성할 수 있는 팩터리로 생성할 수 있습니다.

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x">구성 요소 수명으로 범위 지정</h3>

구성 요소의 수명 동안 존재하는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만들 수 있습니다. 그러면 해당 인스턴스를 [작업 단위](https://martinfowler.com/eaaCatalog/unitOfWork.html)로 사용하고 변경 내용 추적, 동시성 확인과 같은 기본 제공 기능을 활용할 수 있습니다.
팩터리를 사용하여 컨텍스트를 만든 후 구성 요소의 수명 동안 추적할 수 있습니다. 먼저 <xref:System.IDisposable>을 구현하고 `Pages/EditContact.razor`에 표시된 대로 팩터리를 삽입합니다.

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

샘플 앱이 구성 요소가 삭제될 때 컨텍스트가 삭제되는지 확인합니다.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

마지막으로 [`OnInitializedAsync`](xref:blazor/components/lifecycle)를 재정의하여 새 컨텍스트를 만듭니다. 샘플 앱에서 [`OnInitializedAsync`](xref:blazor/components/lifecycle)는 동일한 방법으로 연락처를 로드합니다.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

앞의 예제에서:

* `Busy`를 `true`로 설정한 경우 비동기 작업이 시작될 수 있습니다. `Busy`를 `false`로 다시 설정할 경우 비동기 작업을 완료해야 합니다.
* `catch` 블록에 추가 오류 처리 논리를 삽입합니다.

<h3 id="enable-sensitive-data-logging">중요한 데이터 로깅 사용</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>에는 예외 메시지 및 프레임워크 로깅의 애플리케이션 데이터가 포함됩니다. 로깅된 데이터에는 엔터티 인스턴스 속성에 할당된 값과 데이터베이스로 전송된 명령의 매개 변수 값이 포함될 수 있습니다. <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>으로 데이터를 로깅하면 데이터베이스에 대해 실행되는 SQL 문을 로그할 때 암호와 기타 PII(개인 식별 정보)를 노출할 수 있으므로 **보안상 위험** 합니다.

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>은 개발 및 테스트 용도로만 사용하는 것이 좋습니다.

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a>추가 자료

* [EF Core 설명서](/ef/)
