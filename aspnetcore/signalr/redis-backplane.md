---
title: ASP.NET Core 스케일 아웃에 대 한 Redis 백플레인 SignalR
author: bradygaster
description: ASP.NET Core 앱에 대 한 확장을 사용 하도록 Redis 후면판을 설정 하는 방법에 대해 알아봅니다 SignalR .
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
uid: signalr/redis-backplane
ms.openlocfilehash: e92f515b82b8ee76f98eaa1fca51feb9cdd14d5c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059639"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a>ASP.NET Core 스케일 아웃에 대 한 Redis 후면판 설정 SignalR

[Andrew Stanton-간호사](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)및 [Tom Dykstra](https://github.com/tdykstra)

이 문서에서는 SignalR ASP.NET Core 앱을 확장 하는 데 사용할 [Redis](https://redis.io/) 서버 설정의 특정 측면을 설명 합니다 SignalR .

## <a name="set-up-a-redis-backplane"></a>Redis 후면판 설정

* Redis 서버를 배포 합니다.

  > [!IMPORTANT] 
  > 프로덕션 사용을 위해 Redis 후면판은 앱과 동일한 데이터 센터에서 실행 되는 경우에만 권장 됩니다 SignalR . 그렇지 않으면 네트워크 대기 시간이 성능을 저하 시킵니다. SignalR앱이 azure 클라우드에서 실행 되는 경우 SignalR Redis 후면판 대신 azure 서비스를 권장 합니다. 개발 및 테스트 환경에 Azure Redis Cache 서비스를 사용할 수 있습니다.

  자세한 내용은 다음 자료를 참조하세요.

  * <xref:signalr/scale>
  * [Redis 설명서](https://redis.io/)
  * [Azure Redis Cache 설명서](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* 앱에서 SignalR NuGet 패키지를 설치 합니다 `Microsoft.AspNetCore.SignalR.Redis` .
* 메서드에서 다음 `Startup.ConfigureServices` 을 호출 합니다 `AddRedis` `AddSignalR` .

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* 필요에 따라 옵션을 구성 합니다.
 
  대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다. 에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.

  다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` . 이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 앱에서 SignalR 다음 NuGet 패키지 중 하나를 설치 합니다.

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Redis 2. X.X.에 종속 됩니다. ASP.NET Core 2.2 이상에 권장 되는 패키지입니다.
  * `Microsoft.AspNetCore.SignalR.Redis` -Redis 1. X.X.에 종속 됩니다. 이 패키지는 ASP.NET Core 3.0 이상에 포함 되어 있지 않습니다.

* `Startup.ConfigureServices`메서드에서 다음을 호출 합니다 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> .

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 를 사용 하 `Microsoft.AspNetCore.SignalR.Redis` 는 경우를 호출 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 합니다.

* 필요에 따라 옵션을 구성 합니다.
 
  대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다. 에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.

  다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` . 이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 를 사용 하 `Microsoft.AspNetCore.SignalR.Redis` 는 경우를 호출 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 합니다.

  위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.

  Redis 옵션에 대 한 자세한 내용은 [Stackexchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)를 참조 하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* 앱에서 SignalR 다음 NuGet 패키지를 설치 합니다.

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* `Startup.ConfigureServices`메서드에서 다음을 호출 합니다 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> .

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* 필요에 따라 옵션을 구성 합니다.
 
  대부분의 옵션은 연결 문자열 또는 [Configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체에서 설정할 수 있습니다. 에 지정 된 옵션 `ConfigurationOptions` 은 연결 문자열에 설정 된 옵션을 재정의 합니다.

  다음 예제에서는 개체에서 옵션을 설정 하는 방법을 보여 줍니다 `ConfigurationOptions` . 이 예제에서는 다음 단계에 설명 된 것 처럼 여러 앱이 동일한 Redis 인스턴스를 공유할 수 있도록 채널 접두사를 추가 합니다.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  위의 코드에서 `options.Configuration` 는 연결 문자열에 지정 된 항목을 사용 하 여 초기화 됩니다.

  Redis 옵션에 대 한 자세한 내용은 [Stackexchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)를 참조 하세요.

::: moniker-end

* 여러 앱에 대해 하나의 Redis 서버를 사용 하는 경우 SignalR 각 앱에 대해 다른 채널 접두사를 사용 SignalR 합니다.

  채널 접두사를 설정 하면 SignalR 다른 채널 접두사를 사용 하는 다른 앱에서 하나의 앱을 격리 합니다. 서로 다른 접두사를 할당 하지 않으면 한 앱에서 모든 자체 클라이언트로 전송 되는 메시지는 Redis 서버를 후면판으로 사용 하는 모든 앱의 모든 클라이언트로 이동 합니다.

* 고정 세션에 대해 서버 팜 부하 분산 소프트웨어를 구성 합니다. 이 작업을 수행 하는 방법에 대 한 설명서의 몇 가지 예는 다음과 같습니다.

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis 서버 오류

Redis 서버 작동이 중단 되 면 SignalR 메시지가 전달 되지 않음을 나타내는 예외가 throw 됩니다. 몇 가지 일반적인 예외 메시지:

* *메시지를 쓰지 못했습니다.*
* *' MethodName ' 허브 메서드를 호출 하지 못했습니다.*
* *Redis에 연결 하지 못했습니다.*

SignalR 서버를 백업할 때 메시지를 보내도록 버퍼링 하지 않습니다. Redis 서버를 종료 하는 동안 전송 된 모든 메시지는 손실 됩니다.

SignalR Redis 서버를 다시 사용할 수 있게 되 면 자동으로 다시 연결 합니다.

### <a name="custom-behavior-for-connection-failures"></a>연결 실패에 대 한 사용자 지정 동작

Redis 연결 실패 이벤트를 처리 하는 방법을 보여 주는 예제는 다음과 같습니다.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redis 클러스터링

[Redis 클러스터링](https://redis.io/topics/cluster-spec) 은 여러 Redis 서버를 사용 하 여 고가용성을 달성 하는 방법입니다. 클러스터링은 공식적으로 지원 되지 않지만 작동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* <xref:signalr/scale>
* [Redis 설명서](https://redis.io/documentation)
* [StackExchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis Cache 설명서](/azure/redis-cache/)