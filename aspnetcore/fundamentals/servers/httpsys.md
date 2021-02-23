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
ms.openlocfilehash: ad37f8434b6025c5f3ec97dc52987f5660a64edc
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106676"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="acace-104">ASP.NET Core에서 HTTP.sys 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="acace-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="acace-105">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="acace-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="acace-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="acace-107">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="acace-108">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="acace-109">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="acace-110">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="acace-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="acace-111">포트 공유</span><span class="sxs-lookup"><span data-stu-id="acace-111">Port sharing</span></span>
* <span data-ttu-id="acace-112">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="acace-112">HTTPS with SNI</span></span>
* <span data-ttu-id="acace-113">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="acace-114">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="acace-114">Direct file transmission</span></span>
* <span data-ttu-id="acace-115">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="acace-115">Response caching</span></span>
* <span data-ttu-id="acace-116">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="acace-117">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="acace-117">Supported Windows versions:</span></span>

* <span data-ttu-id="acace-118">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="acace-118">Windows 7 or later</span></span>
* <span data-ttu-id="acace-119">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="acace-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="acace-120">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acace-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="acace-121">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-121">When to use HTTP.sys</span></span>

<span data-ttu-id="acace-122">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="acace-123">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="acace-125">내부 배포에는 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-125">An internal deployment requires a feature not available in Kestrel.</span></span> <span data-ttu-id="acace-126">자세한 내용은 이 문서의 [Kestrel 및 HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span><span class="sxs-lookup"><span data-stu-id="acace-126">For more information, see [Kestrel vs. HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="acace-128">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-128">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="acace-129">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-129">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="acace-130">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="acace-130">HTTP/2 support</span></span>

<span data-ttu-id="acace-131">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-131">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acace-132">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="acace-132">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="acace-133">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="acace-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acace-134">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="acace-134">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acace-135">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acace-136">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="acace-137">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="acace-138">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="acace-139">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="acace-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="acace-140">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="acace-141">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="acace-142">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="acace-143">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="acace-144">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="acace-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="acace-145">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="acace-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="acace-146">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-146">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="acace-147">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-147">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="acace-148">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-148">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="acace-149">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="acace-149">**HTTP.sys options**</span></span>

| <span data-ttu-id="acace-150">속성</span><span class="sxs-lookup"><span data-stu-id="acace-150">Property</span></span> | <span data-ttu-id="acace-151">설명</span><span class="sxs-lookup"><span data-stu-id="acace-151">Description</span></span> | <span data-ttu-id="acace-152">기본값</span><span class="sxs-lookup"><span data-stu-id="acace-152">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="acace-153">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="acace-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="acace-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="acace-155">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="acace-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="acace-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="acace-157">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="acace-158">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="acace-159">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="acace-160">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-160">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="acace-161">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-161">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="acace-162">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-162">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="acace-163">제한 조건으로 인해 요청을 거부할 때 HTTP.sys 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-163">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="acace-164">Http503VerbosityLevel.<br>Basic</span><span class="sxs-lookup"><span data-stu-id="acace-164">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="acace-165">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-165">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="acace-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="acace-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="acace-167">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-167">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="acace-168">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-168">Use `-1` for infinite.</span></span> <span data-ttu-id="acace-169">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-169">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="acace-170">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="acace-170">(machine-wide</span></span><br><span data-ttu-id="acace-171">설정)</span><span class="sxs-lookup"><span data-stu-id="acace-171">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="acace-172"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="acace-173">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="acace-173">30000000 bytes</span></span><br><span data-ttu-id="acace-174">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="acace-174">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="acace-175">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="acace-176">1000</span><span class="sxs-lookup"><span data-stu-id="acace-176">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="acace-177">이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="acace-177">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="acace-178">기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-178">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="acace-179">HTTP.sys 요청 큐의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-179">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="acace-180">`null`(익명 큐)</span><span class="sxs-lookup"><span data-stu-id="acace-180">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="acace-181">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="acace-181">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="acace-182">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="acace-182">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="acace-183">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-183">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="acace-184">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="acace-184">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="acace-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="acace-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="acace-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="acace-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="acace-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="acace-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="acace-191"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-191">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="acace-192">컬렉션에 접두사를 추가하는 데 사용되는 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-192">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="acace-193">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-193">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="acace-194">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="acace-194">**MaxRequestBodySize**</span></span>

