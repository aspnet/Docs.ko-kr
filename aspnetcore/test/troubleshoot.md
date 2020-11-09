---
title: ASP.NET Core 프로젝트 문제 해결 및 디버깅
author: Rick-Anderson
description: ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059574"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="2b60c-103">ASP.NET Core 프로젝트 문제 해결 및 디버깅</span><span class="sxs-lookup"><span data-stu-id="2b60c-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="2b60c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2b60c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2b60c-105">다음 링크는 문제 해결 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="2b60c-106">NDC 회의(런던, 2018년): ASP.NET Core 애플리케이션의 문제 진단</span><span class="sxs-lookup"><span data-stu-id="2b60c-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="2b60c-107">ASP.NET 블로그: ASP.NET Core 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="2b60c-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="2b60c-108">.NET Core SDK 경고</span><span class="sxs-lookup"><span data-stu-id="2b60c-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="2b60c-109">.NET Core SDK 32비트 및 64비트 버전이 모두 설치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="2b60c-110">ASP.NET Core에 대한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="2b60c-111">.NET Core SDK 32비트 및 64비트 버전이 모두 설치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="2b60c-112">'C:\\Program Files\\dotnet\\sdk\\'에 설치된 64비트 버전의 템플릿만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="2b60c-113">이 경고는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 32비트(x86) 및 64비트(x64) 버전이 모두 설치된 경우에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="2b60c-114">두 버전이 모두 설치될 수 있는 일반적인 이유에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="2b60c-115">처음에 32비트 컴퓨터를 사용하여 .NET Core SDK 설치 관리자를 다운로드한 다음, 복사하고 64비트 컴퓨터에 설치했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="2b60c-116">32비트 .NET Core SDK가 다른 애플리케이션에 의해 설치되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="2b60c-117">잘못된 버전이 다운로드 및 설치되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="2b60c-118">이 경고를 피하려면 32비트 .NET Core SDK를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="2b60c-119">**제어판** > **프로그램 및 기능** > **프로그램 제거 또는 변경** 에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="2b60c-120">경고가 발생하는 이유와 그 영향을 이해하는 경우 경고를 무시해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="2b60c-121">.NET Core SDK는 여러 위치에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="2b60c-122">ASP.NET Core에 대한 **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="2b60c-123">.NET Core SDK는 여러 위치에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="2b60c-124">'C:\\Program Files\\dotnet\\sdk\\'에 설치된 SDK의 템플릿만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="2b60c-125">*C:\\Program Files\\dotnet\\sdk\\* 외부의 디렉터리에 .NET Core SDK를 하나 이상 설치하는 경우 이 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="2b60c-126">일반적으로 이 문제는 .NET Core SDK가 MSI 설치 관리자 대신 복사/붙여넣기를 사용하여 컴퓨터에 배포된 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="2b60c-127">이 경고를 방지하려면 모든 32비트 .NET Core SDK 및 런타임을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="2b60c-128">**제어판** > **프로그램 및 기능** > **프로그램 제거 또는 변경** 에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="2b60c-129">경고가 발생하는 이유와 그 영향을 이해하는 경우 경고를 무시해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="2b60c-130">.NET Core SDK가 검색되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="2b60c-131">ASP.NET Core에 대한 Visual Studio **새 프로젝트** 대화 상자에서 다음과 같은 경고가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="2b60c-132">.NET Core SDK를 검색하지 못했습니다. 환경 변수 `PATH`에 포함되어 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2b60c-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="2b60c-133">`dotnet` 명령을 실행하면 다음과 같은 경고가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="2b60c-134">설치된 dotnet SDK를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="2b60c-135">이러한 경고는 환경 변수 `PATH`가 컴퓨터의 .NET Core SDK를 가리키지 않을 때 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="2b60c-136">이 문제를 해결하려면</span><span class="sxs-lookup"><span data-stu-id="2b60c-136">To resolve this problem:</span></span>

