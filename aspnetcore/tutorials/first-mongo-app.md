---
title: ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기
author: prkhandelwal
description: 이 자습서에서는 MongoDB NoSQL 데이터베이스를 사용하여 ASP.NET Core 웹 API를 만드는 방법을 보여 줍니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 489a15f2011e44ffd9f9bfe7d5410b7d79a10632
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587608"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="bedb3-103">ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="bedb3-104">작성자: [Pratik Khandelwal](https://twitter.com/K2Prk) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="bedb3-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bedb3-105">이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="bedb3-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bedb3-107">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-107">Configure MongoDB</span></span>
> * <span data-ttu-id="bedb3-108">MongoDB 데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="bedb3-109">MongoDB 컬렉션 및 스키마 정의</span><span class="sxs-lookup"><span data-stu-id="bedb3-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="bedb3-110">웹 API에서 MongoDB CRUD 작업 수행</span><span class="sxs-lookup"><span data-stu-id="bedb3-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="bedb3-111">JSON serialization 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="bedb3-111">Customize JSON serialization</span></span>

<span data-ttu-id="bedb3-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bedb3-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bedb3-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="bedb3-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bedb3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="bedb3-115">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="bedb3-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="bedb3-116">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="bedb3-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="bedb3-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bedb3-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="bedb3-119">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="bedb3-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="bedb3-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="bedb3-121">Visual Studio Code용 C#</span><span class="sxs-lookup"><span data-stu-id="bedb3-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="bedb3-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bedb3-123">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="bedb3-124">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="bedb3-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="bedb3-125">Mac용 Visual Studio 버전 7.7 이상</span><span class="sxs-lookup"><span data-stu-id="bedb3-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="bedb3-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="bedb3-127">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-127">Configure MongoDB</span></span>

<span data-ttu-id="bedb3-128">Windows를 사용하는 경우 MongoDB는 기본적으로 *C:\\Program Files\\MongoDB* 에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="bedb3-129">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* 을 `Path` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="bedb3-130">이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="bedb3-131">다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="bedb3-132">mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bedb3-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="bedb3-133">개발 머신에서 데이터를 저장할 디렉터리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="bedb3-134">예를 들어 Windows의 경우 *C:\\BooksData* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="bedb3-135">디렉터리가 없을 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="bedb3-136">mongo 셸은 새 디렉터리를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="bedb3-137">명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-137">Open a command shell.</span></span> <span data-ttu-id="bedb3-138">다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="bedb3-139">`<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="bedb3-140">다른 명령 셸 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-140">Open another command shell instance.</span></span> <span data-ttu-id="bedb3-141">다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="bedb3-142">명령 셸에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="bedb3-143">아직 존재하지 않는 경우 *BookstoreDb* 라는 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="bedb3-144">데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="bedb3-145">다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="bedb3-146">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="bedb3-147">다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="bedb3-148">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="bedb3-149">이 문서에 표시된 ID는 이 샘플을 실행할 때의 ID와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="bedb3-150">다음 명령을 사용하여 데이터베이스의 문서를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="bedb3-151">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="bedb3-152">스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="bedb3-153">데이터베이스가 준비되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-153">The database is ready.</span></span> <span data-ttu-id="bedb3-154">이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="bedb3-155">ASP.NET Core 웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bedb3-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bedb3-157">**파일** > **새로 만들기** > **프로젝트** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="bedb3-158">**ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-159">프로젝트 이름을 *BooksApi* 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-160">**.NET Core** 대상 프레임워크 및 **ASP.NET Core 3.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="bedb3-161">**API** 프로젝트 템플릿을 선택하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-162">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="bedb3-163">**패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="bedb3-164">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bedb3-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bedb3-166">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="bedb3-167">.NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="bedb3-168">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 대화 상자에 **빌드 및 디버그에 필요한 자산이 'BooksApi'에서 누락되었습니다.라는 메시지가 표시됩니다. 추가할까요?** .</span><span class="sxs-lookup"><span data-stu-id="bedb3-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="bedb3-169">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-169">Select **Yes**.</span></span>
1. <span data-ttu-id="bedb3-170">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="bedb3-171">**통합 터미널** 을 열고 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="bedb3-172">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bedb3-173">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bedb3-174">Mac용 Visual Studio 버전 8.6 미만에서는 사이드바에서 **파일** > **새 솔루션** >  **.NET Core** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="bedb3-175">버전 8.6 이상에서는 사이드바에서 **파일** > **새 솔루션** > **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="bedb3-176">**ASP.NET Core** > **API** C# 프로젝트 템플릿을 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-177">**대상 프레임워크** 드롭다운 목록에서 **.NET Core 3.1** 을 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-178">**프로젝트 이름** 으로 *BooksApi* 를 입력하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-179">**솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="bedb3-180">검색 상자에 *MongoDB.Driver* 를 입력하여 *MongoDB.Driver* 패키지를 선택하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="bedb3-181">**라이선스 승인** 대화 상자에서 **동의** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="bedb3-182">엔터티 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-182">Add an entity model</span></span>

