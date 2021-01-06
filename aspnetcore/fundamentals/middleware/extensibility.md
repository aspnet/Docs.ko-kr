---
title: ASP.NET Core의 팩터리 기반 미들웨어 활성화
author: rick-anderson
description: ASP.NET Core에서 팩터리 기반 활성화 구현으로 강력한 형식의 미들웨어를 사용하는 방법을 알아봅니다.
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057494"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>ASP.NET Core의 팩터리 기반 미들웨어 활성화

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>는 [미들웨어](xref:fundamentals/middleware/index) 활성화에 대한 확장성 지점입니다.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 확장 메서드는 미들웨어의 등록된 형식이 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 구현하는지 확인합니다. 구현하는 경우 컨테이너에 등록된 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 인스턴스는 규칙 기반 미들웨어 활성화 논리를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.IMiddleware> 구현을 확인하는 데 사용됩니다. 미들웨어는 앱의 서비스 컨테이너의 [범위가 지정되거나 일시적인 서비스](xref:fundamentals/dependency-injection#service-lifetimes)로 등록됩니다.

혜택:

* 클라이언트 요청당 활성화(범위가 지정된 서비스 주입)
* 미들웨어의 강력한 형식 지정

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 클라이언트 요청(연결)마다 활성화되므로 범위가 지정된 서비스는 미들웨어의 생성자에 주입될 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다. [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 메서드는 요청을 처리하고 미들웨어의 실행을 나타내는 <xref:System.Threading.Tasks.Task>를 반환합니다.

규칙에 따라 미들웨어 활성화:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Http.MiddlewareFactory>에 따라 미들웨어 활성화:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

확장은 미들웨어에 대해 생성됩니다.

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

개체를 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>를 사용하여 팩터리 활성화된 미들웨어에 전달할 수 없습니다.

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

팩터리 활성화된 미들웨어는 `Startup.ConfigureServices`의 기본 제공 컨테이너에 추가됩니다.

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

두 미들웨어는 모두 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다. 미들웨어 팩터리 구현은 범위가 지정된 서비스로 컨테이너에 등록됩니다.

기본 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 구현인 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>는 [미들웨어](xref:fundamentals/middleware/index) 활성화에 대한 확장성 지점입니다.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 확장 메서드는 미들웨어의 등록된 형식이 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 구현하는지 확인합니다. 구현하는 경우 컨테이너에 등록된 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 인스턴스는 규칙 기반 미들웨어 활성화 논리를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.IMiddleware> 구현을 확인하는 데 사용됩니다. 미들웨어는 앱의 서비스 컨테이너의 [범위가 지정되거나 일시적인 서비스](xref:fundamentals/dependency-injection#service-lifetimes)로 등록됩니다.

혜택:

* 클라이언트 요청당 활성화(범위가 지정된 서비스 주입)
* 미들웨어의 강력한 형식 지정

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 클라이언트 요청(연결)마다 활성화되므로 범위가 지정된 서비스는 미들웨어의 생성자에 주입될 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다. [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 메서드는 요청을 처리하고 미들웨어의 실행을 나타내는 <xref:System.Threading.Tasks.Task>를 반환합니다.

규칙에 따라 미들웨어 활성화:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Http.MiddlewareFactory>에 따라 미들웨어 활성화:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

확장은 미들웨어에 대해 생성됩니다.

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

개체를 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>를 사용하여 팩터리 활성화된 미들웨어에 전달할 수 없습니다.

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

팩터리 활성화된 미들웨어는 `Startup.ConfigureServices`의 기본 제공 컨테이너에 추가됩니다.

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

두 미들웨어는 모두 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다. 미들웨어 팩터리 구현은 범위가 지정된 서비스로 컨테이너에 등록됩니다.

기본 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 구현인 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
