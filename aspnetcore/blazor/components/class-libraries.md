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
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="edc2b-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="edc2b-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="edc2b-104">작성자: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="edc2b-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="edc2b-105">프로젝트 간에 [RCL(Razor 클래스 라이브러리)](xref:razor-pages/ui-class)의 구성 요소를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="edc2b-106">다음 위치에서 ‘Razor 구성 요소 클래스 라이브러리’를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="edc2b-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="edc2b-107">Another project in the solution.</span></span>
* <span data-ttu-id="edc2b-108">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="edc2b-108">A NuGet package.</span></span>
* <span data-ttu-id="edc2b-109">참조된 .NET 라이브러리</span><span class="sxs-lookup"><span data-stu-id="edc2b-109">A referenced .NET library.</span></span>

<span data-ttu-id="edc2b-110">구성 요소가 일반적인 .NET 형식인 것처럼, RCL에서 제공하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="edc2b-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="edc2b-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="edc2b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="edc2b-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="edc2b-113">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-113">Create a new project.</span></span>
1. <span data-ttu-id="edc2b-114">**Razor 클래스 라이브러리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="edc2b-115">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-115">Select **Next**.</span></span>
1. <span data-ttu-id="edc2b-116">**새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="edc2b-117">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="edc2b-118">이 항목의 예제에서는 프로젝트 이름으로 `ComponentLibrary`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="edc2b-119">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-119">Select **Create**.</span></span>
1. <span data-ttu-id="edc2b-120">솔루션에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="edc2b-121">솔루션을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-121">Right-click the solution.</span></span> <span data-ttu-id="edc2b-122">**추가** > **기존 프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="edc2b-123">RCL의 프로젝트 파일로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="edc2b-124">RCL의 프로젝트 파일(`.csproj`)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="edc2b-125">앱에서 RCL에 대한 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="edc2b-126">앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-126">Right-click the app project.</span></span> <span data-ttu-id="edc2b-127">**추가** > **참조** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="edc2b-128">RCL 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-128">Select the RCL project.</span></span> <span data-ttu-id="edc2b-129">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="edc2b-130">템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란을 선택한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="edc2b-131">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="edc2b-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="edc2b-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="edc2b-133">명령 셸에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 통해 **Razor 클래스 라이브러리** 템플릿(`razorclasslib`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="edc2b-134">다음 예제에서는 이름이 `ComponentLibrary`인 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="edc2b-135">명령을 실행하면 `ComponentLibrary`을 포함하는 폴더가 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="edc2b-136">템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="edc2b-137">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="edc2b-138">기존 프로젝트에 라이브러리를 추가하려면 명령 셸에서 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="edc2b-139">다음 예제에서는 앱에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="edc2b-140">라이브러리 경로를 사용하여 앱의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="edc2b-141">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="edc2b-141">Consume a library component</span></span>

