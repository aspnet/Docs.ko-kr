---
title: ASP.NET에서 ASP.NET Core 2.0으로 마이그레이션
author: isaac2004
description: 기존 ASP.NET MVC 또는 Web API 응용 프로그램을 ASP.NET Core 2.0으로 마이그레이션하기 위한 지침을 받습니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051332"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="7b0a2-103">ASP.NET에서 ASP.NET Core 2.0으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="7b0a2-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="7b0a2-104">작성자: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="7b0a2-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="7b0a2-105">이 문서는 ASP.NET 애플리케이션을 ASP.NET Core 2.0으로 마이그레이션하기 위한 참조 가이드로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b0a2-106">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7b0a2-106">Prerequisites</span></span>

<span data-ttu-id="7b0a2-107">[.Net 다운로드: Windows](https://dotnet.microsoft.com/download)에서 다음 중 **하나** 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="7b0a2-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="7b0a2-108">.NET Core SDK</span></span>
* <span data-ttu-id="7b0a2-109">Visual Studio for Windows</span><span class="sxs-lookup"><span data-stu-id="7b0a2-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="7b0a2-110">**ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="7b0a2-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="7b0a2-111">**.NET Core 플랫폼 간 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="7b0a2-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="7b0a2-112">대상 프레임워크</span><span class="sxs-lookup"><span data-stu-id="7b0a2-112">Target frameworks</span></span>

<span data-ttu-id="7b0a2-113">ASP.NET Core 2.0 프로젝트를 사용하면 개발자가 유연하게 .NET Core, .NET Framework 또는 두 항목을 모두 대상으로 지정하거나 둘 다 대상으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="7b0a2-114">가장 적절한 대상 프레임워크를 결정하려면 [서버 앱에 대해 .NET Core와 .NET Framework 중에서 선택](/dotnet/standard/choosing-core-framework-server)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="7b0a2-115">.NET Framework를 대상으로 지정할 경우 프로젝트는 개별 NuGet 패키지를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="7b0a2-116">.NET Core를 대상으로 지정하면 ASP.NET Core 2.0 [메타패키지](xref:fundamentals/metapackage) 덕분에 수많은 명시적 패키지 참조를 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="7b0a2-117">프로젝트에서 `Microsoft.AspNetCore.All` 메타패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="7b0a2-118">메타패키지를 사용하면 메타패키지에서 참조되는 패키지가 앱과 함께 배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="7b0a2-119">.NET Core 런타임 저장소에는 이러한 자산이 포함되고 해당 자산은 성능을 개선하도록 미리 컴파일되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="7b0a2-120"><xref:fundamentals/metapackage>자세한 내용은을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="7b0a2-121">프로젝트 구조 차이점</span><span class="sxs-lookup"><span data-stu-id="7b0a2-121">Project structure differences</span></span>

<span data-ttu-id="7b0a2-122">*.csproj* 파일 형식은 ASP.NET Core에서 간소화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="7b0a2-123">몇 가지 주요 변경 내용은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-123">Some notable changes include:</span></span>

* <span data-ttu-id="7b0a2-124">파일을 프로젝트의 일부로 간주하기 위해 파일을 명시적으로 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="7b0a2-125">이 덕분에 대규모 팀에서 작업할 때 XML 병합 충돌의 위험이 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="7b0a2-126">다른 프로젝트에 대한 GUID 기반 참조가 없으므로 파일 가독성이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="7b0a2-127">Visual Studio에서 파일을 언로드하지 않고 파일을 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Visual Studio 2017에서 CSPROJ 상황에 맞는 메뉴 옵션 편집](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="7b0a2-129">Global.asax 파일 바꾸기</span><span class="sxs-lookup"><span data-stu-id="7b0a2-129">Global.asax file replacement</span></span>

<span data-ttu-id="7b0a2-130">ASP.NET Core에는 앱을 부트스트랩하기 위한 새로운 메커니즘이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="7b0a2-131">ASP.NET 애플리케이션의 진입점은 *Global.asax* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="7b0a2-132">경로 구성 및 필터/영역 등록과 같은 작업은 *Global.asax* 파일에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="7b0a2-133">이 방법은 구현을 방해하는 방식으로 애플리케이션 및 애플리케이션이 배포되는 서버를 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="7b0a2-134">분리 작업 시 여러 프레임워크를 함께 사용할 수 있는 더 분명한 방식을 제공하도록 [OWIN](https://owin.org/)이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="7b0a2-135">OWIN은 필요한 모듈만 추가하기 위한 파이프라인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="7b0a2-136">호스팅 환경에서는 [Startup](xref:fundamentals/startup) 함수를 사용하여 서비스 및 앱 요청 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="7b0a2-137">`Startup`은 미들웨어 집합을 애플리케이션에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="7b0a2-138">각 요청에 대해 애플리케이션은 기존 처리기 집합에 대한 연결된 목록의 head 포인터를 사용하여 각 미들웨어 구성 요소를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="7b0a2-139">각 미들웨어 구성 요소는 요청 처리 파이프라인에 하나 이상의 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="7b0a2-140">이 작업을 수행하려면 목록의 새 헤드인 처리기에 참조를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="7b0a2-141">각 처리기는 목록의 다음 처리기를 기억하고 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="7b0a2-142">ASP.NET Core에서는 애플리케이션에 대한 진입점이 `Startup`이고 더 이상 *Global.asax* 에 대한 종속성을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="7b0a2-143">.NET Framework에서 OWIN을 사용할 경우 다음과 같은 항목을 파이프라인으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="7b0a2-144">이렇게 하면 기본 경로가 구성되고 기본적으로 JSON을 통한 XmlSerialization으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="7b0a2-145">필요에 따라 이 파이프라인에 다른 미들웨어를 추가합니다(로딩 서비스, 구성 설정, 정적 파일 등).</span><span class="sxs-lookup"><span data-stu-id="7b0a2-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="7b0a2-146">ASP.NET Core는 비슷한 방법을 사용하지만 항목을 처리하는 데 OWIN을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="7b0a2-147">대신, 이 작업에는 *Program.cs* `Main` 메서드(콘솔 애플리케이션과 비슷함)가 사용되고 `Startup`도 이 메서드를 통해 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="7b0a2-148">`Startup`에는 `Configure` 메서드가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="7b0a2-149">`Configure`에서 파이프라인에 필요한 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="7b0a2-150">기본 웹 사이트 템플릿을 기반으로 한 다음 예제에서는 여러 확장 메서드를 사용하여 다음 지원을 통해 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="7b0a2-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="7b0a2-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="7b0a2-152">오류 페이지</span><span class="sxs-lookup"><span data-stu-id="7b0a2-152">Error pages</span></span>
* <span data-ttu-id="7b0a2-153">정적 파일</span><span class="sxs-lookup"><span data-stu-id="7b0a2-153">Static files</span></span>
* <span data-ttu-id="7b0a2-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7b0a2-154">ASP.NET Core MVC</span></span>
* :::no-loc(Identity):::

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="7b0a2-155">호스트와 애플리케이션이 분리되었으므로 나중에 유연하게 다른 플랫폼으로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="7b0a2-156">시작 및 미들웨어 ASP.NET Core에 대 한 자세한 내용은를 참조 하십시오 <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="7b0a2-157">구성 저장</span><span class="sxs-lookup"><span data-stu-id="7b0a2-157">Storing configurations</span></span>

<span data-ttu-id="7b0a2-158">ASP.NET은 정렬 설정을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="7b0a2-159">예를 들어 이러한 설정은 애플리케이션이 배포된 환경을 지원하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="7b0a2-160">일반적으로 모든 사용자 지정 키 값 쌍은 *Web.config* 파일의 `<appSettings>` 섹션에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="7b0a2-161">애플리케이션은 `System.Configuration` 네임스페이스의 `ConfigurationManager.AppSettings` 컬렉션을 사용하여 이러한 설정을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="7b0a2-162">ASP.NET Core는 애플리케이션에 대한 구성 데이터를 파일에 저장하고 미들웨어 부트스트래핑의 일부로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="7b0a2-163">프로젝트 템플릿에 사용 되는 기본 파일은 *:::no-loc(appsettings.json):::* 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-163">The default file used in the project templates is *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="7b0a2-164">파일을 애플리케이션 내부의 `IConfiguration` 인스턴스로 로드하는 작업은 *Startup.cs* 에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="7b0a2-165">앱은 `Configuration`을 읽어서 설정을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="7b0a2-166">DI([종속성 주입](xref:fundamentals/dependency-injection))를 사용하여 이러한 값으로 서비스를 로드하는 것과 같이 이 방법을 확장하여 프로세스를 더 강력하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="7b0a2-167">DI 방법은 강력한 형식의 구성 개체 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="7b0a2-168">**참고:** ASP.NET Core 구성에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="7b0a2-169">네이티브 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7b0a2-169">Native dependency injection</span></span>

<span data-ttu-id="7b0a2-170">크고 확장 가능한 애플리케이션을 빌드할 때 중요한 목표는 구성 요소와 서비스를 느슨하게 결합하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="7b0a2-171">[종속성 주입](xref:fundamentals/dependency-injection) 은이를 달성 하는 데 널리 사용 되는 기술 이며 ASP.NET Core의 네이티브 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="7b0a2-172">ASP.NET 응용 프로그램에서 개발자는 타사 라이브러리를 사용 하 여 종속성 주입을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="7b0a2-173">이러한 라이브러리 중 하나는 Microsoft Patterns & Practices에서 제공하는 [Unity](https://github.com/unitycontainer/unity)입니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="7b0a2-174">Unity를 사용 하 여 종속성 주입을 설정 하는 예제는 `IDependencyResolver` 를 래핑하는 구현입니다 `UnityContainer` .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="7b0a2-175">`UnityContainer`의 인스턴스를 만들고, 서비스를 등록하고, `HttpConfiguration`의 종속성 확인자를 컨테이너에 대한 `UnityResolver`의 새 인스턴스로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="7b0a2-176">필요한 경우 `IProductRepository`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="7b0a2-177">종속성 주입은 ASP.NET Core의 일부 이므로에서 서비스를 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="7b0a2-178">Unity에서 삽입한 것처럼 리포지토리는 어디든지 삽입될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="7b0a2-179">ASP.NET Core의 종속성 주입에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="7b0a2-180">정적 파일 지원</span><span class="sxs-lookup"><span data-stu-id="7b0a2-180">Serving static files</span></span>

<span data-ttu-id="7b0a2-181">웹 개발의 중요한 부분은 정적 클라이언트 쪽 자산을 지원하는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="7b0a2-182">정적 파일의 가장 일반적인 예로는 HTML, CSS, Javascript 및 이미지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="7b0a2-183">이러한 파일은 앱(또는 CDN)의 게시된 위치에 저장되고 요청을 통해 로드될 수 있도록 참조되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="7b0a2-184">이 프로세스는 ASP.NET Core에서 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="7b0a2-185">ASP.NET에서 정적 파일은 다양한 디렉터리에 저장되고 뷰에서 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="7b0a2-186">ASP.NET Core에서 정적 파일은 별도로 구성되지 않는 한 “웹 루트”( *&lt;content root&gt;/wwwroot* )에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-186">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="7b0a2-187">파일은 `Startup.Configure`에서 `UseStaticFiles` 확장 메서드를 호출하는 방식으로 요청 파이프라인에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="7b0a2-188">**참고:** .NET Framework를 대상으로 지정할 경우 NuGet 패키지 `Microsoft.AspNetCore.StaticFiles`를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="7b0a2-189">예를 들어 *wwwroot/images* 폴더의 이미지 자산은 `http://<app>/images/<imageFileName>`과 같은 위치의 브라우저에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b0a2-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="7b0a2-190">**참고:** ASP.NET Core에서 정적 파일을 제공 하는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="7b0a2-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b0a2-191">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7b0a2-191">Additional resources</span></span>

* [<span data-ttu-id="7b0a2-192">.NET Core로 라이브러리 이식</span><span class="sxs-lookup"><span data-stu-id="7b0a2-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
