---
title: 'Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: :::no-loc(Blazor WebAssembly):::에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 680b44a705b66be0aab824487119cdb118b44d0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055310"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="a7d65-103">Azure Active Directory(AAD) 그룹, 관리자 역할, 사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="a7d65-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a7d65-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a7d65-105">AAD(Azure Active Directory)는 :::no-loc(ASP.NET Core Identity):::와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="a7d65-106">사용자 정의 그룹</span><span class="sxs-lookup"><span data-stu-id="a7d65-106">User-defined groups</span></span>
  * <span data-ttu-id="a7d65-107">보안</span><span class="sxs-lookup"><span data-stu-id="a7d65-107">Security</span></span>
  * <span data-ttu-id="a7d65-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="a7d65-108">Microsoft 365</span></span>
  * <span data-ttu-id="a7d65-109">분포</span><span class="sxs-lookup"><span data-stu-id="a7d65-109">Distribution</span></span>
* <span data-ttu-id="a7d65-110">역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-110">Roles</span></span>
  * <span data-ttu-id="a7d65-111">AAD 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="a7d65-112">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-112">User-defined roles</span></span>

<span data-ttu-id="a7d65-113">이 문서의 지침은 다음 항목에서 설명하는 :::no-loc(Blazor WebAssembly)::: AAD 배포 시나리오에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-113">The guidance in this article applies to the :::no-loc(Blazor WebAssembly)::: AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="a7d65-114">Microsoft 계정을 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="a7d65-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a7d65-115">AAD를 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="a7d65-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a7d65-116">AAD를 사용하는 호스트형</span><span class="sxs-lookup"><span data-stu-id="a7d65-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="a7d65-117">범위</span><span class="sxs-lookup"><span data-stu-id="a7d65-117">Scopes</span></span>

