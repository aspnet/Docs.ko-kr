---
title: ASP.NET Core의 정적 파일
author: rick-anderson
description: ASP.NET Core 웹앱에서 정적 파일을 제공 및 보호하고 정적 파일 호스팅 미들웨어 동작을 구성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057858"
---
# <a name="static-files-in-aspnet-core"></a>ASP.NET Core의 정적 파일

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)

HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 기본적으로 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>정적 파일 제공

정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다. 기본 디렉터리는 `{content root}/wwwroot`이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다. 자세한 내용은 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

위의 코드는 웹앱 템플릿을 사용하여 만들었습니다.

정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다. 예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot` 폴더 내에 여러 폴더가 포함되어 있습니다.

* `wwwroot`
  * `css`
  * `js`
  * `lib`

*wwwroot/images* 폴더를 만들고 *wwwroot/images/MyImage.jpg* 파일을 추가하는 것이 좋습니다. `images` 폴더의 파일에 액세스하기 위한 URI 형식은 `https://<hostname>/images/<image_file_name>`입니다. 예를 들면 `https://localhost:5001/images/MyImage.jpg`과 같습니다.

### <a name="serve-files-in-web-root"></a>웹 루트의 파일 제공

기본 웹앱 템플릿은 `Startup.Configure`에서 <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출하여 정적 파일을 제공할 수 있습니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다. 다음 태그는 *wwwroot/images/MyImage.jpg* 를 참조합니다.

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.

### <a name="serve-files-outside-of-web-root"></a>웹 루트 외부의 파일 제공

제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

요청은 정적 파일 미들웨어를 다음과 같이 구성하여 `red-rose.jpg` 파일에 액세스할 수 있습니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다. `https://<hostname>/StaticFiles/images/red-rose.jpg`에 대한 요청은 *red-rose.jpg* 파일에 사용됩니다.

다음 태그는 *MyStaticFiles/images/red-rose.jpg* 를 참조합니다.

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>HTTP 응답 헤더 설정

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다. 다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

정적 파일은 600초 동안 공개적으로 캐시할 수 있습니다.

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>정적 파일 권한 부여

정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다. `wwwroot` 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 액세스할 수 있습니다. 권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.

* 파일을 `wwwroot` 외부의 기본 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.
* `UseAuthorization` 다음에 `UseStaticFiles`를 호출하고 경로를 지정합니다.

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  위의 방법을 사용하려면 사용자를 인증해야 합니다.

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

권한 부여를 기반으로 파일을 제공하는 대체 방법:

* 파일을 `wwwroot` 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.
* 권한 부여가 적용되는 작업 메서드를 통해 파일을 제공하고 <xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>디렉터리 검색

디렉터리 검색을 통해 지정된 디렉터리 내에 디렉터리를 나열할 수 있습니다.

