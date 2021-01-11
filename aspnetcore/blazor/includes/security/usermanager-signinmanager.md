## <a name="usermanager-and-signinmanager"></a>UserManager 및 SignInManager

서버 앱에 다음이 필요한 경우 사용자 ID 클레임 유형을 설정합니다.

* API 엔드포인트의 <xref:Microsoft.AspNetCore.Identity.UserManager%601> 또는 <xref:Microsoft.AspNetCore.Identity.SignInManager%601>.
* 사용자 이름, 이메일 주소 또는 잠금 종료 시간과 같은 <xref:Microsoft.AspNetCore.Identity.IdentityUser> 세부 정보.

`Startup.ConfigureServices`의 경우

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

다음 `WeatherForecastController`는 `Get` 메서드가 호출될 때 <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName>을 로깅합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            this.userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
