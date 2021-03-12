---
title: ASP.NET Core 및 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 이상은 ASP.NET Core 3.1 이상에서 작동합니다.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588271"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core 및 Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

작성자: [Patrick Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>ASP.NET Core에서 Entity Framework 6 사용

새로 개발하는 경우 [Entity Framework Core](/ef/)를 사용해야 합니다. [다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample)에서는 종료된 앱을 ASP.NET Core로 마이그레이션하는 데 사용할 수 있는 [EF6(Entity Framework 6)](/ef/ef6)을 사용합니다.

## <a name="additional-resources"></a>추가 자료

* [Entity Framework - 코드 기반 구성](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) 및 [Tom Dykstra](https://github.com/tdykstra)

이 문서에는 ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 방법을 보여 줍니다.    

## <a name="overview"></a>개요 

Entity Framework 6을 사용하려면 Entity Framework 6에서 .NET Core를 지원하지 않으므로 .NET Framework에 대해 프로젝트를 컴파일해야 합니다. 플랫폼 간 기능이 필요한 경우 [Entity Framework Core](/ef/)로 업그레이드해야 합니다.  

ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 권장되는 방법은 EF6 컨텍스트 및 모델 클래스를 .NET Framework를 대상으로 하는 클래스 라이브러리 프로젝트에 넣는 것입니다. ASP.NET Core 프로젝트에서 클래스 라이브러리에 참조를 추가합니다. 샘플 [EF6 및 ASP.NET Core 프로젝트가 있는 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.    

.NET Core 프로젝트는 *Enable-Migrations* 와 같은 EF6 명령이 요구하는 모든 기능을 지원하지 않으므로 ASP.NET Core 프로젝트에 EF6 컨텍스트를 배치할 수 없습니다.    

EF6 컨텍스트를 찾는 프로젝트 형식에 관계없이 EF6 명령줄 도구만 EF6 컨텍스트에서 작동합니다. 예를 들어 `Scaffold-DbContext`는 Entity Framework Core에서만 사용할 수 있습니다. 데이터베이스를 EF6 모델로 리버스 엔지니어링해야 하는 경우 <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>를 참조하세요.    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>ASP.NET Core 프로젝트에서 전체 프레임워크 및 EF6 참조 

ASP.NET Core 프로젝트는 .NET Framework를 대상으로 하고 EF6을 참조해야 합니다. 예를 들어 ASP.NET Core 프로젝트의 *.csproj* 파일은 다음 예제와 유사합니다(파일의 관련 부분만 표시됨).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

새 프로젝트를 만들 때 **ASP.NET Core 웹 애플리케이션(.NET Framework)** 템플릿을 사용합니다.    

## <a name="handle-connection-strings"></a>연결 문자열 처리    

EF6 클래스 라이브러리 프로젝트에서 사용할 EF6 명령줄 도구에는 컨텍스트를 인스턴스화할 수 있도록 기본 생성자가 필요합니다. 그러나 ASP.NET Core 프로젝트에서 사용할 연결 문자열을 지정하려는 경우 컨텍스트 생성자에 연결 문자열로 전달할 수 있는 매개 변수가 있어야 합니다. 예를 들면 다음과 같습니다.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

EF6 컨텍스트에는 매개 변수가 없는 생성자가 없으므로 EF6 프로젝트는 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>의 구현을 제공해야 합니다. EF6 명령줄 도구는 해당 구현을 찾아 사용하므로 컨텍스트를 인스턴스화할 수 있습니다. 예를 들면 다음과 같습니다.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

이 샘플 코드에서 `IDbContextFactory` 구현은 하드 코딩된 연결 문자열을 전달합니다. 이것은 명령줄 도구에서 사용할 연결 문자열입니다. 클래스 라이브러리가 호출 애플리케이션이 사용하는 것과 동일한 연결 문자열을 사용하도록 하는 전략을 구현하려고 합니다. 예를 들어, 두 프로젝트의 환경 변수에서 값을 가져올 수 있습니다.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>ASP.NET Core 프로젝트에서 종속성 주입 설정  

Core 프로젝트의 *Startup.cs* 파일에서 `ConfigureServices`에 종속성 주입(DI)에 대한 EF6 컨텍스트를 설정합니다. EF 컨텍스트 개체는 요청당 수명에 대해 범위가 지정되어야 합니다.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

그러면 DI를 사용하여 컨트롤러에서 컨텍스트의 인스턴스를 가져올 수 있습니다. 코드는 EF Core 컨텍스트에 대해 작성한 코드와 유사합니다.    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>애플리케이션 예제   

작업 중인 애플리케이션 예제는 이 문서와 함께 제공되는 [샘플 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.    

Visual Studio에서 다음 단계에 따라 이 샘플을 처음부터 만들 수 있습니다.    

* 솔루션을 만듭니다.    

* **추가** > **새 프로젝트** > **웹** > **ASP.NET Core 웹 애플리케이션**    
  * 프로젝트 템플릿 선택 대화 상자의 드롭다운에서 API 및 .NET Framework 선택 

* **추가** > **새 프로젝트** > **Windows 데스크톱** > **클래스 라이브러리(.NET Framework)**  

* 두 프로젝트에 대한 **PMC(패키지 관리자 콘솔)** 에서 `Install-Package Entityframework` 명령을 실행합니다.    

* 클래스 라이브러리 프로젝트에서 데이터 모델 클래스 및 컨텍스트 클래스와 `IDbContextFactory`의 구현을 만듭니다.    

* 클래스 라이브러리 프로젝트에 대한 PMC에서 `Enable-Migrations` 및 `Add-Migration Initial` 명령을 실행합니다. ASP.NET Core 프로젝트를 시작 프로젝트로 설정한 경우 `-StartupProjectName EF6`을 이러한 명령에 추가합니다. 

* Core 프로젝트에서 클래스 라이브러리 프로젝트에 프로젝트 참조를 추가합니다.    

* Core 프로젝트의 *Startup.cs* 에서 DI에 대한 컨텍스트를 등록합니다.    

* Core 프로젝트의 *appsettings.json* 에서 연결 문자열을 추가합니다.  

* Core 프로젝트에서 데이터를 읽고 쓸 수 있는지 확인하는 컨트롤러 및 뷰를 추가합니다. (ASP.NET Core MVC 스캐폴딩은 클래스 라이브러리에서 참조된 EF6 컨텍스트와 작동하지 않습니다.)

::: moniker-end
