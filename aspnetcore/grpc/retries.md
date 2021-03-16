---
title: gRPC 재시도를 통해 일시적인 오류 처리
author: jamesnk
description: .NET에서 재시도로 복원력과 내결함성이 있는 gRPC 호출을 수행하는 방법에 관해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/25/2021
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
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589791"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="77eb2-103">gRPC 재시도를 통해 일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="77eb2-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="77eb2-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="77eb2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="77eb2-105">gRPC 재시도는 gRPC 클라이언트가 실패한 호출을 자동으로 다시 시도하는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="77eb2-106">이 문서에서는 .NET에서 복원력과 내결함성이 있는 gRPC 앱을 만들기 위해 재시도 정책을 구성하는 방법에 관해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="77eb2-107">gRPC 다시 시도에는 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 버전 2.36.0-pre1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-pre1 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="77eb2-108">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="77eb2-108">Transient fault handling</span></span>

<span data-ttu-id="77eb2-109">gRPC 호출은 일시적인 오류로 인해 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="77eb2-110">일시적인 오류에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-110">Transient faults include:</span></span>

* <span data-ttu-id="77eb2-111">네트워크 연결이 일시적으로 끊김</span><span class="sxs-lookup"><span data-stu-id="77eb2-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="77eb2-112">서비스를 일시적으로 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="77eb2-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="77eb2-113">서버 로드로 인한 시간 초과</span><span class="sxs-lookup"><span data-stu-id="77eb2-113">Timeouts due to server load.</span></span>

