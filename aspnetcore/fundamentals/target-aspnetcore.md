---
title: 클래스 라이브러리에서 ASP.NET Core API 사용
author: scottaddie
description: 클래스 라이브러리에서 ASP.NET Core API를 사용하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 571e6c66f60bbc09b902ff9064d2fb1c18c433dc
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "88630057"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>클래스 라이브러리에서 ASP.NET Core API 사용

작성자: [Scott Addie](https://github.com/scottaddie)

이 문서에서는 클래스 라이브러리에서 ASP.NET Core API를 사용하는 방법을 설명합니다. 다른 모든 라이브러리 지침은 [오픈 소스 라이브러리 지침](/dotnet/standard/library-guidance/)을 참조하세요.

## <a name="determine-which-aspnet-core-versions-to-support"></a>지원할 ASP.NET Core 버전 결정

ASP.NET Core는 [.NET Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 준수합니다. 라이브러리에서 지원할 ASP.NET Core 버전을 결정할 때 지원 정책을 참조하세요. 라이브러리는 다음을 수행해야 합니다.

* *LTS* (장기 지원)로 분류된 모든 ASP.NET Core 버전을 지원하도록 노력합니다.
* *EOL* (수명 종료)로 분류된 ASP.NET Core 버전을 지원할 의무는 없습니다.

ASP.NET Core 미리 보기 릴리스가 출시되면 [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub 리포지토리에 최신 변경 내용이 게시됩니다. 프레임워크 기능이 개발되는 동안 라이브러리의 호환성 테스트를 수행할 수 있습니다.

## <a name="use-the-aspnet-core-shared-framework"></a>.NET Core 공유 프레임워크 사용

.NET Core 3.0이 출시되면 많은 ASP.NET Core 어셈블리가 더 이상 NuGet에 패키지로 게시되지 않습니다. 대신 어셈블리는 .NET Core SDK 및 런타임 설치 관리자와 함께 설치되는 `Microsoft.AspNetCore.App` 공유 프레임워크에 포함됩니다. 더 이상 게시되지 않는 패키지 목록은 [사용되지 않는 패키지 참조 제거](xref:migration/22-to-30#remove-obsolete-package-references)를 참조하세요.

.NET Core 3.0부터 `Microsoft.NET.Sdk.Web` MSBuild SDK를 사용하는 프로젝트는 공유 프레임워크를 암시적으로 참조합니다. `Microsoft.NET.Sdk` 또는 `Microsoft.NET.Sdk.Razor` SDK를 사용하는 프로젝트는 공유 프레임워크에서 ASP.NET Core API를 사용하도록 ASP.NET Core을 참조해야 합니다.

ASP.NET Core를 참조하려면 프로젝트 파일에 다음 `<FrameworkReference>` 요소를 추가합니다.

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

이러한 방식의 ASP.NET Core 참조는 .NET Core 3.x를 대상으로 하는 프로젝트에만 지원됩니다.

## <a name="include-no-locblazor-extensibility"></a>Blazor 확장성 포함

Blazor는 WASM(WebAssembly) 및 Server [호스팅 모델](xref:blazor/hosting-models)을 지원합니다. 특별한 이유가 없는 한 [Razor 구성 요소](xref:blazor/components/index) 라이브러리에서 두 호스팅 모델을 모두 지원해야 합니다. Razor 구성 요소 라이브러리는 [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)를 사용해야 합니다.

### <a name="support-both-hosting-models"></a>두 호스팅 모델 모두 지원

[Blazor Server](xref:blazor/hosting-models#blazor-server) 및 [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) 프로젝트 모두에서 Razor 구성 요소를 사용하도록 지원하려면 편집기에 대해 다음 지침을 사용합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Razor 클래스 라이브러리** 프로젝트 템플릿을 사용합니다. 템플릿의 **지원 페이지 및 보기** 확인란을 선택 취소해야 합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

통합 터미널에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

**Razor 클래스 라이브러리** 프로젝트 템플릿을 사용합니다.

---

템플릿에서 생성된 프로젝트는 다음과 같은 작업을 수행합니다.

* .NET Standard 2.0을 대상으로 합니다.
* `RazorLangVersion` 속성을 `3.0`로 설정합니다. `3.0`은 .NET Core 3.x의 기본값입니다.
* 다음 패키지 참조를 추가합니다.
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>특정 호스팅 모델 지원

단일 Blazor 호스팅 모델을 지원하는 것은 일반적이지 않습니다. 예를 들어 [Blazor Server](xref:blazor/hosting-models#blazor-server) 프로젝트에서만 Razor 구성 요소를 사용하도록 지원하려면 다음을 수행합니다.

* .NET Core 3.x를 대상으로 합니다.
* 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.

예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Razor 구성 요소가 포함된 라이브러리에 대한 자세한 내용은 [ASP.NET Core Razor 구성 요소 클래스 라이브러리](xref:blazor/components/class-libraries)를 참조하세요.

## <a name="include-mvc-extensibility"></a>MVC 확장성 포함

이 섹션에서는 다음을 포함하는 라이브러리에 대한 권장 사항을 개략적으로 설명합니다.

* [Razor 보기 또는 Razor Pages](#razor-views-or-razor-pages)
* [태그 도우미](#tag-helpers)
* [뷰 구성 요소](#view-components)

이 섹션에서는 여러 버전의 MVC를 지원하는 멀티 타기팅에 대해서는 설명하지 않습니다. 여러 ASP.NET Core 버전을 지원하는 방법에 대한 지침은 [여러 ASP.NET Core 버전 지원](#support-multiple-aspnet-core-versions)을 참조하세요.

### <a name="no-locrazor-views-or-no-locrazor-pages"></a>Razor 보기 또는 Razor Pages

[Razor 보기](xref:mvc/views/overview) 또는 [Razor Pages](xref:razor-pages/index)를 포함하는 프로젝트는 [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk)를 사용해야 합니다.

프로젝트가 .NET Core 3.x를 대상으로 하는 경우 다음이 필요합니다.

* `true`로 설정된 `AddRazorSupportForMvc` MSBuild 속성.
* 공유 프레임워크에 대한 `<FrameworkReference>` 요소.

**Razor 클래스 라이브러리** 프로젝트 템플릿은 .NET Core 3.x를 대상으로 하는 프로젝트에 대해 위 요구 사항을 충족합니다. 편집기에 대해 다음 지침을 사용합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Razor 클래스 라이브러리** 프로젝트 템플릿을 사용합니다. 템플릿의 **지원 페이지 및 보기** 확인란을 선택해야 합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

통합 터미널에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

지금은 프로젝트 템플릿이 지원되지 않습니다.

---

예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

프로젝트가 대신 .NET Standard를 대상으로 하는 경우 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 패키지 참조가 필요합니다. `Microsoft.AspNetCore.Mvc` 패키지가 ASP.NET Core 3.0의 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다. 예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>태그 도우미

[태그 도우미](xref:mvc/views/tag-helpers/intro)를 포함하는 프로젝트는 `Microsoft.NET.Sdk` SDK를 사용해야 합니다. .NET Core 3.x를 대상으로 하는 경우 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다. 예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

.NET Standard를 대상으로 하는 경우(ASP.NET Core 3.x보다 이전 버전을 지원하기 위해) [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)에 대한 패키지 참조를 추가합니다. `Microsoft.AspNetCore.Mvc.Razor` 패키지는 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다. 예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>보기 구성 요소

[구성 요소 보기](xref:mvc/views/view-components)를 포함하는 프로젝트는 `Microsoft.NET.Sdk` SDK를 사용해야 합니다. .NET Core 3.x를 대상으로 하는 경우 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다. 예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

.NET Standard를 대상으로 하는 경우(ASP.NET Core 3.x보다 이전 버전을 지원하기 위해) [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)에 대한 패키지 참조를 추가합니다. `Microsoft.AspNetCore.Mvc.ViewFeatures` 패키지는 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다. 예를 들어:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>여러 ASP.NET Core 버전 지원

멀티 타기팅은 ASP.NET Core의 여러 변형을 지원하는 라이브러리를 작성하는 데 필요합니다. 태그 도우미 라이브러리가 다음 ASP.NET Core 변형을 지원해야 하는 시나리오를 고려합니다.

* .NET Framework 4.6.1을 대상으로 하는 ASP.NET Core 2.1
* .NET Core 2.x를 대상으로 하는 ASP.NET Core 2.x
* .NET Core 3.x를 대상으로 하는 ASP.NET Core 3.x

다음 프로젝트 파일은 `TargetFrameworks` 속성을 통해 이러한 변형을 지원합니다.

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

이전 프로젝트 파일 사용:

* 모든 소비자에게 `Markdig` 패키지가 추가됩니다.
* [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)에 대한 참조는 .NET Framework 4.6.1 이상 또는 .NET Core 2.x를 대상으로 하는 소비자에게 추가됩니다. 이전 버전과의 호환성으로 인해 패키지의 버전 2.1.0은 ASP.NET Core 2.2에서 작동합니다.
* 공유 프레임워크는 .NET Core 3.x를 대상으로 하는 소비자에 대해 참조됩니다. `Microsoft.AspNetCore.Mvc.Razor` 패키지는 공유 프레임워크에 포함됩니다.

또는 .NET Core 2.1 및 .NET Framework 4.6.1을 모두 대상으로 지정하는 대신 .NET Standard 2.0을 대상으로 지정할 수 있습니다.

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

이전 프로젝트 파일에는 다음과 같은 주의 사항이 있습니다.

* 라이브러리에는 태그 도우미만 포함되어 있으므로 ASP.NET Core가 실행되는 특정 플랫폼, 즉 .NET Core 및 .NET Framework을 대상으로 지정하는 것이 더 간단합니다. 태그 도우미를 Unity, UWP, Xamarin 같은 다른 .NET Standard 2.0 호환 대상 프레임워크에서 사용할 수는 없습니다.
* .NET Framework의 .NET Standard 2.0을 사용하는 경우 .NET Framework 4.7.2에서 해결된 몇 가지 문제가 발생합니다. .NET Framework 4.6.1을 대상으로 하여 .NET Framework 4.6.1 ~ 4.7.1을 사용하는 소비자에 대한 환경을 개선할 수 있습니다.

라이브러리가 플랫폼별 API를 호출해야 하는 경우 .NET Standard 대신 특정 .NET 구현을 대상으로 지정합니다. 자세한 내용은 [멀티 타기팅](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)을 참조하세요.

## <a name="use-an-api-that-hasnt-changed"></a>변경되지 않은 API 사용

미들웨어 라이브러리를 .NET Core 2.2에서 3.0으로 업그레이드하는 시나리오를 가정합니다. 라이브러리에서 사용되는 ASP.NET Core 미들웨어 API는 ASP.NET Core 2.2와 3.0 사이에서 변경되지 않았습니다. .NET Core 3.0에서 미들웨어 라이브러리를 계속 지원하려면 다음 단계를 수행합니다.

* [표준 라이브러리 지침](/dotnet/standard/library-guidance/)을 따릅니다.
* 해당 어셈블리가 공유 프레임워크에 존재하지 않는 경우 각 API의 NuGet 패키지에 대한 패키지 참조를 추가합니다.

## <a name="use-an-api-that-changed"></a>변경된 API 사용

라이브러리를 .NET Core 2.2에서 .NET Core 3.0으로 업그레이드하는 시나리오를 가정합니다. 라이브러리에서 사용되는 ASP.NET Core API는 ASP.NET Core 3.0에 [주요 변경 내용](/dotnet/core/compatibility/breaking-changes)이 있습니다. 모든 버전에서 변경된 API를 사용하지 않도록 라이브러리를 다시 작성할 수 있는지 여부를 고려합니다.

라이브러리를 다시 작성할 수 있는 경우 다시 작성하여 패키지 참조가 포함된 이전 대상 프레임워크(예: .NET Standard 2.0 또는 .NET Framework 4.6.1)를 계속 대상으로 합니다.

라이브러리를 다시 작성할 수 없는 경우 다음 단계를 수행합니다.

* .NET Core 3.0에 대한 대상을 추가합니다.
* 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.
* 적절한 대상 프레임워크 기호와 함께 [#if 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)을 사용하여 조건부로 코드를 컴파일합니다.

예를 들어 HTTP 요청 및 응답 스트림에 대한 동기 읽기 및 쓰기는 ASP.NET Core 3.0부터 기본적으로 사용하지 않도록 설정됩니다. ASP.NET Core 2.2는 기본적으로 동기식 동작을 지원합니다. I/O가 발생하는 경우 동기 읽기 및 쓰기를 사용하도록 설정해야 하는 미들웨어 라이브러리를 고려합니다. 라이브러리는 적절한 전처리기 지시문에서 동기 기능을 사용하도록 설정하는 코드를 포함해야 합니다. 예를 들어:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>3\.0에 도입된 API 사용

ASP.NET Core 3.0에 도입된 ASP.NET Core API를 사용하려 한다고 가정합니다. 다음 질문을 살펴보세요.

1. 라이브러리가 기능하려면 새 API가 필요한가요?
1. 라이브러리에서 이 기능을 다른 방식으로 구현할 수 있나요?

라이브러리에 API가 필요하고 이 API를 하위 수준에서 구현할 방법이 없는 경우:

* .NET Core 3.x만을 대상으로 합니다.
* 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.

라이브러리가 다른 방식으로 기능을 구현할 수 있는 경우:

* .NET Core 3.x를 대상 프레임워크로 추가합니다.
* 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.
* 적절한 대상 프레임워크 기호와 함께 [#if 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)을 사용하여 조건부로 코드를 컴파일합니다.

예를 들어 다음 태그 도우미는 ASP.NET Core 3.0에 도입된 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 인터페이스를 사용합니다. .NET Core 3.0을 대상으로 하는 소비자는 `NETCOREAPP3_0` 대상 프레임워크 기호로 정의된 코드 경로를 실행합니다. 태그 도우미의 생성자 매개 변수 형식이 .NET Core 2.1 및 .NET Framework 4.6.1 소비자에 대한 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>로 변경됩니다. 이 변경은 ASP.NET Core 3.0이 `IHostingEnvironment`를 사용되지 않음으로 표시하고 `IWebHostEnvironment`를 대체 형식으로 권장했기 때문에 필요했습니다.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

다음의 멀티 타게팅 프로젝트 파일은 이 태그 도우미 시나리오를 지원합니다.

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>공유 프레임워크에서 제거된 API 사용

공유 프레임워크에서 제거된 ASP.NET Core 어셈블리를 사용하려면 적절한 패키지 참조를 추가합니다. ASP.NET Core 3.0의 공유 프레임워크에서 제거되는 패키지 목록은 [사용되지 않는 패키지 참조 제거](xref:migration/22-to-30#remove-obsolete-package-references)를 참조하세요.

예를 들어 웹 API 클라이언트를 추가하려면 다음을 수행합니다.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>추가 자료

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [.NET 구현 지원](/dotnet/standard/net-standard#net-implementation-support)
* [.NET 지원 정책](https://dotnet.microsoft.com/platform/support/policy)
