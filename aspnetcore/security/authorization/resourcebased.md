---
title: ASP.NET Core에서 리소스 기반 권한 부여
author: scottaddie
description: 권한 부여 특성이 충분 하지 않을 경우 ASP.NET Core 앱에서 리소스 기반 권한 부여를 구현 하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: 202ca681a66ddf7f729d8835e2f77da846583df1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060172"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>ASP.NET Core에서 리소스 기반 권한 부여

권한 부여 전략은 액세스 하는 리소스에 따라 달라 집니다. Author 속성이 있는 문서를 생각해 보세요. 작성자만 문서를 업데이트할 수 있습니다. 따라서 권한 부여 평가를 수행 하기 전에 데이터 저장소에서 문서를 검색 해야 합니다.

특성 평가는 페이지 처리기 또는 문서를 로드 하는 작업을 실행 하기 전에 데이터 바인딩 전에 발생 합니다. 이러한 이유로 특성을 사용한 선언적 권한 부여는 `[Authorize]` 충분 하지 않습니다. 대신 사용자 지정 권한 부여 메서드를 &mdash; *명령적 권한 부여* 라는 스타일로 호출할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"
[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.
::: moniker-end

[권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data) 리소스 기반 권한 부여를 사용 하는 샘플 앱을 포함 합니다.

## <a name="use-imperative-authorization"></a>명령적 권한 부여 사용

권한 부여는 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 서비스로 구현 되 고 클래스 내의 서비스 컬렉션에 등록 됩니다 `Startup` . 이 서비스는 페이지 처리기 나 작업에 대 한 [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 사용할 수 있습니다.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` 에는 두 개의 `AuthorizeAsync` 메서드 오버 로드가 있습니다. 하나는 리소스를 수락 하 고, 다른 하나는 리소스를 수락 하 고 다른 하나는 평가할 요구 사항의 목록입니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

다음 예제에서는 보호할 리소스가 사용자 지정 개체에 로드 됩니다 `Document` . `AuthorizeAsync`현재 사용자가 제공 된 문서를 편집할 수 있는지 여부를 확인 하기 위해 오버 로드가 호출 됩니다. 사용자 지정 "EditPolicy" 권한 부여 정책이 결정 됩니다. 권한 부여 정책을 만드는 방법에 대 한 자세한 내용은 [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies) 를 참조 하세요.

> [!NOTE]
> 다음 코드 샘플에서는 인증을 실행 하 고 속성을 설정 했다고 가정 합니다 `User` .

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>리소스 기반 처리기 작성

리소스 기반 권한 부여에 대 한 처리기 작성은 [일반 요구 사항 처리기를 작성 하](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)는 것과 크게 다르지 않습니다. 사용자 지정 요구 사항 클래스를 만들고 요구 사항 처리기 클래스를 구현 합니다. 요구 사항 클래스를 만드는 방법에 대 한 자세한 내용은 [요구 사항](xref:security/authorization/policies#requirements)을 참조 하세요.

Handler 클래스는 요구 사항과 리소스 형식을 모두 지정 합니다. 예를 들어 및 리소스를 활용 하는 처리기는 `SameAuthorRequirement` `Document` 다음과 같습니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

앞의 예제에서 `SameAuthorRequirement` 가 보다 일반적인 클래스의 특별 한 경우를 가정 `SpecificAuthorRequirement` 합니다. `SpecificAuthorRequirement`클래스 (표시 되지 않음)에는 `Name` 작성자의 이름을 나타내는 속성이 포함 되어 있습니다. `Name`속성을 현재 사용자로 설정할 수 있습니다.

에서 요구 사항 및 처리기를 등록 합니다 `Startup.ConfigureServices` .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>운영 요구 사항

CRUD (만들기, 읽기, 업데이트, 삭제) 작업의 결과에 따라 결정을 내리는 경우 [Operationauthorizationrequirement 사항](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) 도우미 클래스를 사용 합니다. 이 클래스를 사용 하면 각 작업 형식에 대 한 개별 클래스 대신 단일 처리기를 작성할 수 있습니다. 이를 사용 하려면 몇 가지 작업 이름을 제공 합니다.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

처리기는 요구 사항과 리소스를 사용 하 여 다음과 같이 구현 됩니다 `OperationAuthorizationRequirement` `Document` .

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

위의 처리기는 리소스, 사용자의 id 및 요구 사항의 속성을 사용 하 여 작업의 유효성을 검사 합니다 `Name` .

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>작업 리소스 처리기로 챌린지 및 금지

이 섹션에서는 챌린지 및 금지 작업 결과를 처리 하는 방법과 시도 및 금지의 차이점을 보여 줍니다.

작업 리소스 처리기를 호출 하려면 `AuthorizeAsync` 페이지 처리기 또는 작업에서 호출할 때 작업을 지정 합니다. 다음 예에서는 인증 된 사용자가 제공 된 문서를 볼 수 있는지 여부를 확인 합니다.

> [!NOTE]
> 다음 코드 샘플에서는 인증을 실행 하 고 속성을 설정 했다고 가정 합니다 `User` .

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

권한 부여가 성공 하면 문서를 보는 페이지가 반환 됩니다. 권한 부여가 실패 하지만 사용자가 인증 되는 경우를 반환 하면 `ForbidResult` 권한 부여에 실패 했다는 인증 미들웨어에 게 알립니다. `ChallengeResult`인증을 수행 해야 하는 경우이 반환 됩니다. 대화형 브라우저 클라이언트의 경우 사용자를 로그인 페이지로 리디렉션하는 것이 적합할 수 있습니다.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

권한 부여가 성공 하면 문서에 대 한 뷰가 반환 됩니다. 권한 부여가 실패 하는 경우를 반환 하면 `ChallengeResult` 권한 부여에 실패 했다는 인증 미들웨어에 게 알리고 미들웨어는 적절 한 응답을 받을 수 있습니다. 적절 한 응답에서 401 또는 403 상태 코드를 반환할 수 있습니다. 대화형 브라우저 클라이언트의 경우 사용자를 로그인 페이지로 리디렉션하는 것을 의미할 수 있습니다.

::: moniker-end
