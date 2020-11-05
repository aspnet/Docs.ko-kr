---
title: ASP.NET Core 지역화 문제 해결
author: hishamco
description: ASP.NET Core 앱에서 지역화 관련 문제를 진단하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053620"
---
# <a name="troubleshoot-aspnet-core-localization"></a>ASP.NET Core 지역화 문제 해결

작성자: [Hisham Bin Ateya](https://github.com/hishamco)

이 문서에서는 ASP.NET Core 앱 지역화 문제를 진단하는 방법에 대한 지침을 제공합니다.

## <a name="localization-configuration-issues"></a>지역화 구성 문제

**지역화 미들웨어 순서**  
지역화 미들웨어가 예상대로 정렬되지 않으므로 앱이 지역화되지 않을 수 있습니다.

이 문제를 해결하려면 해당 지역화 미들웨어가 MVC 미들웨어 전에 등록되었는지 확인합니다. 그렇지 않으면 지역화 미들웨어는 적용되지 않습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**지역화 리소스 경로가 없음**

**RequestCultureProvider에서 지원되는 문화권이 등록된 문화권과 한 번 일치하지 않음**  

## <a name="resource-file-naming-issues"></a>리소스 파일 이름 지정 문제

ASP.NET Core에는 지역화 리소스 파일 이름 지정에 대해 미리 정의된 규칙 지침이 있고 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에서 자세히 설명합니다.

## <a name="missing-resources"></a>누락된 리소스

리소스를 찾을 수 없는 일반적인 원인은 다음과 같습니다.

- `resx` 파일 또는 로컬라이저 요청에서 리소스 이름의 철자가 잘못되었습니다.
- 리소스가 일부 언어의 경우 `resx`에서 누락되었지만 다른 언어에서 존재합니다.
- 여전히 문제가 있는 경우 누락된 리소스에 대한 자세한 내용은 `Debug` 로그 수준의 지역화 로그 메시지를 확인하세요.

_**힌트:** `CookieRequestCultureProvider`를 사용하는 경우 지역화 cookie 값 안의 문화권에서 작은따옴표를 사용하지 않았는지 확인합니다. 예를 들어 `c='en-UK'|uic='en-US'`가 잘못된 cookie 값인 반면, `c=en-UK|uic=en-US`는 유효합니다._

## <a name="resources--class-libraries-issues"></a>리소스 및 클래스 라이브러리 문제

기본적으로 ASP.NET Core는 클래스 라이브러리가 [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)를 통해 해당 리소스 파일을 찾을 수 있는 방법을 제공합니다.

클래스 라이브러리와 관련된 일반적인 문제는 다음과 같습니다.
- 라이브러리 클래스에서 `ResourceLocationAttribute`가 누락되면 `ResourceManagerStringLocalizerFactory`가 리소스를 검색하지 못합니다.
- 리소스 파일 이름 지정 자세한 내용은 [리소스 파일 이름 지정 문제](#resource-file-naming-issues) 섹션을 참조하세요.
- 클래스 라이브러리의 루트 네임스페이스 변경 자세한 내용은 [루트 네임스페이스 문제](#root-namespace-issues) 섹션을 참조하세요.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider가 예상대로 작동하지 않습니다.

`RequestLocalizationOptions` 클래스에는 세 가지 기본 공급 기업이 포함됩니다.

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1)를 통해 앱에서 지역화 문화권을 제공하는 방법을 사용자 지정할 수 있습니다. 기본 공급 기업이 사용자 요구 사항을 충족하지 못할 때 `CustomRequestCultureProvider`를 사용합니다.

- 사용자 지정 공급 기업이 제대로 작동하지 않는 일반적인 원인은 `RequestCultureProviders` 목록의 첫 번째 공급 기업이 아니라는 점입니다. 이 문제를 해결하려면:

- 다음과 같이 `RequestCultureProviders` 목록에서 0의 위치에 있는 사용자 지정 공급 기업을 삽입합니다.

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- `AddInitialRequestCultureProvider` 확장 메서드를 사용하여 사용자 지정 공급 기업을 초기 공급 기업으로 설정합니다.

## <a name="root-namespace-issues"></a>루트 네임스페이스 문제

어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다르면 지역화가 기본적으로 작동하지 않습니다. 이 문제를 방지하려면 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에 자세히 설명된 대로 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)를 사용합니다.

> [!WARNING]
> 이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다. 예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다. 

## <a name="resources--build-action"></a>리소스 및 빌드 작업

지역화에 대해 리소스 파일을 사용하는 경우 적절한 빌드 작업이 포함되어야 합니다. 해당 리소스 파일이 **포함 리소스** 여야 하며, 그렇지 않으면 `ResourceStringLocalizer`가 이러한 리소스를 찾을 수 없습니다.
