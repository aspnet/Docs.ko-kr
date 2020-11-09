---
title: ASP.NET Core 3.0의 새로운 기능
author: rick-anderson
description: ASP.NET Core 3.0의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 235daac5c08248ca2052de6b44e66a8162ce23ad
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051241"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="4bead-103">ASP.NET Core 3.0의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="4bead-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="4bead-104">이 문서에서는 ASP.NET Core 3.0의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="4bead-105">Blazor는 .NET을 사용하여 대화형 클라이언트 쪽 웹 UI를 빌드하기 위한 ASP.NET Core 내의 새로운 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="4bead-106">JavaScript 대신 C#을 사용하여 풍부한 대화형 UI를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="4bead-107">.NET에서 작성된 서버 쪽 및 클라이언트 쪽 앱 논리를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="4bead-108">모바일 브라우저를 포함한 광범위한 브라우저 지원을 위해 UI를 HTML 및 CSS로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="4bead-109">Blazor 프레임워크 지원 시나리오:</span><span class="sxs-lookup"><span data-stu-id="4bead-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="4bead-110">재사용 가능한 UI 구성 요소(Razor 구성 요소)</span><span class="sxs-lookup"><span data-stu-id="4bead-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="4bead-111">클라이언트 쪽 라우팅</span><span class="sxs-lookup"><span data-stu-id="4bead-111">Client-side routing</span></span>
* <span data-ttu-id="4bead-112">구성 요소 레이아웃</span><span class="sxs-lookup"><span data-stu-id="4bead-112">Component layouts</span></span>
* <span data-ttu-id="4bead-113">종속성 주입을 위한 지원</span><span class="sxs-lookup"><span data-stu-id="4bead-113">Support for dependency injection</span></span>
* <span data-ttu-id="4bead-114">양식 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="4bead-114">Forms and validation</span></span>
* <span data-ttu-id="4bead-115">Razor 클래스 라이브러리를 사용하여 구성 요소 라이브러리 빌드</span><span class="sxs-lookup"><span data-stu-id="4bead-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="4bead-116">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="4bead-116">JavaScript interop</span></span>

<span data-ttu-id="4bead-117">자세한 내용은 <xref:blazor/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="4bead-118">Blazor는 UI 업데이트 적용 방법에서 구성 요소 렌더링 논리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="4bead-119">Blazor Server에서는 ASP.NET Core 앱의 서버에서 Razor 구성 요소를 호스팅할 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="4bead-120">UI 업데이트는 SignalR 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="4bead-121">Blazor Server는 ASP.NET Core 3.0에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="4bead-122">Blazor WebAssembly(미리 보기)</span><span class="sxs-lookup"><span data-stu-id="4bead-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="4bead-123">Blazor 앱은 WebAssembly 기반 .NET 런타임을 사용하여 브라우저에서 직접 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="4bead-124">Blazor WebAssembly는 미리 보기로 제공되며, ASP.NET Core 3.0에서 지원되지 ‘않습니다’.</span><span class="sxs-lookup"><span data-stu-id="4bead-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bead-125">Blazor WebAssembly는 ASP.NET Core의 이후 릴리스에서 지원될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="4bead-126">Razor 구성 요소</span><span class="sxs-lookup"><span data-stu-id="4bead-126">Razor components</span></span>

<span data-ttu-id="4bead-127">Blazor 앱은 구성 요소에서 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-127">Blazor apps are built from components.</span></span> <span data-ttu-id="4bead-128">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="4bead-129">구성 요소는 UI 렌더링 논리 및 클라이언트 쪽 이벤트 처리기를 정의하는 일반적인 .NET 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="4bead-130">JavaScript 없이 풍부한 대화형 웹앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="4bead-131">Blazor의 구성 요소는 일반적으로 HTML과 C#의 자연 혼합인 Razor 구문을 사용하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="4bead-132">Razor 구성 요소는 둘 다 Razor를 사용한다는 점에서 Razor Pages 및 MVC 뷰와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="4bead-133">요청-응답 모델 기반의 페이지와 보기와는 달리, 구성 요소는 특별히 UI 컴퍼지션을 처리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="4bead-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="4bead-134">gRPC</span></span>

