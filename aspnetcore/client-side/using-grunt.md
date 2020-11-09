---
title: ASP.NET Core에서 Grunt 사용
author: rick-anderson
description: ASP.NET Core에서 Grunt 사용
ms.author: riande
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054556"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="5712a-103">ASP.NET Core에서 Grunt 사용</span><span class="sxs-lookup"><span data-stu-id="5712a-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="5712a-104">Grunt는 스크립트 축소, TypeScript 컴파일, 코드 품질 “Lint” 도구, CSS 전처리기 및 클라이언트 개발을 지원하기 위해 수행해야 하는 거의 모든 반복 작업을 자동화하는 JavaScript 작업 실행기입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="5712a-105">Grunt는 Visual Studio에서 완전히 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="5712a-106">이 예제에서는 빈 ASP.NET Core 프로젝트를 시작점으로 사용하여 클라이언트 빌드 프로세스를 처음부터 자동화하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="5712a-107">완성된 예제는 대상 배포 디렉터리 정리, JavaScript 파일 결합, 코드 품질 검사, JavaScript 파일 콘텐츠 압축 및 웹 애플리케이션 루트에 배포 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="5712a-108">다음 패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-108">We will use the following packages:</span></span>

* <span data-ttu-id="5712a-109">**grunt** : Grunt 작업 실행기 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-109">**grunt** : The Grunt task runner package.</span></span>

* <span data-ttu-id="5712a-110">**grunt-contrib-clean** : 파일 또는 디렉터리를 제거하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-110">**grunt-contrib-clean** : A plugin that removes files or directories.</span></span>

* <span data-ttu-id="5712a-111">**grunt-contrib-jshint** : JavaScript 코드 품질을 검토하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-111">**grunt-contrib-jshint** : A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="5712a-112">**grunt-contrib-concat** : 파일을 단일 파일로 조인하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-112">**grunt-contrib-concat** : A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="5712a-113">**grunt-contrib-uglify** : 크기를 줄이기 위해 JavaScript를 축소하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-113">**grunt-contrib-uglify** : A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="5712a-114">**grunt-contrib-watch** : 파일 작업을 감시하는 플러그 인입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-114">**grunt-contrib-watch** : A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="5712a-115">애플리케이션 준비</span><span class="sxs-lookup"><span data-stu-id="5712a-115">Preparing the application</span></span>

