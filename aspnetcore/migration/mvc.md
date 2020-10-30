---
title: ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션하는 방법 알아보기
author: wadepickett
description: ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 마이그레이션하기 시작 하는 방법에 대해 알아봅니다.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051515"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview)로 마이그레이션하기 시작 하는 방법을 보여 줍니다. 이 프로세스에서는 ASP.NET MVC와 관련 된 변경 내용을 강조 표시 합니다.

ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다. 이 문서에서는 다음 내용을 설명합니다.

* 초기 설정.
* 기본 컨트롤러 및 뷰
* 정적 콘텐츠.
* 클라이언트 쪽 종속성.

구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 [ASP.NET Core 구성 마이그레이션](xref:migration/configuration) 및 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>스타터 ASP.NET MVC 프로젝트 만들기

마이그레이션할 Visual Studio의 예제 ASP.NET MVC 프로젝트를 만듭니다.

1. **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
1. **ASP.NET 웹 응용 프로그램 (.NET Framework)** 을 선택 하 고 **다음** 을 선택 합니다.
1. 다음 단계에서 만든 ASP.NET Core 프로젝트와 네임 스페이스가 일치 하도록 프로젝트 이름을 *WebApp1* 로 합니다. **만들기** 를 선택합니다.
1. **MVC** 를 선택 하 고 **만들기** 를 선택 합니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

마이그레이션할 새 ASP.NET Core 프로젝트를 사용 하 여 새 솔루션을 만듭니다.

1. Visual Studio의 두 번째 인스턴스를 시작 합니다.
1. **파일 메뉴** 에서 **새로 만들기** > **프로젝트** 를 선택합니다.
1. **ASP.NET Core 웹 응용 프로그램** 을 선택한 후, **다음** 을 선택합니다.
1. **새 프로젝트 구성** 대화 상자에서 프로젝트 이름을 *WebApp1* 로 설정 합니다.
1. 동일한 프로젝트 이름을 사용 하려면 위치를 이전 프로젝트가 아닌 다른 디렉터리로 설정 합니다. 동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다. **만들기** 를 선택합니다.
1. **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1** 이 선택되었는지 확인합니다. **웹 응용 프로그램 (모델-뷰-컨트롤러)** 프로젝트 템플릿을 선택 하 고 **만들기** 를 선택 합니다.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>MVC를 사용 하도록 ASP.NET Core 사이트 구성

ASP.NET Core 3.0 이상 프로젝트에서 .NET Framework는 더 이상 지원 되는 대상 프레임 워크가 아닙니다. 프로젝트는 .NET Core를 대상으로 해야 합니다. MVC를 포함 하는 ASP.NET Core 공유 프레임 워크는 .NET Core 런타임 설치의 일부입니다. 프로젝트 파일에서 `Microsoft.NET.Sdk.Web` SDK를 사용할 때 공유 프레임워크가 자동으로 참조됩니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

