---
title: ASP.NET Core의 GDPR (일반 데이터 보호 규정) 지원
author: rick-anderson
description: ASP.NET Core 웹 앱에서 GDPR 확장 점에 액세스 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051007"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core의 GDPR (EU 일반 데이터 보호 규정) 지원

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core은 [GDPR (EU 일반 데이터 보호 규정)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) 요구 사항을 충족 하는 데 도움이 되는 api 및 템플릿을 제공 합니다.

::: moniker range=">= aspnetcore-3.0"

* 프로젝트 템플릿에는 개인 정보 및 정책 사용으로 대체할 수 있는 확장 지점과 스텁 태그가 포함 됩니다 cookie .
* *페이지/개인 정보* 보호 페이지 또는 *보기/홈/개인 정보. cshtml* 보기는 사이트의 개인 정보 취급 방침에 대 한 세부 정보를 제공 하는 페이지를 제공 합니다.

cookieASP.NET Core 3.0 템플릿에서 생성 된 앱의 ASP.NET Core 2.2 템플릿에 있는 것과 같은 기본 동의 기능을 사용 하도록 설정 하려면 다음을 수행 합니다.

* `using Microsoft.AspNetCore.Http`Using 지시문 목록에를 추가 합니다.
* [ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 를에 추가 하 `Startup.ConfigureServices` 고 [ Cookie 정책을 사용](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 하 여 `Startup.Configure` 다음을 수행 합니다.

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* cookie *_Layout. cshtml* 파일에 동의 부분을 추가 합니다.

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* *\_ Cookie ConsentPartial* 파일을 프로젝트에 추가 합니다.

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 승인 기능에 대해 알아보려면이 문서의 ASP.NET Core 2.2 버전을 선택 합니다 cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* 프로젝트 템플릿에는 개인 정보 및 정책 사용으로 대체할 수 있는 확장 지점과 스텁 태그가 포함 됩니다 cookie .
* cookie승인 기능을 사용 하면 개인 정보를 저장 하기 위해 사용자의 동의를 요청 하 고 추적할 수 있습니다. 사용자가 데이터 수집에 동의한 하지 않고 응용 프로그램의 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) 가로 설정 된 경우에는 `true` 필수가 아닌가 cookie 브라우저에 전송 되지 않습니다.
* Cookie는 필수로 표시 될 수 있습니다. cookie사용자가 동의한 하지 않고 추적을 사용할 수 없는 경우에도 필수가 브라우저에 전송 됩니다.
* [TempData 및 Session cookie ](#tempdata) 은 추적을 사용 하지 않도록 설정 된 경우 작동 하지 않습니다.
* [ Identity 관리](#pd) 페이지에서는 사용자 데이터를 다운로드 하 고 삭제할 수 있는 링크를 제공 합니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 을 사용 하면 ASP.NET Core 2.1 템플릿에 추가 된 GDPR 확장 지점과 api를 대부분 테스트할 수 있습니다. 테스트 지침은 [추가 정보](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 파일을 참조 하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>템플릿 생성 코드에서 GDPR 지원 ASP.NET Core

Razor 프로젝트 템플릿을 사용 하 여 만든 페이지 및 MVC 프로젝트에는 다음과 같은 GDPR 지원이 포함 됩니다.

* [ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 및 [Use Cookie Policy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 는 클래스에 설정 되어 있습니다 `Startup` .
* *\_ Cookie ConsentPartial* [부분 뷰입니다](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). **수락** 단추가이 파일에 포함 됩니다. 사용자가 **동의** 단추를 클릭 하면 저장소에 대 한 동의가 cookie 제공 됩니다.
* *페이지/개인 정보* 보호 페이지 또는 *보기/홈/개인 정보. cshtml* 보기는 사이트의 개인 정보 취급 방침에 대 한 세부 정보를 제공 하는 페이지를 제공 합니다. *\_ Cookie ConsentPartial* 파일은 개인 정보 페이지에 대 한 링크를 생성 합니다.
* 개별 사용자 계정을 사용 하 여 만든 앱의 경우 관리 페이지에 [개인 사용자 데이터](#pd)를 다운로드 하 고 삭제할 수 있는 링크가 제공 됩니다.

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions 및 사용 Cookie 정책

[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 는에서 초기화 됩니다 `Startup.ConfigureServices` .

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[사용 Cookie 정책은](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 에서 호출 됩니다 `Startup.Configure` .

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial 부분 보기

*\_ Cookie ConsentPartial* 부분 보기:

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

이 부분:

* 사용자에 대 한 추적 상태를 가져옵니다. 앱이 동의를 요구 하도록 구성 된 경우 사용자는를 추적할 수 있도록 먼저 동의 해야 합니다 cookie . 동의가 필요한 경우에는 cookie *\_ Layout* 파일에 의해 생성 된 탐색 모음 위에서 승인 패널을 고정 합니다.
* `<p>`개인 정보를 요약 하 고 정책을 사용 하는 HTML 요소를 제공 cookie 합니다.
* 사이트의 개인 정보 취급 방침에 대 한 세부 정보를 볼 수 있는 개인 정보 페이지 또는 보기에 대 한 링크를 제공 합니다.

## <a name="essential-no-loccookies"></a>필수 cookie s

저장소에 대 한 동의가 cookie 제공 되지 cookie 않은 경우 필수로 표시 된만 브라우저로 전송 됩니다. 다음 코드는 필수를 만듭니다 cookie .

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>TempData 공급자 및 세션 상태는 cookie 필수가 아닙니다.

[TempData 공급자](xref:fundamentals/app-state#tempdata) 는 필수적이 지 cookie 않습니다. 추적을 사용 하지 않도록 설정 하면 TempData 공급자가 작동 하지 않습니다. 추적이 사용 하지 않도록 설정 된 경우 TempData 공급자를 사용 하도록 설정 하려면 다음과 같이 TempData를 cookie 필수로 표시 합니다 `Startup.ConfigureServices` .

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[세션 상태](xref:fundamentals/app-state) cookie 는 필수적이 지 않습니다. 추적을 사용 하지 않도록 설정 하면 세션 상태가 작동 하지 않습니다. 다음 코드에서는 세션을 필수로 설정 합니다 cookie .

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>개인 데이터

개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 앱은 개인 데이터를 다운로드 하 고 삭제 하는 코드를 포함 합니다.

사용자 이름을 선택 하 고 **개인 데이터** 를 선택 합니다.

![개인 데이터 관리 페이지](gdpr/_static/pd.png)

참고:

* 코드를 생성 하려면 `Account/Manage` [스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity)를 참조 하세요.
* **삭제** 및 **다운로드** 링크는 기본 id 데이터에 대해서만 작동 합니다. 사용자 지정 사용자 데이터를 삭제/다운로드 하려면 사용자 지정 사용자 데이터를 만드는 앱을 확장 해야 합니다. 자세한 내용은 [사용자 지정 사용자 데이터 Identity 추가, 다운로드 및 삭제 ](xref:security/authentication/add-user-data)를 참조 하세요.
* Identity `AspNetUserTokens` [외래 키](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)로 인해 cascade delete 동작을 통해 사용자를 삭제 하면 데이터베이스 테이블에 저장 된 사용자에 대해 저장 된 토큰이 삭제 됩니다.
* Facebook, Google 등의 [외부 공급자 인증은](xref:security/authentication/social/index)정책을 수락 하기 전에 사용할 수 없습니다 cookie .

::: moniker-end

## <a name="encryption-at-rest"></a>미사용 암호화

일부 데이터베이스 및 저장소 메커니즘은 미사용 암호화를 허용 합니다. 저장 데이터 암호화:

* 저장 된 데이터를 자동으로 암호화 합니다.
* 데이터에 액세스 하는 소프트웨어에 대해 구성, 프로그래밍 또는 기타 작업을 수행 하지 않고 암호화 합니다.
* 는 가장 쉽고 안전한 옵션입니다.
* 데이터베이스에서 키와 암호화를 관리할 수 있습니다.

다음은 그 예입니다.

* Microsoft SQL 및 Azure SQL은 tde ( [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) )를 제공 합니다.
* [SQL Azure는 기본적으로 데이터베이스를 암호화 합니다.](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure blob, 파일, 테이블 및 Queue Storage는 기본적으로 암호화 됩니다](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

미사용 기본 암호화를 제공 하지 않는 데이터베이스의 경우 디스크 암호화를 사용 하 여 동일한 보호를 제공할 수 있습니다. 다음은 그 예입니다.

* [Windows Server 용 BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>추가 리소스

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-ASP.NET Core에서 해지 동의 단추를 추가 하는 중](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
