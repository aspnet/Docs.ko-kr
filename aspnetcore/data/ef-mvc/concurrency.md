---
title: '자습서: 동시성 처리 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에는 여러 사용자가 동시에 같은 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d476c836e8d497ca1291992dda38da1fc9f59ed2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054374"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f0c17-103">자습서: 동시성 처리 - ASP.NET MVC 및 EF Core 사용</span><span class="sxs-lookup"><span data-stu-id="f0c17-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f0c17-104">이전 자습서에서 데이터를 업데이트하는 방법을 배웠습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="f0c17-105">이 자습서에는 여러 사용자가 동시에 같은 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="f0c17-106">부서 엔터티를 사용하는 웹 페이지를 만들고 동시성 오류를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="f0c17-107">다음 그림은 동시성 충돌이 발생하는 경우 표시되는 일부 메시지를 포함하여 편집 및 삭제 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![부서 편집 페이지](concurrency/_static/edit-error.png)

![부서 삭제 페이지](concurrency/_static/delete-error.png)

<span data-ttu-id="f0c17-110">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0c17-111">동시성 충돌에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="f0c17-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="f0c17-112">추적 속성 추가</span><span class="sxs-lookup"><span data-stu-id="f0c17-112">Add a tracking property</span></span>
> * <span data-ttu-id="f0c17-113">부서 컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="f0c17-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="f0c17-114">인덱스 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-114">Update Index view</span></span>
> * <span data-ttu-id="f0c17-115">Edit 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-115">Update Edit methods</span></span>
> * <span data-ttu-id="f0c17-116">편집 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-116">Update Edit view</span></span>
> * <span data-ttu-id="f0c17-117">동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="f0c17-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="f0c17-118">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-118">Update the Delete page</span></span>
> * <span data-ttu-id="f0c17-119">세부 정보 및 만들기 보기 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0c17-120">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f0c17-120">Prerequisites</span></span>

