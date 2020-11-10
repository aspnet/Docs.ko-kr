---
title: GRPC에서 JSON 웹 API 만들기
author: jamesnk
description: gRPC 서비스용 JSON HTTP API를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 45e2a1a5e6a9f00294147db769454b78c5b866e5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059938"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="cfbb4-103">GRPC에서 JSON 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="cfbb4-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="cfbb4-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="cfbb4-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cfbb4-105">gRPC HTTP API는 커밋된 제품이 아니라 실험적 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="cfbb4-106">다음 작업을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-106">We want to:</span></span>
>
> * <span data-ttu-id="cfbb4-107">gRPC 서비스용 JSON 웹 API를 만드는 방법이 작동하는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="cfbb4-108">이 방법이 .NET 개발자에게 유용한지에 관한 피드백을 받으세요.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="cfbb4-109">개발자가 좋아하고 생산성을 높일 수 있는 제품을 빌드할 수 있도록 [피드백을 남겨](https://github.com/grpc/grpc-dotnet/issues/167) 주시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="cfbb4-110">gRPC는 앱 간에 통신하는 최신 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="cfbb4-111">gRPC는 HTTP/2, 스트리밍, Protobuf 및 메시지 계약을 사용하여 고성능 실시간 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="cfbb4-112">gRPC의 한 가지 제한 사항은 일부 플랫폼에서 gRPC를 사용할 수 없다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="cfbb4-113">브라우저는 HTTP/2를 완전히 지원하지 않으므로 브라우저 앱으로 데이터를 가져오는 기본 방법으로 REST 및 JSON을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="cfbb4-114">gRPC가 제공하는 혜택이 있지만 REST 및 JSON은 최신 앱에서 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="cfbb4-115">gRPC **및** JSON 웹 API를 빌드하면 앱 개발에 원치 않는 오버헤드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-115">Building gRPC \* **and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="cfbb4-116">이 문서에서는 gRPC 서비스를 사용하여 JSON 웹 API를 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="cfbb4-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="cfbb4-117">gRPC HTTP API</span></span>

<span data-ttu-id="cfbb4-118">gRPC HTTP API는 gRPC 서비스용 RESTful JSON API를 만드는 ASP.NET Core의 실험적 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="cfbb4-119">구성된 후 gRPC HTTP API를 사용하면 앱에서 친숙한 HTTP 개념으로 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="cfbb4-120">HTTP 동사</span><span class="sxs-lookup"><span data-stu-id="cfbb4-120">_ HTTP verbs</span></span>
* <span data-ttu-id="cfbb4-121">URL 매개 변수 바인딩</span><span class="sxs-lookup"><span data-stu-id="cfbb4-121">URL parameter binding</span></span>
* <span data-ttu-id="cfbb4-122">JSON 요청/응답</span><span class="sxs-lookup"><span data-stu-id="cfbb4-122">JSON requests/responses</span></span>

<span data-ttu-id="cfbb4-123">서비스를 호출하는 데 gRPC를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="cfbb4-124">사용</span><span class="sxs-lookup"><span data-stu-id="cfbb4-124">Usage</span></span>

1. <span data-ttu-id="cfbb4-125">[Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="cfbb4-126">*Startup.cs* 의 서비스를 `AddGrpcHttpApi`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="cfbb4-127">[google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) 및 [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) 파일을 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="cfbb4-128">HTTP 바인딩 및 경로가 포함된 *.proto* 파일에서 gRPC 메서드에 주석을 답니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="cfbb4-129">이제 `SayHello` gRPC 메서드를 gRPC+Protobuf 및 HTTP API로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="cfbb4-130">요청: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="cfbb4-131">응답: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="cfbb4-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="cfbb4-132">서버 로그는 gRPC 서비스에서 HTTP 호출을 실행한다는 것을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="cfbb4-133">gRPC HTTP API는 들어오는 HTTP 요청을 gRPC 메시지에 매핑한 다음, 응답 메시지를 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="cfbb4-134">이는 기본 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-134">This is a basic example.</span></span> <span data-ttu-id="cfbb4-135">추가적인 사용자 지정 옵션은 [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="cfbb4-136">gRPC HTTP API 및 gRPC-Web 비교</span><span class="sxs-lookup"><span data-stu-id="cfbb4-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="cfbb4-137">gRPC HTTP API 및 gRPC-Web을 둘 다 사용하여 브라우저에서 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="cfbb4-138">그러나 각각 해당 작업을 수행하는 방법은 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="cfbb4-139">gRPC-Web을 사용하면 브라우저 앱은 gRPC-Web 클라이언트 및 Protobuf를 사용하여 브라우저에서 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="cfbb4-140">gRPC-Web을 사용하려면 브라우저 앱이 gRPC 클라이언트를 생성해야 하며 gRPC-Web에는 작고 빠른 Protobuf 메시지를 보내는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="cfbb4-141">gRPC HTTP API를 사용하면 브라우저 앱은 JSON을 사용하는 RESTful API인 것처럼 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="cfbb4-142">브라우저 앱에서 gRPC 클라이언트를 생성하거나 gRPC에 관해 알고 있을 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="cfbb4-143">생성된 클라이언트는 gRPC HTTP API를 위해 만들어진 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="cfbb4-144">이전 `Greeter` 서비스는 브라우저 JavaScript API를 사용하여 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="cfbb4-145">실험적 상태</span><span class="sxs-lookup"><span data-stu-id="cfbb4-145">Experimental status</span></span>

<span data-ttu-id="cfbb4-146">gRPC HTTP API는 실험입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="cfbb4-147">완료되지 않았으며 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="cfbb4-148">Microsoft는 이 기술과 함께, gRPC 및 JSON 서비스를 동시에 빠르게 만들기 위해 앱 개발자에게 제공하는 기능에 관심이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="cfbb4-149">gRPC HTTP API를 완료한다는 보장은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="cfbb4-150">gRPC HTTP API에 관한 개발자의 관심을 측정하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="cfbb4-151">gRPC HTTP API에 관심이 있는 경우 [피드백을 보내](https://github.com/grpc/grpc-dotnet/issues/167) 주세요.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="cfbb4-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="cfbb4-152">grpc-gateway</span></span>

<span data-ttu-id="cfbb4-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/)는 gRPC 서비스에서 RESTful JSON API를 만들 수 있는 또 다른 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="cfbb4-154">동일한 *.proto* 주석을 사용하여 HTTP 개념을 gRPC 서비스에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="cfbb4-155">grpc-gateway와 gRPC HTTP API의 가장 큰 차이점은 grpc-gateway는 코드 생성을 사용하여 역방향 프록시 서버를 만든다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="cfbb4-156">역방향 프록시는 RESTful 호출을 gRPC로 변환한 다음, gRPC 서비스로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="cfbb4-157">grpc-gateway 설치 및 사용에 대해서는 [grpc-gateway 문서](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cfbb4-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfbb4-158">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cfbb4-158">Additional resources</span></span>

* [<span data-ttu-id="cfbb4-159">google.api.HttpRule 문서</span><span class="sxs-lookup"><span data-stu-id="cfbb4-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
