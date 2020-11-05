---
title: ASP.NET Core의 정적 파일
author: rick-anderson
description: ASP.NET Core 웹앱에서 정적 파일을 제공 및 보호하고 정적 파일 호스팅 미들웨어 동작을 구성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057858"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="37c19-103">ASP.NET Core의 정적 파일</span><span class="sxs-lookup"><span data-stu-id="37c19-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37c19-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="37c19-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="37c19-105">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 기본적으로 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="37c19-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37c19-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="37c19-107">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-107">Serve static files</span></span>

<span data-ttu-id="37c19-108">정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="37c19-109">기본 디렉터리는 `{content root}/wwwroot`이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="37c19-110">자세한 내용은 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="37c19-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="37c19-112">위의 코드는 웹앱 템플릿을 사용하여 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="37c19-113">정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37c19-114">예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot` 폴더 내에 여러 폴더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="37c19-115">*wwwroot/images* 폴더를 만들고 *wwwroot/images/MyImage.jpg* 파일을 추가하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="37c19-116">`images` 폴더의 파일에 액세스하기 위한 URI 형식은 `https://<hostname>/images/<image_file_name>`입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="37c19-117">예를 들면 `https://localhost:5001/images/MyImage.jpg`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="37c19-118">웹 루트의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-118">Serve files in web root</span></span>

<span data-ttu-id="37c19-119">기본 웹앱 템플릿은 `Startup.Configure`에서 <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출하여 정적 파일을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="37c19-120">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="37c19-121">다음 태그는 *wwwroot/images/MyImage.jpg* 를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-121">The following markup references *wwwroot/images/MyImage.jpg* :</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="37c19-122">위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="37c19-123">웹 루트 외부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-123">Serve files outside of web root</span></span>

<span data-ttu-id="37c19-124">제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="37c19-125">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 `red-rose.jpg` 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="37c19-126">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="37c19-127">`https://<hostname>/StaticFiles/images/red-rose.jpg`에 대한 요청은 *red-rose.jpg* 파일에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="37c19-128">다음 태그는 *MyStaticFiles/images/red-rose.jpg* 를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-128">The following markup references *MyStaticFiles/images/red-rose.jpg* :</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="37c19-129">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="37c19-129">Set HTTP response headers</span></span>

<span data-ttu-id="37c19-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="37c19-131">다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="37c19-132">정적 파일은 600초 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-132">Static files are publicly cacheable for 600 seconds:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="37c19-134">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="37c19-134">Static file authorization</span></span>

<span data-ttu-id="37c19-135">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="37c19-136">`wwwroot` 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="37c19-137">권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="37c19-138">파일을 `wwwroot` 외부의 기본 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="37c19-139">`UseAuthorization` 다음에 `UseStaticFiles`를 호출하고 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="37c19-140">위의 방법을 사용하려면 사용자를 인증해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="37c19-141">권한 부여를 기반으로 파일을 제공하는 대체 방법:</span><span class="sxs-lookup"><span data-stu-id="37c19-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="37c19-142">파일을 `wwwroot` 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="37c19-143">권한 부여가 적용되는 작업 메서드를 통해 파일을 제공하고 <xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="37c19-144">디렉터리 검색</span><span class="sxs-lookup"><span data-stu-id="37c19-144">Directory browsing</span></span>

