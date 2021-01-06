::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="2b422-101">**catch-all** 매개 변수는 라우팅의 [버그](https://github.com/dotnet/aspnetcore/issues/18677)로 인해 경로와 일치하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="2b422-102">이 버그의 영향을 받는 앱은 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="2b422-103">catch-all 경로(예: `{**slug}"`)</span><span class="sxs-lookup"><span data-stu-id="2b422-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="2b422-104">catch-all 경로가 일치해야 하는 요청과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="2b422-105">다른 경로를 제거하면 catch-all 경로 작동이 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="2b422-106">이 버그에 해당하는 사례는 GitHub 버그 [18677](https://github.com/dotnet/aspnetcore/issues/18677) 및 [16579](https://github.com/dotnet/aspnetcore/issues/16579)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b422-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="2b422-107">이 버그에 대한 옵트인 픽스가 [.NET Core 3.1.301 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="2b422-108">다음 코드는 이 버그를 수정하는 내부 스위치를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b422-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end