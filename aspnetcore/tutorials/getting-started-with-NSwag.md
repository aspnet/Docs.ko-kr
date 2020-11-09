---
title: NSwag 및 ASP.NET Core 시작
author: zuckerthoben
description: NSwag를 사용하여 ASP.NET Core Web API의 설명서 및 도움말 페이지를 생성하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 78d58d4d544c33862cf502ce63e83560e8009c65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060575"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="1dd9b-103">NSwag 및 ASP.NET Core 시작</span><span class="sxs-lookup"><span data-stu-id="1dd9b-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="1dd9b-104">작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) 및 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="1dd9b-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1dd9b-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1dd9b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="1dd9b-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1dd9b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="1dd9b-107">NSwag는 다음 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="1dd9b-108">Swagger UI 및 Swagger 생성기를 사용하는 기능</span><span class="sxs-lookup"><span data-stu-id="1dd9b-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="1dd9b-109">유연한 코드 생성 기능</span><span class="sxs-lookup"><span data-stu-id="1dd9b-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="1dd9b-110">NSwag를 사용하면 기존 API가 필요하지 않으므로 Swagger를 통합하고 클라이언트 구현을 생성하는 타사 API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="1dd9b-111">NSwag를 사용하면 개발 주기를 단축하고 API 변경에 쉽게 대응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="1dd9b-112">NSwag 미들웨어 등록</span><span class="sxs-lookup"><span data-stu-id="1dd9b-112">Register the NSwag middleware</span></span>

