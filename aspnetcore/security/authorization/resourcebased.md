---
title: ASP.NET Core에서 리소스 기반 권한 부여
author: scottaddie
description: 권한 부여 특성이 충분 하지 않을 경우 ASP.NET Core 앱에서 리소스 기반 권한 부여를 구현 하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/resourcebased
ms.openlocfilehash: 202ca681a66ddf7f729d8835e2f77da846583df1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060172"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="aa956-103">ASP.NET Core에서 리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="aa956-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="aa956-104">권한 부여 전략은 액세스 하는 리소스에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="aa956-105">Author 속성이 있는 문서를 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="aa956-105">Consider a document that has an author property.</span></span> <span data-ttu-id="aa956-106">작성자만 문서를 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="aa956-107">따라서 권한 부여 평가를 수행 하기 전에 데이터 저장소에서 문서를 검색 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="aa956-108">특성 평가는 페이지 처리기 또는 문서를 로드 하는 작업을 실행 하기 전에 데이터 바인딩 전에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="aa956-109">이러한 이유로 특성을 사용한 선언적 권한 부여는 `[Authorize]` 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="aa956-110">대신 사용자 지정 권한 부여 메서드를 &mdash; *명령적 권한 부여* 라는 스타일로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization* .</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="aa956-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="aa956-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="aa956-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="aa956-114">[권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data) 리소스 기반 권한 부여를 사용 하는 샘플 앱을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="aa956-115">명령적 권한 부여 사용</span><span class="sxs-lookup"><span data-stu-id="aa956-115">Use imperative authorization</span></span>

<span data-ttu-id="aa956-116">권한 부여는 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 서비스로 구현 되 고 클래스 내의 서비스 컬렉션에 등록 됩니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="aa956-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="aa956-117">이 서비스는 페이지 처리기 나 작업에 대 한 [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="aa956-118">`IAuthorizationService` 에는 두 개의 `AuthorizeAsync` 메서드 오버 로드가 있습니다. 하나는 리소스를 수락 하 고, 다른 하나는 리소스를 수락 하 고 다른 하나는 평가할 요구 사항의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="aa956-119">다음 예제에서는 보호할 리소스가 사용자 지정 개체에 로드 됩니다 `Document` .</span><span class="sxs-lookup"><span data-stu-id="aa956-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="aa956-120">`AuthorizeAsync`현재 사용자가 제공 된 문서를 편집할 수 있는지 여부를 확인 하기 위해 오버 로드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="aa956-121">사용자 지정 "EditPolicy" 권한 부여 정책이 결정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="aa956-122">권한 부여 정책을 만드는 방법에 대 한 자세한 내용은 [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="aa956-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="aa956-123">다음 코드 샘플에서는 인증을 실행 하 고 속성을 설정 했다고 가정 합니다 `User` .</span><span class="sxs-lookup"><span data-stu-id="aa956-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="aa956-124">리소스 기반 처리기 작성</span><span class="sxs-lookup"><span data-stu-id="aa956-124">Write a resource-based handler</span></span>

<span data-ttu-id="aa956-125">리소스 기반 권한 부여에 대 한 처리기 작성은 [일반 요구 사항 처리기를 작성 하](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)는 것과 크게 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="aa956-126">사용자 지정 요구 사항 클래스를 만들고 요구 사항 처리기 클래스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="aa956-127">요구 사항 클래스를 만드는 방법에 대 한 자세한 내용은 [요구 사항](xref:security/authorization/policies#requirements)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="aa956-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="aa956-128">Handler 클래스는 요구 사항과 리소스 형식을 모두 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="aa956-129">예를 들어 및 리소스를 활용 하는 처리기는 `SameAuthorRequirement` `Document` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="aa956-130">앞의 예제에서 `SameAuthorRequirement` 가 보다 일반적인 클래스의 특별 한 경우를 가정 `SpecificAuthorRequirement` 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="aa956-131">`SpecificAuthorRequirement`클래스 (표시 되지 않음)에는 `Name` 작성자의 이름을 나타내는 속성이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="aa956-132">`Name`속성을 현재 사용자로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="aa956-133">에서 요구 사항 및 처리기를 등록 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aa956-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="aa956-134">운영 요구 사항</span><span class="sxs-lookup"><span data-stu-id="aa956-134">Operational requirements</span></span>

<span data-ttu-id="aa956-135">CRUD (만들기, 읽기, 업데이트, 삭제) 작업의 결과에 따라 결정을 내리는 경우 [Operationauthorizationrequirement 사항](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) 도우미 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="aa956-136">이 클래스를 사용 하면 각 작업 형식에 대 한 개별 클래스 대신 단일 처리기를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="aa956-137">이를 사용 하려면 몇 가지 작업 이름을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="aa956-138">처리기는 요구 사항과 리소스를 사용 하 여 다음과 같이 구현 됩니다 `OperationAuthorizationRequirement` `Document` .</span><span class="sxs-lookup"><span data-stu-id="aa956-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="aa956-139">위의 처리기는 리소스, 사용자의 id 및 요구 사항의 속성을 사용 하 여 작업의 유효성을 검사 합니다 `Name` .</span><span class="sxs-lookup"><span data-stu-id="aa956-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="aa956-140">작업 리소스 처리기로 챌린지 및 금지</span><span class="sxs-lookup"><span data-stu-id="aa956-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="aa956-141">이 섹션에서는 챌린지 및 금지 작업 결과를 처리 하는 방법과 시도 및 금지의 차이점을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="aa956-142">작업 리소스 처리기를 호출 하려면 `AuthorizeAsync` 페이지 처리기 또는 작업에서 호출할 때 작업을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="aa956-143">다음 예에서는 인증 된 사용자가 제공 된 문서를 볼 수 있는지 여부를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="aa956-144">다음 코드 샘플에서는 인증을 실행 하 고 속성을 설정 했다고 가정 합니다 `User` .</span><span class="sxs-lookup"><span data-stu-id="aa956-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="aa956-145">권한 부여가 성공 하면 문서를 보는 페이지가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="aa956-146">권한 부여가 실패 하지만 사용자가 인증 되는 경우를 반환 하면 `ForbidResult` 권한 부여에 실패 했다는 인증 미들웨어에 게 알립니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="aa956-147">`ChallengeResult`인증을 수행 해야 하는 경우이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="aa956-148">대화형 브라우저 클라이언트의 경우 사용자를 로그인 페이지로 리디렉션하는 것이 적합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="aa956-149">권한 부여가 성공 하면 문서에 대 한 뷰가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="aa956-150">권한 부여가 실패 하는 경우를 반환 하면 `ChallengeResult` 권한 부여에 실패 했다는 인증 미들웨어에 게 알리고 미들웨어는 적절 한 응답을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="aa956-151">적절 한 응답에서 401 또는 403 상태 코드를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="aa956-152">대화형 브라우저 클라이언트의 경우 사용자를 로그인 페이지로 리디렉션하는 것을 의미할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa956-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
