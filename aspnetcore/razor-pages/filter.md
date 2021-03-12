---
title: ASP.NET Core에서 Razor Pages를 위한 필터 메서드
author: Rick-Anderson
description: ASP.NET Core에서 Razor Pages를 위한 필터 메서드를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 178e6348d2d50dae34feea6a0ed261de01037136
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586139"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>ASP.NET Core에서 Razor Pages를 위한 필터 메서드

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 페이지 필터 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0)와 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)는 Razor 페이지 처리기 실행 전후에 Razor Pages에서 코드를 실행하도록 허용합니다. Razor 페이지 필터는 개별 페이지 처리기 메서드에 적용할 수 없다는 점을 제외하고는 [ASP.NET Core MVC 작업 필터](xref:mvc/controllers/filters#action-filters)와 유사합니다.

Razor 페이지 필터:

* 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 코드를 실행합니다.
* 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 코드를 실행합니다.
* 처리기 메서드가 실행된 후 코드를 실행합니다.
* 한 페이지에 또는 전역으로 구현할 수 있습니다.
* 특정 페이지 처리기 메서드에는 적용할 수 없습니다.
* 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워질 수 있습니다. 자세한 내용은 [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) 및 [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute)를 참조하세요.

페이지 생성자 및 미들웨어는 처리기 메서드가 실행되기 전에 사용자 지정 코드를 실행할 수 있지만 Razor 페이지 필터를 통해서만 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 및 페이지에 액세스할 수 있습니다. 미들웨어는 `HttpContext`에 액세스할 수 있지만 "페이지 컨텍스트"에는 액세스할 수 없습니다. 필터에는 `HttpContext`에 대한 액세스를 제공하는 <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 파생 매개 변수가 있습니다. 다음은 페이지 필터의 샘플로, 생성자 또는 미들웨어로 수행할 수 없는 응답에 헤더를 추가하는 [필터 특성을 구현](#ifa)합니다. 페이지의 인스턴스 및 페이지의 모델에 대한 액세스를 포함하는 페이지 컨텍스트에 대한 액세스는 Razor 페이지의 필터, 처리기 또는 본문을 실행하는 경우에만 사용할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

Razor 페이지 필터는 전역 또는 페이지 수준에서 적용할 수 있는 다음과 같은 메서드를 제공합니다.

* 동기 메서드:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 호출됩니다.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 호출됩니다.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): 처리기 메서드가 실행된 후 작업 결과 전에 호출됩니다.

* 비동기 메서드:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 비동기적으로 호출됩니다.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 호출되기 전에 비동기적으로 호출됩니다.

필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지** 만 구현하세요. 프레임워크는 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출합니다. 두 인터페이스가 구현되는 경우 비동기 메서드만 호출됩니다. 페이지의 재정의에 동일한 규칙이 적용되며, 재정의의 동기 또는 비동기 버전 중 하나만 구현합니다.

## <a name="implement-razor-page-filters-globally"></a>Razor 페이지 필터를 전역으로 구현

다음 코드는 `IAsyncPageFilter`를 구현합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

위의 코드에서 `ProcessUserAgent.Write`는 사용자 에이전트 문자열에 작동하는 사용자 제공 코드입니다.

다음 코드는 `Startup` 클래스의 `SampleAsyncPageFilter`를 활성화합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 호출하여 `SampleAsyncPageFilter`를 */Movies* 의 페이지에만 적용합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

다음 코드는 동기 `IPageFilter`를 구현합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

다음 코드는 `SamplePageFilter`를 활성화합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>필터 메서드를 재정의하여 Razor 페이지 필터 구현

다음 코드는 비동기 Razor 페이지 필터를 재정의합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>필터 특성 구현

기본 제공 특성 기반 필터 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> 필터는 서브클래싱할 수 있습니다. 다음 필터는 응답에 헤더를 추가합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

다음 코드는 `AddHeader` 특성을 적용합니다.

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

브라우저 개발자 도구와 같은 도구를 사용하여 헤더를 검사합니다. **응답 헤더** 에 `author: Rick`이 표시됩니다.

순서 재정의에 대한 지침은 [기본 순서 재정의](xref:mvc/controllers/filters#overriding-the-default-order)를 참조하세요.

필터에서 필터 파이프라인을 단락(short-circuit)시키는 지침은 [취소 및 단락](xref:mvc/controllers/filters#cancellation-and-short-circuiting)을 참조하세요.

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>권한 부여 필터 특성

[권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 특성은 `PageModel`에 적용될 수 있습니다.

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 페이지 필터 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0)와 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)는 Razor 페이지 처리기 실행 전후에 Razor Pages에서 코드를 실행하도록 허용합니다. Razor 페이지 필터는 개별 페이지 처리기 메서드에 적용할 수 없다는 점을 제외하고는 [ASP.NET Core MVC 작업 필터](xref:mvc/controllers/filters#action-filters)와 유사합니다.

Razor 페이지 필터:

* 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 코드를 실행합니다.
* 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 코드를 실행합니다.
* 처리기 메서드가 실행된 후 코드를 실행합니다.
* 한 페이지에 또는 전역으로 구현할 수 있습니다.
* 특정 페이지 처리기 메서드에는 적용할 수 없습니다.

페이지 생성자 또는 미들웨어를 사용하여 처리기 메서드를 실행하기 전에 코드를 실행할 수 있지만, Razor 페이지 필터만 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)에 액세스할 수 있습니다. 필터에는 `HttpContext`에 대한 액세스를 제공하는 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 파생 매개 변수가 있습니다. 예를 들어 [필터 특성 구현](#ifa) 샘플은 생성자 또는 미들웨어로 수행할 수 없는 응답에 헤더를 추가합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([다운로드 방법](xref:index#how-to-download-a-sample))

Razor 페이지 필터는 전역 또는 페이지 수준에서 적용할 수 있는 다음과 같은 메서드를 제공합니다.

* 동기 메서드:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 호출됩니다.
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 호출됩니다.
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): 처리기 메서드가 실행된 후 작업 결과 전에 호출됩니다.

* 비동기 메서드:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 비동기적으로 호출됩니다.
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 호출되기 전에 비동기적으로 호출됩니다.

> [!NOTE]
> 필터 인터페이스의 동기 또는 비동기 버전 중 **하나** 를 구현합니다. 프레임워크는 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출합니다. 두 인터페이스가 구현되는 경우 비동기 메서드만 호출됩니다. 페이지의 재정의에 동일한 규칙이 적용되며, 재정의의 동기 또는 비동기 버전 중 하나만 구현합니다.

## <a name="implement-razor-page-filters-globally"></a>Razor 페이지 필터를 전역으로 구현

다음 코드는 `IAsyncPageFilter`를 구현합니다.

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

위의 코드에서 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0)는 필요하지 않습니다. 이는 샘플에서 애플리케이션에 대한 추적 정보를 제공하는 데 사용됩니다.

다음 코드는 `Startup` 클래스의 `SampleAsyncPageFilter`를 활성화합니다.

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

다음 코드에서는 전체 `Startup` 클래스를 보여 줍니다.

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

다음 코드는 `AddFolderApplicationModelConvention`을 호출하여 `SampleAsyncPageFilter`를 */subFolder* 의 페이지에만 적용합니다.

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

다음 코드는 동기 `IPageFilter`를 구현합니다.

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

다음 코드는 `SamplePageFilter`를 활성화합니다.

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>필터 메서드를 재정의하여 Razor 페이지 필터 구현

다음 코드는 동기 Razor 페이지 필터를 재정의합니다.

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>필터 특성 구현

기본 제공 특성 기반 필터 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 필터는 서브클래싱할 수 있습니다. 다음 필터는 응답에 헤더를 추가합니다.

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

다음 코드는 `AddHeader` 특성을 적용합니다.

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

순서 재정의에 대한 지침은 [기본 순서 재정의](xref:mvc/controllers/filters#overriding-the-default-order)를 참조하세요.

필터에서 필터 파이프라인을 단락(short-circuit)시키는 지침은 [취소 및 단락](xref:mvc/controllers/filters#cancellation-and-short-circuiting)을 참조하세요. 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>권한 부여 필터 특성

[권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 특성은 `PageModel`에 적용될 수 있습니다.

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end