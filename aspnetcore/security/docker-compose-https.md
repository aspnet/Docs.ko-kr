---
title: HTTPS를 사용 하 여 docker 작성을 사용 하 여 컨테이너에 ASP.NET Core 이미지 호스팅
author: ravipal
description: HTTPS를 통해 Docker Compose를 사용 하 여 ASP.NET Core 이미지를 호스트 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 69c449527a64ec928d436245e9e971ec49303489
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588759"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="7b1f2-103">HTTPS를 통해 Docker Compose를 사용 하 여 ASP.NET Core 이미지 호스팅</span><span class="sxs-lookup"><span data-stu-id="7b1f2-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="7b1f2-104">ASP.NET Core는 [기본적으로 HTTPS를](./enforcing-ssl.md)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="7b1f2-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 는 [인증서](https://en.wikipedia.org/wiki/Public_key_certificate) 를 신뢰, id 및 암호화에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="7b1f2-106">이 문서에서는 HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지를 실행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="7b1f2-107">개발 시나리오는 [HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 응용 프로그램 개발](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="7b1f2-108">이 샘플에서는 [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) 이상의 [Docker 클라이언트](https://www.docker.com/products/docker)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b1f2-109">전제 조건</span><span class="sxs-lookup"><span data-stu-id="7b1f2-109">Prerequisites</span></span>

<span data-ttu-id="7b1f2-110">이 문서의 지침 중 일부에는 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="7b1f2-111">인증서</span><span class="sxs-lookup"><span data-stu-id="7b1f2-111">Certificates</span></span>

<span data-ttu-id="7b1f2-112">도메인에 대 한 [프로덕션 호스팅을](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) 위해서는 [인증 기관의](https://wikipedia.org/wiki/Certificate_authority) 인증서가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="7b1f2-113">[Let's Encrypt](https://letsencrypt.org/) 는 무료 인증서를 제공 하는 인증 기관입니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="7b1f2-114">이 문서에서는 미리 빌드된 이미지를 호스트 하기 위해 [자체 서명 된 개발 인증서](https://wikipedia.org/wiki/Self-signed_certificate) 를 사용 `localhost` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="7b1f2-115">지침은 프로덕션 인증서를 사용 하는 것과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="7b1f2-116">프로덕션 인증서의 경우:</span><span class="sxs-lookup"><span data-stu-id="7b1f2-116">For production certificates:</span></span>

* <span data-ttu-id="7b1f2-117">`dotnet dev-certs`도구는 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="7b1f2-118">지침에 사용 되는 위치에 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="7b1f2-119">사이트 디렉터리 외부의 모든 위치에 인증서를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="7b1f2-120">다음 섹션에 포함 된 지침은 `volumes` *docker-compose.ci.build.yml* 의 속성을 사용 하 여 컨테이너에 인증서를 탑재 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="7b1f2-121">Dockerfile의 명령을 사용 하 여 컨테이너 이미지에 인증서를 추가할 수 `COPY` 있지만 권장 되지는 않습니다. </span><span class="sxs-lookup"><span data-stu-id="7b1f2-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="7b1f2-122">다음과 같은 이유로 인증서를 이미지로 복사 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="7b1f2-123">개발자 인증서를 사용 하 여 테스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="7b1f2-124">프로덕션 인증서를 사용 하 여 호스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="7b1f2-125">인증서 공개에는 상당한 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="7b1f2-126">Docker 작성을 사용 하 여 https 지원으로 컨테이너 시작</span><span class="sxs-lookup"><span data-stu-id="7b1f2-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="7b1f2-127">운영 체제 구성에 대해 다음 지침을 따르십시오.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="7b1f2-128">Linux 컨테이너를 사용 하는 Windows</span><span class="sxs-lookup"><span data-stu-id="7b1f2-128">Windows using Linux containers</span></span>

<span data-ttu-id="7b1f2-129">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="7b1f2-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7b1f2-130">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="7b1f2-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7b1f2-131">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="7b1f2-132">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7b1f2-133">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="7b1f2-134">macOS 또는 Linux</span><span class="sxs-lookup"><span data-stu-id="7b1f2-134">macOS or Linux</span></span>

<span data-ttu-id="7b1f2-135">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="7b1f2-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7b1f2-136">`dotnet dev-certs https --trust` 는 macOS 및 Windows 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="7b1f2-137">배포에서 지원 되는 방식으로 Linux에서 인증서를 신뢰 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="7b1f2-138">브라우저에서 인증서를 신뢰 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="7b1f2-139">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="7b1f2-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7b1f2-140">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="7b1f2-141">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7b1f2-142">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="7b1f2-143">Windows 컨테이너를 사용 하는 windows</span><span class="sxs-lookup"><span data-stu-id="7b1f2-143">Windows using Windows containers</span></span>

<span data-ttu-id="7b1f2-144">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="7b1f2-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7b1f2-145">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="7b1f2-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7b1f2-146">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="7b1f2-147">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7b1f2-148">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b1f2-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
