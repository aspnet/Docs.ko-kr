---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core로 gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/14/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 44a6f1d2a25314460fa4bce469f697a2fa4c0825
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252853"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="8809a-103">ASP.NET Core를 사용하는 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="8809a-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="8809a-104">이 문서에서는 ASP.NET Core를 사용하여 gRPC 서비스를 시작하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="8809a-105">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="8809a-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8809a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8809a-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8809a-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8809a-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8809a-108">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8809a-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="8809a-109">ASP.NET Core에서 gRPC 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="8809a-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="8809a-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8809a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8809a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8809a-112">gRPC 프로젝트를 만드는 방법에 대한 자세한 내용은 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8809a-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8809a-113">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8809a-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8809a-114">명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="8809a-115">ASP.NET Core 앱에 gRPC 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="8809a-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="8809a-116">gRPC를 사용하려면 [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="8809a-117">gRPC 구성</span><span class="sxs-lookup"><span data-stu-id="8809a-117">Configure gRPC</span></span>

<span data-ttu-id="8809a-118">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8809a-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="8809a-119">`AddGrpc` 메서드를 통해 gRPC를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="8809a-120">`MapGrpcService` 메서드를 통해 라우팅 파이프라인에 각 gRPC 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8809a-121">ASP.NET Core 미들웨어와 기능은 라우팅 파이프라인을 공유하므로 추가 요청 처리기를 제공하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="8809a-122">MVC 컨트롤러와 같은 추가 요청 처리기는 구성된 gRPC 서비스와 병렬로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="8809a-123">Kestrel 구성</span><span class="sxs-lookup"><span data-stu-id="8809a-123">Configure Kestrel</span></span>

<span data-ttu-id="8809a-124">Kestrel gRPC 엔드포인트:</span><span class="sxs-lookup"><span data-stu-id="8809a-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8809a-125">HTTP/2가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-125">Require HTTP/2.</span></span>
* <span data-ttu-id="8809a-126">[TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)로 보호해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="8809a-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8809a-127">HTTP/2</span></span>

<span data-ttu-id="8809a-128">gRPC에는 HTTP/2가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8809a-129">ASP.NET Core용 gRPC는 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)이 `HTTP/2`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8809a-130">Kestrel은 대부분의 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel/http2)합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="8809a-131">Kestrel 엔드포인트는 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="8809a-132">TLS</span><span class="sxs-lookup"><span data-stu-id="8809a-132">TLS</span></span>

<span data-ttu-id="8809a-133">gRPC에 사용되는 Kestrel 엔드포인트는 TLS로 보호해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8809a-134">개발에서는 ASP.NET Core 개발 인증서가 있을 경우 TLS로 보호된 엔드포인트가 `https://localhost:5001`에 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8809a-135">구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-135">No configuration is required.</span></span> <span data-ttu-id="8809a-136">`https` 접두사는 Kestrel 엔드포인트가 TLS를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8809a-137">프로덕션에서는 TLS를 명시적으로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8809a-138">다음 *appsettings.json* 예제에서는 TLS로 보호되는 HTTP/2 엔드포인트가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8809a-139">또는 *Program.cs* 에서 Kestrel 엔드포인트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="8809a-140">프로토콜 협상</span><span class="sxs-lookup"><span data-stu-id="8809a-140">Protocol negotiation</span></span>

<span data-ttu-id="8809a-141">TLS는 통신 보호 이상의 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8809a-142">TLS [ALPN](https://tools.ietf.org/html/rfc7301#section-3)(Application-Layer Protocol Negotiation) 핸드셰이크는 엔드포인트가 여러 프로토콜을 지원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8809a-143">이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용할 것인지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8809a-144">TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8809a-145">협상이 없기 때문에 여러 프로토콜이 있는 엔드포인트(예: `HttpProtocols.Http1AndHttp2`)는 TLS 없이 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8809a-146">안전하지 않은 엔드포인트에 대한 모든 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8809a-147">Kestrel을 통해 HTTP/2 및 TLS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8809a-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="8809a-148">macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8809a-149">macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8809a-150">자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8809a-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="8809a-151">Kestrel 구성</span><span class="sxs-lookup"><span data-stu-id="8809a-151">Configure Kestrel</span></span>

