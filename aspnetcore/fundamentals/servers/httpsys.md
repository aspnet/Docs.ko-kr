---
title: ASP.NET Core에서 HTTP.sys 웹 서버 구현
author: rick-anderson
description: Windows의 ASP.NET Core에 대한 웹 서버인 HTTP.sys에 대해 알아봅니다. HTTP.sys 커널 모드 드라이버를 기반으로 한 HTTP.sys는 IIS 없이 인터넷에 대한 직접 연결에 사용될 수 있는 Kestrel에 대한 대안입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 9c65abd5a055bb677a14921296316e7e03760bc2
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855367"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="214cf-104">ASP.NET Core에서 HTTP.sys 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="214cf-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="214cf-105">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="214cf-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="214cf-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="214cf-107">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="214cf-108">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-109">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="214cf-110">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="214cf-111">포트 공유</span><span class="sxs-lookup"><span data-stu-id="214cf-111">Port sharing</span></span>
* <span data-ttu-id="214cf-112">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="214cf-112">HTTPS with SNI</span></span>
* <span data-ttu-id="214cf-113">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="214cf-114">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="214cf-114">Direct file transmission</span></span>
* <span data-ttu-id="214cf-115">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="214cf-115">Response caching</span></span>
* <span data-ttu-id="214cf-116">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="214cf-117">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="214cf-117">Supported Windows versions:</span></span>

* <span data-ttu-id="214cf-118">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-118">Windows 7 or later</span></span>
* <span data-ttu-id="214cf-119">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="214cf-120">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="214cf-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="214cf-121">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-121">When to use HTTP.sys</span></span>

<span data-ttu-id="214cf-122">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="214cf-123">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="214cf-125">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="214cf-127">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="214cf-128">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="214cf-129">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="214cf-129">HTTP/2 support</span></span>

<span data-ttu-id="214cf-130">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="214cf-131">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="214cf-132">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="214cf-133">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="214cf-134">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="214cf-135">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="214cf-136">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="214cf-137">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="214cf-138">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="214cf-139">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="214cf-140">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="214cf-141">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="214cf-142">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="214cf-143">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="214cf-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="214cf-144">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="214cf-145">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="214cf-146">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="214cf-147">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="214cf-148">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="214cf-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="214cf-149">속성</span><span class="sxs-lookup"><span data-stu-id="214cf-149">Property</span></span> | <span data-ttu-id="214cf-150">설명</span><span class="sxs-lookup"><span data-stu-id="214cf-150">Description</span></span> | <span data-ttu-id="214cf-151">기본값</span><span class="sxs-lookup"><span data-stu-id="214cf-151">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="214cf-152">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-152">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="214cf-153">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="214cf-153">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="214cf-154">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-154">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="214cf-155">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="214cf-155">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="214cf-156">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-156">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="214cf-157">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-157">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="214cf-158">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-158">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="214cf-159">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-159">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="214cf-160">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-160">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="214cf-161">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-161">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="214cf-162">제한 조건으로 인해 요청을 거부할 때 HTTP.sys 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-162">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="214cf-163">Http503VerbosityLevel.<br>Basic</span><span class="sxs-lookup"><span data-stu-id="214cf-163">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="214cf-164">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="214cf-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="214cf-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="214cf-166">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="214cf-167">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-167">Use `-1` for infinite.</span></span> <span data-ttu-id="214cf-168">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="214cf-169">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="214cf-169">(machine-wide</span></span><br><span data-ttu-id="214cf-170">설정)</span><span class="sxs-lookup"><span data-stu-id="214cf-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="214cf-171"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="214cf-172">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="214cf-172">30000000 bytes</span></span><br><span data-ttu-id="214cf-173">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="214cf-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="214cf-174">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="214cf-175">1000</span><span class="sxs-lookup"><span data-stu-id="214cf-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="214cf-176">이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="214cf-177">기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="214cf-178">HTTP.sys 요청 큐의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="214cf-179">`null`(익명 큐)</span><span class="sxs-lookup"><span data-stu-id="214cf-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="214cf-180">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="214cf-181">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="214cf-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="214cf-182">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="214cf-183">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="214cf-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="214cf-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="214cf-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="214cf-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="214cf-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="214cf-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="214cf-190"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="214cf-191">컬렉션에 접두사를 추가하는 데 사용되는 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-191">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="214cf-192">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="214cf-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="214cf-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="214cf-194">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="214cf-195">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="214cf-196">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="214cf-197">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="214cf-198">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="214cf-199">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="214cf-200">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="214cf-201">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-202">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="214cf-203">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="214cf-205">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-205">Configure Windows Server</span></span>

