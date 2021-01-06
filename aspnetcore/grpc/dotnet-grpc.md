---
title: dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.
author: juntaoluo
description: dotnet-grpc 전역 도구를 사용하여 Protobuf 참조를 추가, 업데이트, 제거 및 나열하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059912"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.

작성자: [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`는 .NET gRPC 프로젝트 내에서 [Protobuf( *.proto*)](xref:grpc/basics#proto-file) 참조를 관리하기 위한 .NET Core 전역 도구입니다. 이 도구를 사용하여 Protobuf 참조를 추가, 새로 고침, 제거 및 나열할 수 있습니다.

## <a name="installation"></a>설치

`dotnet-grpc` [.NET Core 전역 도구](/dotnet/core/tools/global-tools)를 설치하려면 다음 명령을 실행합니다.

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>참조 추가

`dotnet-grpc`를 사용하여 *.csproj* 파일에 Protobuf 참조를 `<Protobuf />` 항목으로 추가할 수 있습니다.

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 참조는 C# 클라이언트 및/또는 서버 자산을 생성하는 데 사용됩니다. `dotnet-grpc` 도구는 다음을 수행할 수 있습니다.

* 디스크의 로컬 파일에서 Protobuf 참조를 만듭니다.
* URL로 지정된 원격 파일에서 Protobuf 참조를 만듭니다.
* 올바른 gRPC 패키지 종속성이 프로젝트에 추가되도록 합니다.

예를 들어 웹앱에는 `Grpc.AspNetCore` 패키지가 추가됩니다. `Grpc.AspNetCore`는 gRPC 서버 및 클라이언트 라이브러리와 도구 지원을 포함합니다. 또는 콘솔 앱에는 gRPC 클라이언트 라이브러리와 도구 지원만 포함하는 `Grpc.Net.Client`, `Grpc.Tools` 및 `Google.Protobuf` 패키지가 추가됩니다.

### <a name="add-file"></a>파일 추가

`add-file` 명령은 디스크의 로컬 파일을 Protobuf 참조로 추가하는 데 사용됩니다. 제공되는 파일 경로와 관련해서 다음 사항을 확인합니다.

* 현재 디렉터리 기준의 상대 경로이거나 절대 경로일 수 있습니다.
* 패턴 기반 파일 [와일드카드 사용](https://wikipedia.org/wiki/Glob_(programming))을 위해 와일드카드를 포함할 수 있습니다.

프로젝트 디렉터리 외부에 파일이 있는 경우, Visual Studio의 `Protos` 폴더 아래에 있는 파일을 표시하기 위해 `Link` 요소가 추가됩니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| 파일 | protobuf 파일 참조입니다. 로컬 protobuf 파일의 GLOB 경로일 수 있습니다. |

#### <a name="options"></a>옵션

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.
| -S | --services | 생성해야 하는 gRPC 서비스의 형식입니다. `Default`를 지정한 경우, 웹 프로젝트에는 `Both`가 사용되고 웹 프로젝트가 아닌 프로젝트에는 `Client`가 사용됩니다. 허용되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.
| -i | --additional-import-dirs | protobuf 파일의 가져오기를 확인할 때 사용할 추가 디렉터리입니다. 세미콜론으로 구분된 경로 목록입니다.
| | --access | 생성된 C# 클래스에 사용할 액세스 한정자입니다. 기본값은 `Public`입니다. 허용되는 값은 `Internal` 및 `Public`입니다.

### <a name="add-url"></a>URL 추가

`add-url` 명령은 소스 URL에 지정된 원격 파일을 Protobuf 참조로 추가하는 데 사용됩니다. 원격 파일을 다운로드할 위치를 지정하려면 파일 경로를 제공해야 합니다. 파일 경로는 현재 디렉터리 기준의 상대 경로이거나 절대 경로일 수 있습니다. 파일 경로가 프로젝트 디렉터리 외부에 있는 경우, Visual Studio의 `Protos` 가상 폴더 아래에 있는 파일을 표시하기 위해 `Link` 요소가 추가됩니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| url | 원격 protobuf 파일의 URL입니다. |

#### <a name="options"></a>옵션

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -o | --output | 원격 protobuf 파일의 다운로드 경로를 지정합니다. 필수 옵션입니다.
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.
| -S | --services | 생성해야 하는 gRPC 서비스의 형식입니다. `Default`를 지정한 경우, 웹 프로젝트에는 `Both`가 사용되고 웹 프로젝트가 아닌 프로젝트에는 `Client`가 사용됩니다. 허용되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.
| -i | --additional-import-dirs | protobuf 파일의 가져오기를 확인할 때 사용할 추가 디렉터리입니다. 세미콜론으로 구분된 경로 목록입니다.
| | --access | 생성된 C# 클래스에 사용할 액세스 한정자입니다. 기본값은 `Public`여야 합니다. 허용되는 값은 `Internal` 및 `Public`입니다.

## <a name="remove"></a>제거

`remove` 명령은 *.csproj* 파일에서 Protobuf 참조를 제거하는 데 사용됩니다. 이 명령은 경로 인수와 소스 URL을 인수로 사용합니다. 도구와 관련해서 다음 사항을 확인합니다.

* Protobuf 참조만 제거합니다.
* 원래 원격 URL에서 다운로드된 경우에도 *.proto* 파일을 삭제하지 않습니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| 참조 | 제거할 protobuf 참조의 URL 또는 파일 경로입니다. |

### <a name="options"></a>옵션

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.

## <a name="refresh"></a>새로 고침

`refresh` 명령은 소스 URL의 최신 콘텐츠로 원격 참조를 업데이트하는 데 사용됩니다. 다운로드 파일 경로와 소스 URL을 모두 사용하여 업데이트할 참조를 지정할 수 있습니다. 참고:

* 파일 콘텐츠의 해시를 비교하여 로컬 파일을 업데이트해야 하는지를 확인합니다.
* 타임스탬프 정보는 비교되지 않습니다.

업데이트가 필요한 경우, 도구는 항상 로컬 파일을 원격 파일로 바꿉니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>인수

| 인수 | 설명 |
|-|-|
| 참조 | 업데이트해야 하는 원격 protobuf 참조의 URL 또는 파일 경로입니다. 모든 원격 참조를 새로 고치려면 이 인수를 비워 둡니다. |

### <a name="options"></a>옵션

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.
| | --dry-run | 새 콘텐츠를 다운로드하지 않고 업데이트되는 파일 목록을 출력합니다.

## <a name="list"></a>목록

`list` 명령은 프로젝트 파일에 모든 Protobuf 참조를 표시하는 데 사용됩니다. 열의 모든 값이 기본값인 경우 열을 생략할 수 있습니다.

### <a name="usage"></a>사용법

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>옵션

| 짧음 옵션 | 긴 옵션 | 설명 |
|-|-|-|
| -p | --project | 작업할 프로젝트 파일의 경로입니다. 파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
