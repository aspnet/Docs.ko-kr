---
title: ClaimsPrincipal에서 마이그레이션
author: mjrousos
description: ASP.NET Core에서 현재 인증 된 사용자의 id 및 클레임을 검색 하기 위해 ClaimsPrincipal에서 다른 곳으로 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059769"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal에서 마이그레이션

ASP.NET 4.x 프로젝트에서는 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) 를 사용 하 여 현재 인증 된 사용자의 id 및 클레임을 검색 하는 것이 일반적 이었습니다. ASP.NET Core에서이 속성은 더 이상 설정 되지 않습니다. 이에 종속 된 코드는 다른 수단을 통해 현재 인증 된 사용자의 id를 가져오기 위해 업데이트 해야 합니다.

## <a name="context-specific-state-instead-of-static-state"></a>정적 상태 대신 컨텍스트별 상태

ASP.NET Core를 사용 하는 경우 및의 값이 모두 `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` 설정 되지 않습니다. 이러한 속성은 모두 정적 상태를 나타내므로 일반적으로 ASP.NET Core 하지 않습니다. 대신 ASP.NET Core는 DI ( [종속성 주입](xref:fundamentals/dependency-injection) )를 사용 하 여 현재 사용자의 id와 같은 종속성을 제공 합니다. 테스트 id를 쉽게 삽입할 수 있으므로 DI에서 현재 사용자의 id를 가져오는 것은 더 쉽게 테스트할 수 있습니다.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core 앱에서 현재 사용자 검색

현재 인증 된 ASP.NET Core 사용자를 검색 하는 데 사용할 수 있는 몇 가지 옵션은 `ClaimsPrincipal` `ClaimsPrincipal.Current` 다음과 같습니다.

* **Controllerbase. User** . MVC 컨트롤러는 [사용자](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) 속성을 사용 하 여 현재 인증 된 사용자에 액세스할 수 있습니다.
* **HttpContext** . 현재에 대 한 액세스 권한이 있는 구성 요소 `HttpContext` (예: 미들웨어)는 `ClaimsPrincipal` [httpcontext.current](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)에서 현재 사용자를 가져올 수 있습니다.
* **호출자에 게 전달** 됩니다. 현재에 대 한 액세스 권한이 없는 라이브러리 `HttpContext` 는 종종 컨트롤러 또는 미들웨어 구성 요소에서 호출 되며 현재 사용자의 id를 인수로 전달할 수 있습니다.
* **IHttpContextAccessor** . ASP.NET Core로 마이그레이션되는 프로젝트가 너무 커서 현재 사용자의 id를 필요한 모든 위치로 쉽게 전달할 수 없습니다. 이러한 경우에는 [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 를 해결 방법으로 사용할 수 있습니다. `IHttpContextAccessor` 현재에 액세스할 수 `HttpContext` 있습니다 (있는 경우). DI를 사용 하는 경우를 참조 <xref:fundamentals/httpcontext> 하세요. ASP.NET Core의 DI 기반 아키텍처와 함께 작동 하도록 아직 업데이트 되지 않은 코드에서 현재 사용자의 id를 얻는 단기 솔루션은 다음과 같습니다.

  * `IHttpContextAccessor`에서 [Addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) 를 호출 하 여 DI 컨테이너에서 사용 가능 하도록 설정 `Startup.ConfigureServices` 합니다.
  * `IHttpContextAccessor`시작 하는 동안의 인스턴스를 가져와 정적 변수에 저장 합니다. 이전에 정적 속성에서 현재 사용자를 검색 한 코드에서 인스턴스를 사용할 수 있습니다.
  * 를 사용 하 여 현재 사용자를 검색 `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` 합니다. 이 코드를 HTTP 요청의 컨텍스트 외부에서 사용 하는 경우은 `HttpContext` null입니다.

정적 변수에 저장 된 인스턴스를 사용 하는 최종 옵션은 `IHttpContextAccessor` 삽입 된 종속성을 정적 종속성에 사용 하는 ASP.NET Core 원칙이 반대입니다. `IHttpContextAccessor`대신 DI에서 인스턴스를 검색 하도록 계획 합니다. 그러나를 사용 하는 대량 기존 ASP.NET 앱을 마이그레이션하는 경우 정적 도우미는 유용한 브리지가 될 수 있습니다 `ClaimsPrincipal.Current` .
