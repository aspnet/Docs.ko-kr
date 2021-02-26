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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551775"
---
* 다음 명령을 실행하여 HTTPS 개발 인증서를 신뢰합니다.

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  앞의 명령은 Linux에서 작동하지 않습니다. 인증서 신뢰에 대해서는 Linux 배포 설명서를 참조하세요.

  이전 명령으로 인해 다음 대화 상자가 표시됩니다.

  ![보안 경고 대화 상자](~/getting-started/_static/cert.png)

* 개발 인증서를 신뢰하는 데 동의하는 경우 **예** 를 선택합니다.

  자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]