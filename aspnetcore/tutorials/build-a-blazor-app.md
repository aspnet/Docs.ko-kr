---
title: Blazor 할 일 목록 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238309"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="ffe2c-103">Blazor 할 일 목록 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="ffe2c-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="ffe2c-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ffe2c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ffe2c-105">이 자습서에서는 Blazor 앱을 만들고 수정하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="ffe2c-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffe2c-107">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="ffe2c-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ffe2c-108">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="ffe2c-108">Modify Razor components</span></span>
> * <span data-ttu-id="ffe2c-109">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="ffe2c-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ffe2c-110">Blazor 앱에서 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="ffe2c-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="ffe2c-111">이 자습서의 내용을 마치면 할 일 목록 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ffe2c-112">필수 조건</span><span class="sxs-lookup"><span data-stu-id="ffe2c-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="ffe2c-113">할 일 목록 Blazor 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="ffe2c-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="ffe2c-114">명령 셸에서 `TodoList`라는 새 Blazor 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="ffe2c-115">위의 명령은 `-o|--output` 옵션으로 `TodoList`라는 폴더를 만들어 앱을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="ffe2c-116">`TodoList` 폴더는 프로젝트의 ‘루트 폴더’입니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="ffe2c-117">다음 명령을 사용하여 디렉터리를 `TodoList` 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="ffe2c-118">다음 명령을 사용하여 앱에 새 `Todo` Razor 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="ffe2c-119">위 명령의 `-n|--name` 옵션은 새 Razor 구성 요소의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="ffe2c-120">새 구성 요소는 `-o|--output` 옵션을 사용하여 프로젝트의 `Pages` 폴더에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="ffe2c-121">Razor 구성 요소 파일 이름에는 첫 글자를 대문자로 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="ffe2c-122">`Pages` 폴더를 열고 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="ffe2c-123">파일 이름이 `Todo.razor`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="ffe2c-124">원하는 파일 편집기에서 `Todo` 구성 요소를 열고 `/todo`의 상대 URL을 사용하여 `@page` Razor 지시문을 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="ffe2c-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="ffe2c-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="ffe2c-126">`Pages/Todo.razor` 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="ffe2c-127">`Todo` 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ffe2c-128">`NavMenu` 구성 요소는 앱의 레이아웃에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="ffe2c-129">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="ffe2c-130">`NavLink` 구성 요소는 앱에서 구성 요소 URL을 로드할 때 앱의 UI에서 신호를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="ffe2c-131">`NavMenu` 구성 요소의 순서가 지정되지 않은 목록(`<ul>...</ul>`)에서 다음 목록 항목(`<li>...</li>`)과 `Todo` 구성 요소의 `NavLink` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="ffe2c-132">`Shared/NavMenu.razor`의 경우</span><span class="sxs-lookup"><span data-stu-id="ffe2c-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="ffe2c-133">`Shared/NavMenu.razor` 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="ffe2c-134">`TodoList` 폴더의 명령 셸에서 [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) 명령을 실행하여 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="ffe2c-135">앱이 실행되면 앱의 탐색 모음에서 **`Todo`** 링크를 선택하여 새 Todo 페이지를 방문합니다. 그러면 `/todo`의 페이지가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="ffe2c-136">앱에서 명령 셸을 실행 중인 상태로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-136">Leave the app running the command shell.</span></span> <span data-ttu-id="ffe2c-137">파일을 저장할 때마다 앱이 자동으로 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="ffe2c-138">브라우저는 컴파일하고 다시 시작하는 동안 앱과의 연결이 일시적으로 끊깁니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="ffe2c-139">연결이 다시 설정되면 브라우저의 페이지가 자동으로 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="ffe2c-140">프로젝트 루트(`TodoList` 폴더)에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="ffe2c-141">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="ffe2c-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="ffe2c-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="ffe2c-143">Visual Studio를 사용하여 `ToDoItem.cs` 파일 및 `ToDoItem` 클래스를 만드는 경우 다음 방식 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="ffe2c-144">Visual Studio가 클래스에 대해 생성하는 네임스페이스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="ffe2c-145">앞의 코드 블록에 있는 **복사** 단추를 사용하여 Visual Studio가 생성한 파일의 모든 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="ffe2c-146">`Todo` 구성 요소로 돌아가서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="ffe2c-147">`@code` 블록에 있는 todo 항목에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="ffe2c-148">`Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ffe2c-149">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="ffe2c-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="ffe2c-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="ffe2c-151">목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ffe2c-152">순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="ffe2c-153">`TodoItem.cs` 파일 및 업데이트된 `Pages/Todo.razor` 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="ffe2c-154">명령 셸에서는 파일이 저장될 때 앱이 자동으로 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="ffe2c-155">브라우저에서는 앱에 대한 연결이 일시적으로 해제되고 연결이 다시 설정되면 페이지를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="ffe2c-156">**`Add todo`** 단추에 이벤트 처리기가 연결되어 있지 않으므로 단추를 선택해도 아무 일도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="ffe2c-157">`Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 단추에 메서드를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="ffe2c-158">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="ffe2c-159">새 todo 항목의 제목을 가져오려면 `@code` 블록 맨 위에 `newTodo` 문자열 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="ffe2c-160">텍스트 `<input>` 요소를 수정하여 `newTodo`를 `@bind` 특성과 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="ffe2c-161">`AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ffe2c-162">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="ffe2c-163">`Pages/Todo.razor` 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="ffe2c-164">앱은 명령 셸에서 자동으로 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="ffe2c-165">브라우저가 앱에 다시 연결한 후에 브라우저에서 페이지가 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="ffe2c-166">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ffe2c-167">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ffe2c-168">`@todo.Title`을 `@bind`를 사용하여 `todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="ffe2c-169">`<h3>` 헤더를 업데이트하여 완료되지 않은(`IsDone`이 `false`) todo 항목의 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="ffe2c-170">완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="ffe2c-171">`Pages/Todo.razor` 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="ffe2c-172">앱은 명령 셸에서 자동으로 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="ffe2c-173">브라우저가 앱에 다시 연결한 후에 브라우저에서 페이지가 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="ffe2c-174">항목을 추가하고 항목을 편집하고 todo 항목을 완료로 표시하여 구성 요소를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="ffe2c-175">완료되면 명령 셸에서 앱을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="ffe2c-176">많은 명령 셸에서 키보드 명령 <kbd>Ctrl</kbd>+<kbd>C</kbd>를 통해 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ffe2c-177">다음 단계</span><span class="sxs-lookup"><span data-stu-id="ffe2c-177">Next steps</span></span>

<span data-ttu-id="ffe2c-178">이 자습서에서는 다음 작업 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="ffe2c-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ffe2c-179">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="ffe2c-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ffe2c-180">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="ffe2c-180">Modify Razor components</span></span>
> * <span data-ttu-id="ffe2c-181">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="ffe2c-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ffe2c-182">Blazor 앱에서 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="ffe2c-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="ffe2c-183">ASP.NET Core Blazor용 도구에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="ffe2c-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
