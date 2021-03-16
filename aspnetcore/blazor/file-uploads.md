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
ms.date: 02/18/2021
uid: blazor/file-uploads
ms.openlocfilehash: a31821f03efd39d774a4a3c61d027983a1783e2d
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395125"
---
# <a name="aspnet-core-blazor-file-uploads"></a>ASP.NET Core Blazor 파일 업로드

`InputFile` 구성 요소를 사용하여 파일 업로드용을 비롯한 브라우저 파일 데이터를 .NET 코드로 읽습니다.

> [!WARNING]
> 항상 파일 업로드 보안 모범 사례를 따릅니다. 자세한 내용은 <xref:mvc/models/file-uploads#security-considerations>를 참조하세요.

## <a name="inputfile-component"></a>`InputFile` 구성 요소

`InputFile` 구성 요소는 `file` 형식의 HTML 입력으로 렌더링됩니다.

기본적으로 사용자는 단일 파일을 선택합니다. 사용자가 한 번에 여러 파일을 업로드할 수 있도록 `multiple` 특성을 추가합니다. 사용자가 하나 이상의 파일을 선택하면 `InputFile` 구성 요소는 `OnChange` 이벤트를 발생시키고 선택한 파일 목록 및 각 파일에 대한 세부 정보에 액세스할 수 있는 `InputFileChangeEventArgs`를 전달합니다.

사용자가 선택한 파일에서 데이터를 읽으려면

* 파일에 대한 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`을 호출하고 반환된 스트림에서 읽습니다. 자세한 내용은 [파일 스트림](#file-streams) 섹션을 참조하세요.
* `OpenReadStream`에서 반환되는 <xref:System.IO.Stream>은 읽고 있는 `Stream`의 최대 크기(바이트)를 적용합니다. 기본적으로, 추가 읽기로 인해 예외가 발생하기 전에는 512,000바이트(500KB) 이하 크기의 파일만 읽을 수 있습니다. 이 제한은 개발자가 실수로 메모리에 대규모 파일을 읽어 들이는 것을 방지하기 위한 것입니다. 필요한 경우 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`의 `maxAllowedSize` 매개 변수를 사용하여 더 큰 크기를 지정할 수 있습니다.
* 들어오는 파일 스트림을 메모리로 직접 읽지 않도록 합니다. 예를 들어 파일 바이트를 <xref:System.IO.MemoryStream>에 복사하거나 바이트 배열로 읽지 않습니다. 이러한 접근 방식은 특히 Blazor Server에서 성능 및 보안 문제를 초래할 수 있습니다. 대신 Blob 또는 디스크의 파일과 같은 외부 저장소에 파일 바이트를 복사하는 것이 좋습니다.

이미지 파일을 수신하는 구성 요소는 파일에 대한 `RequestImageFileAsync` 편의 메서드를 호출하여 이미지가 앱으로 스트리밍되기 전에 브라우저의 JavaScript 런타임 내에서 이미지 데이터의 크기를 조정할 수 있습니다.

다음 예제에서는 한 구성 요소에서 여러 파일을 업로드하는 방법을 보여 줍니다. `InputFileChangeEventArgs.GetMultipleFiles`는 여러 파일을 읽을 수 있습니다. 악의적인 사용자가 앱에서 예상하는 것보다 많은 수의 파일을 업로드하지 못하도록 예상되는 최대 읽기 파일 수를 지정합니다. `InputFileChangeEventArgs.File`은 파일 업로드가 여러 파일을 지원하지 않을 경우 첫 번째 및 유일한 파일을 읽을 수 있습니다.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs>는 앱 `_Imports.razor` 파일의 네임스페이스 중 하나인 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 네임스페이스에 있습니다.

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.IO

<h3>Upload Files</h3>

<p>
    <label>
        Max file size:
        <input type="number" @bind="maxFileSize" />
    </label>
</p>

<p>
    <label>
        Max allowed files:
        <input type="number" @bind="maxAllowedFiles" />
    </label>
</p>

<p>
    <label>
        Upload up to @maxAllowedFiles files of up to @maxFileSize bytes each:
        <InputFile OnChange="@LoadFiles" multiple />
    </label>
</p>

<p>@exceptionMessage</p>

@if (isLoading)
{
    <p>Loading...</p>
}

<ul>
    @foreach (var (file, content) in loadedFiles)
    {
        <li>
            <ul>
                <li>Name: @file.Name</li>
                <li>Last modified: @file.LastModified.ToString()</li>
                <li>Size (bytes): @file.Size</li>
                <li>Content type: @file.ContentType</li>
                <li>Content: @content</li>
            </ul>
        </li>
    }
</ul>

