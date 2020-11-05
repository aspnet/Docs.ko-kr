---
title: ASP.NET Core 프로젝트 문제 해결 및 디버깅
author: Rick-Anderson
description: ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059574"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core 프로젝트 문제 해결 및 디버깅

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

다음 링크는 문제 해결 지침을 제공합니다.

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC 회의(런던, 2018년): ASP.NET Core 애플리케이션의 문제 진단](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET 블로그: ASP.NET Core 성능 문제 해결](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK 경고

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK 32비트 및 64비트 버전이 모두 설치되어 있습니다.

ASP.NET Core에 대한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.

> .NET Core SDK 32비트 및 64비트 버전이 모두 설치되어 있습니다. 'C:\\Program Files\\dotnet\\sdk\\'에 설치된 64비트 버전의 템플릿만 표시됩니다.

이 경고는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 32비트(x86) 및 64비트(x64) 버전이 모두 설치된 경우에 나타납니다. 두 버전이 모두 설치될 수 있는 일반적인 이유에는 다음이 포함됩니다.

* 처음에 32비트 컴퓨터를 사용하여 .NET Core SDK 설치 관리자를 다운로드한 다음, 복사하고 64비트 컴퓨터에 설치했습니다.
* 32비트 .NET Core SDK가 다른 애플리케이션에 의해 설치되었습니다.
* 잘못된 버전이 다운로드 및 설치되었습니다.

이 경고를 피하려면 32비트 .NET Core SDK를 제거합니다. **제어판** > **프로그램 및 기능** > **프로그램 제거 또는 변경** 에서 제거합니다. 경고가 발생하는 이유와 그 영향을 이해하는 경우 경고를 무시해도 됩니다.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK는 여러 위치에 설치됩니다.

ASP.NET Core에 대한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.

> .NET Core SDK는 여러 위치에 설치됩니다. 'C:\\Program Files\\dotnet\\sdk\\'에 설치된 SDK의 템플릿만 표시됩니다.

*C:\\Program Files\\dotnet\\sdk\\* 외부의 디렉터리에 .NET Core SDK를 하나 이상 설치하는 경우 이 메시지가 표시됩니다. 일반적으로 이 문제는 .NET Core SDK가 MSI 설치 관리자 대신 복사/붙여넣기를 사용하여 컴퓨터에 배포된 경우에 발생합니다.

이 경고를 방지하려면 모든 32비트 .NET Core SDK 및 런타임을 제거합니다. **제어판** > **프로그램 및 기능** > **프로그램 제거 또는 변경** 에서 제거합니다. 경고가 발생하는 이유와 그 영향을 이해하는 경우 경고를 무시해도 됩니다.

### <a name="no-net-core-sdks-were-detected"></a>.NET Core SDK가 검색되지 않았습니다.

* ASP.NET Core에 대한 Visual Studio **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.

  > .NET Core SDK를 검색하지 못했습니다. 환경 변수 `PATH`에 포함되어 있는지 확인하세요.

* `dotnet` 명령을 실행하면 다음과 같은 경고가 표시됩니다.

  > 설치된 dotnet SDK를 찾을 수 없습니다.

이러한 경고는 환경 변수 `PATH`가 컴퓨터의 .NET Core SDK를 가리키지 않을 때 나타납니다. 이 문제를 해결하려면

* .NET Core SDK를 설치합니다. [.NET 다운로드](https://dotnet.microsoft.com/download)에서 최신 설치 관리자를 가져옵니다.
* `PATH` 환경 변수가 SDK 설치 위치를 가리키는지 확인합니다(64비트/x64의 경우 `C:\Program Files\dotnet\` 또는 32비트/x86인 경우 `C:\Program Files (x86)\dotnet\`). SDK 설치 관리자는 일반적으로 `PATH`를 설정합니다. 항상 동일한 비트 SDK 및 런타임을 동일한 컴퓨터에 설치합니다.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들을 설치한 후 SDK가 누락됨

[.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)을 설치하면 .NET Core 런타임을 설치할 때 32비트(x86) 버전의 .NET Core(`C:\Program Files (x86)\dotnet\`)를 가리키도록 `PATH`를 수정합니다. 32비트(x86) .NET Core `dotnet` 명령을 사용하는 경우 SDK가 누락될 수 있습니다([.NET Core SDK가 검색되지않음](#no-net-core-sdks-were-detected)). 이 문제를 해결하려면 `C:\Program Files\dotnet\`을 `PATH`의 `C:\Program Files (x86)\dotnet\` 앞으로 이동합니다.

## <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 미들웨어를 사용하여 앱에서 다음 데이터를 가져올 수 있습니다.

* 요청: 메서드, 체계, 호스트, pathbase, 경로, 쿼리 문자열, 헤더
* 연결: 원격 IP 주소, 원격 포트, 로컬 IP 주소, 로컬 포트, 클라이언트 인증서
* Identity: 이름, 표시 이름
* 구성 설정
* 환경 변수

`Startup.Configure` 메서드의 요청 처리 파이프라인의 시작 부분에 다음 [미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 코드를 추가합니다. 개발 환경에서만 코드가 실행되도록 하기 위해 미들웨어가 실행되기 전에 환경이 검사됩니다.

환경을 가져오려면 다음 방법 중 하나를 사용합니다.

* `IHostingEnvironment`를 `Startup.Configure` 메서드에 주입하고 지역 변수를 사용하여 환경을 확인합니다. 다음 샘플 코드에서는 이 방법을 보여 줍니다.

* `Startup` 클래스의 속성에 환경을 할당합니다. 속성(예: `if (Environment.IsDevelopment())`)을 사용하여 환경을 확인합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core 앱 디버그

다음 링크는 ASP.NET Core 앱을 디버그하는 방법에 대한 정보를 제공합니다.

* [Linux에서 ASP Core 디버그](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH를 통해 Unix에서 .NET Core 디버그](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [빠른 시작: Visual Studio 디버거를 사용하여 ASP.NET 디버그](/visualstudio/debugger/quickstart-debug-aspnet)
* 자세한 디버깅 정보는 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/2960)를 참조하세요.
