---
title: ASP.NET Core 2.0의 새로운 기능
author: rick-anderson
description: ASP.NET Core 2.0의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
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
uid: aspnetcore-2.0
ms.openlocfilehash: f9fad5a63c76a3b21341a12fd40baafcdf2c9dac
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059730"
---
# <a name="whats-new-in-aspnet-core-20"></a>ASP.NET Core 2.0의 새로운 기능

이 문서에서는 ASP.NET Core 2.0의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages는 더 쉽고 더 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만드는 ASP.NET Core MVC의 새로운 기능입니다.

자세한 내용은 소개 및 자습서를 참조하세요.

* [Razor Pages 소개](xref:razor-pages/index)
* [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core 메타패키지

새로운 ASP.NET Core 메타패키지에는 ASP.NET Core 및 Entity Framework Core 팀에서 만들고 지원하는 모든 패키지와 함께 해당하는 내부 및 타사 종속성이 포함됩니다. 더 이상 패키지별로 개별 ASP.NET Core 기능을 선택할 필요가 없습니다. 모든 기능은 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 패키지에 포함되어 있습니다. 기본 템플릿은 이 패키지를 사용합니다.

자세한 내용은 [ASP.NET Core 2.0에 대한 Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)를 참조하세요.

## <a name="runtime-store"></a>런타임 저장소

`Microsoft.AspNetCore.All` 메타패키지를 사용하는 애플리케이션은 새로운 .NET Core 런타임 저장소를 자동으로 활용합니다. 저장소에는 ASP.NET Core 2.0 애플리케이션을 실행하는 데 필요한 모든 런타임 자산이 포함됩니다. `Microsoft.AspNetCore.All` 메타패키지를 사용할 경우 참조되는 ASP.NET Core NuGet 패키지의 자산은 대상 시스템에 있기 때문에 애플리케이션과 함께 배포되지 않습니다. 또한 애플리케이션 시작 시간을 개선하기 위해 런타임 저장소의 자산은 미리 컴파일됩니다.

자세한 내용은 [런타임 저장소](/dotnet/core/deploying/runtime-store)를 참조하세요.

## <a name="net-standard-20"></a>.NET Standard 2.0

ASP.NET Core 2.0 패키지는 .NET Standard 2.0을 대상으로 합니다. 패키지는 다른 .NET Standard 2.0 라이브러리에서 참조될 수 있고 .NET Core 2.0, .NET Framework 4.6.1 등 .NET의 .NET Standard 2.0 규격 구현에서 실행될 수 있습니다. 

`Microsoft.AspNetCore.All` 메타패키지는 .NET Core 2.0 런타임 저장소와 함께 사용되므로 .NET Core 2.0만 대상으로 합니다.

## <a name="configuration-update"></a>구성 업데이트

`IConfiguration` 인스턴스는 기본적으로 ASP.NET Core 2.0의 서비스 컨테이너에 추가됩니다. 서비스 컨테이너의 `IConfiguration`을 사용하면 애플리케이션이 컨테이너에서 구성 값을 더 쉽게 검색할 수 있습니다.

계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387)(GitHub 문제)를 참조하세요.

## <a name="logging-update"></a>로깅 업데이트

ASP.NET Core 2.0에서 로깅은 기본적으로 DI(종속성 주입) 시스템에 통합되어 있습니다. *Startup.cs* 파일 대신에 *Program.cs* 파일에서 공급자를 추가하고 필터링을 구성합니다. 기본 `ILoggerFactory`는 전체 공급자 필터링 및 특정 공급자 필터링에 둘 다 하나의 유연한 방법을 사용하는 방식으로 필터링을 지원합니다.

자세한 내용은 [로깅 소개](xref:fundamentals/logging/index)를 참조하세요.

## <a name="authentication-update"></a>인증 업데이트

새 인증 모델을 사용하면 DI를 사용하는 애플리케이션에 대한 인증을 더 쉽게 구성할 수 있습니다.