@code {
    private Dictionary<IBrowserFile, string> loadedFiles =
        new Dictionary<IBrowserFile, string>();
    private long maxFileSize = 1024 * 15;
    private int maxAllowedFiles = 3;
    private bool isLoading;
    string exceptionMessage;

    async Task LoadFiles(InputFileChangeEventArgs e)
    {
        isLoading = true;
        loadedFiles.Clear();
        exceptionMessage = string.Empty;

        try
        {
            foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
            {
                using var reader = 
                    new StreamReader(file.OpenReadStream(maxFileSize));

                loadedFiles.Add(file, await reader.ReadToEndAsync());
            }
        }
        catch (Exception ex)
        {
            exceptionMessage = ex.Message;
        }

        isLoading = false;
    }
}
```

`IBrowserFile`은 [브라우저](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)에서 속성으로 노출하는 메타데이터를 반환합니다. 이 메타데이터는 예비 유효성 검사에 유용할 수 있습니다.

## <a name="upload-files-to-a-server"></a>서버에 파일 업로드

다음 예제에서는 호스트된 Blazor WebAssembly 솔루션을 사용하여 서버에 파일을 업로드하는 방법을 보여 줍니다.

> [!WARNING]
> 사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다. 자세한 내용은 <xref:mvc/models/file-uploads#security-considerations>를 참조하세요.

**`Shared`** 프로젝트의 다음 `UploadResult` 클래스는 업로드된 파일의 결과를 유지 관리합니다. 서버에서 파일을 업로드하지 못할 경우 사용자에게 표시하기 위해 `ErrorCode`로 오류 코드가 반환됩니다. 각 파일에 대해 안전한 저장된 파일 이름이 서버에서 생성되며 표시를 위해 `StoredFileName`으로 클라이언트에 반환됩니다. `FileName`의 안전하지 않은/신뢰할 수 없는 파일 이름을 사용하여 클라이언트와 서버 간에 파일의 키가 지정됩니다.

`UploadResult.cs`:

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

**`Client`** 프로젝트의 다음 `UploadFiles` 구성 요소:

* 사용자가 클라이언트에서 파일을 업로드하도록 허용합니다.
* Razor에서 문자열을 자동으로 HTML로 인코딩하므로 클라이언트가 제공하는 신뢰할 수 없는/안전하지 않은 파일 이름을 UI에 표시합니다. **다른 시나리오에서는 클라이언트가 제공하는 파일 이름을 신뢰하지 마세요.**

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.Linq
@using Microsoft.Extensions.Logging
@inject HttpClient Http
@inject ILogger<UploadFiles> logger

<h1>Upload Files</h1>

<p>
    <label>
        Upload up to @maxAllowedFiles files:
        <InputFile OnChange="@OnInputFileChange" multiple />
    </label>
</p>

@if (files.Count > 0)
{
    <div class="card">
        <div class="card-body">
            <ul>
                @foreach (var file in files)
                {
                    <li>
                        File: @file.Name
                        <br>
                        @if (FileUpload(uploadResults, file.Name, logger,
                            out var result))
                        {
                            <span>
                                Stored File Name: @result.StoredFileName
                            </span>
                        }
                        else
                        {
                            <span>
                                There was an error uploading the file
                                (Error: @result.ErrorCode).
                            </span>
                        }
                    </li>
                }
            </ul>
        </div>
    </div>
}

@code {
    private IList<File> files = new List<File>();
    private IList<UploadResult> uploadResults = new List<UploadResult>();
    private int maxAllowedFiles = 3;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        shouldRender = false;
        long maxFileSize = 1024 * 1024 * 15;
        var upload = false;

        using var content = new MultipartFormDataContent();

        foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
        {
            if (uploadResults.SingleOrDefault(
                f => f.FileName == file.Name) is null)
            {
                var fileContent = new StreamContent(file.OpenReadStream());

                files.Add(
                    new File()
                    {
                        Name = file.Name,
                    });

                if (file.Size < maxFileSize)
                {
                    content.Add(
                        content: fileContent,
                        name: "\"files\"",
                        fileName: file.Name);

                    upload = true;
                }
                else
                {
                    logger.LogInformation("{FileName} not uploaded", file.Name);

                    uploadResults.Add(
                        new UploadResult()
                        {
                            FileName = file.Name,
                            ErrorCode = 6,
                            Uploaded = false,
                        });
                }
            }
        }

        if (upload)
        {
            var response = await Http.PostAsync("/Filesave", content);

            var newUploadResults = await response.Content
                .ReadFromJsonAsync<IList<UploadResult>>();

            uploadResults = uploadResults.Concat(newUploadResults).ToList();
        }

        shouldRender = true;
    }

    private static bool FileUpload(IList<UploadResult> uploadResults,
        string fileName, ILogger<UploadFiles> logger, out UploadResult result)
    {
        result = uploadResults.SingleOrDefault(f => f.FileName == fileName);

        if (result is null)
        {
            logger.LogInformation("{FileName} not uploaded", fileName);
            result = new UploadResult();
            result.ErrorCode = 5;
        }

        return result.Uploaded;
    }

    private class File
    {
        public string Name { get; set; }
    }
}
```

