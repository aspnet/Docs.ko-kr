---
title: 웹 API 분석기 사용
author: pranavkm
description: ASP.NET Core MVC 웹 API 분석기 패키지에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057923"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="44e84-103">웹 API 분석기 사용</span><span class="sxs-lookup"><span data-stu-id="44e84-103">Use web API analyzers</span></span>

<span data-ttu-id="44e84-104">ASP.NET Core 2.2 이상에서는 웹 API 프로젝트와 함께 사용하기 위한 MVC 분석기 패키지를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="44e84-105">분석기는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러와 함께 작동하지만 [웹 API 규칙](xref:web-api/advanced/conventions)으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="44e84-106">분석기 패키지는 다음을 수행하는 컨트롤러 작업을 모두 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="44e84-107">선언되지 않은 상태 코드를 반환</span><span class="sxs-lookup"><span data-stu-id="44e84-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="44e84-108">선언되지 않은 성공 결과를 반환</span><span class="sxs-lookup"><span data-stu-id="44e84-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="44e84-109">반환되지 않은 상태 코드를 문서화</span><span class="sxs-lookup"><span data-stu-id="44e84-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="44e84-110">명시적 모델 유효성 검사를 포함</span><span class="sxs-lookup"><span data-stu-id="44e84-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="44e84-111">분석기 패키지 참조</span><span class="sxs-lookup"><span data-stu-id="44e84-111">Reference the analyzer package</span></span>

<span data-ttu-id="44e84-112">ASP.NET Core 3.0 이상에서 분석기는 .NET Core SDK에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="44e84-113">프로젝트에서 분석기를 사용하도록 설정하려면 프로젝트 파일에 `IncludeOpenAPIAnalyzers` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="44e84-114">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="44e84-114">Package installation</span></span>

<span data-ttu-id="44e84-115">다음 방법 중 하나를 사용하여 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="44e84-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44e84-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44e84-117">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="44e84-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="44e84-118">**View** > **다른 Windows** > **패키지 관리자 콘솔** 보기로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-118">Go to **View** > **Other Windows** > **Package Manager Console** .</span></span>
  * <span data-ttu-id="44e84-119">*ApiConventions.csproj* 파일이 위치한 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="44e84-120">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44e84-121">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44e84-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44e84-122">*Packages* **Solution Pad** > **패키지 추가** Solution Pad에서 패키지 폴더를 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...** .</span></span>
* <span data-ttu-id="44e84-123">**패키지 추가** 창의 **원본** 드롭다운을 "nuget.org"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="44e84-124">검색 상자에 "Microsoft.AspNetCore.Mvc.Api.Analyzers"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="44e84-125">결과 창에서 "Microsoft.AspNetCore.Mvc.Api.Analyzers" 패키지를 선택하고 **패키지 추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package** .</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="44e84-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44e84-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44e84-127">**통합 터미널** 에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-127">Run the following command from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="44e84-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="44e84-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="44e84-129">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="44e84-130">웹 API 규칙용 분석기</span><span class="sxs-lookup"><span data-stu-id="44e84-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="44e84-131">OpenAPI 문서에는 작업이 반환할 수 있는 상태 코드 및 응답 형식이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="44e84-132">ASP.NET Core MVC에서는 <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 및 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 와 같은 특성을 사용하여 작업을 문서화합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="44e84-133"><xref:tutorials/web-api-help-pages-using-swagger>는 웹 API 문서화에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="44e84-134">패키지의 분석기 중 하나는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러를 검사하고 응답을 완전히 문서화하지 않은 작업을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="44e84-135">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e84-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="44e84-136">앞의 작업은 HTTP 200 성공 반환 유형을 문서화하지만 HTTP 404 실패 상태 코드는 문서화하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="44e84-137">분석기는 HTTP 404 상태 코드에 대한 누락된 설명서를 경고로 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="44e84-138">문제를 해결하기 위한 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e84-138">An option to fix the problem is provided.</span></span>

![경고를 보고하는 분석기](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="44e84-140">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="44e84-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
