---
title: ASP.NET Core 디렉터리 구조
author: rick-anderson
description: 게시된 ASP.NET Core 앱의 디렉터리 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
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
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 918bc11e06b8f2bea5506d3b61f462e15998efa0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059860"
---
# <a name="aspnet-core-directory-structure"></a>ASP.NET Core 디렉터리 구조

::: moniker range=">= aspnetcore-3.0"

*게시* 디렉터리에는 [dotnet 게시](/dotnet/core/tools/dotnet-publish) 명령에 의해 생성된 앱의 배포 가능한 자산이 포함되어 있습니다. 디렉터리에는 다음이 포함됩니다.

* 애플리케이션 파일
* 구성 파일
* 정적 자산
* 패키지
* 런타임([자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)만 해당)

| 앱 형식 | 디렉터리 구조 |
| -------- | ------------------- |
| [프레임워크 종속 실행 파일](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</li><li>Pages&dagger; MVC 또는 Razor Pages 앱, 페이지가 미리 컴파일되지 않은 경우</li><li>wwwroot&dagger;</li><li>\*.dll 파일</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>Windows의 {ASSEMBLY NAME}{.EXTENSION} .exe 확장, macOS 또는 Linux에 확장 없음</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config(IIS 배포)</li><li>createdump([Linux createdump 유틸리티](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so(Linux 공유 개체 라이브러리)</li><li>\*.a(macOS 아카이브)</li><li>\*.dylib(macOS 동적 라이브러리)</li></ul></li></ul> |
| [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</li><li>Pages&dagger; MVC 또는 Razor Pages 앱, 페이지가 미리 컴파일되지 않은 경우</li><li>wwwroot&dagger;</li><li>\*.dll 파일</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}.exe</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config(IIS 배포)</li></ul></li></ul> |

&dagger;디렉터리를 나타냄

*publish* 디렉터리는 배포의 *애플리케이션 기본 경로* 라고도 하는 *콘텐츠 루트 경로* 를 나타냅니다. 서버에 배포된 앱의 *publish* 디렉터리에 어떤 이름을 지정하더라도 해당 위치는 호스트된 앱에 대한 서버의 실제 경로로 사용됩니다.

*wwwroot* 디렉터리(있는 경우)에는 정적 자산만 포함됩니다.

## <a name="additional-resources"></a>추가 자료

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)
* [대상 프레임워크](/dotnet/standard/frameworks)
* [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*게시* 디렉터리에는 [dotnet 게시](/dotnet/core/tools/dotnet-publish) 명령에 의해 생성된 앱의 배포 가능한 자산이 포함되어 있습니다. 디렉터리에는 다음이 포함됩니다.

* 애플리케이션 파일
* 구성 파일
* 정적 자산
* 패키지
* 런타임([자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)만 해당)

| 앱 형식 | 디렉터리 구조 |
| -------- | ------------------- |
| [프레임워크 종속 실행 파일](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</li><li>Pages&dagger; MVC 또는 Razor Pages 앱, 페이지가 미리 컴파일되지 않은 경우</li><li>wwwroot&dagger;</li><li>\*.dll 파일</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>Windows의 {ASSEMBLY NAME}{.EXTENSION} .exe 확장, macOS 또는 Linux에 확장 없음</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config(IIS 배포)</li><li>createdump([Linux createdump 유틸리티](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so(Linux 공유 개체 라이브러리)</li><li>\*.a(macOS 아카이브)</li><li>\*.dylib(macOS 동적 라이브러리)</li></ul></li></ul> |
| [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</li><li>Pages&dagger; MVC 또는 Razor Pages 앱, 페이지가 미리 컴파일되지 않은 경우</li><li>wwwroot&dagger;</li><li>\*.dll 파일</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}.exe</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config(IIS 배포)</li></ul></li></ul> |

&dagger;디렉터리를 나타냄

*publish* 디렉터리는 배포의 *애플리케이션 기본 경로* 라고도 하는 *콘텐츠 루트 경로* 를 나타냅니다. 서버에 배포된 앱의 *publish* 디렉터리에 어떤 이름을 지정하더라도 해당 위치는 호스트된 앱에 대한 서버의 실제 경로로 사용됩니다.

*wwwroot* 디렉터리(있는 경우)에는 정적 자산만 포함됩니다.

*로그* 폴더를 만드는 것은 [ASP.NET Core 모듈 향상된 디버그 로깅](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 유용합니다. `<handlerSetting>` 값에 제공된 경로에 있는 폴더는 모듈에서 자동으로 생성되지 않으며 모듈이 디버그 로그를 작성할 수 있도록 배포에 미리 존재해야 합니다.

다음 두 가지 방법 중 하나를 사용하여 *Logs* 디렉터리를 배포용으로 만들 수 있습니다.

* 다음 `<Target>` 요소를 프로젝트 파일에 추가합니다.

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   `<MakeDir>` 요소는 게시된 출력에 빈 *Logs* 폴더를 만듭니다. 이 요소는 `PublishDir` 속성을 사용하여 폴더를 만들 대상 위치를 확인합니다. 웹 배포와 같은 여러 배포 방법에서는 배포 중에 빈 폴더를 건너뜁니다. `<WriteLinesToFile>` 요소는 파일이 서버에 배포되도록 *Logs* 폴더에 파일을 생성합니다. 대상 폴더에 대한 쓰기 권한이 작업자 프로세스에 없는 경우 이 방법을 사용한 폴더 만들기가 실패합니다.

* 배포 시 서버에서 *Logs* 디렉터리를 실제로 만드세요.

배포 디렉터리에는 읽기/실행 권한이 필요합니다. *Logs* 디렉터리에는 읽기/쓰기 권한이 필요합니다. 파일이 작성되는 추가 디렉터리에는 읽기/쓰기 권한이 필요합니다.

## <a name="additional-resources"></a>추가 자료

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)
* [대상 프레임워크](/dotnet/standard/frameworks)
* [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)

::: moniker-end
