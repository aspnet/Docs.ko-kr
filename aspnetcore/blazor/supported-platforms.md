---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054959"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor 지원 플랫폼

[Luke Latham](https://github.com/guardrex)으로

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly 및 Blazor Server는 다음 표에 나와 있는 브라우저에서 지원됩니다.

| 브라우저                          | 버전         |
| -------------------------------- | --------------- |
| Apple Safari(iOS 포함)      | 현재&dagger; |
| Google Chrome(Android 포함) | 현재&dagger; |
| Microsoft Edge                   | 현재&dagger; |
| Mozilla Firefox                  | 현재&dagger; |  

&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| 브라우저                          | 버전               |
| -------------------------------- | --------------------- |
| Apple Safari(iOS 포함)      | 현재&dagger;       |
| Google Chrome(Android 포함) | 현재&dagger;       |
| Microsoft Edge                   | 현재&dagger;       |
| Microsoft Internet Explorer      | 지원되지 않음&Dagger; |
| Mozilla Firefox                  | 현재&dagger;       |  

&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.  
&Dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.

## Blazor Server

| 브라우저                          | 버전         |
| -------------------------------- | --------------- |
| Apple Safari(iOS 포함)      | 현재&dagger; |
| Google Chrome(Android 포함) | 현재&dagger; |
| Microsoft Edge                   | 현재&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | 현재&dagger; |

&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.  
&Dagger;추가 보충 기능이 필요합니다. 예를 들어 프라미스는 [`Polyfill.io`](https://polyfill.io/v3/) 번들을 통해 추가할 수 있습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
