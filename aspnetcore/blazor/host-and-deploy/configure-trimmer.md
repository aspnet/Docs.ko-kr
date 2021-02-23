---
title: ASP.NET Core Blazor용 트리머 구성
author: guardrex
description: Blazor 앱을 빌드할 때 IL(중간 언어) 링커(트리머)를 제어하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975219"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>ASP.NET Core Blazor용 트리머 구성

Blazor WebAssembly에서 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 트리밍을 수행하여 게시된 출력값의 크기를 줄입니다. 기본적으로 트리밍은 앱을 게시할 때 수행됩니다.

트리밍은 부정적인 영향을 미칠 수 있습니다. 리플렉션을 사용하는 앱에서 트리머는 대개 리플렉션에 필요한 형식을 런타임에 결정하지 못합니다. 리플렉션을 사용하는 앱을 트리밍하려면 앱이 종속된 패키지나 프레임워크 및 앱의 코드에서 리플렉션에 필요한 형식을 트리머가 알고 있어야 합니다. 또한 트리머는 런타임에 앱의 동적 동작에 반응할 수 없습니다. 트리밍된 앱이 배포된 후 올바르게 작동하도록 하려면 개발하는 동안 게시된 출력을 자주 테스트하세요.

트리머를 구성하려면 다음 주제에 대한 지침에 포함되어 있는 .NET 기본 사항 설명서에서 [트리밍 옵션](/dotnet/core/deploying/trimming-options) 문서를 참조하세요.

* 프로젝트 파일에서 `<PublishTrimmed>` 속성을 사용하여 전체 앱에 대해 트리밍을 사용하지 않도록 설정합니다.
* 적극적으로 사용되지 않는 IL을 트리머에서 삭제하는 방법을 제어합니다.
* 트리머가 특정 어셈블리를 트리밍하는 것을 중지합니다.
* 트리밍을 위한 “루트” 어셈블리.
* 프로젝트 파일에서 `<SuppressTrimAnalysisWarnings>` 속성을 `false`로 설정하여 반영된 형식에 대한 경고를 표시합니다.
* 기호 트리밍 및 디버거 지원을 제어합니다.
* 트리밍 프레임워크 라이브러리 기능에 대해 트리머 기능을 설정합니다.

## <a name="additional-resources"></a>추가 리소스

* [자체 포함 배포 및 실행 파일 트리밍](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
