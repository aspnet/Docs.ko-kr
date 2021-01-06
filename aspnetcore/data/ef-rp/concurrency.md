---
title: 8부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 동시성
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053997"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="a6224-103">8부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 동시성</span><span class="sxs-lookup"><span data-stu-id="a6224-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="a6224-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) 및 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="a6224-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6224-105">이 자습서에는 여러 사용자가 동시에(같은 시간에) 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="a6224-106">동시성 충돌</span><span class="sxs-lookup"><span data-stu-id="a6224-106">Concurrency conflicts</span></span>

<span data-ttu-id="a6224-107">동시성 충돌이 발생한 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="a6224-108">사용자는 엔터티에 대한 편집 페이지를 탐색합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="a6224-109">첫 번째 사용자가 데이터베이스에 변경 내용을 기록하기 전에 다른 사용자가 동일한 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="a6224-110">동시성 검색이 사용하도록 설정되지 않으면 누구든지 데이터베이스를 마지막으로 업데이트하면 다른 사용자의 변경 내용을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="a6224-111">이 위험이 허용 가능한 경우 동시성에 대한 프로그래밍의 비용은 이점보다 클 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="a6224-112">비관적 동시성(잠금)</span><span class="sxs-lookup"><span data-stu-id="a6224-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="a6224-113">동시성 충돌을 방지하는 한 가지 방법은 데이터베이스 잠금을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="a6224-114">이를 비관적 동시성이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="a6224-115">앱은 업데이트하려는 데이터베이스 행을 읽기 전에 잠금을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="a6224-116">업데이트 액세스를 위해 행이 잠긴 후에는 첫 번째 잠금이 해제될 때까지 다른 사용자가 행을 잠글 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="a6224-117">잠금 관리에는 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="a6224-118">프로그래밍하기에 복잡할 수 있으며 사용자 수가 증가하면 성능 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="a6224-119">Entity Framework Core는 이에 대한 기본 제공 지원을 제공하지 않으며 이 자습서에서는 구현하는 방법을 보여 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="a6224-120">낙관적 동시성</span><span class="sxs-lookup"><span data-stu-id="a6224-120">Optimistic concurrency</span></span>

<span data-ttu-id="a6224-121">낙관적 동시성은 동시성 충돌 발생을 허용하고, 이에 적절하게 반응합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="a6224-122">예를 들어, Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![예산을 0으로 변경](concurrency/_static/change-budget30.png)

<span data-ttu-id="a6224-124">Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![시작 날짜를 2013으로 변경](concurrency/_static/change-date30.png)

