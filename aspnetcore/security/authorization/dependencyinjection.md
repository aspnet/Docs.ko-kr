---
title: ASP.NET Core 요구 사항 처리기의 종속성 주입
author: rick-anderson
description: 종속성 주입을 사용 하 여 ASP.NET Core 앱에 권한 부여 요구 사항 처리기를 삽입 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060263"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="9bf7c-103">ASP.NET Core 요구 사항 처리기의 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="9bf7c-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="9bf7c-104">[권한 부여 처리기](xref:security/authorization/policies#handler-registration) 는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용 하 여 구성 하는 동안 서비스 컬렉션에 등록 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9bf7c-105">권한 부여 처리기 내에서 평가 하려는 규칙의 리포지토리가 있고 해당 리포지토리가 서비스 컬렉션에 등록 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="9bf7c-106">권한 부여는이를 확인 하 고 생성자에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="9bf7c-107">예를 들어 ASP를 사용 합니다. NET의 로깅 인프라를 처리기에 삽입 `ILoggerFactory` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="9bf7c-108">이러한 처리기는 다음 코드와 같이 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="9bf7c-109">이전 처리기는 모든 [서비스 수명](/dotnet/core/extensions/dependency-injection#service-lifetimes)으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9bf7c-110">다음 코드에서는를 사용 하 여 `AddSingleton` 이전 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="9bf7c-111">처리기의 인스턴스는 앱이 시작 될 때 만들어지며 DI는 생성자에 등록 된를 삽입 합니다 `ILoggerFactory` .</span><span class="sxs-lookup"><span data-stu-id="9bf7c-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="9bf7c-112">Entity Framework를 사용 하는 처리기는 단일 항목로 등록 하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9bf7c-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
