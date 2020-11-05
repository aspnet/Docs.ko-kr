---
title: ASP.NET Core의 Docker 이미지
author: rick-anderson
description: Docker 레지스트리에서 게시된 .NET Core Docker 이미지를 사용하는 방법을 알아봅니다. 이미지를 끌어오고 고유한 이미지를 빌드합니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 81daa1d4a996519f44e513b4f61c27cdf2b6ef5e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059808"
---
# <a name="docker-images-for-aspnet-core"></a>ASP.NET Core의 Docker 이미지

이 자습서에서는 Docker 컨테이너에서 ASP.NET Core 앱을 실행하는 방법을 보여 줍니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.
> [!div class="checklist"]
> * Microsoft .NET Core Docker 이미지에 대해 알아보기
> * ASP.NET Core 샘플 앱 다운로드
> * 샘플 앱을 로컬로 실행
> * Linux 컨테이너에서 샘플 앱 실행
> * Windows 컨테이너에서 샘플 앱 실행
> * 수동으로 빌드 및 배포

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker 이미지

이 자습서의 경우 ASP.NET Core 샘플 앱을 다운로드하고 Docker 컨테이너에서 실행합니다. 샘플은 Linux 컨테이너와 Windows 컨테이너 둘 다에서 작동합니다.

샘플 Dockerfile은 [Docker 다단계 빌드 기능](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)을 사용하여 서로 다른 컨테이너에서 빌드 및 실행합니다. 빌드 및 실행 컨테이너는 Microsoft에서 Docker 허브에 제공한 이미지를 기반으로 만들어집니다.

* `dotnet/core/sdk`

  샘플에서는 앱을 빌드하는 데 이 이미지를 사용합니다. 이미지는 CLI(명령줄 도구)가 포함된 .NET Core SDK를 포함합니다. 이미지는 로컬 개발, 디버깅 및 유닛 테스트에 최적화되어 있습니다. 개발 및 컴파일용으로 설치된 도구는 이 이미지를 비교적 큰 이미지로 만듭니다. 

* `dotnet/core/aspnet`

   샘플에서는 앱을 실행하는 데 이 이미지를 사용합니다. 이 이미지는 ASP.NET Core 런타임 및 라이브러리를 포함하며 프로덕션에서 실행 중인 앱에 최적화되어 있습니다. 배포 및 앱 시작 속도를 위해 디자인된 이 이미지는 비교적 작기 때문에 Docker 레지스트리에서 Docker 호스트까지 네트워크 성능이 최적화됩니다. 앱을 실행하는 데 필요한 이진 파일 및 콘텐츠만 컨테이너에 복사됩니다. 콘텐츠를 실행할 준비가 되면 `Docker run`부터 앱 시작까지 가장 빠른 시간에 수행할 수 있습니다. 동적 코드 컴파일은 Docker 모델에 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항
::: moniker range="< aspnetcore-3.0"

* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [.NET Core SDK 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Docker 클라이언트 18.03 이상

  * Linux 배포
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>샘플 앱 다운로드

* [.NET Core Docker 리포지토리](https://github.com/dotnet/dotnet-docker)를 복제하여 샘플을 다운로드합니다. 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>로컬에서 앱 실행

* *dotnet-docker/samples/aspnetapp/aspnetapp* 에 있는 프로젝트 폴더로 이동합니다.

* 다음 명령을 실행하여 앱을 로컬로 빌드 및 실행합니다.

  ```dotnetcli
  dotnet run
  ```

* 브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.

* 명령 프롬프트에서 Ctrl+C를 눌러 앱을 중지합니다.

## <a name="run-in-a-linux-container"></a>Linux 컨테이너에서 실행

* Docker 클라이언트에서 [Linux 컨테이너로 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)합니다.

* *dotnet-docker/samples/aspnetapp* 에 있는 Dockerfile 폴더로 이동합니다.

* 다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build` 명령 인수:
  * 이미지 이름을 aspnetapp으로 지정합니다.
  * 현재 폴더(끝에 마침표)에서 Dockerfile을 찾습니다.

  실행 명령 인수:
  * 의사 TTY를 할당하고 연결되지 않은 경우에도 열어 둡니다. (`--interactive --tty`와 결과가 동일함)
  * 종료될 때 자동으로 컨테이너를 제거합니다.
  * 로컬 머신의 포트 5000을 컨테이너의 포트 80에 매핑합니다.
  * 컨테이너 이름을 aspnetcore_sample로 지정합니다.
  * aspnetapp 이미지를 지정합니다.

* 브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.

## <a name="run-in-a-windows-container"></a>Windows 컨테이너에서 실행

* Docker 클라이언트에서 [Windows 컨테이너로 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)합니다.

`dotnet-docker/samples/aspnetapp`에 있는 Docker 파일 폴더로 이동합니다.

* 다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Windows 컨테이너의 경우 컨테이너의 IP 주소가 필요합니다(`http://localhost:5000`로 이동되지 않음).
  * 다른 명령 프롬프트를 엽니다.
  * `docker ps`를 실행하여 실행 중인 컨테이너를 봅니다. “aspnetcore_sample” 컨테이너가 있는지 확인합니다.
  * `docker exec aspnetcore_sample ipconfig`를 실행하여 컨테이너의 IP 주소를 표시합니다. 명령의 출력은 다음 예제와 같습니다.

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* 컨테이너 IPv4 주소(예: 172.29.245.43)를 복사하고 브라우저 주소 표시줄에 붙여넣어 앱을 테스트합니다.

## <a name="build-and-deploy-manually"></a>수동으로 빌드 및 배포

일부 시나리오에서는 런타임에 필요한 애플리케이션 파일을 컨테이너에 복사하여 앱을 컨테이너에 배포할 수 있습니다. 이 섹션에서는 수동으로 배포하는 방법을 보여 줍니다.

* *dotnet-docker/samples/aspnetapp/aspnetapp* 에 있는 프로젝트 폴더로 이동합니다.

* [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 실행합니다.

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  명령 인수:
  * 릴리스 모드에서 애플리케이션을 빌드합니다(기본값은 디버그 모드).
  * *published* 폴더에서 파일을 만듭니다.

* 애플리케이션을 실행합니다.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* `http://localhost:5000`으로 이동하여 홈페이지를 표시합니다.

Docker 컨테이너 내에서 수동으로 게시된 애플리케이션을 사용하려면 새 Dockerfile을 만들고 `docker build .` 명령을 사용하여 컨테이너를 빌드합니다.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

이전에 실행한 `docker build` 명령에서 사용되는 *Dockerfile* 은 다음과 같습니다.  이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

이전에 실행한 `docker build` 명령에서 사용되는 *Dockerfile* 은 다음과 같습니다.  이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

위의 Dockerfile에 설명된 것처럼 `*.csproj` 파일은 개별 ‘계층’으로서 복사 및 복원됩니다.  `docker build` 명령은 이미지를 빌드할 때 기본 제공 캐시를 사용합니다. `docker build` 명령을 마지막으로 실행한 후 `*.csproj` 파일이 변경되지 않았다면 `dotnet restore` 명령을 다시 실행할 필요가 없습니다. 대신 해당 `dotnet restore` 계층에 대한 기본 제공 캐시가 다시 사용됩니다. 자세한 내용은 [Dockerfiles 작성에 대한 모범 사례](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)를 참조하세요.

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [Docker 빌드 명령](https://docs.docker.com/engine/reference/commandline/build)
* [Docker 실행 명령](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET Core Docker 샘플](https://github.com/dotnet/dotnet-docker)(이 자습서에서 사용되는 샘플)
* [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](../proxy-load-balancer.md)
* [Visual Studio Docker 도구로 작업](./visual-studio-tools-for-docker.md)
* [Debugging with Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)(Visual Studio Code를 사용한 디버깅)
* [GC using Docker and small containers](xref:performance/memory#sc)(Docker 및 소형 컨테이너를 사용하는 GC)

## <a name="next-steps"></a>다음 단계

샘플 앱이 포함된 Git 리포지토리도 문서를 포함합니다. 리포지토리에서 사용 가능한 리소스의 개요를 보려면 [README 파일](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)을 참조하세요. 특히, HTTPS를 구현하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)(HTTPS를 통해 Docker를 사용하여 ASP.NET Core 애플리케이션 개발)
