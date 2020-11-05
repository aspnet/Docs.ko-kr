---
title: Visual Studio에서 ASP.NET Core와 함께 LibMan 사용
author: scottaddie
description: ASP.NET Core 프로젝트에서 Visual Studio와 함께 LibMan을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 1c97f5d7fbf64c5043e6d2277091b9a477833bf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054712"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Visual Studio에서 ASP.NET Core와 함께 LibMan 사용

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio에는 ASP.NET Core 프로젝트의 [LibMan](xref:client-side/libman/index)에 대한 다음과 같은 기본 제공 지원이 있습니다.

* 빌드에서 LibMan 복원 작업 구성 및 실행 지원
* LibMan 복원 및 정리 작업을 트리거하기 위한 메뉴 항목
* 라이브러리를 찾고 프로젝트에 파일을 추가하기 위한 검색 대화 상자
* *libman.json* (LibMan 매니페스트 파일) 편집 지원

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

* **ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="add-library-files"></a>라이브러리 파일 추가

다음 두 가지 방법으로 ASP.NET Core 프로젝트에 라이브러리 파일을 추가할 수 있습니다.

1. [클라이언트 쪽 라이브러리 추가 대화 상자 사용](#use-the-add-client-side-library-dialog)
1. [수동으로 LibMan 매니페스트 파일 항목 구성](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>클라이언트 쪽 라이브러리 추가 대화 상자 사용

클라이언트 쪽 라이브러리를 설치하려면 다음 단계를 수행합니다.

* **솔루션 탐색기** 에서 파일을 추가해야 하는 프로젝트 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **클라이언트 쪽 라이브러리** 를 선택합니다. **클라이언트 쪽 라이브러리 추가** 대화 상자가 나타납니다.

  ![클라이언트 쪽 라이브러리 추가 대화 상자](_static/add-library-dialog.png)

* **공급자** 드롭다운에서 라이브러리 공급자를 선택합니다. 기본 공급자는 CDNJS입니다.
* **라이브러리** 텍스트 상자에 가져올 라이브러리 이름을 입력합니다. IntelliSense에서 입력된 텍스트로 시작하는 라이브러리 목록을 제공합니다.
* IntelliSense 목록에서 라이브러리를 선택합니다. 라이브러리 이름에는 `@` 기호와 선택한 공급자에게 알려진 안정적인 최신 버전이 접미사로 추가됩니다.
* 포함할 파일을 결정합니다.
  * 모든 라이브러리 파일을 포함하려면 **모든 라이브러리 파일 포함** 라디오 단추를 선택합니다.
  * 라이브러리 파일의 하위 집합을 포함하려면 **특정 파일 선택** 라디오 단추를 선택합니다. 라디오 단추를 선택하면 파일 선택기 트리를 사용할 수 있게 됩니다. 다운로드할 파일 이름 왼쪽에 있는 확인란을 선택합니다.
* **대상 위치** 텍스트 상자에 파일을 저장할 프로젝트 폴더를 지정합니다. 각 라이브러리를 개별 폴더에 저장하는 것이 좋습니다.

  제안된 **대상 위치** 폴더는 대화 상자가 시작된 위치를 기반으로 합니다.

  * 프로젝트 루트에서 시작한 경우
    * *wwwroot* 가 있으면 *wwwroot/lib* 가 사용됩니다.
    * *wwwroot* 가 없으면 *lib* 가 사용됩니다.
  * 프로젝트 폴더에서 시작한 경우 해당 폴더 이름이 사용됩니다.

  폴더 제안에 라이브러리 이름이 접미사로 추가됩니다. 다음 표에는 Razor Pages 프로젝트에 jQuery를 설치하는 경우의 폴더 제안이 나와 있습니다.
  
  |시작 위치                           |제안된 폴더      |
  |------------------------------------------|----------------------|
  |프로젝트 루트( *wwwroot* 가 있는 경우)        |*wwwroot/lib/jquery/* |
  |프로젝트 루트( *wwwroot* 가 없는 경우) |*lib/jquery/*         |
  |프로젝트의 *Pages* 폴더                 |*Pages/jquery/*       |

* **설치** 단추를 클릭하여 *libman.json* 의 구성에 따라 파일을 다운로드합니다.
* **출력** 창의 **라이브러리 관리자** 피드에서 설치 정보를 검토합니다. 예를 들어:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>수동으로 LibMan 매니페스트 파일 항목 구성

Visual Studio의 모든 LibMan 작업은 프로젝트 루트의 LibMan 매니페스트( *libman.json* ) 콘텐츠를 기반으로 합니다. *libman.json* 을 수동으로 편집하여 프로젝트의 라이브러리 파일을 구성할 수 있습니다. *libman.json* 을 저장하면 Visual Studio에서 모든 라이브러리 파일을 복원합니다.

편집을 위해 *libman.json* 을 열려는 경우 다음과 같은 옵션이 있습니다.

* **솔루션 탐색기** 에서 *libman.json* 파일을 두 번 클릭합니다.
* **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **클라이언트 쪽 라이브러리 관리** 를 선택합니다. **&#8224;**
* Visual Studio **프로젝트** 메뉴에서 **클라이언트 쪽 라이브러리 관리** 를 선택합니다. **&#8224;**

**&#8224;** 프로젝트 루트에 *libman.json* 파일이 아직 없는 경우, 기본 항목 템플릿 콘텐츠를 사용하여 생성됩니다.

Visual Studio는 색 지정, 서식 지정, IntelliSense, 스키마 유효성 검사 등의 다양한 JSON 편집 지원을 제공합니다. LibMan 매니페스트의 JSON 스키마는 [https://json.schemastore.org/libman](https://json.schemastore.org/libman)에 있습니다.

다음 매니페스트 파일을 사용할 경우 LibMan은 `libraries` 속성에서 정의된 구성에 따라 파일을 검색합니다. `libraries` 내에서 정의된 개체 리터럴에 대한 설명은 다음과 같습니다.

* [jQuery](https://jquery.com/) 버전 3.3.1의 하위 집합이 CDNJS 공급자에서 검색됩니다. 하위 집합은 `files` 속성( *jquery.min.js* , *jquery.js* , *jquery.min.map* )에서 정의됩니다. 파일은 프로젝트의 *wwwroot/lib/jquery* 폴더에 배치됩니다.
* [부트스트랩](https://getbootstrap.com/) 버전 4.1.3 전체가 검색되어 *wwwroot/lib/bootstrap* 폴더에 배치됩니다. 개체 리터럴의 `provider` 속성이 `defaultProvider` 속성 값을 재정의합니다. LibMan은 unpkg 공급자에서 부트스트랩 파일을 검색합니다.
* 조직 내의 규제 기관이 [Lodash](https://lodash.com/)의 하위 집합을 승인했습니다. *lodash.js* 및 *lodash.min.js* 파일은 로컬 파일 시스템의 *C:\\temp\\lodash\\* 에서 검색됩니다. 프로젝트의 *wwwroot/lib/lodash* 폴더에 파일이 복사됩니다.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan은 각 공급자의 라이브러리마다 하나의 버전만 지원합니다. 지정된 공급자와 관련해서 동일한 라이브러리 이름을 가진 라이브러리가 두 개 포함되어 있으면 *libman.json* 파일이 스키마 유효성 검사에 실패합니다.

## <a name="restore-library-files"></a>라이브러리 파일 복원

Visual Studio 내에서 라이브러리 파일을 복원하려면 프로젝트 루트에 유효한 *libman.json* 파일이 있어야 합니다. 복원된 파일은 프로젝트에서 각 라이브러리의 지정된 위치에 배치됩니다.

다음 두 가지 방법으로 ASP.NET Core 프로젝트에서 라이브러리 파일을 복원할 수 있습니다.

1. [빌드 중에 파일 복원](#restore-files-during-build)
1. [수동으로 파일 복원](#restore-files-manually)

### <a name="restore-files-during-build"></a>빌드 중에 파일 복원

LibMan은 빌드 프로세스의 일부로, 정의된 라이브러리 파일을 복원할 수 있습니다. 기본적으로 ‘빌드 시 복원’ 동작은 사용되지 않습니다.

빌드 시 복원 동작을 사용하도록 설정하고 테스트하려면 다음을 수행합니다.

* **솔루션 탐색기** 에서 *libman.json* 을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **빌드에서 클라이언트 쪽 라이브러리 복원 사용** 을 선택합니다.
* NuGet 패키지를 설치하라는 메시지가 표시되 면 **예** 단추를 클릭합니다. [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 패키지가 프로젝트에 추가됩니다.

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* 프로젝트를 빌드하여 LibMan 파일이 복원되는지 확인합니다. `Microsoft.Web.LibraryManager.Build` 패키지는 프로젝트의 빌드 작업 중에 LibMan을 실행하는 MSBuild 대상을 삽입합니다.
* **출력** 창의 **빌드** 피드에서 LibMan 활동 로그를 검토합니다.

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

빌드 시 복원 동작을 사용하도록 설정한 경우, *libman.json* 상황에 맞는 메뉴에 **빌드에서 클라이언트 쪽 라이브러리 복원 사용 안 함** 옵션이 표시됩니다. 이 옵션을 선택하면 프로젝트 파일에서 `Microsoft.Web.LibraryManager.Build` 패키지 참조가 제거됩니다. 또한 각 빌드에서 클라이언트 쪽 라이브러리가 이제 복원되지 않습니다.

빌드 시 복원 설정에 관계없이 언제든지 *libman.json* 상황에 맞는 메뉴에서 수동으로 복원할 수 있습니다. 자세한 내용은 [수동으로 파일 복원](#restore-files-manually)을 참조하세요.

### <a name="restore-files-manually"></a>수동으로 파일 복원

라이브러리 파일을 수동으로 복원하려면 다음을 수행합니다.

* 솔루션에 있는 모든 프로젝트의 경우
  * **솔루션 탐색기** 에서 솔루션 이름을 마우스 오른쪽 단추로 클릭합니다.
  * **클라이언트 쪽 라이브러리 복원** 옵션을 선택합니다.
* 특정 프로젝트의 경우
  * **솔루션 탐색기** 에서 *libman.json* 파일을 마우스 오른쪽 단추로 클릭합니다.
  * **클라이언트 쪽 라이브러리 복원** 옵션을 선택합니다.

복원 작업이 실행되는 동안

* Visual Studio 상태 표시줄의 TSC(작업 상태 센터) 아이콘에 애니메이션이 적용되고 ‘복원 작업 시작됨’이 표시됩니다. 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.
* 상태 표시줄과 **출력** 창의 **라이브러리 관리자** 피드에 메시지가 전송됩니다. 예를 들어:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>라이브러리 파일 삭제

Visual Studio에서 이전에 복원된 라이브러리 파일을 삭제하는 ‘정리’ 작업을 수행하려면 다음을 수행합니다.

* **솔루션 탐색기** 에서 *libman.json* 파일을 마우스 오른쪽 단추로 클릭합니다.
* **클라이언트 쪽 라이브러리 정리** 옵션을 선택합니다.

비라이브러리 파일이 실수로 제거되는 것을 방지하기 위해 정리 작업은 전체 디렉터리를 삭제하지 않습니다. 이전 복원에 포함된 파일만 제거합니다.

정리 작업이 실행되는 동안

* Visual Studio 상태 표시줄의 TSC 아이콘에 애니메이션이 적용되고 ‘클라이언트 라이브러리 작업 시작됨’이 표시됩니다. 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.
* 상태 표시줄과 **출력** 창의 **라이브러리 관리자** 피드에 메시지가 전송됩니다. 예를 들어:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

정리 작업은 프로젝트의 파일만 삭제합니다. 라이브러리 파일은 이후 복원 작업 시 빠르게 검색하기 위해 캐시에 유지됩니다. 로컬 컴퓨터의 캐시에 저장된 라이브러리 파일을 관리하려면 [LibMan CLI](xref:client-side/libman/libman-cli)를 사용합니다.

## <a name="uninstall-library-files"></a>라이브러리 파일 제거

라이브러리 파일을 제거하려면 다음을 수행합니다.

* *libman.json* 을 엽니다.
* 해당 `libraries` 개체 리터럴 안에 캐럿을 배치합니다.
* 왼쪽 여백에 표시되는 전구 아이콘을 클릭하고 **제거\<library_name>@\<library_version>** 를 선택합니다.

  ![라이브러리 제거 상황에 맞는 메뉴 옵션](_static/uninstall-menu-option.png)

또는 수동으로 LibMan 매니페스트( *libman.json* )를 편집하고 저장할 수 있습니다. 파일을 저장하면 [복원 작업](#restore-library-files)이 실행됩니다. *libman.json* 에서 더는 정의되지 않은 라이브러리 파일은 프로젝트에서 제거됩니다.

## <a name="update-library-version"></a>라이브러리 버전 업데이트

업데이트된 라이브러리 버전을 확인하려면 다음을 수행합니다.

* *libman.json* 을 엽니다.
* 해당 `libraries` 개체 리터럴 안에 캐럿을 배치합니다.
* 왼쪽 여백에 표시되는 전구 아이콘을 클릭합니다. **업데이트 확인** 을 마우스로 가리킵니다.

LibMan은 설치된 버전보다 최신 라이브러리 버전이 있는지 확인합니다. 다음과 같은 결과가 발생할 수 있습니다.

* 최신 버전이 이미 설치되어 있는 경우 **업데이트를 찾을 수 없음** 메시지가 표시됩니다.
* 아직 설치되지 않은 경우 안정적인 최신 버전이 표시됩니다.

  ![업데이트 확인 상황에 맞는 메뉴 옵션](_static/update-menu-option.png)

* 설치된 버전보다 더 최신 시험판을 사용할 수 있는 경우 시험판이 표시됩니다.

이전 라이브러리 버전으로 다운그레이드하려면 *libman.json* 파일을 수동으로 편집합니다. 파일을 저장하면 LibMan [복원 작업](#restore-library-files)이 다음을 수행합니다.

* 이전 버전에서 중복 파일을 제거합니다.
* 새 버전에 있는 새 파일과 업데이트된 파일을 추가합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
