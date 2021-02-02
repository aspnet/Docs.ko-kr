---
title: Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssembly에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758265"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="c779d-103">AAD(Azure Active Directory) 그룹, 관리자 역할 및 앱 역할</span><span class="sxs-lookup"><span data-stu-id="c779d-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="c779d-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c779d-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="c779d-105">AAD(Azure Active Directory)는 ASP.NET Core Identity와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c779d-106">그룹</span><span class="sxs-lookup"><span data-stu-id="c779d-106">Groups</span></span>
  * <span data-ttu-id="c779d-107">보안</span><span class="sxs-lookup"><span data-stu-id="c779d-107">Security</span></span>
  * <span data-ttu-id="c779d-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="c779d-108">Microsoft 365</span></span>
  * <span data-ttu-id="c779d-109">분포</span><span class="sxs-lookup"><span data-stu-id="c779d-109">Distribution</span></span>
* <span data-ttu-id="c779d-110">역할</span><span class="sxs-lookup"><span data-stu-id="c779d-110">Roles</span></span>
  * <span data-ttu-id="c779d-111">AAD 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="c779d-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="c779d-112">앱 역할</span><span class="sxs-lookup"><span data-stu-id="c779d-112">App Roles</span></span>

<span data-ttu-id="c779d-113">이 문서의 지침은 다음 항목에서 설명하는 Blazor WebAssembly AAD 배포 시나리오에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="c779d-114">Microsoft 계정을 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="c779d-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="c779d-115">AAD를 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="c779d-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="c779d-116">AAD를 사용하는 호스트형</span><span class="sxs-lookup"><span data-stu-id="c779d-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="c779d-117">이 문서에서는 클라이언트 앱과 서버 앱에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-117">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="c779d-118">**CLIENT**: 독립 실행형 Blazor WebAssembly 앱 또는 호스트된 Blazor 솔루션의 *`Client`* 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-118">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="c779d-119">**서버**: 독립 실행형 ASP.NET Core 서버 API/웹 API 앱 또는 호스트된 Blazor 솔루션의 *`Server`* 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-119">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="c779d-120">범위</span><span class="sxs-lookup"><span data-stu-id="c779d-120">Scopes</span></span>

<span data-ttu-id="c779d-121">사용자 프로필, 역할 할당, 그룹 멤버 자격에 대한 [Microsoft Graph API](/graph/use-the-api) 호출을 허용하기 위해 Azure Portal에서 (`https://graph.microsoft.com/User.Read`) [Graph API 사용 권한(범위)](/graph/permissions-reference)을 사용하여 **CLIENT** 가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-121">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="c779d-122">역할 및 그룹 멤버 자격 데이터를 위해 Graph API를 호출하는 **SERVER** 앱에 Azure Portal에서 `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API 사용 권한(범위)](/graph/permissions-reference)가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-122">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="c779d-123">이러한 범위는 이 문서 첫 번째 섹션에 나열된 주제에서 설명하는 AAD 배포 시나리오에 필요한 범위 외에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-123">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="c779d-124">"사용 권한" 단어와 "범위" 단어는 Azure Portal과 다양한 Microsoft 및 외부 설명서에서 서로 바꿔 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-124">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="c779d-125">이 문서에서는 Azure Portal에서 앱에 할당된 사용 권한에 "범위"라는 단어를 계속 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-125">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="c779d-126">그룹 멤버 자격 클레임 특성</span><span class="sxs-lookup"><span data-stu-id="c779d-126">Group Membership Claims attribute</span></span>

<span data-ttu-id="c779d-127">Azure Portal에서 **CLIENT** 및 **SERVER** 앱의 앱 매니페스트에서 [`groupMembershipClaims` 특성](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)을 `All`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-127">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="c779d-128">`All` 값은 로그인한 사용자가 멤버인 모든 보안 그룹, 배포 그룹 및 역할을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-128">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="c779d-129">앱의 Azure Portal 등록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-129">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="c779d-130">사이드바에서 **관리** > **매니페스트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-130">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="c779d-131">`groupMembershipClaims` 특성을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-131">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="c779d-132">값을 `All`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-132">Set the value to `All`.</span></span>
1. <span data-ttu-id="c779d-133">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-133">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="c779d-134">사용자 지정 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="c779d-134">Custom user account</span></span>

