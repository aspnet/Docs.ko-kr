---
title: ASP.NET Core 호스트 및 배포 Blazor WebAssembly
author: guardrex
description: ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 63954bd2fbb8fdb2e347d552a10adc52263c3ad6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900715"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core 호스트 및 배포 Blazor WebAssembly

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) 및 [Safia Abdalla](https://safia.rocks)

[Blazor WebAssembly 호스팅 모델](xref:blazor/hosting-models#blazor-webassembly)을 사용하면 다음과 같이 실행됩니다.

* Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 병렬로 브라우저에 다운로드됩니다.
* 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.

다음 배포 전략이 지원됩니다.

* Blazor 앱은 ASP.NET Core 앱에서 제공됩니다. 이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.
* Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다. 이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.

## <a name="compression"></a>압축

Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다. 다음 압축 알고리즘이 사용됩니다.

* [Brotli](https://tools.ietf.org/html/rfc7932)(최고 수준)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor는 호스트를 사용하여 적절한 압축 파일을 제공합니다. ASP.NET Core 호스트 프로젝트를 사용하는 경우 호스트 프로젝트는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 제공할 수 있습니다. Blazor WebAssembly 독립 실행형 앱을 호스트하는 경우 정적으로 압축된 파일이 제공되도록 추가 작업이 필요할 수도 있습니다.

* IIS `web.config` 압축 구성에 대해서는 [IIS: Brotli 및 Gzip 압축](#brotli-and-gzip-compression) 섹션을 참조하세요. 
* GitHub 페이지와 같이 정적으로 압축된 파일 콘텐츠 협상을 지원하지 않는 정적 호스팅 솔루션에서 호스트하는 경우 Brotli로 압축된 파일을 가져와 디코딩하는 앱을 구성하는 것이 좋습니다.

  * [google/brotli GitHub 리포지토리](https://github.com/google/brotli)에서 JavaScript Brotli 디코더를 가져옵니다. 2020년 9월부터 디코더 파일의 이름은 `decode.js`이며 리포지토리의 [`js` 폴더](https://github.com/google/brotli/tree/master/js)에 있습니다.
  
    > [!NOTE]
    > 회귀는 [google/brotli GitHub 리포지토리](https://github.com/google/brotli)에서 `decode.js` 스크립트(`decode.min.js`)의 축소 버전으로 제공됩니다. [Window.BrotliDecode가 decode.min.js에서 설정되지 않음(google/brotli #844)](https://github.com/google/brotli/issues/844) 문제가 해결되기 전까지는 직접 스크립트를 축소하거나 [npm 패키지](https://www.npmjs.com/package/brotli)를 사용합니다. 이 섹션의 예제 코드는 **축소되지 않은** 버전의 스크립트를 사용합니다.

  * 디코더를 사용하도록 앱을 업데이트합니다. `wwwroot/index.html`에서 닫는 `<body>` 태그 내부의 태그를 다음과 같이 변경합니다.
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
압축을 해제하려면 앱의 프로젝트 파일에 `BlazorEnableCompression` MSBuild 속성을 추가하고 값을 `false`로 설정합니다.

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

명령 셸에서 다음 구문을 사용하여 `BlazorEnableCompression` 속성을 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에 전달할 수 있습니다.

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>올바른 라우팅을 위해 URL 다시 생성

Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor Server에서 호스트된 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다. 다음 두 구성 요소를 사용하는 Blazor WebAssembly 앱을 살펴보겠습니다.

* `Main.razor`: 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.
* `About.razor`: `About` 구성 요소입니다.

브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):

1. 브라우저가 요청을 합니다.
1. 기본 페이지(일반적으로 `index.html`)가 반환됩니다.
1. `index.html`이 앱을 부트스트랩합니다.
1. Blazor의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.

Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다. ‘Blazor WebAssembly 앱 내’의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다. 라우터가 내부적으로 요청을 처리합니다.

브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다. 앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.

브라우저는 인터넷 기반 호스트에 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 `index.html` 페이지에 다시 써야 합니다. `index.html`이 반환되는 경우 앱의 Blazor 라우터가 넘겨받아 올바른 리소스로 응답합니다.

IIS 서버에 배포하는 경우 앱의 게시된 `web.config` 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다. 자세한 내용은 [IIS](#iis) 섹션을 참조하세요.

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core를 사용하여 호스트된 배포

‘호스트된 배포’는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.

클라이언트 Blazor WebAssembly 앱이 서버 앱의 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다. 두 앱이 함께 배포됩니다. ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다. 호스트된 배포의 경우 Visual Studio는 **`Hosted`** (`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)을 포함합니다.

ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.

Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>여러 Blazor WebAssembly 앱을 사용하여 호스트된 배포

### <a name="app-configuration"></a>앱 구성

여러 Blazor WebAssembly 앱을 제공하도록 호스트된 Blazor 솔루션을 구성하려면 다음을 수행합니다.

* 호스트된 기존 Blazor 솔루션을 사용하거나 Blazor 호스트 프로젝트 템플릿에서 새 솔루션을 만듭니다.

* 클라이언트 앱의 프로젝트 파일에서 값이 `FirstApp`인 `<PropertyGroup>`에 `<StaticWebAssetBasePath>` 속성을 추가하여 프로젝트의 정적 자산에 대한 기본 경로를 설정합니다.

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* 두 번째 클라이언트 앱을 솔루션에 추가합니다.

  * `SecondClient`라는 폴더를 솔루션의 폴더에 추가합니다.
  * Blazor WebAssembly 프로젝트 템플릿의 `SecondClient` 폴더에 `SecondBlazorApp.Client`라는 Blazor WebAssembly 앱을 만듭니다.
  * 앱의 프로젝트 파일에서:

    * `SecondApp` 값을 사용하여 `<PropertyGroup>`에 `<StaticWebAssetBasePath>` 속성을 추가합니다.

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * `Shared` 프로젝트에 프로젝트 참조를 추가합니다.

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      자리 표시자 `{SOLUTION NAME}`은 솔루션의 이름입니다.

* 서버 앱의 프로젝트 파일에서 추가된 클라이언트 앱에 대한 프로젝트 참조를 만듭니다.

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* 서버 앱의 `Properties/launchSettings.json` 파일에서 포트 5001 및 5002의 클라이언트 앱에 액세스하도록 Kestrel 프로필(`{SOLUTION NAME}.Server`)의 `applicationUrl`을 구성합니다.

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* 서버 앱의 `Startup.Configure` 메서드(`Startup.cs`)에서 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>에 대한 호출 뒤에 표시되는 다음 줄을 제거합니다.

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  클라이언트 앱에 요청을 매핑하는 미들웨어를 추가합니다. 다음 예제는 다음 경우에 실행되도록 미들웨어를 구성합니다.

  * 요청 포트가 원래 클라이언트 앱의 경우 5001이고 추가된 클라이언트 앱의 경우 5002입니다.
  * 요청 호스트가 원래 클라이언트 앱의 경우 `firstapp.com`이고 추가된 클라이언트 앱의 경우 `secondapp.com`입니다.

    > [!NOTE]
    > 이 섹션에 표시된 예제에는 다음에 대한 추가 구성이 필요합니다.
    >
    > * 예제 호스트 도메인, `firstapp.com` 및 `secondapp.com`에 있는 앱에 액세스.
    > * TLS 보안(HTTPS)을 사용하도록 설정하는 클라이언트 앱 인증서.
    >
    > 필요한 구성은 이 문서의 범위를 벗어나며 솔루션이 호스트되는 방법에 따라 달라집니다. 자세한 내용은 [호스트 및 배포 문서](xref:host-and-deploy/index)를 참조하세요.

  이전에 줄이 제거된 곳에 다음 코드를 넣습니다.

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* 서버 앱의 일기 예보 컨트롤러(`Controllers/WeatherForecastController.cs`)에서 기존 `WeatherForecastController` 경로(`[Route("[controller]")]`)를 다음 경로로 바꿉니다.

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  이전에 서버 앱의 `Startup.Configure` 메서드에 추가된 미들웨어는 `/WeatherForecast`로 들어오는 요청을 포트(5001/5002) 또는 도메인(`firstapp.com`/`secondapp.com`)에 따라 `/FirstApp/WeatherForecast` 또는 `/SecondApp/WeatherForecast`로 수정합니다. 서버 앱에서 클라이언트 앱으로 날씨 데이터를 반환하려면 이전 컨트롤러 경로가 필요합니다.

### <a name="static-assets-and-class-libraries"></a>정적 자산 및 클래스 라이브러리

정적 자산에는 다음 방법을 사용합니다.

* 자산이 클라이언트 앱의 `wwwroot` 폴더에 있는 경우 정상적으로 해당 경로를 제공합니다.

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* 자산이 [Razor 클래스 라이브러리(RCL)](xref:blazor/components/class-libraries)의 `wwwroot` 폴더에 있는 경우 [RCL 문서](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)의 지침에 따라 클라이언트 앱에서 정적 자산을 참조합니다.

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

클래스 라이브러리에 의해 클라이언트 앱에 제공된 구성 요소는 정상적으로 참조됩니다. 구성 요소에 스타일시트 또는 JavaScript 파일이 필요한 경우 다음 방법 중 하나를 사용하여 정적 자산을 가져옵니다.

* 클라이언트 앱의 `wwwroot/index.html` 파일은 정적 자산에 연결(`<link>`)할 수 있습니다.
* 구성 요소는 프레임워크의 [`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하여 정적 자산을 가져올 수 있습니다.

다음 예제에서는 앞의 방법을 보여 줍니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

클래스 라이브러리에 의해 클라이언트 앱에 제공된 구성 요소는 정상적으로 참조됩니다. 구성 요소에 스타일시트 또는 JavaScript 파일이 필요한 경우 클라이언트 앱의 `wwwroot/index.html` 파일은 올바른 정적 자산 링크를 포함해야 합니다. 다음 예제에서는 이러한 방법을 보여 줍니다.

::: moniker-end

클라이언트 앱 중 하나에 다음 `Jeep` 구성 요소를 추가합니다. `Jeep` 구성 요소는 다음을 사용합니다.

* 클라이언트 앱의 `wwwroot` 폴더(`jeep-cj.png`)에 있는 이미지.
* [추가된 Razor 구성 요소 라이브러리](xref:blazor/components/class-libraries)(`JeepImage`) `wwwroot` 폴더(`jeep-yj.png`)의 이미지.
* 예제 구성 요소(`Component1`)는 `JeepImage` 라이브러리가 솔루션에 추가될 때 RCL 프로젝트 템플릿에 의해 자동으로 만들어집니다.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> 이미지를 소유하고 있지 않다면 차량 이미지를 공개적으로 게시하지 **마세요**. 게시할 경우 저작권 침해의 위험이 있습니다.

::: moniker range=">= aspnetcore-5.0"

라이브러리의 `jeep-yj.png` 이미지를 라이브러리의 `Component1` 구성 요소(`Component1.razor`)에 추가할 수도 있습니다. 클라이언트 앱의 페이지에 `my-component` CSS 클래스를 제공하려면 프레임워크의 [`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하여 라이브러리의 스타일시트에 연결합니다.

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

[`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하는 대신 클라이언트 앱의 `wwwroot/index.html` 파일에서 스타일시트를 로드할 수 있습니다. 이 방법을 사용하면 클라이언트 앱의 모든 구성 요소에서 스타일시트를 사용할 수 있습니다.

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

라이브러리의 `jeep-yj.png` 이미지를 라이브러리의 `Component1` 구성 요소(`Component1.razor`)에 추가할 수도 있습니다.

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

클라이언트 앱의 `wwwroot/index.html` 파일은 추가된 다음 `<link>` 태그를 사용하여 라이브러리의 스타일시트를 요청합니다.

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

클라이언트 앱의 `NavMenu` 구성 요소(`Shared/NavMenu.razor`)에 `Jeep` 구성 요소에 대한 탐색을 추가합니다.

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

RCL에 대한 자세한 내용은 다음을 참조하세요.

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>독립 실행형 배포

‘독립 실행형 배포’는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다. 모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.

독립 실행형 배포 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 게시됩니다.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly 앱은 [IIS](#iis)에서 앱을 호스트하는 Windows의 Azure App Services에 배포할 수 있습니다.

Linux용 Azure App Service에 독립 실행형 Blazor WebAssembly 앱 배포는 현재 지원되지 않습니다. 지금은 앱을 호스트하는 Linux 서버 이미지를 사용할 수 없습니다. 이 시나리오를 사용할 수 있도록 하는 작업이 진행 중입니다.

### <a name="iis"></a>IIS

IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다. Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.

게시된 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 생성됩니다. 웹 서버 또는 호스팅 서비스에서 `publish` 폴더의 콘텐츠를 호스트합니다.

#### <a name="webconfig"></a>web.config

Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 `web.config` 파일이 생성됩니다.

* 다음 파일 확장명에 대해 MIME 형식을 설정합니다.
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* 다음 MIME 형식에 대해 HTTP 압축을 사용합니다.
  * `application/octet-stream`
  * `application/wasm`
* URL 재작성 모듈 규칙을 설정합니다.
  * 앱의 정적 자산이 상주하는 하위 디렉터리(`wwwroot/{PATH REQUESTED}`)를 제공합니다.
  * 파일이 아닌 자산 요청이 정적 자산 폴더에 있는 앱의 기본 문서(`wwwroot/index.html`)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.
  
#### <a name="use-a-custom-webconfig"></a>사용자 지정 web.config 사용

사용자 지정 `web.config` 파일을 사용하려면 사용자 지정 `web.config` 파일을 프로젝트 폴더의 루트에 배치하고 프로젝트를 게시합니다.

#### <a name="install-the-url-rewrite-module"></a>URL 재작성 모듈 설치

URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다. 이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다. 이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다. 웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.

1. 로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다. 영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다. 다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.
1. 설치 관리자를 서버에 복사합니다. 설치 관리자를 실행합니다. **설치** 버튼을 선택하고 사용 조건에 동의합니다. 설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.

#### <a name="configure-the-website"></a>웹 사이트 구성

웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다. 이 폴더는 다음을 포함합니다.

* 필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 `web.config` 파일
* 앱의 정적 자산 폴더입니다.

#### <a name="host-as-an-iis-sub-app"></a>IIS 하위 앱으로 호스트

독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.

* 상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.

  Blazor 앱의 게시된 `web.config` 파일에 `<handlers>` 섹션을 추가하여 파일에서 처리기를 제거합니다.

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:blazor/host-and-deploy/index#app-base-path)에 더하여 수행됩니다. 앱의 `index.html` 파일에 있는 앱 기본 경로를 IIS에서 하위 앱을 구성할 때 사용되는 IIS 별칭으로 설정합니다.

#### <a name="brotli-and-gzip-compression"></a>Brotli 및 Gzip 압축

`web.config`를 통해 IIS를 구성하여 Brotli 또는 Gzip 압축 Blazor 자산을 제공할 수 있습니다. 예제 구성은 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true)을 참조합니다.

#### <a name="troubleshooting"></a>문제 해결

500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다. 모듈이 설치되지 않은 경우 IIS가 `web.config` 파일을 구문 분석할 수 없습니다. 그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.

IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다. 사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.

스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:

* **인덱스 문서 이름**을 `index.html`로 설정합니다.
* **오류 문서 경로**를 `index.html`로 설정합니다. Razor 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob 서비스에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다. 이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다. `index.html` BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.

파일의 `Content-Type` 헤더에 부적절한 MIME 형식으로 인해 런타임에 파일이 로드되지 않을 경우 다음 작업 중 하나를 수행합니다.

* 파일이 배포될 때 올바른 MIME 형식(`Content-Type` 헤더)을 설정하도록 도구를 구성합니다.
* 앱이 배포된 후 파일에 대한 MIME 형식(`Content-Type` 헤더)을 변경합니다.

  각 파일의 스토리지 탐색기(Azure Portal)에서
  
  1. 파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.
  1. **ContentType**을 설정하고 **저장** 단추를 선택합니다.

자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.

### <a name="nginx"></a>Nginx

다음 `nginx.conf` 파일은 Nginx가 디스크에서 대응하는 파일을 찾을 수 없을 때마다 `index.html` 파일을 보내도록 구성하는 방법을 보여 주기 위해 단순화되었습니다.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

[`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req)를 사용하여 [NGINX 버스트 속도 한도](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)를 설정하는 경우 Blazor WebAssembly 앱에서 수행하는 비교적 많은 수의 요청을 수용하기 위해 큰 `burst` 매개 변수 값이 필요할 수 있습니다. 처음에는 값을 60 이상으로 설정합니다.

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

브라우저 개발자 도구 또는 네트워크 트래픽 도구에서 요청이 ‘503 - 서비스를 사용할 수 없음’ 상태 코드를 수신하는 것으로 확인되는 경우 값을 늘립니다.

프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.

### <a name="nginx-in-docker"></a>Docker의 Nginx

Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다. Dockerfile을 업데이트하여 `nginx.config` 파일을 컨테이너에 복사합니다.

다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

CentOS 7 이상에 Blazor WebAssembly 앱을 배포하려면 다음을 수행합니다.

1. Apache 구성 파일을 만듭니다. 다음 예제는 단순화된 구성 파일입니다(`blazorapp.config`).

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.

1. 앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.

1. Apache 서비스를 다시 시작합니다.

자세한 내용은 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)을 참조하세요.

### <a name="github-pages"></a>GitHub 페이지

URL 다시 쓰기를 처리하려면 `index.html` 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 `wwwroot/404.html` 파일을 추가합니다. 예제는 [SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)를 참조하세요.

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [라이브 사이트](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

조직 사이트 대신 프로젝트 사이트를 사용하는 경우 `wwwroot/index.html`의 `<base>` 태그를 업데이트합니다. `href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `/my-repository/`). [SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)에서 기본 `href`는 [`.github/workflows/main.yml` 구성 파일](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)에 의해 게시 시 업데이트됩니다.

> [!NOTE]
> [SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)는 .NET Foundation 또는 Microsoft에서 소유, 유지 관리 또는 지원하지 않습니다.

## <a name="host-configuration-values"></a>호스트 구성 값

[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.

### <a name="content-root"></a>콘텐츠 루트

`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다. 다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다. 앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>경로 기준

`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.) 다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다. 자세한 내용은 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)를 참조하세요.

> [!IMPORTANT]
> `<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다. 앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다. 앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.

* 명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다. 앱의 디렉터리에서 다음을 실행합니다.

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다. 앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다. Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>트리머 구성

Blazor는 각 릴리스 빌드에 IL(중간 언어) 트리밍을 수행하여 출력 어셈블리에서 필요 없는 IL을 제거합니다. 자세한 내용은 <xref:blazor/host-and-deploy/configure-trimmer>를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>링커 구성

Blazor는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다. 자세한 내용은 <xref:blazor/host-and-deploy/configure-linker>를 참조하세요.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>사용자 지정 부팅 리소스 로드

`loadBootResource` 함수로 Blazor WebAssembly 앱을 초기화하여 기본 제공 부팅 리소스 로드 메커니즘을 재정의할 수 있습니다. 다음 시나리오에 `loadBootResource`를 사용합니다.

* 사용자가 CDN에서 표준 시간대 데이터 또는 `dotnet.wasm` 같은 정적 리소스를 로드하도록 합니다.
* HTTP 요청을 사용하여 압축된 어셈블리를 로드하고 서버에서 압축된 콘텐츠 페치를 지원하지 않는 호스트의 경우 클라이언트에서 압축을 풉니다.
* 각 `fetch` 요청을 새 이름으로 리디렉션하여 리소스 별칭을 다른 이름으로 지정합니다.

`loadBootResource` 매개 변수는 다음 표에 나와 있습니다.

| 매개 변수    | Description |
| ------------ | ----------- |
| `type`       | 리소스 형식입니다. 허용되는 형식: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` |
| `name`       | 리소스의 이름입니다. |
| `defaultUri` | 리소스의 상대 또는 절대 URI입니다. |
| `integrity`  | 응답에서 예상되는 콘텐츠를 나타내는 무결성 문자열입니다. |

`loadBootResource`는 로드 프로세스를 재정의하기 위해 다음 항목을 반환합니다.

* URI 문자열. 다음 예제(`wwwroot/index.html`)에서 다음 파일은 `https://my-awesome-cdn.com/`의 CDN에서 제공됩니다.

  * `dotnet.*.js`
  * `dotnet.wasm`
  * 표준 시간대 데이터

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. 헤더에서 `integrity` 매개 변수를 전달하여 기본 무결성 검사 동작을 유지합니다.

  다음 예제(`wwwroot/index.html`)에서는 사용자 지정 HTTP 헤더를 아웃바운드 요청에 추가하고 `integrity` 매개 변수를 `fetch` 호출에 전달합니다.
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, 기본 로드 동작이 발생합니다.

외부 소스는 브라우저에서 원본 간 리소스 로드를 허용하는 데 필요한 CORS 헤더를 반환해야 합니다. 일반적으로 CDN은 필요한 헤더를 기본적으로 제공합니다.

사용자 지정 동작의 경우에만 형식을 지정해야 합니다. `loadBootResource`에 지정되지 않은 형식은 기본 로드 동작에 따라 프레임워크에서 로드됩니다.

## <a name="change-the-filename-extension-of-dll-files"></a>DLL 파일의 파일 이름 확장명 변경

앱 게시 `.dll` 파일의 파일 이름 확장명을 변경해야 하는 경우 이 섹션의 지침을 따르세요.

앱을 게시한 후 셸 스크립트 또는 DevOps 빌드 파이프라인을 사용하여 다른 파일 확장명을 사용하도록 `.dll` 파일의 이름을 바꿉니다. 앱 게시 출력(예: `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`)의 `wwwroot` 디렉터리에 있는 `.dll` 파일을 대상으로 합니다.

다음 예제에서는 `.bin` 파일 확장명을 사용하도록 `.dll` 파일의 이름이 바뀝니다.

Windows에서:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Linux 또는 macOS에서:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
`.bin` 이외의 다른 파일 확장명을 사용하려면 이전 명령에서 `.bin`을 바꿉니다.

압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 처리하려면 다음 방법 중 하나를 채택합니다.

* 압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 제거합니다. 압축은 이 접근 방법으로 사용하지 않도록 설정됩니다.
* 업데이트된 `blazor.boot.json` 파일을 다시 압축합니다.

위의 지침은 서비스 작업자 자산을 사용 중인 경우에도 적용됩니다. `wwwroot/service-worker-assets.js.br` 및 `wwwroot/service-worker-assets.js.gz`를 제거하거나 다시 압축합니다. 그렇지 않으면 브라우저에서 파일 무결성 검사가 실패합니다.

다음 Windows 예제에서는 프로젝트의 루트에 배치된 PowerShell 스크립트를 사용합니다.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

프로젝트 파일에서 스크립트는 앱을 게시한 후에 실행됩니다.

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> 동일한 어셈블리의 이름을 바꾸고 로드를 지연하는 경우 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>의 지침을 참조하세요.

## <a name="resolve-integrity-check-failures"></a>무결성 검사 실패 해결

Blazor WebAssembly는 앱의 시작 파일을 다운로드할 때 응답에 대한 무결성 검사를 수행하도록 브라우저에 지시합니다. `blazor.boot.json` 파일의 정보를 사용하여 `.dll`, `.wasm` 및 기타 파일에 대해 예상되는 SHA-256 해시 값을 지정합니다. 이 기능은 다음과 같은 이유로 유용합니다.

* 사용자가 애플리케이션 파일을 다운로드하는 동안 새 배포가 웹 서버에 적용되는 경우와 같이 일관되지 않은 파일 세트를 로드할 위험이 없는지 확인합니다. 일관되지 않은 파일로 인해 정의되지 않은 동작이 발생할 수 있습니다.
* 사용자의 브라우저가 일관되지 않거나 잘못된 응답을 캐시하지 않는지 확인하여 페이지를 수동으로 새로 고치는 경우에도 앱을 시작하지 못하도록 할 수 있습니다.
* 응답을 안전하게 캐시하며 예상된 SHA-256 해시가 변경될 때까지 서버 쪽 변경 내용을 확인하지 않도록 설정하므로 후속 페이지 로드는 더 적은 요청을 포함하여 훨씬 더 빠르게 완료됩니다.

웹 서버가 예상된 SHA-256 해시와 일치하지 않는 응답을 반환하는 경우에는 브라우저의 개발자 콘솔에 다음과 같은 오류가 표시됩니다.

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

대부분의 경우에는 무결성 검사 자체에서 발생하는 문제가 ‘아닙니다’. 대신 일부 다른 문제가 있음을 의미하며 무결성 검사는 다른 문제에 관한 경고를 표시합니다.

### <a name="diagnosing-integrity-problems"></a>무결성 문제 진단

앱이 빌드되면 생성된 `blazor.boot.json` 매니페스트는 빌드 출력이 생성될 때 부팅 리소스(예: `.dll`, `.wasm` 및 기타 파일)의 SHA-256 해시를 설명합니다. `blazor.boot.json`의 SHA-256 해시가 브라우저에 배달된 파일과 일치하는 경우 무결성 검사가 통과합니다.

이 작업이 실패하는 일반적인 이유는 다음과 같습니다.

 * 웹 서버의 응답은 브라우저가 요청한 파일이 아닌 오류(예: ‘404 - 찾을 수 없음’ 또는 ‘500 - 내부 서버 오류’)입니다.  이는 브라우저에서 응답 실패가 아닌 무결성 검사 실패로 보고됩니다.
 * 무엇인가 브라우저에 대한 파일의 빌드와 제공 사이에 파일 콘텐츠를 변경했습니다. 이 문제가 발생할 수 있는 경우는 다음과 같습니다.
   * 사용자 또는 빌드 도구가 빌드 출력을 수동으로 수정하는 경우.
   * 배포 프로세스의 일부 측면이 파일을 수정한 경우. 예를 들어 Git 기반 배포 메커니즘을 사용하는 경우 파일을 Windows에서 커밋하고 Linux에서 체크 아웃하는 경우 Git에서는 Windows 스타일 줄 끝을 Unix 스타일 줄 끝으로 투명하게 변환합니다. 파일 줄 끝을 변경하면 SHA-256 해시가 변경됩니다. 이 문제를 방지하려면 [`.gitattributes`를 사용하여 빌드 아티팩트를 `binary` 파일로 처리](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)하는 것이 좋습니다.
   * 웹 서버는 파일 콘텐츠를 제공하는 동안 수정합니다. 예를 들어 일부 CDN(Content Delivery Network)은 자동으로 HTML을 [축소](xref:client-side/bundling-and-minification#minification)함으로써 수정합니다. 관련 기능을 사용하지 않도록 설정해야 할 수 있습니다.

어떤 기능이 해당 사례에서 적용되는지 진단하려면:

 1. 오류 메시지를 읽어서 어떤 파일이 오류를 트리거하는지 확인합니다.
 1. 브라우저의 개발자 도구를 열고 ‘네트워크’ 탭을 확인합니다. 필요한 경우 페이지를 다시 로드하여 요청 및 응답 목록을 확인합니다. 해당 목록에서 오류를 트리거하는 파일을 찾습니다.
 1. 응답의 HTTP 상태 코드를 확인합니다. 서버가 *200 - OK*(또는 또 다른 2xx 상태 코드) 이외의 값을 반환하면 진단해야 하는 서버 쪽 문제가 있는 것입니다. 예를 들어 상태 코드 403은 권한 부여 문제가 있음을 의미하는 반면, 상태 코드 500은 서버가 지정되지 않은 방식으로 실패함을 의미합니다. 서버 쪽 로그를 참조하여 앱을 진단하고 수정합니다.
 1. 리소스에 대한 상태 코드가 *200-OK*인 경우 브라우저 개발자 도구에서 응답 콘텐츠를 확인하고 콘텐츠가 예상 데이터와 일치하는지 확인합니다. 예를 들어 일반적인 문제는 요청이 다른 파일에 대해서도 `index.html` 데이터를 반환하도록 라우팅을 잘못 구성하는 것입니다. `.wasm` 요청에 대한 응답이 WebAssembly 이진 파일이고 `.dll` 요청에 대한 응답이 .NET 어셈블리 이진 파일인지 확인합니다. 그렇지 않으면 진단해야 할 서버 쪽 라우팅 문제가 있는 것입니다.

서버가 올바른 것 같은 데이터를 반환하고 있는지 확인하는 경우 파일의 빌드와 제공 사이에 콘텐츠를 수정하는 다른 항목이 있어야 합니다. 이를 조사하려면:

 * 파일이 빌드된 후 파일을 수정 중인 경우 빌드 도구 체인 및 배포 메커니즘을 검토합니다. 여기에 해당하는 예제는 앞에서 설명한 대로 Git이 파일 줄 끝을 변환하는 경우입니다.
 * 응답을 동적으로 수정하도록(예: HTML 축소 시도) 설정된 경우 웹 서버 또는 CDN 구성을 검토합니다. 압축을 푼 후 결과에 영향을 주지 않으므로 웹 서버가 HTTP 압축을 구현해도 괜찮습니다(예: `content-encoding: br` 또는 `content-encoding: gzip` 반환). 그러나 웹 서버가 압축되지 않은 데이터를 수정하는 것은 괜찮지 ‘않습니다’.

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>비 PWA 앱에 대한 무결성 검사 사용 안 함

대부분의 경우 무결성 검사를 사용하지 않도록 설정하지 마세요. 무결성 검사를 사용하지 않으면 예기치 않은 응답을 발생시킨 근본적인 문제가 해결되지 않아 앞서 언급한 이점을 얻을 수 없습니다.

웹 서버를 사용하여 일관된 응답을 반환할 수 없고 무결성 검사를 사용하지 않도록 설정하는 것 외에 선택 사항이 없는 경우가 있습니다. 무결성 검사를 사용하지 않도록 설정하려면 Blazor WebAssembly 프로젝트의 `.csproj` 파일에서 속성 그룹에 다음을 추가합니다.

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

또한, `BlazorCacheBootResources` 속성은 SHA-256 해시의 정확성을 기대할 수 없음을 나타내기 때문에 `.dll`, `.wasm` 및 SHA-256 해시 기반 기타 파일을 캐시하는 Blazor의 기본 동작을 사용하지 않도록 설정합니다. 이 설정을 사용하는 경우에도 브라우저의 일반 HTTP 캐시는 해당 파일을 캐시할 수 있지만 이 상황이 발생하는지는 웹 서버 구성 및 해당 구성이 제공하는 `cache-control` 헤더에 따라 달라집니다.

> [!NOTE]
> `BlazorCacheBootResources` 속성은 [PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)에 대한 무결성 검사를 사용하지 않도록 설정하지 않습니다. PWA 관련 지침은 [PWA에 대한 무결성 검사 사용 안 함](#disable-integrity-checking-for-pwas) 섹션을 참조하세요.

### <a name="disable-integrity-checking-for-pwas"></a>PWA에 대한 무결성 검사 사용 안 함

Blazor의 PWA(프로그레시브 웹 애플리케이션) 템플릿에는 오프라인에서 사용하기 위해 애플리케이션 파일을 페치하고 저장해야 하는 제안된 `service-worker.published.js` 파일이 포함됩니다. 이는 일반적인 앱 시작 메커니즘과 별도의 프로세스이며 자체적인 별도의 무결성 검사 논리를 포함합니다.

`service-worker.published.js` 파일 내에 다음 줄이 있습니다.

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

무결성 검사를 사용하지 않으려면 줄을 다음으로 변경하여 `integrity` 매개 변수를 제거합니다.

```javascript
.map(asset => new Request(asset.url));
```

또한 무결성 검사를 사용하지 않도록 설정하면 무결성 검사를 통해 제공되는 안전 보증이 손실됩니다. 예를 들어 사용자의 브라우저가 새 버전을 배포하는 바로 그 순간에 앱을 캐시 중인 경우 이전 배포의 일부 파일과 새 배포의 일부 파일을 캐시할 수 있는 위험이 있습니다. 이 경우 추가 업데이트를 배포할 때까지 앱이 중단됨 상태로 중단됩니다.
