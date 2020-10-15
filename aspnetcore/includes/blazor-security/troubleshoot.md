## <a name="troubleshoot"></a>문제 해결

### <a name="cookies-and-site-data"></a>쿠키 및 사이트 데이터

쿠키 및 사이트 데이터가 앱을 업데이트할 때에도 유지되어 테스트 및 문제 해결에 방해가 될 수 있습니다. 앱 코드를 변경하거나 공급자를 사용하여 사용자 계정을 변경하거나 공급자 앱 구성을 변경하는 경우 다음을 지우세요.

* 사용자 로그인 쿠키
* 앱 쿠키
* 캐시되고 저장된 사이트 데이터

남겨진 쿠키 및 사이트 데이터로 인해 테스트 및 문제 해결이 지장을 받지 않도록 하려면 다음을 수행합니다.

* 브라우저 구성
  * 브라우저에서 브라우저를 닫을 때마다 모든 쿠키 및 사이트 데이터를 삭제하도록 구성할 수 있는지 테스트합니다.
  * 앱, 테스트 사용자 또는 공급자 구성을 변경할 때 수동으로 또는 IDE를 통해 브라우저를 닫습니다.
* 사용자 지정 명령을 사용하여 Visual Studio에서 시크릿 또는 프라이빗 모드로 브라우저를 엽니다.
  * Visual Studio의 **실행** 단추를 통해 **브라우저 선택** 대화 상자를 엽니다.
  * **추가** 단추를 선택합니다.
  * **프로그램** 필드에서 브라우저의 경로를 제공합니다. 다음 실행 파일 경로는 Windows 10에서 일반적인 설치 위치입니다. 브라우저가 다른 위치에 설치되어 있거나 Windows 10을 사용하지 않는 경우 브라우저 실행 파일의 경로를 제공합니다.
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * **인수** 필드에 브라우저에서 시크릿 또는 프라이빗 모드로 열 때 사용하는 명령줄 옵션을 제공합니다. 일부 브라우저에는 앱의 URL이 필요합니다.
    * Microsoft Edge: `-inprivate`을 사용하십시오.
    * Google Chrome: `--incognito --new-window {URL}`를 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).
    * Mozilla Firefox: `-private -url {URL}`을 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).
  * **이름** 필드에 이름을 입력합니다. 예: `Firefox Auth Testing`.
  * **확인** 단추를 선택합니다.
  * 앱 테스트를 반복할 때마다 브라우저 프로필을 선택할 필요가 없도록 하려면 **기본값으로 설정** 단추를 사용하여 프로필을 기본값으로 설정합니다.
  * 앱, 테스트 사용자 또는 공급자 구성을 변경할 때 IDE를 통해 브라우저를 닫습니다.

### <a name="run-the-server-app"></a>서버 앱 실행

호스트된 Blazor 앱을 테스트하고 문제를 해결할 때 **`Server`** 프로젝트에서 앱을 실행하고 있는지 확인합니다. 예를 들어 Visual Studio에서 다음 방법 중 하나를 사용하여 앱을 시작하기 전에 **솔루션 탐색기**에서 서버 프로젝트가 강조 표시되어 있는지 확인합니다.

* **실행** 단추를 선택합니다.
* 메뉴에서 **디버그** > **디버깅 시작**을 사용합니다.
* <kbd>F5</kbd>키를 누릅니다.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>JWT(JSON Web Token)의 콘텐츠 검사

JWT(JSON Web Token)를 디코딩하려면 Microsoft의 [jwt.ms](https://jwt.ms/) 도구를 사용합니다. UI의 값은 브라우저에 남겨지지 않습니다.