자세한 내용은 [프레임 워크 참조](xref:migration/22-to-30#framework-reference)를 참조 하세요.

ASP.NET Core에서 클래스는 `Startup` 다음과 같습니다.

* *Global.asax* 를 바꿉니다.
* 모든 앱 시작 작업을 처리 합니다.

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

ASP.NET Core 프로젝트에서 *Startup.cs* 파일을 엽니다.

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core 앱은 미들웨어를 사용 하 여 프레임 워크 기능을 옵트인 (opt in) 해야 합니다. 이전 템플릿에서 생성 된 코드는 다음 서비스 및 미들웨어를 추가 합니다.

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>확장 메서드는 컨트롤러, API 관련 기능 및 뷰에 대 한 MVC 서비스 지원을 등록 합니다. MVC 서비스 등록 옵션에 대 한 자세한 내용은 [mvc 서비스 등록](xref:migration/22-to-30#mvc-service-registration) 을 참조 하세요.
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>확장 메서드는 정적 파일 처리기를 추가 합니다 `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`확장 메서드는 이전에 호출 해야 합니다 `UseRouting` . 자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>확장 메서드는 라우팅을 추가 합니다. 자세한 내용은 <xref:fundamentals/routing>를 참조하세요.

이 기존 구성에는 예제 ASP.NET MVC 프로젝트를 마이그레이션하는 데 필요한 항목이 포함 되어 있습니다. 미들웨어 옵션 ASP.NET Core에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>컨트롤러 및 뷰 마이그레이션

ASP.NET Core 프로젝트에서 마이그레이션할 ASP.NET MVC 프로젝트의 컨트롤러 및 뷰 클래스와 동일한 이름을 사용 하 여 자리 표시자 역할을 하는 새로운 빈 컨트롤러 클래스 및 뷰 클래스가 추가 됩니다.

ASP.NET Core *WebApp1* 프로젝트에는 ASP.NET MVC 프로젝트와 동일한 이름의 최소 예제 컨트롤러 및 뷰가 이미 포함 되어 있습니다. ASP.NET MVC 컨트롤러에 대 한 자리 표시자와 ASP.NET MVC *WebApp1* 프로젝트에서 마이그레이션할 뷰를 제공 합니다.

1. ASP.NET MVC에서 메서드를 복사 `HomeController` 하 여 새 ASP.NET Core 메서드를 `HomeController` 대체 합니다. 작업 메서드의 반환 형식을 변경할 필요가 없습니다. ASP.NET MVC 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> . MVC ASP.NET Core 작업 메서드는 대신을 반환 합니다 `IActionResult` . `ActionResult`는 `IActionResult`를 구현합니다.
1. ASP.NET Core 프로젝트에서 *Views/Home* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목** 추가를 선택 합니다.
1. **기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views/Home* 디렉터리로 이동 합니다.
1. *About. cshtml* 및 *Contact* 를 선택 하 고,이 파일을 *인덱스* 를 선택 하 고, Razor **추가** 를 선택 하 고 기존 파일을 바꿉니다.

자세한 내용은 <xref:mvc/controllers/actions> 및 <xref:mvc/views/overview>를 참조하세요.

## <a name="test-each-method"></a>각 메서드 테스트

그러나 각 컨트롤러 끝점을 테스트할 수 있지만 레이아웃 및 스타일은 문서의 뒷부분에서 다룹니다.

1. ASP.NET Core 앱을 실행 합니다.
1. 현재 포트 번호를 ASP.NET Core 프로젝트에 사용 되는 포트 번호로 바꿔서 실행 중인 ASP.NET Core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다. 예: `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>정적 콘텐츠 마이그레이션

ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트 디렉터리에서 호스팅되며 서버 쪽 파일과 혼합 되어 있습니다. ASP.NET Core에서 정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리에 저장 됩니다. 기본 디렉터리는 *{content root}/wwwroot* 이지만 변경할 수 있습니다. 자세한 내용은 [ASP.NET Core의 정적 파일](xref:fundamentals/static-files#serve-static-files)을 참조하세요.

ASP.NET MVC *WebApp1* 프로젝트의 정적 콘텐츠를 ASP.NET Core *WebApp1* 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.

1. ASP.NET Core 프로젝트에서 *wwwroot* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목** 추가를 선택 합니다.
1. **기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트로 이동 합니다.
1. *Favicon* 파일을 선택한 다음 **추가** 를 선택 하 고 기존 파일을 바꿉니다.

## <a name="migrate-the-layout-files"></a>레이아웃 파일 마이그레이션

ASP.NET MVC 프로젝트 레이아웃 파일을 ASP.NET Core 프로젝트에 복사 합니다.

1. ASP.NET Core 프로젝트에서 *Views* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목** 추가를 선택 합니다.
1. **기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views* 디렉터리로 이동 합니다.
1. *_ViewStart* 파일을 선택한 다음 **추가** 를 선택 합니다.

ASP.NET MVC 프로젝트 공유 레이아웃 파일을 ASP.NET Core 프로젝트에 복사 합니다.

1. ASP.NET Core 프로젝트에서 *Views/Shared* 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **Add** > **기존 항목** 추가를 선택 합니다.
1. **기존 항목 추가** 대화 상자에서 ASP.NET MVC *WebApp1* 프로젝트의 *Views/Shared* 디렉터리로 이동 합니다.
1. *_Layout* 파일을 선택한 다음 **추가** 를 선택 하 여 기존 파일을 바꿉니다.

ASP.NET Core 프로젝트에서 *_Layout* 파일을 엽니다. 아래에 표시 된 완료 된 코드와 일치 하도록 다음과 같이 변경 합니다.

아래 완료 된 코드와 일치 하도록 부트스트랩 CSS 포함을 업데이트 합니다.

1. `@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).
1. `@Scripts.Render("~/bundles/modernizr")`를 제거합니다.

부트스트랩 CSS 포함에 대해 완료 된 대체 태그는 다음과 같습니다.

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

다음 완성 된 코드와 일치 하도록 jQuery 및 부트스트랩 JavaScript 포함을 업데이트 합니다.

1. `@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).
1. `@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).

