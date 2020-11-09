---
title: 'ASP.NET Core SignalR 소개'
author: bradygaster
description: 'ASP.NET Core SignalR 라이브러리가 앱에 실시간 기능 추가를 간소화 하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051475"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="ee7ce-103">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="ee7ce-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="ee7ce-104">SignalR란?</span><span class="sxs-lookup"><span data-stu-id="ee7ce-104">What is SignalR?</span></span>

<span data-ttu-id="ee7ce-105">ASP.NET Core SignalR 는 응용 프로그램에 실시간 웹 기능 추가를 간소화 하는 오픈 소스 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="ee7ce-106">실시간 웹 기능을 사용 하면 서버측 코드에서 클라이언트에 콘텐츠를 즉시 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="ee7ce-107">적합 한 후보 SignalR :</span><span class="sxs-lookup"><span data-stu-id="ee7ce-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="ee7ce-108">서버에서 자주 업데이트해야 하는 앱.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="ee7ce-109">예를 들어 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="ee7ce-110">대시보드 및 모니터링 앱.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="ee7ce-111">예를 들어 회사 대시보드, 즉석 판매 업데이트 또는 여행 경고가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="ee7ce-112">공동 작업 앱.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-112">Collaborative apps.</span></span> <span data-ttu-id="ee7ce-113">예를 들어 화이트보드 앱 및 팀 회의 소프트웨어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="ee7ce-114">알림이 필요한 앱.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-114">Apps that require notifications.</span></span> <span data-ttu-id="ee7ce-115">소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱에서 알림을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="ee7ce-116">SignalR 는 서버-클라이언트 [원격 프로시저 호출 (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 만들기 위한 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="ee7ce-117">Rpc는 서버 쪽 .NET Core 코드에서 클라이언트에 대 한 JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="ee7ce-118">SignalRASP.NET Core에 대 한 몇 가지 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="ee7ce-119">연결 관리를 자동으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="ee7ce-120">모든 연결 된 클라이언트에 동시에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="ee7ce-121">예를 들어 대화방입니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-121">For example, a chat room.</span></span>
* <span data-ttu-id="ee7ce-122">특정 클라이언트나 클라이언트 그룹으로 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="ee7ce-123">늘어난 트래픽을 처리 하도록 크기를 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="ee7ce-124">소스는 [ SignalR GitHub의 리포지토리에서](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="ee7ce-125">전송</span><span class="sxs-lookup"><span data-stu-id="ee7ce-125">Transports</span></span>

<span data-ttu-id="ee7ce-126">SignalR 에서는 실시간 통신을 처리 하는 다음과 같은 기술을 지원 합니다 (정상적인 대체 순서).</span><span class="sxs-lookup"><span data-stu-id="ee7ce-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="ee7ce-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee7ce-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="ee7ce-128">Server-Sent 이벤트</span><span class="sxs-lookup"><span data-stu-id="ee7ce-128">Server-Sent Events</span></span>
* <span data-ttu-id="ee7ce-129">긴 폴링</span><span class="sxs-lookup"><span data-stu-id="ee7ce-129">Long Polling</span></span>

<span data-ttu-id="ee7ce-130">SignalR 는 서버 및 클라이언트의 기능 내에서 가장 적합 한 전송 방법을 자동으로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="ee7ce-131">허브</span><span class="sxs-lookup"><span data-stu-id="ee7ce-131">Hubs</span></span>

<span data-ttu-id="ee7ce-132">SignalR*허브* 를 사용 하 여 클라이언트와 서버 간에 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="ee7ce-133">허브는 클라이언트와 서버가 서로 메서드를 호출할 수 있도록 하는 고급 파이프라인입니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="ee7ce-134">SignalR 클라이언트에서 서버에 대 한 메서드를 호출할 수 있도록 하 고 그 반대의 경우도 가능 하 게 하는 컴퓨터 경계 간 디스패치를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="ee7ce-135">강력한 형식의 매개 변수를 메서드에 전달 하 여 모델 바인딩을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="ee7ce-136">SignalR 에서는 두 가지 기본 제공 허브 프로토콜인 JSON 기반의 텍스트 프로토콜과 [MessagePack](https://msgpack.org/)기반 이진 프로토콜을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="ee7ce-137">MessagePack는 일반적으로 JSON과 비교 하 여 더 작은 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="ee7ce-138">이전 브라우저는 MessagePack 프로토콜 지원을 제공 하기 위해 [Xhr 수준 2](https://caniuse.com/#feat=xhr2) 를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="ee7ce-139">허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 포함 하는 메시지를 전송 하 여 클라이언트 쪽 코드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="ee7ce-140">메서드 매개 변수로 보낸 개체는 구성 된 프로토콜을 사용 하 여 deserialize 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="ee7ce-141">클라이언트는 클라이언트 쪽 코드의 메서드와 이름을 일치 시 키 려 고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="ee7ce-142">클라이언트에서 일치 하는 항목을 찾으면 메서드를 호출 하 여 deserialize 된 매개 변수 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee7ce-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee7ce-143">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ee7ce-143">Additional resources</span></span>

* [<span data-ttu-id="ee7ce-144">SignalRASP.NET Core에 대 한 시작</span><span class="sxs-lookup"><span data-stu-id="ee7ce-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ee7ce-145">지원 되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="ee7ce-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="ee7ce-146">허브</span><span class="sxs-lookup"><span data-stu-id="ee7ce-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ee7ce-147">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ee7ce-147">JavaScript client</span></span>](xref:signalr/javascript-client)
