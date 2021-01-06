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
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a>ASP.NET Core Blazor용 링커 구성

[Luke Latham](https://github.com/guardrex)으로

Blazor WebAssembly는 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 잘라냅니다. 디버그 구성에서 빌드할 때 링커를 사용할 수 없습니다. 링커를 사용하도록 설정하려면 앱이 릴리스 구성으로 빌드해야 합니다. Blazor WebAssembly 앱을 배포할 때 릴리스에서 빌드하는 것이 좋습니다. 

앱 연결은 크기에 맞게 최적화되지만 부작용이 발생할 수 있습니다. 링커는 이 동적 동작에 대한 정보를 알지 못하고 일반적으로 런타임에서 리플렉션에 필요한 형식을 결정할 수 없기 때문에, 리플렉션 또는 관련 동적 기능을 사용하는 앱은 잘린 경우 중단될 수 있습니다. 이러한 앱을 잘라내려면 링커에서는 앱이 의존하는 코드 및 패키지 또는 프레임워크의 리플렉션에서 필요로 하는 형식에 대해 알고 있어야 합니다.

잘린 앱이 배포된 후 올바로 작동하도록 하려면 개발하는 동안 앱의 릴리스 빌드를 자주 테스트하는 것이 중요합니다.

다음 MSBuild 기능을 사용하여 Blazor 앱에 대한 연결을 구성할 수 있습니다.

* [MSBuild 속성](#control-linking-with-an-msbuild-property)을 사용하여 전역적으로 연결을 구성합니다.
* [구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.

## <a name="control-linking-with-an-msbuild-property"></a>MSBuild 속성을 사용하여 연결 제어

앱이 `Release` 구성에서 빌드될 때 연결이 설정됩니다. 이를 변경하려면 프로젝트 파일에서 `BlazorWebAssemblyEnableLinking` MSBuild 속성을 구성합니다.

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>구성 파일과의 연결 제어

XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

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

자세한 내용 및 예제는 [데이터 형식](https://github.com/mono/linker/blob/master/docs/data-formats.md)(mono/linker GitHub 리포지토리)을 참조하세요.

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>라이브러리에 XML 링커 구성 파일 추가

특정 라이브러리의 링커를 구성하려면 XML 링커 구성 파일을 포함 리소스로 라이브러리에 추가합니다. 포함 리소스의 이름은 어셈블리와 동일해야 합니다.

다음 예제에서 `LinkerConfig.xml` 파일은 라이브러리의 어셈블리와 이름이 동일한 포함 리소스로 지정됩니다.

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>국제화를 위한 링커 구성

기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다. 이 어셈블리를 제거하면 앱 크기를 최소화합니다.

유지되는 I18N 어셈블리 종류를 제어하려면 프로젝트 파일에서 `<BlazorWebAssemblyI18NAssemblies>` MSBuild 속성을 설정합니다.

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| 지역 값     | Mono 지역 어셈블리    |
| ---------------- | ----------------------- |
| `all`            | 포함된 모든 어셈블리 |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none`(기본값) | 없음                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

여러 값을 지정할 경우 쉼표를 사용하여 구분합니다(예: `mideast,west`).

자세한 내용은 [I18N: Pnetlib 국제화 프레임워크 라이브러리(mono/mono GitHub 리포지토리)](https://github.com/mono/mono/tree/master/mcs/class/I18N)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
