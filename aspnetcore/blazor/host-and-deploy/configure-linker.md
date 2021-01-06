---
title: ASP.NET Core Blazor용 링커 구성
author: guardrex
description: Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 0c99056053356133e901d6cf468fec8034dfb845
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055830"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="fba3b-103">ASP.NET Core Blazor용 링커 구성</span><span class="sxs-lookup"><span data-stu-id="fba3b-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="fba3b-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="fba3b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fba3b-105">Blazor WebAssembly는 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 잘라냅니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="fba3b-106">디버그 구성에서 빌드할 때 링커를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="fba3b-107">링커를 사용하도록 설정하려면 앱이 릴리스 구성으로 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="fba3b-108">Blazor WebAssembly 앱을 배포할 때 릴리스에서 빌드하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="fba3b-109">앱 연결은 크기에 맞게 최적화되지만 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="fba3b-110">링커는 이 동적 동작에 대한 정보를 알지 못하고 일반적으로 런타임에서 리플렉션에 필요한 형식을 결정할 수 없기 때문에, 리플렉션 또는 관련 동적 기능을 사용하는 앱은 잘린 경우 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="fba3b-111">이러한 앱을 잘라내려면 링커에서는 앱이 의존하는 코드 및 패키지 또는 프레임워크의 리플렉션에서 필요로 하는 형식에 대해 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="fba3b-112">잘린 앱이 배포된 후 올바로 작동하도록 하려면 개발하는 동안 앱의 릴리스 빌드를 자주 테스트하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="fba3b-113">다음 MSBuild 기능을 사용하여 Blazor 앱에 대한 연결을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="fba3b-114">[MSBuild 속성](#control-linking-with-an-msbuild-property)을 사용하여 전역적으로 연결을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="fba3b-115">[구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="fba3b-116">MSBuild 속성을 사용하여 연결 제어</span><span class="sxs-lookup"><span data-stu-id="fba3b-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="fba3b-117">앱이 `Release` 구성에서 빌드될 때 연결이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="fba3b-118">이를 변경하려면 프로젝트 파일에서 `BlazorWebAssemblyEnableLinking` MSBuild 속성을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="fba3b-119">구성 파일과의 연결 제어</span><span class="sxs-lookup"><span data-stu-id="fba3b-119">Control linking with a configuration file</span></span>

<span data-ttu-id="fba3b-120">XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="fba3b-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="fba3b-121">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="fba3b-122">자세한 내용 및 예제는 [데이터 형식](https://github.com/mono/linker/blob/master/docs/data-formats.md)(mono/linker GitHub 리포지토리)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fba3b-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="fba3b-123">라이브러리에 XML 링커 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="fba3b-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="fba3b-124">특정 라이브러리의 링커를 구성하려면 XML 링커 구성 파일을 포함 리소스로 라이브러리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="fba3b-125">포함 리소스의 이름은 어셈블리와 동일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="fba3b-126">다음 예제에서 `LinkerConfig.xml` 파일은 라이브러리의 어셈블리와 이름이 동일한 포함 리소스로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="fba3b-127">국제화를 위한 링커 구성</span><span class="sxs-lookup"><span data-stu-id="fba3b-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="fba3b-128">기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="fba3b-129">이 어셈블리를 제거하면 앱 크기를 최소화합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="fba3b-130">유지되는 I18N 어셈블리 종류를 제어하려면 프로젝트 파일에서 `<BlazorWebAssemblyI18NAssemblies>` MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fba3b-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="fba3b-131">지역 값</span><span class="sxs-lookup"><span data-stu-id="fba3b-131">Region Value</span></span>     | <span data-ttu-id="fba3b-132">Mono 지역 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fba3b-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="fba3b-133">포함된 모든 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fba3b-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="fba3b-134">`none`(기본값)</span><span class="sxs-lookup"><span data-stu-id="fba3b-134">`none` (default)</span></span> | <span data-ttu-id="fba3b-135">없음</span><span class="sxs-lookup"><span data-stu-id="fba3b-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="fba3b-136">여러 값을 지정할 경우 쉼표를 사용하여 구분합니다(예: `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="fba3b-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="fba3b-137">자세한 내용은 [I18N: Pnetlib 국제화 프레임워크 라이브러리(mono/mono GitHub 리포지토리)](https://github.com/mono/mono/tree/master/mcs/class/I18N)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fba3b-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fba3b-138">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fba3b-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
