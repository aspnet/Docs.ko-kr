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
ms.openlocfilehash: e44cdcb7e427c1ae2531c452a7c8b49e104b3d11
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586074"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="93521-104">ASP.NET Core에서 HTTP.sys 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="93521-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="93521-105">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="93521-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="93521-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="93521-107">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="93521-108">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="93521-109">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="93521-110">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="93521-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="93521-111">포트 공유</span><span class="sxs-lookup"><span data-stu-id="93521-111">Port sharing</span></span>
* <span data-ttu-id="93521-112">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="93521-112">HTTPS with SNI</span></span>
* <span data-ttu-id="93521-113">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="93521-114">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="93521-114">Direct file transmission</span></span>
* <span data-ttu-id="93521-115">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="93521-115">Response caching</span></span>
* <span data-ttu-id="93521-116">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="93521-117">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="93521-117">Supported Windows versions:</span></span>

* <span data-ttu-id="93521-118">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="93521-118">Windows 7 or later</span></span>
* <span data-ttu-id="93521-119">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="93521-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="93521-120">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93521-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="93521-121">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-121">When to use HTTP.sys</span></span>

<span data-ttu-id="93521-122">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="93521-123">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="93521-125">내부 배포에는 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-125">An internal deployment requires a feature not available in Kestrel.</span></span> <span data-ttu-id="93521-126">자세한 내용은 이 문서의 [Kestrel 및 HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span><span class="sxs-lookup"><span data-stu-id="93521-126">For more information, see [Kestrel vs. HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="93521-128">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-128">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="93521-129">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-129">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="93521-130">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="93521-130">HTTP/2 support</span></span>

<span data-ttu-id="93521-131">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-131">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="93521-132">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="93521-132">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="93521-133">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="93521-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="93521-134">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="93521-134">TLS 1.2 or later connection</span></span>

<span data-ttu-id="93521-135">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="93521-136">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="93521-137">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="93521-138">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="93521-139">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="93521-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="93521-140">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="93521-141">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="93521-142">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="93521-143">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="93521-144">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="93521-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="93521-145">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="93521-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="93521-146">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-146">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="93521-147">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-147">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="93521-148">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-148">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="93521-149">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="93521-149">**HTTP.sys options**</span></span>

| <span data-ttu-id="93521-150">속성</span><span class="sxs-lookup"><span data-stu-id="93521-150">Property</span></span> | <span data-ttu-id="93521-151">설명</span><span class="sxs-lookup"><span data-stu-id="93521-151">Description</span></span> | <span data-ttu-id="93521-152">기본값</span><span class="sxs-lookup"><span data-stu-id="93521-152">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="93521-153">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="93521-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="93521-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="93521-155">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="93521-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="93521-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="93521-157">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="93521-158">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="93521-159">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="93521-160">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-160">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="93521-161">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-161">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="93521-162">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-162">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="93521-163">제한 조건으로 인해 요청을 거부할 때 HTTP.sys 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-163">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="93521-164">Http503VerbosityLevel.<br>Basic</span><span class="sxs-lookup"><span data-stu-id="93521-164">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="93521-165">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-165">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="93521-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="93521-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="93521-167">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-167">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="93521-168">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-168">Use `-1` for infinite.</span></span> <span data-ttu-id="93521-169">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-169">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="93521-170">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="93521-170">(machine-wide</span></span><br><span data-ttu-id="93521-171">설정)</span><span class="sxs-lookup"><span data-stu-id="93521-171">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="93521-172"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="93521-173">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="93521-173">30000000 bytes</span></span><br><span data-ttu-id="93521-174">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="93521-174">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="93521-175">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="93521-176">1000</span><span class="sxs-lookup"><span data-stu-id="93521-176">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="93521-177">이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="93521-177">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="93521-178">기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-178">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="93521-179">HTTP.sys 요청 큐의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-179">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="93521-180">`null`(익명 큐)</span><span class="sxs-lookup"><span data-stu-id="93521-180">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="93521-181">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="93521-181">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="93521-182">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="93521-182">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="93521-183">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-183">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="93521-184">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="93521-184">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="93521-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="93521-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="93521-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="93521-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="93521-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="93521-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="93521-191"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-191">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="93521-192">컬렉션에 접두사를 추가하는 데 사용되는 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-192">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="93521-193">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-193">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="93521-194">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="93521-194">**MaxRequestBodySize**</span></span>