<span data-ttu-id="37c19-145">디렉터리 검색을 통해 지정된 디렉터리 내에 디렉터리를 나열할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="37c19-146">기본적으로 디렉터리 검색은 보안상의 이유로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="37c19-147">자세한 내용은 [고려 사항](#considerations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="37c19-148">다음을 통해 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="37c19-149">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 사용</span><span class="sxs-lookup"><span data-stu-id="37c19-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="37c19-150">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 사용</span><span class="sxs-lookup"><span data-stu-id="37c19-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="37c19-151">위의 코드를 통해 URL `https://<hostname>/MyImages`를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="37c19-153">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-153">Serve default documents</span></span>

<span data-ttu-id="37c19-154">기본 페이지를 설정하면 방문자에게 사이트의 시작 지점이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="37c19-155">정규화된 URI 없이 `wwwroot`의 기본 페이지를 제공하려면 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="37c19-156">기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="37c19-157">`UseDefaultFiles`는 파일을 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="37c19-158">`UseDefaultFiles`를 사용하면 `wwwroot`의 폴더에 대한 요청이 다음을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="37c19-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="37c19-159">*default.htm*</span></span>
* <span data-ttu-id="37c19-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-160">*default.html*</span></span>
* <span data-ttu-id="37c19-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="37c19-161">*index.htm*</span></span>
* <span data-ttu-id="37c19-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-162">*index.html*</span></span>

<span data-ttu-id="37c19-163">목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="37c19-164">브라우저 URL은 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="37c19-165">다음 코드는 기본 파일 이름을 *mydefault.html* 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-165">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="37c19-166">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="37c19-167">기본 문서의 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="37c19-167">UseFileServer for default documents</span></span>

<span data-ttu-id="37c19-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="37c19-169">`app.UseFileServer`를 호출하여 정적 파일 및 기본 파일을 제공할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="37c19-170">디렉터리 검색은 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="37c19-171">다음 코드는 `UseFileServer`와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="37c19-172">다음 코드를 사용하면 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="37c19-173">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="37c19-174">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="37c19-175">다음 코드를 사용하면 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="37c19-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>는 `EnableDirectoryBrowsing` 속성 값이 `true`인 경우 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="37c19-177">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="37c19-178">URI</span><span class="sxs-lookup"><span data-stu-id="37c19-178">URI</span></span>            |      <span data-ttu-id="37c19-179">응답</span><span class="sxs-lookup"><span data-stu-id="37c19-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="37c19-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="37c19-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="37c19-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="37c19-182">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 `https://<hostname>/StaticFiles`는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

<span data-ttu-id="37c19-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 후행 `/`가 없는 대상 URI에서 후행 `/`가 있는 대상 URI로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="37c19-185">예를 들어 `https://<hostname>/StaticFiles`에서 `https://<hostname>/StaticFiles/`로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="37c19-186">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시(`/`)가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="37c19-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="37c19-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="37c19-188"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="37c19-189">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="37c19-190">*.rtf* 확장명은 대체되며 *.mp4* 는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="37c19-191">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="37c19-192">[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="37c19-193">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="37c19-193">Non-standard content types</span></span>

<span data-ttu-id="37c19-194">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="37c19-195">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="37c19-196">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="37c19-197">디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="37c19-198">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="37c19-199">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="37c19-200">위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-201"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="37c19-202">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="37c19-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="37c19-204">여러 위치에서 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-204">Serve files from multiple locations</span></span>

<span data-ttu-id="37c19-205">`UseStaticFiles` 및 `UseFileServer`는 기본적으로 `wwwroot`를 가리키는 파일 공급자로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="37c19-206">다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="37c19-207">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="37c19-208">정적 파일의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="37c19-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-209">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="37c19-210">프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="37c19-211">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="37c19-212">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="37c19-213">`<content_root>/wwwroot`와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="37c19-214">MVC 뷰, :::no-loc(Razor)::: Pages, 구성 파일 등과 해당 파일을 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-214">Separate these files from MVC views, :::no-loc(Razor)::: Pages, configuration files, etc.</span></span>

* <span data-ttu-id="37c19-215">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="37c19-216">예를 들어 Windows는 대/소문자를 구분하지 않지만 macOS 및 Linux는 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="37c19-217">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="37c19-218">IIS 정적 파일 처리기는 사용되지 않으며 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="37c19-219">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="37c19-220">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="37c19-221">목록에서 **StaticFileModule** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="37c19-222">**동작** 사이드바에서 **제거** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-223">IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="37c19-224">예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="37c19-225">*.cs* 및 *.cshtml* 을 포함한 코드 파일을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-225">Place code files, including *.cs* and *.cshtml* , outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37c19-226">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="37c19-227">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37c19-228">추가 자료</span><span class="sxs-lookup"><span data-stu-id="37c19-228">Additional resources</span></span>

* [<span data-ttu-id="37c19-229">미들웨어</span><span class="sxs-lookup"><span data-stu-id="37c19-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="37c19-230">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="37c19-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37c19-231">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="37c19-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="37c19-232">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="37c19-233">일부 구성은 이러한 파일을 제공하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="37c19-234">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37c19-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="37c19-235">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-235">Serve static files</span></span>

<span data-ttu-id="37c19-236">정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="37c19-237">기본 디렉터리는 *{content root}/wwwroot* 이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-237">The default directory is *{content root}/wwwroot* , but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="37c19-238">자세한 정보는 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="37c19-239">앱의 웹 호스트에서 콘텐츠 루트 디렉터리를 인식하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="37c19-240">`WebHost.CreateDefaultBuilder` 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="37c19-241">정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37c19-242">예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot`폴더 내에 여러 폴더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="37c19-243">*images* 하위 폴더에 있는 파일에 액세스하기 위한 URI 형식은 *http://\<server_address>/images/\<image_file_name>* 입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="37c19-244">예를 들어 *http://localhost:9189/images/banner3.svg* 와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="37c19-245">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="37c19-246">.NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 이 패키지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="37c19-247">정적 파일을 제공할 수 있도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="37c19-248">웹 루트 내부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-248">Serve files inside of web root</span></span>

<span data-ttu-id="37c19-249">`Startup.Configure` 내에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="37c19-250">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="37c19-251">다음 표시는 *wwwroot/images/banner1.svg* 를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-251">The following markup references *wwwroot/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="37c19-252">위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="37c19-253">웹 루트 외부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-253">Serve files outside of web root</span></span>

<span data-ttu-id="37c19-254">제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="37c19-255">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 *banner1.svg* 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="37c19-256">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="37c19-257">*http://\<server_address>/StaticFiles/images/banner1.svg* 에 대한 요청은 *banner1.svg* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="37c19-258">다음 태그는 *MyStaticFiles/images/banner1.svg* 를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-258">The following markup references *MyStaticFiles/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="37c19-259">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="37c19-259">Set HTTP response headers</span></span>

<span data-ttu-id="37c19-260"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="37c19-261">다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="37c19-262"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> 메서드는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="37c19-263">개발 환경에서 파일은 10분(600초) 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="37c19-265">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="37c19-265">Static file authorization</span></span>

<span data-ttu-id="37c19-266">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="37c19-267">*wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-267">Any files served by it, including those under *wwwroot* , are publicly accessible.</span></span> <span data-ttu-id="37c19-268">권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="37c19-269">파일을 *wwwroot* 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="37c19-270">권한 부여가 적용되는 작업 메서드를 통해서 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="37c19-271"><xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="37c19-272">디렉터리 검색 사용</span><span class="sxs-lookup"><span data-stu-id="37c19-272">Enable directory browsing</span></span>

<span data-ttu-id="37c19-273">디렉터리 검색을 사용하면 웹앱 사용자가 지정된 디렉터리 내의 디렉터리 목록 및 파일을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="37c19-274">기본적으로 디렉터리 검색은 보안상의 이유로 비활성화되어 있습니다([고려할 사항](#considerations) 참조).</span><span class="sxs-lookup"><span data-stu-id="37c19-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="37c19-275">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 메서드를 호출하여 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="37c19-276">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 메서드를 호출하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="37c19-277">위의 코드를 통해 URL *http://\<server_address>/MyImages* 를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* , with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

<span data-ttu-id="37c19-279">검색을 활성화하는 경우의 보안 위험에 대한 [고려할 사항](#considerations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="37c19-280">다음 예제에서 두 `UseStaticFiles` 호출을 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="37c19-281">첫 번째 호출은 *wwwroot* 폴더에서 정적 파일 제공을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="37c19-282">두 번째 호출은 URL *http://\<server_address>/MyImages* 를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="37c19-283">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-283">Serve a default document</span></span>

<span data-ttu-id="37c19-284">기본 홈페이지를 설정하면 방문자가 사이트를 방문할 때 논리적 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="37c19-285">사용자가 URI를 정규화하지 않더라도 기본 페이지를 제공하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="37c19-286">기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="37c19-287">`UseDefaultFiles`는 실제로 파일을 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="37c19-288">파일을 제공하도록 `UseStaticFiles`를 통해 정적 파일 미들웨어를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="37c19-289">`UseDefaultFiles`를 사용하면 폴더에 대한 요청이 다음 파일들을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="37c19-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="37c19-290">*default.htm*</span></span>
* <span data-ttu-id="37c19-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-291">*default.html*</span></span>
* <span data-ttu-id="37c19-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="37c19-292">*index.htm*</span></span>
* <span data-ttu-id="37c19-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-293">*index.html*</span></span>

<span data-ttu-id="37c19-294">목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="37c19-295">브라우저 URL은 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="37c19-296">다음 코드는 기본 파일 이름을 *mydefault.html* 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-296">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="37c19-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="37c19-297">UseFileServer</span></span>

<span data-ttu-id="37c19-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="37c19-299">다음 코드는 정적 파일 및 기본 파일 제공을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="37c19-300">디렉터리 검색은 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="37c19-301">다음 코드는 매개 변수가 없는 오버로드에 추가적으로 디렉터리 검색을 활성화하여 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="37c19-302">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="37c19-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="37c19-303">**wwwroot**</span></span>
  * <span data-ttu-id="37c19-304">**css**</span><span class="sxs-lookup"><span data-stu-id="37c19-304">**css**</span></span>
  * <span data-ttu-id="37c19-305">**images**</span><span class="sxs-lookup"><span data-stu-id="37c19-305">**images**</span></span>
  * <span data-ttu-id="37c19-306">**js**</span><span class="sxs-lookup"><span data-stu-id="37c19-306">**js**</span></span>
* <span data-ttu-id="37c19-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="37c19-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="37c19-308">**images**</span><span class="sxs-lookup"><span data-stu-id="37c19-308">**images**</span></span>
    * <span data-ttu-id="37c19-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="37c19-309">*banner1.svg*</span></span>
  * <span data-ttu-id="37c19-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="37c19-310">*default.html*</span></span>

<span data-ttu-id="37c19-311">다음 코드는 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="37c19-312">`EnableDirectoryBrowsing` 속성 값이 `true`인 경우 `AddDirectoryBrowser`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="37c19-313">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="37c19-314">URI</span><span class="sxs-lookup"><span data-stu-id="37c19-314">URI</span></span>            |                             <span data-ttu-id="37c19-315">응답</span><span class="sxs-lookup"><span data-stu-id="37c19-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="37c19-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="37c19-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="37c19-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="37c19-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="37c19-318">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="37c19-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="37c19-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="37c19-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="37c19-320">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 *http://\<server_address>/StaticFiles* 는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="37c19-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 `http://{SERVER ADDRESS}/StaticFiles`(후행 슬래시 없음)에서 `http://{SERVER ADDRESS}/StaticFiles/`(후행 슬래시 있음)로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="37c19-323">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="37c19-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="37c19-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="37c19-325"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="37c19-326">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="37c19-327">*.rtf* 확장은 대체되며 *.mp4* 는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="37c19-328">[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="37c19-329">사용자 지정 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>를 사용하거나 :::no-loc(Blazor)::: Server 앱에서 다른 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 구성하는 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/additional-scenarios#static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in :::no-loc(Blazor)::: Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="37c19-330">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="37c19-330">Non-standard content types</span></span>

<span data-ttu-id="37c19-331">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="37c19-332">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="37c19-333">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="37c19-334">디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="37c19-335">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="37c19-336">위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-337"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="37c19-338">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="37c19-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="37c19-340">여러 위치에서 파일 제공</span><span class="sxs-lookup"><span data-stu-id="37c19-340">Serve files from multiple locations</span></span>

<span data-ttu-id="37c19-341">`UseStaticFiles` 및 `UseFileServer`은(는) *wwwroot* 를 가리키는 파일 공급자를 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="37c19-342">다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="37c19-343">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="37c19-344">고려 사항</span><span class="sxs-lookup"><span data-stu-id="37c19-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-345">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="37c19-346">프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="37c19-347">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="37c19-348">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="37c19-349">*\<content_root>/wwwroot* 와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="37c19-350">MVC 뷰, :::no-loc(Razor)::: Pages(2.x에만 해당), 구성 파일 등과 해당 파일을 분리하세요.</span><span class="sxs-lookup"><span data-stu-id="37c19-350">Separate these files from MVC views, :::no-loc(Razor)::: Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="37c19-351">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="37c19-352">예를 들어 Windows는 대/소문자를 구분하지 않는 반면 macOS 및 Linux는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="37c19-353">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="37c19-354">IIS 정적 파일 처리기는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="37c19-355">모듈에서 처리하기 전에는 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="37c19-356">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="37c19-357">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="37c19-358">목록에서 **StaticFileModule** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="37c19-359">**동작** 사이드바에서 **제거** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="37c19-360">IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="37c19-361">예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="37c19-362">코드 파일( *.cs* 및 *.cshtml* 포함)을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-362">Place code files (including *.cs* and *.cshtml* ) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="37c19-363">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="37c19-364">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37c19-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37c19-365">추가 자료</span><span class="sxs-lookup"><span data-stu-id="37c19-365">Additional resources</span></span>

* [<span data-ttu-id="37c19-366">미들웨어</span><span class="sxs-lookup"><span data-stu-id="37c19-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="37c19-367">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="37c19-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
