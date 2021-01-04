---
title: ASP.NET Core Blazor 구성
author: guardrex
description: 앱 설정, 인증, 로깅 구성을 포함하여 Blazor 앱의 구성에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485969"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="3d5b1-103">ASP.NET Core Blazor 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="3d5b1-104">이 항목은 Blazor WebAssembly에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="3d5b1-105">ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/configuration/index>의 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3d5b1-106">Blazor WebAssembly는 기본적으로 다음 앱 설정 파일에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="3d5b1-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="3d5b1-108">`wwwroot/appsettings.{ENVIRONMENT}.json`. 여기서 `{ENVIRONMENT}` 자리 표시자는 앱의 [런타임 환경](xref:fundamentals/environments)입니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3d5b1-109">앱에서 등록한 다른 구성 공급자가 구성을 제공할 수도 있지만, 일부 공급자나 공급자 기능은 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="3d5b1-110">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration): 공급자는 클라이언트 암호 시나리오가 포함된 관리 ID 및 애플리케이션 ID(클라이언트 ID)에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="3d5b1-111">Azure Key Vault 서비스에 액세스하는 클라이언트 쪽에서는 클라이언트 암호 보안을 설정할 수 없기 때문에 ASP.NET Core 앱, 특히 Blazor WebAssembly 앱에는 클라이언트 암호를 사용하는 애플리케이션 ID를 권장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="3d5b1-112">[Azure 앱 구성 제공자](/azure/azure-app-configuration/quickstart-aspnet-core-app): Blazor WebAssembly 앱이 Azure의 서버에서 실행되지 않기 때문에 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="3d5b1-113">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3d5b1-114">**Blazor WebAssembly 앱의 구성에 앱 비밀, 자격 증명, 기타 중요한 데이터를 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="3d5b1-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="3d5b1-115">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="3d5b1-116">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-116">App settings configuration</span></span>

<span data-ttu-id="3d5b1-117">앱 설정 파일의 구성이 기본적으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="3d5b1-118">다음 예제에서는 UI 구성 값이 앱 설정 파일에 저장되어 있고 Blazor 프레임워크에서 자동으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="3d5b1-119">해당 값을 구성 요소에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-119">The value is read by a component.</span></span>

<span data-ttu-id="3d5b1-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="3d5b1-121"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="3d5b1-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="3d5b1-123">`wwwroot` 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="3d5b1-124">다음 예제에서는 구성 파일(`cars.json`)을 앱의 구성으로 읽어옵니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="3d5b1-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="3d5b1-126"><xref:Microsoft.Extensions.Configuration?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="3d5b1-127">`Program.cs`의 `Program.Main`에서 클라이언트를 사용하여 파일을 읽도록 기존 <xref:System.Net.Http.HttpClient> 서비스 등록을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="3d5b1-128">EF Core가 포함된 사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="3d5b1-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="3d5b1-129"><xref:fundamentals/configuration/index#custom-configuration-provider>에 설명된 EF Core가 포함된 사용자 지정 구성 공급자는 Blazor WebAssembly 앱에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="3d5b1-130">Blazor WebAssembly 앱에 로드된 데이터베이스 연결 문자열과 데이터베이스는 안전하지 않으며 중요한 데이터를 저장하는 데 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="3d5b1-131">[`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) 및 [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)에 대한 패키지 참조를 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="3d5b1-132"><xref:fundamentals/configuration/index#custom-configuration-provider>에 설명된 EF Core 구성 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="3d5b1-133"><xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> 및 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="3d5b1-134">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="3d5b1-135"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="3d5b1-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-136">`Pages/EFCoreConfig.razor`:</span></span>

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="3d5b1-137">메모리 구성 소스</span><span class="sxs-lookup"><span data-stu-id="3d5b1-137">Memory Configuration Source</span></span>

<span data-ttu-id="3d5b1-138">다음 예제에서는 `Program.Main`에서 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="3d5b1-139"><xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="3d5b1-140">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-140">In `Program.Main` of `Program.cs`:</span></span>

```csharp
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

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="3d5b1-141"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="3d5b1-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-142">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="3d5b1-143"><xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>을 사용하여 C# 코드에서 구성 섹션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3d5b1-144">다음 예제에서는 앞의 예제에서 구성의 `wheels` 섹션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="3d5b1-145">인증 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-145">Authentication configuration</span></span>

<span data-ttu-id="3d5b1-146">앱 설정 파일에 인증 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="3d5b1-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="3d5b1-148">`Program.Main`에서 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType>를 사용하여 Identity 공급자의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="3d5b1-149">다음 예제에서는 [OIDC 공급자](xref:blazor/security/webassembly/standalone-with-authentication-library)의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="3d5b1-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="3d5b1-151">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-151">Logging configuration</span></span>

<span data-ttu-id="3d5b1-152">[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)에 대한 패키지 참조를 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="3d5b1-153">앱 설정 파일에서 로깅 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="3d5b1-154">로깅 구성은 `Program.Main`에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="3d5b1-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="3d5b1-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="3d5b1-156"><xref:Microsoft.Extensions.Logging?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="3d5b1-157">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="3d5b1-158">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-158">Host builder configuration</span></span>

<span data-ttu-id="3d5b1-159">`Program.Main`의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType>에서 호스트 빌더 구성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="3d5b1-160">`Program.cs`의 `Program.Main`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="3d5b1-161">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="3d5b1-161">Cached configuration</span></span>

<span data-ttu-id="3d5b1-162">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3d5b1-163">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3d5b1-164">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3d5b1-165">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3d5b1-166">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 `service-worker.js` 및 `service-worker-assets.js` 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3d5b1-167">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3d5b1-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
