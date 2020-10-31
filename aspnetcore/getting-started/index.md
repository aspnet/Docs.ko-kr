---
title: ASP.NET Core 시작
author: rick-anderson
description: ASP.NET Core를 사용하여 기본 Hello World 앱을 만들고 실행하는 간단한 자습서입니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057793"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>자습서: ASP.NET Core 시작

이 자습서에서는 .NET Core CLI를 사용하여 ASP.NET Core 웹앱을 만들고 실행하는 방법을 보여 줍니다.

다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 웹앱 프로젝트를 만듭니다.
> * 개발 인증서를 신뢰합니다.
> * 앱을 실행합니다.
> * Razor 페이지를 편집합니다.

최종적으로 로컬 머신에서 구동되는 웹앱을 실행할 수 있습니다.

![웹앱 홈페이지](_static/home-page.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>웹앱 프로젝트 만들기

명령 셸을 열고 다음 명령을 입력합니다.

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

이전 명령은

* 새 웹앱을 만듭니다.  
* `-o aspnetcoreapp` 매개 변수는 앱의 원본 파일을 사용하여 *aspnetcoreapp* 라는 이름의 디렉터리를 만듭니다.

### <a name="trust-the-development-certificate"></a>개발 인증서 신뢰

HTTPS 개발 인증서 신뢰:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

이전 명령으로 인해 다음 대화 상자가 표시됩니다.

![보안 경고 대화 상자](~/getting-started/_static/cert.png)

개발 인증서를 신뢰하는 데 동의하는 경우 **예** 를 선택합니다.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

이전 명령으로 인해 다음 메시지가 표시됩니다.

*HTTPS 개발 인증서를 신뢰해야 합니다. 인증서를 신뢰할 수 없는 경우*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'` 명령을 실행합니다.

이 명령은 시스템 키 집합에 인증서를 설치하기 위해 암호를 묻는 메시지를 표시할 수 있습니다. 개발 인증서를 신뢰하는 데 동의하는 경우 암호를 입력합니다.

# <a name="linux"></a>[Linux](#tab/linux)

HTTPS 개발 인증서를 신뢰하는 방법은 Linux 배포에 대한 설명서를 참조하세요.

---

자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.

## <a name="run-the-app"></a>앱 실행

다음 명령을 실행합니다.

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

명령 셸에서 앱이 시작되었음을 확인했으면 `https://localhost:5001`로 이동합니다.

## <a name="edit-a-no-locrazor-page"></a>Razor 페이지 편집

*Pages/Index.cshtml* 을 열고 다음에서 강조 표시된 영역처럼 페이지를 수정하고 저장합니다.

[!code-cshtml[](sample/index.cshtml?highlight=9)]

`https://localhost:5001`로 이동하여 페이지를 새로 고치고 변경 내용이 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * 웹앱 프로젝트를 만듭니다.
> * 개발 인증서를 신뢰합니다.
> * 프로젝트를 실행합니다.
> * 페이지를 수정합니다.

ASP.NET Core에 대해 자세히 알아보려면 소개에서 권장되는 학습 경로를 참조하세요.

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
