---
title: ASP.NET Core의 컨트롤러에 종속성 주입
author: ardalis
description: ASP.NET Core MVC 컨트롤러가 ASP.NET Core의 종속성 주입을 사용하여 해당 생성자를 통해 명시적으로 해당 종속성을 요청하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 1282cd984584be423fba755e64e5d2f1afd2af89
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060614"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET Core의 컨트롤러에 종속성 주입

::: moniker range=">= aspnetcore-3.0"

작성자: [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC 컨트롤러는 생성자를 통해 명시적으로 종속성을 요청합니다. ASP.NET Core는 기본적으로 [DI(종속성 주입 )](xref:fundamentals/dependency-injection)를 지원합니다. DI를 사용하면 앱의 테스트와 유지 관리가 쉬워집니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>생성자 주입

서비스는 생성자 매개 변수로 추가되며, 런타임은 서비스 컨테이너에서 서비스를 해결합니다. 서비스는 일반적으로 인터페이스를 사용하여 정의됩니다. 예를 들어, 현재 시간이 필요한 앱을 고려해보세요. 다음 인터페이스는 `IDateTime` 서비스를 노출합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

다음 코드는 `IDateTime` 인터페이스를 구현합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

서비스를 서비스 컨테이너에 추가합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>에 대한 자세한 내용은 [DI 서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 참조하세요.

다음 코드는 현재 시간을 기준으로 사용자에게 인사말을 표시합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

앱을 실행하면 시간을 기준으로 메시지가 표시됩니다.

## <a name="action-injection-with-fromservices"></a>FromServices를 이용한 작업 주입

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>를 사용하면 생성자 주입을 사용하지 않고 작업 메서드에 직접 서비스를 주입할 수 있습니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>컨트롤러에서 설정 액세스

컨트롤러 내에서 앱 또는 구성 설정에 액세스하는 것은 일반적인 패턴입니다. <xref:fundamentals/configuration/options>에 설명된 *옵션 패턴* 은 설정을 관리하기 위해 선호되는 방법입니다. 일반적으로 컨트롤러에 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 직접 주입하지 마세요.

옵션을 나타내는 클래스를 만듭니다. 다음은 그 예입니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

서비스 컬렉션에 구성 클래스를 추가합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

JSON 형식 파일에서 설정을 읽도록 앱을 구성합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

다음 코드는 서비스 컨테이너에서 `IOptions<SampleWebSettings>` 설정을 요청하고 이를 `Index` 메서드에 사용합니다.

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>추가 리소스

* 컨트롤러에서 종속성을 명시적으로 요청하여 코드를 더 쉽게 테스트할 수 있는 방법을 알아보려면 <xref:mvc/controllers/testing>을 참조하세요.

* [기본 종속성 주입 컨테이너를 타사 구현으로 바꿉니다](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC 컨트롤러는 생성자를 통해 명시적으로 종속성을 요청합니다. ASP.NET Core는 기본적으로 [DI(종속성 주입 )](xref:fundamentals/dependency-injection)를 지원합니다. DI를 사용하면 앱의 테스트와 유지 관리가 쉬워집니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>생성자 주입

서비스는 생성자 매개 변수로 추가되며, 런타임은 서비스 컨테이너에서 서비스를 해결합니다. 서비스는 일반적으로 인터페이스를 사용하여 정의됩니다. 예를 들어, 현재 시간이 필요한 앱을 고려해보세요. 다음 인터페이스는 `IDateTime` 서비스를 노출합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

다음 코드는 `IDateTime` 인터페이스를 구현합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

서비스를 서비스 컨테이너에 추가합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>에 대한 자세한 내용은 [DI 서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 참조하세요.

다음 코드는 현재 시간을 기준으로 사용자에게 인사말을 표시합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

앱을 실행하면 시간을 기준으로 메시지가 표시됩니다.

## <a name="action-injection-with-fromservices"></a>FromServices를 이용한 작업 주입

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>를 사용하면 생성자 주입을 사용하지 않고 작업 메서드에 직접 서비스를 주입할 수 있습니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>컨트롤러에서 설정 액세스

컨트롤러 내에서 앱 또는 구성 설정에 액세스하는 것은 일반적인 패턴입니다. <xref:fundamentals/configuration/options>에 설명된 *옵션 패턴* 은 설정을 관리하기 위해 선호되는 방법입니다. 일반적으로 컨트롤러에 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 직접 주입하지 마세요.

옵션을 나타내는 클래스를 만듭니다. 다음은 그 예입니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

서비스 컬렉션에 구성 클래스를 추가합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

JSON 형식 파일에서 설정을 읽도록 앱을 구성합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

다음 코드는 서비스 컨테이너에서 `IOptions<SampleWebSettings>` 설정을 요청하고 이를 `Index` 메서드에 사용합니다.

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>추가 리소스

* 컨트롤러에서 종속성을 명시적으로 요청하여 코드를 더 쉽게 테스트할 수 있는 방법을 알아보려면 <xref:mvc/controllers/testing>을 참조하세요.

* [기본 종속성 주입 컨테이너를 타사 구현으로 바꿉니다](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end