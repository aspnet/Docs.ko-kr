---
title: GRPC에서 JSON 웹 API 만들기
author: jamesnk
description: gRPC 서비스용 JSON HTTP API를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 78247a9b775ec8c9a3bc4a58209f09e5b714c07d
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217520"
---
# <a name="create-json-web-apis-from-grpc"></a>GRPC에서 JSON 웹 API 만들기

작성자: [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API는 커밋된 제품이 아니라 실험적 프로젝트입니다. 다음 작업을 수행하려고 합니다.
>
> * gRPC 서비스용 JSON 웹 API를 만드는 방법이 작동하는지 테스트합니다.
> * 이 방법이 .NET 개발자에게 유용한지에 관한 피드백을 받으세요.
>
> 개발자가 좋아하고 생산성을 높일 수 있는 제품을 빌드할 수 있도록 [피드백을 남겨](https://github.com/grpc/grpc-dotnet/issues/167) 주시기 바랍니다.

gRPC는 앱 간에 통신하는 최신 방법입니다. gRPC는 HTTP/2, 스트리밍, Protobuf 및 메시지 계약을 사용하여 고성능 실시간 서비스를 만듭니다.

gRPC의 한 가지 제한 사항은 일부 플랫폼에서 gRPC를 사용할 수 없다는 것입니다. 브라우저는 HTTP/2를 완전히 지원하지 않으므로 브라우저 앱으로 데이터를 가져오는 기본 방법으로 REST 및 JSON을 설정합니다. gRPC가 제공하는 혜택이 있지만 REST 및 JSON은 최신 앱에서 중요합니다. gRPC **및** JSON 웹 API를 빌드하면 앱 개발에 원치 않는 오버헤드가 추가됩니다.

이 문서에서는 gRPC 서비스를 사용하여 JSON 웹 API를 만드는 방법을 설명합니다.

## <a name="grpc-http-api"></a>gRPC HTTP API

gRPC HTTP API는 gRPC 서비스용 RESTful JSON API를 만드는 ASP.NET Core의 실험적 확장입니다. 구성된 후 gRPC HTTP API를 사용하면 앱에서 친숙한 HTTP 개념으로 gRPC 서비스를 호출할 수 있습니다.

HTTP 동사
* URL 매개 변수 바인딩
* JSON 요청/응답

서비스를 호출하는 데 gRPC를 계속 사용할 수 있습니다.

### <a name="usage"></a>사용

1. [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)에 패키지 참조를 추가합니다.
1. *Startup.cs* 의 서비스를 `AddGrpcHttpApi`에 등록합니다.
1. [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) 및 [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) 파일을 프로젝트에 추가합니다.
1. HTTP 바인딩 및 경로가 포함된 *.proto* 파일에서 gRPC 메서드에 주석을 답니다.

[!code-protobuf[](~/grpc/httpapi/greet.proto?highlight=3,9-11)]

이제 `SayHello` gRPC 메서드를 gRPC+Protobuf 및 HTTP API로 호출할 수 있습니다.

* 요청: `HTTP/1.1 GET /v1/greeter/world`
* 응답: `{ "message": "Hello world" }`

서버 로그는 gRPC 서비스에서 HTTP 호출을 실행한다는 것을 보여 줍니다. gRPC HTTP API는 들어오는 HTTP 요청을 gRPC 메시지에 매핑한 다음, 응답 메시지를 JSON으로 변환합니다.

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

이는 기본 예제입니다. 추가적인 사용자 지정 옵션은 [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)을 참조하세요.

### <a name="enable-swaggeropenapi-support"></a>Swagger/OpenAPI 지원 사용

Swagger(OpenAPI)는 REST API를 설명하는 언어 중립적 사양입니다. gRPC HTTP API는 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)과 통합되어 RESTful gRPC 서비스용 Swagger 엔드포인트를 생성할 수 있습니다. 그러면 Swagger 엔드포인트를 [Swagger UI](https://swagger.io/swagger-ui/) 및 기타 도구에 사용할 수 있습니다.

gRPC HTTP API로 Swagger를 사용하도록 설정하려면 다음을 수행합니다.

1. [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger)에 패키지 참조를 추가합니다.
2. *Startup.cs* 에서 Swashbuckle을 구성합니다. `AddGrpcSwagger` 메서드는 gRPC HTTP API 엔드포인트를 포함하도록 Swashbuckle을 구성합니다.

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

Swashbuckle이 RESTful gRPC 서비스를 위한 Swagger를 생성하고 있는지 확인하려면 앱을 시작하고 Swagger UI 페이지로 이동합니다.

![Swagger UI](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API 및 gRPC-Web 비교

gRPC HTTP API 및 gRPC-Web을 둘 다 사용하여 브라우저에서 gRPC 서비스를 호출할 수 있습니다. 그러나 각각 해당 작업을 수행하는 방법은 다릅니다.

* gRPC-Web을 사용하면 브라우저 앱은 gRPC-Web 클라이언트 및 Protobuf를 사용하여 브라우저에서 gRPC 서비스를 호출할 수 있습니다. gRPC-Web을 사용하려면 브라우저 앱이 gRPC 클라이언트를 생성해야 하며 gRPC-Web에는 작고 빠른 Protobuf 메시지를 보내는 이점이 있습니다.
* gRPC HTTP API를 사용하면 브라우저 앱은 JSON을 사용하는 RESTful API인 것처럼 gRPC 서비스를 호출할 수 있습니다. 브라우저 앱에서 gRPC 클라이언트를 생성하거나 gRPC에 관해 알고 있을 필요가 없습니다.

생성된 클라이언트는 gRPC HTTP API를 위해 만들어진 것이 아닙니다. 이전 `Greeter` 서비스는 브라우저 JavaScript API를 사용하여 호출할 수 있습니다.

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>실험적 상태

gRPC HTTP API는 실험입니다. 완료되지 않았으며 지원되지 않습니다. Microsoft는 이 기술과 함께, gRPC 및 JSON 서비스를 동시에 빠르게 만들기 위해 앱 개발자에게 제공하는 기능에 관심이 있습니다. gRPC HTTP API를 완료한다는 보장은 없습니다.

gRPC HTTP API에 관한 개발자의 관심을 측정하려고 합니다. gRPC HTTP API에 관심이 있는 경우 [피드백을 보내](https://github.com/grpc/grpc-dotnet/issues/167) 주세요.

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/)는 gRPC 서비스에서 RESTful JSON API를 만들 수 있는 또 다른 기술입니다. 동일한 *.proto* 주석을 사용하여 HTTP 개념을 gRPC 서비스에 매핑합니다.

grpc-gateway와 gRPC HTTP API의 가장 큰 차이점은 grpc-gateway는 코드 생성을 사용하여 역방향 프록시 서버를 만든다는 것입니다. 역방향 프록시는 RESTful 호출을 gRPC로 변환한 다음, gRPC 서비스로 보냅니다.

grpc-gateway 설치 및 사용에 대해서는 [grpc-gateway 추가 정보](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [google.api.HttpRule 문서](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
