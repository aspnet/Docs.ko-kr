---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
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
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060341"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core에 대 한 인증 샘플

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie 인증은](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [cookie요청에 따라 및 다른 인증 체계 중에서 선택](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/dotnet/AspNetCore)를 선택 합니다. 예를 들어 `release/3.1`
* [ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookie 인증은](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [cookie요청에 따라 및 다른 인증 체계 중에서 선택](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/dotnet/AspNetCore)를 선택 합니다. 예를 들어 `release/2.1`
* [ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.

::: moniker-end
