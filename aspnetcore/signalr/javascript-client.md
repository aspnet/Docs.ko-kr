---
title: SignalRJavaScript 클라이언트 ASP.NET Core
author: bradygaster
description: SignalRJavaScript 클라이언트 ASP.NET Core 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: b4b1bc6131a6676710adbf2503efe3f304d89a58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050851"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="b27e2-103">SignalRJavaScript 클라이언트 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b27e2-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b27e2-104">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="b27e2-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="b27e2-105">개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="b27e2-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b27e2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="b27e2-107">클라이언트 패키지를 설치 합니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="b27e2-107">Install the SignalR client package</span></span>

<span data-ttu-id="b27e2-108">SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="b27e2-109">다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="b27e2-110">Npm를 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="b27e2-110">Install with npm</span></span>

<span data-ttu-id="b27e2-111">Visual Studio의 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="b27e2-112">Visual Studio Code의 경우 **통합 터미널** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="b27e2-113">npm 패키지 콘텐츠를 *node_modules \\ @microsoft\signalr\dist\browser* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="b27e2-114">*Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="b27e2-115">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="b27e2-116">SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="b27e2-117">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="b27e2-118">CDN (Content Delivery Network) 사용</span><span class="sxs-lookup"><span data-stu-id="b27e2-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="b27e2-119">Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="b27e2-120">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="b27e2-121">클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="b27e2-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="b27e2-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="b27e2-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="b27e2-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="b27e2-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="b27e2-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="b27e2-125">을 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="b27e2-125">Install with LibMan</span></span>

<span data-ttu-id="b27e2-126">라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index)</span><span class="sxs-lookup"><span data-stu-id="b27e2-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="b27e2-127">예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="b27e2-128">이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b27e2-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="b27e2-129">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-129">Connect to a hub</span></span>

<span data-ttu-id="b27e2-130">다음 코드에서는 연결을 만들고 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="b27e2-131">허브 이름은 대/소문자를 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="b27e2-132">크로스-원본 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-132">Cross-origin connections</span></span>

<span data-ttu-id="b27e2-133">일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="b27e2-134">그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-134">However, there are occasions when a connection to another domain is required.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b27e2-135">클라이언트 코드는 상대 URL이 아닌 절대 URL을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-135">The client code must use an absolute URL instead of a relative URL.</span></span> <span data-ttu-id="b27e2-136">`.withUrl("/chathub")`을 `.withUrl("https://myappurl/chathub")`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-136">Change `.withUrl("/chathub")` to `.withUrl("https://myappurl/chathub")`.</span></span>

<span data-ttu-id="b27e2-137">악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-137">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="b27e2-138">원본 간 요청을 허용 하려면 클래스에서 사용 하도록 설정 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-138">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="b27e2-139">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="b27e2-139">Call hub methods from the client</span></span>