<span data-ttu-id="1dd9b-113">다음 작업을 수행하려면 NSwag 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="1dd9b-114">구현된 Web API에 대한 Swagger 사양을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="1dd9b-115">Web API를 찾아보고 테스트하는 Swagger UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="1dd9b-116">[NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core 미들웨어를 사용하려면 [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="1dd9b-117">이 패키지에는 Swagger 사양, Swagger UI(v2 및 v3) 및 [ReDoc UI](https://github.com/Rebilly/ReDoc)를 생성하고 제공하는 미들웨어가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="1dd9b-118">다음 방법 중 하나를 사용하여 NSwag NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1dd9b-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dd9b-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1dd9b-120">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="1dd9b-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="1dd9b-121">**보기** > **다른 창** > **패키지 관리자 콘솔** 로 이동</span><span class="sxs-lookup"><span data-stu-id="1dd9b-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="1dd9b-122">*TodoApi.csproj* 파일이 위치한 디렉터리로 이동</span><span class="sxs-lookup"><span data-stu-id="1dd9b-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="1dd9b-123">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="1dd9b-124">**NuGet 패키지 관리** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="1dd9b-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="1dd9b-125">**솔루션 탐색기** > **NuGet 패키지 관리** 에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="1dd9b-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="1dd9b-126">**패키지 소스** 를 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="1dd9b-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="1dd9b-127">검색 상자에 “NSwag.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="1dd9b-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="1dd9b-128">**찾아보기** 탭에서 "NSwag.AspNetCore" 패키지를 선택하고 **설치** 클릭</span><span class="sxs-lookup"><span data-stu-id="1dd9b-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1dd9b-129">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dd9b-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1dd9b-130">**Solution Pad** 에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택</span><span class="sxs-lookup"><span data-stu-id="1dd9b-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="1dd9b-131">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="1dd9b-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="1dd9b-132">검색 상자에 “NSwag.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="1dd9b-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="1dd9b-133">결과 창에서 "NSwag.AspNetCore" 패키지를 선택하고 **패키지 추가** 클릭</span><span class="sxs-lookup"><span data-stu-id="1dd9b-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1dd9b-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1dd9b-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1dd9b-135">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="1dd9b-136">Swagger 미들웨어 추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="1dd9b-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="1dd9b-137">다음 단계를 수행하여 ASP.NET Core 앱에서 Swagger를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="1dd9b-138">`Startup.ConfigureServices` 메서드에서 필수 Swagger 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="1dd9b-139">`Startup.Configure` 메서드에서 생성된 Swagger 사양 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="1dd9b-140">앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-140">Launch the app.</span></span> <span data-ttu-id="1dd9b-141">다음으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-141">Navigate to:</span></span>
  * <span data-ttu-id="1dd9b-142">`http://localhost:<port>/swagger` - Swagger UI를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="1dd9b-143">`http://localhost:<port>/swagger/v1/swagger.json` - Swagger 사양을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="1dd9b-144">코드 생성</span><span class="sxs-lookup"><span data-stu-id="1dd9b-144">Code generation</span></span>

<span data-ttu-id="1dd9b-145">다음 옵션 중 하나를 선택하여 NSwag의 코드 생성 기능을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="1dd9b-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): C# 또는 TypeScript로 API 클라이언트 코드를 생성하기 위한 Windows 데스크톱 앱.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): A Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="1dd9b-147">프로젝트 내에서 코드 생성을 위한 [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) 또는 [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="1dd9b-148">[명령줄](https://github.com/RicoSuter/NSwag/wiki/CommandLine)의 NSwag.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="1dd9b-149">[NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="1dd9b-150">[Unchase OpenAPI(Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): C# 또는 TypeScript로 API 클라이언트 코드를 생성하기 위한 Visual Studio Connected Service.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): A Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="1dd9b-151">또한 NSwag를 사용하여 OpenAPI 서비스용 C# 컨트롤러를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="1dd9b-152">NSwagStudio로 코드 생성</span><span class="sxs-lookup"><span data-stu-id="1dd9b-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="1dd9b-153">[NSwagStudio GitHub 리포지토리](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)의 지침에 따라 NSwagStudio를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="1dd9b-154">NSwag 릴리스 페이지에서 설치 및 관리자 권한 없이 시작할 수 있는 xcopy 버전을 다운로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="1dd9b-155">NSwagStudio를 시작하고 **Swagger 사양 URL** 텍스트 상자에 *swagger.json* 파일 URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="1dd9b-156">예를 들어 *http://localhost:44354/swagger/v1/swagger.json* 와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="1dd9b-157">**로컬 복사본 만들기** 단추를 클릭하여 Swagger 사양의 JSON 표시를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Swagger 사양의 로컬 복사본 만들기](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="1dd9b-159">**출력** 영역에서 **CSharp 클라이언트** 확인란을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="1dd9b-160">프로젝트에 따라 **TypeScript 클라이언트** 또는 **CSharp 웹 API 컨트롤러** 를 선택할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="1dd9b-161">**CSharp 웹 API 컨트롤러** 를 선택하면 서비스 사양에서 역방향 생성으로 사용되는 서비스를 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-161">If you select **CSharp Web API Controller** , a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="1dd9b-162">**출력 생성** 을 클릭하여 *TodoApi.NSwag* 프로젝트의 완전한 C# 클라이언트 구현을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="1dd9b-163">생성된 클라이언트 코드를 보려면 **CSharp 클라이언트** 탭을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="1dd9b-164">C# 클라이언트 코드는 **설정** 탭의 선택에 따라 생성됩니다. 기본 네임 스페이스 이름 바꾸기 및 동기 메서드 생성 등의 작업을 수행하도록 설정을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="1dd9b-165">API를 사용할 클라이언트 프로젝트의 파일에 생성된 C# 코드를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="1dd9b-166">Web API를 사용하기 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="1dd9b-167">API 문서 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="1dd9b-167">Customize API documentation</span></span>

<span data-ttu-id="1dd9b-168">Swagger는 Web API를 보다 쉽게 사용하도록 개체 모델을 문서화하는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="1dd9b-169">API 정보 및 설명</span><span class="sxs-lookup"><span data-stu-id="1dd9b-169">API info and description</span></span>

<span data-ttu-id="1dd9b-170">`Startup.ConfigureServices` 메서드에서 `AddSwaggerDocument` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="1dd9b-171">Swagger UI는 버전의 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-171">The Swagger UI displays the version's information:</span></span>

![버전 정보를 포함한 Swagger UI](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="1dd9b-173">XML 주석</span><span class="sxs-lookup"><span data-stu-id="1dd9b-173">XML comments</span></span>

<span data-ttu-id="1dd9b-174">XML 주석을 사용하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1dd9b-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dd9b-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1dd9b-176">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="1dd9b-177">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1dd9b-178">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 선택</span><span class="sxs-lookup"><span data-stu-id="1dd9b-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="1dd9b-179">**빌드** 탭의 **출력** 섹션에서 **XML 문서 파일** 상자 선택</span><span class="sxs-lookup"><span data-stu-id="1dd9b-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1dd9b-180">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dd9b-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1dd9b-181">*Solution Pad* 에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-181">From the *Solution Pad* , press **control** and click the project name.</span></span> <span data-ttu-id="1dd9b-182">**도구** > **파일 편집** 으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="1dd9b-183">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1dd9b-184">**프로젝트 옵션** 대화 상자 > **빌드** > **컴파일러** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="1dd9b-185">**일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택</span><span class="sxs-lookup"><span data-stu-id="1dd9b-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="1dd9b-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1dd9b-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1dd9b-187">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="1dd9b-188">데이터 주석</span><span class="sxs-lookup"><span data-stu-id="1dd9b-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="1dd9b-189">NSwag는 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)을 사용하고 웹 API 작업의 권장 반환 형식은 [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult)이므로 작업이 수행 중인 작업과 반환 결과를 유추할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="1dd9b-190">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="1dd9b-191">이전 작업은 `IActionResult`를 반환하지만 작업 내에서는 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) 또는 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="1dd9b-192">데이터 주석을 사용하여 이 작업이 반환하는 것으로 알려진 HTTP 상태 코드를 클라이언트에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="1dd9b-193">다음 특성을 사용하여 작업을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1dd9b-194">NSwag는 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)을 사용하고 웹 API 작업의 권장 반환 형식은 [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)이므로 `T`로 정의된 반환 형식만 유추할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="1dd9b-195">다른 가능한 반환 형식은 자동으로 유추할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="1dd9b-196">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="1dd9b-197">이전 작업이 `ActionResult<T>`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="1dd9b-198">작업 내에서 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="1dd9b-199">컨트롤러에는 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성이 있으므로 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) 응답도 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="1dd9b-200">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="1dd9b-201">데이터 주석을 사용하여 이 작업이 반환하는 것으로 알려진 HTTP 상태 코드를 클라이언트에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="1dd9b-202">다음 특성을 사용하여 작업을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="1dd9b-203">ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이트하는 대신 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="1dd9b-204">자세한 내용은 <xref:web-api/advanced/conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="1dd9b-205">이제 Swagger 생성기는 이 작업을 정확하게 설명할 수 있으며, 생성된 클라이언트가 엔드포인트를 호출할 때 수신한 내용을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="1dd9b-206">모든 작업을 이러한 특성으로 표시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="1dd9b-207">API 작업에서 반환해야 하는 HTTP 응답에 대한 지침은 [RFC 7231 사양](https://tools.ietf.org/html/rfc7231#section-4.3)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1dd9b-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
