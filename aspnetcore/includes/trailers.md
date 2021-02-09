HTTP 트레일러는 응답 본문이 전송된 후에 전송된다는 점을 제외하고 HTTP 헤더와 유사합니다. IIS 및 HTTP.sys의 경우 HTTP/2 응답 트레일러만 지원됩니다.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

앞의 예제 코드에서:

* `SupportsTrailers`는 응답을 위해 트레일러가 지원되는지 확인합니다.
* `DeclareTrailer`는 `Trailer` 응답 헤더에 지정된 트레일러 이름을 추가합니다. 응답 트레일러 선언은 선택 사항이지만 권장됩니다. `DeclareTrailer`가 호출되는 경우 해당 트레일러는 응답 헤더가 전송되기 전에 있어야 합니다.
* `AppendTrailer`는 트레일러를 추가합니다.
