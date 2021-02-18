---
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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536307"
---
<a name="csc"></a>

<span data-ttu-id="37943-101">서비스를 등록하고 옵션을 구성하는 다음 `ConfigureServices` 메서드를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="37943-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="37943-102">관련 등록 그룹을 확장 메서드로 이동하여 서비스를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37943-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="37943-103">예를 들어, 구성 서비스는 다음 클래스에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="37943-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="37943-104">나머지 서비스는 유사한 클래스에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="37943-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="37943-105">다음 `ConfigureServices` 메서드는 새 확장 메서드를 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="37943-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="37943-106">**_참고:_** 각 `services.Add{GROUP_NAME}` 확장 메서드는 서비스를 추가하고 잠재적으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="37943-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="37943-107">예를 들어 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>는 보기에 필요한 서비스 MVC 컨트롤러를 추가하고 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>는 Razor Pages에 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37943-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="37943-108">앱에서 이 명명 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37943-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="37943-109">확장 메서드를 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="37943-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
