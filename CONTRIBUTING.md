# <a name="contribute-to-the-aspnet-core-documentation"></a><span data-ttu-id="8fe27-101">ASP.NET Core 설명서에 참가</span><span class="sxs-lookup"><span data-stu-id="8fe27-101">Contribute to the ASP.NET Core documentation</span></span>

<span data-ttu-id="8fe27-102">이 문서에서는 [ASP.NET 문서 사이트](https://docs.microsoft.com/aspnet/)에서 호스팅되는 문서 및 코드 샘플에 참여하는 프로세스를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-102">This document covers the process for contributing to the articles and code samples that are hosted on the [ASP.NET documentation site](https://docs.microsoft.com/aspnet/).</span></span> <span data-ttu-id="8fe27-103">오타 수정 및 새 문서 작성을 통한 참여를 환영합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-103">Typo corrections and new articles are welcome contributions.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="8fe27-104">간단한 수정 또는 제안을 수행하는 방법</span><span class="sxs-lookup"><span data-stu-id="8fe27-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="8fe27-105">문서는 리포지토리에 Markdown 파일로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="8fe27-106">Markdown 파일의 내용에 대한 간단한 변경 작업은 브라우저 창의 오른쪽 상단 모서리에서 **편집** 링크를 선택하여 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-106">Simple changes to the content of a Markdown file are made in the browser by selecting the **Edit** link in the upper-right corner of the browser window.</span></span> <span data-ttu-id="8fe27-107">(축소된 브라우저 창에서 **편집** 링크를 표시하려면 **옵션** 막대를 확장합니다.) 끌어오기 요청(PR)을 만들려면 지시를 따르세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-107">(In a narrow browser window, expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="8fe27-108">Microsoft에서 PR을 살펴보고 변경 사항을 수락하거나 제안합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-108">We will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="8fe27-109">더 복잡한 제출을 수행하는 방법</span><span class="sxs-lookup"><span data-stu-id="8fe27-109">How to make a more complex submission</span></span>

<span data-ttu-id="8fe27-110">[Git 및 GitHub.com](https://guides.github.com/activities/hello-world/)의 기본적인 내용을 이해하고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-110">You need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="8fe27-111">기존 문서 변경 또는 새 문서 생성처럼 원하는 작업을 설명하는 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/new)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-111">Open an [issue](https://github.com/dotnet/AspNetCore.Docs/issues/new) describing what you want to do, such as changing an existing article or creating a new one.</span></span> <span data-ttu-id="8fe27-112">Microsoft에서는 새 항목 제안의 개요를 요청하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-112">We often request an outline for a new topic suggestion.</span></span> <span data-ttu-id="8fe27-113">많은 시간을 투자하기 전에 팀의 승인을 기다리세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-113">Wait for approval from the team before you invest much time.</span></span>
* <span data-ttu-id="8fe27-114">[aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) 리포지토리를 포크하고 변경 내용에 대한 분기를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-114">Fork the [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="8fe27-115">변경 내용과 함께 PR을 *main* 분기에 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-115">Submit a PR to the *main* branch with your changes.</span></span>
* <span data-ttu-id="8fe27-116">PR에 'cla-required' 레이블이 할당되어 있으면 [CLA(기여 사용권 계약)를 작성](https://cla.dotnetfoundation.org/)하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-116">If your PR has the label 'cla-required' assigned, [complete the Contribution License Agreement (CLA)](https://cla.dotnetfoundation.org/).</span></span>
* <span data-ttu-id="8fe27-117">PR 피드백에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-117">Respond to PR feedback.</span></span>

<span data-ttu-id="8fe27-118">예를 들어 이 프로세스를 통해 새 문서가 게시되는 경우 .NET Docs 리포지토리에서 [이슈 1477](https://github.com/dotnet/docs/issues/1477) 및 [끌어오기 요청 18955](https://github.com/dotnet/docs/pull/18955)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-118">For an example where this process led to publication of a new article, see [Issue 1477](https://github.com/dotnet/docs/issues/1477) and [Pull Request 18955](https://github.com/dotnet/docs/pull/18955) in the .NET Docs repository.</span></span> <span data-ttu-id="8fe27-119">새 문서는 [유닛 테스트에 코드 검사 사용](/dotnet/core/testing/unit-testing-code-coverage)입니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-119">The new article is [Use code coverage for unit testing](/dotnet/core/testing/unit-testing-code-coverage).</span></span>

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a><span data-ttu-id="8fe27-120">Visual Studio Code에서 Docs Authoring Pack 확장</span><span class="sxs-lookup"><span data-stu-id="8fe27-120">Docs Authoring Pack extension in Visual Studio Code</span></span>

<span data-ttu-id="8fe27-121">Visual Studio Code를 사용하여 ASP.NET 문서에 참여하는 경우 [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) 확장을 설치하여 생산성을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-121">If you use Visual Studio Code to contribute to the ASP.NET documentation, you can boost your productivity by installing the [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) extension.</span></span> <span data-ttu-id="8fe27-122">이 확장은 Markdown 린팅, 코드 맞춤법 검사 및 문서 서식 파일에 도움이 되는 다양한 도구를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-122">The extension provides a variety of tools that helps with Markdown linting, code spell checking, and article templates.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="8fe27-123">Markdown 구문</span><span class="sxs-lookup"><span data-stu-id="8fe27-123">Markdown syntax</span></span>

<span data-ttu-id="8fe27-124">문서는 [GitHub-flavored Markdown(GFM)](https://guides.github.com/features/mastering-markdown/)의 상위 집합인 [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-124">Articles are written in [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="8fe27-125">ASP.NET 문서에서 일반적으로 사용되는 UI 기능의 DFM 구문 예는 .NET Docs 리포지토리 스타일 가이드의 [메타데이터 및 Markdown 템플릿](https://github.com/dotnet/docs/blob/main/styleguide/template.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-125">For examples of DFM syntax for UI features commonly used in the ASP.NET documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/main/styleguide/template.md) in the .NET Docs repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="8fe27-126">폴더 구조 규칙</span><span class="sxs-lookup"><span data-stu-id="8fe27-126">Folder structure conventions</span></span>

<span data-ttu-id="8fe27-127">각 Markdown 파일에는 이미지 폴더와 샘플 코드 폴더가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-127">For each Markdown file, a folder for images and a folder for sample code may exist.</span></span> <span data-ttu-id="8fe27-128">문서가 [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md)일 경우 이미지는 [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static)에 있으며, 샘플 앱 프로젝트 파일은 [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-128">If the article is [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md), the images are in [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static) and the sample app project files are in [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample).</span></span> <span data-ttu-id="8fe27-129">*fundamentals/configuration/index.md* 파일의 이미지는 다음과 같은 Markdown으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-129">An image in the *fundamentals/configuration/index.md* file is rendered by the following Markdown:</span></span>

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

<span data-ttu-id="8fe27-130">모든 이미지에는 [대체 텍스트](https://wikipedia.org/wiki/Alt_attribute)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-130">All images should have [alternative (alt) text](https://wikipedia.org/wiki/Alt_attribute).</span></span> <span data-ttu-id="8fe27-131">대체 텍스트를 지정하는 방법에 대한 조언을 보려면 [WebAIM: 대체 텍스트](https://webaim.org/techniques/alttext/)와 같은 온라인 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-131">For advice on specifying alt text, see online resources, such as [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/).</span></span>

<span data-ttu-id="8fe27-132">Markdown 파일 이름 및 이미지 파일 이름에는 소문자를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-132">Use lowercase for Markdown file names and image file names.</span></span>

## <a name="internal-links"></a><span data-ttu-id="8fe27-133">내부 링크</span><span class="sxs-lookup"><span data-stu-id="8fe27-133">Internal links</span></span>

<span data-ttu-id="8fe27-134">내부 링크는 xref 링크가 있는 대상 문서의 `uid`를 사용해야 합니다(링크 텍스트는 연결된 콘텐츠의 제목으로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="8fe27-134">Internal links should use the `uid` of the target article with an xref link (link text is set to the linked content's title):</span></span>

```md
<xref:uid_of_the_topic>
```

<span data-ttu-id="8fe27-135">문서의 제목이 링크 텍스트에 적합하지 않은 경우(예: 문장의 단어나 구문이 링크 텍스트인 경우) 다음을 사용하여 xref 링크 및 링크 텍스트를 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-135">If the title of the article is unsuitable for link text (for example, a word or phrase in a sentence is the link text), specify the xref link and link text with the following:</span></span>

```md
[link text](xref:uid_of_the_topic)
```

<span data-ttu-id="8fe27-136">자세한 내용은 [DocFX 교차 참조](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-136">For more information, see the [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span></span>

## <a name="images-and-screenshots"></a><span data-ttu-id="8fe27-137">이미지 및 스크린샷</span><span class="sxs-lookup"><span data-stu-id="8fe27-137">Images and screenshots</span></span>

<span data-ttu-id="8fe27-138">다음을 제외하고 문서와 함께 이미지를 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-138">Don't include images with articles, except:</span></span>

* <span data-ttu-id="8fe27-139">기본 온보딩(초급) 자습서에서</span><span class="sxs-lookup"><span data-stu-id="8fe27-139">In basic onboarding (beginner) tutorials.</span></span>
* <span data-ttu-id="8fe27-140">명확성을 위해 이미지가 필요한 경우</span><span class="sxs-lookup"><span data-stu-id="8fe27-140">When an image is needed for clarity.</span></span>

<span data-ttu-id="8fe27-141">이러한 제한 사항은 리포지토리의 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-141">These restrictions reduce the size of the repository.</span></span>

<span data-ttu-id="8fe27-142">선택적 단계로, 문서에서 사용되는 이미지 및 스크린샷이 압축되었는지 확인하세요. 그러면 파일 크기 및 페이지 로드 성능이 개선됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-142">As an optional step, ensure that any images and screenshots used in the documentation are compressed, which helps with file size and page load performance.</span></span> <span data-ttu-id="8fe27-143">주요 도구로는 TinyPNG([TinyPNG 웹 사이트](https://tinypng.com/) 또는 [TinyPNG API](https://tinypng.com/developers) 사용) 또는 [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio 확장이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-143">A few popular tools include TinyPNG (using the [TinyPNG website](https://tinypng.com/) or the [TinyPNG API](https://tinypng.com/developers)) or the [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio extension.</span></span> 

## <a name="code-snippets"></a><span data-ttu-id="8fe27-144">코드 조각</span><span class="sxs-lookup"><span data-stu-id="8fe27-144">Code snippets</span></span>

<span data-ttu-id="8fe27-145">문서에는 요점을 설명하는 코드 조각이 포함된 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-145">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="8fe27-146">DFM을 사용하여 Markdown 파일에 코드를 복사하거나 별도의 코드 파일을 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-146">DFM allows you to copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="8fe27-147">코드에서 오류 가능성을 최소화하기 위해 가능하면 별도의 코드 파일을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-147">We prefer to use separate code files whenever possible to minimize the chance of errors in the code.</span></span> <span data-ttu-id="8fe27-148">코드 파일은 이전에 샘플 프로젝트에서 설명한 폴더 구조를 사용하여 리포지토리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-148">The code files are stored in the repo using the folder structure described earlier for sample projects.</span></span> 

<span data-ttu-id="8fe27-149">다음 예에서는 *configuration/index.md* 파일에서 사용할 [DFM 코드 조각 구문](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-149">The following examples illustrate [DFM code snippet syntax](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) for use in a *configuration/index.md* file.</span></span>

<span data-ttu-id="8fe27-150">전체 코드 파일을 코드 조각으로 렌더링하려면</span><span class="sxs-lookup"><span data-stu-id="8fe27-150">To render an entire code file as a snippet:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

<span data-ttu-id="8fe27-151">줄 번호를 사용하여 파일의 일부를 코드 조각으로 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="8fe27-151">To render a portion of a file as a snippet by using line numbers:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

<span data-ttu-id="8fe27-152">C# 코드 조각의 경우 [C# 지역](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-152">For C# snippets, reference a [C# region](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span></span> <span data-ttu-id="8fe27-153">가능할 때마다 줄 번호보다는 지역을 사용하세요. 코드 파일의 줄 번호는 변경되거나, Markdown의 줄 번호 참조와 비동기화될 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-153">Whenever possible, use regions rather than line numbers because line numbers in a code file tend to change and become out of sync with line number references in Markdown.</span></span> <span data-ttu-id="8fe27-154">C# 지역은 중첩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-154">C# regions can be nested.</span></span> <span data-ttu-id="8fe27-155">외부 지역을 참조할 경우 내부 `#region` 및 `#endregion` 지시문은 코드 조각에 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-155">If referencing the outer region, the inner `#region` and `#endregion` directives aren't rendered in a snippet.</span></span> 

<span data-ttu-id="8fe27-156">"snippet_Example"이라는 C# 지역을 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="8fe27-156">To render a C# region named "snippet_Example":</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="8fe27-157">렌더링된 코드 조각에서 선택된 줄을 강조 표시하려면(일반적으로 노란색 배경으로 렌더링됨)</span><span class="sxs-lookup"><span data-stu-id="8fe27-157">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a><span data-ttu-id="8fe27-158">DocFX로 변경 내용 테스트</span><span class="sxs-lookup"><span data-stu-id="8fe27-158">Test changes with DocFX</span></span>

<span data-ttu-id="8fe27-159">로컬에서 호스팅되는 사이트 버전을 만드는 [DocFX 명령줄 도구](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)로 변경 내용을 테스트하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-159">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="8fe27-160">DocFX는 docs.microsoft.com용으로 생성된 스타일 및 사이트 확장을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-160">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="8fe27-161">DocFX 요구 사항:</span><span class="sxs-lookup"><span data-stu-id="8fe27-161">DocFX requires:</span></span>

* <span data-ttu-id="8fe27-162">Windows에 설치된 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="8fe27-162">.NET Framework on Windows.</span></span>
* <span data-ttu-id="8fe27-163">Linux 또는 macOS용 Mono.</span><span class="sxs-lookup"><span data-stu-id="8fe27-163">Mono for Linux or macOS.</span></span> 

### <a name="windows-instructions"></a><span data-ttu-id="8fe27-164">Windows 지침</span><span class="sxs-lookup"><span data-stu-id="8fe27-164">Windows instructions</span></span>

* <span data-ttu-id="8fe27-165">[DocFX 릴리스](https://github.com/dotnet/docfx/releases)에서 *docfx.zip* 을 다운로드하고 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-165">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="8fe27-166">PATH에 DocFX를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-166">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="8fe27-167">명령 셸에서 *docfx.json* 파일(ASP.NET 콘텐츠용 *aspnet* 또는 ASP.NET Core 콘텐츠용 *aspnetcore*)이 포함된 폴더로 이동하여 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-167">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command:</span></span>

  ```console
  docfx --serve
  ```

* <span data-ttu-id="8fe27-168">브라우저에서 `http://localhost:8080/group1-dest/`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-168">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="8fe27-169">Mono 지침</span><span class="sxs-lookup"><span data-stu-id="8fe27-169">Mono instructions</span></span>

* <span data-ttu-id="8fe27-170">Homebrew를 통해 Mono 설치:</span><span class="sxs-lookup"><span data-stu-id="8fe27-170">Install Mono via Homebrew:</span></span>

  ```console
  brew install mono
  ```

* <span data-ttu-id="8fe27-171">[최신 버전의 DocFX](https://github.com/dotnet/docfx/releases)를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-171">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="8fe27-172">*$HOME/bin/docfx* 에 보관 파일을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-172">Extract the archive to *$HOME/bin/docfx*.</span></span>
* <span data-ttu-id="8fe27-173">bash 셸에서 **docfx** 의 별칭 쌍을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-173">Create a pair of aliases for **docfx** in a bash shell.</span></span> <span data-ttu-id="8fe27-174">첫 번째 별칭은 문서를 작성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-174">The first alias is used to build the documentation.</span></span> <span data-ttu-id="8fe27-175">두 번째 별칭은 문서를 작성하고 서비스를 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-175">The second alias is used to build and serve the documentation.</span></span>

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* <span data-ttu-id="8fe27-176">명령 셸에서 *docfx.json* 파일(ASP.NET 콘텐츠용 *aspnet* 또는 ASP.NET Core 콘텐츠용 *aspnetcore*)이 포함된 폴더로 이동하고 다음 명령을 실행하여 해당 별칭을 통해 문서를 빌드하고 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-176">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command to build and serve the docs via its alias:</span></span>

  ```console
  docfx-serve
  ```

* <span data-ttu-id="8fe27-177">브라우저에서 `http://localhost:8080/group1-dest/`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-177">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="8fe27-178">음성 및 톤</span><span class="sxs-lookup"><span data-stu-id="8fe27-178">Voice and tone</span></span>

<span data-ttu-id="8fe27-179">가장 폭넓은 잠재 고객이 쉽게 이해할 수 있는 설명서를 작성하는 것이 목표입니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-179">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="8fe27-180">이에 따라 Microsoft는 기여자가 지켜주었으면 하는 문장체에 대한 지침을 정했습니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-180">To that end, we established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="8fe27-181">자세한 내용은 .NET 리포지토리에서 [어투 및 어조 지침](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-181">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md) in the .NET repo.</span></span>

## <a name="microsoft-writing-style-guide"></a><span data-ttu-id="8fe27-182">Microsoft 문장체 가이드</span><span class="sxs-lookup"><span data-stu-id="8fe27-182">Microsoft Writing Style Guide</span></span>

<span data-ttu-id="8fe27-183">[Microsoft 문장체 가이드](https://docs.microsoft.com/style-guide/welcome/)에서는 ASP.NET Core 설명서를 포함한 모든 형태의 기술 설명서에 대한 문장체 및 용어 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-183">The [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/) provides writing style and terminology guidance for all forms of technology communication, including the ASP.NET Core documentation.</span></span>

## <a name="redirects"></a><span data-ttu-id="8fe27-184">리디렉션</span><span class="sxs-lookup"><span data-stu-id="8fe27-184">Redirects</span></span>

<span data-ttu-id="8fe27-185">문서를 삭제하거나, 파일 이름을 변경하거나, 다른 폴더로 이동할 경우 해당 문서를 책갈피에 추가한 사람이 *404 찾을 수 없음* 오류를 받지 않도록 리디렉션을 만드세요.</span><span class="sxs-lookup"><span data-stu-id="8fe27-185">If you delete an article, change its file name, or move it to a different folder, create a redirect so that people who bookmarked the article don't receive a *404 Not Found* error.</span></span> <span data-ttu-id="8fe27-186">[main 리디렉션 파일](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json)에 리디렉션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8fe27-186">Add redirects to the [main redirect file](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json).</span></span>
