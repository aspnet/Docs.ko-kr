# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 새 프로젝트를 만듭니다.
1. **작업자 서비스** 선택합니다. **새로 만들기** 를 선택합니다.
1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **만들기** 를 선택합니다.
1. **작업자 서비스 만들기** 대화 상자에서 **만들기** 선택합니다.

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 새 프로젝트를 만듭니다.
1. 사이드바에서 **.NET Core** 아래에 있는 **앱** 을 선택합니다.
1. **ASP.NET Core** 아래에 있는 **작업자** 를 선택합니다. **새로 만들기** 를 선택합니다.
1. **대상 프레임워크** 로 **.NET Core 3.0** 이상을 선택합니다. **새로 만들기** 를 선택합니다.
1. **프로젝트 이름** 필드에 이름을 입력합니다. **만들기** 를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 Worker Service(`worker`) 템플릿을 사용합니다. 다음 예제에서는 `ContosoWorker`라는 Worker Service 앱을 만듭니다. 명령이 실행될 때 `ContosoWorker` 앱의 폴더가 자동으로 만들어집니다.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