JQuery 및 부트스트랩 JavaScript를 포함 하는 완성 된 대체 태그는 다음과 같습니다.

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

브라우저에서 사이트를 봅니다. 예상 되는 스타일을 사용 하 여 렌더링 해야 합니다.

## <a name="configure-bundling-and-minification"></a>번들링 및 축소 구성하기

ASP.NET Core는 [WebOptimizer](https://github.com/ligershark/WebOptimizer) 및 기타 유사한 라이브러리와 같은 여러 오픈 소스 번들 및 축소 솔루션과 호환 됩니다. ASP.NET Core는 네이티브 묶음 및 축소 솔루션을 제공 하지 않습니다. 묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.

## <a name="solve-http-500-errors"></a>HTTP 500 오류 해결

문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다. 기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 환경에서 *개발* 중일 때 실행 됩니다. 다음 코드에 자세히 설명 되어 있습니다.

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다. 일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다. 자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* <xref:migration/identity>

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview) 2.2로 마이그레이션하기 시작 하는 방법을 보여 줍니다. 이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다. ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다. 이 문서에서는 다음 내용을 설명합니다.

* 초기 설정
* 기본 컨트롤러 및 뷰
* 정적 콘텐츠
* 클라이언트 쪽 종속성.

구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 및을 참조 하십시오 <xref:migration/configuration> <xref:migration/identity> .

> [!NOTE]
> 샘플의 버전 번호가 최신이 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 합니다.

## <a name="create-the-starter-aspnet-mvc-project"></a>스타터 ASP.NET MVC 프로젝트 만들기

업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다. *WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만든 ASP.NET Core 프로젝트와 일치 합니다.

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5* 로 변경 합니다. Visual Studio에서 새 솔루션 이름 ( *Mvc5* )을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 ( *WebApp1* )을 새로 만듭니다. 동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다. 같은 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만듭니다.

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다. 프로젝트 이름을 *WebApp1* 로 하 고 **개별 사용자 계정** 에 대 한 인증 옵션을 선택 합니다. 이 앱의 이름을 *FullAspNetCore* 로 바꿉니다. 이 프로젝트를 만들면 변환 시 시간이 절약 됩니다. 템플릿 생성 코드에서 최종 결과를 볼 수 있습니다. 변환 프로젝트에 코드를 복사 하거나 템플릿 생성 프로젝트와 비교할 수 있습니다.

## <a name="configure-the-site-to-use-mvc"></a>MVC를 사용 하도록 사이트 구성

* .NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다. 이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다. .NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.

`Microsoft.AspNetCore.Mvc` 는 ASP.NET Core MVC 프레임 워크입니다. `Microsoft.AspNetCore.StaticFiles` 는 정적 파일 처리기입니다. ASP.NET Core apps는 정적 파일을 제공 하는 등 미들웨어에 대해 명시적으로 옵트인 합니다. 자세한 내용은 [정적 파일](xref:fundamentals/static-files)을 참조하세요.

* *Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>확장 메서드는 정적 파일 처리기를 추가 합니다. 자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.

