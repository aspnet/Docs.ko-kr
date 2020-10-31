---
title: ASP.NET Core 및 Azure에서 DevOps
author: CamSoper
description: Azure에서 호스팅되는 ASP.NET Core 앱에 대한 DevOps 파이프라인을 빌드하는 방법에 대한 엔드투엔드 지침을 제공하는 가이드입니다.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10a6bae73743d3063ad2e1ab49fc418ad1d63756
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052294"
---
# <a name="devops-with-aspnet-core-and-azure"></a>ASP.NET Core 및 Azure에서 DevOps

[![표지 이미지](./media/cover-large.png)](https://aka.ms/devopsbook)

작성자: [Cam Soper](https://twitter.com/camsoper) 및 [Scott Addie](https://twitter.com/scottaddie)

이 가이드는 [다운로드 가능한 PDF eBook](https://aka.ms/devopsbook)으로 제공됩니다.

## <a name="welcome"></a>환영 

.NET용 Azure 개발 수명 주기 가이드를 시작합니다. 이 가이드에서는 .NET 도구 및 프로세스를 사용하여 Azure 관련 개발 수명 주기를 빌드하는 기본 개념을 소개합니다. 이 가이드를 완료하면 성숙한 DevOps 도구 체인의 혜택을 누릴 수 있습니다.

## <a name="who-this-guide-is-for"></a>이 가이드의 대상

ASP.NET Core에 익숙한 개발자여야 합니다(200~300레벨). 이 소개에서 설명할 것처럼 Azure에 대한 지식이 없어도 됩니다. 이 가이드는 개발보다 작업에 더 집중하는 DevOps 엔지니어의 경우에 유용할 수도 있습니다.

이 가이드는 Windows 개발자를 대상으로 합니다. 그러나 Linux 및 macOS는 .NET Core에서 완전히 지원됩니다. Linux/macOS에 이 가이드를 적용하려면 Linux/macOS 차이점에 대한 설명선에 유의하세요.

## <a name="what-this-guide-doesnt-cover"></a>이 가이드에서 다루지 않는 내용

이 가이드는 .NET 개발자를 위한 엔드투엔드 연속 배포 환경에 중점을 둡니다. Azure의 모든 항목에 대한 완전한 가이드가 아니며 Azure 서비스에 대한 .NET API에만 광범위하게 집중하지 않습니다. 지속적인 통합, 배포, 모니터링 및 디버깅과 관련된 모든 항목을 강조합니다. 이 가이드의 끝에서 다음 단계에 대한 권장 사항이 제공됩니다. ASP.NET Core 개발자에게 유용한 Azure 플랫폼 서비스가 제안에 포함됩니다.

## <a name="whats-in-this-guide"></a>설명서의 내용

### <a name="tools-and-downloads"></a>[도구 및 다운로드](xref:azure/devops/tools-and-downloads)

이 가이드에 사용되는 도구를 얻을 수 있는 위치에 대해 알아봅니다.

### <a name="deploy-to-app-service"></a>[App Service에 배포](xref:azure/devops/deploy-to-app-service)

Azure App Service에 ASP.NET Core 앱을 배포하는 다양한 방법을 알아봅니다.

### <a name="continuous-integration-and-deployment"></a>[연속 통합 및 배포](xref:azure/devops/cicd)

GitHub, Azure DevOps Services, Azure를 사용하여 엔드투엔드 연속 통합 및 ASP.NET Core 앱에 대한 배포 솔루션을 빌드합니다.

### <a name="monitor-and-debug"></a>[모니터링 및 디버그](xref:azure/devops/monitor)

Azure 도구를 사용하여 애플리케이션을 모니터링하고, 문제를 해결하고, 조정합니다.

### <a name="next-steps"></a>[다음 단계](xref:azure/devops/next-steps)

Azure를 학습하는 ASP.NET Core 개발자를 위한 다른 학습 경로입니다.

## <a name="additional-introductory-reading"></a>추가 소개 읽기

클라우드 컴퓨팅을 처음 접하는 경우 다음 문서를 통해 기본 사항을 알아볼 수 있습니다.

* [클라우드 컴퓨팅이란?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [클라우드 컴퓨팅의 예제](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [IaaS란?](https://azure.microsoft.com/overview/what-is-iaas/)
* [란?](https://azure.microsoft.com/overview/what-is-paas/)
