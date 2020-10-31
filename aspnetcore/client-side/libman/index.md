---
title: LibMan을 사용하여 ASP.NET Core에서 클라이언트 쪽 라이브러리 획득
author: scottaddie
description: 라이브러리 관리자(LibMan)를 사용하여 ASP.NET Core 프로젝트에 클라이언트 쪽 라이브러리 자산을 설치하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 62b6859b0a8ad0f98a2684f21c0f68dbbd67c4c1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054660"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>LibMan을 사용하여 ASP.NET Core에서 클라이언트 쪽 라이브러리 획득

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

라이브러리 관리자(LibMan)는 가벼운 클라이언트 쪽 라이브러리 획득 도구입니다. LibMan은 파일 시스템에서 또는 [CDN(콘텐츠 전송 네트워크)](https://wikipedia.org/wiki/Content_delivery_network)에서 인기 있는 라이브러리 및 프레임워크를 다운로드합니다. 지원되는 CDN은 [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/) 및 [unpkg](https://unpkg.com/#/)입니다. 선택한 라이브러리 파일은 ASP.NET Core 프로젝트 내부의 적절한 위치에 페치 및 배치됩니다.

## <a name="libman-use-cases"></a>LibMan 사용 사례

LibMan은 다음과 같은 이점을 제공합니다.

* 필요한 라이브러리 파일만 다운로드됩니다.
* 라이브러리의 파일 하위 집합을 획득하기 위해 [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) 및 [WebPack](https://webpack.js.org) 같은 추가 도구가 반드시 필요한 것은 아닙니다.
* 파일은 작업 또는 수동 파일 복사를 빌드하도록 재정렬하지 않고도 특정 위치에 배치할 수 있습니다.

LibMan의 이점에 대한 자세한 내용은 [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)(Visual Studio 2017의 최신 프런트 엔드 웹 개발: LibMan 세그먼트)를 참조하세요.

LibMan은 패키지 관리 시스템이 아닙니다. npm 또는 [yarn](https://yarnpkg.com) 같은 패키지 관리자를 이미 사용 중인 경우 계속 사용하시면 됩니다. LibMan은 이러한 도구를 대체하기 위해 개발된 것이 아닙니다.

## <a name="additional-resources"></a>추가 자료

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
