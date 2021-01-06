---
title: SignalR와 ASP.NET Core 간의 차이점SignalR
author: bradygaster
description: SignalR와 ASP.NET Core 간의 차이점SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c37f0a3c528b6285fbf35f41eed781fb76c68de4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854602"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>ASP.NET SignalR 와 ASP.NET Core 간의 차이점 SignalR

ASP.NET Core SignalR ASP.NET의 클라이언트 또는 서버와 호환 되지 않습니다 SignalR . 이 문서에서는 ASP.NET Core에서 제거 되거나 변경 된 기능에 대해 자세히 설명 SignalR 합니다.

## <a name="how-to-identify-the-no-locsignalr-version"></a>버전을 확인 하는 방법 SignalR

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **서버 NuGet 패키지** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | 없음 [AspNetCore](xref:fundamentals/metapackage-app) 공유 프레임 워크에 포함 되어 있습니다. |
| **클라이언트 NuGet 패키지** | [Microsoft SignalR . AspNet. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft SignalR . AspNet. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore SignalR . 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **JavaScript client npm 패키지** | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Java 클라이언트** | [GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)  | Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **서버 앱 유형** | ASP.NET (System.web) 또는 OWIN Self-Host | ASP.NET Core |
| **지원 되는 서버 플랫폼** | .NET Framework 4.5 이상 | .NET Core 3.0 이상 |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **서버 NuGet 패키지** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **클라이언트 NuGet 패키지** | [Microsoft SignalR . AspNet. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft SignalR . AspNet. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore SignalR . 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **JavaScript client npm 패키지** | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Java 클라이언트** | [GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)  | Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **서버 앱 유형** | ASP.NET (System.web) 또는 OWIN Self-Host | ASP.NET Core |
| **지원 되는 서버 플랫폼** | .NET Framework 4.5 이상 | .NET Framework 4.6.1 이상<br>.NET Core 2.1 이상 |

::: moniker-end

## <a name="feature-differences"></a>기능 차이점

### <a name="automatic-reconnects"></a>자동 다시 연결

::: moniker range=">= aspnetcore-3.0"

ASP.NET SignalR :

* 기본적으로는 SignalR 연결을 끊을 때 서버에 다시 연결을 시도 합니다. 

ASP.NET Core SignalR :

* 자동 다시 연결은 [.net 클라이언트](xref:signalr/dotnet-client#automatically-reconnect) 와 [JavaScript 클라이언트](xref:signalr/javascript-client#automatically-reconnect)모두에서 옵트인 (opt in) 됩니다.

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 3.0 이전에는가 자동 다시 연결을 SignalR 지원 하지 않습니다. 클라이언트의 연결이 끊어지면 사용자는 새 연결을 명시적으로 시작 하 여 다시 연결 해야 합니다. ASP.NET에서 SignalR SignalR 연결을 삭제 하면 서버에 다시 연결을 시도 합니다.

::: moniker-end

### <a name="protocol-support"></a>프로토콜 지원

ASP.NET Core는 SignalR JSON을 지원 하 고 [MessagePack](xref:signalr/messagepackhubprotocol)을 기반으로 하는 새 이진 프로토콜도 지원 합니다. 또한 사용자 지정 프로토콜을 만들 수 있습니다.

### <a name="transports"></a>전송

ASP.NET Core에서 무한 프레임 전송은 지원 되지 않습니다 SignalR .

## <a name="differences-on-the-server"></a>서버의 차이점

ASP.NET Core SignalR 서버 쪽 라이브러리는 및 MVC 프로젝트의 **ASP.NET Core 웹 응용 프로그램** 템플릿에 사용 되는 [AspNetCore](xref:fundamentals/metapackage-app)에 포함 되어 있습니다. Razor

ASP.NET Core SignalR 은 ASP.NET Core 미들웨어입니다. 에서를 호출 하 여 구성 해야 합니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

라우팅을 구성 하려면 메서드의 메서드 호출 내에서 허브에 경로를 매핑합니다 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` .

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

라우팅을 구성 하려면 메서드의 메서드 호출 내에서 허브에 경로를 매핑합니다 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure` .

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>고정 세션

ASP.NET에 대 한 확장 모델을 SignalR 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다. ASP.NET Core SignalR 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다. Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다. [Azure SignalR 서비스](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.

### <a name="single-hub-per-connection"></a>연결당 단일 허브

ASP.NET Core에서는 SignalR 연결 모델이 간소화 되었습니다. 여러 허브에 대 한 액세스를 공유 하는 데 사용 되는 단일 연결이 아닌 단일 허브로 직접 연결 됩니다.

### <a name="streaming"></a>스트리밍

ASP.NET Core는 SignalR 이제 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming) 를 지원 합니다.

### <a name="state"></a>주

클라이언트와 허브 (라고도 함) 간에 임의의 상태를 전달 하는 기능이 `HubState` 제거 되었으며 진행 메시지를 지원 합니다. 현재 허브 프록시의 대응 항목이 없습니다.

### <a name="persistentconnection-removal"></a>PersistentConnection 제거

ASP.NET Core SignalR [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) 클래스가 제거 되었습니다.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다. 서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다. ASP.NET에를 `GlobalHost` 가져오는 데 사용 되는 개체가 SignalR `HubContext` ASP.NET Core에 없는 SignalR 경우

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR 모듈을 지원 하지 않습니다 `HubPipeline` .

## <a name="differences-on-the-client"></a>클라이언트의 차이점

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성 됩니다. [Javascript 클라이언트](xref:signalr/javascript-client)를 사용 하는 경우 Javascript 또는 TypeScript로 작성할 수 있습니다.

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript 클라이언트는 npm에서 호스팅됩니다.

::: moniker range=">= aspnetcore-3.0"

ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다. ASP.NET Core 버전에서 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 패키지는 JavaScript 라이브러리를 포함 합니다. 이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함 되어 있지 않습니다. Npm를 사용 하 여 npm 패키지를 가져오고 설치 `@microsoft/signalr` 합니다.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다. ASP.NET Core 버전에서 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지는 JavaScript 라이브러리를 포함 합니다. 이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함 되어 있지 않습니다. Npm를 사용 하 여 npm 패키지를 가져오고 설치 `@aspnet/signalr` 합니다.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

JQuery에 대 한 종속성이 제거 되었지만 프로젝트에서 jQuery를 계속 사용할 수 있습니다.

### <a name="internet-explorer-support"></a>Internet Explorer 지원

ASP.NET microsoft internet explorer 8 이상을 지원 SignalR 하는 반면, ASP.NET Core는 Microsoft Internet explorer를 지원 하지 않습니다 SignalR .
자세한 내용은 <xref:signalr/supported-platforms#javascript-client>를 참조하세요.

### <a name="javascript-client-method-syntax"></a>JavaScript 클라이언트 메서드 구문

::: moniker range=">= aspnetcore-3.0"

JavaScript 구문이 ASP.NET 버전의에서 변경 되었습니다 SignalR . 개체를 사용 하는 대신 `$connection` [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API를 사용 하 여 연결을 만듭니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[On](/javascript/api/@microsoft/signalr/HubConnection#on) 메서드를 사용 하 여 허브에서 호출할 수 있는 클라이언트 메서드를 지정 합니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript 구문이 ASP.NET 버전의에서 변경 되었습니다 SignalR . 개체를 사용 하는 대신 `$connection` [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API를 사용 하 여 연결을 만듭니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[On](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용 하 여 허브에서 호출할 수 있는 클라이언트 메서드를 지정 합니다.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

클라이언트 메서드를 만든 후 허브 연결을 시작 합니다. [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결 하 여 오류를 기록 하거나 처리 합니다.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>허브 프록시

::: moniker range=">= aspnetcore-3.0"

허브 프록시는 더 이상 자동으로 생성 되지 않습니다. 대신 메서드 이름이 [호출](/javascript/api/@microsoft/signalr/hubconnection#invoke) API에 문자열로 전달 됩니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

허브 프록시는 더 이상 자동으로 생성 되지 않습니다. 대신 메서드 이름이 [호출](/javascript/api/@aspnet/signalr/hubconnection#invoke) API에 문자열로 전달 됩니다.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET 및 기타 클라이언트

[AspNetCore. SignalR 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet 패키지에는 ASP.NET Core에 대 한 .net 클라이언트 라이브러리가 포함 되어 있습니다 SignalR .

을 사용 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 하 여 허브에 대 한 연결의 인스턴스를 만들고 빌드합니다.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>확장 차이점

ASP.NET는 SignalR SQL Server 및 Redis를 지원 합니다. ASP.NET Core는 SignalR Azure SignalR 서비스 및 Redis을 지원 합니다.

### <a name="aspnet"></a>ASP.NET

* [SignalR Azure Service Bus 확장](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR Redis를 사용 하는 확장](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR SQL Server 확장](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR 서비스](/azure/azure-signalr/)
* [Redis 백플레인](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>추가 리소스

* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
