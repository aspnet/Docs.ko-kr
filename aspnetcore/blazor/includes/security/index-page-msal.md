인덱스 페이지(`wwwroot/index.html`)는 JavaScript로 `AuthenticationService`를 정의하는 스크립트를 포함합니다. `AuthenticationService`는 OIDC 프로토콜의 하위 수준 세부 정보를 처리합니다. 앱은 내부적으로 스크립트에 정의된 메서드를 호출하여 인증 작업을 수행합니다.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