<span data-ttu-id="4bead-135">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="4bead-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="4bead-136">인기 있는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="4bead-137">API 개발에 독자적인 계약 중심 접근 방식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="4bead-138">다음과 같은 최신 기술을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="4bead-139">전송용 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4bead-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="4bead-140">인터페이스 설명 언어인 프로토콜 버퍼</span><span class="sxs-lookup"><span data-stu-id="4bead-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="4bead-141">이진 직렬화 형식</span><span class="sxs-lookup"><span data-stu-id="4bead-141">Binary serialization format.</span></span>
* <span data-ttu-id="4bead-142">다음과 같은 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-142">Provides features such as:</span></span>

  * <span data-ttu-id="4bead-143">인증</span><span class="sxs-lookup"><span data-stu-id="4bead-143">Authentication</span></span>
  * <span data-ttu-id="4bead-144">양방향 스트리밍 및 흐름 제어</span><span class="sxs-lookup"><span data-stu-id="4bead-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="4bead-145">취소 및 시간 제한</span><span class="sxs-lookup"><span data-stu-id="4bead-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="4bead-146">ASP.NET Core 3.0의 gRPC 기능에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="4bead-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): gRPC 서비스 호스팅을 위한 ASP.NET Core 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="4bead-148">ASP.NET의 gRPC는 로깅, DI(종속성 주입), 인증 및 권한 부여와 같은 표준 ASP.NET Core 기능과 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="4bead-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): 친숙한 `HttpClient`를 기반으로 하는 .NET Core용 gRPC 클라이언트입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="4bead-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC 클라이언트와 `HttpClientFactory`를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="4bead-151">자세한 내용은 <xref:grpc/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="4bead-152">마이그레이션 지침은 [SignalR 코드 업데이트](xref:migration/22-to-30#signalr)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="4bead-153">이제 SignalR이 `System.Text.Json`을 사용하여 JSON 메시지를 직렬화 및 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="4bead-154">`Newtonsoft.Json` 기반 직렬 변환기를 복원하려면 [Newtonsoft.Json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)에서 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="4bead-155">SignalR용 JavaScript 및 .NET Clients에서 자동 다시 연결을 위해 지원이 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="4bead-156">기본적으로 클라이언트는 즉시 다시 연결을 시도하고 필요한 경우 2, 10, 30초 후에 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="4bead-157">클라이언트는 다시 연결되면 새로운 연결 ID를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="4bead-158">자동 다시 연결 옵트인(opt-in:):</span><span class="sxs-lookup"><span data-stu-id="4bead-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="4bead-159">밀리초 기반 기간의 배열을 전달하여 다시 연결 간격을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="4bead-160">사용자 지정 구현을 전달하여 다시 연결 간격을 완전히 컨트롤할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="4bead-161">마지막 다시 연결 간격 후 다시 연결이 실패하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4bead-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="4bead-162">클라이언트는 연결이 오프라인 상태인 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="4bead-163">클라이언트에서 다시 연결 시도를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="4bead-164">다시 연결을 시도하는 동안 다시 연결을 시도하고 있음을 사용자에게 알리기 위해 앱 UI를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="4bead-165">연결이 중단될 때 UI 피드백을 제공하기 위해 SignalR 클라이언트 API는 다음과 같은 이벤트 처리기를 포함하도록 확장되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="4bead-166">`onreconnecting`:  개발자에게 UI를 사용하지 않도록 설정하거나 사용자에게 해당 앱이 오프라인 상태임을 알릴 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="4bead-167">`onreconnected`: 개발자에게 연결이 다시 구축되면 UI를 업데이트할 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="4bead-168">다음 코드는 `onreconnecting`을 사용하여 연결을 시도하는 동안 UI를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="4bead-169">다음 코드는 `onreconnected`를 사용하여 연결 시 UI를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="4bead-170">SignalR 3.0 이상에서는 허브 메서드에 권한 부여가 필요할 때 권한 부여 처리기에 사용자 지정 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="4bead-171">리소스가 `HubInvocationContext`의 인스턴스인 경우</span><span class="sxs-lookup"><span data-stu-id="4bead-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="4bead-172">`HubInvocationContext`는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="4bead-173">호출되는 허브 메서드의 이름</span><span class="sxs-lookup"><span data-stu-id="4bead-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="4bead-174">허브 메서드에 대한 인수</span><span class="sxs-lookup"><span data-stu-id="4bead-174">Arguments to the hub method.</span></span>

