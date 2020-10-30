---
title: ASP.NET Core 권한 부여 소개
author: rick-anderson
description: 권한 부여의 기본 사항과 ASP.NET Core apps에서 권한 부여가 작동 하는 방식에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060250"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core 권한 부여 소개

<a name="security-authorization-introduction"></a>

권한 부여는 사용자가 수행할 수 있는 작업을 결정 하는 프로세스를 나타냅니다. 예를 들어 관리자가 문서 라이브러리를 만들고, 문서를 추가 하 고, 문서를 편집 하 고, 삭제할 수 있습니다. 라이브러리를 사용 하는 관리자가 아닌 사용자는 문서를 읽을 수 있는 권한만 부여 됩니다.

권한 부여는 직교 하 고 인증과 독립적입니다. 그러나 권한 부여에는 인증 메커니즘이 필요 합니다. 인증은 사용자를 확인 하는 프로세스입니다. 인증으로 현재 사용자에 대해 하나 이상의 ID가 만들어질 수 있습니다.

ASP.NET Core의 인증에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authentication/index> .

## <a name="authorization-types"></a>권한 부여 유형

ASP.NET Core 권한 부여는 간단한 선언적 [역할과](xref:security/authorization/roles) 풍부한 [정책 기반](xref:security/authorization/policies) 모델을 제공 합니다. 권한 부여는 요구 사항으로 표현 되며, 처리기는 요구 사항에 대 한 사용자의 클레임을 평가 합니다. 명령적 검사는 사용자가 액세스 하려고 하는 리소스의 사용자 id와 속성을 모두 평가 하는 간단한 정책 또는 정책을 기반으로 할 수 있습니다.

## <a name="namespaces"></a>네임스페이스

및 특성을 비롯 한 권한 부여 구성 요소 `AuthorizeAttribute` `AllowAnonymousAttribute` 는 `Microsoft.AspNetCore.Authorization` 네임 스페이스에 있습니다.

[간단한 권한 부여](xref:security/authorization/simple)에 대 한 설명서를 참조 하세요.