## <a name="add-a-controller-and-view"></a>컨트롤러 및 뷰 추가

이 섹션에서는 다음 섹션에서 마이그레이션된 ASP.NET MVC 컨트롤러 및 뷰에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.

* *Controller* 디렉터리를 추가 합니다.

* *HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 디렉터리에 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* *Views* 디렉터리를 추가 합니다.

* *뷰/홈* 디렉터리를 추가 합니다.

* 뷰 */홈* 디렉터리에 *Index. cshtml* 이라는 **Razor 뷰** 를 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/view.png)

프로젝트 구조는 다음과 같습니다.

![WebApp1의 파일 및 디렉터리를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.

```html
<h1>Hello world!</h1>
```

앱을 실행합니다.

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview)를 참조 하세요.

다음 기능을 사용 하려면 예제 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트로 마이그레이션해야 합니다.

* 클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)

* controllers

* 뷰

* 모델

* 묶음

* filters

* 로그인/로그 아웃 합니다 Identity (다음 자습서에서 수행 됨).

## <a name="controllers-and-views"></a>컨트롤러 및 뷰

* ASP.NET MVC의 각 메서드 `HomeController` 를 새로 복사 `HomeController` 합니다. ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다. <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ASP.NET CORE mvc에서는 작업 메서드가 대신을 반환 합니다 `IActionResult` . `ActionResult` 는 `IActionResult` 를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.

* *Contact.cshtml* ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한* 파일을 *복사 합니다.* Razor

## <a name="test-each-method"></a>각 메서드 테스트

레이아웃 파일 및 스타일은 아직 마이그레이션되지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다. 및 뷰에 대해 생성 된 레이아웃 파일 링크를 `About` `Contact` 아직 사용할 수 없습니다.

현재 포트 번호를 ASP.NET core 프로젝트에서 사용 되는 포트 번호로 바꿔 실행 중인 ASP.NET core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다. 예: `https://localhost:44375/home/about`.

![연락처 페이지](mvc/_static/contact-page.png)

스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오. 다음 섹션에서 스타일을 수정 합니다.

## <a name="static-content"></a>정적 콘텐츠

ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과는 결합 되어 있습니다. ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 디렉터리에서 호스팅됩니다. ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다. 이 샘플 변환에서:

* ASP.NET MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.

ASP.NET MVC 프로젝트는 해당 스타일에 [부트스트랩](https://getbootstrap.com/) 을 사용 하 여 *콘텐츠* 및 *스크립트* 디렉터리에 부트스트랩 파일을 저장 합니다. ASP.NET MVC 프로젝트를 생성 한 템플릿은 레이아웃 파일 ( *Views/Shared/_Layout cshtml* )의 부트스트랩을 참조 합니다. *bootstrap.js* 및 *부트스트랩 .css* 파일은 ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 디렉터리로 복사 될 수 있습니다. 대신이 문서는 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.

## <a name="migrate-the-layout-file"></a>레이아웃 파일 마이그레이션

* ASP.NET MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewStart.* s a s 파일을 복사 합니다. *_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.

* *뷰/공유* 디렉터리를 만듭니다.

* *선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewImports* 를 복사 합니다. *_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다. *_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다. 태그 도우미는 새 레이아웃 파일에 사용 됩니다. *_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.

* ASP.NET MVC 프로젝트의 *views/shared* 디렉터리에서 ASP.NET Core 프로젝트의 *views/shared* 디렉터리로 *_Layout cshtml* 파일을 복사 합니다.

*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).

* `@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).

* `@Scripts.Render("~/bundles/modernizr")`를 제거합니다.

* 줄을 주석 `@Html.Partial("_LoginPartial")` 으로 처리 `@*...*@` 합니다. 자세한 내용은 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity) 을 참조 하세요.

* `@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).

