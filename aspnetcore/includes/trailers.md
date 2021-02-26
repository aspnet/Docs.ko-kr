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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551399"
---
<span data-ttu-id="f326a-101">HTTP 트레일러는 응답 본문이 전송된 후에 전송된다는 점을 제외하고 HTTP 헤더와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="f326a-102">IIS 및 HTTP.sys의 경우 HTTP/2 응답 트레일러만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="f326a-103">앞의 예제 코드에서:</span><span class="sxs-lookup"><span data-stu-id="f326a-103">In the preceding example code:</span></span>

* <span data-ttu-id="f326a-104">`SupportsTrailers`는 응답을 위해 트레일러가 지원되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="f326a-105">`DeclareTrailer`는 `Trailer` 응답 헤더에 지정된 트레일러 이름을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="f326a-106">응답 트레일러 선언은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="f326a-107">`DeclareTrailer`가 호출되는 경우 해당 트레일러는 응답 헤더가 전송되기 전에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="f326a-108">`AppendTrailer`는 트레일러를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f326a-108">`AppendTrailer` appends the trailer.</span></span>
