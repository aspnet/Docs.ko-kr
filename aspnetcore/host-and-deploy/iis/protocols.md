---
title: IIS의 HTTP/2에서 ASP.NET Core 사용
author: rick-anderson
description: IIS에서 HTTP/2 기능을 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654353"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="05287-103">IIS의 HTTP/2에서 ASP.NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="05287-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="05287-104">작성자 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="05287-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="05287-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="05287-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="05287-106">Windows Server 2016 이상/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="05287-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="05287-107">IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="05287-107">IIS 10 or later</span></span>
* <span data-ttu-id="05287-108">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="05287-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="05287-109">[Out of Process를 호스트](xref:host-and-deploy/iis/index#out-of-process-hosting-model)하는 경우: 공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="05287-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="05287-110">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="05287-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="05287-111">Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="05287-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="05287-112">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05287-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="05287-113">HTTP/2는 HTTPS/TLS 연결에서 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="05287-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="05287-114">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="05287-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="05287-115">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="05287-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="05287-116">gRPC를 지원하기 위한 고급 HTTP/2 기능</span><span class="sxs-lookup"><span data-stu-id="05287-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="05287-117">IIS의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="05287-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="05287-118">IIS에서 gRPC를 실행하기 위한 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="05287-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="05287-119">In Process 호스팅.</span><span class="sxs-lookup"><span data-stu-id="05287-119">In-process hosting.</span></span>
* <span data-ttu-id="05287-120">Windows 10, OS 빌드 20300.1000 이상.</span><span class="sxs-lookup"><span data-stu-id="05287-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="05287-121">Windows 참가자 빌드를 사용해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05287-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="05287-122">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="05287-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="05287-123">트레일러</span><span class="sxs-lookup"><span data-stu-id="05287-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="05287-124">다시 설정</span><span class="sxs-lookup"><span data-stu-id="05287-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
