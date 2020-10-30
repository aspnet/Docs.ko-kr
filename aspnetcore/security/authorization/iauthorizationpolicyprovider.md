---
title: ASP.NET Core의 사용자 지정 권한 부여 정책 공급자
author: mjrousos
description: ASP.NET Core 앱에서 사용자 지정 IAuthorizationPolicyProvider를 사용 하 여 권한 부여 정책을 동적으로 생성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053139"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="25ddc-103">ASP.NET Core에서 IAuthorizationPolicyProvider를 사용 하는 사용자 지정 권한 부여 정책 공급자</span><span class="sxs-lookup"><span data-stu-id="25ddc-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="25ddc-104">[Mike Rousos](https://github.com/mjrousos) 작성</span><span class="sxs-lookup"><span data-stu-id="25ddc-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="25ddc-105">일반적으로 [정책 기반 권한 부여](xref:security/authorization/policies)를 사용 하는 경우 `AuthorizationOptions.AddPolicy` 권한 부여 서비스 구성의 일부로를 호출 하 여 정책을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="25ddc-106">일부 시나리오에서는 이러한 방식으로 모든 권한 부여 정책을 등록 하는 것이 가능 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="25ddc-107">이러한 경우 [사용자 지정 `IAuthorizationPolicyProvider` 을 사용](#ci) 하 여 권한 부여 정책이 제공 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="25ddc-108">사용자 지정 [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 유용 하 게 사용할 수 있는 시나리오의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="25ddc-109">외부 서비스를 사용 하 여 정책 평가를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="25ddc-110">많은 범위의 정책 (예: 방 번호 또는 연령)을 사용 하 여 각 개별 권한 부여 정책을 호출에 추가 하는 것은 의미가 없습니다 `AuthorizationOptions.AddPolicy` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="25ddc-111">외부 데이터 원본 (예: 데이터베이스)의 정보를 기반으로 런타임에 정책을 만들거나 다른 메커니즘을 통해 권한 부여 요구 사항을 동적으로 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="25ddc-112">[AspNetCore GitHub 리포지토리에서](https://github.com/dotnet/AspNetCore) [샘플 코드를 보거나 다운로드](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="25ddc-113">Dotnet/AspNetCore 리포지토리 ZIP 파일을 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="25ddc-114">파일의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-114">Unzip the file.</span></span> <span data-ttu-id="25ddc-115">*Src/Security/samples/CustomPolicyProvider* 프로젝트 폴더로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="25ddc-116">정책 검색 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="25ddc-116">Customize policy retrieval</span></span>

<span data-ttu-id="25ddc-117">ASP.NET Core apps는 인터페이스 구현을 사용 `IAuthorizationPolicyProvider` 하 여 권한 부여 정책을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="25ddc-118">기본적으로 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 가 등록 되 고 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="25ddc-119">`DefaultAuthorizationPolicyProvider``AuthorizationOptions`호출에서 제공 된 정책을 반환 합니다 `IServiceCollection.AddAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="25ddc-120">`IAuthorizationPolicyProvider`앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 다른 구현을 등록 하 여이 동작을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="25ddc-121">인터페이스에는 `IAuthorizationPolicyProvider` 다음과 같은 세 가지 api가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="25ddc-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 는 지정 된 이름에 대 한 권한 부여 정책을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="25ddc-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 는 기본 권한 부여 정책 ( `[Authorize]` 정책이 지정 되지 않은 특성에 사용 되는 정책)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="25ddc-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) 는 대체 권한 부여 정책 (정책을 지정 하지 않은 경우 권한 부여 미들웨어에서 사용 되는 정책)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="25ddc-125">이러한 Api를 구현 하 여 권한 부여 정책이 제공 되는 방식을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="25ddc-126">매개 변수가 있는 권한 부여 특성 예제</span><span class="sxs-lookup"><span data-stu-id="25ddc-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="25ddc-127">가 유용한 시나리오 중 하나는 `IAuthorizationPolicyProvider` `[Authorize]` 요구 사항이 매개 변수에 따라 달라 지는 사용자 지정 특성을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="25ddc-128">예를 들어 [정책 기반 권한 부여](xref:security/authorization/policies) 설명서에서 사용 기간 기반 ("AtLeast21") 정책은 샘플로 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="25ddc-129">앱의 다른 컨트롤러 작업을 *다른* 연령의 사용자가 사용할 수 있도록 설정 해야 하는 경우 다양 한 연령 기반 정책을 사용 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="25ddc-130">응용 프로그램에 필요한 다른 모든 연령 기반 정책을 등록 하는 대신 `AuthorizationOptions` 사용자 지정을 사용 하 여 정책을 동적으로 생성할 수 있습니다 `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="25ddc-131">정책을 더 쉽게 사용 하기 위해와 같은 사용자 지정 권한 부여 특성을 사용 하 여 작업에 주석을 달 수 있습니다 `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="25ddc-132">사용자 지정 권한 부여 특성</span><span class="sxs-lookup"><span data-stu-id="25ddc-132">Custom Authorization attributes</span></span>

<span data-ttu-id="25ddc-133">권한 부여 정책은 이름으로 식별 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="25ddc-134">`MinimumAgeAuthorizeAttribute`앞에서 설명한 사용자 지정은 인수를 해당 권한 부여 정책을 검색 하는 데 사용할 수 있는 문자열에 매핑해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="25ddc-135">에서 파생 시켜 속성을 래핑하는 방법으로이 작업을 수행할 수 있습니다 `AuthorizeAttribute` `Age` `AuthorizeAttribute.Policy` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="25ddc-136">이 특성 형식에는 `Policy` 하드 코드 된 접두사 ()를 기반으로 `"MinimumAge"` 하는 문자열과 생성자를 통해 전달 된 정수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="25ddc-137">`Authorize`정수를 매개 변수로 사용 한다는 점을 제외 하 고 다른 특성과 동일한 방식으로 작업에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="25ddc-138">사용자 지정 IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="25ddc-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="25ddc-139">사용자 지정을 `MinimumAgeAuthorizeAttribute` 사용 하면 원하는 최소 사용 기간에 대 한 권한 부여 정책을 쉽게 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="25ddc-140">다음에 해결 해야 하는 문제는 다른 모든 연령에 대해 권한 부여 정책을 사용할 수 있는지 확인 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="25ddc-141">여기서는 `IAuthorizationPolicyProvider` 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="25ddc-142">를 사용 하는 경우 `MinimumAgeAuthorizationAttribute` 권한 부여 정책 이름이 패턴 `"MinimumAge" + Age` 을 따르기 때문에 사용자 지정은 `IAuthorizationPolicyProvider` 다음을 통해 권한 부여 정책을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="25ddc-143">정책 이름에서 나이를 구문 분석 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="25ddc-144">을 사용 하 여 `AuthorizationPolicyBuilder` 새을 만듭니다. `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="25ddc-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="25ddc-145">이 및 다음 예제에서는 사용자가를 통해 인증 된 것으로 간주 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="25ddc-145">In this and following examples it will be assumed that the user is authenticated via a :::no-loc(cookie):::.</span></span> <span data-ttu-id="25ddc-146">는 `AuthorizationPolicyBuilder` 하나 이상의 인증 체계 이름을 사용 하 여 생성 되거나 항상 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="25ddc-147">그렇지 않으면 사용자에 게 챌린지를 제공 하는 방법에 대 한 정보가 없으므로 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="25ddc-148">사용 기간에 따라 정책에 요구 사항을 추가 `AuthorizationPolicyBuilder.AddRequirements` 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="25ddc-149">다른 시나리오에서는, 또는를 대신 사용할 수 있습니다 `RequireClaim` `RequireRole` `RequireUserName` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="25ddc-150">여러 권한 부여 정책 공급자</span><span class="sxs-lookup"><span data-stu-id="25ddc-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="25ddc-151">사용자 지정 구현을 사용 하 `IAuthorizationPolicyProvider` 는 경우 ASP.NET Core는 인스턴스를 하나만 사용 한다는 점에 유의 하세요 `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="25ddc-152">사용자 지정 공급자가 사용할 모든 정책 이름에 대해 권한 부여 정책을 제공할 수 없는 경우 백업 공급자에 게이를 연기 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="25ddc-153">예를 들어 사용자 지정 사용 기간 정책과 기존의 역할 기반 정책 검색을 모두 필요로 하는 응용 프로그램을 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="25ddc-154">이러한 앱은 다음과 같은 사용자 지정 권한 부여 정책 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="25ddc-155">정책 이름을 구문 분석 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="25ddc-156">`DefaultAuthorizationPolicyProvider`정책 이름에 age가 포함 되어 있지 않으면 다른 정책 공급자 (예:)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="25ddc-157">`IAuthorizationPolicyProvider`위에 표시 된 예제 구현은 해당 `DefaultAuthorizationPolicyProvider` 생성자에서 백업 정책 공급자를 만들어를 사용 하도록 업데이트할 수 있습니다 (정책 이름이 ' 이상 보존 기간 ' + 연령의 예상 패턴과 일치 하지 않는 경우에 사용 됨).</span><span class="sxs-lookup"><span data-stu-id="25ddc-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="25ddc-158">그런 다음 `GetPolicyAsync` null을 반환 하는 대신를 사용 하도록 메서드를 업데이트할 수 있습니다 `BackupPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="25ddc-159">기본 정책</span><span class="sxs-lookup"><span data-stu-id="25ddc-159">Default policy</span></span>

<span data-ttu-id="25ddc-160">명명 된 권한 부여 정책을 제공 하는 것 외에도 사용자 지정은를 `IAuthorizationPolicyProvider` 구현 하 여 `GetDefaultPolicyAsync` `[Authorize]` 정책 이름이 지정 되지 않은 특성에 대 한 권한 부여 정책을 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="25ddc-161">대부분의 경우이 권한 부여 특성에는 인증 된 사용자만 필요 하므로를 호출 하 여 필요한 정책을 만들 수 있습니다 `RequireAuthenticatedUser` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="25ddc-162">사용자 지정의 모든 측면과 마찬가지로 `IAuthorizationPolicyProvider` 필요에 따라이를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="25ddc-163">경우에 따라 대체 (fallback)에서 기본 정책을 검색 하는 것이 좋을 수도 있습니다 `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="25ddc-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="25ddc-164">대체 정책</span><span class="sxs-lookup"><span data-stu-id="25ddc-164">Fallback policy</span></span>

<span data-ttu-id="25ddc-165">사용자 지정은 선택적으로를 `IAuthorizationPolicyProvider` 구현 하 여 정책을 `GetFallbackPolicyAsync` [조합할](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) 때 및 정책을 지정 하지 않을 때 사용 되는 정책을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="25ddc-166">에서 `GetFallbackPolicyAsync` null이 아닌 정책을 반환 하는 경우 요청에 정책을 지정 하지 않은 경우 권한 부여 미들웨어에서 반환 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="25ddc-167">대체 (fallback) 정책이 필요 하지 않은 경우 공급자는 `null` 대체 (fallback) 공급자를 반환 하거나 연기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="25ddc-168">사용자 지정 IAuthorizationPolicyProvider 사용</span><span class="sxs-lookup"><span data-stu-id="25ddc-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="25ddc-169">에서 사용자 지정 정책을 사용 하려면 `IAuthorizationPolicyProvider` 다음을 수행 **해야 합니다** .</span><span class="sxs-lookup"><span data-stu-id="25ddc-169">To use custom policies from an `IAuthorizationPolicyProvider`, you \* **must** _:</span></span>

<span data-ttu-id="25ddc-170">_ `AuthorizationHandler` 모든 정책 기반 권한 부여 시나리오와 마찬가지로 종속성 주입 ( [정책 기반 권한 부여](xref:security/authorization/policies#authorization-handlers)에 설명)을 사용 하 여 적절 한 형식을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-170">_ Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="25ddc-171">`IAuthorizationPolicyProvider`응용 프로그램의 종속성 주입 서비스 컬렉션에서 사용자 지정 형식을 등록 `Startup.ConfigureServices` 하 여 기본 정책 공급자를 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="25ddc-172">전체 사용자 지정 `IAuthorizationPolicyProvider` 샘플은 [dotnet/aspnetcore GitHub 리포지토리에서](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25ddc-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
