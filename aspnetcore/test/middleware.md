---
title: ASP.NET Core 미들웨어 테스트
author: tratcher
description: TestServer를 사용하여 ASP.NET Core 미들웨어를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051436"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="b9d5d-103">ASP.NET Core 미들웨어 테스트</span><span class="sxs-lookup"><span data-stu-id="b9d5d-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="b9d5d-104">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b9d5d-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="b9d5d-105">미들웨어는 <xref:Microsoft.AspNetCore.TestHost.TestServer>와 격리하여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="b9d5d-106">다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-106">It allows you to:</span></span>

* <span data-ttu-id="b9d5d-107">테스트해야 하는 구성 요소만 포함하는 앱 파이프라인을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="b9d5d-108">미들웨어 동작을 확인하는 사용자 지정 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="b9d5d-109">장점</span><span class="sxs-lookup"><span data-stu-id="b9d5d-109">Advantages:</span></span>

* <span data-ttu-id="b9d5d-110">요청은 네트워크를 통해 직렬화되는 것이 아니라 메모리 내에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="b9d5d-111">이렇게 하면 포트 관리 및 HTTPS 인증서와 같은 추가 문제가 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="b9d5d-112">미들웨어의 예외는 호출하는 테스트로 직접 다시 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="b9d5d-113">테스트에서 직접 <xref:Microsoft.AspNetCore.Http.HttpContext>와 같은 서버 데이터 구조를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="b9d5d-114">TestServer 설정</span><span class="sxs-lookup"><span data-stu-id="b9d5d-114">Set up the TestServer</span></span>

<span data-ttu-id="b9d5d-115">테스트 프로젝트에서 테스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-115">In the test project, create a test:</span></span>

* <span data-ttu-id="b9d5d-116"><xref:Microsoft.AspNetCore.TestHost.TestServer>를 사용하는 호스트를 빌드하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="b9d5d-117">미들웨어에서 사용하는 모든 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="b9d5d-118">[Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="b9d5d-119">테스트에 미들웨어를 사용하도록 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="b9d5d-120">HttpClient를 사용하여 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="b9d5d-120">Send requests with HttpClient</span></span>

<span data-ttu-id="b9d5d-121"><xref:System.Net.Http.HttpClient>를 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="b9d5d-122">결과를 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-122">Assert the result.</span></span> <span data-ttu-id="b9d5d-123">먼저 어설션을 원하는 결과의 반대로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="b9d5d-124">가양성 어설션을 사용한 초기 실행에서는 미들웨어가 제대로 수행되는 동안 테스트가 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="b9d5d-125">테스트를 실행하고 테스트가 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="b9d5d-126">다음 예제에서 미들웨어는 루트 엔드포인트가 요청될 때 404 상태 코드(‘찾을 수 없음’)를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-126">In the following example, the middleware should return a 404 status code ( *Not Found* ) when the root endpoint is requested.</span></span> <span data-ttu-id="b9d5d-127">`Assert.NotEqual( ... );`을 사용하여 실패해야 하는 첫 번째 테스트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="b9d5d-128">정상적인 작동 조건에서 미들웨어를 테스트하도록 어설션을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="b9d5d-129">최종 테스트는 `Assert.Equal( ... );`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="b9d5d-130">테스트를 다시 실행하여 통과하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="b9d5d-131">HttpContext를 사용하여 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="b9d5d-131">Send requests with HttpContext</span></span>

<span data-ttu-id="b9d5d-132">테스트 앱은 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)를 사용하여 요청을 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="b9d5d-133">다음 예제에서는 미들웨어가 `https://example.com/A/Path/?and=query`를 처리할 때 몇 가지 항목이 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="b9d5d-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>는 <xref:System.Net.Http.HttpClient> 추상화를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.HttpContext> 개체를 직접 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="b9d5d-135"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>를 사용하여 서버에서만 사용할 수 있는 구조를 조작합니다(예: [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 또는 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)).</span><span class="sxs-lookup"><span data-stu-id="b9d5d-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="b9d5d-136">‘404 - 찾을 수 없음’ 응답을 테스트한 이전 예제와 마찬가지로 이전 테스트에서 각 `Assert` 문의 반대를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="b9d5d-137">이 확인에서는 미들웨어가 정상적으로 작동할 때 테스트가 제대로 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="b9d5d-138">가양성 테스트가 작동하는지 확인한 후에는 테스트의 예상 조건 및 값에 관한 마지막 `Assert` 문을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="b9d5d-139">테스트를 다시 실행하여 테스트가 통과하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="b9d5d-140">TestServer 제한 사항</span><span class="sxs-lookup"><span data-stu-id="b9d5d-140">TestServer limitations</span></span>

<span data-ttu-id="b9d5d-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="b9d5d-141">TestServer:</span></span>

* <span data-ttu-id="b9d5d-142">서버 동작을 복제하여 미들웨어를 테스트하기 위해 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="b9d5d-143">모든 <xref:System.Net.Http.HttpClient> 동작을 복제하려 시도하지는 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-143">Does \* **not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="b9d5d-144">클라이언트에 서버에 대한 제어 및 서버에서 발생하는 상황에 대한 가시성을 최대한 제공하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="b9d5d-145">예를 들어 서버 상태를 직접 전달하기 위해 일반적으로 `HttpClient`에서 throw하지 않는 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="b9d5d-146">일부 전송 관련 헤더는 일반적으로 미들웨어와 관련되지 않으므로 기본적으로 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="b9d5d-147">자세한 내용은 다음 단원을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="b9d5d-148">Content-Length 및 Transfer-Encoding 헤더</span><span class="sxs-lookup"><span data-stu-id="b9d5d-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="b9d5d-149">TestServer는 [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) 또는 [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) 등의 전송 관련 요청 또는 응답 헤더를 설정하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-149">TestServer does \* **not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="b9d5d-150">애플리케이션은 클라이언트, 시나리오 및 프로토콜에 따라 사용량이 달라지므로 이러한 헤더는 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="b9d5d-151">특정 시나리오를 테스트하는 데 `Content-Length` 및 `Transfer-Encoding`이 필요한 경우 <xref:System.Net.Http.HttpRequestMessage> 또는 <xref:Microsoft.AspNetCore.Http.HttpContext>를 작성할 때 테스트에 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="b9d5d-152">자세한 내용은 다음 GitHub 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b9d5d-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="b9d5d-153">[dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="b9d5d-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="b9d5d-154">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="b9d5d-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="b9d5d-155">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="b9d5d-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
