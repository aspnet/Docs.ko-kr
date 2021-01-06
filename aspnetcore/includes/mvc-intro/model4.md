<span data-ttu-id="13174-101">다음 표에서는 ASP.NET Core 코드 생성기 매개 변수를 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="13174-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="13174-102">매개 변수</span><span class="sxs-lookup"><span data-stu-id="13174-102">Parameter</span></span>               | <span data-ttu-id="13174-103">설명</span><span class="sxs-lookup"><span data-stu-id="13174-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="13174-104">-m</span><span class="sxs-lookup"><span data-stu-id="13174-104">-m</span></span>  | <span data-ttu-id="13174-105">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="13174-105">The name of the model.</span></span> |
| <span data-ttu-id="13174-106">-dc</span><span class="sxs-lookup"><span data-stu-id="13174-106">-dc</span></span>  | <span data-ttu-id="13174-107">데이터 컨텍스트입니다.</span><span class="sxs-lookup"><span data-stu-id="13174-107">The data context.</span></span> |
| <span data-ttu-id="13174-108">-udl</span><span class="sxs-lookup"><span data-stu-id="13174-108">-udl</span></span> | <span data-ttu-id="13174-109">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="13174-109">Use the default layout.</span></span> |
| <span data-ttu-id="13174-110">--relativeFolderPath</span><span class="sxs-lookup"><span data-stu-id="13174-110">--relativeFolderPath</span></span> | <span data-ttu-id="13174-111">파일을 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="13174-111">The relative output folder path to create the files.</span></span> |
| <span data-ttu-id="13174-112">--useDefaultLayout</span><span class="sxs-lookup"><span data-stu-id="13174-112">--useDefaultLayout</span></span> | <span data-ttu-id="13174-113">보기에는 기본 레이아웃을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13174-113">The default layout should be used for the views.</span></span> |
| <span data-ttu-id="13174-114">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="13174-114">--referenceScriptLibraries</span></span> | <span data-ttu-id="13174-115">Edit 및 Create 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="13174-115">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="13174-116">`h` 스위치를 사용하여 `aspnet-codegenerator controller` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13174-116">Use the `h` switch to get help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="13174-117">자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="13174-117">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span></span>
