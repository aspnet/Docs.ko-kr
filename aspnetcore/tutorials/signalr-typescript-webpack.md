---
title: TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용
author: ssougnez
description: 이 자습서에서는 클라이언트가 TypeScript로 작성된 ASP.NET Core SignalR 웹앱을 번들링 및 빌드하도록 WebPack을 구성합니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 9d52b72c669a9345cc7c5386876db22af55a97c7
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589753"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="2221a-103">TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용</span><span class="sxs-lookup"><span data-stu-id="2221a-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="2221a-104">작성자: [Sébastien Sougnez](https://twitter.com/ssougnez) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2221a-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2221a-105">[WebPack](https://webpack.js.org/)을 사용하면 개발자가 웹 앱의 클라이언트 쪽 리소스를 번들링 및 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="2221a-106">이 자습서에서는 클라이언트가 [TypeScript](https://www.typescriptlang.org/)로 작성된 ASP.NET Core SignalR 웹앱에서 WebPack을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="2221a-107">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2221a-108">시작 ASP.NET Core SignalR 앱 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="2221a-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="2221a-109">SignalR TypeScript 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="2221a-110">WebPack을 사용하여 빌드 파이프라인 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="2221a-111">SignalR 서버 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="2221a-112">클라이언트 및 서버 간 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="2221a-112">Enable communication between client and server</span></span>

<span data-ttu-id="2221a-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2221a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="2221a-114">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="2221a-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2221a-116">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="2221a-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="2221a-117">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="2221a-118">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="2221a-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="2221a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="2221a-121">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="2221a-122">Visual Studio Code용 C# 버전 1.17.1 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="2221a-123">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="2221a-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="2221a-124">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="2221a-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2221a-126">*PATH* 환경 변수에서 npm을 찾도록 Visual Studio를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="2221a-127">기본적으로 Visual Studio는 설치 디렉터리에 있는 npm의 버전을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="2221a-128">Visual Studio에서 다음 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="2221a-129">Visual Studio를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-129">Launch Visual Studio.</span></span> <span data-ttu-id="2221a-130">시작 창에서 **코드를 사용하지 않고 계속** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="2221a-131">**도구** > **옵션** > **프로젝트 및 솔루션** > **웹 패키지 관리** > **외부 웹 도구** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="2221a-132">목록에서 *$(PATH)* 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="2221a-133">위쪽 화살표를 클릭하여 이 항목을 목록의 두 번째 위치로 이동한 후 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio 구성](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="2221a-135">Visual Studio의 구성이 완료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="2221a-136">**파일** > **새로 만들기** > **프로젝트** 메뉴 옵션에서 **ASP.NET Core 웹 애플리케이션** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="2221a-137">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-137">Select **Next**.</span></span>
1. <span data-ttu-id="2221a-138">프로젝트 이름을 *SignalRWebPack* 으로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="2221a-139">대상 프레임워크 드롭다운에서 *.NET Core* 를 선택하고, 프레임워크 선택기 드롭다운에서 *ASP.NET Core 3.1* 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="2221a-140">**빈** 템플릿을 선택하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="2221a-141">프로젝트에 `Microsoft.TypeScript.MSBuild` 패키지를 추가:</span><span class="sxs-lookup"><span data-stu-id="2221a-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="2221a-142">**솔루션 탐색기**(오른쪽 창)에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="2221a-143">**찾아보기** 탭에서 `Microsoft.TypeScript.MSBuild`를 검색한 다음 오른쪽에 있는 **설치** 를 클릭하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="2221a-144">Visual Studio가 **솔루션 탐색기** 의 **종속성** 노드에 NuGet 패키지를 추가하여 프로젝트의 TypeScript 컴파일을 사용 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2221a-146">**통합 터미널** 에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="2221a-147">`dotnet new` 명령은 *SignalRWebPack* 디렉터리에 빈 ASP.NET Core 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="2221a-148">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *SignalRWebPack* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="2221a-149">**통합 터미널** 에서 다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="2221a-150">위의 명령은 [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) 패키지를 추가하여 프로젝트의 TypeScript 컴파일을 사용 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="2221a-151">WebPack 및 TypeScript 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="2221a-152">다음 단계에서는 TypeScript에서 JavaScript로 변환 및 클라이언트 쪽 리소스의 번들링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="2221a-153">프로젝트 루트에서 다음 명령을 실행하여 *package.json* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="2221a-154">강조 표시된 속성을 *package.json* 파일에 추가하고 파일 변경 내용을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="2221a-155">`private` 속성을 `true`로 설정하면 다음 단계에서 패키지 설치 경고가 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="2221a-156">필요한 npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-156">Install the required npm packages.</span></span> <span data-ttu-id="2221a-157">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="2221a-158">참고할 몇몇 명령 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="2221a-158">Some command details to note:</span></span>

    * <span data-ttu-id="2221a-159">버전 번호는 각 패키지 이름에 대해 `@` 부호 뒤에 옵니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="2221a-160">npm은 해당 특정 패키지 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="2221a-161">`-E` 옵션은 [유의적 버전](https://semver.org/) 범위 연산자를 *package.json* 에 쓰는 npm의 기본 동작을 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="2221a-162">예를 들어 `"webpack": "^4.41.5"` 대신 `"webpack": "4.41.5"`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="2221a-163">이 옵션은 최신 패키지 버전으로 의도하지 않은 업그레이드를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="2221a-164">자세한 내용은 [npm-install](https://docs.npmjs.com/cli/install) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2221a-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="2221a-165">*package.json* 파일의 `scripts` 속성을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="2221a-166">스크립트에 대한 간략한 설명:</span><span class="sxs-lookup"><span data-stu-id="2221a-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="2221a-167">`build`: 개발 모드에서 클라이언트 쪽 리소스를 번들링하고 파일 변경을 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="2221a-168">파일 감시자는 프로젝트 파일이 변경될 때마다 번들이 다시 생성되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="2221a-169">`mode` 옵션은 프로덕션 최적화(예: 트리 셰이킹 및 축소)를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="2221a-170">개발 시에는 `build`만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="2221a-171">`release`: 프로덕션 모드에서 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="2221a-172">`publish`: 프로덕션 모드에서 `release` 스크립트를 실행하여 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="2221a-173">이는 .NET Core CLI의 [publish](/dotnet/core/tools/dotnet-publish) 명령을 호출하여 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="2221a-174">프로젝트 루트에 다음 코드를 포함한 *webpack.config.js* 라는 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="2221a-175">앞의 파일은 WebPack 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="2221a-176">참고할 일부 구성 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="2221a-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="2221a-177">`output` 속성은 *dist* 의 기본값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="2221a-178">번들은 *wwwroot* 디렉터리에 대신 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="2221a-179">`resolve.extensions` 배열은 SignalR 클라이언트 JavaScript를 가져오기 위한 *.js* 를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="2221a-180">프로젝트 루트에 새 *src* 디렉터리를 만들어 프로젝트의 클라이언트 쪽 자산을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="2221a-181">다음 마크업을 포함한 *src/index.html* 을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="2221a-182">이 HTML은 홈페이지의 전형적인 마크업을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="2221a-183">새 *src/css* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="2221a-184">그 목적은 프로젝트의 *.css* 파일을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="2221a-185">다음 CSS를 포함한 *src/css/main.css* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="2221a-186">이 *main.css* 파일은 앱의 스타일을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="2221a-187">다음 JSON을 포함한 *src/tsconfig.json* 을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="2221a-188">이 코드는 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 호환 JavaScript를 생성하도록 TypeScript 컴파일러를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="2221a-189">다음 코드를 포함한 *src/index.ts* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="2221a-190">이 TypeScript는 DOM 요소 참조를 조회하여 두 가지 이벤트 핸들러를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="2221a-191">`keyup`: 이 이벤트는 사용자가 `tbMessage`텍스트 상자에 입력할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="2221a-192">`send` 함수는 사용자가 **Enter** 키를 누를 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="2221a-193">`click`: 이 이벤트는 사용자가 **보내기** 단추를 클릭할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="2221a-194">`send` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="2221a-195">앱 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-195">Configure the app</span></span>

1. <span data-ttu-id="2221a-196">`Startup.Configure`에서 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 및 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)에 대한 호출을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="2221a-197">위의 코드를 사용하면 서버에서 *index.html* 파일을 찾고 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="2221a-198">이 파일은 사용자가 웹앱의 전체 URL을 입력하든 아니면 루트 URL을 입력하든 관계없이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="2221a-199">`Startup.Configure`의 끝에서 */hub* 경로를 `ChatHub` 허브에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="2221a-200">*Hello World!* 를 표시하는 코드를</span><span class="sxs-lookup"><span data-stu-id="2221a-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="2221a-201">다음 줄로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="2221a-202">`Startup.ConfigureServices`에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="2221a-203">프로젝트 루트 *SignalRWebPack/* 에 *Hubs* 라는 새 디렉터리를 만들어 SignalR 허브를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="2221a-204">다음 내용을 포함한 *Hubs/ChatHub.cs* 허브를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="2221a-205">`ChatHub` 참조를 확인하기 위해 *Startup.cs* 파일의 맨 위에 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="2221a-206">클라이언트 및 서버 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="2221a-206">Enable client and server communication</span></span>

<span data-ttu-id="2221a-207">현재 앱에는 메시지를 전송하기 위한 기본 양식이 표시되어 있지만 아직 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="2221a-208">서버는 특정 경로를 수신 대기하지만 보낸 메시지를 사용하여 아무 작업도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="2221a-209">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="2221a-210">이전 명령은 다음을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-210">The preceding command installs:</span></span>

     * <span data-ttu-id="2221a-211">클라이언트에서 서버로 메시지를 전송할 수 있게 해주는 [SignalR TypeScript 클라이언트](https://www.npmjs.com/package/@microsoft/signalr).</span><span class="sxs-lookup"><span data-stu-id="2221a-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="2221a-212">Node.js 형식에 대한 컴파일 시간 검사를 가능하게 하는 Node.js에 대한 TypeScript 형식 정의.</span><span class="sxs-lookup"><span data-stu-id="2221a-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="2221a-213">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="2221a-214">앞의 코드는 서버에서 오는 메시지의 수신을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="2221a-215">`HubConnectionBuilder` 클래스는 서버 연결을 구성하기 위한 새 빌더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="2221a-216">`withUrl` 함수는 허브 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="2221a-217">SignalR은 클라이언트 및 서버 간 메시지 교환을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="2221a-218">각 메시지는 특정 이름을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-218">Each message has a specific name.</span></span> <span data-ttu-id="2221a-219">예를 들어 `messageReceived`라는 이름을 가진 메시지가 메시지 영역에 새 메시지를 표시하는 로직을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="2221a-220">특정 메시지 수신 대기는 `on` 함수를 통해 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="2221a-221">임의 개수의 메시지 이름을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="2221a-222">또한 수신 메시지의 작성자 이름 및 내용 등을 메시지에 파라미터로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="2221a-223">클라이언트가 메시지를 수신한 후 `innerHTML` 특성의 작성자 이름 및 메시지 내용을 사용하여 새 `div` 요소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="2221a-224">이 요소가 주 `div` 요소에 추가되어 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="2221a-225">이제 클라이언트가 메시지를 수신할 수 있으므로, 메시지를 보내도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="2221a-226">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="2221a-227">WebSockets 연결을 통해 메시지를 보내면 `send` 메서드를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="2221a-228">이 메서드의 첫 번째 매개 변수는 메시지의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="2221a-229">메시지의 데이터는 다른 매개 변수를 통해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="2221a-230">이 예제에서는 `newMessage`로 식별되는 메시지가 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="2221a-231">메시지는 사용자 이름 및 텍스트 상자에 입력된 사용자 입력으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="2221a-232">전송이 완료되면 텍스트 상자의 값이 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="2221a-233">`ChatHub` 클래스에 `NewMessage` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="2221a-234">앞의 코드는 서버가 메시지를 수신한 후 수신된 메시지를 모든 연결된 사용자에게 브로드캐스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="2221a-235">모든 메시지를 수신하기 위해 일반 `on` 메서드를 포함할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="2221a-236">메시지 이름 뒤에 명명한 메서드로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="2221a-237">이 예에서 TypeScript 클라이언트는 `newMessage`로 식별된 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="2221a-238">C# `NewMessage` 메서드에는 클라이언트가 보낸 데이터가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="2221a-239">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)에 대한 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="2221a-240">수신된 메시지를 허브에 연결된 모든 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2221a-241">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-241">Test the app</span></span>

<span data-ttu-id="2221a-242">다음 단계를 사용하여 앱이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2221a-244">WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="2221a-245">**패키지 관리자 콘솔** 창을 사용하여 프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="2221a-246">프로젝트 루트가 아닌 경우 명령을 입력하기 전에 `cd SignalRWebPack`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2221a-247">**디버그** > **디버그하지 않고 시작** 을 선택하여 디버거를 연결하지 않고 브라우저에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="2221a-248">`http://localhost:<port_number>`에서 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="2221a-249">컴파일 오류가 발생하면 솔루션을 닫았다가 다시 열어 보세요.</span><span class="sxs-lookup"><span data-stu-id="2221a-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="2221a-250">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="2221a-251">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2221a-252">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2221a-253">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2221a-255">프로젝트 루트에서 다음 명령을 실행하여 WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2221a-256">프로젝트 루트에서 다음 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="2221a-257">웹 서버가 앱을 시작하고 로컬 호스트에서 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="2221a-258">`http://localhost:<port_number>`에 대해 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="2221a-259">그러면 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="2221a-260">주소 표시줄에서 URL을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="2221a-261">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="2221a-262">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2221a-263">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2221a-264">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![두 브라우저 창 모두에 표시된 메시지](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="2221a-266">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="2221a-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2221a-268">**ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="2221a-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="2221a-269">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="2221a-270">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="2221a-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="2221a-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="2221a-273">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="2221a-274">Visual Studio Code용 C# 버전 1.17.1 이상</span><span class="sxs-lookup"><span data-stu-id="2221a-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="2221a-275">[Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)</span><span class="sxs-lookup"><span data-stu-id="2221a-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="2221a-276">ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="2221a-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2221a-278">*PATH* 환경 변수에서 npm을 찾도록 Visual Studio를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="2221a-279">기본적으로 Visual Studio는 설치 디렉터리에 있는 npm의 버전을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="2221a-280">Visual Studio에서 다음 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="2221a-281">**도구** > **옵션** > **프로젝트 및 솔루션** > **웹 패키지 관리** > **외부 웹 도구** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="2221a-282">목록에서 *$(PATH)* 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="2221a-283">위쪽 화살표를 클릭하여 이 항목을 목록의 두 번째 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 구성](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="2221a-285">Visual Studio 구성이 완료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="2221a-286">이제 프로젝트를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-286">It's time to create the project.</span></span>

1. <span data-ttu-id="2221a-287">**파일** > **새로 만들기** > **프로젝트** 메뉴 옵션에서 **ASP.NET Core 웹 애플리케이션** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="2221a-288">프로젝트 이름을 *SignalRWebPack* 으로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="2221a-289">대상 프레임워크 드롭다운에서 *.NET Core* 를 선택하고, 프레임워크 선택기 드롭다운에서 *ASP.NET Core 2.2* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="2221a-290">**빈** 템플릿을 선택하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2221a-292">**통합 터미널** 에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="2221a-293">.NET Core를 대상으로 하는 빈 ASP.NET Core 웹앱이 *SignalRWebPack* 디렉터리에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="2221a-294">WebPack 및 TypeScript 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="2221a-295">다음 단계에서는 TypeScript에서 JavaScript로 변환 및 클라이언트 쪽 리소스의 번들링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="2221a-296">프로젝트 루트에서 다음 명령을 실행하여 *package.json* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="2221a-297">강조 표시된 속성을 *package.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="2221a-298">`private` 속성을 `true`로 설정하면 다음 단계에서 패키지 설치 경고가 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="2221a-299">필요한 npm 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-299">Install the required npm packages.</span></span> <span data-ttu-id="2221a-300">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="2221a-301">참고할 몇몇 명령 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="2221a-301">Some command details to note:</span></span>

    * <span data-ttu-id="2221a-302">버전 번호는 각 패키지 이름에 대해 `@` 부호 뒤에 옵니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="2221a-303">npm은 해당 특정 패키지 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="2221a-304">`-E` 옵션은 [유의적 버전](https://semver.org/) 범위 연산자를 *package.json* 에 쓰는 npm의 기본 동작을 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="2221a-305">예를 들어 `"webpack": "^4.29.3"` 대신 `"webpack": "4.29.3"`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="2221a-306">이 옵션은 최신 패키지 버전으로 의도하지 않은 업그레이드를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="2221a-307">자세한 내용은 [npm-install](https://docs.npmjs.com/cli/install) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2221a-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="2221a-308">*package.json* 파일의 `scripts` 속성을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="2221a-309">스크립트에 대한 간략한 설명:</span><span class="sxs-lookup"><span data-stu-id="2221a-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="2221a-310">`build`: 개발 모드에서 클라이언트 쪽 리소스를 번들링하고 파일 변경을 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="2221a-311">파일 감시자는 프로젝트 파일이 변경될 때마다 번들이 다시 생성되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="2221a-312">`mode` 옵션은 프로덕션 최적화(예: 트리 셰이킹 및 축소)를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="2221a-313">개발 시에는 `build`만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="2221a-314">`release`: 프로덕션 모드에서 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="2221a-315">`publish`: 프로덕션 모드에서 `release` 스크립트를 실행하여 클라이언트 쪽 리소스를 번들링합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="2221a-316">이는 .NET Core CLI의 [publish](/dotnet/core/tools/dotnet-publish) 명령을 호출하여 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="2221a-317">프로젝트 루트에 다음 내용을 포함한 *webpack.config.js* 라는 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="2221a-318">앞의 파일은 WebPack 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="2221a-319">참고할 일부 구성 세부 정보:</span><span class="sxs-lookup"><span data-stu-id="2221a-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="2221a-320">`output` 속성은 *dist* 의 기본값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="2221a-321">번들은 *wwwroot* 디렉터리에 대신 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="2221a-322">`resolve.extensions` 배열은 SignalR 클라이언트 JavaScript를 가져오기 위한 *.js* 를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="2221a-323">프로젝트 루트에 새 *src* 디렉터리를 만들어 프로젝트의 클라이언트 쪽 자산을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="2221a-324">다음 마크업을 포함한 *src/index.html* 을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="2221a-325">이 HTML은 홈페이지의 전형적인 마크업을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="2221a-326">새 *src/css* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="2221a-327">그 목적은 프로젝트의 *.css* 파일을 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="2221a-328">다음 마크업을 포함한 *src/css/main.css* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="2221a-329">이 *main.css* 파일은 앱의 스타일을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="2221a-330">다음 JSON을 포함한 *src/tsconfig.json* 을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="2221a-331">이 코드는 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 호환 JavaScript를 생성하도록 TypeScript 컴파일러를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="2221a-332">다음 코드를 포함한 *src/index.ts* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="2221a-333">이 TypeScript는 DOM 요소 참조를 조회하여 두 가지 이벤트 핸들러를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="2221a-334">`keyup`: 이 이벤트는 사용자가 `tbMessage` 텍스트 상자에 입력할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="2221a-335">`send` 함수는 사용자가 **Enter** 키를 누를 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="2221a-336">`click`: 이 이벤트는 사용자가 **보내기** 단추를 클릭할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="2221a-337">`send` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="2221a-338">ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="2221a-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="2221a-339">`Startup.Configure` 메서드에 제공된 코드는 *Hello World!* 를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="2221a-340">`app.Run` 메서드를 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 및 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)에 대한 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="2221a-341">이 코드는 사용자가 전체 URL을 입력하는지 아니면 웹앱의 루트 URL만 입력하는지 관계없이 서버가 *index.html* 파일을 찾아서 제공할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="2221a-342">`Startup.ConfigureServices`에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2221a-343">그러면 SignalR 서비스가 프로젝트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="2221a-344">*/hub* 경로를 `ChatHub` 허브에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="2221a-345">`Startup.Configure` 끝에 다음 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="2221a-346">프로젝트 루트에 *Hubs* 라는 새 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="2221a-347">그 목적은 다음 단계에서 생성되는 SignalR 허브를 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="2221a-348">다음 내용을 포함한 *Hubs/ChatHub.cs* 허브를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="2221a-349">`ChatHub` 참조를 확인하기 위해 *Startup.cs* 파일의 맨 위에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="2221a-350">클라이언트 및 서버 통신 활성화</span><span class="sxs-lookup"><span data-stu-id="2221a-350">Enable client and server communication</span></span>

<span data-ttu-id="2221a-351">앱은 현재 메시지를 보낼 간단한 양식을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="2221a-352">그런 시도를 할 때 아무 일도 일어나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="2221a-353">서버는 특정 경로를 수신 대기하지만 보낸 메시지를 사용하여 아무 작업도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="2221a-354">프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="2221a-355">이 명령은 클라이언트에서 서버로 메시지를 전송할 수 있게 해주는 [SignalR TypeScript 클라이언트](https://www.npmjs.com/package/@microsoft/signalr)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="2221a-356">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="2221a-357">앞의 코드는 서버에서 오는 메시지의 수신을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="2221a-358">`HubConnectionBuilder` 클래스는 서버 연결을 구성하기 위한 새 빌더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="2221a-359">`withUrl` 함수는 허브 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="2221a-360">SignalR은 클라이언트 및 서버 간 메시지 교환을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="2221a-361">각 메시지는 특정 이름을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-361">Each message has a specific name.</span></span> <span data-ttu-id="2221a-362">예를 들어 `messageReceived`라는 이름을 가진 메시지가 메시지 영역에 새 메시지를 표시하는 로직을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="2221a-363">특정 메시지 수신 대기는 `on` 함수를 통해 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="2221a-364">임의 개수의 메시지 이름을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-364">You can listen to any number of message names.</span></span> <span data-ttu-id="2221a-365">또한 수신 메시지의 작성자 이름 및 내용 등을 메시지에 파라미터로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="2221a-366">클라이언트가 메시지를 수신한 후 `innerHTML` 특성의 작성자 이름 및 메시지 내용을 사용하여 새 `div` 요소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="2221a-367">메시지를 표시하는 주 `div` 요소에 새 메시지가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="2221a-368">이제 클라이언트가 메시지를 수신할 수 있으므로, 메시지를 보내도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="2221a-369">강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="2221a-370">WebSockets 연결을 통해 메시지를 보내면 `send` 메서드를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="2221a-371">이 메서드의 첫 번째 매개 변수는 메시지의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="2221a-372">메시지의 데이터는 다른 매개 변수를 통해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="2221a-373">이 예제에서는 `newMessage`로 식별되는 메시지가 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="2221a-374">메시지는 사용자 이름 및 텍스트 상자에 입력된 사용자 입력으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="2221a-375">전송이 완료되면 텍스트 상자의 값이 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="2221a-376">`ChatHub` 클래스에 `NewMessage` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="2221a-377">앞의 코드는 서버가 메시지를 수신한 후 수신된 메시지를 모든 연결된 사용자에게 브로드캐스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="2221a-378">모든 메시지를 수신하기 위해 일반 `on` 메서드를 포함할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="2221a-379">메시지 이름 뒤에 명명한 메서드로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="2221a-380">이 예에서 TypeScript 클라이언트는 `newMessage`로 식별된 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="2221a-381">C# `NewMessage` 메서드에는 클라이언트가 보낸 데이터가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="2221a-382">[Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)에 대한 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="2221a-383">수신된 메시지를 허브에 연결된 모든 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2221a-384">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-384">Test the app</span></span>

<span data-ttu-id="2221a-385">다음 단계를 사용하여 앱이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2221a-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2221a-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2221a-387">WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="2221a-388">**패키지 관리자 콘솔** 창을 사용하여 프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="2221a-389">프로젝트 루트가 아닌 경우 명령을 입력하기 전에 `cd SignalRWebPack`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2221a-390">**디버그** > **디버그하지 않고 시작** 을 선택하여 디버거를 연결하지 않고 브라우저에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="2221a-391">*wwwroot/index.html* 파일은 `http://localhost:<port_number>`에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="2221a-392">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="2221a-393">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2221a-394">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2221a-395">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2221a-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2221a-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2221a-397">프로젝트 루트에서 다음 명령을 실행하여 WebPack을 *release* 모드로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="2221a-398">프로젝트 루트에서 다음 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="2221a-399">웹 서버가 앱을 시작하고 로컬 호스트에서 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="2221a-400">`http://localhost:<port_number>`에 대해 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="2221a-401">그러면 *wwwroot/index.html* 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="2221a-402">주소 표시줄에서 URL을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="2221a-403">다른 브라우저 인스턴스(임의 브라우저)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="2221a-404">URL을 주소 표시줄에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2221a-405">브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="2221a-406">고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2221a-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![두 브라우저 창 모두에 표시된 메시지](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2221a-408">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2221a-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