새 템플릿을 사용하여 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)를 통해 웹앱과 웹 API에 대한 인증을 구성할 수 있습니다.

계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054)(GitHub 문제)를 참조하세요.

## <a name="no-locidentity-update"></a>Identity 업데이트

ASP.NET Core 2.0에서는 Identity를 사용하여 보안 Web API를 더 쉽게 빌드할 수 있습니다. [MSAL(Microsoft Authentication Library)](https://www.nuget.org/packages/Microsoft.Identity.Client)을 사용하여 Web API에 액세스하기 위해 액세스 토큰을 얻을 수 있습니다.

2\.0의 인증 변경에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core의 계정 확인 및 암호 복구](xref:security/authentication/accconfirm)
* [ASP.NET Core에서 인증자 앱에 QR 코드 생성 사용](xref:security/authentication/identity-enable-qrcodes)
* [ASP.NET Core 2.0으로 인증 및 Identity 마이그레이션](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>SPA 템플릿

Angular, Aurelia, Knockout.js, React.js 및 React.js에 대한 SPA(단일 페이지 애플리케이션) 프로젝트 템플릿을 사용할 수 있습니다. Angular 템플릿은 Angular 4로 업데이트되었습니다. Angular 및 React 템플릿은 기본적으로 제공됩니다. 다른 템플릿을 가져오는 방법에 대한 자세한 내용은 [새 SPA 프로젝트 만들기](xref:client-side/spa-services#create-a-new-project)를 참조하세요. ASP.NET Core에서 SPA를 빌드하는 방법에 대한 정보는 <xref:client-side/spa-services>를 참조하세요.

## <a name="kestrel-improvements"></a>Kestrel 기능 향상

Kestrel 웹 서버에는 인터넷 연결 서버로서 더 적합하도록 도와주는 새로운 기능이 있습니다. 다양한 서버 제약 조건 구성 옵션이 `KestrelServerOptions` 클래스의 새 `Limits` 속성에 추가됩니다. 다음에 대한 제한을 추가합니다.

* 최대 클라이언트 연결
* 최대 요청 본문 크기
* 최소 요청 본문 데이터 속도

자세한 내용은 [ASP.NET Core의 Kestrel 웹 서버 구현](xref:fundamentals/servers/kestrel)을 참조하세요.

## <a name="weblistener-renamed-to-httpsys"></a>WebListener 이름이 HTTP.sys로 바뀜

패키지 `Microsoft.AspNetCore.Server.WebListener` 및 `Microsoft.Net.Http.Server`가 새 패키지 `Microsoft.AspNetCore.Server.HttpSys`로 병합되었습니다. 네임스페이스가 일치하도록 업데이트되었습니다.

자세한 내용은 [ASP.NET Core의 HTTP.sys 웹 서버 구현](xref:fundamentals/servers/httpsys)을 참조하세요.

## <a name="enhanced-http-header-support"></a>향상된 HTTP 헤더 지원

MVC를 사용하여 `FileStreamResult` 또는 `FileContentResult`를 전송할 경우 이제 전송하는 콘텐츠에서 `ETag` 또는 `LastModified` 날짜를 설정하는 옵션이 제공됩니다. 다음과 비슷한 코드를 사용하여 반환된 콘텐츠에서 이러한 값을 설정할 수 있습니다.

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

방문자에게 반환된 파일이 `ETag` 및 `LastModified` 값에 해당하는 HTTP 헤더를 갖습니다.

애플리케이션 방문자가 범위 요청 헤더가 포함된 콘텐츠를 요청하면 ASP.NET Core는 요청을 인식하고 헤더를 처리합니다. 요청된 콘텐츠가 부분적으로 전달될 수 있으면 ASP.NET Core는 적절히 건너뛰고 요청된 바이트 집합만 반환합니다. 이 기능에 맞게 조정하거나 이 기능을 처리하기 위해 메서드에 특수 처리기를 작성할 필요가 없습니다. 기능이 자동으로 처리됩니다.

## <a name="hosting-startup-and-application-insights"></a>호스팅 시작 및 Application Insights

이제 호스팅 환경에서는 애플리케이션이 명시적으로 종속성을 사용하거나 메서드를 호출할 필요 없이 추가 패키지 종속성을 삽입하고 애플리케이션 시작 중에 코드를 실행할 수 있습니다. 이 기능을 사용하면 애플리케이션이 미리 인식할 필요 없이 특정 환경이 해당 환경에 고유한 기능을 “강화”할 수 있습니다. 

ASP.NET Core 2.0에서 이 기능은 Visual Studio에서 디버그할 경우 및 Azure App Services에서 실행될 경우(옵트인(opt in) 후) 자동으로 Application Insights 진단을 사용하도록 설정하는 데 사용됩니다. 따라서 프로젝트 템플릿은 더 이상 기본적으로 Application Insights 패키지와 코드를 추가하지 않습니다.

계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389)(GitHub 문제)를 참조하세요.

## <a name="automatic-use-of-anti-forgery-tokens"></a>위조 방지 토큰 자동 사용

ASP.NET Core는 언제나 기본적으로 콘텐츠를 HTML 인코딩하는 기능을 제공했지만, 새 버전에서는 XSRF(교차 사이트 요청 위조) 공격을 방지할 수 있도록 추가 조치를 취합니다. 이제 ASP.NET Core는 기본적으로 위조 방지 토큰을 내보내고 추가 구성없이 폼 POST 작업 및 페이지에서 토큰의 유효성을 검사합니다.

자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

## <a name="automatic-precompilation"></a>자동으로 미리 컴파일

Razor 뷰 미리 컴파일이 기본적으로 게시 중에 사용하도록 설정되므로 게시 출력 크기와 애플리케이션 시작 시간이 감소합니다.

자세한 내용은 [ASP.NET Core에서 Razor 뷰 컴파일 및 미리 컴파일](xref:mvc/views/view-compilation)을 참조하세요.

## <a name="no-locrazor-support-for-c-71"></a>C# 7.1의 Razor 지원

Razor 뷰 엔진이 새 Roslyn 컴파일러를 사용하도록 업데이트되었습니다. 여기에는 기본 식, 유추된 튜플 이름 및 제네릭 패턴 일치 같은 C# 7.1 기능에 대한 지원이 포함됩니다. 프로젝트에서 C# 7.1을 사용하려면 프로젝트 파일에 다음 속성을 추가하고 나서 솔루션을 다시 로드합니다.

```xml
<LangVersion>latest</LangVersion>
```

C# 7.1 기능 상태에 대한 자세한 내용은 [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)(Roslyn GitHub 리포지토리)를 참조하세요.

## <a name="other-documentation-updates-for-20"></a>2\.0에 대한 기타 문서 업데이트

* [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles)
* [키 관리](xref:security/data-protection/implementation/key-management)
* [Facebook 인증 구성](xref:security/authentication/facebook-logins)
* [Twitter 인증 구성](xref:security/authentication/twitter-logins)
* [Google 인증 구성](xref:security/authentication/google-logins)
* [Microsoft 계정 인증 구성](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>마이그레이션 지침

ASP.NET Core 1.x 애플리케이션을 ASP.NET Core 2.0으로 마이그레이션하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core 1.x에서 ASP.NET Core 2.0으로 마이그레이션](xref:migration/1x-to-2x/index)
* [ASP.NET Core 2.0으로 인증 및 Identity 마이그레이션](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>추가 정보

전체 변경 내용 목록을 보려면 [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)(ASP.NET Core 2.0 릴리스 정보)를 참조하세요.

ASP.NET Core 개발팀의 진행 상황 및 계획과 연결하려면 [ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)을 시청하세요.
