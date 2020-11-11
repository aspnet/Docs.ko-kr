---
title: HTTP REPL 원격 분석
author: scottaddie
description: HTTP REPL에 의해 수집 된 원격 분석에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502007"
---
# <a name="http-repl-telemetry"></a>HTTP REPL 원격 분석

[HTTP REPL (읽기-평가-인쇄 루프)](xref:web-api/http-repl) 은 사용 데이터를 수집 하는 원격 분석 기능을 포함 합니다. HTTP REPL 팀은 도구를 사용 하 여 개선할 수 있는 방법을 이해 하는 것이 중요 합니다.

## <a name="how-to-opt-out"></a>옵트아웃(opt out)하는 방법

HTTP REPL 원격 분석 기능은 기본적으로 사용 하도록 설정 되어 있습니다. 원격 분석 기능을 옵트아웃하려면 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 환경 변수를 `1` 또는 `true`로 설정합니다.

## <a name="disclosure"></a>공개

HttpRepl 도구를 처음 실행할 때 다음과 비슷한 텍스트를 표시 합니다. 텍스트는 실행 중인 도구의 버전에 따라 약간씩 다를 수 있습니다. 이 "첫 실행" 경험이 Microsoft가 사용자에게 데이터 수집에 대해 알리는 방법입니다.

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a>데이터 요소

원격 분석 기능은 다음과 같습니다.

* 사용자 이름, 전자 메일 주소 또는 Url 등의 개인 데이터를 수집 합니다.
* HTTP 요청 또는 응답을 검색 합니다.

데이터는 Microsoft 서버에 안전 하 게 전송 되 고 제한 된 액세스로 유지 됩니다.

개인 정보 보호는 Microsoft에 중요합니다. 원격 분석 기능이 중요 한 데이터를 수집 하거나 데이터를에서 비보안 방식 또는 부적절 하 게 처리 하는 것으로 의심 되는 경우 다음 작업 중 하나를 수행 합니다.

* [Dotnet/httprepl](https://github.com/dotnet/httprepl/issues) 리포지토리에서 문제를 해결 합니다.
* 조사를 위해에 전자 메일을 보냅니다 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) .

원격 분석 기능은 다음 데이터를 수집 합니다.

| .NET SDK 버전 | 데이터 |
|--------------|------|
| >= 5.0        | 호출의 타임스탬프 |
| >= 5.0        | 지리적 위치를 결정 하는 데 사용 되는 3 옥텟 IP 주소입니다. |
| >= 5.0        | 운영 체제 및 버전 |
| >= 5.0        | 도구가 실행 되 고 있는 RID (런타임 ID)입니다. |
| >= 5.0        | 도구가 컨테이너에서 실행 되 고 있는지 여부를 나타냅니다. |
| >= 5.0        | 해시 된 미디어 Access Control (MAC) 주소: 컴퓨터에 대 한 암호화 (SHA256) 해시 및 고유 ID입니다. |
| >= 5.0        | 커널 버전 |
| >= 5.0        | HTTP REPL 버전입니다. |
| >= 5.0        | 도구가 `help` , 또는 인수를 사용 하 여 시작 되었는지 여부를 나타냅니다 `run` `connect` . 실제 인수 값은 수집 되지 않습니다. |
| >= 5.0        | 호출 되는 명령 (예: `get` ) 및 성공 여부입니다. |
| >= 5.0        | 명령의 경우, `connect` `root` `base` 또는 인수가 제공 되었는지 여부 `openapi` 입니다. 실제 인수 값은 수집 되지 않습니다. |
| >= 5.0        | 명령에 대해 `pref` `get` 또는 `set` 이 실행 되었는지 여부와 액세스 한 기본 설정입니다. 잘 알려진 기본 설정이 아닌 경우 이름이 해시 됩니다. 값이 수집 되지 않습니다. |
| >= 5.0        | 명령의 경우 `set header` 설정 되는 헤더 이름입니다. 잘 알려진 헤더가 아니면 이름이 해시 됩니다. 값이 수집 되지 않습니다. |
| >= 5.0        | 명령에 `connect` 대 한 특수 한 사례가 사용 되었는지 여부 `dotnet new webapi` 와 기본 설정에 따라 무시 되었는지 여부를 나타냅니다. |
| >= 5.0        | 모든 HTTP 명령 (예: GET, POST, PUT)의 경우 각 옵션이 지정 되었는지 여부입니다. 이 옵션의 값은 수집 되지 않습니다. |

## <a name="additional-resources"></a>추가 자료

* [.NET Core SDK 원격 분석](/dotnet/core/tools/telemetry)
* [원격 분석 데이터 .NET Core CLI](https://dotnet.microsoft.com/platform/telemetry)
