---
title: Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssembly에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
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
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636779"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="791b7-103">Azure Active Directory(AAD) 그룹, 관리자 역할, 사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="791b7-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="791b7-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="791b7-105">AAD(Azure Active Directory)는 ASP.NET Core Identity와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="791b7-106">사용자 정의 그룹</span><span class="sxs-lookup"><span data-stu-id="791b7-106">User-defined groups</span></span>
  * <span data-ttu-id="791b7-107">보안</span><span class="sxs-lookup"><span data-stu-id="791b7-107">Security</span></span>
  * <span data-ttu-id="791b7-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="791b7-108">Microsoft 365</span></span>
  * <span data-ttu-id="791b7-109">분포</span><span class="sxs-lookup"><span data-stu-id="791b7-109">Distribution</span></span>
* <span data-ttu-id="791b7-110">역할</span><span class="sxs-lookup"><span data-stu-id="791b7-110">Roles</span></span>
  * <span data-ttu-id="791b7-111">AAD 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="791b7-112">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-112">User-defined roles</span></span>

<span data-ttu-id="791b7-113">이 문서의 지침은 다음 항목에서 설명하는 Blazor WebAssembly AAD 배포 시나리오에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="791b7-114">Microsoft 계정을 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="791b7-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="791b7-115">AAD를 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="791b7-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="791b7-116">AAD를 사용하는 호스트형</span><span class="sxs-lookup"><span data-stu-id="791b7-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="791b7-117">Microsoft Graph API 권한</span><span class="sxs-lookup"><span data-stu-id="791b7-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="791b7-118">5개 이상의 AAD 관리자 역할 및 보안 그룹 구성원 자격이 있는 앱 사용자에게는 [Microsoft Graph API](/graph/use-the-api) 호출이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="791b7-119">Graph API 호출을 허용하려면 Azure Portal에서 호스트된 Blazor 솔루션의 독립 실행형 또는 클라이언트 앱에 다음 [Graph API 권한](/graph/permissions-reference)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="791b7-120">`Directory.Read.All`은 최소 권한이며 이 문서에서 설명하는 예제에 사용되는 권한입니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="791b7-121">사용자 정의 그룹 및 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="791b7-122">Azure Portal에서 `groups` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 다음 Azure 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="791b7-123">사용자 정의 AAD 그룹 및 AAD 관리자 역할에 사용자를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="791b7-124">Azure AD 보안 그룹을 사용하는 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="791b7-125">`groupMembershipClaims` 특성</span><span class="sxs-lookup"><span data-stu-id="791b7-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="791b7-126">다음 예제에서는 사용자가 AAD 대금 청구 관리자 역할에 할당되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="791b7-127">AAD에서 보낸 단일 `groups` 클레임은 사용자의 그룹 및 역할을 JSON 배열의 개체 ID(GUID)로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="791b7-128">앱은 그룹 및 역할의 JSON 배열을 앱이 기준으로 삼아 [정책](xref:security/authorization/policies)을 빌드할 수 있는 개별 `group` 클레임으로 변환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="791b7-129">할당된 AAD 관리자 역할 및 사용자 정의 그룹 수가 5개를 초과하면 AAD는 `groups` 클레임을 전송하는 대신 `true` 값으로 `hasgroups` 클레임을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="791b7-130">사용자에게 할당된 역할과 그룹이 5개 이상일 수 있는 모든 앱은 사용자의 역할 및 그룹을 가져오기 위해 별도의 Graph API 호출을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="791b7-131">이 문서에서 제공하는 예제 구현은 이 시나리오를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="791b7-132">`groups` 및 `hasgroups` 클레임 정보([Microsoft ID 플랫폼 액세스 토큰: 페이로드 클레임](/azure/active-directory/develop/access-tokens#payload-claims) 문서)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="791b7-133">그룹 및 역할의 배열 속성을 포함하도록 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="791b7-134">나중에 `foreach` 루프에서 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 속성에 빈 배열을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="791b7-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="791b7-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="791b7-136">호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱에서 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="791b7-137">역할 및 그룹 정보를 가져오는 Graph API 호출에 올바른 범위(권한)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="791b7-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="791b7-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="791b7-139">`Program.Main`(`Program.cs`)에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 구현 서비스를 추가하고 Graph API 요청을 위한 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="791b7-140">다음 예제에서는 클라이언트의 이름을 `GraphAPI`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="791b7-141">Graph API 호출에서 OData(개방형 데이터 프로토콜) 역할 및 그룹을 수신하는 AAD 디렉터리 개체 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="791b7-142">OData는 JSON 형식으로 도착하고 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 호출은 `DirectoryObjects` 클래스의 인스턴스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="791b7-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="791b7-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="791b7-144">역할 및 그룹 클레임을 처리하는 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="791b7-145">다음 예제 구현에서는 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명하는 `roles` 클레임 배열도 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="791b7-146">`hasgroups` 클레임이 있는 경우 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="791b7-147">이 구현에서는 Microsoft Identity Platform v1.0 엔드포인트`https://graph.microsoft.com/v1.0/me/memberOf`([API 설명서](/graph/api/user-list-memberof))를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="791b7-148">이 토픽의 지침은 MSAL 패키지가 v2.0으로 업그레이드될 때 Identity v2.0에 맞게 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="791b7-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="791b7-149">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
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
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="791b7-150">원래 `groups` 클레임이 있다면 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="791b7-151">이 예제의 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="791b7-152">사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 추가하여 나가는 요청에 액세스 토큰을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="791b7-153">안전한 외부 웹 API 엔드포인트에 API 요청을 하기 위해 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="791b7-154">명명된 <xref:System.Net.Http.HttpClient>를 사용하여 권한 있는 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="791b7-155">이 방법에 대한 일반적인 설명은 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 문서에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="791b7-156">호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱의 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="791b7-157">앱의 추가 범위로 `Directory.Read.All` 권한 범위에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
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

