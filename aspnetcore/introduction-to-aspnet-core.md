---
title: ASP.NET Core 소개
author: rick-anderson
description: 최신 클라우드 사용 인터넷 연결 앱을 빌드하기 위한 플랫폼 간 고성능 오픈 소스 프레임워크인 ASP.NET Core를 소개합니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: 3e41336d084e25319f8b1ab4c4ab3175b758d23d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588804"
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 소개

작성자: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 최신 클라우드 사용 인터넷 연결 앱을 빌드하기 위한 플랫폼 간 고성능 [오픈 소스](https://github.com/dotnet/aspnetcore) 프레임워크입니다. ASP.NET Core를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

* 웹앱 및 서비스, [IoT(사물 인터넷)](https://www.microsoft.com/internet-of-things/) 앱 및 모바일 백 엔드를 빌드할 수 있습니다.
* Windows, macOS 및 Linux에서 선호하는 개발 도구를 사용할 수 있습니다.
* 클라우드 또는 온-프레미스에 배포할 수 있습니다.
* [.NET Core](/dotnet/core/introduction)에서 실행합니다.

## <a name="why-choose-aspnet-core"></a>ASP.NET Core를 선택해야 하는 이유

수백만 명의 개발자가 [ASP.NET 4.x](/aspnet/overview)를 사용하여 웹앱을 만들었고 지금도 만들고 있습니다. ASP.NET Core는 더 간결하고 모듈화된 프레임워크를 만드는 아키텍처 변경 내용이 포함된 ASP.NET 4.x의 재설계입니다.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC를 사용하여 웹 API 및 웹 UI 빌드

ASP.NET Core MVC는 [Web API](xref:tutorials/first-web-api) 및 [웹앱](xref:tutorials/razor-pages/index)을 만들기 위한 기능을 제공합니다.

* [MVC(모델-뷰-컨트롤러) 패턴](xref:mvc/overview)을 통해 웹 API 및 웹앱을 테스트 가능하게 합니다.
* [Razor Pages](xref:razor-pages/index)는 웹 UI를 쉽게 빌드하고 생산성을 높일 수 있는 페이지 기반 프로그래밍 모델입니다.
* [Razor 태그](xref:mvc/views/razor)는 [Razor Pages](xref:razor-pages/index) 및 [MVC 뷰](xref:mvc/views/overview)를 위한 생산적인 구문을 제공합니다.
* [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.
* [다양한 데이터 형식 및 콘텐츠 협상](xref:web-api/advanced/formatting)에 대한 기본 제공 지원을 통해 웹 API를 브라우저 및 모바일 디바이스를 포함한 다양한 클라이언트에 연결할 수 있습니다.
* [모델 바인딩](xref:mvc/models/model-binding)은 HTTP 요청의 데이터를 작업 메서드 매개 변수에 자동으로 매핑합니다.
* [모델 유효성 검사](xref:mvc/models/validation)는 클라이언트 쪽 및 서버 쪽 유효성 검사를 자동으로 수행합니다.

## <a name="client-side-development"></a>클라이언트 쪽 개발

ASP.NET Core는 [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) 및 [부트스트랩](https://getbootstrap.com/) 등 유명한 클라이언트 쪽 프레임워크 및 라이브러리와 원활하게 통합됩니다. 자세한 내용은 <xref:blazor/index> 및 ‘클라이언트 쪽 개발’의 관련 항목을 참조하세요.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core 대상 프레임워크

ASP.NET Core 3.x 이상에서는 .NET Core만 대상으로 지정할 수 있습니다. 일반적으로 ASP.NET Core는 [.NET Standard](/dotnet/standard/net-standard) 라이브러리로 구성됩니다. .NET Standard 2.0으로 작성된 라이브러리는 [.NET Standard 2.0을 구현하는 모든 .NET 플랫폼](/dotnet/standard/net-standard#net-implementation-support)에서 실행됩니다.

.NET Core를 대상으로 지정하면 여러 이점이 있으며 이러한 이점은 각 릴리스마다 늘어나고 있습니다. .NET Framework에 비해 .NET Core를 사용하여 얻을 수 있는 몇 가지 이점은 다음과 같습니다.

* 플랫폼 간 사용 가능. Windows, macOS 및 Linux에서 실행됩니다.
* 향상된 성능
* [Side-by-side 버전 관리](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* 새로운 API
* 오픈 소스

## <a name="recommended-learning-path"></a>권장되는 학습 경로

ASP.NET Core 앱 개발을 소개하는 자습서는 다음의 순서대로 살펴보는 것이 좋습니다.

1. 개발하거나 유지 관리하려는 앱 형식에 대한 자습서를 살펴보세요.

   |앱 형식  |시나리오  |자습서  |
   |----------|----------|----------|
   |웹앱                   | 새로운 서버 쪽 웹 UI 개발 |[Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) |
   |웹앱                   | MVC 앱 유지 관리 |[MVC 시작](xref:tutorials/first-mvc-app/start-mvc)|
   |웹앱                   | 클라이언트 쪽 웹 UI 개발 |[Blazor 시작하기](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |Web API                   | RESTful HTTP 서비스 |[웹 API 만들기](xref:tutorials/first-web-api)&dagger; |
   |원격 프로시저 호출 앱 | 프로토콜 버퍼를 사용하는 계약 중심 서비스 |[gRPC 서비스 시작](xref:tutorials/grpc/grpc-start) |
   |실시간 앱             | 서버 및 연결된 클라이언트 간의 양방향 통신 |[SignalR 시작하기](xref:tutorials/signalr) |

1. 기본 데이터 액세스를 수행하는 방법을 보여 주는 자습서를 살펴보세요.

   |시나리오  |자습서  |
   |----------|----------|
   |새로운 개발        |[Entity Framework Core를 사용한 Razor Pages](xref:data/ef-rp/intro) |
   |MVC 앱 유지 관리 |[Entity Framework Core를 사용한 MVC](xref:data/ef-mvc/intro) |

1. 모든 앱 형식에 적용되는 ASP.NET Core [기본 사항](xref:fundamentals/index)의 개요를 참고하세요.

1. 관심 있는 다른 항목은 목차를 찾아보세요.

&dagger;[대화형 웹 API 자습서](/learn/modules/build-web-api-net-core)도 있습니다. 개발 도구를 로컬에 설치할 필요가 없습니다. 해당 코드는 브라우저로 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에서 실행되고, 테스트에는 [curl](https://curl.haxx.se/)이 사용됩니다.

## <a name="migrate-from-net-framework"></a>.NET Framework에서 마이그레이션

ASP.NET 4.x 앱을 ASP.NET Core로 마이그레이션하기 위한 참조 가이드는 <xref:migration/proper-to-2x/index> 문서를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core는 최신 클라우드 사용 인터넷 연결 앱을 빌드하기 위한 플랫폼 간 고성능 [오픈 소스](https://github.com/dotnet/aspnetcore) 프레임워크입니다. ASP.NET Core를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

* 웹앱 및 서비스, [IoT(사물 인터넷)](https://www.microsoft.com/internet-of-things/) 앱 및 모바일 백 엔드를 빌드할 수 있습니다.
* Windows, macOS 및 Linux에서 선호하는 개발 도구를 사용할 수 있습니다.
* 클라우드 또는 온-프레미스에 배포할 수 있습니다.
* [.NET Core 또는.NET Framework](/dotnet/articles/standard/choosing-core-framework-server)에서 실행할 수 있습니다.

## <a name="why-choose-aspnet-core"></a>ASP.NET Core를 선택해야 하는 이유

수백만 명의 개발자가 [ASP.NET 4.x](/aspnet/overview)를 사용하여 웹앱을 만들었고 지금도 만들고 있습니다. ASP.NET Core는 보다 간결하고 모듈화된 프레임워크를 만드는 아키텍처 변경 내용이 담긴 ASP.NET 4.x의 재설계입니다.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC를 사용하여 웹 API 및 웹 UI 빌드

ASP.NET Core MVC는 [Web API](xref:tutorials/first-web-api) 및 [웹앱](xref:tutorials/razor-pages/index)을 만들기 위한 기능을 제공합니다.

* [MVC(모델-뷰-컨트롤러) 패턴](xref:mvc/overview)을 통해 웹 API 및 웹앱을 테스트 가능하게 합니다.
* [Razor Pages](xref:razor-pages/index)는 웹 UI를 쉽게 빌드하고 생산성을 높일 수 있는 페이지 기반 프로그래밍 모델입니다.
* [Razor 태그](xref:mvc/views/razor)는 [Razor Pages](xref:razor-pages/index) 및 [MVC 뷰](xref:mvc/views/overview)를 위한 생산적인 구문을 제공합니다.
* [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.
* [다양한 데이터 형식 및 콘텐츠 협상](xref:web-api/advanced/formatting)에 대한 기본 제공 지원을 통해 웹 API를 브라우저 및 모바일 디바이스를 포함한 다양한 클라이언트에 연결할 수 있습니다.
* [모델 바인딩](xref:mvc/models/model-binding)은 HTTP 요청의 데이터를 작업 메서드 매개 변수에 자동으로 매핑합니다.
* [모델 유효성 검사](xref:mvc/models/validation)는 클라이언트 쪽 및 서버 쪽 유효성 검사를 자동으로 수행합니다.

## <a name="client-side-development"></a>클라이언트 쪽 개발

ASP.NET Core는 [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) 및 [부트스트랩](https://getbootstrap.com/) 등 유명한 클라이언트 쪽 프레임워크 및 라이브러리와 원활하게 통합됩니다. 자세한 내용은 <xref:blazor/index> 및 ‘클라이언트 쪽 개발’의 관련 항목을 참조하세요.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core 대상 .NET Framework

ASP.NET Core 2.x는 .NET Core 또는 .NET Framework를 대상으로 지정할 수 있습니다. .NET Framework를 대상으로 지정한 ASP.NET Core 앱은 플랫폼 간 교차 사용이 불가능하며&mdash;Windows에서만 실행됩니다. 일반적으로 ASP.NET Core 2.x는 [.NET 표준](/dotnet/standard/net-standard) 라이브러리로 구성됩니다. .NET Standard 2.0으로 작성된 라이브러리는 [.NET Standard 2.0을 구현하는 모든 .NET 플랫폼](/dotnet/standard/net-standard#net-implementation-support)에서 실행됩니다.

ASP.NET Core 2.x는 .NET Standard 2.0을 구현하는 .NET Framework 버전에서 지원됩니다.

* .NET Framework 최신 버전을 사용하는 것이 좋습니다.
* .NET Framework 4.6.1 이상

ASP.NET Core 3.0 이상은 .NET Core에서만 실행됩니다. 이 변경 사항에 대한 자세한 내용은 [ASP.NET Core 3.0에 도입되는 변경 사항 개요](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)를 참조하세요.

.NET Core를 대상으로 지정하면 여러 이점이 있으며 이러한 이점은 각 릴리스마다 늘어나고 있습니다. .NET Framework에 비해 .NET Core를 사용하여 얻을 수 있는 몇 가지 이점은 다음과 같습니다.

* 플랫폼 간 사용 가능. macOS, Linux 및 Windows에서 실행됩니다.
* 향상된 성능
* [Side-by-side 버전 관리](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* 새로운 API
* 오픈 소스

.NET Framework와 .NET Core 사이의 API 격차를 없애기 위해 [Windows 호환 기능 팩](/dotnet/core/porting/windows-compat-pack)을 통해 수천 개의 Windows 전용 API를 .NET Core에서도 사용할 수 있습니다. 이러한 API는 .NET Core 1.x에서는 사용할 수 없습니다.

## <a name="recommended-learning-path"></a>권장되는 학습 경로

ASP.NET Core 앱 개발을 소개하는 자습서 및 문서는 다음의 순서대로 살펴보는 것이 좋습니다.

1. 개발하거나 유지 관리하려는 앱 형식에 대한 자습서를 살펴보세요.

   |앱 형식  |시나리오  |자습서  |
   |----------|----------|----------|
   |웹앱                   | 새로운 개발의 경우        |[Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) |
   |웹앱                   | MVC 앱을 유지 관리하는 경우 |[MVC 시작](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API                   |                            |[웹 API 만들기](xref:tutorials/first-web-api)&dagger; |
   |실시간 앱             |                            |[SignalR 시작하기](xref:tutorials/signalr) |

1. 기본 데이터 액세스를 수행하는 방법을 보여 주는 자습서를 살펴보세요.

   |시나리오  |자습서  |
   |----------|----------|
   | 새로운 개발의 경우        |[Entity Framework Core를 사용한 Razor Pages](xref:data/ef-rp/intro) |
   | MVC 앱을 유지 관리하는 경우 |[Entity Framework Core를 사용한 MVC](xref:data/ef-mvc/intro) |

1. 모든 앱 형식에 적용되는 ASP.NET Core [기본 사항](xref:fundamentals/index)의 개요를 참고하세요.

1. 관심 있는 다른 항목은 목차를 찾아보세요.

&dagger;로컬 IDE를 설치할 필요 없이, [브라우저에서 모든 과정을 따라해볼 수 있는 웹 API 자습서](/learn/modules/build-web-api-net-core)가 제공됩니다. 해당 코드는 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에서 실행되고, 테스트에는 [curl](https://curl.haxx.se/)이 사용됩니다.

## <a name="migrate-from-net-framework"></a>.NET Framework에서 마이그레이션

ASP.NET 앱을 ASP.NET Core로 마이그레이션하기 위한 참조 가이드는 <xref:migration/proper-to-2x/index>를 참조하세요.

::: moniker-end

## <a name="how-to-download-a-sample"></a>예제 다운로드 방법

대부분의 문서 및 자습서에는 예제 코드에 대한 링크가 포함되어 있습니다.

1. [ASP.NET 리포지토리 zip 파일을 다운로드합니다](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/main).
1. *Docs-main.zip* 파일의 압축을 풉니다.
1. 예제 링크의 URL을 참고하여 예제 디렉터리로 이동할 수 있습니다.

### <a name="preprocessor-directives-in-sample-code"></a>예제 코드의 전처리기 지시문

다양한 시나리오를 보여주기 위해 예제 앱은 `#define` 및 `#if-#else/#elif-#endif` 전처리기 지시문을 사용하여 예제 코드의 서로 다른 섹션을 선택적으로 컴파일하고 실행합니다. 이 방법을 사용하는 예제의 경우 C# 파일 상단에 위치한 `#define` 지시문을 설정하여 실행할 시나리오와 연결된 기호를 정의합니다. 일부 예제는 시나리오를 실행하기 위해 여러 파일의 맨 상단에 기호를 정의해야 합니다.

예를 들어, 다음 `#define` 기호 목록은 네 가지 시나리오를 사용할 수 있음을 나타냅니다(기호당 하나의 시나리오). 현재 예제 구성에서는 `TemplateCode` 시나리오를 실행합니다.

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

`ExpandDefault` 시나리오를 실행하도록 예제를 변경하려면 `ExpandDefault` 기호를 정의하고 나머지 기호는 주석으로 처리하세요.

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

[C# 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/)을 사용하여 코드 섹션을 선택적으로 컴파일하는 방법에 대한 자세한 내용은 [#define(C# 참조)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) 및 [#if(C# 참조)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)를 참조하세요.

### <a name="regions-in-sample-code"></a>예제 코드의 지역

일부 예제 앱에는 [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) 및 [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# 지시문으로 둘러싸인 코드 섹션이 포함되어 있습니다. 설명서 빌드 시스템은 렌더링된 설명서 토픽에 이러한 지역을 삽입합니다.  

일반적으로 지역 이름에는 "snippet"이라는 단어가 포함됩니다. 다음 예제는 `snippet_WebHostDefaults`라는 지역을 보여줍니다.

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

이 C# 코드 조각은 다음 줄을 포함하고 있는 항목의 마크다운 파일에서 참조됩니다.

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

코드를 둘러싸고 있는 `#region` 및 `#endregion` 지시문은 안전하게 무시(또는 제거)할 수 있습니다. 항목에 설명된 예제 시나리오를 실행하려는 경우 이러한 지시문 내에서 코드를 변경하지 마세요. 다른 시나리오를 실험하는 경우 자유롭게 코드를 변경할 수 있습니다.

자세한 내용은 [ASP.NET 설명서에 참여: 코드 조각](https://github.com/dotnet/AspNetCore.Docs/blob/main/CONTRIBUTING.md#code-snippets)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [ASP.NET Core 기본 사항](xref:fundamentals/index)
* [주간 ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)은 팀의 진행 상태 및 계획을 다룹니다. 새로운 블로그 및 타사 소프트웨어도 소개합니다.