<span data-ttu-id="edc2b-142">다른 프로젝트의 라이브러리에서 정의된 구성 요소를 사용하려면 다음 방법의 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="edc2b-143">네임스페이스를 포함하는 전체 형식 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="edc2b-144">Razor의 [`@using`](xref:mvc/views/razor#using) 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="edc2b-145">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-145">Individual components can be added by name.</span></span>

<span data-ttu-id="edc2b-146">다음 예제에서 `ComponentLibrary`는 `Component1` 구성 요소(`Component1.razor`)가 포함된 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="edc2b-147">`Component1` 구성 요소는 라이브러리가 만들어질 때 RCL 프로젝트 템플릿에 의해 자동으로 추가되는 예제 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="edc2b-148">네임스페이스를 사용하여 `Component1` 구성 요소를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="edc2b-149">또는 [`@using`](xref:mvc/views/razor#using) 지시문을 사용하여 라이브러리를 범위로 전환하고 네임스페이스 없이 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="edc2b-150">원하는 경우 최상위 `_Import.razor` 파일에 `@using ComponentLibrary` 지시문을 포함하여 전체 프로젝트에서 라이브러리 구성 요소를 사용할 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="edc2b-151">임의 수준의 `_Import.razor` 파일에 지시문을 추가하여 폴더 내의 단일 구성 요소 또는 구성 요소 집합에 네임스페이스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="edc2b-152">[CSS 격리](xref:blazor/components/css-isolation)를 사용하는 라이브러리 구성 요소의 경우 라이브러리를 사용하는 앱에서 라이브러리의 개별 구성 요소 스타일시트를 명시적으로 연결할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="edc2b-153">구성 요소 스타일은 사용 앱에 자동으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-153">The component styles are automatically made available to the consuming app.</span></span>

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

<span data-ttu-id="edc2b-154">라이브러리의 `wwwroot` 폴더에 있는 스타일시트에서 추가 라이브러리 구성 요소 스타일을 제공하려면 사용 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 스타일시트를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

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

<span data-ttu-id="edc2b-155">`Component1`의 `my-component` CSS 클래스를 제공하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 라이브러리의 스타일시트에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="edc2b-156">정적 자산을 사용하여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="edc2b-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="edc2b-157">RCL에는 정적 자산이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-157">An RCL can include static assets.</span></span> <span data-ttu-id="edc2b-158">정적 자산은 라이브러리를 사용하는 모든 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="edc2b-159">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="edc2b-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="edc2b-160">호스트된 여러 Blazor 앱에 구성 요소 및 정적 자산 제공</span><span class="sxs-lookup"><span data-stu-id="edc2b-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="edc2b-161">자세한 내용은 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="edc2b-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="edc2b-162">Blazor WebAssembly용 브라우저 호환성 분석기</span><span class="sxs-lookup"><span data-stu-id="edc2b-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="edc2b-163">Blazor WebAssembly 앱은 전체 .NET API 노출 영역을 대상으로 하지만 일부 .NET API는 브라우저 샌드박스 제약 조건으로 인해 WebAssembly에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="edc2b-164">지원되지 않는 API는 WebAssembly에서 실행될 때 <xref:System.PlatformNotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="edc2b-165">플랫폼 호환성 분석기는 앱이 앱의 대상 플랫폼에서 지원하지 않는 API를 사용하는 경우 개발자에게 경고합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="edc2b-166">Blazor WebAssembly 앱에서는 API가 브라우저에서 지원되는지를 확인한다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="edc2b-167">호환성 분석기용 .NET Framework API에 주석 달기는 지속적인 프로세스이므로 일부 .NET Framework API에는 현재 주석이 달려 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="edc2b-168">Blazor WebAssembly 및 Razor 클래스 라이브러리 프로젝트는 `SupportedPlatform` MSBuild 항목을 사용하여 지원되는 플랫폼으로 `browser`를 추가함으로써 브라우저 호환성 검사를 ‘자동으로’ 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="edc2b-169">라이브러리 개발자는 라이브러리의 프로젝트 파일에 `SupportedPlatform` 항목을 수동으로 추가하여 기능을 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="edc2b-170">라이브러리를 작성할 때 `browser`를 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>로 지정하여 특정 API가 브라우저에서 지원되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="edc2b-171">자세한 내용은 [Annotating APIs as unsupported on specific platforms(dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)(특정 플랫폼에서 지원되지 않음으로 API에 주석 달기(dotnet/designs GitHub 리포지토리))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="edc2b-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="edc2b-172">Blazor JavaScript 격리 및 개체 참조</span><span class="sxs-lookup"><span data-stu-id="edc2b-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="edc2b-173">Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="edc2b-174">JavaScript 격리는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="edc2b-175">가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="edc2b-176">라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 수동으로 가져올 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="edc2b-177">자세한 내용은 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="edc2b-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="edc2b-178">빌드, 패키지 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="edc2b-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="edc2b-179">구성 요소 라이브러리는 표준 .NET 라이브러리이기 때문에 패키지하여 NuGet에 제공하는 과정이 라이브러리를 패키지하여 NuGet에 제공하는 과정과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="edc2b-180">명령 셸에서 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 패키지합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="edc2b-181">명령 셸에서 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용하여 패키지를 NuGet에 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="edc2b-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="edc2b-182">추가 자료</span><span class="sxs-lookup"><span data-stu-id="edc2b-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="edc2b-183">라이브러리에 XML IL(중간 언어) 트리머 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="edc2b-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="edc2b-184">Razor 클래스 라이브러리를 사용한 CSS 격리 지원</span><span class="sxs-lookup"><span data-stu-id="edc2b-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="edc2b-185">라이브러리에 XML IL(중간 언어) 링커 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="edc2b-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