<span data-ttu-id="93521-195">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-195">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="93521-196">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-196">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="93521-197">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-197">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="93521-198">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-198">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="93521-199">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-199">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="93521-200">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-200">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="93521-201">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-201">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="93521-202">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-202">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="93521-203">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-203">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="93521-204">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-204">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="93521-206">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="93521-206">Configure Windows Server</span></span>

1. <span data-ttu-id="93521-207">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-207">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="93521-208">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-208">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-209">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-209">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="93521-210">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-210">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-211">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-211">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="93521-212">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93521-212">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="93521-213">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-213">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="93521-214">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-214">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="93521-215">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-215">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="93521-216">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-216">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="93521-217">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-217">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="93521-218">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-218">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="93521-219">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-219">Install the required .NET Framework.</span></span> <span data-ttu-id="93521-220">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-220">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="93521-221">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-221">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="93521-222">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-222">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="93521-223">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-223">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="93521-224">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-224">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="93521-225">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-225">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="93521-226">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="93521-226">`urls` command-line argument</span></span>
   * <span data-ttu-id="93521-227">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="93521-227">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="93521-228">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="93521-228">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="93521-229">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-229">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="93521-230">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-230">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="93521-231">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-231">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="93521-232">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-232">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="93521-233">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-233">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="93521-234">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-234">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="93521-235">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-235">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="93521-236">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-236">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="93521-237">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="93521-237">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="93521-238">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-238">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="93521-239">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-239">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="93521-240">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-240">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="93521-241">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-241">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="93521-242">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-242">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="93521-243">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-243">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="93521-244">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-244">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="93521-245">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-245">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-246">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-246">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="93521-247">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="93521-247">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="93521-248">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-248">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="93521-249">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-249">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="93521-250">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-250">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="93521-251">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-251">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="93521-252">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-252">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="93521-253">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-253">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="93521-254">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-254">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="93521-255">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-255">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-256">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-256">Use a valid IP address.</span></span>
   * <span data-ttu-id="93521-257">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-257">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="93521-258">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-258">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="93521-259">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-259">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="93521-260">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-260">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="93521-261">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-261">In Visual Studio:</span></span>
     * <span data-ttu-id="93521-262">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-262">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="93521-263">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-263">Select the **Package** tab.</span></span>
     * <span data-ttu-id="93521-264">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-264">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="93521-265">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="93521-265">When not using Visual Studio:</span></span>
     * <span data-ttu-id="93521-266">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-266">Open the app's project file.</span></span>
     * <span data-ttu-id="93521-267">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-267">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="93521-268">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="93521-268">In the following example:</span></span>

   * <span data-ttu-id="93521-269">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-269">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="93521-270">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-270">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="93521-271">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-271">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="93521-272">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-272">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="93521-273">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-273">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="93521-274">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="93521-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="93521-275">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="93521-275">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="93521-276">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-276">Run the app.</span></span>

   <span data-ttu-id="93521-277">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-277">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="93521-278">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-278">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="93521-279">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-279">The app responds at the server's public IP address.</span></span> <span data-ttu-id="93521-280">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-280">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="93521-281">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-281">A development certificate is used in this example.</span></span> <span data-ttu-id="93521-282">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-282">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="93521-284">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="93521-284">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="93521-285">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-285">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="93521-286">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-286">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="93521-287">gRPC를 지원하기 위한 고급 HTTP/2 기능</span><span class="sxs-lookup"><span data-stu-id="93521-287">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="93521-288">HTTP.sys의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-288">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="93521-289">HTTP.sys를 사용하여 gRPC를 실행하기 위한 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="93521-289">Requirements to run gRPC with HTTP.sys:</span></span>

