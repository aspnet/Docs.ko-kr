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
# <a name="migrate-configuration-to-aspnet-core"></a>구성을 ASP.NET Core로 마이그레이션

작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)

이전 문서에서는 [ASP.NET mvc 프로젝트를 ASP.NET CORE mvc로 마이그레이션하기](xref:migration/mvc)시작 했습니다. 이 문서에서는 구성을 마이그레이션합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>설정 구성

ASP.NET Core는 이전 버전의 ASP.NET에서 사용 하는 *global.asax* 및 *web.config* 파일을 더 이상 사용 하지 않습니다. 이전 버전의 ASP.NET에서는 응용 프로그램 시작 논리가 global.asax 내의 메서드에 배치 되었습니다 `Application_StartUp` .  나중에 ASP.NET MVC에서 *Startup.cs* 파일은 프로젝트의 루트에 포함 되었습니다. 응용 프로그램이 시작 될 때이 메서드를 호출 했습니다. ASP.NET Core는 모든 시작 논리를 *Startup.cs* 파일에 배치 하 여이 방법을 완전히 채택 했습니다.

*web.config* 파일도 ASP.NET Core에서 바뀌었습니다. 이제 *Startup.cs* 에 설명 된 응용 프로그램 시작 절차의 일부로 구성 자체를 구성할 수 있습니다. 구성에서는 XML 파일을 계속 사용할 수 있지만 일반적으로 ASP.NET Core 프로젝트는와 같은 JSON 형식의 파일에 구성 값을 넣습니다 *appsettings.json* . ASP.NET Core 구성 시스템은 환경 변수에 [더 안전 하 고 강력한 위치](xref:security/app-secrets) 를 제공할 수 있는 환경 변수에 쉽게 액세스할 수도 있습니다. 이는 소스 제어로 체크 인하지 말아야 하는 연결 문자열 및 API 키와 같은 비밀의 경우 특히 그렇습니다. ASP.NET Core의 구성에 대 한 자세한 내용은 [구성](xref:fundamentals/configuration/index) 을 참조 하세요.

이 문서에서는 [이전 문서](xref:migration/mvc)에서 부분적으로 마이그레이션된 ASP.NET Core 프로젝트를 시작 합니다. 구성을 설정 하려면 프로젝트의 루트에 있는 *Startup.cs* 파일에 다음 생성자와 속성을 추가 합니다.

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

이 시점에서 다음 문을 추가 해야 하므로 *Startup.cs* 파일은 컴파일되지 않습니다 `using` .

```csharp
using Microsoft.Extensions.Configuration;
```

*appsettings.json* 적절 한 항목 템플릿을 사용 하 여 프로젝트의 루트에 파일을 추가 합니다.

![AppSettings JSON 추가](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>web.config에서 구성 설정 마이그레이션

ASP.NET MVC 프로젝트에는 *web.config* 의 요소에 필요한 데이터베이스 연결 문자열이 포함 되어 `<connectionStrings>` 있습니다. ASP.NET Core 프로젝트에서는이 정보를 파일에 저장 *appsettings.json* 합니다. *appsettings.json* 을 열고 다음이 이미 포함 되어 있는지 확인 합니다.

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

위에서 설명한 강조 표시 된 줄에서 데이터베이스의 이름을 **_CHANGE_ME** 에서 데이터베이스 이름으로 변경 합니다.

## <a name="summary"></a>요약

ASP.NET Core는 응용 프로그램의 모든 시작 논리를 단일 파일에 배치 합니다 .이 파일은 필요한 서비스와 종속성을 정의 하 고 구성할 수 있습니다. JSON과 같은 다양 한 파일 형식 및 환경 변수를 활용할 수 있는 유연한 구성 기능으로 *web.config* 파일을 대체 합니다.
