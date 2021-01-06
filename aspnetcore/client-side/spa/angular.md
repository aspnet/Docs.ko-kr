---
title: ASP.NET Core에서 Angular 프로젝트 템플릿 사용
author: SteveSandersonMS
description: Angular 및 Angular CLI에 대한 ASP.NET Core SPA(단일 페이지 애플리케이션) 프로젝트 템플릿을 시작하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
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
uid: spa/angular
ms.openlocfilehash: 2fff0d60b71bbbab9347dbe74cad023264247388
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054569"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="4e809-103">ASP.NET Core에서 Angular 프로젝트 템플릿 사용</span><span class="sxs-lookup"><span data-stu-id="4e809-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="4e809-104">업데이트된 Angular 프로젝트 템플릿은 Angular 및 Angular CLI를 사용하여 풍부한 클라이언트 쪽 UI(사용자 인터페이스)를 구현하는 ASP.NET Core 앱에 대한 편리한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="4e809-105">이 템플릿은 API 백 엔드로 사용되는 ASP.NET Core 프로젝트 및 UI로 사용되는 Angular CLI 프로젝트를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="4e809-106">템플릿은 단일 앱 프로젝트에서 두 프로젝트 유형을 모두 호스트하는 편리한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="4e809-107">따라서 앱 프로젝트를 단일 단위로 빌드하고 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="4e809-108">새 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4e809-108">Create a new app</span></span>

<span data-ttu-id="4e809-109">ASP.NET Core 2.1이 설치되어 있는 경우 Angular 프로젝트 템플릿을 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="4e809-110">빈 디렉터리에 `dotnet new angular` 명령을 사용하여 명령 프롬프트에서 새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="4e809-111">예를 들어 다음 명령은 *my-new-app* 디렉터리에 앱을 만들고 해당 디렉터리로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="4e809-112">Visual Studio 또는 .NET Core CLI에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e809-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e809-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="4e809-114">생성된 *.csproj* 파일을 열고 여기에서 정상적으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="4e809-115">빌드 프로세스는 첫 번째 실행에서 npm 종속성을 복원하고 이 작업에는 몇 분 정도 소요될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="4e809-116">후속 빌드는 훨씬 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4e809-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4e809-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4e809-118">`Development` 값을 가진 `ASPNETCORE_Environment` 환경 변수가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="4e809-119">Windows(PowerShell 이외의 프롬프트)에서 `SET ASPNETCORE_Environment=Development`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="4e809-120">Linux 또는 macOS에서 `export ASPNETCORE_Environment=Development`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="4e809-121">[dotnet build](/dotnet/core/tools/dotnet-build)를 실행하여 앱이 제대로 빌드되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="4e809-122">첫 번째 실행에서 빌드 프로세스는 npm 종속성을 복원하고 이 작업에는 몇 분 정도 소요될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="4e809-123">후속 빌드는 훨씬 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="4e809-124">[dotnet run](/dotnet/core/tools/dotnet-run)을 실행하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="4e809-125">다음과 유사한 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="4e809-126">브라우저에서 이 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="4e809-127">앱이 백그라운드에서 Angular CLI 서버의 인스턴스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="4e809-128">다음과 유사한 메시지가 기록됩니다. ‘NG Live 개발 서버는 localhost:&lt;otherport&gt;에서 수신 대기하고 브라우저에서 http://localhost:&lt ;otherport&gt;/ 를 엽니다.’ </span><span class="sxs-lookup"><span data-stu-id="4e809-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="4e809-129">이 메시지 무시&mdash;결합된 ASP.NET Core 및 Angular CLI 앱의 URL이 **아닙니다**.</span><span class="sxs-lookup"><span data-stu-id="4e809-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="4e809-130">프로젝트 템플릿은 ASP.NET Core 앱과 Angular 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="4e809-131">ASP.NET Core 앱은 데이터 액세스, 권한 부여 및 기타 서버 쪽 문제에 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="4e809-132">*ClientApp* 하위 디렉터리에 있는 Angular 앱은 모든 UI 문제에 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="4e809-133">페이지, 이미지, 스타일, 모듈 등을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="4e809-134">*ClientApp* 디렉터리에는 표준 Angular CLI 앱이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="4e809-135">자세한 내용은 공식 [Angular 설명서](https://angular.io)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4e809-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="4e809-136">이 템플릿에서 만들어진 Angular 앱과 Angular CLI 자체에서(`ng new`을 통해) 만들어진 앱 간에는 약간 차이가 있지만 앱의 기능은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="4e809-137">템플릿에서 만들어진 앱에는 [부트스트랩](https://getbootstrap.com/) 기반 레이아웃 및 기본 라우팅 예제가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="4e809-138">ng 명령 실행</span><span class="sxs-lookup"><span data-stu-id="4e809-138">Run ng commands</span></span>

