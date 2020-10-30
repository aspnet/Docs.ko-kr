---
title: Razor ASP.NET Core에서 파일 컴파일
author: rick-anderson
description: ASP.NET Core 앱에서 파일 컴파일이 발생 하는 방법에 대해 알아봅니다 Razor .
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059080"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor ASP.NET Core에서 파일 컴파일

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor확장명이 *cshtml* 인 파일은 [ Razor SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간에 모두 컴파일됩니다. 프로젝트를 구성 하 여 런타임 컴파일을 선택적으로 사용 하도록 설정할 수 있습니다.

## <a name="no-locrazor-compilation"></a>Razor 컴파일과

파일의 빌드 시간 및 게시 타임 컴파일은 Razor SDK에서 기본적으로 사용 하도록 설정 됩니다 Razor . 사용 하도록 설정 하면 런타임 컴파일이 빌드 시간 컴파일을 보완 하 여 Razor 파일이 편집 될 때 파일이 업데이트 될 수 있도록 합니다.

## <a name="enable-runtime-compilation-at-project-creation"></a>프로젝트를 만들 때 런타임 컴파일 사용

Razor페이지 및 MVC 프로젝트 템플릿에는 프로젝트를 만들 때 런타임 컴파일을 사용 하도록 설정 하는 옵션이 포함 되어 있습니다. 이 옵션은 ASP.NET Core 3.1 이상에서 지원 됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**새 ASP.NET Core 웹 응용 프로그램 만들기** 대화 상자에서 다음을 수행 합니다.

1. **웹 응용 프로그램** 또는 **웹 응용 프로그램 (모델-뷰-컨트롤러)** 프로젝트 템플릿을 선택 합니다.
1. **Razor 런타임 컴파일 사용** 확인란을 선택 합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

`-rrc`또는 `--razor-runtime-compilation` 템플릿 옵션을 사용 합니다. 예를 들어 다음 명령은 Razor 런타임 컴파일이 설정 된 새 페이지 프로젝트를 만듭니다.

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>기존 프로젝트에서 런타임 컴파일 사용

기존 프로젝트의 모든 환경에 대해 런타임 컴파일을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. AspNetCore를 설치 합니다. [ Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지입니다.
1. <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다. 다음은 그 예입니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>기존 프로젝트에서 조건부로 런타임 컴파일 사용

런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다. 이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.

* 컴파일된 보기를 사용합니다.
* 프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.

개발 환경 에서만 런타임 컴파일을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. AspNetCore를 설치 합니다. [ Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지입니다.
1. launchSettings.js에서 시작 프로필 `environmentVariables` 섹션을 *launchSettings.json* 수정 합니다.
    * `ASPNETCORE_ENVIRONMENT`가로 설정 되어 있는지 확인 `"Development"` 합니다.
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`를 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`로 설정합니다.

다음 예제에서는 `IIS Express` 및 시작 프로필에 대 한 개발 환경에서 런타임 컴파일을 사용 하도록 설정 합니다 `RazorPagesApp` .

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

프로젝트의 클래스에는 코드를 변경할 필요가 없습니다 `Startup` . 런타임에는 ASP.NET Core에서 [어셈블리 수준 HostingStartup 특성](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) 을 검색 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 합니다. `HostingStartup`특성은 실행할 앱 시작 코드를 지정 합니다. 이 시작 코드는 런타임 컴파일을 사용 하도록 설정 합니다.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>클래스 라이브러리에 대해 런타임 컴파일 사용 Razor

RazorPages 프로젝트가 이름이 *MyClassLib* 인 [ Razor 클래스 라이브러리 (rcl)](xref:razor-pages/ui-class) 를 참조 하는 시나리오를 고려해 보세요. RCL에는 모든 팀의 MVC 및 페이지 프로젝트에서 사용 하는 *_Layout cshtml* 파일이 포함 되어 있습니다 Razor . RCL에서 *_Layout. cshtml* 파일에 대해 런타임 컴파일을 사용 하도록 설정 하려고 합니다. Pages 프로젝트에서 다음과 같이 변경 합니다 Razor .

1. [기존 프로젝트에서 조건부로 런타임 컴파일을 사용 하도록 설정](#conditionally-enable-runtime-compilation-in-an-existing-project)하는 지침을 사용 하 여 런타임 컴파일을 사용 하도록 설정 합니다.
1. 에서 런타임 컴파일 옵션을 구성 합니다 `Startup.ConfigureServices` .

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    위의 코드에서는 *MyClassLib* rcl에 대 한 절대 경로가 생성 됩니다. 실제 파일 [공급자 API](xref:fundamentals/file-providers#physicalfileprovider) 는 해당 절대 경로에서 디렉터리와 파일을 찾는 데 사용 됩니다. 마지막으로 `PhysicalFileProvider` 인스턴스는 RCL의 *cshtml* 파일에 액세스할 수 있도록 하는 파일 공급자 컬렉션에 추가 됩니다.

## <a name="additional-resources"></a>추가 리소스

* [ Razor CompileOnBuild 및 Razor CompileOnPublish](xref:razor-pages/sdk#properties) 속성
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor확장명이 *cshtml* 인 파일은 [ Razor SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간에 모두 컴파일됩니다. 애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.

## <a name="no-locrazor-compilation"></a>Razor 컴파일과

파일의 빌드 시간 및 게시 타임 컴파일은 Razor SDK에서 기본적으로 사용 하도록 설정 됩니다 Razor . 사용 하도록 설정 하면 런타임 컴파일이 빌드 시간 컴파일을 보완 하 여 Razor 파일이 편집 될 때 파일이 업데이트 될 수 있도록 합니다.

## <a name="runtime-compilation"></a>런타임 컴파일

모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.

1. AspNetCore를 설치 합니다. [ Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지입니다.

1. <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다. 다음은 그 예입니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>조건부로 런타임 컴파일을 사용하도록 설정

런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다. 이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.

* 컴파일된 보기를 사용합니다.
* 크기가 더 작습니다.
* 프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.

환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.

1. 조건부로 참조 [AspNetCore Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 활성 값을 기반으로 하는 RuntimeCompilation 패키지 `Configuration` :

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. `AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다. `ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `AddRazorRuntimeCompilation`을 실행합니다.

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>추가 리소스

* [ Razor CompileOnBuild 및 Razor CompileOnPublish](xref:razor-pages/sdk#properties) 속성
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* 프로젝트에서 런타임 컴파일을 수행 하는 방법을 보여 주는 샘플은 [GitHub의 런타임 컴파일 샘플](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) 을 참조 하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor파일은 연결 된 Razor 페이지 또는 MVC 뷰가 호출 될 때 런타임에 컴파일됩니다. Razor파일은 [ Razor SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간 모두에 컴파일됩니다.

## <a name="no-locrazor-compilation"></a>Razor 컴파일과

파일의 빌드 및 게시 타임 컴파일은 Razor SDK에서 기본적으로 사용 하도록 설정 됩니다 Razor . Razor파일이 업데이트 된 후의 편집은 빌드할 때 지원 됩니다. 기본적으로 컴파일된 *Views.dll* 와 파일을 컴파일하는 *.cshtml* 데 필요한 참조 어셈블리는 Razor 응용 프로그램과 함께 배포 됩니다.

> [!IMPORTANT]
> 사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다. [ Razor Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.
>
> RazorSDK는 프로젝트 파일에 미리 컴파일 관련 속성이 설정 되지 않은 경우에만 적용 됩니다. 예를 들어 *.csproj* 파일의 속성을로 설정 하면 `MvcRazorCompileOnPublish` `true` SDK가 사용 되지 않습니다 Razor .

## <a name="runtime-compilation"></a>런타임 컴파일

빌드 타임 컴파일은 파일의 런타임 컴파일을 통해 보완 됩니다 Razor . ASP.NET Core MVC는 Razor *cshtml* 파일의 내용이 변경 될 때 파일을 다시 컴파일합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
