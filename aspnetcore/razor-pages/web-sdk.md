---
title: ASP.NET Core 웹 SDK
author: Rick-Anderson
description: Microsoft.NET.Sdk.Web의 개요
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059756"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core 웹 SDK

### <a name="overview"></a>개요

`Microsoft.NET.Sdk.Web`은 ASP.NET Core 앱을 빌드하기 위한 [MSBuild 프로젝트 SDK](/visualstudio/msbuild/how-to-use-project-sdk)입니다. 이 SDK를 사용하지 않고 ASP.NET Core 앱을 빌드할 수는 있지만 웹 SDK에는 다음이 적용됩니다.

* 최고 수준의 환경을 제공하도록 조정되었습니다.
* 대부분의 사용자에게 권장되는 대상입니다.

프로젝트에서 웹 SDK를 사용합니다.

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

웹 SDK를 사용하여 다음과 같은 기능이 사용하도록 설정됩니다.

* .NET Core 3.0 이상을 대상으로 하는 프로젝트는 암시적으로 다음을 참조합니다.

  * [ASP.NET Core 공유 프레임워크](xref:fundamentals/metapackage-app)
  * ASP.NET Core 앱을 빌드하기 위해 디자인된 [분석기](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)
* 웹 SDK는 게시 프로필을 사용하도록 설정하고 WebDeploy를 사용하여 게시하도록 하는 MSBuild 대상을 가져옵니다.

### <a name="properties"></a>속성

| 속성 | 설명 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | `Microsoft.AspNetCore.App` 공유 프레임워크에 대한 암시적 참조를 사용하지 않도록 설정합니다. |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core 분석기에 대한 암시적 참조를 사용하지 않도록 설정합니다. |
| `DisableImplicitComponentsAnalyzers` | Blazor(서버) 애플리케이션을 빌드할 때 Razor 구성 요소 분석기에 대한 암시적 참조를 사용하지 않도록 설정합니다. |