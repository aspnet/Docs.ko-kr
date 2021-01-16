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
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="7cbe0-103">ASP.NET Core에서 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="7cbe0-104">작성자, [Steve Smith](https://ardalis.com/) 및 [Rutger 스톰](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7cbe0-105">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="7cbe0-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7cbe0-107">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-107">Security considerations</span></span>

<span data-ttu-id="7cbe0-108">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="7cbe0-109">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="7cbe0-110">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="7cbe0-111">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="7cbe0-112">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="7cbe0-113">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="7cbe0-114">전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="7cbe0-115">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="7cbe0-116">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="7cbe0-117">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="7cbe0-118">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="7cbe0-119">사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="7cbe0-120">예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="7cbe0-121">앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="7cbe0-122">서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="7cbe0-123">업로드된 파일의 크기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="7cbe0-124">대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="7cbe0-125">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="7cbe0-126">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="7cbe0-127">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-128">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="7cbe0-129">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="7cbe0-130">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="7cbe0-131">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="7cbe0-132">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="7cbe0-133">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="7cbe0-134">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="7cbe0-135">Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인</span><span class="sxs-lookup"><span data-stu-id="7cbe0-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="7cbe0-136">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="7cbe0-137">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-137">Storage scenarios</span></span>

<span data-ttu-id="7cbe0-138">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-138">Common storage options for files include:</span></span>

* <span data-ttu-id="7cbe0-139">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-139">Database</span></span>

  * <span data-ttu-id="7cbe0-140">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="7cbe0-141">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="7cbe0-142">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="7cbe0-143">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="7cbe0-144">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-144">For large file uploads:</span></span>
    * <span data-ttu-id="7cbe0-145">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="7cbe0-146">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="7cbe0-147">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="7cbe0-148">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="7cbe0-149">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="7cbe0-150">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="7cbe0-151">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="7cbe0-152">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="7cbe0-153">자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="7cbe0-154">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-154">File upload scenarios</span></span>

<span data-ttu-id="7cbe0-155">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="7cbe0-156">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-156">**Buffering**</span></span>

<span data-ttu-id="7cbe0-157">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="7cbe0-158">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="7cbe0-159">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="7cbe0-160">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-161">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="7cbe0-162">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="7cbe0-163">물리적 스토리지</span><span class="sxs-lookup"><span data-stu-id="7cbe0-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="7cbe0-164">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="7cbe0-165">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-165">**Streaming**</span></span>

<span data-ttu-id="7cbe0-166">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="7cbe0-167">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="7cbe0-168">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="7cbe0-169">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="7cbe0-170">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="7cbe0-171">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="7cbe0-172">다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="7cbe0-173">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="7cbe0-174">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="7cbe0-175">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-175">There's no validation.</span></span>

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

<span data-ttu-id="7cbe0-176">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="7cbe0-177">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="7cbe0-178">`XMLHttpRequest`을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="7cbe0-179">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-179">For example:</span></span>

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

<span data-ttu-id="7cbe0-180">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="7cbe0-181">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="7cbe0-182">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="7cbe0-183">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="7cbe0-184">표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="7cbe0-185">표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="7cbe0-186">공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="7cbe0-187">애플리케이션에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-187">Applications should:</span></span>
>
> * <span data-ttu-id="7cbe0-188">사용자가 제공한 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="7cbe0-189">UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="7cbe0-190">스토리지의 새 임의 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="7cbe0-191">다음 코드는 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="7cbe0-192">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-193">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-194">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-195">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-195">Validation</span></span>](#validation)

<span data-ttu-id="7cbe0-196">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="7cbe0-197">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="7cbe0-198">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="7cbe0-199">[은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="7cbe0-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-200">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-200">Binding matches form files by name.</span></span> <span data-ttu-id="7cbe0-201">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="7cbe0-202">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="7cbe0-203">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-203">The following example:</span></span>

* <span data-ttu-id="7cbe0-204">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="7cbe0-205">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="7cbe0-206">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="7cbe0-207">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="7cbe0-208">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="7cbe0-209">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="7cbe0-210">에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="7cbe0-211">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="7cbe0-212"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="7cbe0-213">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="7cbe0-214">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="7cbe0-215">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-216">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7cbe0-217">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="7cbe0-218">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="7cbe0-219">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="7cbe0-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="7cbe0-220">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="7cbe0-221">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="7cbe0-222"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="7cbe0-223"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="7cbe0-224">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="7cbe0-225">는 `FileUpload` Pages 폼에 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="7cbe0-226">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="7cbe0-227">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="7cbe0-228">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="7cbe0-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="7cbe0-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-231">관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="7cbe0-232">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="7cbe0-233">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="7cbe0-234">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-235">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-236">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-237">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="7cbe0-238">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-238">Upload large files with streaming</span></span>

<span data-ttu-id="7cbe0-239">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="7cbe0-240">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="7cbe0-241">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="7cbe0-242">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="7cbe0-243">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="7cbe0-244">초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="7cbe0-245">특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="7cbe0-246">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="7cbe0-247">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="7cbe0-248">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="7cbe0-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="7cbe0-249">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="7cbe0-250">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="7cbe0-251">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="7cbe0-252">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="7cbe0-253">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="7cbe0-254">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="7cbe0-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="7cbe0-255">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="7cbe0-256">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="7cbe0-257">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-257">Validation</span></span>

<span data-ttu-id="7cbe0-258">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="7cbe0-259">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="7cbe0-260">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-261">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="7cbe0-262">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="7cbe0-263">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="7cbe0-264">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="7cbe0-265">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="7cbe0-266">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="7cbe0-267">콘텐츠 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-267">Content validation</span></span>

<span data-ttu-id="7cbe0-268">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="7cbe0-269">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="7cbe0-270">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="7cbe0-271">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="7cbe0-272">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="7cbe0-273">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="7cbe0-274">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="7cbe0-275">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-275">File extension validation</span></span>

<span data-ttu-id="7cbe0-276">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="7cbe0-277">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="7cbe0-278">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-278">File signature validation</span></span>

<span data-ttu-id="7cbe0-279">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="7cbe0-280">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="7cbe0-281">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="7cbe0-282">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="7cbe0-283">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="7cbe0-284">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="7cbe0-284">File name security</span></span>

<span data-ttu-id="7cbe0-285">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="7cbe0-286">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="7cbe0-287">Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="7cbe0-288">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="7cbe0-289">외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="7cbe0-290">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="7cbe0-291">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="7cbe0-292">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-292">Size validation</span></span>

<span data-ttu-id="7cbe0-293">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="7cbe0-294">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="7cbe0-295">이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="7cbe0-296">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="7cbe0-297">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="7cbe0-298">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="7cbe0-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="7cbe0-299">폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="7cbe0-300">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7cbe0-300">In the following example:</span></span>

* <span data-ttu-id="7cbe0-301">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="7cbe0-302">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="7cbe0-303">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="7cbe0-304">Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="7cbe0-305">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="7cbe0-306">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7cbe0-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="7cbe0-307">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-307">Multipart body length limit</span></span>

<span data-ttu-id="7cbe0-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="7cbe0-309">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="7cbe0-310">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="7cbe0-311">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="7cbe0-313">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-314">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="7cbe0-315">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="7cbe0-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="7cbe0-316">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-317">[MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="7cbe0-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="7cbe0-319">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-320">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="7cbe0-321">`RequestSizeLimitAttribute`지시문을 사용 하 여를 적용할 수도 있습니다 [`@attribute`](xref:mvc/views/razor#attribute) Razor .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="7cbe0-322">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-322">Other Kestrel limits</span></span>

<span data-ttu-id="7cbe0-323">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="7cbe0-324">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [<span data-ttu-id="7cbe0-325">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="7cbe0-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="7cbe0-326">IIS</span><span class="sxs-lookup"><span data-stu-id="7cbe0-326">IIS</span></span>

<span data-ttu-id="7cbe0-327">기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-328">파일의 제한을 사용자 지정 합니다 `web.config` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="7cbe0-329">다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="7cbe0-330">`maxAllowedContentLength`설정은 IIS에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="7cbe0-331">자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7cbe0-332">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-332">Troubleshoot</span></span>

<span data-ttu-id="7cbe0-333">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="7cbe0-334">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="7cbe0-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="7cbe0-335">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="7cbe0-336">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="7cbe0-337">연결 실패</span><span class="sxs-lookup"><span data-stu-id="7cbe0-337">Connection failure</span></span>

<span data-ttu-id="7cbe0-338">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="7cbe0-339">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="7cbe0-340">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="7cbe0-341">IFormFile 사용 시 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="7cbe0-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="7cbe0-342">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="7cbe0-343">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="7cbe0-344">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="7cbe0-345">스트림이 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-345">Stream was too long</span></span>

<span data-ttu-id="7cbe0-346">이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="7cbe0-347">`MemoryStream`의 크기 제한은 `int.MaxValue`입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="7cbe0-348">앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7cbe0-349">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="7cbe0-350">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7cbe0-351">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-351">Security considerations</span></span>

<span data-ttu-id="7cbe0-352">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="7cbe0-353">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="7cbe0-354">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="7cbe0-355">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="7cbe0-356">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="7cbe0-357">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="7cbe0-358">전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="7cbe0-359">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="7cbe0-360">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="7cbe0-361">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="7cbe0-362">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="7cbe0-363">사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="7cbe0-364">예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="7cbe0-365">앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="7cbe0-366">서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="7cbe0-367">업로드된 파일의 크기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="7cbe0-368">대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="7cbe0-369">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="7cbe0-370">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="7cbe0-371">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-372">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="7cbe0-373">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="7cbe0-374">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="7cbe0-375">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="7cbe0-376">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="7cbe0-377">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="7cbe0-378">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="7cbe0-379">Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인</span><span class="sxs-lookup"><span data-stu-id="7cbe0-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="7cbe0-380">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="7cbe0-381">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-381">Storage scenarios</span></span>

<span data-ttu-id="7cbe0-382">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-382">Common storage options for files include:</span></span>

* <span data-ttu-id="7cbe0-383">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-383">Database</span></span>

  * <span data-ttu-id="7cbe0-384">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="7cbe0-385">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="7cbe0-386">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="7cbe0-387">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="7cbe0-388">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-388">For large file uploads:</span></span>
    * <span data-ttu-id="7cbe0-389">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="7cbe0-390">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="7cbe0-391">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="7cbe0-392">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="7cbe0-393">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="7cbe0-394">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="7cbe0-395">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="7cbe0-396">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="7cbe0-397">자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="7cbe0-398">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-398">File upload scenarios</span></span>

<span data-ttu-id="7cbe0-399">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="7cbe0-400">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-400">**Buffering**</span></span>

<span data-ttu-id="7cbe0-401">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="7cbe0-402">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="7cbe0-403">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="7cbe0-404">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-405">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="7cbe0-406">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="7cbe0-407">물리적 스토리지</span><span class="sxs-lookup"><span data-stu-id="7cbe0-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="7cbe0-408">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="7cbe0-409">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-409">**Streaming**</span></span>

<span data-ttu-id="7cbe0-410">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="7cbe0-411">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="7cbe0-412">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="7cbe0-413">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="7cbe0-414">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="7cbe0-415">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="7cbe0-416">다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="7cbe0-417">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="7cbe0-418">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="7cbe0-419">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-419">There's no validation.</span></span>

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

<span data-ttu-id="7cbe0-420">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="7cbe0-421">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="7cbe0-422">`XMLHttpRequest`을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="7cbe0-423">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-423">For example:</span></span>

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

<span data-ttu-id="7cbe0-424">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="7cbe0-425">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="7cbe0-426">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="7cbe0-427">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="7cbe0-428">표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="7cbe0-429">표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="7cbe0-430">공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="7cbe0-431">애플리케이션에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-431">Applications should:</span></span>
>
> * <span data-ttu-id="7cbe0-432">사용자가 제공한 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="7cbe0-433">UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="7cbe0-434">스토리지의 새 임의 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="7cbe0-435">다음 코드는 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="7cbe0-436">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-437">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-438">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-439">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-439">Validation</span></span>](#validation)

<span data-ttu-id="7cbe0-440">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="7cbe0-441">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="7cbe0-442">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="7cbe0-443">[은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="7cbe0-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-444">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-444">Binding matches form files by name.</span></span> <span data-ttu-id="7cbe0-445">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="7cbe0-446">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="7cbe0-447">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-447">The following example:</span></span>

* <span data-ttu-id="7cbe0-448">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="7cbe0-449">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="7cbe0-450">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="7cbe0-451">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="7cbe0-452">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="7cbe0-453">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="7cbe0-454">에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="7cbe0-455">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="7cbe0-456"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="7cbe0-457">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="7cbe0-458">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="7cbe0-459">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-460">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7cbe0-461">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="7cbe0-462">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="7cbe0-463">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="7cbe0-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="7cbe0-464">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="7cbe0-465">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="7cbe0-466"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="7cbe0-467"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="7cbe0-468">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="7cbe0-469">는 `FileUpload` Pages 폼에 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="7cbe0-470">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="7cbe0-471">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="7cbe0-472">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="7cbe0-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="7cbe0-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-475">관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="7cbe0-476">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="7cbe0-477">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="7cbe0-478">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-479">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-480">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-481">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="7cbe0-482">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-482">Upload large files with streaming</span></span>

<span data-ttu-id="7cbe0-483">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="7cbe0-484">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="7cbe0-485">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="7cbe0-486">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="7cbe0-487">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="7cbe0-488">초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="7cbe0-489">특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="7cbe0-490">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="7cbe0-491">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="7cbe0-492">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="7cbe0-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="7cbe0-493">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="7cbe0-494">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="7cbe0-495">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="7cbe0-496">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="7cbe0-497">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="7cbe0-498">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="7cbe0-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="7cbe0-499">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="7cbe0-500">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="7cbe0-501">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-501">Validation</span></span>

<span data-ttu-id="7cbe0-502">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="7cbe0-503">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="7cbe0-504">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-505">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="7cbe0-506">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="7cbe0-507">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="7cbe0-508">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="7cbe0-509">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="7cbe0-510">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="7cbe0-511">콘텐츠 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-511">Content validation</span></span>

<span data-ttu-id="7cbe0-512">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="7cbe0-513">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="7cbe0-514">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="7cbe0-515">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="7cbe0-516">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="7cbe0-517">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="7cbe0-518">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="7cbe0-519">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-519">File extension validation</span></span>

<span data-ttu-id="7cbe0-520">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="7cbe0-521">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="7cbe0-522">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-522">File signature validation</span></span>

<span data-ttu-id="7cbe0-523">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="7cbe0-524">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="7cbe0-525">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="7cbe0-526">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="7cbe0-527">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="7cbe0-528">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="7cbe0-528">File name security</span></span>

<span data-ttu-id="7cbe0-529">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="7cbe0-530">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="7cbe0-531">Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="7cbe0-532">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="7cbe0-533">외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="7cbe0-534">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="7cbe0-535">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="7cbe0-536">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-536">Size validation</span></span>

<span data-ttu-id="7cbe0-537">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="7cbe0-538">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="7cbe0-539">이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="7cbe0-540">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="7cbe0-541">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="7cbe0-542">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="7cbe0-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="7cbe0-543">폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="7cbe0-544">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7cbe0-544">In the following example:</span></span>

* <span data-ttu-id="7cbe0-545">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="7cbe0-546">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="7cbe0-547">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="7cbe0-548">Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="7cbe0-549">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="7cbe0-550">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7cbe0-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="7cbe0-551">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-551">Multipart body length limit</span></span>

<span data-ttu-id="7cbe0-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="7cbe0-553">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="7cbe0-554">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="7cbe0-555">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="7cbe0-557">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-558">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="7cbe0-559">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="7cbe0-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="7cbe0-560">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-561">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="7cbe0-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="7cbe0-563">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-564">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="7cbe0-565">`RequestSizeLimitAttribute`지시문을 사용 하 여를 적용할 수도 있습니다 [`@attribute`](xref:mvc/views/razor#attribute) Razor .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="7cbe0-566">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-566">Other Kestrel limits</span></span>

<span data-ttu-id="7cbe0-567">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="7cbe0-568">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="7cbe0-569">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="7cbe0-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="7cbe0-570">IIS</span><span class="sxs-lookup"><span data-stu-id="7cbe0-570">IIS</span></span>

<span data-ttu-id="7cbe0-571">기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-572">파일의 제한을 사용자 지정 합니다 `web.config` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="7cbe0-573">다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="7cbe0-574">`maxAllowedContentLength`설정은 IIS에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="7cbe0-575">자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="7cbe0-576">에서을 설정 하 여 HTTP 요청에 대 한 최대 요청 본문 크기를 늘립니다 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7cbe0-577">다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="7cbe0-578">자세한 내용은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7cbe0-579">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-579">Troubleshoot</span></span>

<span data-ttu-id="7cbe0-580">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="7cbe0-581">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="7cbe0-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="7cbe0-582">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="7cbe0-583">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="7cbe0-584">연결 실패</span><span class="sxs-lookup"><span data-stu-id="7cbe0-584">Connection failure</span></span>

<span data-ttu-id="7cbe0-585">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="7cbe0-586">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="7cbe0-587">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="7cbe0-588">IFormFile 사용 시 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="7cbe0-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="7cbe0-589">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="7cbe0-590">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="7cbe0-591">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="7cbe0-592">스트림이 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-592">Stream was too long</span></span>

<span data-ttu-id="7cbe0-593">이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="7cbe0-594">`MemoryStream`의 크기 제한은 `int.MaxValue`입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="7cbe0-595">앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7cbe0-596">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="7cbe0-597">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7cbe0-598">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-598">Security considerations</span></span>

<span data-ttu-id="7cbe0-599">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="7cbe0-600">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="7cbe0-601">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="7cbe0-602">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="7cbe0-603">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="7cbe0-604">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="7cbe0-605">전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="7cbe0-606">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="7cbe0-607">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="7cbe0-608">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="7cbe0-609">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="7cbe0-610">사용자가 제공한 파일 이름 또는 업로드 된 파일의 신뢰할 수 없는 파일 이름을 사용 하지 마세요. &dagger; HTML을 표시 하는 경우 신뢰할 수 없는 파일 이름을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="7cbe0-611">예를 들어 파일 이름을 기록 하거나 UI에 표시 하는 경우 ( Razor 자동으로 HTML 인코딩 출력)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="7cbe0-612">앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="7cbe0-613">서버에서 클라이언트 쪽 검사가 수행 되는지 확인 합니다. &dagger; 클라이언트 쪽 검사는 쉽게 피할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="7cbe0-614">업로드된 파일의 크기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="7cbe0-615">대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7cbe0-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="7cbe0-616">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="7cbe0-617">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="7cbe0-618">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-619">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="7cbe0-620">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="7cbe0-621">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="7cbe0-622">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="7cbe0-623">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="7cbe0-624">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="7cbe0-625">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="7cbe0-626">Azure 보안: 사용자의 파일을 수락할 때 적절한 제어 장치가 있는지 확인</span><span class="sxs-lookup"><span data-stu-id="7cbe0-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="7cbe0-627">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="7cbe0-628">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-628">Storage scenarios</span></span>

<span data-ttu-id="7cbe0-629">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-629">Common storage options for files include:</span></span>

* <span data-ttu-id="7cbe0-630">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-630">Database</span></span>

  * <span data-ttu-id="7cbe0-631">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="7cbe0-632">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="7cbe0-633">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="7cbe0-634">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="7cbe0-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="7cbe0-635">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-635">For large file uploads:</span></span>
    * <span data-ttu-id="7cbe0-636">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="7cbe0-637">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="7cbe0-638">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="7cbe0-639">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="7cbe0-640">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="7cbe0-641">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="7cbe0-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="7cbe0-642">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="7cbe0-643">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="7cbe0-644">자세한 내용은 [빠른 시작: .net을 사용 하 여 개체 저장소에 blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="7cbe0-645">이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="7cbe0-646">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="7cbe0-646">File upload scenarios</span></span>

<span data-ttu-id="7cbe0-647">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="7cbe0-648">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-648">**Buffering**</span></span>

<span data-ttu-id="7cbe0-649">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="7cbe0-650">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="7cbe0-651">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="7cbe0-652">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-653">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="7cbe0-654">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="7cbe0-655">물리적 스토리지</span><span class="sxs-lookup"><span data-stu-id="7cbe0-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="7cbe0-656">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="7cbe0-657">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-657">**Streaming**</span></span>

<span data-ttu-id="7cbe0-658">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="7cbe0-659">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="7cbe0-660">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="7cbe0-661">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="7cbe0-662">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="7cbe0-663">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="7cbe0-664">다음 예제에서는 Razor pages 폼을 사용 하 여 단일 파일 (샘플 응용 프로그램의 *Pages/BufferedSingleFileUploadPhysical* )을 업로드 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="7cbe0-665">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="7cbe0-666">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="7cbe0-667">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-667">There's no validation.</span></span>

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

<span data-ttu-id="7cbe0-668">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="7cbe0-669">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="7cbe0-670">`XMLHttpRequest`을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="7cbe0-671">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-671">For example:</span></span>

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

<span data-ttu-id="7cbe0-672">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="7cbe0-673">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="7cbe0-674">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="7cbe0-675">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="7cbe0-676">표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="7cbe0-677">표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="7cbe0-678">공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="7cbe0-679">애플리케이션에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-679">Applications should:</span></span>
>
> * <span data-ttu-id="7cbe0-680">사용자가 제공한 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="7cbe0-681">UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="7cbe0-682">스토리지의 새 임의 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="7cbe0-683">다음 코드는 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="7cbe0-684">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-685">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-686">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-687">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-687">Validation</span></span>](#validation)

<span data-ttu-id="7cbe0-688">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="7cbe0-689">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="7cbe0-690">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="7cbe0-691">[은](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="7cbe0-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="7cbe0-692">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-692">Binding matches form files by name.</span></span> <span data-ttu-id="7cbe0-693">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="7cbe0-694">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="7cbe0-695">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-695">The following example:</span></span>

* <span data-ttu-id="7cbe0-696">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="7cbe0-697">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="7cbe0-698">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="7cbe0-699">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="7cbe0-700">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="7cbe0-701">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="7cbe0-702">에 전달된 경로는 <xref:System.IO.FileStream> *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="7cbe0-703">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="7cbe0-704"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="7cbe0-705">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="7cbe0-706">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="7cbe0-707">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-708">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7cbe0-709">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="7cbe0-710">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="7cbe0-711">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="7cbe0-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="7cbe0-712">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="7cbe0-713">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="7cbe0-714"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="7cbe0-715"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="7cbe0-716">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="7cbe0-717">는 `FileUpload` Pages 폼에 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="7cbe0-718">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="7cbe0-719">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="7cbe0-720">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="7cbe0-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="7cbe0-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7cbe0-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-723">관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="7cbe0-724">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="7cbe0-725">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="7cbe0-726">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="7cbe0-727">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7cbe0-728">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7cbe0-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7cbe0-729">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="7cbe0-730">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-730">Upload large files with streaming</span></span>

<span data-ttu-id="7cbe0-731">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="7cbe0-732">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="7cbe0-733">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="7cbe0-734">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="7cbe0-735">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="7cbe0-736">초기 페이지 응답은 양식을 로드 하 고 특성을 통해의 위조 방지 토큰을에 저장 합니다 cookie `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="7cbe0-737">특성은 ASP.NET Core의 기본 제공 [위조 방지 지원 기능](xref:security/anti-request-forgery) 을 사용 하 여 cookie 요청 토큰으로를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="7cbe0-738">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="7cbe0-739">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및는 페이지 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 규칙](xref:razor-pages/razor-pages-conventions)을 사용 하 여 및의 페이지 응용 프로그램 모델에 필터로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="7cbe0-740">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="7cbe0-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="7cbe0-741">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="7cbe0-742">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="7cbe0-743">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="7cbe0-744">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="7cbe0-745">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="7cbe0-746">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="7cbe0-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="7cbe0-747">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="7cbe0-748">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="7cbe0-749">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-749">Validation</span></span>

<span data-ttu-id="7cbe0-750">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="7cbe0-751">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="7cbe0-752">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="7cbe0-753">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="7cbe0-754">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="7cbe0-755">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="7cbe0-756">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="7cbe0-757">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="7cbe0-758">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="7cbe0-759">콘텐츠 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-759">Content validation</span></span>

<span data-ttu-id="7cbe0-760">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="7cbe0-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="7cbe0-761">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="7cbe0-762">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="7cbe0-763">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="7cbe0-764">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="7cbe0-765">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="7cbe0-766">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="7cbe0-767">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-767">File extension validation</span></span>

<span data-ttu-id="7cbe0-768">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="7cbe0-769">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="7cbe0-770">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-770">File signature validation</span></span>

<span data-ttu-id="7cbe0-771">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="7cbe0-772">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="7cbe0-773">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="7cbe0-774">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="7cbe0-775">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="7cbe0-776">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="7cbe0-776">File name security</span></span>

<span data-ttu-id="7cbe0-777">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="7cbe0-778">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="7cbe0-779">Razor 자동으로 표시 하기 위해 속성 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="7cbe0-780">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="7cbe0-781">외부 Razor 에서는 항상 <xref:System.Net.WebUtility.HtmlEncode*> 사용자 요청의 파일 이름 콘텐츠를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="7cbe0-782">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="7cbe0-783">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="7cbe0-784">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7cbe0-784">Size validation</span></span>

<span data-ttu-id="7cbe0-785">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="7cbe0-786">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="7cbe0-787">이 제한은 파일의 [구성을](xref:fundamentals/configuration/index) 통해 제공 됩니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="7cbe0-788">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="7cbe0-789">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="7cbe0-790">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="7cbe0-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="7cbe0-791">폼 데이터를 Razor 게시 하거나 JavaScript를 직접 사용 하는 형식이 아닌 `FormData` 경우 폼의 요소에 지정 된 이름이 나 `FormData` 컨트롤러 작업의 매개 변수 이름과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="7cbe0-792">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7cbe0-792">In the following example:</span></span>

* <span data-ttu-id="7cbe0-793">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="7cbe0-794">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="7cbe0-795">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="7cbe0-796">Razor페이지 페이지 처리기 메서드를 `Upload` 다음과 같이 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="7cbe0-797">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="7cbe0-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="7cbe0-798">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7cbe0-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="7cbe0-799">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-799">Multipart body length limit</span></span>

<span data-ttu-id="7cbe0-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="7cbe0-801">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="7cbe0-802">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="7cbe0-803">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="7cbe0-805">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-806">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 모델 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="7cbe0-807">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="7cbe0-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="7cbe0-808">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-809">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="7cbe0-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="7cbe0-811">Razor페이지 앱에서 다음과 같은 [규칙](xref:razor-pages/razor-pages-conventions) 을 사용 하 여 필터를 적용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7cbe0-812">페이지 Razor 앱 또는 MVC 앱에서 필터를 페이지 처리기 클래스 또는 작업 메서드에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="7cbe0-813">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="7cbe0-813">Other Kestrel limits</span></span>

<span data-ttu-id="7cbe0-814">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="7cbe0-815">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="7cbe0-816">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="7cbe0-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="7cbe0-817">IIS</span><span class="sxs-lookup"><span data-stu-id="7cbe0-817">IIS</span></span>

<span data-ttu-id="7cbe0-818">기본 요청 제한 ( `maxAllowedContentLength` )은 3000만 바이트 이며 약 28.6 MB입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7cbe0-819">파일의 제한을 사용자 지정 합니다 `web.config` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="7cbe0-820">다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="7cbe0-821">`maxAllowedContentLength`설정은 IIS에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="7cbe0-822">자세한 내용은 [요청 제한 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="7cbe0-823">에서을 설정 하 여 HTTP 요청에 대 한 최대 요청 본문 크기를 늘립니다 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7cbe0-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7cbe0-824">다음 예제에서 제한은 50 (52428800 바이트)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="7cbe0-825">자세한 내용은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7cbe0-826">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7cbe0-826">Troubleshoot</span></span>

<span data-ttu-id="7cbe0-827">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="7cbe0-828">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="7cbe0-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="7cbe0-829">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="7cbe0-830">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="7cbe0-831">연결 실패</span><span class="sxs-lookup"><span data-stu-id="7cbe0-831">Connection failure</span></span>

<span data-ttu-id="7cbe0-832">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="7cbe0-833">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="7cbe0-834">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="7cbe0-835">IFormFile 사용 시 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="7cbe0-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="7cbe0-836">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="7cbe0-837">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="7cbe0-838">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="7cbe0-839">스트림이 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-839">Stream was too long</span></span>

<span data-ttu-id="7cbe0-840">이 항목의 예제에서는 업로드된 파일의 콘텐츠를 저장하는 데 <xref:System.IO.MemoryStream>에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="7cbe0-841">`MemoryStream`의 크기 제한은 `int.MaxValue`입니다.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="7cbe0-842">앱의 파일 업로드 시나리오에서 50MB보다 큰 파일 콘텐츠를 보관해야 하는 경우 업로드된 파일의 콘텐츠를 보관할 때 단일 `MemoryStream`에 의존하지 않는 대체 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7cbe0-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="7cbe0-843">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7cbe0-843">Additional resources</span></span>

::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="7cbe0-844">HTTP 연결 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="7cbe0-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="7cbe0-845">HTTP 연결 요청 드레이닝</span><span class="sxs-lookup"><span data-stu-id="7cbe0-845">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [<span data-ttu-id="7cbe0-846">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7cbe0-846">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="7cbe0-847">Azure 보안: 보안 프레임: 입력 유효성 검사 | 조치</span><span class="sxs-lookup"><span data-stu-id="7cbe0-847">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="7cbe0-848">Azure 클라우드 디자인 패턴: Valet 키 패턴</span><span class="sxs-lookup"><span data-stu-id="7cbe0-848">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
