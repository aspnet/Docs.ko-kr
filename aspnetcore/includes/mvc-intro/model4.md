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
ms.openlocfilehash: f7a7a661334cafa590ce99aff906bbfac1f18e2c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552341"
---
다음 표에서는 ASP.NET Core 코드 생성기 매개 변수를 자세히 설명합니다.

| 매개 변수               | Description|
| ----------------- | ------------ |
| -M  | 모델의 이름입니다. |
| -dc  | 데이터 컨텍스트입니다. |
| -udl | 기본 레이아웃을 사용합니다. |
| --relativeFolderPath | 파일을 만들기 위한 상태 출력 폴더 경로입니다. |
| --useDefaultLayout | 보기에는 기본 레이아웃을 사용해야 합니다. |
| --referenceScriptLibraries | Edit 및 Create 페이지에 `_ValidationScriptsPartial`을 추가합니다. |

`h` 스위치를 사용하여 `aspnet-codegenerator controller` 명령에 대한 도움말을 확인합니다.

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요.
