Blazor 서버 앱은 서버 메모리에 존재합니다. 즉, 동일한 프로세스 내에서 호스트되는 여러 앱이 있습니다. 각 앱 세션에서 Blazor는 자체 DI 컨테이너 범위를 포함하는 회로를 시작합니다. 즉, 범위가 지정된 서비스는 Blazor 세션별로 고유합니다.

> [!WARNING]
> 매우 주의하지 않는 한 싱글톤 서비스를 사용하는 동일한 서버 공유 상태에서는 앱을 사용하지 않는 것이 좋습니다. 회로 간 사용자 상태 누수와 같은 보안 취약성이 도입될 수 있기 때문입니다.

특별히 해당 용도로 설계된 경우 Blazor 앱에서 상태 저장 싱글톤 서비스를 사용할 수 있습니다. 예를 들어 사용자가 사용되는 캐시 키를 제어할 수 없는 경우 지정된 항목에 액세스하는 데 키가 필요하므로 메모리 캐시를 싱글톤으로 사용할 수 있습니다.

**또한 보안상의 이유로 다시 Blazor 앱 내에서 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>를 사용하면 안 됩니다.** Blazor 앱은 ASP.NET Core 파이프라인의 컨텍스트 외부에서 실행됩니다. <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 내에서의 <xref:Microsoft.AspNetCore.Http.HttpContext> 사용은 보장되지 않으며 Blazor 앱을 시작하는 컨텍스트 유지도 보장되지 않습니다.

Blazor 앱에 요청 상태를 전달하려면 앱을 처음 렌더링할 때 루트 구성 요소에 대한 매개 변수를 사용하는 것이 좋습니다.

* Blazor 앱에 전달하려는 모든 데이터를 사용하여 클래스를 정의합니다.
* 해당 시점에 사용할 수 있는 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용하여 Razor 페이지에서 해당 데이터를 채웁니다.
* Blazor 앱에 데이터를 루트 구성 요소(앱)에 대한 매개 변수로 전달합니다.
* 앱에 전달되는 데이터를 유지할 루트 구성 요소의 매개 변수를 정의합니다.
* 앱 내에서 사용자 관련 데이터를 사용하거나, 앱 전체에서 사용할 수 있도록 해당 데이터를 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 내의 범위가 지정된 서비스에 복사합니다.

자세한 내용과 예제 코드는 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>를 참조하십시오.