<span data-ttu-id="4bead-175">여러 조직에서 Azure Active Directory를 통해 로그인할 수 있도록 지원하는 대화방 앱의 다음 예를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="4bead-176">Microsoft 계정을 가진 사용자는 채팅에 로그인할 수 있지만 소유 조직의 멤버만 사용자를 금지하거나 사용자의 채팅 기록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="4bead-177">앱이 특정 사용자의 특정 기능을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="4bead-178">위의 코드에서 `DomainRestrictedRequirement`는 사용자 지정 `IAuthorizationRequirement` 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="4bead-179">`HubInvocationContext` 리소스 매개 변수가 전달되기 때문에 내부 로직은 다음이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="4bead-180">허브가 호출되는 컨텍스트를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="4bead-181">사용자가 개별 허브 메서드를 실행할 수 있도록 하는 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="4bead-182">개별 허브 메서드는 런타임에 코드가 확인하는 정책의 이름을 사용하여 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="4bead-183">클라이언트에서 개별 허브 메서드를 호출하려고 하면 `DomainRestrictedRequirement` 처리기가 실행되고 메서드에 대한 액세스를 컨트롤합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="4bead-184">`DomainRestrictedRequirement`가 액세스를 컨트롤하는 방식에 따라 다음이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="4bead-185">로그인된 모든 사용자는 `SendMessage` 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="4bead-186">`@jabbr.net` 전자 메일 주소로 로그인한 사용자들만 사용자의 기록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="4bead-187">`bob42@jabbr.net`만 사용자들을 대화방에서 차단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="4bead-188">`DomainRestricted` 정책을 생성하려면 다음이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="4bead-189">*Startup.cs* 에서 새 정책 추가</span><span class="sxs-lookup"><span data-stu-id="4bead-189">In *Startup.cs* , adding the new policy.</span></span>
* <span data-ttu-id="4bead-190">사용자 지정 `DomainRestrictedRequirement` 요구 사항을 매개 변수로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="4bead-191">권한 부여 미들웨어로 `DomainRestricted` 등록</span><span class="sxs-lookup"><span data-stu-id="4bead-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="4bead-192">SignalR 허브는 [엔드포인트 라우팅](xref:fundamentals/routing)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="4bead-193">SignalR 허브 연결은 이전에 명시적으로 수행되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="4bead-194">이전 버전에서는 개발자가 다양한 위치에서 컨트롤러, Razor Pages 및 허브를 연결해야 했습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="4bead-195">명시적 연결로 인해 다음과 같이 거의 동일한 일련의 라우팅 세그먼트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="4bead-196">SignalR 3.0 허브는 엔드포인트 라우팅을 통해 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="4bead-197">엔드포인트 라우팅을 사용하면 일반적으로 모든 라우팅을 `UseRouting`에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="4bead-198">ASP.NET Core 3.0 SignalR에는 다음 기능이 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="4bead-199">클라이언트-서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="4bead-199">Client-to-server streaming.</span></span> <span data-ttu-id="4bead-200">클라이언트-서버 스트리밍을 사용하는 경우 서버 쪽 메서드는 `IAsyncEnumerable<T>` 또는 `ChannelReader<T>`의 인스턴스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="4bead-201">다음 C# 샘플에서 허브에 있는 `UploadStream` 메서드는 다음 클라이언트로부터 문자열 스트림을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="4bead-202">.NET 클라이언트 앱은 위 `UploadStream` 허브 메서드의 `stream` 인수로 `IAsyncEnumerable<T>` 또는 `ChannelReader<T>` 인스턴스를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="4bead-203">`for` 루프가 완료되고 로컬 함수가 종료되면 스트림 완료가 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="4bead-204">JavaScript 클라이언트 앱은 위의 `UploadStream` 허브 메서드의 `stream` 인수에 대해 SignalR `Subject`(또는 [RxJS Subject](https://rxjs.dev/api/index/class/Subject))를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="4bead-205">JavaScript 코드는 `subject.next` 메서드를 사용하여 캡처되고 서버에 보낼 준비가 된 문자열을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="4bead-206">위의 두 코드 조각과 같은 코드를 사용하여 실시간 스트리밍 환경을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="4bead-207">새로운 JSON 직렬화</span><span class="sxs-lookup"><span data-stu-id="4bead-207">New JSON serialization</span></span>