<span data-ttu-id="acace-195">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-195">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="acace-196">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-196">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="acace-197">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-197">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="acace-198">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-198">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acace-199">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-199">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="acace-200">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-200">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acace-201">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-201">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="acace-202">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-202">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="acace-203">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-203">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="acace-204">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-204">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="acace-206">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="acace-206">Configure Windows Server</span></span>

1. <span data-ttu-id="acace-207">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-207">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="acace-208">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-208">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-209">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-209">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="acace-210">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-210">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-211">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-211">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="acace-212">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="acace-212">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="acace-213">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-213">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="acace-214">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-214">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="acace-215">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-215">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="acace-216">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-216">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="acace-217">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-217">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="acace-218">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-218">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="acace-219">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-219">Install the required .NET Framework.</span></span> <span data-ttu-id="acace-220">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-220">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="acace-221">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-221">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="acace-222">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-222">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="acace-223">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-223">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="acace-224">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-224">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="acace-225">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-225">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="acace-226">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="acace-226">`urls` command-line argument</span></span>
   * <span data-ttu-id="acace-227">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="acace-227">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="acace-228">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="acace-228">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="acace-229">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-229">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="acace-230">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-230">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="acace-231">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-231">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="acace-232">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-232">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="acace-233">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-233">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="acace-234">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-234">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="acace-235">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-235">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="acace-236">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-236">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="acace-237">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="acace-237">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="acace-238">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-238">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="acace-239">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-239">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="acace-240">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-240">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="acace-241">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-241">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="acace-242">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-242">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="acace-243">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-243">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="acace-244">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-244">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="acace-245">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-245">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-246">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-246">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="acace-247">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="acace-247">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="acace-248">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-248">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="acace-249">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-249">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="acace-250">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-250">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="acace-251">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-251">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="acace-252">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-252">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="acace-253">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-253">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="acace-254">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-254">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="acace-255">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-255">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-256">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-256">Use a valid IP address.</span></span>
   * <span data-ttu-id="acace-257">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-257">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="acace-258">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-258">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="acace-259">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-259">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="acace-260">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-260">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="acace-261">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-261">In Visual Studio:</span></span>
     * <span data-ttu-id="acace-262">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-262">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="acace-263">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-263">Select the **Package** tab.</span></span>
     * <span data-ttu-id="acace-264">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-264">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="acace-265">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="acace-265">When not using Visual Studio:</span></span>
     * <span data-ttu-id="acace-266">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-266">Open the app's project file.</span></span>
     * <span data-ttu-id="acace-267">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-267">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="acace-268">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="acace-268">In the following example:</span></span>

   * <span data-ttu-id="acace-269">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-269">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="acace-270">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-270">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="acace-271">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-271">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="acace-272">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-272">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="acace-273">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-273">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="acace-274">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="acace-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="acace-275">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="acace-275">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="acace-276">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-276">Run the app.</span></span>

   <span data-ttu-id="acace-277">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-277">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="acace-278">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-278">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="acace-279">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-279">The app responds at the server's public IP address.</span></span> <span data-ttu-id="acace-280">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-280">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="acace-281">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-281">A development certificate is used in this example.</span></span> <span data-ttu-id="acace-282">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-282">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="acace-284">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="acace-284">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="acace-285">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-285">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="acace-286">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-286">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="acace-287">gRPC를 지원하기 위한 고급 HTTP/2 기능</span><span class="sxs-lookup"><span data-stu-id="acace-287">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="acace-288">HTTP.sys의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-288">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="acace-289">HTTP.sys를 사용하여 gRPC를 실행하기 위한 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="acace-289">Requirements to run gRPC with HTTP.sys:</span></span>

* <span data-ttu-id="acace-290">Windows 10, OS 빌드 19041.508 이상</span><span class="sxs-lookup"><span data-stu-id="acace-290">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="acace-291">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="acace-291">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="acace-292">트레일러</span><span class="sxs-lookup"><span data-stu-id="acace-292">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="acace-293">다시 설정</span><span class="sxs-lookup"><span data-stu-id="acace-293">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="acace-294">추가 자료</span><span class="sxs-lookup"><span data-stu-id="acace-294">Additional resources</span></span>