* <span data-ttu-id="2b60c-137">.NET Core SDK를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="2b60c-138">[.NET 다운로드](https://dotnet.microsoft.com/download)에서 최신 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="2b60c-139">`PATH` 환경 변수가 SDK 설치 위치를 가리키는지 확인합니다(64비트/x64의 경우 `C:\Program Files\dotnet\` 또는 32비트/x86인 경우 `C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="2b60c-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="2b60c-140">SDK 설치 관리자는 일반적으로 `PATH`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="2b60c-141">항상 동일한 비트 SDK 및 런타임을 동일한 컴퓨터에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="2b60c-142">.NET Core 호스팅 번들을 설치한 후 SDK가 누락됨</span><span class="sxs-lookup"><span data-stu-id="2b60c-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="2b60c-143">[.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)을 설치하면 .NET Core 런타임을 설치할 때 32비트(x86) 버전의 .NET Core(`C:\Program Files (x86)\dotnet\`)를 가리키도록 `PATH`를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="2b60c-144">32비트(x86) .NET Core `dotnet` 명령을 사용하는 경우 SDK가 누락될 수 있습니다([.NET Core SDK가 검색되지않음](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="2b60c-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="2b60c-145">이 문제를 해결하려면 `C:\Program Files\dotnet\`을 `PATH`의 `C:\Program Files (x86)\dotnet\` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="2b60c-146">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="2b60c-146">Obtain data from an app</span></span>

<span data-ttu-id="2b60c-147">앱이 요청에 응답할 수 있는 경우 미들웨어를 사용하여 앱에서 다음 데이터를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="2b60c-148">요청: 메서드, 체계, 호스트, pathbase, 경로, 쿼리 문자열, 헤더</span><span class="sxs-lookup"><span data-stu-id="2b60c-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="2b60c-149">연결: 원격 IP 주소, 원격 포트, 로컬 IP 주소, 로컬 포트, 클라이언트 인증서</span><span class="sxs-lookup"><span data-stu-id="2b60c-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="2b60c-150">Identity: 이름, 표시 이름</span><span class="sxs-lookup"><span data-stu-id="2b60c-150">Identity: Name, display name</span></span>
* <span data-ttu-id="2b60c-151">구성 설정</span><span class="sxs-lookup"><span data-stu-id="2b60c-151">Configuration settings</span></span>
* <span data-ttu-id="2b60c-152">환경 변수</span><span class="sxs-lookup"><span data-stu-id="2b60c-152">Environment variables</span></span>

<span data-ttu-id="2b60c-153">`Startup.Configure` 메서드의 요청 처리 파이프라인의 시작 부분에 다음 [미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="2b60c-154">개발 환경에서만 코드가 실행되도록 하기 위해 미들웨어가 실행되기 전에 환경이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="2b60c-155">환경을 가져오려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="2b60c-156">`IHostingEnvironment`를 `Startup.Configure` 메서드에 주입하고 지역 변수를 사용하여 환경을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="2b60c-157">다음 샘플 코드에서는 이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="2b60c-158">`Startup` 클래스의 속성에 환경을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="2b60c-159">속성(예: `if (Environment.IsDevelopment())`)을 사용하여 환경을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="2b60c-160">ASP.NET Core 앱 디버그</span><span class="sxs-lookup"><span data-stu-id="2b60c-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="2b60c-161">다음 링크는 ASP.NET Core 앱을 디버그하는 방법에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b60c-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="2b60c-162">Linux에서 ASP Core 디버그</span><span class="sxs-lookup"><span data-stu-id="2b60c-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="2b60c-163">SSH를 통해 Unix에서 .NET Core 디버그</span><span class="sxs-lookup"><span data-stu-id="2b60c-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="2b60c-164">빠른 시작: Visual Studio 디버거를 사용하여 ASP.NET 디버그</span><span class="sxs-lookup"><span data-stu-id="2b60c-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="2b60c-165">자세한 디버깅 정보는 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/2960)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b60c-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
