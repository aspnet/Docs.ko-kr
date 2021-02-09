---
title: .NET의 gRPC에 지원되는 플랫폼
author: jamesnk
description: .NET의 gRPC에 지원되는 플랫폼에 대해 자세히 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530166"
---
# <a name="grpc-on-net-supported-platforms"></a>.NET의 gRPC에 지원되는 플랫폼

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 .NET로 gRPC를 사용하기 위한 요구 사항 및 지원되는 플랫폼에 대해 설명합니다.

gRPC는 HTTP/2에서 사용 가능한 고급 기능을 사용합니다. HTTP/2는 모든 플랫폼에서 지원되지는 않지만 HTTP/1.1을 사용하는 2차 통신 형식은 gRPC에 사용할 수 있습니다.

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2를 통한 gRPC는 gRPC가 사용되는 일반적인 방법입니다.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web은 gRPC 프로토콜을 HTTP/1.1과 호환 가능하도록 수정합니다. gRPC-Web은 더 많은 곳에서 사용될 수 있으며, 특히 브라우저 앱에서 호출할 수 있습니다. 고급 gRPC 기능 2개(클라이언트 스트리밍 및 양방향 스트리밍)는 더이상 지원되지 않습니다.

.NET의 gRPC는 두 가지 통신 형식을 지원하며, HTTP/2를 통한 gRPC가 기본적으로 사용됩니다. gRPC-Web 설정에 대한 자세한 내용은 <xref:grpc/browser>를 참조하세요.

## <a name="device-requirements"></a>디바이스 요구 사항

.NET의 gRPC는 .NET Core가 지원하는 모든 디바이스를 지원합니다.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Browsers&Dagger;

&dagger;[macOS는 HTTPS를 사용하여 ASP.NET Core 앱 호스팅을 지원하지 않습니다](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). macOS의 gRPC 클라이언트는 HTTPS를 사용하는 원격 서비스를 호출할 수 있습니다.

&Dagger;Blazor WebAssembly 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.

## <a name="aspnet-core-server-requirements"></a>ASP.NET Core 서버 요구 사항

gRPC 서비스는 모든 기본 제공 ASP.NET Core 서버에서 호스트될 수 있습니다.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS에는 .NET 5 및 Windows 10 빌드 20241 이상이 필요합니다.

&Dagger;HTTP.sys에는 .NET 5 및 Windows 10 빌드 19529 이상이 필요합니다.

ASP.NET Core 서버가 gRPC를 실행하도록 구성하는 방법에 대한 자세한 내용은 <xref:grpc/aspnetcore#server-options>를 참조하세요.

## <a name="net-version-requirements"></a>.NET 버전 요구 사항

.NET의 gRPC는 .NET Core 3 및 .NET 5 이상을 지원합니다.

> [!div class="checklist"]
>
> * .NET 5 이상
> * .NET Core 3

.NET의 gRPC는 .NET Framework 및 Xamarin에서의 실행을 지원하지 않습니다. [gRPC C# 핵심 라이브러리](https://grpc.io/docs/languages/csharp/quickstart/)는 .NET Framework 및 Xamarin을 지원하는 타사 라이브러리입니다. gRPC C-core는 Microsoft에서 지원되지 않습니다.

## <a name="azure-services"></a>Azure 서비스

> [!div class="checklist"]
>
> * [AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service는 HTTP/2를 통한 gRPC 호스팅을 지원하지 않으며, gRPC-Web이 호환 가능한 대안입니다.

Azure App Service의 HTTP/2에서 gRPC 지원을 개선하기 위한 작업이 진행 중입니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore/issues/9020)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [gRPC C# 핵심 라이브러리](https://grpc.io/docs/languages/csharp/quickstart/)
