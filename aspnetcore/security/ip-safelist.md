---
title: ASP.NET Core에 대 한 클라이언트 IP safelist
author: damienbod
description: 승인 된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사 하는 미들웨어 또는 작업 필터를 작성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: dfc134b97bb0976bc682a53d536cd27785550c7d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059665"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core에 대 한 클라이언트 IP safelist

[Damien Bowden](https://twitter.com/damien_bod) 및 [Tom Dykstra](https://github.com/tdykstra)
 
이 문서에서는 ASP.NET Core 앱에서 IP 주소 safelist (허용 목록이 라고도 함)를 구현 하는 세 가지 방법을 보여 줍니다. 함께 제공 되는 샘플 앱은 세 가지 방법을 모두 보여 줍니다. 다음을 사용할 수 있습니다.

* 미들웨어를 통해 모든 요청의 원격 IP 주소를 확인 합니다.
* MVC 작업 필터를 통해 특정 컨트롤러 또는 작업 메서드에 대 한 요청의 원격 IP 주소를 확인 합니다.
* Razor 페이지 필터는 페이지에 대 한 요청의 원격 IP 주소를 확인 합니다 Razor .

각각의 경우 승인 된 클라이언트 IP 주소를 포함 하는 문자열은 앱 설정에 저장 됩니다. 미들웨어 또는 필터:

* 문자열을 배열로 구문 분석 합니다. 
* 원격 IP 주소가 배열에 있는지 확인 합니다.

배열에 IP 주소가 포함 되어 있으면 액세스가 허용 됩니다. 그렇지 않으면 HTTP 403 사용할 수 없음 상태 코드가 반환 됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP 주소 safelist

샘플 앱에서 IP 주소 safelist는 다음과 같습니다.

* `AdminSafeList`파일의 속성으로 정의 *appsettings.json* 됩니다.
* [IPv4 (인터넷 프로토콜 버전 4)](https://wikipedia.org/wiki/IPv4) 및 [IPv6 (인터넷 프로토콜 버전 6)](https://wikipedia.org/wiki/IPv6) 주소를 둘 다 포함할 수 있는 세미콜론으로 구분 된 문자열입니다.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

앞의 예제에서 및의 IPv4 주소와 `127.0.0.1` `192.168.1.5` 의 IPv6 루프백 주소 `::1` (압축 형식 `0:0:0:0:0:0:0:1` )가 허용 됩니다.

## <a name="middleware"></a>미들웨어

`Startup.Configure`메서드는 사용자 지정 `AdminSafeListMiddleware` 미들웨어 형식을 응용 프로그램의 요청 파이프라인에 추가 합니다. Safelist는 .NET Core 구성 공급자를 사용 하 여 검색 되 고 생성자 매개 변수로 전달 됩니다.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

미들웨어는 문자열을 배열로 구문 분석 하 고 배열에서 원격 IP 주소를 검색 합니다. 원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 403을 사용할 수 없음으로 반환 합니다. 이 유효성 검사 프로세스는 HTTP GET 요청에 대해 무시 됩니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>작업 필터

특정 MVC 컨트롤러나 작업 메서드에 대 한 safelist 기반 액세스 제어를 원하는 경우 작업 필터를 사용 합니다. 다음은 그 예입니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

에서 `Startup.ConfigureServices` MVC 필터 컬렉션에 작업 필터를 추가 합니다. 다음 예에서는 `ClientIpCheckActionFilter` 작업 필터를 추가 합니다. Safelist 및 콘솔로 거 인스턴스는 생성자 매개 변수로 전달 됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

그런 다음 작업 필터를 컨트롤러 또는 작업 메서드에 [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) 특성으로 적용할 수 있습니다.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

샘플 앱에서 작업 필터는 컨트롤러의 `Get` 동작 메서드에 적용 됩니다. 다음을 전송 하 여 앱을 테스트 하는 경우:

* HTTP GET 요청에서 특성은 `[ServiceFilter]` 클라이언트 IP 주소의 유효성을 검사 합니다. 작업 메서드에 대 한 액세스가 허용 되는 경우 `Get` 작업 필터 및 작업 메서드에서 다음 콘솔 출력의 변형이 생성 됩니다.

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET이 아닌 HTTP 요청 동사로 `AdminSafeListMiddleware` 미들웨어는 클라이언트 IP 주소의 유효성을 검사 합니다.

## <a name="no-locrazor-pages-filter"></a>Razor 페이지 필터

페이지 앱에 대 한 safelist 기반 access control Razor 을 사용 하려면 Razor 페이지 필터를 사용 합니다. 다음은 그 예입니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

에서 `Startup.ConfigureServices` Razor 페이지 필터를 MVC filters 컬렉션에 추가 하 여 사용 하도록 설정 합니다. 다음 예제에서는 `ClientIpCheckPageFilter` Razor 페이지 필터를 추가 합니다. Safelist 및 콘솔로 거 인스턴스는 생성자 매개 변수로 전달 됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

샘플 앱의 *인덱스* Razor 페이지가 요청 되 면 Razor 페이지 필터는 클라이언트 IP 주소의 유효성을 검사 합니다. 필터는 다음 콘솔 출력의 변형을 생성 합니다.

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/middleware/index>
* [작업 필터](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
