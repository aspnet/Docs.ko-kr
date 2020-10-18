---
title: AuthorizationMiddleware의 동작 사용자 지정
author: pranavkm
ms.author: prkrishn
description: 이 문서에서는 AuthorizationMiddleware의 결과 처리를 사용자 지정 하는 방법을 설명 합니다.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156771"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>AuthorizationMiddleware의 동작 사용자 지정

응용 프로그램은를 등록 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` 하 여 미들웨어가 권한 부여 결과를 처리 하는 방식을 사용자 지정할 수 있습니다. 응용 프로그램은 사용자 지정 미들웨어를 사용 하 여 다음을 수행할 수 있습니다.

* 사용자 지정 응답을 반환 합니다.
* 기본 챌린지를 개선 하거나 응답을 금지 합니다.

다음 코드에서는 특정 종류의 권한 부여 오류에 대 한 사용자 지정 응답을 반환 하는 권한 부여 처리기의 예를 보여 줍니다.

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

등록 `MyAuthorizationMiddlewareResultHandler` 위치 `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->