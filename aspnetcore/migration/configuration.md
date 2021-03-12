---
title: 구성을 ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트에서 ASP.NET Core MVC 프로젝트로 구성을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587946"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="9a2c6-103">구성을 ASP.NET Core로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="9a2c6-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="9a2c6-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="9a2c6-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="9a2c6-105">이전 문서에서는 [ASP.NET mvc 프로젝트를 ASP.NET CORE mvc로 마이그레이션하기](xref:migration/mvc)시작 했습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="9a2c6-106">이 문서에서는 구성을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="9a2c6-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a2c6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="9a2c6-108">설정 구성</span><span class="sxs-lookup"><span data-stu-id="9a2c6-108">Setup configuration</span></span>

<span data-ttu-id="9a2c6-109">ASP.NET Core는 이전 버전의 ASP.NET에서 사용 하는 *global.asax* 및 *web.config* 파일을 더 이상 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="9a2c6-110">이전 버전의 ASP.NET에서는 응용 프로그램 시작 논리가 global.asax 내의 메서드에 배치 되었습니다 `Application_StartUp` . </span><span class="sxs-lookup"><span data-stu-id="9a2c6-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="9a2c6-111">나중에 ASP.NET MVC에서 *Startup.cs* 파일은 프로젝트의 루트에 포함 되었습니다. 응용 프로그램이 시작 될 때이 메서드를 호출 했습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="9a2c6-112">ASP.NET Core는 모든 시작 논리를 *Startup.cs* 파일에 배치 하 여이 방법을 완전히 채택 했습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="9a2c6-113">*web.config* 파일도 ASP.NET Core에서 바뀌었습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="9a2c6-114">이제 *Startup.cs* 에 설명 된 응용 프로그램 시작 절차의 일부로 구성 자체를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="9a2c6-115">구성에서는 XML 파일을 계속 사용할 수 있지만 일반적으로 ASP.NET Core 프로젝트는와 같은 JSON 형식의 파일에 구성 값을 넣습니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9a2c6-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="9a2c6-116">ASP.NET Core 구성 시스템은 환경 변수에 [더 안전 하 고 강력한 위치](xref:security/app-secrets) 를 제공할 수 있는 환경 변수에 쉽게 액세스할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="9a2c6-117">이는 소스 제어로 체크 인하지 말아야 하는 연결 문자열 및 API 키와 같은 비밀의 경우 특히 그렇습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="9a2c6-118">ASP.NET Core의 구성에 대 한 자세한 내용은 [구성](xref:fundamentals/configuration/index) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="9a2c6-119">이 문서에서는 [이전 문서](xref:migration/mvc)에서 부분적으로 마이그레이션된 ASP.NET Core 프로젝트를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="9a2c6-120">구성을 설정 하려면 프로젝트의 루트에 있는 *Startup.cs* 파일에 다음 생성자와 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="9a2c6-121">이 시점에서 다음 문을 추가 해야 하므로 *Startup.cs* 파일은 컴파일되지 않습니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="9a2c6-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9a2c6-122">*appsettings.json* 적절 한 항목 템플릿을 사용 하 여 프로젝트의 루트에 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings JSON 추가](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="9a2c6-124">web.config에서 구성 설정 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="9a2c6-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="9a2c6-125">ASP.NET MVC 프로젝트에는 *web.config* 의 요소에 필요한 데이터베이스 연결 문자열이 포함 되어 `<connectionStrings>` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="9a2c6-126">ASP.NET Core 프로젝트에서는이 정보를 파일에 저장 *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="9a2c6-127">*appsettings.json* 을 열고 다음이 이미 포함 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="9a2c6-128">위에서 설명한 강조 표시 된 줄에서 데이터베이스의 이름을 **_CHANGE_ME** 에서 데이터베이스 이름으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="9a2c6-129">요약</span><span class="sxs-lookup"><span data-stu-id="9a2c6-129">Summary</span></span>

<span data-ttu-id="9a2c6-130">ASP.NET Core는 응용 프로그램의 모든 시작 논리를 단일 파일에 배치 합니다 .이 파일은 필요한 서비스와 종속성을 정의 하 고 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="9a2c6-131">JSON과 같은 다양 한 파일 형식 및 환경 변수를 활용할 수 있는 유연한 구성 기능으로 *web.config* 파일을 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="9a2c6-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
