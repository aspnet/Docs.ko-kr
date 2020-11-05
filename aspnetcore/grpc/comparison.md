---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: gRPC와 HTTP API를 비교한 방법과 권장 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: 0fb50f07153f5f9953b667fe32062ad24b2bd66d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059951"
---
# <a name="compare-grpc-services-with-http-apis"></a>gRPC 서비스와 HTTP API 비교

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 [gRPC 서비스](https://grpc.io/docs/guides/)와 JSON을 사용한 HTTP API(ASP.NET Core [Web API](xref:web-api/index) 포함)를 비교하는 방법을 설명합니다. 앱에 대한 API를 제공하는 데 사용되는 기술은 중요한 선택 이며 gRPC는 HTTP API와 비교해서 고유한 이점을 제공합니다. 이 문서에서는 gRPC의 장점과 단점을 설명하 고 다른 기술에서 gRPC를 사용하는 시나리오를 권장합니다.

## <a name="high-level-comparison"></a>개괄적인 비교

다음 표에서는 gRPC와 JSON을 사용하는 HTTP API 간의 고급 기능 비교를 제공합니다.

| 기능          | gRPC                                               | JSON을 사용하는 HTTP API           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| 계약         | 필수( *.proto* )                                | 선택 사항(OpenAPI)            |
| 프로토콜         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf](#performance)(소형, 이진)           | JSON(대형, 사람이 읽을 수 있음)  |
| 규범 | [엄격한 사양](#strict-specification)      | 느슨함. 모든 HTTP가 유효합니다.     |
| 스트리밍        | [클라이언트, 서버, 양방향](#streaming)       | 클라이언트, 서버                |
| 브라우저 지원  | [아니요(gRPC-웹 필요)](#limited-browser-support) | 예                           |
| 보안         | 전송(TLS)                                    | 전송(TLS)               |
| 클라이언트 코드 생성 | [예](#code-generation)                      | OpenAPI + 타사 도구 |

## <a name="grpc-strengths"></a>gRPC 장점

### <a name="performance"></a>성능

gRPC 메시지는 효율적인 이진 메시지 형식인 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)를 사용하여 직렬화됩니다. Protobuf는 서버와 클라이언트에서 매우 빠르게 직렬화합니다. Protobuf serialization은 작은 메시지 페이로드를 발생시키며 이는 모바일 앱과 같은 제한된 대역폭 시나리오에서 중요합니다.

gRPC는 HTTP 1.x에 비해 상당한 성능 이점을 제공하는, HTTP의 주요 개정판인 HTTP/2용으로 설계되었습니다.

* 이진 프레이밍 및 압축. HTTP/2 프로토콜은 간단하며, 보내고 받을 때 모두 효율적입니다.
* 단일 TCP 연결보다 여러 HTTP/2 호출의 멀티플렉싱. 멀티플렉싱은 [HOL(Head of Line) 차단](https://en.wikipedia.org/wiki/Head-of-line_blocking)을 제거합니다.

HTTP/2는 gRPC에만 국한되지 않습니다. JSON을 사용한 HTTP API를 포함하여 다양한 요청 형식에서 HTTP/2를 사용하고 성능 개선을 활용할 수 있습니다.

### <a name="code-generation"></a>코드 생성

모든 gRPC 프레임워크는 코드 생성에 대한 최고 수준의 지원을 제공합니다. gRPC 개발에 대한 핵심 파일은 gRPC 서비스 및 메시지의 계약을 정의하는 [.proto file](https://developers.google.com/protocol-buffers/docs/proto3)입니다. 이 파일에서 gRPC 프레임워크는 서비스 기본 클래스, 메시지 및 전체 클라이언트를 코드 생성합니다.

서버와 클라이언트 간에 *proto* 파일을 공유하여 메시지와 클라이언트 코드를 종단 간에 생성할 수 있습니다. 클라이언트의 코드 생성은 클라이언트와 서버에서 메시지의 중복을 제거하고 강력한 형식의 클라이언트를 만듭니다. 클라이언트를 작성하지 않아도 되므로 많은 서비스를 갖춘 응용 프로그램의 개발 시간이 상당히 절감됩니다.

### <a name="strict-specification"></a>엄격한 사양

JSON을 사용하는 HTTP API에 대한 공식 사양은 없습니다. 개발자는 URL, HTTP 동사 및 응답 코드의 가장 좋은 형식을 논의합니다.

[gRPC 사양](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)은 gRPC 서비스가 따라야 하는 형식에 대한 지침입니다. gRPC는 플랫폼 및 구현에 상관없이 일치하므로 논쟁이 불필요하며 개발자 시간을 절약합니다.

### <a name="streaming"></a>스트리밍

HTTP/2는 수명이 긴 실시간 통신 스트림에 대한 기초를 제공합니다. gRPC는 HTTP/2를 통한 스트리밍을 위한 최고 수준의 지원을 제공합니다.

gRPC 서비스는 모든 스트리밍 조합을 지원합니다.

* 단항(스트리밍 없음)
* 서버-클라이언트 스트리밍
* 클라이언트-서버 스트리밍
* 양방향 스트리밍

### <a name="deadlinetimeouts-and-cancellation"></a>최종 기한/시간 초과 및 취소

gRPC는 클라이언트가 RPC가 완료될 때까지 대기하는 기간을 지정하도록 할 수 있습니다. [최종 기한](https://grpc.io/blog/deadlines)이 서버에 전송되고 서버에서 최종 기한을 초과하는 경우 수행할 작업을 결정할 수 있습니다. 예를 들어 서버에서 제한 시간에 진행 중인 gRPC/HTTP/데이터베이스 요청을 취소할 수 있습니다.

자식 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용 제한을 강제로 적용할 수 있습니다.

## <a name="grpc-recommended-scenarios"></a>gRPC 권장 시나리오

gRPC는 다음과 같은 시나리오에 적합합니다.

* **마이크로 서비스** : gRPC는 대기 시간이 짧고 처리량이 높은 통신을 위해 설계되었습니다. gRPC는 효율성이 중요한 경량 마이크로 서비스에 적합합니다.
* **지점 간 실시간 통신** : 양방향 스트리밍을 위한 뛰어난 지원 기능을 제공합니다. gRPC 서비스는 폴링을 사용하지 않고 실시간으로 메시지를 푸시할 수 있습니다.
* **Polyglot 환경** : gRPC 도구는 널리 사용되는 모든 개발 언어를 지원하며, 따라서 gRPC는 다중 언어 환경에 적합합니다.
* **네트워크 제한 환경** : gRPC 메시지는 경량 메시지 형식인 Protobuf를 사용하여 직렬화됩니다. gRPC 메시지는 항상 해당하는 JSON 메시지보다 작습니다.
* **IPC(프로세스 간 통신)** : Unix 도메인 소켓 및 명명된 파이프와 같은 IPC 전송은 gRPC에서 동일한 머신에 있는 앱 간에 통신하는 데 사용할 수 있습니다. 자세한 내용은 <xref:grpc/interprocess>를 참조하세요.

## <a name="grpc-weaknesses"></a>gRPC 약점

### <a name="limited-browser-support"></a>제한된 브라우저 지원

현재 브라우저에서 gRPC 서비스를 직접 호출하는 것은 불가능합니다. gRPC는 HTTP/2 기능을 많이 사용하며, 브라우저에서 gRPC 클라이언트를 지원하기 위해 웹 요청에 필요한 제어 수준을 제공하지 않습니다. 예를 들어, 브라우저는 호출자가 HTTP/2를 사용하도록 요구하거나 기본 HTTP/2 프레임에 대한 액세스를 제공하지 않습니다.

다음과 같은 두 가지 일반적인 방법으로 gRPC를 브라우저 앱으로 가져올 수 있습니다.

* [gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html)은 브라우저에서 gRPC 지원을 제공하는 gRPC 팀의 추가 기술입니다. gRPC-Web을 사용하면 브라우저 앱에서 gRPC의 고성능과 낮은 네트워크 사용량을 활용할 수 있습니다. gRPC-웹에서 모든 gRPC 기능을 지원하지는 않습니다. 클라이언트 및 양방향 스트리밍이 지원되지 않으며 서버 스트리밍이 제한적으로 지원됩니다.

  .NET Core는 gRPC-Web을 지원합니다. 자세한 내용은 <xref:grpc/browser>를 참조하세요.

* RESTful JSON 웹 API는 [HTTP 메타데이터](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)로 *.proto* 파일에 주석을 달아 gRPC 서비스에서 자동으로 만들 수 있습니다. 따라서 앱은 gRPC와 JSON 웹 API 둘 다에 대해 별도의 서비스를 구축하는 노력을 들이지 않고도 둘 다를 지원할 수 있습니다.

  .NET Core는 gRPC 서비스에서 JSON 웹 API 만들기에 대한 실험적인 지원을 제공합니다. 자세한 내용은 <xref:grpc/httpapi>를 참조하세요.

### <a name="not-human-readable"></a>사람이 읽을 수 없음

HTTP API 요청은 텍스트로 전송되며, 사람이 읽고 만들 수 있습니다.

gRPC 메시지는 기본적으로 Protobuf로 인코딩됩니다. Protobuf는 송신 및 수신에 효율적이지만, 이진 형식은 사람이 읽을 수 없습니다. Protobuf를 사용하려면 올바른 역직렬화를 위해 *.proto* 파일에 지정된 메시지의 인터페이스 설명이 필요합니다. 네트워크에서 Protobuf 페이로드를 분석하고 요청을 직접 작성하려면 추가 도구가 필요합니다.

[서버 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 및 [gRPC 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)와 같은 기능은 이진 Protobuf 메시지를 지원하기 위해 존재합니다. 또한 Protobuf 메시지는 [JSON 변환](https://developers.google.com/protocol-buffers/docs/proto3#json)을 지원합니다. 기본 제공 JSON 변환은 디버그할 때 Protobuf 메시지를 사람이 읽을 수 있는 형식으로 변환하는 효율적인 방법을 제공합니다.

## <a name="alternative-framework-scenarios"></a>대체 프레임워크 시나리오

다음과 같은 시나리오에서는 gRPC보다 다른 프레임워크가 권장됩니다.

* **브라우저에서 액세스 가능한 API** : gRPC는 브라우저에서 완전히 지원되지는 않습니다. gRPC-웹은 브라우저 지원을 제공할 수 있지만 제한 사항이 있으며 서버 프록시를 도입합니다.
* **브로드캐스트 실시간 통신** : gRPC는 스트리밍을 통해 실시간 통신을 지원하지만 등록된 연결에 메시지를 브로드캐스트하는 개념이 없습니다. 예를 들어 대화방의 모든 클라이언트에 새 채팅 메시지를 보내야 하는 대화방 시나리오에서 각 gRPC 호출은 클라이언트에 새 채팅 메시지를 개별적으로 스트리밍하는 데 필요합니다. [SignalR](xref:signalr/introduction)은 이 시나리오에 유용한 프레임워크입니다. SignalR에는 영구 연결 개념과 메시지 브로드캐스트에 대한 기본 제공 지원이 있습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