* `@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).

부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

브라우저에서 사이트를 봅니다. 이제 올바른 스타일을 적절히 로드 해야 합니다.

* *선택 사항:* 새 레이아웃 파일을 사용해 보세요. *FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사 합니다. 새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.

## <a name="configure-bundling-and-minification"></a>번들링 및 축소 구성하기

묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.

## <a name="solve-http-500-errors"></a>HTTP 500 오류 해결

문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다. 기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 구성이 *개발* 될 때 실행 됩니다. 다음 코드의 예제를 참조 하세요.

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다. 일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다. 자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](xref:mvc/overview) 2.1로 마이그레이션하기 시작 하는 방법을 보여 줍니다. 이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다. ASP.NET MVC에서 마이그레이션하는 과정은 여러 단계로 진행 됩니다. 이 문서에서는 다음 내용을 설명합니다.

* 초기 설정
* 기본 컨트롤러 및 뷰
* 정적 콘텐츠
* 클라이언트 쪽 종속성.

구성 및 코드 마이그레이션에 대 한 자세한 Identity 내용은 [ASP.NET Core 구성 마이그레이션](xref:migration/configuration) 및 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity)을 참조 하세요.

> [!NOTE]
> 샘플의 버전 번호가 최신이 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 합니다.

## <a name="create-the-starter-aspnet-mvc-project"></a>스타터 ASP.NET MVC 프로젝트 만들기

업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다. *WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만든 ASP.NET Core 프로젝트와 일치 합니다.

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5* 로 변경 합니다. Visual Studio에서 새 솔루션 이름 ( *Mvc5* )을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 ( *WebApp1* )을 새로 만듭니다. 동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다. 같은 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만듭니다.

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다. 프로젝트 이름을 *WebApp1* 로 하 고 **개별 사용자 계정** 에 대 한 인증 옵션을 선택 합니다. 이 앱의 이름을 *FullAspNetCore* 로 바꿉니다. 이 프로젝트를 만들면 변환 시 시간이 절약 됩니다. 템플릿 생성 코드에서 최종 결과를 볼 수 있습니다. 변환 프로젝트에 코드를 복사 하거나 템플릿 생성 프로젝트와 비교할 수 있습니다.

## <a name="configure-the-site-to-use-mvc"></a>MVC를 사용 하도록 사이트 구성

* .NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다. 이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다. .NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.

`Microsoft.AspNetCore.Mvc` 는 ASP.NET Core MVC 프레임 워크입니다. `Microsoft.AspNetCore.StaticFiles` 는 정적 파일 처리기입니다. ASP.NET Core apps는 정적 파일을 제공 하는 등 미들웨어에 대해 명시적으로 옵트인 합니다. 자세한 내용은 [정적 파일](xref:fundamentals/static-files)을 참조하세요.

* *Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>확장 메서드는 정적 파일 처리기를 추가 합니다. `UseMvc`확장 메서드는 라우팅을 추가 합니다. 자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.

## <a name="add-a-controller-and-view"></a>컨트롤러 및 뷰 추가

이 섹션에서는 다음 섹션에서 마이그레이션된 ASP.NET MVC 컨트롤러 및 뷰에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.

* *Controller* 디렉터리를 추가 합니다.

* *HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 디렉터리에 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* *Views* 디렉터리를 추가 합니다.

* *뷰/홈* 디렉터리를 추가 합니다.

* 뷰 */홈* 디렉터리에 *Index. cshtml* 이라는 **Razor 뷰** 를 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/view.png)

프로젝트 구조는 다음과 같습니다.

![WebApp1의 파일 및 디렉터리를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.

```html
<h1>Hello world!</h1>
```

앱을 실행합니다.

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview)를 참조 하세요.

다음 기능을 사용 하려면 예제 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트로 마이그레이션해야 합니다.

* 클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)

* controllers

* 뷰

* 모델

* 묶음

* filters

* 로그인/로그 아웃 합니다 Identity (다음 자습서에서 수행 됨).

## <a name="controllers-and-views"></a>컨트롤러 및 뷰

* ASP.NET MVC의 각 메서드 `HomeController` 를 새로 복사 `HomeController` 합니다. ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은입니다. <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ASP.NET CORE mvc에서는 작업 메서드가 대신을 반환 합니다 `IActionResult` . `ActionResult` 는 `IActionResult` 를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.

* *Contact.cshtml* ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한* 파일을 *복사 합니다.* Razor

## <a name="test-each-method"></a>각 메서드 테스트

레이아웃 파일 및 스타일은 아직 마이그레이션되지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다. 및 뷰에 대해 생성 된 레이아웃 파일 링크를 `About` `Contact` 아직 사용할 수 없습니다.

* 현재 포트 번호를 ASP.NET core 프로젝트에서 사용 되는 포트 번호로 바꿔 실행 중인 ASP.NET core 앱의 브라우저에서 렌더링 된 뷰를 호출 합니다. 예: `https://localhost:44375/home/about`.

