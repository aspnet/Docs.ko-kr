---
title: ASP.NET Core Web API의 JsonPatch
author: rick-anderson
description: ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: da507974b88c21de22e2c7a56950943207565138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060549"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="b4672-103">ASP.NET Core Web API의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="b4672-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="b4672-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b4672-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b4672-105">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="b4672-106">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="b4672-106">Package installation</span></span>

<span data-ttu-id="b4672-107">앱에서 JSON 패치 지원을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="b4672-108">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="b4672-109">프로젝트의 메서드를 업데이트 `Startup.ConfigureServices` 하 여를 호출 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="b4672-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="b4672-111">`AddNewtonsoftJson`은 MVC 서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="b4672-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="b4672-112">JSON 패치, AddNewtonsoftJson 및 System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="b4672-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="b4672-113">`AddNewtonsoftJson``System.Text.Json` **모든** JSON 콘텐츠의 서식을 지정 하는 데 사용 되는 기반 입력 및 출력 포맷터를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="b4672-114">를 사용 하 여 JSON 패치에 대 한 지원을 추가 하 고 `Newtonsoft.Json` 다른 포맷터는 변경 하지 않은 채 프로젝트의 메서드를 다음과 같이 업데이트 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b4672-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="b4672-115">위의 코드에는 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 패키지와 다음 문이 필요 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="b4672-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="b4672-116">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="b4672-116">PATCH HTTP request method</span></span>

<span data-ttu-id="b4672-117">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="b4672-118">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="b4672-119">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="b4672-119">JSON Patch</span></span>

<span data-ttu-id="b4672-120">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="b4672-121">JSON 패치 문서에는 작업 배열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-121">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="b4672-122">각 작업은 특정 유형의 변경 내용을 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="b4672-123">이러한 변경의 예로는 배열 요소 추가 또는 속성 값 바꾸기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="b4672-124">예를 들어, 다음 JSON 문서는 리소스, 리소스에 대 한 JSON 패치 문서 및 패치 작업 적용 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="b4672-125">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="b4672-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="b4672-126">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="b4672-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="b4672-127">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="b4672-127">In the preceding JSON:</span></span>

* <span data-ttu-id="b4672-128">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="b4672-129">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="b4672-130">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="b4672-131">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="b4672-131">Resource after patch</span></span>

<span data-ttu-id="b4672-132">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="b4672-133">JSON 패치 문서를 리소스에 적용 하 여 변경한 내용은 원자성입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="b4672-134">목록에서 작업이 실패 하면 목록에 작업이 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="b4672-135">경로 구문</span><span class="sxs-lookup"><span data-stu-id="b4672-135">Path syntax</span></span>

<span data-ttu-id="b4672-136">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="b4672-137">예: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="b4672-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="b4672-138">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="b4672-139">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="b4672-140">배열의 끝에 대 한 `add` `-` 인덱스 번호 대신 하이픈 ()을 사용 `/addresses/-` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="b4672-141">작업</span><span class="sxs-lookup"><span data-stu-id="b4672-141">Operations</span></span>

<span data-ttu-id="b4672-142">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="b4672-143">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="b4672-143">Operation</span></span>  | <span data-ttu-id="b4672-144">참고</span><span class="sxs-lookup"><span data-stu-id="b4672-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="b4672-145">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-145">Add a property or array element.</span></span> <span data-ttu-id="b4672-146">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="b4672-147">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="b4672-148">동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="b4672-149">소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="b4672-150">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="b4672-151">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="b4672-152">ASP.NET Core의 JSON 패치</span><span class="sxs-lookup"><span data-stu-id="b4672-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="b4672-153">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="b4672-154">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="b4672-154">Action method code</span></span>

<span data-ttu-id="b4672-155">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="b4672-156">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="b4672-157">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="b4672-158">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="b4672-159">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="b4672-160">샘플 앱의이 코드는 다음 모델과 함께 작동 합니다 `Customer` .</span><span class="sxs-lookup"><span data-stu-id="b4672-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="b4672-161">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="b4672-161">The sample action method:</span></span>

* <span data-ttu-id="b4672-162">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="b4672-163">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-163">Applies the patch.</span></span>
* <span data-ttu-id="b4672-164">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="b4672-165">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="b4672-166">모델 상태</span><span class="sxs-lookup"><span data-stu-id="b4672-166">Model state</span></span>

<span data-ttu-id="b4672-167">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="b4672-168">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="b4672-169">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="b4672-170">동적 개체</span><span class="sxs-lookup"><span data-stu-id="b4672-170">Dynamic objects</span></span>

<span data-ttu-id="b4672-171">다음 동작 메서드 예에서는 동적 개체에 패치를 적용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="b4672-172">추가 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-172">The add operation</span></span>

* <span data-ttu-id="b4672-173">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="b4672-174">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="b4672-175">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="b4672-176">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="b4672-177">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="b4672-178">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="b4672-179">제거 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-179">The remove operation</span></span>

