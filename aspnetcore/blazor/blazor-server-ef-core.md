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
ms.openlocfilehash: 10fc0afe84065f2c226d1e9c2f4314142369613a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97011886"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="b4c26-103">EFCore(Entity Framework Core)를 사용한 ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b4c26-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="b4c26-104">기준: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="b4c26-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b4c26-105">Blazor Server는 상태 저장 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b4c26-106">앱은 서버에 대한 지속적인 연결을 유지하고, 사용자 상태는 ‘회로’의 서버 메모리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b4c26-107">사용자 상태의 한 예는 회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b4c26-108">Blazor Server에서 제공하는 고유한 애플리케이션 모델을 사용하려면 Entity Framework Core를 사용하는 특별한 방법이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c26-109">이 문서에서는 Blazor Server 앱의 EF Core에 대해 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b4c26-110">Blazor WebAssembly 앱은 대부분 직접 데이터베이스 연결을 방지하는 WebAssembly 샌드박스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b4c26-111">Blazor WebAssembly에서 EF Core를 실행하는 것은 이 문서에서 다루지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="b4c26-112">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="b4c26-112">Sample app</span></span></h2>

<span data-ttu-id="b4c26-113">샘플 앱은 EF Core를 사용하는 Blazor Server 앱에 대한 참조로 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b4c26-114">샘플 앱에는 정렬 및 필터링, 삭제, 추가 및 업데이트 작업을 포함하는 표가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b4c26-115">이 샘플에서는 EF Core를 사용하여 낙관적 동시성을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b4c26-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4c26-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b4c26-117">이 샘플에서는 모든 플랫폼에서 사용할 수 있도록 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b4c26-118">또한 생성되는 SQL 쿼리를 표시하도록 데이터베이스 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b4c26-119">이는 `appsettings.Development.json`에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b4c26-120">표, 추가 및 보기 구성 요소에서는 작업별로 하나의 컨텍스트를 생성하는 “context-per-operation” 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b4c26-121">편집 구성 요소에서는 구성 요소별로 하나의 컨텍스트를 생성하는 “context-per-component” 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c26-122">이 항목의 일부 코드 예제에는 표시되지 않은 네임스페이스와 서비스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b4c26-123">Razor 예제에 대한 필수 [`@using`](xref:mvc/views/razor#using) 및 [`@inject`](xref:mvc/views/razor#inject) 지시문을 포함하여 완전히 작동하는 코드를 검사하려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4c26-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="b4c26-124">데이터베이스 액세스</span><span class="sxs-lookup"><span data-stu-id="b4c26-124">Database access</span></span></h2>

