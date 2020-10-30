---
title: SignalR HubContext
author: bradygaster
description: ASP.NET Core HubContext 서비스를 사용 하 여 SignalR 허브 외부에서 클라이언트로 알림을 보내는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058183"
---
# <a name="send-messages-from-outside-a-hub"></a>허브 외부에서 메시지 보내기

작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)

SignalR허브는 서버에 연결 된 클라이언트에 메시지를 보내기 위한 핵심 추상화입니다 SignalR . 또한 서비스를 사용 하 여 앱의 다른 위치에서 메시지를 보낼 수 있습니다 `IHubContext` . 이 문서에서는에 액세스 하 여 SignalR `IHubContext` 허브 외부에서 클라이언트에 알림을 보내는 방법을 설명 합니다.

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>IHubContext의 인스턴스 가져오기

ASP.NET Core에서 SignalR 종속성 주입을 통해의 인스턴스에 액세스할 수 있습니다 `IHubContext` . 인스턴스를 `IHubContext` 컨트롤러, 미들웨어 또는 기타 DI 서비스에 삽입할 수 있습니다. 인스턴스를 사용 하 여 클라이언트에 메시지를 보냅니다.

> [!NOTE]
> 이는 SignalR GlobalHost를 사용 하 여에 대 한 액세스를 제공 하는 ASP.NET 4.x와는 다릅니다 `IHubContext` . ASP.NET Core에는이 전역 단일 항목에 대 한 필요성을 제거 하는 종속성 주입 프레임 워크가 있습니다.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>컨트롤러에 IHubContext의 인스턴스를 삽입 합니다.

인스턴스를 `IHubContext` 생성자에 추가 하 여 컨트롤러에 삽입할 수 있습니다.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

이제 인스턴스에 액세스할 `IHubContext` 수 있으므로 허브 자체에 있는 것 처럼 허브 메서드를 호출할 수 있습니다.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>미들웨어에서 IHubContext의 인스턴스 가져오기

다음과 `IHubContext` 같이 미들웨어 파이프라인 내에서에 액세스 합니다.

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> 클래스 외부에서 허브 메서드를 호출 하는 경우 `Hub` 호출에 연결 된 호출자가 없습니다. 따라서, 및 속성에 대 한 액세스 권한이 없습니다 `ConnectionId` `Caller` `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>IHost에서 IHubContext의 인스턴스 가져오기

`IHubContext`웹 호스트에서에 액세스 하는 것은 타사 종속성 주입 프레임 워크를 사용 하는 등 ASP.NET Core 외부 영역과 통합 하는 데 유용 합니다.

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>강력한 형식의 HubContext을 삽입 합니다.

강력한 형식의 HubContext을 삽입 하려면 허브가에서 상속 되는지 확인 `Hub<T>` 합니다. 대신 인터페이스를 사용 하 여 삽입 `IHubContext<THub, T>` `IHubContext<THub>` 합니다.

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

자세한 내용은 [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs) 를 참조 하세요.

## <a name="related-resources"></a>관련 리소스

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
