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
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332156"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 이미지 호스팅

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core는 [기본적으로 HTTPS를](./enforcing-ssl.md)사용 합니다. [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 는 [인증서](https://en.wikipedia.org/wiki/Public_key_certificate) 를 신뢰, id 및 암호화에 의존 합니다.

이 문서에서는 HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지를 실행 하는 방법을 설명 합니다.

개발 시나리오는 [HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 응용 프로그램 개발](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) 을 참조 하세요.

이 샘플에는 docker [클라이언트](https://www.docker.com/products/docker)의 [docker 17.06](https://docs.docker.com/release-notes/docker-ce) 이상이 필요 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 지침 중 일부에는 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 이상이 필요 합니다.

## <a name="certificates"></a>인증서

도메인에 대 한 [프로덕션 호스팅을](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) 위해서는 [인증 기관의](https://wikipedia.org/wiki/Certificate_authority) 인증서가 필요 합니다. [Let's Encrypt](https://letsencrypt.org/) 는 무료 인증서를 제공 하는 인증 기관입니다.

이 문서에서는 미리 빌드된 이미지를 호스트 하기 위해 [자체 서명 된 개발 인증서](https://en.wikipedia.org/wiki/Self-signed_certificate) 를 사용 `localhost` 합니다. 지침은 프로덕션 인증서를 사용 하는 것과 유사 합니다.

[Dotnet dev-인증서](/dotnet/core/additional-tools/self-signed-certificates-guide) 를 사용 하 여 개발 및 테스트를 위한 자체 서명 된 인증서를 만듭니다.

프로덕션 인증서의 경우:

* `dotnet dev-certs`도구는 필요 하지 않습니다.
* 지침에 사용 되는 위치에 인증서를 저장할 필요가 없습니다. 사이트 디렉터리 내에 인증서를 저장 하지 않는 것이 좋지만 모든 위치는 작동 해야 합니다.

다음 섹션에 포함 된 지침은 Docker의 명령줄 옵션을 사용 하 여 컨테이너에 인증서를 탑재 합니다 `-v` . Dockerfile의 명령을 사용 하 여 컨테이너 이미지에 인증서를 추가할 수 `COPY` 있지만 권장 되지는 않습니다. *Dockerfile* 다음과 같은 이유로 인증서를 이미지로 복사 하는 것은 권장 되지 않습니다.

* 개발자 인증서를 사용 하 여 테스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.
* 프로덕션 인증서를 사용 하 여 호스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.
* 인증서 공개에는 상당한 위험이 있습니다.

## <a name="running-pre-built-container-images-with-https"></a>HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지 실행

운영 체제 구성에 대해 다음 지침을 따르십시오.

### <a name="windows-using-linux-containers"></a>Linux 컨테이너를 사용 하는 Windows

인증서 생성 및 로컬 컴퓨터 구성:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

위의 명령에서을 암호로 바꿉니다 `{ password here }` .

명령 셸에서 HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너 이미지를 실행 합니다.

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

[PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .

암호는 인증서에 사용 된 암호와 일치 해야 합니다.


참고:이 경우 인증서는 파일 이어야 합니다 `.pfx` .  `.crt`또는 파일을 `.key` 암호를 사용 하거나 사용 하지 않고 사용 하는 것은 샘플 컨테이너에서 지원 되지 않습니다.  예를 들어 파일을 지정할 때 `.crt` 컨테이너는 ' 서버 모드 SSL에서 연결 된 개인 키가 있는 인증서를 사용 해야 합니다. '와 같은 오류 메시지를 반환할 수 있습니다. [Wsl](/windows/wsl/about)을 사용 하는 경우 탑재 경로의 유효성을 검사 하 여 인증서가 올바르게 로드 되는지 확인 합니다.

### <a name="macos-or-linux"></a>macOS 또는 Linux

인증서 생성 및 로컬 컴퓨터 구성:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` 는 macOS 및 Windows 에서만 지원 됩니다. 배포에서 지원 되는 방식으로 Linux에서 인증서를 신뢰 해야 합니다. 브라우저에서 인증서를 신뢰 해야 할 수도 있습니다.

위의 명령에서을 암호로 바꿉니다 `{ password here }` .

HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너 이미지를 실행 합니다.

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

암호는 인증서에 사용 된 암호와 일치 해야 합니다.

### <a name="windows-using-windows-containers"></a>Windows 컨테이너를 사용 하는 windows

인증서 생성 및 로컬 컴퓨터 구성:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

위의 명령에서을 암호로 바꿉니다 `{ password here }` . [PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .

HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너 이미지를 실행 합니다.

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

암호는 인증서에 사용 된 암호와 일치 해야 합니다. [PowerShell](/powershell/scripting/overview)을 사용 하는 경우를 `%USERPROFILE%` 로 바꿉니다 `$env:USERPROFILE` .
