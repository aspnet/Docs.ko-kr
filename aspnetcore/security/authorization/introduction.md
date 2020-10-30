---
title: ASP.NET Core 권한 부여 소개
author: rick-anderson
description: 권한 부여의 기본 사항과 ASP.NET Core apps에서 권한 부여가 작동 하는 방식에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060250"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="e563e-103">ASP.NET Core 권한 부여 소개</span><span class="sxs-lookup"><span data-stu-id="e563e-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="e563e-104">권한 부여는 사용자가 수행할 수 있는 작업을 결정 하는 프로세스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="e563e-105">예를 들어 관리자가 문서 라이브러리를 만들고, 문서를 추가 하 고, 문서를 편집 하 고, 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="e563e-106">라이브러리를 사용 하는 관리자가 아닌 사용자는 문서를 읽을 수 있는 권한만 부여 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="e563e-107">권한 부여는 직교 하 고 인증과 독립적입니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="e563e-108">그러나 권한 부여에는 인증 메커니즘이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="e563e-109">인증은 사용자를 확인 하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="e563e-110">인증으로 현재 사용자에 대해 하나 이상의 ID가 만들어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="e563e-111">ASP.NET Core의 인증에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="e563e-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="e563e-112">권한 부여 유형</span><span class="sxs-lookup"><span data-stu-id="e563e-112">Authorization types</span></span>

<span data-ttu-id="e563e-113">ASP.NET Core 권한 부여는 간단한 선언적 [역할과](xref:security/authorization/roles) 풍부한 [정책 기반](xref:security/authorization/policies) 모델을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="e563e-114">권한 부여는 요구 사항으로 표현 되며, 처리기는 요구 사항에 대 한 사용자의 클레임을 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="e563e-115">명령적 검사는 사용자가 액세스 하려고 하는 리소스의 사용자 id와 속성을 모두 평가 하는 간단한 정책 또는 정책을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="e563e-116">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="e563e-116">Namespaces</span></span>

<span data-ttu-id="e563e-117">및 특성을 비롯 한 권한 부여 구성 요소 `AuthorizeAttribute` `AllowAnonymousAttribute` 는 `Microsoft.AspNetCore.Authorization` 네임 스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e563e-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="e563e-118">[간단한 권한 부여](xref:security/authorization/simple)에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e563e-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
