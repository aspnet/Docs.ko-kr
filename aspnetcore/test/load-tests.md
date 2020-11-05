---
title: ASP.NET Core 부하/스트레스 테스트
author: Jeremy-Meng
description: ASP.NET Core 앱의 부하 테스트 및 스트레스 테스트를 위한 몇 가지 주요 도구와 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060718"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="14fb1-103">ASP.NET Core 부하/스트레스 테스트</span><span class="sxs-lookup"><span data-stu-id="14fb1-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="14fb1-104">부하 테스트와 스트레스 테스트는 웹앱의 성능과 확장성을 보장하는 데 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="14fb1-105">유사한 테스트를 공유하는 경우가 많지만, 두 테스트의 목표는 서로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="14fb1-106">**부하 테스트** : 앱에서 응답 목표를 충족하면서 특정 시나리오에서 지정된 사용자 부하를 처리할 수 있는지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-106">**Load tests** : Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="14fb1-107">정상 조건으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="14fb1-108">**스트레스 테스트** : 극단적인 조건에서 앱을 실행할 때의 앱 안정성을 테스트하며, 오랫동안 실행하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-108">**Stress tests** : Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="14fb1-109">이 테스트는 앱에서 부하를 급하게 또는 점진적으로 늘려 높은 사용자 부하를 적용하거나, 앱의 컴퓨팅 리소스를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="14fb1-110">스트레스 테스트는 스트레스 상태의 앱이 오류를 복구하고 정상적으로 예상 동작으로 돌아올 수 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="14fb1-111">스트레스 상태에서는 정상 조건으로 앱을 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="14fb1-112">Visual Studio 2019는 [부하 테스트 사용 중단](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) 계획을 발표했습니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="14fb1-113">해당하는 Azure DevOps 클라우드 기반 부하 테스트 서비스가 중지되었습니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="14fb1-114">타사 도구</span><span class="sxs-lookup"><span data-stu-id="14fb1-114">Third-party tools</span></span>

<span data-ttu-id="14fb1-115">다음 목록에는 다양한 기능 집합을 포함하는 타사 웹 성능 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14fb1-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="14fb1-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="14fb1-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* <span data-ttu-id="14fb1-117">[ApacheBench](https://httpd.apache.org/docs/2.4/programs/ab.html)(ab)</span><span class="sxs-lookup"><span data-stu-id="14fb1-117">[ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)</span></span>
* [<span data-ttu-id="14fb1-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="14fb1-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="14fb1-119">k6</span><span class="sxs-lookup"><span data-stu-id="14fb1-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="14fb1-120">Locust</span><span class="sxs-lookup"><span data-stu-id="14fb1-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="14fb1-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="14fb1-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="14fb1-122">Netling</span><span class="sxs-lookup"><span data-stu-id="14fb1-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="14fb1-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="14fb1-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)