1. <span data-ttu-id="214cf-206">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="214cf-207">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-208">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="214cf-209">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-210">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="214cf-211">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="214cf-212">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="214cf-213">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="214cf-214">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="214cf-215">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="214cf-216">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="214cf-217">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="214cf-218">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-218">Install the required .NET Framework.</span></span> <span data-ttu-id="214cf-219">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="214cf-220">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="214cf-221">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="214cf-222">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="214cf-223">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="214cf-224">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="214cf-225">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="214cf-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="214cf-226">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="214cf-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="214cf-227">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="214cf-228">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="214cf-229">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="214cf-230">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="214cf-231">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="214cf-232">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="214cf-233">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="214cf-234">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="214cf-235">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="214cf-236">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="214cf-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="214cf-237">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="214cf-238">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="214cf-239">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="214cf-240">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="214cf-241">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="214cf-242">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="214cf-243">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="214cf-244">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-245">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="214cf-246">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="214cf-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="214cf-247">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="214cf-248">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="214cf-249">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="214cf-250">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="214cf-251">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="214cf-252">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="214cf-253">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="214cf-254">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-255">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="214cf-256">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="214cf-257">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="214cf-258">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="214cf-259">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="214cf-260">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-260">In Visual Studio:</span></span>
     * <span data-ttu-id="214cf-261">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="214cf-262">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="214cf-263">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="214cf-264">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="214cf-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="214cf-265">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-265">Open the app's project file.</span></span>
     * <span data-ttu-id="214cf-266">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="214cf-267">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="214cf-267">In the following example:</span></span>

   * <span data-ttu-id="214cf-268">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="214cf-269">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="214cf-270">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="214cf-271">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="214cf-272">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="214cf-273">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="214cf-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="214cf-274">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="214cf-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="214cf-275">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-275">Run the app.</span></span>

   <span data-ttu-id="214cf-276">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="214cf-277">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="214cf-278">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="214cf-279">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="214cf-280">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-280">A development certificate is used in this example.</span></span> <span data-ttu-id="214cf-281">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="214cf-283">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="214cf-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="214cf-284">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="214cf-285">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="214cf-286">gRPC를 지원하기 위한 고급 HTTP/2 기능</span><span class="sxs-lookup"><span data-stu-id="214cf-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="214cf-287">HTTP.sys의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="214cf-288">HTTP.SYS를 사용하여 gRPC를 실행하기 위한 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="214cf-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="214cf-289">Windows 10, OS 빌드 19041.508 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="214cf-290">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="214cf-291">트레일러</span><span class="sxs-lookup"><span data-stu-id="214cf-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="214cf-292">다시 설정</span><span class="sxs-lookup"><span data-stu-id="214cf-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="214cf-293">추가 자료</span><span class="sxs-lookup"><span data-stu-id="214cf-293">Additional resources</span></span>

