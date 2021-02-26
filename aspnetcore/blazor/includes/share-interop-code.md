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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551237"
---
## <a name="share-interop-code-in-a-class-library"></a>클래스 라이브러리의 interop 코드 공유

JS interop 코드는 클래스 라이브러리에 포함할 수 있습니다. 이렇게 하면 NuGet 패키지에서 코드를 공유할 수 있습니다.

클래스 라이브러리는 기본 제공 어셈블리에 JavaScript 리소스를 포함하는 작업을 처리합니다. JavaScript 파일은 `wwwroot` 폴더에 배치됩니다. 라이브러리가 빌드될 때 도구에서 리소스를 포함하는 작업을 처리합니다.

기본 제공되는 NuGet 패키지는 앱의 프로젝트 파일에서 다른 NuGet 패키지가 참조되는 것과 같은 방식으로 참조됩니다. 패키지가 복원된 후에는 앱 코드가 C#을 호출하는 것처럼 JavaScript를 호출할 수 있습니다.

자세한 내용은 <xref:blazor/components/class-libraries>를 참조하세요.
