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
# <a name="build-a-blazor-todo-list-app"></a>Blazor 할 일 목록 앱 빌드

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

## <a name="create-a-todo-list-blazor-app"></a>할 일 목록 Blazor 앱 만들기

1. 명령 셸에서 `TodoList`라는 새 Blazor 앱을 만듭니다.

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   위의 명령은 `-o|--output` 옵션으로 `TodoList`라는 폴더를 만들어 앱을 저장합니다. `TodoList` 폴더는 프로젝트의 ‘루트 폴더’입니다. 다음 명령을 사용하여 디렉터리를 `TodoList` 폴더로 변경합니다.

   ```dotnetcli
   cd TodoList
   ```

1. 다음 명령을 사용하여 앱에 새 `Todo` Razor 구성 요소를 추가합니다.

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   위 명령의 `-n|--name` 옵션은 새 Razor 구성 요소의 이름을 지정합니다. 새 구성 요소는 `-o|--output` 옵션을 사용하여 프로젝트의 `Pages` 폴더에 만들어집니다.

   > [!IMPORTANT]
   > Razor 구성 요소 파일 이름에는 첫 글자를 대문자로 사용해야 합니다. `Pages` 폴더를 열고 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다. 파일 이름이 `Todo.razor`여야 합니다.

1. 원하는 파일 편집기에서 `Todo` 구성 요소를 열고 `/todo`의 상대 URL을 사용하여 `@page` Razor 지시문을 파일의 맨 위에 추가합니다.

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   `Pages/Todo.razor` 파일을 저장합니다.

1. `Todo` 구성 요소를 탐색 모음에 추가합니다.

   `NavMenu` 구성 요소는 앱의 레이아웃에 사용됩니다. 레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다. `NavLink` 구성 요소는 앱에서 구성 요소 URL을 로드할 때 앱의 UI에서 신호를 제공합니다.

   `NavMenu` 구성 요소의 순서가 지정되지 않은 목록(`<ul>...</ul>`)에서 다음 목록 항목(`<li>...</li>`)과 `Todo` 구성 요소의 `NavLink` 구성 요소를 추가합니다.

   `Shared/NavMenu.razor`의 경우

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   `Shared/NavMenu.razor` 파일을 저장합니다.

1. `TodoList` 폴더의 명령 셸에서 [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) 명령을 실행하여 앱을 빌드하고 실행합니다. 앱이 실행되면 앱의 탐색 모음에서 **`Todo`** 링크를 선택하여 새 Todo 페이지를 방문합니다. 그러면 `/todo`의 페이지가 로드됩니다.

   앱에서 명령 셸을 실행 중인 상태로 둡니다. 파일을 저장할 때마다 앱이 자동으로 다시 빌드됩니다. 브라우저는 컴파일하고 다시 시작하는 동안 앱과의 연결이 일시적으로 끊깁니다. 연결이 다시 설정되면 브라우저의 페이지가 자동으로 다시 로드됩니다.

1. 프로젝트 루트(`TodoList` 폴더)에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다. `TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.

   `TodoItem.cs`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > Visual Studio를 사용하여 `ToDoItem.cs` 파일 및 `ToDoItem` 클래스를 만드는 경우 다음 방식 중 하나를 사용합니다.
   >
   > * Visual Studio가 클래스에 대해 생성하는 네임스페이스를 제거합니다.
   > * 앞의 코드 블록에 있는 **복사** 단추를 사용하여 Visual Studio가 생성한 파일의 모든 콘텐츠를 바꿉니다.

1. `Todo` 구성 요소로 돌아가서 다음 작업을 수행합니다.

   * `@code` 블록에 있는 todo 항목에 대한 필드를 추가합니다. `Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.
   * 순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. 목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다. 순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. `TodoItem.cs` 파일 및 업데이트된 `Pages/Todo.razor` 파일을 저장합니다. 명령 셸에서는 파일이 저장될 때 앱이 자동으로 다시 빌드됩니다. 브라우저에서는 앱에 대한 연결이 일시적으로 해제되고 연결이 다시 설정되면 페이지를 다시 로드합니다.

1. **`Add todo`** 단추에 이벤트 처리기가 연결되어 있지 않으므로 단추를 선택해도 아무 일도 발생하지 않습니다.

1. `Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 단추에 메서드를 등록합니다. 단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. 새 todo 항목의 제목을 가져오려면 `@code` 블록 맨 위에 `newTodo` 문자열 필드를 추가합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   텍스트 `<input>` 요소를 수정하여 `newTodo`를 `@bind` 특성과 바인딩합니다.

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다. `newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. `Pages/Todo.razor` 파일을 저장합니다. 앱은 명령 셸에서 자동으로 다시 빌드됩니다. 브라우저가 앱에 다시 연결한 후에 브라우저에서 페이지가 다시 로드됩니다.

1. 각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다. 각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다. `@todo.Title`을 `@bind`를 사용하여 `todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. `<h3>` 헤더를 업데이트하여 완료되지 않은(`IsDone`이 `false`) todo 항목의 수를 표시합니다.

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. `Pages/Todo.razor` 파일을 저장합니다. 앱은 명령 셸에서 자동으로 다시 빌드됩니다. 브라우저가 앱에 다시 연결한 후에 브라우저에서 페이지가 다시 로드됩니다.

1. 항목을 추가하고 항목을 편집하고 todo 항목을 완료로 표시하여 구성 요소를 테스트합니다.

1. 완료되면 명령 셸에서 앱을 종료합니다. 많은 명령 셸에서 키보드 명령 <kbd>Ctrl</kbd>+<kbd>C</kbd>를 통해 앱을 중지합니다.

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
