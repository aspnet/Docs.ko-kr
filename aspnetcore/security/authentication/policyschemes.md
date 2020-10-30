---
title: ASP.NET Core의 정책 스키마
author: rick-anderson
description: 인증 정책 스키마를 사용 하면 단일 논리 인증 체계를 보다 쉽게 수행할 수 있습니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053230"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core의 정책 스키마

인증 정책 스키마를 사용 하면 단일 논리 인증 체계에서 잠재적으로 여러 방법을 사용할 수 있습니다. 예를 들어 정책 체계는 챌린지에 대해 Google 인증을 사용 하 고 cookie 다른 모든 항목에 대 한 인증을 사용할 수 있습니다. 인증 정책 스키마는 다음과 같습니다.

* 다른 체계에 인증 작업을 쉽게 전달할 수 있습니다.
* 요청에 따라 동적으로 전달 합니다.

파생 된 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)를 사용 하는 모든 인증 스키마는 다음과 같습니다.

* 는 ASP.NET Core 2.1 이상에서 자동으로 정책 스키마를 구성 합니다.
* 구성표의 옵션을 구성 하 여 사용할 수 있습니다.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>예

다음 예에서는 하위 수준 스키마를 조합 하는 더 높은 수준의 스키마를 보여 줍니다. Google 인증은 챌린지에 사용 되며, cookie 다른 모든 항목에 대해 인증이 사용 됩니다.

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

다음 예에서는 요청당 스키마를 동적으로 선택할 수 있습니다. 즉, 및 API 인증을 혼합 하는 방법 cookie :

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