기본적으로 디렉터리 검색은 보안상의 이유로 사용하지 않도록 설정됩니다. 자세한 내용은 [고려 사항](#considerations)을 참조하세요.

다음을 통해 디렉터리 검색을 사용하도록 설정합니다.

* `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 사용
* `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 사용

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

위의 코드를 통해 URL `https://<hostname>/MyImages`를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.

![디렉터리 검색](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>기본 문서 제공

기본 페이지를 설정하면 방문자에게 사이트의 시작 지점이 제공됩니다. 정규화된 URI 없이 `wwwroot`의 기본 페이지를 제공하려면 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다. `UseDefaultFiles`는 파일을 제공하지 않는 URL 재작성기입니다.

`UseDefaultFiles`를 사용하면 `wwwroot`의 폴더에 대한 요청이 다음을 검색합니다.

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다. 브라우저 URL은 요청된 URI를 계속 반영합니다.

다음 코드는 기본 파일 이름을 *mydefault.html* 로 변경합니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>기본 문서의 UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.

`app.UseFileServer`를 호출하여 정적 파일 및 기본 파일을 제공할 수 있도록 합니다. 디렉터리 검색은 활성화되지 않습니다. 다음 코드는 `UseFileServer`와 함께 `Startup.Configure`를 보여 줍니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

다음 코드를 사용하면 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

다음 디렉터리 계층 구조를 고려해보세요.

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

다음 코드를 사용하면 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>는 `EnableDirectoryBrowsing` 속성 값이 `true`인 경우 호출해야 합니다.

URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.

| URI            |      응답  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 `https://<hostname>/StaticFiles`는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.

![정적 파일 목록](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 후행 `/`가 없는 대상 URI에서 후행 `/`가 있는 대상 URI로 클라이언트 쪽 리디렉션을 수행합니다. 예를 들어 `https://<hostname>/StaticFiles`에서 `https://<hostname>/StaticFiles/`로 리디렉션합니다. *StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시(`/`)가 있어야 유효합니다.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다. 다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 매핑됩니다. *.rtf* 확장명은 대체되며 *.mp4* 는 제거됩니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.

## <a name="non-standard-content-types"></a>비표준 콘텐츠 형식

정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다. 사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다. 요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다. 디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.

다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.

> [!WARNING]
> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다. 기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.

## <a name="serve-files-from-multiple-locations"></a>여러 위치에서 파일 제공

`UseStaticFiles` 및 `UseFileServer`는 기본적으로 `wwwroot`를 가리키는 파일 공급자로 설정됩니다. 다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>정적 파일의 보안 고려 사항

> [!WARNING]
> `UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다. 프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다. `UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요. 전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다. `<content_root>/wwwroot`와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장합니다. MVC 뷰, Razor Pages, 구성 파일 등과 해당 파일을 분리합니다.

* `UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다. 예를 들어 Windows는 대/소문자를 구분하지 않지만 macOS 및 Linux는 대/소문자를 구분합니다.

* IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다. IIS 정적 파일 처리기는 사용되지 않으며 요청을 처리할 수 없습니다.

* 서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.
    1. **모듈** 기능으로 이동합니다.
    1. 목록에서 **StaticFileModule** 을 선택합니다.
    1. **동작** 사이드바에서 **제거** 를 클릭합니다.

> [!WARNING]
> IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다. 예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.

* *.cs* 및 *.cshtml* 을 포함한 코드 파일을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다. 따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다. 그러면 서버 쪽 코드가 유출되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [미들웨어](xref:fundamentals/middleware/index)
* [ASP.NET Core 소개](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Addie](https://twitter.com/Scott_Addie)

HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다. 일부 구성은 이러한 파일을 제공하는 데 필수적입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>정적 파일 제공

정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다. 기본 디렉터리는 *{content root}/wwwroot* 이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다. 자세한 정보는 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.

앱의 웹 호스트에서 콘텐츠 루트 디렉터리를 인식하도록 해야 합니다.

`WebHost.CreateDefaultBuilder` 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다. 예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot`폴더 내에 여러 폴더가 포함되어 있습니다.

* `wwwroot`
  * `css`
  * `images`
  * `js`

*images* 하위 폴더에 있는 파일에 액세스하기 위한 URI 형식은 *http://\<server_address>/images/\<image_file_name>* 입니다. 예를 들어 *http://localhost:9189/images/banner3.svg* 와 같습니다.

.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다. .NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 이 패키지가 포함되어 있습니다.

정적 파일을 제공할 수 있도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.

### <a name="serve-files-inside-of-web-root"></a>웹 루트 내부의 파일 제공

`Startup.Configure` 내에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다. 다음 표시는 *wwwroot/images/banner1.svg* 를 참조합니다.

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.

### <a name="serve-files-outside-of-web-root"></a>웹 루트 외부의 파일 제공

제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

요청은 정적 파일 미들웨어를 다음과 같이 구성하여 *banner1.svg* 파일에 액세스할 수 있습니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다. *http://\<server_address>/StaticFiles/images/banner1.svg* 에 대한 요청은 *banner1.svg* 파일을 제공합니다.

다음 태그는 *MyStaticFiles/images/banner1.svg* 를 참조합니다.

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>HTTP 응답 헤더 설정

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다. 다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> 메서드는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.

개발 환경에서 파일은 10분(600초) 동안 공개적으로 캐시할 수 있습니다.

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>정적 파일 권한 부여

정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다. *wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다. 권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.

* 파일을 *wwwroot* 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.
* 권한 부여가 적용되는 작업 메서드를 통해서 파일을 제공합니다. <xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>디렉터리 검색 사용

디렉터리 검색을 사용하면 웹앱 사용자가 지정된 디렉터리 내의 디렉터리 목록 및 파일을 볼 수 있습니다. 기본적으로 디렉터리 검색은 보안상의 이유로 비활성화되어 있습니다([고려할 사항](#considerations) 참조). `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 메서드를 호출하여 디렉터리 검색을 사용하도록 설정합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 메서드를 호출하여 필요한 서비스를 추가합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

위의 코드를 통해 URL *http://\<server_address>/MyImages* 를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.

![디렉터리 검색](static-files/_static/dir-browse.png)

검색을 활성화하는 경우의 보안 위험에 대한 [고려할 사항](#considerations)을 참조하세요.

다음 예제에서 두 `UseStaticFiles` 호출을 참고하세요. 첫 번째 호출은 *wwwroot* 폴더에서 정적 파일 제공을 사용하도록 설정합니다. 두 번째 호출은 URL *http://\<server_address>/MyImages* 를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 사용하도록 설정합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>기본 문서 제공

기본 홈페이지를 설정하면 방문자가 사이트를 방문할 때 논리적 시작점을 제공합니다. 사용자가 URI를 정규화하지 않더라도 기본 페이지를 제공하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> 기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다. `UseDefaultFiles`는 실제로 파일을 제공하지 않는 URL 재작성기입니다. 파일을 제공하도록 `UseStaticFiles`를 통해 정적 파일 미들웨어를 활성화합니다.

`UseDefaultFiles`를 사용하면 폴더에 대한 요청이 다음 파일들을 검색합니다.

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다. 브라우저 URL은 요청된 URI를 계속 반영합니다.

다음 코드는 기본 파일 이름을 *mydefault.html* 로 변경합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.

다음 코드는 정적 파일 및 기본 파일 제공을 활성화합니다. 디렉터리 검색은 활성화되지 않습니다.

```csharp
app.UseFileServer();
```

다음 코드는 매개 변수가 없는 오버로드에 추가적으로 디렉터리 검색을 활성화하여 빌드합니다.

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

다음 디렉터리 계층 구조를 고려해보세요.

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

다음 코드는 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 활성화합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`EnableDirectoryBrowsing` 속성 값이 `true`인 경우 `AddDirectoryBrowser`를 호출해야 합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.

| URI            |                             응답  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 *http://\<server_address>/StaticFiles* 는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.

![정적 파일 목록](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 `http://{SERVER ADDRESS}/StaticFiles`(후행 슬래시 없음)에서 `http://{SERVER ADDRESS}/StaticFiles/`(후행 슬래시 있음)로 클라이언트 쪽 리디렉션을 수행합니다. *StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시가 있어야 유효합니다.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다. 다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 등록됩니다. *.rtf* 확장은 대체되며 *.mp4* 는 제거됩니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.

사용자 지정 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>를 사용하거나 Blazor Server 앱에서 다른 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 구성하는 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/additional-scenarios#static-files>를 참조하세요.

## <a name="non-standard-content-types"></a>비표준 콘텐츠 형식

정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다. 사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다. 요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다. 디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.

다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.

> [!WARNING]
> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다. 기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.

## <a name="serve-files-from-multiple-locations"></a>여러 위치에서 파일 제공

`UseStaticFiles` 및 `UseFileServer`은(는) *wwwroot* 를 가리키는 파일 공급자를 기본값으로 설정합니다. 다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.

### <a name="considerations"></a>고려 사항

> [!WARNING]
> `UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다. 프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다. `UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요. 전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다. *\<content_root>/wwwroot* 와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장하세요. MVC 뷰, Razor Pages(2.x에만 해당), 구성 파일 등과 해당 파일을 분리하세요.

* `UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다. 예를 들어 Windows는 대/소문자를 구분하지 않는 반면 macOS 및 Linux는 그렇지 않습니다.

* IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다. IIS 정적 파일 처리기는 사용되지 않습니다. 모듈에서 처리하기 전에는 요청을 처리할 수 없습니다.

* 서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.
    1. **모듈** 기능으로 이동합니다.
    1. 목록에서 **StaticFileModule** 을 선택합니다.
    1. **동작** 사이드바에서 **제거** 를 클릭합니다.

> [!WARNING]
> IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다. 예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.

* 코드 파일( *.cs* 및 *.cshtml* 포함)을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다. 따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다. 그러면 서버 쪽 코드가 유출되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [미들웨어](xref:fundamentals/middleware/index)
* [ASP.NET Core 소개](xref:index)

::: moniker-end
