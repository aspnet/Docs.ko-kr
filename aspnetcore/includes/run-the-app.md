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
ms.openlocfilehash: d05b0f327eb1670847125e6a73ef7277ebd069b5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552574"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl+F5를 눌러 디버거 없이 실행합니다.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다. 주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. `localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다. Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* **Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.

  Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다. 주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다. 그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.

  
# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Visual Studio에서 **Opt-Cmd-Return** 을 눌러 디버거 없이 실행합니다. 또는 메뉴 모음에서 **실행>디버깅하지 않고 시작** 으로 이동합니다.

  Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.

<!-- End of VS tabs -->

---