* <span data-ttu-id="b4672-180">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="b4672-181">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="b4672-182">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="b4672-183">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="b4672-184">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="b4672-185">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="b4672-186">다음 샘플 패치 문서는 `CustomerName` 를 null로 설정 하 고 삭제 합니다 `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="b4672-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="b4672-187">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-187">The replace operation</span></span>

<span data-ttu-id="b4672-188">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="b4672-189">다음 샘플 패치 문서는의 값을 설정 `CustomerName` 하 고를 `Orders[0]` 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="b4672-190">이동 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-190">The move operation</span></span>

* <span data-ttu-id="b4672-191">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="b4672-192">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="b4672-193">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="b4672-194">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="b4672-195">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="b4672-196">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-196">The following sample patch document:</span></span>

* <span data-ttu-id="b4672-197">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b4672-198">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="b4672-199">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="b4672-200">복사 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-200">The copy operation</span></span>

<span data-ttu-id="b4672-201">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="b4672-202">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-202">The following sample patch document:</span></span>

* <span data-ttu-id="b4672-203">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b4672-204">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="b4672-205">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-205">The test operation</span></span>

<span data-ttu-id="b4672-206">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="b4672-207">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="b4672-208">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="b4672-209">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="b4672-210">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="b4672-210">Get the code</span></span>

<span data-ttu-id="b4672-211">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)</span><span class="sxs-lookup"><span data-stu-id="b4672-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="b4672-212">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4672-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b4672-213">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="b4672-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="b4672-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="b4672-215">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="b4672-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="b4672-216">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="b4672-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="b4672-217">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4672-218">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b4672-218">Additional resources</span></span>

* [<span data-ttu-id="b4672-219">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="b4672-220">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="b4672-221">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="b4672-222">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="b4672-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="b4672-223">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="b4672-224">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="b4672-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4672-225">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="b4672-226">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="b4672-226">PATCH HTTP request method</span></span>

<span data-ttu-id="b4672-227">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="b4672-228">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="b4672-229">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="b4672-229">JSON Patch</span></span>

<span data-ttu-id="b4672-230">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="b4672-231">JSON 패치 문서에는 작업 배열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-231">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="b4672-232">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="b4672-233">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="b4672-234">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="b4672-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="b4672-235">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="b4672-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="b4672-236">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="b4672-236">In the preceding JSON:</span></span>

* <span data-ttu-id="b4672-237">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="b4672-238">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="b4672-239">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="b4672-240">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="b4672-240">Resource after patch</span></span>

<span data-ttu-id="b4672-241">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="b4672-242">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="b4672-243">경로 구문</span><span class="sxs-lookup"><span data-stu-id="b4672-243">Path syntax</span></span>

<span data-ttu-id="b4672-244">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="b4672-245">예: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="b4672-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="b4672-246">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="b4672-247">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="b4672-248">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="b4672-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="b4672-249">작업</span><span class="sxs-lookup"><span data-stu-id="b4672-249">Operations</span></span>

<span data-ttu-id="b4672-250">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="b4672-251">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="b4672-251">Operation</span></span>  | <span data-ttu-id="b4672-252">참고</span><span class="sxs-lookup"><span data-stu-id="b4672-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="b4672-253">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-253">Add a property or array element.</span></span> <span data-ttu-id="b4672-254">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="b4672-255">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="b4672-256">동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="b4672-257">소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="b4672-258">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="b4672-259">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="b4672-260">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="b4672-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="b4672-261">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="b4672-262">패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="b4672-263">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="b4672-263">Action method code</span></span>

<span data-ttu-id="b4672-264">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="b4672-265">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="b4672-266">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="b4672-267">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="b4672-268">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="b4672-269">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="b4672-270">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="b4672-270">The sample action method:</span></span>

* <span data-ttu-id="b4672-271">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="b4672-272">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-272">Applies the patch.</span></span>
* <span data-ttu-id="b4672-273">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="b4672-274">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="b4672-275">모델 상태</span><span class="sxs-lookup"><span data-stu-id="b4672-275">Model state</span></span>

<span data-ttu-id="b4672-276">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="b4672-277">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="b4672-278">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="b4672-279">동적 개체</span><span class="sxs-lookup"><span data-stu-id="b4672-279">Dynamic objects</span></span>

<span data-ttu-id="b4672-280">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="b4672-281">추가 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-281">The add operation</span></span>

* <span data-ttu-id="b4672-282">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="b4672-283">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="b4672-284">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="b4672-285">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="b4672-286">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="b4672-287">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="b4672-288">제거 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-288">The remove operation</span></span>

* <span data-ttu-id="b4672-289">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="b4672-290">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="b4672-291">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="b4672-292">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="b4672-293">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="b4672-294">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="b4672-295">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="b4672-296">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-296">The replace operation</span></span>

<span data-ttu-id="b4672-297">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="b4672-298">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="b4672-299">이동 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-299">The move operation</span></span>

* <span data-ttu-id="b4672-300">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="b4672-301">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="b4672-302">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4672-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="b4672-303">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="b4672-304">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="b4672-305">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-305">The following sample patch document:</span></span>

* <span data-ttu-id="b4672-306">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b4672-307">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="b4672-308">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="b4672-309">복사 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-309">The copy operation</span></span>

<span data-ttu-id="b4672-310">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="b4672-311">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-311">The following sample patch document:</span></span>

* <span data-ttu-id="b4672-312">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b4672-313">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="b4672-314">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="b4672-314">The test operation</span></span>

<span data-ttu-id="b4672-315">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="b4672-316">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="b4672-317">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="b4672-318">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="b4672-319">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="b4672-319">Get the code</span></span>

<span data-ttu-id="b4672-320">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="b4672-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="b4672-321">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4672-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b4672-322">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="b4672-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="b4672-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="b4672-324">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="b4672-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="b4672-325">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="b4672-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="b4672-326">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4672-327">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b4672-327">Additional resources</span></span>

* [<span data-ttu-id="b4672-328">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="b4672-329">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="b4672-330">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="b4672-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="b4672-331">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="b4672-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="b4672-332">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b4672-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="b4672-333">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="b4672-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
