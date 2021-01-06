---
title: ASP.NET Core에서 LibMan CLI 사용
author: scottaddie
description: ASP.NET Core 프로젝트에서 LibMan CLI를 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054751"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>ASP.NET Core에서 LibMan CLI 사용

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI는 .NET Core가 지원되는 플랫폼 간 도구입니다.

## <a name="prerequisites"></a>사전 요구 사항

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>설치

LibMan CLI를 설치하려면 다음을 수행합니다.

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[.NET Core 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool)가 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 패키지에서 설치됩니다.

특정 NuGet 패키지 원본에서 LibMan CLI를 설치하려면 다음을 수행합니다.

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

위의 예제에서 .NET Core 전역 도구는 로컬 Windows 머신의 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일에서 설치됩니다.

## <a name="usage"></a>사용법

CLI를 성공적으로 설치한 후에는 다음 명령을 사용할 수 있습니다.

```console
libman
```

설치된 CLI 버전을 보려면 다음을 수행합니다.

```console
libman --version
```

사용 가능한 CLI 명령을 보려면 다음을 수행합니다.

```console
libman --help
```

위의 명령은 다음과 유사한 출력을 표시합니다.

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.

## <a name="initialize-libman-in-the-project"></a>프로젝트에서 LibMan 초기화

`libman init` 명령은 *libman.json* 파일이 없는 경우 새로 만듭니다. 이 파일은 기본 항목 템플릿 콘텐츠를 사용하여 만들어집니다.

### <a name="synopsis"></a>개요

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션은 `libman init` 명령에 사용할 수 있습니다.

* `-d|--default-destination <PATH>`

  현재 폴더에 상대적인 경로입니다. *libman.json* 의 라이브러리에 대해 `destination` 속성이 정의되지 않으면 라이브러리 파일이 이 위치에 설치됩니다. `<PATH>` 값은 *libman.json* 의 `defaultDestination` 속성에 기록됩니다.

* `-p|--default-provider <PROVIDER>`

  지정된 라이브러리에 대해 공급자를 지정하지 않은 경우 사용할 공급자입니다. `<PROVIDER>` 값은 *libman.json* 의 `defaultProvider` 속성에 기록됩니다. `<PROVIDER>`을 다음 값 중 하나로 바꿉니다.

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

ASP.NET Core 프로젝트에서 *libman.json* 파일을 만들려면 다음을 수행합니다.

* 프로젝트 루트로 이동합니다.
* 다음 명령을 실행합니다.

  ```console
  libman init
  ```

* 기본 공급자의 이름을 입력하거나 `Enter` 키를 눌러 기본 CDNJS 공급자를 사용합니다. 유효한 값은 다음과 같습니다.

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 명령 - 기본 공급자](_static/libman-init-provider.png)

다음 콘텐츠를 사용하여 *libman.json* 파일이 프로젝트 루트에 추가됩니다.

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>라이브러리 파일 추가

`libman install` 명령은 라이브러리 파일을 다운로드하여 프로젝트에 설치합니다. *libman.json* 파일이 없는 경우 추가됩니다. 라이브러리 파일에 대한 구성 세부 정보를 저장하도록 *libman.json* 파일이 수정됩니다.

### <a name="synopsis"></a>개요

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

설치할 라이브러리의 이름입니다. 이 이름에는 버전 번호 표기법(예: `@1.2.0`)이 포함될 수 있습니다.

### <a name="options"></a>옵션

다음 옵션은 `libman install` 명령에 사용할 수 있습니다.

* `-d|--destination <PATH>`

  라이브러리를 설치할 위치입니다. 이 값을 지정하지 않으면 기본 위치가 사용됩니다. *libman.json* 에 `defaultDestination` 속성이 지정되지 않은 경우 이 옵션은 필수입니다.

* `--files <FILE>`

  라이브러리에서 설치할 파일의 이름을 지정합니다. 지정하지 않으면 라이브러리의 모든 파일이 설치됩니다. 설치할 파일마다 하나의 `--files` 옵션을 제공합니다. 상대 경로도 지원됩니다. 예를 들어 `--files dist/browser/signalr.js`을 참조하십시오.

* `-p|--provider <PROVIDER>`

  라이브러리 획득에 사용할 공급자의 이름입니다. `<PROVIDER>`을 다음 값 중 하나로 바꿉니다.
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  지정하지 않으면 *libman.json* 의 `defaultProvider` 속성이 사용됩니다. *libman.json* 에 `defaultProvider` 속성이 지정되지 않은 경우 이 옵션은 필수입니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

다음 *libman.json* 파일을 고려하세요.

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

CDNJS 공급자를 사용하여 jQuery 버전 3.2.1 *jquery.min.js* 파일을 *wwwroot/scripts/jquery* 폴더에 설치하려면 다음을 수행합니다.

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*libman.json* 파일은 다음과 비슷합니다.

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

파일 시스템 공급자를 사용하여 *C:\\temp\\contosoCalendar\\* 에서 *calendar.js* 및 *calendar.css* 파일을 설치하려면 다음을 수행합니다.

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

다음과 같은 두 가지 이유로 다음 프롬프트가 표시됩니다.

* *libman.json* 파일에는 `defaultDestination` 속성이 포함되어 있지 않습니다.
* `libman install` 명령에는 `-d|--destination` 옵션이 포함되어 있지 않습니다.

![libman install 명령 - 대상](_static/libman-install-destination.png)

기본 대상을 승인한 후에 *libman.json* 파일은 다음과 유사합니다.

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>라이브러리 파일 복원