* [<span data-ttu-id="acace-295">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="acace-295">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="acace-296">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="acace-296">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="acace-297">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="acace-297">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="acace-298">호스트</span><span class="sxs-lookup"><span data-stu-id="acace-298">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="acace-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="acace-300">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-300">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="acace-301">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-301">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="acace-302">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-302">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="acace-303">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="acace-303">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="acace-304">포트 공유</span><span class="sxs-lookup"><span data-stu-id="acace-304">Port sharing</span></span>
* <span data-ttu-id="acace-305">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="acace-305">HTTPS with SNI</span></span>
* <span data-ttu-id="acace-306">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-306">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="acace-307">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="acace-307">Direct file transmission</span></span>
* <span data-ttu-id="acace-308">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="acace-308">Response caching</span></span>
* <span data-ttu-id="acace-309">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-309">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="acace-310">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="acace-310">Supported Windows versions:</span></span>

* <span data-ttu-id="acace-311">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="acace-311">Windows 7 or later</span></span>
* <span data-ttu-id="acace-312">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="acace-312">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="acace-313">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acace-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="acace-314">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-314">When to use HTTP.sys</span></span>

<span data-ttu-id="acace-315">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-315">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="acace-316">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-316">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="acace-318">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-318">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="acace-320">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-320">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="acace-321">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-321">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="acace-322">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="acace-322">HTTP/2 support</span></span>

<span data-ttu-id="acace-323">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-323">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acace-324">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="acace-324">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="acace-325">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="acace-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acace-326">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="acace-326">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acace-327">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-327">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acace-328">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-328">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="acace-329">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-329">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="acace-330">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-330">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="acace-331">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="acace-331">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="acace-332">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-332">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="acace-333">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-333">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="acace-334">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-334">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="acace-335">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-335">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="acace-336">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="acace-336">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="acace-337">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="acace-337">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="acace-338">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-338">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="acace-339">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-339">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="acace-340">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-340">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="acace-341">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="acace-341">**HTTP.sys options**</span></span>

| <span data-ttu-id="acace-342">속성</span><span class="sxs-lookup"><span data-stu-id="acace-342">Property</span></span> | <span data-ttu-id="acace-343">설명</span><span class="sxs-lookup"><span data-stu-id="acace-343">Description</span></span> | <span data-ttu-id="acace-344">기본값</span><span class="sxs-lookup"><span data-stu-id="acace-344">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="acace-345">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="acace-345">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="acace-346">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-346">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="acace-347">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="acace-347">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="acace-348">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-348">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="acace-349">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="acace-349">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="acace-350">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-350">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="acace-351">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-351">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="acace-352">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-352">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="acace-353">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="acace-353">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="acace-354">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-354">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="acace-355">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-355">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="acace-356">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-356">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="acace-357">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-357">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="acace-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="acace-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="acace-359">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-359">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="acace-360">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-360">Use `-1` for infinite.</span></span> <span data-ttu-id="acace-361">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-361">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="acace-362">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="acace-362">(machine-wide</span></span><br><span data-ttu-id="acace-363">설정)</span><span class="sxs-lookup"><span data-stu-id="acace-363">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="acace-364"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-364">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="acace-365">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="acace-365">30000000 bytes</span></span><br><span data-ttu-id="acace-366">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="acace-366">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="acace-367">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-367">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="acace-368">1000</span><span class="sxs-lookup"><span data-stu-id="acace-368">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="acace-369">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="acace-369">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="acace-370">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="acace-370">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="acace-371">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-371">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="acace-372">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="acace-372">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="acace-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="acace-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="acace-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="acace-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="acace-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="acace-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="acace-379"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-379">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="acace-380">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-380">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="acace-381">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-381">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="acace-382">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="acace-382">**MaxRequestBodySize**</span></span>

<span data-ttu-id="acace-383">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-383">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="acace-384">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-384">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="acace-385">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-385">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="acace-386">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-386">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acace-387">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-387">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="acace-388">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-388">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acace-389">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-389">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="acace-390">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-390">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="acace-391">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-391">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="acace-392">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-392">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="acace-394">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="acace-394">Configure Windows Server</span></span>

1. <span data-ttu-id="acace-395">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-395">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="acace-396">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-396">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-397">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-397">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="acace-398">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-398">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-399">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-399">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="acace-400">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="acace-400">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="acace-401">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-401">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="acace-402">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-402">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="acace-403">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-403">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="acace-404">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-404">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="acace-405">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-405">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="acace-406">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-406">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="acace-407">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-407">Install the required .NET Framework.</span></span> <span data-ttu-id="acace-408">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-408">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="acace-409">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-409">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="acace-410">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-410">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="acace-411">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-411">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="acace-412">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-412">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="acace-413">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-413">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="acace-414">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="acace-414">`urls` command-line argument</span></span>
   * <span data-ttu-id="acace-415">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="acace-415">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="acace-416">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="acace-416">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="acace-417">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-417">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="acace-418">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-418">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="acace-419">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-419">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="acace-420">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-420">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="acace-421">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-421">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="acace-422">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-422">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="acace-423">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-423">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="acace-424">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-424">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="acace-425">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="acace-425">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="acace-426">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-426">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="acace-427">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-427">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="acace-428">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-428">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="acace-429">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-429">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="acace-430">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-430">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="acace-431">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-431">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="acace-432">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-432">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="acace-433">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-433">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-434">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-434">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="acace-435">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="acace-435">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="acace-436">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-436">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="acace-437">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-437">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="acace-438">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-438">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="acace-439">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-439">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="acace-440">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-440">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="acace-441">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-441">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="acace-442">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-442">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="acace-443">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-443">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-444">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-444">Use a valid IP address.</span></span>
   * <span data-ttu-id="acace-445">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-445">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="acace-446">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-446">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="acace-447">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-447">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="acace-448">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-448">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="acace-449">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-449">In Visual Studio:</span></span>
     * <span data-ttu-id="acace-450">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-450">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="acace-451">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-451">Select the **Package** tab.</span></span>
     * <span data-ttu-id="acace-452">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-452">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="acace-453">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="acace-453">When not using Visual Studio:</span></span>
     * <span data-ttu-id="acace-454">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-454">Open the app's project file.</span></span>
     * <span data-ttu-id="acace-455">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-455">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="acace-456">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="acace-456">In the following example:</span></span>

   * <span data-ttu-id="acace-457">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-457">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="acace-458">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-458">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="acace-459">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-459">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="acace-460">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-460">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="acace-461">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-461">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="acace-462">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="acace-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="acace-463">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="acace-463">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="acace-464">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-464">Run the app.</span></span>

   <span data-ttu-id="acace-465">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-465">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="acace-466">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-466">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="acace-467">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-467">The app responds at the server's public IP address.</span></span> <span data-ttu-id="acace-468">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-468">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="acace-469">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-469">A development certificate is used in this example.</span></span> <span data-ttu-id="acace-470">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-470">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="acace-472">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="acace-472">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="acace-473">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-473">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="acace-474">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-474">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acace-475">추가 자료</span><span class="sxs-lookup"><span data-stu-id="acace-475">Additional resources</span></span>

* [<span data-ttu-id="acace-476">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="acace-476">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="acace-477">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="acace-477">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="acace-478">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="acace-478">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="acace-479">호스트</span><span class="sxs-lookup"><span data-stu-id="acace-479">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="acace-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="acace-481">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-481">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="acace-482">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-482">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="acace-483">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-483">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="acace-484">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="acace-484">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="acace-485">포트 공유</span><span class="sxs-lookup"><span data-stu-id="acace-485">Port sharing</span></span>
* <span data-ttu-id="acace-486">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="acace-486">HTTPS with SNI</span></span>
* <span data-ttu-id="acace-487">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-487">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="acace-488">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="acace-488">Direct file transmission</span></span>
* <span data-ttu-id="acace-489">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="acace-489">Response caching</span></span>
* <span data-ttu-id="acace-490">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-490">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="acace-491">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="acace-491">Supported Windows versions:</span></span>

* <span data-ttu-id="acace-492">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="acace-492">Windows 7 or later</span></span>
* <span data-ttu-id="acace-493">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="acace-493">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="acace-494">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acace-494">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="acace-495">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-495">When to use HTTP.sys</span></span>

<span data-ttu-id="acace-496">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-496">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="acace-497">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-497">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="acace-499">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-499">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="acace-501">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-501">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="acace-502">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-502">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="acace-503">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="acace-503">HTTP/2 support</span></span>

<span data-ttu-id="acace-504">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-504">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acace-505">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="acace-505">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="acace-506">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="acace-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acace-507">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="acace-507">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acace-508">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-508">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acace-509">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-509">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="acace-510">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-510">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="acace-511">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-511">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="acace-512">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="acace-512">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="acace-513">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-513">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="acace-514">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-514">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="acace-515">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-515">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="acace-516">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-516">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="acace-517">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="acace-517">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="acace-518">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="acace-518">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="acace-519">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-519">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="acace-520">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-520">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="acace-521">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-521">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="acace-522">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-522">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="acace-523">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-523">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="acace-524">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="acace-524">**HTTP.sys options**</span></span>

| <span data-ttu-id="acace-525">속성</span><span class="sxs-lookup"><span data-stu-id="acace-525">Property</span></span> | <span data-ttu-id="acace-526">설명</span><span class="sxs-lookup"><span data-stu-id="acace-526">Description</span></span> | <span data-ttu-id="acace-527">기본값</span><span class="sxs-lookup"><span data-stu-id="acace-527">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="acace-528">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="acace-528">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="acace-529">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-529">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="acace-530">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="acace-530">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="acace-531">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-531">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="acace-532">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="acace-532">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="acace-533">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-533">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="acace-534">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-534">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="acace-535">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-535">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="acace-536">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="acace-536">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="acace-537">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-537">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="acace-538">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-538">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="acace-539">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-539">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="acace-540">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-540">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="acace-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="acace-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="acace-542">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-542">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="acace-543">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-543">Use `-1` for infinite.</span></span> <span data-ttu-id="acace-544">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-544">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="acace-545">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="acace-545">(machine-wide</span></span><br><span data-ttu-id="acace-546">설정)</span><span class="sxs-lookup"><span data-stu-id="acace-546">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="acace-547"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-547">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="acace-548">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="acace-548">30000000 bytes</span></span><br><span data-ttu-id="acace-549">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="acace-549">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="acace-550">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-550">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="acace-551">1000</span><span class="sxs-lookup"><span data-stu-id="acace-551">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="acace-552">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="acace-552">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="acace-553">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="acace-553">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="acace-554">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-554">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="acace-555">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="acace-555">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="acace-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="acace-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="acace-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="acace-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="acace-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="acace-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="acace-562"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-562">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="acace-563">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-563">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="acace-564">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-564">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="acace-565">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="acace-565">**MaxRequestBodySize**</span></span>

<span data-ttu-id="acace-566">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-566">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="acace-567">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-567">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="acace-568">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-568">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="acace-569">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-569">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acace-570">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-570">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="acace-571">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-571">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acace-572">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-572">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="acace-573">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-573">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="acace-574">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-574">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="acace-575">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-575">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="acace-577">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="acace-577">Configure Windows Server</span></span>

1. <span data-ttu-id="acace-578">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-578">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="acace-579">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-579">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-580">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-580">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="acace-581">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-581">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-582">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-582">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="acace-583">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="acace-583">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="acace-584">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-584">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="acace-585">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-585">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="acace-586">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-586">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="acace-587">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-587">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="acace-588">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-588">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="acace-589">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-589">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="acace-590">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-590">Install the required .NET Framework.</span></span> <span data-ttu-id="acace-591">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-591">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="acace-592">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-592">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="acace-593">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-593">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="acace-594">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-594">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="acace-595">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-595">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="acace-596">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-596">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="acace-597">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="acace-597">`urls` command-line argument</span></span>
   * <span data-ttu-id="acace-598">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="acace-598">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="acace-599">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="acace-599">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="acace-600">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-600">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="acace-601">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-601">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="acace-602">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-602">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="acace-603">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-603">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="acace-604">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-604">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="acace-605">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-605">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="acace-606">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-606">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="acace-607">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-607">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="acace-608">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="acace-608">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="acace-609">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-609">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="acace-610">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-610">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="acace-611">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-611">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="acace-612">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-612">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="acace-613">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-613">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="acace-614">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-614">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="acace-615">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-615">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="acace-616">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-616">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-617">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-617">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="acace-618">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="acace-618">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="acace-619">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-619">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="acace-620">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-620">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="acace-621">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-621">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="acace-622">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-622">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="acace-623">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-623">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="acace-624">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-624">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="acace-625">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-625">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="acace-626">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-626">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-627">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-627">Use a valid IP address.</span></span>
   * <span data-ttu-id="acace-628">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-628">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="acace-629">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-629">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="acace-630">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-630">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="acace-631">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-631">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="acace-632">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-632">In Visual Studio:</span></span>
     * <span data-ttu-id="acace-633">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-633">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="acace-634">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-634">Select the **Package** tab.</span></span>
     * <span data-ttu-id="acace-635">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-635">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="acace-636">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="acace-636">When not using Visual Studio:</span></span>
     * <span data-ttu-id="acace-637">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-637">Open the app's project file.</span></span>
     * <span data-ttu-id="acace-638">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-638">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="acace-639">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="acace-639">In the following example:</span></span>

   * <span data-ttu-id="acace-640">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-640">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="acace-641">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-641">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="acace-642">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-642">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="acace-643">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-643">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="acace-644">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-644">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="acace-645">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="acace-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="acace-646">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="acace-646">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="acace-647">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-647">Run the app.</span></span>

   <span data-ttu-id="acace-648">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-648">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="acace-649">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-649">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="acace-650">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-650">The app responds at the server's public IP address.</span></span> <span data-ttu-id="acace-651">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-651">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="acace-652">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-652">A development certificate is used in this example.</span></span> <span data-ttu-id="acace-653">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-653">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="acace-655">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="acace-655">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="acace-656">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-656">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="acace-657">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-657">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acace-658">추가 자료</span><span class="sxs-lookup"><span data-stu-id="acace-658">Additional resources</span></span>

* [<span data-ttu-id="acace-659">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="acace-659">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="acace-660">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="acace-660">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="acace-661">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="acace-661">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="acace-662">호스트</span><span class="sxs-lookup"><span data-stu-id="acace-662">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="acace-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="acace-664">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-664">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="acace-665">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-665">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="acace-666">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-666">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="acace-667">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="acace-667">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="acace-668">포트 공유</span><span class="sxs-lookup"><span data-stu-id="acace-668">Port sharing</span></span>
* <span data-ttu-id="acace-669">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="acace-669">HTTPS with SNI</span></span>
* <span data-ttu-id="acace-670">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-670">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="acace-671">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="acace-671">Direct file transmission</span></span>
* <span data-ttu-id="acace-672">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="acace-672">Response caching</span></span>
* <span data-ttu-id="acace-673">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="acace-673">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="acace-674">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="acace-674">Supported Windows versions:</span></span>

* <span data-ttu-id="acace-675">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="acace-675">Windows 7 or later</span></span>
* <span data-ttu-id="acace-676">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="acace-676">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="acace-677">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="acace-677">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="acace-678">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-678">When to use HTTP.sys</span></span>

<span data-ttu-id="acace-679">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-679">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="acace-680">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="acace-680">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="acace-682">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-682">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="acace-684">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-684">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="acace-685">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-685">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="acace-686">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="acace-686">HTTP/2 support</span></span>

<span data-ttu-id="acace-687">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-687">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acace-688">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="acace-688">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="acace-689">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="acace-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acace-690">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="acace-690">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acace-691">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-691">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="acace-692">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-692">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="acace-693">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-693">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="acace-694">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-694">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="acace-695">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="acace-695">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="acace-696">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-696">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="acace-697">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-697">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="acace-698">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-698">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="acace-699">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-699">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="acace-700">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="acace-700">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="acace-701">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="acace-701">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="acace-702">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-702">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="acace-703">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-703">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="acace-704">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-704">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="acace-705">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-705">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="acace-706">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-706">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="acace-707">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="acace-707">**HTTP.sys options**</span></span>

| <span data-ttu-id="acace-708">속성</span><span class="sxs-lookup"><span data-stu-id="acace-708">Property</span></span> | <span data-ttu-id="acace-709">설명</span><span class="sxs-lookup"><span data-stu-id="acace-709">Description</span></span> | <span data-ttu-id="acace-710">기본값</span><span class="sxs-lookup"><span data-stu-id="acace-710">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="acace-711">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="acace-711">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="acace-712">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-712">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="acace-713">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="acace-713">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="acace-714">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-714">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="acace-715">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="acace-715">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="acace-716">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-716">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="acace-717">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-717">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="acace-718">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-718">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="acace-719">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="acace-719">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="acace-720">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-720">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="acace-721">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-721">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="acace-722">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-722">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="acace-723">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-723">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="acace-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="acace-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="acace-725">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-725">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="acace-726">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-726">Use `-1` for infinite.</span></span> <span data-ttu-id="acace-727">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-727">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="acace-728">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="acace-728">(machine-wide</span></span><br><span data-ttu-id="acace-729">설정)</span><span class="sxs-lookup"><span data-stu-id="acace-729">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="acace-730"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-730">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="acace-731">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="acace-731">30000000 bytes</span></span><br><span data-ttu-id="acace-732">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="acace-732">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="acace-733">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-733">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="acace-734">1000</span><span class="sxs-lookup"><span data-stu-id="acace-734">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="acace-735">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="acace-735">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="acace-736">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="acace-736">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="acace-737">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-737">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="acace-738">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="acace-738">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="acace-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="acace-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="acace-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="acace-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="acace-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="acace-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="acace-745"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-745">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="acace-746">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-746">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="acace-747">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-747">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="acace-748">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="acace-748">**MaxRequestBodySize**</span></span>

<span data-ttu-id="acace-749">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-749">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="acace-750">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-750">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="acace-751">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-751">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="acace-752">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-752">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acace-753">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-753">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="acace-754">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-754">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acace-755">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-755">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="acace-756">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-756">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="acace-757">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-757">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="acace-758">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-758">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="acace-760">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="acace-760">Configure Windows Server</span></span>

1. <span data-ttu-id="acace-761">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-761">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="acace-762">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-762">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-763">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-763">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="acace-764">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-764">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="acace-765">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-765">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="acace-766">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="acace-766">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="acace-767">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-767">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="acace-768">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-768">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="acace-769">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-769">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="acace-770">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-770">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="acace-771">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-771">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="acace-772">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-772">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="acace-773">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-773">Install the required .NET Framework.</span></span> <span data-ttu-id="acace-774">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-774">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="acace-775">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-775">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="acace-776">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-776">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="acace-777">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-777">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="acace-778">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-778">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="acace-779">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-779">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="acace-780">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="acace-780">`urls` command-line argument</span></span>
   * <span data-ttu-id="acace-781">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="acace-781">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="acace-782">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="acace-782">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="acace-783">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-783">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="acace-784">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-784">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="acace-785">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-785">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="acace-786">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-786">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="acace-787">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-787">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="acace-788">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-788">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="acace-789">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-789">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="acace-790">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-790">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="acace-791">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="acace-791">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="acace-792">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-792">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="acace-793">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-793">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="acace-794">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-794">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="acace-795">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-795">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="acace-796">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-796">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="acace-797">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-797">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="acace-798">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-798">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="acace-799">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-799">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-800">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-800">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="acace-801">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="acace-801">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="acace-802">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-802">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="acace-803">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-803">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="acace-804">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-804">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="acace-805">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-805">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="acace-806">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-806">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="acace-807">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-807">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="acace-808">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-808">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="acace-809">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="acace-809">Don't use a wildcard binding.</span></span> <span data-ttu-id="acace-810">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-810">Use a valid IP address.</span></span>
   * <span data-ttu-id="acace-811">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-811">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="acace-812">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-812">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="acace-813">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-813">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="acace-814">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-814">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="acace-815">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-815">In Visual Studio:</span></span>
     * <span data-ttu-id="acace-816">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-816">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="acace-817">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-817">Select the **Package** tab.</span></span>
     * <span data-ttu-id="acace-818">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-818">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="acace-819">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="acace-819">When not using Visual Studio:</span></span>
     * <span data-ttu-id="acace-820">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="acace-820">Open the app's project file.</span></span>
     * <span data-ttu-id="acace-821">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-821">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="acace-822">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="acace-822">In the following example:</span></span>

   * <span data-ttu-id="acace-823">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-823">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="acace-824">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-824">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="acace-825">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-825">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="acace-826">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-826">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="acace-827">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="acace-827">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="acace-828">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="acace-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="acace-829">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="acace-829">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="acace-830">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-830">Run the app.</span></span>

   <span data-ttu-id="acace-831">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-831">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="acace-832">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-832">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="acace-833">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="acace-833">The app responds at the server's public IP address.</span></span> <span data-ttu-id="acace-834">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-834">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="acace-835">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-835">A development certificate is used in this example.</span></span> <span data-ttu-id="acace-836">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="acace-836">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="acace-838">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="acace-838">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="acace-839">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="acace-839">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="acace-840">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="acace-840">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acace-841">추가 자료</span><span class="sxs-lookup"><span data-stu-id="acace-841">Additional resources</span></span>

* [<span data-ttu-id="acace-842">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="acace-842">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="acace-843">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="acace-843">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="acace-844">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="acace-844">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="acace-845">호스트</span><span class="sxs-lookup"><span data-stu-id="acace-845">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
