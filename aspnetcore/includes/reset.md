<span data-ttu-id="b038a-101">초기화를 사용하면 서버에서 지정된 오류 코드를 사용하여 HTTP/2 요청을 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b038a-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="b038a-102">초기화 요청은 중단된 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="b038a-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="b038a-103">이전 코드 예제의 `Reset`은 `INTERNAL_ERROR` 오류 코드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b038a-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="b038a-104">HTTP/2 오류 코드에 관한 자세한 내용은 [HTTP/2 사양 오류 코드 섹션](https://tools.ietf.org/html/rfc7540#page-50)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b038a-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>