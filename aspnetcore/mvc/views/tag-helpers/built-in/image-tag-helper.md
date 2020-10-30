---
title: ASP.NET Core의 이미지 태그 도우미
author: pkellner
description: 이미지 태그 도우미의 사용 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: edc6971ac42756504b5b371ba509d7a657a0c396
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061121"
---
# <a name="image-tag-helper-in-aspnet-core"></a>ASP.NET Core의 이미지 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net)

이미지 태그 도우미는 정적 이미지 파일에 대한 캐시 버스팅 동작을 제공하여 `<img>` 태그를 향상시킵니다.

캐시 버스팅 문자열은 자산의 URL에 추가되는 정적 이미지 파일의 해시를 나타내는 고유한 값입니다. 고유한 문자열은 클라이언트(및 일부 프록시)에게 클라이언트의 캐시가 아닌 호스트 웹 서버에서 이미지를 다시 로드하게 합니다.

이미지 원본(`src`)이 호스트 웹 서버의 정적 파일일 경우:

* 이미지 원본에 고유한 캐시 버스팅 문자열이 쿼리 매개 변수로 추가됩니다.
* 호스트 웹 서버의 파일이 변경되면 갱신된 요청 매개 변수를 포함하는 고유한 요청 URL이 생성됩니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

## <a name="image-tag-helper-attributes"></a>이미지 태그 도우미 특성

### <a name="src"></a>src

이미지 태그 도우미를 활성화하려면 `<img>` 요소에 `src` 특성이 필요합니다.

이미지 원본(`src`)은 서버의 물리적 정적 파일을 가리켜야 합니다. `src`가 원격 URI인 경우 캐시 버스팅 쿼리 문자열 매개 변수가 생성되지 않습니다.

### <a name="asp-append-version"></a>asp-append-version

`asp-append-version`에 `true` 값이 지정되어 `src` 특성과 함께 사용되면 이미지 태그 도우미가 호출됩니다.

다음 예제는 이미지 태그 도우미를 사용합니다.

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

정적 파일이 */wwwroot/images/* 디렉터리에 존재할 경우 생성되는 HTML은 다음과 비슷합니다(해시는 다를 것입니다).

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

매개 변수 `v`에 할당된 값은 디스크에 존재하는 *asplogo.png* 파일의 해시 값입니다. 웹 서버가 정적 파일에 대한 읽기 액세스를 얻을 수 없으면 렌더링된 태그의 `src` 특성에 `v` 매개 변수가 추가되지 않습니다.

## <a name="hash-caching-behavior"></a>해시 캐싱 동작

이미지 태그 도우미는 로컬 웹 서버의 캐시 공급자를 사용하여 해당 파일의 계산된 `Sha512` 해시를 저장합니다. 파일이 여러 번 요청되더라도 해시가 다시 계산되지 않습니다. 캐시는 파일의 `Sha512` 해시가 계산될 때 파일에 첨부된 파일 감시자에 의해 무효화됩니다. 디스크에서 파일이 변경되면 새 해시가 계산되고 캐시됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/memory>
