---
title: SignalR API 디자인 고려 사항
author: anurse
description: SignalR앱 버전 간 호환성을 위해 api를 설계 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059652"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR API 디자인 고려 사항

[Andrew Stanton-간호사](https://twitter.com/anurse)

이 문서에서는 기반 Api를 빌드하기 위한 지침을 제공 SignalR 합니다.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>사용자 지정 개체 매개 변수를 사용 하 여 이전 버전과의 호환성 보장

SignalR클라이언트 또는 서버에서 허브 메서드에 매개 변수를 추가 하는 것은 *주요 변경 사항* 입니다. 즉, 이전 클라이언트/서버에서 적절 한 수의 매개 변수를 사용 하지 않고 메서드를 호출 하려고 하면 오류가 발생 합니다. 그러나 사용자 지정 개체 매개 변수에 속성을 추가 하는 것은 주요 변경 사항이 **아닙니다** . 이를 사용 하 여 클라이언트 또는 서버의 변경 내용에 대해 복원 력이 있는 호환 Api를 디자인할 수 있습니다.

예를 들어 다음과 같은 서버 쪽 API를 살펴보겠습니다.

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript 클라이언트는 다음과 같이를 사용 하 여이 메서드를 호출 합니다 `invoke` .

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

나중에 서버 메서드에 두 번째 매개 변수를 추가 하는 경우 이전 클라이언트는이 매개 변수 값을 제공 하지 않습니다. 다음은 그 예입니다.

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

이전 클라이언트에서이 메서드를 호출 하려고 하면 다음과 같은 오류가 발생 합니다.

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

서버에 다음과 같은 로그 메시지가 표시 됩니다.

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

이전 클라이언트는 매개 변수를 하나만 보냈지만 최신 서버 API에는 두 개의 매개 변수가 필요 합니다. 사용자 지정 개체를 매개 변수로 사용 하면 더 많은 유연성이 제공 됩니다. 사용자 지정 개체를 사용 하도록 원래 API를 다시 디자인 해 보겠습니다.

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

이제 클라이언트는 개체를 사용 하 여 메서드를 호출 합니다.

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

매개 변수를 추가 하는 대신 개체에 속성을 추가 합니다 `TotalLengthRequest` .

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

이전 클라이언트에서 단일 매개 변수를 보낼 때 추가 `Param2` 속성은 그대로 유지 됩니다 `null` . 를 확인 하 `Param2` `null` 고 기본값을 적용 하 여 이전 클라이언트에서 보낸 메시지를 검색할 수 있습니다. 새 클라이언트는 두 매개 변수를 모두 보낼 수 있습니다.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

클라이언트에 정의 된 메서드에 대해 동일한 기술이 작동 합니다. 서버 쪽에서 사용자 지정 개체를 보낼 수 있습니다.

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

클라이언트 쪽에서 `Message` 매개 변수를 사용 하는 대신 속성에 액세스 합니다.

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

나중에 메시지 보낸 사람을 페이로드에 추가 하기로 결정 한 경우에는 개체에 속성을 추가 합니다.

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

이전 클라이언트에는 값이 필요 하지 않으므로 `Sender` 무시 됩니다. 새 클라이언트는 새 속성을 읽도록 업데이트 하 여이를 수락할 수 있습니다.

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

이 경우 새 클라이언트도 값을 제공 하지 않는 이전 서버를 허용 합니다 `Sender` . 이전 서버는 값을 제공 하지 않으므로 `Sender` 클라이언트는 액세스 하기 전에 존재 하는지 확인 합니다.
