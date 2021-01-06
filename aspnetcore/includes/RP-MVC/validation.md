<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>영화 모델에 유효성 검사 규칙 추가

DataAnnotations 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 모음을 제공합니다. 또한 DataAnnotations는 서식 지정을 돕고 아무런 유효성 검사도 제공하지 않는 `DataType` 같은 서식 지정 특성도 포함하고 있습니다.

기본 제공되는 `Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 수정합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

유효성 검사 특성은 해당 특성이 적용되는 모델 속성에 시행하려는 동작을 지정합니다

* `Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 함을 지정합니다. 그러나 사용자가 이 유효성 검사를 만족시키기 위해 공백을 입력하는 것까지 막을 수는 없습니다.
* `RegularExpression` 특성은 입력할 수 있는 문자를 제한하는 데 사용됩니다. 이전 코드에서 “Genre”는 다음 조건을 충족해야 합니다.

  * 문자만 사용해야 합니다.
  * 첫 번째 문자는 대문자여야 합니다 공백, 숫자 및 특수 문자는 허용되지 않습니다.

* `RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.

  * 첫 번째 문자는 대문자여야 합니다.
  * 이어지는 자리에서는 특수 문자와 숫자가 허용됩니다. “PG-13”은 등급에서는 유효하지만 “Genre”에서는 허용되지 않습니다.

* `Range` 특성은 지정한 범위 내로 값을 제한합니다.
* `StringLength` 특성을 사용하면 문자열 속성의 최대 길이와, 그리고 필요에 따라 최소 길이를 설정할 수 있습니다.
* 값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필수적이며 `[Required]` 특성이 필요하지 않습니다.

ASP.NET Core가 자동으로 적용하는 유효성 검사 규칙을 사용하면 앱을 더욱 강력하게 만들 수 있습니다. 또한 무언가의 유효성 검사를 잊거나, 실수로 데이터베이스에 불량 데이터가 들어가지 않도록 할 수 있습니다.
