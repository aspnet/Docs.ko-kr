---
title: HttpRepl 원격 분석
author: scottaddie
description: HttpRepl에 의해 수집 된 원격 분석에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550610"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="28ebe-103">HttpRepl 원격 분석</span><span class="sxs-lookup"><span data-stu-id="28ebe-103">HttpRepl telemetry</span></span>

<span data-ttu-id="28ebe-104">[HttpRepl](xref:web-api/http-repl) 에는 사용 현황 데이터를 수집 하는 원격 분석 기능이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="28ebe-105">HttpRepl 팀이 도구를 사용 하 여 개선할 수 있는 방법을 이해 하는 것이 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="28ebe-106">옵트아웃(opt out)하는 방법</span><span class="sxs-lookup"><span data-stu-id="28ebe-106">How to opt out</span></span>

<span data-ttu-id="28ebe-107">HttpRepl 원격 분석 기능은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="28ebe-108">원격 분석 기능을 옵트아웃하려면 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 환경 변수를 `1` 또는 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="28ebe-109">공개</span><span class="sxs-lookup"><span data-stu-id="28ebe-109">Disclosure</span></span>

<span data-ttu-id="28ebe-110">HttpRepl는 도구를 처음 실행할 때 다음과 유사한 텍스트를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="28ebe-111">텍스트는 실행 중인 도구의 버전에 따라 약간씩 다를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="28ebe-112">이 "첫 실행" 경험이 Microsoft가 사용자에게 데이터 수집에 대해 알리는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="28ebe-113">"첫 실행" 환경 텍스트를 표시 하지 않으려면 `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` 환경 변수를 또는로 설정 합니다 `1` `true` .</span><span class="sxs-lookup"><span data-stu-id="28ebe-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="28ebe-114">데이터 요소</span><span class="sxs-lookup"><span data-stu-id="28ebe-114">Data points</span></span>

<span data-ttu-id="28ebe-115">원격 분석 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="28ebe-116">사용자 이름, 전자 메일 주소 또는 Url 등의 개인 데이터를 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="28ebe-117">HTTP 요청 또는 응답을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="28ebe-118">데이터는 Microsoft 서버에 안전 하 게 전송 되 고 제한 된 액세스로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="28ebe-119">개인 정보 보호는 Microsoft에 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="28ebe-120">원격 분석 기능이 중요 한 데이터를 수집 하거나 데이터를에서 비보안 방식 또는 부적절 하 게 처리 하는 것으로 의심 되는 경우 다음 작업 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="28ebe-121">[Dotnet/httprepl](https://github.com/dotnet/httprepl/issues) 리포지토리에서 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="28ebe-122">조사를 위해에 전자 메일을 보냅니다 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) .</span><span class="sxs-lookup"><span data-stu-id="28ebe-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="28ebe-123">원격 분석 기능은 다음 데이터를 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="28ebe-124">.NET SDK 버전</span><span class="sxs-lookup"><span data-stu-id="28ebe-124">.NET SDK versions</span></span> | <span data-ttu-id="28ebe-125">데이터</span><span class="sxs-lookup"><span data-stu-id="28ebe-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="28ebe-126">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-126">>=5.0</span></span>        | <span data-ttu-id="28ebe-127">호출의 타임스탬프</span><span class="sxs-lookup"><span data-stu-id="28ebe-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="28ebe-128">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-128">>=5.0</span></span>        | <span data-ttu-id="28ebe-129">지리적 위치를 결정 하는 데 사용 되는 3 옥텟 IP 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="28ebe-130">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-130">>=5.0</span></span>        | <span data-ttu-id="28ebe-131">운영 체제 및 버전</span><span class="sxs-lookup"><span data-stu-id="28ebe-131">Operating system and version.</span></span> |
| <span data-ttu-id="28ebe-132">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-132">>=5.0</span></span>        | <span data-ttu-id="28ebe-133">도구가 실행 되 고 있는 RID (런타임 ID)입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="28ebe-134">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-134">>=5.0</span></span>        | <span data-ttu-id="28ebe-135">도구가 컨테이너에서 실행 되 고 있는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="28ebe-136">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-136">>=5.0</span></span>        | <span data-ttu-id="28ebe-137">해시 된 미디어 Access Control (MAC) 주소: 컴퓨터에 대 한 암호화 (SHA256) 해시 및 고유 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="28ebe-138">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-138">>=5.0</span></span>        | <span data-ttu-id="28ebe-139">커널 버전</span><span class="sxs-lookup"><span data-stu-id="28ebe-139">Kernel version.</span></span> |
| <span data-ttu-id="28ebe-140">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-140">>=5.0</span></span>        | <span data-ttu-id="28ebe-141">HttpRepl 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-141">HttpRepl version.</span></span> |
| <span data-ttu-id="28ebe-142">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-142">>=5.0</span></span>        | <span data-ttu-id="28ebe-143">도구가 `help` , 또는 인수를 사용 하 여 시작 되었는지 여부를 나타냅니다 `run` `connect` .</span><span class="sxs-lookup"><span data-stu-id="28ebe-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="28ebe-144">실제 인수 값은 수집 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="28ebe-145">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-145">>=5.0</span></span>        | <span data-ttu-id="28ebe-146">호출 되는 명령 (예: `get` ) 및 성공 여부입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="28ebe-147">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-147">>=5.0</span></span>        | <span data-ttu-id="28ebe-148">명령의 경우, `connect` `root` `base` 또는 인수가 제공 되었는지 여부 `openapi` 입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="28ebe-149">실제 인수 값은 수집 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="28ebe-150">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-150">>=5.0</span></span>        | <span data-ttu-id="28ebe-151">명령에 대해 `pref` `get` 또는 `set` 이 실행 되었는지 여부와 액세스 한 기본 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="28ebe-152">잘 알려진 기본 설정이 아닌 경우 이름이 해시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="28ebe-153">값이 수집 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-153">The value isn't collected.</span></span> |
| <span data-ttu-id="28ebe-154">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-154">>=5.0</span></span>        | <span data-ttu-id="28ebe-155">명령의 경우 `set header` 설정 되는 헤더 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="28ebe-156">잘 알려진 헤더가 아니면 이름이 해시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="28ebe-157">값이 수집 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-157">The value isn't collected.</span></span> |
| <span data-ttu-id="28ebe-158">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-158">>=5.0</span></span>        | <span data-ttu-id="28ebe-159">명령에 `connect` 대 한 특수 한 사례가 사용 되었는지 여부 `dotnet new webapi` 와 기본 설정에 따라 무시 되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="28ebe-160">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="28ebe-160">>=5.0</span></span>        | <span data-ttu-id="28ebe-161">모든 HTTP 명령 (예: GET, POST, PUT)의 경우 각 옵션이 지정 되었는지 여부입니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="28ebe-162">이 옵션의 값은 수집 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28ebe-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="28ebe-163">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="28ebe-163">Additional resources</span></span>

* [<span data-ttu-id="28ebe-164">.NET Core SDK 원격 분석</span><span class="sxs-lookup"><span data-stu-id="28ebe-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="28ebe-165">원격 분석 데이터 .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="28ebe-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
