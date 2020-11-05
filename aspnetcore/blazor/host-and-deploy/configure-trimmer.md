---
title: ASP.NET Core :::no-loc(Blazor):::용 트리머 구성
author: guardrex
description: ':::no-loc(Blazor)::: 앱을 빌드할 때 IL(중간 언어) 링커(트리머)를 제어하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055804"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="178f2-103">ASP.NET Core :::no-loc(Blazor):::용 트리머 구성</span><span class="sxs-lookup"><span data-stu-id="178f2-103">Configure the Trimmer for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="178f2-104">작성자: [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="178f2-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="178f2-105">:::no-loc(Blazor WebAssembly):::에서 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 트리밍을 수행하여 게시된 출력값의 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="178f2-106">앱 트리밍은 크기에 맞게 최적화되지만 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="178f2-107">트리머는 동적 동작에 대한 정보를 알지 못하고 일반적으로 런타임에서 리플렉션에 필요한 형식을 결정할 수 없기 때문에, 리플렉션 또는 관련 동적 기능을 사용하는 앱은 잘린 경우 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="178f2-108">이러한 앱을 자르려면 앱이 의존하는 코드 및 패키지 또는 프레임워크의 리플렉션에서 필요로 하는 형식에 대해 트리머가 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="178f2-109">트리밍된 앱이 배포된 후 올바로 작동하도록 하려면 개발하는 동안 게시된 출력값을 자주 테스트하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="178f2-110">`PublishTrimmed` MSBuild 속성을 앱의 프로젝트 파일에서 `false`로 설정하면 .NET 앱에 대한 트리밍을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="178f2-111">트리머를 구성하는 추가 옵션은 [트리밍 옵션](/dotnet/core/deploying/trimming-options)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="178f2-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="178f2-112">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="178f2-112">Additional resources</span></span>

* [<span data-ttu-id="178f2-113">자체 포함 배포 및 실행 파일 트리밍</span><span class="sxs-lookup"><span data-stu-id="178f2-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
