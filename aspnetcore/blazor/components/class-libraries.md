---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 구성 요소를 Blazor 앱에 포함하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252554"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>ASP.NET Core Razor 구성 요소 클래스 라이브러리

작성자: [Simon Timms](https://github.com/stimms)

프로젝트 간에 [RCL(Razor 클래스 라이브러리)](xref:razor-pages/ui-class)의 구성 요소를 공유할 수 있습니다. 다음 위치에서 ‘Razor 구성 요소 클래스 라이브러리’를 포함할 수 있습니다.

* 솔루션의 다른 프로젝트
* NuGet 패키지
* 참조된 .NET 라이브러리

구성 요소가 일반적인 .NET 형식인 것처럼, RCL에서 제공하는 구성 요소는 일반적인 .NET 어셈블리입니다.

## <a name="create-an-rcl"></a>RCL 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 새 프로젝트를 만듭니다.
1. **Razor 클래스 라이브러리** 를 선택합니다. **새로 만들기** 를 선택합니다.
1. **새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기** 를 선택합니다.
1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. 이 항목의 예제에서는 프로젝트 이름으로 `ComponentLibrary`을 사용합니다. **만들기** 를 선택합니다.
1. 솔루션에 RCL을 추가합니다.
   1. 솔루션을 마우스 오른쪽 단추로 클릭합니다. **추가** > **기존 프로젝트** 를 선택합니다.
   1. RCL의 프로젝트 파일로 이동합니다.
   1. RCL의 프로젝트 파일(`.csproj`)을 선택합니다.
1. 앱에서 RCL에 대한 참조를 추가합니다.
   1. 앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **참조** 를 선택합니다.
   1. RCL 프로젝트를 선택합니다. **확인** 을 선택합니다.

> [!NOTE]
> 템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란을 선택한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> 생성된 프로젝트 루트에 파일을 수동으로 추가합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 명령 셸에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 통해 **Razor 클래스 라이브러리** 템플릿(`razorclasslib`)을 사용합니다. 다음 예제에서는 이름이 `ComponentLibrary`인 RCL을 만듭니다. 명령을 실행하면 `ComponentLibrary`을 포함하는 폴더가 자동으로 생성됩니다.

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > 템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > 생성된 프로젝트 루트에 파일을 수동으로 추가합니다.

1. 기존 프로젝트에 라이브러리를 추가하려면 명령 셸에서 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 명령을 사용합니다. 다음 예제에서는 앱에 RCL을 추가합니다. 라이브러리 경로를 사용하여 앱의 프로젝트 폴더에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>라이브러리 구성 요소 사용

다른 프로젝트의 라이브러리에서 정의된 구성 요소를 사용하려면 다음 방법의 하나를 사용합니다.

* 네임스페이스를 포함하는 전체 형식 이름을 사용합니다.
* Razor의 [`@using`](xref:mvc/views/razor#using) 지시문을 사용합니다. 개별 구성 요소는 이름으로 추가할 수 있습니다.

다음 예제에서 `ComponentLibrary`는 `Component1` 구성 요소(`Component1.razor`)가 포함된 구성 요소 라이브러리입니다. `Component1` 구성 요소는 라이브러리가 만들어질 때 RCL 프로젝트 템플릿에 의해 자동으로 추가되는 예제 구성 요소입니다.

네임스페이스를 사용하여 `Component1` 구성 요소를 참조합니다.

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

또는 [`@using`](xref:mvc/views/razor#using) 지시문을 사용하여 라이브러리를 범위로 전환하고 네임스페이스 없이 구성 요소를 사용합니다.

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

원하는 경우 최상위 `_Import.razor` 파일에 `@using ComponentLibrary` 지시문을 포함하여 전체 프로젝트에서 라이브러리 구성 요소를 사용할 수 있게 합니다. 임의 수준의 `_Import.razor` 파일에 지시문을 추가하여 폴더 내의 단일 구성 요소 또는 구성 요소 집합에 네임스페이스를 적용합니다.

::: moniker range=">= aspnetcore-5.0"

[CSS 격리](xref:blazor/components/css-isolation)를 사용하는 라이브러리 구성 요소의 경우 라이브러리를 사용하는 앱에서 라이브러리의 개별 구성 요소 스타일시트를 명시적으로 연결할 필요가 없습니다. 구성 요소 스타일은 사용 앱에 자동으로 제공됩니다.

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

라이브러리의 `wwwroot` 폴더에 있는 스타일시트에서 추가 라이브러리 구성 요소 스타일을 제공하려면 사용 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 스타일시트를 연결합니다.

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Component1`의 `my-component` CSS 클래스를 제공하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 라이브러리의 스타일시트에 연결합니다.

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>정적 자산을 사용하여 Razor 구성 요소 클래스 라이브러리 만들기

RCL에는 정적 자산이 포함될 수 있습니다. 정적 자산은 라이브러리를 사용하는 모든 앱에서 제공됩니다. 자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>를 참조하세요.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>호스트된 여러 Blazor 앱에 구성 요소 및 정적 자산 제공

자세한 내용은 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>를 참조하세요.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Blazor WebAssembly용 브라우저 호환성 분석기

Blazor WebAssembly 앱은 전체 .NET API 노출 영역을 대상으로 하지만 일부 .NET API는 브라우저 샌드박스 제약 조건으로 인해 WebAssembly에서 지원되지 않습니다. 지원되지 않는 API는 WebAssembly에서 실행될 때 <xref:System.PlatformNotSupportedException>을 throw합니다. 플랫폼 호환성 분석기는 앱이 앱의 대상 플랫폼에서 지원하지 않는 API를 사용하는 경우 개발자에게 경고합니다. Blazor WebAssembly 앱에서는 API가 브라우저에서 지원되는지를 확인한다는 의미입니다. 호환성 분석기용 .NET Framework API에 주석 달기는 지속적인 프로세스이므로 일부 .NET Framework API에는 현재 주석이 달려 있지 않습니다.

Blazor WebAssembly 및 Razor 클래스 라이브러리 프로젝트는 `SupportedPlatform` MSBuild 항목을 사용하여 지원되는 플랫폼으로 `browser`를 추가함으로써 브라우저 호환성 검사를 ‘자동으로’ 사용하도록 설정합니다. 라이브러리 개발자는 라이브러리의 프로젝트 파일에 `SupportedPlatform` 항목을 수동으로 추가하여 기능을 사용하도록 설정할 수 있습니다.

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

라이브러리를 작성할 때 `browser`를 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>로 지정하여 특정 API가 브라우저에서 지원되지 않음을 나타냅니다.

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

자세한 내용은 [Annotating APIs as unsupported on specific platforms(dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)(특정 플랫폼에서 지원되지 않음으로 API에 주석 달기(dotnet/designs GitHub 리포지토리))를 참조하세요.

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 격리 및 개체 참조

Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다. JavaScript 격리는 다음과 같은 이점을 제공합니다.

* 가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.
* 라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 수동으로 가져올 필요가 없습니다.

자세한 내용은 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>를 참조하세요.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>빌드, 패키지 및 NuGet에 제공

구성 요소 라이브러리는 표준 .NET 라이브러리이기 때문에 패키지하여 NuGet에 제공하는 과정이 라이브러리를 패키지하여 NuGet에 제공하는 과정과 다르지 않습니다. 명령 셸에서 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 패키지합니다.

```dotnetcli
dotnet pack
```

명령 셸에서 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용하여 패키지를 NuGet에 업로드합니다.

## <a name="additional-resources"></a>추가 자료

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [라이브러리에 XML IL(중간 언어) 트리머 구성 파일 추가](xref:blazor/host-and-deploy/configure-trimmer)
* [Razor 클래스 라이브러리를 사용한 CSS 격리 지원](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [라이브러리에 XML IL(중간 언어) 링커 구성 파일 추가](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
