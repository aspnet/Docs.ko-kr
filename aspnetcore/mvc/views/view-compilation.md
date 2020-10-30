---
title: ':::no-loc(Razor)::: ASP.NET Core에서 파일 컴파일'
author: rick-anderson
description: 'ASP.NET Core 앱에서 파일 컴파일이 발생 하는 방법에 대해 알아봅니다 :::no-loc(Razor)::: .'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059080"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="aacdd-103">:::no-loc(Razor)::: ASP.NET Core에서 파일 컴파일</span><span class="sxs-lookup"><span data-stu-id="aacdd-103">:::no-loc(Razor)::: file compilation in ASP.NET Core</span></span>

<span data-ttu-id="aacdd-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aacdd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="aacdd-105">:::no-loc(Razor):::확장명이 *cshtml* 인 파일은 [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간에 모두 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-105">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="aacdd-106">프로젝트를 구성 하 여 런타임 컴파일을 선택적으로 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="aacdd-107">:::no-loc(Razor)::: 컴파일과</span><span class="sxs-lookup"><span data-stu-id="aacdd-107">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="aacdd-108">파일의 빌드 시간 및 게시 타임 컴파일은 :::no-loc(Razor)::: SDK에서 기본적으로 사용 하도록 설정 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-108">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="aacdd-109">사용 하도록 설정 하면 런타임 컴파일이 빌드 시간 컴파일을 보완 하 여 :::no-loc(Razor)::: 파일이 편집 될 때 파일이 업데이트 될 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-109">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="aacdd-110">프로젝트를 만들 때 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="aacdd-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="aacdd-111">:::no-loc(Razor):::페이지 및 MVC 프로젝트 템플릿에는 프로젝트를 만들 때 런타임 컴파일을 사용 하도록 설정 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-111">The :::no-loc(Razor)::: Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="aacdd-112">이 옵션은 ASP.NET Core 3.1 이상에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aacdd-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aacdd-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aacdd-114">**새 ASP.NET Core 웹 응용 프로그램 만들기** 대화 상자에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="aacdd-115">**웹 응용 프로그램** 또는 **웹 응용 프로그램 (모델-뷰-컨트롤러)** 프로젝트 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="aacdd-116">**:::no-loc(Razor)::: 런타임 컴파일 사용** 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-116">Select the **Enable :::no-loc(Razor)::: runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aacdd-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="aacdd-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aacdd-118">`-rrc`또는 `--razor-runtime-compilation` 템플릿 옵션을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="aacdd-119">예를 들어 다음 명령은 :::no-loc(Razor)::: 런타임 컴파일이 설정 된 새 페이지 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-119">For example, the following command creates a new :::no-loc(Razor)::: Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="aacdd-120">기존 프로젝트에서 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="aacdd-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="aacdd-121">기존 프로젝트의 모든 환경에 대해 런타임 컴파일을 사용 하도록 설정 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="aacdd-122">AspNetCore를 설치 합니다. [ :::no-loc(Razor)::: RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-122">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="aacdd-123"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="aacdd-124">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="aacdd-125">기존 프로젝트에서 조건부로 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="aacdd-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="aacdd-126">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="aacdd-127">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="aacdd-128">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-128">Uses compiled views.</span></span>
* <span data-ttu-id="aacdd-129">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="aacdd-130">개발 환경 에서만 런타임 컴파일을 사용 하도록 설정 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="aacdd-131">AspNetCore를 설치 합니다. [ :::no-loc(Razor)::: RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-131">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="aacdd-132">launchSettings.js에서 시작 프로필 `environmentVariables` 섹션을 *launchSettings.json* 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-132">Modify the launch profile `environmentVariables` section in *launchSettings.json* :</span></span>
    * <span data-ttu-id="aacdd-133">`ASPNETCORE_ENVIRONMENT`가로 설정 되어 있는지 확인 `"Development"` 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="aacdd-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`를 `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span></span>

<span data-ttu-id="aacdd-135">다음 예제에서는 `IIS Express` 및 시작 프로필에 대 한 개발 환경에서 런타임 컴파일을 사용 하도록 설정 합니다 `:::no-loc(Razor):::PagesApp` .</span><span class="sxs-lookup"><span data-stu-id="aacdd-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `:::no-loc(Razor):::PagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="aacdd-136">프로젝트의 클래스에는 코드를 변경할 필요가 없습니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="aacdd-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="aacdd-137">런타임에는 ASP.NET Core에서 [어셈블리 수준 HostingStartup 특성](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) 을 검색 `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation` 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`.</span></span> <span data-ttu-id="aacdd-138">`HostingStartup`특성은 실행할 앱 시작 코드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="aacdd-139">이 시작 코드는 런타임 컴파일을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="aacdd-140">클래스 라이브러리에 대해 런타임 컴파일 사용 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="aacdd-140">Enable runtime compilation for a :::no-loc(Razor)::: Class Library</span></span>

<span data-ttu-id="aacdd-141">:::no-loc(Razor):::Pages 프로젝트가 이름이 *MyClassLib* 인 [ :::no-loc(Razor)::: 클래스 라이브러리 (rcl)](xref:razor-pages/ui-class) 를 참조 하는 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="aacdd-141">Consider a scenario in which a :::no-loc(Razor)::: Pages project references a [:::no-loc(Razor)::: Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib* .</span></span> <span data-ttu-id="aacdd-142">RCL에는 모든 팀의 MVC 및 페이지 프로젝트에서 사용 하는 *_Layout cshtml* 파일이 포함 되어 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and :::no-loc(Razor)::: Pages projects consume.</span></span> <span data-ttu-id="aacdd-143">RCL에서 *_Layout. cshtml* 파일에 대해 런타임 컴파일을 사용 하도록 설정 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="aacdd-144">Pages 프로젝트에서 다음과 같이 변경 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-144">Make the following changes in the :::no-loc(Razor)::: Pages project:</span></span>

1. <span data-ttu-id="aacdd-145">[기존 프로젝트에서 조건부로 런타임 컴파일을 사용 하도록 설정](#conditionally-enable-runtime-compilation-in-an-existing-project)하는 지침을 사용 하 여 런타임 컴파일을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="aacdd-146">에서 런타임 컴파일 옵션을 구성 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aacdd-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="aacdd-147">위의 코드에서는 *MyClassLib* rcl에 대 한 절대 경로가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="aacdd-148">실제 파일 [공급자 API](xref:fundamentals/file-providers#physicalfileprovider) 는 해당 절대 경로에서 디렉터리와 파일을 찾는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="aacdd-149">마지막으로 `PhysicalFileProvider` 인스턴스는 RCL의 *cshtml* 파일에 액세스할 수 있도록 하는 파일 공급자 컬렉션에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aacdd-150">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="aacdd-150">Additional resources</span></span>

* <span data-ttu-id="aacdd-151">[ :::no-loc(Razor)::: CompileOnBuild 및 :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) 속성</span><span class="sxs-lookup"><span data-stu-id="aacdd-151">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="aacdd-152">:::no-loc(Razor):::확장명이 *cshtml* 인 파일은 [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간에 모두 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-152">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="aacdd-153">애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="aacdd-154">:::no-loc(Razor)::: 컴파일과</span><span class="sxs-lookup"><span data-stu-id="aacdd-154">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="aacdd-155">파일의 빌드 시간 및 게시 타임 컴파일은 :::no-loc(Razor)::: SDK에서 기본적으로 사용 하도록 설정 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-155">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="aacdd-156">사용 하도록 설정 하면 런타임 컴파일이 빌드 시간 컴파일을 보완 하 여 :::no-loc(Razor)::: 파일이 편집 될 때 파일이 업데이트 될 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-156">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="aacdd-157">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="aacdd-157">Runtime compilation</span></span>

<span data-ttu-id="aacdd-158">모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="aacdd-159">AspNetCore를 설치 합니다. [ :::no-loc(Razor)::: RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-159">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="aacdd-160"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="aacdd-161">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="aacdd-162">조건부로 런타임 컴파일을 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="aacdd-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="aacdd-163">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="aacdd-164">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="aacdd-165">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-165">Uses compiled views.</span></span>
* <span data-ttu-id="aacdd-166">크기가 더 작습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-166">Is smaller in size.</span></span>
* <span data-ttu-id="aacdd-167">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="aacdd-168">환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="aacdd-169">조건부로 참조 [AspNetCore :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) 활성 값을 기반으로 하는 RuntimeCompilation 패키지 `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="aacdd-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="aacdd-170">`Add:::no-loc(Razor):::RuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-170">Update the project's `Startup.ConfigureServices` method to include a call to `Add:::no-loc(Razor):::RuntimeCompilation`.</span></span> <span data-ttu-id="aacdd-171">`ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `Add:::no-loc(Razor):::RuntimeCompilation`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-171">Conditionally execute `Add:::no-loc(Razor):::RuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="aacdd-172">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="aacdd-172">Additional resources</span></span>

* <span data-ttu-id="aacdd-173">[ :::no-loc(Razor)::: CompileOnBuild 및 :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) 속성</span><span class="sxs-lookup"><span data-stu-id="aacdd-173">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="aacdd-174">프로젝트에서 런타임 컴파일을 수행 하는 방법을 보여 주는 샘플은 [GitHub의 런타임 컴파일 샘플](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="aacdd-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aacdd-175">:::no-loc(Razor):::파일은 연결 된 :::no-loc(Razor)::: 페이지 또는 MVC 뷰가 호출 될 때 런타임에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-175">A :::no-loc(Razor)::: file is compiled at runtime, when the associated :::no-loc(Razor)::: Page or MVC view is invoked.</span></span> <span data-ttu-id="aacdd-176">:::no-loc(Razor):::파일은 [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk)를 사용 하 여 빌드 및 게시 시간 모두에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-176">:::no-loc(Razor)::: files are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="aacdd-177">:::no-loc(Razor)::: 컴파일과</span><span class="sxs-lookup"><span data-stu-id="aacdd-177">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="aacdd-178">파일의 빌드 및 게시 타임 컴파일은 :::no-loc(Razor)::: SDK에서 기본적으로 사용 하도록 설정 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-178">Build- and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="aacdd-179">:::no-loc(Razor):::파일이 업데이트 된 후의 편집은 빌드할 때 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-179">Editing :::no-loc(Razor)::: files after they're updated is supported at build time.</span></span> <span data-ttu-id="aacdd-180">기본적으로 컴파일된 *Views.dll* 와 파일을 컴파일하는 *.cshtml* 데 필요한 참조 어셈블리는 :::no-loc(Razor)::: 응용 프로그램과 함께 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile :::no-loc(Razor)::: files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aacdd-181">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="aacdd-182">[ :::no-loc(Razor)::: Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-182">We recommend migrating to [:::no-loc(Razor)::: Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="aacdd-183">:::no-loc(Razor):::SDK는 프로젝트 파일에 미리 컴파일 관련 속성이 설정 되지 않은 경우에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-183">The :::no-loc(Razor)::: SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="aacdd-184">예를 들어 *.csproj* 파일의 속성을로 설정 하면 `Mvc:::no-loc(Razor):::CompileOnPublish` `true` SDK가 사용 되지 않습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-184">For instance, setting the *.csproj* file's `Mvc:::no-loc(Razor):::CompileOnPublish` property to `true` disables the :::no-loc(Razor)::: SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="aacdd-185">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="aacdd-185">Runtime compilation</span></span>

<span data-ttu-id="aacdd-186">빌드 타임 컴파일은 파일의 런타임 컴파일을 통해 보완 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="aacdd-186">Build-time compilation is supplemented by runtime compilation of :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="aacdd-187">ASP.NET Core MVC는 :::no-loc(Razor)::: *cshtml* 파일의 내용이 변경 될 때 파일을 다시 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="aacdd-187">ASP.NET Core MVC will recompile :::no-loc(Razor)::: files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aacdd-188">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="aacdd-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
