---
title: ASP.NET Core에서 Grunt 사용
author: rick-anderson
description: ASP.NET Core에서 Grunt 사용
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054556"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core에서 Grunt 사용

Grunt는 스크립트 축소, TypeScript 컴파일, 코드 품질 “Lint” 도구, CSS 전처리기 및 클라이언트 개발을 지원하기 위해 수행해야 하는 거의 모든 반복 작업을 자동화하는 JavaScript 작업 실행기입니다. Grunt는 Visual Studio에서 완전히 지원됩니다.

이 예제에서는 빈 ASP.NET Core 프로젝트를 시작점으로 사용하여 클라이언트 빌드 프로세스를 처음부터 자동화하는 방법을 보여 줍니다.

완성된 예제는 대상 배포 디렉터리 정리, JavaScript 파일 결합, 코드 품질 검사, JavaScript 파일 콘텐츠 압축 및 웹 애플리케이션 루트에 배포 작업을 수행합니다. 다음 패키지를 사용합니다.

* **grunt** : Grunt 작업 실행기 패키지입니다.

* **grunt-contrib-clean** : 파일 또는 디렉터리를 제거하는 플러그 인입니다.

* **grunt-contrib-jshint** : JavaScript 코드 품질을 검토하는 플러그 인입니다.

* **grunt-contrib-concat** : 파일을 단일 파일로 조인하는 플러그 인입니다.

* **grunt-contrib-uglify** : 크기를 줄이기 위해 JavaScript를 축소하는 플러그 인입니다.

* **grunt-contrib-watch** : 파일 작업을 감시하는 플러그 인입니다.

## <a name="preparing-the-application"></a>애플리케이션 준비

먼저, 비어 있는 새로운 웹 애플리케이션을 설정하고 TypeScript 예제 파일을 추가합니다. TypeScript 파일은 기본 Visual Studio 설정을 사용하여 JavaScript로 자동 컴파일되며, Grunt를 통해 처리할 원시 자료입니다.

1. Visual Studio에서 새 `ASP.NET Web Application`을 만듭니다.

