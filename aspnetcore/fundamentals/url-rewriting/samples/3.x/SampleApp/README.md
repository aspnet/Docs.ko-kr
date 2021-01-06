# <a name="aspnet-core-url-rewriting-sample"></a>ASP.NET Core URL 다시 작성 샘플

이 샘플은 ASP.NET Core URL 다시 작성 미들웨어의 사용법을 보여 줍니다. 앱은 URL 리디렉션 및 URL 다시 작성 옵션을 보여 줍니다.

샘플을 실행하면, 규칙 중 하나가 요청 URL에 적용될 때 파일 외 응답이 다시 작성되거나 리디렉션된 URL로 돌아갑니다. XML 및 텍스트 파일 예제에서, 정적 파일 미들웨어는 미들웨어에서 요청 URL을 다시 작성한 후 파일을 제공합니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - 성공 상태 코드: 302(Found)
  - 예(리디렉션): **/redirect-rule/{capture_group}** 을 **/redirected/{capture_group}** 으로
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - 성공 상태 코드: 200(OK)
  - 예(다시 작성): **/rewrite-rule/{capture_group_1}/{capture_group_2}** 를 **/rewrtten?var1={capture_group_1}&var2={capture_group_2}** 로
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - 성공 상태 코드: 302(Found)
  - 예(리디렉션): **/apache-mod-rules-redirect/{capture_group}** 을 **/redirected?id={capture_group}** 으로
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - 성공 상태 코드: 200(OK)
  - 예(다시 작성): **/iis-rules-rewrite/{capture_group}** 을 **/rewritten?id={capture_group}** 으로
* `Add(RedirectXmlFileRequests)`
  - 성공 상태 코드: 301(영구적 이동)
  - 예(리디렉션): **/file.xml** 을 **/xmlfiles/file.xml** 로
* `Add(RewriteTextFileRequests)`
  - 성공 상태 코드: 200(OK)
  - 예(다시 작성): **/some_file.txt** 를 **/file.txt** 로
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - 성공 상태 코드: 301(영구적 이동)
  - 예(리디렉션): **/image.png** 를 **/png-images/image.png** 로
  - 예(리디렉션): **/image.jpg** 를 **/jpg-images/image.jpg** 로

## <a name="use-a-physicalfileprovider"></a>PhysicalFileProvider 사용

또한 `PhysicalFileProvider`를 만들어 `IFileProvider`를 얻은 후 `AddApacheModRewrite()` 및 `AddIISUrlRewrite()` 메서드로 전달할 수 있습니다.

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>보안 리디렉션 확장

이 샘플은 URL(`https://localhost:5001`, `https://localhost`)을 사용하는 데 필요한 앱에 대한 `WebHostBuilder`구성 및 보안 리디렉션 메서드를 살펴보는 데 도움이 되는 테스트 인증서(*testCert.pfx*)를 포함하고 있습니다. 서버가 이미 포트 443을 할당하거나 사용중인 경우, `https://localhost`예제는 작동하지 않습니다. &mdash;*Program.cs* 파일의 `CreateWebHostBuilder`메서드에서 포트 443용 `ListenOptions`를 제거하거나 서버에서 포트 443을 바인딩 해제하여 Kestrel이 포트를 사용할 수 있도록 합니다.

| 메서드                           | 상태 코드 |    포트    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | NULL(465) |
| `.AddRedirectToHttps()`          |     302     | NULL(465) |
| `.AddRedirectToHttps(301)`       |     301     | NULL(465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
