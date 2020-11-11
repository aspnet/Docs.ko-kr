---
title: ASP.NET Core의 키 저장소 공급자
author: rick-anderson
description: ASP.NET Core의 키 저장소 공급자 및 키 저장소 위치를 구성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 6a70183ce4b1a129ef213300473b233a5ef822f9
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94463888"
---
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core의 키 저장소 공급자

데이터 보호 시스템은 [기본적으로 검색 메커니즘을 채택](xref:security/data-protection/configuration/default-settings) 하 여 암호화 키가 유지 되어야 하는 위치를 결정 합니다. 개발자는 기본 검색 메커니즘을 재정의 하 고 위치를 수동으로 지정할 수 있습니다.

> [!WARNING]
> 명시적 키 지 속성 위치를 지정 하는 경우 데이터 보호 시스템은 미사용 기본 키 암호화 메커니즘을 등록 취소 더 이상 미사용에서 키가 암호화 되지 않습니다. 프로덕션 배포를 위한 [명시적 키 암호화 메커니즘도 추가로 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 하는 것이 좋습니다.

## <a name="file-system"></a>파일 시스템

파일 시스템 기반 키 리포지토리를 구성 하려면 아래와 같이 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 구성 루틴을 호출 합니다. 키를 저장 해야 하는 리포지토리를 가리키는 [system.io.directoryinfo](/dotnet/api/system.io.directoryinfo) 를 제공 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

는 `DirectoryInfo` 로컬 컴퓨터의 디렉터리를 가리키거나 네트워크 공유의 폴더를 가리킬 수 있습니다. 로컬 컴퓨터의 디렉터리를 가리키는 경우 (그리고 로컬 컴퓨터의 앱만이 리포지토리를 사용 하기 위해 액세스 해야 하는 경우) [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (windows)를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다. 그렇지 않으면 [x.509 인증서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다.

## <a name="azure-storage"></a>Azure Storage

[AspNetCore](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) 패키지를 사용 하 여 Azure Blob Storage에 데이터 보호 키를 저장할 수 있습니다. 웹 앱의 여러 인스턴스 간에 키를 공유할 수 있습니다. 앱은 cookie 여러 서버에서 인증 s 또는 CSRF 보호를 공유할 수 있습니다.

Azure Blob Storage 공급자를 구성 하려면 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) 오버 로드 중 하나를 호출 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

웹 앱이 Azure 서비스로 실행 되는 경우 azure [. blob](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient)을 사용 하 여 azure storage에 인증 하는 데 연결 문자열을 사용할 수 있습니다.

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> 저장소 계정에 대 한 연결 문자열은 Azure Portal의 "액세스 키" 섹션 아래에서 또는 다음 CLI 명령을 실행 하 여 찾을 수 있습니다. 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

[AspNetCore StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) 패키지를 사용 하면 Redis cache에 데이터 보호 키를 저장할 수 있습니다. 웹 앱의 여러 인스턴스 간에 키를 공유할 수 있습니다. 앱은 cookie 여러 서버에서 인증 s 또는 CSRF 보호를 공유할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[AspNetCore Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) 패키지를 사용 하면 Redis cache에 데이터 보호 키를 저장할 수 있습니다. 웹 앱의 여러 인스턴스 간에 키를 공유할 수 있습니다. 앱은 cookie 여러 서버에서 인증 s 또는 CSRF 보호를 공유할 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Redis에서를 구성 하려면 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) 오버 로드 중 하나를 호출 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Redis에서를 구성 하려면 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) 오버 로드 중 하나를 호출 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

자세한 내용은 다음 항목을 참조하세요.

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis 캐시(영문)](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASP.NET Core DataProtection 샘플](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>레지스트리

**Windows 배포에만 적용 됩니다.**

응용 프로그램에 파일 시스템에 대 한 쓰기 권한이 없을 수도 있습니다. 앱이 가상 서비스 계정 (예: *w3wp.exe* 의 응용 프로그램 풀 id)으로 실행 되는 시나리오를 고려해 보세요. 이러한 경우 관리자는 서비스 계정 id로 액세스할 수 있는 레지스트리 키를 프로 비전 할 수 있습니다. 아래와 같이 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) 확장 메서드를 호출 합니다. 암호화 키를 저장 해야 하는 위치를 가리키는 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) 를 제공 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) 를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[AspNetCore microsoft.entityframeworkcore.tools.dotnet](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) 패키지는 Entity Framework Core을 사용 하 여 데이터베이스에 데이터 보호 키를 저장 하기 위한 메커니즘을 제공 합니다. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet 패키지는 프로젝트 파일에 추가 해야 하며, [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)의 일부가 아닙니다.

이 패키지를 사용 하면 웹 앱의 여러 인스턴스 간에 키를 공유할 수 있습니다.

EF Core 공급자를 구성 하려면 [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) 메서드를 호출 합니다.

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

제네릭 매개 변수는 `TContext` [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 에서 상속 해야 하며 [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)를 구현 해야 합니다.

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

테이블을 만듭니다 `DataProtectionKeys` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**패키지 관리자 콘솔** (PMC) 창에서 다음 명령을 실행 합니다.

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

명령 셸에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` 는 `DbContext` 앞의 코드 샘플에 정의 되어 있습니다. 다른 이름을 사용 하는을 사용 하는 경우 `DbContext` 에는 사용자 `DbContext` 의 이름을로 바꿉니다 `MyKeysContext` .

`DataProtectionKeys`클래스/엔터티는 다음 표에 나와 있는 구조를 가집니다.

| 속성/필드 | CLR 형식 | SQL 형식              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>사용자 지정 키 리포지토리

기본 제공 메커니즘이 적절 하지 않은 경우 개발자는 사용자 지정 [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)을 제공 하 여 고유한 키 지 속성 메커니즘을 지정할 수 있습니다.
