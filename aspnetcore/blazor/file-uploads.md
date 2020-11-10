---
title: ASP.NET Core Blazor 파일 업로드
author: guardrex
description: InputFile 구성 요소를 사용하여 Blazor에서 파일을 업로드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056129"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor 파일 업로드

작성자: [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

`InputFile` 구성 요소를 사용하여 파일 업로드용을 비롯한 브라우저 파일 데이터를 .NET 코드로 읽습니다.

> [!WARNING]
> 항상 파일 업로드 보안 모범 사례를 따릅니다. 자세한 내용은 <xref:mvc/models/file-uploads#security-considerations>를 참조하세요.

## <a name="inputfile-component"></a>`InputFile` 구성 요소

`InputFile` 구성 요소는 `file` 형식의 HTML 입력으로 렌더링됩니다.

기본적으로 사용자는 단일 파일을 선택합니다. 사용자가 한 번에 여러 파일을 업로드할 수 있도록 `multiple` 특성을 추가합니다. 사용자가 하나 이상의 파일을 선택하면 `InputFile` 구성 요소는 `OnChange` 이벤트를 발생시키고 선택한 파일 목록 및 각 파일에 대한 세부 정보에 액세스할 수 있는 `InputFileChangeEventArgs`를 전달합니다.

사용자가 선택한 파일에서 데이터를 읽으려면

* 파일에 대한 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`을 호출하고 반환된 스트림에서 읽습니다. 자세한 내용은 [파일 스트림](#file-streams) 섹션을 참조하세요.
* `OpenReadStream`에서 반환되는 <xref:System.IO.Stream>은 읽고 있는 `Stream`의 최대 크기(바이트)를 적용합니다. 기본적으로, 추가 읽기로 인해 예외가 발생하기 전에는 524,288KB(512KB)보다 작은 파일만 읽을 수 있습니다. 이 제한은 개발자가 실수로 메모리에 대규모 파일을 읽어 들이는 것을 방지하기 위한 것입니다. 필요한 경우 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`의 `maxAllowedSize` 매개 변수를 사용하여 더 큰 크기를 지정할 수 있습니다.
* 들어오는 파일 스트림을 메모리로 직접 읽지 않도록 합니다. 예를 들어 파일 바이트를 <xref:System.IO.MemoryStream>에 복사하거나 바이트 배열로 읽지 않습니다. 이러한 접근 방식은 특히 Blazor Server에서 성능 및 보안 문제를 초래할 수 있습니다. 대신 Blob 또는 디스크의 파일과 같은 외부 저장소에 파일 바이트를 복사하는 것이 좋습니다.

이미지 파일을 수신하는 구성 요소는 파일에 대한 `RequestImageFileAsync` 편의 메서드를 호출하여 이미지가 앱으로 스트리밍되기 전에 브라우저의 JavaScript 런타임 내에서 이미지 데이터의 크기를 조정할 수 있습니다.

다음 예제에서는 한 구성 요소에서 여러 이미지 파일을 업로드하는 방법을 보여 줍니다. `InputFileChangeEventArgs.GetMultipleFiles`는 여러 파일을 읽을 수 있습니다. 악의적인 사용자가 앱에서 예상하는 것보다 많은 수의 파일을 업로드하지 못하도록 예상되는 최대 읽기 파일 수를 지정합니다. `InputFileChangeEventArgs.File`은 파일 업로드가 여러 파일을 지원하지 않을 경우 첫 번째 및 유일한 파일을 읽을 수 있습니다.

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

`IBrowserFile`은 [브라우저](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)에서 속성으로 노출하는 메타데이터를 반환합니다. 이 메타데이터는 예비 유효성 검사에 유용할 수 있습니다. 예제는 [`FileUpload.razor` 및 `FilePreview.razor` 샘플 구성 요소](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)를 참조하세요.

## <a name="file-streams"></a>파일 스트림

Blazor WebAssembly 앱에서 데이터는 브라우저 내의 .NET 코드로 직접 스트리밍됩니다.

Blazor Server 앱에서는 스트림에서 파일을 읽을 때 파일 데이터가 SignalR 연결을 통해 서버의 .NET 코드로 스트리밍됩니다. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs)를 통해 Blazor Server에 대한 파일 업로드 특성을 구성할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
