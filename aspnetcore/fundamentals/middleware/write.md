---
title: 사용자 지정 ASP.NET Core 미들웨어 작성
author: rick-anderson
description: 사용자 지정 ASP.NET Core 미들웨어 작성 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057468"
---
# <a name="write-custom-aspnet-core-middleware"></a>사용자 지정 ASP.NET Core 미들웨어 작성

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)

미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다. ASP.NET Core는 풍부한 기본 제공 미들웨어 구성 요소 세트를 제공하지만 일부 시나리오에서는 사용자 지정 미들웨어를 작성하려고 할 수 있습니다.

> [!NOTE]
> 이 항목에서는 *규칙 기반* 미들웨어를 작성하는 방법을 설명합니다. 강력한 형식화 및 요청당 활성화를 사용하는 방법에 대한 자세한 내용은 <xref:fundamentals/middleware/extensibility>를 참조하세요.

## <a name="middleware-class"></a>미들웨어 클래스

미들웨어는 일반적으로 클래스에서 캡슐화되고 확장 메서드로 노출됩니다. 쿼리 문자열에서 현재 요청에 대한 문화권을 설정하는 다음 미들웨어를 고려합니다.

[!code-csharp[](write/snapshot/StartupCulture.cs)]

위의 샘플 코드는 미들웨어 구성 요소를 만드는 방법을 보여주는 데 사용됩니다. ASP.NET Core의 기본 제공 지역화 지원은 <xref:fundamentals/localization>을 참조하세요.

문화권을 전달하여 미들웨어를 테스트합니다. 예를 들어 `https://localhost:5001/?culture=no`를 요청합니다.

다음 코드는 미들웨어 대리자를 클래스로 이동합니다.

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

미들웨어 클래스는 다음을 포함해야 합니다.

* <xref:Microsoft.AspNetCore.Http.RequestDelegate> 형식의 매개 변수가 있는 공용 생성자
* `Invoke` 또는 `InvokeAsync`라는 공용 메서드. 이 메서드는 다음 작업을 수행해야 합니다.
  * `Task`를 반환합니다.
  * <xref:Microsoft.AspNetCore.Http.HttpContext> 형식의 첫 번째 매개 변수를 허용합니다.
  
생성자 및 `Invoke`/`InvokeAsync`의 추가 매개 변수는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)로 채워집니다.

## <a name="middleware-dependencies"></a>미들웨어 종속성

미들웨어는 해당 생성자에서 해당 종속성을 노출하여 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따라야 합니다. 미들웨어는 *애플리케이션 수명* 당 한 번 생성됩니다. 요청 내에서 서비스를 미들웨어와 공유해야 하는 경우 [요청당 미들웨어 종속성](#per-request-middleware-dependencies) 섹션을 참조하세요.

미들웨어 구성 요소는 생성자 매개 변수를 통해 [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 해당 종속성을 확인할 수 있습니다. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___)는 추가 매개 변수를 직접 수락할 수도 있습니다.

## <a name="per-request-middleware-dependencies"></a>요청당 미들웨어 종속성

미들웨어는 요청당이 아닌 앱 시작 시 생성되므로 미들웨어 생성자에 의해 사용되는 *범위가 지정된* 수명 서비스는 각 요청 중에 다른 종속성 주입된 형식과 공유되지 않습니다. *범위가 지정된* 서비스를 미들웨어와 다른 형식 간에 공유해야 하는 경우 이러한 서비스를 `Invoke` 메서드의 서명에 추가합니다. `Invoke` 메서드는 DI로 채워지는 추가 매개 변수를 수락할 수 있습니다.

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

[수명 및 등록 옵션](xref:fundamentals/dependency-injection#lifetime-and-registration-options)에는 ‘범위가 지정된’ 수명 서비스를 사용하는 미들웨어의 전체 샘플이 포함되어 있습니다.

## <a name="middleware-extension-method"></a>미들웨어 확장 메서드

다음 확장 메서드는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>를 통해 미들웨어를 공개합니다.

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

다음 코드는 `Startup.Configure`에서 미들웨어를 호출합니다.

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>추가 자료

* [수명 및 등록 옵션](xref:fundamentals/dependency-injection#lifetime-and-registration-options) ‘범위가 지정되고’, ‘일시적인’ *singleton* 수명 서비스를 사용하는 미들웨어의 전체 샘플이 포함되어 있습니다. 
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
