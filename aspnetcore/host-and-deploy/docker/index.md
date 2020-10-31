---
title: Docker 컨테이너에서 ASP.NET Core 호스트
author: rick-anderson
description: Docker 컨테이너에서 ASP.NET Core 앱을 호스트하는 방법을 학습하기 위한 리소스 링크를 검색합니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059782"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Docker 컨테이너에서 ASP.NET Core 호스트

다음 문서는 Docker에서 ASP.NET Core 앱을 호스팅하는 학습에서 사용할 수 있습니다.

[컨테이너 및 Docker 소개](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
애플리케이션 또는 서비스, 이에 해당하는 종속성 및 구성이 컨테이너 이미지로 패키지되는 소프트웨어 개발 방법인 컨테이너화에 대해 살펴볼 수 있습니다. 이미지를 테스트한 후 호스트에 배포할 수 있습니다.

[Docker란?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
클라우드 또는 온-프레미스로 실행될 수 있는 이식 가능하고 문제를 스스로 해결할 수 있는 컨테이너로서 앱 배포를 자동화하기 위한 오픈 소스 프로젝트인 Docker에 대해 살펴볼 수 있습니다.

[Docker 용어](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Docker 기술에 대한 용어 및 정의를 알아봅니다.

[Docker 컨테이너, 이미지 및 레지스트리](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
여러 환경 간의 일관된 배포를 위해 Docker 컨테이너 이미지가 이미지 레지스트리에 저장되는 방법을 확인합니다.

<xref:host-and-deploy/docker/building-net-docker-images> ASP.NET Core 앱을 빌드하고 Docker화하는 방법을 알아봅니다. Microsoft에서 관리하는 Docker 이미지를 살펴보고 사용 사례를 검토합니다.

[Visual Studio 컨테이너 도구](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Visual Studio에서 Windows용 Docker에 대해 .NET Framework 또는 .NET Core를 대상으로 하는 ASP.NET Core 앱의 빌드, 디버깅 및 실행을 지원하는 방법을 살펴볼 수 있습니다. Windows 및 Linux 컨테이너가 모두 지원됩니다.

[Azure Container Registry에 게시](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
PowerShell을 사용하여 Azure의 Docker 호스트에 ASP.NET Core 앱을 배포하기 위해 Visual Studio 컨테이너 도구 확장을 사용하는 방법을 알아봅니다.

[프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)  
프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다. 프록시를 통해 요청을 전달하면 체계 및 클라이언트 IP와 같은 원래 요청에 대한 정보를 모호하게 합니다. 앱에 대한 수동 요청에 대한 정보를 전달해야 할 수도 있습니다.

[Docker 및 소형 컨테이너를 사용하는 GC](xref:performance/memory#sc) 소형 컨테이너를 사용하여 GC 선택을 설명합니다.