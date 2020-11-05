---
title: C#을 사용하는 gRPC 서비스
author: juntaoluo
description: C#으로 gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061017"
---
# <a name="grpc-services-with-c"></a>C\#을 사용하는 gRPC 서비스

이 문서에서는 C#으로 [gRPC](https://grpc.io/docs/guides/) 앱을 작성하는 데 필요한 개념을 간략하게 설명합니다. 여기에서 설명하는 내용은 [C-core](https://grpc.io/blog/grpc-stacks) 기반 gRPC 앱과 ASP.NET Core 기반 gRPC 앱에 모두 적용됩니다.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>proto 파일

gRPC는 API 개발에 계약 중심 접근 방식을 사용합니다. 기본적으로 프로토콜 버퍼(protobuf)가 IDL(Interface Design Language)로 사용됩니다. \* *.proto* 파일에는 다음이 포함됩니다.

* gRPC 서비스 정의
* 클라이언트와 서버 간에 전송된 메시지

protobuf 파일의 구문에 대한 자세한 내용은 <xref:grpc/protobuf>를 참조하세요.

예를 들어 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)에서 사용된 *greet.proto* 파일의 내용은 다음과 같습니다.

* `Greeter` 서비스를 정의합니다.
* `Greeter` 서비스가 `SayHello` 호출을 정의합니다.
* `SayHello`가 `HelloRequest` 메시지를 보내고 `HelloReply` 메시지를 받습니다.

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>C\# 앱에 .proto 파일 추가

\* *.proto* 파일을 `<Protobuf>` 항목 그룹에 추가하여 프로젝트에 포함합니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

기본적으로 `<Protobuf>` 참조는 구체적인 클라이언트 및 서비스 기본 클래스를 생성합니다. reference 요소의 `GrpcServices` 특성을 사용하여 C# 자산 생성을 제한할 수 있습니다. 유효한 `GrpcServices` 옵션은 다음과 같습니다.

* `Both`(없을 경우 기본값)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>.proto 파일에 대한 C# 도구 지원

*\*.proto* 파일에서 C# 자산을 생성하려면 도구 패키지 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)가 필요합니다. 생성된 자산(파일)과 관련해서 다음 사항을 확인합니다.

* 프로젝트를 빌드할 때마다 필요에 따라 생성됩니다.
* 프로젝트에 추가되거나 소스 제어에 체크 인되지 않습니다.
* *obj* 디렉터리에 포함된 빌드 아티팩트입니다.

이 패키지는 서버 프로젝트와 클라이언트 프로젝트에서 모두 필요합니다. `Grpc.AspNetCore` 메타패키지에는 `Grpc.Tools`에 대한 참조가 포함됩니다. 서버 프로젝트는 Visual Studio의 패키지 관리자를 사용하거나 프로젝트 파일에 `<PackageReference>`를 추가하여 `Grpc.AspNetCore`를 추가할 수 있습니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

클라이언트 프로젝트는 gRPC 클라이언트를 사용하는 데 필요한 다른 패키지와 함께 `Grpc.Tools`를 직접 참조해야 합니다. 도구 패키지는 런타임에 필요하지 않으므로 종속성이 `PrivateAssets="All"`로 표시됩니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>생성된 C# 자산

도구 패키지는 포함된 *\*.proto* 파일에서 정의된 메시지를 나타내는 C# 형식을 생성합니다.

서버 쪽 자산의 경우, 추상 서비스 기본 형식이 생성됩니다. 기본 형식은 *.proto* 파일에 포함된 모든 gRPC 호출의 정의를 포함합니다. 이 기본 형식에서 파생되고 gRPC 호출의 논리를 구현하는 구체적인 서비스 구현을 만듭니다. 앞에서 설명한 예제인 `greet.proto`의 경우, 가상 `SayHello` 메서드를 포함하는 추상 `GreeterBase` 형식이 생성됩니다. 구체적인 구현 `GreeterService`는 메서드를 재정의하고, gRPC 호출을 처리하는 논리를 구현합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

클라이언트 쪽 자산의 경우, 구체적인 클라이언트 형식이 생성됩니다. *.proto* 파일의 gRPC 호출은 호출할 수 있는 구체적인 형식의 메서드로 변환됩니다. 앞에서 설명한 예제인 `greet.proto`의 경우, 구체적인 `GreeterClient` 형식이 생성됩니다. `GreeterClient.SayHelloAsync`를 호출하여 서버에 대한 gRPC 호출을 시작합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

기본적으로 `<Protobuf>` 항목 그룹에 포함된 각 *\*.proto* 파일에 대해 서버 자산과 클라이언트 자산이 생성됩니다. 서버 프로젝트에서 서버 자산만 생성되도록 하려면 `GrpcServices` 특성을 `Server`로 설정합니다.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

마찬가지로, 클라이언트 프로젝트에서는 특성을 `Client`로 설정합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
