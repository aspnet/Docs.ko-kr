---
title: ASP.NET 4.x와 ASP.NET Core 중에서 선택
author: rick-anderson
description: ASP.NET Core 및 ASP.NET 4.x에 대해 설명하고 둘 중 선택하는 방법을 설명합니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 192784b4f2cb3b80511814de4f777c4a49fc594b
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710739"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>ASP.NET 4.x와 ASP.NET Core 중에서 선택

ASP.NET Core는 ASP.NET 4.x를 새롭게 디자인한 것입니다. 이 문서에 차이점이 나와 있습니다.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core는 Windows, macOS 또는 Linux에서 클라우드 기반 최신 웹앱을 빌드하기 위한 오픈 소스 플랫폼 간 프레임워크입니다.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x는 Windows에서 엔터프라이즈급 서버 기반 웹앱을 빌드할 때 필요한 서비스를 제공하는 완성도 있는 프레임워크입니다.

## <a name="framework-selection"></a>프레임워크 선택 영역

다음 표는 ASP.NET Core를 ASP.NET 4.x와 비교합니다.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Windows, macOS 또는 Linux용 빌드|Windows용 빌드|
|[Razor Pages](xref:razor-pages/index)는 ASP.NET Core 2.x에서 웹 UI를 만드는 좋은 방법입니다. [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api) 및 [SignalR](xref:signalr/introduction)도 참조하세요.|[Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [웹후크](/aspnet/webhooks/) 또는 [웹 페이지](/aspnet/web-pages) 사용|
|컴퓨터당 여러 버전|컴퓨터당 하나의 버전|
|C# 또는 F#을 사용하여 [Visual Studio](https://visualstudio.microsoft.com/vs/), [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/), 또는 [Visual Studio Code](https://code.visualstudio.com/)에서 개방|C#, VB 또는 F#을 사용하여 [Visual Studio](https://visualstudio.microsoft.com/vs/)에서 개발|
|ASP.NET 4.x보다 고성능|성능 양호|
|[Use .NET Core 런타임](/dotnet/standard/choosing-core-framework-server)|.NET Framework 런타임 사용|

.NET Framework의 ASP.NET Core 2.x 지원에 대한 자세한 내용은 [ASP.NET Core 대상 .NET Framework](xref:index#target-framework)를 참조하세요.

## <a name="aspnet-core-scenarios"></a>ASP.NET Core 시나리오

* [웹 사이트](xref:tutorials/first-mvc-app/start-mvc)
* [API](xref:tutorials/first-web-api)
* [실시간](xref:signalr/introduction)
* [Azure에 ASP.NET Core 앱 배포](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x 시나리오

* [웹 사이트](/aspnet/mvc)
* [API](/aspnet/web-api)
* [실시간](/aspnet/signalr)
* [Azure에서 ASP.NET 4.x 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>추가 자료

* [ASP.NET 소개](/aspnet/overview)
* [ASP.NET Core 소개](xref:index)
* <xref:host-and-deploy/azure-apps/index>