* [<span data-ttu-id="214cf-294">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="214cf-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="214cf-295">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="214cf-295">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="214cf-296">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="214cf-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="214cf-297">호스트</span><span class="sxs-lookup"><span data-stu-id="214cf-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="214cf-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="214cf-299">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="214cf-300">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-301">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="214cf-302">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="214cf-303">포트 공유</span><span class="sxs-lookup"><span data-stu-id="214cf-303">Port sharing</span></span>
* <span data-ttu-id="214cf-304">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="214cf-304">HTTPS with SNI</span></span>
* <span data-ttu-id="214cf-305">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="214cf-306">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="214cf-306">Direct file transmission</span></span>
* <span data-ttu-id="214cf-307">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="214cf-307">Response caching</span></span>
* <span data-ttu-id="214cf-308">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="214cf-309">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="214cf-309">Supported Windows versions:</span></span>

* <span data-ttu-id="214cf-310">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-310">Windows 7 or later</span></span>
* <span data-ttu-id="214cf-311">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="214cf-312">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="214cf-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="214cf-313">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-313">When to use HTTP.sys</span></span>

<span data-ttu-id="214cf-314">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="214cf-315">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="214cf-317">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="214cf-319">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="214cf-320">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="214cf-321">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="214cf-321">HTTP/2 support</span></span>

<span data-ttu-id="214cf-322">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="214cf-323">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="214cf-324">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="214cf-325">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="214cf-326">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="214cf-327">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="214cf-328">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="214cf-329">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="214cf-330">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="214cf-331">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="214cf-332">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="214cf-333">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="214cf-334">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="214cf-335">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="214cf-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="214cf-336">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="214cf-337">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="214cf-338">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="214cf-339">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="214cf-340">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="214cf-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="214cf-341">속성</span><span class="sxs-lookup"><span data-stu-id="214cf-341">Property</span></span> | <span data-ttu-id="214cf-342">설명</span><span class="sxs-lookup"><span data-stu-id="214cf-342">Description</span></span> | <span data-ttu-id="214cf-343">기본값</span><span class="sxs-lookup"><span data-stu-id="214cf-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="214cf-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="214cf-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="214cf-345">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="214cf-346">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="214cf-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="214cf-347">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="214cf-348">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="214cf-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="214cf-349">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="214cf-350">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="214cf-351">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="214cf-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="214cf-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="214cf-353">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="214cf-354">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-354">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="214cf-355">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="214cf-356">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="214cf-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="214cf-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="214cf-358">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="214cf-359">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-359">Use `-1` for infinite.</span></span> <span data-ttu-id="214cf-360">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="214cf-361">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="214cf-361">(machine-wide</span></span><br><span data-ttu-id="214cf-362">설정)</span><span class="sxs-lookup"><span data-stu-id="214cf-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="214cf-363"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="214cf-364">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="214cf-364">30000000 bytes</span></span><br><span data-ttu-id="214cf-365">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="214cf-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="214cf-366">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="214cf-367">1000</span><span class="sxs-lookup"><span data-stu-id="214cf-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="214cf-368">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="214cf-369">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="214cf-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="214cf-370">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="214cf-371">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="214cf-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="214cf-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="214cf-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="214cf-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="214cf-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="214cf-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="214cf-378"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="214cf-379">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="214cf-380">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="214cf-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="214cf-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="214cf-382">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="214cf-383">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="214cf-384">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="214cf-385">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="214cf-386">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="214cf-387">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="214cf-388">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="214cf-389">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-390">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="214cf-391">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="214cf-393">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-393">Configure Windows Server</span></span>

1. <span data-ttu-id="214cf-394">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="214cf-395">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-396">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="214cf-397">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-398">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="214cf-399">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="214cf-400">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="214cf-401">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="214cf-402">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="214cf-403">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-403">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="214cf-404">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="214cf-405">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-405">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="214cf-406">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-406">Install the required .NET Framework.</span></span> <span data-ttu-id="214cf-407">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="214cf-408">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="214cf-409">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="214cf-410">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="214cf-411">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="214cf-412">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="214cf-413">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="214cf-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="214cf-414">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="214cf-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="214cf-415">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="214cf-416">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="214cf-417">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="214cf-418">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="214cf-419">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="214cf-420">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="214cf-421">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="214cf-422">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="214cf-423">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="214cf-424">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="214cf-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="214cf-425">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="214cf-426">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="214cf-427">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="214cf-428">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="214cf-429">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="214cf-430">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="214cf-431">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="214cf-432">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-433">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="214cf-434">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="214cf-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="214cf-435">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="214cf-436">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="214cf-437">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="214cf-438">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="214cf-439">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="214cf-440">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="214cf-441">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="214cf-442">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-443">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="214cf-444">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="214cf-445">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="214cf-446">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="214cf-447">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="214cf-448">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-448">In Visual Studio:</span></span>
     * <span data-ttu-id="214cf-449">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="214cf-450">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="214cf-451">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="214cf-452">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="214cf-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="214cf-453">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-453">Open the app's project file.</span></span>
     * <span data-ttu-id="214cf-454">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="214cf-455">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="214cf-455">In the following example:</span></span>

   * <span data-ttu-id="214cf-456">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="214cf-457">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="214cf-458">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="214cf-459">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="214cf-460">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-460">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="214cf-461">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="214cf-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="214cf-462">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="214cf-462">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="214cf-463">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-463">Run the app.</span></span>

   <span data-ttu-id="214cf-464">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="214cf-465">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="214cf-466">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="214cf-467">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="214cf-468">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-468">A development certificate is used in this example.</span></span> <span data-ttu-id="214cf-469">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="214cf-471">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="214cf-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="214cf-472">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="214cf-473">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="214cf-474">추가 자료</span><span class="sxs-lookup"><span data-stu-id="214cf-474">Additional resources</span></span>

* [<span data-ttu-id="214cf-475">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="214cf-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="214cf-476">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="214cf-476">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="214cf-477">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="214cf-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="214cf-478">호스트</span><span class="sxs-lookup"><span data-stu-id="214cf-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="214cf-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="214cf-480">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="214cf-481">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-482">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="214cf-483">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="214cf-484">포트 공유</span><span class="sxs-lookup"><span data-stu-id="214cf-484">Port sharing</span></span>
* <span data-ttu-id="214cf-485">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="214cf-485">HTTPS with SNI</span></span>
* <span data-ttu-id="214cf-486">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="214cf-487">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="214cf-487">Direct file transmission</span></span>
* <span data-ttu-id="214cf-488">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="214cf-488">Response caching</span></span>
* <span data-ttu-id="214cf-489">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="214cf-490">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="214cf-490">Supported Windows versions:</span></span>

* <span data-ttu-id="214cf-491">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-491">Windows 7 or later</span></span>
* <span data-ttu-id="214cf-492">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="214cf-493">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="214cf-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="214cf-494">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-494">When to use HTTP.sys</span></span>

<span data-ttu-id="214cf-495">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="214cf-496">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="214cf-498">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="214cf-500">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="214cf-501">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="214cf-502">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="214cf-502">HTTP/2 support</span></span>

<span data-ttu-id="214cf-503">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="214cf-504">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="214cf-505">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="214cf-506">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="214cf-507">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="214cf-508">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="214cf-509">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="214cf-510">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="214cf-511">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="214cf-512">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="214cf-513">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="214cf-514">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="214cf-515">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="214cf-516">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="214cf-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="214cf-517">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="214cf-518">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="214cf-519">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="214cf-520">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="214cf-521">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="214cf-522">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="214cf-523">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="214cf-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="214cf-524">속성</span><span class="sxs-lookup"><span data-stu-id="214cf-524">Property</span></span> | <span data-ttu-id="214cf-525">설명</span><span class="sxs-lookup"><span data-stu-id="214cf-525">Description</span></span> | <span data-ttu-id="214cf-526">기본값</span><span class="sxs-lookup"><span data-stu-id="214cf-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="214cf-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="214cf-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="214cf-528">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="214cf-529">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="214cf-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="214cf-530">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="214cf-531">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="214cf-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="214cf-532">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="214cf-533">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="214cf-534">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="214cf-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="214cf-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="214cf-536">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="214cf-537">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-537">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="214cf-538">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="214cf-539">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="214cf-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="214cf-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="214cf-541">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="214cf-542">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-542">Use `-1` for infinite.</span></span> <span data-ttu-id="214cf-543">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="214cf-544">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="214cf-544">(machine-wide</span></span><br><span data-ttu-id="214cf-545">설정)</span><span class="sxs-lookup"><span data-stu-id="214cf-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="214cf-546"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="214cf-547">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="214cf-547">30000000 bytes</span></span><br><span data-ttu-id="214cf-548">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="214cf-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="214cf-549">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="214cf-550">1000</span><span class="sxs-lookup"><span data-stu-id="214cf-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="214cf-551">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="214cf-552">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="214cf-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="214cf-553">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="214cf-554">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="214cf-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="214cf-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="214cf-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="214cf-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="214cf-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="214cf-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="214cf-561"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="214cf-562">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="214cf-563">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="214cf-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="214cf-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="214cf-565">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="214cf-566">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="214cf-567">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="214cf-568">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="214cf-569">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="214cf-570">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="214cf-571">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="214cf-572">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-573">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="214cf-574">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="214cf-576">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-576">Configure Windows Server</span></span>

1. <span data-ttu-id="214cf-577">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="214cf-578">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-579">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="214cf-580">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-581">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="214cf-582">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="214cf-583">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="214cf-584">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="214cf-585">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="214cf-586">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-586">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="214cf-587">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="214cf-588">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-588">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="214cf-589">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-589">Install the required .NET Framework.</span></span> <span data-ttu-id="214cf-590">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="214cf-591">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="214cf-592">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="214cf-593">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="214cf-594">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="214cf-595">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="214cf-596">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="214cf-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="214cf-597">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="214cf-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="214cf-598">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="214cf-599">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="214cf-600">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="214cf-601">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="214cf-602">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="214cf-603">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="214cf-604">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="214cf-605">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="214cf-606">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="214cf-607">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="214cf-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="214cf-608">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="214cf-609">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="214cf-610">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="214cf-611">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="214cf-612">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="214cf-613">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="214cf-614">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="214cf-615">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-616">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="214cf-617">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="214cf-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="214cf-618">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="214cf-619">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="214cf-620">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="214cf-621">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="214cf-622">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="214cf-623">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="214cf-624">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="214cf-625">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-626">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="214cf-627">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="214cf-628">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="214cf-629">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="214cf-630">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="214cf-631">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-631">In Visual Studio:</span></span>
     * <span data-ttu-id="214cf-632">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="214cf-633">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="214cf-634">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="214cf-635">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="214cf-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="214cf-636">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-636">Open the app's project file.</span></span>
     * <span data-ttu-id="214cf-637">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="214cf-638">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="214cf-638">In the following example:</span></span>

   * <span data-ttu-id="214cf-639">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="214cf-640">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="214cf-641">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="214cf-642">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="214cf-643">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-643">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="214cf-644">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="214cf-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="214cf-645">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="214cf-645">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="214cf-646">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-646">Run the app.</span></span>

   <span data-ttu-id="214cf-647">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="214cf-648">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="214cf-649">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="214cf-650">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="214cf-651">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-651">A development certificate is used in this example.</span></span> <span data-ttu-id="214cf-652">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="214cf-654">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="214cf-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="214cf-655">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="214cf-656">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="214cf-657">추가 자료</span><span class="sxs-lookup"><span data-stu-id="214cf-657">Additional resources</span></span>

* [<span data-ttu-id="214cf-658">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="214cf-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="214cf-659">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="214cf-659">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="214cf-660">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="214cf-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="214cf-661">호스트</span><span class="sxs-lookup"><span data-stu-id="214cf-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="214cf-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="214cf-663">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="214cf-664">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-665">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="214cf-666">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="214cf-667">포트 공유</span><span class="sxs-lookup"><span data-stu-id="214cf-667">Port sharing</span></span>
* <span data-ttu-id="214cf-668">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="214cf-668">HTTPS with SNI</span></span>
* <span data-ttu-id="214cf-669">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="214cf-670">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="214cf-670">Direct file transmission</span></span>
* <span data-ttu-id="214cf-671">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="214cf-671">Response caching</span></span>
* <span data-ttu-id="214cf-672">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="214cf-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="214cf-673">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="214cf-673">Supported Windows versions:</span></span>

* <span data-ttu-id="214cf-674">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-674">Windows 7 or later</span></span>
* <span data-ttu-id="214cf-675">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="214cf-676">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="214cf-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="214cf-677">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-677">When to use HTTP.sys</span></span>

<span data-ttu-id="214cf-678">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="214cf-679">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="214cf-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="214cf-681">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="214cf-683">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="214cf-684">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="214cf-685">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="214cf-685">HTTP/2 support</span></span>

<span data-ttu-id="214cf-686">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="214cf-687">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="214cf-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="214cf-688">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="214cf-689">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="214cf-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="214cf-690">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="214cf-691">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="214cf-692">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="214cf-693">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="214cf-694">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="214cf-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="214cf-695">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="214cf-696">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="214cf-697">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="214cf-698">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="214cf-699">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="214cf-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="214cf-700">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="214cf-701">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="214cf-702">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="214cf-703">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="214cf-704">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="214cf-705">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="214cf-706">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="214cf-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="214cf-707">속성</span><span class="sxs-lookup"><span data-stu-id="214cf-707">Property</span></span> | <span data-ttu-id="214cf-708">설명</span><span class="sxs-lookup"><span data-stu-id="214cf-708">Description</span></span> | <span data-ttu-id="214cf-709">기본값</span><span class="sxs-lookup"><span data-stu-id="214cf-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="214cf-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="214cf-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="214cf-711">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="214cf-712">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="214cf-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="214cf-713">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="214cf-714">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="214cf-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="214cf-715">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="214cf-716">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="214cf-717">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="214cf-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="214cf-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="214cf-719">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="214cf-720">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-720">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="214cf-721">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="214cf-722">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="214cf-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="214cf-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="214cf-724">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="214cf-725">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-725">Use `-1` for infinite.</span></span> <span data-ttu-id="214cf-726">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="214cf-727">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="214cf-727">(machine-wide</span></span><br><span data-ttu-id="214cf-728">설정)</span><span class="sxs-lookup"><span data-stu-id="214cf-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="214cf-729"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="214cf-730">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="214cf-730">30000000 bytes</span></span><br><span data-ttu-id="214cf-731">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="214cf-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="214cf-732">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="214cf-733">1000</span><span class="sxs-lookup"><span data-stu-id="214cf-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="214cf-734">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="214cf-735">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="214cf-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="214cf-736">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="214cf-737">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="214cf-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="214cf-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="214cf-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="214cf-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="214cf-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="214cf-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="214cf-744"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="214cf-745">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="214cf-746">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="214cf-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="214cf-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="214cf-748">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="214cf-749">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="214cf-750">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="214cf-751">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="214cf-752">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="214cf-753">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="214cf-754">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="214cf-755">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="214cf-756">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="214cf-757">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="214cf-759">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="214cf-759">Configure Windows Server</span></span>

1. <span data-ttu-id="214cf-760">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="214cf-761">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-762">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="214cf-763">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="214cf-764">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="214cf-765">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="214cf-766">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="214cf-767">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="214cf-768">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="214cf-769">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-769">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="214cf-770">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="214cf-771">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-771">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="214cf-772">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-772">Install the required .NET Framework.</span></span> <span data-ttu-id="214cf-773">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="214cf-774">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="214cf-775">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="214cf-776">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="214cf-777">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="214cf-778">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="214cf-779">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="214cf-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="214cf-780">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="214cf-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="214cf-781">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="214cf-782">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="214cf-783">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="214cf-784">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="214cf-785">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="214cf-786">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="214cf-787">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="214cf-788">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="214cf-789">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="214cf-790">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="214cf-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="214cf-791">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="214cf-792">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="214cf-793">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="214cf-794">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="214cf-795">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="214cf-796">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="214cf-797">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="214cf-798">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-799">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="214cf-800">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="214cf-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="214cf-801">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="214cf-802">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="214cf-803">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="214cf-804">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="214cf-805">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="214cf-806">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="214cf-807">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="214cf-808">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="214cf-809">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="214cf-810">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="214cf-811">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="214cf-812">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="214cf-813">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="214cf-814">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-814">In Visual Studio:</span></span>
     * <span data-ttu-id="214cf-815">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="214cf-816">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="214cf-817">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="214cf-818">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="214cf-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="214cf-819">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-819">Open the app's project file.</span></span>
     * <span data-ttu-id="214cf-820">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="214cf-821">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="214cf-821">In the following example:</span></span>

   * <span data-ttu-id="214cf-822">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="214cf-823">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="214cf-824">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="214cf-825">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="214cf-826">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-826">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="214cf-827">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="214cf-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="214cf-828">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="214cf-828">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="214cf-829">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-829">Run the app.</span></span>

   <span data-ttu-id="214cf-830">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="214cf-831">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="214cf-832">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="214cf-833">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="214cf-834">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-834">A development certificate is used in this example.</span></span> <span data-ttu-id="214cf-835">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="214cf-837">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="214cf-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="214cf-838">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="214cf-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="214cf-839">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="214cf-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="214cf-840">추가 자료</span><span class="sxs-lookup"><span data-stu-id="214cf-840">Additional resources</span></span>

* [<span data-ttu-id="214cf-841">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="214cf-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="214cf-842">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="214cf-842">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="214cf-843">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="214cf-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="214cf-844">호스트</span><span class="sxs-lookup"><span data-stu-id="214cf-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
