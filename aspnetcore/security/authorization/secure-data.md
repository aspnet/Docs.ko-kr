---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: '권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 알아봅니다. 에는 HTTPS, 인증, 보안이 포함 됩니다 ASP.NET Core Identity .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051943"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="477cc-104">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="477cc-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="477cc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="477cc-106">[이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) 보기</span><span class="sxs-lookup"><span data-stu-id="477cc-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="477cc-107">이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="477cc-108">사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="477cc-109">다음 세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-109">There are three security groups:</span></span>

* <span data-ttu-id="477cc-110">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="477cc-111">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="477cc-112">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="477cc-113">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="477cc-114">이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="477cc-115">다음 이미지에서는 사용자 Rick ( `rick@example.com` )가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="477cc-116">Rick는 승인 된 연락처만 볼 수 **있으며** / **Delete Delete** 는 / 연락처에 대 한 새 링크를 **만듭니다** .</span><span class="sxs-lookup"><span data-stu-id="477cc-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="477cc-117">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="477cc-118">관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="477cc-120">다음 이미지에서 `manager@contoso.com` 는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인을 보여 주는 스크린샷 manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="477cc-122">다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-122">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자 보기](secure-data/_static/manager.png)

<span data-ttu-id="477cc-124">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="477cc-125">다음 이미지에서 `admin@contoso.com` 는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인을 보여 주는 스크린샷 admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="477cc-127">관리자에 게는 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-127">The administrator has all privileges.</span></span> <span data-ttu-id="477cc-128">연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="477cc-129">다음 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 앱을 만들었습니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="477cc-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="477cc-130">이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="477cc-131">`ContactIsOwnerAuthorizationHandler`: 사용자가 해당 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="477cc-132">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="477cc-133">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="477cc-134">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="477cc-134">Prerequisites</span></span>

<span data-ttu-id="477cc-135">이 자습서는 고급입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-135">This tutorial is advanced.</span></span> <span data-ttu-id="477cc-136">다음에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-136">You should be familiar with:</span></span>

