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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="dc48d-103">AuthorizationMiddleware의 동작 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="dc48d-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="dc48d-104">응용 프로그램은를 등록 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` 하 여 미들웨어가 권한 부여 결과를 처리 하는 방식을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc48d-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="dc48d-105">응용 프로그램은 사용자 지정 미들웨어를 사용 하 여 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc48d-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="dc48d-106">사용자 지정 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="dc48d-106">Return customized responses.</span></span>
* <span data-ttu-id="dc48d-107">기본 챌린지를 개선 하거나 응답을 금지 합니다.</span><span class="sxs-lookup"><span data-stu-id="dc48d-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="dc48d-108">다음 코드에서는 특정 종류의 권한 부여 오류에 대 한 사용자 지정 응답을 반환 하는 권한 부여 처리기의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dc48d-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="dc48d-109">등록 `MyAuthorizationMiddlewareResultHandler` 위치 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dc48d-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->