![연락처 페이지](mvc/_static/contact-page.png)

스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오. 다음 섹션에서 스타일을 수정 합니다.

## <a name="static-content"></a>정적 콘텐츠

ASP.NET MVC 5 이전 버전에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과는 결합 되어 있습니다. ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 디렉터리에서 호스팅됩니다. ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다. 이 샘플 변환에서:

* ASP.NET MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 디렉터리에 복사 합니다.

ASP.NET MVC 프로젝트는 해당 스타일에 [부트스트랩](https://getbootstrap.com/) 을 사용 하 여 *콘텐츠* 및 *스크립트* 디렉터리에 부트스트랩 파일을 저장 합니다. ASP.NET MVC 프로젝트를 생성 한 템플릿은 레이아웃 파일 ( *Views/Shared/_Layout cshtml* )의 부트스트랩을 참조 합니다. *bootstrap.js* 및 *부트스트랩 .css* 파일은 ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 디렉터리로 복사 될 수 있습니다. 대신이 문서는 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.

## <a name="migrate-the-layout-file"></a>레이아웃 파일 마이그레이션

* ASP.NET MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewStart.* s a s 파일을 복사 합니다. *_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.

* *뷰/공유* 디렉터리를 만듭니다.

* *선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 디렉터리에서 ASP.NET Core 프로젝트의 *views* 디렉터리에 *_ViewImports* 를 복사 합니다. *_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다. *_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다. 태그 도우미는 새 레이아웃 파일에 사용 됩니다. *_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.

* ASP.NET MVC 프로젝트의 *views/shared* 디렉터리에서 ASP.NET Core 프로젝트의 *views/shared* 디렉터리로 *_Layout cshtml* 파일을 복사 합니다.

*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).

* `@Styles.Render("~/Content/css")` `<link>` *부트스트랩* 을 로드할 요소로 대체 합니다 (아래 참조).

* `@Scripts.Render("~/bundles/modernizr")`를 제거합니다.

* 줄을 주석 `@Html.Partial("_LoginPartial")` 으로 처리 `@*...*@` 합니다. 자세한 내용은 [인증 및 Identity ASP.NET Core 마이그레이션](xref:migration/identity) 을 참조 하세요.

* `@Scripts.Render("~/bundles/jquery")` `<script>` 요소로 대체 합니다 (아래 참조).

* `@Scripts.Render("~/bundles/bootstrap")` `<script>` 요소로 대체 합니다 (아래 참조).

부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

브라우저에서 사이트를 봅니다. 이제 올바른 스타일을 적절히 로드 해야 합니다.

* *선택 사항:* 새 레이아웃 파일을 사용해 보세요. *FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사 합니다. 새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.

## <a name="configure-bundling-and-minification"></a>번들링 및 축소 구성하기

묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](xref:client-side/bundling-and-minification)를 참조 하세요.

## <a name="solve-http-500-errors"></a>HTTP 500 오류 해결

문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 생성 됩니다. 기본적으로 ASP.NET Core 앱에서는 확장이에 `UseDeveloperExceptionPage` 추가 되 `IApplicationBuilder` 고 구성이 *개발* 될 때 실행 됩니다. 다음 코드의 예제를 참조 하세요.

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다. 일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다. 자세한 내용은 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