2. **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET Core **비어 있음** 템플릿을 선택하고 확인 단추를 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 구조를 검토합니다. `\src` 폴더는 비어 있는 `wwwroot` 및 `Dependencies` 노드를 포함합니다.

    ![비어 있는 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4. 이름이 `TypeScript`인 새 폴더를 프로젝트 디렉터리에 추가합니다.

5. 파일을 추가하기 전에 Visual Studio에서 TypeScript 파일에 대한 ‘저장 시 컴파일’ 옵션이 선택되었는지 확인합니다. **도구** > **옵션** > **텍스트 편집기** > **Typescript** > **프로젝트** 로 이동합니다.

    ![TypeScript 파일의 자동 컴파일 설정 옵션](using-grunt/_static/typescript-options.png)

6. `TypeScript` 디렉터리를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 > 새 항목** 을 선택합니다. **JavaScript 파일** 항목을 선택하고 파일 이름을 *Tastes.ts* 로 지정합니다(\*.ts 확장명 확인). 아래 TypeScript 코드 줄을 파일에 복사합니다. 저장하면 새 *Tastes.js* 파일이 JavaScript 소스와 함께 표시됩니다.

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. **TypeScript** 디렉터리에 두 번째 파일을 추가하고 이름을 `Food.ts`로 지정합니다. 아래 코드를 파일에 복사합니다.

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

## <a name="configuring-npm"></a>NPM 구성

grunt 및 grunt-tasks를 다운로드하도록 NPM을 구성합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 > 새 항목** 을 선택합니다. **NPM 구성 파일** 항목을 선택하고 기본 이름인 *package.json* 을 그대로 둔 다음, **추가** 단추를 클릭합니다.

2. *package.json* 파일에서 `devDependencies` 개체 중괄호 안에 “grunt”를 입력합니다. Intellisense 목록에서 `grunt`를 선택하고 Enter 키를 누릅니다. Visual Studio에서 grunt 패키지 이름에 따옴표를 붙이고 콜론을 추가합니다. 콜론 오른쪽에서 Intellisense 목록 맨 위에 있는 안정적인 최신 패키지 버전을 선택합니다(Intellisense가 표시되지 않는 경우 `Ctrl-Space`를 누름).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM은 [유의적 버전](https://semver.org/)을 사용하여 종속성을 구성합니다. SemVer이라는 유의적 버전은 번호 매기기 체계 \<major>.\<minor>.\<patch>를 사용하여 패키지를 확인합니다. Intellisense는 몇 가지 일반적인 선택 항목만 표시하여 유의적 버전을 간소화합니다. Intellisense 목록의 맨 위 항목(위 예제에서는 0.4.5)은 안정적인 최신 패키지 버전으로 간주됩니다. 캐럿(^) 기호는 가장 최근의 주 버전과 일치하고, 물결표(~)는 가장 최근의 부 버전과 일치합니다. SemVer에서 제공하는 전체 표현의 가이드로 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver)를 참조하세요.

3. 종속성을 더 추가하여 아래 예제와 같이 *clean* , *jshint* , *concat* , *uglify* , *watch* 용 grunt-contrib-\* 패키지를 로드합니다. 버전은 예제와 일치하지 않아도 됩니다.

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

4. *package.json* 파일을 저장합니다.

각 패키지에 필요한 모든 파일과 함께 각 `devDependencies` 항목의 패키지가 다운로드됩니다. **솔루션 탐색기** 에서 **모든 파일 표시** 단추를 사용하도록 설정하여 *node_modules* 디렉터리에서 패키지 파일을 찾을 수 있습니다.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 필요한 경우, `Dependencies\NPM`을 마우스 오른쪽 단추로 클릭하고 **패키지 복원** 메뉴 옵션을 선택하여 **솔루션 탐색기** 에서 종속성을 수동으로 복원할 수 있습니다.

![패키지 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt 구성

Grunt는 수동으로 실행하거나 Visual Studio의 이벤트를 기준으로 자동 실행되도록 구성할 수 있는 작업을 정의, 로드, 등록하는 *Gruntfile.js* 라는 매니페스트를 사용하여 구성합니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 선택합니다. **JavaScript 파일** 항목 템플릿을 선택하고 이름을 *Gruntfile.js* 로 변경한 다음, **추가** 단추를 클릭합니다.

1. *Gruntfile.js* 에 다음 코드를 추가합니다. `initConfig` 함수가 각 패키지의 옵션을 설정하고, 모듈의 나머지 부분이 작업을 로드 및 등록합니다.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. `initConfig` 함수 내에서 아래 예제 *Gruntfile.js* 와 같이 `clean` 작업의 옵션을 추가합니다. `clean` 작업은 디렉터리 문자열 배열을 허용합니다. 이 작업은 *wwwroot/lib* 에서 파일을 제거하고, 전체 */temp* 디렉터리를 제거합니다.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. `initConfig` 함수 아래에 `grunt.loadNpmTasks` 호출을 추가합니다. 그러면 Visual Studio에서 작업을 실행할 수 있게 됩니다.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile.js* 를 저장합니다. 파일이 아래 스크린샷과 같이 표시되어야 합니다.

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *Gruntfile.js* 를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **작업 실행기 탐색기** 를 선택합니다. **작업 실행기 탐색기** 창이 열립니다.

    ![작업 실행기 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

1. **작업 실행기 탐색기** 의 **작업** 아래에 `clean`이 표시되는지 확인합니다.

    ![작업 실행기 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

1. 정리 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **실행** 을 선택합니다. 명령 창에 작업 진행률이 표시됩니다.

    ![작업 실행기 탐색기 정리 작업 실행](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 아직 정리할 파일이나 디렉터리가 없습니다. 원하는 경우 솔루션 탐색기에서 수동으로 만든 다음, 정리 작업을 테스트로 실행할 수 있습니다.

1. `initConfig` 함수에서 아래 코드를 사용하여 `concat`의 항목을 추가합니다.

    `src` 속성 배열에는 결합할 파일이 결합 순서대로 나열됩니다. `dest` 속성은 생성된 결합 파일의 경로를 할당합니다.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 위의 코드에서 `all` 속성은 대상 이름입니다. 대상은 여러 빌드 환경을 허용하기 위해 일부 Grunt 작업에서 사용됩니다. IntelliSense를 사용하여 기본 제공 대상을 보거나 고유한 대상을 할당할 수 있습니다.

1. 아래 코드를 사용하여 `jshint` 작업을 추가합니다.

    ‘임시’ 디렉터리에 있는 모든 JavaScript 파일에 대해 jshint `code-quality` 유틸리티를 실행합니다.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > “-W069” 옵션은 JavaScript에서 점 표기법 대신 대괄호 구문을 사용하여 속성을 할당하는 경우(즉, `Tastes.Sweet` 대신 `Tastes["Sweet"]` 사용) 발생하는 오류입니다. 이 옵션은 나머지 프로세스를 계속할 수 있도록 경고를 끕니다.

1. 아래 코드를 사용하여 `uglify` 작업을 추가합니다.

    작업은 임시 디렉터리에 있는 *combined.js* 파일을 축소하고, 표준 명명 규칙인 *\<file name\>.min.js* 에 따라 결과 파일을 wwwroot/lib에 만듭니다.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. `grunt-contrib-clean`을 로드하는 `grunt.loadNpmTasks` 호출 아래에서, 다음 코드를 사용하여 jshint, concat 및 uglify에 대해 동일한 호출을 포함합니다.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile.js* 를 저장합니다. 파일이 아래 예제와 같이 표시되어야 합니다.

    ![grunt 파일 예제 완료](using-grunt/_static/gruntfile-js-complete.png)

1. **작업 실행기 탐색기** 작업 목록에 `clean`, `concat`, `jshint` 및 `uglify` 작업이 포함되었습니다. 각 작업을 순서대로 실행하고 **솔루션 탐색기** 에서 결과를 확인합니다. 각 작업이 오류 없이 실행되어야 합니다.

    ![작업 실행기 탐색기 각 작업 실행](using-grunt/_static/task-runner-explorer-run-each-task.png)

    concat 작업은 새 *combined.js* 파일을 만들어 임시 디렉터리에 배치합니다. `jshint` 작업은 실행되기만 하고 출력을 생성하지 않습니다. `uglify` 작업은 새 *combined.min.js* 파일을 만들어 *wwwroot/lib* 에 배치합니다. 완료되면 솔루션이 아래 스크린샷과 같이 표시되어야 합니다.

    ![모든 작업 후의 솔루션 탐색기](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 각 패키지의 옵션에 대한 자세한 내용을 보려면 [https://www.npmjs.com/](https://www.npmjs.com/)으로 이동한 다음, 기본 페이지의 검색 상자에서 패키지 이름을 조회합니다. 예를 들어 grunt-contrib-clean 패키지를 조회하여 해당 매개 변수를 모두 설명하는 설명서 링크를 가져올 수 있습니다.

### <a name="all-together-now"></a>요약

Grunt `registerTask()` 메서드를 사용하여 일련의 작업을 특정 순서로 실행할 수 있습니다. 예를 들어 위의 예제 단계를 clean -> concat -> jshint -> uglify 순서로 실행하려면 모듈에 아래 코드를 추가합니다. InitConfig 외부에서 loadNpmTasks() 호출과 동일한 수준에 코드를 추가해야 합니다.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

작업 실행기 탐색기의 별칭 작업 아래에 새 작업이 표시됩니다. 마우스 오른쪽 단추를 클릭하고 다른 작업처럼 실행할 수 있습니다. `all` 작업은 `clean`, `concat`, `jshint`, `uglify`를 순서대로 실행합니다.

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>변경 내용 감시하기

`watch` 작업은 파일과 디렉터리를 감시합니다. 변경 내용이 검색되면 조사식이 자동으로 작업을 트리거합니다. InitConfig에 아래 코드를 추가하여 TypeScript 디렉터리에 있는 \*.js 파일의 변경 내용을 감시합니다. JavaScript 파일이 변경되면 `watch`에서 `all` 작업을 실행합니다.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

`loadNpmTasks()` 호출을 추가하여 작업 실행기 탐색기에 `watch` 작업을 표시합니다.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

작업 실행기 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 실행을 선택합니다. 실행 중인 조사식 작업을 보여 주는 명령 창에 “대기 중 ...” 메시지가 표시됩니다. TypeScript 파일 중 하나를 열고 공백을 추가한 다음, 파일을 저장합니다. 그러면 조사식 작업이 트리거되고, 다른 작업이 순서대로 실행되도록 트리거됩니다. 아래 스크린샷은 샘플 실행을 보여 줍니다.

![실행 중인 작업 출력](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio 이벤트에 바인딩

Visual Studio에서 작업할 때마다 수동으로 작업을 시작하려는 경우가 아니면, **빌드 전** , **빌드 후** , **정리** , **프로젝트 열기** 이벤트에 작업을 바인딩합니다.

Visual Studio를 열 때마다 실행되도록 `watch`를 바인딩합니다. 작업 실행기 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **바인딩** > **프로젝트 열기** 를 선택합니다.

![프로젝트 열기에 작업 바인딩](using-grunt/_static/bindings-project-open.png)

프로젝트를 언로드했다가 다시 로드합니다. 프로젝트가 다시 로드되면 조사식 작업이 자동으로 실행되기 시작합니다.

## <a name="summary"></a>요약

Grunt는 대부분의 클라이언트 빌드 작업을 자동화하는 데 사용할 수 있는 강력한 작업 실행기입니다. Grunt는 NPM을 이용하여 해당 패키지를 제공하고, Visual Studio와 도구가 통합되었습니다. Visual Studio의 작업 실행기 탐색기는 구성 파일의 변경 내용을 검색하고 작업 실행, 실행 중인 작업 보기, Visual Studio 이벤트에 작업 바인딩 등을 위한 편리한 인터페이스를 제공합니다.
