---
title: 10부. ASP.NET Core 앱의 Details 및 Delete 메서드 검토
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 10부입니다.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 9ceb6c3c8a6622d6e203641c2ce97a483d3d4325
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050747"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="ea355-103">10부. ASP.NET Core 앱의 Details 및 Delete 메서드 검토</span><span class="sxs-lookup"><span data-stu-id="ea355-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="ea355-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea355-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea355-105">Movie 컨트롤러를 열고 `Details` 메서드를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="ea355-106">이 작업 메서드를 만든 MVC 스캐폴딩 엔진은 메서드를 호출하는 HTTP 요청을 보여주는 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="ea355-107">이 경우 `Movies` 컨트롤러, `Details` 메서드 및 `id` 값의 세 가지 URL 세그먼트를 가진 GET 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="ea355-108">이러한 세그먼트는 *Startup.cs* 에서 정의되었다는 점을 기억하세요.</span><span class="sxs-lookup"><span data-stu-id="ea355-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="ea355-109">EF를 사용하면 `FirstOrDefaultAsync` 메서드를 이용하여 데이터를 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="ea355-110">메서드에 기본 구성된 중요한 보안 기능은 영화를 사용하여 무언가 작업을 시도하기 전에 검색 메서드가 영화를 발견했는지부터 코드에서 확인하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="ea355-111">예를 들어 해커는 링크에서 만든 URL을 `http://localhost:{PORT}/Movies/Details/1`에서 `http://localhost:{PORT}/Movies/Details/12345`(또는 실제 영화를 나타내지 않는 다른 값)와 같은 URL로 변경하여 사이트에 오류를 발생시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="ea355-112">Null 영화를 검사하지 않으면 앱은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="ea355-113">`Delete` 및 `DeleteConfirmed` 메서드를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="ea355-114">`HTTP GET Delete` 메서드는 지정한 영화를 삭제하지 않고 삭제를 제출(HttpPost)할 수 있는 영화의 보기를 반환한다는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="ea355-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="ea355-115">GET 요청에 대한 응답에서 삭제 작업을 수행하는 것은(또는 해당 문제에 대한 편집 작업, 만들기 작업 또는 데이터를 변경하는 기타 작업을 수행하는 것은) 보안 허점을 야기합니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="ea355-116">데이터를 삭제하는 `[HttpPost]` 메서드의 이름은 HTTP POST 메서드에 고유한 서명 또는 이름을 부여하기 위해 `DeleteConfirmed`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="ea355-117">두 메서드의 서명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="ea355-118">CLR(공용 언어 런타임)은 고유한 매개 변수 서명을 갖기 위해 오버로드된 메서드가 필요합니다(동일한 메서드 이름이지만 다른 매개 변수의 목록).</span><span class="sxs-lookup"><span data-stu-id="ea355-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="ea355-119">그러나 여기에서 두 개의 `Delete` 메서드가 필요합니다. 하나는 GET에 대한 것이며 다른 하나는 POST에 대한 것입니다. 두 메서드에는 동일한 매개 변수 서명이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="ea355-120">(모두 매개 변수로 단일 정수를 허용해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="ea355-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="ea355-121">이 문제에 대한 두 가지의 방법이 있습니다. 하나는 메서드에 서로 다른 이름을 지정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="ea355-122">앞의 예에서 스캐폴딩 메커니즘이 수행한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="ea355-123">그러나 이는 작은 문제를 야기합니다. ASP.NET은 URL의 세그먼트를 이름으로 작업 메서드에 매핑하고 메서드의 이름을 바꾸면 정상적으로 라우팅하여 해당 메서드를 찾을 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="ea355-124">솔루션은 예제에서 확인한 것으로, `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드에 추가하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="ea355-125">해당 특성은 POST 요청에 대한 /Delete/를 포함하는 URL이 `DeleteConfirmed` 메서드를 찾도록 라우팅 시스템에 대한 매핑을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="ea355-126">동일한 이름 및 서명을 가진 메서드에 대한 또 다른 일반적인 해결 방법은 별도의 매개 변수(사용되지 않는)를 포함하도록 POST 메서드의 서명을 인위적으로 변경하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="ea355-127">이는 이전 게시에서 `notUsed` 매개 변수를 추가했을 때 수행했던 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="ea355-128">여기에서 `[HttpPost] Delete` 메서드에 대해 동일한 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="ea355-129">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="ea355-129">Publish to Azure</span></span>

<span data-ttu-id="ea355-130">Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](/azure/app-service/tutorial-dotnetcore-sqldb-app)에서 중단한 곳에서 이어집니다.</span><span class="sxs-lookup"><span data-stu-id="ea355-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ea355-131">이전</span><span class="sxs-lookup"><span data-stu-id="ea355-131">Previous</span></span>](validation.md)
