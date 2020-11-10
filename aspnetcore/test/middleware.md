---
title: ASP.NET Core 미들웨어 테스트
author: tratcher
description: TestServer를 사용하여 ASP.NET Core 미들웨어를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051436"
---
# <a name="test-aspnet-core-middleware"></a>ASP.NET Core 미들웨어 테스트

작성자: [Chris Ross](https://github.com/Tratcher)

미들웨어는 <xref:Microsoft.AspNetCore.TestHost.TestServer>와 격리하여 테스트할 수 있습니다. 다음을 수행할 수 있습니다.

* 테스트해야 하는 구성 요소만 포함하는 앱 파이프라인을 인스턴스화합니다.
* 미들웨어 동작을 확인하는 사용자 지정 요청을 보냅니다.

장점

* 요청은 네트워크를 통해 직렬화되는 것이 아니라 메모리 내에 전송됩니다.
* 이렇게 하면 포트 관리 및 HTTPS 인증서와 같은 추가 문제가 발생하지 않습니다.
* 미들웨어의 예외는 호출하는 테스트로 직접 다시 이동할 수 있습니다.
* 테스트에서 직접 <xref:Microsoft.AspNetCore.Http.HttpContext>와 같은 서버 데이터 구조를 사용자 지정할 수 있습니다.

## <a name="set-up-the-testserver"></a>TestServer 설정

테스트 프로젝트에서 테스트를 만듭니다.

* <xref:Microsoft.AspNetCore.TestHost.TestServer>를 사용하는 호스트를 빌드하고 시작합니다.
* 미들웨어에서 사용하는 모든 필요한 서비스를 추가합니다.
* [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet 패키지를 프로젝트에 추가합니다.
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* 테스트에 미들웨어를 사용하도록 처리 파이프라인을 구성합니다.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>HttpClient를 사용하여 요청 보내기

<xref:System.Net.Http.HttpClient>를 사용하여 요청을 보냅니다.

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

결과를 어설션합니다. 먼저 어설션을 원하는 결과의 반대로 만듭니다. 가양성 어설션을 사용한 초기 실행에서는 미들웨어가 제대로 수행되는 동안 테스트가 실패하는지 확인합니다. 테스트를 실행하고 테스트가 실패하는지 확인합니다.

다음 예제에서 미들웨어는 루트 엔드포인트가 요청될 때 404 상태 코드(‘찾을 수 없음’)를 반환해야 합니다. `Assert.NotEqual( ... );`을 사용하여 실패해야 하는 첫 번째 테스트를 실행합니다.

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

정상적인 작동 조건에서 미들웨어를 테스트하도록 어설션을 변경합니다. 최종 테스트는 `Assert.Equal( ... );`을 사용합니다. 테스트를 다시 실행하여 통과하는지 확인합니다.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>HttpContext를 사용하여 요청 보내기

테스트 앱은 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)를 사용하여 요청을 보낼 수도 있습니다. 다음 예제에서는 미들웨어가 `https://example.com/A/Path/?and=query`를 처리할 때 몇 가지 항목이 확인됩니다.

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>는 <xref:System.Net.Http.HttpClient> 추상화를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.HttpContext> 개체를 직접 구성할 수 있습니다. <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>를 사용하여 서버에서만 사용할 수 있는 구조를 조작합니다(예: [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 또는 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)).

‘404 - 찾을 수 없음’ 응답을 테스트한 이전 예제와 마찬가지로 이전 테스트에서 각 `Assert` 문의 반대를 확인합니다. 이 확인에서는 미들웨어가 정상적으로 작동할 때 테스트가 제대로 실패하는지 확인합니다. 가양성 테스트가 작동하는지 확인한 후에는 테스트의 예상 조건 및 값에 관한 마지막 `Assert` 문을 설정합니다. 테스트를 다시 실행하여 테스트가 통과하는지 확인합니다.

## <a name="testserver-limitations"></a>TestServer 제한 사항

TestServer:

* 서버 동작을 복제하여 미들웨어를 테스트하기 위해 만들었습니다.
* 모든 <xref:System.Net.Http.HttpClient> 동작을 복제하려 시도하지는 **않습니다**.
클라이언트에 서버에 대한 제어 및 서버에서 발생하는 상황에 대한 가시성을 최대한 제공하려고 시도합니다. 예를 들어 서버 상태를 직접 전달하기 위해 일반적으로 `HttpClient`에서 throw하지 않는 예외를 throw할 수 있습니다.
* 일부 전송 관련 헤더는 일반적으로 미들웨어와 관련되지 않으므로 기본적으로 설정되지 않습니다. 자세한 내용은 다음 단원을 참조하세요.

### <a name="content-length-and-transfer-encoding-headers"></a>Content-Length 및 Transfer-Encoding 헤더

TestServer는 [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) 또는 [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) 등의 전송 관련 요청 또는 응답 헤더를 설정하지 **않습니다**. 애플리케이션은 클라이언트, 시나리오 및 프로토콜에 따라 사용량이 달라지므로 이러한 헤더는 사용하지 않아야 합니다. 특정 시나리오를 테스트하는 데 `Content-Length` 및 `Transfer-Encoding`이 필요한 경우 <xref:System.Net.Http.HttpRequestMessage> 또는 <xref:Microsoft.AspNetCore.Http.HttpContext>를 작성할 때 테스트에 지정할 수 있습니다. 자세한 내용은 다음 GitHub 문제를 참조하세요.

[dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore#18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore#13273](https://github.com/dotnet/aspnetcore/issues/13273)
