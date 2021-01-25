---
title: ASP.NET Core Kestrel 웹 서버를 사용한 요청 드레이닝
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel을 사용한 요청 드레이닝에 관해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253865"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel 웹 서버를 사용한 요청 드레이닝

HTTP 연결을 여는 데 시간이 오래 걸립니다. HTTPS의 경우 리소스도 많이 사용됩니다. 따라서 Kestrel은 HTTP/1.1 프로토콜에 따라 연결을 다시 사용하려고 시도합니다. 연결을 다시 사용하려면 요청 본문이 완전히 사용되어야 합니다. 서버가 리디렉션 또는 404 응답을 반환하는 경우 앱이 항상 HTTP POST 요청 같은 요청 본문을 사용하는 것은 아닙니다. HTTP POST 리디렉션 사례에서:

* 클라이언트가 이미 POST 데이터 일부를 전송했을 수 있습니다.
* 서버가 301 응답을 기록합니다.
* 이전 요청 본문의 POST 데이터를 완전히 읽을 때까지 새 요청에 연결을 사용할 수 없습니다.
* Kestrel은 요청 본문을 드레이닝하려고 시도합니다. 요청 본문을 드레이닝하면 데이터가 처리되지 않고 읽고 삭제됩니다.

드레이닝 프로세스는 연결이 다시 사용되도록 허용하는 것과 남은 데이터를 드레이닝하는 데 걸리는 시간을 절충합니다.

* 드레이닝의 시간 제한은 5초이며, 이는 구성할 수 없습니다.
* `Content-Length` 또는 `Transfer-Encoding` 헤더에 지정된 모든 데이터를 시간 제한 전에 읽지 않으면 연결이 닫힙니다.

때에 따라 응답을 쓰기 전이나 쓴 후에 즉시 요청을 종료하려고 할 수 있습니다. 예를 들어, 클라이언트에는 제한적 데이터 한도가 있을 수 있습니다. 우선적으로 업로드된 데이터를 제한해야 할 수도 있습니다. 이 경우 요청을 종료하려면 컨트롤러, Razor 페이지 또는 미들웨어에서 [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)를 호출합니다.

`Abort` 호출에 관련된 주의 사항이 있습니다.

* 새 연결을 만드는 것은 느리고 비용이 많이 들 수 있습니다.
* 연결이 닫히기 전에 클라이언트가 응답을 읽었다는 보장은 없습니다.
* `Abort` 호출은 드문 경우이며 일반적인 오류가 아닌 심각한 오류 사례용으로 예약되어야 합니다.
  * 특정 문제를 해결해야 하는 경우에만 `Abort`를 호출합니다. 예를 들어, 악의적인 클라이언트가 데이터를 POST하려고 하거나 클라이언트 코드에 대규모 요청 또는 여러 요청을 발생시키는 버그가 있는 경우 `Abort`를 호출합니다.
  * HTTP 404(찾을 수 없음)와 같은 일반적인 오류 상황에서는 `Abort`를 호출하지 마세요.

`Abort`를 호출하기 전에 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A)를 호출하면 서버가 응답 쓰기를 완료합니다. 그러나 클라이언트 동작은 예측할 수 없으며 연결이 중단되기 전에 응답을 읽지 못할 수 있습니다.

프로토콜이 연결을 닫지 않고 개별 요청 스트림을 중단하는 동작을 지원하기 때문에 HTTP/2의 경우 이 프로세스가 다릅니다. 5초 드레이닝 시간 제한이 적용되지 않습니다. 응답을 완료한 후에 읽지 않은 요청 본문 데이터가 있으면 서버는 HTTP/2 RST 프레임을 보냅니다. 추가 요청 본문 데이터 프레임은 무시됩니다.

가능하면 클라이언트가 [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 요청 헤더를 사용하고 서버가 요청 본문을 보내기 시작하기 전에 응답할 때까지 기다리는 것이 좋습니다. 이렇게 하면 클라이언트는 응답을 검사하고 불필요한 데이터를 보내기 전에 중단할 수 있습니다.
