---
title: ASP.NET Core SignalR Java 클라이언트
author: mikaelm12
description: ASP.NET Core Java 클라이언트를 사용 하는 방법을 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024758"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.NET Core SignalR Java 클라이언트

작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)

Java 클라이언트를 사용 하면 SignalR Android 앱을 비롯 한 java 코드에서 ASP.NET Core 서버에 연결할 수 있습니다. [JavaScript 클라이언트](xref:signalr/javascript-client) 및 [.net 클라이언트](xref:signalr/dotnet-client)와 마찬가지로 Java 클라이언트를 사용 하면 실시간으로 허브에 메시지를 보내고 받을 수 있습니다. Java 클라이언트는 ASP.NET Core 2.2 이상에서 사용할 수 있습니다.

이 문서에서 참조 되는 샘플 Java 콘솔 앱은 SignalR java 클라이언트를 사용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>Java 클라이언트 패키지를 설치 합니다. SignalR

*Signalr-1.0.0* JAR 파일을 사용 하면 클라이언트가 허브에 연결할 수 있습니다 SignalR . 최신 JAR 파일 버전 번호를 찾으려면 [Maven 검색 결과](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)를 참조 하세요.

Gradle를 사용 하는 경우 `dependencies` *Gradle* 파일의 섹션에 다음 줄을 추가 합니다.

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven를 사용 하는 경우 `<dependencies>` *pom.xml* 파일의 요소 내에 다음 줄을 추가 합니다.

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>허브에 연결

을 설정 하려면를 `HubConnection` `HubConnectionBuilder` 사용 해야 합니다. 연결을 빌드하는 동안 허브 URL 및 로그 수준을 구성할 수 있습니다. 이전에 메서드를 호출 하 여 필요한 옵션을 구성 `HubConnectionBuilder` `build` 합니다. 와의 연결을 시작 `start` 합니다.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출

에 대 한 호출은 `send` 허브 메서드를 호출 합니다. 허브 메서드 이름 및 허브 메서드에 정의 된 모든 인수를에 전달 `send` 합니다.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> 클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다. 자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출

`hubConnection.on`를 사용 하 여 허브에서 호출할 수 있는 클라이언트에서 메서드를 정의 합니다. 빌드 후와 연결을 시작 하기 전에 메서드를 정의 합니다.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>로깅 추가

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

## <a name="android-development-notes"></a>Android 개발 정보

클라이언트 기능에 대 한 Android SDK 호환성과 관련 하 여 SignalR 대상 Android SDK 버전을 지정할 때 다음 항목을 고려 하십시오.

* SignalRJava 클라이언트는 ANDROID API 수준 16 이상에서 실행 됩니다.
* Azure 서비스 SignalR 에는 TLS 1.2이 필요 하 고 SHA-1 기반 암호 [그룹 SignalR ](/azure/azure-signalr/signalr-overview) 은 지원 하지 않으므로 azure 서비스를 통해 연결 하려면 Android API 수준 20 이상이 필요 합니다. Android는 API 수준 20에서 [SHA-256 이상 암호 그룹에 대 한 지원을 추가 했습니다](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .

## <a name="configure-bearer-token-authentication"></a>전달자 토큰 인증 구성

SignalRJava 클라이언트에서는 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)에 "액세스 토큰 팩터리"를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>Java로 클래스 정보 전달

`on` `invoke` Java 클라이언트에서의, 또는 메서드를 호출 하는 경우 `stream` `HubConnection` 사용자는 `Type` 개체 대신 개체를 전달 하 여 `Class<?>` 메서드에 전달 된 제네릭을 설명 해야 합니다 `Object` . `Type`제공 된 클래스를 사용 하 여을 가져올 수 있습니다 `TypeReference` . 예를 들어 라는 사용자 지정 제네릭 클래스를 사용 하는 경우 `Foo<T>` 다음 코드는를 가져옵니다 `Type` .

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

기본 형식을 사용 하는 경우와 같이 제네릭이 아닌 경우에 `String` 는 기본 제공만 사용 하면 됩니다 `.class` .

하나 이상의 개체 형식을 사용 하 여 이러한 메서드 중 하나를 호출 하는 경우 메서드를 호출할 때 제네릭 구문을 사용 합니다. 예를 들어 `on` 문자열 및 개체를 인수로 사용 하는 라는 메서드에 대 한 처리기를 등록 하는 경우 `func` `Foo<String>` 다음 코드를 사용 하 여 인수를 인쇄 하는 작업을 설정 합니다.

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

이 규칙은 `Object.getClass` Java에서 형식 지우기로 인해 메서드를 사용 하 여 복합 형식에 대 한 전체 정보를 검색할 수 없기 때문에 필요 합니다. 예를 들어,에 대해를 호출 하면가 반환 되지 않고,이를 위해 `getClass` `ArrayList<String>` `Class<ArrayList<String>>` `Class<ArrayList>` 역직렬 변환기에 들어오는 메시지를 올바르게 deserialize 할 수 있는 충분 한 정보가 제공 되지 않습니다. 사용자 지정 개체의 경우에도 마찬가지입니다.

::: moniker-end

## <a name="known-limitations"></a>알려진 제한 사항

::: moniker range=">= aspnetcore-5.0"

* 전송 대체 (fallback) 및 전송 된 서버 전송 이벤트 전송은 지원 되지 않습니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* 전송 대체 (fallback) 및 전송 된 서버 전송 이벤트 전송은 지원 되지 않습니다.
* JSON 프로토콜만 지원 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* JSON 프로토콜만 지원 됩니다.
* Websocket 전송만 지원 됩니다.
* 스트리밍은 아직 지원 되지 않습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [Java API 참조](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
