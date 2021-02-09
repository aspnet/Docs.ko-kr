---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core로 gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530205"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core를 사용하는 gRPC 서비스

이 문서에서는 ASP.NET Core를 사용하여 gRPC 서비스를 시작하는 방법을 보여 줍니다.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core에서 gRPC 서비스 시작

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

gRPC 프로젝트를 만드는 방법에 대한 자세한 내용은 [gRPC 서비스 시작](xref:tutorials/grpc/grpc-start)을 참조하세요.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core 앱에 gRPC 서비스 추가

gRPC를 사용하려면 [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 패키지가 필요합니다.

### <a name="configure-grpc"></a>gRPC 구성

*Startup.cs*:

* `AddGrpc` 메서드를 통해 gRPC를 사용하도록 설정합니다.
* `MapGrpcService` 메서드를 통해 라우팅 파이프라인에 각 gRPC 서비스를 추가합니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core 미들웨어와 기능은 라우팅 파이프라인을 공유하므로 추가 요청 처리기를 제공하도록 앱을 구성할 수 있습니다. MVC 컨트롤러와 같은 추가 요청 처리기는 구성된 gRPC 서비스와 병렬로 작동합니다.

## <a name="server-options"></a>서버 옵션

gRPC 서비스는 모든 기본 제공 ASP.NET Core 서버에서 호스트할 수 있습니다.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS에는 .NET 5 및 Windows 10 빌드 20241 이상이 필요합니다.

&Dagger;HTTP.sys에는 .NET 5 및 Windows 10 빌드 19529 이상이 필요합니다.

ASP.NET Core 앱에 대해 올바른 서버를 선택하는 방법의 자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel)은 ASP.NET Core의 플랫폼 간 웹 서버입니다. Kestrel은 최상의 성능과 메모리 사용률을 제공하지만 포트 공유와 같은 HTTP.sys의 고급 기능 중 일부를 제공하지 않습니다.

Kestrel gRPC 엔드포인트:

* HTTP/2가 필요합니다.
* [TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)로 보호해야 합니다.

### <a name="http2"></a>HTTP/2

gRPC에는 HTTP/2가 필요합니다. ASP.NET Core용 gRPC는 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)이 `HTTP/2`인지 확인합니다.

Kestrel은 대부분의 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel/http2)합니다. Kestrel 엔드포인트는 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원하도록 구성됩니다.

### <a name="tls"></a>TLS

gRPC에 사용되는 Kestrel 엔드포인트는 TLS로 보호해야 합니다. 개발에서는 ASP.NET Core 개발 인증서가 있을 경우 TLS로 보호된 엔드포인트가 `https://localhost:5001`에 자동으로 생성됩니다. 구성이 필요하지 않습니다. `https` 접두사는 Kestrel 엔드포인트가 TLS를 사용하는지 확인합니다.

프로덕션에서는 TLS를 명시적으로 구성해야 합니다. 다음 *appsettings.json* 예제에서는 TLS로 보호되는 HTTP/2 엔드포인트가 제공됩니다.

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

또는 *Program.cs* 에서 Kestrel 엔드포인트를 구성할 수 있습니다.

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>프로토콜 협상

TLS는 통신 보호 이상의 용도로 사용됩니다. TLS [ALPN](https://tools.ietf.org/html/rfc7301#section-3)(Application-Layer Protocol Negotiation) 핸드셰이크는 엔드포인트가 여러 프로토콜을 지원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상하는 데 사용됩니다. 이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용할 것인지를 결정합니다.

TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다. 협상이 없기 때문에 여러 프로토콜이 있는 엔드포인트(예: `HttpProtocols.Http1AndHttp2`)는 TLS 없이 사용할 수 없습니다. 안전하지 않은 엔드포인트에 대한 모든 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.

Kestrel을 통해 HTTP/2 및 TLS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel/endpoints)을 참조하세요.

> [!NOTE]
> macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다. macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다. 자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.

## <a name="iis"></a>IIS

[IIS(인터넷 정보 서비스)](xref:host-and-deploy/iis/index)는 ASP.NET Core를 비롯한 웹앱을 호스팅하기 위한 유연하고 안전하며 관리하기 쉬운 웹 서버입니다. IIS로 gRPC 서비스를 호스트하려면 .NET 5 및 Windows 10 빌드 20241 이상이 필요합니다.

