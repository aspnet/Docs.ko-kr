---
title: 'ASP.NET Core SignalR Java 클라이언트'
author: mikaelm12
description: 'ASP.NET Core Java 클라이언트를 사용 하는 방법을 알아봅니다 SignalR .'
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: 638333176ae31b088bdf5ebefe97e87bde6c0d32
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051462"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="2db5d-103">ASP.NET Core SignalR Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="2db5d-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="2db5d-104">작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="2db5d-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="2db5d-105">Java 클라이언트를 사용 하면 SignalR Android 앱을 비롯 한 java 코드에서 ASP.NET Core 서버에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="2db5d-106">[JavaScript 클라이언트](xref:signalr/javascript-client) 및 [.net 클라이언트](xref:signalr/dotnet-client)와 마찬가지로 Java 클라이언트를 사용 하면 실시간으로 허브에 메시지를 보내고 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="2db5d-107">Java 클라이언트는 ASP.NET Core 2.2 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="2db5d-108">이 문서에서 참조 되는 샘플 Java 콘솔 앱은 SignalR java 클라이언트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="2db5d-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2db5d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="2db5d-110">Java 클라이언트 패키지를 설치 합니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="2db5d-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="2db5d-111">*Signalr-1.0.0* JAR 파일을 사용 하면 클라이언트가 허브에 연결할 수 있습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="2db5d-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="2db5d-112">최신 JAR 파일 버전 번호를 찾으려면 [Maven 검색 결과](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2db5d-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="2db5d-113">Gradle를 사용 하는 경우 `dependencies` *Gradle* 파일의 섹션에 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="2db5d-114">Maven를 사용 하는 경우 `<dependencies>` *pom.xml* 파일의 요소 내에 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="2db5d-115">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="2db5d-115">Connect to a hub</span></span>

<span data-ttu-id="2db5d-116">을 설정 하려면를 `HubConnection` `HubConnectionBuilder` 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="2db5d-117">연결을 빌드하는 동안 허브 URL 및 로그 수준을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="2db5d-118">이전에 메서드를 호출 하 여 필요한 옵션을 구성 `HubConnectionBuilder` `build` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="2db5d-119">와의 연결을 시작 `start` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="2db5d-120">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="2db5d-120">Call hub methods from client</span></span>

<span data-ttu-id="2db5d-121">에 대 한 호출은 `send` 허브 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="2db5d-122">허브 메서드 이름 및 허브 메서드에 정의 된 모든 인수를에 전달 `send` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="2db5d-123">클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="2db5d-124">자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2db5d-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="2db5d-125">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="2db5d-125">Call client methods from hub</span></span>

<span data-ttu-id="2db5d-126">`hubConnection.on`를 사용 하 여 허브에서 호출할 수 있는 클라이언트에서 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="2db5d-127">빌드 후와 연결을 시작 하기 전에 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="2db5d-128">로깅 추가</span><span class="sxs-lookup"><span data-stu-id="2db5d-128">Add logging</span></span>

<span data-ttu-id="2db5d-129">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2db5d-130">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2db5d-131">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="2db5d-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2db5d-132">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2db5d-133">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="2db5d-134">Android 개발 정보</span><span class="sxs-lookup"><span data-stu-id="2db5d-134">Android development notes</span></span>

<span data-ttu-id="2db5d-135">클라이언트 기능에 대 한 Android SDK 호환성과 관련 하 여 SignalR 대상 Android SDK 버전을 지정할 때 다음 항목을 고려 하십시오.</span><span class="sxs-lookup"><span data-stu-id="2db5d-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="2db5d-136">SignalRJava 클라이언트는 ANDROID API 수준 16 이상에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="2db5d-137">Azure 서비스 SignalR 에는 TLS 1.2이 필요 하 고 SHA-1 기반 암호 [그룹 SignalR ](/azure/azure-signalr/signalr-overview) 은 지원 하지 않으므로 azure 서비스를 통해 연결 하려면 Android API 수준 20 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="2db5d-138">Android는 API 수준 20에서 [SHA-256 이상 암호 그룹에 대 한 지원을 추가 했습니다](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .</span><span class="sxs-lookup"><span data-stu-id="2db5d-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="2db5d-139">전달자 토큰 인증 구성</span><span class="sxs-lookup"><span data-stu-id="2db5d-139">Configure bearer token authentication</span></span>

<span data-ttu-id="2db5d-140">SignalRJava 클라이언트에서는 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 "액세스 토큰 팩터리"를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2db5d-141">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2db5d-142">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="2db5d-143">알려진 제한 사항</span><span class="sxs-lookup"><span data-stu-id="2db5d-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="2db5d-144">JSON 프로토콜만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="2db5d-145">전송 대체 (fallback) 및 전송 된 서버 전송 이벤트 전송은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="2db5d-146">JSON 프로토콜만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="2db5d-147">Websocket 전송만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="2db5d-148">스트리밍은 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2db5d-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2db5d-149">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="2db5d-149">Additional resources</span></span>

* [<span data-ttu-id="2db5d-150">Java API 참조</span><span class="sxs-lookup"><span data-stu-id="2db5d-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="2db5d-151">Azure SignalR 서비스 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="2db5d-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