<span data-ttu-id="a6224-126">Jane이 먼저 **저장** 을 클릭하고 변경 내용이 적용되는 것을 확인합니다. 브라우저에는 예산 금액이 0인 인덱스 페이지가 표시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="a6224-127">John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="a6224-128">다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="a6224-129">사용자가 수정한 속성의 추적을 유지하고 데이터베이스에서 해당하는 열만 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="a6224-130">이 시나리오에서는 데이터가 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="a6224-131">다른 속성이 두 사용자에 의해 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="a6224-132">다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="a6224-133">이 업데이트 메서드는 데이터 손실로 이어질 수 있는 충돌 횟수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="a6224-134">이 방법이 제공하는 몇 가지 단점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="a6224-135">같은 속성에 변경 사항이 적용된 경우 데이터 손실을 방지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="a6224-136">일반적으로 웹앱에서는 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="a6224-137">페치된 값과 새 값을 모두 추적하기 위해 유효한 상태를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="a6224-138">많은 양의 상태를 유지하는 것은 응용 프로그램 성능에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="a6224-139">엔터티에 대한 동시성 감지보다 앱 복잡성이 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="a6224-140">Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="a6224-141">다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 페치된 $350,000.00 값을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="a6224-142">이 방법을 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="a6224-143">(클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 동시성 처리에 대한 코딩을 수행하지 않은 경우 클라이언트 우선이 자동으로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="a6224-144">John의 변경 내용이 데이터베이스에서 업데이트되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="a6224-145">일반적으로 앱은:</span><span class="sxs-lookup"><span data-stu-id="a6224-145">Typically, the app would:</span></span>

  * <span data-ttu-id="a6224-146">오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-146">Display an error message.</span></span>
  * <span data-ttu-id="a6224-147">데이터의 현재 상태를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="a6224-148">사용자가 변경 내용을 다시 적용하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="a6224-149">이를 *저장소 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="a6224-150">(데이터 저장소 값은 클라이언트가 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="a6224-151">이 메서드는 사용자 알림 없이 덮어쓴 변경 내용이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="a6224-152">EF Core의 충돌 검색</span><span class="sxs-lookup"><span data-stu-id="a6224-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="a6224-153">EF Core는 충돌을 검색할 때 `DbConcurrencyException` 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="a6224-154">데이터 모델은 충돌 검색을 사용하도록 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="a6224-155">충돌 검색을 사용하도록 설정하기 위한 몇 가지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="a6224-156">Update 및 Delete 명령의 Where 절에서 [동시성 토큰](/ef/core/modeling/concurrency)으로 구성된 열의 원래 값을 포함하도록 EF Core를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="a6224-157">`SaveChanges`가 호출되면 Where 절은 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 특성으로 주석 지정된 속성의 원래 값을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="a6224-158">Update 문은 행을 처음 읽은 후 동시성 토큰 속성이 변경된 경우 업데이트할 행을 찾지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="a6224-159">EF Core는 이 상황을 동시성 충돌로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="a6224-160">많은 열이 있는 데이터베이스 테이블의 경우 이 방법으로 많은 Where 절이 발생할 수 있으며 많은 양의 상태가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="a6224-161">따라서 이 방법은 일반적으로 권장되지 않으며 이 자습서에서 사용되는 방법이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="a6224-162">데이터베이스 테이블에서 행이 변경된 시기를 확인하는 데 사용될 수 있는 추적 열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="a6224-163">SQL Server 데이터베이스에서 추적 열의 데이터 형식은 `rowversion`입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="a6224-164">`rowversion` 값은 행이 업데이트될 때마다 증가되는 순차적 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a6224-165">Update 또는 Delete 명령에서 Where 절은 추적 열의 원래 값을 포함합니다(원래 행 버전 번호).</span><span class="sxs-lookup"><span data-stu-id="a6224-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="a6224-166">업데이트되는 행이 다른 사용자에 의해 변경된 경우 `rowversion` 열의 값은 원래 값과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="a6224-167">이 경우 Update 또는 Delete 문은 Where 절 때문에 업데이트할 행을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="a6224-168">Update 또는 Delete 명령의 영향을 받는 행이 없는 경우 EF Core는 동시성 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="a6224-169">추적 속성 추가</span><span class="sxs-lookup"><span data-stu-id="a6224-169">Add a tracking property</span></span>

<span data-ttu-id="a6224-170">*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="a6224-171"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 특성은 열을 동시성 추적 열로 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="a6224-172">흐름 API는 추적 속성을 지정하는 대체 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="a6224-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6224-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6224-174">SQL Server 데이터베이스의 경우 엔터티 속성의 `[Timestamp]` 특성은 바이트 배열로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="a6224-175">DELETE 및 UPDATE WHERE 절에 열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="a6224-176">데이터베이스의 열 형식을 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="a6224-177">데이터베이스는 행이 업데이트될 때마다 증가하는 순차적 행 버전 번호를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a6224-178">`Update` 또는 `Delete` 명령에서 `Where` 절은 페치된 행 버전 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="a6224-179">페치된 후 업데이트되는 행이 변경된 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="a6224-180">현재 행 버전 값이 페치된 값과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="a6224-181">`Where` 절이 페치된 행 버전 값을 검색하므로 `Update` 또는 `Delete` 명령은 행을 찾지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="a6224-182">`DbUpdateConcurrencyException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="a6224-183">다음 코드는 부서 이름이 업데이트될 때 EF Core에서 생성된 T-SQL의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="a6224-184">위에 강조 표시된 코드는 `RowVersion`을 포함하는 `WHERE` 절을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="a6224-185">데이터베이스 `RowVersion`이 `RowVersion` 매개 변수(`@p2`)와 다를 경우 행은 업데이트되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="a6224-186">다음 강조 표시된 코드는 정확히 한 개의 행이 업데이트되었음을 확인하는 T-SQL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="a6224-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql)는 마지막 명령문의 영향을 받는 행 수를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="a6224-188">행이 업데이트되지 않으면 EF Core는 `DbUpdateConcurrencyException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6224-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6224-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a6224-190">SQLite 데이터베이스의 경우 엔터티 속성의 `[Timestamp]` 특성은 바이트 배열로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="a6224-191">DELETE 및 UPDATE WHERE 절에 열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="a6224-192">BLOB 열 형식에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="a6224-193">데이터베이스 트리거는 행이 업데이트될 때마다 새 임의 바이트 배열을 사용하여 RowVersion 열을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="a6224-194">`Update` 또는 `Delete` 명령에서 `Where` 절은 RowVersion 열의 페치된 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="a6224-195">페치된 후 업데이트되는 행이 변경된 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="a6224-196">현재 행 버전 값이 페치된 값과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="a6224-197">`Where` 절이 원래 행 버전 값을 검색하므로 `Update` 또는 `Delete` 명령은 행을 찾지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="a6224-198">`DbUpdateConcurrencyException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="a6224-199">데이터베이스 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-199">Update the database</span></span>

<span data-ttu-id="a6224-200">`RowVersion` 속성을 추가하면 마이그레이션이 필요한 데이터 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="a6224-201">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="a6224-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6224-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6224-203">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6224-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6224-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6224-205">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="a6224-206">이 명령은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-206">This command:</span></span>

* <span data-ttu-id="a6224-207">*Migrations/{time stamp}_RowVersion.cs* 마이그레이션 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="a6224-208">*Migrations/SchoolContextModelSnapshot.cs* 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="a6224-209">업데이트는 다음 강조 표시된 코드를 `BuildModel` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="a6224-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6224-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6224-211">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6224-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6224-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6224-213">`Migrations/<timestamp>_RowVersion.cs` 파일을 열고 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="a6224-214">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="a6224-214">The preceding code:</span></span>

  * <span data-ttu-id="a6224-215">임의의 BLOB 값을 사용하여 기존 행을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="a6224-216">행이 업데이트될 때마다 RowVersion 열을 임의 BLOB 값으로 설정하는 데이터베이스 트리거를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="a6224-217">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="a6224-218">부서 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="a6224-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6224-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6224-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6224-220">다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="a6224-221">*Pages/Departments* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="a6224-222">모델 클래스에 `Department`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="a6224-223">새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6224-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6224-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6224-225">*Pages/Departments* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="a6224-226">다음 명령을 실행하여 부서 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="a6224-227">**Windows:**</span><span class="sxs-lookup"><span data-stu-id="a6224-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="a6224-228">**Linux 또는 macOS:**</span><span class="sxs-lookup"><span data-stu-id="a6224-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="a6224-229">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="a6224-230">인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-230">Update the Index page</span></span>

<span data-ttu-id="a6224-231">스캐폴딩 도구는 인덱스 페이지에 대한 `RowVersion` 열을 만들지만, 해당 필드는 프로덕션 앱에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="a6224-232">이 자습서에서는 동시성 처리의 작동 방식을 보여 줄 수 있는 `RowVersion`의 마지막 바이트가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="a6224-233">마지막 바이트는 자체적으로 고유하게 보장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="a6224-234">*Pages\Departments\Index.cshtml* 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="a6224-235">인덱스를 부서로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="a6224-236">바이트 배열의 마지막 바이트만 표시하도록 `RowVersion`을 포함하는 코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="a6224-237">FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="a6224-238">다음 코드에서는 업데이트된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="a6224-239">편집 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-239">Update the Edit page model</span></span>

<span data-ttu-id="a6224-240">*Pages\Departments\Edit.cshtml.cs* 를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="a6224-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>는 `OnGet` 메서드에서 페치될 때 엔터티의 `rowVersion` 값을 사용하여 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="a6224-242">EF Core는 원본 `RowVersion` 값을 포함하는 WHERE 절과 함께 SQL UPDATE 명령을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="a6224-243">UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) `DbUpdateConcurrencyException` 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="a6224-244">앞의 강조 표시된 코드에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="a6224-245">`Department.RowVersion`의 값은 편집 페이지에 대한 Get 요청에서 원래 페치되었을 때 엔터티에 있었던 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="a6224-246">이 값은 편집할 엔터티를 표시하는 Razor 페이지의 숨겨진 필드를 통해 `OnPost` 메서드에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="a6224-247">숨겨진 필드 값은 모델 바인더를 통해 `Department.RowVersion`에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="a6224-248">`OriginalValue`는 EF Core가 Where 절에서 사용할 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="a6224-249">강조 표시된 코드 줄이 실행되기 전에 `OriginalValue`에는 `FirstOrDefaultAsync`가 이 메서드에서 호출될 때 데이터베이스에 있었던 값이 포함되며, 이 값은 편집 페이지에 표시된 값과 다를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="a6224-250">강조 표시된 코드는 EF Core가 SQL UPDATE 문의 Where 절에 표시된 `Department` 엔터티의 원래 `RowVersion` 값을 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="a6224-251">동시성 오류가 발생하면 다음 강조 표시된 코드는 클라이언트 값(이 메서드에 게시된 값)과 데이터베이스 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="a6224-252">다음 코드는 데이터베이스 값이 `OnPostAsync`에 게시된 값과 다른 각 열에 대한 사용자 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="a6224-253">다음 강조 표시된 코드는 `RowVersion` 값을 데이터베이스에서 검색된 새 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="a6224-254">다음에 사용자가 **저장** 을 클릭하면, 편집 페이지의 마지막 표시 이후 발생한 동시성 오류만 catch됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="a6224-255">`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="a6224-256">Razor 페이지에서 필드의 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).</span><span class="sxs-lookup"><span data-stu-id="a6224-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="a6224-257">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-257">Update the Edit page</span></span>

<span data-ttu-id="a6224-258">다음 코드로 *Pages/Departments/Edit.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="a6224-259">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="a6224-259">The preceding code:</span></span>

* <span data-ttu-id="a6224-260">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a6224-261">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-261">Adds a hidden row version.</span></span> <span data-ttu-id="a6224-262">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="a6224-263">디버깅을 위해 `RowVersion`의 마지막 바이트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="a6224-264">`ViewData`를 강력한 형식의 `InstructorNameSL`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="a6224-265">편집 페이지로 동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="a6224-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="a6224-266">영어 부서에 있는 편집의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="a6224-267">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="a6224-268">영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a6224-269">첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="a6224-270">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a6224-271">첫 번째 브라우저 탭의 이름을 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-271">Change the name in the first browser tab and click **Save**.</span></span>

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="a6224-273">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a6224-274">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a6224-275">두 번째 브라우저 탭에서 다른 필드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-275">Change a different field in the second browser tab.</span></span>

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="a6224-277">**저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-277">Click **Save**.</span></span> <span data-ttu-id="a6224-278">데이터베이스 값과 일치하지 않는 모든 필드에 대한 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-278">You see error messages for all fields that don't match the database values:</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error30.png)

<span data-ttu-id="a6224-280">이 브라우저 창은 Name 필드 변경용으로 의도되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="a6224-281">현재 값(Languages)을 복사하여 Name 필드에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="a6224-282">필드 밖을 탭합니다. 클라이언트 쪽 유효성 검사가 오류 메시지를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="a6224-283">**저장** 을 다시 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-283">Click **Save** again.</span></span> <span data-ttu-id="a6224-284">두 번째 브라우저 탭에 입력한 값이 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="a6224-285">인덱스 페이지에 저장된 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="a6224-286">삭제 페이지 업데이트 모델</span><span class="sxs-lookup"><span data-stu-id="a6224-286">Update the Delete page model</span></span>

<span data-ttu-id="a6224-287">다음 코드로 *Pages/Departments/Delete.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="a6224-288">페이지 삭제는 엔터티가 페치된 후 변경될 때 동시성 충돌을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="a6224-289">`Department.RowVersion`은 엔터티가 페치될 때 행 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="a6224-290">EF Core가 SQL DELETE 명령을 만들 때 `RowVersion`과 함께 WHERE 절이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="a6224-291">SQL DELETE 명령의 영향을 받는 행이 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="a6224-292">SQL DELETE 명령의 `RowVersion`이 데이터베이스의 `RowVersion`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="a6224-293">DbUpdateConcurrencyException 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="a6224-294">`OnGetAsync`가 `concurrencyError`를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="a6224-295">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-295">Update the Delete page</span></span>

<span data-ttu-id="a6224-296">*Pages/Departments/Delete.cshtml* 을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="a6224-297">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a6224-298">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a6224-299">오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-299">Adds an error message.</span></span>
* <span data-ttu-id="a6224-300">**Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="a6224-301">마지막 바이트를 표시하도록 `RowVersion`을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="a6224-302">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-302">Adds a hidden row version.</span></span> <span data-ttu-id="a6224-303">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="a6224-304">동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="a6224-304">Test concurrency conflicts</span></span>

<span data-ttu-id="a6224-305">테스트 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-305">Create a test department.</span></span>

<span data-ttu-id="a6224-306">테스트 부서에 있는 삭제의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="a6224-307">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="a6224-308">테스트 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a6224-309">테스트 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="a6224-310">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a6224-311">첫 번째 브라우저 탭의 예산을 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="a6224-312">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a6224-313">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a6224-314">두 번째 탭에서 테스트 부서를 삭제합니다. 동시성 오류는 데이터베이스의 현재 값으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="a6224-315">`RowVersion`이 업데이트되지 않았다면 **삭제** 를 클릭하면 엔터티가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6224-316">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a6224-316">Additional resources</span></span>

* [<span data-ttu-id="a6224-317">EF Core의 동시성 토큰</span><span class="sxs-lookup"><span data-stu-id="a6224-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="a6224-318">EF Core의 동시성 처리</span><span class="sxs-lookup"><span data-stu-id="a6224-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="a6224-319">ASP.NET Core 2.x 소스 디버깅</span><span class="sxs-lookup"><span data-stu-id="a6224-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="a6224-320">다음 단계</span><span class="sxs-lookup"><span data-stu-id="a6224-320">Next steps</span></span>

<span data-ttu-id="a6224-321">이 자습서는 시리즈의 마지막 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="a6224-322">추가 항목은 [이 자습서 시리즈의 MVC 버전](xref:data/ef-mvc/index)에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a6224-323">이전 자습서</span><span class="sxs-lookup"><span data-stu-id="a6224-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6224-324">이 자습서에는 여러 사용자가 동시에(같은 시간에) 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="a6224-325">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="a6224-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="a6224-326">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a6224-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="a6224-327">동시성 충돌</span><span class="sxs-lookup"><span data-stu-id="a6224-327">Concurrency conflicts</span></span>

<span data-ttu-id="a6224-328">동시성 충돌이 발생한 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="a6224-329">사용자는 엔터티에 대한 편집 페이지를 탐색합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="a6224-330">첫 번째 사용자가 DB에 변경 내용을 기록하기 전에 다른 사용자가 동일한 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="a6224-331">동시성 감지가 활성화되지 않으면 동시 업데이트 시 다음이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="a6224-332">마지막 업데이트가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-332">The last update wins.</span></span> <span data-ttu-id="a6224-333">즉, 마지막 업데이트 값이 DB에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="a6224-334">현재 업데이트 중 첫 번째 작업이 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="a6224-335">낙관적 동시성</span><span class="sxs-lookup"><span data-stu-id="a6224-335">Optimistic concurrency</span></span>

<span data-ttu-id="a6224-336">낙관적 동시성은 동시성 충돌 발생을 허용하고, 이에 적절하게 반응합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="a6224-337">예를 들어, Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![예산을 0으로 변경](concurrency/_static/change-budget.png)

<span data-ttu-id="a6224-339">Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![시작 날짜를 2013으로 변경](concurrency/_static/change-date.png)

<span data-ttu-id="a6224-341">Jane이 먼저 **저장** 을 클릭하여 브라우저에 인덱스 페이지가 표시될 때 변경 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![0으로 변경된 예산](concurrency/_static/budget-zero.png)

<span data-ttu-id="a6224-343">John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="a6224-344">다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="a6224-345">낙관적 동시성에는 다음과 같은 옵션이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="a6224-346">사용자가 수정한 속성을 추적하고 DB에서 해당하는 열만 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="a6224-347">이 시나리오에서는 데이터가 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="a6224-348">다른 속성이 두 사용자에 의해 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="a6224-349">다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="a6224-350">이 업데이트 메서드는 데이터 손실로 이어질 수 있는 충돌 횟수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="a6224-351">이 방법은:</span><span class="sxs-lookup"><span data-stu-id="a6224-351">This approach:</span></span>
 
  * <span data-ttu-id="a6224-352">같은 속성에 변경 사항이 적용된 경우 데이터 손실을 방지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="a6224-353">일반적으로 웹앱에서는 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="a6224-354">페치된 값과 새 값을 모두 추적하기 위해 유효한 상태를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="a6224-355">많은 양의 상태를 유지하는 것은 응용 프로그램 성능에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="a6224-356">엔터티에 대한 동시성 감지보다 앱 복잡성이 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="a6224-357">Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="a6224-358">다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 페치된 $350,000.00 값을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="a6224-359">이 방법을 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="a6224-360">(클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 동시성 처리에 대한 코딩을 수행하지 않은 경우 클라이언트 우선이 자동으로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="a6224-361">John의 변경 내용이 DB에서 업데이트되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="a6224-362">일반적으로 앱은:</span><span class="sxs-lookup"><span data-stu-id="a6224-362">Typically, the app would:</span></span>

  * <span data-ttu-id="a6224-363">오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-363">Display an error message.</span></span>
  * <span data-ttu-id="a6224-364">데이터의 현재 상태를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="a6224-365">사용자가 변경 내용을 다시 적용하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="a6224-366">이를 *저장소 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="a6224-367">(데이터 저장소 값은 클라이언트가 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="a6224-368">이 메서드는 사용자 알림 없이 덮어쓴 변경 내용이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="a6224-369">동시성 처리</span><span class="sxs-lookup"><span data-stu-id="a6224-369">Handling concurrency</span></span> 

<span data-ttu-id="a6224-370">속성이 [동시성 토큰](/ef/core/modeling/concurrency)으로 구성되는 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="a6224-371">EF Core는 속성이 페치된 후 수정되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="a6224-372">확인 작업은 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 또는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="a6224-373">속성이 페치된 후 변경된 경우 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="a6224-374">`DbUpdateConcurrencyException`의 throw를 지원하도록 DB 및 데이터 모델을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="a6224-375">속성에서 동시성 충돌 감지</span><span class="sxs-lookup"><span data-stu-id="a6224-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="a6224-376">동시성 충돌은 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 특성을 사용하여 속성 수준에서 감지될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="a6224-377">특성은 모델에서 여러 속성에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="a6224-378">자세한 내용은 [데이터 주석 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6224-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="a6224-379">`[ConcurrencyCheck]` 특성은 이 자습서에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="a6224-380">행에서 동시성 충돌 감지</span><span class="sxs-lookup"><span data-stu-id="a6224-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="a6224-381">동시성 충돌을 감지하기 위해 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 추적 열이 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="a6224-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="a6224-382">`rowversion` :</span></span>

* <span data-ttu-id="a6224-383">SQL Server 한정적입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-383">Is SQL Server specific.</span></span> <span data-ttu-id="a6224-384">다른 데이터베이스는 유사한 기능을 제공하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="a6224-385">DB에서 페치된 이후로 엔터티가 변경되지 않았는지를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="a6224-386">DB는 행이 업데이트될 때마다 증가되는 순차적 `rowversion` 번호를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a6224-387">`Update` 또는 `Delete` 명령에서 `Where` 절은 `rowversion`의 페치된 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="a6224-388">업데이트되는 행이 변경되는 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="a6224-389">`rowversion`은 페치된 값에 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="a6224-390">`Update` 또는 `Delete` 명령은 `Where` 절이 페치된 `rowversion`을 포함하므로 행을 찾지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="a6224-391">`DbUpdateConcurrencyException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="a6224-392">EF Core에서 `Update` 또는 `Delete` 명령에 의해 업데이트된 행이 없는 경우 동시성 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="a6224-393">추적 속성을 부서 엔터티에 추가</span><span class="sxs-lookup"><span data-stu-id="a6224-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="a6224-394">*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="a6224-395">[타임스탬프](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 특성은 이 열이 `Update` 및 `Delete` 명령의 `Where` 절에 포함되어 있음을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="a6224-396">SQL `rowversion` 형식이 대체하기 전에 이전 버전의 SQL Server가 SQL `timestamp` 데이터 형식을 사용했으므로 특성은 `Timestamp`라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="a6224-397">또한 흐름 API가 추적 속성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="a6224-398">다음 코드는 부서 이름이 업데이트될 때 EF Core에서 생성된 T-SQL의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="a6224-399">위에 강조 표시된 코드는 `RowVersion`을 포함하는 `WHERE` 절을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="a6224-400">DB `RowVersion`이 `RowVersion` 매개 변수(`@p2`)와 다를 경우 행은 업데이트되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="a6224-401">다음 강조 표시된 코드는 정확히 한 개의 행이 업데이트되었음을 확인하는 T-SQL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="a6224-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql)는 마지막 명령문의 영향을 받는 행 수를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="a6224-403">행이 업데이트되지 않은 경우 EF Core는 `DbUpdateConcurrencyException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="a6224-404">Visual Studio의 출력 창에서 T-SQL EF Core 생성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="a6224-405">DB 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-405">Update the DB</span></span>

<span data-ttu-id="a6224-406">`RowVersion` 속성을 추가하면 마이그레이션이 필요한 DB 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="a6224-407">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-407">Build the project.</span></span> <span data-ttu-id="a6224-408">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="a6224-409">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="a6224-409">The preceding commands:</span></span>

* <span data-ttu-id="a6224-410">*Migrations/{time stamp}_RowVersion.cs* 마이그레이션 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="a6224-411">*Migrations/SchoolContextModelSnapshot.cs* 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="a6224-412">업데이트는 다음 강조 표시된 코드를 `BuildModel` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="a6224-413">마이그레이션을 실행하여 DB를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="a6224-414">부서 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="a6224-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6224-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6224-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a6224-416">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따르고 `Department`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6224-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6224-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="a6224-418">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="a6224-419">위의 명령은 `Department` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="a6224-420">Visual Studio에서 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="a6224-421">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="a6224-422">부서 인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-422">Update the Departments Index page</span></span>

<span data-ttu-id="a6224-423">스캐폴딩 엔진은 인덱스 페이지에 대한 `RowVersion` 열을 만들지만, 해당 필드는 표시되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="a6224-424">이 자습서에서는 동시성을 이해하는 데 도움을 주기 위해 `RowVersion`의 마지막 바이트가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="a6224-425">마지막 바이트는 고유하게 보장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="a6224-426">실제 앱에서는 `RowVersion` 또는 `RowVersion`의 마지막 바이트가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="a6224-427">인덱스 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-427">Update the Index page:</span></span>

* <span data-ttu-id="a6224-428">인덱스를 부서로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="a6224-429">`RowVersion`을 포함하는 표시를 `RowVersion`의 마지막 바이트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="a6224-430">FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="a6224-431">다음 표시는 업데이트된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="a6224-432">편집 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-432">Update the Edit page model</span></span>

<span data-ttu-id="a6224-433">*Pages\Departments\Edit.cshtml.cs* 를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="a6224-434">동시성 문제를 감지하기 위해 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)가 페치된 엔터티의 `rowVersion` 값으로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="a6224-435">EF Core는 원본 `RowVersion` 값을 포함하는 WHERE 절과 함께 SQL UPDATE 명령을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="a6224-436">UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) `DbUpdateConcurrencyException` 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="a6224-437">위의 코드에서 `Department.RowVersion`은 엔터티가 페치될 때 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="a6224-438">`OriginalValue`는 `FirstOrDefaultAsync`가 이 메서드에서 호출될 때 DB의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="a6224-439">다음 코드는 클라이언트 값(이 메서드에 게시된 값) 및 DB 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="a6224-440">다음 코드는 DB 값이 `OnPostAsync`에 게시된 값과 다른 각 열에 대한 사용자 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="a6224-441">다음 강조 표시된 코드는 `RowVersion` 값을 DB에서 검색된 새 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="a6224-442">다음에 사용자가 **저장** 을 클릭하면, 편집 페이지의 마지막 표시 이후 발생한 동시성 오류만 catch됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="a6224-443">`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="a6224-444">Razor 페이지에서 필드의 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).</span><span class="sxs-lookup"><span data-stu-id="a6224-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="a6224-445">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-445">Update the Edit page</span></span>

<span data-ttu-id="a6224-446">*Pages/Departments/Edit.cshtml* 을 다음 표시로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="a6224-447">위의 표시는:</span><span class="sxs-lookup"><span data-stu-id="a6224-447">The preceding markup:</span></span>

* <span data-ttu-id="a6224-448">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a6224-449">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-449">Adds a hidden row version.</span></span> <span data-ttu-id="a6224-450">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="a6224-451">디버깅을 위해 `RowVersion`의 마지막 바이트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="a6224-452">`ViewData`를 강력한 형식의 `InstructorNameSL`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="a6224-453">편집 페이지로 동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="a6224-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="a6224-454">영어 부서에 있는 편집의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="a6224-455">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="a6224-456">영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a6224-457">첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="a6224-458">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a6224-459">첫 번째 브라우저 탭의 이름을 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-459">Change the name in the first browser tab and click **Save**.</span></span>

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="a6224-461">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a6224-462">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a6224-463">두 번째 브라우저 탭에서 다른 필드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-463">Change a different field in the second browser tab.</span></span>

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="a6224-465">**저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-465">Click **Save**.</span></span> <span data-ttu-id="a6224-466">DB 값과 일치하지 않는 모든 필드에 대한 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-466">You see error messages for all fields that don't match the DB values:</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

<span data-ttu-id="a6224-468">이 브라우저 창은 Name 필드 변경용으로 의도되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="a6224-469">현재 값(Languages)을 복사하여 Name 필드에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="a6224-470">필드 밖을 탭합니다. 클라이언트 쪽 유효성 검사가 오류 메시지를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-470">Tab out. Client-side validation removes the error message.</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/cv.png)

<span data-ttu-id="a6224-472">**저장** 을 다시 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-472">Click **Save** again.</span></span> <span data-ttu-id="a6224-473">두 번째 브라우저 탭에 입력한 값이 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="a6224-474">인덱스 페이지에 저장된 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="a6224-475">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-475">Update the Delete page</span></span>

<span data-ttu-id="a6224-476">다음 코드로 삭제 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="a6224-477">페이지 삭제는 엔터티가 페치된 후 변경될 때 동시성 충돌을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="a6224-478">`Department.RowVersion`은 엔터티가 페치될 때 행 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="a6224-479">EF Core가 SQL DELETE 명령을 만들 때 `RowVersion`과 함께 WHERE 절이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="a6224-480">SQL DELETE 명령의 영향을 받는 행이 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="a6224-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="a6224-481">SQL DELETE 명령의 `RowVersion`이 DB의 `RowVersion`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="a6224-482">DbUpdateConcurrencyException 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="a6224-483">`OnGetAsync`가 `concurrencyError`를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="a6224-484">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="a6224-484">Update the Delete page</span></span>

<span data-ttu-id="a6224-485">*Pages/Departments/Delete.cshtml* 을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="a6224-486">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a6224-487">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a6224-488">오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-488">Adds an error message.</span></span>
* <span data-ttu-id="a6224-489">**Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="a6224-490">마지막 바이트를 표시하도록 `RowVersion`을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="a6224-491">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-491">Adds a hidden row version.</span></span> <span data-ttu-id="a6224-492">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="a6224-493">삭제 페이지로 동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="a6224-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="a6224-494">테스트 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-494">Create a test department.</span></span>

<span data-ttu-id="a6224-495">테스트 부서에 있는 삭제의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="a6224-496">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="a6224-497">테스트 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a6224-498">테스트 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="a6224-499">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a6224-500">첫 번째 브라우저 탭의 예산을 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="a6224-501">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a6224-502">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a6224-503">두 번째 탭에서 테스트 부서를 삭제합니다. 동시성 오류는 DB의 현재 값으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="a6224-504">`RowVersion`이 업데이트되지 않았다면 **삭제** 를 클릭하면 엔터티가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6224-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="a6224-505">데이터 모델을 상속하는 방법은 [상속](xref:data/ef-mvc/inheritance)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6224-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="a6224-506">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a6224-506">Additional resources</span></span>

* [<span data-ttu-id="a6224-507">EF Core의 동시성 토큰</span><span class="sxs-lookup"><span data-stu-id="a6224-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="a6224-508">EF Core의 동시성 처리</span><span class="sxs-lookup"><span data-stu-id="a6224-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="a6224-509">이 자습서의 YouTube 버전(동시성 충돌 처리)</span><span class="sxs-lookup"><span data-stu-id="a6224-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="a6224-510">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="a6224-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="a6224-511">이 자습서의 YouTube 버전(3부)</span><span class="sxs-lookup"><span data-stu-id="a6224-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="a6224-512">이전</span><span class="sxs-lookup"><span data-stu-id="a6224-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

