# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly 샘플 앱

이 샘플은 Blazor 설명서에 설명된 Blazor 시나리오를 사용하는 방법을 보여 줍니다.

## <a name="call-web-api-example"></a>웹 API 호출 예제

웹 API 예제를 사용하려면 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Core를 사용하여 웹 API 만들기</a> 항목의 샘플 앱을 기반으로 한 웹 API가 실행되고 있어야 합니다. 웹 API는 기본적으로 Blazor 샘플 앱과 동일한 HTTPS 포트(5001)를 사용합니다. 동일한 머신에서 두 앱을 동시에 사용하려면 웹 API의 포트를 변경합니다(예: 포트 10000 사용). 샘플 앱은 `https://localhost:10000/api/TodoItems`에서 웹 API에 요청합니다. 다른 웹 API 주소를 사용하는 경우 Razor 구성 요소의 `@code` 블록에서 `ServiceEndpoint` 상수 값을 업데이트합니다.</p>

샘플 앱은 `http://localhost:5000` 또는 `https://localhost:5001`에서 웹 API에 대해 <a href="https://docs.microsoft.com/aspnet/core/security/cors">CORS</a>(원본 간 리소스 공유) 요청을 합니다. 자격 증명(권한 부여 쿠키/헤더)이 허용됩니다. 웹 API의 `Startup.Configure` 메서드에 다음 CORS 미들웨어 구성을 추가합니다.</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor 앱에 필요한 경우 `WithOrigins`의 도메인과 포트를 조정합니다.

웹 API는 CORS에서 클라이언트 코드의 권한 부여 쿠키/헤더와 요청을 허용하도록 구성되어 있지만, 자습서에서 만든 웹 API는 실제로 요청 권한을 부여하지 않습니다. 구현 지침은 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 보안 및 ID 문서</a>를 참조하세요.
