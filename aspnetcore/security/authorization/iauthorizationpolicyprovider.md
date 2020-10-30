---
title: ASP.NET Core의 사용자 지정 권한 부여 정책 공급자
author: mjrousos
description: ASP.NET Core 앱에서 사용자 지정 IAuthorizationPolicyProvider를 사용 하 여 권한 부여 정책을 동적으로 생성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053139"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core에서 IAuthorizationPolicyProvider를 사용 하는 사용자 지정 권한 부여 정책 공급자 

[Mike Rousos](https://github.com/mjrousos) 작성

일반적으로 [정책 기반 권한 부여](xref:security/authorization/policies)를 사용 하는 경우 `AuthorizationOptions.AddPolicy` 권한 부여 서비스 구성의 일부로를 호출 하 여 정책을 등록 합니다. 일부 시나리오에서는 이러한 방식으로 모든 권한 부여 정책을 등록 하는 것이 가능 하지 않을 수 있습니다. 이러한 경우 [사용자 지정 `IAuthorizationPolicyProvider` 을 사용](#ci) 하 여 권한 부여 정책이 제공 되는 방식을 제어할 수 있습니다.

사용자 지정 [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 유용 하 게 사용할 수 있는 시나리오의 예는 다음과 같습니다.

* 외부 서비스를 사용 하 여 정책 평가를 제공 합니다.
* 많은 범위의 정책 (예: 방 번호 또는 연령)을 사용 하 여 각 개별 권한 부여 정책을 호출에 추가 하는 것은 의미가 없습니다 `AuthorizationOptions.AddPolicy` .
* 외부 데이터 원본 (예: 데이터베이스)의 정보를 기반으로 런타임에 정책을 만들거나 다른 메커니즘을 통해 권한 부여 요구 사항을 동적으로 결정 합니다.

[AspNetCore GitHub 리포지토리에서](https://github.com/dotnet/AspNetCore) [샘플 코드를 보거나 다운로드](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) 합니다. Dotnet/AspNetCore 리포지토리 ZIP 파일을 다운로드 합니다. 파일의 압축을 풉니다. *Src/Security/samples/CustomPolicyProvider* 프로젝트 폴더로 이동 합니다.

## <a name="customize-policy-retrieval"></a>정책 검색 사용자 지정

ASP.NET Core apps는 인터페이스 구현을 사용 `IAuthorizationPolicyProvider` 하 여 권한 부여 정책을 검색 합니다. 기본적으로 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 가 등록 되 고 사용 됩니다. `DefaultAuthorizationPolicyProvider``AuthorizationOptions`호출에서 제공 된 정책을 반환 합니다 `IServiceCollection.AddAuthorization` .

`IAuthorizationPolicyProvider`앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 다른 구현을 등록 하 여이 동작을 사용자 지정 합니다. 

인터페이스에는 `IAuthorizationPolicyProvider` 다음과 같은 세 가지 api가 포함 되어 있습니다.

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 는 지정 된 이름에 대 한 권한 부여 정책을 반환 합니다.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 는 기본 권한 부여 정책 ( `[Authorize]` 정책이 지정 되지 않은 특성에 사용 되는 정책)을 반환 합니다. 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) 는 대체 권한 부여 정책 (정책을 지정 하지 않은 경우 권한 부여 미들웨어에서 사용 되는 정책)을 반환 합니다. 

이러한 Api를 구현 하 여 권한 부여 정책이 제공 되는 방식을 사용자 지정할 수 있습니다.

## <a name="parameterized-authorize-attribute-example"></a>매개 변수가 있는 권한 부여 특성 예제

가 유용한 시나리오 중 하나는 `IAuthorizationPolicyProvider` `[Authorize]` 요구 사항이 매개 변수에 따라 달라 지는 사용자 지정 특성을 사용 하는 것입니다. 예를 들어 [정책 기반 권한 부여](xref:security/authorization/policies) 설명서에서 사용 기간 기반 ("AtLeast21") 정책은 샘플로 사용 되었습니다. 앱의 다른 컨트롤러 작업을 *다른* 연령의 사용자가 사용할 수 있도록 설정 해야 하는 경우 다양 한 연령 기반 정책을 사용 하는 것이 유용할 수 있습니다. 응용 프로그램에 필요한 다른 모든 연령 기반 정책을 등록 하는 대신 `AuthorizationOptions` 사용자 지정을 사용 하 여 정책을 동적으로 생성할 수 있습니다 `IAuthorizationPolicyProvider` . 정책을 더 쉽게 사용 하기 위해와 같은 사용자 지정 권한 부여 특성을 사용 하 여 작업에 주석을 달 수 있습니다 `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>사용자 지정 권한 부여 특성

권한 부여 정책은 이름으로 식별 됩니다. `MinimumAgeAuthorizeAttribute`앞에서 설명한 사용자 지정은 인수를 해당 권한 부여 정책을 검색 하는 데 사용할 수 있는 문자열에 매핑해야 합니다. 에서 파생 시켜 속성을 래핑하는 방법으로이 작업을 수행할 수 있습니다 `AuthorizeAttribute` `Age` `AuthorizeAttribute.Policy` .

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

이 특성 형식에는 `Policy` 하드 코드 된 접두사 ()를 기반으로 `"MinimumAge"` 하는 문자열과 생성자를 통해 전달 된 정수가 있습니다.

`Authorize`정수를 매개 변수로 사용 한다는 점을 제외 하 고 다른 특성과 동일한 방식으로 작업에 적용할 수 있습니다.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>사용자 지정 IAuthorizationPolicyProvider

사용자 지정을 `MinimumAgeAuthorizeAttribute` 사용 하면 원하는 최소 사용 기간에 대 한 권한 부여 정책을 쉽게 요청할 수 있습니다. 다음에 해결 해야 하는 문제는 다른 모든 연령에 대해 권한 부여 정책을 사용할 수 있는지 확인 하는 것입니다. 여기서는 `IAuthorizationPolicyProvider` 유용 합니다.

를 사용 하는 경우 `MinimumAgeAuthorizationAttribute` 권한 부여 정책 이름이 패턴 `"MinimumAge" + Age` 을 따르기 때문에 사용자 지정은 `IAuthorizationPolicyProvider` 다음을 통해 권한 부여 정책을 생성 해야 합니다.

* 정책 이름에서 나이를 구문 분석 합니다.
* 을 사용 하 여 `AuthorizationPolicyBuilder` 새을 만듭니다. `AuthorizationPolicy`
* 이 및 다음 예제에서는 사용자가를 통해 인증 된 것으로 간주 됩니다 cookie . 는 `AuthorizationPolicyBuilder` 하나 이상의 인증 체계 이름을 사용 하 여 생성 되거나 항상 성공 해야 합니다. 그렇지 않으면 사용자에 게 챌린지를 제공 하는 방법에 대 한 정보가 없으므로 예외가 throw 됩니다.
* 사용 기간에 따라 정책에 요구 사항을 추가 `AuthorizationPolicyBuilder.AddRequirements` 합니다. 다른 시나리오에서는, 또는를 대신 사용할 수 있습니다 `RequireClaim` `RequireRole` `RequireUserName` .

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
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>여러 권한 부여 정책 공급자

사용자 지정 구현을 사용 하 `IAuthorizationPolicyProvider` 는 경우 ASP.NET Core는 인스턴스를 하나만 사용 한다는 점에 유의 하세요 `IAuthorizationPolicyProvider` . 사용자 지정 공급자가 사용할 모든 정책 이름에 대해 권한 부여 정책을 제공할 수 없는 경우 백업 공급자에 게이를 연기 해야 합니다. 

예를 들어 사용자 지정 사용 기간 정책과 기존의 역할 기반 정책 검색을 모두 필요로 하는 응용 프로그램을 가정해 보겠습니다. 이러한 앱은 다음과 같은 사용자 지정 권한 부여 정책 공급자를 사용할 수 있습니다.

* 정책 이름을 구문 분석 하려고 합니다. 
* `DefaultAuthorizationPolicyProvider`정책 이름에 age가 포함 되어 있지 않으면 다른 정책 공급자 (예:)를 호출 합니다.

`IAuthorizationPolicyProvider`위에 표시 된 예제 구현은 해당 `DefaultAuthorizationPolicyProvider` 생성자에서 백업 정책 공급자를 만들어를 사용 하도록 업데이트할 수 있습니다 (정책 이름이 ' 이상 보존 기간 ' + 연령의 예상 패턴과 일치 하지 않는 경우에 사용 됨).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

그런 다음 `GetPolicyAsync` null을 반환 하는 대신를 사용 하도록 메서드를 업데이트할 수 있습니다 `BackupPolicyProvider` .

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>기본 정책

명명 된 권한 부여 정책을 제공 하는 것 외에도 사용자 지정은를 `IAuthorizationPolicyProvider` 구현 하 여 `GetDefaultPolicyAsync` `[Authorize]` 정책 이름이 지정 되지 않은 특성에 대 한 권한 부여 정책을 제공 해야 합니다.

대부분의 경우이 권한 부여 특성에는 인증 된 사용자만 필요 하므로를 호출 하 여 필요한 정책을 만들 수 있습니다 `RequireAuthenticatedUser` .

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

사용자 지정의 모든 측면과 마찬가지로 `IAuthorizationPolicyProvider` 필요에 따라이를 사용자 지정할 수 있습니다. 경우에 따라 대체 (fallback)에서 기본 정책을 검색 하는 것이 좋을 수도 있습니다 `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>대체 정책

사용자 지정은 선택적으로를 `IAuthorizationPolicyProvider` 구현 하 여 정책을 `GetFallbackPolicyAsync` [조합할](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) 때 및 정책을 지정 하지 않을 때 사용 되는 정책을 제공할 수 있습니다. 에서 `GetFallbackPolicyAsync` null이 아닌 정책을 반환 하는 경우 요청에 정책을 지정 하지 않은 경우 권한 부여 미들웨어에서 반환 된 정책을 사용 합니다.

대체 (fallback) 정책이 필요 하지 않은 경우 공급자는 `null` 대체 (fallback) 공급자를 반환 하거나 연기할 수 있습니다.

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>사용자 지정 IAuthorizationPolicyProvider 사용

에서 사용자 지정 정책을 사용 하려면 `IAuthorizationPolicyProvider` 다음을 수행 **해야 합니다** .

_ `AuthorizationHandler` 모든 정책 기반 권한 부여 시나리오와 마찬가지로 종속성 주입 ( [정책 기반 권한 부여](xref:security/authorization/policies#authorization-handlers)에 설명)을 사용 하 여 적절 한 형식을 등록 합니다.
* `IAuthorizationPolicyProvider`응용 프로그램의 종속성 주입 서비스 컬렉션에서 사용자 지정 형식을 등록 `Startup.ConfigureServices` 하 여 기본 정책 공급자를 대체 합니다.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

전체 사용자 지정 `IAuthorizationPolicyProvider` 샘플은 [dotnet/aspnetcore GitHub 리포지토리에서](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)사용할 수 있습니다.
