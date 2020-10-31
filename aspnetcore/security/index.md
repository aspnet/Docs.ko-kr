---
title: ASP.NET Core 보안 개요
author: rick-anderson
description: ASP.NET Core의 인증, 권한 부여 및 보안 기본 사항에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051631"
---
# <a name="overview-of-aspnet-core-security"></a>ASP.NET Core 보안 개요

개발자는 ASP.NET Core를 사용하여 앱의 보안을 간편하게 구성 및 관리할 수 있습니다. ASP.NET Core는 인증, 권한 부여, 데이터 보호, HTTPS 적용, 앱 비밀, XSRF/CSRF 방지 및 CORS 관리를 위한 기능을 포함하고 있습니다. 이러한 보안 기능을 사용하여 강력하면서도 안전한 ASP.NET Core 앱을 빌드할 수 있습니다.

## <a name="aspnet-core-security-features"></a>ASP.NET Core 보안 기능

ASP.NET Core는 기본 제공 ID 공급자를 포함하여 앱을 보호하는 여러 도구와 라이브러리를 제공하지만 Facebook, Twitter, LinkedIn 등의 타사 ID 서비스를 사용할 수도 있습니다. ASP.NET Core를 사용하면 기밀 정보를 코드에 노출할 필요 없이 저장하고 사용할 수 있는 방법인 앱 비밀을 간편하게 관리할 수 있습니다.

## <a name="authentication-vs-authorization"></a>인증 vs. 권한 부여

인증은 사용자가 자격 증명을 제공하면 운영 체제, 데이터베이스, 앱 또는 리소스에 저장된 자격 증명과 비교하는 프로세스입니다. 두 자격 증명이 일치하면 사용자 인증이 성공하고 사용자는 권한 부여 프로세스 동안 권한이 부여된 작업을 수행할 수 있습니다. 권한 부여는 사용자가 할 수 있는 작업을 결정하는 프로세스를 말합니다.

인증을 이해하기 위한 또 다른 개념으로 서버, 데이터베이스, 앱, 리소스 등의 공간에 들어가는 것으로 생각할 수 있고, 권한 부여는 사용자가 해당 공간(서버, 데이터베이스 또는 앱) 내에서 어떤 개체에 대해 어떤 작업을 수행할 수 있는지 결정하는 것입니다.

## <a name="common-vulnerabilities-in-software"></a>소프트웨어의 일반적인 취약점

ASP.NET Core 및 EF는 앱을 보호하고 보안 위반을 방지하는 기능을 포함하고 있습니다. 다음 링크 목록을 선택하면 웹앱의 가장 일반적인 보안 취약점을 방지하는 기술을 구체적으로 설명하는 문서로 이동합니다.

* [XSS(교차 사이트 스크립팅) 공격](xref:security/cross-site-scripting)
* [SQL 삽입 공격](/ef/core/querying/raw-sql)
* [XSRF/CSRF(교차 사이트 요청 위조) 공격](xref:security/anti-request-forgery)
* [오픈 리디렉션 공격](xref:security/preventing-open-redirects)

그 외에도 알고 계셔야 하는 취약점이 더 있습니다. 자세한 내용은 목차의 **보안 및 Identity** 섹션에 있는 다른 문서를 참조하세요.
