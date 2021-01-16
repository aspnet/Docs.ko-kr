---
title: ASP.NET Core에서 파일 업로드
author: rick-anderson
description: 모델 바인딩 및 스트리밍을 사용하여 ASP.NET Core MVC에서 파일을 업로드하는 방법입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252801"
---
# <a name="upload-files-in-aspnet-core"></a>ASP.NET Core에서 파일 업로드

작성자, [Steve Smith](https://ardalis.com/) 및 [Rutger 스톰](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>보안 고려 사항

사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다. 공격자는 다음을 시도할 수 있습니다.

* [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.
* 바이러스 또는 맬웨어를 업로드.
* 다른 방법으로 네트워크 및 서버를 손상.

공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.

* 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다. 전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다. 파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;
* 업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;
* 앱에 의해 결정된 안전한 파일 이름을 사용합니다. 사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다. 예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)
* 앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.
* 업로드된 파일의 크기를 확인합니다. 대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;
* 업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.
* **파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**

&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.

> [!WARNING]
> 시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.
>
> * 시스템을 완전히 제어합니다.
> * 시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.
> * 사용자 또는 시스템 데이터를 손상시킵니다.
> * 공용 UI에 그래피티를 적용합니다.
>
> 사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
>
> * [무제한 파일 업로드](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.

## <a name="storage-scenarios"></a>스토리지 시나리오

파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.

* 데이터베이스

  * 소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.
  * 사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.
  * 데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.

* 실제 스토리지(파일 시스템 또는 네트워크 공유)

  * 대용량 파일 업로드의 경우:
    * 데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.
    * 실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.
  * 실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.
  * 앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다. **실행 권한을 부여하지 마세요.**

* 데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * 서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.
  * 서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.

  자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요.

## <a name="file-upload-scenarios"></a>파일 업로드 시나리오

파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.

**버퍼링**

전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.

파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다. 앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다. 파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.

> [!NOTE]
> 버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.

소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.

* [물리적 스토리지](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [데이터베이스](#upload-small-files-with-buffered-model-binding-to-a-database)

**스트리밍**

파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다. 스트리밍은 성능을 크게 개선하지 않습니다. 스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.

대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드

소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.

다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.

* 양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.
* 유효성 검사를 수행하지 않습니다.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.

* Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.
* `XMLHttpRequest`을 사용하세요. 예를 들면 다음과 같습니다.

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.

`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다. 샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.

<a name="filename"></a>

> [!WARNING]
> 표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**. 표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다. 공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다. 애플리케이션에서 다음을 수행해야 합니다.
>
> * 사용자가 제공한 파일 이름에서 경로를 제거합니다.
> * UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.
> * 스토리지의 새 임의 파일 이름을 생성합니다.
>
> 다음 코드는 파일 이름에서 경로를 제거합니다.
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.

* 단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.
* 여러 파일을 나타내는 다음 컬렉션 중 하나.
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 바인딩은 이름을 기준으로 양식 파일을 일치시킵니다. 예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다. 자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.

다음 예제를 참조하세요.

* 하나 이상의 업로드된 파일을 반복합니다.
* [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다. 
* 앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.
* 업로드된 파일의 총 수와 크기를 반환합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다. 다음 예제에서는 경로를 구성에서 가져옵니다.

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다. 파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.

<xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다. 작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다. 로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.

업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.

> [!WARNING]
> 이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다. 65535개 파일 제한은 서버당 제한입니다. Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.
>
> * [GetTempFileNameA 함수](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드

[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다. 위 예제에서는 바인딩된 모델 속성을 사용합니다.

는 `FileUpload` Pages 폼에 사용 됩니다 Razor .

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다. 다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> 관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.
>
> 유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요. `FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.
>
> 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

### <a name="upload-large-files-with-streaming"></a>스트리밍을 사용하여 대용량 파일 업로드

다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다. 사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다. 작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다. 작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다. 다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.

초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` . 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함). 작업 메서드는 `Request` 속성으로 직접 작동합니다. `MultipartReader`는 각 섹션을 읽는 데 사용됩니다. 키/값 데이터는 `KeyValueAccumulator`에 저장됩니다. 다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.

EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.

## <a name="validation"></a>유효성 검사

샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다. 샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요. 스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.

> [!WARNING]
> 샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다. 대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.
>
> 항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.
>
> * 구현을 완전히 이해합니다.
> * 앱의 환경 및 사양에 맞게 구현을 수정합니다.
>
> **이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**

### <a name="content-validation"></a>콘텐츠 유효성 검사

**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**

대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다. 파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다. 일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다. 파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다. 이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다. 이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.

### <a name="file-extension-validation"></a>파일 확장명 유효성 검사

업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다. 예를 들면 다음과 같습니다.

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>파일 서명 유효성 검사

파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다. 이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다. 샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다. 다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.

### <a name="file-name-security"></a>파일 이름 보안

실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요. [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.

Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다. 다음 코드는 안전하게 사용할 수 있습니다.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.

많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다. 앱의 사양을 충족하는 추가 논리를 제공합니다.

### <a name="size-validation"></a>크기 유효성 검사

업로드된 파일의 크기를 제한합니다.

샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다. 이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

파일 크기가 제한을 초과하는 파일은 거부됩니다.

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>이름 특성 값을 POST 메서드의 매개 변수 이름과 일치

폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.

다음 예제에서는

* `<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.

* Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST 컨트롤러 작업 메서드:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>서버 및 앱 구성

### <a name="multipart-body-length-limit"></a>다중 파트 본문 길이 제한

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다. 양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다. 기본값은 134,217,728(128MB)입니다. `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 최대 요청 본문 크기

Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다. [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size)를 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`지시문을 사용 하 여를 적용할 수도 있습니다 [`@attribute`](xref:mvc/views/razor#attribute) Razor .

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>기타 Kestrel 제한

다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.

* [최대 클라이언트 연결](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [요청 및 응답 데이터 속도](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다. 파일의 제한을 사용자 지정 합니다 `web.config` . 다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`설정은 IIS에만 적용 됩니다. 자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.

## <a name="troubleshoot"></a>문제 해결

다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>IIS 서버에 배포할 때 찾을 수 없음 오류

다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

자세한 내용은 [IIS](#iis) 섹션을 참조하세요.

### <a name="connection-failure"></a>연결 실패

연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다. 자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요. Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 사용 시 Null 참조 예외

컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다. `<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다. 또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.

### <a name="stream-was-too-long"></a>스트림이 너무 깁니다.

이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다. `MemoryStream`의 크기 제한은 `int.MaxValue`입니다. 앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>보안 고려 사항

사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다. 공격자는 다음을 시도할 수 있습니다.

* [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.
* 바이러스 또는 맬웨어를 업로드.
* 다른 방법으로 네트워크 및 서버를 손상.

공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.

* 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다. 전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다. 파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;
* 업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;
* 앱에 의해 결정된 안전한 파일 이름을 사용합니다. 사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다. 예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)
* 앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.
* 업로드된 파일의 크기를 확인합니다. 대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;
* 업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.
* **파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**

&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.

> [!WARNING]
> 시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.
>
> * 시스템을 완전히 제어합니다.
> * 시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.
> * 사용자 또는 시스템 데이터를 손상시킵니다.
> * 공용 UI에 그래피티를 적용합니다.
>
> 사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
>
> * [무제한 파일 업로드](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.

## <a name="storage-scenarios"></a>스토리지 시나리오

파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.

* 데이터베이스

  * 소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.
  * 사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.
  * 데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.

* 실제 스토리지(파일 시스템 또는 네트워크 공유)

  * 대용량 파일 업로드의 경우:
    * 데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.
    * 실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.
  * 실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.
  * 앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다. **실행 권한을 부여하지 마세요.**

* 데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * 서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.
  * 서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.

  자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요.

## <a name="file-upload-scenarios"></a>파일 업로드 시나리오

파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.

**버퍼링**

전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.

파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다. 앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다. 파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.

> [!NOTE]
> 버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.

소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.

* [물리적 스토리지](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [데이터베이스](#upload-small-files-with-buffered-model-binding-to-a-database)

**스트리밍**

파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다. 스트리밍은 성능을 크게 개선하지 않습니다. 스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.

대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드

소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.

다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.

* 양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.
* 유효성 검사를 수행하지 않습니다.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.

* Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.
* `XMLHttpRequest`을 사용하세요. 예를 들면 다음과 같습니다.

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.

`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다. 샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.

<a name="filename"></a>

> [!WARNING]
> 표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**. 표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다. 공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다. 애플리케이션에서 다음을 수행해야 합니다.
>
> * 사용자가 제공한 파일 이름에서 경로를 제거합니다.
> * UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.
> * 스토리지의 새 임의 파일 이름을 생성합니다.
>
> 다음 코드는 파일 이름에서 경로를 제거합니다.
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.

* 단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.
* 여러 파일을 나타내는 다음 컬렉션 중 하나.
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 바인딩은 이름을 기준으로 양식 파일을 일치시킵니다. 예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다. 자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.

다음 예제를 참조하세요.

* 하나 이상의 업로드된 파일을 반복합니다.
* [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다. 
* 앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.
* 업로드된 파일의 총 수와 크기를 반환합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다. 다음 예제에서는 경로를 구성에서 가져옵니다.

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다. 파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.

<xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다. 작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다. 로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.

업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.

> [!WARNING]
> 이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다. 65535개 파일 제한은 서버당 제한입니다. Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.
>
> * [GetTempFileNameA 함수](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드

[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다. 위 예제에서는 바인딩된 모델 속성을 사용합니다.

는 `FileUpload` Pages 폼에 사용 됩니다 Razor .

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다. 다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> 관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.
>
> 유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요. `FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.
>
> 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

### <a name="upload-large-files-with-streaming"></a>스트리밍을 사용하여 대용량 파일 업로드

다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다. 사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다. 작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다. 작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다. 다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.

초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` . 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함). 작업 메서드는 `Request` 속성으로 직접 작동합니다. `MultipartReader`는 각 섹션을 읽는 데 사용됩니다. 키/값 데이터는 `KeyValueAccumulator`에 저장됩니다. 다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.

EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.

## <a name="validation"></a>유효성 검사

샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다. 샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요. 스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.

> [!WARNING]
> 샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다. 대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.
>
> 항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.
>
> * 구현을 완전히 이해합니다.
> * 앱의 환경 및 사양에 맞게 구현을 수정합니다.
>
> **이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**

### <a name="content-validation"></a>콘텐츠 유효성 검사

**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**

대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다. 파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다. 일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다. 파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다. 이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다. 이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.

### <a name="file-extension-validation"></a>파일 확장명 유효성 검사

업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다. 예를 들면 다음과 같습니다.

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>파일 서명 유효성 검사

파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다. 이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다. 샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다. 다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.

### <a name="file-name-security"></a>파일 이름 보안

실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요. [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.

Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다. 다음 코드는 안전하게 사용할 수 있습니다.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.

많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다. 앱의 사양을 충족하는 추가 논리를 제공합니다.

### <a name="size-validation"></a>크기 유효성 검사

업로드된 파일의 크기를 제한합니다.

샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다. 이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

파일 크기가 제한을 초과하는 파일은 거부됩니다.

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>이름 특성 값을 POST 메서드의 매개 변수 이름과 일치

폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.

다음 예제에서는

* `<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.

* Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST 컨트롤러 작업 메서드:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>서버 및 앱 구성

### <a name="multipart-body-length-limit"></a>다중 파트 본문 길이 제한

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다. 양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다. 기본값은 134,217,728(128MB)입니다. `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 최대 요청 본문 크기

Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`지시문을 사용 하 여를 적용할 수도 있습니다 [`@attribute`](xref:mvc/views/razor#attribute) Razor .

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>기타 Kestrel 제한

다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.

* [최대 클라이언트 연결](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [요청 및 응답 데이터 속도](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다. 파일의 제한을 사용자 지정 합니다 `web.config` . 다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`설정은 IIS에만 적용 됩니다. 자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.

에서을 설정 하 여 HTTP 요청에 대 한 최대 요청 본문 크기를 늘립니다 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` . 다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

자세한 내용은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.

## <a name="troubleshoot"></a>문제 해결

다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>IIS 서버에 배포할 때 찾을 수 없음 오류

다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

자세한 내용은 [IIS](#iis) 섹션을 참조하세요.

### <a name="connection-failure"></a>연결 실패

연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다. 자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요. Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 사용 시 Null 참조 예외

컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다. `<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다. 또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.

### <a name="stream-was-too-long"></a>스트림이 너무 깁니다.

이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다. `MemoryStream`의 크기 제한은 `int.MaxValue`입니다. 앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>보안 고려 사항

사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다. 공격자는 다음을 시도할 수 있습니다.

* [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.
* 바이러스 또는 맬웨어를 업로드.
* 다른 방법으로 네트워크 및 서버를 손상.

공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.

* 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다. 전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다. 파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;
* 업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;
* 앱에 의해 결정된 안전한 파일 이름을 사용합니다. 사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다. 예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)
* 앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.
* 업로드된 파일의 크기를 확인합니다. 대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;
* 업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.
* **파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**

&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.

> [!WARNING]
> 시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.
>
> * 시스템을 완전히 제어합니다.
> * 시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.
> * 사용자 또는 시스템 데이터를 손상시킵니다.
> * 공용 UI에 그래피티를 적용합니다.
>
> 사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
>
> * [무제한 파일 업로드](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.

## <a name="storage-scenarios"></a>스토리지 시나리오

파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.

* 데이터베이스

  * 소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.
  * 사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.
  * 데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.

* 실제 스토리지(파일 시스템 또는 네트워크 공유)

  * 대용량 파일 업로드의 경우:
    * 데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.
    * 실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.
  * 실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.
  * 앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다. **실행 권한을 부여하지 마세요.**

* 데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * 서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.
  * 서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.

  자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요. 이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.

## <a name="file-upload-scenarios"></a>파일 업로드 시나리오

파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.

**버퍼링**

전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.

파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다. 앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다. 파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.

> [!NOTE]
> 버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.

소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.

* [물리적 스토리지](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [데이터베이스](#upload-small-files-with-buffered-model-binding-to-a-database)

**스트리밍**

파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다. 스트리밍은 성능을 크게 개선하지 않습니다. 스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.

대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드

소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.

다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.

* 양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.
* 유효성 검사를 수행하지 않습니다.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.

* Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.
* `XMLHttpRequest`을 사용하세요. 예를 들면 다음과 같습니다.

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.

`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다. 샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.

<a name="filename2"></a>

> [!WARNING]
> 표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**. 표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다. 공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다. 애플리케이션에서 다음을 수행해야 합니다.
>
> * 사용자가 제공한 파일 이름에서 경로를 제거합니다.
> * UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.
> * 스토리지의 새 임의 파일 이름을 생성합니다.
>
> 다음 코드는 파일 이름에서 경로를 제거합니다.
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.

* 단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.
* 여러 파일을 나타내는 다음 컬렉션 중 하나.
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 바인딩은 이름을 기준으로 양식 파일을 일치시킵니다. 예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다. 자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.

다음 예제를 참조하세요.

* 하나 이상의 업로드된 파일을 반복합니다.
* [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다. 
* 앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.
* 업로드된 파일의 총 수와 크기를 반환합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다. 다음 예제에서는 경로를 구성에서 가져옵니다.

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다. 파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.

<xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다. 작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다. 로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.

업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.

> [!WARNING]
> 이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다. 65535개 파일 제한은 서버당 제한입니다. Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.
>
> * [GetTempFileNameA 함수](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드

[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다. 위 예제에서는 바인딩된 모델 속성을 사용합니다.

는 `FileUpload` Pages 폼에 사용 됩니다 Razor .

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다. 다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> 관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.
>
> 유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요. `FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.
>
> 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다. 추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.
>
> * [보안 고려 사항](#security-considerations)
> * [유효성 검사](#validation)

### <a name="upload-large-files-with-streaming"></a>스트리밍을 사용하여 대용량 파일 업로드

다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다. 사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다. 작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다. 작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다. 다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.

초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` . 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함). 작업 메서드는 `Request` 속성으로 직접 작동합니다. `MultipartReader`는 각 섹션을 읽는 데 사용됩니다. 키/값 데이터는 `KeyValueAccumulator`에 저장됩니다. 다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.

EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.

## <a name="validation"></a>유효성 검사

샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다. 샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요. 스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.

> [!WARNING]
> 샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다. 대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.
>
> 항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.
>
> * 구현을 완전히 이해합니다.
> * 앱의 환경 및 사양에 맞게 구현을 수정합니다.
>
> **이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**

### <a name="content-validation"></a>콘텐츠 유효성 검사

**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**

대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다. 파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다. 일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다. 파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다. 이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다. 이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.

### <a name="file-extension-validation"></a>파일 확장명 유효성 검사

업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다. 예를 들면 다음과 같습니다.

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>파일 서명 유효성 검사

파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다. 이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다. 샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다. 다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.

### <a name="file-name-security"></a>파일 이름 보안

실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요. [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.

Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다. 다음 코드는 안전하게 사용할 수 있습니다.

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.

많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다. 앱의 사양을 충족하는 추가 논리를 제공합니다.

### <a name="size-validation"></a>크기 유효성 검사

업로드된 파일의 크기를 제한합니다.

샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다. 이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

파일 크기가 제한을 초과하는 파일은 거부됩니다.

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>이름 특성 값을 POST 메서드의 매개 변수 이름과 일치

폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.

다음 예제에서는

* `<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.

* Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST 컨트롤러 작업 메서드:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>서버 및 앱 구성

### <a name="multipart-body-length-limit"></a>다중 파트 본문 길이 제한

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다. 양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다. 기본값은 134,217,728(128MB)입니다. `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 최대 요청 본문 크기

Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.

Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>기타 Kestrel 제한

다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.

* [최대 클라이언트 연결](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [요청 및 응답 데이터 속도](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다. 파일의 제한을 사용자 지정 합니다 `web.config` . 다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`설정은 IIS에만 적용 됩니다. 자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.

에서을 설정 하 여 HTTP 요청에 대 한 최대 요청 본문 크기를 늘립니다 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` . 다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

자세한 내용은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.

## <a name="troubleshoot"></a>문제 해결

다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>IIS 서버에 배포할 때 찾을 수 없음 오류

다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

자세한 내용은 [IIS](#iis) 섹션을 참조하세요.

### <a name="connection-failure"></a>연결 실패

연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다. 자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요. Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 사용 시 Null 참조 예외

컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다. `<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다. 또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.

### <a name="stream-was-too-long"></a>스트림이 너무 깁니다.

이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다. `MemoryStream`의 크기 제한은 `int.MaxValue`입니다. 앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.

::: moniker-end


## <a name="additional-resources"></a>추가 리소스

::: moniker range="< aspnetcore-5.0"
* [HTTP 연결 요청 드레이닝](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [HTTP 연결 요청 드레이닝](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [무제한 파일 업로드](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Azure 보안: 보안 프레임: 입력 유효성 검사 | 조치](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure 클라우드 디자인 패턴: Valet 키 패턴](/azure/architecture/patterns/valet-key)
