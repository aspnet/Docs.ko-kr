---
title: ASP.NET Core용 gRPC의 보안 고려 사항
author: jamesnk
description: ASP.NET Core용 gRPC의 보안 고려 사항을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058833"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core용 gRPC의 보안 고려 사항

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 .NET Core를 사용하여 gRPC를 보호하는 방법을 설명합니다.

## <a name="transport-security"></a>전송 보안

HTTP/2를 사용하여 gRPC 메시지를 보내고 받습니다. 다음이 권장됩니다.

* [TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)를 사용하여 프로덕션 gRPC 앱에서 메시지를 보호합니다.
* gRPC 서비스는 보안 포트에서만 수신 대기하고 응답해야 합니다.

Kestrel에서 TLS를 구성합니다. Kestrel 엔드포인트를 구성하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.

## <a name="exceptions"></a>예외

일반적으로 예외 메시지는 클라이언트에 공개되지 않아야 하는 중요한 데이터로 간주됩니다. 기본적으로 gRPC는 gRPC 서비스에서 throw된 예외의 세부 정보를 클라이언트에 보내지 않습니다. 대신, 클라이언트는 오류가 발생했음을 나타내는 일반 메시지를 받게 됩니다. [EnableDetailedErrors](xref:grpc/configuration#configure-services-options)를 사용하여 예외 메시지를 클라이언트에 전달하는 옵션을 재정의할 수 있습니다(예: 개발 또는 테스트). 프로덕션 앱에서는 예외 메시지를 클라이언트에 공개하면 안 됩니다.

## <a name="message-size-limits"></a>메시지 크기 제한

gRPC 클라이언트와 서비스로 들어오는 메시지는 메모리에 로드됩니다. 메시지 크기 제한은 gRPC가 과도한 리소스를 사용하는 것을 방지하는 메커니즘입니다.

gRPC는 메시지당 크기 제한을 사용하여 들어오는 메시지와 보내는 메시지를 관리합니다. 기본적으로 gRPC는 들어오는 메시지를 4MB로 제한합니다. 보내는 메시지에 대한 제한은 없습니다.

서버에서 `AddGrpc`를 사용하여 앱의 모든 서비스에 대해 gRPC 메시지 제한을 구성할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

`AddServiceOptions<TService>`를 사용하여 개별 서비스에 대해 제한을 구성할 수도 있습니다. 메시지 크기 제한을 구성하는 방법에 대한 자세한 내용은 [gRPC 구성](xref:grpc/configuration)을 참조하세요.

## <a name="client-certificate-validation"></a>클라이언트 인증서 유효성 검사

처음에는 연결 시 [클라이언트 인증서](https://tools.ietf.org/html/rfc5246#section-7.4.4)의 유효성을 검사합니다. 기본적으로 Kestrel은 연결의 클라이언트 인증서에 대해 추가 유효성 검사를 수행하지 않습니다.

클라이언트 인증서로 보호되는 gRPC 서비스는 [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) 패키지를 사용하는 것이 좋습니다. ASP.NET Core 인증서 인증은 클라이언트 인증서에 대해 다음을 비롯한 추가 유효성 검사를 수행합니다.

* 인증서에 유효한 EKU(확장된 키 사용)가 있음
* 유효 기간이 만료되지 않음
* 인증서 해지 확인