<span data-ttu-id="4bead-208">ASP.NET Core 3.0은 이제 JSON 직렬화를 위해 기본적으로 <xref:System.Text.Json>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="4bead-209">JSON을 비동기적으로 읽고 씁니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="4bead-210">UTF-8 텍스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="4bead-211">일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="4bead-212">Json.NET을 ASP.NET Core 3.0에 추가하려면 [Newtonsoft.Json 기반 JSON 형식 지원](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="4bead-213">새 Razor 지시문</span><span class="sxs-lookup"><span data-stu-id="4bead-213">New Razor directives</span></span>

<span data-ttu-id="4bead-214">다음 목록에는 새 Razor 지시문이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="4bead-215">[`@attribute`](xref:mvc/views/razor#attribute): `@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="4bead-216">예: `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="4bead-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="4bead-217">[`@implements`](xref:mvc/views/razor#implements): `@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="4bead-218">예: `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="4bead-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="4bead-219">IdentityServer4의 웹 API 및 SPA에 대한 인증 및 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="4bead-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="4bead-220">ASP.NET Core 3.0에서는 웹 API 권한 부여에 대한 지원을 사용하여 SPA(단일 페이지 앱)의 인증을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="4bead-221">사용자를 인증하고 저장하기 위한 ASP.NET Core Identity는 OpenID Connect 구현을 위해 [IdentityServer4](https://identityserver.io/)와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="4bead-222">IdentityServer4는 ASP.NET Core 3.0용 OpenID Connect 및 OAuth 2.0 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bead-223">다음과 같은 보안 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-223">It enables the following security features:</span></span>

* <span data-ttu-id="4bead-224">AaaS(Authentication as a Service)</span><span class="sxs-lookup"><span data-stu-id="4bead-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="4bead-225">여러 응용 프로그램 유형에 대한 SSO(Single Sign-On/Off)</span><span class="sxs-lookup"><span data-stu-id="4bead-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="4bead-226">API에 대한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="4bead-226">Access control for APIs</span></span>
* <span data-ttu-id="4bead-227">페더레이션 게이트웨이</span><span class="sxs-lookup"><span data-stu-id="4bead-227">Federation Gateway</span></span>

<span data-ttu-id="4bead-228">자세한 내용은 [IdentityServer4 설명서](http://docs.identityserver.io/en/latest/index.html) 또는 [SPA에 대한 인증 및 권한 부여](xref:security/authentication/identity/spa)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="4bead-229">인증서 및 Kerberos 인증</span><span class="sxs-lookup"><span data-stu-id="4bead-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="4bead-230">인증서 인증은 다음을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="4bead-231">인증서를 허용하도록 서버 구성</span><span class="sxs-lookup"><span data-stu-id="4bead-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="4bead-232">`Startup.Configure`에서 인증 미들웨어 추가</span><span class="sxs-lookup"><span data-stu-id="4bead-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="4bead-233">`Startup.ConfigureServices`에서 인증서 인증 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="4bead-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="4bead-234">인증서 인증에 대한 옵션에는 다음 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="4bead-235">자체 서명된 인증서 허용</span><span class="sxs-lookup"><span data-stu-id="4bead-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="4bead-236">인증서 해지 확인</span><span class="sxs-lookup"><span data-stu-id="4bead-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="4bead-237">제공된 인증서에 올바른 사용량 플래그가 있는지 확인</span><span class="sxs-lookup"><span data-stu-id="4bead-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="4bead-238">본 사용자 계정은 인증서 속성으로부터 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="4bead-239">사용자 계정에는 해당 계정을 보완하거나 대체할 수 있게 해주는 이벤트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="4bead-240">자세한 내용은 <xref:security/authentication/certauth>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="4bead-241">[Windows 인증](/windows-server/security/windows-authentication/windows-authentication-overview)이 Linux 및 macOS로 확장되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="4bead-242">이전 버전에서 Windows 인증은 [IIS](xref:host-and-deploy/iis/index) 및 [HttpSys](xref:fundamentals/servers/httpsys)로 제한되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="4bead-243">ASP.NET Core 3.0에서 [Kestrel](xref:fundamentals/servers/kestrel)은 Windows 도메인 가입 호스트로 Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux 및 macOS를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="4bead-244">이러한 인증 체계의 Kestrel 지원은 [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="4bead-245">다른 인증 서비스와 마찬가지로 인증 앱 전체를 구성한 다음 해당 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="4bead-246">호스트 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="4bead-246">Host requirements:</span></span>

* <span data-ttu-id="4bead-247">Windows 호스트에는 해당 앱을 호스트하는 사용자 계정에 [서비스 사용자 이름](/windows/win32/ad/service-principal-names)(SPN)을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="4bead-248">Linux 및 macOS 컴퓨터는 해당 도메인에 가입되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="4bead-249">해당 웹 프로세스에 대한 SPN을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="4bead-250">호스트 컴퓨터에서 [Keytab 파일](/archive/blogs/pie/all-you-need-to-know-about-keytab-files)을 생성하고 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-250">[Keytab files](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="4bead-251">자세한 내용은 <xref:security/authentication/windowsauth>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="4bead-252">템플릿 변경 사항</span><span class="sxs-lookup"><span data-stu-id="4bead-252">Template changes</span></span>

<span data-ttu-id="4bead-253">웹 UI 템플릿(컨트롤러 및 뷰를 사용하는 Razor Pages, MVC)에서 다음이 제거되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="4bead-254">cookie 동의 UI가 더 이상 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="4bead-255">ASP.NET Core 3.0 템플릿에서 생성한 앱에서 cookie 동의 기능을 사용하려면 <xref:security/gdpr>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="4bead-256">스크립트 및 관련 정적 자산은 이제 CDN을 사용하는 대신 로컬 파일로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="4bead-257">자세한 내용은 [스크립트 및 관련 정적 자산은 현재 환경을 기반으로 CDN을 사용하는 대신 로컬 파일로 참조됩니다(aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="4bead-258">Angular 템플릿은 Angular 8을 사용하는 것으로 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="4bead-259">RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="4bead-260">Visual Studio의 새 템플릿 옵션은 페이지 및 보기에 대한 템플릿 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="4bead-261">명령 셸의 템플릿으로부터 RCL을 생성할 때 `--support-pages-and-views` 옵션(`dotnet new razorclasslib --support-pages-and-views`)을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="4bead-262">제네릭 호스트</span><span class="sxs-lookup"><span data-stu-id="4bead-262">Generic Host</span></span>

<span data-ttu-id="4bead-263">ASP.NET Core 3.0 템플릿은 <xref:fundamentals/host/generic-host>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="4bead-264">이전 버전은 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="4bead-265">.NET Core 제네릭 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 사용하면 ASP.NET Core 앱과 웹에 고유하지 않은 다른 서버 시나리오의 통합을 더 효율적으로 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="4bead-266">자세한 내용은 [HostBuilder가 WebHostBuilder 대체](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="4bead-267">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="4bead-267">Host configuration</span></span>

<span data-ttu-id="4bead-268">ASP.NET Core 3.0 릴리스 전에는 웹 호스트의 호스트 구성에 대해 `ASPNETCORE_`가 접두사로 추가된 환경 변수가 로드되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="4bead-269">3\.0에서 `AddEnvironmentVariables`는 `CreateDefaultBuilder`와의 호스트 구성을 위해 `DOTNET_`가 접두사로 추가된 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="4bead-270">Startup 생성자 주입의 변경 내용</span><span class="sxs-lookup"><span data-stu-id="4bead-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="4bead-271">제네릭 호스트는 `Startup` 생성자 주입에 대해서만 다음 형식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="4bead-272">모든 서비스는 여전히 `Startup.Configure` 메서드에 대한 인수로 직접 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="4bead-273">자세한 내용은 [제네릭 호스트가 시작 생성자 삽입 제한(aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="4bead-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4bead-274">Kestrel</span></span>

* <span data-ttu-id="4bead-275">제네릭 호스트로 마이그레이션하기 위해 Kestrel 구성이 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="4bead-276">3\.0에서 Kestrel은 `ConfigureWebHostDefaults`가 제공하는 웹 호스트 작성기에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="4bead-277">연결 어댑터는 Kestrel에서 제거되었고 연결 미들웨어로 대체되었습니다. 이는 ASP.NET Core 파이프라인의 HTTP 미들웨어와 비슷하며, 하위 수준 연결을 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="4bead-278">Kestrel 전송 계층이 `Connections.Abstractions`에서 공용 인터페이스로 공개되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="4bead-279">후행 헤더를 새 컬렉션으로 이동하여 헤더와 트레일러 사이의 모호성을 해결했습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="4bead-280">`HttpRequest.Body.Read`와 같은 동기 I/O API는 앱 크래시로 이어지는 일반적인 스레드 굶주림 현상(starvation)의 원인입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="4bead-281">3\.0에서 `AllowSynchronousIO`는 기본적으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="4bead-282">자세한 내용은 <xref:migration/22-to-30#kestrel>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="4bead-283">HTTP/2는 기본적으로 사용하도록 설정됨</span><span class="sxs-lookup"><span data-stu-id="4bead-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="4bead-284">HTTP/2는 HTTPS 엔드포인트에 대해 Kestrel에서 기본적으로 사용하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="4bead-285">운영 체제에서 지원되는 경우 IIS 또는 HTTP.sys에 대한 HTTP/2 지원이 사용하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="4bead-286">EventCounters 요청</span><span class="sxs-lookup"><span data-stu-id="4bead-286">EventCounters on request</span></span>

<span data-ttu-id="4bead-287">호스팅 EventSource인 `Microsoft.AspNetCore.Hosting`은 들어오는 요청과 관련된 다음과 같은 새 <xref:System.Diagnostics.Tracing.EventCounter> 형식을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="4bead-288">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="4bead-288">Endpoint routing</span></span>

<span data-ttu-id="4bead-289">프레임워크(예: MVC)가 미들웨어와 잘 작동하도록 허용하는 엔드포인트 라우팅이 다음과 같이 향상되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="4bead-290">미들웨어 및 엔드포인트의 순서는 `Startup.Configure`의 요청 처리 파이프라인에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="4bead-291">엔드포인트 및 미들웨어는 상태 검사와 같은 다른 ASP.NET Core 기반 기술과 함께 잘 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="4bead-292">엔드포인트는 CORS 또는 권한 부여와 같은 정책을 미들웨어와 MVC에서 모두 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="4bead-293">컨트롤러의 메서드에 필터 및 특성을 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="4bead-294">자세한 내용은 <xref:fundamentals/routing#routing-basics>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="4bead-295">상태 검사</span><span class="sxs-lookup"><span data-stu-id="4bead-295">Health Checks</span></span>

<span data-ttu-id="4bead-296">상태 검사는 제네릭 호스트와의 엔드포인트 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="4bead-297">`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 엔드포인트 작성기에 `MapHealthChecks`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="4bead-298">상태 검사 엔드포인트는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="4bead-299">허용되는 호스트/포트를 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="4bead-300">권한 부여가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-300">Require authorization.</span></span>
* <span data-ttu-id="4bead-301">CORS가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-301">Require CORS.</span></span>

<span data-ttu-id="4bead-302">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="4bead-303">HttpContext의 파이프</span><span class="sxs-lookup"><span data-stu-id="4bead-303">Pipes on HttpContext</span></span>

<span data-ttu-id="4bead-304">이제 <xref:System.IO.Pipelines> API를 사용하여 요청 본문을 읽고 응답 본문을 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="4bead-305">Component</span><span class="sxs-lookup"><span data-stu-id="4bead-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="4bead-306">`HttpRequest.BodyReader` 속성은 요청 본문을 읽는 데 사용할 수 있는 <xref:System.IO.Pipelines.PipeReader>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="4bead-307">Component</span><span class="sxs-lookup"><span data-stu-id="4bead-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="4bead-308">`HttpResponse.BodyWriter` 속성은 응답 본문을 쓰는 데 사용할 수 있는 <xref:System.IO.Pipelines.PipeWriter>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="4bead-309">`HttpRequest.BodyReader`는 `HttpRequest.Body` 스트림의 아날로그입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="4bead-310">`HttpResponse.BodyWriter`는 `HttpResponse.Body` 스트림의 아날로그입니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="4bead-311">IIS에서 오류 보고 개선</span><span class="sxs-lookup"><span data-stu-id="4bead-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="4bead-312">IIS에서 ASP.NET Core 앱을 호스팅할 때 시작 오류가 발생하면 더 많은 진단 데이터가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="4bead-313">이러한 오류는 해당될 때마다 스택 추적을 사용하여 Windows 이벤트 로그에 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="4bead-314">또한 모든 경고, 오류 및 처리되지 않은 예외는 Windows 이벤트 로그에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="4bead-315">작업자 서비스 및 작업자 SDK</span><span class="sxs-lookup"><span data-stu-id="4bead-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="4bead-316">.NET Core 3.0에는 새로운 작업자 서비스 앱 템플릿이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="4bead-317">이 템플릿은 .NET Core에서 장기간 실행되는 서비스를 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="4bead-318">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-318">For more information, see:</span></span>

* [<span data-ttu-id="4bead-319">Windows 서비스로서 .NET Core 작업자</span><span class="sxs-lookup"><span data-stu-id="4bead-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="4bead-320">전달된 헤더 미들웨어 개선</span><span class="sxs-lookup"><span data-stu-id="4bead-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="4bead-321">이전 버전의 ASP.NET Core에서는 Azure Linux 또는 IIS 이외의 역방향 프록시 뒤에 배포 할 때 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 및 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>을 호출하는 데 문제가 있었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="4bead-322">이전 버전에 대한 수정은 [Linux용 스키마 및 비 IIS 역방향 프록시 전달](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="4bead-323">이 시나리오는 ASP.NET Core 3.0에서 해결되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bead-324">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 호스트는 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="4bead-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`는 컨테이너 이미지에서 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="4bead-326">성능 향상</span><span class="sxs-lookup"><span data-stu-id="4bead-326">Performance improvements</span></span>

<span data-ttu-id="4bead-327">ASP.NET Core 3.0에는 메모리 사용을 줄이고 처리량을 향상시키는 다음과 같은 여러 개선 사항이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="4bead-328">범위가 지정된 서비스에 기본 제공 종속성 주입 컨테이너를 사용하는 경우 메모리 사용량 감소</span><span class="sxs-lookup"><span data-stu-id="4bead-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="4bead-329">미들웨어 시나리오 및 라우팅을 포함하여 프레임워크에서 할당 감소</span><span class="sxs-lookup"><span data-stu-id="4bead-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="4bead-330">WebSocket 연결에 대한 메모리 사용량 감소</span><span class="sxs-lookup"><span data-stu-id="4bead-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="4bead-331">HTTPS 연결에 대한 메모리 감소 및 처리량 향상</span><span class="sxs-lookup"><span data-stu-id="4bead-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="4bead-332">새롭게 최적화된 완전한 비동기 JSON 직렬 변환기</span><span class="sxs-lookup"><span data-stu-id="4bead-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="4bead-333">양식 구문 분석에서 처리량 향상 및 메모리 사용량 감소</span><span class="sxs-lookup"><span data-stu-id="4bead-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="4bead-334">ASP.NET Core 3.0은 .NET Core 3.0에서만 실행됩니다</span><span class="sxs-lookup"><span data-stu-id="4bead-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="4bead-335">ASP.NET Core 3.0에서 .NET Framework는 더 이상 지원 되는 대상 프레임워크가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="4bead-336">.NET Framework를 대상으로 하는 프로젝트는 [.NET Core 2.1 LTS 릴리스](https://dotnet.microsoft.com/download/dotnet-core/2.1)를 사용하여 완전히 지원되는 방식으로 계속할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="4bead-337">대부분의 ASP.NET Core 2.1.x 관련 패키지는 .NET Core 2.1의 3년 LTS 기간을 초과하여 무기한 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="4bead-338">마이그레이션 정보는 [.NET Framework에서 .NET Core로 코드 포팅](/dotnet/core/porting/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="4bead-339">.NET Core 공유 프레임워크 사용</span><span class="sxs-lookup"><span data-stu-id="4bead-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="4bead-340">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함된 ASP.NET Core 3.0 공유 프레임워크는 더 이상 프로젝트 파일에서 명시적인 `<PackageReference />` 요소가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="4bead-341">프로젝트 파일에서 `Microsoft.NET.Sdk.Web` SDK를 사용할 때 공유 프레임워크가 자동으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="4bead-342">ASP.NET Core 공유 프레임워크에서 제거된 어셈블리</span><span class="sxs-lookup"><span data-stu-id="4bead-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="4bead-343">ASP.NET Core 3.0 공유 프레임워크에서 제거된 가장 주목할 만한 어셈블리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="4bead-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(Json.NET).</span><span class="sxs-lookup"><span data-stu-id="4bead-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="4bead-345">Json.NET을 ASP.NET Core 3.0에 추가하려면 [Newtonsoft.Json 기반 JSON 형식 지원](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="4bead-346">ASP.NET Core 3.0에서는 JSON을 읽고 쓰는 `System.Text.Json`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4bead-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="4bead-347">자세한 내용은 이 문서에서 [새 JSON 직렬화](#new-json-serialization)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="4bead-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4bead-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="4bead-349">공유 프레임워크에서 제거되는 어셈블리의 전체 목록은 [Microsoft.AspNetCore.App 3.0에서 제거되는 어셈블리](https://github.com/dotnet/AspNetCore/issues/3755)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="4bead-350">이러한 변경을 하는 동기에 관한 자세한 내용은 [Microsoft.AspNetCore.App 3.0에서 호환성이 손상되는 변경](https://github.com/aspnet/Announcements/issues/325) 및 [ASP.NET Core 3.0의 변경 내용 처음 보기](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4bead-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
