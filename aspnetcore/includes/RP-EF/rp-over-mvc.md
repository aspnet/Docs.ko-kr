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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551828"
---
이 자습서에서는 컨트롤러와 보기를 통해 ASP.NET Core MVC 및 Entity Framework Core에 대해 설명합니다. [Razor Pages](xref:razor-pages/index)는 대체 프로그래밍 모델입니다. 새로 개발하는 경우에는 컨트롤러와 보기를 통해 MVC를 사용하는 것보다 Razor Pages를 사용할 것을 권장합니다. 이 자습서의 [Razor Pages](xref:data/ef-rp/intro) 버전을 참조하세요. 각 자습서는 다른 자습서에서 다루지 않는 일부 내용을 다룹니다.

이 MVC 자습서에는 Razor Pages 자습서에서 다루지 않는 몇 가지 사항이 있습니다.

* 데이터 모델에서 상속 구현
* 원시 SQL 쿼리 수행
* 동적 LINQ를 사용하여 코드 단순화

Razor Pages 자습서에는 여기에서 다루지 않는 몇 가지 사항이 있습니다.

* Select 메서드를 사용하여 관련 데이터 로드
* EF 모범 사례