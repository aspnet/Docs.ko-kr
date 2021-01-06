---
title: .NET용 gRPC 구성
author: jamesnk
description: .NET 앱용 gRPC를 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/23/2020
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
uid: grpc/configuration
ms.openlocfilehash: 617c042c628dc431391f39c2ecb2d2f9c9463fa5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417593"
---
# <a name="grpc-for-net-configuration"></a>.NET용 gRPC 구성

## <a name="configure-services-options"></a>서비스 옵션 구성

gRPC 서비스는 *Startup.cs* 에서 `AddGrpc`를 사용하여 구성됩니다. 다음 표에서는 gRPC 서비스를 구성하기 위한 옵션을 설명합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | 서버에서 보낼 수 있는 최대 메시지 크기(바이트)입니다. 구성된 최대 메시지 크기를 초과하는 메시지를 전송하려고 하면 예외가 발생합니다. `null`로 설정하면 메시지 크기의 제한이 없습니다. |
| MaxReceiveMessageSize | 4MB | 서버에서 받을 수 있는 최대 메시지 크기(바이트)입니다. 서버에서 이 한도를 초과하는 메시지를 수신하면 예외가 throw됩니다. 이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. `null`로 설정하면 메시지 크기의 제한이 없습니다. |
| EnableDetailedErrors | `false` | `true`인 경우 서비스 메서드에서 예외가 throw되면 자세한 예외 메시지가 클라이언트에 반환됩니다. 기본값은 `false`입니다. `EnableDetailedErrors`를 `true`로 설정하면 중요한 정보가 노출될 수 있습니다. |
| CompressionProviders | gzip | 메시지를 압축하고 압축을 푸는 데 사용되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성된 기본 공급자는 **gzip** 압축을 지원합니다. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | 서버에서 보낸 메시지를 압축하는 데 사용되는 압축 알고리즘입니다. 이 알고리즘은 `CompressionProviders`의 압축 공급자와 일치해야 합니다. 이 알고리즘이 응답을 압축하려면 클라이언트는 **grpc-accept-encoding** 헤더에 전송하여 알고리즘을 지원한다는 사실을 표시해야 합니다. |
| ResponseCompressionLevel | `null` | 서버에서 보낸 메시지를 압축하는 데 사용되는 압축 수준입니다. |
| 인터셉터 | 없음 | 각 gRPC 호출을 사용하여 실행되는 인터셉터의 컬렉션입니다. 인터셉터는 등록된 순서대로 실행됩니다. 전역적으로 구성된 인터셉터는 단일 서비스에 대해 구성된 인터셉터보다 먼저 실행됩니다. gRPC 인터셉터에 대한 자세한 내용은 [gRPC 인터셉터 및 미들웨어](xref:grpc/migration#grpc-interceptors-vs-middleware)를 참조하세요. |
| IgnoreUnknownServices | `false` | `true`인 경우 알 수 없는 서비스 및 메서드에 대한 호출이 **UNIMPLEMENTED** 상태를 반환하지 않고 요청이 ASP.NET Core에서 등록된 다음 미들웨어에 전달됩니다. |

`Startup.ConfigureServices`에서 `AddGrpc` 호출에 옵션 대리자를 제공하여 모든 서비스에 대해 옵션을 구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

단일 서비스에 대한 옵션은 `AddGrpc`에 제공된 전역 옵션을 재정의하며 `AddServiceOptions<TService>`를 사용하여 구성할 수 있습니다.

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>클라이언트 옵션 구성

gRPC 클라이언트 구성은 `GrpcChannelOptions`에 설정되어 있습니다. 다음 표에서는 gRPC 채널을 구성하기 위한 옵션을 설명합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| HttpHandler | 새 인스턴스 | gRPC를 호출하는 데 사용되는 `HttpMessageHandler`입니다. 사용자 지정 `HttpClientHandler`를 구성하거나 gRPC 호출을 위해 HTTP 파이프라인에 추가 처리기를 추가하도록 클라이언트를 설정할 수 있습니다. 지정된 `HttpMessageHandler`가 없으면 채널에 새 `HttpClientHandler` 인스턴스가 만들어집니다(자동으로 삭제됨). |
| HttpClient | `null` | gRPC를 호출하는 데 사용되는 `HttpClient`입니다. 이 설정은 `HttpHandler`의 대안입니다. |
| DisposeHttpClient | `false` | `true`로 설정되고 `HttpMessageHandler` 또는 `HttpClient`가 지정된 경우 `GrpcChannel`이 삭제될 때 `HttpHandler` 또는 `HttpClient`가 삭제됩니다. |
| LoggerFactory | `null` | 클라이언트에서 gRPC 호출에 대한 정보를 기록하는 데 사용되는 `LoggerFactory`입니다. `LoggerFactory` 인스턴스는 종속성 주입을 통해 확인되거나 `LoggerFactory.Create`를 사용하여 만들 수 있습니다. 로깅 구성 예제는 <xref:grpc/diagnostics#grpc-client-logging>을 참조하세요. |
| MaxSendMessageSize | `null` | 클라이언트에서 보낼 수 있는 최대 메시지 크기(바이트)입니다. 구성된 최대 메시지 크기를 초과하는 메시지를 전송하려고 하면 예외가 발생합니다. `null`로 설정하면 메시지 크기의 제한이 없습니다. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4MB | 클라이언트에서 받을 수 있는 최대 메시지 크기(바이트)입니다. 클라이언트에서 이 한도를 초과하는 메시지를 수신하면 예외가 throw됩니다. 이 값을 늘리면 클라이언트가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. `null`로 설정하면 메시지 크기의 제한이 없습니다. |
| 자격 증명 | `null` | `ChannelCredentials` 인스턴스입니다. 자격 증명은 gRPC 호출에 인증 메타데이터를 추가하는 데 사용됩니다. |
| CompressionProviders | gzip | 메시지를 압축하고 압축을 푸는 데 사용되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다. 구성된 기본 공급자는 **gzip** 압축을 지원합니다. |
| ThrowOperationCanceledOnCancellation | `false` | `true`로 설정된 경우 호출이 취소되었거나 기한이 지났으면 클라이언트가 <xref:System.OperationCanceledException>을 throw합니다. |

코드는 다음과 같습니다.

* 채널의 최대 송신 및 수신 메시지 크기를 설정합니다.
* 클라이언트를 만듭니다.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
