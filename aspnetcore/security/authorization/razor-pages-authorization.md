---
title: Razor ASP.NET Core의 페이지 권한 부여 규칙
author: rick-anderson
description: 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙으로 페이지에 대 한 액세스를 제어 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060185"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a>Razor ASP.NET Core의 페이지 권한 부여 규칙

::: moniker range=">= aspnetcore-3.0"

페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다. 이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다. 이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 [ cookie 없이 ASP.NET Core Identity 인증 ](xref:security/authentication/cookie)을 사용 합니다. 이 항목에 표시 된 개념과 예제는를 사용 하는 앱에 동일 하 게 적용 ASP.NET Core Identity 됩니다. 를 사용 하려면 ASP.NET Core Identity 의 지침을 따르세요 <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>페이지에 액세스 하기 위한 권한 부여 필요

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> 는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` . 자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>페이지의 폴더에 액세스 하기 위한 권한 부여 필요

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>영역 페이지에 액세스 하기 위한 권한 부여 필요

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로의 영역 페이지에를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다. 예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts* 입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>영역 폴더에 액세스 하기 위한 권한 부여 필요

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 영역에를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다. 예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리* 입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>페이지에 대 한 익명 액세스 허용

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>페이지의 폴더에 대 한 익명 액세스 허용

규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항

페이지의 폴더에 권한 부여가 필요 하다 고 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

그러나 반대의 경우는 유효 하지 않습니다. 익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

개인 페이지에 대 한 권한 부여가 실패 합니다. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다. 이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다. 이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 [ cookie 없이 ASP.NET Core Identity 인증 ](xref:security/authentication/cookie)을 사용 합니다. 이 항목에 표시 된 개념과 예제는를 사용 하는 앱에 동일 하 게 적용 ASP.NET Core Identity 됩니다. 를 사용 하려면 ASP.NET Core Identity 의 지침을 따르세요 <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>페이지에 액세스 하기 위한 권한 부여 필요

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> 는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` . 자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>페이지의 폴더에 액세스 하기 위한 권한 부여 필요

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>영역 페이지에 액세스 하기 위한 권한 부여 필요

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로의 영역 페이지에를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다. 예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts* 입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>영역 폴더에 액세스 하기 위한 권한 부여 필요

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 영역에를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다. 예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리* 입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>페이지에 대 한 익명 액세스 허용

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>페이지의 폴더에 대 한 익명 액세스 허용

를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항

권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

그러나 반대의 경우는 유효 하지 않습니다. 익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

개인 페이지에 대 한 권한 부여가 실패 합니다. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
