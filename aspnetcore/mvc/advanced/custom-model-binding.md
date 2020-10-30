---
title: ASP.NET Core의 사용자 지정 모델 바인딩
author: ardalis
description: 모델 바인딩을 통해 컨트롤러 작업이 ASP.NET Core의 모델 형식과 함께 직접 작동할 수 있게 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 7675e95c43b9ee428ee5fda86ea3ead9815ed645
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058469"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="f74b9-103">ASP.NET Core의 사용자 지정 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="f74b9-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f74b9-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f74b9-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="f74b9-105">모델 바인딩을 통해 컨트롤러 작업에서 HTTP 요청이 아닌 모델 형식(메서드 인수로 전달된)을 직접 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="f74b9-106">들어오는 요청 데이터와 애플리케이션 모델 간의 매핑은 모델 바인더를 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="f74b9-107">개발자는 사용자 지정 모델 바인더를 구현하여 기본 모델 바인딩 기능을 확장할 수 있습니다(일반적으로 개발자가 고유의 공급자를 작성할 필요는 없음).</span><span class="sxs-lookup"><span data-stu-id="f74b9-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="f74b9-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f74b9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="f74b9-109">기본 모델 바인더 제한 사항</span><span class="sxs-lookup"><span data-stu-id="f74b9-109">Default model binder limitations</span></span>

<span data-ttu-id="f74b9-110">기본 모델 바인더는 대부분의 공용 .NET Core 데이터 형식을 지원하며 대부분의 개발자 요구 사항을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="f74b9-111">요청의 텍스트 기반 입력을 모델 형식에 직접 바인딩할 것으로 예상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="f74b9-112">바인딩하려면 입력을 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="f74b9-113">예를 들어 모델 데이터를 조회하는 데 사용할 수 있는 키를 갖고 있는 경우</span><span class="sxs-lookup"><span data-stu-id="f74b9-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="f74b9-114">그 키를 기반으로 사용자 지정 모델 바인더를 사용하여 데이터를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="f74b9-115">모델 바인딩 검토</span><span class="sxs-lookup"><span data-stu-id="f74b9-115">Model binding review</span></span>

