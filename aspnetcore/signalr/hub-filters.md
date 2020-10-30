---
title: ASP.NET Core에서 허브 필터 사용 SignalR
author: brecon
description: ASP.NET Core에서 허브 필터를 사용 하는 방법에 대해 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052723"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>ASP.NET Core에서 허브 필터 사용 SignalR

허브 필터:

* ASP.NET Core 5.0 이상에서 사용할 수 있습니다.
* 클라이언트에서 허브 메서드를 호출 하기 전과 후에 논리를 실행할 수 있습니다.

이 문서에서는 허브 필터를 작성 및 사용 하기 위한 지침을 제공 합니다.

## <a name="configure-hub-filters"></a>허브 필터 구성

허브 필터는 전역적으로 또는 허브 유형에 따라 적용할 수 있습니다. 필터를 추가 하는 순서는 필터를 실행 하는 순서입니다. 전역 허브 필터는 로컬 허브 필터 전에 실행 됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

허브 필터는 다음 중 한 가지 방법으로 추가할 수 있습니다.

* 구체적 형식으로 필터 추가:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    이는 DI (종속성 주입) 또는 활성화 된 유형에 서 확인 됩니다.

* 런타임 유형별 필터 추가:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    이는 DI 또는 형식 활성화에서 확인 됩니다.

* 인스턴스별 필터 추가:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    이 인스턴스는 단일 항목 처럼 사용 됩니다. 모든 허브 메서드 호출에서 동일한 인스턴스를 사용 합니다.

허브 필터는 허브 호출 당 생성 및 삭제 됩니다. 전역 상태를 필터에 저장 하거나 상태를 저장 하지 않으려면 더 나은 성능을 위해 DI에 허브 필터 형식을 singleton으로 추가 합니다. 또는 가능 하면 필터를 인스턴스로 추가 합니다.

## <a name="create-hub-filters"></a>허브 필터 만들기

에서 상속 되는 클래스를 선언 하 `IHubFilter` 고 메서드를 추가 하 여 필터를 만듭니다 `InvokeMethodAsync` . 및 `OnConnectedAsync` `OnDisconnectedAsync` 허브 메서드를 각각 래핑하는 선택적으로를 구현할 수도 `OnConnectedAsync` 있습니다 `OnDisconnectedAsync` .

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

필터는 미들웨어와 매우 비슷합니다. `next`메서드는 다음 필터를 호출 합니다. 최종 필터는 허브 메서드를 호출 합니다. 또한 필터는 `next` 에서 결과를 반환 하기 전에 허브 메서드가 호출 된 후 대기 및 실행 논리의 결과를 저장할 수 있습니다 `next` .

필터에서 허브 메서드 호출을 건너뛰려면를 호출 하는 대신 형식의 예외를 throw `HubException` `next` 합니다. 클라이언트에서 결과가 예상 되는 경우 오류를 수신 합니다.

## <a name="use-hub-filters"></a>허브 필터 사용

필터 논리를 작성 하는 경우 허브 메서드 이름을 확인 하는 대신 허브 메서드의 특성을 사용 하 여 제네릭으로 설정 해 봅니다.

금지 된 구에 대 한 허브 메서드 인수를 확인 하 고 발견 되는 모든 구를 교체 하는 필터를 고려해 보세요 `***` .
이 예에서는 `LanguageFilterAttribute` 클래스가 정의 되어 있다고 가정 합니다. 클래스에는 `FilterArgument` 특성을 사용 하는 경우 설정할 수 있는 라는 속성이 있습니다.

1. 제거할 문자열 인수를 포함 하는 허브 메서드에 특성을 추가 합니다.

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. 허브 필터를 정의 하 여 특성을 확인 하 고 허브 메서드 인수에서 금지 된 구를 `**_` 다음과 같이 바꿉니다.

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. 메서드에 허브 필터를 등록 합니다 `Startup.ConfigureServices` . 모든 호출에 대해 금지 된 문구 목록을 다시 초기화 하지 않도록 하기 위해 허브 필터는 단일 항목으로 등록 됩니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>HubInvocationContext 개체

에는 `HubInvocationContext` 현재 허브 메서드 호출에 대 한 정보가 포함 되어 있습니다.

| 속성 | 설명 | 형식 |
| ------ | ------ | ----------- |
| `Context ` | 에는 `HubCallerContext` 연결에 대 한 정보가 포함 되어 있습니다. | `HubCallerContext` |
| `Hub` | 이 허브 메서드 호출에 사용 되는 허브의 인스턴스입니다. | `Hub` |
| `HubMethodName` | 호출 되는 허브 메서드의 이름입니다. | `string` |
| `HubMethodArguments` | 허브 메서드에 전달 되는 인수 목록입니다. | `IReadOnlyList<string>` |
| `ServiceProvider` | 이 허브 메서드 호출에 대 한 범위가 지정 된 서비스 공급자입니다. | `IServiceProvider` |
| `HubMethod` | 허브 메서드 정보입니다. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>HubLifetimeContext 개체

에는 `HubLifetimeContext` `OnConnectedAsync` 및 허브 메서드에 대 한 정보가 포함 되어 있습니다 `OnDisconnectedAsync` .

| 속성 | 설명 | 형식 |
| ------ | ------ | ----------- |
| `Context ` | 에는 `HubCallerContext` 연결에 대 한 정보가 포함 되어 있습니다. | `HubCallerContext` |
| `Hub` | 이 허브 메서드 호출에 사용 되는 허브의 인스턴스입니다. | `Hub` |
| `ServiceProvider` | 이 허브 메서드 호출에 대 한 범위가 지정 된 서비스 공급자입니다. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>권한 부여 및 필터

허브 필터 보다 먼저 실행 되는 [허브 메서드에서 특성을 부여](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) 합니다.
