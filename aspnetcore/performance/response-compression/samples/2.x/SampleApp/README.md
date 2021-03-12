# <a name="response-compression-sample-application-aspnet-core-2x"></a>응답 압축 샘플 응용 프로그램 (ASP.NET Core 2.x)

이 샘플에서는 ASP.NET Core 2.x 응답 압축 미들웨어를 사용 하 여 HTTP 응답을 압축 하는 방법을 보여 줍니다. 이 샘플에서는 텍스트 및 이미지 응답을 위한 Gzip, Brotli 및 사용자 지정 압축 공급자를 보여주고 압축을 위해 MIME 형식을 추가 하는 방법을 보여 줍니다. 1.x 샘플 ASP.NET Core의 경우 [응답 압축 샘플 응용 프로그램 (ASP.NET Core 1.x)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples/1.x)을 참조 하세요.

## <a name="examples-in-this-sample"></a>이 샘플의 예

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -2044 바이트의 Lorem Ipsum text file response ~ 979 바이트를 압축 합니다.
    * **/testfile1kb.txt** -1033 바이트에서 ~ 36 바이트로 압축 되는 텍스트 파일 응답입니다.
    * **/trickle** -1 초 간격으로 단일 문자로 발급 된 응답입니다.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -2044 바이트의 Lorem Ipsum text file response ~ 927 바이트를 압축 합니다.
    * **/testfile1kb.txt** -1033 바이트에서 ~ 47 바이트로 압축 되는 텍스트 파일 응답입니다.
    * **/trickle** -1 초 간격으로 단일 문자로 발급 된 응답입니다.
  * `image/svg+xml`
    * **/banner.svg** -9707 바이트에서 ~ 4459 바이트를 압축 하는 Svg (확장 가능한 벡터 그래픽) 이미지 응답입니다.
* `CustomCompressionProvider`<br>미들웨어에서 사용할 사용자 지정 압축 공급자를 구현 하는 방법을 보여 줍니다.

요청에 헤더가 포함 되 `Accept-Encoding` 고 응답 압축이 성공적으로 완료 되 면 미들웨어는 자동으로 `Vary: Accept-Encoding` 응답에 헤더를 추가 합니다. `Vary`헤더는의 대체 값에 따라 응답의 여러 복사본을 유지 관리 하도록 캐시에 지시 하므로 압축 된 `Accept-Encoding` (Gzip 또는 Brotli) 및 압축 되지 않은 버전은 모두 압축 된 응답 또는 압축 되지 않은 응답을 수락할 수 있는 시스템에 대 한 캐시에 저장 됩니다.

## <a name="use-the-sample"></a>샘플 사용

1. 헤더 없이 응용 프로그램에 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 을 사용 하 여 요청 하 `Accept-Encoding` 고 응답 페이로드, 응답 크기 및 응답 헤더를 확인 합니다.
1. `Accept-Encoding: br`또는 헤더를 추가 `Accept-Encoding: gzip` 하 고 압축 된 응답 크기 및 응답 헤더를 적어 둡니다. 응답 크기가 감소 하 고 `Content-Encoding` 응답 헤더가 Gzip 또는 Brotli의 압축이 발생 했음을 나타내는 미들웨어에 의해 포함 됩니다. 응답 본문에서 Lorem Ipsum 또는 **testfile1kb.txt** 응답을 보면 텍스트가 압축 되 고 읽을 수 없다는 것을 알 수 있습니다.
1. 헤더를 추가 `Accept-Encoding: mycustomcompression` 하 고 응답 헤더를 확인 합니다. 는 `CustomCompressionProvider` 실제로 응답을 압축 하지 않는 빈 구현 이지만 메서드에 대 한 사용자 지정 압축 스트림 래퍼를 만들 수 있습니다 `CreateStream()` .
