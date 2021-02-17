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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536308"
---
<span data-ttu-id="a646c-101">관련 구성 값을 읽는 기본 방법은 [옵션 패턴](xref:fundamentals/configuration/options)를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a646c-102">예를 들어 다음 구성 값을 읽으려면:</span><span class="sxs-lookup"><span data-stu-id="a646c-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="a646c-103">다음 `PositionOptions` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a646c-104">옵션 클래스:</span><span class="sxs-lookup"><span data-stu-id="a646c-104">An options class:</span></span>

* <span data-ttu-id="a646c-105">매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="a646c-106">형식의 모든 공용 읽기-쓰기 속성이 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="a646c-107">필드가 바인딩되지 ***않았습니다***.</span><span class="sxs-lookup"><span data-stu-id="a646c-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="a646c-108">위 코드에서 `Position`은 바운딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="a646c-109">`Position` 속성을 사용하므로 클래스를 구성 공급자에 바인딩할 때 문자열 `"Position"`을 앱에서 하드 코딩하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="a646c-110">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-110">The following code:</span></span>

* <span data-ttu-id="a646c-111">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)를 호출하여 `PositionOptions` 클래스를 `Position` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="a646c-112">`Position` 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="a646c-113">위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a646c-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a646c-115">`ConfigurationBinder.Get<T>`가 `ConfigurationBinder.Bind`를 사용하는 것보다 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="a646c-116">다음 코드에서는 `ConfigurationBinder.Get<T>`와 `PositionOptions` 클래스를 함께 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="a646c-117">위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a646c-118">**옵션 패턴** 을 사용하는 경우 대체 방법은 `Position` 섹션을 바인딩하고 [종속성 주입 서비스 컨테이너](xref:fundamentals/dependency-injection)에 추가하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a646c-119">다음 코드에서 `PositionOptions`는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="a646c-120">위의 코드를 사용하여 다음 코드는 위치 옵션을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="a646c-121">위 코드에서는 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽지 ***않습니다***.</span><span class="sxs-lookup"><span data-stu-id="a646c-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="a646c-122">앱을 시작한 후의 변경 사항을 읽으려면 [IOptionsSnapshot](xref:fundamentals/configuration/options#ios)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a646c-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