<span data-ttu-id="5712a-116">먼저, 비어 있는 새로운 웹 애플리케이션을 설정하고 TypeScript 예제 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="5712a-117">TypeScript 파일은 기본 Visual Studio 설정을 사용하여 JavaScript로 자동 컴파일되며, Grunt를 통해 처리할 원시 자료입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="5712a-118">Visual Studio에서 새 `ASP.NET Web Application`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="5712a-119">**새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET Core **비어 있음** 템플릿을 선택하고 확인 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="5712a-120">솔루션 탐색기에서 프로젝트 구조를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="5712a-121">`\src` 폴더는 비어 있는 `wwwroot` 및 `Dependencies` 노드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![비어 있는 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="5712a-123">이름이 `TypeScript`인 새 폴더를 프로젝트 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="5712a-124">파일을 추가하기 전에 Visual Studio에서 TypeScript 파일에 대한 ‘저장 시 컴파일’ 옵션이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="5712a-125">**도구** > **옵션** > **텍스트 편집기** > **Typescript** > **프로젝트** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project** :</span></span>

    ![TypeScript 파일의 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="5712a-127">`TypeScript` 디렉터리를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 > 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5712a-128">**JavaScript 파일** 항목을 선택하고 파일 이름을 *Tastes.ts* 로 지정합니다(\*.ts 확장명 확인).</span><span class="sxs-lookup"><span data-stu-id="5712a-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="5712a-129">아래 TypeScript 코드 줄을 파일에 복사합니다. 저장하면 새 *Tastes.js* 파일이 JavaScript 소스와 함께 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="5712a-130">**TypeScript** 디렉터리에 두 번째 파일을 추가하고 이름을 `Food.ts`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="5712a-131">아래 코드를 파일에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="5712a-132">NPM 구성</span><span class="sxs-lookup"><span data-stu-id="5712a-132">Configuring NPM</span></span>

<span data-ttu-id="5712a-133">grunt 및 grunt-tasks를 다운로드하도록 NPM을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="5712a-134">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 > 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5712a-135">**NPM 구성 파일** 항목을 선택하고 기본 이름인 *package.json* 을 그대로 둔 다음, **추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-135">Select the **NPM configuration file** item, leave the default name, *package.json* , and click the **Add** button.</span></span>

2. <span data-ttu-id="5712a-136">*package.json* 파일에서 `devDependencies` 개체 중괄호 안에 “grunt”를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="5712a-137">Intellisense 목록에서 `grunt`를 선택하고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="5712a-138">Visual Studio에서 grunt 패키지 이름에 따옴표를 붙이고 콜론을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="5712a-139">콜론 오른쪽에서 Intellisense 목록 맨 위에 있는 안정적인 최신 패키지 버전을 선택합니다(Intellisense가 표시되지 않는 경우 `Ctrl-Space`를 누름).</span><span class="sxs-lookup"><span data-stu-id="5712a-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="5712a-141">NPM은 [유의적 버전](https://semver.org/)을 사용하여 종속성을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="5712a-142">SemVer이라는 유의적 버전은 번호 매기기 체계 \<major>.\<minor>.\<patch>를 사용하여 패키지를 확인합니다. Intellisense는 몇 가지 일반적인 선택 항목만 표시하여 유의적 버전을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="5712a-143">Intellisense 목록의 맨 위 항목(위 예제에서는 0.4.5)은 안정적인 최신 패키지 버전으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="5712a-144">캐럿(^) 기호는 가장 최근의 주 버전과 일치하고, 물결표(~)는 가장 최근의 부 버전과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="5712a-145">SemVer에서 제공하는 전체 표현의 가이드로 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5712a-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="5712a-146">종속성을 더 추가하여 아래 예제와 같이 *clean* , *jshint* , *concat* , *uglify* , *watch* 용 grunt-contrib-\* 패키지를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-146">Add more dependencies to load grunt-contrib-\* packages for *clean* , *jshint* , *concat* , *uglify* , and *watch* as shown in the example below.</span></span> <span data-ttu-id="5712a-147">버전은 예제와 일치하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="5712a-148">*package.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-148">Save the *package.json* file.</span></span>

<span data-ttu-id="5712a-149">각 패키지에 필요한 모든 파일과 함께 각 `devDependencies` 항목의 패키지가 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="5712a-150">**솔루션 탐색기** 에서 **모든 파일 표시** 단추를 사용하도록 설정하여 *node_modules* 디렉터리에서 패키지 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="5712a-152">필요한 경우, `Dependencies\NPM`을 마우스 오른쪽 단추로 클릭하고 **패키지 복원** 메뉴 옵션을 선택하여 **솔루션 탐색기** 에서 종속성을 수동으로 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="5712a-154">Grunt 구성</span><span class="sxs-lookup"><span data-stu-id="5712a-154">Configuring Grunt</span></span>

<span data-ttu-id="5712a-155">Grunt는 수동으로 실행하거나 Visual Studio의 이벤트를 기준으로 자동 실행되도록 구성할 수 있는 작업을 정의, 로드, 등록하는 *Gruntfile.js* 라는 매니페스트를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="5712a-156">프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="5712a-157">**JavaScript 파일** 항목 템플릿을 선택하고 이름을 *Gruntfile.js* 로 변경한 다음, **추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js* , and click the **Add** button.</span></span>

1. <span data-ttu-id="5712a-158">*Gruntfile.js* 에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="5712a-159">`initConfig` 함수가 각 패키지의 옵션을 설정하고, 모듈의 나머지 부분이 작업을 로드 및 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="5712a-160">`initConfig` 함수 내에서 아래 예제 *Gruntfile.js* 와 같이 `clean` 작업의 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="5712a-161">`clean` 작업은 디렉터리 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="5712a-162">이 작업은 *wwwroot/lib* 에서 파일을 제거하고, 전체 */temp* 디렉터리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="5712a-163">`initConfig` 함수 아래에 `grunt.loadNpmTasks` 호출을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="5712a-164">그러면 Visual Studio에서 작업을 실행할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="5712a-165">*Gruntfile.js* 를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="5712a-166">파일이 아래 스크린샷과 같이 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-166">The file should look something like the screenshot below.</span></span>

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="5712a-168">*Gruntfile.js* 를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **작업 실행기 탐색기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="5712a-169">**작업 실행기 탐색기** 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-169">The **Task Runner Explorer** window will open.</span></span>

    ![작업 실행기 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="5712a-171">**작업 실행기 탐색기** 의 **작업** 아래에 `clean`이 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![작업 실행기 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="5712a-173">정리 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **실행** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="5712a-174">명령 창에 작업 진행률이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-174">A command window displays progress of the task.</span></span>

    ![작업 실행기 탐색기 정리 작업 실행](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="5712a-176">아직 정리할 파일이나 디렉터리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="5712a-177">원하는 경우 솔루션 탐색기에서 수동으로 만든 다음, 정리 작업을 테스트로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="5712a-178">`initConfig` 함수에서 아래 코드를 사용하여 `concat`의 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="5712a-179">`src` 속성 배열에는 결합할 파일이 결합 순서대로 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="5712a-180">`dest` 속성은 생성된 결합 파일의 경로를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5712a-181">위의 코드에서 `all` 속성은 대상 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="5712a-182">대상은 여러 빌드 환경을 허용하기 위해 일부 Grunt 작업에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="5712a-183">IntelliSense를 사용하여 기본 제공 대상을 보거나 고유한 대상을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="5712a-184">아래 코드를 사용하여 `jshint` 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="5712a-185">‘임시’ 디렉터리에 있는 모든 JavaScript 파일에 대해 jshint `code-quality` 유틸리티를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5712a-186">“-W069” 옵션은 JavaScript에서 점 표기법 대신 대괄호 구문을 사용하여 속성을 할당하는 경우(즉, `Tastes.Sweet` 대신 `Tastes["Sweet"]` 사용) 발생하는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="5712a-187">이 옵션은 나머지 프로세스를 계속할 수 있도록 경고를 끕니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="5712a-188">아래 코드를 사용하여 `uglify` 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="5712a-189">작업은 임시 디렉터리에 있는 *combined.js* 파일을 축소하고, 표준 명명 규칙인 *\<file name\>.min.js* 에 따라 결과 파일을 wwwroot/lib에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="5712a-190">`grunt-contrib-clean`을 로드하는 `grunt.loadNpmTasks` 호출 아래에서, 다음 코드를 사용하여 jshint, concat 및 uglify에 대해 동일한 호출을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="5712a-191">*Gruntfile.js* 를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="5712a-192">파일이 아래 예제와 같이 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-192">The file should look something like the example below.</span></span>

    ![grunt 파일 예제 완료](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="5712a-194">**작업 실행기 탐색기** 작업 목록에 `clean`, `concat`, `jshint` 및 `uglify` 작업이 포함되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="5712a-195">각 작업을 순서대로 실행하고 **솔루션 탐색기** 에서 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="5712a-196">각 작업이 오류 없이 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-196">Each task should run without errors.</span></span>

    ![작업 실행기 탐색기 각 작업 실행](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="5712a-198">concat 작업은 새 *combined.js* 파일을 만들어 임시 디렉터리에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="5712a-199">`jshint` 작업은 실행되기만 하고 출력을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="5712a-200">`uglify` 작업은 새 *combined.min.js* 파일을 만들어 *wwwroot/lib* 에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="5712a-201">완료되면 솔루션이 아래 스크린샷과 같이 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![모든 작업 후의 솔루션 탐색기](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="5712a-203">각 패키지의 옵션에 대한 자세한 내용을 보려면 [https://www.npmjs.com/](https://www.npmjs.com/)으로 이동한 다음, 기본 페이지의 검색 상자에서 패키지 이름을 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="5712a-204">예를 들어 grunt-contrib-clean 패키지를 조회하여 해당 매개 변수를 모두 설명하는 설명서 링크를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="5712a-205">요약</span><span class="sxs-lookup"><span data-stu-id="5712a-205">All together now</span></span>

<span data-ttu-id="5712a-206">Grunt `registerTask()` 메서드를 사용하여 일련의 작업을 특정 순서로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="5712a-207">예를 들어 위의 예제 단계를 clean -> concat -> jshint -> uglify 순서로 실행하려면 모듈에 아래 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="5712a-208">InitConfig 외부에서 loadNpmTasks() 호출과 동일한 수준에 코드를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="5712a-209">작업 실행기 탐색기의 별칭 작업 아래에 새 작업이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="5712a-210">마우스 오른쪽 단추를 클릭하고 다른 작업처럼 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="5712a-211">`all` 작업은 `clean`, `concat`, `jshint`, `uglify`를 순서대로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="5712a-213">변경 내용 감시하기</span><span class="sxs-lookup"><span data-stu-id="5712a-213">Watching for changes</span></span>

<span data-ttu-id="5712a-214">`watch` 작업은 파일과 디렉터리를 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="5712a-215">변경 내용이 검색되면 조사식이 자동으로 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="5712a-216">InitConfig에 아래 코드를 추가하여 TypeScript 디렉터리에 있는 \*.js 파일의 변경 내용을 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="5712a-217">JavaScript 파일이 변경되면 `watch`에서 `all` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="5712a-218">`loadNpmTasks()` 호출을 추가하여 작업 실행기 탐색기에 `watch` 작업을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="5712a-219">작업 실행기 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 실행을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="5712a-220">실행 중인 조사식 작업을 보여 주는 명령 창에 “대기 중 ...” 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="5712a-221">TypeScript 파일 중 하나를 열고 공백을 추가한 다음, 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="5712a-222">그러면 조사식 작업이 트리거되고, 다른 작업이 순서대로 실행되도록 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="5712a-223">아래 스크린샷은 샘플 실행을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-223">The screenshot below shows a sample run.</span></span>

![실행 중인 작업 출력](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="5712a-225">Visual Studio 이벤트에 바인딩</span><span class="sxs-lookup"><span data-stu-id="5712a-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="5712a-226">Visual Studio에서 작업할 때마다 수동으로 작업을 시작하려는 경우가 아니면, **빌드 전** , **빌드 후** , **정리** , **프로젝트 열기** 이벤트에 작업을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build** , **After Build** , **Clean** , and **Project Open** events.</span></span>

<span data-ttu-id="5712a-227">Visual Studio를 열 때마다 실행되도록 `watch`를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="5712a-228">작업 실행기 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **바인딩** > **프로젝트 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![프로젝트 열기에 작업 바인딩](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="5712a-230">프로젝트를 언로드했다가 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-230">Unload and reload the project.</span></span> <span data-ttu-id="5712a-231">프로젝트가 다시 로드되면 조사식 작업이 자동으로 실행되기 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="5712a-232">요약</span><span class="sxs-lookup"><span data-stu-id="5712a-232">Summary</span></span>

<span data-ttu-id="5712a-233">Grunt는 대부분의 클라이언트 빌드 작업을 자동화하는 데 사용할 수 있는 강력한 작업 실행기입니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="5712a-234">Grunt는 NPM을 이용하여 해당 패키지를 제공하고, Visual Studio와 도구가 통합되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="5712a-235">Visual Studio의 작업 실행기 탐색기는 구성 파일의 변경 내용을 검색하고 작업 실행, 실행 중인 작업 보기, Visual Studio 이벤트에 작업 바인딩 등을 위한 편리한 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5712a-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
