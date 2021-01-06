---
page_type: sample
description: 이 자습서에서는 MongoDB NoSQL 데이터베이스를 사용하여 ASP.NET Core 웹 API를 만드는 방법을 보여 줍니다.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "84106522"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기

이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

* MongoDB 구성
* MongoDB 데이터베이스 만들기
* MongoDB 컬렉션 및 스키마 정의
* 웹 API에서 MongoDB CRUD 작업 수행
* JSON serialization 사용자 지정

## <a name="prerequisites"></a>사전 요구 사항

* [.NET Core SDK 3.0 이상](https://dotnet.microsoft.com/download/dotnet-core)
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019 미리 보기](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>MongoDB 구성

Windows를 사용하는 경우 MongoDB는 기본적으로 *C:\\Program Files\\MongoDB* 에 설치됩니다. *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* 을 `Path` 환경 변수에 추가합니다. 이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.

다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다. mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.

1. 개발 머신에서 데이터를 저장할 디렉터리를 선택합니다. 예를 들어 Windows의 경우 *C:\\BooksData* 를 선택합니다. 디렉터리가 없을 경우 새로 만듭니다. mongo 셸은 새 디렉터리를 만들지 않습니다.
1. 명령 셸을 엽니다. 다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다. `<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. 다른 명령 셸 인스턴스를 엽니다. 다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.

    ```console
    mongo
    ```

1. 명령 셸에서 다음을 실행합니다.

    ```console
    use BookstoreDb
    ```

    아직 존재하지 않는 경우 *BookstoreDb* 라는 데이터베이스가 생성됩니다. 데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.

1. 다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.

    ```console
    db.createCollection('Books')
    ```

    다음 결과가 표시됩니다.

    ```console
    { "ok" : 1 }
    ```

1. 다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    다음 결과가 표시됩니다.

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
  > 이 문서에 표시된 ID는 이 샘플을 실행할 때의 ID와 일치하지 않습니다.

1. 다음 명령을 사용하여 데이터베이스의 문서를 봅니다.

    ```console
    db.Books.find({}).pretty()
    ```

    다음 결과가 표시됩니다.

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

    스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.

데이터베이스가 준비되었습니다. 이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core 웹 API 프로젝트 만들기

1. **파일** > **새로 만들기** > **프로젝트** 로 이동합니다.
1. **ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음** 을 선택합니다.
1. 프로젝트 이름을 *BooksApi* 로 지정하고 **만들기** 를 선택합니다.
1. **.NET Core** 대상 프레임워크 및 **ASP.NET Core 3.0** 을 선택합니다. **API** 프로젝트 템플릿을 선택하고 **만들기** 를 선택합니다.
1. [NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다. **패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다. 다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>엔터티 모델 추가

1. 프로젝트 루트에 *Models* 디렉터리를 추가합니다.
1. 다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.

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

    앞의 클래스에서 `Id` 속성은

    * CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.
    * 이 속성을 문서의 기본 키로 지정하려면 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)로 주석이 추가됩니다.
    * [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조체 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)로 주석이 추가됩니다. Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.

    `BookName` 속성은 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다. `Name`의 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.

## <a name="add-a-configuration-model"></a>구성 모델 추가

1. 다음 데이터베이스 구성 값을 *appsettings.json* 에 추가합니다.

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. 다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다. JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.

1. 다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    위의 코드에서

    * *appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다. 예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json* 의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.
    * `IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다. 삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.

1. *Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>CRUD 작업 서비스 추가

1. 프로젝트 루트에 *Services* 디렉터리를 추가합니다.
1. 다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다. 이 기술은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.

1. 다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다. `BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다. 공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.

1. *Startup.cs* 의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.


    ```csharp
    using BooksApi.Services;
    ```

`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다. 이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다. 이 자습서에서는 인터페이스의 일반 [GetCollection\<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다. 이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다. `GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.
  * `collection`은 컬렉션 이름을 나타냅니다.
  * `TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.

`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다. 이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): 제공된 개체를 컬렉션에 새 문서로 삽입합니다.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.

## <a name="add-a-controller"></a>컨트롤러 추가

다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

앞의 웹 API 컨트롤러는 다음과 같습니다.

* `BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.
* GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.
* `Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다. 상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다. 또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다. `Location` 헤더는 새로 만든 책의 URI를 지정합니다.

## <a name="test-the-web-api"></a>웹 API 테스트

1. 앱을 빌드하고 실행합니다.

1. `http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다. 다음 JSON 응답이 표시됩니다.

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

1. `http://localhost:<port>/api/books/{id here}`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다. 다음 JSON 응답이 표시됩니다.

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>JSON serialization 옵션 구성

[웹 API 테스트](#test-the-web-api) 섹션에서 반환된 JSON 응답에 대해 변경하는 두 개의 세부 정보가 있습니다.

* 속성 이름의 기본 카멜식 대/소문자는 CLR 개체의 속성 이름의 파스칼식 대/소문자와 일치하도록 변경되어야 합니다.
* `bookName` 속성은 `Name`으로 반환되어야 합니다.

앞의 요구 사항을 충족하기 위해 다음과 같이 변경합니다.

1. ASP.NET 공유 프레임워크에서 JSON.NET이 제거되었습니다. [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)에 대한 패키지 참조를 추가합니다.

1. `Startup.ConfigureServices`에서 다음 강조 표시된 코드를 `AddMvc` 메서드 호출에 연결합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    이전 변경으로 웹 API의 직렬화된 JSON 응답에서 속성 이름은 CLR 개체 형식의 해당 속성 이름과 일치합니다. 예를 들어 `Book` 클래스의 `Author` 속성은 `Author`로 직렬화합니다.

1. *Models/Book.cs* 에서 다음 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 특성으로 `BookName` 속성에 주석을 추가합니다.

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    `Name`의 `[JsonProperty]` 특성 값은 웹 API의 직렬화된 JSON 응답의 속성 이름을 나타냅니다.

1. *Models/Book.cs* 의 맨 위에 다음 코드를 추가하여 `[JsonProperty]` 특성 참조를 확인합니다.

    ```csharp
    using Newtonsoft.Json;
    ```

1. [웹 API 테스트](#test-the-web-api) 섹션에 정의된 단계를 반복합니다. JSON 속성 이름의 차이를 확인합니다.

## <a name="next-steps"></a>다음 단계

ASP.NET Core 웹 API 빌드 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [이 문서의 YouTube 버전](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [ASP.NET Core로 웹 API 만들기](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
