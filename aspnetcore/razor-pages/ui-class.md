---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: ASP.NET Core에서 클래스 라이브러리의 부분 뷰를 사용하여 재사용 가능한 Razor UI를 만드는 방법을 설명합니다.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: e87e74533fe6900d8e0a73708ad24b765a968493
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056805"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용하여 재사용 가능한 UI 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

RCL(Razor 클래스 라이브러리)에 Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [Razor 구성 요소](xref:blazor/components/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 기본적으로 빌드할 수 있습니다. RCL은 패키지되고 재사용될 수 있습니다. 애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다. 뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Razor UI를 포함하는 클래스 라이브러리 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.
* **Razor 클래스 라이브러리** > **다음** 을 선택합니다.
* 라이브러리 이름을 지정하고(예: “RazorClassLib”), **만들기** 를 선택합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.
* 뷰를 지원해야 하는 경우 **페이지 및 뷰 지원** 을 선택합니다. 기본적으로 Razor Pages만 지원됩니다. **만들기** 를 선택합니다.

RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다. **페이지 및 뷰 지원** 옵션은 페이지와 뷰를 지원합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 `dotnet new razorclasslib`을 실행합니다. 예를 들어:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다. 페이지 및 뷰 지원을 제공하려면 `--support-pages-and-views` 옵션을 전달합니다(`dotnet new razorclasslib --support-pages-and-views`).

자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.

---

RCL에 Razor 파일을 추가합니다.

ASP.NET Core 템플릿은 RCL 콘텐츠가 *Areas* 폴더에 있다고 가정합니다. `~/Areas/Pages`가 아닌 `~/Pages`의 콘텐츠를 공개하는 RCL을 만들려면 [RCL 페이지 레이아웃](#rcl-pages-layout)을 참조하세요.

## <a name="reference-rcl-content"></a>RCL 콘텐츠 참조

RCL은 다음에서 참조할 수 있습니다.

* NuGet 패키지. [NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.
* *{ProjectName}.csproj*. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.

## <a name="override-views-partial-views-and-pages"></a>보기, 부분 보기 및 페이지 재정의

뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다. 예를 들어 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 을 WebApp1에 추가하면, WebApp1의 Page1이 RCL의 Page1보다 우선 적용됩니다.

샘플 다운로드에서 *WebApp1/Areas/MyFeature2* 를 *WebApp1/Areas/MyFeature* 로 이름을 바꾸어 우선적으로 테스트합니다.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 뷰를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에 복사합니다. 새 위치를 나타내기 위해 태그를 업데이트합니다. 해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.

### <a name="rcl-pages-layout"></a>RCL 페이지 레이아웃

웹앱의 *Pages* 폴더에 있는 것처럼 RCL 콘텐츠를 참조하려면 다음 파일 구조로 이루어진 RCL 프로젝트를 만듭니다.

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

*RazorUIClassLib/Pages/Shared* 에 *_Header.cshtml* 및 *_Footer.cshtml* 이라는 두 개의 부분 파일이 들어 있다고 가정합니다. *_Layout.cshtml* 파일에 `<partial>` 태그를 추가할 수 있습니다.

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>정적 자산을 사용하여 RCL 만들기

RCL 또는 RCL의 사용 앱에서 참조할 수 있는 도우미 정적 자산이 RCL에 필요할 수도 있습니다. ASP.NET Core에서는 사용 앱이 사용할 수 있는 정적 자산을 포함하는 RCL을 만들 수 있습니다.

도우미 자산을 RCL의 일부로 포함하려면 클래스 라이브러리에 *wwwroot* 폴더를 만들고 필요한 모든 파일을 해당 폴더에 포함합니다.

RCL을 패키지하면 *wwwroot* 폴더에 있는 모든 도우미 자산이 패키지에 자동으로 포함됩니다.

NuGet.exe 버전 `nuget pack` 대신 `dotnet pack` 명령을 사용합니다.

### <a name="exclude-static-assets"></a>정적 자산 제외

정적 자산을 제외하려면 프로젝트 파일의 `$(DefaultItemExcludes)` 속성 그룹에 원하는 제외 경로를 추가합니다. 항목을 세미콜론(`;`)으로 구분합니다.

다음 예제에서 *wwwroot* 폴더에 있는 *lib.css* 스타일시트는 정적 자산으로 간주되지 않아 게시된 RCL에 포함되지 않습니다.

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>TypeScript 통합

RCL에 TypeScript 파일을 포함하려면 다음을 수행합니다.

1. *wwwroot* 폴더 외부에 TypeScript 파일( *.ts* )을 배치합니다. 예를 들어 *Client* 폴더에 파일을 배치합니다.

1. *wwwroot* 폴더에 대한 TypeScript 빌드 출력을 구성합니다. 프로젝트 파일의 `PropertyGroup` 내부에 `TypescriptOutDir` 속성을 설정합니다.

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. 프로젝트 파일의 `PropertyGroup` 내부에 다음 대상을 추가하여 TypeScript 대상을 `ResolveCurrentProjectStaticWebAssets` 대상의 종속성으로 포함합니다.

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>참조된 RCL의 콘텐츠 사용

RCL의 *wwwroot* 폴더에 포함된 파일은 접두사 `_content/{LIBRARY NAME}/` 아래에서 RCL 또는 사용 앱에 공개됩니다. 예를 들어 *Razor.Class.Lib* 라는 라이브러리의 정적 콘텐츠 경로는 `_content/Razor.Class.Lib/`가 됩니다. NuGet 패키지를 생성할 때 어셈블리 이름이 패키지 ID와 다른 경우 `{LIBRARY NAME}`에 패키지 ID를 사용합니다.

사용 앱은 `<script>`, `<style>`, `<img>` 및 기타 HTML 태그를 사용하여 라이브러리에서 제공하는 정적 자산을 참조합니다. `Startup.Configure`에서 사용 앱에 대해 [정적 파일 지원](xref:fundamentals/static-files)이 사용하도록 설정되어 있어야 합니다.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

빌드 출력(`dotnet run`)에서 사용 앱을 실행하는 경우, 개발 환경에서는 정적 웹 자산이 기본적으로 사용됩니다. 빌드 출력에서 실행할 때 다른 환경의 자산을 지원하려면 호스트 작성기의 *Program.cs* 에서 `UseStaticWebAssets`를 호출합니다.

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

게시된 출력(`dotnet publish`)에서 앱을 실행하는 경우 `UseStaticWebAssets`를 호출할 필요가 없습니다.

### <a name="multi-project-development-flow"></a>다중 프로젝트 개발 흐름

사용 앱이 실행되는 경우와 관련해서 다음 사항을 확인합니다.

* RCL의 자산은 원래 폴더에 유지됩니다. 자산이 사용 앱으로 이동되지 않습니다.
* RCL *wwwroot* 폴더 내의 모든 변경 내용은 RCL을 다시 빌드한 후 사용 앱에 반영됩니다. 사용 앱을 다시 빌드할 필요는 없습니다.

RCL을 빌드하면 정적 웹 자산 위치를 설명하는 매니페스트가 생성됩니다. 사용 앱은 런타임에 매니페스트를 읽어 참조된 프로젝트와 패키지의 자산을 사용합니다. RCL에 새 자산이 추가된 경우 RCL을 다시 빌드해서 매니페스트를 업데이트해야 사용 앱이 새 자산에 액세스할 수 있습니다.

### <a name="publish"></a>게시

앱을 게시하면 참조된 모든 프로젝트와 패키지의 도우미 자산이 `_content/{LIBRARY NAME}/` 아래에 있는 게시된 앱의 *wwwroot* 폴더에 복사됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RCL(Razor 클래스 라이브러리)에 Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [Razor 구성 요소](xref:blazor/components/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 기본적으로 빌드할 수 있습니다. RCL은 패키지되고 재사용될 수 있습니다. 애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다. 뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Razor UI를 포함하는 클래스 라이브러리 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.
* 라이브러리 이름 지정을 지정하고(예: “RazorClassLib”), **확인** 을 선택합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인** 을 선택합니다.

RCL에는 다음과 같은 프로젝트 파일이 있습니다.

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 `dotnet new razorclasslib`을 실행합니다. 예를 들어:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다. 생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.

---

RCL에 Razor 파일을 추가합니다.

ASP.NET Core 템플릿은 RCL 콘텐츠가 *Areas* 폴더에 있다고 가정합니다. `~/Areas/Pages`가 아닌 `~/Pages`의 콘텐츠를 공개하는 RCL을 만들려면 [RCL 페이지 레이아웃](#rcl-pages-layout)을 참조하세요.

## <a name="reference-rcl-content"></a>RCL 콘텐츠 참조

RCL은 다음에서 참조할 수 있습니다.

* NuGet 패키지. [NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.
* *{ProjectName}.csproj*. [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>연습: RCL 프로젝트를 만들고 Razor Pages 프로젝트에서 사용

[전체 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다. 샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다. 샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.

### <a name="test-the-download-app"></a>다운로드 앱 테스트

완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-an-rcl)으로 건너 뜁니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 *.sln* 파일을 엽니다. 앱을 실행합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.

```dotnetcli
dotnet build
```

*WebApp1* 디렉터리로 이동해 앱을 실행합니다.

```dotnetcli
dotnet run
```

---

[WebApp1 테스트](#test-webapp1)의 지침 준수

## <a name="create-an-rcl"></a>RCL 만들기

이 섹션에서는 RCL을 만듭니다. Razor 파일이 RCL에 추가됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL 프로젝트를 만듭니다.

* Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.
* 앱 이름을 **RazorUIClassLib** 로 지정하고, **확인** 을 선택합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **Razor 클래스 라이브러리** > **확인** 을 선택합니다.
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 이라는 Razor 부분 뷰 파일을 추가합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령줄에서 다음을 실행하세요.

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

이전 명령은

* `RazorUIClassLib` RCL을 만듭니다.
* Razor _Message 페이지를 만들어 RCL에 추가합니다. `-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.
* [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일을 만들어 RCL에 추가합니다.

다음 섹션에서 추가하는 Razor Pages 프로젝트의 레이아웃을 사용하려면 *_ViewStart.cshtml* 파일이 필요합니다.

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>프로젝트에 Razor 파일 및 폴더 추가

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에서 태그를 다음 코드로 바꿉니다.

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* 에서 태그를 다음 코드로 바꿉니다.

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다. `@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다. 예를 들어:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  *_ViewImports.cshtml* 에 대한 자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.

* 컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll* 을 포함합니다. *RazorUIClassLib.Views.dll* 은 컴파일된 Razor 콘텐츠를 포함합니다.

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>Razor Pages 프로젝트에서 Razor UI 라이브러리 사용

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages 웹앱을 만듭니다.

* **솔루션 탐색기** 에서 솔루션을 마우스 오른쪽 단추로 클릭하고 > **추가** > **새 프로젝트** 를 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.
* **WebApp1** 앱 이름을 지정합니다.
* **ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.
* **웹 애플리케이션** > **확인** 을 선택합니다.

* **솔루션 탐색기** 에서 **WebApp1** 를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정** 을 선택합니다.
* **솔루션 탐색기** 에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭하고 **빌드 종속성** > **프로젝트 종속성** 을 선택합니다.
* **RazorUIClassLib** 를 **WebApp1** 의 종속성으로 확인합니다.
* **솔루션 탐색기** 에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭하고 **추가** > **참조** 를 선택합니다.
* **참조 관리자** 대화 상자에서 **RazorUIClassLib** > **확인** 을 선택합니다.

앱을 실행합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor Pages 앱과 RCL을 포함하는 Razor Pages 웹앱 및 솔루션 파일을 만듭니다.

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

웹앱을 빌드하고 실행합니다.

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>WebApp1 테스트

`/MyFeature/Page1`로 이동하여 Razor UI 클래스 라이브러리가 사용 중인지 확인합니다.

## <a name="override-views-partial-views-and-pages"></a>보기, 부분 보기 및 페이지 재정의

뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다. 예를 들어 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 을 WebApp1에 추가하면, WebApp1의 Page1이 RCL의 Page1보다 우선 적용됩니다.

샘플 다운로드에서 *WebApp1/Areas/MyFeature2* 를 *WebApp1/Areas/MyFeature* 로 이름을 바꾸어 우선적으로 테스트합니다.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 뷰를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에 복사합니다. 새 위치를 나타내기 위해 태그를 업데이트합니다. 해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.

### <a name="rcl-pages-layout"></a>RCL 페이지 레이아웃

웹앱의 *Pages* 폴더에 있는 것처럼 RCL 콘텐츠를 참조하려면 다음 파일 구조로 이루어진 RCL 프로젝트를 만듭니다.

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

*RazorUIClassLib/Pages/Shared* 에 *_Header.cshtml* 및 *_Footer.cshtml* 이라는 두 개의 부분 파일이 들어 있다고 가정합니다. *_Layout.cshtml* 파일에 `<partial>` 태그를 추가할 수 있습니다.

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/components/class-libraries>