<span data-ttu-id="a7d65-118">5개 이상의 AAD 관리자 역할 및 보안 그룹 구성원 자격이 있는 앱 사용자에게는 [Microsoft Graph API](/graph/use-the-api) 호출이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="a7d65-119">Graph API 호출을 허용하려면 Azure Portal에서 호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 또는 *`Client`* 앱에 다음 [Graph API 권한(범위)](/graph/permissions-reference)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted :::no-loc(Blazor)::: solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="a7d65-120">`Directory.Read.All`은 최소 권한 범위이며 이 문서에 설명된 예제에 사용되는 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-120">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a7d65-121">사용자 정의 그룹 및 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a7d65-122">Azure Portal에서 `groups` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 다음 Azure 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="a7d65-123">사용자 정의 AAD 그룹 및 AAD 관리자 역할에 사용자를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="a7d65-124">Azure AD 보안 그룹을 사용하는 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="a7d65-125">`groupMembershipClaims` 특성</span><span class="sxs-lookup"><span data-stu-id="a7d65-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="a7d65-126">다음 예제에서는 사용자가 AAD 대금 청구 관리자 역할에 할당되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="a7d65-127">AAD에서 보낸 단일 `groups` 클레임은 사용자의 그룹 및 역할을 JSON 배열의 개체 ID(GUID)로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="a7d65-128">앱은 그룹 및 역할의 JSON 배열을 앱이 기준으로 삼아 [정책](xref:security/authorization/policies)을 빌드할 수 있는 개별 `group` 클레임으로 변환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="a7d65-129">할당된 AAD 관리자 역할 및 사용자 정의 그룹 수가 5개를 초과하면 AAD는 `groups` 클레임을 전송하는 대신 `true` 값으로 `hasgroups` 클레임을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="a7d65-130">사용자에게 할당된 역할과 그룹이 5개 이상일 수 있는 모든 앱은 사용자의 역할 및 그룹을 가져오기 위해 별도의 Graph API 호출을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="a7d65-131">이 문서에서 제공하는 예제 구현은 이 시나리오를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="a7d65-132">`groups` 및 `hasgroups` 클레임 정보([Microsoft ID 플랫폼 액세스 토큰: 페이로드 클레임](/azure/active-directory/develop/access-tokens#payload-claims) 문서)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="a7d65-133">그룹 및 역할의 배열 속성을 포함하도록 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="a7d65-134">나중에 `foreach` 루프에서 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 속성에 빈 배열을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="a7d65-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7d65-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7d65-136">다음 방법 중 **하나** 를 사용하여 AAD 그룹 및 역할에 대한 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-136">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="a7d65-137">Graph SDK 사용</span><span class="sxs-lookup"><span data-stu-id="a7d65-137">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="a7d65-138">명명된 `HttpClient` 사용</span><span class="sxs-lookup"><span data-stu-id="a7d65-138">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="a7d65-139">Graph SDK 사용</span><span class="sxs-lookup"><span data-stu-id="a7d65-139">Use the Graph SDK</span></span>

<span data-ttu-id="a7d65-140">[`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)에 대해 호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-140">Add a package reference to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="a7d65-141"><xref:blazor/security/webassembly/graph-api#graph-sdk> 문서의 *Graph SDK* 섹션에서 Graph SDK 유틸리티 클래스 및 구성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-141">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="a7d65-142">호스트된 :::no-loc(Blazor)::: 솔루션(`CustomAccountFactory.cs`)의 독립 실행형 앱 또는 *`Client`* 앱에 다음 사용자 지정 사용자 계정 팩터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-142">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted :::no-loc(Blazor)::: solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="a7d65-143">사용자 지정 사용자 팩터리는 역할 및 그룹 클레임을 처리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-143">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="a7d65-144">`roles` 클레임 배열은 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-144">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a7d65-145">`hasgroups` 클레임이 있는 경우 Graph SDK를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-145">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = user:::no-loc(Identity):::.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                user:::no-loc(Identity):::.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="a7d65-146">위의 코드에는 전이적 멤버 자격이 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-146">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="a7d65-147">앱에 직접 및 전이적 그룹 멤버 자격 클레임이 필요한 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-147">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="a7d65-148">`groupsAndAzureRoles`에 대한 `IUserMemberOfCollectionWithReferencesPage` 형식을 `IUserTransitiveMemberOfCollectionWithReferencesPage`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-148">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="a7d65-149">사용자의 그룹 및 역할을 요청하는 경우 `MemberOf` 속성을 `TransitiveMemberOf`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-149">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="a7d65-150">`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-150">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a><span data-ttu-id="a7d65-151">명명된 `HttpClient` 사용</span><span class="sxs-lookup"><span data-stu-id="a7d65-151">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="a7d65-152">호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에서 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-152">In the standalone app or the *`Client`* app of a hosted :::no-loc(Blazor)::: solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="a7d65-153">역할 및 그룹 정보를 가져오는 Graph API 호출에 올바른 범위를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-153">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="a7d65-154">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7d65-154">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="a7d65-155">`Program.Main`(`Program.cs`)에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 구현 서비스를 추가하고 Graph API 요청을 위한 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-155">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="a7d65-156">다음 예제에서는 클라이언트의 이름을 `GraphAPI`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-156">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="a7d65-157">Graph API 호출에서 OData(개방형 데이터 프로토콜) 역할 및 그룹을 수신하는 AAD 디렉터리 개체 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-157">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="a7d65-158">OData는 JSON 형식으로 도착하고 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 호출은 `DirectoryObjects` 클래스의 인스턴스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-158">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="a7d65-159">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7d65-159">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="a7d65-160">역할 및 그룹 클레임을 처리하는 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-160">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="a7d65-161">다음 예제 구현에서는 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명하는 `roles` 클레임 배열도 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-161">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a7d65-162">`hasgroups` 클레임이 있는 경우 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-162">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="a7d65-163">이 구현에서는 Microsoft :::no-loc(Identity)::: Platform v1.0 엔드포인트`https://graph.microsoft.com/v1.0/me/memberOf`([API 설명서](/graph/api/user-list-memberof))를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-163">This implementation uses the Microsoft :::no-loc(Identity)::: Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="a7d65-164">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7d65-164">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            user:::no-loc(Identity):::.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        user:::no-loc(Identity):::.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="a7d65-165">원래 `groups` 클레임이 있다면 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-165">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="a7d65-166">이 예제의 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-166">The approach in this example:</span></span>
>
> * <span data-ttu-id="a7d65-167">사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 추가하여 나가는 요청에 액세스 토큰을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-167">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="a7d65-168">안전한 외부 웹 API 엔드포인트에 API 요청을 하기 위해 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-168">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="a7d65-169">명명된 <xref:System.Net.Http.HttpClient>를 사용하여 권한 있는 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-169">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="a7d65-170">이 방법에 대한 일반적인 설명은 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 문서에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-170">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="a7d65-171">호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 앱 또는 *`Client`* 앱의 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-171">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="a7d65-172">앱의 추가 범위로 `Directory.Read.All` 범위에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-172">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a><span data-ttu-id="a7d65-173">권한 부여 구성</span><span class="sxs-lookup"><span data-stu-id="a7d65-173">Authorization configuration</span></span>

<span data-ttu-id="a7d65-174">`Program.Main`의 그룹 또는 역할에 대해 [정책](xref:security/authorization/policies)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-174">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="a7d65-175">다음 예제에서는 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-175">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a7d65-176">AAD 역할 개체 ID의 전체 목록은 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-176">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="a7d65-177">다음 예제에서 앱은 앞에 나온 정책을 사용하여 사용자에게 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-177">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="a7d65-178">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)는 이 정책을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-178">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="a7d65-179">전체 구성 요소에 대한 액세스는 [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)을 사용하는 정책을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-179">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="a7d65-180">사용자가 로그인되어 있지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인한 후에 구성 요소로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-180">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="a7d65-181">[절차적 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-181">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a7d65-182">사용자 정의 그룹 및 관리자 역할에 서버 API 액세스 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a7d65-182">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a7d65-183">서버 API는 클라이언트 쪽 WebAssembly 앱의 사용자에게 페이지와 리소스에 대한 액세스 권한을 부여하는 것 외에도 보안 API 엔드포인트에 대한 액세스 권한을 사용자에게 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-183">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="a7d65-184">서버 앱이 사용자 액세스 토큰의 유효성을 검사한 후:</span><span class="sxs-lookup"><span data-stu-id="a7d65-184">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="a7d65-185">서버 API 앱은 액세스 토큰에서 사용자의 변경 불가능한 [개체 식별자 클레임(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)을 사용하여 Graph API의 액세스 토큰을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-185">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="a7d65-186">Graph API 호출은 사용자에 대해 [`memberOf`](/graph/api/user-list-memberof)를 호출하여 사용자의 Azure 사용자 정의 보안 그룹 및 관리자 역할 멤버 자격을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-186">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="a7d65-187">멤버 자격은 `group` 클레임을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-187">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="a7d65-188">[권한 부여 정책](xref:security/authorization/policies)을 사용하여 앱 전체에서 서버 API 엔드포인트에 대한 사용자 액세스를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-188">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a7d65-189">이 지침에는 현재 [AAD 사용자 정의 역할](#user-defined-roles)을 기준으로 한 사용자 권한 부여가 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-189">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="a7d65-190">이 섹션의 지침에서는 서버 API 앱을 Microsoft Graph API 호출에 대한 [‘디먼 앱’](/azure/active-directory/develop/scenario-daemon-overview)으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-190">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="a7d65-191">이 방법은 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-191">This approach does **not** :</span></span>

* <span data-ttu-id="a7d65-192">`access_as_user` 범위가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-192">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="a7d65-193">API를 요청하는 사용자/클라이언트를 대신하여 Graph API에 액세스하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-193">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="a7d65-194">서버 API 앱에서 Graph API를 호출하려면 Azure Portal의 `Directory.Read.All`에 대한 서버 API 앱 **애플리케이션** Graph API 범위만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-194">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="a7d65-195">이 방법을 사용하면 클라이언트 앱이 서버 API에서 명시적으로 허용하지 않는 디렉터리 데이터에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-195">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="a7d65-196">클라이언트 앱은 서버 API 앱의 컨트롤러 엔드포인트에만 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-196">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="a7d65-197">Azure 구성</span><span class="sxs-lookup"><span data-stu-id="a7d65-197">Azure configuration</span></span>

* <span data-ttu-id="a7d65-198">‘서버’ 앱 등록에 보안 그룹의 최소 권한 액세스 수준인 `Directory.Read.All`에 대한 **애플리케이션** ( **위임** 이 아님) Graph API 범위가 지정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-198">Confirm that the *Server* app registration is given **Application** (not **Delegated** ) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="a7d65-199">범위 할당을 수행한 후 관리자 동의가 범위에 적용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-199">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="a7d65-200">서버 앱에 새 클라이언트 암호를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-200">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="a7d65-201">[앱 설정](#app-settings) 섹션에서 앱 구성을 위한 암호를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-201">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="a7d65-202">앱 설정</span><span class="sxs-lookup"><span data-stu-id="a7d65-202">App settings</span></span>

<span data-ttu-id="a7d65-203">앱 설정 파일(`:::no-loc(appsettings.json):::` 또는 `appsettings.Production.json`)에서 Azure Portal의 서버 앱 클라이언트 암호를 사용하여 `ClientSecret` 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-203">In the app settings file (`:::no-loc(appsettings.json):::` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="a7d65-204">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-204">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a7d65-205">테넌트 게시자 도메인이 확인되지 않으면 사용자/클라이언트 액세스에 대한 서버 API 범위에서 `https://` 기반 URI를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-205">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="a7d65-206">이 시나리오에서 서버 API 앱에는 `:::no-loc(appsettings.json):::` 파일의 `Audience` 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-206">In this scenario, the server API app requires `Audience` configuration in the `:::no-loc(appsettings.json):::` file.</span></span> <span data-ttu-id="a7d65-207">다음 구성에서 `Audience` 값의 끝에는 기본 범위 `/{DEFAULT SCOPE}`가 포함되지 **않습니다**. 여기서 `{DEFAULT SCOPE}` 자리 표시자는 기본 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-207">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="a7d65-208">위의 예제 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-208">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="a7d65-209">테넌트 도메인은 `contoso.onmicrosoft.com`입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-209">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="a7d65-210">서버 API 앱 `ClientId`는 `41451fa7-82d9-4673-8fa5-69eff5a761fd`입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-210">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="a7d65-211">명시적으로 `Audience`를 구성하면 `api://` 기반 API 범위가 있는 확인된 게시자 도메인이 앱에 필요하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="a7d65-211">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="a7d65-212">자세한 내용은 <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-212">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="a7d65-213">네임스페이스 수준에서 구성하는 권한 부여 정책도</span><span class="sxs-lookup"><span data-stu-id="a7d65-213">Authorization policies</span></span>

<span data-ttu-id="a7d65-214">그룹 개체 ID와 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids)를 기준으로 서버 앱의 `Startup.ConfigureServices`(`Startup.cs`)에서 AAD 보안 그룹 및 AAD 관리자 역할의 [권한 부여 정책](xref:security/authorization/policies)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-214">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="a7d65-215">예를 들어 Azure 대금 청구 관리자 역할 정책의 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-215">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a7d65-216">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-216">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="a7d65-217">컨트롤러 액세스</span><span class="sxs-lookup"><span data-stu-id="a7d65-217">Controller access</span></span>

<span data-ttu-id="a7d65-218">서버 앱 컨트롤러에 대한 정책이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-218">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="a7d65-219">다음 예제는 [권한 부여 정책](#authorization-policies) 섹션에서 구성된 대로 정책 이름 `BillingAdmin`을 사용하여 `BillingDataController`의 청구 데이터에 대한 액세스를 Azure 대금 청구 관리자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-219">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a7d65-220">패키지</span><span class="sxs-lookup"><span data-stu-id="a7d65-220">Packages</span></span>

<span data-ttu-id="a7d65-221">다음 패키지의 서버 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-221">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a7d65-222">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a7d65-222">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a7d65-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span><span class="sxs-lookup"><span data-stu-id="a7d65-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span></span>

### <a name="services"></a><span data-ttu-id="a7d65-224">서비스</span><span class="sxs-lookup"><span data-stu-id="a7d65-224">Services</span></span>

<span data-ttu-id="a7d65-225">‘서버’ 앱의 `Startup.ConfigureServices` 메서드에서, ‘서버’ 앱의 `Startup` 클래스에 있는 코드에는 추가 네임스페이스가 필요합니다. </span><span class="sxs-lookup"><span data-stu-id="a7d65-225">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a7d65-226">다음 네임스페이스를 `Startup.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-226">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.:::no-loc(Identity):::.Client;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="a7d65-227"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>를 구성하는 경우:</span><span class="sxs-lookup"><span data-stu-id="a7d65-227">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a7d65-228">필요한 경우 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>에 대한 처리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-228">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a7d65-229">예를 들어 앱은 실패한 인증을 로그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-229">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a7d65-230"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>에서 Graph API 호출을 실행하여 사용자의 그룹과 역할을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-230">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a7d65-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType>는 로깅 메시지에 PII(개인 식별 정보)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a7d65-232">테스트 사용자 계정을 사용하여 디버깅하는 경우에만 PII를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-232">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a7d65-233">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="a7d65-233">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="a7d65-234">위의 코드에서 다음 토큰 오류 처리는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-234">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="a7d65-235">`MsalUiRequiredException`: 앱에 충분한 권한(범위)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-235">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="a7d65-236">Azure Portal의 서버 API 앱 범위에 `Directory.Read.All`에 대한 **애플리케이션** 권한이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-236">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="a7d65-237">테넌트 관리자가 앱에 대한 사용 권한을 부여했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-237">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="a7d65-238">`MsalServiceException`(`AADSTS70011`): 범위가 `https://graph.microsoft.com/.default`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-238">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a7d65-239">패키지</span><span class="sxs-lookup"><span data-stu-id="a7d65-239">Packages</span></span>

<span data-ttu-id="a7d65-240">다음 패키지의 서버 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-240">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a7d65-241">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a7d65-241">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a7d65-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="a7d65-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="a7d65-243">서비스 구성</span><span class="sxs-lookup"><span data-stu-id="a7d65-243">Service configuration</span></span>

<span data-ttu-id="a7d65-244">서버 앱의 `Startup.ConfigureServices` 메서드는 Graph API 호출을 실행하는 논리를 추가하고 사용자의 보안 그룹 및 역할에 대한 사용자 `group` 클레임을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-244">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="a7d65-245">이 섹션의 예제 코드에서는 Microsoft :::no-loc(Identity)::: Platform v1.0을 기반으로 하는 ADAL(Active Directory 인증 라이브러리)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-245">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft :::no-loc(Identity)::: Platform v1.0.</span></span>

<span data-ttu-id="a7d65-246">서버 앱의 `Startup` 클래스에 있는 코드에는 추가 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-246">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a7d65-247">다음 `using` 문 집합에는 이 섹션 뒷부분에 나오는 코드의 필수 네임스페이스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-247">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="a7d65-248"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>를 구성하는 경우:</span><span class="sxs-lookup"><span data-stu-id="a7d65-248">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a7d65-249">필요한 경우 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>에 대한 처리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-249">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a7d65-250">예를 들어 앱은 실패한 인증을 로그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-250">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a7d65-251"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>에서 Graph API 호출을 실행하여 사용자의 그룹과 역할을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-251">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a7d65-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType>는 로깅 메시지에 PII(개인 식별 정보)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a7d65-253">테스트 사용자 계정을 사용하여 디버깅하는 경우에만 PII를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-253">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a7d65-254">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="a7d65-254">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="a7d65-255">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="a7d65-255">In the preceding example:</span></span>

* <span data-ttu-id="a7d65-256">액세스 토큰이 ADAL 토큰 캐시에 이미 저장되어 있을 수 있으므로 자동 토큰 획득(<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>)이 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-256">Silent token acquisition (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="a7d65-257">새 토큰을 요청하는 것보다 캐시에서 토큰을 얻는 것이 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-257">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="a7d65-258">캐시에서 액세스 토큰을 획득하지 않는 경우(<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 또는 <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>이 throw 됨) 클라이언트 자격 증명(<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>)을 사용한 사용자 어설션(<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>)을 통해 사용자를 대신하여 토큰을 얻습니다(<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="a7d65-258">If the access token isn't acquired from cache (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="a7d65-259">다음으로 `Microsoft.Graph.GraphServiceClient`가 토큰을 사용하여 Graph API 호출을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-259">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="a7d65-260">이 토큰은 ADAL 토큰 캐시에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-260">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="a7d65-261">향후 동일한 사용자에 대한 Graph API 호출의 경우 토큰은 <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>와 함께 캐시에서 자동으로 획득됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-261">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="a7d65-262"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 전이적 멤버 자격을 얻지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-262">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="a7d65-263">직접 및 전이적 멤버 자격을 얻도록 코드를 변경하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-263">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="a7d65-264">다음 코드 줄의 경우</span><span class="sxs-lookup"><span data-stu-id="a7d65-264">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="a7d65-265">위 줄을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-265">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="a7d65-266">다음 코드 줄의 경우</span><span class="sxs-lookup"><span data-stu-id="a7d65-266">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="a7d65-267">위 줄을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-267">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="a7d65-268"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 클레임을 만들 때 AAD 보안 그룹과 AAD 관리자 역할을 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="a7d65-269">앱이 그룹과 역할을 구분하도록 그룹과 역할을 반복할 때 `entry.ODataType`을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-269">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="a7d65-270">별도의 보안 그룹 및 역할 클레임을 만들려면 다음과 유사한 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-270">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        user:::no-loc(Identity):::.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="a7d65-271">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-271">User-defined roles</span></span>

<span data-ttu-id="a7d65-272">AAD에 등록된 앱은 사용자 정의 역할을 사용하도록 구성될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-272">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="a7d65-273">Azure Portal에서 `roles` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 Azure 설명서에서 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-273">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="a7d65-274">다음 예제에서는 앱이 두 개의 역할로 구성되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-274">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="a7d65-275">Azure AD Premium 계정을 사용하지 않고서는 보안 그룹에 역할을 할당할 수 없지만, 표준 Azure 계정을 사용하여 사용자를 역할에 할당하고 사용자에 대한 `roles` 클레임을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-275">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="a7d65-276">이 섹션의 지침에는 Azure AD Premium 계정이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-276">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="a7d65-277">Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-277">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="a7d65-278">AAD에서 보낸 단일 `roles` 클레임은 사용자 정의 역할을 JSON 배열의 `appRoles`의 `value`로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-278">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="a7d65-279">앱은 역할의 JSON 배열을 개별 `role` 클레임으로 변환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-279">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="a7d65-280">[사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 표시된 `CustomUserFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에서 작동하도록 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-280">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="a7d65-281">[사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 나와 있는 것처럼 호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에 `CustomUserFactory`를 추가하고 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-281">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="a7d65-282">원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-282">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="a7d65-283">호스트된 :::no-loc(Blazor)::: 솔루션의 독립 실행형 앱 또는 *`Client`* 앱의 `Program.Main`에서 "`role`"이라는 클레임을 역할 클레임으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-283">In `Program.Main` of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="a7d65-284">이 시점에서는 구성 요소 권한 부여 방식이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-284">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a7d65-285">구성 요소의 모든 권한 부여 메커니즘에서 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-285">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="a7d65-286">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="a7d65-286">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a7d65-287">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="a7d65-287">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a7d65-288">[절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="a7d65-288">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a7d65-289">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-289">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="a7d65-290">AAD 관리자 역할 개체 ID</span><span class="sxs-lookup"><span data-stu-id="a7d65-290">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="a7d65-291">다음 표에 나와 있는 개체 ID는 `group` 클레임에 대한 [정책](xref:security/authorization/policies)을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-291">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="a7d65-292">정책은 앱이 앱의 여러 활동에 대해 사용자에게 권한을 부여하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-292">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="a7d65-293">자세한 내용은 [사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7d65-293">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="a7d65-294">AAD 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="a7d65-294">AAD Administrator Role</span></span> | <span data-ttu-id="a7d65-295">개체 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="a7d65-295">Object ID</span></span>
--- | ---
<span data-ttu-id="a7d65-296">애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-296">Application administrator</span></span> | <span data-ttu-id="a7d65-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="a7d65-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="a7d65-298">애플리케이션 개발자</span><span class="sxs-lookup"><span data-stu-id="a7d65-298">Application developer</span></span> | <span data-ttu-id="a7d65-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="a7d65-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="a7d65-300">인증 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-300">Authentication administrator</span></span> | <span data-ttu-id="a7d65-301">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="a7d65-301">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="a7d65-302">Azure DevOps 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-302">Azure DevOps administrator</span></span> | <span data-ttu-id="a7d65-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="a7d65-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="a7d65-304">Azure Information Protection 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-304">Azure Information Protection administrator</span></span> | <span data-ttu-id="a7d65-305">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="a7d65-305">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="a7d65-306">B2C IEF 키 세트 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-306">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="a7d65-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="a7d65-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="a7d65-308">B2C IEF 정책 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-308">B2C IEF Policy administrator</span></span> | <span data-ttu-id="a7d65-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="a7d65-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="a7d65-310">B2C 사용자 흐름 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-310">B2C user flow administrator</span></span> | <span data-ttu-id="a7d65-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="a7d65-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="a7d65-312">B2C 사용자 흐름 특성 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-312">B2C user flow attribute administrator</span></span> | <span data-ttu-id="a7d65-313">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="a7d65-313">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="a7d65-314">대금 청구 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-314">Billing administrator</span></span> | <span data-ttu-id="a7d65-315">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="a7d65-315">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="a7d65-316">클라우드 애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-316">Cloud application administrator</span></span> | <span data-ttu-id="a7d65-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="a7d65-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="a7d65-318">클라우드 디바이스 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-318">Cloud device administrator</span></span> | <span data-ttu-id="a7d65-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="a7d65-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="a7d65-320">규정 준수 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-320">Compliance administrator</span></span> | <span data-ttu-id="a7d65-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="a7d65-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="a7d65-322">규정 준수 데이터 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-322">Compliance data administrator</span></span> | <span data-ttu-id="a7d65-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="a7d65-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="a7d65-324">조건부 액세스 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-324">Conditional Access administrator</span></span> | <span data-ttu-id="a7d65-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="a7d65-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="a7d65-326">고객 LockBox 액세스 승인자</span><span class="sxs-lookup"><span data-stu-id="a7d65-326">Customer LockBox access approver</span></span> | <span data-ttu-id="a7d65-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="a7d65-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="a7d65-328">Desktop Analytics 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-328">Desktop Analytics administrator</span></span> | <span data-ttu-id="a7d65-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="a7d65-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="a7d65-330">디렉터리 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="a7d65-330">Directory readers</span></span> | <span data-ttu-id="a7d65-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="a7d65-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="a7d65-332">Dynamics 365 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-332">Dynamics 365 administrator</span></span> | <span data-ttu-id="a7d65-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="a7d65-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="a7d65-334">Exchange 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-334">Exchange administrator</span></span> | <span data-ttu-id="a7d65-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="a7d65-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="a7d65-336">외부 :::no-loc(Identity)::: 공급자 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-336">External :::no-loc(Identity)::: Provider administrator</span></span> | <span data-ttu-id="a7d65-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="a7d65-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="a7d65-338">전역 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-338">Global administrator</span></span> | <span data-ttu-id="a7d65-339">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="a7d65-339">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="a7d65-340">글로벌 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="a7d65-340">Global reader</span></span> | <span data-ttu-id="a7d65-341">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="a7d65-341">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="a7d65-342">그룹 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-342">Groups administrator</span></span> | <span data-ttu-id="a7d65-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="a7d65-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="a7d65-344">게스트 초대자</span><span class="sxs-lookup"><span data-stu-id="a7d65-344">Guest inviter</span></span> | <span data-ttu-id="a7d65-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="a7d65-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="a7d65-346">기술 지원팀 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-346">Helpdesk administrator</span></span> | <span data-ttu-id="a7d65-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="a7d65-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="a7d65-348">Intune 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-348">Intune administrator</span></span> | <span data-ttu-id="a7d65-349">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="a7d65-349">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="a7d65-350">Kaizala 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-350">Kaizala administrator</span></span> | <span data-ttu-id="a7d65-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="a7d65-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="a7d65-352">라이선스 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-352">License administrator</span></span> | <span data-ttu-id="a7d65-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="a7d65-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="a7d65-354">메시지 센터 개인 정보 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="a7d65-354">Message center privacy reader</span></span> | <span data-ttu-id="a7d65-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="a7d65-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="a7d65-356">메시지 센터 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="a7d65-356">Message center reader</span></span> | <span data-ttu-id="a7d65-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="a7d65-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="a7d65-358">Office 앱 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-358">Office apps administrator</span></span> | <span data-ttu-id="a7d65-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="a7d65-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="a7d65-360">암호 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-360">Password administrator</span></span> | <span data-ttu-id="a7d65-361">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="a7d65-361">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="a7d65-362">Power BI 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-362">Power BI administrator</span></span> | <span data-ttu-id="a7d65-363">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="a7d65-363">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="a7d65-364">Power Platform 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-364">Power platform administrator</span></span> | <span data-ttu-id="a7d65-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="a7d65-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="a7d65-366">권한 있는 인증 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-366">Privileged authentication administrator</span></span> | <span data-ttu-id="a7d65-367">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="a7d65-367">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="a7d65-368">권한 있는 역할 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-368">Privileged role administrator</span></span> | <span data-ttu-id="a7d65-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="a7d65-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="a7d65-370">보고서 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="a7d65-370">Reports reader</span></span> | <span data-ttu-id="a7d65-371">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="a7d65-371">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="a7d65-372">Search 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-372">Search administrator</span></span> | <span data-ttu-id="a7d65-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="a7d65-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="a7d65-374">Search 편집자</span><span class="sxs-lookup"><span data-stu-id="a7d65-374">Search editor</span></span> | <span data-ttu-id="a7d65-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="a7d65-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="a7d65-376">보안 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-376">Security administrator</span></span> | <span data-ttu-id="a7d65-377">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="a7d65-377">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="a7d65-378">보안 운영자</span><span class="sxs-lookup"><span data-stu-id="a7d65-378">Security operator</span></span> | <span data-ttu-id="a7d65-379">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="a7d65-379">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="a7d65-380">보안 판독기</span><span class="sxs-lookup"><span data-stu-id="a7d65-380">Security reader</span></span> | <span data-ttu-id="a7d65-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="a7d65-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="a7d65-382">서비스 지원 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-382">Service support administrator</span></span> | <span data-ttu-id="a7d65-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="a7d65-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="a7d65-384">SharePoint 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-384">SharePoint administrator</span></span> | <span data-ttu-id="a7d65-385">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="a7d65-385">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="a7d65-386">비즈니스용 Skype 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-386">Skype for Business administrator</span></span> | <span data-ttu-id="a7d65-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="a7d65-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="a7d65-388">Teams 통신 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-388">Teams Communications Administrator</span></span> | <span data-ttu-id="a7d65-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="a7d65-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="a7d65-390">Teams 통신 지원 엔지니어</span><span class="sxs-lookup"><span data-stu-id="a7d65-390">Teams Communications Support Engineer</span></span> | <span data-ttu-id="a7d65-391">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="a7d65-391">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="a7d65-392">Teams 커뮤니케이션 전문가</span><span class="sxs-lookup"><span data-stu-id="a7d65-392">Teams Communications Specialist</span></span> | <span data-ttu-id="a7d65-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="a7d65-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="a7d65-394">Teams 서비스 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-394">Teams Service Administrator</span></span> | <span data-ttu-id="a7d65-395">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="a7d65-395">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="a7d65-396">사용자 관리자</span><span class="sxs-lookup"><span data-stu-id="a7d65-396">User administrator</span></span> | <span data-ttu-id="a7d65-397">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="a7d65-397">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7d65-398">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a7d65-398">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
