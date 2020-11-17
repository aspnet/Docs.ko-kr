---
title: Blazor 할 일 목록 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
ms.openlocfilehash: 4d5bd977b52dd20ffb62519720106ef0a4560914
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508138"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Blazor 할 일 목록 앱 빌드

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

이 자습서에서는 Blazor 앱을 만들고 수정하는 방법을 보여줍니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 할 일 목록 Blazor 앱 프로젝트 만들기
> * Razor 구성 요소 수정
> * 구성 요소에서 이벤트 처리 및 데이터 바인딩 사용
> * Blazor 앱에서 라우팅 사용

이 자습서의 내용을 마치면 할 일 목록 앱을 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>할 일 목록 Blazor 앱 만들기

1. 명령 셸에서 `TodoList`라는 새 Blazor 앱을 만듭니다.

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   위의 명령은 `TodoList`라는 폴더를 만들어 앱을 저장합니다. `TodoList` 폴더는 프로젝트의 ‘루트 폴더’입니다. 다음 명령을 사용하여 디렉터리를 `TodoList` 폴더로 변경합니다.

   ```dotnetcli
   cd TodoList
   ```

1. 다음 명령을 사용하여 `Pages` 폴더의 앱에 새 `Todo` Razor 구성 요소를 추가합니다.

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor 구성 요소 파일 이름에는 첫 글자를 대문자로 사용해야 합니다. `Pages` 폴더를 열고 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다. 파일 이름이 `Todo.razor`여야 합니다.

1. `Pages/Todo.razor`에서 구성 요소에 초기 태그를 제공합니다.

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. `Todo` 구성 요소를 탐색 모음에 추가합니다.

   `NavMenu` 구성 요소(`Shared/NavMenu.razor`)는 앱의 레이아웃에 사용됩니다. 레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.

   `Shared/NavMenu.razor` 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 `Todo` 구성 요소에 대한 `<NavLink>` 요소를 추가합니다.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. `TodoList` 폴더의 명령 셸에서 `dotnet run` 명령을 실행하여 앱을 빌드하고 실행합니다. `https://localhost:5001/todo`의 새 Todo 페이지를 방문하여 `Todo` 구성 요소에 대한 링크가 작동하는지 확인합니다.

1. 프로젝트 루트(`TodoList` 폴더)에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다. `TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. `Todo` 구성 요소(`Pages/Todo.razor`)로 돌아갑니다.

   * `@code` 블록에 있는 할 일 항목에 대한 필드를 추가합니다. `Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.
   * 순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. 목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다. 순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. 명령 셸에서 실행 중인 앱을 중지합니다. 많은 명령 셸에서 키보드 명령 <kbd>Ctrl</kbd>+<kbd>C</kbd>를 통해 앱을 중지합니다. `dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다. 단추에 이벤트 처리기가 연결되어 있지 않으므로 **`Add todo`** 단추를 선택해도 아무 일도 발생하지 않습니다.

1. `Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 이를 단추 선택에 등록합니다. 단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. 새 할 일 항목의 제목을 가져오기 위해 `@code` 블록의 상단에 `newTodo` 문자열 필드를 추가하고 `<input>` 요소에서 `bind` 특성을 사용하여 이를 텍스트 입력 값에 바인딩합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다. `newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. 명령 셸에서 실행 중인 앱을 중지합니다. `dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다. 할 일 목록에 몇 개의 할 일 항목을 추가하여 새 코드를 테스트합니다.

1. 각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다. 각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다. `@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. 해당 값이 바인딩되었는지 확인하기 위해 `<h3>` 헤더를 수정하여 완료되지 않은(`IsDone`이 `false`) 할 일 항목 수를 표시합니다.

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. 명령 셸에서 실행 중인 앱을 중지합니다. `dotnet run` 명령으로 앱을 다시 빌드하고 실행합니다. 할 일 항목을 추가하여 새 코드를 테스트합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 할 일 목록 Blazor 앱 프로젝트 만들기
> * Razor 구성 요소 수정
> * 구성 요소에서 이벤트 처리 및 데이터 바인딩 사용
> * Blazor 앱에서 라우팅 사용

ASP.NET Core Blazor용 도구에 대해 알아보기

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
