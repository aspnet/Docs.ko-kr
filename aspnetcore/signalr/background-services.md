---
title: SignalR백그라운드 서비스의 호스트 ASP.NET Core
author: bradygaster
description: SignalR.Net Core BackgroundService 클래스에서 클라이언트에 메시지를 보내는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
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
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530179"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>SignalR백그라운드 서비스의 호스트 ASP.NET Core

[Brady Gaster](https://twitter.com/bradygaster)

이 문서에서는 다음에 대 한 지침을 제공 합니다.

* SignalRASP.NET Core로 호스트 된 백그라운드 작업자 프로세스를 사용 하 여 허브를 호스팅합니다.
* .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)내에서 연결 된 클라이언트에 메시지를 보냅니다.

::: moniker range=">= aspnetcore-3.0"

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x)[(다운로드 방법)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2)[(다운로드 방법)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>SignalR시작 시 사용

::: moniker range=">= aspnetcore-3.0"

SignalR백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core 허브를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. 메서드에서는 `Startup.ConfigureServices` 를 호출 `services.AddSignalR` 하 여 지원 되는 DI (ASP.NET Core 종속성 주입) 계층에 필요한 서비스를 추가 SignalR 합니다. 에서 `Startup.Configure` 메서드는 `MapHub` `UseEndpoints` ASP.NET Core 요청 파이프라인의 허브 끝점을 연결 하기 위해 콜백에서 호출 됩니다.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

SignalR백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core 허브를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. 메서드에서는 `Startup.ConfigureServices` 를 호출 `services.AddSignalR` 하 여 지원 되는 DI (ASP.NET Core 종속성 주입) 계층에 필요한 서비스를 추가 SignalR 합니다. 에서 `Startup.Configure` 메서드를 `UseSignalR` 호출 하 여 ASP.NET Core 요청 파이프라인의 허브 끝점을 연결 합니다.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

앞의 예제에서 클래스는 `ClockHub` 클래스를 구현 `Hub<T>` 하 여 강력한 형식의 허브를 만듭니다. 는 `ClockHub` `Startup` 끝점에서 요청에 응답 하도록 클래스에서 구성 되었습니다 `/hubs/clock` .

강력한 형식의 허브에 대 한 자세한 내용은 [ SignalR ASP.NET Core에 대해 허브 사용](xref:signalr/hubs#strongly-typed-hubs)을 참조 하세요.

> [!NOTE]
> 이 기능은 [허브 \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) 클래스로 제한 되지 않습니다. [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)와 같이 [허브](xref:Microsoft.AspNetCore.SignalR.Hub)에서 상속 되는 모든 클래스는 작동 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

강력 하 게 형식화 된에서 사용 하는 인터페이스는 `ClockHub` `IClock` 인터페이스입니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>SignalR백그라운드 서비스에서 허브 호출

시작 중에 `Worker` 는를 `BackgroundService` 사용 하 여 클래스를 사용할 수 `AddHostedService` 있습니다.

```csharp
services.AddHostedService<Worker>();
```

SignalR는 단계 중에도 활성화 되므로 `Startup` 각 허브는 ASP.NET CORE의 HTTP 요청 파이프라인에서 개별 끝점에 연결 됩니다. 각 허브는 서버에서로 표시 됩니다 `IHubContext<T>` . ASP.NET Core의 DI 기능을 사용 하 여 클래스, MVC 컨트롤러 클래스 또는 페이지 모델과 같이 호스팅 계층에 의해 인스턴스화된 다른 클래스는 `BackgroundService` Razor 생성 중의 인스턴스를 허용 하 여 서버 쪽 허브에 대 한 참조를 가져올 수 있습니다 `IHubContext<ClockHub, IClock>` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

`ExecuteAsync`메서드가 백그라운드 서비스에서 반복적으로 호출 되 면 서버의 현재 날짜 및 시간이를 사용 하 여 연결 된 클라이언트에 전송 됩니다 `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>SignalR백그라운드 서비스를 사용 하 여 이벤트에 대응

용 JavaScript 클라이언트를 사용 하는 단일 페이지 앱 SignalR 또는를 사용 하는 .net 데스크톱 응용 프로그램 처럼 <xref:signalr/dotnet-client> `BackgroundService` `IHostedService` 허브에 연결 하 SignalR 고 이벤트에 응답 하는 데 또는 구현을 사용할 수도 있습니다.

`ClockHubClient`클래스는 인터페이스와 인터페이스를 둘 다 구현 `IClock` `IHostedService` 합니다. 이러한 방식으로 `Startup` 에서 계속 실행 하 고 서버에서 허브 이벤트에 응답 하는 데 사용할 수 있습니다.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

초기화 중에는 `ClockHubClient` 의 인스턴스를 만들고 `HubConnection` `IClock.ShowTime` 메서드를 허브 이벤트에 대 한 처리기로 사용 하도록 설정 합니다 `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

구현에서 `IHostedService.StartAsync` `HubConnection` 가 비동기적으로 시작 됩니다.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

메서드를 실행 하는 동안 `IHostedService.StopAsync` 은 `HubConnection` 비동기적으로 삭제 됩니다.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

구현에서 `IHostedService.StartAsync` `HubConnection` 가 비동기적으로 시작 됩니다.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

메서드를 실행 하는 동안 `IHostedService.StopAsync` 은 `HubConnection` 비동기적으로 삭제 됩니다.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs)