<span data-ttu-id="4e809-139">명령 프롬프트에서 *ClientApp* 하위 디렉터리로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="4e809-140">`ng` 도구가 전역으로 설치된 경우 해당 명령을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="4e809-141">예를 들어 `ng lint`, `ng test` 또는 기타 모든 [Angular CLI 명령](https://angular.io/cli)을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="4e809-142">ASP.NET Core 앱은 앱의 서버 쪽 및 클라이언트 쪽 파트를 둘 다 처리하기 때문에 `ng serve`를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="4e809-143">내부적으로는 개발 시 `ng serve`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="4e809-144">`ng` 도구가 설치되지 않은 경우에는 대신에 `npm run ng`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="4e809-145">예를 들어 `npm run ng lint` 또는 `npm run ng test`를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="4e809-146">npm 패키지 설치</span><span class="sxs-lookup"><span data-stu-id="4e809-146">Install npm packages</span></span>

<span data-ttu-id="4e809-147">타사 npm 패키지를 설치하려면 *ClientApp* 하위 디렉터리에서 명령 프롬프트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="4e809-148">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="4e809-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="4e809-149">게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="4e809-149">Publish and deploy</span></span>

<span data-ttu-id="4e809-150">개발 시에 앱은 개발자 편의를 위해 최적화된 모드로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="4e809-151">예를 들어 디버그할 때 원래 TypeScript 코드를 볼 수 있도록 JavaScript 번들에 소스 맵이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="4e809-152">앱은 디스크에서 TypeScript, HTML 및 CSS 파일 변경 내용을 감시하고 해당 파일의 변경을 확인하면 자동으로 다시 컴파일하고 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="4e809-153">프로덕션 시에는 성능에 최적화된 앱의 버전을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="4e809-154">이는 자동으로 수행하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-154">This is configured to happen automatically.</span></span> <span data-ttu-id="4e809-155">게시할 때 빌드 구성은 클라이언트 쪽 코드의 축소된 AOT(Ahead-Of-Time) 컴파일된 빌드를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="4e809-156">개발 빌드와 달리 프로덕션 빌드에서는 SSR(서버 쪽 사전 렌더링)을 사용하도록 설정하지 않은 경우 서버에 Node.js를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="4e809-157">표준 [ASP.NET Core 호스팅 및 배포 방법](xref:host-and-deploy/index)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="4e809-158">개별적으로 “ng serve” 실행</span><span class="sxs-lookup"><span data-stu-id="4e809-158">Run "ng serve" independently</span></span>

<span data-ttu-id="4e809-159">프로젝트는 ASP.NET Core 앱이 개발 모드에서 시작될 때 백그라운드에서 Angular CLI 서버의 자체 인스턴스를 시작하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="4e809-160">이 방법은 별도의 서버를 수동으로 실행할 필요가 없기 때문에 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="4e809-161">이 기본 설정에는 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="4e809-162">C# 코드를 수정하고 ASP.NET Core 앱을 다시 시작해야 할 때마다 Angular CLI 서버가 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="4e809-163">백업을 시작하려면 10초 정도가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="4e809-164">C# 코드를 자주 편집하고 있고 Angular CLI가 다시 시작될 때까지 기다리지 않으려면 ASP.NET Core 프로세스와는 별도로 Angular CLI 서버를 외부에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="4e809-165">이를 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="4e809-165">To do so:</span></span>

1. <span data-ttu-id="4e809-166">명령 프롬프트에서 *ClientApp* 하위 디렉터리로 전환하고 Angular CLI 개발 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="4e809-167">`ng serve`가 아니라 `npm start`를 사용하여 Angular CLI 개발 서버를 시작하면 *package.json* 의 구성이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="4e809-168">추가 매개 변수를 Angular CLI 서버에 전달하려면 *package.json* 파일의 관련 `scripts` 줄에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="4e809-169">자체 인스턴스 중 하나를 시작하지 않고 외부 Angular CLI 인스턴스를 사용하도록 ASP.NET Core 앱을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="4e809-170">*Startup* 클래스에서 `spa.UseAngularCliServer` 호출을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="4e809-171">ASP.NET Core 앱을 시작할 때 Angular CLI 서버는 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="4e809-172">수동으로 시작한 인스턴스가 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="4e809-173">이를 통해 더 빠르게 시작하고 다시 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="4e809-174">더 이상 Angular CLI가 매번 클라이언트 앱을 다시 빌드할 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="4e809-175">.NET 코드의 데이터를 TypeScript 코드로 전달</span><span class="sxs-lookup"><span data-stu-id="4e809-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="4e809-176">SSR 중에 ASP.NET Core 앱에서 요청별 데이터를 Angular 앱으로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="4e809-177">예를 들어 cookie 정보를 전달하거나 데이터베이스에서 무언가를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="4e809-178">이를 수행하려면 *Startup* 클래스를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="4e809-179">`UseSpaPrerendering`에 대한 콜백에서 다음과 같은 `options.SupplyData` 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="4e809-180">`SupplyData` 콜백을 사용하면 임의, 요청별, JSON serialize 가능 데이터(예: 문자열, 부울 또는 숫자)를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="4e809-181">*main.server.ts* 코드는 이 데이터를 `params.data`로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="4e809-182">예를 들어 앞의 코드 샘플은 부울 값을 `params.data.isHttpsRequest`로 `createServerRenderer` 콜백에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="4e809-183">Angular에서 지원되는 모든 방법으로 앱의 다른 파트에 이 값을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="4e809-184">예를 들어 *main.server.ts* 에서 생성자가 `BASE_URL` 값을 수신하도록 선언된 모든 구성 요소에 이 값을 전달하는 방법을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4e809-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="4e809-185">SSR의 단점</span><span class="sxs-lookup"><span data-stu-id="4e809-185">Drawbacks of SSR</span></span>

<span data-ttu-id="4e809-186">일부 앱에서는 SSR의 이점을 볼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="4e809-187">주된 이점은 체감 성능입니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="4e809-188">느린 네트워크 연결을 통하거나 느린 모바일 디바이스에서 앱에 도달하는 방문자는 JavaScript 번들을 페치 또는 구문 분석하는 동안 시간이 걸리더라도 초기 UI를 빠르게 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="4e809-189">그러나 많은 SPA는 앱이 거의 즉각적으로 나타나는 고성능 컴퓨터에서 고속 사내 네트워크를 통해 주로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="4e809-190">또한, SSR을 사용하는 데는 상당한 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="4e809-191">개발 프로세스가 복잡해지고</span><span class="sxs-lookup"><span data-stu-id="4e809-191">It adds complexity to your development process.</span></span> <span data-ttu-id="4e809-192">코드를 클라이언트 쪽 및 서버 쪽(ASP.NET Core에서 호출되는 Node.js 환경) 등 두 가지 환경에서 실행해야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="4e809-193">다음과 같은 몇 가지 사항을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="4e809-194">SSR을 사용하려면 프로덕션 서버에 Node.js를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="4e809-195">Azure App Services와 같은 일부 배포 시나리오의 경우에는 이 내용이 적용되지만 Azure Service Fabric과 같은 다른 배포 시나리오에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="4e809-196">`BuildServerSideRenderer` 빌드 플래그를 사용하면 *node_modules* 디렉터리가 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="4e809-197">이 폴더에는 20,000개 이상의 파일이 포함되어 있으므로 배포 시간이 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="4e809-198">Node.js 환경에서 코드를 실행하기 위해 `window` 또는 `localStorage`와 같은 브라우저 관련 JavaScript API를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="4e809-199">코드(또는 참조하는 일부 타사 라이브러리)가 이러한 API를 사용하려고 하면 SSR 중에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="4e809-200">예를 들어 많은 위치에서 브라우저 관련 API를 참조하므로 jQuery를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4e809-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="4e809-201">오류를 방지하려면 SSR 또는 브라우저 관련 API를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4e809-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="4e809-202">검사에서 해당 API에 대한 모든 호출을 래핑하여 SSR 중에 호출되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="4e809-203">예를 들어 JavaScript 또는 TypeScript 코드에서 다음과 같은 검사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4e809-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="4e809-204">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4e809-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
