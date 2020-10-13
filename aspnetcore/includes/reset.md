초기화를 사용하면 서버에서 지정된 오류 코드를 사용하여 HTTP/2 요청을 초기화할 수 있습니다. 초기화 요청은 중단된 것으로 간주합니다.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

이전 코드 예제의 `Reset`은 `INTERNAL_ERROR` 오류 코드를 지정합니다. HTTP/2 오류 코드에 관한 자세한 내용은 [HTTP/2 사양 오류 코드 섹션](https://tools.ietf.org/html/rfc7540#page-50)을 참조하세요.