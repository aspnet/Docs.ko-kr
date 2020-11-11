* `-F|--no-formatting`

  <span data-ttu-id="8052a-101">HTTP 응답 형식을 표시하지 않는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="8052a-102">HTTP 요청 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-102">Sets an HTTP request header.</span></span> <span data-ttu-id="8052a-103">다음 두 가지 값 형식이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="8052a-104">HTTP 응답 본문을 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="8052a-105">예들 들어 `--response:body "C:\response.json"`입니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="8052a-106">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="8052a-107">HTTP 응답 헤더를 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="8052a-108">예들 들어 `--response:headers "C:\response.txt"`입니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="8052a-109">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="8052a-110">HTTP 응답의 스트리밍을 사용하도록 설정하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="8052a-110">A flag whose presence enables streaming of the HTTP response.</span></span>
