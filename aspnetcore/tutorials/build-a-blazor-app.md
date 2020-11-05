---
title: ':::no-loc(Blazor)::: 할 일 목록 앱 빌드'
author: guardrex
description: ':::no-loc(Blazor)::: 앱을 단계별로 빌드합니다.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 68a38b82f5a89365e4f345a60f1f34b697c027ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060094"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="e3a59-103">:::no-loc(Blazor)::: 할 일 목록 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="e3a59-103">Build a :::no-loc(Blazor)::: todo list app</span></span>

<span data-ttu-id="e3a59-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e3a59-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e3a59-105">이 자습서에서는 :::no-loc(Blazor)::: 앱을 만들고 수정하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-105">This tutorial shows you how to build and modify a :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="e3a59-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3a59-107">할 일 목록 :::no-loc(Blazor)::: 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="e3a59-107">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="e3a59-108">:::no-loc(Razor)::: 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="e3a59-108">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="e3a59-109">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="e3a59-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e3a59-110">:::no-loc(Blazor)::: 앱에서 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="e3a59-110">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="e3a59-111">이 자습서의 내용을 마치면 할 일 목록 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3a59-112">필수 조건</span><span class="sxs-lookup"><span data-stu-id="e3a59-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="e3a59-113">할 일 목록 :::no-loc(Blazor)::: 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e3a59-113">Create a todo list :::no-loc(Blazor)::: app</span></span>

1. <span data-ttu-id="e3a59-114">명령 셸에서 `TodoList`라는 새 :::no-loc(Blazor)::: 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-114">Create a new :::no-loc(Blazor)::: app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="e3a59-115">위의 명령은 `TodoList`라는 폴더를 만들어 앱을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="e3a59-116">`TodoList` 폴더는 프로젝트의 ‘루트 폴더’입니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="e3a59-117">다음 명령을 사용하여 디렉터리를 `TodoList` 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="e3a59-118">다음 명령을 사용하여 `Pages` 폴더의 앱에 새 `Todo` :::no-loc(Razor)::: 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-118">Add a new `Todo` :::no-loc(Razor)::: component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="e3a59-119">:::no-loc(Razor)::: 구성 요소 파일 이름에는 첫 글자를 대문자로 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-119">:::no-loc(Razor)::: component file names require a capitalized first letter.</span></span> <span data-ttu-id="e3a59-120">`Pages` 폴더를 열고 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="e3a59-121">파일 이름이 `Todo.razor`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="e3a59-122">`Pages/Todo.razor`에서 구성 요소에 초기 태그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="e3a59-123">`Todo` 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="e3a59-124">`NavMenu` 구성 요소(`Shared/NavMenu.razor`)는 앱의 레이아웃에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="e3a59-125">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="e3a59-126">`Shared/NavMenu.razor` 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 `Todo` 구성 요소에 대한 `<NavLink>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="e3a59-127">`TodoList` 폴더의 명령 셸에서 `dotnet run` 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="e3a59-128">새 Todo 페이지를 방문하여 `Todo` 구성 요소에 대한 링크가 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-128">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="e3a59-129">프로젝트 루트(`TodoList` 폴더)에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="e3a59-130">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="e3a59-131">`Todo` 구성 요소(`Pages/Todo.razor`)로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="e3a59-132">`@code` 블록에 있는 할 일 항목에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="e3a59-133">`Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="e3a59-134">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="e3a59-135">목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="e3a59-136">순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="e3a59-137">명령 셸에서 실행 중인 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3a59-138">많은 명령 셸에서 키보드 명령 <kbd>Ctrl</kbd>+<kbd>C</kbd>를 통해 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="e3a59-139">`dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3a59-140">단추에 이벤트 처리기가 연결되어 있지 않으므로 **`Add todo`** 단추를 선택해도 아무 일도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="e3a59-141">`Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 이를 단추 선택에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="e3a59-142">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="e3a59-143">새 할 일 항목의 제목을 가져오기 위해 `@code` 블록의 상단에 `newTodo` 문자열 필드를 추가하고 `<input>` 요소에서 `bind` 특성을 사용하여 이를 텍스트 입력 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="e3a59-144">`AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="e3a59-145">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="e3a59-146">명령 셸에서 실행 중인 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3a59-147">`dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3a59-148">할 일 목록에 몇 개의 할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="e3a59-149">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="e3a59-150">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="e3a59-151">`@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="e3a59-152">해당 값이 바인딩되었는지 확인하기 위해 `<h3>` 헤더를 수정하여 완료되지 않은(`IsDone`이 `false`) 할 일 항목 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="e3a59-153">완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="e3a59-154">명령 셸에서 실행 중인 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3a59-155">`dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3a59-156">할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e3a59-157">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e3a59-157">Next steps</span></span>

<span data-ttu-id="e3a59-158">이 자습서에서는 다음 작업 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="e3a59-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3a59-159">할 일 목록 :::no-loc(Blazor)::: 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="e3a59-159">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="e3a59-160">:::no-loc(Razor)::: 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="e3a59-160">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="e3a59-161">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="e3a59-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e3a59-162">:::no-loc(Blazor)::: 앱에서 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="e3a59-162">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="e3a59-163">ASP.NET Core :::no-loc(Blazor):::용 도구에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="e3a59-163">Learn about tooling for ASP.NET Core :::no-loc(Blazor)::::</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
