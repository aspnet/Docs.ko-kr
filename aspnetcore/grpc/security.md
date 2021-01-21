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
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252814"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="0ba12-103">ASP.NET Core용 gRPC의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="0ba12-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="0ba12-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0ba12-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0ba12-105">이 문서에서는 .NET Core를 사용하여 gRPC를 보호하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="0ba12-106">전송 보안</span><span class="sxs-lookup"><span data-stu-id="0ba12-106">Transport security</span></span>

<span data-ttu-id="0ba12-107">HTTP/2를 사용하여 gRPC 메시지를 보내고 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="0ba12-108">다음이 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-108">We recommend:</span></span>

* <span data-ttu-id="0ba12-109">[TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)를 사용하여 프로덕션 gRPC 앱에서 메시지를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="0ba12-110">gRPC 서비스는 보안 포트에서만 수신 대기하고 응답해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-110">gRPC services should only listen and respond over secured ports.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="0ba12-111">Kestrel에서 TLS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="0ba12-112">Kestrel 엔드포인트를 구성하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0ba12-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="0ba12-113">Kestrel에서 TLS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-113">TLS is configured in Kestrel.</span></span> <span data-ttu-id="0ba12-114">Kestrel 엔드포인트를 구성하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0ba12-114">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
::: moniker-end

## <a name="exceptions"></a><span data-ttu-id="0ba12-115">예외</span><span class="sxs-lookup"><span data-stu-id="0ba12-115">Exceptions</span></span>

<span data-ttu-id="0ba12-116">일반적으로 예외 메시지는 클라이언트에 공개되지 않아야 하는 중요한 데이터로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-116">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="0ba12-117">기본적으로 gRPC는 gRPC 서비스에서 throw된 예외의 세부 정보를 클라이언트에 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-117">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="0ba12-118">대신, 클라이언트는 오류가 발생했음을 나타내는 일반 메시지를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-118">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="0ba12-119">[EnableDetailedErrors](xref:grpc/configuration#configure-services-options)를 사용하여 예외 메시지를 클라이언트에 전달하는 옵션을 재정의할 수 있습니다(예: 개발 또는 테스트).</span><span class="sxs-lookup"><span data-stu-id="0ba12-119">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="0ba12-120">프로덕션 앱에서는 예외 메시지를 클라이언트에 공개하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-120">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="0ba12-121">메시지 크기 제한</span><span class="sxs-lookup"><span data-stu-id="0ba12-121">Message size limits</span></span>

<span data-ttu-id="0ba12-122">gRPC 클라이언트와 서비스로 들어오는 메시지는 메모리에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-122">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="0ba12-123">메시지 크기 제한은 gRPC가 과도한 리소스를 사용하는 것을 방지하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-123">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="0ba12-124">gRPC는 메시지당 크기 제한을 사용하여 들어오는 메시지와 보내는 메시지를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-124">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="0ba12-125">기본적으로 gRPC는 들어오는 메시지를 4MB로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-125">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="0ba12-126">보내는 메시지에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-126">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="0ba12-127">서버에서 `AddGrpc`를 사용하여 앱의 모든 서비스에 대해 gRPC 메시지 제한을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-127">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="0ba12-128">`AddServiceOptions<TService>`를 사용하여 개별 서비스에 대해 제한을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-128">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="0ba12-129">메시지 크기 제한을 구성하는 방법에 대한 자세한 내용은 [gRPC 구성](xref:grpc/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0ba12-129">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="0ba12-130">클라이언트 인증서 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="0ba12-130">Client certificate validation</span></span>

<span data-ttu-id="0ba12-131">처음에는 연결 시 [클라이언트 인증서](https://tools.ietf.org/html/rfc5246#section-7.4.4)의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-131">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="0ba12-132">기본적으로 Kestrel은 연결의 클라이언트 인증서에 대해 추가 유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-132">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="0ba12-133">클라이언트 인증서로 보호되는 gRPC 서비스는 [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) 패키지를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-133">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="0ba12-134">ASP.NET Core 인증서 인증은 클라이언트 인증서에 대해 다음을 비롯한 추가 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0ba12-134">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="0ba12-135">인증서에 유효한 EKU(확장된 키 사용)가 있음</span><span class="sxs-lookup"><span data-stu-id="0ba12-135">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="0ba12-136">유효 기간이 만료되지 않음</span><span class="sxs-lookup"><span data-stu-id="0ba12-136">Is within its validity period</span></span>
* <span data-ttu-id="0ba12-137">인증서 해지 확인</span><span class="sxs-lookup"><span data-stu-id="0ba12-137">Check certificate revocation</span></span>
