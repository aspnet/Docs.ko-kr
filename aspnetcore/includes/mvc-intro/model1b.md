<span data-ttu-id="c7bea-101">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="c7bea-102">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="c7bea-103">기본 키를 위해 데이터베이스에서 필요한 `Id` 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="c7bea-104">`[DataType(DataType.Date)]`:  [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="c7bea-105">이 특성 사용:</span><span class="sxs-lookup"><span data-stu-id="c7bea-105">With this attribute:</span></span>

  * <span data-ttu-id="c7bea-106">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c7bea-107">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c7bea-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="c7bea-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>