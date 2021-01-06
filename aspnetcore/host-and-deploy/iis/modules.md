---
title: IIS 모듈 및 ASP.NET Core
author: rick-anderson
description: ASP.NET Core 앱용 활성 및 비활성 IIS 모듈과 IIS 모듈을 관리하는 방법을 살펴봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057403"
---
# <a name="iis-modules-with-aspnet-core"></a>IIS 모듈 및 ASP.NET Core

일부 네이티브 IIS 모듈과 모든 IIS 관리 모듈은 ASP.NET Core 앱에 대한 요청을 처리할 수 없습니다. 대부분의 경우 ASP.NET Core는 IIS 네이티브 및 관리 모듈에서 처리되는 시나리오의 대안을 제공합니다.

## <a name="native-modules"></a>네이티브 모듈

이 표는 ASP.NET Core 앱 및 ASP.NET Core 모듈에서 작동하는 네이티브 IIS 모듈을 나타냅니다.

| Module | ASP.NET Core 앱 기능 | ASP.NET Core 옵션 |
| --- | :---: | --- |
| **익명 인증**<br>`AnonymousAuthenticationModule`                                  | 예 | |
| **기본 인증**<br>`BasicAuthenticationModule`                                          | 예 | |
| **클라이언트 인증 매핑 인증**<br>`CertificateMappingAuthenticationModule`      | 예 | |
| **CGI**<br>`CgiModule`                                                                           | 아니요  | |
| **구성 유효성 검사**<br>`ConfigurationValidationModule`                                  | 예 | |
| **HTTP 오류**<br>`CustomErrorModule`                                                           | 아니요  | [상태 코드 페이지 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) |
| **사용자 지정 로깅**<br>`CustomLoggingModule`                                                      | 예 | |
| **기본 문서**<br>`DefaultDocumentModule`                                                  | 아니요  | [기본 파일 미들웨어](xref:fundamentals/static-files#serve-a-default-document) |
| **다이제스트 인증**<br>`DigestAuthenticationModule`                                        | 예 | |
| **디렉터리 검색**<br>`DirectoryListingModule`                                               | 아니요  | [디렉터리 검색 미들웨어](xref:fundamentals/static-files#enable-directory-browsing) |
| **동적 압축**<br>`DynamicCompressionModule`                                            | 예 | [응답 압축 미들웨어](xref:performance/response-compression) |
| **실패한 요청 추적**<br>`FailedRequestsTracingModule`                                     | 예 | [ASP.NET Core 로깅](xref:fundamentals/logging/index#tracesource-provider) |
| **파일 캐싱**<br>`FileCacheModule`                                                            | 아니요  | [응답 캐싱 미들웨어](xref:performance/caching/middleware) |
| **HTTP 캐싱**<br>`HttpCacheModule`                                                            | 아니요  | [응답 캐싱 미들웨어](xref:performance/caching/middleware) |
| **HTTP 로깅**<br>`HttpLoggingModule`                                                          | 예 | [ASP.NET Core 로깅](xref:fundamentals/logging/index) |
| **HTTP 리디렉션**<br>`HttpRedirectionModule`                                                  | 예 | [URL 재작성 미들웨어](xref:fundamentals/url-rewriting) |
| **HTTP 추적**<br>`TracingModule`                                                              | 예 | |
| **IIS 클라이언트 인증서 매핑 인증**<br>`IISCertificateMappingAuthenticationModule` | 예 | |
| **IP 및 도메인 제한**<br>`IpRestrictionModule`                                          | 예 | |
| **ISAPI 필터**<br>`IsapiFilterModule`                                                         | 예 | [미들웨어](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | 예 | [미들웨어](xref:fundamentals/middleware/index) |
| **프로토콜 지원**<br>`ProtocolSupportModule`                                                  | 예 | |
| **요청 필터링**<br>`RequestFilteringModule`                                                | 예 | [URL 재작성 미들웨어`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **요청 모니터**<br>`RequestMonitorModule`                                                    | 예 | |
| **URL 재작성**&#8224;<br>`RewriteModule`                                                      | 예 | [URL 재작성 미들웨어](xref:fundamentals/url-rewriting) |
| **서버 쪽 포함**<br>`ServerSideIncludeModule`                                            | 아니요  | |
| **정적 압축**<br>`StaticCompressionModule`                                              | 아니요  | [응답 압축 미들웨어](xref:performance/response-compression) |
| **정적 콘텐츠**<br>`StaticFileModule`                                                         | 아니요  | [정적 파일 미들웨어](xref:fundamentals/static-files) |
| **토큰 캐싱**<br>`TokenCacheModule`                                                          | 예 | |
| **URI 캐싱**<br>`UriCacheModule`                                                              | 예 | |
| **URL 권한 부여**<br>`UrlAuthorizationModule`                                                | 예 | [ASP.NET Core Identity](xref:security/authentication/identity) |
| **Windows 인증**<br>`WindowsAuthenticationModule`                                      | 예 | |

&#8224;URL 재작성 모듈의 `isFile` 및 `isDirectory` 일치 유형이 [디렉터리 구조](xref:host-and-deploy/directory-structure)의 변경으로 인해 ASP.NET Core 앱에서 작동하지 않습니다.

## <a name="managed-modules"></a>관리 모듈

앱 풀의 .NET CLR 버전이 **관리 코드 없음** 으로 설정된 경우 관리 모듈은 호스트된 ASP.NET Core 앱에서 작동하지 ‘않습니다’. ASP.NET Core는 여러 경우에 미들웨어 대체 방법을 제공합니다.

| Module                  | ASP.NET Core 옵션 |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Cookie 인증 미들웨어](xref:security/authentication/cookie) |
| OutputCache             | [응답 캐싱 미들웨어](xref:performance/caching/middleware) |
| 프로필                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| 세션                 | [세션 미들웨어](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL 재작성 미들웨어](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS 관리자 애플리케이션 변경 내용

IIS 관리자를 사용하여 설정을 구성하는 경우 앱의 *web.config* 파일이 변경됩니다. 앱을 배포하고 *web.config* 를 포함하는 경우 IIS 관리자를 사용하여 변경한 모든 내용을 배포된 *web.config* 파일이 덮어씁니다. 서버의 *web.config* 파일을 변경하는 경우 서버의 업데이트된 *web.config* 파일을 로컬 프로젝트에 즉시 복사합니다.

## <a name="disabling-iis-modules"></a>IIS 모듈 사용 안 함

IIS 모듈이 앱에 대해 사용되지 않아야 하는 서버 수준에서 구성된 경우 앱의 *web.config* 파일에 추가하여 모듈을 사용하지 않도록 설정할 수 있습니다. 모듈을 제자리에 두고 구성 설정(사용 가능한 경우)을 사용하여 모듈을 비활성화하거나 앱에서 모듈을 제거합니다.

### <a name="module-deactivation"></a>모듈 비활성화

많은 모듈에서는 앱에서 모듈을 제거하지 않고도 사용하지 않도록 설정할 수 있는 구성 설정을 제공합니다. 이는 모듈을 비활성화하는 가장 간단하고 가장 빠른 방법입니다. 예를 들어 HTTP 리디렉션 모듈은 *web.config* 의 `<httpRedirect>` 요소로 사용하지 않도록 설정할 수 있습니다.

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

구성 설정을 사용하여 모듈을 사용하지 않도록 설정하는 방법에 관한 자세한 내용은 [IIS \<system.webServer>](/iis/configuration/system.webServer/)의 ‘자식 요소’ 섹션에 있는 링크를 참조하세요.

### <a name="module-removal"></a>모듈 제거

*web.config* 에서 설정과 함께 모듈을 제거하도록 선택한 경우 먼저 모듈을 잠금 해제하고 *web.config* 의 `<modules>` 섹션을 작금 해제합니다.

1. 서버 수준에서 모듈의 잠금을 해제합니다. IIS 관리자 **연결** 사이드바에서 IIS 서버를 선택합니다. **IIS** 영역에서 **모듈** 을 엽니다. 목록에서 모듈을 선택합니다. 오른쪽의 **작업** 사이드바에서 **잠금 해제** 를 선택합니다. 모듈에 대한 작업 항목이 **잠금** 으로 나타나면 모듈이 이미 잠금 해제되어 있으며 작업이 필요하지 않습니다. 나중에 *web.config* 에서 제거하려고 계획한 만큼 모듈을 잠금 해제합니다.

2. *web.config* 의 `<modules>` 섹션 없이 앱을 배포합니다. IIS 관리자에서 먼저 섹션을 잠금 해제하지 않고 `<modules>` 섹션이 포함된 *web.config* 를 사용하여 앱을 배포하면 Configuration Manager에서 섹션을 잠금 해제하려고 할 때 예외가 throw됩니다. 따라서 `<modules>` 섹션 없이 앱을 배포합니다.

3. *web.config* 의 `<modules>` 섹션을 잠금 해제합니다. **연결** 사이드바의 **사이트** 에서 웹 사이트를 선택합니다. **관리** 영역에서 **구성 편집기** 를 엽니다. 탐색 컨트롤을 사용하여 `system.webServer/modules` 섹션을 선택합니다. 오른쪽의 **작업** 사이드바에서 섹션을 **잠금 해제** 하도록 선택합니다. 모듈 섹션에 대한 작업 항목이 **섹션 잠금** 으로 나타나면 모듈 섹션이 이미 잠금 해제되어 있으며 작업이 필요하지 않습니다.

4. `<modules>` 섹션을 `<remove>` 요소가 포함된 앱의 로컬 *web.config* 파일에 추가하여 앱에서 모듈을 제거합니다. 여러 `<remove>` 요소를 추가하여 여러 모듈을 제거합니다. 서버에서 *web.config* 가 변경되면 로컬에서 프로젝트의 *web.config* 파일에 동일한 변경 내용이 즉시 적용됩니다. 이 방법을 사용하여 모듈을 제거해도 서버의 다른 앱과 함께 모듈을 사용하는 데 영향을 주지 않습니다.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*web.config* 를 사용하여 IIS Express용 모듈을 추가 또는 제거하려면 *applicationHost.config* 를 수정하여 `<modules>` 섹션을 잠금 해제합니다.

1. *{APPLICATION ROOT}\\.vs\config\applicationhost.config* 를 엽니다.

1. IIS 모듈에 대한 `<section>` 요소를 찾고 `overrideModeDefault`를 `Deny`에서 `Allow`로 변경합니다.

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` 섹션을 찾습니다. 제거하려는 모듈의 경우 `lockItem`을 `true`에서 `false`로 설정합니다. 다음 예제에서는 CGI 모듈의 잠금이 해제됩니다.

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>` 섹션과 개별 모듈의 잠금이 해제된 후에는 IIS Express에서 앱을 실행하기 위한 앱의 *web.config* 파일을 사용하여 IIS 모듈을 추가하거나 제거할 수 있습니다.

IIS 모듈을 *Appcmd.exe* 를 사용하여 제거할 수도 있습니다. 다음 명령에 `MODULE_NAME` 및 `APPLICATION_NAME`을 제공합니다.

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

예를 들어 기본 웹 사이트에서 `DynamicCompressionModule`을 제거합니다.

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>최소 모듈 구성

ASP.NET Core 앱을 실행하는 데 필요한 유일한 모듈은 익명 인증 모듈 및 ASP.NET Core 모듈입니다.

URI 캐싱 모듈(`UriCacheModule`)을 통해 IIS가 URL 수준에서 웹 사이트 구성을 캐시할 수 있습니다. 이 모듈이 없으면 동일한 URL이 반복적으로 요청되더라도 IIS는 요청될 때마다 구성을 읽고 구문 분석해야 합니다. 요청될 때마다 구성을 구문 분석하면 성능이 크게 저하됩니다. ‘URI 캐싱 모듈은 호스트된 ASP.NET Core 앱을 실행하는 데 꼭 필요하지는 않지만, 모든 ASP.NET Core 배포에 대해 URI 캐싱 모듈을 사용하도록 설정하는 것이 좋습니다.’

HTTP 캐싱 모듈(`HttpCacheModule`)은 IIS 출력 캐시 및 HTTP.sys 캐시에 있는 항목을 캐시하기 위한 논리를 구현합니다. 이 모듈이 없으면 콘텐츠가 커널 모드에서 더 이상 캐시되지 않으며 캐시 프로필이 무시됩니다. 일반적으로 HTTP 캐싱 모듈을 제거하면 성능 및 리소스 사용에 부정적인 영향을 줍니다. ‘HTTP 캐싱 모듈은 호스트된 ASP.NET Core 앱을 실행하는 데 꼭 필요하지는 않지만, 모든 ASP.NET Core 배포에 대해 HTTP 캐싱 모듈을 사용하도록 설정하는 것이 좋습니다.’

## <a name="additional-resources"></a>추가 자료

* [IIS 아키텍처 소개: IIS의 모듈](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS 모듈 개요](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7.0 역할 및 모듈 사용자 지정](/previous-versions/tn-archive/cc627313(v=technet.10))
* [IIS \<system.webServer>](/iis/configuration/system.webServer/)