* <span data-ttu-id="93521-290">Windows 10, OS 빌드 19041.508 이상</span><span class="sxs-lookup"><span data-stu-id="93521-290">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="93521-291">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="93521-291">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="93521-292">트레일러</span><span class="sxs-lookup"><span data-stu-id="93521-292">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="93521-293">다시 설정</span><span class="sxs-lookup"><span data-stu-id="93521-293">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="93521-294">추가 자료</span><span class="sxs-lookup"><span data-stu-id="93521-294">Additional resources</span></span>

* [<span data-ttu-id="93521-295">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="93521-295">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="93521-296">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="93521-296">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="93521-297">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="93521-297">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="93521-298">호스트</span><span class="sxs-lookup"><span data-stu-id="93521-298">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="93521-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="93521-300">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-300">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="93521-301">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-301">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="93521-302">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-302">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="93521-303">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="93521-303">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="93521-304">포트 공유</span><span class="sxs-lookup"><span data-stu-id="93521-304">Port sharing</span></span>
* <span data-ttu-id="93521-305">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="93521-305">HTTPS with SNI</span></span>
* <span data-ttu-id="93521-306">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-306">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="93521-307">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="93521-307">Direct file transmission</span></span>
* <span data-ttu-id="93521-308">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="93521-308">Response caching</span></span>
* <span data-ttu-id="93521-309">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-309">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="93521-310">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="93521-310">Supported Windows versions:</span></span>

* <span data-ttu-id="93521-311">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="93521-311">Windows 7 or later</span></span>
* <span data-ttu-id="93521-312">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="93521-312">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="93521-313">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93521-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="93521-314">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-314">When to use HTTP.sys</span></span>

<span data-ttu-id="93521-315">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-315">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="93521-316">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-316">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="93521-318">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-318">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="93521-320">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-320">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="93521-321">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-321">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="93521-322">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="93521-322">HTTP/2 support</span></span>

<span data-ttu-id="93521-323">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-323">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="93521-324">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="93521-324">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="93521-325">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="93521-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="93521-326">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="93521-326">TLS 1.2 or later connection</span></span>

<span data-ttu-id="93521-327">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-327">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="93521-328">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-328">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="93521-329">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-329">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="93521-330">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-330">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="93521-331">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="93521-331">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="93521-332">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-332">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="93521-333">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-333">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="93521-334">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-334">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="93521-335">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-335">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="93521-336">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="93521-336">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="93521-337">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="93521-337">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="93521-338">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-338">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="93521-339">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-339">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="93521-340">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-340">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="93521-341">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="93521-341">**HTTP.sys options**</span></span>

| <span data-ttu-id="93521-342">속성</span><span class="sxs-lookup"><span data-stu-id="93521-342">Property</span></span> | <span data-ttu-id="93521-343">설명</span><span class="sxs-lookup"><span data-stu-id="93521-343">Description</span></span> | <span data-ttu-id="93521-344">기본값</span><span class="sxs-lookup"><span data-stu-id="93521-344">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="93521-345">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="93521-345">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="93521-346">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-346">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="93521-347">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="93521-347">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="93521-348">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-348">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="93521-349">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="93521-349">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="93521-350">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-350">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="93521-351">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-351">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="93521-352">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-352">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="93521-353">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="93521-353">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="93521-354">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-354">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="93521-355">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-355">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="93521-356">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-356">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="93521-357">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-357">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="93521-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="93521-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="93521-359">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-359">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="93521-360">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-360">Use `-1` for infinite.</span></span> <span data-ttu-id="93521-361">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-361">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="93521-362">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="93521-362">(machine-wide</span></span><br><span data-ttu-id="93521-363">설정)</span><span class="sxs-lookup"><span data-stu-id="93521-363">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="93521-364"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-364">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="93521-365">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="93521-365">30000000 bytes</span></span><br><span data-ttu-id="93521-366">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="93521-366">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="93521-367">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-367">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="93521-368">1000</span><span class="sxs-lookup"><span data-stu-id="93521-368">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="93521-369">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="93521-369">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="93521-370">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="93521-370">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="93521-371">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-371">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="93521-372">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="93521-372">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="93521-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="93521-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="93521-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="93521-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="93521-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="93521-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="93521-379"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-379">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="93521-380">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-380">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="93521-381">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-381">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="93521-382">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="93521-382">**MaxRequestBodySize**</span></span>

<span data-ttu-id="93521-383">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-383">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="93521-384">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-384">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="93521-385">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-385">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="93521-386">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-386">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="93521-387">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-387">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="93521-388">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-388">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="93521-389">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-389">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="93521-390">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-390">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="93521-391">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-391">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="93521-392">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-392">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="93521-394">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="93521-394">Configure Windows Server</span></span>

1. <span data-ttu-id="93521-395">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-395">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="93521-396">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-396">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-397">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-397">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="93521-398">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-398">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-399">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-399">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="93521-400">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93521-400">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="93521-401">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-401">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="93521-402">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-402">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="93521-403">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-403">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="93521-404">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-404">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="93521-405">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-405">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="93521-406">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-406">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="93521-407">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-407">Install the required .NET Framework.</span></span> <span data-ttu-id="93521-408">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-408">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="93521-409">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-409">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="93521-410">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-410">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="93521-411">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-411">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="93521-412">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-412">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="93521-413">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-413">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="93521-414">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="93521-414">`urls` command-line argument</span></span>
   * <span data-ttu-id="93521-415">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="93521-415">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="93521-416">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="93521-416">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="93521-417">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-417">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="93521-418">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-418">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="93521-419">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-419">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="93521-420">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-420">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="93521-421">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-421">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="93521-422">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-422">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="93521-423">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-423">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="93521-424">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-424">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="93521-425">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="93521-425">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="93521-426">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-426">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="93521-427">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-427">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="93521-428">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-428">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="93521-429">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-429">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="93521-430">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-430">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="93521-431">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-431">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="93521-432">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-432">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="93521-433">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-433">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-434">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-434">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="93521-435">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="93521-435">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="93521-436">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-436">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="93521-437">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-437">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="93521-438">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-438">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="93521-439">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-439">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="93521-440">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-440">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="93521-441">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-441">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="93521-442">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-442">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="93521-443">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-443">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-444">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-444">Use a valid IP address.</span></span>
   * <span data-ttu-id="93521-445">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-445">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="93521-446">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-446">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="93521-447">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-447">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="93521-448">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-448">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="93521-449">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-449">In Visual Studio:</span></span>
     * <span data-ttu-id="93521-450">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-450">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="93521-451">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-451">Select the **Package** tab.</span></span>
     * <span data-ttu-id="93521-452">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-452">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="93521-453">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="93521-453">When not using Visual Studio:</span></span>
     * <span data-ttu-id="93521-454">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-454">Open the app's project file.</span></span>
     * <span data-ttu-id="93521-455">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-455">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="93521-456">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="93521-456">In the following example:</span></span>

   * <span data-ttu-id="93521-457">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-457">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="93521-458">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-458">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="93521-459">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-459">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="93521-460">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-460">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="93521-461">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-461">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="93521-462">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="93521-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="93521-463">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="93521-463">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="93521-464">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-464">Run the app.</span></span>

   <span data-ttu-id="93521-465">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-465">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="93521-466">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-466">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="93521-467">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-467">The app responds at the server's public IP address.</span></span> <span data-ttu-id="93521-468">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-468">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="93521-469">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-469">A development certificate is used in this example.</span></span> <span data-ttu-id="93521-470">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-470">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="93521-472">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="93521-472">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="93521-473">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-473">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="93521-474">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-474">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93521-475">추가 자료</span><span class="sxs-lookup"><span data-stu-id="93521-475">Additional resources</span></span>

* [<span data-ttu-id="93521-476">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="93521-476">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="93521-477">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="93521-477">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="93521-478">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="93521-478">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="93521-479">호스트</span><span class="sxs-lookup"><span data-stu-id="93521-479">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="93521-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="93521-481">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-481">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="93521-482">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-482">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="93521-483">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-483">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="93521-484">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="93521-484">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="93521-485">포트 공유</span><span class="sxs-lookup"><span data-stu-id="93521-485">Port sharing</span></span>
* <span data-ttu-id="93521-486">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="93521-486">HTTPS with SNI</span></span>
* <span data-ttu-id="93521-487">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-487">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="93521-488">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="93521-488">Direct file transmission</span></span>
* <span data-ttu-id="93521-489">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="93521-489">Response caching</span></span>
* <span data-ttu-id="93521-490">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-490">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="93521-491">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="93521-491">Supported Windows versions:</span></span>

* <span data-ttu-id="93521-492">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="93521-492">Windows 7 or later</span></span>
* <span data-ttu-id="93521-493">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="93521-493">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="93521-494">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93521-494">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="93521-495">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-495">When to use HTTP.sys</span></span>

<span data-ttu-id="93521-496">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-496">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="93521-497">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-497">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="93521-499">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-499">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="93521-501">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-501">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="93521-502">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-502">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="93521-503">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="93521-503">HTTP/2 support</span></span>

<span data-ttu-id="93521-504">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-504">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="93521-505">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="93521-505">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="93521-506">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="93521-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="93521-507">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="93521-507">TLS 1.2 or later connection</span></span>

<span data-ttu-id="93521-508">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-508">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="93521-509">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-509">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="93521-510">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-510">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="93521-511">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-511">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="93521-512">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="93521-512">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="93521-513">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-513">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="93521-514">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-514">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="93521-515">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-515">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="93521-516">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-516">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="93521-517">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="93521-517">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="93521-518">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="93521-518">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="93521-519">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-519">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="93521-520">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-520">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="93521-521">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-521">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="93521-522">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-522">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="93521-523">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-523">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="93521-524">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="93521-524">**HTTP.sys options**</span></span>

| <span data-ttu-id="93521-525">속성</span><span class="sxs-lookup"><span data-stu-id="93521-525">Property</span></span> | <span data-ttu-id="93521-526">설명</span><span class="sxs-lookup"><span data-stu-id="93521-526">Description</span></span> | <span data-ttu-id="93521-527">기본값</span><span class="sxs-lookup"><span data-stu-id="93521-527">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="93521-528">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="93521-528">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="93521-529">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-529">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="93521-530">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="93521-530">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="93521-531">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-531">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="93521-532">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="93521-532">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="93521-533">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-533">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="93521-534">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-534">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="93521-535">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-535">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="93521-536">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="93521-536">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="93521-537">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-537">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="93521-538">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-538">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="93521-539">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-539">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="93521-540">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-540">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="93521-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="93521-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="93521-542">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-542">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="93521-543">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-543">Use `-1` for infinite.</span></span> <span data-ttu-id="93521-544">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-544">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="93521-545">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="93521-545">(machine-wide</span></span><br><span data-ttu-id="93521-546">설정)</span><span class="sxs-lookup"><span data-stu-id="93521-546">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="93521-547"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-547">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="93521-548">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="93521-548">30000000 bytes</span></span><br><span data-ttu-id="93521-549">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="93521-549">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="93521-550">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-550">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="93521-551">1000</span><span class="sxs-lookup"><span data-stu-id="93521-551">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="93521-552">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="93521-552">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="93521-553">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="93521-553">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="93521-554">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-554">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="93521-555">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="93521-555">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="93521-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="93521-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="93521-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="93521-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="93521-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="93521-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="93521-562"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-562">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="93521-563">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-563">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="93521-564">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-564">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="93521-565">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="93521-565">**MaxRequestBodySize**</span></span>

<span data-ttu-id="93521-566">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-566">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="93521-567">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-567">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="93521-568">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-568">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="93521-569">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-569">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="93521-570">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-570">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="93521-571">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-571">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="93521-572">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-572">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="93521-573">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-573">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="93521-574">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-574">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="93521-575">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-575">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="93521-577">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="93521-577">Configure Windows Server</span></span>

1. <span data-ttu-id="93521-578">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-578">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="93521-579">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-579">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-580">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-580">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="93521-581">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-581">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-582">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-582">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="93521-583">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93521-583">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="93521-584">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-584">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="93521-585">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-585">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="93521-586">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-586">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="93521-587">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-587">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="93521-588">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-588">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="93521-589">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-589">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="93521-590">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-590">Install the required .NET Framework.</span></span> <span data-ttu-id="93521-591">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-591">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="93521-592">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-592">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="93521-593">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-593">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="93521-594">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-594">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="93521-595">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-595">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="93521-596">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-596">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="93521-597">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="93521-597">`urls` command-line argument</span></span>
   * <span data-ttu-id="93521-598">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="93521-598">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="93521-599">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="93521-599">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="93521-600">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-600">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="93521-601">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-601">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="93521-602">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-602">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="93521-603">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-603">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="93521-604">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-604">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="93521-605">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-605">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="93521-606">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-606">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="93521-607">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-607">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="93521-608">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="93521-608">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="93521-609">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-609">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="93521-610">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-610">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="93521-611">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-611">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="93521-612">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-612">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="93521-613">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-613">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="93521-614">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-614">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="93521-615">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-615">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="93521-616">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-616">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-617">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-617">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="93521-618">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="93521-618">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="93521-619">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-619">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="93521-620">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-620">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="93521-621">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-621">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="93521-622">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-622">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="93521-623">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-623">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="93521-624">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-624">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="93521-625">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-625">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="93521-626">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-626">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-627">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-627">Use a valid IP address.</span></span>
   * <span data-ttu-id="93521-628">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-628">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="93521-629">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-629">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="93521-630">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-630">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="93521-631">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-631">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="93521-632">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-632">In Visual Studio:</span></span>
     * <span data-ttu-id="93521-633">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-633">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="93521-634">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-634">Select the **Package** tab.</span></span>
     * <span data-ttu-id="93521-635">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-635">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="93521-636">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="93521-636">When not using Visual Studio:</span></span>
     * <span data-ttu-id="93521-637">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-637">Open the app's project file.</span></span>
     * <span data-ttu-id="93521-638">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-638">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="93521-639">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="93521-639">In the following example:</span></span>

   * <span data-ttu-id="93521-640">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-640">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="93521-641">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-641">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="93521-642">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-642">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="93521-643">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-643">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="93521-644">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-644">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="93521-645">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="93521-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="93521-646">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="93521-646">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="93521-647">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-647">Run the app.</span></span>

   <span data-ttu-id="93521-648">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-648">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="93521-649">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-649">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="93521-650">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-650">The app responds at the server's public IP address.</span></span> <span data-ttu-id="93521-651">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-651">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="93521-652">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-652">A development certificate is used in this example.</span></span> <span data-ttu-id="93521-653">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-653">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="93521-655">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="93521-655">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="93521-656">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-656">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="93521-657">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-657">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93521-658">추가 자료</span><span class="sxs-lookup"><span data-stu-id="93521-658">Additional resources</span></span>

* [<span data-ttu-id="93521-659">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="93521-659">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="93521-660">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="93521-660">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="93521-661">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="93521-661">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="93521-662">호스트</span><span class="sxs-lookup"><span data-stu-id="93521-662">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="93521-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="93521-664">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-664">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="93521-665">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-665">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="93521-666">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-666">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="93521-667">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="93521-667">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="93521-668">포트 공유</span><span class="sxs-lookup"><span data-stu-id="93521-668">Port sharing</span></span>
* <span data-ttu-id="93521-669">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="93521-669">HTTPS with SNI</span></span>
* <span data-ttu-id="93521-670">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-670">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="93521-671">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="93521-671">Direct file transmission</span></span>
* <span data-ttu-id="93521-672">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="93521-672">Response caching</span></span>
* <span data-ttu-id="93521-673">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="93521-673">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="93521-674">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="93521-674">Supported Windows versions:</span></span>

* <span data-ttu-id="93521-675">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="93521-675">Windows 7 or later</span></span>
* <span data-ttu-id="93521-676">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="93521-676">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="93521-677">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93521-677">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="93521-678">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-678">When to use HTTP.sys</span></span>

<span data-ttu-id="93521-679">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-679">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="93521-680">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="93521-680">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="93521-682">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-682">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="93521-684">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-684">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="93521-685">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-685">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="93521-686">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="93521-686">HTTP/2 support</span></span>

<span data-ttu-id="93521-687">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-687">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="93521-688">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="93521-688">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="93521-689">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="93521-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="93521-690">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="93521-690">TLS 1.2 or later connection</span></span>

<span data-ttu-id="93521-691">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-691">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="93521-692">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-692">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="93521-693">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-693">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="93521-694">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-694">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="93521-695">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="93521-695">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="93521-696">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-696">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="93521-697">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-697">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="93521-698">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-698">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="93521-699">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-699">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="93521-700">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="93521-700">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="93521-701">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="93521-701">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="93521-702">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-702">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="93521-703">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-703">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="93521-704">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-704">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="93521-705">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-705">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="93521-706">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-706">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="93521-707">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="93521-707">**HTTP.sys options**</span></span>

| <span data-ttu-id="93521-708">속성</span><span class="sxs-lookup"><span data-stu-id="93521-708">Property</span></span> | <span data-ttu-id="93521-709">설명</span><span class="sxs-lookup"><span data-stu-id="93521-709">Description</span></span> | <span data-ttu-id="93521-710">기본값</span><span class="sxs-lookup"><span data-stu-id="93521-710">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="93521-711">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="93521-711">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="93521-712">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-712">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="93521-713">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="93521-713">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="93521-714">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-714">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="93521-715">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="93521-715">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="93521-716">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-716">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="93521-717">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-717">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="93521-718">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-718">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="93521-719">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="93521-719">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="93521-720">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-720">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="93521-721">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-721">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="93521-722">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-722">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="93521-723">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-723">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="93521-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="93521-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="93521-725">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-725">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="93521-726">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-726">Use `-1` for infinite.</span></span> <span data-ttu-id="93521-727">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-727">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="93521-728">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="93521-728">(machine-wide</span></span><br><span data-ttu-id="93521-729">설정)</span><span class="sxs-lookup"><span data-stu-id="93521-729">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="93521-730"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-730">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="93521-731">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="93521-731">30000000 bytes</span></span><br><span data-ttu-id="93521-732">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="93521-732">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="93521-733">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-733">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="93521-734">1000</span><span class="sxs-lookup"><span data-stu-id="93521-734">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="93521-735">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="93521-735">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="93521-736">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="93521-736">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="93521-737">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-737">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="93521-738">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="93521-738">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="93521-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="93521-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="93521-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="93521-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="93521-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="93521-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="93521-745"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-745">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="93521-746">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-746">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="93521-747">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-747">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="93521-748">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="93521-748">**MaxRequestBodySize**</span></span>

<span data-ttu-id="93521-749">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-749">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="93521-750">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-750">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="93521-751">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-751">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="93521-752">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-752">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="93521-753">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-753">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="93521-754">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-754">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="93521-755">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-755">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="93521-756">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-756">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="93521-757">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-757">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="93521-758">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-758">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="93521-760">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="93521-760">Configure Windows Server</span></span>

1. <span data-ttu-id="93521-761">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-761">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="93521-762">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-762">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-763">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-763">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="93521-764">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-764">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="93521-765">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-765">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="93521-766">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93521-766">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="93521-767">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-767">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="93521-768">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-768">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="93521-769">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-769">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="93521-770">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-770">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="93521-771">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-771">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="93521-772">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-772">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="93521-773">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-773">Install the required .NET Framework.</span></span> <span data-ttu-id="93521-774">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-774">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="93521-775">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-775">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="93521-776">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-776">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="93521-777">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-777">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="93521-778">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-778">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="93521-779">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-779">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="93521-780">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="93521-780">`urls` command-line argument</span></span>
   * <span data-ttu-id="93521-781">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="93521-781">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="93521-782">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="93521-782">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="93521-783">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-783">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="93521-784">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-784">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="93521-785">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-785">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="93521-786">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-786">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="93521-787">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-787">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="93521-788">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-788">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="93521-789">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-789">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="93521-790">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-790">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="93521-791">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="93521-791">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="93521-792">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-792">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="93521-793">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-793">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="93521-794">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe* 입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-794">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="93521-795">*netsh.exe* 는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-795">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="93521-796">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-796">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="93521-797">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-797">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="93521-798">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-798">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="93521-799">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-799">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-800">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-800">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="93521-801">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="93521-801">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="93521-802">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-802">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="93521-803">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-803">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="93521-804">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-804">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="93521-805">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-805">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="93521-806">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-806">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="93521-807">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-807">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="93521-808">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-808">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="93521-809">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="93521-809">Don't use a wildcard binding.</span></span> <span data-ttu-id="93521-810">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-810">Use a valid IP address.</span></span>
   * <span data-ttu-id="93521-811">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-811">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="93521-812">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-812">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="93521-813">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-813">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="93521-814">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-814">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="93521-815">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-815">In Visual Studio:</span></span>
     * <span data-ttu-id="93521-816">**솔루션 탐색기** 에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-816">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="93521-817">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-817">Select the **Package** tab.</span></span>
     * <span data-ttu-id="93521-818">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-818">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="93521-819">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="93521-819">When not using Visual Studio:</span></span>
     * <span data-ttu-id="93521-820">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93521-820">Open the app's project file.</span></span>
     * <span data-ttu-id="93521-821">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-821">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="93521-822">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="93521-822">In the following example:</span></span>

   * <span data-ttu-id="93521-823">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-823">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="93521-824">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-824">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="93521-825">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-825">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="93521-826">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-826">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="93521-827">*netsh.exe* 에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="93521-827">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="93521-828">[HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="93521-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="93521-829">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="93521-829">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="93521-830">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-830">Run the app.</span></span>

   <span data-ttu-id="93521-831">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-831">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="93521-832">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-832">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="93521-833">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="93521-833">The app responds at the server's public IP address.</span></span> <span data-ttu-id="93521-834">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-834">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="93521-835">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-835">A development certificate is used in this example.</span></span> <span data-ttu-id="93521-836">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="93521-836">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="93521-838">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="93521-838">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="93521-839">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93521-839">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="93521-840">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="93521-840">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93521-841">추가 자료</span><span class="sxs-lookup"><span data-stu-id="93521-841">Additional resources</span></span>

* [<span data-ttu-id="93521-842">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="93521-842">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="93521-843">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="93521-843">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="93521-844">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="93521-844">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="93521-845">호스트</span><span class="sxs-lookup"><span data-stu-id="93521-845">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
