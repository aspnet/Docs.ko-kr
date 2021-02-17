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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551269"
---
전달된 헤더 미들웨어는 다른 미들웨어보다 먼저 실행해야 합니다. 이 순서를 지정하면 전달된 헤더 정보에 따라 달라지는 미들웨어는 처리하기 위해 헤더 값을 사용할 수 있습니다. 진단 및 오류 처리 미들웨어 다음에 전달된 헤더 미들웨어를 실행하려면 [전달된 헤더 미들웨어 순서](xref:host-and-deploy/proxy-load-balancer#fhmo)를 참조하세요.