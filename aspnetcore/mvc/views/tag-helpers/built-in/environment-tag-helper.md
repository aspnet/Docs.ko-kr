---
title: ASP.NET Core의 환경 태그 도우미
author: pkellner
description: 모든 속성을 포함하여 정의된 ASP.NET Core 환경 태그 도우미
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061134"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="ff252-103">ASP.NET Core의 환경 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="ff252-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ff252-104">작성자: [Peter Kellner](https://peterkellner.net) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ff252-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ff252-105">환경 태그 도우미는 현재 [호스팅 환경](xref:fundamentals/environments)에 기반하여 포함된 콘텐츠를 조건부로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="ff252-106">환경 태그 도우미의 단일 특성인 `names`는 쉼표로 구분된 환경 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="ff252-107">제공된 환경 이름 중 현재 환경과 일치하는 이름이 존재하면 포함된 콘텐츠가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="ff252-108">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff252-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="ff252-109">환경 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="ff252-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="ff252-110">이름</span><span class="sxs-lookup"><span data-stu-id="ff252-110">names</span></span>

<span data-ttu-id="ff252-111">`names`는 포함된 콘텐츠의 렌더링을 트리거하는 단일 호스팅 환경 이름 또는 쉼표로 구분된 호스팅 환경 이름 목록을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="ff252-112">환경 값은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)에서 반환된 현재 값과 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="ff252-113">이 비교에서 대/소문자는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-113">The comparison ignores case.</span></span>

<span data-ttu-id="ff252-114">다음 예제는 환경 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="ff252-115">호스팅 환경이 스테이징 또는 프로덕션일 경우 콘텐츠가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="ff252-116">include 및 exclude 특성</span><span class="sxs-lookup"><span data-stu-id="ff252-116">include and exclude attributes</span></span>

<span data-ttu-id="ff252-117">`include`& `exclude` 특성은 포함 되거나 제외 된 호스팅 환경 이름을 기반으로 포함 된 콘텐츠를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="ff252-118">include</span><span class="sxs-lookup"><span data-stu-id="ff252-118">include</span></span>

<span data-ttu-id="ff252-119">`include` 속성은 `names` 특성과 유사한 동작을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="ff252-120">`include` 특성 값에 나열된 환경과 앱의 호스팅 환경([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*))이 일치해야만 `<environment>` 태그의 콘텐츠를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="ff252-121">제외</span><span class="sxs-lookup"><span data-stu-id="ff252-121">exclude</span></span>

<span data-ttu-id="ff252-122">`include` 특성과는 대조적으로 `<environment>` 태그의 콘텐츠는 호스팅 환경이 `exclude` 특성 값에 나열된 환경과 일치하지 않을 경우 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff252-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ff252-123">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ff252-123">Additional resources</span></span>

* <xref:fundamentals/environments>
