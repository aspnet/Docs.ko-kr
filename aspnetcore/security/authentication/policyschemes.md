---
title: ASP.NET Core의 정책 스키마
author: rick-anderson
description: 인증 정책 스키마를 사용 하면 단일 논리 인증 체계를 보다 쉽게 수행할 수 있습니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053230"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="bfc7d-103">ASP.NET Core의 정책 스키마</span><span class="sxs-lookup"><span data-stu-id="bfc7d-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="bfc7d-104">인증 정책 스키마를 사용 하면 단일 논리 인증 체계에서 잠재적으로 여러 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="bfc7d-105">예를 들어 정책 체계는 챌린지에 대해 Google 인증을 사용 하 고 :::no-loc(cookie)::: 다른 모든 항목에 대 한 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-105">For example, a policy scheme might use Google authentication for challenges, and :::no-loc(cookie)::: authentication for everything else.</span></span> <span data-ttu-id="bfc7d-106">인증 정책 스키마는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="bfc7d-107">다른 체계에 인증 작업을 쉽게 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="bfc7d-108">요청에 따라 동적으로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="bfc7d-109">파생 된 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)를 사용 하는 모든 인증 스키마는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="bfc7d-110">는 ASP.NET Core 2.1 이상에서 자동으로 정책 스키마를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="bfc7d-111">구성표의 옵션을 구성 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="bfc7d-112">예</span><span class="sxs-lookup"><span data-stu-id="bfc7d-112">Examples</span></span>

<span data-ttu-id="bfc7d-113">다음 예에서는 하위 수준 스키마를 조합 하는 더 높은 수준의 스키마를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="bfc7d-114">Google 인증은 챌린지에 사용 되며, :::no-loc(cookie)::: 다른 모든 항목에 대해 인증이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-114">Google authentication is used for challenges, and :::no-loc(cookie)::: authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="bfc7d-115">다음 예에서는 요청당 스키마를 동적으로 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bfc7d-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="bfc7d-116">즉, 및 API 인증을 혼합 하는 방법 :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="bfc7d-116">That is, how to mix :::no-loc(cookie):::s and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
