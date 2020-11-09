---
title: Visual Studio에서 ASP.NET Core와 함께 LibMan 사용
author: scottaddie
description: ASP.NET Core 프로젝트에서 Visual Studio와 함께 LibMan을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: client-side/libman/libman-vs
ms.openlocfilehash: 1c97f5d7fbf64c5043e6d2277091b9a477833bf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054712"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="a257c-103">Visual Studio에서 ASP.NET Core와 함께 LibMan 사용</span><span class="sxs-lookup"><span data-stu-id="a257c-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="a257c-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a257c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a257c-105">Visual Studio에는 ASP.NET Core 프로젝트의 [LibMan](xref:client-side/libman/index)에 대한 다음과 같은 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="a257c-106">빌드에서 LibMan 복원 작업 구성 및 실행 지원</span><span class="sxs-lookup"><span data-stu-id="a257c-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="a257c-107">LibMan 복원 및 정리 작업을 트리거하기 위한 메뉴 항목</span><span class="sxs-lookup"><span data-stu-id="a257c-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="a257c-108">라이브러리를 찾고 프로젝트에 파일을 추가하기 위한 검색 대화 상자</span><span class="sxs-lookup"><span data-stu-id="a257c-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="a257c-109">*libman.json* (LibMan 매니페스트 파일) 편집 지원</span><span class="sxs-lookup"><span data-stu-id="a257c-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="a257c-110">[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a257c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a257c-111">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="a257c-111">Prerequisites</span></span>

* <span data-ttu-id="a257c-112">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="a257c-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="a257c-113">라이브러리 파일 추가</span><span class="sxs-lookup"><span data-stu-id="a257c-113">Add library files</span></span>

<span data-ttu-id="a257c-114">다음 두 가지 방법으로 ASP.NET Core 프로젝트에 라이브러리 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="a257c-115">클라이언트 쪽 라이브러리 추가 대화 상자 사용</span><span class="sxs-lookup"><span data-stu-id="a257c-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="a257c-116">수동으로 LibMan 매니페스트 파일 항목 구성</span><span class="sxs-lookup"><span data-stu-id="a257c-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="a257c-117">클라이언트 쪽 라이브러리 추가 대화 상자 사용</span><span class="sxs-lookup"><span data-stu-id="a257c-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="a257c-118">클라이언트 쪽 라이브러리를 설치하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="a257c-119">**솔루션 탐색기** 에서 파일을 추가해야 하는 프로젝트 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-119">In **Solution Explorer** , right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="a257c-120">**추가** > **클라이언트 쪽 라이브러리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="a257c-121">**클라이언트 쪽 라이브러리 추가** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![클라이언트 쪽 라이브러리 추가 대화 상자](_static/add-library-dialog.png)

* <span data-ttu-id="a257c-123">**공급자** 드롭다운에서 라이브러리 공급자를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="a257c-124">기본 공급자는 CDNJS입니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="a257c-125">**라이브러리** 텍스트 상자에 가져올 라이브러리 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="a257c-126">IntelliSense에서 입력된 텍스트로 시작하는 라이브러리 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="a257c-127">IntelliSense 목록에서 라이브러리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="a257c-128">라이브러리 이름에는 `@` 기호와 선택한 공급자에게 알려진 안정적인 최신 버전이 접미사로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="a257c-129">포함할 파일을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-129">Decide which files to include:</span></span>
  * <span data-ttu-id="a257c-130">모든 라이브러리 파일을 포함하려면 **모든 라이브러리 파일 포함** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="a257c-131">라이브러리 파일의 하위 집합을 포함하려면 **특정 파일 선택** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="a257c-132">라디오 단추를 선택하면 파일 선택기 트리를 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="a257c-133">다운로드할 파일 이름 왼쪽에 있는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="a257c-134">**대상 위치** 텍스트 상자에 파일을 저장할 프로젝트 폴더를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="a257c-135">각 라이브러리를 개별 폴더에 저장하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="a257c-136">제안된 **대상 위치** 폴더는 대화 상자가 시작된 위치를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="a257c-137">프로젝트 루트에서 시작한 경우</span><span class="sxs-lookup"><span data-stu-id="a257c-137">If launched from the project root:</span></span>
    * <span data-ttu-id="a257c-138">*wwwroot* 가 있으면 *wwwroot/lib* 가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="a257c-139">*wwwroot* 가 없으면 *lib* 가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="a257c-140">프로젝트 폴더에서 시작한 경우 해당 폴더 이름이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="a257c-141">폴더 제안에 라이브러리 이름이 접미사로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="a257c-142">다음 표에는 Razor Pages 프로젝트에 jQuery를 설치하는 경우의 폴더 제안이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="a257c-143">시작 위치</span><span class="sxs-lookup"><span data-stu-id="a257c-143">Launch location</span></span>                           |<span data-ttu-id="a257c-144">제안된 폴더</span><span class="sxs-lookup"><span data-stu-id="a257c-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="a257c-145">프로젝트 루트( *wwwroot* 가 있는 경우)</span><span class="sxs-lookup"><span data-stu-id="a257c-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="a257c-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a257c-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="a257c-147">프로젝트 루트( *wwwroot* 가 없는 경우)</span><span class="sxs-lookup"><span data-stu-id="a257c-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="a257c-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a257c-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="a257c-149">프로젝트의 *Pages* 폴더</span><span class="sxs-lookup"><span data-stu-id="a257c-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="a257c-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a257c-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="a257c-151">**설치** 단추를 클릭하여 *libman.json* 의 구성에 따라 파일을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="a257c-152">**출력** 창의 **라이브러리 관리자** 피드에서 설치 정보를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="a257c-153">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a257c-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="a257c-154">수동으로 LibMan 매니페스트 파일 항목 구성</span><span class="sxs-lookup"><span data-stu-id="a257c-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="a257c-155">Visual Studio의 모든 LibMan 작업은 프로젝트 루트의 LibMan 매니페스트( *libman.json* ) 콘텐츠를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest ( *libman.json* ).</span></span> <span data-ttu-id="a257c-156">*libman.json* 을 수동으로 편집하여 프로젝트의 라이브러리 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="a257c-157">*libman.json* 을 저장하면 Visual Studio에서 모든 라이브러리 파일을 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="a257c-158">편집을 위해 *libman.json* 을 열려는 경우 다음과 같은 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="a257c-159">**솔루션 탐색기** 에서 *libman.json* 파일을 두 번 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="a257c-160">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **클라이언트 쪽 라이브러리 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="a257c-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="a257c-161">**&#8224;**</span></span>
* <span data-ttu-id="a257c-162">Visual Studio **프로젝트** 메뉴에서 **클라이언트 쪽 라이브러리 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="a257c-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="a257c-163">**&#8224;**</span></span>

<span data-ttu-id="a257c-164">**&#8224;** 프로젝트 루트에 *libman.json* 파일이 아직 없는 경우, 기본 항목 템플릿 콘텐츠를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="a257c-165">Visual Studio는 색 지정, 서식 지정, IntelliSense, 스키마 유효성 검사 등의 다양한 JSON 편집 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="a257c-166">LibMan 매니페스트의 JSON 스키마는 [https://json.schemastore.org/libman](https://json.schemastore.org/libman)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="a257c-167">다음 매니페스트 파일을 사용할 경우 LibMan은 `libraries` 속성에서 정의된 구성에 따라 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="a257c-168">`libraries` 내에서 정의된 개체 리터럴에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="a257c-169">[jQuery](https://jquery.com/) 버전 3.3.1의 하위 집합이 CDNJS 공급자에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="a257c-170">하위 집합은 `files` 속성( *jquery.min.js* , *jquery.js* , *jquery.min.map* )에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-170">The subset is defined in the `files` property&mdash;*jquery.min.js* , *jquery.js* , and *jquery.min.map*.</span></span> <span data-ttu-id="a257c-171">파일은 프로젝트의 *wwwroot/lib/jquery* 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="a257c-172">[부트스트랩](https://getbootstrap.com/) 버전 4.1.3 전체가 검색되어 *wwwroot/lib/bootstrap* 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="a257c-173">개체 리터럴의 `provider` 속성이 `defaultProvider` 속성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="a257c-174">LibMan은 unpkg 공급자에서 부트스트랩 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="a257c-175">조직 내의 규제 기관이 [Lodash](https://lodash.com/)의 하위 집합을 승인했습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="a257c-176">*lodash.js* 및 *lodash.min.js* 파일은 로컬 파일 시스템의 *C:\\temp\\lodash\\* 에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="a257c-177">프로젝트의 *wwwroot/lib/lodash* 폴더에 파일이 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="a257c-178">LibMan은 각 공급자의 라이브러리마다 하나의 버전만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="a257c-179">지정된 공급자와 관련해서 동일한 라이브러리 이름을 가진 라이브러리가 두 개 포함되어 있으면 *libman.json* 파일이 스키마 유효성 검사에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="a257c-180">라이브러리 파일 복원</span><span class="sxs-lookup"><span data-stu-id="a257c-180">Restore library files</span></span>

<span data-ttu-id="a257c-181">Visual Studio 내에서 라이브러리 파일을 복원하려면 프로젝트 루트에 유효한 *libman.json* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="a257c-182">복원된 파일은 프로젝트에서 각 라이브러리의 지정된 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="a257c-183">다음 두 가지 방법으로 ASP.NET Core 프로젝트에서 라이브러리 파일을 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="a257c-184">빌드 중에 파일 복원</span><span class="sxs-lookup"><span data-stu-id="a257c-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="a257c-185">수동으로 파일 복원</span><span class="sxs-lookup"><span data-stu-id="a257c-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="a257c-186">빌드 중에 파일 복원</span><span class="sxs-lookup"><span data-stu-id="a257c-186">Restore files during build</span></span>

<span data-ttu-id="a257c-187">LibMan은 빌드 프로세스의 일부로, 정의된 라이브러리 파일을 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="a257c-188">기본적으로 ‘빌드 시 복원’ 동작은 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="a257c-189">빌드 시 복원 동작을 사용하도록 설정하고 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="a257c-190">**솔루션 탐색기** 에서 *libman.json* 을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **빌드에서 클라이언트 쪽 라이브러리 복원 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="a257c-191">NuGet 패키지를 설치하라는 메시지가 표시되 면 **예** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="a257c-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 패키지가 프로젝트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="a257c-193">프로젝트를 빌드하여 LibMan 파일이 복원되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="a257c-194">`Microsoft.Web.LibraryManager.Build` 패키지는 프로젝트의 빌드 작업 중에 LibMan을 실행하는 MSBuild 대상을 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="a257c-195">**출력** 창의 **빌드** 피드에서 LibMan 활동 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="a257c-196">빌드 시 복원 동작을 사용하도록 설정한 경우, *libman.json* 상황에 맞는 메뉴에 **빌드에서 클라이언트 쪽 라이브러리 복원 사용 안 함** 옵션이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="a257c-197">이 옵션을 선택하면 프로젝트 파일에서 `Microsoft.Web.LibraryManager.Build` 패키지 참조가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="a257c-198">또한 각 빌드에서 클라이언트 쪽 라이브러리가 이제 복원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="a257c-199">빌드 시 복원 설정에 관계없이 언제든지 *libman.json* 상황에 맞는 메뉴에서 수동으로 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="a257c-200">자세한 내용은 [수동으로 파일 복원](#restore-files-manually)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a257c-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="a257c-201">수동으로 파일 복원</span><span class="sxs-lookup"><span data-stu-id="a257c-201">Restore files manually</span></span>

<span data-ttu-id="a257c-202">라이브러리 파일을 수동으로 복원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-202">To manually restore library files:</span></span>

* <span data-ttu-id="a257c-203">솔루션에 있는 모든 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="a257c-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="a257c-204">**솔루션 탐색기** 에서 솔루션 이름을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="a257c-205">**클라이언트 쪽 라이브러리 복원** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="a257c-206">특정 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="a257c-206">For a specific project:</span></span>
  * <span data-ttu-id="a257c-207">**솔루션 탐색기** 에서 *libman.json* 파일을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="a257c-208">**클라이언트 쪽 라이브러리 복원** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="a257c-209">복원 작업이 실행되는 동안</span><span class="sxs-lookup"><span data-stu-id="a257c-209">While the restore operation is running:</span></span>

* <span data-ttu-id="a257c-210">Visual Studio 상태 표시줄의 TSC(작업 상태 센터) 아이콘에 애니메이션이 적용되고 ‘복원 작업 시작됨’이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="a257c-211">아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="a257c-212">상태 표시줄과 **출력** 창의 **라이브러리 관리자** 피드에 메시지가 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="a257c-213">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a257c-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="a257c-214">라이브러리 파일 삭제</span><span class="sxs-lookup"><span data-stu-id="a257c-214">Delete library files</span></span>

<span data-ttu-id="a257c-215">Visual Studio에서 이전에 복원된 라이브러리 파일을 삭제하는 ‘정리’ 작업을 수행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="a257c-216">**솔루션 탐색기** 에서 *libman.json* 파일을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="a257c-217">**클라이언트 쪽 라이브러리 정리** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="a257c-218">비라이브러리 파일이 실수로 제거되는 것을 방지하기 위해 정리 작업은 전체 디렉터리를 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="a257c-219">이전 복원에 포함된 파일만 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="a257c-220">정리 작업이 실행되는 동안</span><span class="sxs-lookup"><span data-stu-id="a257c-220">While the clean operation is running:</span></span>

* <span data-ttu-id="a257c-221">Visual Studio 상태 표시줄의 TSC 아이콘에 애니메이션이 적용되고 ‘클라이언트 라이브러리 작업 시작됨’이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="a257c-222">아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="a257c-223">상태 표시줄과 **출력** 창의 **라이브러리 관리자** 피드에 메시지가 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="a257c-224">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a257c-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="a257c-225">정리 작업은 프로젝트의 파일만 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="a257c-226">라이브러리 파일은 이후 복원 작업 시 빠르게 검색하기 위해 캐시에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="a257c-227">로컬 컴퓨터의 캐시에 저장된 라이브러리 파일을 관리하려면 [LibMan CLI](xref:client-side/libman/libman-cli)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="a257c-228">라이브러리 파일 제거</span><span class="sxs-lookup"><span data-stu-id="a257c-228">Uninstall library files</span></span>

<span data-ttu-id="a257c-229">라이브러리 파일을 제거하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-229">To uninstall library files:</span></span>

* <span data-ttu-id="a257c-230">*libman.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-230">Open *libman.json*.</span></span>
* <span data-ttu-id="a257c-231">해당 `libraries` 개체 리터럴 안에 캐럿을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="a257c-232">왼쪽 여백에 표시되는 전구 아이콘을 클릭하고 **제거\<library_name>@\<library_version>** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>** :</span></span>

  ![라이브러리 제거 상황에 맞는 메뉴 옵션](_static/uninstall-menu-option.png)

<span data-ttu-id="a257c-234">또는 수동으로 LibMan 매니페스트( *libman.json* )를 편집하고 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-234">Alternatively, you can manually edit and save the LibMan manifest ( *libman.json* ).</span></span> <span data-ttu-id="a257c-235">파일을 저장하면 [복원 작업](#restore-library-files)이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="a257c-236">*libman.json* 에서 더는 정의되지 않은 라이브러리 파일은 프로젝트에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="a257c-237">라이브러리 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="a257c-237">Update library version</span></span>

<span data-ttu-id="a257c-238">업데이트된 라이브러리 버전을 확인하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-238">To check for an updated library version:</span></span>

* <span data-ttu-id="a257c-239">*libman.json* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-239">Open *libman.json*.</span></span>
* <span data-ttu-id="a257c-240">해당 `libraries` 개체 리터럴 안에 캐럿을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="a257c-241">왼쪽 여백에 표시되는 전구 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="a257c-242">**업데이트 확인** 을 마우스로 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="a257c-243">LibMan은 설치된 버전보다 최신 라이브러리 버전이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="a257c-244">다음과 같은 결과가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="a257c-245">최신 버전이 이미 설치되어 있는 경우 **업데이트를 찾을 수 없음** 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="a257c-246">아직 설치되지 않은 경우 안정적인 최신 버전이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-246">The latest stable version is displayed if not already installed.</span></span>

  ![업데이트 확인 상황에 맞는 메뉴 옵션](_static/update-menu-option.png)

* <span data-ttu-id="a257c-248">설치된 버전보다 더 최신 시험판을 사용할 수 있는 경우 시험판이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="a257c-249">이전 라이브러리 버전으로 다운그레이드하려면 *libman.json* 파일을 수동으로 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="a257c-250">파일을 저장하면 LibMan [복원 작업](#restore-library-files)이 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="a257c-251">이전 버전에서 중복 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="a257c-252">새 버전에 있는 새 파일과 업데이트된 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a257c-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a257c-253">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a257c-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="a257c-254">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="a257c-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
