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
- IHubContext
uid: signalr/hubcontext
ms.openlocfilehash: 2b2939a7692a195c6dc1b8421433a723310b4bd6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589207"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="2fc2c-103">허브 외부에서 메시지 보내기</span><span class="sxs-lookup"><span data-stu-id="2fc2c-103">Send messages from outside a hub</span></span>

<span data-ttu-id="2fc2c-104">작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="2fc2c-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="2fc2c-105">SignalR허브는 서버에 연결 된 클라이언트에 메시지를 보내기 위한 핵심 추상화입니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fc2c-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="2fc2c-106">또한 서비스를 사용 하 여 앱의 다른 위치에서 메시지를 보낼 수 있습니다 `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="2fc2c-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="2fc2c-107">이 문서에서는에 액세스 하 여 SignalR `IHubContext` 허브 외부에서 클라이언트에 알림을 보내는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="2fc2c-108">[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubcontext/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2fc2c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="2fc2c-109">인스턴스 가져오기 IHubContext</span><span class="sxs-lookup"><span data-stu-id="2fc2c-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="2fc2c-110">ASP.NET Core에서 SignalR 종속성 주입을 통해의 인스턴스에 액세스할 수 있습니다 `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="2fc2c-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="2fc2c-111">인스턴스를 `IHubContext` 컨트롤러, 미들웨어 또는 기타 DI 서비스에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="2fc2c-112">인스턴스를 사용 하 여 클라이언트에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2fc2c-113">이는 SignalR GlobalHost를 사용 하 여에 대 한 액세스를 제공 하는 ASP.NET 4.x와는 다릅니다 `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="2fc2c-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="2fc2c-114">ASP.NET Core에는이 전역 단일 항목에 대 한 필요성을 제거 하는 종속성 주입 프레임 워크가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="2fc2c-115">컨트롤러에서의 인스턴스를 삽입 IHubContext 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="2fc2c-116">인스턴스를 `IHubContext` 생성자에 추가 하 여 컨트롤러에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="2fc2c-117">이제 인스턴스에 액세스할 `IHubContext` 수 있으므로 허브 자체에 있는 것 처럼 허브 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="2fc2c-118">미들웨어에서 인스턴스 가져오기 IHubContext</span><span class="sxs-lookup"><span data-stu-id="2fc2c-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="2fc2c-119">다음과 `IHubContext` 같이 미들웨어 파이프라인 내에서에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="2fc2c-120">클래스 외부에서 허브 메서드를 호출 하는 경우 `Hub` 호출에 연결 된 호출자가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="2fc2c-121">따라서, 및 속성에 대 한 액세스 권한이 없습니다 `ConnectionId` `Caller` `Others` .</span><span class="sxs-lookup"><span data-stu-id="2fc2c-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="2fc2c-122">IHubContextIHost에서 인스턴스 가져오기</span><span class="sxs-lookup"><span data-stu-id="2fc2c-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="2fc2c-123">`IHubContext`웹 호스트에서에 액세스 하는 것은 타사 종속성 주입 프레임 워크를 사용 하는 등 ASP.NET Core 외부 영역과 통합 하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="2fc2c-124">강력한 형식의 HubContext을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="2fc2c-125">강력한 형식의 HubContext을 삽입 하려면 허브가에서 상속 되는지 확인 `Hub<T>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="2fc2c-126">대신 인터페이스를 사용 하 여 삽입 `IHubContext<THub, T>` `IHubContext<THub>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

<span data-ttu-id="2fc2c-127">자세한 내용은 [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fc2c-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2fc2c-128">관련 리소스</span><span class="sxs-lookup"><span data-stu-id="2fc2c-128">Related resources</span></span>

* [<span data-ttu-id="2fc2c-129">시작</span><span class="sxs-lookup"><span data-stu-id="2fc2c-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2fc2c-130">허브</span><span class="sxs-lookup"><span data-stu-id="2fc2c-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2fc2c-131">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="2fc2c-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