* [<span data-ttu-id="477cc-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="477cc-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="477cc-138">인증</span><span class="sxs-lookup"><span data-stu-id="477cc-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="477cc-139">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="477cc-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="477cc-140">권한 부여</span><span class="sxs-lookup"><span data-stu-id="477cc-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="477cc-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="477cc-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="477cc-142">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="477cc-142">The starter and completed app</span></span>

<span data-ttu-id="477cc-143">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="477cc-144">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="477cc-145">시작 앱</span><span class="sxs-lookup"><span data-stu-id="477cc-145">The starter app</span></span>

<span data-ttu-id="477cc-146">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="477cc-147">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="477cc-148">보안 사용자 데이터</span><span class="sxs-lookup"><span data-stu-id="477cc-148">Secure user data</span></span>

<span data-ttu-id="477cc-149">다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="477cc-150">완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="477cc-151">사용자에 게 연락처 데이터 연결</span><span class="sxs-lookup"><span data-stu-id="477cc-151">Tie the contact data to the user</span></span>

<span data-ttu-id="477cc-152">ASP.NET [Identity](xref:security/authentication/identity) 사용자 ID를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="477cc-153">`OwnerID` `ContactStatus` 모델에 및을 추가 합니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="477cc-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="477cc-154">`OwnerID``AspNetUser`데이터베이스에 있는 테이블의 사용자 ID입니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="477cc-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="477cc-155">`Status`필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="477cc-156">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="477cc-157">역할 서비스 추가 Identity</span><span class="sxs-lookup"><span data-stu-id="477cc-157">Add Role services to Identity</span></span>

<span data-ttu-id="477cc-158">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="477cc-159">인증 된 사용자 필요</span><span class="sxs-lookup"><span data-stu-id="477cc-159">Require authenticated users</span></span>

<span data-ttu-id="477cc-160">사용자 인증을 요구 하도록 대체 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="477cc-161">위의 강조 표시 된 코드는 [대체 인증 정책을](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="477cc-162">대체 인증 정책에는 *_all_* Razor 인증 특성이 있는 페이지, 컨트롤러 또는 작업 메서드를 제외 하 고 \* 모든 _ 사용자를 인증 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="477cc-163">예를 들어 Razor 또는를 사용 하는 페이지, 컨트롤러 또는 작업 메서드 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 는 대체 인증 정책 대신 적용 된 인증 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="477cc-164">대체 인증 정책:</span><span class="sxs-lookup"><span data-stu-id="477cc-164">The fallback authentication policy:</span></span>

<span data-ttu-id="477cc-165">_는 인증 정책을 명시적으로 지정 하지 않는 모든 요청에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="477cc-166">끝점 라우팅을 통해 처리 되는 요청의 경우 권한 부여 특성을 지정 하지 않는 끝점이 여기에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="477cc-167">[정적 파일과](xref:fundamentals/static-files)같이 권한 부여 미들웨어 후 다른 미들웨어에서 처리 하는 요청의 경우 모든 요청에 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="477cc-168">사용자를 인증 하도록 요구 하는 대체 인증 정책을 설정 하면 새로 추가 된 Razor 페이지와 컨트롤러를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="477cc-169">기본적으로 인증을 요구 하는 것은 특성을 포함 하는 새 컨트롤러 및 페이지에 의존 하는 것 보다 안전 Razor 합니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="477cc-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="477cc-170">클래스에는 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> 도 포함 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="477cc-171">는 `DefaultPolicy` `[Authorize]` 정책을 지정 하지 않은 경우 특성에 사용 되는 정책입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="477cc-172">`[Authorize]` 는와 달리 명명 된 정책을 포함 하지 않습니다 `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="477cc-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="477cc-173">정책에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="477cc-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="477cc-174">MVC 컨트롤러 및 Razor 페이지에서 모든 사용자를 인증 하도록 요구 하는 다른 방법은 권한 부여 필터를 추가 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="477cc-175">이전 코드는 권한 부여 필터를 사용 하며, 대체 정책 설정에서는 끝점 라우팅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="477cc-176">대체 (fallback) 정책을 설정 하는 것은 모든 사용자를 인증 하도록 요구 하는 기본 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="477cc-177">[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` 익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 및 페이지에 allowanonymous를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="477cc-178">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="477cc-178">Configure the test account</span></span>

<span data-ttu-id="477cc-179">`SeedData`클래스는 두 개의 계정 즉, 관리자와 관리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="477cc-180">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="477cc-181">프로젝트 디렉터리 ( *Program.cs* 가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="477cc-182">강력한 암호를 지정 하지 않으면가 호출 될 때 예외가 throw 됩니다 `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="477cc-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="477cc-183">`Main`테스트 암호를 사용 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="477cc-184">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="477cc-185">`Initialize`클래스의 메서드를 업데이트 `SeedData` 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="477cc-186">관리자 사용자 ID와 `ContactStatus` 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="477cc-187">연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="477cc-188">모든 연락처에 사용자 ID 및 상태를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="477cc-189">하나의 연락처만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="477cc-190">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="477cc-191">`ContactIsOwnerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="477cc-192">는 `ContactIsOwnerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="477cc-193">`ContactIsOwnerAuthorizationHandler`호출 [컨텍스트입니다. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="477cc-194">권한 부여 처리기 일반적:</span><span class="sxs-lookup"><span data-stu-id="477cc-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="477cc-195">`context.Succeed`요구 사항이 충족 되 면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="477cc-196">`Task.CompletedTask`요구 사항이 충족 되지 않으면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="477cc-197">`Task.CompletedTask` 은 (는) 성공 또는 실패 하지 않으므로 &mdash; 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="477cc-198">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="477cc-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="477cc-199">앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="477cc-200">`ContactIsOwnerAuthorizationHandler` 는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="477cc-201">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-201">Create a manager authorization handler</span></span>

<span data-ttu-id="477cc-202">`ContactManagerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="477cc-203">는 `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="477cc-204">관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="477cc-205">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="477cc-206">`ContactAdministratorsAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="477cc-207">는 `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="477cc-208">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="477cc-209">권한 부여 처리기 등록</span><span class="sxs-lookup"><span data-stu-id="477cc-209">Register the authorization handlers</span></span>

<span data-ttu-id="477cc-210">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="477cc-211">는 `ContactIsOwnerAuthorizationHandler` Entity Framework Core를 기반으로 하는 ASP.NET Core을 사용 합니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="477cc-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="477cc-212">종속성 주입을 통해에 사용할 수 있도록 서비스 컬렉션에 처리기를 `ContactsController` 등록 [dependency injection](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="477cc-213">끝에 다음 코드를 추가 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="477cc-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="477cc-214">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 는 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="477cc-215">이는 EF를 사용 하지 않고 필요한 모든 정보가 메서드의 매개 변수에 단일 항목 때문에 발생 `Context` `HandleRequirementAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="477cc-216">인증 지원</span><span class="sxs-lookup"><span data-stu-id="477cc-216">Support authorization</span></span>

<span data-ttu-id="477cc-217">이 섹션에서는 페이지를 업데이트 하 Razor 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="477cc-218">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="477cc-219">클래스를 검토 `ContactOperations` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="477cc-220">이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="477cc-221">연락처 페이지에 대 한 기본 클래스 만들기 Razor</span><span class="sxs-lookup"><span data-stu-id="477cc-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="477cc-222">연락처 페이지에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="477cc-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="477cc-223">기본 클래스는 초기화 코드를 한 위치에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="477cc-224">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="477cc-224">The preceding code:</span></span>

* <span data-ttu-id="477cc-225">`IAuthorizationService`권한 부여 처리기에 액세스 하는 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="477cc-226">서비스를 추가 Identity `UserManager` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="477cc-227">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="477cc-228">CreateModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-228">Update the CreateModel</span></span>

<span data-ttu-id="477cc-229">기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다 `DI_BasePageModel` .</span><span class="sxs-lookup"><span data-stu-id="477cc-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="477cc-230">메서드를 `CreateModel.OnPostAsync` 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="477cc-231">모델에 사용자 ID를 추가 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="477cc-232">권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="477cc-233">IndexModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-233">Update the IndexModel</span></span>

<span data-ttu-id="477cc-234">`OnGetAsync`승인 된 연락처만 일반 사용자에 게 표시 되도록 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="477cc-235">EditModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-235">Update the EditModel</span></span>

<span data-ttu-id="477cc-236">사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="477cc-237">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="477cc-238">특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="477cc-239">리소스 기반 권한 부여는 필수적 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="477cc-240">앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="477cc-241">리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="477cc-242">DeleteModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-242">Update the DeleteModel</span></span>

<span data-ttu-id="477cc-243">권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="477cc-244">권한 부여 서비스를 뷰에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="477cc-245">현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="477cc-246">모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="477cc-247">위의 태그는 여러 문을 추가 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="477cc-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="477cc-248">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="477cc-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="477cc-249">데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="477cc-250">링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="477cc-251">사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="477cc-252">Razor페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="477cc-253">업데이트 세부 정보</span><span class="sxs-lookup"><span data-stu-id="477cc-253">Update Details</span></span>

<span data-ttu-id="477cc-254">관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="477cc-255">세부 정보 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="477cc-256">역할에 사용자 추가 또는 제거</span><span class="sxs-lookup"><span data-stu-id="477cc-256">Add or remove a user to a role</span></span>

<span data-ttu-id="477cc-257">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="477cc-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="477cc-258">사용자가 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-258">Removing privileges from a user.</span></span> <span data-ttu-id="477cc-259">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="477cc-260">사용자에 게 권한 추가</span><span class="sxs-lookup"><span data-stu-id="477cc-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="477cc-261">챌린지와 금지 간의 차이점</span><span class="sxs-lookup"><span data-stu-id="477cc-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="477cc-262">이 앱은 인증 된 사용자를 [요구](#rau)하도록 기본 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="477cc-263">다음 코드는 익명 사용자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-263">The following code allows anonymous users.</span></span> <span data-ttu-id="477cc-264">익명 사용자는 챌린지 vs 금지 간의 차이점을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="477cc-265">앞의 코드에서 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-265">In the preceding code:</span></span>

* <span data-ttu-id="477cc-266">사용자가 인증 **되지 않은** 경우 `ChallengeResult` 이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="477cc-267">가 반환 되 면 `ChallengeResult` 사용자가 로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="477cc-268">사용자가 인증 되었지만 권한이 부여 되지 않은 경우 `ForbidResult` 이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="477cc-269">가 반환 되 면 `ForbidResult` 사용자가 액세스 거부 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="477cc-270">완성 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="477cc-270">Test the completed app</span></span>

<span data-ttu-id="477cc-271">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="477cc-272">강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="477cc-273">예를 들어 `Passw0rd!` 은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="477cc-274">프로젝트의 폴더에서 다음 명령을 실행 합니다 `<PW>` . 여기서은 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="477cc-275">앱에 연락처가 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="477cc-275">If the app has contacts:</span></span>

* <span data-ttu-id="477cc-276">테이블의 모든 레코드를 삭제 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="477cc-277">응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="477cc-278">완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="477cc-279">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="477cc-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="477cc-280">다른 사용자로 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="477cc-281">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-281">Verify the following operations:</span></span>

* <span data-ttu-id="477cc-282">등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="477cc-283">등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="477cc-284">관리자는 연락처 데이터를 승인/거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="477cc-285">`Details`보기는 **승인** 및 **거부** 단추를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="477cc-286">관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="477cc-287">사용자</span><span class="sxs-lookup"><span data-stu-id="477cc-287">User</span></span>                | <span data-ttu-id="477cc-288">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="477cc-288">Seeded by the app</span></span> | <span data-ttu-id="477cc-289">옵션</span><span class="sxs-lookup"><span data-stu-id="477cc-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="477cc-290">아니요</span><span class="sxs-lookup"><span data-stu-id="477cc-290">No</span></span>                | <span data-ttu-id="477cc-291">자신의 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="477cc-292">Yes</span><span class="sxs-lookup"><span data-stu-id="477cc-292">Yes</span></span>               | <span data-ttu-id="477cc-293">자신의 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="477cc-294">Yes</span><span class="sxs-lookup"><span data-stu-id="477cc-294">Yes</span></span>               | <span data-ttu-id="477cc-295">모든 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="477cc-296">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="477cc-297">관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="477cc-298">이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="477cc-299">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-299">Create the starter app</span></span>

* <span data-ttu-id="477cc-300">Razor"연락처 관리자" 라는 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="477cc-301">**개별 사용자 계정을** 사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="477cc-302">네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="477cc-303">`-uld` SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="477cc-304">*모델/연락처를 추가 합니다. cs* :</span><span class="sxs-lookup"><span data-stu-id="477cc-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="477cc-305">모델을 스 캐 폴드 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="477cc-306">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="477cc-307">명령을 사용 하 여 버그를 발생 하는 경우 `dotnet aspnet-codegenerator razorpage` [이 GitHub 문제](https://github.com/aspnet/Scaffolding/issues/984)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="477cc-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="477cc-308">*Pages/Shared/_Layout cshtml* 파일에서 지 각를 **업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="477cc-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="477cc-309">연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="477cc-310">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="477cc-310">Seed the database</span></span>

<span data-ttu-id="477cc-311">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="477cc-312">`SeedData.Initialize`다음에서 호출 `Main` :</span><span class="sxs-lookup"><span data-stu-id="477cc-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="477cc-313">응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-313">Test that the app seeded the database.</span></span> <span data-ttu-id="477cc-314">Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="477cc-315">이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="477cc-316">사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="477cc-317">다음 세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-317">There are three security groups:</span></span>

* <span data-ttu-id="477cc-318">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="477cc-319">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="477cc-320">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="477cc-321">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="477cc-322">다음 이미지에서는 사용자 Rick ( `rick@example.com` )가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="477cc-323">Rick는 승인 된 연락처만 볼 수 **있으며** / **Delete Delete** 는 / 연락처에 대 한 새 링크를 **만듭니다** .</span><span class="sxs-lookup"><span data-stu-id="477cc-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="477cc-324">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="477cc-325">관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="477cc-327">다음 이미지에서 `manager@contoso.com` 는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인을 보여 주는 스크린샷 manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="477cc-329">다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-329">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자 보기](secure-data/_static/manager.png)

<span data-ttu-id="477cc-331">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="477cc-332">다음 이미지에서 `admin@contoso.com` 는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인을 보여 주는 스크린샷 admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="477cc-334">관리자에 게는 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-334">The administrator has all privileges.</span></span> <span data-ttu-id="477cc-335">연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="477cc-336">다음 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 앱을 만들었습니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="477cc-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="477cc-337">이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="477cc-338">`ContactIsOwnerAuthorizationHandler`: 사용자가 해당 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="477cc-339">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="477cc-340">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="477cc-341">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="477cc-341">Prerequisites</span></span>

<span data-ttu-id="477cc-342">이 자습서는 고급입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-342">This tutorial is advanced.</span></span> <span data-ttu-id="477cc-343">다음에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-343">You should be familiar with:</span></span>

* [<span data-ttu-id="477cc-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="477cc-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="477cc-345">인증</span><span class="sxs-lookup"><span data-stu-id="477cc-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="477cc-346">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="477cc-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="477cc-347">권한 부여</span><span class="sxs-lookup"><span data-stu-id="477cc-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="477cc-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="477cc-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="477cc-349">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="477cc-349">The starter and completed app</span></span>

<span data-ttu-id="477cc-350">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="477cc-351">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="477cc-352">시작 앱</span><span class="sxs-lookup"><span data-stu-id="477cc-352">The starter app</span></span>

<span data-ttu-id="477cc-353">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="477cc-354">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="477cc-355">보안 사용자 데이터</span><span class="sxs-lookup"><span data-stu-id="477cc-355">Secure user data</span></span>

<span data-ttu-id="477cc-356">다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="477cc-357">완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="477cc-358">사용자에 게 연락처 데이터 연결</span><span class="sxs-lookup"><span data-stu-id="477cc-358">Tie the contact data to the user</span></span>

<span data-ttu-id="477cc-359">ASP.NET [Identity](xref:security/authentication/identity) 사용자 ID를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="477cc-360">`OwnerID` `ContactStatus` 모델에 및을 추가 합니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="477cc-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="477cc-361">`OwnerID``AspNetUser`데이터베이스에 있는 테이블의 사용자 ID입니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="477cc-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="477cc-362">`Status`필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="477cc-363">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="477cc-364">역할 서비스 추가 Identity</span><span class="sxs-lookup"><span data-stu-id="477cc-364">Add Role services to Identity</span></span>

<span data-ttu-id="477cc-365">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="477cc-366">인증 된 사용자 필요</span><span class="sxs-lookup"><span data-stu-id="477cc-366">Require authenticated users</span></span>

<span data-ttu-id="477cc-367">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="477cc-368">특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다 `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="477cc-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="477cc-369">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 하면 새로 추가 된 Razor 페이지와 컨트롤러를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="477cc-370">기본적으로 인증을 요구 하는 것은 특성을 포함 하는 새 컨트롤러 및 페이지에 의존 하는 것 보다 안전 Razor 합니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="477cc-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="477cc-371">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스, 정보 및 연락처 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="477cc-372">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="477cc-372">Configure the test account</span></span>

<span data-ttu-id="477cc-373">`SeedData`클래스는 두 개의 계정 즉, 관리자와 관리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="477cc-374">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="477cc-375">프로젝트 디렉터리 ( *Program.cs* 가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="477cc-376">강력한 암호를 지정 하지 않으면가 호출 될 때 예외가 throw 됩니다 `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="477cc-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="477cc-377">`Main`테스트 암호를 사용 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="477cc-378">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="477cc-379">`Initialize`클래스의 메서드를 업데이트 `SeedData` 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="477cc-380">관리자 사용자 ID와 `ContactStatus` 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="477cc-381">연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="477cc-382">모든 연락처에 사용자 ID 및 상태를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="477cc-383">하나의 연락처만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="477cc-384">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="477cc-385">*권한 부여* 폴더를 만들고 `ContactIsOwnerAuthorizationHandler` 여기에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="477cc-386">는 `ContactIsOwnerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="477cc-387">`ContactIsOwnerAuthorizationHandler`호출 [컨텍스트입니다. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="477cc-388">권한 부여 처리기 일반적:</span><span class="sxs-lookup"><span data-stu-id="477cc-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="477cc-389">`context.Succeed`요구 사항이 충족 되 면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="477cc-390">`Task.CompletedTask`요구 사항이 충족 되지 않으면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="477cc-391">`Task.CompletedTask` 은 (는) 성공 또는 실패 하지 않으므로 &mdash; 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="477cc-392">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="477cc-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="477cc-393">앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="477cc-394">`ContactIsOwnerAuthorizationHandler` 는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="477cc-395">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-395">Create a manager authorization handler</span></span>

<span data-ttu-id="477cc-396">`ContactManagerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="477cc-397">는 `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="477cc-398">관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="477cc-399">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="477cc-400">`ContactAdministratorsAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="477cc-401">는 `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="477cc-402">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="477cc-403">권한 부여 처리기 등록</span><span class="sxs-lookup"><span data-stu-id="477cc-403">Register the authorization handlers</span></span>

<span data-ttu-id="477cc-404">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="477cc-405">는 `ContactIsOwnerAuthorizationHandler` Entity Framework Core를 기반으로 하는 ASP.NET Core을 사용 합니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="477cc-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="477cc-406">종속성 주입을 통해에 사용할 수 있도록 서비스 컬렉션에 처리기를 `ContactsController` 등록 [dependency injection](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="477cc-407">끝에 다음 코드를 추가 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="477cc-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="477cc-408">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 는 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="477cc-409">이는 EF를 사용 하지 않고 필요한 모든 정보가 메서드의 매개 변수에 단일 항목 때문에 발생 `Context` `HandleRequirementAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="477cc-410">인증 지원</span><span class="sxs-lookup"><span data-stu-id="477cc-410">Support authorization</span></span>

<span data-ttu-id="477cc-411">이 섹션에서는 페이지를 업데이트 하 Razor 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="477cc-412">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="477cc-413">클래스를 검토 `ContactOperations` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="477cc-414">이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="477cc-415">연락처 페이지에 대 한 기본 클래스 만들기 Razor</span><span class="sxs-lookup"><span data-stu-id="477cc-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="477cc-416">연락처 페이지에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="477cc-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="477cc-417">기본 클래스는 초기화 코드를 한 위치에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="477cc-418">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="477cc-418">The preceding code:</span></span>

* <span data-ttu-id="477cc-419">`IAuthorizationService`권한 부여 처리기에 액세스 하는 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="477cc-420">서비스를 추가 Identity `UserManager` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="477cc-421">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="477cc-422">CreateModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-422">Update the CreateModel</span></span>

<span data-ttu-id="477cc-423">기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다 `DI_BasePageModel` .</span><span class="sxs-lookup"><span data-stu-id="477cc-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="477cc-424">메서드를 `CreateModel.OnPostAsync` 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="477cc-425">모델에 사용자 ID를 추가 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="477cc-426">권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="477cc-427">IndexModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-427">Update the IndexModel</span></span>

<span data-ttu-id="477cc-428">`OnGetAsync`승인 된 연락처만 일반 사용자에 게 표시 되도록 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="477cc-429">EditModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-429">Update the EditModel</span></span>

<span data-ttu-id="477cc-430">사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="477cc-431">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="477cc-432">특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="477cc-433">리소스 기반 권한 부여는 필수적 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="477cc-434">앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="477cc-435">리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="477cc-436">DeleteModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-436">Update the DeleteModel</span></span>

<span data-ttu-id="477cc-437">권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="477cc-438">권한 부여 서비스를 뷰에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="477cc-439">현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="477cc-440">모든 보기에서 사용할 수 있도록 *views/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="477cc-441">위의 태그는 여러 문을 추가 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="477cc-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="477cc-442">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="477cc-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="477cc-443">데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="477cc-444">링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="477cc-445">사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="477cc-446">Razor페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="477cc-447">업데이트 세부 정보</span><span class="sxs-lookup"><span data-stu-id="477cc-447">Update Details</span></span>

<span data-ttu-id="477cc-448">관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="477cc-449">세부 정보 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="477cc-450">역할에 사용자 추가 또는 제거</span><span class="sxs-lookup"><span data-stu-id="477cc-450">Add or remove a user to a role</span></span>

<span data-ttu-id="477cc-451">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="477cc-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="477cc-452">사용자가 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-452">Removing privileges from a user.</span></span> <span data-ttu-id="477cc-453">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="477cc-454">사용자에 게 권한 추가</span><span class="sxs-lookup"><span data-stu-id="477cc-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="477cc-455">완성 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="477cc-455">Test the completed app</span></span>

<span data-ttu-id="477cc-456">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="477cc-457">강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="477cc-458">예를 들어 `Passw0rd!` 은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="477cc-459">프로젝트의 폴더에서 다음 명령을 실행 합니다 `<PW>` . 여기서은 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="477cc-460">데이터베이스 삭제 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="477cc-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="477cc-461">응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="477cc-462">완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="477cc-463">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="477cc-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="477cc-464">다른 사용자로 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="477cc-465">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-465">Verify the following operations:</span></span>

* <span data-ttu-id="477cc-466">등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="477cc-467">등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="477cc-468">관리자는 연락처 데이터를 승인/거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="477cc-469">`Details`보기는 **승인** 및 **거부** 단추를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="477cc-470">관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="477cc-471">사용자</span><span class="sxs-lookup"><span data-stu-id="477cc-471">User</span></span>                | <span data-ttu-id="477cc-472">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="477cc-472">Seeded by the app</span></span> | <span data-ttu-id="477cc-473">옵션</span><span class="sxs-lookup"><span data-stu-id="477cc-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="477cc-474">아니요</span><span class="sxs-lookup"><span data-stu-id="477cc-474">No</span></span>                | <span data-ttu-id="477cc-475">자신의 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="477cc-476">Yes</span><span class="sxs-lookup"><span data-stu-id="477cc-476">Yes</span></span>               | <span data-ttu-id="477cc-477">자신의 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="477cc-478">Yes</span><span class="sxs-lookup"><span data-stu-id="477cc-478">Yes</span></span>               | <span data-ttu-id="477cc-479">모든 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="477cc-480">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="477cc-481">관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="477cc-482">이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="477cc-483">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-483">Create the starter app</span></span>

* <span data-ttu-id="477cc-484">Razor"연락처 관리자" 라는 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="477cc-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="477cc-485">**개별 사용자 계정을** 사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="477cc-486">네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="477cc-487">`-uld` SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="477cc-488">*모델/연락처를 추가 합니다. cs* :</span><span class="sxs-lookup"><span data-stu-id="477cc-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="477cc-489">모델을 스 캐 폴드 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="477cc-490">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="477cc-491">*Pages/_Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="477cc-492">연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="477cc-493">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="477cc-493">Seed the database</span></span>

<span data-ttu-id="477cc-494">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="477cc-495">`SeedData.Initialize`다음에서 호출 `Main` :</span><span class="sxs-lookup"><span data-stu-id="477cc-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="477cc-496">응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-496">Test that the app seeded the database.</span></span> <span data-ttu-id="477cc-497">Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="477cc-498">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="477cc-498">Additional resources</span></span>

* [<span data-ttu-id="477cc-499">Azure App Service에서 .NET Core 및 SQL 데이터베이스 웹앱 빌드</span><span class="sxs-lookup"><span data-stu-id="477cc-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="477cc-500">[권한 부여 랩을 ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="477cc-501">이 랩에서는이 자습서에서 소개 하는 보안 기능에 대해 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="477cc-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="477cc-502">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="477cc-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