1. <span data-ttu-id="bedb3-183">프로젝트 루트에 *Models* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="bedb3-184">다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="bedb3-185">앞의 클래스에서 `Id` 속성은</span><span class="sxs-lookup"><span data-stu-id="bedb3-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="bedb3-186">CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="bedb3-187">이 속성을 문서의 기본 키로 지정하려면 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="bedb3-188">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조체 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="bedb3-189">Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="bedb3-190">`BookName` 속성은 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="bedb3-191">`Name`의 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="bedb3-192">구성 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-192">Add a configuration model</span></span>

1. <span data-ttu-id="bedb3-193">다음 데이터베이스 구성 값을 *appsettings.json* 에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="bedb3-194">다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="bedb3-195">위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="bedb3-196">JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="bedb3-197">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="bedb3-198">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="bedb3-198">In the preceding code:</span></span>

   * <span data-ttu-id="bedb3-199">*appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="bedb3-200">예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json* 의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="bedb3-201">`IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="bedb3-202">삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="bedb3-203">*Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="bedb3-204">CRUD 작업 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="bedb3-205">프로젝트 루트에 *Services* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="bedb3-206">다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="bedb3-207">위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="bedb3-208">이 방법은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="bedb3-209">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="bedb3-210">위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="bedb3-211">`BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="bedb3-212">공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="bedb3-213">*Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="bedb3-214">`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="bedb3-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="bedb3-216">이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="bedb3-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="bedb3-218">이 자습서에서는 인터페이스의 일반 [GetCollection\<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="bedb3-219">이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="bedb3-220">`GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="bedb3-221">`collection`은 컬렉션 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="bedb3-222">`TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="bedb3-223">`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="bedb3-224">이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="bedb3-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="bedb3-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="bedb3-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): 제공된 개체를 컬렉션에 새 문서로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="bedb3-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="bedb3-229">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-229">Add a controller</span></span>

<span data-ttu-id="bedb3-230">다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="bedb3-231">앞의 웹 API 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-231">The preceding web API controller:</span></span>

* <span data-ttu-id="bedb3-232">`BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="bedb3-233">GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="bedb3-234">`Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="bedb3-235">상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="bedb3-236">또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="bedb3-237">`Location` 헤더는 새로 만든 책의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="bedb3-238">웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="bedb3-238">Test the web API</span></span>

1. <span data-ttu-id="bedb3-239">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-239">Build and run the app.</span></span>

1. <span data-ttu-id="bedb3-240">`http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="bedb3-241">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="bedb3-242">`http://localhost:<port>/api/books/{id here}`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="bedb3-243">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="bedb3-244">JSON serialization 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-244">Configure JSON serialization options</span></span>

