---
title: HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 이미지 호스팅
author: rick-anderson
description: HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 이미지를 호스트 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
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
uid: security/docker-https
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588642"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="120bd-103">HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 이미지 호스팅</span><span class="sxs-lookup"><span data-stu-id="120bd-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="120bd-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="120bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="120bd-105">ASP.NET Core는 [기본적으로 HTTPS를](./enforcing-ssl.md)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="120bd-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 는 [인증서](https://en.wikipedia.org/wiki/Public_key_certificate) 를 신뢰, id 및 암호화에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="120bd-107">이 문서에서는 HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지를 실행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="120bd-108">개발 시나리오는 [HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 응용 프로그램 개발](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="120bd-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="120bd-109">이 샘플에서는 [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) 이상의 [Docker 클라이언트](https://www.docker.com/products/docker)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="120bd-110">전제 조건</span><span class="sxs-lookup"><span data-stu-id="120bd-110">Prerequisites</span></span>

<span data-ttu-id="120bd-111">이 문서의 지침 중 일부에는 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="120bd-112">인증서</span><span class="sxs-lookup"><span data-stu-id="120bd-112">Certificates</span></span>

<span data-ttu-id="120bd-113">도메인에 대 한 [프로덕션 호스팅을](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) 위해서는 [인증 기관의](https://wikipedia.org/wiki/Certificate_authority) 인증서가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="120bd-114">[Let's Encrypt](https://letsencrypt.org/) 는 무료 인증서를 제공 하는 인증 기관입니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="120bd-115">이 문서에서는 미리 빌드된 이미지를 호스트 하기 위해 [자체 서명 된 개발 인증서](https://en.wikipedia.org/wiki/Self-signed_certificate) 를 사용 `localhost` 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="120bd-116">지침은 프로덕션 인증서를 사용 하는 것과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="120bd-117">[Dotnet dev-인증서](/dotnet/core/additional-tools/self-signed-certificates-guide) 를 사용 하 여 개발 및 테스트를 위한 자체 서명 된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="120bd-118">프로덕션 인증서의 경우:</span><span class="sxs-lookup"><span data-stu-id="120bd-118">For production certs:</span></span>

* <span data-ttu-id="120bd-119">`dotnet dev-certs`도구는 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="120bd-120">지침에 사용 되는 위치에 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="120bd-121">사이트 디렉터리 내에 인증서를 저장 하지 않는 것이 좋지만 모든 위치는 작동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="120bd-122">다음 섹션에 포함 된 지침은 Docker의 명령줄 옵션을 사용 하 여 컨테이너에 인증서를 탑재 합니다 `-v` .</span><span class="sxs-lookup"><span data-stu-id="120bd-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="120bd-123">Dockerfile의 명령을 사용 하 여 컨테이너 이미지에 인증서를 추가할 수 `COPY` 있지만 권장 되지는 않습니다. </span><span class="sxs-lookup"><span data-stu-id="120bd-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="120bd-124">다음과 같은 이유로 인증서를 이미지로 복사 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="120bd-125">개발자 인증서를 사용 하 여 테스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="120bd-126">프로덕션 인증서를 사용 하 여 호스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="120bd-127">인증서 공개에는 상당한 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="120bd-128">HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지 실행</span><span class="sxs-lookup"><span data-stu-id="120bd-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="120bd-129">운영 체제 구성에 대해 다음 지침을 따르십시오.</span><span class="sxs-lookup"><span data-stu-id="120bd-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="120bd-130">Linux 컨테이너를 사용 하는 Windows</span><span class="sxs-lookup"><span data-stu-id="120bd-130">Windows using Linux containers</span></span>

<span data-ttu-id="120bd-131">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="120bd-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="120bd-132">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="120bd-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="120bd-133">명령 셸에서 HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너 이미지를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="120bd-134">[PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="120bd-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="120bd-135">암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="120bd-136">참고:이 경우 인증서는 파일 이어야 합니다 `.pfx` .</span><span class="sxs-lookup"><span data-stu-id="120bd-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="120bd-137">`.crt`또는 파일을 `.key` 암호를 사용 하거나 사용 하지 않고 사용 하는 것은 샘플 컨테이너에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="120bd-138">예를 들어 파일을 지정할 때 `.crt` 컨테이너는 ' 서버 모드 SSL에서 연결 된 개인 키가 있는 인증서를 사용 해야 합니다. '와 같은 오류 메시지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="120bd-139">[Wsl](/windows/wsl/about)을 사용 하는 경우 탑재 경로의 유효성을 검사 하 여 인증서가 올바르게 로드 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="120bd-140">macOS 또는 Linux</span><span class="sxs-lookup"><span data-stu-id="120bd-140">macOS or Linux</span></span>

<span data-ttu-id="120bd-141">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="120bd-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="120bd-142">`dotnet dev-certs https --trust` 는 macOS 및 Windows 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="120bd-143">배포에서 지원 되는 방식으로 Linux에서 인증서를 신뢰 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="120bd-144">브라우저에서 인증서를 신뢰 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="120bd-145">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="120bd-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="120bd-146">HTTPS용으로 구성된 ASP.NET Core를 사용하여 컨테이너 이미지를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="120bd-147">암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="120bd-148">Windows 컨테이너를 사용 하는 windows</span><span class="sxs-lookup"><span data-stu-id="120bd-148">Windows using Windows containers</span></span>

<span data-ttu-id="120bd-149">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="120bd-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="120bd-150">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="120bd-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="120bd-151">[PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="120bd-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="120bd-152">HTTPS용으로 구성된 ASP.NET Core를 사용하여 컨테이너 이미지를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="120bd-153">암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="120bd-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="120bd-154">[PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="120bd-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