<span data-ttu-id="791b7-158">`Program.Main`의 그룹 또는 역할에 대해 [정책](xref:security/authorization/policies)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="791b7-159">다음 예제에서는 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="791b7-160">AAD 역할 개체 ID의 전체 목록은 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="791b7-161">다음 예제에서 앱은 앞에 나온 정책을 사용하여 사용자에게 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="791b7-162">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)는 이 정책을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="791b7-163">전체 구성 요소에 대한 액세스는 [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)을 사용하는 정책을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="791b7-164">사용자가 로그인되어 있지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인한 후에 구성 요소로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="791b7-165">[절차적 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="791b7-166">사용자 정의 그룹 및 관리자 역할에 서버 API 액세스 권한 부여</span><span class="sxs-lookup"><span data-stu-id="791b7-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="791b7-167">서버 API는 클라이언트 쪽 WebAssembly 앱의 사용자에게 페이지와 리소스에 대한 액세스 권한을 부여하는 것 외에도 보안 API 엔드포인트에 대한 액세스 권한을 사용자에게 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="791b7-168">서버 앱이 사용자 액세스 토큰의 유효성을 검사한 후:</span><span class="sxs-lookup"><span data-stu-id="791b7-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="791b7-169">앱은 JWT(`id_token`)에서 사용자의 변경 불가능한 [개체 식별자 클레임(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)을 사용하여 Graph API의 액세스 토큰을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="791b7-170">Graph API 호출은 사용자의 Azure 사용자 정의 보안 그룹 및 관리자 역할 멤버 자격을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="791b7-171">멤버 자격은 `group` 클레임을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="791b7-172">[권한 부여 정책](xref:security/authorization/policies)을 사용하여 서버 API 엔드포인트에 대한 사용자 액세스를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="791b7-173">이 지침에는 현재 [AAD 사용자 정의 역할](#user-defined-roles)을 기준으로 한 사용자 권한 부여가 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="791b7-174">패키지</span><span class="sxs-lookup"><span data-stu-id="791b7-174">Packages</span></span>

<span data-ttu-id="791b7-175">다음 패키지의 서버 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="791b7-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="791b7-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="791b7-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="791b7-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="791b7-178">Azure 구성</span><span class="sxs-lookup"><span data-stu-id="791b7-178">Azure configuration</span></span>

* <span data-ttu-id="791b7-179">서버 앱 등록에 `Directory.Read.All`(보안 그룹의 최소 권한 액세스 수준)을 위한 Graph API 권한에 대한 API 액세스 권한이 제공되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="791b7-180">권한 할당을 수행한 후 관리자 동의가 권한에 적용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="791b7-181">서버 앱에 새 클라이언트 암호를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="791b7-182">[앱 설정](#app-settings) 섹션에서 앱 구성을 위한 암호를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="791b7-183">앱 설정</span><span class="sxs-lookup"><span data-stu-id="791b7-183">App settings</span></span>

<span data-ttu-id="791b7-184">앱 설정 파일(`appsettings.json` 또는 `appsettings.Production.json`)에서 Azure Portal의 서버 앱 클라이언트 암호를 사용하여 `ClientSecret` 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="791b7-185">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="791b7-186">네임스페이스 수준에서 구성하는 권한 부여 정책도</span><span class="sxs-lookup"><span data-stu-id="791b7-186">Authorization policies</span></span>

<span data-ttu-id="791b7-187">그룹 개체 ID와 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids)를 기준으로 서버 앱의 `Startup.ConfigureServices`(`Startup.cs`)에서 AAD 보안 그룹 및 AAD 관리자 역할의 [권한 부여 정책](xref:security/authorization/policies)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="791b7-188">예를 들어 Azure 대금 청구 관리자 역할 정책의 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="791b7-189">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="791b7-190">컨트롤러 액세스</span><span class="sxs-lookup"><span data-stu-id="791b7-190">Controller access</span></span>

<span data-ttu-id="791b7-191">서버 앱 컨트롤러에 대한 정책이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="791b7-192">다음 예제는 [권한 부여 정책](#authorization-policies) 섹션에서 구성된 대로 정책 이름 `BillingAdmin`을 사용하여 `BillingDataController`의 청구 데이터에 대한 액세스를 Azure 대금 청구 관리자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="791b7-193">서비스 구성</span><span class="sxs-lookup"><span data-stu-id="791b7-193">Service configuration</span></span>

<span data-ttu-id="791b7-194">서버 앱의 `Startup.ConfigureServices` 메서드는 Graph API 호출을 실행하는 논리를 추가하고 사용자의 보안 그룹 및 역할에 대한 사용자 `group` 클레임을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="791b7-195">이 섹션의 예제 코드에서는 Microsoft Identity Platform v1.0을 기반으로 하는 ADAL(Active Directory 인증 라이브러리)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="791b7-196">.NET 5용 Identity v2.0의 경우 이 토픽이 업데이트될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="791b7-197">[Blazor(dotnet/AspNetCore.Docs #19503)용 [RC1] Microsoft Identity Platform 2.0](https://github.com/dotnet/AspNetCore.Docs/issues/19503)을 모니터링하여 이 작업의 진행 상황을 추적하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="791b7-198">서버 앱의 `Startup` 클래스에 있는 코드에는 추가 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="791b7-199">다음 `using` 문 집합에는 이 섹션 뒷부분에 나오는 코드의 필수 네임스페이스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
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
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="791b7-200"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>를 구성하는 경우:</span><span class="sxs-lookup"><span data-stu-id="791b7-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="791b7-201">필요한 경우 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>에 대한 처리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="791b7-202">예를 들어 앱은 실패한 인증을 로그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="791b7-203"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>에서 Graph API 호출을 실행하여 사용자의 그룹과 역할을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="791b7-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType>는 로깅 메시지에 PII(개인 식별 정보)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="791b7-205">테스트 사용자 계정을 사용하여 디버깅하는 경우에만 PII를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="791b7-206">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="791b7-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
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

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

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

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="791b7-207">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="791b7-207">In the preceding example:</span></span>

* <span data-ttu-id="791b7-208">액세스 토큰이 ADAL 토큰 캐시에 이미 저장되어 있을 수 있으므로 자동 토큰 획득(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>)이 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="791b7-209">새 토큰을 요청하는 것보다 캐시에서 토큰을 얻는 것이 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="791b7-210">캐시에서 액세스 토큰을 획득하지 않는 경우(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 또는 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>이 throw 됨) 클라이언트 자격 증명(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>)을 사용한 사용자 어설션(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>)을 통해 사용자를 대신하여 토큰을 얻습니다(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="791b7-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="791b7-211">다음으로 `Microsoft.Graph.GraphServiceClient`가 토큰을 사용하여 Graph API 호출을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="791b7-212">이 토큰은 ADAL 토큰 캐시에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="791b7-213">향후 동일한 사용자에 대한 Graph API 호출의 경우 토큰은 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>와 함께 캐시에서 자동으로 획득됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="791b7-214"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 전이적 멤버 자격을 얻지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="791b7-215">직접 및 전이적 멤버 자격을 얻도록 코드를 변경하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="791b7-216">다음 코드 줄의 경우</span><span class="sxs-lookup"><span data-stu-id="791b7-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="791b7-217">위 줄을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="791b7-218">다음 코드 줄의 경우</span><span class="sxs-lookup"><span data-stu-id="791b7-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="791b7-219">위 줄을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="791b7-220"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 클레임을 만들 때 AAD 보안 그룹과 AAD 관리자 역할을 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="791b7-221">앱이 그룹과 역할을 구분하도록 그룹과 역할을 반복할 때 `entry.ODataType`을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="791b7-222">별도의 보안 그룹 및 역할 클레임을 만들려면 다음과 유사한 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="791b7-223">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-223">User-defined roles</span></span>

<span data-ttu-id="791b7-224">AAD에 등록된 앱은 사용자 정의 역할을 사용하도록 구성될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="791b7-225">Azure Portal에서 `roles` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 Azure 설명서에서 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="791b7-226">다음 예제에서는 앱이 두 개의 역할로 구성되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="791b7-227">Azure AD Premium 계정을 사용하지 않고서는 보안 그룹에 역할을 할당할 수 없지만, 표준 Azure 계정을 사용하여 사용자를 역할에 할당하고 사용자에 대한 `roles` 클레임을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="791b7-228">이 섹션의 지침에는 Azure AD Premium 계정이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="791b7-229">Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="791b7-230">AAD에서 보낸 단일 `roles` 클레임은 사용자 정의 역할을 JSON 배열의 `appRoles`의 `value`로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="791b7-231">앱은 역할의 JSON 배열을 개별 `role` 클레임으로 변환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="791b7-232">[사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 표시된 `CustomUserFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에서 작동하도록 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="791b7-233">[사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 나와 있는 것처럼 호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱에 `CustomUserFactory`를 추가하고 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-233">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="791b7-234">원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="791b7-235">호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱의 `Program.Main`에서 "`role`"이라는 클레임을 역할 클레임으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-235">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="791b7-236">이 시점에서는 구성 요소 권한 부여 방식이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="791b7-237">구성 요소의 모든 권한 부여 메커니즘에서 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="791b7-238">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="791b7-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="791b7-239">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="791b7-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="791b7-240">[절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="791b7-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="791b7-241">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="791b7-242">AAD 관리자 역할 개체 ID</span><span class="sxs-lookup"><span data-stu-id="791b7-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="791b7-243">다음 표에 나와 있는 개체 ID는 `group` 클레임에 대한 [정책](xref:security/authorization/policies)을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="791b7-244">정책은 앱이 앱의 여러 활동에 대해 사용자에게 권한을 부여하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="791b7-245">자세한 내용은 [사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="791b7-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="791b7-246">AAD 관리자 역할</span><span class="sxs-lookup"><span data-stu-id="791b7-246">AAD Administrator Role</span></span> | <span data-ttu-id="791b7-247">개체 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="791b7-247">Object ID</span></span>
--- | ---
<span data-ttu-id="791b7-248">애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-248">Application administrator</span></span> | <span data-ttu-id="791b7-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="791b7-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="791b7-250">애플리케이션 개발자</span><span class="sxs-lookup"><span data-stu-id="791b7-250">Application developer</span></span> | <span data-ttu-id="791b7-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="791b7-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="791b7-252">인증 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-252">Authentication administrator</span></span> | <span data-ttu-id="791b7-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="791b7-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="791b7-254">Azure DevOps 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-254">Azure DevOps administrator</span></span> | <span data-ttu-id="791b7-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="791b7-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="791b7-256">Azure Information Protection 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="791b7-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="791b7-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="791b7-258">B2C IEF 키 세트 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="791b7-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="791b7-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="791b7-260">B2C IEF 정책 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="791b7-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="791b7-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="791b7-262">B2C 사용자 흐름 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-262">B2C user flow administrator</span></span> | <span data-ttu-id="791b7-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="791b7-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="791b7-264">B2C 사용자 흐름 특성 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="791b7-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="791b7-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="791b7-266">대금 청구 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-266">Billing administrator</span></span> | <span data-ttu-id="791b7-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="791b7-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="791b7-268">클라우드 애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-268">Cloud application administrator</span></span> | <span data-ttu-id="791b7-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="791b7-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="791b7-270">클라우드 디바이스 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-270">Cloud device administrator</span></span> | <span data-ttu-id="791b7-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="791b7-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="791b7-272">규정 준수 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-272">Compliance administrator</span></span> | <span data-ttu-id="791b7-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="791b7-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="791b7-274">규정 준수 데이터 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-274">Compliance data administrator</span></span> | <span data-ttu-id="791b7-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="791b7-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="791b7-276">조건부 액세스 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-276">Conditional Access administrator</span></span> | <span data-ttu-id="791b7-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="791b7-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="791b7-278">고객 LockBox 액세스 승인자</span><span class="sxs-lookup"><span data-stu-id="791b7-278">Customer LockBox access approver</span></span> | <span data-ttu-id="791b7-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="791b7-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="791b7-280">Desktop Analytics 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="791b7-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="791b7-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="791b7-282">디렉터리 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="791b7-282">Directory readers</span></span> | <span data-ttu-id="791b7-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="791b7-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="791b7-284">Dynamics 365 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="791b7-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="791b7-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="791b7-286">Exchange 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-286">Exchange administrator</span></span> | <span data-ttu-id="791b7-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="791b7-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="791b7-288">외부 Identity 공급자 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-288">External Identity Provider administrator</span></span> | <span data-ttu-id="791b7-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="791b7-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="791b7-290">전역 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-290">Global administrator</span></span> | <span data-ttu-id="791b7-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="791b7-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="791b7-292">글로벌 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="791b7-292">Global reader</span></span> | <span data-ttu-id="791b7-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="791b7-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="791b7-294">그룹 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-294">Groups administrator</span></span> | <span data-ttu-id="791b7-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="791b7-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="791b7-296">게스트 초대자</span><span class="sxs-lookup"><span data-stu-id="791b7-296">Guest inviter</span></span> | <span data-ttu-id="791b7-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="791b7-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="791b7-298">기술 지원팀 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-298">Helpdesk administrator</span></span> | <span data-ttu-id="791b7-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="791b7-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="791b7-300">Intune 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-300">Intune administrator</span></span> | <span data-ttu-id="791b7-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="791b7-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="791b7-302">Kaizala 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-302">Kaizala administrator</span></span> | <span data-ttu-id="791b7-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="791b7-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="791b7-304">라이선스 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-304">License administrator</span></span> | <span data-ttu-id="791b7-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="791b7-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="791b7-306">메시지 센터 개인 정보 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="791b7-306">Message center privacy reader</span></span> | <span data-ttu-id="791b7-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="791b7-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="791b7-308">메시지 센터 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="791b7-308">Message center reader</span></span> | <span data-ttu-id="791b7-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="791b7-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="791b7-310">Office 앱 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-310">Office apps administrator</span></span> | <span data-ttu-id="791b7-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="791b7-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="791b7-312">암호 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-312">Password administrator</span></span> | <span data-ttu-id="791b7-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="791b7-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="791b7-314">Power BI 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-314">Power BI administrator</span></span> | <span data-ttu-id="791b7-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="791b7-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="791b7-316">Power Platform 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-316">Power platform administrator</span></span> | <span data-ttu-id="791b7-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="791b7-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="791b7-318">권한 있는 인증 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-318">Privileged authentication administrator</span></span> | <span data-ttu-id="791b7-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="791b7-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="791b7-320">권한 있는 역할 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-320">Privileged role administrator</span></span> | <span data-ttu-id="791b7-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="791b7-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="791b7-322">보고서 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="791b7-322">Reports reader</span></span> | <span data-ttu-id="791b7-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="791b7-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="791b7-324">Search 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-324">Search administrator</span></span> | <span data-ttu-id="791b7-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="791b7-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="791b7-326">Search 편집자</span><span class="sxs-lookup"><span data-stu-id="791b7-326">Search editor</span></span> | <span data-ttu-id="791b7-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="791b7-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="791b7-328">보안 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-328">Security administrator</span></span> | <span data-ttu-id="791b7-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="791b7-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="791b7-330">보안 운영자</span><span class="sxs-lookup"><span data-stu-id="791b7-330">Security operator</span></span> | <span data-ttu-id="791b7-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="791b7-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="791b7-332">보안 판독기</span><span class="sxs-lookup"><span data-stu-id="791b7-332">Security reader</span></span> | <span data-ttu-id="791b7-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="791b7-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="791b7-334">서비스 지원 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-334">Service support administrator</span></span> | <span data-ttu-id="791b7-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="791b7-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="791b7-336">SharePoint 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-336">SharePoint administrator</span></span> | <span data-ttu-id="791b7-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="791b7-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="791b7-338">비즈니스용 Skype 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-338">Skype for Business administrator</span></span> | <span data-ttu-id="791b7-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="791b7-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="791b7-340">Teams 통신 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-340">Teams Communications Administrator</span></span> | <span data-ttu-id="791b7-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="791b7-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="791b7-342">Teams 통신 지원 엔지니어</span><span class="sxs-lookup"><span data-stu-id="791b7-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="791b7-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="791b7-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="791b7-344">Teams 커뮤니케이션 전문가</span><span class="sxs-lookup"><span data-stu-id="791b7-344">Teams Communications Specialist</span></span> | <span data-ttu-id="791b7-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="791b7-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="791b7-346">Teams 서비스 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-346">Teams Service Administrator</span></span> | <span data-ttu-id="791b7-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="791b7-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="791b7-348">사용자 관리자</span><span class="sxs-lookup"><span data-stu-id="791b7-348">User administrator</span></span> | <span data-ttu-id="791b7-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="791b7-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="791b7-350">추가 자료</span><span class="sxs-lookup"><span data-stu-id="791b7-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