<span data-ttu-id="8809a-152">Kestrel gRPC 엔드포인트:</span><span class="sxs-lookup"><span data-stu-id="8809a-152">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8809a-153">HTTP/2가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-153">Require HTTP/2.</span></span>
* <span data-ttu-id="8809a-154">[TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)로 보호해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-154">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="8809a-155">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8809a-155">HTTP/2</span></span>

<span data-ttu-id="8809a-156">gRPC에는 HTTP/2가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-156">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8809a-157">ASP.NET Core용 gRPC는 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)이 `HTTP/2`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-157">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8809a-158">Kestrel은 대부분의 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel#http2-support)합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-158">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="8809a-159">Kestrel 엔드포인트는 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-159">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="8809a-160">TLS</span><span class="sxs-lookup"><span data-stu-id="8809a-160">TLS</span></span>

<span data-ttu-id="8809a-161">gRPC에 사용되는 Kestrel 엔드포인트는 TLS로 보호해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-161">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8809a-162">개발에서는 ASP.NET Core 개발 인증서가 있을 경우 TLS로 보호된 엔드포인트가 `https://localhost:5001`에 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-162">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8809a-163">구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-163">No configuration is required.</span></span> <span data-ttu-id="8809a-164">`https` 접두사는 Kestrel 엔드포인트가 TLS를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-164">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8809a-165">프로덕션에서는 TLS를 명시적으로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-165">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8809a-166">다음 *appsettings.json* 예제에서는 TLS로 보호되는 HTTP/2 엔드포인트가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-166">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8809a-167">또는 *Program.cs* 에서 Kestrel 엔드포인트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-167">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="8809a-168">프로토콜 협상</span><span class="sxs-lookup"><span data-stu-id="8809a-168">Protocol negotiation</span></span>

<span data-ttu-id="8809a-169">TLS는 통신 보호 이상의 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-169">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8809a-170">TLS [ALPN](https://tools.ietf.org/html/rfc7301#section-3)(Application-Layer Protocol Negotiation) 핸드셰이크는 엔드포인트가 여러 프로토콜을 지원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-170">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8809a-171">이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용할 것인지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-171">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8809a-172">TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-172">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8809a-173">협상이 없기 때문에 여러 프로토콜이 있는 엔드포인트(예: `HttpProtocols.Http1AndHttp2`)는 TLS 없이 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-173">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8809a-174">안전하지 않은 엔드포인트에 대한 모든 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-174">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8809a-175">Kestrel을 통해 HTTP/2 및 TLS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8809a-175">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="8809a-176">macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-176">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8809a-177">macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-177">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8809a-178">자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8809a-178">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="8809a-179">ASP.NET Core API와 통합</span><span class="sxs-lookup"><span data-stu-id="8809a-179">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="8809a-180">gRPC 서비스는 [DI](xref:fundamentals/dependency-injection)(종속성 주입), [로깅](xref:fundamentals/logging/index) 등의 ASP.NET Core 기능에 대한 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-180">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8809a-181">예를 들어 서비스 구현에서 생성자를 통해 DI 컨테이너의 로거 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-181">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="8809a-182">기본적으로 gRPC 서비스 구현은 수명(Singleton, 범위 지정 또는 임시)을 사용하는 다른 DI 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-182">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="8809a-183">gRPC 메서드의 HttpContext 확인</span><span class="sxs-lookup"><span data-stu-id="8809a-183">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="8809a-184">gRPC API는 메서드, 호스트, 헤더, 트레일러 등의 일부 HTTP/2 메시지 데이터에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-184">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="8809a-185">각 gRPC 메서드에 전달된 `ServerCallContext` 인수를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-185">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="8809a-186">`ServerCallContext`는 모든 ASP.NET API의 `HttpContext`에 대한 모든 권한을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-186">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="8809a-187">`GetHttpContext` 확장 메서드는 ASP.NET API의 기본 HTTP/2 메시지를 나타내는 `HttpContext`에 대한 모든 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8809a-187">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="8809a-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8809a-188">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
