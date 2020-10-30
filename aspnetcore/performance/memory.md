---
title: ASP.NET Core의 메모리 관리 및 패턴
author: rick-anderson
description: ASP.NET Core에서 메모리를 관리 하는 방법 및 GC (가비지 수집기)의 작동 방식에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061485"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET Core의 메모리 관리 및 GC (가비지 수집)

[Sébastien Ros](https://github.com/sebastienros) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET 등의 관리 되는 프레임 워크 에서도 메모리 관리가 복잡 합니다. 메모리 문제를 분석 하 고 이해 하는 것은 어려울 수 있습니다. 이 문서의 내용은 다음과 같습니다.

* 는 많은 *메모리 누수로* 인해 발생 했으며 *GC가 작동 하지 않습니다* . 이러한 문제의 대부분은 .NET Core에서 메모리 소비가 어떻게 작동 하는지 이해 하거나 측정 방법을 이해 하지 못하는 경우에 발생 합니다.
* 문제가 있는 메모리 사용을 보여 주고 대체 방법을 제안 합니다.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>.NET Core에서 GC (가비지 수집)가 작동 하는 방식

GC는 각 세그먼트가 연속 되는 메모리 범위인 힙 세그먼트를 할당 합니다. 힙에 배치 된 개체는 3 세대 (0, 1 또는 2 개)로 분류 됩니다. 세대는 GC가 앱에서 더 이상 참조 하지 않는 관리 되는 개체의 메모리를 해제 하려고 시도 하는 빈도를 결정 합니다. 번호가 낮은 세대는 GC가 더 자주 있습니다.

개체는 수명에 따라 한 세대에서 다른 세대로 이동 됩니다. 개체가 더 오래 지속 되 면 더 높은 생성으로 이동 됩니다. 앞에서 설명한 것 처럼 더 높은 세대는 GC가 그다지 자주 발생 하지 않습니다. 수명이 짧은 개체는 항상 0 세대에 남아 있습니다. 예를 들어 웹 요청 수명 중에 참조 되는 개체는 수명이 짧습니다. 응용 프로그램 수준 [단일 항목](xref:fundamentals/dependency-injection#service-lifetimes) 일반적으로 2 세대로 마이그레이션됩니다.

ASP.NET Core 앱이 시작 되 면 GC는 다음과 같습니다.

* 초기 힙 세그먼트에 대 한 일부 메모리를 예약 합니다.
* 런타임이 로드 될 때 작은 메모리 부분을 커밋합니다.

위의 메모리 할당은 성능상의 이유로 수행 됩니다. 성능 혜택은 연속 메모리의 힙 세그먼트에서 제공 됩니다.

### <a name="call-gccollect"></a>GC를 호출 합니다. 모으려면

[GC를 호출 합니다. ](xref:System.GC.Collect*)명시적으로 수집:

* 프로덕션 ASP.NET Core 앱에서 수행 **하면 안 됩니다** .
* 는 메모리 누수를 조사할 때 유용 합니다.
* 조사할 때, GC가 메모리에서 모든 현 수 개체를 제거 하 여 메모리를 측정할 수 있는지 확인 합니다.

## <a name="analyzing-the-memory-usage-of-an-app"></a>앱의 메모리 사용 분석

전용 도구는 메모리 사용을 분석 하는 데 도움이 됩니다.

- 개체 참조 계산
- GC가 CPU 사용량에 미치는 영향 측정
- 각 세대에 사용 되는 메모리 공간 측정

다음 도구를 사용 하 여 메모리 사용을 분석 합니다.

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): 프로덕션 컴퓨터에서 사용할 수 있습니다.
* [Visual Studio 디버거를 사용 하지 않고 메모리 사용 분석](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio에서 프로필 메모리 사용량](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>메모리 문제 검색

작업 관리자를 사용 하 여 ASP.NET 앱에서 사용 하는 메모리의 양을 파악할 수 있습니다. 작업 관리자 메모리 값:

* ASP.NET 프로세스에서 사용 하는 메모리의 양을 나타냅니다.
* 응용 프로그램의 살아 있는 개체와 네이티브 메모리 사용량과 같은 기타 메모리 소비자를 포함 합니다.

작업 관리자 메모리 값이 무기한으로 늘어나고 평면화 되지 않으면 앱에 메모리 누수가 발생 합니다. 다음 섹션에서는 몇 가지 메모리 사용 패턴을 설명 하 고 설명 합니다.

## <a name="sample-display-memory-usage-app"></a>샘플 메모리 사용량 앱 표시

[Memoryleak 샘플 앱](https://github.com/sebastienros/memoryleak) 은 GitHub에서 사용할 수 있습니다. MemoryLeak 앱:

* 앱에 대 한 실시간 메모리 및 GC 데이터를 수집 하는 진단 컨트롤러를 포함 합니다.
* 에는 메모리 및 GC 데이터를 표시 하는 인덱스 페이지가 있습니다. 인덱스 페이지는 매 초 마다 새로 고쳐집니다.
* 다양 한 메모리 로드 패턴을 제공 하는 API 컨트롤러를 포함 합니다.
* 는 지원 되는 도구가 아닙니다. 그러나 ASP.NET Core 앱의 메모리 사용 패턴을 표시 하는 데 사용할 수 있습니다.

MemoryLeak를 실행 합니다. 할당 된 메모리는 GC가 발생할 때까지 천천히 늘어납니다. 도구에서 사용자 지정 개체를 할당 하 여 데이터를 캡처하도록 하므로 메모리가 늘어납니다. 다음 이미지는 Gen 0 GC가 발생할 때 MemoryLeak 인덱스 페이지를 보여 줍니다. API 컨트롤러의 API 끝점이 호출 되지 않았기 때문에 차트에 0 개 RPS (초당 요청 수)가 표시 됩니다.

![이전 차트](memory/_static/0RPS.png)

차트에는 메모리 사용량에 대 한 두 개의 값이 표시 됩니다.

- 할당 됨: 관리 되는 개체가 차지 하는 메모리 양
- [작업 집합](/windows/win32/memory/working-set): 현재 실제 메모리에 상주 하는 프로세스의 가상 주소 공간에 있는 페이지 집합입니다. 표시 되는 작업 집합은 작업 관리자에 표시 되는 것과 동일한 값입니다.

### <a name="transient-objects"></a>임시 개체

다음 API는 10kb 문자열 인스턴스를 만들고이를 클라이언트에 반환 합니다. 각 요청에서 새 개체는 메모리에 할당 되 고 응답에 기록 됩니다. 문자열은 .NET에서 u t f-16 자로 저장 되므로 각 문자는 메모리에서 2 바이트를 사용 합니다.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

다음 그래프는에서 상대적으로 적은 로드를 사용 하 여 메모리 할당이 GC의 영향을 받는 방법을 보여 주기 위해 생성 됩니다.

![이전 차트](memory/_static/bigstring.png)

위의 차트는 다음을 보여 줍니다.

* 4K RPS (초당 요청 수)
* 0 세대 GC 컬렉션은 2 초 마다 발생 합니다.
* 작업 집합은 약 500 MB의 상수입니다.
* CPU는 12%입니다.
* 메모리 사용량과 릴리스 (GC를 통해)는 안정적입니다.

다음 차트는 컴퓨터에서 처리할 수 있는 최대 처리량으로 수행 됩니다.

![이전 차트](memory/_static/bigstring2.png)

위의 차트는 다음을 보여 줍니다.

* 22K RPS
* 0 세대 GC 컬렉션은 초당 여러 번 발생 합니다.
* 앱이 초당 더 많은 메모리를 할당 했기 때문에 1 세대 수집이 트리거됩니다.
* 작업 집합은 약 500 MB의 상수입니다.
* CPU는 33%입니다.
* 메모리 사용량과 릴리스 (GC를 통해)는 안정적입니다.
* CPU (33%) 과도 하 게 사용 되지 않으므로 가비지 수집은 많은 수의 할당을 유지할 수 있습니다.

### <a name="workstation-gc-vs-server-gc"></a>워크스테이션 GC와 서버 GC 비교

.NET 가비지 수집기에는 다음과 같은 두 가지 모드가 있습니다.

* **워크스테이션 GC** : 데스크톱에 최적화 되어 있습니다.
* **서버 GC** . ASP.NET Core 앱에 대 한 기본 GC입니다. 서버에 최적화 되어 있습니다.

GC 모드는 프로젝트 파일이 나 게시 된 앱의 파일에 있는 *runtimeconfig.js* 에서 명시적으로 설정할 수 있습니다. 다음 태그는 `ServerGarbageCollection` 프로젝트 파일의 설정을 보여 줍니다.

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

`ServerGarbageCollection`프로젝트 파일에서 변경 하려면 앱을 다시 빌드해야 합니다.

**참고:** 단일 코어가 있는 컴퓨터에서는 서버 가비지 수집을 사용할 수 **없습니다** . 자세한 내용은 <xref:System.Runtime.GCSettings.IsServerGC>를 참조하세요.

다음 이미지는 워크스테이션 GC를 사용 하는 5K RPS 아래의 메모리 프로필을 보여 줍니다.

![이전 차트](memory/_static/workstation.png)

이 차트와 서버 버전의 차이점은 중요 합니다.

- 작업 집합이 500 MB에서 70 MB로 줄어듭니다.
- GC는 2 초 마다 초당 0 세대 컬렉션을 여러 번 생성 합니다.
- GC는 300 m b에서 10mb로 삭제 됩니다.

일반적인 웹 서버 환경에서 CPU 사용량은 메모리 보다 더 중요 하므로 서버 GC가 더 효율적입니다. 메모리 사용률이 높고 CPU 사용량이 상대적으로 낮으면 워크스테이션 GC가 더 성능이 향상 될 수 있습니다. 예를 들어 메모리가 부족 한 여러 웹 앱을 호스트 하는 고밀도가 있습니다.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC using Docker and small containers(Docker 및 소형 컨테이너를 사용하는 GC)

여러 컨테이너 화 된 apps가 한 컴퓨터에서 실행 되는 경우 워크스테이션 GC가 서버 GC 보다 더 미리 추가 될 수 있습니다. 자세한 내용은 [작은 컨테이너에서 서버 gc를 사용 하 여 실행](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 및 [작은 컨테이너 시나리오에서 서버 gc로 실행 1 부 – GC 힙의 하드 제한](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)을 참조 하세요.

### <a name="persistent-object-references"></a>영구 개체 참조

GC는 참조 되는 개체를 해제할 수 없습니다. 참조 되지만 더 이상 필요 하지 않은 개체는 메모리 누수가 발생 합니다. 앱에서 자주 개체를 할당 하 고 더 이상 필요 하지 않은 개체를 해제 하지 못한 경우 시간이 지남에 따라 메모리 사용이 증가 합니다.

다음 API는 10kb 문자열 인스턴스를 만들고이를 클라이언트에 반환 합니다. 이전 예제와의 차이점은이 인스턴스가 정적 멤버에 의해 참조 되는 것입니다. 즉, 컬렉션에 사용할 수 없습니다.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

위의 코드는

* 는 일반적인 메모리 누수의 한 예입니다.
* 를 자주 호출 하면 예외가 발생 하 여 프로세스가 중단 될 때까지 응용 프로그램 메모리가 늘어납니다 `OutOfMemory` .

![이전 차트](memory/_static/eternal.png)

이전 이미지에서 다음을 수행 합니다.

* 끝점의 부하 테스트 `/api/staticstring` 를 수행 하면 메모리가 선형적으로 증가 합니다.
* GC는 2 세대 컬렉션을 호출 하 여 메모리 압력이 증가 함에 따라 메모리를 해제 하려고 시도 합니다.
* GC에서 누수 된 메모리를 해제할 수 없습니다. 할당 된 작업과 작업 집합이 시간에 의해 증가 합니다.

캐싱과 같은 일부 시나리오에서는 메모리가 중이 강제로 해제 될 때까지 개체 참조를 유지 해야 합니다. <xref:System.WeakReference>이 유형의 캐싱 코드에 클래스를 사용할 수 있습니다. `WeakReference`개체는 memory pressures에서 수집 됩니다. 의 기본 구현에서는를 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 사용 `WeakReference` 합니다.

### <a name="native-memory"></a>기본 메모리

일부 .NET Core 개체는 네이티브 메모리를 사용 합니다. Native memory는 GC에서 수집할 수 **없습니다** . 네이티브 메모리를 사용 하는 .NET 개체는 네이티브 코드를 사용 하 여 해제 해야 합니다.

.NET에서는 <xref:System.IDisposable> 개발자가 네이티브 메모리를 릴리스할 수 있는 인터페이스를 제공 합니다. <xref:System.IDisposable.Dispose*>가 호출 되지 않더라도 올바르게 구현 `Dispose` 된 클래스는 [종료 자가](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 실행 될 때를 호출 합니다.

다음 코드를 생각해보세요.

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

실제 [Fileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 는 관리 되는 클래스 이므로 인스턴스가 요청 끝에서 수집 됩니다.

다음 이미지는 API를 계속 호출 하는 동안 메모리 프로필을 보여 줍니다 `fileprovider` .

![이전 차트](memory/_static/fileprovider.png)

위의 차트는 메모리 사용이 늘어나는 것을 유지 하므로이 클래스의 구현과 관련 된 명확한 문제를 보여 줍니다. 이 문제는 [이 문제](https://github.com/dotnet/aspnetcore/issues/3110)에서 추적 되는 알려진 문제입니다.

사용자 코드에서 다음 중 하나를 사용 하 여 동일한 누수를 발생 시킬 수 있습니다.

* 클래스를 올바르게 해제 하지 않습니다.
* `Dispose`삭제 해야 하는 종속 개체의 메서드를 호출 하지 않습니다.

### <a name="large-objects-heap"></a>Large objects 힙

메모리를 자주 할당/해제 하는 주기는 메모리를 조각화 할 수 있습니다. 특히 대량의 메모리를 할당 하는 경우입니다. 개체는 인접 한 메모리 블록에 할당 됩니다. 조각화를 완화 하기 위해 GC가 메모리를 해제 하는 경우 조각 모음을 시도 합니다. 이 프로세스를 **압축** 이라고 합니다. 압축에는 개체 이동이 포함 됩니다. 개체를 이동 하면 성능이 저하 됩니다. 이러한 이유로, GC는 LOH ( [large object heap](/dotnet/standard/garbage-collection/large-object-heap) ) 라는 _커다란_ 개체에 대 한 특수 메모리 영역을 만듭니다. 85000 바이트 (약 83 KB) 보다 큰 개체는 다음과 같습니다.

* LOH에 배치 됩니다.
* 압축 되지 않습니다.
* 2 세대 Gc 중 수집 됩니다.

LOH가 가득 차면 GC가 2 세대 수집을 트리거합니다. 2 세대 컬렉션:

* 기본적으로 속도가 느립니다.
* 다른 모든 세대에서 수집을 트리거하는 비용도 추가로 발생 합니다.

다음 코드는 LOH를 즉시 압축 합니다.

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>LOH를 압축 하는 방법에 대 한 자세한 내용은을 참조 하세요.

.NET Core 3.0 이상 버전을 사용 하는 컨테이너에서는 LOH가 자동으로 압축 됩니다.

이 동작을 설명 하는 API는 다음과 같습니다.

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

다음 차트는 `/api/loh/84975` 최대 부하 상태에서 끝점을 호출 하는 메모리 프로필을 보여 줍니다.

![이전 차트](memory/_static/loh1.png)

다음 차트에서는 끝점을 호출 하는 메모리 프로필 `/api/loh/84976` 을 보여 주며 *바이트를 하나 더* 할당 합니다.

![이전 차트](memory/_static/loh2.png)

참고: `byte[]` 구조에 오버 헤드 바이트가 있습니다. 84976 바이트가 85000 제한을 트리거하는 이유입니다.

위의 두 차트 비교:

* 작업 집합은 두 시나리오 (약 450 MB)에 대해 유사 합니다.
* LOH 요청 (84975 바이트)은 대부분 0 세대 컬렉션을 보여 줍니다.
* LOH 초과 요청은 상수 2 세대 컬렉션을 생성 합니다. 2 세대 컬렉션은 비용이 많이 듭니다. 더 많은 CPU가 필요 하 고 처리량이 거의 50% 감소 합니다.

임시 큰 개체는 gen2 Gc를 야기 하므로 특히 문제가 됩니다.

성능을 최대화 하려면 많은 개체 사용을 최소화 해야 합니다. 가능 하면 크게 개체를 분할 합니다. 예를 들어 ASP.NET Core의 [응답 캐싱](xref:performance/caching/response) 미들웨어는 캐시 항목을 85000 바이트 미만 블록으로 분할 합니다.

다음 링크에서는 개체를 LOH 제한으로 유지 하는 ASP.NET Core 방법을 보여 줍니다.

* [ResponseCaching/스트림/StreamUtilities .cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

자세한 내용은 다음을 참조하십시오.

* [대량 개체 힙 검사](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Large object 힙](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

를 잘못 사용 <xref:System.Net.Http.HttpClient> 하면 리소스 누수가 발생할 수 있습니다. 데이터베이스 연결, 소켓, 파일 핸들 등의 시스템 리소스:

* 메모리 보다 부족 합니다.
* 는 메모리 보다 누수 될 때 더 문제가 됩니다.

숙련 된 .NET 개발자는을 <xref:System.IDisposable.Dispose*> 구현 하는 개체에 대해를 호출 해야 <xref:System.IDisposable> 합니다. 일반적으로를 구현 하는 개체를 삭제 하지 않으면 `IDisposable` 메모리 누수가 발생 하거나 시스템 리소스가 누수 됩니다.

`HttpClient``IDisposable`는를 구현 하지만 모든 호출에서 삭제 하면 **안 됩니다** . 대신를 `HttpClient` 다시 사용 해야 합니다.

다음 끝점은 모든 요청에 새 인스턴스를 만들고 삭제 합니다  `HttpClient` .

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

로드 아래에 다음 오류 메시지가 기록 됩니다.

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

`HttpClient`인스턴스가 삭제 된 경우에도 운영 체제에서 실제 네트워크 연결을 해제 하는 데 약간의 시간이 걸립니다. 계속 해 서 새 연결을 만들면 _포트 고갈_ 가 발생 합니다. 각 클라이언트 연결에는 자체 클라이언트 포트가 필요 합니다.

포트 소모를 방지 하는 한 가지 방법은 동일한 인스턴스를 다시 사용 하는 것입니다 `HttpClient` .

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`앱이 중지 되 면 인스턴스가 해제 됩니다. 이 예제에서는 각 사용 후 삭제 가능한 모든 리소스를 삭제 해야 함을 보여 줍니다.

인스턴스의 수명을 처리 하는 더 좋은 방법은 다음을 참조 하십시오 `HttpClient` .

* [HttpClient 및 수명 관리](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [HTTPClient 팩터리 블로그](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>개체 풀링

이전 예제에서는 `HttpClient` 인스턴스를 정적으로 설정 하 고 모든 요청에서 다시 사용할 수 있는 방법을 보여 주었습니다. 다시 사용 하면 리소스가 부족 하지 않습니다.

개체 풀링:

* 에서는 재사용 패턴을 사용 합니다.
* 는 만드는 데 비용이 많이 드는 개체를 위해 설계 되었습니다.

풀은 스레드 간에 예약 및 해제할 수 있는 미리 초기화 된 개체의 컬렉션입니다. 풀은 제한, 미리 정의 된 크기 또는 증가율과 같은 할당 규칙을 정의할 수 있습니다.

NuGet 패키지의 경우 이러한 풀을 관리 하는 데 도움이 되는 클래스가 포함 되어 [있습니다.](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)

다음 API 끝점은 `byte` 각 요청에서 임의 숫자로 채워진 버퍼를 인스턴스화합니다.

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

다음 차트는 중간 부하로 이전 API를 호출 하는 것을 보여 주는 것입니다.

![이전 차트](memory/_static/array.png)

위의 차트에서 0 세대 수집은 초당 한 번 수행 됩니다.

이전 코드는 `byte` [ArrayPool \<T> ](xref:System.Buffers.ArrayPool`1)를 사용 하 여 버퍼를 풀링 하 여 최적화할 수 있습니다. 정적 인스턴스는 요청에서 재사용 됩니다.

이 방법의 다른 기능은 풀링된 개체가 API에서 반환 된다는 것입니다. 이는 다음을 의미합니다.

* 메서드에서 반환 하는 즉시 개체는 컨트롤에서 제외 됩니다.
* 개체를 해제할 수 없습니다.

개체의 삭제를 설정 하려면 다음을 수행 합니다.

* 삭제 가능한 개체에 풀링된 배열을 캡슐화 합니다.
* 풀링된 개체를 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)로 등록 합니다.

`RegisterForDispose` 는 `Dispose` HTTP 요청이 완료 된 경우에만 해제 되도록 대상 개체에 대 한 호출을 처리 합니다.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

풀링된 버전이 아닌 버전과 동일한 부하를 적용 하면 다음 차트가 생성 됩니다.

![이전 차트](memory/_static/pooledarray.png)

주요 차이점은 바이트를 할당 하는 것이 고, 결과적으로 0 세대 수집이 훨씬 더 적기 때문입니다.

## <a name="additional-resources"></a>추가 리소스

* [가비지 수집](/dotnet/standard/garbage-collection/)
* [동시성 시각화 도우미를 사용 하 여 다양 한 GC 모드 이해](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [대량 개체 힙 검사](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Large object 힙](/dotnet/standard/garbage-collection/large-object-heap)