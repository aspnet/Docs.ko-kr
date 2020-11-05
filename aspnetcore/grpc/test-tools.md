---
title: ASP.NET Core에서 gRPCurl을 사용하여 gRPC 서비스 테스트
author: jamesnk
description: gRPC 도구를 사용하여 서비스를 테스트하는 방법을 알아봅니다. gRPCurl은 gRPC 서비스와 상호 작용하기 위한 명령줄 도구입니다. gRPCui는 대화형 웹 UI입니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058794"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>ASP.NET Core에서 gRPCurl을 사용하여 gRPC 서비스 테스트

작성자: [James Newton-King](https://twitter.com/jamesnk)

개발자가 클라이언트 앱을 빌드하지 않고도 서비스를 테스트할 수 있도록 하는 도구를 gRPC에 사용할 수 있습니다.

* [gRPCurl](https://github.com/fullstorydev/grpcurl)은 gRPC 서비스와의 상호 작용을 제공하는 명령줄 도구입니다.
* [gRPCui](https://github.com/fullstorydev/grpcui)는 gRPCurl을 기반으로 구축되고 gRPC용 대화형 웹 UI를 추가하며, Postman 및 Swagger UI 같은 도구와 유사합니다.

이 문서에서는 다음 작업의 수행 방법에 대해 설명합니다.

* gRPCurl 및 gRPCui를 다운로드하고 설치합니다.
* gRPC ASP.NET Core 앱을 사용하여 gRPC 리플렉션을 설정합니다.
* `grpcurl`를 사용하여 gRPC 서비스를 검색하고 테스트합니다.
* `grpcui`를 사용하여 브라우저를 통해 gRPC 서비스와 상호 작용합니다.

## <a name="about-grpcurl"></a>gRPCurl 정보

gRPCurl은 gRPC 커뮤니티에서 만든 명령줄 도구입니다. 해당 기능은 다음과 같습니다.

* 스트리밍 서비스를 포함하여 gRPC 서비스 호출.
* [gRPC 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)을 사용하여 서비스 검색.
* gRPC 서비스 나열 및 설명.
* 안전한(TLS) 서버 및 안전하지 않은(일반 텍스트) 서버에서 작동.

`grpcurl`다운로드 및 설치에 대한 자세한 내용은 [gRPCurl GitHub 홈페이지](https://github.com/fullstorydev/grpcurl#installation)를 참조하세요.

![gRPCurl 명령줄](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>gRPC 리플렉션 설정

`grpcurl`에서 서비스를 호출하려면 먼저 서비스의 Protobuf 계약을 알고 있어야 합니다. 이때 다음과 같은 두 가지 방법을 사용할 수 있습니다.

* 서버에서 [gRPC 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 을 설정합니다. gRPCurl은 서비스 계약을 자동으로 검색합니다.
* gRPCurl에 대한 명령줄 인수에 `.proto` 파일을 지정합니다.

gRPC 리플렉션에서는 gRPCurl을 사용하기가 더 쉽습니다. gRPC 리플렉션은 클라이언트가 서비스를 검색하기 위해 호출할 수 있는 앱에 새 gRPC 서비스를 추가합니다.

gRPC ASP.NET Core는 [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 패키지에서 gRPC 리플렉션을 기본적으로 지원합니다. 앱에서 리플렉션을 구성하려면 다음을 수행합니다.

* `Grpc.AspNetCore.Server.Reflection` 패키지 참조를 추가합니다.
* `Startup.cs`에서 리플렉션을 등록합니다.
  * `AddGrpcReflection` - 리플렉션을 사용하는 서비스를 등록합니다.
  * `MapGrpcReflectionService` - 리플렉션 서비스 엔드포인트를 추가합니다.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

gRPC 리플렉션이 설정된 경우:

* gRPC 리플렉션 서비스가 서버 앱에 추가됩니다.
* gRPC 리플렉션을 지원하는 클라이언트 앱에서 리플렉션 서비스를 호출하여 서버에서 호스트되는 서비스를 검색할 수 있습니다.
* gRPC 서비스는 클라이언트에서 계속 호출됩니다. 리플렉션에서는 서비스 검색만 사용할 수 있으며 서버 쪽 보안을 무시하지 않습니다. [인증 및 권한 부여](xref:grpc/authn-and-authz)으로 보호되는 엔드포인트는 호출자가 엔드포인트에 대한 자격 증명을 전달하여 성공적으로 호출되도록 해야 합니다.

## <a name="use-grpcurl"></a>`grpcurl` 사용

`-help` 인수는 `grpcurl` 명령줄 옵션을 설명합니다.

```console
$ grpcurl -help
```

### <a name="discover-services"></a>서비스 검색

`describe` 동사를 사용하여 서버에서 정의한 서비스를 확인합니다.

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

위의 예제는 다음과 같습니다.

* `localhost:5001` 서버에서 `describe` 동사를 실행합니다.
* gRPC 리플렉션에서 반환한 서비스 및 메서드를 출력합니다.
  * `Greeter`는 앱에 의해 구현된 서비스입니다.
  * `ServerReflection`은 `Grpc.AspNetCore.Server.Reflection` 패키지에 의해 추가된 서비스입니다.

`describe`를 서비스, 메서드 또는 메시지 이름과 결합하여 세부 정보를 확인합니다.

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>gRPC 서비스 호출

요청 메시지를 나타내는 JSON 인수와 함께 서비스 및 메서드 이름을 지정하여 gRPC 서비스를 호출합니다. JSON은 Protobuf로 변환되어 서비스로 전송됩니다.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

이전 예제에서는 다음을 수행합니다.

* `-d` 인수는 JSON으로 요청 메시지를 지정합니다. 이 인수는 서버 주소 및 메서드 이름 앞에 와야 합니다.
* `greeter.Greeter` 서비스에서 `SayHello` 메서드를 호출합니다.
* 응답 메시지를 JSON으로 출력합니다.

## <a name="about-grpcui"></a>gRPCui 정보

gRPCui는 gRPC의 대화형 웹 UI입니다. gRPCurl을 기반으로 구축되고 gRPC 서비스를 검색하고 테스트하기 위한 GUI를 제공하며, Postman이나 Swagger UI 같은 HTTP 도구와 유사합니다.

`grpcui` 다운로드 및 설치에 대한 자세한 내용은 [gRPCui GitHub 홈페이지](https://github.com/fullstorydev/grpcui#installation)를 참조하세요.

## <a name="using-grpcui"></a>`grpcui` 사용

상호 작용할 서버 주소에서 `grpcui`를 인수로 실행합니다.

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

이 도구는 대화형 웹 UI와 함께 브라우저 창을 시작합니다. gRPC 서비스는 gRPC 리플렉션을 사용하여 자동으로 검색됩니다.

![gRPCui 웹 UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>추가 리소스

* [gRPCurl GitHub 홈페이지](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub 홈페이지](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
