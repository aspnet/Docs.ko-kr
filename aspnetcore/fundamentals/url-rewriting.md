---
title: ASP.NET Core에서 URL 재작성 미들웨어
author: rick-anderson
description: ASP.NET Core 애플리케이션에서 URL 재작성 미들웨어로 URL 재작성 및 리디렉션 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: e7bd5f4d61661dd23eb0907f896d0d32b7799aac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061303"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>ASP.NET Core에서 URL 재작성 미들웨어

작성자: [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.

URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다. URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다. URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.

* 서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.
* 여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.
* 들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.
* SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.
* 친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.
* 안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.
* 외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.

> [!NOTE]
> URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다. 가능한 경우 규칙의 수와 복잡성을 제한합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>URL 리디렉션 및 URL 재작성

*URL 리디렉션* 과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다. ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.

*URL 리디렉션* 은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다. 이 경우 서버를 왕복해야 합니다. 클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.

`/resource`가 `/different-resource`로 *리디렉션* 되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다. 클라이언트는 버전 1의 경로 /v1/api로 서비스를 요청합니다. 서버는 다시 버전 2의 /v2/api라는 서비스의 임시 경로로 302 (임시 이동) 응답을 보냅니다. 클라이언트는 리디렉션 URL로 서비스에 대한 두 번째 요청을 만듭니다. 서버는 200 (정상) 상태 코드를 응답합니다.](url-rewriting/_static/url_redirect.png)

요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.

* *301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다. *301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*

* *302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다. 302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.

상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.

*URL 재작성* 은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다. URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다. 다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.

`/resource`가 `/different-resource`에 *다시 작성* 되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.

클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다. 클라이언트는 버전 1의 경로 /v1/api로 서비스를 요청합니다. 요청 URL은 버전 2의 경로 /v2/api에서 서비스에 접근하도록 재작성됩니다. 서비스는 200 (정상) 상태 코드를 클라이언트에 응답합니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL 재작성 예제 응용 프로그램

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다. 이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.

## <a name="when-to-use-url-rewriting-middleware"></a>URL 재작성 미들웨어를 사용해야 하는 경우

다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.

* Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)
* Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)
* [Nginx의 URL 재작성](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.

IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.

* 미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.

  서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다. 바로 이런 시나리오에서 대신 미들웨어를 사용합니다.
* 미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.

  벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.

## <a name="package"></a>패키지

URL 다시 작성 미들웨어는 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱에 포함됩니다.

## <a name="extension-and-options"></a>확장 및 옵션

각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다. 처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다. `RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>www 이외 요청을 www로 리디렉션

이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다. [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다. [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다. 오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다. 상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.

### <a name="url-redirect"></a>URL 리디렉션

요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다. 첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다. 두 번째 매개 변수는 대체 문자열입니다. 필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다. 상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음* 으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다. 그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다. 리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다. 브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다. 샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.

* 두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.
* 응답 본문에 리디렉션 URL이 표시됩니다.

URL이 *리디렉션* 되면 서버로의 왕복이 수행됩니다.

> [!WARNING]
> 리디렉션 규칙을 설정할 때에는 주의하세요. 리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다. 따라서 *무한 리디렉션 루프* 를 실수로 만들기 쉽습니다.

원본 요청: `/redirect-rule/1234/5678`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다. 그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다. 마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다. 따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다. 즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.

대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다. 첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다. 예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다. 규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.

### <a name="url-redirect-to-a-secure-endpoint"></a>보안 엔드포인트에 대한 URL 리디렉션

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다. 상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음* 으로 설정됩니다. 포트가 제공되지 않는 경우 다음과 같이 수행됩니다.

* 미들웨어가 기본적으로 `null`로 설정됩니다.
* 체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.

다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다. 미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정합니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> 추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다. 자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.

예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다. 먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다. 모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다. 자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.

`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL 재작성

URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다. 첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다. 두 번째 매개 변수는 대체 문자열입니다. 세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

원래 요청: `/rewrite-rule/1234/5678`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.

`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다. 따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.

| 경로                               | 일치 |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | 예   |
| `/my-cool-redirect-rule/1234/5678` | 예   |
| `/anotherredirect-rule/1234/5678`  | 예   |

반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다. 다음 표에는 일치에서의 차이가 나와 있습니다.

| 경로                              | 일치 |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 예   |
| `/my-cool-rewrite-rule/1234/5678` | 아니요    |
| `/anotherrewrite-rule/1234/5678`  | 아니요    |

표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다. 여기서 `\d`는 *숫자 하나와 일치* 함을 뜻합니다. 그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치* 함을 나타냅니다. 따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다. 이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다. 재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다. 즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다. 원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.

리소스를 가져오기 위해 서버를 왕복하지 않습니다. 리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다. 클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다. 클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.

> [!NOTE]
> 일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다. 최대한 빠른 응용 프로그램 응답을 위해서는:
>
> * 재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.
> * 일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

<xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다. 규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다. mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.

<xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다. 응답 상태 코드는 *302 - 있음* 입니다.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

원본 요청: `/apache-mod-rules-redirect/1234`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL 재작성 모듈 규칙

IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다. 규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다. Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다. IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다. IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.

<xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다. 응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

원본 요청: `/iis-rules-rewrite/1234`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

#### <a name="unsupported-features"></a>지원되지 않는 기능

ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.

* 아웃바운드 규칙
* 사용자 지정 서버 변수
* 와일드카드
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>지원되는 서버 변수

미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다. 이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다. 재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>메서드 기반 규칙

메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다. `Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다. 값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.

| 컨텍스트 결과 다시 작성               | 작업                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(기본값) | 규칙 적용을 계속합니다.                                         |
| `RuleResult.EndResponse`             | 규칙 적용을 중지하고 응답을 보냅니다.                       |
| `RuleResult.SkipRemainingRules`      | 규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

예제 응용 프로그램은 *.xml* 로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다. `/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다. 상태 코드는 *301 - 영구적으로 이동됨* 으로 설정됩니다. 브라우저에서 */xmlfiles/file.xml* 에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다. 리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다. 그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

또한 이 방법은 요청을 다시 작성할 수도 있습니다. 샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다. 정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule 기반 규칙

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다. `IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다. 구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다. `extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png* , *.jpg* , 또는 *.gif* 중 하나이어야 합니다. 만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다. *image.png* 에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다. *image.jpg* 에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다. 상태 코드는 *301 - 영구적으로 이동됨* 으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

원본 요청: `/image.png`

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

원본 요청: `/image.jpg`

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>정규식 예제

| Goal | 정규식 문자열 및<br>일치 예제 | 대체 문자열 및<br>출력 예제 |
| ---- | ------------------------------- | -------------------------------------- |
| 경로를 쿼리 문자열로 재작성 | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| 후행 슬래시 제거 | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 후행 슬래시 적용 | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 특정 요청 재작성 방지 | `^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`<br>예: `/resource.htm`<br>아니요: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| URL 세그먼트 재정렬 | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| URL 세그먼트 대체 | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.

URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다. URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다. URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.

* 서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.
* 여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.
* 들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.
* SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.
* 친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.
* 안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.
* 외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.

> [!NOTE]
> URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다. 가능한 경우 규칙의 수와 복잡성을 제한합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>URL 리디렉션 및 URL 재작성

*URL 리디렉션* 과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다. ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.

*URL 리디렉션* 은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다. 이 경우 서버를 왕복해야 합니다. 클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.

`/resource`가 `/different-resource`로 *리디렉션* 되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다. 클라이언트는 버전 1의 경로 /v1/api로 서비스를 요청합니다. 서버는 다시 버전 2의 /v2/api라는 서비스의 임시 경로로 302 (임시 이동) 응답을 보냅니다. 클라이언트는 리디렉션 URL로 서비스에 대한 두 번째 요청을 만듭니다. 서버는 200 (정상) 상태 코드를 응답합니다.](url-rewriting/_static/url_redirect.png)

요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.

* *301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다. *301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*

* *302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다. 302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.

상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.

*URL 재작성* 은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다. URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다. 다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.

`/resource`가 `/different-resource`에 *다시 작성* 되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.

클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다. 클라이언트는 버전 1의 경로 /v1/api로 서비스를 요청합니다. 요청 URL은 버전 2의 경로 /v2/api에서 서비스에 접근하도록 재작성됩니다. 서비스는 200 (정상) 상태 코드를 클라이언트에 응답합니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL 재작성 예제 응용 프로그램

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다. 이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.

## <a name="when-to-use-url-rewriting-middleware"></a>URL 재작성 미들웨어를 사용해야 하는 경우

다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.

* Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)
* Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)
* [Nginx의 URL 재작성](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.

IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.

* 미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.

  서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다. 바로 이런 시나리오에서 대신 미들웨어를 사용합니다.
* 미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.

  벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.

## <a name="package"></a>패키지

프로젝트에 미들웨어를 포함시키려면 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지가 포함된 프로젝트 파일의 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 패키지 참조를 추가합니다.

`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 `Microsoft.AspNetCore.Rewrite` 패키지에 프로젝트 참조를 추가합니다.

## <a name="extension-and-options"></a>확장 및 옵션

각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다. 처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다. `RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>www 이외 요청을 www로 리디렉션

이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다. [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다. [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다. 오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다. 상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.

### <a name="url-redirect"></a>URL 리디렉션

요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다. 첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다. 두 번째 매개 변수는 대체 문자열입니다. 필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다. 상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음* 으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다. 그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다. 리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다. 브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다. 샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.

* 두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.
* 응답 본문에 리디렉션 URL이 표시됩니다.

URL이 *리디렉션* 되면 서버로의 왕복이 수행됩니다.

> [!WARNING]
> 리디렉션 규칙을 설정할 때에는 주의하세요. 리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다. 따라서 *무한 리디렉션 루프* 를 실수로 만들기 쉽습니다.

원본 요청: `/redirect-rule/1234/5678`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다. 그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다. 마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다. 따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다. 즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.

대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다. 첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다. 예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다. 규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.

### <a name="url-redirect-to-a-secure-endpoint"></a>보안 엔드포인트에 대한 URL 리디렉션

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다. 상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음* 으로 설정됩니다. 포트가 제공되지 않는 경우 다음과 같이 수행됩니다.

* 미들웨어가 기본적으로 `null`로 설정됩니다.
* 체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.

다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다. 미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정합니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> 추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다. 자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.

예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다. 먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다. 모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다. 자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.

`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL 재작성

URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다. 첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다. 두 번째 매개 변수는 대체 문자열입니다. 세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

원래 요청: `/rewrite-rule/1234/5678`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.

`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다. 따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.

| 경로                               | 일치 |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | 예   |
| `/my-cool-redirect-rule/1234/5678` | 예   |
| `/anotherredirect-rule/1234/5678`  | 예   |

반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다. 다음 표에는 일치에서의 차이가 나와 있습니다.

| 경로                              | 일치 |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 예   |
| `/my-cool-rewrite-rule/1234/5678` | 아니요    |
| `/anotherrewrite-rule/1234/5678`  | 아니요    |

표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다. 여기서 `\d`는 *숫자 하나와 일치* 함을 뜻합니다. 그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치* 함을 나타냅니다. 따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다. 이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다. 재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다. 즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다. 원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.

리소스를 가져오기 위해 서버를 왕복하지 않습니다. 리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다. 클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다. 클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.

> [!NOTE]
> 일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다. 최대한 빠른 응용 프로그램 응답을 위해서는:
>
> * 재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.
> * 일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

<xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다. 규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다. mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.

<xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다. 응답 상태 코드는 *302 - 있음* 입니다.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

원본 요청: `/apache-mod-rules-redirect/1234`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL 재작성 모듈 규칙

IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다. 규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다. Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다. IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다. IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.

<xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다. 응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

원본 요청: `/iis-rules-rewrite/1234`

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

#### <a name="unsupported-features"></a>지원되지 않는 기능

ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.

* 아웃바운드 규칙
* 사용자 지정 서버 변수
* 와일드카드
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>지원되는 서버 변수

미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다. 이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다. 재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>메서드 기반 규칙

메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다. `Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다. 값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.

| 컨텍스트 결과 다시 작성               | 작업                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(기본값) | 규칙 적용을 계속합니다.                                         |
| `RuleResult.EndResponse`             | 규칙 적용을 중지하고 응답을 보냅니다.                       |
| `RuleResult.SkipRemainingRules`      | 규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

예제 응용 프로그램은 *.xml* 로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다. `/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다. 상태 코드는 *301 - 영구적으로 이동됨* 으로 설정됩니다. 브라우저에서 */xmlfiles/file.xml* 에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다. 리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다. 그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

또한 이 방법은 요청을 다시 작성할 수도 있습니다. 샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다. 정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule 기반 규칙

<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다. `IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다. 구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다. `extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png* , *.jpg* , 또는 *.gif* 중 하나이어야 합니다. 만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다. *image.png* 에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다. *image.jpg* 에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다. 상태 코드는 *301 - 영구적으로 이동됨* 으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

원본 요청: `/image.png`

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

원본 요청: `/image.jpg`

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>정규식 예제

| Goal | 정규식 문자열 및<br>일치 예제 | 대체 문자열 및<br>출력 예제 |
| ---- | ------------------------------- | -------------------------------------- |
| 경로를 쿼리 문자열로 재작성 | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| 후행 슬래시 제거 | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 후행 슬래시 적용 | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 특정 요청 재작성 방지 | `^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`<br>예: `/resource.htm`<br>아니요: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| URL 세그먼트 재정렬 | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| URL 세그먼트 대체 | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [.NET에서의 정규식](/dotnet/articles/standard/base-types/regular-expressions)
* [정규식 언어 - 빠른 참조](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Url 재작성 모듈 2.0 사용(IIS용)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL 재작성 모듈 포럼](https://forums.iis.net/1152.aspx)
* [간단한 URL 구조 유지](https://support.google.com/webmasters/answer/76329?hl=en)
* [10가지 URL 재작성 팁과 요령](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [슬래시 여부](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
