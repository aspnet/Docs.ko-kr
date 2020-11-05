---
title: 브라우저 앱에서 gRPC 사용
author: jamesnk
description: gRPC-Web을 사용하여 브라우저 앱에서 호출할 수 있도록 gRPC 서비스를 ASP.NET Core에서 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058911"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="f9298-103">브라우저 앱에서 gRPC 사용</span><span class="sxs-lookup"><span data-stu-id="f9298-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="f9298-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f9298-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="f9298-105">[gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 프로토콜을 사용하여 브라우저 앱에서 기존 ASP.NET Core gRPC 서비스를 호출할 수 있도록 구성하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="f9298-106">gRPC-Web을 사용하여 브라우저 JavaScript 및 :::no-loc(Blazor)::: 앱에서 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-106">gRPC-Web allows browser JavaScript and :::no-loc(Blazor)::: apps to call gRPC services.</span></span> <span data-ttu-id="f9298-107">브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="f9298-108">HTTP/2 gRPC와 함께 gRPC-Web을 지원하도록 ASP.NET Core에서 호스트된 gRPC 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="f9298-109">기존 ASP.NET Core 앱에 gRPC 서비스를 추가하는 방법에 관한 지침은 [ASP.NET Core 앱에 gRPC 서비스 추가](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9298-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="f9298-110">gRPC 프로젝트를 만드는 방법에 관한 지침은 <xref:tutorials/grpc/grpc-start>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9298-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="f9298-111">ASP.NET Core의 gRPC-Web 및 Envoy</span><span class="sxs-lookup"><span data-stu-id="f9298-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="f9298-112">ASP.NET Core 앱에 gRPC-Web을 추가하는 방법은 두 가지입니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="f9298-113">ASP.NET Core에서 gRPC HTTP/2와 함께 gRPC-Web을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="f9298-114">이 옵션은 `Grpc.AspNetCore.Web` 패키지에서 제공하는 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="f9298-115">[Envoy 프록시의](https://www.envoyproxy.io/) gRPC-Web 지원을 사용하여 gRPC-Web을 gRPC HTTP/2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="f9298-116">그러면 변환된 호출이 ASP.NET Core 앱으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="f9298-117">각 접근 방식에는 장/단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="f9298-118">앱 환경에서 Envoy를 프록시로 이미 사용하고 있는 경우 Envoy를 사용하여 gRPC-Web 지원을 제공하는 것이 적합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="f9298-119">ASP.NET Core만 필요한 gRPC-Web을 위한 기본 솔루션의 경우 `Grpc.AspNetCore.Web`을 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="f9298-120">ASP.NET Core에서 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="f9298-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="f9298-121">HTTP/2 gRPC와 함께 gRPC-Web을 지원하도록 ASP.NET Core에서 호스트된 gRPC 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="f9298-122">gRPC-Web을 사용하기 위해 서비스를 변경할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="f9298-123">시작 구성만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="f9298-124">ASP.NET Core gRPC 서비스에서 gRPC-Web을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="f9298-125">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="f9298-126">*Startup.cs* 에 `UseGrpcWeb` 및 `EnableGrpcWeb`을 추가하여 gRPC-Web을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs* :</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="f9298-127">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f9298-127">The preceding code:</span></span>

* <span data-ttu-id="f9298-128">라우팅 이후, 엔드포인트 이전에 gRPC-Web 미들웨어인 `UseGrpcWeb`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="f9298-129">`EnableGrpcWeb`을 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 gRPC-Web을 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="f9298-130">또는 모든 서비스가 기본적으로 gRPC-Web을 지원하고 `EnableGrpcWeb`이 필요하지 않도록 gRPC-Web 미들웨어를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="f9298-131">미들웨어를 추가할 때 `new GrpcWebOptions { DefaultEnabled = true }`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="f9298-132">.NET Core 3.x에서 [Http.sys로 호스트](xref:fundamentals/servers/httpsys)될 때 gRPC-Web이 실패하는 알려진 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="f9298-133">gRPC-Web이 Http.sys에서 작동하도록 하는 해결 방법은 [여기](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="f9298-134">gRPC-Web 및 CORS</span><span class="sxs-lookup"><span data-stu-id="f9298-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="f9298-135">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="f9298-136">이 제한은 브라우저 앱에서 gRPC-Web 호출을 수행하는 데에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="f9298-137">예를 들어 `https://www.contoso.com`에서 제공하는 브라우저 앱은 `https://services.contoso.com`에서 호스트된 gRPC-Web 서비스를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="f9298-138">CORS(원본 간 리소스 공유)를 사용하여 이 제한을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="f9298-139">브라우저 앱에서 원본 간 gRPC-Web 호출을 수행할 수 있게 하려면 [ASP.NET Core에서 CORS](xref:security/cors)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="f9298-140">기본 제공 CORS 지원을 사용하고, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>를 통해 gRPC 특정 헤더를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="f9298-141">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f9298-141">The preceding code:</span></span>

* <span data-ttu-id="f9298-142">`AddCors`를 호출하여 CORS 서비스를 추가하고 gRPC 특정 헤더를 공개하는 CORS 정책을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="f9298-143">`UseCors`를 호출하여 라우팅 이후, 엔드포인트 이전에 CORS 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="f9298-144">`RequiresCors`를 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 CORS를 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="f9298-145">gRPC-Web 및 스트리밍</span><span class="sxs-lookup"><span data-stu-id="f9298-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="f9298-146">기존의 HTTP/2를 통한 gRPC는 모든 방향에서 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="f9298-147">gRPC-Web은 스트리밍을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="f9298-148">gRPC-Web 브라우저 클라이언트는 클라이언트 스트리밍 및 양방향 스트리밍 메서드 호출을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="f9298-149">Azure App Service 및 IIS에서 호스트되는 ASP.NET Core gRPC 서비스는 양방향 스트리밍을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="f9298-150">gRPC-Web을 사용하는 경우 단항 메서드 및 서버 스트리밍 메서드를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="f9298-151">브라우저에서 gRPC-Web 호출</span><span class="sxs-lookup"><span data-stu-id="f9298-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="f9298-152">브라우저 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="f9298-153">브라우저에서 gRPC-Web을 사용하여 gRPC 서비스를 호출하는 경우 다음과 같은 몇 가지 요구 사항과 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="f9298-154">서버가 gRPC-Web을 지원하도록 구성되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="f9298-155">클라이언트 스트리밍 및 양방향 스트리밍 호출이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="f9298-156">서버 스트리밍이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-156">Server streaming is supported.</span></span>
* <span data-ttu-id="f9298-157">다른 도메인에 있는 gRPC 서비스를 호출하려면 서버에서 [CORS](xref:security/cors)를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="f9298-158">JavaScript gRPC-Web 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f9298-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="f9298-159">JavaScript gRPC-Web 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="f9298-160">JavaScript에서 gRPC-Web을 사용하는 방법에 대한 자세한 내용은 [gRPC-Web을 사용하여 JavaScript 클라이언트 코드 작성](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9298-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="f9298-161">.NET gRPC 클라이언트를 사용하여 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="f9298-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="f9298-162">gRPC-Web 호출을 수행하도록 .NET gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="f9298-163">이 기능은 브라우저에서 호스트되고 JavaScript 코드와 동일한 HTTP 제한 사항이 있는 [:::no-loc(Blazor WebAssembly):::](xref:blazor/index#blazor-webassembly) 앱에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-163">This is useful for [:::no-loc(Blazor WebAssembly):::](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="f9298-164">.NET 클라이언트를 사용하여 gRPC-Web을 호출하는 것은 [HTTP/2 gRPC와 동일](xref:grpc/client)합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="f9298-165">채널을 만드는 방법만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="f9298-166">gRPC-Web을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="f9298-167">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="f9298-168">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 패키지의 참조가 2.29.0 이상인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="f9298-169">`GrpcWebHandler`를 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="f9298-170">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f9298-170">The preceding code:</span></span>

* <span data-ttu-id="f9298-171">gRPC-Web을 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="f9298-172">클라이언트를 만들고 채널을 사용하여 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="f9298-173">`GrpcWebHandler`에는 다음과 같은 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="f9298-174">**InnerHandler** : gRPC HTTP 요청을 수행하는 기본 <xref:System.Net.Http.HttpMessageHandler>입니다(예: `HttpClientHandler`).</span><span class="sxs-lookup"><span data-stu-id="f9298-174">**InnerHandler** : The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="f9298-175">**GrpcWebMode** : gRPC HTTP 요청 `Content-Type`이 `application/grpc-web` 또는 `application/grpc-web-text`인지를 지정하는 열거형 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-175">**GrpcWebMode** : An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="f9298-176">`GrpcWebMode.GrpcWeb`은 인코딩 없이 전송되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="f9298-177">기본값.</span><span class="sxs-lookup"><span data-stu-id="f9298-177">Default value.</span></span>
    * <span data-ttu-id="f9298-178">`GrpcWebMode.GrpcWebText`는 base64로 인코딩되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="f9298-179">브라우저에서 서버 스트리밍 호출을 수행하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="f9298-180">**HttpVersion** : 기본 gRPC HTTP 요청에 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)을 설정하는 데 사용되는 HTTP 프로토콜 `Version`입니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-180">**HttpVersion** : HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="f9298-181">gRPC-Web은 특정 버전이 필요하지 않으며, 지정하지 않을 경우 기본값을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f9298-182">생성된 gRPC 클라이언트에는 단항 메서드를 호출하기 위한 동기 및 비동기 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="f9298-183">예를 들어 `SayHello`는 동기이고, `SayHelloAsync`는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="f9298-184">:::no-loc(Blazor WebAssembly)::: 앱에서 동기 메서드를 호출하면 앱이 응답하지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-184">Calling a sync method in a :::no-loc(Blazor WebAssembly)::: app will cause the app to become unresponsive.</span></span> <span data-ttu-id="f9298-185">비동기 메서드는 항상 :::no-loc(Blazor WebAssembly):::에서 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-185">Async methods must always be used in :::no-loc(Blazor WebAssembly):::.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="f9298-186">gRPC-Web과 함께 gRPC 클라이언트 팩터리 사용</span><span class="sxs-lookup"><span data-stu-id="f9298-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="f9298-187">gRPC-Web 호환 .NET 클라이언트는 [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory)와 통합된 gRPC를 사용하여 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="f9298-188">클라이언트 팩터리와 함께 gRPC-Web을 사용하려면:</span><span class="sxs-lookup"><span data-stu-id="f9298-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="f9298-189">다음 패키지의 프로젝트 파일에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="f9298-190">Grpc.Net.Client.Web</span><span class="sxs-lookup"><span data-stu-id="f9298-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="f9298-191">Grpc.Net.ClientFactory</span><span class="sxs-lookup"><span data-stu-id="f9298-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="f9298-192">제네릭 `AddGrpcClient` 확장 메서드를 사용하여 DI(종속성 주입)에 gRPC 클라이언트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="f9298-193">:::no-loc(Blazor WebAssembly)::: 앱에서 서비스는 `Program.cs`의 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-193">In a :::no-loc(Blazor WebAssembly)::: app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="f9298-194"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 확장 메서드를 사용하여 `GrpcWebHandler`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9298-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="f9298-195">자세한 내용은 <xref:grpc/clientfactory>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9298-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9298-196">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9298-196">Additional resources</span></span>

* [<span data-ttu-id="f9298-197">웹 클라이언트용 gRPC GitHub 프로젝트</span><span class="sxs-lookup"><span data-stu-id="f9298-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