TLS 및 HTTP/2를 사용하도록 IIS를 구성해야 합니다. 자세한 내용은 <xref:host-and-deploy/iis/protocols>를 참조하세요.

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys)는 Windows에서만 실행되는 ASP.NET Core에 대한 웹 서버입니다. HTTP.sys로 gRPC 서비스를 호스트하려면 .NET 5 및 Windows 10 빌드 20241 이상이 필요합니다.

TLS 및 HTTP/2를 사용하도록 HTTP.sys를 구성해야 합니다. 자세한 내용은 [HTTP.sys 웹 서버 HTTP/2 지원](xref:fundamentals/servers/httpsys#http2-support)을 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel)은 ASP.NET Core의 플랫폼 간 웹 서버입니다. Kestrel은 최상의 성능과 메모리 사용률을 제공하지만 포트 공유와 같은 HTTP.sys의 고급 기능 중 일부를 제공하지 않습니다.

Kestrel gRPC 엔드포인트:

* HTTP/2가 필요합니다.
* [TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)로 보호해야 합니다.

### <a name="http2"></a>HTTP/2

gRPC에는 HTTP/2가 필요합니다. ASP.NET Core용 gRPC는 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)이 `HTTP/2`인지 확인합니다.

Kestrel은 대부분의 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel#http2-support)합니다. Kestrel 엔드포인트는 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원하도록 구성됩니다.

### <a name="tls"></a>TLS

gRPC에 사용되는 Kestrel 엔드포인트는 TLS로 보호해야 합니다. 개발에서는 ASP.NET Core 개발 인증서가 있을 경우 TLS로 보호된 엔드포인트가 `https://localhost:5001`에 자동으로 생성됩니다. 구성이 필요하지 않습니다. `https` 접두사는 Kestrel 엔드포인트가 TLS를 사용하는지 확인합니다.

프로덕션에서는 TLS를 명시적으로 구성해야 합니다. 다음 *appsettings.json* 예제에서는 TLS로 보호되는 HTTP/2 엔드포인트가 제공됩니다.

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

또는 *Program.cs* 에서 Kestrel 엔드포인트를 구성할 수 있습니다.

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>프로토콜 협상

TLS는 통신 보호 이상의 용도로 사용됩니다. TLS [ALPN](https://tools.ietf.org/html/rfc7301#section-3)(Application-Layer Protocol Negotiation) 핸드셰이크는 엔드포인트가 여러 프로토콜을 지원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상하는 데 사용됩니다. 이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용할 것인지를 결정합니다.

TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다. 협상이 없기 때문에 여러 프로토콜이 있는 엔드포인트(예: `HttpProtocols.Http1AndHttp2`)는 TLS 없이 사용할 수 없습니다. 안전하지 않은 엔드포인트에 대한 모든 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.

Kestrel을 통해 HTTP/2 및 TLS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.

> [!NOTE]
> macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다. macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다. 자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core API와 통합

gRPC 서비스는 [DI](xref:fundamentals/dependency-injection)(종속성 주입), [로깅](xref:fundamentals/logging/index) 등의 ASP.NET Core 기능에 대한 모든 권한이 있습니다. 예를 들어 서비스 구현에서 생성자를 통해 DI 컨테이너의 로거 서비스를 확인할 수 있습니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

기본적으로 gRPC 서비스 구현은 수명(Singleton, 범위 지정 또는 임시)을 사용하는 다른 DI 서비스를 확인할 수 있습니다.

### <a name="resolve-httpcontext-in-grpc-methods"></a>gRPC 메서드의 HttpContext 확인

gRPC API는 메서드, 호스트, 헤더, 트레일러 등의 일부 HTTP/2 메시지 데이터에 대한 액세스를 제공합니다. 각 gRPC 메서드에 전달된 `ServerCallContext` 인수를 통해 액세스할 수 있습니다.

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`는 모든 ASP.NET API의 `HttpContext`에 대한 모든 권한을 제공하지 않습니다. `GetHttpContext` 확장 메서드는 ASP.NET API의 기본 HTTP/2 메시지를 나타내는 `HttpContext`에 대한 모든 권한을 제공합니다.

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
