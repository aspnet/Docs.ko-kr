---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor Pages를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060874"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>개요

[!INCLUDE[](~/includes/2.1-SDK.md)]에는 `Microsoft.NET.Sdk.Razor` MSBuild SDK(Razor SDK)가 포함되어 있습니다. Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* ASP.NET Core MVC 기반 또는 [Blazor](xref:blazor/index) 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트를 빌드, 패키지 및 게시하는 데 필요합니다.
* Razor( *.cshtml* 또는 *.razor*) 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 세트가 포함됩니다.

Razor SDK에는 `Include` 특성이 `**\*.cshtml` 및 `**\*.razor` 와일드카드 사용 패턴으로 설정된 `Content` 항목이 포함되어 있습니다. 일치 파일이 게시됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.
* Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.

Razor SDK에는 `Include` 특성이 `**\*.cshtml` 와일드카드 사용 패턴으로 설정된 `Content` 항목이 포함되어 있습니다. 일치 파일이 게시됩니다.

::: moniker-end

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Razor SDK 사용

대부분의 웹앱은 Razor SDK를 명시적으로 참조할 필요가 없습니다.

::: moniker range=">= aspnetcore-3.0"

Razor SDK를 사용하여 Razor 뷰나 Razor Pages를 포함하는 클래스 라이브러리를 빌드하려면 RCL(Razor 클래스 라이브러리) 프로젝트 템플릿을 사용하는 것이 좋습니다. Blazor( *.razor*) 파일을 빌드하는 데 사용되는 RCL에는 [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 패키지에 대한 참조가 필요합니다. Razor 뷰 또는 페이지( *.cshtml* 파일)를 빌드하는 데 사용되는 RCL은 적어도 `netcoreapp3.0` 이상을 대상으로 지정해야 하며 해당 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 대한 `FrameworkReference`가 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor SDK를 사용하여 Razor 뷰 또는 Razor Pages를 포함하는 클래스 라이브러리를 빌드하려면 다음을 수행 및 확인합니다.

* `Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 일반적으로 Razor Pages 및 Razor 뷰를 빌드하고 컴파일하는 데 필요한 추가 종속성을 수신하려면 `Microsoft.AspNetCore.Mvc`에 대한 패키지 참조가 필요합니다. 최소한 프로젝트는 다음에 패키지 참조를 추가해야 합니다.

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` 패키지는 프로젝트에 대한 Razor 컴파일 작업 및 대상을 제공합니다.

  이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다. 다음 태그에서는 Razor SDK를 사용하여 ASP.NET Core Razor Pages 앱용 Razor 파일을 빌드하는 프로젝트 파일을 보여 줍니다.

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` 및 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다. 그러나 더 낮은 버전의 `Microsoft.AspNetCore.App` 패키지 참조는 최신 `Microsoft.AspNetCore.Razor.Design` 버전을 포함하지 않는 앱에 대한 메타패키지를 제공합니다. 프로젝트는 Razor에 대한 최신 빌드 타임 수정 사항이 포함되도록 일관된 `Microsoft.AspNetCore.Razor.Design` 버전(또는 `Microsoft.AspNetCore.Mvc`)을 참조해야 합니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/aspnet/Razor/issues/2553)를 참조하세요.

::: moniker-end

### <a name="properties"></a>속성

다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.

* `RazorCompileOnBuild`: `true`인 경우, 프로젝트 빌드 중에 Razor 어셈블리를 컴파일하고 내보냅니다. 기본값은 `true`입니다.
* `RazorCompileOnPublish`: `true`인 경우, 프로젝트 게시 중에 Razor 어셈블리를 컴파일하고 내보냅니다. 기본값은 `true`입니다.

다음 표의 속성 및 항목은 Razor SDK에 대한 입력 및 출력을 구성하는 데 사용됩니다.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ASP.NET Core 3.0부터 프로젝트 파일의 `RazorCompileOnBuild` 또는 `RazorCompileOnPublish` MSBuild 속성을 사용할 수 없는 경우 MVC 뷰 또는 Razor Pages가 기본적으로 제공되지 않습니다. 애플리케이션은 런타임 컴파일을 사용하여 *.cshtml* 파일을 처리하는 경우 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) 패키지에 대한 명시적 참조를 추가해야 합니다.

::: moniker-end

| 항목 | 설명 |
| ----- | ----------- |
| `RazorGenerate` | 코드 생성에 입력되는 항목 요소( *.cshtml* 파일)입니다. |
| `RazorComponent` | Razor 구성 요소 코드 생성에 입력되는 항목 요소( *.razor* 파일)입니다. |
| `RazorCompile` | Razor 컴파일 대상에 입력되는 항목 요소( *.cs* 파일)입니다. 이 `ItemGroup`을 사용하여 Razor 어셈블리에 컴파일할 추가 파일을 지정합니다. |
| `RazorTargetAssemblyAttribute` | 코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다. 예를 들어:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 생성된 Razor 어셈블리에 포함 리소스로 추가된 항목 요소입니다. |

::: moniker range=">= aspnetcore-3.0"

| 속성 | 설명 |
| -------- | ----------- |
| `RazorTargetName` | Razor에서 생성한 어셈블리의 파일 이름(확장명 제외)입니다. |
| `RazorOutputPath` | Razor 출력 디렉터리입니다. |
| `RazorCompileToolset` | Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다. 유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 기본값은 `true`입니다. `true`인 경우 *web.config*, *.json* 및 *.cshtml* 파일을 프로젝트의 콘텐츠로 포함합니다. `Microsoft.NET.Sdk.Web`를 통해 참조되는 경우 *wwwroot* 의 파일 및 구성 파일도 포함됩니다. |
| `EnableDefaultRazorGenerateItems` | `true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다. |
| `GenerateRazorTargetAssemblyInfo` | `true`인 경우, `RazorAssemblyAttribute`로 지정된 특성을 포함하는 *.cs* 파일을 생성하고 이 파일을 컴파일 출력에 포함시킵니다. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다. |
| `CopyRazorGenerateFilesToPublishDirectory` | `true`인 경우, `RazorGenerate` 항목( *.cshtml*) 파일을 게시 디렉터리에 복사합니다. 일반적으로 Razor 파일은 빌드 시간 또는 게시 시간에 컴파일에 참여하는 경우 게시된 앱에 필요하지 않습니다. 기본값은 `false`입니다. |
| `PreserveCompilationReferences` | `true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다. 일반적으로 참조 어셈블리는 Razor 컴파일이 빌드 시간 또는 게시 시간에 발생하는 경우 게시된 앱에 필요하지 않습니다. 게시된 앱에 런타임 컴파일이 필요한 경우 `true`로 설정합니다. 예를 들어, 앱이 런타임에 *.cshtml* 파일을 수정하거나 포함된 뷰를 사용하는 경우 값을 `true`로 설정합니다. 기본값은 `false`입니다. |
| `IncludeRazorContentInPack` | `true`인 경우, 모든 Razor 콘텐츠 항목( *.cshtml* 파일)은 생성된 NuGet 패키지에 포함되도록 표시됩니다. 기본값은 `false`입니다. |
| `EmbedRazorGenerateSources` | `true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml*) 항목을 추가합니다. 기본값은 `false`입니다. |
| `UseRazorBuildServer` | `true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다. 기본값은 `UseSharedCompilation`입니다. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true`인 경우 SDK는 런타임에 MVC에서 애플리케이션 파트 검색을 수행하는 데 사용되는 추가 특성을 생성합니다. |
| `DefaultWebContentItemExcludes` | 웹 또는 Razor SDK를 대상으로 하는 프로젝트의 `Content` 항목 그룹에서 제외될 항목 요소에 대한 globbing 패턴입니다. |
| `ExcludeConfigFilesFromBuildOutput` | `true`인 경우 *.config* 및 *.json* 파일은 빌드 출력 디렉터리에 복사되지 않습니다. |
| `AddRazorSupportForMvc` | `true`인 경우 MVC 뷰나 Razor Pages를 포함하는 애플리케이션을 빌드할 때 필요한 MVC 구성에 대한 지원을 추가하도록 Razor SDK를 구성합니다. 이 속성은 웹 SDK를 대상으로 하는 .NET Core 3.0 이상 프로젝트용으로 암시적으로 설정됩니다. |
| `RazorLangVersion` | 대상으로 지정할 Razor 언어의 버전입니다. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| 속성 | 설명 |
| -------- | ----------- |
| `RazorTargetName` | Razor에서 생성한 어셈블리의 파일 이름(확장명 제외)입니다. |
| `RazorOutputPath` | Razor 출력 디렉터리입니다. |
| `RazorCompileToolset` | Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다. 유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 기본값은 `true`입니다. `true`인 경우 *web.config*, *.json* 및 *.cshtml* 파일을 프로젝트의 콘텐츠로 포함합니다. `Microsoft.NET.Sdk.Web`를 통해 참조되는 경우 *wwwroot* 의 파일 및 구성 파일도 포함됩니다. |
| `EnableDefaultRazorGenerateItems` | `true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다. |
| `GenerateRazorTargetAssemblyInfo` | `true`인 경우, `RazorAssemblyAttribute`로 지정된 특성을 포함하는 *.cs* 파일을 생성하고 이 파일을 컴파일 출력에 포함시킵니다. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다. |
| `CopyRazorGenerateFilesToPublishDirectory` | `true`인 경우, `RazorGenerate` 항목( *.cshtml*) 파일을 게시 디렉터리에 복사합니다. 일반적으로 Razor 파일은 빌드 시간 또는 게시 시간에 컴파일에 참여하는 경우 게시된 앱에 필요하지 않습니다. 기본값은 `false`입니다. |
| `CopyRefAssembliesToPublishDirectory` | `true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다. 일반적으로 참조 어셈블리는 Razor 컴파일이 빌드 시간 또는 게시 시간에 발생하는 경우 게시된 앱에 필요하지 않습니다. 게시된 앱에 런타임 컴파일이 필요한 경우 `true`로 설정합니다. 예를 들어, 앱이 런타임에 *.cshtml* 파일을 수정하거나 포함된 뷰를 사용하는 경우 값을 `true`로 설정합니다. 기본값은 `false`입니다. |
| `IncludeRazorContentInPack` | `true`인 경우, 모든 Razor 콘텐츠 항목( *.cshtml* 파일)은 생성된 NuGet 패키지에 포함되도록 표시됩니다. 기본값은 `false`입니다. |
| `EmbedRazorGenerateSources` | `true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml*) 항목을 추가합니다. 기본값은 `false`입니다. |
| `UseRazorBuildServer` | `true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다. 기본값은 `UseSharedCompilation`입니다. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true`인 경우 SDK는 런타임에 MVC에서 애플리케이션 파트 검색을 수행하는 데 사용되는 추가 특성을 생성합니다. |
| `DefaultWebContentItemExcludes` | 웹 또는 Razor SDK를 대상으로 하는 프로젝트의 `Content` 항목 그룹에서 제외될 항목 요소에 대한 globbing 패턴입니다. |
| `ExcludeConfigFilesFromBuildOutput` | `true`인 경우 *.config* 및 *.json* 파일은 빌드 출력 디렉터리에 복사되지 않습니다. |
| `AddRazorSupportForMvc` | `true`인 경우 MVC 뷰나 Razor Pages를 포함하는 애플리케이션을 빌드할 때 필요한 MVC 구성에 대한 지원을 추가하도록 Razor SDK를 구성합니다. 이 속성은 웹 SDK를 대상으로 하는 .NET Core 3.0 이상 프로젝트용으로 암시적으로 설정됩니다. |
| `RazorLangVersion` | 대상으로 지정할 Razor 언어의 버전입니다. |

::: moniker-end

속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.

### <a name="targets"></a>대상

Razor SDK는 두 기본 대상을 정의합니다.

* `RazorGenerate`: 코드는 `RazorGenerate` 항목 요소에서 *.cs* 파일을 생성합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorGenerateDependsOn` 속성을 사용합니다.
* `RazorCompile`: 생성된 *.cs* 파일을 Razor 어셈블리로 컴파일합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorCompileDependsOn`을 사용합니다.
* `RazorComponentGenerate`: 코드는 `RazorComponent` 항목 요소에 대한 *.cs* 파일을 생성합니다. 이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorComponentGenerateDependsOn` 속성을 사용합니다.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Razor 뷰의 런타임 컴파일

* 기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다. 이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함). `CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.

* 웹앱의 경우 앱이 `Microsoft.NET.Sdk.Web` SDK를 대상으로 지정하도록 합니다.

## <a name="no-locrazor-language-version"></a>Razor 언어 버전

`Microsoft.NET.Sdk.Web` SDK를 대상으로 지정하는 경우 앱의 대상 프레임워크 버전에서 Razor 언어 버전이 유추됩니다. `Microsoft.NET.Sdk.Razor` SDK를 대상으로 하는 프로젝트 또는 앱이 유추된 값과는 다른 Razor 언어 버전이 필요한 드문 경우에 앱의 프로젝트 파일에서 `<RazorLangVersion>` 속성을 설정하여 버전을 구성할 수 있습니다.

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor의 언어 버전은 빌드된 런타임 버전과 긴밀하게 통합됩니다. 런타임용으로 디자인된 언어 버전을 대상으로 하는 것은 지원되지 않으며 빌드 오류가 발생할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* [.NET Core용 csproj 형식에 대한 추가 사항](/dotnet/core/tools/csproj)
* [일반적인 MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)
