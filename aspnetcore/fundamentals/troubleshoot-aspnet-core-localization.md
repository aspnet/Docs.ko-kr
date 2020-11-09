---
title: ASP.NET Core 지역화 문제 해결
author: hishamco
description: ASP.NET Core 앱에서 지역화 관련 문제를 진단하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053620"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="e4e12-103">ASP.NET Core 지역화 문제 해결</span><span class="sxs-lookup"><span data-stu-id="e4e12-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="e4e12-104">작성자: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="e4e12-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="e4e12-105">이 문서에서는 ASP.NET Core 앱 지역화 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="e4e12-106">지역화 구성 문제</span><span class="sxs-lookup"><span data-stu-id="e4e12-106">Localization configuration issues</span></span>

<span data-ttu-id="e4e12-107">**지역화 미들웨어 순서**</span><span class="sxs-lookup"><span data-stu-id="e4e12-107">**Localization middleware order**</span></span>  
<span data-ttu-id="e4e12-108">지역화 미들웨어가 예상대로 정렬되지 않으므로 앱이 지역화되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="e4e12-109">이 문제를 해결하려면 해당 지역화 미들웨어가 MVC 미들웨어 전에 등록되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="e4e12-110">그렇지 않으면 지역화 미들웨어는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="e4e12-111">**지역화 리소스 경로가 없음**</span><span class="sxs-lookup"><span data-stu-id="e4e12-111">**Localization resources path not found**</span></span>

<span data-ttu-id="e4e12-112">**RequestCultureProvider에서 지원되는 문화권이 등록된 문화권과 한 번 일치하지 않음**</span><span class="sxs-lookup"><span data-stu-id="e4e12-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="e4e12-113">리소스 파일 이름 지정 문제</span><span class="sxs-lookup"><span data-stu-id="e4e12-113">Resource file naming issues</span></span>

<span data-ttu-id="e4e12-114">ASP.NET Core에는 지역화 리소스 파일 이름 지정에 대해 미리 정의된 규칙 지침이 있고 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="e4e12-115">누락된 리소스</span><span class="sxs-lookup"><span data-stu-id="e4e12-115">Missing resources</span></span>

<span data-ttu-id="e4e12-116">리소스를 찾을 수 없는 일반적인 원인은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="e4e12-117">`resx` 파일 또는 로컬라이저 요청에서 리소스 이름의 철자가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="e4e12-118">리소스가 일부 언어의 경우 `resx`에서 누락되었지만 다른 언어에서 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="e4e12-119">여전히 문제가 있는 경우 누락된 리소스에 대한 자세한 내용은 `Debug` 로그 수준의 지역화 로그 메시지를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e12-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="e4e12-120">_**힌트:** `CookieRequestCultureProvider`를 사용하는 경우 지역화 cookie 값 안의 문화권에서 작은따옴표를 사용하지 않았는지 확인합니다. 예를 들어 `c='en-UK'|uic='en-US'`가 잘못된 cookie 값인 반면, `c=en-UK|uic=en-US`는 유효합니다._</span><span class="sxs-lookup"><span data-stu-id="e4e12-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="e4e12-121">리소스 및 클래스 라이브러리 문제</span><span class="sxs-lookup"><span data-stu-id="e4e12-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="e4e12-122">기본적으로 ASP.NET Core는 클래스 라이브러리가 [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)를 통해 해당 리소스 파일을 찾을 수 있는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="e4e12-123">클래스 라이브러리와 관련된 일반적인 문제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="e4e12-124">라이브러리 클래스에서 `ResourceLocationAttribute`가 누락되면 `ResourceManagerStringLocalizerFactory`가 리소스를 검색하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="e4e12-125">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="e4e12-125">Resource file naming.</span></span> <span data-ttu-id="e4e12-126">자세한 내용은 [리소스 파일 이름 지정 문제](#resource-file-naming-issues) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e12-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="e4e12-127">클래스 라이브러리의 루트 네임스페이스 변경</span><span class="sxs-lookup"><span data-stu-id="e4e12-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="e4e12-128">자세한 내용은 [루트 네임스페이스 문제](#root-namespace-issues) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e12-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="e4e12-129">CustomRequestCultureProvider가 예상대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="e4e12-130">`RequestLocalizationOptions` 클래스에는 세 가지 기본 공급 기업이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e4e12-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1)를 통해 앱에서 지역화 문화권을 제공하는 방법을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="e4e12-132">기본 공급 기업이 사용자 요구 사항을 충족하지 못할 때 `CustomRequestCultureProvider`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="e4e12-133">사용자 지정 공급 기업이 제대로 작동하지 않는 일반적인 원인은 `RequestCultureProviders` 목록의 첫 번째 공급 기업이 아니라는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="e4e12-134">이 문제를 해결하려면:</span><span class="sxs-lookup"><span data-stu-id="e4e12-134">To resolve this issue:</span></span>

- <span data-ttu-id="e4e12-135">다음과 같이 `RequestCultureProviders` 목록에서 0의 위치에 있는 사용자 지정 공급 기업을 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="e4e12-136">`AddInitialRequestCultureProvider` 확장 메서드를 사용하여 사용자 지정 공급 기업을 초기 공급 기업으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="e4e12-137">루트 네임스페이스 문제</span><span class="sxs-lookup"><span data-stu-id="e4e12-137">Root Namespace issues</span></span>

<span data-ttu-id="e4e12-138">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다르면 지역화가 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="e4e12-139">이 문제를 방지하려면 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에 자세히 설명된 대로 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="e4e12-140">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e4e12-141">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="e4e12-142">리소스 및 빌드 작업</span><span class="sxs-lookup"><span data-stu-id="e4e12-142">Resources & Build Action</span></span>

<span data-ttu-id="e4e12-143">지역화에 대해 리소스 파일을 사용하는 경우 적절한 빌드 작업이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="e4e12-144">해당 리소스 파일이 **포함 리소스** 여야 하며, 그렇지 않으면 `ResourceStringLocalizer`가 이러한 리소스를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e12-144">They should be **Embedded Resource** , otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
