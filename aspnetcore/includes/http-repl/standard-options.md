* `-F|--no-formatting`

  HTTP 응답 형식을 표시하지 않는 플래그입니다.

* `-h|--header`

  HTTP 요청 헤더를 설정합니다. 다음 두 가지 값 형식이 지원됩니다.

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  HTTP 응답 본문을 작성할 파일을 지정합니다. 예들 들어 `--response:body "C:\response.json"`입니다. 파일이 없는 경우 새로 만들어집니다.

* `--response:headers`

  HTTP 응답 헤더를 작성할 파일을 지정합니다. 예들 들어 `--response:headers "C:\response.txt"`입니다. 파일이 없는 경우 새로 만들어집니다.

* `-s|--streaming`

  HTTP 응답의 스트리밍을 사용하도록 설정하는 플래그입니다.
