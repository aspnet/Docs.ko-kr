---
title: OpenAPI를 사용한 ASP.NET Core 앱 개발
author: ryanbrandenburg
description: "'Microsoft dotnet-openapi' 도구를 사용하여 OpenAPI 파일에 참조를 추가하는 방법을 보여줍니다."
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 28a71c7040667c7544cc17c1184c09b5b39959b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052554"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>OpenAPI 도구를 사용한 ASP.NET Core 앱 개발

Ryan Brandenburg가 개발

[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi)은 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 참조를 관리하기 위한 [.NET Core 전역 도구](/dotnet/core/tools/global-tools)입니다.

## <a name="installation"></a>설치

`Microsoft.dotnet-openapi`를 설치하려면 다음 명령을 실행합니다.

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>추가

이 페이지의 명령 중 하나를 사용하여 OpenAPI 참조를 추가하면 다음과 유사한 `<OpenApiReference />` 요소가 *.csproj* 파일에 추가됩니다.

```xml
<OpenApiReference Include="openapi.json" />
```

생성된 클라이언트 코드를 앱에서 호출하려면 이전 참조가 필요합니다.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>파일 추가

#### <a name="options"></a>옵션

| 짧음 옵션| 긴 옵션| 설명 | 예제 |
|-------|------|-------|---------|
| -p|--updateProject | 작동할 프로젝트입니다. |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--code-generator| 참조에 적용할 코드 생성기입니다. 옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다. `--code-generator`가 지정되지 않으면 도구는 기본적으로 `NSwagCSharp`가 됩니다.|dotnet openapi add file .\OpenApi.json --code-generator
| -H|--help|도움말 정보를 표시합니다.|dotnet openapi add file --help|

#### <a name="arguments"></a>인수

|  인수  | 설명 | 예제 |
|-------------|-------------|---------|
| source-file | 참조를 만들 원본입니다. OpenAPI 파일이어야 합니다. |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>URL 추가

#### <a name="options"></a>옵션

| 짧음 옵션| 긴 옵션| 설명 | 예제 |
|-------|------|-------------|---------|
| -p|--updateProject | 작동할 프로젝트입니다. |dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--output-file | OpenAPI 파일의 로컬 복사본을 저장할 위치입니다. |dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--code-generator| 참조에 적용할 코드 생성기입니다. 옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다. |dotnet openapi add file .\OpenApi.json --code-generator
| -H|--help|도움말 정보를 표시합니다.|dotnet openapi add url --help|

#### <a name="arguments"></a>인수

|  인수  | 설명 | 예제 |
|-------------|-------------|---------|
| 원본-URL | 참조를 만들 원본입니다. URL이어야 합니다. |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>제거

*.csproj* 파일에서 지정된 파일 이름과 일치하는 OpenAPI 참조를 제거합니다. OpenAPI 참조가 제거되면 클라이언트가 생성되지 않습니다. 로컬 *.json* and *.yaml* 파일이 삭제됩니다.

### <a name="options"></a>옵션

| 짧음 옵션| 긴 옵션| 설명| 예제 |
|-------|------|------------|---------|
| -p|--updateProject | 작동할 프로젝트입니다. |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -H|--help|도움말 정보를 표시합니다.|dotnet openapi remove --help|

### <a name="arguments"></a>인수

|  인수  | 설명| 예제 |
| ------------|------------|---------|
| source-file | 참조를 제거할 속성입니다. |dotnet openapi remove *.\OpenAPI.json* |

## <a name="refresh"></a>새로 고침

다운로드 URL의 최신 콘텐츠를 사용하여 다운로드한 파일의 로컬 버전을 새로 고칩니다.

### <a name="options"></a>옵션

| 짧음 옵션| 긴 옵션| 설명 | 예제 |
|-------|------|-------------|---------|
| -p|--updateProject | 작동할 프로젝트입니다. | dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -H|--help|도움말 정보를 표시합니다.|dotnet openapi refresh --help|

### <a name="arguments"></a>인수

|  인수  | 설명 | 예제 |
| ------------|-------------|---------|
| 원본-URL | 참조를 새로 고칠 URL입니다. | dotnet openapi refresh `https://contoso.com/openapi.json` |
