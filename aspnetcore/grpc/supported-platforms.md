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
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="f2a27-103">.NET의 gRPC에 지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="f2a27-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="f2a27-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f2a27-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f2a27-105">이 문서에서는 .NET로 gRPC를 사용하기 위한 요구 사항 및 지원되는 플랫폼에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="f2a27-106">gRPC는 HTTP/2에서 사용 가능한 고급 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="f2a27-107">HTTP/2는 모든 플랫폼에서 지원되지는 않지만 HTTP/1.1을 사용하는 2차 통신 형식은 gRPC에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="f2a27-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2를 통한 gRPC는 gRPC가 사용되는 일반적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="f2a27-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web은 gRPC 프로토콜을 HTTP/1.1과 호환 가능하도록 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="f2a27-110">gRPC-Web은 더 많은 곳에서 사용될 수 있으며, 특히 브라우저 앱에서 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="f2a27-111">고급 gRPC 기능 2개(클라이언트 스트리밍 및 양방향 스트리밍)는 더이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="f2a27-112">.NET의 gRPC는 두 가지 통신 형식을 지원하며,</span><span class="sxs-lookup"><span data-stu-id="f2a27-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="f2a27-113">HTTP/2를 통한 gRPC가 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="f2a27-114">gRPC-Web 설정에 대한 자세한 내용은 <xref:grpc/browser>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2a27-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="f2a27-115">디바이스 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f2a27-115">Device requirements</span></span>

<span data-ttu-id="f2a27-116">.NET의 gRPC는 .NET Core가 지원하는 모든 디바이스를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f2a27-117">Windows</span><span class="sxs-lookup"><span data-stu-id="f2a27-117">Windows</span></span>
> * <span data-ttu-id="f2a27-118">Linux</span><span class="sxs-lookup"><span data-stu-id="f2a27-118">Linux</span></span>
> * <span data-ttu-id="f2a27-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="f2a27-119">macOS&dagger;</span></span>
> * <span data-ttu-id="f2a27-120">Browsers&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f2a27-120">Browsers&Dagger;</span></span>

<span data-ttu-id="f2a27-121">&dagger;[macOS는 HTTPS를 사용하여 ASP.NET Core 앱 호스팅을 지원하지 않습니다](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="f2a27-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="f2a27-122">macOS의 gRPC 클라이언트는 HTTPS를 사용하는 원격 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="f2a27-123">&Dagger;Blazor WebAssembly 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="f2a27-124">ASP.NET Core 서버 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f2a27-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="f2a27-125">gRPC 서비스는 모든 기본 제공 ASP.NET Core 서버에서 호스트될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f2a27-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f2a27-126">Kestrel</span></span>
> * <span data-ttu-id="f2a27-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="f2a27-127">TestServer</span></span>
> * <span data-ttu-id="f2a27-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="f2a27-128">IIS&dagger;</span></span>
> * <span data-ttu-id="f2a27-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f2a27-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="f2a27-130">&dagger;IIS에는 .NET 5 및 Windows 10 빌드 20241 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="f2a27-131">&Dagger;HTTP.sys에는 .NET 5 및 Windows 10 빌드 19529 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="f2a27-132">ASP.NET Core 서버가 gRPC를 실행하도록 구성하는 방법에 대한 자세한 내용은 <xref:grpc/aspnetcore#server-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2a27-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="f2a27-133">.NET 버전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f2a27-133">.NET version requirements</span></span>

<span data-ttu-id="f2a27-134">.NET의 gRPC는 .NET Core 3 및 .NET 5 이상을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="f2a27-135">.NET 5 이상</span><span class="sxs-lookup"><span data-stu-id="f2a27-135">.NET 5 or later</span></span>
> * <span data-ttu-id="f2a27-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="f2a27-136">.NET Core 3</span></span>

<span data-ttu-id="f2a27-137">.NET의 gRPC는 .NET Framework 및 Xamarin에서의 실행을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="f2a27-138">[gRPC C# 핵심 라이브러리](https://grpc.io/docs/languages/csharp/quickstart/)는 .NET Framework 및 Xamarin을 지원하는 타사 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="f2a27-139">gRPC C-core는 Microsoft에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="f2a27-140">Azure 서비스</span><span class="sxs-lookup"><span data-stu-id="f2a27-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="f2a27-141">AKS(Azure Kubernetes Service)</span><span class="sxs-lookup"><span data-stu-id="f2a27-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="f2a27-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="f2a27-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="f2a27-143">&dagger;Azure App Service는 HTTP/2를 통한 gRPC 호스팅을 지원하지 않으며,</span><span class="sxs-lookup"><span data-stu-id="f2a27-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="f2a27-144">gRPC-Web이 호환 가능한 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="f2a27-145">Azure App Service의 HTTP/2에서 gRPC 지원을 개선하기 위한 작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="f2a27-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="f2a27-146">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore/issues/9020)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2a27-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2a27-147">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f2a27-147">Additional resources</span></span>

* [<span data-ttu-id="f2a27-148">gRPC C# 핵심 라이브러리</span><span class="sxs-lookup"><span data-stu-id="f2a27-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
