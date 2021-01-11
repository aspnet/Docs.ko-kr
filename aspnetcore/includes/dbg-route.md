## <a name="debug-diagnostics"></a>디버그 진단

자세한 라우팅 진단 출력을 위해 `Logging:LogLevel:Microsoft`를 `Debug`로 설정합니다. 개발 환경에서 *appsettings.Development.json* 의 로그 수준을 설정합니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