<span data-ttu-id="bedb3-245">[웹 API 테스트](#test-the-web-api) 섹션에서 반환된 JSON 응답에 대해 변경하는 두 개의 세부 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="bedb3-246">속성 이름의 기본 카멜식 대/소문자는 CLR 개체의 속성 이름의 파스칼식 대/소문자와 일치하도록 변경되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="bedb3-247">`bookName` 속성은 `Name`으로 반환되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="bedb3-248">앞의 요구 사항을 충족하기 위해 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="bedb3-249">ASP.NET 공유 프레임워크에서 JSON.NET이 제거되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="bedb3-250">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="bedb3-251">`Startup.ConfigureServices`에서 다음 강조 표시된 코드를 `AddControllers` 메서드 호출에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="bedb3-252">이전 변경으로 웹 API의 직렬화된 JSON 응답에서 속성 이름은 CLR 개체 형식의 해당 속성 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="bedb3-253">예를 들어 `Book` 클래스의 `Author` 속성은 `Author`로 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="bedb3-254">*Models/Book.cs* 에서 다음 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 특성으로 `BookName` 속성에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="bedb3-255">`Name`의 `[JsonProperty]` 특성 값은 웹 API의 직렬화된 JSON 응답의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="bedb3-256">*Models/Book.cs* 의 맨 위에 다음 코드를 추가하여 `[JsonProperty]` 특성 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="bedb3-257">[웹 API 테스트](#test-the-web-api) 섹션에 정의된 단계를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="bedb3-258">JSON 속성 이름의 차이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bedb3-259">이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="bedb3-260">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bedb3-261">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-261">Configure MongoDB</span></span>
> * <span data-ttu-id="bedb3-262">MongoDB 데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="bedb3-263">MongoDB 컬렉션 및 스키마 정의</span><span class="sxs-lookup"><span data-stu-id="bedb3-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="bedb3-264">웹 API에서 MongoDB CRUD 작업 수행</span><span class="sxs-lookup"><span data-stu-id="bedb3-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="bedb3-265">JSON serialization 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="bedb3-265">Customize JSON serialization</span></span>

<span data-ttu-id="bedb3-266">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bedb3-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bedb3-267">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="bedb3-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bedb3-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="bedb3-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="bedb3-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="bedb3-270">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="bedb3-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="bedb3-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bedb3-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="bedb3-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="bedb3-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="bedb3-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="bedb3-275">Visual Studio Code용 C#</span><span class="sxs-lookup"><span data-stu-id="bedb3-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="bedb3-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bedb3-277">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="bedb3-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="bedb3-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="bedb3-279">Mac용 Visual Studio 버전 7.7 이상</span><span class="sxs-lookup"><span data-stu-id="bedb3-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="bedb3-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="bedb3-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="bedb3-281">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-281">Configure MongoDB</span></span>

<span data-ttu-id="bedb3-282">Windows를 사용하는 경우 MongoDB는 기본적으로 *C:\\Program Files\\MongoDB* 에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="bedb3-283">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* 을 `Path` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="bedb3-284">이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="bedb3-285">다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="bedb3-286">mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bedb3-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="bedb3-287">개발 머신에서 데이터를 저장할 디렉터리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="bedb3-288">예를 들어 Windows의 경우 *C:\\BooksData* 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="bedb3-289">디렉터리가 없을 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="bedb3-290">mongo 셸은 새 디렉터리를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="bedb3-291">명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-291">Open a command shell.</span></span> <span data-ttu-id="bedb3-292">다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="bedb3-293">`<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="bedb3-294">다른 명령 셸 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-294">Open another command shell instance.</span></span> <span data-ttu-id="bedb3-295">다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="bedb3-296">명령 셸에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="bedb3-297">아직 존재하지 않는 경우 *BookstoreDb* 라는 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="bedb3-298">데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="bedb3-299">다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="bedb3-300">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="bedb3-301">다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="bedb3-302">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="bedb3-303">이 문서에 표시된 ID는 이 샘플을 실행할 때의 ID와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="bedb3-304">다음 명령을 사용하여 데이터베이스의 문서를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="bedb3-305">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="bedb3-306">스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="bedb3-307">데이터베이스가 준비되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-307">The database is ready.</span></span> <span data-ttu-id="bedb3-308">이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="bedb3-309">ASP.NET Core 웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bedb3-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bedb3-311">**파일** > **새로 만들기** > **프로젝트** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="bedb3-312">**ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-313">프로젝트 이름을 *BooksApi* 로 지정하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-314">**.NET Core** 대상 프레임워크 및 **ASP.NET Core 2.2** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="bedb3-315">**API** 프로젝트 템플릿을 선택하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-316">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="bedb3-317">**패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="bedb3-318">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bedb3-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bedb3-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bedb3-320">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="bedb3-321">.NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="bedb3-322">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 대화 상자에 **빌드 및 디버그에 필요한 자산이 'BooksApi'에서 누락되었습니다.라는 메시지가 표시됩니다. 추가할까요?** .</span><span class="sxs-lookup"><span data-stu-id="bedb3-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="bedb3-323">**Yes** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-323">Select **Yes**.</span></span>
1. <span data-ttu-id="bedb3-324">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="bedb3-325">**통합 터미널** 을 열고 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="bedb3-326">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bedb3-327">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bedb3-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bedb3-328">Mac용 Visual Studio 버전 8.6 미만에서는 사이드바에서 **파일** > **새 솔루션** >  **.NET Core** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="bedb3-329">버전 8.6 이상에서는 사이드바에서 **파일** > **새 솔루션** > **웹 및 콘솔** > **앱** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="bedb3-330">**ASP.NET Core Web API** C# 프로젝트 템플릿을 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-331">**대상 프레임워크** 드롭다운 목록에서 **.NET Core 2.2** 를 선택하고 **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="bedb3-332">**프로젝트 이름** 으로 *BooksApi* 를 입력하고 **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="bedb3-333">**솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="bedb3-334">검색 상자에 *MongoDB.Driver* 를 입력하여 *MongoDB.Driver* 패키지를 선택하고 **패키지 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="bedb3-335">**라이선스 승인** 대화 상자에서 **동의** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="bedb3-336">엔터티 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-336">Add an entity model</span></span>

1. <span data-ttu-id="bedb3-337">프로젝트 루트에 *Models* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="bedb3-338">다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="bedb3-339">앞의 클래스에서 `Id` 속성은</span><span class="sxs-lookup"><span data-stu-id="bedb3-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="bedb3-340">CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="bedb3-341">이 속성을 문서의 기본 키로 지정하려면 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="bedb3-342">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조체 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="bedb3-343">Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="bedb3-344">`BookName` 속성은 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="bedb3-345">`Name`의 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="bedb3-346">구성 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-346">Add a configuration model</span></span>

1. <span data-ttu-id="bedb3-347">다음 데이터베이스 구성 값을 *appsettings.json* 에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="bedb3-348">다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="bedb3-349">위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="bedb3-350">JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="bedb3-351">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="bedb3-352">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="bedb3-352">In the preceding code:</span></span>

   * <span data-ttu-id="bedb3-353">*appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="bedb3-354">예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json* 의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="bedb3-355">`IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="bedb3-356">삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="bedb3-357">*Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="bedb3-358">CRUD 작업 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="bedb3-359">프로젝트 루트에 *Services* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="bedb3-360">다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="bedb3-361">위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="bedb3-362">이 방법은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="bedb3-363">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="bedb3-364">위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="bedb3-365">`BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="bedb3-366">공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="bedb3-367">*Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="bedb3-368">`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="bedb3-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="bedb3-370">이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="bedb3-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="bedb3-372">이 자습서에서는 인터페이스의 일반 [GetCollection\<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="bedb3-373">이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="bedb3-374">`GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="bedb3-375">`collection`은 컬렉션 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="bedb3-376">`TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="bedb3-377">`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="bedb3-378">이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="bedb3-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="bedb3-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="bedb3-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): 제공된 개체를 컬렉션에 새 문서로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="bedb3-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="bedb3-383">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-383">Add a controller</span></span>

<span data-ttu-id="bedb3-384">다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="bedb3-385">앞의 웹 API 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-385">The preceding web API controller:</span></span>

* <span data-ttu-id="bedb3-386">`BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="bedb3-387">GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="bedb3-388">`Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="bedb3-389">상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="bedb3-390">또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="bedb3-391">`Location` 헤더는 새로 만든 책의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="bedb3-392">웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="bedb3-392">Test the web API</span></span>

1. <span data-ttu-id="bedb3-393">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-393">Build and run the app.</span></span>

1. <span data-ttu-id="bedb3-394">`http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="bedb3-395">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="bedb3-396">`http://localhost:<port>/api/books/{id here}`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="bedb3-397">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="bedb3-398">JSON serialization 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="bedb3-398">Configure JSON serialization options</span></span>

<span data-ttu-id="bedb3-399">[웹 API 테스트](#test-the-web-api) 섹션에서 반환된 JSON 응답에 대해 변경하는 두 개의 세부 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="bedb3-400">속성 이름의 기본 카멜식 대/소문자는 CLR 개체의 속성 이름의 파스칼식 대/소문자와 일치하도록 변경되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="bedb3-401">`bookName` 속성은 `Name`으로 반환되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="bedb3-402">앞의 요구 사항을 충족하기 위해 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="bedb3-403">`Startup.ConfigureServices`에서 다음 강조 표시된 코드를 `AddMvc` 메서드 호출에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="bedb3-404">이전 변경으로 웹 API의 직렬화된 JSON 응답에서 속성 이름은 CLR 개체 형식의 해당 속성 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="bedb3-405">예를 들어 `Book` 클래스의 `Author` 속성은 `Author`로 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="bedb3-406">*Models/Book.cs* 에서 다음 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 특성으로 `BookName` 속성에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="bedb3-407">`Name`의 `[JsonProperty]` 특성 값은 웹 API의 직렬화된 JSON 응답의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="bedb3-408">*Models/Book.cs* 의 맨 위에 다음 코드를 추가하여 `[JsonProperty]` 특성 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="bedb3-409">[웹 API 테스트](#test-the-web-api) 섹션에 정의된 단계를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="bedb3-410">JSON 속성 이름의 차이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bedb3-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="bedb3-411">웹 API에 인증 지원 추가</span><span class="sxs-lookup"><span data-stu-id="bedb3-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="bedb3-412">다음 단계</span><span class="sxs-lookup"><span data-stu-id="bedb3-412">Next steps</span></span>

<span data-ttu-id="bedb3-413">ASP.NET Core 웹 API 빌드 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bedb3-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="bedb3-414">이 문서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="bedb3-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [<span data-ttu-id="bedb3-415">Microsoft Learn: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="bedb3-415">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