다음 코드를 사용하려면 **`Server`** 프로젝트의 루트에 `Development/unsafe_uploads` 폴더를 만듭니다.

**`Server`** 프로젝트의 다음 `FilesaveController` 컨트롤러는 클라이언트에서 업로드된 파일을 저장합니다.

> [!WARNING]
> 이 예제에서는 콘텐츠를 검사하지 않고 파일을 저장합니다. 대부분의 프로덕션 시나리오에서는 파일을 다운로드하거나 다른 시스템에서 사용할 수 있도록 하기 전에 바이러스 백신/맬웨어 방지 스캐너 API가 파일에 사용됩니다. 서버에 파일을 업로드할 때의 보안 고려 사항에 대한 자세한 내용은 <xref:mvc/models/file-uploads#security-considerations>를 참조하세요.

`Controllers/FilesaveController.cs`:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

[ApiController]
[Route("[controller]")]
public class FilesaveController : ControllerBase
{
    private readonly IWebHostEnvironment env;
    private readonly ILogger<FilesaveController> logger;

    public FilesaveController(IWebHostEnvironment env, 
        ILogger<FilesaveController> logger)
    {
        this.env = env;
        this.logger = logger;
    }

    [HttpPost]
    public async Task<ActionResult<IList<UploadResult>>> PostFile(
        [FromForm] IEnumerable<IFormFile> files)
    {
        var maxAllowedFiles = 3;
        long maxFileSize = 1024 * 1024 * 15;
        var filesProcessed = 0;
        var resourcePath = new Uri($"{Request.Scheme}://{Request.Host}/");
        IList<UploadResult> uploadResults = new List<UploadResult>();

        foreach (var file in files)
        {
            var uploadResult = new UploadResult();
            string trustedFileNameForFileStorage;
            var untrustedFileName = file.FileName;
            uploadResult.FileName = untrustedFileName;
            var trustedFileNameForDisplay = 
                WebUtility.HtmlEncode(untrustedFileName);

            if (filesProcessed < maxAllowedFiles)
            {
                if (file.Length == 0)
                {
                    logger.LogInformation("{FileName} length is 0", 
                        trustedFileNameForDisplay);
                    uploadResult.ErrorCode = 1;
                }
                else if (file.Length > maxFileSize)
                {
                    logger.LogInformation("{FileName} of {Length} bytes is " +
                        "larger than the limit of {Limit} bytes", 
                        trustedFileNameForDisplay, file.Length, maxFileSize);
                    uploadResult.ErrorCode = 2;
                }
                else
                {
                    try
                    {
                        trustedFileNameForFileStorage = Path.GetRandomFileName();
                        var path = Path.Combine(env.ContentRootPath, 
                            env.EnvironmentName, "unsafe_uploads", 
                            trustedFileNameForFileStorage);
                        using MemoryStream ms = new();
                        await file.CopyToAsync(ms);
                        await System.IO.File.WriteAllBytesAsync(path, ms.ToArray());
                        logger.LogInformation("{FileName} saved at {Path}", 
                            trustedFileNameForDisplay, path);
                        uploadResult.Uploaded = true;
                        uploadResult.StoredFileName = trustedFileNameForFileStorage;
                    }
                    catch (IOException ex)
                    {
                        logger.LogError("{FileName} error on upload: {Message}", 
                            trustedFileNameForDisplay, ex.Message);
                        uploadResult.ErrorCode = 3;
                    }
                }

                filesProcessed++;
            }
            else
            {
                logger.LogInformation("{FileName} not uploaded because the " +
                    "request exceeded the allowed {Count} of files", 
                    trustedFileNameForDisplay, maxAllowedFiles);
                uploadResult.ErrorCode = 4;
            }

            uploadResults.Add(uploadResult);
        }

        return new CreatedResult(resourcePath, uploadResults);
    }
}
```

## <a name="file-streams"></a>파일 스트림

Blazor WebAssembly에서는 파일 데이터가 브라우저 내의 .NET 코드로 직접 스트리밍됩니다.

Blazor Server에서는 스트림에서 파일을 읽을 때 파일 데이터가 SignalR 연결을 통해 서버의 .NET 코드로 스트리밍됩니다. <xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions>를 통해 Blazor Server에 대한 파일 업로드 특성을 구성할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
