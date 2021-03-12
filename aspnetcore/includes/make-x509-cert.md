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
ms.openlocfilehash: d3012acfa044fce6556043cf5b520cc05ce96c18
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589046"
---
Windows에서 자체 서명된 인증서는 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 만들 수 있습니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

macOS, Linux 및 Windows에서는 [OpenSSL](https://www.openssl.org/)을 사용하여 인증서를 만들 수 있습니다.
