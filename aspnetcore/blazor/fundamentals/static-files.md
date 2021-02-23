---
title: ASP.NET Core Blazor 정적 파일
author: guardrex
description: Blazor 앱에 대한 정적 파일을 구성하고 관리하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100109960"
---
# <a name="aspnet-core-blazor-static-files"></a>ASP.NET Core Blazor 정적 파일

‘이 문서는 Blazor Server에 적용됩니다.’

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>를 사용하여 추가 파일 매핑을 만들거나 다른 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 구성하려면 다음 방법 중 **하나** 를 사용합니다. 다음 예제에서 `{EXTENSION}` 자리 표시자는 파일 확장명이고 `{CONTENT TYPE}` 자리 표시자는 콘텐츠 형식입니다.

* <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 `Startup.ConfigureServices`(`Startup.cs`)에서 [DI(종속성 주입)](xref:blazor/fundamentals/dependency-injection)를 통해 옵션을 구성합니다.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  이 방법에서는 `blazor.server.js`를 처리하는 데 사용되는 것과 동일한 파일 공급자를 구성하므로 사용자 지정 구성이 `blazor.server.js` 처리를 방해하지 않는지 확인해야 합니다. 예를 들어 `provider.Mappings.Remove(".js")`를 통해 공급자를 구성하여 JavaScript 파일에 대한 매핑을 제거하지 마세요.

* `Startup.Configure`(`Startup.cs`)에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>에 대한 두 호출을 사용합니다.
  * <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 첫 번째 호출에서 사용자 지정 파일 공급자를 구성합니다.
  * 두 번째 미들웨어는 Blazor 프레임워크에서 제공하는 기본 정적 파일 구성을 사용하는 `blazor.server.js`를 처리합니다.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>을 사용해 사용자 지정 정적 파일 미들웨어를 실행하여 `_framework/blazor.server.js` 처리를 방해하지 않게 할 수 있습니다.

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