* [<span data-ttu-id="f0c17-121">관련 데이터 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="f0c17-122">동시성 충돌</span><span class="sxs-lookup"><span data-stu-id="f0c17-122">Concurrency conflicts</span></span>

<span data-ttu-id="f0c17-123">한 명의 사용자가 편집하기 위해 엔터티의 데이터를 표시한 다음, 다른 사용자가 첫 번째 사용자의 변경 내용이 데이터베이스에 기록되기 전에 동일한 엔터티의 데이터를 업데이트하는 경우 동시성 충돌이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="f0c17-124">이러한 충돌의 감지를 활성화하지 않는 경우 누구든지 데이터베이스를 마지막으로 업데이트하면 다른 사용자의 변경 내용을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="f0c17-125">많은 애플리케이션에서 이 위험은 허용 가능합니다. 적은 수의 사용자 또는 적은 업데이트가 있거나 일부 변경 내용이 덮어쓰여지는지 여부가 실제로 중요하지 않은 경우 동시성에 대한 프로그래밍의 비용은 이점보다 클 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="f0c17-126">이 경우 동시성 충돌을 처리하도록 애플리케이션을 구성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="f0c17-127">비관적 동시성(잠금)</span><span class="sxs-lookup"><span data-stu-id="f0c17-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="f0c17-128">애플리케이션에서 동시성 시나리오에서 실수로 인한 데이터 손실을 방지할 필요가 있는 경우 해당 작업을 수행하는 한 가지 방법은 데이터베이스 잠금을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="f0c17-129">이를 비관적 동시성이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="f0c17-130">예를 들어 데이터베이스에서 행을 읽기 전에 읽기 전용 또는 업데이트 액세스에 대한 잠금을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="f0c17-131">업데이트 액세스에 대한 행을 잠그는 경우 변경 중인 데이터의 복사본을 가져오기 때문에 다른 사용자는 읽기 전용 또는 업데이트 액세스에 대한 행을 잠그도록 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="f0c17-132">읽기 전용 액세스에 대한 행을 잠그는 경우 다른 사용자도 읽기 전용에 대해 잠글 수 있지만 업데이트에 대해서는 잠글 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="f0c17-133">잠금 관리에는 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="f0c17-134">프로그램을 설정하는 데 복잡할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-134">It can be complex to program.</span></span> <span data-ttu-id="f0c17-135">상당한 데이터베이스 관리 리소스가 필요하며 애플리케이션의 사용자 수가 증가할 수록 성능 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="f0c17-136">이러한 이유로 모든 데이터베이스 관리 시스템은 비관적 동시성을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="f0c17-137">Entity Framework Core는 이에 대한 기본 제공 지원을 제공하지 않으며 이 자습서에서는 구현하는 방법을 보여 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="f0c17-138">낙관적 동시성</span><span class="sxs-lookup"><span data-stu-id="f0c17-138">Optimistic Concurrency</span></span>

<span data-ttu-id="f0c17-139">비관적 동시성에 대한 대안은 낙관적 동시성입니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="f0c17-140">낙관적 동시성은 동시성 충돌 발생을 허용한 다음, 그럴 경우 적절하게 반응하는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="f0c17-141">예를 들어 Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![예산을 0으로 변경](concurrency/_static/change-budget.png)

<span data-ttu-id="f0c17-143">Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-143">Before Jane clicks **Save** , John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![시작 날짜를 2013으로 변경](concurrency/_static/change-date.png)

<span data-ttu-id="f0c17-145">Jane이 먼저 **저장** 을 클릭하여 브라우저가 인덱스 페이지로 반환될 때 변경 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![0으로 변경된 예산](concurrency/_static/budget-zero.png)

<span data-ttu-id="f0c17-147">그런 다음, John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="f0c17-148">다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="f0c17-149">몇 가지 옵션에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-149">Some of the options include the following:</span></span>

* <span data-ttu-id="f0c17-150">사용자가 수정한 속성의 추적을 유지하고 데이터베이스에서 해당하는 열만 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="f0c17-151">예제 시나리오에서 서로 다른 속성이 두 사용자에 의해 업데이트되었기 때문에 데이터가 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="f0c17-152">다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다. - 2013년 9월 1일의 시작 날짜 및 0달러의 예산</span><span class="sxs-lookup"><span data-stu-id="f0c17-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="f0c17-153">이 업데이트의 메서드는 데이터 손실이 발생할 수 있는 충돌 수를 줄일 수 있지만 경쟁하는 변경 내용이 동일한 엔터티의 속성에 만들어지는 경우 데이터 손실을 방지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="f0c17-154">Entity Framework가 이 방식으로 작동하는지 여부는 업데이트 코드를 구현하는 방법에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="f0c17-155">엔터티에 대한 모든 기존 속성 값 뿐만 아니라 새 값의 추적을 유지하기 위해 많은 양의 상태를 유지 관리해야 하므로 웹 애플리케이션에서는 종종 실용적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="f0c17-156">서버 리소스가 필요하거나 웹 페이지 자체(예: 숨겨진 필드에) 또는 :::no-loc(cookie):::에 포함되어야 하기 때문에 많은 양의 상태를 유지 관리하는 것은 애플리케이션 성능에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a :::no-loc(cookie):::.</span></span>

* <span data-ttu-id="f0c17-157">Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="f0c17-158">다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 복원된 $350,000.00 값을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="f0c17-159">이를 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="f0c17-160">(클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 이 섹션의 소개에서 언급했듯이 동시성 처리에 대해 아무런 코딩을 수행하지 않는 경우 이는 자동으로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="f0c17-161">John의 변경 내용이 데이터베이스에서 업데이트되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="f0c17-162">일반적으로 오류 메시지를 표시하고, 데이터의 현재 상태를 보여 주고, 여전히 변경하고자 하는 경우 변경 내용을 다시 적용하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="f0c17-163">이를 *저장소 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="f0c17-164">(데이터 저장소 값은 클라이언트에서 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="f0c17-165">이 메서드는 상황에 대한 경고를 받는 사용자 없이 변경 내용을 덮어쓰지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="f0c17-166">동시성 충돌 검색</span><span class="sxs-lookup"><span data-stu-id="f0c17-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="f0c17-167">Entity Framework에서 throw하는 `DbConcurrencyException` 예외를 처리하여 충돌을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="f0c17-168">이러한 예외를 throw하는 시기를 확인하기 위해 Entity Framework에서 충돌을 검색할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="f0c17-169">따라서 데이터베이스와 데이터 모델을 적절하게 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="f0c17-170">충돌 검색을 활성화하기 위한 몇 가지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="f0c17-171">데이터베이스 테이블에서 행이 변경된 시기를 확인하는 데 사용될 수 있는 추적 열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="f0c17-172">그런 다음, SQL Update의 Where 절 또는 Delete 명령에 해당 열을 포함하도록 Entity Framework를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="f0c17-173">추적 열의 데이터 형식은 일반적으로 `rowversion`입니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="f0c17-174">`rowversion` 값은 행이 업데이트될 때마다 증가되는 순차적 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="f0c17-175">Update 또는 Delete 명령에서 Where 절은 추적 열의 원래 값을 포함합니다(원래 행 버전).</span><span class="sxs-lookup"><span data-stu-id="f0c17-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="f0c17-176">업데이트되는 행이 다른 사용자에 의해 변경된 경우 `rowversion` 열의 값은 원래 값과 다르므로 Update 또는 Delete 문은 Where 절 때문에 업데이트할 행을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="f0c17-177">Entity Framework에서 Update 또는 Delete 명령에 의해 업데이트된 행이 없다는 것을 찾은 경우(즉, 영향을 받은 행의 수가 0인 경우) 동시성 충돌로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="f0c17-178">Update 및 Delete 명령의 Where 절에서 테이블의 모든 열의 원래 값을 포함하도록 Entity Framework를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="f0c17-179">첫 번째 옵션에서 행이 먼저 읽혔기 때문에 행의 일부가 변경된 경우 Where 절은 업데이트할 열을 반환하지 않습니다. Entity Framework는 동시성 충돌로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="f0c17-180">많은 열이 있는 데이터베이스 테이블의 경우 이 방법으로 많은 Where 절이 발생할 수 있으며 많은 양의 상태를 유지 관리해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="f0c17-181">앞에서 설명한 것처럼 많은 양의 상태를 유지 관리하는 것은 애플리케이션 성능에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="f0c17-182">따라서 이 방법은 일반적으로 권장되지 않으며 이 자습서에서 사용되는 방법이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="f0c17-183">동시성에 대해 이 방법을 구현하려는 경우 `ConcurrencyCheck` 특성을 추가하여 동시성을 추적하려는 엔터티의 모든 비 기본 키 속성을 표시해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="f0c17-184">해당 변경 내용을 통해 Entity Framework는 Update 및 Delete 문의 SQL Where 절에 모든 열을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="f0c17-185">이 자습서의 나머지 부분에서는 부서 엔터티에 `rowversion` 추적 속성을 추가하고, 컨트롤러와 보기를 만들고, 모든 항목이 올바르게 작동하는지 확인하도록 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="f0c17-186">추적 속성 추가</span><span class="sxs-lookup"><span data-stu-id="f0c17-186">Add a tracking property</span></span>

<span data-ttu-id="f0c17-187">*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-187">In *Models/Department.cs* , add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="f0c17-188">`Timestamp` 특성은 데이터베이스에 전송된 Update 및 Delete 명령의 Where 절에 이 열이 포함되도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="f0c17-189">SQL `rowversion`이 대체하기 전에 이전 버전의 SQL Server가 SQL `timestamp` 데이터 형식을 사용했으므로 특성을 `Timestamp`라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="f0c17-190">`rowversion`에 대한 .NET 유형은 바이트 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="f0c17-191">Fluent API를 사용하는 것을 선호하는 경우 `IsConcurrencyToken` 메서드( *Data/SchoolContext.cs* 에서)를 사용하여 다음 예제와 같이 추적 속성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs* ) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="f0c17-192">속성을 추가하여 데이터베이스 모델을 변경했으므로 다른 마이그레이션을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="f0c17-193">변경 내용을 저장하고 프로젝트를 빌드한 다음, 명령 창에 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="f0c17-194">부서 컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="f0c17-194">Create Departments controller and views</span></span>

<span data-ttu-id="f0c17-195">학생, 강좌 및 강사에 대해 이전에 수행한 것처럼 부서 컨트롤러와 보기를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![부서 스캐폴드](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="f0c17-197">*DepartmentsController.cs* 파일에서 부서 관리자 드롭다운 목록이 강사의 성 보다는 전체 이름을 포함하도록 "FirstMidName"에서 "FullName"으로 네 개의 모든 항목을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="f0c17-198">인덱스 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-198">Update Index view</span></span>

<span data-ttu-id="f0c17-199">스캐폴딩 엔진은 인덱스 보기에 RowVersion 열을 만들었지만 해당 필드는 표시되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="f0c17-200">*Views/Departments/Index.cshtml* 의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="f0c17-201">이는 제목을 "부서"로 변경하고, RowVersion 열을 삭제하고, 관리자의 이름 대신 전체 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="f0c17-202">Edit 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-202">Update Edit methods</span></span>

<span data-ttu-id="f0c17-203">HttpGet `Edit` 메서드 및 `Details` 메서드 모두에 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="f0c17-204">HttpGet `Edit` 메서드에서 관리자에 대해 즉시 로드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="f0c17-205">HttpPost `Edit` 메서드에 대한 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="f0c17-206">코드는 업데이트될 부서 읽기를 시도하여 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="f0c17-207">`FirstOrDefaultAsync` 메서드가 Null을 반환하는 경우 부서가 다른 사용자에 의해 삭제되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="f0c17-208">이 경우 코드는 편집 페이지가 오류 메시지와 함께 다시 표시될 수 있도록 게시된 양식 값을 사용하여 부서 엔터티를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="f0c17-209">대신 부서 필드를 다시 표시하지 않고 오류 메시지만을 표시하는 경우 부서 엔터티를 다시 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="f0c17-210">보기는 숨겨진 필드에 원래 `RowVersion` 값을 저장하고, 이 메서드는 `rowVersion` 매개 변수에서 해당 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="f0c17-211">`SaveChanges`를 호출하기 전에 엔터티에 대한 `OriginalValues` 컬렉션에 해당 원래 `RowVersion` 속성 값을 넣어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="f0c17-212">그런 다음, Entity Framework에서 SQL UPDATE 명령을 만들 때 해당 명령은 원래 `RowVersion` 값이 있는 행을 찾는 WHERE 절을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="f0c17-213">UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) Entity Framework는 `DbUpdateConcurrencyException` 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="f0c17-214">해당 예외에 대한 catch 블록의 코드에서 예외 개체의 `Entries` 속성에서 업데이트된 값이 있는 영향을 받은 부서 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="f0c17-215">`Entries` 컬렉션은 하나의 `EntityEntry` 개체만 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="f0c17-216">해당 개체를 사용하여 사용자가 입력한 새 값 및 현재 데이터베이스 값을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="f0c17-217">코드는 사용자가 편집 페이지에서 입력한 것과 다른 데이터베이스 값이 있는 각 열에 대해 사용자 지정 오류 메시지를 추가합니다(여기에서는 간단히 하기 위해 하나의 필드만 표시됨).</span><span class="sxs-lookup"><span data-stu-id="f0c17-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="f0c17-218">마지막으로 코드는 `departmentToUpdate`의 `RowVersion` 값을 데이터베이스에서 검색된 새 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="f0c17-219">이 새로운 `RowVersion` 값은 편집 페이지가 다시 표시되고, 다음 번에 사용자가 **저장** 을 클릭할 때 숨겨진 필드에 저장되고, 편집 페이지의 다시 표시로 인해 발생하는 동시성 오류만 catch됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save** , only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="f0c17-220">`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="f0c17-221">보기에서 필드에 대한 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).</span><span class="sxs-lookup"><span data-stu-id="f0c17-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="f0c17-222">편집 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-222">Update Edit view</span></span>

<span data-ttu-id="f0c17-223">*Views/Departments/Edit.cshtml* 에서 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-223">In *Views/Departments/Edit.cshtml* , make the following changes:</span></span>

* <span data-ttu-id="f0c17-224">숨겨진 필드를 추가하여 `DepartmentID` 속성에 대한 숨겨진 필드 바로 다음에 `RowVersion` 속성 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="f0c17-225">드롭다운 목록에 "관리자 선택" 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="f0c17-226">동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="f0c17-226">Test concurrency conflicts</span></span>

<span data-ttu-id="f0c17-227">앱을 실행하고 부서 인덱스 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="f0c17-228">영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택한 다음, 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab** , then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="f0c17-229">이제 두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="f0c17-230">첫 번째 브라우저 탭의 필드를 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-230">Change a field in the first browser tab and click **Save**.</span></span>

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="f0c17-232">브라우저에 변경된 값과 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="f0c17-233">두 번째 브라우저 탭에서 필드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-233">Change a field in the second browser tab.</span></span>

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="f0c17-235">**저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-235">Click **Save**.</span></span> <span data-ttu-id="f0c17-236">오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-236">You see an error message:</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

<span data-ttu-id="f0c17-238">**저장** 을 다시 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-238">Click **Save** again.</span></span> <span data-ttu-id="f0c17-239">두 번째 브라우저 탭에 입력한 값이 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="f0c17-240">인덱스 페이지가 나타날 때 저장된 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f0c17-241">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-241">Update the Delete page</span></span>

<span data-ttu-id="f0c17-242">삭제 페이지의 경우 Entity Framework는 비슷한 방식으로 부서를 편집하는 사용자에 의해 발생한 동시성 충돌을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="f0c17-243">HttpGet `Delete` 메서드가 확인 보기를 표시하는 경우 보기는 숨겨진 필드에 원래 `RowVersion` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="f0c17-244">그러면 해당 값을 사용자가 삭제를 확인할 때 호출된 HttpPost `Delete` 메서드에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="f0c17-245">Entity Framework가 SQL DELETE 명령을 만들 때 원래 `RowVersion` 값과 함께 WHERE 절을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="f0c17-246">명령으로 인해 영향을 받은 0개의 행이 발생하는 경우(삭제 확인 페이지가 표시된 후 행이 변경되었음을 의미함) 동시성 예외가 throw되고, HttpGet `Delete` 메서드가 오류 메시지와 함께 확인 페이지를 다시 표시하기 위해 true로 설정된 오류 플래그와 함께 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="f0c17-247">행이 다른 사용자에 의해 삭제되었기 때문에 0개의 행이 영향을 받았을 수도 있습니다. 따라서 이 경우 오류 메시지가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="f0c17-248">부서 컨트롤러의 Delete 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="f0c17-249">*DepartmentsController.cs* 에서 HttpGet `Delete` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-249">In *DepartmentsController.cs* , replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="f0c17-250">메서드는 동시성 오류가 발생한 후 페이지가 다시 표시되고 있는지 여부를 나타내는 선택적 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="f0c17-251">이 플래그가 true이고 지정된 부서가 더 이상 존재하지 않는 경우 다른 사용자에 의해 삭제되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="f0c17-252">이 경우 코드는 인덱스 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="f0c17-253">이 플래그가 true이고 부서가 존재하는 경우 다른 사용자에 의해 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="f0c17-254">이 경우 코드는 `ViewData`를 사용하여 보기에 오류 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="f0c17-255">HttpPost `Delete` 메서드의 코드(`DeleteConfirmed`라는)를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="f0c17-256">방금 바꾼 스캐폴드된 코드에서 이 메서드는 레코드 ID만 허용했습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="f0c17-257">이 매개 변수를 모델 바인더로 만든 부서 엔터티 인스턴스로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="f0c17-258">이는 레코드 키 뿐만 아니라 RowVersion 속성 값에 대한 EF 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="f0c17-259">또한 `DeleteConfirmed`에서 `Delete`로 작업 메서드 이름을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="f0c17-260">스캐폴드된 코드는 HttpPost 메서드에 고유한 서명을 제공하기 위해 이름 `DeleteConfirmed`를 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="f0c17-261">(CLR은 다른 메서드 매개 변수를 갖기 위해 오버로드된 메서드가 필요합니다.) 이제 서명이 고유하므로 MVC 규칙을 유지하고 HttpPost 및 HttpGet delete 메서드에 대해 동일한 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="f0c17-262">부서가 이미 삭제된 경우 `AnyAsync` 메서드에서 false를 반환하고 애플리케이션은 인덱스 메서드로 다시 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="f0c17-263">동시성 오류가 catch되는 경우 코드는 삭제 확인 페이지를 다시 표시하고 동시성 오류 메시지를 표시해야 함을 나타내는 플래그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="f0c17-264">삭제 보기 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-264">Update the Delete view</span></span>

<span data-ttu-id="f0c17-265">*Views/Departments/Delete.cshtml* 에서 스캐폴드된 코드를 DepartmentID 및 RowVersion 속성에 대해 오류 메시지 필드와 숨겨진 필드를 추가하는 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-265">In *Views/Departments/Delete.cshtml* , replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="f0c17-266">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-266">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="f0c17-267">이렇게 하면 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-267">This makes the following changes:</span></span>

* <span data-ttu-id="f0c17-268">`h2` 및 `h3` 제목 사이에 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="f0c17-269">**Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="f0c17-270">RowVersion 필드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="f0c17-271">`RowVersion` 속성에 대해 숨겨진 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="f0c17-272">앱을 실행하고 부서 인덱스 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="f0c17-273">영어 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택한 다음, 첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab** , then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="f0c17-274">첫 번째 창에서 값 중 하나를 변경하고, **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-274">In the first window, change one of the values, and click **Save** :</span></span>

![삭제 전 변경 후 부서 편집 페이지](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="f0c17-276">두 번째 탭에서 **삭제** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="f0c17-277">동시성 오류 메시지가 표시되며 부서 값은 데이터베이스의 현재 값으로 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![동시성 오류가 있는 부서 삭제 확인 페이지](concurrency/_static/delete-error.png)

<span data-ttu-id="f0c17-279">**삭제** 를 다시 클릭하면 부서가 삭제되었음을 보여 주는 인덱스 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="f0c17-280">세부 정보 및 만들기 보기 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-280">Update Details and Create views</span></span>

<span data-ttu-id="f0c17-281">세부 정보 및 만들기 보기에서 스캐폴드된 코드를 필요에 따라 정리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="f0c17-282">*Views/Departments/Details.cshtml* 의 코드를 바꿔 RowVersion 열을 삭제하고 관리자의 전체 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="f0c17-283">*Views/Departments/Create.cshtml* 의 코드를 바꿔 드롭다운 목록에 선택 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="f0c17-284">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="f0c17-284">Get the code</span></span>

[<span data-ttu-id="f0c17-285">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="f0c17-286">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f0c17-286">Additional resources</span></span>

 <span data-ttu-id="f0c17-287">EF Core에서 동시성을 처리하는 방법에 대한 자세한 내용은 [동시성 충돌](/ef/core/saving/concurrency)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f0c17-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f0c17-288">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f0c17-288">Next steps</span></span>

<span data-ttu-id="f0c17-289">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0c17-290">동시성 충돌에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="f0c17-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="f0c17-291">추적 속성 추가</span><span class="sxs-lookup"><span data-stu-id="f0c17-291">Added a tracking property</span></span>
> * <span data-ttu-id="f0c17-292">부서 컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="f0c17-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="f0c17-293">인덱스 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-293">Updated Index view</span></span>
> * <span data-ttu-id="f0c17-294">Edit 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-294">Updated Edit methods</span></span>
> * <span data-ttu-id="f0c17-295">편집 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-295">Updated Edit view</span></span>
> * <span data-ttu-id="f0c17-296">동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="f0c17-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="f0c17-297">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-297">Updated the Delete page</span></span>
> * <span data-ttu-id="f0c17-298">세부 정보 및 만들기 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="f0c17-298">Updated Details and Create views</span></span>

<span data-ttu-id="f0c17-299">강사 및 학생 엔터티에 대한 계층당 테이블 상속을 구현하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="f0c17-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f0c17-300">다음: 계층당 하나의 테이블 상속 구현</span><span class="sxs-lookup"><span data-stu-id="f0c17-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