<span data-ttu-id="c779d-135">Azure Portal에서 사용자를 AAD 보안 그룹 및 AAD 관리자 역할에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-135">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="c779d-136">이 문서의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-136">The examples in this article:</span></span>

* <span data-ttu-id="c779d-137">서버 API 데이터에 엑세스할 수 있는 권한 부여를 위해 사용자가 Azure Portal AAD 테넌트의 AAD 대금 청구 관리자 역할에 할당되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-137">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="c779d-138">[권한 부여 정책](xref:security/authorization/policies)을 사용하여 **CLIENT** 및 **SERVER** 앱 내의 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-138">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="c779d-139">**CLIENT** 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하여 다음 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-139">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="c779d-140">`Roles`: AAD 앱 역할 배열([앱 역할](#app-roles) 섹션에서 설명)</span><span class="sxs-lookup"><span data-stu-id="c779d-140">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="c779d-141">`Wids`: [잘 알려진 ID 클레임(`wids`)의 AAD 관리자 역할](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="c779d-141">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="c779d-142">`Oid`: 변경할 수 없는 [개체 식별자 클레임(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)(여러 테넌트와 테넌트 내에서 사용자를 고유하게 식별)</span><span class="sxs-lookup"><span data-stu-id="c779d-142">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="c779d-143">`foreach` 루프에서 배열 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 배열 속성에 빈 배열을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-143">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="c779d-144">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="c779d-144">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="c779d-145">[`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)용 **CLIENT** 앱의 프로젝트 파일에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-145">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="c779d-146"><xref:blazor/security/webassembly/graph-api#graph-sdk> 문서의 *Graph SDK* 섹션에서 Graph SDK 유틸리티 클래스 및 구성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-146">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="c779d-147">`GraphClientExtensions` 클래스에서 `AuthenticateRequestAsync` 메서드에 있는 액세스 토큰의 `User.Read` 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-147">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="c779d-148">**CLIENT** 앱에 다음 사용자 지정 사용자 계정 팩터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-148">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="c779d-149">사용자 지정 사용자 팩터리는 다음을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-149">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="c779d-150">앱 역할 클레임(`appRole`)([앱 역할](#app-roles) 섹션에서 설명)</span><span class="sxs-lookup"><span data-stu-id="c779d-150">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="c779d-151">AAD 관리자 역할 클레임(`directoryRole`)</span><span class="sxs-lookup"><span data-stu-id="c779d-151">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="c779d-152">사용자의 휴대폰 번호에 대한 예제 사용자 프로필 데이터 클레임(`mobilePhone`)</span><span class="sxs-lookup"><span data-stu-id="c779d-152">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="c779d-153">AAD 그룹 클레임(`directoryGroup`)</span><span class="sxs-lookup"><span data-stu-id="c779d-153">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="c779d-154">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="c779d-154">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="c779d-155">위의 코드에는 전이적 멤버 자격이 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-155">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="c779d-156">앱에 직접 및 전이적 그룹 멤버 자격 클레임이 필요한 경우 `MemberOf` 속성(`IUserMemberOfCollectionWithReferencesRequestBuilder`)을 `TransitiveMemberOf`(`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-156">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="c779d-157">Microsoft Identity Platform 2.0에서 반환된 GUID 값이 AAD 관리자 역할 **엔터티 ID** 이고 [**역할 템플릿 ID**](/azure/active-directory/roles/permissions-reference#role-template-ids)가 아니므로 앞의 코드는 AAD 관리자 역할(`#microsoft.graph.directoryRole` 형식)인 그룹 멤버 자격 클레임(`groups`)을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-157">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="c779d-158">엔터티 ID는 Microsoft Identity Platform 2.0의 테넌트에서 안정적이지 않으며 앱에서 사용자에 대한 권한 부여 정책을 만드는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-158">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="c779d-159">**`wids` 클레임에서 제공하는** AAD 관리자 역할에 항상 **역할 템플릿 ID** 를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-159">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="c779d-160">**CLIENT** 앱의 `Program.Main`에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-160">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="c779d-161">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="c779d-161">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="c779d-162">권한 부여 구성</span><span class="sxs-lookup"><span data-stu-id="c779d-162">Authorization configuration</span></span>

<span data-ttu-id="c779d-163">**CLIENT** 앱에서 `Program.Main`의 각 [앱 역할](#app-roles), AAD 관리자 역할 또는 보안 그룹의 [정책](xref:security/authorization/policies)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-163">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="c779d-164">다음 예제에서는 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-164">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="c779d-165">AAD 관리자 역할 ID의 전체 목록은 Azure 설명서에서 [역할 템플릿 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-165">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="c779d-166">권한 부여 정책에 대한 자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-166">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c779d-167">다음 예제에서 **CLIENT** 앱은 앞의 정책을 사용하여 사용자에게 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-167">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="c779d-168">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)는 이 정책을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-168">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="c779d-169">전체 구성 요소에 대한 액세스는 [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)을 사용하는 정책을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-169">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="c779d-170">사용자가 로그인되어 있지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인한 후에 구성 요소로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-170">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="c779d-171">[절차 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-171">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="c779d-172">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="c779d-172">`Pages/CheckPolicy.razor`:</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="c779d-173">서버 API/웹 API 액세스 권한 부여</span><span class="sxs-lookup"><span data-stu-id="c779d-173">Authorize server API/web API access</span></span>

<span data-ttu-id="c779d-174">**SERVER** API 앱은 액세스 토큰에 `groups`, `wids` 및 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 클레임이 포함된 경우 보안 그룹, AAD 관리자 역할 및 앱 역할의 [권한 부여 정책](xref:security/authorization/policies)을 사용하여 보안 API 엔드포인트에 액세스할 수 있는 권한을 사용자에게 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-174">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="c779d-175">다음 예제는 `wids`(잘 알려진 ID/역할 템플릿 ID) 클레임을 사용하여 `Startup.ConfigureServices`에서 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-175">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="c779d-176">AAD 관리자 역할 ID의 전체 목록은 Azure 설명서에서 [역할 템플릿 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-176">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="c779d-177">권한 부여 정책에 대한 자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-177">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c779d-178">**SERVER** 앱의 컨트롤러에 대한 액세스는 정책 이름과 함께 [`[Authorize]` 특성](xref:security/authorization/simple) 사용을 기반으로 할 수 있습니다(API 설명서: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span><span class="sxs-lookup"><span data-stu-id="c779d-178">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="c779d-179">다음 예제는 정책 이름 `BillingAdministrator`를 사용하여 `BillingDataController`의 청구 데이터에 대한 액세스를 Azure 대금 청구 관리자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-179">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="c779d-180">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-180">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="c779d-181">앱 역할</span><span class="sxs-lookup"><span data-stu-id="c779d-181">App Roles</span></span>

<span data-ttu-id="c779d-182">Azure Portal에서 Azure 역할 멤버 자격 클레임을 제공하도록 앱을 구성하려면 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c779d-182">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="c779d-183">다음 예제에서는 **CLIENT** 및 **SERVER** 앱이 두 개의 역할로 구성되고 이 역할이 테스트 사용자에게 할당된다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-183">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="c779d-184">호스트된 Blazor WebAssembly 앱 또는 독립 실행형 앱의 클라이언트-서버 쌍(독립 실행형 Blazor WebAssembly 앱 및 독립 실행형 ASP.NET Core 서버 API/웹 API 앱)을 개발하는 경우 클라이언트 및 서버 Azure Portal 앱 등록 `appRoles` 매니페스트 속성은 구성된 동일한 역할을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-184">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="c779d-185">클라이언트 앱의 매니페스트에 역할을 설정한 후 역할 전체를 서버 앱의 매니페스트에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-185">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="c779d-186">클라이언트 앱 등록과 서버 앱 등록 간에 매니페스트 `appRoles`를 미러링하지 않는 경우 액세스 토큰에 올바른 역할 클레임이 있는 경우에도 서버 API/웹 API의 인증된 사용자에 대해 역할 클레임이 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-186">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="c779d-187">Azure AD Premium 계정을 사용하지 않고서는 그룹에 역할을 할당할 수 없지만 표준 Azure 계정을 사용하여 역할을 사용자에게 할당하고 사용자에 대한 역할 클레임을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-187">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="c779d-188">이 섹션의 지침에는 AAD Premium 계정이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-188">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="c779d-189">Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-189">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="c779d-190">[사용자 지정 사용자 계정](#custom-user-account) 섹션에 표시된 `CustomAccountFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에 대해 작동하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-190">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="c779d-191">[사용자 지정 사용자 계정](#custom-user-account) 섹션에 표시된 대로 **CLIENT** 앱에 `CustomAccountFactory`를 추가하고 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-191">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="c779d-192">원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-192">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="c779d-193">**CLIENT** 앱의 `Program.Main`에서 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 검사를 위한 역할 클레임으로 "`appRole`"이라는 클레임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-193">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="c779d-194">`directoryRoles` 클레임(ADD 관리자 역할)을 사용하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>에 "`directoryRoles`"를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-194">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c779d-195">**SERVER** 앱의 `Startup.ConfigureServices`에서 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 검사를 위한 역할 클레임으로 "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`"이라는 클레임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-195">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="c779d-196">`wids` 클레임(ADD 관리자 역할)을 사용하려면 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>에 "`wids`"를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-196">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c779d-197">이 시점에서는 구성 요소 권한 부여 방식이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-197">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="c779d-198">**CLIENT** 앱의 구성 요소에 있는 권한 부여 메커니즘은 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-198">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="c779d-199">`AuthorizeView` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="c779d-199">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="c779d-200">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="c779d-200">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="c779d-201">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="c779d-201">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="c779d-202">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-202">Multiple role tests are supported:</span></span>

* <span data-ttu-id="c779d-203">사용자는 `AuthorizeView` 구성 요소가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-203">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="c779d-204">사용자는 `AuthorizeView` 구성 요소가 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-204">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="c779d-205">사용자는 `[Authorize]` 특성이 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-205">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="c779d-206">사용자는 `[Authorize]` 특성이 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-206">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="c779d-207">사용자는 프로시저 코드가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-207">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="c779d-208">앞의 예제에서 [조건부 OR(`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 를 [조건부 AND(`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)로 변경하면 사용자가 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-208">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="c779d-209">**SERVER** 앱의 구성 요소에 있는 권한 부여 메커니즘은 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-209">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="c779d-210">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="c779d-210">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="c779d-211">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="c779d-211">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="c779d-212">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-212">Multiple role tests are supported:</span></span>

* <span data-ttu-id="c779d-213">사용자는 `[Authorize]` 특성이 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-213">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="c779d-214">사용자는 `[Authorize]` 특성이 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-214">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="c779d-215">사용자는 프로시저 코드가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-215">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="c779d-216">앞의 예제에서 [조건부 OR(`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 를 [조건부 AND(`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)로 변경하면 사용자가 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c779d-216">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="c779d-217">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c779d-217">Additional resources</span></span>

* [<span data-ttu-id="c779d-218">역할 템플릿 ID(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="c779d-218">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="c779d-219">`groupMembershipClaims` 특성(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="c779d-219">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="c779d-220">방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="c779d-220">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="c779d-221">애플리케이션 역할(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="c779d-221">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