<span data-ttu-id="77eb2-114">gRPC 호출이 중단되면 클라이언트는 오류에 대한 세부 정보와 함께 `RpcException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="77eb2-115">클라이언트 앱은 예외를 catch하고 오류를 처리하는 방법을 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-115">The client app must catch the exception and choose how to handle the error.</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

<span data-ttu-id="77eb2-116">앱 전체에서 다시 시도 논리를 중복하는 것은 장황한 일이며 오류를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="77eb2-117">다행히 .NET gRPC 클라이언트는 자동 다시 시도를 기본적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="77eb2-118">gRPC 재시도 정책 구성</span><span class="sxs-lookup"><span data-stu-id="77eb2-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="77eb2-119">gRPC 채널을 만들 때 재시도 정책이 한 번 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-119">A retry policy is configured once when a gRPC channel is created:</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

<span data-ttu-id="77eb2-120">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="77eb2-120">The preceding code:</span></span>

* <span data-ttu-id="77eb2-121">`MethodConfig`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="77eb2-122">다시 시도 정책은 메서드당 구성할 수 있으며 `Names` 속성을 사용하여 메서드를 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="77eb2-123">이 메서드는 `MethodName.Default`로 구성되므로 이 채널에서 호출하는 모든 gRPC 메서드에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="77eb2-124">재시도 정책을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-124">Configures a retry policy.</span></span> <span data-ttu-id="77eb2-125">이 정책은 상태 코드 `Unavailable`로 실패한 gRPC 호출을 자동으로 다시 시도하도록 클라이언트에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="77eb2-126">`GrpcChannelOptions.ServiceConfig`를 설정하여 만든 채널이 재시도 정책을 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="77eb2-127">채널로 만든 gRPC 클라이언트는 실패한 호출을 자동으로 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a><span data-ttu-id="77eb2-128">gRPC 다시 시도 옵션</span><span class="sxs-lookup"><span data-stu-id="77eb2-128">gRPC retry options</span></span>

<span data-ttu-id="77eb2-129">다음 표에서는 gRPC 다시 시도 정책을 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-129">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="77eb2-130">옵션</span><span class="sxs-lookup"><span data-stu-id="77eb2-130">Option</span></span> | <span data-ttu-id="77eb2-131">설명</span><span class="sxs-lookup"><span data-stu-id="77eb2-131">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="77eb2-132">원래 시도를 포함한 최대 호출 시도 횟수입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-132">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="77eb2-133">이 값은 `GrpcChannelOptions.MaxRetryAttempts`에 의해 제한되며 기본값은 5입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-133">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="77eb2-134">값은 필수이며 1보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-134">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="77eb2-135">다시 시도 사이의 초기 백오프 지연입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-135">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="77eb2-136">0과 현재 백오프 사이의 임의 지연은 언제 다음 다시 시도가 수행될지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-136">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="77eb2-137">각 시도 후에는 현재 백오프에 `BackoffMultiplier`를 곱합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-137">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="77eb2-138">값은 필수이며 0보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-138">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="77eb2-139">최대 백오프는 지수 백오프 증가에 대한 상한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-139">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="77eb2-140">값은 필수이며 0보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-140">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="77eb2-141">다시 시도할 때마다 백오프에 이 값을 곱하게 되며 승수가 1보다 크면 백오프가 기하급수적으로 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-141">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="77eb2-142">값은 필수이며 0보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-142">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="77eb2-143">상태 코드의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-143">A collection of status codes.</span></span> <span data-ttu-id="77eb2-144">일치 상태와 함께 실패하는 gRPC 호출을 자동으로 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-144">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="77eb2-145">상태 코드에 대한 자세한 내용은 [상태 코드 및 gRPC에서의 사용](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77eb2-145">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="77eb2-146">다시 시도 가능한 상태 코드가 하나 이상 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-146">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="77eb2-147">Hedging</span><span class="sxs-lookup"><span data-stu-id="77eb2-147">Hedging</span></span>

<span data-ttu-id="77eb2-148">Hedging은 대체 다시 시도 전략입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-148">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="77eb2-149">Hedging은 응답을 기다리지 않고 단일 gRPC 호출의 여러 복사본을 적극적으로 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-149">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="77eb2-150">Hedged gRPC 호출은 서버에서 여러 번 실행될 수 있으며 처음 성공한 결과가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-150">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="77eb2-151">Hedging은 부정적인 영향 없이 여러 번 실행해도 안전한 메서드에만 사용하도록 설정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-151">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="77eb2-152">Hedging은 다시 시도와 비교했을 때 장단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-152">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="77eb2-153">Hedging의 장점은 성공적인 결과를 더 빠르게 반환할 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-153">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="77eb2-154">여러 동시 gRPC 호출을 허용하고 첫 번째 성공적인 결과를 사용할 수 있을 때 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-154">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="77eb2-155">Hedging의 단점은 낭비가 될 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-155">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="77eb2-156">여러 번 호출할 수 있으며 모두 성공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-156">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="77eb2-157">첫 번째 결과만 사용되고 나머지는 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-157">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="77eb2-158">gRPC hedging 정책 구성</span><span class="sxs-lookup"><span data-stu-id="77eb2-158">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="77eb2-159">Hedging 정책은 재시도 정책처럼 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-159">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="77eb2-160">Hedging 정책은 재시도 정책과 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-160">Note that a hedging policy can't be combined with a retry policy.</span></span>

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a><span data-ttu-id="77eb2-161">gRPC hedging 옵션</span><span class="sxs-lookup"><span data-stu-id="77eb2-161">gRPC hedging options</span></span>

<span data-ttu-id="77eb2-162">다음 표에서는 gRPC hedging 정책을 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-162">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="77eb2-163">옵션</span><span class="sxs-lookup"><span data-stu-id="77eb2-163">Option</span></span> | <span data-ttu-id="77eb2-164">설명</span><span class="sxs-lookup"><span data-stu-id="77eb2-164">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="77eb2-165">Hedging 정책은 이 호출 수만큼 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-165">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="77eb2-166">`MaxAttempts`는 원래 시도를 포함한 모든 시도의 총횟수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-166">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="77eb2-167">이 값은 `GrpcChannelOptions.MaxRetryAttempts`에 의해 제한되며 기본값은 5입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-167">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="77eb2-168">값은 필수이며 2 이상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-168">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="77eb2-169">첫 번째 호출은 즉시 전송되지만 후속 hedging 호출은 이 값으로 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-169">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="77eb2-170">지연을 0 또는 `null`로 설정하면 모든 hedged 호출이 즉시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-170">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="77eb2-171">기본값은 0입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-171">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="77eb2-172">다른 hedge 호출이 성공할 수 있음을 나타내는 상태 코드의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-172">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="77eb2-173">서버에서 심각하지 않은 상태 코드를 반환하는 경우에는 hedged 호출이 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-173">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="77eb2-174">아니면 처리되지 않은 요청이 취소되고 오류가 앱으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="77eb2-174">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="77eb2-175">상태 코드에 대한 자세한 내용은 [상태 코드 및 gRPC에서의 사용](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77eb2-175">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="77eb2-176">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="77eb2-176">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="77eb2-177">다시 시도 일반 지침 - 클라우드 애플리케이션에 대한 모범 사례</span><span class="sxs-lookup"><span data-stu-id="77eb2-177">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
