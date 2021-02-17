---
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
ms.openlocfilehash: e8791aee6dd6a16efc30d94133c197ff03333cc1
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551617"
---
<span data-ttu-id="318a5-101">스캐폴딩 오류가 발생하면 TFM(대상 프레임워크 모니커)이 프로젝트 파일의 NuGet 패키지 버전과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="318a5-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="318a5-102">예를 들어 다음 프로젝트 파일의 .NET Core 및 나열된 NuGet 패키지에는 버전 3.1이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="318a5-102">For example, the following project file contains the version 3.1 for .NET Core and the listed NuGet packages:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.0" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.0" />
  </ItemGroup>

</Project>
```
