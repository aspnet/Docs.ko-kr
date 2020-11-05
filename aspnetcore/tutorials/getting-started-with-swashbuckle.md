---
title: Swashbuckle 및 ASP.NET Core 시작
author: zuckerthoben
description: ASP.NET Core Web API 프로젝트에 Swashbuckle을 추가하여 Swagger UI를 통합하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 9fbd4bc5e91d8f05afc99a4f04c706399f83a518
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057611"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="9106e-103">Swashbuckle 및 ASP.NET Core 시작</span><span class="sxs-lookup"><span data-stu-id="9106e-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="9106e-104">작성자: [Shayne Boyer](https://twitter.com/spboyer) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9106e-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9106e-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9106e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9106e-106">Swashbuckle에 대한 세 가지 주 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="9106e-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` 개체를 JSON 엔드포인트로 노출하기 위한 Swagger 개체 모델 및 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="9106e-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="9106e-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): 경로, 컨트롤러 및 모델에서 직접 `SwaggerDocument` 개체를 빌드하는 Swagger 생성기.</span><span class="sxs-lookup"><span data-stu-id="9106e-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="9106e-109">일반적으로 Swagger 엔드포인트 미들웨어와 결합되어 자동으로 Swagger JSON을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="9106e-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI 도구의 포함된 버전.</span><span class="sxs-lookup"><span data-stu-id="9106e-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="9106e-111">Swagger JSON을 해석하여 웹 API 기능을 설명하기 위한 다양한 사용자 지정 가능한 환경을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="9106e-112">여기에는 공용 메서드에 대한 기본 제공 테스트 도구가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="9106e-113">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="9106e-113">Package installation</span></span>

<span data-ttu-id="9106e-114">다음 방법으로 Swashbuckle을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="9106e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9106e-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9106e-116">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="9106e-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="9106e-117">**보기** > **다른 창** > **패키지 관리자 콘솔** 로 이동</span><span class="sxs-lookup"><span data-stu-id="9106e-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="9106e-118">*TodoApi.csproj* 파일이 위치한 디렉터리로 이동</span><span class="sxs-lookup"><span data-stu-id="9106e-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="9106e-119">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* <span data-ttu-id="9106e-120">**NuGet 패키지 관리** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="9106e-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="9106e-121">**솔루션 탐색기** > **NuGet 패키지 관리** 에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="9106e-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="9106e-122">**패키지 소스** 를 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="9106e-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="9106e-123">"시험판 포함" 옵션을 사용하도록 설정했는지 확인</span><span class="sxs-lookup"><span data-stu-id="9106e-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="9106e-124">검색 상자에 “Swashbuckle.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="9106e-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="9106e-125">**찾아보기** 탭에서 최신 “Swashbuckle.AspNetCore” 패키지를 선택하고 **설치** 클릭</span><span class="sxs-lookup"><span data-stu-id="9106e-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9106e-126">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9106e-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9106e-127">**Solution Pad** 에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택</span><span class="sxs-lookup"><span data-stu-id="9106e-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="9106e-128">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="9106e-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="9106e-129">"시험판 패키지 표시" 옵션을 사용하도록 설정했는지 확인</span><span class="sxs-lookup"><span data-stu-id="9106e-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="9106e-130">검색 상자에 “Swashbuckle.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="9106e-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="9106e-131">결과 창에서 최신 "Swashbuckle.AspNetCore" 패키지를 선택하고 **패키지 추가** 클릭</span><span class="sxs-lookup"><span data-stu-id="9106e-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="9106e-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9106e-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9106e-133">**통합 터미널** 에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-133">Run the following command from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="9106e-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9106e-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9106e-135">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="9106e-136">Swagger 미들웨어 추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="9106e-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="9106e-137">`Startup.ConfigureServices` 메서드의 서비스 컬렉션에 Swagger 생성기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-137">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="9106e-138">`Startup.Configure` 메서드에서 생성된 JSON 문서 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-138">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="9106e-139">Swashbuckle은 MVC의 <xref:Microsoft.AspNetCore.Mvc.ApiExplorer>를 사용하여 경로 및 엔드포인트를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-139">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="9106e-140">프로젝트에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>를 호출하면 경로 및 엔드포인트가 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-140">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="9106e-141"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>를 호출하는 경우 <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> 메서드를 명시적으로 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-141">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="9106e-142">자세한 내용은 [Swashbuckle, ApiExplorer 및 라우팅](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9106e-142">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="9106e-143">위의 `UseSwaggerUI` 메서드 호출은 [정적 파일 미들웨어](xref:fundamentals/static-files)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-143">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="9106e-144">.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-144">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="9106e-145">앱을 시작하고 `http://localhost:<port>/swagger/v1/swagger.json`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-145">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9106e-146">엔드포인트를 설명하는 생성된 문서는 [OpenAPI 사양(openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson)에 표시된 것과 같이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-146">The generated document describing the endpoints appears as shown in [OpenAPI specification (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span></span>

<span data-ttu-id="9106e-147">Swagger UI는 `http://localhost:<port>/swagger`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-147">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="9106e-148">Swagger UI를 통해 API를 탐색하고 다른 프로그램에 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-148">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="9106e-149">앱의 루트(`http://localhost:<port>/`)에서 Swagger UI를 제공하려면 `RoutePrefix` 속성을 빈 문자열로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-149">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="9106e-150">IIS 또는 역방향 프록시에서 디렉터리를 사용하는 경우 `./` 접두사를 사용하여 Swagger 엔드포인트를 상대 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-150">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="9106e-151">예: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="9106e-151">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9106e-152">`/swagger/v1/swagger.json`을 사용하면 앱이 URL의 실제 루트(사용되는 경우 경로 접두사)에서 JSON 파일을 찾도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-152">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="9106e-153">예를 들어 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` 대신 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-153">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="9106e-154">기본적으로 Swashbuckle은 공식적으로 OpenAPI 사양이라고 하는 사양의 버전 3.0에서 Swagger JSON을 생성하고 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-154">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="9106e-155">이전 버전과의 호환성을 지원하기 위해 2.0 형식으로 JSON을 공개하도록 옵트인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-155">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="9106e-156">현재 OpenAPI 버전 2.0을 지원하는 Microsoft Power Apps 및 Microsoft Flow와 같은 통합의 경우 이 2.0 형식이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-156">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="9106e-157">2\.0 형식을 옵트인하려면 `Startup.Configure`에서 `SerializeAsV2` 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-157">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="9106e-158">사용자 지정 및 확장</span><span class="sxs-lookup"><span data-stu-id="9106e-158">Customize and extend</span></span>

<span data-ttu-id="9106e-159">Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-159">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="9106e-160">`Startup` 클래스에서 다음 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-160">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="9106e-161">API 정보 및 설명</span><span class="sxs-lookup"><span data-stu-id="9106e-161">API info and description</span></span>

<span data-ttu-id="9106e-162">`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-162">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="9106e-163">`Startup` 클래스에서는 `OpenApiInfo` 클래스를 사용하기 위해 다음 네임스페이스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-163">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="9106e-164">`OpenApiInfo` 클래스를 사용하여 UI에 표시되는 정보를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-164">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="9106e-165">Swagger UI는 버전의 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-165">The Swagger UI displays the version's information:</span></span>

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="9106e-167">XML 주석</span><span class="sxs-lookup"><span data-stu-id="9106e-167">XML comments</span></span>

<span data-ttu-id="9106e-168">XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-168">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="9106e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9106e-169">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="9106e-170">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-170">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="9106e-171">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="9106e-172">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-172">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="9106e-173">**빌드** 탭의 **출력** 섹션에서 **XML 설명서 파일** 상자를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-173">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9106e-174">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9106e-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="9106e-175">*Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-175">From the *Solution Pad* , press **control** and click the project name.</span></span> <span data-ttu-id="9106e-176">**도구** > **파일 편집** 으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-176">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="9106e-177">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="9106e-178">**프로젝트 옵션** 대화 상자 > **빌드** > **컴파일러** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-178">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="9106e-179">**일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택</span><span class="sxs-lookup"><span data-stu-id="9106e-179">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="9106e-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9106e-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9106e-181">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-181">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="9106e-182">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9106e-182">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9106e-183">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="9106e-184">XML 주석을 사용하면 문서화되지 않은 public 형식과 멤버에 대한 디버그 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-184">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="9106e-185">문서화되지 않은 형식 및 멤버는 경고 메시지로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-185">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="9106e-186">예를 들어 다음 메시지는 경고 코드 1591 위반을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-186">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="9106e-187">프로젝트 전반에서 경고를 표시하지 않으려면 프로젝트 파일에서 무시할 경고 코드의 세미콜론으로 구분된 목록을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-187">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="9106e-188">`$(NoWarn);`에 경고 코드를 추가하면 [C# 기본값](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-188">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="9106e-189">특정 멤버에 대해서만 경고를 표시하지 않으려면 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 전처리기 지시문에 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-189">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="9106e-190">이 방법은 API 문서를 통해 노출하지 않아야 하는 코드에 유용합니다. 다음 예제에서는 전체 `Program` 클래스에 대해 경고 코드 CS1591이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-190">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="9106e-191">클래스 정의를 닫으면 경고 코드 적용이 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-191">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="9106e-192">여러 경고 코드는 쉼표로 구분된 목록으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-192">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="9106e-193">이전 지침에 따라 생성된 XML 파일을 사용하도록 Swagger를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-193">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="9106e-194">Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-194">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="9106e-195">예를 들어 *TodoApi.XML* 파일은 Windows에는 유효하지만 CentOS에는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-195">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="9106e-196">이전 코드에서 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)은 웹 API 프로젝트의 이름과 일치하는 XML 파일 이름을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-196">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="9106e-197">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) 속성은 XML 파일에 대한 경로를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-197">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="9106e-198">일부 Swagger 기능(예: 각 특성에서 입력 매개 변수 또는 HTTP 메서드와 응답 코드의 schemata)은 XML 문서 파일을 사용하지 않고 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-198">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="9106e-199">대부분 기능, 메서드 요약 및 매개 변수/응답 코드 설명의 경우 XML 파일을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-199">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="9106e-200">작업에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-200">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="9106e-201">`Delete` 작업 위에 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-201">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="9106e-202">Swagger UI는 이전 코드 `<summary>` 요소의 내부 텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-202">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="9106e-205">UI는 생성된 JSON 스키마에 의해 구동됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-205">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="9106e-206">[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 요소를 `Create` 작업 메서드 문서에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-206">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="9106e-207">이는 `<summary>` 요소에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-207">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="9106e-208">`<remarks>` 요소 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-208">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="9106e-209">이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-209">Notice the UI enhancements with these additional comments:</span></span>

![추가 주석이 표시된 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="9106e-211">데이터 주석</span><span class="sxs-lookup"><span data-stu-id="9106e-211">Data annotations</span></span>

<span data-ttu-id="9106e-212">[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 네임스페이스에 있는 특성으로 모델을 표시하여 Swagger UI 구성 요소를 구동합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-212">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="9106e-213">`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-213">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="9106e-214">이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-214">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="9106e-215">API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-215">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="9106e-216">이 특성은 컨트롤러 동작이 *application/json* 의 응답 콘텐츠 형식을 지원함을 선언하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-216">Its purpose is to declare that the controller's actions support a response content type of *application/json* :</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="9106e-217">컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-217">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="9106e-219">웹 API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-219">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="9106e-220">응답 형식 설명</span><span class="sxs-lookup"><span data-stu-id="9106e-220">Describe response types</span></span>

<span data-ttu-id="9106e-221">웹 API를 사용하는 개발자는 반환된 내용&mdash; 중에서도 특히 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-221">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="9106e-222">응답 형식 및 오류 코드는 XML 주석 및 데이터 주석에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-222">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="9106e-223">`Create` 작업은 성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-223">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="9106e-224">게시된 요청 본문이 null일 경우 HTTP 400 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-224">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="9106e-225">Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-225">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="9106e-226">다음 예제에서 강조 표시된 줄을 추가하여 해당 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-226">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="9106e-227">이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-227">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9106e-229">ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이팅하는 대신 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-229">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="9106e-230">자세한 내용은 <xref:web-api/advanced/conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9106e-230">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="9106e-231">`[ProducesResponseType]` 데코레이션을 지원하기 위해 [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) 패키지는 응답, 스키마 및 매개 변수 메타데이터를 사용하도록 지원하고 보강하는 확장을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-231">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="9106e-232">UI 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="9106e-232">Customize the UI</span></span>

<span data-ttu-id="9106e-233">기본 UI는 기능적이며 표시 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-233">The default UI is both functional and presentable.</span></span> <span data-ttu-id="9106e-234">그러나 API 설명서 페이지는 브랜드 또는 테마를 나타내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-234">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="9106e-235">Swashbuckle 구성 요소를 브랜딩하려면 리소스를 추가하여 정적 파일을 지원하고 폴더 구조를 빌드하여 해당 파일을 호스트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-235">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="9106e-236">.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-236">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="9106e-237">이전 NuGet 패키지는 .NET Core 2.x를 대상으로 하고 [metapackage](xref:fundamentals/metapackage)를 사용하는 경우 이미 설치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-237">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="9106e-238">정적 파일 미들웨어 사용:</span><span class="sxs-lookup"><span data-stu-id="9106e-238">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="9106e-239">CSS 스타일시트를 추가로 삽입하려면 프로젝트의 *wwwroot* 폴더에 추가하고 미들웨어 옵션에서 상대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9106e-239">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
