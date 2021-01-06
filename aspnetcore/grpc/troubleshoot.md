---
title: .NET Core에서 gRPC 문제 해결
author: jamesnk
description: .NET Core에서 gRPC를 사용할 때 발생하는 오류 문제를 해결합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: cbce85caf7ba792253ba62c6be084c8905acd00f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058716"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="506ca-103">.NET Core에서 gRPC 문제 해결</span><span class="sxs-lookup"><span data-stu-id="506ca-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="506ca-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="506ca-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="506ca-105">이 문서에서는 .NET에서 gRPC 앱을 개발할 때 발생하는 일반적인 문제를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="506ca-106">클라이언트와 서비스의 SSL/TLS 구성이 일치하지 않음</span><span class="sxs-lookup"><span data-stu-id="506ca-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="506ca-107">gRPC 템플릿과 샘플은 기본적으로 [TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)를 사용하여 gRPC 서비스를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="506ca-108">gRPC 클라이언트는 보안 연결을 사용하여 안전한 gRPC 서비스를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="506ca-109">앱 시작 시 작성된 로그에서 ASP.NET Core gRPC 서비스가 TLS를 사용하고 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="506ca-110">서비스는 HTTPS 엔드포인트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="506ca-111">.NET Core 클라이언트는 서버 주소에 `https`를 사용하여 보안 연결을 통해 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="506ca-112">모든 gRPC 클라이언트 구현에서 TLS를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="506ca-113">다른 언어의 gRPC 클라이언트에는 일반적으로 `SslCredentials`로 구성된 채널이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="506ca-114">`SslCredentials`는 클라이언트에서 사용할 인증서를 지정하며, 안전하지 않은 자격 증명 대신 이 인증서를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="506ca-115">TLS를 사용하도록 여러 gRPC 클라이언트 구현을 구성하는 예제는 [gRPC 인증](https://www.grpc.io/docs/guides/auth/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="506ca-116">신뢰할 수 없는/잘못된 인증서를 사용하여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="506ca-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="506ca-117">.NET gRPC 클라이언트를 사용하려면 서비스에 신뢰할 수 있는 인증서가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="506ca-118">신뢰할 수 있는 인증서 없이 gRPC 서비스를 호출할 경우 다음 오류 메시지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="506ca-119">처리되지 않은 예외가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-119">Unhandled exception.</span></span> <span data-ttu-id="506ca-120">System.Net.Http.HttpRequestException: SSL 연결을 설정할 수 없습니다. 내부 예외를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="506ca-121">---> System.Security.Authentication.AuthenticationException: 유효성 검사 절차에 따르면 원격 인증서가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="506ca-122">로컬에서 앱을 테스트 중이며 ASP.NET Core HTTPS 개발 인증서를 신뢰할 수 없는 경우 이 오류가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="506ca-123">이 문제의 해결 지침은 [Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="506ca-124">다른 머신에서 gRPC 서비스를 호출 중이며 인증서를 신뢰할 수 없는 경우 잘못된 인증서를 무시하도록 gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="506ca-125">다음 코드는 [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback)을 사용하여 신뢰할 수 있는 인증서 없이 호출을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="506ca-126">신뢰할 수 없는 인증서는 앱 개발 중에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="506ca-127">프로덕션 앱은 항상 유효한 인증서를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="506ca-128">.NET Core 클라이언트를 사용하여 안전하지 않은 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="506ca-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="506ca-129">앱이 .NET Core 3.x를 사용하는 경우 .NET Core 클라이언트로 안전하지 않은 gRPC 서비스를 호출하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="506ca-130">gRPC 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를 `true`로 설정하고 서버 주소에 `http`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="506ca-131">.NET 5 앱에는 추가 구성이 필요하지 않지만 안전하지 않은 gRPC 서비스를 호출하려면 `Grpc.Net.Client` 버전 2.32.0 이상을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="506ca-132">macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음</span><span class="sxs-lookup"><span data-stu-id="506ca-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="506ca-133">Kestrel은 macOS 및 이전 Windows 버전(예: Windows 7)에서 TLS를 사용하는 HTTP/2를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="506ca-134">ASP.NET Core gRPC 템플릿과 샘플은 기본적으로 TLS를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="506ca-135">gRPC 서버를 시작하려고 하면 다음과 같은 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="506ca-136">IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ‘macOS에는 ALPN 지원이 없으므로 TLS를 통한 HTTP/2가 지원되지 않습니다.’</span><span class="sxs-lookup"><span data-stu-id="506ca-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="506ca-137">이 문제를 해결하려면 TLS ‘없이’ HTTP/2를 사용하도록 Kestrel 및 gRPC 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="506ca-138">이 작업은 개발 중에만 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-138">You should only do this during development.</span></span> <span data-ttu-id="506ca-139">TLS를 사용하지 않으면 gRPC 메시지가 암호화되지 않고 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="506ca-140">Kestrel은 *Program.cs* 에서 TLS 없이 HTTP/2 엔드포인트를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="506ca-141">TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="506ca-142">HTTP/2를 협상하려면 TLS가 필요하기 때문에 `HttpProtocols.Http1AndHttp2`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="506ca-143">TLS가 없을 경우 모든 엔드포인트 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="506ca-144">또한 TLS를 사용하지 않도록 gRPC 클라이언트를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-144">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="506ca-145">자세한 내용은 [.NET Core 클라이언트를 사용하여 안전하지 않은 gRPC 서비스 호출](#call-insecure-grpc-services-with-net-core-client)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-145">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="506ca-146">TLS가 없는 HTTP/2는 앱 개발 중에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-146">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="506ca-147">프로덕션 앱은 항상 전송 보안을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-147">Production apps should always use transport security.</span></span> <span data-ttu-id="506ca-148">자세한 내용은 [ASP.NET Core용 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-148">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="506ca-149">gRPC C# 자산은 .proto 파일에서 생성된 코드가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-149">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="506ca-150">구체적인 클라이언트와 서비스 기본 클래스의 gRPC 코드를 생성하려면 프로젝트에서 protobuf 파일 및 도구를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-150">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="506ca-151">다음을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-151">You must include:</span></span>

* <span data-ttu-id="506ca-152">`<Protobuf>` 항목 그룹에서 사용하려는 *.proto* 파일.</span><span class="sxs-lookup"><span data-stu-id="506ca-152">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="506ca-153">[가져온 *.proto* 파일](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)을 프로젝트에서 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-153">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="506ca-154">gRPC 도구 패키지인 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)의 패키지 참조</span><span class="sxs-lookup"><span data-stu-id="506ca-154">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="506ca-155">gRPC C# 자산을 생성하는 방법에 대한 자세한 내용은 <xref:grpc/basics>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="506ca-155">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="506ca-156">gRPC 서비스를 호스트하는 ASP.NET Core 웹앱에는 생성된 서비스 기본 클래스만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-156">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="506ca-157">gRPC 호출을 수행하는 gRPC 클라이언트 앱에는 생성된 구체적인 클라이언트만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-157">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="506ca-158">WPF 프로젝트가 .proto 파일에서 gRPC C# 자산을 생성할 수 없음</span><span class="sxs-lookup"><span data-stu-id="506ca-158">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="506ca-159">WPF 프로젝트에는 gRPC 코드 생성이 제대로 작동하지 않도록 하는 [알려진 문제](https://github.com/dotnet/wpf/issues/810)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-159">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="506ca-160">`Grpc.Tools` 및 *.proto* 파일을 참조하여 WPF 프로젝트에서 생성된 gRPC 형식을 사용하면 컴파일 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-160">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="506ca-161">오류 CS0246: 형식 또는 네임스페이스 이름 ‘MyGrpcServices’를 찾을 수 없습니다. (사용 중인 지시문 또는 어셈블리 참조가 없습니까?)</span><span class="sxs-lookup"><span data-stu-id="506ca-161">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="506ca-162">이 문제는 다음과 같은 방법으로 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-162">You can workaround this issue by:</span></span>

1. <span data-ttu-id="506ca-163">새 .NET Core 클래스 라이브러리 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-163">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="506ca-164">새 프로젝트에서 참조를 추가하여 [ *\*.proto* 파일에서 C# 코드 생성](xref:grpc/basics#generated-c-assets)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-164">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="506ca-165">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-165">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="506ca-166">`<Protobuf>` 항목 그룹에 *\*.proto* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-166">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="506ca-167">WPF 애플리케이션에서 새 프로젝트의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-167">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="506ca-168">WPF 애플리케이션은 새 클래스 라이브러리 프로젝트의 gRPC 생성 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="506ca-168">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
