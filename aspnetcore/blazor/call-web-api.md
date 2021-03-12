---
title: ASP.NET Core Blazor WebAssembly에서 웹 API 호출
author: guardrex
description: CORS(원본 간 리소스 공유) 요청 만들기를 포함하여 JSON 도우미를 사용하여 Blazor WebAssembly 앱에서 웹 API를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 9ac17e7c22b23ced7a8f12a6ef0d456f6244318b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586763"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="5b779-103">ASP.NET Core Blazor에서 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="5b779-103">Call a web API from ASP.NET Core Blazor</span></span>

> [!NOTE]
> <span data-ttu-id="5b779-104">이 항목은 Blazor WebAssembly에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="5b779-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) 앱은 일반적으로 <xref:System.Net.Http.IHttpClientFactory>를 사용하여 만드는 <xref:System.Net.Http.HttpClient> 인스턴스를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="5b779-106">Blazor Server에 적용되는 지침은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-106">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="5b779-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 앱은 미리 구성된 <xref:System.Net.Http.HttpClient> 서비스를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="5b779-108">Blazor JSON 도우미 또는 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-108">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="5b779-109">Blazor WebAssembly 앱의 <xref:System.Net.Http.HttpClient> 서비스는 원본 서버에 대해 다시 요청을 수행하는 데 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-109">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="5b779-110">이 항목의 지침은 Blazor WebAssembly 앱에만 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-110">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="5b779-111">[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/)([다운로드 방법](xref:index#how-to-download-a-sample)): `BlazorWebAssemblySample` 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="5b779-112">`BlazorWebAssemblySample` 샘플 앱에서 다음 구성 요소를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-112">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="5b779-113">웹 API 호출(`Pages/CallWebAPI.razor`)</span><span class="sxs-lookup"><span data-stu-id="5b779-113">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="5b779-114">HTTP 요청 테스터(`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="5b779-114">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="5b779-115">패키지</span><span class="sxs-lookup"><span data-stu-id="5b779-115">Packages</span></span>

<span data-ttu-id="5b779-116">프로젝트 파일에서 [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-116">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="5b779-117">HttpClient 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="5b779-117">Add the HttpClient service</span></span>

<span data-ttu-id="5b779-118">`Program.Main`에서 아직 없는 경우 <xref:System.Net.Http.HttpClient> 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-118">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="5b779-119">HttpClient 및 JSON 도우미</span><span class="sxs-lookup"><span data-stu-id="5b779-119">HttpClient and JSON helpers</span></span>

<span data-ttu-id="5b779-120">Blazor WebAssembly 앱에서 [`HttpClient`](xref:fundamentals/http-requests)는 원본 서버에 대해 다시 요청을 수행하기 위해 미리 구성된 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-120">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="5b779-121">Blazor Server 앱은 기본적으로 <xref:System.Net.Http.HttpClient> 서비스를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-121">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="5b779-122">[`HttpClient` 팩터리 인프라](xref:fundamentals/http-requests)를 사용하여 앱에 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-122">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="5b779-123"><xref:System.Net.Http.HttpClient> 및 JSON 도우미는 타사 웹 API 엔드포인트를 호출하는 데에도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-123"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="5b779-124"><xref:System.Net.Http.HttpClient>는 브라우저 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 구현되며 동일한 원본 정책 적용을 비롯한 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-124"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="5b779-125">클라이언트의 기본 주소가 원래 서버의 주소로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-125">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="5b779-126">[`@inject`](xref:mvc/views/razor#inject) 지시문을 사용하여 <xref:System.Net.Http.HttpClient> 인스턴스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-126">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="5b779-127">다음 예제에서 Todo 웹 API는 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-127">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="5b779-128">예제는 다음을 저장하는 `TodoItem` 클래스를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-128">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="5b779-129">ID(`Id`, `long`): 항목의 고유한 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-129">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="5b779-130">이름(`Name`, `string`): 항목의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-130">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="5b779-131">상태(`IsComplete`, `bool`): 할 일 항목이 완료되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-131">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="5b779-132">JSON 도우미 메서드는 URI(다음 예제의 웹 API)에 요청을 보내고 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-132">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="5b779-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="5b779-134">다음 코드에서 `todoItems`는 구성 요소에 의해 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-134">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="5b779-135">`GetTodoItems` 메서드는 구성 요소가 렌더링을 완료할 때 트리거됩니다([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="5b779-135">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="5b779-136">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-136">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="5b779-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: JSON 인코딩 콘텐츠를 포함하여 HTTP POST 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="5b779-138">다음 코드에서 구성 요소의 바인딩된 요소는 `newItemName`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-138">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="5b779-139">`AddItem` 메서드는 `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-139">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="5b779-140">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-140">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="5b779-141"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-141">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="5b779-142">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 `ReadFromJsonAsync<T>` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-142">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="5b779-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: JSON 인코딩 콘텐츠를 포함하여 HTTP PUT 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="5b779-144">다음 코드에서 구성 요소의 바인딩된 요소는 `Name` 및 `IsCompleted`에 대한 `editItem` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-144">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="5b779-145">항목의 `Id`는 UI의 다른 부분에서 해당 항목을 선택하고 `EditItem`을 호출하면 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="5b779-146">`SaveItem` 메서드는 Save `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="5b779-147">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-147">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}", editItem);
  }
  ```
  
  <span data-ttu-id="5b779-148"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-148">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="5b779-149">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-149">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="5b779-150"><xref:System.Net.Http>는 HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-150"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="5b779-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>는 웹 API에 HTTP DELETE 요청을 보내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="5b779-152">다음 코드에서 Delete `<button>` 요소는 `DeleteItem` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-152">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="5b779-153">바인딩된 `<input>` 요소는 삭제할 항목의 `id`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-153">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="5b779-154">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-154">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="5b779-155">IHttpClientFactory를 사용하여 명명된 HttpClient</span><span class="sxs-lookup"><span data-stu-id="5b779-155">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="5b779-156"><xref:System.Net.Http.IHttpClientFactory> 서비스 및 명명된 <xref:System.Net.Http.HttpClient>의 구성이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-156"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="5b779-157">프로젝트 파일에서 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-157">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="5b779-158">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5b779-158">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="5b779-159">`FetchData` 구성 요소(`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="5b779-159">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="5b779-160">형식화된 HttpClient</span><span class="sxs-lookup"><span data-stu-id="5b779-160">Typed HttpClient</span></span>

<span data-ttu-id="5b779-161">형식화된 <xref:System.Net.Http.HttpClient>는 앱의 기본값 또는 명명된 <xref:System.Net.Http.HttpClient> 인스턴스를 사용하여 하나 이상의 웹 API 엔드포인트에서 데이터를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="5b779-162">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="5b779-162">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

<span data-ttu-id="5b779-163">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5b779-163">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="5b779-164">구성 요소는 형식화된 <xref:System.Net.Http.HttpClient>를 삽입하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-164">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="5b779-165">`FetchData` 구성 요소(`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="5b779-165">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="5b779-166">Fetch API 요청 옵션이 있는 `HttpClient` 및 `HttpRequestMessage`</span><span class="sxs-lookup"><span data-stu-id="5b779-166">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="5b779-167">Blazor WebAssembly 앱의 WebAssembly에서 실행될 경우 [`HttpClient`](xref:fundamentals/http-requests)([API 설명서](xref:System.Net.Http.HttpClient)) 및 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 요청을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-167">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="5b779-168">예를 들어, HTTP 메서드 및 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-168">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="5b779-169">다음 구성 요소는 서버에서 To Do List API 엔드포인트에 대한 `POST` 요청을 만들고 응답 본문을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-169">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="5b779-170">.NET WebAssembly의 <xref:System.Net.Http.HttpClient> 구현은 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-170">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="5b779-171">Fetch를 사용하면 여러 [요청 관련 옵션](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-171">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="5b779-172">HTTP 페치 요청 옵션은 다음 표에 표시된 <xref:System.Net.Http.HttpRequestMessage> 확장 메서드로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-172">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="5b779-173">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="5b779-173">Extension method</span></span> | <span data-ttu-id="5b779-174">Fetch 요청 속성</span><span class="sxs-lookup"><span data-stu-id="5b779-174">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="5b779-175">보다 일반적인 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 확장 메서드를 사용하여 추가 옵션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-175">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="5b779-176">HTTP 응답은 일반적으로 응답 콘텐츠에서 동기 읽기를 지원할 수 있도록 Blazor WebAssembly 앱에서 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="5b779-177">응답 스트리밍을 지원하도록 설정하려면 요청에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-177">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="5b779-178">원본 간 요청에 자격 증명을 포함하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-178">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="5b779-179">Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="5b779-180">오류 처리</span><span class="sxs-lookup"><span data-stu-id="5b779-180">Handle errors</span></span>

<span data-ttu-id="5b779-181">웹 API와 상호 작용하는 동안 오류가 발생하는 경우 개발자 코드로 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-181">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="5b779-182">예를 들어 <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>에는 `application/json`의 `Content-Type`을 가진 서버 API의 JSON 응답이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-182">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="5b779-183">응답이 JSON 형식이 아닌 경우 콘텐츠 유효성 검사는 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-183">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="5b779-184">다음 예제에서는 날씨 예측 데이터 요청에 대한 URI 엔드포인트의 철자가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-184">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="5b779-185">URI는 `WeatherForecast`가 되어야 하지만 호출에는 `WeatherForcast`(“e” 누락)로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-185">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="5b779-186"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 호출에서는 JSON이 반환되어야 하지만, 서버는 `text/html`의 `Content-Type`을 사용하여 서버에서 처리되지 않은 예외에 대한 HTML을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-186">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="5b779-187">경로를 찾을 수 없고 미들웨어에서 요청에 대한 페이지 또는 보기를 제공할 수 없기 때문에 처리되지 않은 예외가 서버에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-187">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="5b779-188">클라이언트의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>에서 응답 콘텐츠가 JSON이 아닌 것으로 확인되면 <xref:System.NotSupportedException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-188">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="5b779-189">`catch` 블록에서 예외가 catch됩니다. 여기서 사용자 지정 논리는 오류를 기록하거나 사용자에게 친숙한 오류 메시지를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-189">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="5b779-190">앞의 예제는 데모용입니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-190">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="5b779-191">엔드포인트가 없거나 서버에서 처리되지 않은 예외가 발생한 경우에도 JSON을 반환하도록 웹 API 서버 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-191">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="5b779-192">자세한 내용은 <xref:blazor/fundamentals/handle-errors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-192">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="5b779-193">CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="5b779-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="5b779-194">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-194">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="5b779-195">이러한 제한 사항을 *동일 원본 정책* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-195">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5b779-196">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-196">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5b779-197">브라우저에서 원본이 다른 엔드포인트로의 요청을 만들려면 *엔드포인트* 가 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-197">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="5b779-198">[Blazor WebAssembly 샘플 앱(BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/)은 웹 API 호출 구성 요소(`Pages/CallWebAPI.razor`)에서 CORS를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-198">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="5b779-199">Blazor 앱에서 보안 요청을 포함하는 CORS에 대한 자세한 내용은 <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-199">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="5b779-200">ASP.NET Core 앱을 사용하는 CORS에 대한 일반 정보는 <xref:security/cors>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b779-200">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b779-201">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5b779-201">Additional resources</span></span>

* <span data-ttu-id="5b779-202"><xref:blazor/security/webassembly/additional-scenarios>: 보안 웹 API 요청을 만들기 위해 <xref:System.Net.Http.HttpClient> 사용에 대한 검사를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b779-202"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="5b779-203">Kestrel HTTPS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="5b779-203">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="5b779-204">Kestrel HTTPS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="5b779-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [<span data-ttu-id="5b779-205">W3C의 CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="5b779-205">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
