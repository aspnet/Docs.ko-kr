---
title: ASP.NET Core Blazor용 트리머 구성
author: guardrex
description: Blazor 앱을 빌드할 때 IL(중간 언어) 링커(트리머)를 제어하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975219"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="91db0-103">ASP.NET Core Blazor용 트리머 구성</span><span class="sxs-lookup"><span data-stu-id="91db0-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="91db0-104">Blazor WebAssembly에서 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 트리밍을 수행하여 게시된 출력값의 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="91db0-105">기본적으로 트리밍은 앱을 게시할 때 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="91db0-106">트리밍은 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="91db0-107">리플렉션을 사용하는 앱에서 트리머는 대개 리플렉션에 필요한 형식을 런타임에 결정하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="91db0-108">리플렉션을 사용하는 앱을 트리밍하려면 앱이 종속된 패키지나 프레임워크 및 앱의 코드에서 리플렉션에 필요한 형식을 트리머가 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="91db0-109">또한 트리머는 런타임에 앱의 동적 동작에 반응할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="91db0-110">트리밍된 앱이 배포된 후 올바르게 작동하도록 하려면 개발하는 동안 게시된 출력을 자주 테스트하세요.</span><span class="sxs-lookup"><span data-stu-id="91db0-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="91db0-111">트리머를 구성하려면 다음 주제에 대한 지침에 포함되어 있는 .NET 기본 사항 설명서에서 [트리밍 옵션](/dotnet/core/deploying/trimming-options) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91db0-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="91db0-112">프로젝트 파일에서 `<PublishTrimmed>` 속성을 사용하여 전체 앱에 대해 트리밍을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="91db0-113">적극적으로 사용되지 않는 IL을 트리머에서 삭제하는 방법을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="91db0-114">트리머가 특정 어셈블리를 트리밍하는 것을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="91db0-115">트리밍을 위한 “루트” 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="91db0-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="91db0-116">프로젝트 파일에서 `<SuppressTrimAnalysisWarnings>` 속성을 `false`로 설정하여 반영된 형식에 대한 경고를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="91db0-117">기호 트리밍 및 디버거 지원을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="91db0-118">트리밍 프레임워크 라이브러리 기능에 대해 트리머 기능을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="91db0-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91db0-119">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="91db0-119">Additional resources</span></span>

* [<span data-ttu-id="91db0-120">자체 포함 배포 및 실행 파일 트리밍</span><span class="sxs-lookup"><span data-stu-id="91db0-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
