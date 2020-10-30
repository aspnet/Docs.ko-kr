---
title: HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/http-modules
ms.openlocfilehash: 9664f49bd709d2c9e46130773211c339e391d1f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060705"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="c747b-102">HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="c747b-103">이 문서에서는 기존 ASP.NET [HTTP 모듈 및 처리기를 system.webserver에서](/iis/configuration/system.webserver/) ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)로 마이그레이션하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="c747b-104">모듈 및 처리기를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-104">Modules and handlers revisited</span></span>

<span data-ttu-id="c747b-105">미들웨어 ASP.NET Core를 진행 하기 전에 먼저 HTTP 모듈 및 처리기가 작동 하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![모듈 처리기](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="c747b-107">**처리기는 다음과 같습니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-107">**Handlers are:**</span></span>

* <span data-ttu-id="c747b-108">[IHttpHandler](/dotnet/api/system.web.ihttphandler) 를 구현 하는 클래스</span><span class="sxs-lookup"><span data-stu-id="c747b-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="c747b-109">지정 된 파일 이름 또는 확장명이 있는 요청을 처리 하는 데 사용 됩니다 (예: *. report).*</span><span class="sxs-lookup"><span data-stu-id="c747b-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="c747b-110">*Web.config* 에서 [구성 됨](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="c747b-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="c747b-111">**모듈은 다음과 같습니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-111">**Modules are:**</span></span>

* <span data-ttu-id="c747b-112">[IHttpModule](/dotnet/api/system.web.ihttpmodule) 를 구현 하는 클래스</span><span class="sxs-lookup"><span data-stu-id="c747b-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="c747b-113">모든 요청에 대해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-113">Invoked for every request</span></span>

* <span data-ttu-id="c747b-114">단기 회로 가능 (요청 추가 처리 중지)</span><span class="sxs-lookup"><span data-stu-id="c747b-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="c747b-115">HTTP 응답에 추가 하거나 직접 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="c747b-116">*Web.config* 에서 [구성 됨](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="c747b-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="c747b-117">**모듈에서 들어오는 요청을 처리 하는 순서는 다음에 의해 결정 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="c747b-118"><https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)등에 의해 발생 된 시리즈 이벤트 인입니다. 각 모듈은 하나 이상의 이벤트에 대 한 처리기를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-118">The <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="c747b-119">동일한 이벤트의 경우 *Web.config* 에서 구성 된 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-119">For the same event, the order in which they're configured in *Web.config* .</span></span>

<span data-ttu-id="c747b-120">모듈 외에도 *Global.asax.cs* 파일에 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="c747b-121">이러한 처리기는 구성 된 모듈의 처리기 다음에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="c747b-122">처리기 및 모듈에서 미들웨어로</span><span class="sxs-lookup"><span data-stu-id="c747b-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="c747b-123">**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="c747b-124">모듈, 처리기, *Global.asax.cs* , *Web.config* (IIS 구성 제외) 및 응용 프로그램 수명 주기가 사라졌습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-124">Modules, handlers, *Global.asax.cs* , *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="c747b-125">미들웨어에서 모듈 및 처리기의 역할을 모두 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="c747b-126">미들웨어는 *Web.config* 아닌 코드를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c747b-127">[파이프라인 분기](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 를 사용 하 여 URL 뿐만 아니라 요청 헤더, 쿼리 문자열 등에 따라 특정 미들웨어에 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="c747b-128">[파이프라인 분기](xref:fundamentals/middleware/index#use-run-and-map) 를 사용 하 여 URL 뿐만 아니라 요청 헤더, 쿼리 문자열 등에 따라 특정 미들웨어에 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="c747b-129">**미들웨어는 모듈과 매우 유사 합니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="c747b-130">모든 요청에 대해 원금에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="c747b-131">요청을 [다음 미들웨어로 전달 하지 않고](#http-modules-shortcircuiting-middleware) 요청을 단기 회로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="c747b-132">자체 HTTP 응답을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="c747b-133">**미들웨어와 모듈은 다른 순서로 처리 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="c747b-134">미들웨어의 순서는 요청 파이프라인에 삽입 되는 순서를 기반으로 하며, 모듈 순서는 주로 이벤트를 기반으로 합니다. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)></span><span class="sxs-lookup"><span data-stu-id="c747b-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> events</span></span>

* <span data-ttu-id="c747b-135">응답에 대 한 미들웨어의 순서는 요청의 반대 이며, 모듈 순서는 요청 및 응답에 대해 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="c747b-136">[IApplicationBuilder를 사용 하 여 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c747b-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![미들웨어](http-modules/_static/middleware.png)

<span data-ttu-id="c747b-138">위의 이미지에서 인증 미들웨어는 요청을 단기 short-circuit 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="c747b-139">미들웨어로 모듈 코드 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-139">Migrating module code to middleware</span></span>

<span data-ttu-id="c747b-140">기존 HTTP 모듈은 다음과 유사 하 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="c747b-141">[미들웨어](xref:fundamentals/middleware/index) 페이지에 표시 된 것 처럼 ASP.NET Core 미들웨어는를 `Invoke` 가져와서을 반환 하는 메서드를 노출 하는 클래스입니다 `HttpContext` `Task` .</span><span class="sxs-lookup"><span data-stu-id="c747b-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="c747b-142">새 미들웨어는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="c747b-143">위의 미들웨어 템플릿은 [미들웨어 작성](xref:fundamentals/middleware/write)에 대 한 섹션에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="c747b-144">*MyMiddlewareExtensions* helper 클래스를 사용 하면 클래스에서 미들웨어를 더 쉽게 구성할 수 있습니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c747b-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="c747b-145">`UseMyMiddleware`메서드는 미들웨어 클래스를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="c747b-146">미들웨어에 필요한 서비스는 미들웨어의 생성자에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="c747b-147">사용자에 게 권한이 부여 되지 않은 경우와 같이 모듈에서 요청을 종료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="c747b-148">미들웨어는 `Invoke` 파이프라인의 다음 미들웨어에서를 호출 하지 않고이를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="c747b-149">이전 미들웨어는 응답이 파이프라인을 통해 다시 사용 될 때 계속 호출 되기 때문에 요청을 완전히 종료 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="c747b-150">모듈의 기능을 새로운 미들웨어로 마이그레이션하면 `HttpContext` ASP.NET Core에서 클래스가 크게 변경 되어 코드가 컴파일되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c747b-151">[나중에](#migrating-to-the-new-httpcontext)새 ASP.NET Core HttpContext로 마이그레이션하는 방법을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="c747b-152">모듈 삽입을 요청 파이프라인으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="c747b-153">일반적으로 HTTP 모듈은 *Web.config* 를 사용 하 여 요청 파이프라인에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-153">HTTP modules are typically added to the request pipeline using *Web.config* :</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="c747b-154">클래스의 요청 파이프라인에 [새 미들웨어를 추가](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 하 여이를 변환 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c747b-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="c747b-155">파이프라인에서 새 미들웨어를 삽입 하는 정확한 위치는 모듈 (, 등)로 처리 되는 이벤트 `BeginRequest` `EndRequest` 와 *Web.config* 의 모듈 목록에서 해당 순서에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config* .</span></span>

<span data-ttu-id="c747b-156">앞에서 설명한 것 처럼 ASP.NET Core에는 응용 프로그램 수명 주기가 없으며 미들웨어에서 응답이 처리 되는 순서는 모듈에서 사용 하는 순서와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="c747b-157">이렇게 하면 순서 결정을 더 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="c747b-158">정렬이 문제가 되는 경우 독립적으로 정렬할 수 있는 여러 미들웨어 구성 요소로 모듈을 분할할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="c747b-159">미들웨어로 처리기 코드 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="c747b-160">HTTP 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="c747b-161">ASP.NET Core 프로젝트에서 다음과 유사한 미들웨어로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="c747b-162">이 미들웨어는 모듈에 해당 하는 미들웨어와 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="c747b-163">여기에는에 대 한 호출이 없다는 점만 다릅니다 `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="c747b-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="c747b-164">이는 처리기가 요청 파이프라인의 끝에 있기 때문에 의미가 있으므로 호출할 다음 미들웨어가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="c747b-165">요청 파이프라인으로 처리기 삽입 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="c747b-166">HTTP 처리기 구성은 *Web.config* 에서 수행 되며 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="c747b-167">`Startup`모듈에서 변환 된 미들웨어와 마찬가지로 클래스의 요청 파이프라인에 새 처리기 미들웨어를 추가 하 여이를 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="c747b-168">이 방법의 문제는 새 처리기 미들웨어에 모든 요청을 전송 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="c747b-169">그러나 지정 된 확장을 포함 하는 요청만 미들웨어에 도달 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="c747b-170">그러면 HTTP 처리기와 동일한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="c747b-171">한 가지 해결 방법은 확장 메서드를 사용 하 여 지정 된 확장명을 가진 요청의 파이프라인을 분기 하는 것입니다 `MapWhen` .</span><span class="sxs-lookup"><span data-stu-id="c747b-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="c747b-172">다른 미들웨어를 추가 하는 것과 같은 방법으로이 작업을 수행 합니다 `Configure` .</span><span class="sxs-lookup"><span data-stu-id="c747b-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="c747b-173">`MapWhen` 는 다음 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="c747b-174">을 사용 하 `HttpContext` 고 `true` 요청을 분기 아래로 이동 해야 하는 경우를 반환 하는 람다입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="c747b-175">즉, 확장 뿐만 아니라 요청 헤더, 쿼리 문자열 매개 변수 등에서 요청을 분기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="c747b-176">을 사용 하 `IApplicationBuilder` 고 분기에 대 한 모든 미들웨어를 추가 하는 람다입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="c747b-177">즉, 처리기 미들웨어 앞의 분기에 추가 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="c747b-178">모든 요청에서 분기가 호출 되기 전에 파이프라인에 추가 되는 미들웨어 분기는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="c747b-179">옵션 패턴을 사용 하 여 미들웨어 옵션 로드</span><span class="sxs-lookup"><span data-stu-id="c747b-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="c747b-180">일부 모듈 및 처리기는 *Web.config* 에 저장 되는 구성 옵션을 포함 합니다. 그러나 ASP.NET Core *Web.config* 대신 새 구성 모델이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-180">Some modules and handlers have configuration options that are stored in *Web.config* . However, in ASP.NET Core a new configuration model is used in place of *Web.config* .</span></span>

<span data-ttu-id="c747b-181">새 [구성 시스템](xref:fundamentals/configuration/index) 은이를 해결 하는 다음과 같은 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="c747b-182">[다음 섹션](#loading-middleware-options-through-direct-injection)에 표시 된 것 처럼 미들웨어에 직접 옵션을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="c747b-183">[옵션 패턴](xref:fundamentals/configuration/options)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="c747b-184">미들웨어 옵션을 포함 하는 클래스를 만듭니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="c747b-185">옵션 값 저장</span><span class="sxs-lookup"><span data-stu-id="c747b-185">Store the option values</span></span>

   <span data-ttu-id="c747b-186">구성 시스템을 사용 하 여 원하는 위치에 옵션 값을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="c747b-187">그러나 대부분의 사이트에서는 *:::no-loc(appsettings.json):::* 를 사용 하기 때문에 다음 방법을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-187">However, most sites use *:::no-loc(appsettings.json):::* , so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/:::no-loc(appsettings.json):::?range=1,14-18)]

   <span data-ttu-id="c747b-188">여기서 *MyMiddlewareOptionsSection* 는 섹션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="c747b-189">옵션 클래스의 이름과 같을 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="c747b-190">옵션 클래스와 옵션 값 연결</span><span class="sxs-lookup"><span data-stu-id="c747b-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="c747b-191">옵션 패턴은 ASP.NET Core의 종속성 주입 프레임 워크를 사용 하 여 옵션 유형 (예: `MyMiddlewareOptions` )을 `MyMiddlewareOptions` 실제 옵션을 포함 하는 개체와 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="c747b-192">클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c747b-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="c747b-193">를 사용 하는 경우 *:::no-loc(appsettings.json):::* 생성자의 구성 작성기에 추가 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c747b-193">If you're using *:::no-loc(appsettings.json):::* , add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="c747b-194">옵션 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="c747b-195">옵션과 옵션 클래스를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="c747b-196">미들웨어 생성자에 옵션을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="c747b-197">이는 컨트롤러에 옵션을 삽입 하는 것과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="c747b-198">에 미들웨어를 추가 하는 [Usemiddleware](#http-modules-usemiddleware) 확장 메서드는 `IApplicationBuilder` 종속성 주입을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="c747b-199">이는 개체로 제한 되지 않습니다 `IOptions` .</span><span class="sxs-lookup"><span data-stu-id="c747b-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="c747b-200">미들웨어에 필요한 다른 모든 개체를 이러한 방식으로 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="c747b-201">직접 주입을 통해 미들웨어 옵션 로드</span><span class="sxs-lookup"><span data-stu-id="c747b-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="c747b-202">옵션 패턴에는 옵션 값과 소비자 간의 느슨한 결합을 만드는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="c747b-203">옵션 클래스를 실제 옵션 값과 연결한 후에는 다른 모든 클래스가 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스 권한을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="c747b-204">옵션 값 주위에 전달할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="c747b-205">이는 다른 옵션을 사용 하 여 동일한 미들웨어를 두 번 사용 하려는 경우에도 중단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="c747b-206">예를 들어 서로 다른 역할을 허용 하는 여러 분기에서 사용 되는 권한 부여 미들웨어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="c747b-207">두 가지 옵션 개체를 하나의 옵션 클래스에 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="c747b-208">이 솔루션은 클래스의 실제 옵션 값을 사용 하 여 옵션 개체를 가져온 `Startup` 다음 미들웨어의 각 인스턴스에 직접 전달 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="c747b-209">두 번째 키를에 추가 합니다. *:::no-loc(appsettings.json):::*</span><span class="sxs-lookup"><span data-stu-id="c747b-209">Add a second key to *:::no-loc(appsettings.json):::*</span></span>

   <span data-ttu-id="c747b-210">파일에 두 번째 옵션 집합을 추가 하려면 새 키를 사용 하 여 해당 옵션을 고유 하 게 *:::no-loc(appsettings.json):::* 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-210">To add a second set of options to the *:::no-loc(appsettings.json):::* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/:::no-loc(appsettings.json):::?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="c747b-211">옵션 값을 검색 하 여 미들웨어에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="c747b-212">`Use...`파이프라인에 미들웨어를 추가 하는 확장 메서드는 옵션 값에 전달할 논리적인 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="c747b-213">미들웨어가 options 매개 변수를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="c747b-214">`Use...`옵션 매개 변수를 사용 하 여에 전달 하는 확장 메서드의 오버 로드를 제공 `UseMiddleware` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="c747b-215">`UseMiddleware`매개 변수를 사용 하 여를 호출 하면 미들웨어 개체를 인스턴스화할 때 미들웨어 생성자에 매개 변수를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="c747b-216">개체의 옵션 개체를 래핑하는 방법을 확인 `OptionsWrapper` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="c747b-217">이는 `IOptions` 미들웨어 생성자에서 예상한 대로을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="c747b-218">새 HttpContext로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="c747b-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="c747b-219">`Invoke`미들웨어의 메서드는 다음 형식의 매개 변수를 사용 한다는 것을 앞에서 살펴보았습니다 `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="c747b-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="c747b-220">`HttpContext` ASP.NET Core에서 대폭 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c747b-221">이 섹션에서는 [system.web](/dotnet/api/system.web.httpcontext) 의 가장 일반적으로 사용 되는 속성을 새로 변환 하는 방법을 보여 `Microsoft.AspNetCore.Http.HttpContext` 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="c747b-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="c747b-222">HttpContext</span></span>

<span data-ttu-id="c747b-223">**HttpContext 항목** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="c747b-224">**고유한 요청 ID (System.web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="c747b-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="c747b-225">각 요청에 대 한 고유 id를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="c747b-226">로그에를 포함 하는 것이 매우 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="c747b-227">HttpContext 요청</span><span class="sxs-lookup"><span data-stu-id="c747b-227">HttpContext.Request</span></span>

<span data-ttu-id="c747b-228">**HttpMethod** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="c747b-229">**HttpContext. QueryString** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="c747b-230">**RawUrl** 및:로 변환 **합니다.**</span><span class="sxs-lookup"><span data-stu-id="c747b-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="c747b-231">**HttpContext. IsSecureConnection** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="c747b-232">**HttpContext. UserHostAddress** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="c747b-233">**HttpContext 요청. :::no-loc(Cookie):::** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-233">**HttpContext.Request.:::no-loc(Cookie):::s** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_:::no-loc(Cookie):::s)]

<span data-ttu-id="c747b-234">**RouteData** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="c747b-235">**HttpContext. 헤더** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="c747b-236">**UserAgent** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="c747b-237">**HttpContext** 참조 페이지는 다음으로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="c747b-238">**HttpContext. ContentType** 은 다음과 같이 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="c747b-239">**HttpContext. Form** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="c747b-240">콘텐츠 하위 형식이 *x-www-form-urlencoded* 또는 *양식 데이터* 인 경우에만 양식 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data* .</span></span>

<span data-ttu-id="c747b-241">**InputStream** 는 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="c747b-242">이 코드는 파이프라인의 끝에 있는 처리기 형식 미들웨어 에서만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="c747b-243">위에서 설명한 대로 요청 당 한 번만 원시 본문을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="c747b-244">첫 번째 읽은 후 본문을 읽으려고 시도 하는 미들웨어는 빈 본문을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="c747b-245">이는 버퍼에서 수행 되기 때문에 이전에 표시 된 대로 폼을 읽는 경우에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="c747b-246">HttpContext 응답</span><span class="sxs-lookup"><span data-stu-id="c747b-246">HttpContext.Response</span></span>

<span data-ttu-id="c747b-247">**Httpcontext. Status** 및 **httpcontext 설명** 으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="c747b-248">**Httpcontext. ContentEncoding** 및 Httpcontext. **ContentType** 은 다음으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="c747b-249">또한 직접 Httpcontext.current에 대 한 **ContentType** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="c747b-250">**HttpContext. 출력** 은 다음과 같이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="c747b-251">**TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="c747b-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="c747b-252">파일을 처리 하는 방법은 [미들웨어 및 요청 기능](xref:fundamentals/request-features#middleware-and-request-features)에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-252">Serving up a file is discussed in [Middleware and request features](xref:fundamentals/request-features#middleware-and-request-features).</span></span>

<span data-ttu-id="c747b-253">**HttpContext. 헤더**</span><span class="sxs-lookup"><span data-stu-id="c747b-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="c747b-254">응답 본문에 쓴 후에 응답 헤더를 설정 하는 경우 응답 헤더를 보내는 것은 복잡 하므로 전송 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="c747b-255">해결 방법은 응답에 쓰기를 시작 하기 직전에 호출 되는 콜백 메서드를 설정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="c747b-256">미들웨어에서 메서드를 시작할 때이 작업을 수행 하는 것이 가장 좋습니다 `Invoke` .</span><span class="sxs-lookup"><span data-stu-id="c747b-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="c747b-257">이 콜백 메서드는 응답 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="c747b-258">다음 코드에서는 라는 콜백 메서드를 설정 합니다 `SetHeaders` .</span><span class="sxs-lookup"><span data-stu-id="c747b-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c747b-259">`SetHeaders`콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="c747b-260">**HttpContext 응답입니다. :::no-loc(Cookie)::: 삭제**</span><span class="sxs-lookup"><span data-stu-id="c747b-260">**HttpContext.Response.:::no-loc(Cookie):::s**</span></span>

<span data-ttu-id="c747b-261">:::no-loc(Cookie):::s는 *설정- :::no-loc(Cookie):::* 응답 헤더의 브라우저로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-261">:::no-loc(Cookie):::s travel to the browser in a *Set-:::no-loc(Cookie):::* response header.</span></span> <span data-ttu-id="c747b-262">따라서를 보내는 :::no-loc(cookie)::: 데는 응답 헤더를 보내는 데 사용 되는 것과 동일한 콜백이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-262">As a result, sending :::no-loc(cookie):::s requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(Set:::no-loc(Cookie):::s, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c747b-263">`Set:::no-loc(Cookie):::s`콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c747b-263">The `Set:::no-loc(Cookie):::s` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Set:::no-loc(Cookie):::s)]

## <a name="additional-resources"></a><span data-ttu-id="c747b-264">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c747b-264">Additional resources</span></span>

* [<span data-ttu-id="c747b-265">HTTP 처리기 및 HTTP 모듈 개요</span><span class="sxs-lookup"><span data-stu-id="c747b-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="c747b-266">Configuration</span><span class="sxs-lookup"><span data-stu-id="c747b-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="c747b-267">응용 프로그램 시작</span><span class="sxs-lookup"><span data-stu-id="c747b-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="c747b-268">미들웨어</span><span class="sxs-lookup"><span data-stu-id="c747b-268">Middleware</span></span>](xref:fundamentals/middleware/index)