`libman restore` 명령은 *libman.json* 에 정의된 라이브러리 파일을 설치합니다. 이 때 적용되는 규칙은 다음과 같습니다.

* 프로젝트 루트에 *libman.json* 파일이 없으면 오류가 반환됩니다.
* 라이브러리가 공급자를 지정하는 경우 *libman.json* 의 `defaultProvider` 속성이 무시됩니다.
* 라이브러리가 대상을 지정하는 경우 *libman.json* 의 `defaultDestination` 속성이 무시됩니다.

### <a name="synopsis"></a>개요

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션은 `libman restore` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

*libman.json* 파일에 정의된 라이브러리 파일을 복원하려면 다음을 수행합니다.

```console
libman restore
```

## <a name="delete-library-files"></a>라이브러리 폴더 삭제

`libman clean` 명령은 LibMan을 통해 이전에 복원한 라이브러리 파일을 삭제합니다. 이 작업 후 삭제된 후에 비어 있게 되는 폴더입니다. *libman.json* 의 `libraries` 속성에 있는 라이브러리 파일의 연결된 구성은 제거되지 않습니다.

### <a name="synopsis"></a>개요

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션은 `libman clean` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

LibMan을 통해 설치된 라이브러리 파일을 삭제하려면 다음을 수행합니다.

```console
libman clean
```

## <a name="uninstall-library-files"></a>라이브러리 파일 제거

`libman uninstall` 명령은 다음을 수행합니다.

* 지정된 라이브러리와 연결된 모든 파일을 *libman.json* 의 대상에서 삭제합니다.
* 연결된 라이브러리 구성을 *libman.json* 에서 제거합니다.

다음 경우에 오류가 발생합니다.

* 프로젝트 루트에는 *libman.json* 파일이 없습니다.
* 지정된 라이브러리가 없습니다.

이름이 같은 라이브러리가 2개 이상 설치되어 있으면 하나를 선택하라는 메시지가 표시됩니다.

### <a name="synopsis"></a>개요

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

제거할 라이브러리의 이름입니다. 이 이름에는 버전 번호 표기법(예: `@1.2.0`)이 포함될 수 있습니다.

### <a name="options"></a>옵션

다음 옵션은 `libman uninstall` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

다음 *libman.json* 파일을 고려하세요.

[!code-json[](samples/LibManSample/libman.json)]

* jQuery를 제거하려면 다음 명령 중 하나를 수행합니다.

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* `filesystem` 공급자를 통해 설치된 Lodash 파일을 제거하려면 다음을 수행합니다.

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>라이브러리 버전 업데이트

`libman update` 명령은 LibMan을 통해 설치된 라이브러리를 지정된 버전으로 업데이트합니다.

다음 경우에 오류가 발생합니다.

* 프로젝트 루트에는 *libman.json* 파일이 없습니다.
* 지정된 라이브러리가 없습니다.

이름이 같은 라이브러리가 2개 이상 설치되어 있으면 하나를 선택하라는 메시지가 표시됩니다.

### <a name="synopsis"></a>개요

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

업데이트할 라이브러리의 이름입니다.

### <a name="options"></a>옵션

다음 옵션은 `libman update` 명령에 사용할 수 있습니다.

* `-pre`

  라이브러리의 최신 시험판 버전을 가져옵니다.

* `--to <VERSION>`

  라이브러리의 특정 버전을 가져옵니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

* jQuery를 최신 버전으로 업데이트하려면 다음을 수행합니다.

  ```console
  libman update jquery
  ```

* jQuery를 버전 3.3.1로 업데이트하려면 다음을 수행합니다.

  ```console
  libman update jquery --to 3.3.1
  ```

* jQuery를 최신 시험판 버전으로 업데이트하려면 다음을 수행합니다.

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>라이브러리 캐시 관리

`libman cache` 명령은 LibMan 라이브러리 캐시를 관리합니다. `filesystem` 공급자는 라이브러리 캐시를 사용하지 않습니다.

### <a name="synopsis"></a>개요

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>인수

`PROVIDER`

`clean` 명령에서만 사용됩니다. 정리할 공급자 캐시를 지정합니다. 유효한 값은 다음과 같습니다.

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>옵션

다음 옵션은 `libman cache` 명령에 사용할 수 있습니다.

* `--files`

  캐시된 파일의 이름을 나열합니다.

* `--libraries`

  캐시된 라이브러리의 이름을 나열합니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

* 공급자당 캐시된 라이브러리의 이름을 보려면 다음 명령 중 하나를 사용합니다.

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  다음과 비슷한 출력이 표시됩니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* 공급자당 캐시된 라이브러리 파일의 이름을 보려면 다음을 수행합니다.

  ```console
  libman cache list --files
  ```

  다음과 비슷한 출력이 표시됩니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  위의 출력은 jQuery 버전 3.2.1 및 3.3.1이 CDNJS 공급자 아래에 캐시됨을 보여 줍니다.

* CDNJS 공급자에 대한 라이브러리 캐시를 비우려면 다음을 수행합니다.

  ```console
  libman cache clean cdnjs
  ```

  CDNJS 공급자 캐시를 비우면 `libman cache list` 명령은 다음을 표시합니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* 지원되는 모든 공급자에 대한 캐시를 비우려면 다음을 수행합니다.

  ```console
  libman cache clean
  ```

  모든 공급자 캐시를 비우면 `libman cache list` 명령은 다음을 표시합니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>추가 자료

* [전역 도구 설치](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
