---
title: ASP.NET Core Blazor Server 앱 보호
author: guardrex
description: Blazor Server 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 147ebbeb84e1755307d627ef428d92d1b0248c74
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394865"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>ASP.NET Core Blazor Server 앱 보호

Blazor Server 앱은 ASP.NET Core 앱과 동일한 방식으로 보안이 구성됩니다. 자세한 내용은 <xref:security/index>의 문서를 참조하세요. 이 개요의 항목은 Blazor Server에만 적용됩니다.

## <a name="blazor-server-project-template"></a>Blazor Server 프로젝트 템플릿

프로젝트를 만들 때 인증을 위해 [Blazor Server 프로젝트 템플릿](xref:blazor/project-structure)을 구성할 수 있습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/tooling>의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.

**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor Server 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.

다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.

* **인증 없음**
* **개별 사용자 계정**: 사용자 계정은 다음과 같이 저장될 수 있습니다.
  * ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용
* **회사 또는 학교 계정**
* **Windows 인증**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/tooling>의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘 | 설명 |
| ------------------------ | ----------- |
| `None`(기본값)         | 인증 없음 |
| `Individual`             | ASP.NET Core Identity를 사용하여 앱에 저장된 사용자 |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 |
| `SingleOrg`              | 단일 테넌트에 대한 조직 인증 |
| `MultiOrg`               | 여러 테넌트에 대한 조직 인증 |
| `Windows`                | Windows 인증 |

`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.

* 프로젝트의 폴더를 만듭니다.
* 프로젝트의 이름을 지정합니다.

자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. <xref:blazor/tooling>의 Mac용 Visual Studio 지침을 따릅니다.

1. **새 Blazor Server 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.

1. ASP.NET Core Identity를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 사용해 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘 | 설명 |
| ------------------------ | ----------- |
| `None`(기본값)         | 인증 없음 |
| `Individual`             | ASP.NET Core Identity를 사용하여 앱에 저장된 사용자 |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 |
| `SingleOrg`              | 단일 테넌트에 대한 조직 인증 |
| `MultiOrg`               | 여러 테넌트에 대한 조직 인증 |
| `Windows`                | Windows 인증 |

`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.

* 프로젝트의 폴더를 만듭니다.
* 프로젝트의 이름을 지정합니다.

자세한 내용은 다음을 참조하세요.

* .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.
* 명령 셸에서 Blazor Server 템플릿(`blazorserver`)에 대한 도움말 명령을 실행합니다.

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a>스캐폴드 Identity

Blazor Server 프로젝트에 Identity를 스캐폴드합니다.

* [기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="additional-claims-and-tokens-from-external-providers"></a>외부 공급자의 추가 클레임 및 토큰

외부 공급자의 추가 클레임을 저장하려면 <xref:security/authentication/social/additional-claims>을 참조 하십시오.

## <a name="azure-app-service-on-linux-with-identity-server"></a>Identity Server를 사용한 Azure App Service on Linux

Identity Server를 사용하여 Azure App Service on Linux에 배포할 때 발급자를 명시적으로 지정합니다. 자세한 내용은 <xref:security/authentication/identity/spa#azure-app-service-on-linux>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [빠른 시작: Microsoft ID 플랫폼을 사용하여 ASP.NET Core 웹 API 보호](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.
  * 전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.
  * 수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.
