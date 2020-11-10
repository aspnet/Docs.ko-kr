---
title: 'ASP.NET Core :::no-loc(Blazor)::: 파일 업로드'
author: guardrex
description: InputFile 구성 요소를 사용하여 :::no-loc(Blazor):::에서 파일을 업로드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056129"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="d3258-103">ASP.NET Core :::no-loc(Blazor)::: 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="d3258-103">ASP.NET Core :::no-loc(Blazor)::: file uploads</span></span>

<span data-ttu-id="d3258-104">작성자: [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="d3258-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d3258-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3258-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d3258-106">`InputFile` 구성 요소를 사용하여 파일 업로드용을 비롯한 브라우저 파일 데이터를 .NET 코드로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="d3258-107">항상 파일 업로드 보안 모범 사례를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="d3258-108">자세한 내용은 <xref:mvc/models/file-uploads#security-considerations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3258-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="d3258-109">`InputFile` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="d3258-109">`InputFile` component</span></span>

<span data-ttu-id="d3258-110">`InputFile` 구성 요소는 `file` 형식의 HTML 입력으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="d3258-111">기본적으로 사용자는 단일 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-111">By default, the user selects single files.</span></span> <span data-ttu-id="d3258-112">사용자가 한 번에 여러 파일을 업로드할 수 있도록 `multiple` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="d3258-113">사용자가 하나 이상의 파일을 선택하면 `InputFile` 구성 요소는 `OnChange` 이벤트를 발생시키고 선택한 파일 목록 및 각 파일에 대한 세부 정보에 액세스할 수 있는 `InputFileChangeEventArgs`를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="d3258-114">사용자가 선택한 파일에서 데이터를 읽으려면</span><span class="sxs-lookup"><span data-stu-id="d3258-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="d3258-115">파일에 대한 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`을 호출하고 반환된 스트림에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="d3258-116">자세한 내용은 [파일 스트림](#file-streams) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3258-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="d3258-117">`OpenReadStream`에서 반환되는 <xref:System.IO.Stream>은 읽고 있는 `Stream`의 최대 크기(바이트)를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="d3258-118">기본적으로, 추가 읽기로 인해 예외가 발생하기 전에는 524,288KB(512KB)보다 작은 파일만 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-118">By default, only files smaller than 524,288 KB (512 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="d3258-119">이 제한은 개발자가 실수로 메모리에 대규모 파일을 읽어 들이는 것을 방지하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="d3258-120">필요한 경우 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`의 `maxAllowedSize` 매개 변수를 사용하여 더 큰 크기를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="d3258-121">들어오는 파일 스트림을 메모리로 직접 읽지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="d3258-122">예를 들어 파일 바이트를 <xref:System.IO.MemoryStream>에 복사하거나 바이트 배열로 읽지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="d3258-123">이러한 접근 방식은 특히 :::no-loc(Blazor Server):::에서 성능 및 보안 문제를 초래할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-123">These approaches can result in performance and security problems, especially in :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="d3258-124">대신 Blob 또는 디스크의 파일과 같은 외부 저장소에 파일 바이트를 복사하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="d3258-125">이미지 파일을 수신하는 구성 요소는 파일에 대한 `RequestImageFileAsync` 편의 메서드를 호출하여 이미지가 앱으로 스트리밍되기 전에 브라우저의 JavaScript 런타임 내에서 이미지 데이터의 크기를 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="d3258-126">다음 예제에서는 한 구성 요소에서 여러 이미지 파일을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="d3258-127">`InputFileChangeEventArgs.GetMultipleFiles`는 여러 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="d3258-128">악의적인 사용자가 앱에서 예상하는 것보다 많은 수의 파일을 업로드하지 못하도록 예상되는 최대 읽기 파일 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="d3258-129">`InputFileChangeEventArgs.File`은 파일 업로드가 여러 파일을 지원하지 않을 경우 첫 번째 및 유일한 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="d3258-130">`IBrowserFile`은 [브라우저](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)에서 속성으로 노출하는 메타데이터를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="d3258-131">이 메타데이터는 예비 유효성 검사에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="d3258-132">예제는 [`FileUpload.razor` 및 `FilePreview.razor` 샘플 구성 요소](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3258-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="d3258-133">파일 스트림</span><span class="sxs-lookup"><span data-stu-id="d3258-133">File streams</span></span>

<span data-ttu-id="d3258-134">:::no-loc(Blazor WebAssembly)::: 앱에서 데이터는 브라우저 내의 .NET 코드로 직접 스트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-134">In a :::no-loc(Blazor WebAssembly)::: app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="d3258-135">:::no-loc(Blazor Server)::: 앱에서는 스트림에서 파일을 읽을 때 파일 데이터가 :::no-loc(SignalR)::: 연결을 통해 서버의 .NET 코드로 스트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-135">In a :::no-loc(Blazor Server)::: app, the file data is streamed over the :::no-loc(SignalR)::: connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="d3258-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs)를 통해 :::no-loc(Blazor Server):::에 대한 파일 업로드 특성을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3258-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for :::no-loc(Blazor Server):::.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3258-137">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d3258-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
