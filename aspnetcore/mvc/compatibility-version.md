---
title: ASP.NET Core MVC에 대한 호환성 버전
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051293"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>ASP.NET Core MVC에 대한 호환성 버전

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드는 ASP.NET Core 3.0 앱에 대해 작동하지 않습니다. 즉, <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion>의 아무 값으로나 `SetCompatibilityVersion`을 호출해도 해당 애플리케이션에는 영향이 없습니다.

* ASP.NET Core의 다음 부 버전은 새 `CompatibilityVersion` 값을 제공할 수 있습니다.
* `CompatibilityVersion` 값 `Version_2_0`~`Version_2_2`은 `[Obsolete(...)]`로 표시됩니다.
* [위조 방지, CORS, 진단, Mvc 및 라우팅 내에서 주요 API 변경 내용](https://github.com/aspnet/Announcements/issues/387)을 참조하세요. 이 목록에는 호환성 스위치에 대한 주요 변경 내용이 포함되어 있습니다.

`SetCompatibilityVersion`이 ASP.NET Core 2.x 앱과 어떻게 작동하는지 확인하려면 [이 문서의 ASP.NET Core 2.2 버전](?view=aspnetcore-2.2)을 확인하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 또는 2.2에서 도입된 주요 동작 변경 내용을 ASP.NET Core 2.x 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다. 이 주요 동작 변경 내용은 일반적으로 MVC 하위 시스템의 작동 방식과 런타임에서 **코드** 를 호출하는 방법과 관련이 있습니다. 옵트인을 하면 ASP.NET Core의 최신 동작과 장기 동작을 가져올 수 있습니다.

다음 코드는 호환성 모드를 ASP.NET Core 2.2로 설정합니다.

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

최신 버전(`CompatibilityVersion.Latest`)을 사용하여 앱을 테스트하는 것이 좋습니다. 대부분의 앱은 최신 버전을 사용하여 주요 동작을 변경하지 않을 것으로 예상합니다.

`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 앱은 ASP.NET Core 2.1/2.2 MVC 버전에서 도입된 주요 동작 변경으로부터 보호됩니다. 이 보호:

* 2.1 이상의 모든 변경 내용에 적용되지는 않으며, MVC 하위 시스템의 주요 ASP.NET Core 런타임 동작 변경을 대상으로 합니다.
* ASP.NET Core 3.0으로 확장되지 않습니다.

`SetCompatibilityVersion`을 호출하지 **않는** ASP.NET Core 2.1 및 2.2 앱의 기본 호환성은 2.0 호환성입니다. 즉, `SetCompatibilityVersion`을 호출하지 않는 것은 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 것과 같습니다.

다음 코드는 다음 동작을 제외하고, 호환성 모드를 ASP.NET Core 2.2로 설정합니다.

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

적절한 호환성 스위치를 사용하여 주요 동작 변경 내용이 발생하는 앱의 경우:

* 최신 릴리스를 사용하고 특정 주요 동작 변경 내용을 옵트아웃(opt out)할 수 있습니다.
* 최신 변경 내용과 함께 작동하도록 앱을 업데이트할 시간을 제공합니다.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions> 설명서에는 변경된 내용과 변경 내용이 대부분의 사용자에게 향상된 기능인 이유가 잘 설명되어 있습니다.

ASP.NET Core 3.0에서는 호환성 스위치가 지원하는 이전 동작이 제거되었습니다. 거의 모든 사용자에게 혜택을 주는 긍정적인 변화라고 생각합니다. 이러한 변경 내용을 2.1 및 2.2에 도입하여 대부분의 앱은 혜택을 얻을 수 있는 반면, 다른 앱은 업데이트할 시간이 있습니다.
::: moniker-end