---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110120"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core에 대 한 인증 샘플

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[ASP.NET Core 리포지토리에](https://github.com/dotnet/aspnetcore) 는 다음 인증 샘플 (분기)이 포함 되어 있습니다 `main` .

* [클레임 변환](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie 인증은](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [사용자 지정 권한 부여 실패 응답](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [cookie요청에 따라 및 다른 인증 체계 중에서 선택](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>샘플 가져오기 및 실행

이 문서에 제공 된 샘플 링크는 예정 된 ASP.NET Core 릴리스에 대 한 샘플을 제공 합니다. 현재 릴리스 또는 이전 릴리스에 대 한 샘플을 얻으려면 다음 단계를 수행 합니다.

* [ASP.NET Core 리포지토리의](https://github.com/dotnet/aspnetcore)릴리스 분기 ()를 선택 https://github.com/dotnet/aspnetcore) 합니다. 예를 `release/5.0` 들어 분기에 ASP.NET Core 5.0 릴리스에 대 한 샘플이 포함 되어 있습니다.
* ASP.NET Core 리포지토리를 복제 하거나 다운로드 합니다.
* 로컬 시스템에서 ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전의 설치를 확인 합니다.
* 폴더의 샘플로 이동 하 `aspnetcore/src/Security/samples` 고 [ `dotnet run` 명령을](/dotnet/core/tools/dotnet-run)사용 하 여 샘플을 실행 합니다.
