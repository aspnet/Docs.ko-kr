---
title: 다음 단계 - ASP.NET Core 및 Azure를 사용하는 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하는 DevOps의 추가 학습 경로입니다.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056597"
---
# <a name="next-steps"></a>다음 단계

이 가이드에서는 ASP.NET Core 샘플 앱에 사용할 DevOps 파이프라인을 만들었습니다. 지금까지 Azure App Service에 ASP.NET Core 웹앱을 게시하고 변경 내용의 연속 통합을 자동화하는 방법을 배우는 데 도움이 되었기를 바랍니다.

웹 호스팅과 DevOps 외에도 Azure에는 ASP.NET Core 개발자에게 유용한 여러 가지 PaaS(Platform as a Service) 서비스가 있습니다. 이 섹션에는 가장 일반적으로 사용되는 몇 가지 서비스가 간략하게 요약되어 있습니다.

## <a name="storage-and-databases"></a>스토리지 및 데이터베이스

[Redis Cache](/azure/redis-cache/)는 서비스로 제공되며, 처리량이 높고 대기 시간이 짧은 데이터 캐싱입니다. 페이지 출력을 캐시하고, 데이터베이스 요청을 줄이며, 여러 앱 인스턴스 간에 ASP.NET Core 세션 상태를 제공하는 데 사용할 수 있습니다.

[Azure Storage](/azure/storage/)는 대규모로 확장 가능한 Azure 클라우드 스토리지입니다. 개발자는 신뢰할 수 있는 메시지 큐를 위해 [Queue Storage](/azure/storage/queues/storage-queues-introduction)를 활용할 수 있으며, [Table Storage](/azure/storage/tables/table-storage-overview)는 대규모 반구조적 데이터 세트를 사용한 신속한 개발을 위해 설계된 NoSQL 키-값 저장소입니다.

[Azure SQL Database](/azure/sql-database/)는 Microsoft SQL Server 엔진을 사용하여 친숙한 관계형 데이터베이스 기능을 서비스로 제공합니다.

[Cosmos DB](/azure/cosmos-db/)는 전 세계에 분산된 다중 모델 NoSQL 데이터베이스 서비스입니다. SQL API(이전의 DocumentDB), Cassandra, MongoDB 등을 포함하여 여러 API를 사용할 수 있습니다.

## Identity

[Azure Active Directory](/azure/active-directory/) 및 [Azure Active Directory B2C](/azure/active-directory-b2c/)는 모두 ID 서비스입니다. Azure Active Directory는 엔터프라이즈 시나리오용으로 설계되었으며 Azure AD B2B 협업을 지원하며 Azure Active Directory B2C는 소셜 네트워크 로그인을 비롯한 B2C 시나리오에 적합합니다.

## <a name="mobile"></a>휴대폰

[Notification Hubs](/azure/notification-hubs/)는 다양한 형식의 디바이스에서 실행되는 앱에 수백만 개의 메시지를 빠르게 보내기 위한 확장 가능한 다중 플랫폼 푸시 알림 엔진입니다.

## <a name="web-infrastructure"></a>웹 인프라

[Azure Container Service](/azure/aks/)는 호스트된 Kubernetes 환경을 관리하여 컨테이너 오케스트레이션에 관한 전문 지식 없이도 컨테이너화된 앱을 빠르고 쉽게 배포하고 관리할 수 있게 해줍니다.

[Azure Search](/azure/search/)는 프라이빗 이기종 콘텐츠에 대한 엔터프라이즈 검색 솔루션을 만드는 데 사용됩니다.

[Service Fabric](/azure/service-fabric/)은 확장 가능하고 안정적인 마이크로 서비스와 컨테이너를 손쉽게 패키지, 배포, 관리할 수 있게 해주는 분산 시스템 플랫폼입니다.
