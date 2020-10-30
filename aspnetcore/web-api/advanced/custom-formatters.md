---
title: ASP.NET Core Web API에서 포맷터 사용자 지정
author: rick-anderson
description: ASP.NET Core의 웹 API에서 사용자 지정 포맷터를 만들고 사용하는 방법을 알아봅니다.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058924"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API에서 포맷터 사용자 지정

[Kirk Larkin](https://twitter.com/serpent5) 및 [Tom Dykstra](https://github.com/tdykstra)를 기준으로 합니다.

ASP.NET Core MVC는 입력 및 출력 포맷터를 사용하여 Web API에서 데이터 교환을 지원합니다. 입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다. 출력 포맷터는 [응답 형식을 지정](xref:web-api/advanced/formatting)하는 데 사용 됩니다.

프레임워크는 JSON 및 XML에 대한 기본 제공 입력 및 출력 포맷터를 제공합니다. 일반 텍스트에 대한 기본 제공 출력 포맷터는 제공하지만 일반 텍스트에 대한 입력 포맷터는 제공하지 않습니다.

이 문서에서는 사용자 지정 포맷터를 만들어 추가 형식에 대한 지원을 추가하는 방법을 보여줍니다. 사용자 지정 일반 텍스트 입력 포맷터의 예제는 GitHub의 [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) 를 참조 하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>사용자 지정 포맷터를 사용하는 경우

사용자 지정 포맷터를 사용 하 여 기본 제공 포맷터에서 처리 하지 않는 콘텐츠 형식에 대 한 지원을 추가 합니다.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>사용자 지정 포맷터를 사용하는 방법 개요

사용자 지정 포맷터를 만들려면:

* 클라이언트로 전송 된 데이터를 serialize 하려면 출력 포맷터 클래스를 만듭니다.
* 클라이언트에서 받은 데이터를 deserialize 하는 경우 입력 포맷터 클래스를 만듭니다.
* 포맷터 클래스의 인스턴스를 `InputFormatters` 의 및 컬렉션에 추가 `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 합니다.

## <a name="how-to-create-a-custom-formatter-class"></a>사용자 지정 포맷터 클래스를 만드는 방법

포맷터를 만들려면:

* 적절한 기본 클래스에서 클래스를 파생시킵니다. 샘플 앱은 및에서 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 파생 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 됩니다.
* 생성자에서 유효한 미디어 형식 및 인코딩을 지정합니다.
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> 및 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> 메서드를 재정의합니다.
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> 및 `WriteResponseBodyAsync` 메서드를 재정의합니다.

다음 코드는 샘플의 `VcardOutputFormatter` 클래스를 보여 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)줍니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>적절한 기본 클래스에서 파생

텍스트 미디어 형식 (예: vCard)의 경우 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 또는 기본 클래스에서 파생 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 됩니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

이진 형식의 경우 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> 또는 기본 클래스에서 파생 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> 합니다.

### <a name="specify-valid-media-types-and-encodings"></a>유효한 미디어 형식 및 인코딩 지정

생성자에서 `SupportedMediaTypes` 및 `SupportedEncodings` 컬렉션에 추가하여 유효한 미디어 형식 및 인코딩을 지정합니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

포맷터 클래스는 해당 종속성에 대 한 생성자 삽입을 사용할 수 **없습니다** . 예를 들어는 `ILogger<VcardOutputFormatter>` 생성자에 매개 변수로 추가할 수 없습니다. 서비스에 액세스 하려면 메서드에 전달 되는 컨텍스트 개체를 사용 합니다. 이 문서의 코드 예제 및 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) 은이 작업을 수행 하는 방법을 보여 줍니다.

### <a name="override-canreadtype-and-canwritetype"></a>CanReadType 및 CanWriteType 재정의

또는 메서드를 재정의 하 여 deserialize 하거나 serialize 할 형식을 지정 `CanReadType` 합니다 `CanWriteType` . 예를 들어 형식에서 vCard 텍스트를 만들거나 `Contact` 그 반대의 경우도 마찬가지입니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>CanWriteResult 메서드

일부 시나리오에서는가 대신를 `CanWriteResult` 재정의 해야 합니다 `CanWriteType` . 다음 조건이 true인 경우 `CanWriteResult`를 사용합니다.

* 동작 메서드는 모델 클래스를 반환 합니다.
* 런타임 시 반환될 수 있는 파생 클래스가 있습니다.
* 작업에서 반환 된 파생 클래스는 런타임에 알고 있어야 합니다.

예를 들어, 작업 메서드는 다음과 같습니다.

* Signature는 `Person` 형식을 반환 합니다.
* 는 `Student` `Instructor` 에서 파생 된 또는 형식을 반환할 수 있습니다 `Person` . 

포맷터가 개체만 처리 하려면 `Student` <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> 메서드에 제공 된 컨텍스트 개체의 형식을 확인 합니다 `CanWriteResult` . 동작 메서드가 반환 하는 경우 `IActionResult` :

* 를 반드시 사용할 필요는 없습니다 `CanWriteResult` .
* `CanWriteType`메서드는 런타임 형식을 받습니다.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>ReadRequestBodyAsync 및 WriteResponseBodyAsync 재정의

Deserialization 또는 serialization은 또는에서 수행 됩니다 `ReadRequestBodyAsync` `WriteResponseBodyAsync` . 다음 예제에서는 종속성 주입 컨테이너에서 서비스를 가져오는 방법을 보여 줍니다. 생성자 매개 변수에서 서비스를 가져올 수 없습니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>사용자 지정 포맷터를 사용하도록 MVC를 구성하는 방법

사용자 지정 포맷터를 사용하려면 `InputFormatters` 또는 `OutputFormatters` 컬렉션에 포맷터 클래스의 인스턴스를 추가합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

포맷터는 삽입한 순서 대로 평가됩니다. 첫 번째 포맷터가 우선됩니다.

## <a name="the-complete-vcardinputformatter-class"></a>Complete `VcardInputFormatter` 클래스

다음 코드는 샘플의 `VcardInputFormatter` 클래스를 보여 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)줍니다.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>앱 테스트

기본 vCard 입력 및 출력 포맷터를 구현 하는 [이 문서에 대 한 샘플 앱을 실행](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)합니다. 앱은 다음과 유사 하 게 Vcard를 읽고 씁니다.

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

VCard 출력을 보려면 응용 프로그램을 실행 하 고 Accept 헤더가 포함 된 Get 요청 `text/vcard` 을에 보냅니다 `https://localhost:5001/api/contacts` .

VCard를 메모리 내 연락처 컬렉션에 추가 하려면 다음을 수행 합니다.

* `Post` `/api/contacts` Postman과 같은 도구를 사용 하 여 요청을 보냅니다.
* `Content-Type` 헤더를 `text/vcard`으로 설정합니다.
* `vCard`앞의 예제와 같이 서식이 지정 된 본문에 텍스트를 설정 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