<span data-ttu-id="f74b9-116">모델 바인딩은 작업을 수행하는 형식에 대한 특정 정의를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="f74b9-117">*단순 형식* 은 입력의 단일 문자열에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="f74b9-118">*복합 형식* 은 여러 입력 값에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="f74b9-119">프레임워크는 `TypeConverter`의 존재 여부에 따라 차이점을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="f74b9-120">외부 리소스가 필요 없는 간단한 `string` -> `SomeType` 매핑이 있는 경우 형식 변환기를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="f74b9-121">개발자 고유의 사용자 지정 모델 바인더를 만들기 전에 기존 모델 바인더가 구현되는 방식을 검토하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="f74b9-122">base64로 인코딩된 문자열을 바이트 배열로 변환하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="f74b9-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="f74b9-123">바이트 배열은 종종 파일 또는 데이터베이스 BLOB 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="f74b9-124">ByteArrayModelBinder 사용</span><span class="sxs-lookup"><span data-stu-id="f74b9-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="f74b9-125">Base64로 인코딩된 문자열은 이진 데이터를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="f74b9-126">예를 들어 이미지를 문자열로 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="f74b9-127">이 샘플에는 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt)에 base64로 인코딩된 문자열로 이미지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="f74b9-128">ASP.NET Core MVC는 base64로 인코드된 문자열을 가져온 후 `ByteArrayModelBinder`를 사용하여 바이트 배열로 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="f74b9-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>는 `byte[]` 인수를 `ByteArrayModelBinder`에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="f74b9-130">개발자 고유의 사용자 지정 모델 바인더를 만들 때 고유의 `IModelBinderProvider` 형식을 구현해도 되고 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>를 사용해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="f74b9-131">다음 예제에서는 `ByteArrayModelBinder`를 사용하여 base64로 인코딩된 문자열을 `byte[]`로 변환하고 그 결과를 파일에 저장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f74b9-132">[Postman](https://www.getpostman.com/) 같은 도구를 사용하여 base64로 인코딩된 문자열을 이 api 메서드에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="f74b9-133">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="f74b9-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="f74b9-134">바인더가 요청 데이터를 적절한 이름의 속성 또는 인수로 바인딩할 수 있는 한, 모델 바인딩은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="f74b9-135">다음 예제에서는 보기 모델에 `ByteArrayModelBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="f74b9-136">사용자 지정 모델 바인더 샘플</span><span class="sxs-lookup"><span data-stu-id="f74b9-136">Custom model binder sample</span></span>

<span data-ttu-id="f74b9-137">이 섹션에서는 다음과 같은 사용자 지정 모델 바인더를 구현하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="f74b9-138">들어오는 요청 데이터를 강력한 형식의 키 인수로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="f74b9-139">Entity Framework Core를 사용하여 연결된 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="f74b9-140">연결된 엔터티를 작업 메서드에 인수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="f74b9-141">다음 샘플은 `Author` 모델에서 `ModelBinder` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="f74b9-142">이전 코드에서 `ModelBinder` 특성은 `Author` 작업 매개 변수를 바인딩하는 데 사용할 `IModelBinder` 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="f74b9-143">다음 `AuthorEntityBinder` 클래스는 Entity Framework Core 및 `authorId`를 사용하여 데이터 원본에서 엔터티를 가져와 `Author` 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="f74b9-144">앞의 `AuthorEntityBinder` 클래스는 사용자 지정 모델 바인더를 설명하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="f74b9-145">이 클래스는 조회 시나리오의 모범 사례를 설명하기 위한 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="f74b9-146">조회의 경우 `authorId`를 바인딩하고 작업 메서드에서 데이터베이스를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="f74b9-147">이 방식은 모델 바인딩 실패를 `NotFound` 사례와 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="f74b9-148">다음 코드는 작업 메서드에 `AuthorEntityBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="f74b9-149">`ModelBinder` 특성은 기본 규칙을 사용하지 않는 매개 변수에 `AuthorEntityBinder`를 적용하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="f74b9-150">이 예에서는 인수 이름이 기본 `authorId`가 아니기 때문에 `ModelBinder` 특성을 사용하여 매개 변수에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="f74b9-151">컨트롤러와 작업 메서드 모두 작업 메서드에서 엔터티를 조회하는 것에 비해 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="f74b9-152">Entity Framework Core를 사용하여 작성자를 가져오는 논리는 모델 바인더로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="f74b9-153">`Author` 모델에 바인딩하는 메서드가 여러 개 있는 경우 상당히 간소화될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="f74b9-154">개별 모델 속성(viewmodel처럼) 또는 작업 메서드 매개 변수에 `ModelBinder` 특성을 적용하여 그 형식 또는 작업에만 해당하는 특정 모델 바인더 또는 모델을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="f74b9-155">ModelBinderProvider 구현</span><span class="sxs-lookup"><span data-stu-id="f74b9-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="f74b9-156">특성을 적용하는 대신, `IModelBinderProvider`를 구현할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="f74b9-157">기본 제공 프레임워크 바인더는 이 방식으로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="f74b9-158">바인더가 작동하는 형식을 지정할 때 바인더가 허용하는 입력이 **아니라** 바인더가 생성하는 인수 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="f74b9-159">다음 바인더 공급자는 `AuthorEntityBinder`를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="f74b9-160">공급자의 MVC 컬렉션에 추가할 때 `Author` 또는 `Author`-형식 매개 변수에서 `ModelBinder` 특성을 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="f74b9-161">참고: 앞의 코드는 `BinderTypeModelBinder`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="f74b9-162">`BinderTypeModelBinder`는 모델 바인더에 대한 팩터리 역할을 하며 DI(종속성 주입)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="f74b9-163">`AuthorEntityBinder`는 EF Core에 액세스하려면 DI가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="f74b9-164">모델 바인더에서 DI의 서비스를 요구하는 경우 `BinderTypeModelBinder`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="f74b9-165">사용자 지정 모델 바인더 공급자를 사용하려면 `ConfigureServices`에서 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="f74b9-166">모델 바인더를 평가할 때 공급자 컬렉션은 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="f74b9-167">입력 모델과 일치 하는 바인더를 반환 하는 첫 번째 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="f74b9-168">따라서 컬렉션의 끝에 공급자를 추가 하면 사용자 지정 바인더가 가능 하기 전에 기본 제공 모델 바인더가 호출 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="f74b9-169">이 예제에서는 작업 인수에 항상 사용 되도록 사용자 지정 공급자를 컬렉션의 시작 부분에 추가 합니다 `Author` .</span><span class="sxs-lookup"><span data-stu-id="f74b9-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="f74b9-170">다형 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="f74b9-170">Polymorphic model binding</span></span>

<span data-ttu-id="f74b9-171">파생 형식의 다른 모델에 바인딩하는 것을 다형 모델 바인딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="f74b9-172">요청 값을 특정 파생 모델 형식에 바인딩해야 하는 경우 다형 사용자 지정 모델 바인딩이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="f74b9-173">다형 모델 바인딩에는 다음과 같은 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="f74b9-174">모든 언어와 상호 운용할 수 있도록 설계된 REST API에는 일반적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="f74b9-175">바인딩된 모델에 대해 추론하기 어려워집니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="f74b9-176">그러나 앱에 다형 모델 바인딩이 필요한 경우 구현은 다음 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="f74b9-177">권장 사항 및 모범 사례</span><span class="sxs-lookup"><span data-stu-id="f74b9-177">Recommendations and best practices</span></span>

<span data-ttu-id="f74b9-178">사용자 지정 모델 바인더:</span><span class="sxs-lookup"><span data-stu-id="f74b9-178">Custom model binders:</span></span>

- <span data-ttu-id="f74b9-179">상태 코드를 설정하거나 결과를 반환하려고 시도하면 안 됩니다(예를 들어 404 찾을 수 없음).</span><span class="sxs-lookup"><span data-stu-id="f74b9-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="f74b9-180">모델 바인딩이 실패하면 [작업 필터](xref:mvc/controllers/filters) 또는 작업 메서드 자체의 논리에서 오류를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="f74b9-181">작업 메서드에서 반복 코드와 교차 편집 문제를 제거하는 데 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="f74b9-182">일반적으로 문자열을 사용자 지정 형식으로 변환하는 데 사용되지 않습니다. <xref:System.ComponentModel.TypeConverter>가 더 좋은 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f74b9-183">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f74b9-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f74b9-184">모델 바인딩을 통해 컨트롤러 작업에서 HTTP 요청이 아닌 모델 형식(메서드 인수로 전달된)을 직접 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="f74b9-185">들어오는 요청 데이터와 애플리케이션 모델 간의 매핑은 모델 바인더를 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="f74b9-186">개발자는 사용자 지정 모델 바인더를 구현하여 기본 모델 바인딩 기능을 확장할 수 있습니다(일반적으로 개발자가 고유의 공급자를 작성할 필요는 없음).</span><span class="sxs-lookup"><span data-stu-id="f74b9-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="f74b9-187">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f74b9-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="f74b9-188">기본 모델 바인더 제한 사항</span><span class="sxs-lookup"><span data-stu-id="f74b9-188">Default model binder limitations</span></span>

<span data-ttu-id="f74b9-189">기본 모델 바인더는 대부분의 공용 .NET Core 데이터 형식을 지원하며 대부분의 개발자 요구 사항을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="f74b9-190">요청의 텍스트 기반 입력을 모델 형식에 직접 바인딩할 것으로 예상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="f74b9-191">바인딩하려면 입력을 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="f74b9-192">예를 들어 모델 데이터를 조회하는 데 사용할 수 있는 키를 갖고 있는 경우</span><span class="sxs-lookup"><span data-stu-id="f74b9-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="f74b9-193">그 키를 기반으로 사용자 지정 모델 바인더를 사용하여 데이터를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="f74b9-194">모델 바인딩 검토</span><span class="sxs-lookup"><span data-stu-id="f74b9-194">Model binding review</span></span>

<span data-ttu-id="f74b9-195">모델 바인딩은 작업을 수행하는 형식에 대한 특정 정의를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="f74b9-196">*단순 형식* 은 입력의 단일 문자열에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="f74b9-197">*복합 형식* 은 여러 입력 값에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="f74b9-198">프레임워크는 `TypeConverter`의 존재 여부에 따라 차이점을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="f74b9-199">외부 리소스가 필요 없는 간단한 `string` -> `SomeType` 매핑이 있는 경우 형식 변환기를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="f74b9-200">개발자 고유의 사용자 지정 모델 바인더를 만들기 전에 기존 모델 바인더가 구현되는 방식을 검토하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="f74b9-201">base64로 인코딩된 문자열을 바이트 배열로 변환하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="f74b9-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="f74b9-202">바이트 배열은 종종 파일 또는 데이터베이스 BLOB 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="f74b9-203">ByteArrayModelBinder 사용</span><span class="sxs-lookup"><span data-stu-id="f74b9-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="f74b9-204">Base64로 인코딩된 문자열은 이진 데이터를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="f74b9-205">예를 들어 이미지를 문자열로 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="f74b9-206">이 샘플에는 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt)에 base64로 인코딩된 문자열로 이미지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="f74b9-207">ASP.NET Core MVC는 base64로 인코드된 문자열을 가져온 후 `ByteArrayModelBinder`를 사용하여 바이트 배열로 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="f74b9-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>는 `byte[]` 인수를 `ByteArrayModelBinder`에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="f74b9-209">개발자 고유의 사용자 지정 모델 바인더를 만들 때 고유의 `IModelBinderProvider` 형식을 구현해도 되고 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>를 사용해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="f74b9-210">다음 예제에서는 `ByteArrayModelBinder`를 사용하여 base64로 인코딩된 문자열을 `byte[]`로 변환하고 그 결과를 파일에 저장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="f74b9-211">[Postman](https://www.getpostman.com/) 같은 도구를 사용하여 base64로 인코딩된 문자열을 이 api 메서드에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="f74b9-212">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="f74b9-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="f74b9-213">바인더가 요청 데이터를 적절한 이름의 속성 또는 인수로 바인딩할 수 있는 한, 모델 바인딩은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="f74b9-214">다음 예제에서는 보기 모델에 `ByteArrayModelBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="f74b9-215">사용자 지정 모델 바인더 샘플</span><span class="sxs-lookup"><span data-stu-id="f74b9-215">Custom model binder sample</span></span>

<span data-ttu-id="f74b9-216">이 섹션에서는 다음과 같은 사용자 지정 모델 바인더를 구현하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="f74b9-217">들어오는 요청 데이터를 강력한 형식의 키 인수로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="f74b9-218">Entity Framework Core를 사용하여 연결된 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="f74b9-219">연결된 엔터티를 작업 메서드에 인수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="f74b9-220">다음 샘플은 `Author` 모델에서 `ModelBinder` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="f74b9-221">이전 코드에서 `ModelBinder` 특성은 `Author` 작업 매개 변수를 바인딩하는 데 사용할 `IModelBinder` 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="f74b9-222">다음 `AuthorEntityBinder` 클래스는 Entity Framework Core 및 `authorId`를 사용하여 데이터 원본에서 엔터티를 가져와 `Author` 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="f74b9-223">앞의 `AuthorEntityBinder` 클래스는 사용자 지정 모델 바인더를 설명하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="f74b9-224">이 클래스는 조회 시나리오의 모범 사례를 설명하기 위한 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="f74b9-225">조회의 경우 `authorId`를 바인딩하고 작업 메서드에서 데이터베이스를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="f74b9-226">이 방식은 모델 바인딩 실패를 `NotFound` 사례와 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="f74b9-227">다음 코드는 작업 메서드에 `AuthorEntityBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="f74b9-228">`ModelBinder` 특성은 기본 규칙을 사용하지 않는 매개 변수에 `AuthorEntityBinder`를 적용하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="f74b9-229">이 예에서는 인수 이름이 기본 `authorId`가 아니기 때문에 `ModelBinder` 특성을 사용하여 매개 변수에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="f74b9-230">컨트롤러와 작업 메서드 모두 작업 메서드에서 엔터티를 조회하는 것에 비해 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="f74b9-231">Entity Framework Core를 사용하여 작성자를 가져오는 논리는 모델 바인더로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="f74b9-232">`Author` 모델에 바인딩하는 메서드가 여러 개 있는 경우 상당히 간소화될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="f74b9-233">개별 모델 속성(viewmodel처럼) 또는 작업 메서드 매개 변수에 `ModelBinder` 특성을 적용하여 그 형식 또는 작업에만 해당하는 특정 모델 바인더 또는 모델을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="f74b9-234">ModelBinderProvider 구현</span><span class="sxs-lookup"><span data-stu-id="f74b9-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="f74b9-235">특성을 적용하는 대신, `IModelBinderProvider`를 구현할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="f74b9-236">기본 제공 프레임워크 바인더는 이 방식으로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="f74b9-237">바인더가 작동하는 형식을 지정할 때 바인더가 허용하는 입력이 **아니라** 바인더가 생성하는 인수 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="f74b9-238">다음 바인더 공급자는 `AuthorEntityBinder`를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="f74b9-239">공급자의 MVC 컬렉션에 추가할 때 `Author` 또는 `Author`-형식 매개 변수에서 `ModelBinder` 특성을 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="f74b9-240">참고: 앞의 코드는 `BinderTypeModelBinder`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="f74b9-241">`BinderTypeModelBinder`는 모델 바인더에 대한 팩터리 역할을 하며 DI(종속성 주입)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="f74b9-242">`AuthorEntityBinder`는 EF Core에 액세스하려면 DI가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="f74b9-243">모델 바인더에서 DI의 서비스를 요구하는 경우 `BinderTypeModelBinder`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="f74b9-244">사용자 지정 모델 바인더 공급자를 사용하려면 `ConfigureServices`에서 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="f74b9-245">모델 바인더를 평가할 때 공급자 컬렉션은 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="f74b9-246">바인더를 반환하는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="f74b9-247">컬렉션 끝에 공급자를 추가하면 사용자 지정 바인더가 기회를 얻기도 전에 기본 제공 모델 바인더가 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="f74b9-248">이 예제에서는 `Author` 작업 인수에 사용되도록 사용자 지정 공급자를 컬렉션의 시작 부분에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="f74b9-249">다형 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="f74b9-249">Polymorphic model binding</span></span>

<span data-ttu-id="f74b9-250">파생 형식의 다른 모델에 바인딩하는 것을 다형 모델 바인딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="f74b9-251">요청 값을 특정 파생 모델 형식에 바인딩해야 하는 경우 다형 사용자 지정 모델 바인딩이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="f74b9-252">다형 모델 바인딩에는 다음과 같은 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="f74b9-253">모든 언어와 상호 운용할 수 있도록 설계된 REST API에는 일반적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="f74b9-254">바인딩된 모델에 대해 추론하기 어려워집니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="f74b9-255">그러나 앱에 다형 모델 바인딩이 필요한 경우 구현은 다음 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="f74b9-256">권장 사항 및 모범 사례</span><span class="sxs-lookup"><span data-stu-id="f74b9-256">Recommendations and best practices</span></span>

<span data-ttu-id="f74b9-257">사용자 지정 모델 바인더:</span><span class="sxs-lookup"><span data-stu-id="f74b9-257">Custom model binders:</span></span>

- <span data-ttu-id="f74b9-258">상태 코드를 설정하거나 결과를 반환하려고 시도하면 안 됩니다(예를 들어 404 찾을 수 없음).</span><span class="sxs-lookup"><span data-stu-id="f74b9-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="f74b9-259">모델 바인딩이 실패하면 [작업 필터](xref:mvc/controllers/filters) 또는 작업 메서드 자체의 논리에서 오류를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="f74b9-260">작업 메서드에서 반복 코드와 교차 편집 문제를 제거하는 데 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="f74b9-261">일반적으로 문자열을 사용자 지정 형식으로 변환하는 데 사용되지 않습니다. <xref:System.ComponentModel.TypeConverter>가 더 좋은 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="f74b9-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
