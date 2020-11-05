---
title: ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화
author: rick-anderson
description: ASP.NET Core에서 팩터리 기반 활성화 및 타사 컨테이너를 사용하여 강력한 형식의 미들웨어를 사용하는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057767"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다. `IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다. 이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.

이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다. 이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.

> [!NOTE]
> 이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다. 간단한 인젝터의 사용은 보증하지 않습니다. 간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다. 자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.

샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다. 미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.

`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

미들웨어에 대한 확장( *Middleware/MiddlewareExtensions.cs* )이 생성됩니다.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.

* 간단한 인젝터 컨테이너를 설정합니다.
* 팩터리와 미들웨어를 등록합니다.
* 간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다. `IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다. 이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.

이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다. 이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.

> [!NOTE]
> 이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다. 간단한 인젝터의 사용은 보증하지 않습니다. 간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다. 자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.

샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다. 미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.

`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

미들웨어에 대한 확장( *Middleware/MiddlewareExtensions.cs* )이 생성됩니다.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.

* 간단한 인젝터 컨테이너를 설정합니다.
* 팩터리와 미들웨어를 등록합니다.
* 간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [미들웨어](xref:fundamentals/middleware/index)
* [팩터리 기반 미들웨어 활성화](xref:fundamentals/middleware/extensibility)
* [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)
* [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html)
