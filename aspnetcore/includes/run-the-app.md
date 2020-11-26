# <a name="visual-studio"></a>[<span data-ttu-id="2c9b1-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c9b1-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c9b1-102">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2c9b1-103">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2c9b1-104">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c9b1-105">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2c9b1-106">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2c9b1-107">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="2c9b1-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c9b1-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2c9b1-109">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2c9b1-110">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2c9b1-111">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c9b1-112">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2c9b1-113">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c9b1-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c9b1-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2c9b1-115">Visual Studio에서 **Opt-Cmd-Return** 을 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="2c9b1-116">또는 메뉴 모음에서 **실행>디버깅하지 않고 시작** 으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="2c9b1-117">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2c9b1-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