<span data-ttu-id="b4c26-125">EF Core에서는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용하여 [데이터베이스 액세스를 구성](/ef/core/miscellaneous/configuring-dbcontext)하고 [‘작업 단위’](https://martinfowler.com/eaaCatalog/unitOfWork.html) 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b4c26-126">EF Core는 컨텍스트를 기본적으로 ‘범위가 지정된’ 서비스로 등록하는 ASP.NET Core 앱에 대한 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b4c26-127">Blazor Server 앱에서는 사용자 회로 내의 구성 요소 간에 인스턴스가 공유되므로 범위가 지정된 서비스 등록을 사용할 경우 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b4c26-128"><xref:Microsoft.EntityFrameworkCore.DbContext>는 스레드로부터 안전하지 않고 동시 사용을 위해 설계되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b4c26-129">기존 수명은 다음과 같은 이유로 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b4c26-130">**Singleton** 은 앱의 모든 사용자에 대한 상태를 공유하고 부적절한 동시 사용을 초래합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b4c26-131">**범위 지정**(기본값)은 동일한 사용자에 대한 구성 요소 간에 유사한 문제를 초래합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b4c26-132">**임시** 는 요청별로 새 인스턴스를 생성하지만 구성 요소가 오래 지속될 수 있으므로 의도한 것보다 수명이 긴 컨텍스트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b4c26-133">다음 권장 사항은 Blazor Server 앱에서 EF Core를 사용하는 일관된 방법을 제공하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b4c26-134">기본적으로 작업당 하나의 컨텍스트를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="b4c26-135">이 컨텍스트는 빠르고 낮은 오버헤드 인스턴스화를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b4c26-136">플래그를 사용하여 여러 동시 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b4c26-137">`try` 블록의 `Loading = true;` 줄 뒤에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b4c26-138">EF Core의 [변경 내용 추적](/ef/core/querying/tracking) 또는 [동시성 제어](/ef/core/saving/concurrency)를 활용하는 장기 작업의 경우 [컨텍스트의 범위를 구성 요소의 수명으로 지정합니다](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="b4c26-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="b4c26-139">새 DbContext 인스턴스</span><span class="sxs-lookup"><span data-stu-id="b4c26-139">New DbContext instances</span></span></h3>

<span data-ttu-id="b4c26-140">새 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스를 만드는 가장 빠른 방법은 `new`를 사용하여 새 인스턴스를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b4c26-141">하지만 일부 시나리오에서는 추가 종속성을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b4c26-142">예를 들어 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions)를 사용하여 컨텍스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b4c26-143">종속성을 사용하여 새 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만드는 권장 솔루션은 팩터리를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b4c26-144">EF Core 5.0 이상에서는 새 컨텍스트를 만들기 위한 기본 제공 팩터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="b4c26-145">다음 예에서는 [SQLite](https://www.sqlite.org/index.html)를 구성하고 데이터 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b4c26-146">이 코드에서는 [확장 메서드(`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs)를 사용하여 DI용 데이터베이스 팩터리를 구성하고 기본 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b4c26-147">팩터리는 구성 요소에 삽입되어 새 인스턴스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b4c26-148">예를 들어 `Pages/Index.razor`에서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b4c26-149">`Wrapper`는 `GridWrapper` 구성 요소에 대한 [구성 요소 참조](xref:blazor/components/index#capture-references-to-components)입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b4c26-150">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)의 `Index` 구성 요소(`Pages/Index.razor`)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4c26-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b4c26-151">새로운 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스는 [ASP.NET Core의 Identity 모델](xref:security/authentication/customize_identity_model)을 사용하는 경우와 같이 `DbContext`에 따라 연결 문자열을 구성할 수 있는 팩터리로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="b4c26-152">구성 요소 수명으로 범위 지정</span><span class="sxs-lookup"><span data-stu-id="b4c26-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b4c26-153">구성 요소의 수명 동안 존재하는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b4c26-154">그러면 해당 인스턴스를 [작업 단위](https://martinfowler.com/eaaCatalog/unitOfWork.html)로 사용하고 변경 내용 추적, 동시성 확인과 같은 기본 제공 기능을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b4c26-155">팩터리를 사용하여 컨텍스트를 만든 후 구성 요소의 수명 동안 추적할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b4c26-156">먼저 <xref:System.IDisposable>을 구현하고 `Pages/EditContact.razor`에 표시된 대로 팩터리를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b4c26-157">샘플 앱이 구성 요소가 삭제될 때 컨텍스트가 삭제되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b4c26-158">마지막으로 [`OnInitializedAsync`](xref:blazor/components/lifecycle)를 재정의하여 새 컨텍스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b4c26-159">샘플 앱에서 [`OnInitializedAsync`](xref:blazor/components/lifecycle)는 동일한 방법으로 연락처를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b4c26-160">중요한 데이터 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="b4c26-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b4c26-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>에는 예외 메시지 및 프레임워크 로깅의 애플리케이션 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b4c26-162">로깅된 데이터에는 엔터티 인스턴스 속성에 할당된 값과 데이터베이스로 전송된 명령의 매개 변수 값이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b4c26-163"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>으로 데이터를 로깅하면 데이터베이스에 대해 실행되는 SQL 문을 로그할 때 암호와 기타 PII(개인 식별 정보)를 노출할 수 있으므로 **보안상 위험** 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b4c26-164"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>은 개발 및 테스트 용도로만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

<span data-ttu-id="b4c26-165">Blazor Server는 상태 저장 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-165">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b4c26-166">앱은 서버에 대한 지속적인 연결을 유지하고, 사용자 상태는 ‘회로’의 서버 메모리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b4c26-167">사용자 상태의 한 예는 회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b4c26-168">Blazor Server에서 제공하는 고유한 애플리케이션 모델을 사용하려면 Entity Framework Core를 사용하는 특별한 방법이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-168">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c26-169">이 문서에서는 Blazor Server 앱의 EF Core에 대해 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-169">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b4c26-170">Blazor WebAssembly 앱은 대부분 직접 데이터베이스 연결을 방지하는 WebAssembly 샌드박스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-170">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b4c26-171">Blazor WebAssembly에서 EF Core를 실행하는 것은 이 문서에서 다루지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-171">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="b4c26-172">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="b4c26-172">Sample app</span></span></h2>

<span data-ttu-id="b4c26-173">샘플 앱은 EF Core를 사용하는 Blazor Server 앱에 대한 참조로 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-173">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b4c26-174">샘플 앱에는 정렬 및 필터링, 삭제, 추가 및 업데이트 작업을 포함하는 표가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b4c26-175">이 샘플에서는 EF Core를 사용하여 낙관적 동시성을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b4c26-176">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4c26-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b4c26-177">이 샘플에서는 모든 플랫폼에서 사용할 수 있도록 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b4c26-178">또한 생성되는 SQL 쿼리를 표시하도록 데이터베이스 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b4c26-179">이는 `appsettings.Development.json`에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b4c26-180">표, 추가 및 보기 구성 요소에서는 작업별로 하나의 컨텍스트를 생성하는 “context-per-operation” 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b4c26-181">편집 구성 요소에서는 구성 요소별로 하나의 컨텍스트를 생성하는 “context-per-component” 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b4c26-182">이 항목의 일부 코드 예제에는 표시되지 않은 네임스페이스와 서비스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b4c26-183">Razor 예제에 대한 필수 [`@using`](xref:mvc/views/razor#using) 및 [`@inject`](xref:mvc/views/razor#inject) 지시문을 포함하여 완전히 작동하는 코드를 검사하려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4c26-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="b4c26-184">데이터베이스 액세스</span><span class="sxs-lookup"><span data-stu-id="b4c26-184">Database access</span></span></h2>

<span data-ttu-id="b4c26-185">EF Core에서는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 사용하여 [데이터베이스 액세스를 구성](/ef/core/miscellaneous/configuring-dbcontext)하고 [‘작업 단위’](https://martinfowler.com/eaaCatalog/unitOfWork.html) 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b4c26-186">EF Core는 컨텍스트를 기본적으로 ‘범위가 지정된’ 서비스로 등록하는 ASP.NET Core 앱에 대한 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b4c26-187">Blazor Server 앱에서는 사용자 회로 내의 구성 요소 간에 인스턴스가 공유되므로 이 경우 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-187">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b4c26-188"><xref:Microsoft.EntityFrameworkCore.DbContext>는 스레드로부터 안전하지 않고 동시 사용을 위해 설계되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b4c26-189">기존 수명은 다음과 같은 이유로 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b4c26-190">**Singleton** 은 앱의 모든 사용자에 대한 상태를 공유하고 부적절한 동시 사용을 초래합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b4c26-191">**범위 지정**(기본값)은 동일한 사용자에 대한 구성 요소 간에 유사한 문제를 초래합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b4c26-192">**임시** 는 요청별로 새 인스턴스를 생성하지만 구성 요소가 오래 지속될 수 있으므로 의도한 것보다 수명이 긴 컨텍스트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b4c26-193">다음 권장 사항은 Blazor Server 앱에서 EF Core를 사용하는 일관된 방법을 제공하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-193">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b4c26-194">기본적으로 작업당 하나의 컨텍스트를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="b4c26-195">이 컨텍스트는 빠르고 낮은 오버헤드 인스턴스화를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b4c26-196">플래그를 사용하여 여러 동시 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-196">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b4c26-197">`try` 블록의 `Loading = true;` 줄 뒤에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b4c26-198">EF Core의 [변경 내용 추적](/ef/core/querying/tracking) 또는 [동시성 제어](/ef/core/saving/concurrency)를 활용하는 장기 작업의 경우 [컨텍스트의 범위를 구성 요소의 수명으로 지정합니다](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="b4c26-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="b4c26-199">새 DbContext 인스턴스</span><span class="sxs-lookup"><span data-stu-id="b4c26-199">New DbContext instances</span></span></h3>

<span data-ttu-id="b4c26-200">새 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스를 만드는 가장 빠른 방법은 `new`를 사용하여 새 인스턴스를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b4c26-201">하지만 일부 시나리오에서는 추가 종속성을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b4c26-202">예를 들어 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions)를 사용하여 컨텍스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b4c26-203">종속성을 사용하여 새 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만드는 권장 솔루션은 팩터리를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b4c26-204">샘플 앱은 `Data/DbContextFactory.cs`에서 자체 팩터리를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="b4c26-205">이전 팩터리에서</span><span class="sxs-lookup"><span data-stu-id="b4c26-205">In the preceding factory:</span></span>

* <span data-ttu-id="b4c26-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>은 서비스 공급자를 통해 종속 항목을 충족합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="b4c26-207">`IDbContextFactory`는 EF Core ASP.NET Core 5.0 이상에서 사용할 수 있으므로 [ASP.NET Core 3.x에 대한 샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)에서 인터페이스가 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="b4c26-208">다음 예에서는 [SQLite](https://www.sqlite.org/index.html)를 구성하고 데이터 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b4c26-209">이 코드에서는 확장 메서드를 사용하여 DI용 데이터베이스 팩터리를 구성하고 기본 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b4c26-210">팩터리는 구성 요소에 삽입되어 새 인스턴스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b4c26-211">예를 들어 `Pages/Index.razor`에서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b4c26-212">`Wrapper`는 `GridWrapper` 구성 요소에 대한 [구성 요소 참조](xref:blazor/components/index#capture-references-to-components)입니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b4c26-213">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)의 `Index` 구성 요소(`Pages/Index.razor`)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4c26-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b4c26-214">새로운 <xref:Microsoft.EntityFrameworkCore.DbContext> 인스턴스는 [ASP.NET Core의 Identity 모델])(xref:security/authentication/customize_identity_model)을 사용하는 경우와 같이 `DbContext`에 따라 연결 문자열을 구성할 수 있는 팩터리로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="b4c26-215">구성 요소 수명으로 범위 지정</span><span class="sxs-lookup"><span data-stu-id="b4c26-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b4c26-216">구성 요소의 수명 동안 존재하는 <xref:Microsoft.EntityFrameworkCore.DbContext>를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b4c26-217">그러면 해당 인스턴스를 [작업 단위](https://martinfowler.com/eaaCatalog/unitOfWork.html)로 사용하고 변경 내용 추적, 동시성 확인과 같은 기본 제공 기능을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b4c26-218">팩터리를 사용하여 컨텍스트를 만든 후 구성 요소의 수명 동안 추적할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b4c26-219">먼저 <xref:System.IDisposable>을 구현하고 `Pages/EditContact.razor`에 표시된 대로 팩터리를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b4c26-220">샘플 앱이 구성 요소가 삭제될 때 컨텍스트가 삭제되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b4c26-221">마지막으로 [`OnInitializedAsync`](xref:blazor/components/lifecycle)를 재정의하여 새 컨텍스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b4c26-222">샘플 앱에서 [`OnInitializedAsync`](xref:blazor/components/lifecycle)는 동일한 방법으로 연락처를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="b4c26-223">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="b4c26-223">In the preceding example:</span></span>

* <span data-ttu-id="b4c26-224">`Busy`를 `true`로 설정한 경우 비동기 작업이 시작될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="b4c26-225">`Busy`를 `false`로 다시 설정할 경우 비동기 작업을 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="b4c26-226">`catch` 블록에 추가 오류 처리 논리를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b4c26-227">중요한 데이터 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="b4c26-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b4c26-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>에는 예외 메시지 및 프레임워크 로깅의 애플리케이션 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b4c26-229">로깅된 데이터에는 엔터티 인스턴스 속성에 할당된 값과 데이터베이스로 전송된 명령의 매개 변수 값이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b4c26-230"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>으로 데이터를 로깅하면 데이터베이스에 대해 실행되는 SQL 문을 로그할 때 암호와 기타 PII(개인 식별 정보)를 노출할 수 있으므로 **보안상 위험** 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b4c26-231"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>은 개발 및 테스트 용도로만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b4c26-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b4c26-232">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b4c26-232">Additional resources</span></span>

* [<span data-ttu-id="b4c26-233">EF Core 설명서</span><span class="sxs-lookup"><span data-stu-id="b4c26-233">EF Core documentation</span></span>](/ef/)
