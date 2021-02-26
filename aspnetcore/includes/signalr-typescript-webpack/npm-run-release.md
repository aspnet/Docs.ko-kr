---
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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552492"
---
```console
npm run release
```

<span data-ttu-id="41743-101">이 명령은 앱을 실행할 때 제공되는 클라이언트 쪽 자산을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="41743-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="41743-102">자산은 *wwwroot* 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="41743-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="41743-103">Webpack은 다음 작업을 완료했습니다.</span><span class="sxs-lookup"><span data-stu-id="41743-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="41743-104">*wwwroot* 디렉터리의 콘텐츠를 제거했습니다.</span><span class="sxs-lookup"><span data-stu-id="41743-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="41743-105">트랜스파일이라는 프로세스에서 TypeScript를 JavaScript로 변환했습니다. </span><span class="sxs-lookup"><span data-stu-id="41743-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="41743-106">축소라는 프로세스에서 파일 크기를 줄이기 위해 생성된 JavaScript를 변환했습니다. </span><span class="sxs-lookup"><span data-stu-id="41743-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="41743-107">*src* 에서 *wwwroot* 디렉터리로 처리된 JavaScript, CSS 및 HTML 파일을 복사했습니다.</span><span class="sxs-lookup"><span data-stu-id="41743-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="41743-108">다음 요소를 *wwwroot/index.html* 파일에 삽입했습니다.</span><span class="sxs-lookup"><span data-stu-id="41743-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="41743-109">*wwwroot/main.\<hash\>.css* 파일을 참조하는 `<link>` 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="41743-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="41743-110">이 태그는 `</head>` 태그를 닫기 전에 즉시 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="41743-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="41743-111">축소된 *wwwroot/main.\<hash\>.js* 파일을 참조하는 `<script>` 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="41743-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="41743-112">이 태그는 `</body>` 태그를 닫기 전에 즉시 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="41743-112">This tag is placed immediately before the closing `</body>` tag.</span></span>