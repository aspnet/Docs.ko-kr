---
title: 'ASP.NET Core Blazor 구성'
author: guardrex
description: '앱 설정, 인증, 로깅 구성을 포함하여 Blazor 앱의 구성에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234376"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="07eb5-103">ASP.NET Core Blazor 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="07eb5-104">이 항목은 Blazor WebAssembly에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="07eb5-105">ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/configuration/index>의 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07eb5-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="07eb5-106">Blazor WebAssembly는 기본적으로 앱 설정에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="07eb5-107">앱이 등록한 다른 구성 공급자도 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="07eb5-108">일부 공급자 또는 공급자 기능은 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="07eb5-109">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration): 공급자는 클라이언트 암호 시나리오가 포함된 관리 ID 및 애플리케이션 ID(클라이언트 ID)에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="07eb5-110">서비스에 액세스하는 클라이언트 쪽에서는 클라이언트 암호 보안을 설정할 수 없기 때문에 ASP.NET Core 앱, 특히 Blazor WebAssembly 앱에는 클라이언트 암호를 사용하는 애플리케이션 ID를 권장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="07eb5-111">[Azure 앱 구성 제공자](/azure/azure-app-configuration/quickstart-aspnet-core-app): Blazor WebAssembly 앱이 Azure의 서버에서 실행되지 않기 때문에 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="07eb5-112">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="07eb5-113">**구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="07eb5-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="07eb5-114">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07eb5-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="07eb5-115">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-115">App settings configuration</span></span>

<span data-ttu-id="07eb5-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="07eb5-117"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="07eb5-118">EF Core가 포함된 사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="07eb5-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="07eb5-119"><xref:fundamentals/configuration/index#custom-configuration-provider>에 설명된 EF Core가 포함된 사용자 지정 구성 공급자는 Blazor WebAssembly 앱에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="07eb5-120">`Program.Main`(`Program.cs`)에서 다음 코드와 함께 예제의 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="07eb5-121"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="07eb5-122">메모리 구성 소스</span><span class="sxs-lookup"><span data-stu-id="07eb5-122">Memory Configuration Source</span></span>

<span data-ttu-id="07eb5-123">다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="07eb5-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="07eb5-125"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="07eb5-126">`wwwroot` 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="07eb5-127">이 방법을 사용하는 경우 다음 예제와 같이 기존 <xref:System.Net.Http.HttpClient> 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="07eb5-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="07eb5-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="07eb5-130">인증 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-130">Authentication configuration</span></span>

<span data-ttu-id="07eb5-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="07eb5-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="07eb5-133">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-133">Logging configuration</span></span>

<span data-ttu-id="07eb5-134">[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="07eb5-135">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="07eb5-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-136">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="07eb5-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="07eb5-138">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-138">Host builder configuration</span></span>

<span data-ttu-id="07eb5-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07eb5-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="07eb5-140">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="07eb5-140">Cached configuration</span></span>

<span data-ttu-id="07eb5-141">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="07eb5-142">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="07eb5-143">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="07eb5-144">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="07eb5-145">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 `service-worker.js` 및 `service-worker-assets.js` 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07eb5-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="07eb5-146">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07eb5-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
