# <a name="blazor-webassembly-sample-app"></a><span data-ttu-id="91161-101">Blazor WebAssembly 샘플 앱</span><span class="sxs-lookup"><span data-stu-id="91161-101">Blazor WebAssembly Sample App</span></span>

<span data-ttu-id="91161-102">이 샘플은 Blazor 설명서에 설명된 Blazor 시나리오를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="91161-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="91161-103">웹 API 호출 예제</span><span class="sxs-lookup"><span data-stu-id="91161-103">Call web API example</span></span>

<span data-ttu-id="91161-104">웹 API 예제를 사용하려면 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Core를 사용하여 웹 API 만들기</a> 항목의 샘플 앱을 기반으로 한 웹 API가 실행되고 있어야 합니다. 웹 API는 기본적으로 Blazor 샘플 앱과 동일한 HTTPS 포트(5001)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Create a web API with ASP.NET Core</a> topic, which by default uses the same HTTPS port (5001) as the Blazor sample app.</span></span> <span data-ttu-id="91161-105">동일한 머신에서 두 앱을 동시에 사용하려면 웹 API의 포트를 변경합니다(예: 포트 10000 사용).</span><span class="sxs-lookup"><span data-stu-id="91161-105">To use both apps on the same machine at the same time, change the port of the web API (for example, use port 10000).</span></span> <span data-ttu-id="91161-106">샘플 앱은 `https://localhost:10000/api/TodoItems`에서 웹 API에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-106">The sample app makes requests to the web API at `https://localhost:10000/api/TodoItems`.</span></span> <span data-ttu-id="91161-107">다른 웹 API 주소를 사용하는 경우 Razor 구성 요소의 `@code` 블록에서 `ServiceEndpoint` 상수 값을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-107">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@code` block.</span></span></p>

<span data-ttu-id="91161-108">샘플 앱은 `http://localhost:5000` 또는 `https://localhost:5001`에서 웹 API에 대해 <a href="https://docs.microsoft.com/aspnet/core/security/cors">CORS</a>(원본 간 리소스 공유) 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-108">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="91161-109">자격 증명(권한 부여 쿠키/헤더)이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91161-109">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="91161-110">웹 API의 `Startup.Configure` 메서드에 다음 CORS 미들웨어 구성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-110">Add the following CORS middleware configuration to the web API's `Startup.Configure` method:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="91161-111">Blazor 앱에 필요한 경우 `WithOrigins`의 도메인과 포트를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="91161-111">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="91161-112">웹 API는 CORS에서 클라이언트 코드의 권한 부여 쿠키/헤더와 요청을 허용하도록 구성되어 있지만, 자습서에서 만든 웹 API는 실제로 요청 권한을 부여하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91161-112">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="91161-113">구현 지침은 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 보안 및 ID 문서</a>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91161-113">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
