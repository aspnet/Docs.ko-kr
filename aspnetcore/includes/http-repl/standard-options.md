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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552583"
---
* `-F|--no-formatting`

  <span data-ttu-id="164bb-101">HTTP 응답 형식을 표시하지 않는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="164bb-102">HTTP 요청 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-102">Sets an HTTP request header.</span></span> <span data-ttu-id="164bb-103">다음 두 가지 값 형식이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="164bb-104">HTTP 응답 본문을 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="164bb-105">예들 들어 `--response:body "C:\response.json"`입니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="164bb-106">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="164bb-107">HTTP 응답 헤더를 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="164bb-108">예들 들어 `--response:headers "C:\response.txt"`입니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="164bb-109">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="164bb-110">HTTP 응답의 스트리밍을 사용하도록 설정하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="164bb-110">A flag whose presence enables streaming of the HTTP response.</span></span>
