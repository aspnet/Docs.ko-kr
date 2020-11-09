---
title: ASP.NET Core에서 .NET Core gRPC 클라이언트 및 서버 만들기
author: juntaoluo
description: 이 자습서는 ASP.NET Core에서 gRPC 서비스 및 gRPC 클라이언트를 만드는 방법을 보여줍니다. gRPC 서비스 프로젝트를 만들고, proto 파일을 편집하고, 이중 스트리밍 호출을 추가하는 방법을 알아봅니다.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: fbfd37b8f796990ff035f7fffeb906e23a8739d4
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678580"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="475d5-104">자습서: ASP.NET Core에서 gRPC 클라이언트 및 서버 만들기</span><span class="sxs-lookup"><span data-stu-id="475d5-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="475d5-105">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="475d5-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="475d5-106">이 자습서에서는 .NET Core [gRPC](https://grpc.io/docs/guides/) 클라이언트와 ASP.NET Core gRPC 서버를 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="475d5-107">자습서를 마치고 나면 gRPC Greeter 서비스와 통신하는 gRPC 클라이언트를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="475d5-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="475d5-109">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="475d5-110">gRPC 서버를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="475d5-111">gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="475d5-112">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 서비스를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="475d5-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="475d5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="475d5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="475d5-117">[Mac용 Visual Studio 버전 8.7 이상](/visualstudio/releasenotes/vs2019-mac-relnotes).</span><span class="sxs-lookup"><span data-stu-id="475d5-117">[Visual Studio for Mac version 8.7 or later](/visualstudio/releasenotes/vs2019-mac-relnotes)</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="475d5-118">gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="475d5-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="475d5-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="475d5-120">Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-120">Start Visual Studio and select **Create a new project** .</span></span> <span data-ttu-id="475d5-121">또는 Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="475d5-122">**새 프로젝트 만들기** 대화 상자에서 **gRPC 서비스** 를 선택한 후 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![새 프로젝트 만들기 대화 상자](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="475d5-124">프로젝트 이름을 **GrpcGreeter** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-124">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="475d5-125">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter* 로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-125">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="475d5-126">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-126">Select **Create** .</span></span>
* <span data-ttu-id="475d5-127">**새 gRPC 서비스 만들기** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="475d5-128">**gRPC 서비스** 템플릿이 선택되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="475d5-129">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-129">Select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="475d5-131">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="475d5-132">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-132">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="475d5-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="475d5-134">`dotnet new` 명령은 *GrpcGreeter* 폴더에 새 gRPC 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="475d5-135">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *GrpcGreeter* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="475d5-136">다음과 같은 대화 상자가 표시됩니다. **Required assets to build and debug are missing from 'GrpcGreeter'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="475d5-137">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-137">Select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-138">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="475d5-139">Mac용 Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-139">Start Visual Studio for Mac and select **Create a new project** .</span></span> <span data-ttu-id="475d5-140">또는 Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="475d5-141">**새 프로젝트 만들기** 대화 상자에서 **웹 및 콘솔** > **앱** > **gRPC 서비스** 를 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![새 프로젝트 만들기 대화 상자](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="475d5-143">대상 프레임워크에 대해 **.NET Core 3.1** 을 선택하고 **다음** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-143">Select **.NET Core 3.1** for the target framework and click **Next**</span></span>
* <span data-ttu-id="475d5-144">프로젝트 이름을 **GrpcGreeter** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-144">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="475d5-145">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *GrpcGreeter* 로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-145">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="475d5-146">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-146">Select **Create** .</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="475d5-147">서비스 실행</span><span class="sxs-lookup"><span data-stu-id="475d5-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="475d5-148">로그는 `https://localhost:5001`에서 서비스가 수신 대기 중임을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="475d5-149">gRPC 템플릿은 [TLS(전송 계층 보안)](https://tools.ietf.org/html/rfc5246)를 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="475d5-150">gRPC 클라이언트는 HTTPS를 사용하여 서버를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="475d5-151">macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="475d5-152">macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="475d5-153">자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="475d5-154">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="475d5-154">Examine the project files</span></span>

<span data-ttu-id="475d5-155">*GrpcGreeter* 프로젝트 파일:</span><span class="sxs-lookup"><span data-stu-id="475d5-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="475d5-156">*greet.proto* : *Protos/greet.proto* 파일은 `Greeter` gRPC를 정의하고 gRPC 서버 자산을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="475d5-157">자세한 내용은 [gRPC 소개](xref:grpc/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="475d5-158">*Services* 폴더: `Greeter` 서비스의 구현을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="475d5-159">*appSettings.json* : Kestrel에서 사용하는 프로토콜과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="475d5-160">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="475d5-161">*Program.cs* : gRPC 서비스의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="475d5-162">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="475d5-163">*Startup.cs* : 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="475d5-164">자세한 내용은 [앱 시작](xref:fundamentals/startup)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="475d5-165">.NET 콘솔 앱에서 gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="475d5-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="475d5-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="475d5-167">Visual Studio의 두 번째 인스턴스를 열고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-167">Open a second instance of Visual Studio and select **Create a new project** .</span></span>
* <span data-ttu-id="475d5-168">**새 프로젝트 만들기** 대화 상자에서 **콘솔 앱(.NET Core)** 을 선택한 후 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next** .</span></span>
* <span data-ttu-id="475d5-169">**프로젝트 이름** 텍스트 상자에 **GrpcGreeterClient** 를 입력하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="475d5-171">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="475d5-172">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-172">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="475d5-173">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-174">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="475d5-175">[Mac용 Visual Studio를 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)의 지침에 따라 *GrpcGreeterClient* 라는 이름의 콘솔 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient* .</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="475d5-176">필수 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="475d5-176">Add required packages</span></span>

<span data-ttu-id="475d5-177">gRPC 클라이언트 프로젝트에는 다음 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="475d5-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)는 .NET Core 클라이언트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="475d5-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)는 C#용 protobuf 메시지 API를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="475d5-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)는 protobuf 파일에 대한 C# 도구 지원을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="475d5-181">도구 패키지는 런타임에는 필요하지 않으므로 종속성은 `PrivateAssets="All"`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="475d5-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="475d5-183">PMC(패키지 관리자 콘솔) 또는 NuGet 패키지 관리를 사용하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="475d5-184">PMC를 사용한 패키지 설치</span><span class="sxs-lookup"><span data-stu-id="475d5-184">PMC option to install packages</span></span>

* <span data-ttu-id="475d5-185">Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="475d5-186">**패키지 관리자 콘솔** 창에서 `cd GrpcGreeterClient`를 실행하여 디렉터리를 *GrpcGreeterClient.csproj* 파일이 있는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="475d5-187">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="475d5-188">NuGet 패키지 관리를 사용한 패키지 설치</span><span class="sxs-lookup"><span data-stu-id="475d5-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="475d5-189">**솔루션 탐색기** > **NuGet 패키지 관리** 에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="475d5-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="475d5-190">**찾아보기** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="475d5-191">검색 상자에 **Grpc.Net.Client** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="475d5-192">**찾아보기** 탭에서 **Grpc.Net.Client** 패키지를 선택하고 **설치** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install** .</span></span>
* <span data-ttu-id="475d5-193">`Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="475d5-195">**통합 터미널** 에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-196">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="475d5-197">**Solution Pad** 에서 **GrpcGreeterClient** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**</span></span>
* <span data-ttu-id="475d5-198">검색 상자에 **Grpc.Net.Client** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="475d5-199">결과 창에서 **Grpc.Net.Client** 패키지를 선택하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="475d5-200">**라이선스 동의** 대화 상자에서 **동의** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-200">Click the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="475d5-201">`Google.Protobuf` 및 `Grpc.Tools`에 대해 반복하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="475d5-202">greet.proto 추가</span><span class="sxs-lookup"><span data-stu-id="475d5-202">Add greet.proto</span></span>

* <span data-ttu-id="475d5-203">gRPC 클라이언트 프로젝트에서 *Protos* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="475d5-204">gRPC Greeter 서비스에서 *Protos\greet.proto* 파일을 gRPC 클라이언트 프로젝트로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="475d5-205">`greet.proto` 파일 내의 네임스페이스를 프로젝트의 네임스페이스로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="475d5-206">*GrpcGreeterClient.csproj* 프로젝트 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="475d5-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="475d5-208">프로젝트를 마우스 오른쪽 단추로 클릭하고 **프로젝트 파일 편집** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-208">Right-click the project and select **Edit Project File** .</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="475d5-210">*GrpcGreeterClient.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-211">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="475d5-212">프로젝트를 마우스 오른쪽 단추로 클릭하고 **프로젝트 파일 편집** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-212">Right-click the project and select **Edit Project File** .</span></span>

  ---

* <span data-ttu-id="475d5-213">*greet.proto* 파일을 참조하는 `<Protobuf>` 요소를 포함하는 항목 그룹을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="475d5-214">Greeter 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="475d5-214">Create the Greeter client</span></span>

<span data-ttu-id="475d5-215">클라이언트 프로젝트를 빌드하여 `GrpcGreeter` 네임스페이스에 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="475d5-216">`GrpcGreeter` 형식은 빌드 프로세스에 의해 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="475d5-217">gRPC 클라이언트 *Program.cs* 파일을 다음 코드로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="475d5-218">*Program.cs* 는 gRPC 클라이언트의 진입점 및 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="475d5-219">Greeter 클라이언트는 다음에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="475d5-220">gRPC 서비스에 대한 연결을 만들기 위한 정보가 포함된 `GrpcChannel` 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="475d5-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="475d5-221">`GrpcChannel`을 사용하여 Greeter 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="475d5-222">Greeter 클라이언트가 비동기 `SayHello` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="475d5-223">`SayHello` 호출의 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="475d5-224">gRPC Greeter 서비스를 사용하여 gRPC 클라이언트 테스트</span><span class="sxs-lookup"><span data-stu-id="475d5-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="475d5-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="475d5-226">Greeter 서비스에서 `Ctrl+F5`를 눌러 디버거 없이 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="475d5-227">`GrpcGreeterClient` 프로젝트에서 `Ctrl+F5`를 눌러 디버거 없이 클라이언트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="475d5-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="475d5-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="475d5-229">Greeter 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-229">Start the Greeter service.</span></span>
* <span data-ttu-id="475d5-230">클라이언트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="475d5-231">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="475d5-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="475d5-232">앞에서 설명한 [macOS의 HTTP/2 TLS 문제 해결 방법](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)으로 인해 클라이언트의 채널 주소를 “http://localhost:5000”으로 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="475d5-233">**GrpcGreeterClient/Program.cs** 의 줄 13을 다음과 같이 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="475d5-234">Greeter 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-234">Start the Greeter service.</span></span>
* <span data-ttu-id="475d5-235">클라이언트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-235">Start the client.</span></span>

---

<span data-ttu-id="475d5-236">클라이언트가 *GreeterClient* 라는 이름을 포함한 메시지와 함께 인사말을 서비스에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-236">The client sends a greeting to the service with a message containing its name, *GreeterClient* .</span></span> <span data-ttu-id="475d5-237">서비스는 "Hello GreeterClient"라는 메시지를 응답으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="475d5-238">"Hello GreeterClient" 응답이 명령 프롬프트에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="475d5-239">gRPC 서비스는 성공한 호출의 세부 정보를 명령 프롬프트에 작성된 로그에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="475d5-240">이 문서의 코드에는 gRPC 서비스 보호를 위해 ASP.NET Core HTTPS 개발 인증서가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="475d5-241">.NET gRPC 클라이언트가 `The remote certificate is invalid according to the validation procedure.` 또는 `The SSL connection could not be established.` 메시지와 함께 실패하는 경우에는 개발 인증서를 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="475d5-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="475d5-242">이 문제를 해결하려면 [신뢰할 수 없는/잘못된 인증서를 사용하여 gRPC 서비스 호출](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="475d5-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="475d5-243">다음 단계</span><span class="sxs-lookup"><span data-stu-id="475d5-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
