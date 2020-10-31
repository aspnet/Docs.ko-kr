---
title: '자습서: ASP.NET Core를 사용하여 :::no-loc(Razor)::: Pages 웹앱 만들기'
author: rick-anderson
description: 'Windows에서 Visual Studio, ASP.NET Core 및 EF Core를 사용하여 :::no-loc(Razor)::: Pages 웹앱을 만듭니다.'
ms.author: riande
ms.date: 08/09/2019
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
uid: tutorials/razor-pages/index
ms.openlocfilehash: 0feb60c7e0189bf12d584c3916e0254b3be729c0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058066"
---
# <a name="tutorial-create-a-no-locrazor-pages-web-app-with-aspnet-core"></a><span data-ttu-id="3c290-103">자습서: ASP.NET Core를 사용하여 :::no-loc(Razor)::: Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="3c290-103">Tutorial: Create a :::no-loc(Razor)::: Pages web app with ASP.NET Core</span></span>

<span data-ttu-id="3c290-104">이 시리즈의 자습서는 :::no-loc(Razor)::: Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3c290-104">This series of tutorials explains the basics of building a :::no-loc(Razor)::: Pages web app.</span></span> 

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3c290-105">이 시리즈에는 다음 자습서가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c290-105">This series includes the following tutorials:</span></span>

1. [<span data-ttu-id="3c290-106">:::no-loc(Razor)::: Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="3c290-106">Create a :::no-loc(Razor)::: Pages web app</span></span>](xref:tutorials/razor-pages/razor-pages-start)
1. [<span data-ttu-id="3c290-107">:::no-loc(Razor)::: Pages 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="3c290-107">Add a model to a :::no-loc(Razor)::: Pages app</span></span>](xref:tutorials/razor-pages/model)
1. [<span data-ttu-id="3c290-108">:::no-loc(Razor)::: Pages 스캐폴드(생성)</span><span class="sxs-lookup"><span data-stu-id="3c290-108">Scaffold (generate) :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/page)
1. [<span data-ttu-id="3c290-109">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="3c290-109">Work with a database</span></span>](xref:tutorials/razor-pages/sql)
1. [<span data-ttu-id="3c290-110">:::no-loc(Razor)::: Pages 업데이트</span><span class="sxs-lookup"><span data-stu-id="3c290-110">Update :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/da1)
1. [<span data-ttu-id="3c290-111">검색 추가</span><span class="sxs-lookup"><span data-stu-id="3c290-111">Add search</span></span>](xref:tutorials/razor-pages/search)
1. [<span data-ttu-id="3c290-112">새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="3c290-112">Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
1. [<span data-ttu-id="3c290-113">유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="3c290-113">Add validation</span></span>](xref:tutorials/razor-pages/validation)

<span data-ttu-id="3c290-114">끝에서 영화의 데이터베이스를 표시하고 관리할 수 있는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c290-114">At the end, you'll have an app that can display and manage a database of movies.</span></span>

![샘플 앱의 샘플 페이지](index/_static/sample-page.png)
