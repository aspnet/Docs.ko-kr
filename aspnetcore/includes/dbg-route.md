## <a name="debug-diagnostics"></a><span data-ttu-id="99300-101">디버그 진단</span><span class="sxs-lookup"><span data-stu-id="99300-101">Debug diagnostics</span></span>

<span data-ttu-id="99300-102">자세한 라우팅 진단 출력을 위해 `Logging:LogLevel:Microsoft`를 `Debug`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="99300-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="99300-103">개발 환경에서 *appsettings.Development.json* 의 로그 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="99300-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