<span data-ttu-id="b27e2-140">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)의 [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-140">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="b27e2-141">이 `invoke` 메서드는 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-141">The `invoke` method accepts:</span></span>

* <span data-ttu-id="b27e2-142">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-142">The name of the hub method.</span></span>
* <span data-ttu-id="b27e2-143">허브 메서드에 정의 된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-143">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="b27e2-144">다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-144">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="b27e2-145">허브 메서드의 및 인수에 매핑하기 위해 전달 되는 두 번째 및 세 번째 인수는 `invoke` `user` `message` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-145">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="b27e2-146">클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-146">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="b27e2-147">자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b27e2-147">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="b27e2-148">`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-148">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="b27e2-149">는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-149">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="b27e2-150">서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-150">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b27e2-151">`async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-151">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="b27e2-152">JavaScript 클라이언트는의 [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출할 수도 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-152">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="b27e2-153">메서드와 달리 `invoke` `send` 메서드는 서버의 응답을 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-153">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="b27e2-154">`send`메서드는 JavaScript를 반환 합니다 `Promise` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-154">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="b27e2-155">`Promise`메시지가 서버에 전송 되 면이 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-155">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="b27e2-156">메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-156">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b27e2-157">`async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-157">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="b27e2-158">를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-158">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="b27e2-159">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-159">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="b27e2-160">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="b27e2-160">Call client methods from the hub</span></span>

<span data-ttu-id="b27e2-161">허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-161">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="b27e2-162">JavaScript 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-162">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="b27e2-163">허브가 메서드에 전달 하는 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-163">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="b27e2-164">다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-164">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="b27e2-165">인수 이름은 및입니다 `user` `message` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-165">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="b27e2-166">에서 위의 코드는 `connection.on` 메서드를 사용 하 여 서버 쪽 코드가 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b27e2-166">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="b27e2-167">SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-167">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="b27e2-168">모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-168">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="b27e2-169">이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-169">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="b27e2-170">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="b27e2-170">Error handling and logging</span></span>

<span data-ttu-id="b27e2-171">및 `try` `catch` `async` `await` 또는의 메서드를 사용 `Promise` `catch` 하 여 클라이언트 쪽 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-171">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="b27e2-172">`console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-172">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="b27e2-173">연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-173">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="b27e2-174">메시지는 지정 된 로그 수준 이상으로 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-174">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="b27e2-175">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-175">Available log levels are as follows:</span></span>

* <span data-ttu-id="b27e2-176">`signalR.LogLevel.Error`: 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-176">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="b27e2-177">`Error`메시지만 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-177">Logs `Error` messages only.</span></span>
* <span data-ttu-id="b27e2-178">`signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-178">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="b27e2-179">로그 `Warning` 및 `Error` 메시지.</span><span class="sxs-lookup"><span data-stu-id="b27e2-179">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b27e2-180">`signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-180">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="b27e2-181">`Information`, `Warning` 및 메시지를 기록 `Error` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-181">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b27e2-182">`signalR.LogLevel.Trace`: 추적 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-182">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="b27e2-183">허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-183">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="b27e2-184">[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-184">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="b27e2-185">메시지는 브라우저 콘솔에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-185">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="b27e2-186">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-186">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="b27e2-187">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-187">Automatically reconnect</span></span>

<span data-ttu-id="b27e2-188">SignalR `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)에서 메서드를 사용 하 여 자동으로 다시 연결 되도록에 대 한 JavaScript 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-188">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="b27e2-189">기본적으로 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-189">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="b27e2-190">매개 변수를 사용 하지 않으면는 `withAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고, 실패 한 네 번 시도 하면 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-190">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="b27e2-191">다시 연결 시도를 시작 하기 전에는 상태로 `HubConnection` 전환 되 고 `HubConnectionState.Reconnecting` `onreconnecting` `Disconnected` `onclose` `HubConnection` 자동 다시 연결이 구성 되지 않은 등의 콜백 트리거를 상태로 전환 하는 대신 해당 콜백을 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-191">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="b27e2-192">이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-192">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b27e2-193">클라이언트에서 처음 네 번의 시도 내에 다시 연결 하는 경우 `HubConnection` 이 상태로 다시 전환 되 `Connected` 고 `onreconnected` 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-193">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="b27e2-194">이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-194">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="b27e2-195">연결은 서버에서 완전히 새로운 `connectionId` 것 이므로 콜백에 새가 제공 됩니다 `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-195">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="b27e2-196">가 `onreconnected` `connectionId` `HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 콜백의 매개 변수는 정의 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-196">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b27e2-197">`withAutomaticReconnect()``HubConnection`초기 시작 오류를 다시 시도 하도록를 구성 하지 않으므로 시작 실패를 수동으로 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-197">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="b27e2-198">클라이언트가 처음 네 번의 시도 내에서 성공적으로 다시 연결 되지 않으면 `HubConnection` 이 상태로 전환 되 `Disconnected` 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="b27e2-199">이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-199">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b27e2-200">연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자가 다시 연결 시도 횟수를 구성 하기 위해는 `withAutomaticReconnect` 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-200">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="b27e2-201">앞의 예제에서는 `HubConnection` 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-201">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="b27e2-202">기본 구성의 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-202">This is also true for the default configuration.</span></span>

<span data-ttu-id="b27e2-203">첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-203">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="b27e2-204">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-204">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="b27e2-205">그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.</span><span class="sxs-lookup"><span data-stu-id="b27e2-205">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="b27e2-206">자동 다시 연결 시도의 타이밍과 수를 더 많이 제어 하려는 경우는 `withAutomaticReconnect` 라는 단일 메서드가 있는 인터페이스를 구현 하는 개체를 허용 `IRetryPolicy` `nextRetryDelayInMilliseconds` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-206">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="b27e2-207">`nextRetryDelayInMilliseconds` 는 형식의 단일 인수를 사용 `RetryContext` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-207">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="b27e2-208">에는 `RetryContext` `previousRetryCount` , `elapsedMilliseconds` 및 `retryReason` `number` `number` `Error` 각각, 및 인 세 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-208">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="b27e2-209">첫 번째 다시 연결을 시도 하기 전에와는 모두 0이 되며 `previousRetryCount` `elapsedMilliseconds` 은 `retryReason` 연결이 끊어지는 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-209">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="b27e2-210">실패 한 각 재시도 후에는 `previousRetryCount` 1 씩 증가 하 `elapsedMilliseconds` 고, 지금까지 밀리초 단위로 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 되며,는 `retryReason` 마지막 다시 연결 시도가 실패 한 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-210">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="b27e2-211">`nextRetryDelayInMilliseconds` 는 다음 다시 연결 시도 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 해야 합니다. 그렇지 않으면에서 다시 연결을 중지 해야 합니다 `null` `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-211">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="b27e2-212">또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-212">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="b27e2-213">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-213">Manually reconnect</span></span>

<span data-ttu-id="b27e2-214">다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-214">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="b27e2-215">함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-215">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="b27e2-216">`start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-216">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="b27e2-217">실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-217">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b27e2-218">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b27e2-218">Additional resources</span></span>

* [<span data-ttu-id="b27e2-219">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="b27e2-219">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="b27e2-220">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="b27e2-220">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b27e2-221">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="b27e2-221">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="b27e2-222">허브</span><span class="sxs-lookup"><span data-stu-id="b27e2-222">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b27e2-223">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="b27e2-223">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b27e2-224">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="b27e2-224">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="b27e2-225">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="b27e2-225">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="b27e2-226">Azure SignalR 서비스 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="b27e2-226">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="b27e2-227">연결 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="b27e2-227">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b27e2-228">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="b27e2-228">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="b27e2-229">개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-229">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="b27e2-230">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b27e2-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="b27e2-231">클라이언트 패키지를 설치 합니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="b27e2-231">Install the SignalR client package</span></span>

<span data-ttu-id="b27e2-232">SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-232">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="b27e2-233">다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-233">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="b27e2-234">Npm를 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="b27e2-234">Install with npm</span></span>

<span data-ttu-id="b27e2-235">Visual Studio를 사용 하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-235">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="b27e2-236">Visual Studio Code의 경우 **통합 터미널** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-236">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="b27e2-237">npm 패키지 콘텐츠를 *node_modules \\ @aspnet\signalr\dist\browser* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-237">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="b27e2-238">*Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-238">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="b27e2-239">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-239">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="b27e2-240">SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-240">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="b27e2-241">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-241">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="b27e2-242">CDN (Content Delivery Network) 사용</span><span class="sxs-lookup"><span data-stu-id="b27e2-242">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="b27e2-243">Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-243">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="b27e2-244">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-244">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="b27e2-245">클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-245">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="b27e2-246">cdnjs</span><span class="sxs-lookup"><span data-stu-id="b27e2-246">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="b27e2-247">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="b27e2-247">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="b27e2-248">unpkg</span><span class="sxs-lookup"><span data-stu-id="b27e2-248">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="b27e2-249">을 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="b27e2-249">Install with LibMan</span></span>

<span data-ttu-id="b27e2-250">라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index)</span><span class="sxs-lookup"><span data-stu-id="b27e2-250">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="b27e2-251">예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-251">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="b27e2-252">이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b27e2-252">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="b27e2-253">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-253">Connect to a hub</span></span>

<span data-ttu-id="b27e2-254">다음 코드에서는 연결을 만들고 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-254">The following code creates and starts a connection.</span></span> <span data-ttu-id="b27e2-255">허브 이름은 대/소문자를 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-255">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="b27e2-256">크로스-원본 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-256">Cross-origin connections</span></span>

<span data-ttu-id="b27e2-257">일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-257">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="b27e2-258">그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-258">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="b27e2-259">악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-259">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="b27e2-260">원본 간 요청을 허용 하려면 클래스에서 해당 요청을 사용 하도록 설정 `Startup` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-260">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="b27e2-261">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="b27e2-261">Call hub methods from client</span></span>

<span data-ttu-id="b27e2-262">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-262">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="b27e2-263">`invoke`메서드는 두 개의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-263">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="b27e2-264">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-264">The name of the hub method.</span></span> <span data-ttu-id="b27e2-265">다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-265">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="b27e2-266">허브 메서드에 정의 된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-266">Any arguments defined in the hub method.</span></span> <span data-ttu-id="b27e2-267">다음 예에서는 인수 이름이 `message` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-267">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="b27e2-268">예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-268">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="b27e2-269">클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-269">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="b27e2-270">자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b27e2-270">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="b27e2-271">`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-271">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="b27e2-272">는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-272">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="b27e2-273">서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-273">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b27e2-274">`then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-274">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="b27e2-275">`send`메서드는 JavaScript를 반환 합니다 `Promise` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-275">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="b27e2-276">`Promise`메시지가 서버에 전송 되 면이 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-276">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="b27e2-277">메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-277">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b27e2-278">`then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-278">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="b27e2-279">를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-279">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="b27e2-280">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-280">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="b27e2-281">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="b27e2-281">Call client methods from hub</span></span>

<span data-ttu-id="b27e2-282">허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-282">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="b27e2-283">JavaScript 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-283">The name of the JavaScript client method.</span></span> <span data-ttu-id="b27e2-284">다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-284">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="b27e2-285">허브가 메서드에 전달 하는 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-285">Arguments the hub passes to the method.</span></span> <span data-ttu-id="b27e2-286">다음 예제에서 인수 값은 `message` 입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-286">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="b27e2-287">에서 위의 코드는 `connection.on` 서버 쪽 코드가 메서드를 사용 하 여 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b27e2-287">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="b27e2-288">SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-288">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="b27e2-289">모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-289">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="b27e2-290">이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-290">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="b27e2-291">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="b27e2-291">Error handling and logging</span></span>

<span data-ttu-id="b27e2-292">메서드를 `catch` 메서드의 끝에 연결 하 여 `start` 클라이언트 쪽 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-292">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="b27e2-293">`console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-293">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="b27e2-294">연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-294">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="b27e2-295">메시지는 지정 된 로그 수준 이상으로 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-295">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="b27e2-296">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-296">Available log levels are as follows:</span></span>

* <span data-ttu-id="b27e2-297">`signalR.LogLevel.Error`: 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-297">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="b27e2-298">`Error`메시지만 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-298">Logs `Error` messages only.</span></span>
* <span data-ttu-id="b27e2-299">`signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-299">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="b27e2-300">로그 `Warning` 및 `Error` 메시지.</span><span class="sxs-lookup"><span data-stu-id="b27e2-300">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b27e2-301">`signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-301">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="b27e2-302">`Information`, `Warning` 및 메시지를 기록 `Error` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-302">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b27e2-303">`signalR.LogLevel.Trace`: 추적 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-303">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="b27e2-304">허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-304">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="b27e2-305">[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-305">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="b27e2-306">메시지는 브라우저 콘솔에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-306">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="b27e2-307">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-307">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="b27e2-308">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b27e2-308">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="b27e2-309">3.0 이전 버전의에 대 한 JavaScript 클라이언트는 SignalR 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-309">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="b27e2-310">클라이언트를 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-310">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="b27e2-311">다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-311">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="b27e2-312">함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-312">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="b27e2-313">`start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="b27e2-313">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="b27e2-314">실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.</span><span class="sxs-lookup"><span data-stu-id="b27e2-314">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b27e2-315">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b27e2-315">Additional resources</span></span>

* [<span data-ttu-id="b27e2-316">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="b27e2-316">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="b27e2-317">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="b27e2-317">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b27e2-318">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="b27e2-318">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="b27e2-319">허브</span><span class="sxs-lookup"><span data-stu-id="b27e2-319">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b27e2-320">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="b27e2-320">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b27e2-321">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="b27e2-321">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="b27e2-322">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="b27e2-322">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="b27e2-323">Azure SignalR 서비스 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="b27e2-323">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
