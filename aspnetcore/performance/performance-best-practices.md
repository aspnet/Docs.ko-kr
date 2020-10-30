---
title: ASP.NET Core 성능 모범 사례
author: mjrousos
description: ASP.NET Core 앱의 성능을 향상 하 고 일반적인 성능 문제를 방지 하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: a3fc398569fafefc0b4634e80433a5d4e0e1b4ff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061004"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core 성능 모범 사례

[Mike Rousos](https://github.com/mjrousos) 작성

이 문서에서는 ASP.NET Core의 성능 모범 사례에 대 한 지침을 제공 합니다.

## <a name="cache-aggressively"></a>적극적으로 캐시

캐싱은이 문서의 여러 부분에서 설명 합니다. 자세한 내용은 <xref:performance/caching/response>를 참조하세요.

## <a name="understand-hot-code-paths"></a>핫 코드 경로 이해

이 문서에서 *핫 코드 경로* 는 자주 호출 되는 코드 경로로 정의 되며 대부분의 실행 시간이 발생 합니다. 핫 코드 경로는 일반적으로 앱 확장 및 성능을 제한 하며이 문서의 여러 부분에서 설명 합니다.

## <a name="avoid-blocking-calls"></a>호출 차단 방지

ASP.NET Core 앱은 여러 요청을 동시에 처리 하도록 설계 해야 합니다. 비동기 Api를 사용 하면 작은 스레드 풀에서 차단 호출을 기다리지 않고 수천 개의 동시 요청을 처리할 수 있습니다. 장기 실행 동기 작업이 완료 되기를 기다리지 않고 스레드가 다른 요청에서 작동할 수 있습니다.

ASP.NET Core 앱의 일반적인 성능 문제는 비동기 일 수 있는 호출을 차단 하는 것입니다. 많은 동기 차단 호출이 [스레드 풀 고갈](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) 및 저하 된 응답 시간을 초래 합니다.

안 **함** :

* [작업. 대기](/dotnet/api/system.threading.tasks.task.wait) 또는 [작업](/dotnet/api/system.threading.tasks.task-1.result)을 호출 하 여 비동기 실행을 차단 합니다.
* 공용 코드 경로에서 잠금을 가져옵니다. ASP.NET Core 앱은 코드를 병렬로 실행 하도록 설계 될 때 가장 효율적입니다.
* 작업을 호출 [합니다 .를 실행](/dotnet/api/system.threading.tasks.task.run) 하 고 즉시 기다립니다. ASP.NET Core는 정상적인 스레드 풀 스레드에서 이미 앱 코드를 실행 하 고 있으므로 작업을 호출 하면 추가 불필요 한 스레드 풀 예약이 발생 합니다. 예약 된 코드에서 스레드를 차단 하는 경우에도 작업. 실행은이를 방지 하지 않습니다.

**Do** :

* [핫 코드 경로](#understand-hot-code-paths) 를 비동기식으로 만듭니다.
* 비동기 API를 사용할 수 있는 경우 데이터 액세스, i/o 및 장기 실행 작업 Api를 비동기적으로 호출 합니다. 작업을 실행 **하 여 동기** API를 비동기식으로 만듭니다 [.](/dotnet/api/system.threading.tasks.task.run)
* 컨트롤러/ Razor 페이지 작업을 비동기식으로 만듭니다. 비동기 [/](/dotnet/csharp/programming-guide/concepts/async/) 대기 패턴을 활용 하기 위해 전체 호출 스택은 비동기입니다.

[Perfview](https://github.com/Microsoft/perfview)와 같은 프로파일러를 사용 하 여 [스레드 풀](/windows/desktop/procthread/thread-pools)에 자주 추가 되는 스레드를 찾을 수 있습니다. 이벤트는 스레드 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 풀에 추가 된 스레드를 나타냅니다. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>IEnumerable \<T> 또는 IAsyncEnumerable을 반환 합니다.\<T>

작업에서 반환 하면 serializer에의 `IEnumerable<T>` 한 동기 컬렉션 반복이 발생 합니다. 그 결과는 호출 차단이며 스레드 풀 고갈의 가능성도 있습니다. 동기 열거를 방지 하려면 `ToListAsync` 열거 가능를 반환 하기 전에를 사용 합니다.

ASP.NET Core 3.0부터는를 `IAsyncEnumerable<T>` 비동기적으로 열거 하는 대신 사용할 수 있습니다 `IEnumerable<T>` . 자세한 내용은 [컨트롤러 작업 반환 형식](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)을 참조 하세요.

## <a name="minimize-large-object-allocations"></a>대량 개체 할당 최소화

[.Net Core 가비지 수집기](/dotnet/standard/garbage-collection/) 는 ASP.NET Core 앱에서 메모리의 할당 및 해제를 자동으로 관리 합니다. 자동 가비지 수집은 일반적으로 개발자가 메모리 해제 방법 또는 시기에 대해 걱정할 필요가 없음을 의미 합니다. 그러나 참조 되지 않은 개체를 정리 하는 경우 CPU 시간이 걸리므로 개발자는 [핫 코드 경로](#understand-hot-code-paths)에서 개체 할당을 최소화 해야 합니다. 가비지 수집은 큰 개체 (> 85 K 바이트)에서 특히 비용이 많이 듭니다. Large 개체는 [large object 힙에](/dotnet/standard/garbage-collection/large-object-heap) 저장 되며 정리 하려면 전체 (2 세대) 가비지 수집이 필요 합니다. 0 세대 및 1 세대 컬렉션과 달리 2 세대 수집에는 앱 실행을 임시로 일시 중단 해야 합니다. 자주 큰 개체를 할당 및 할당 취소 하면 성능이 저하 될 수 있습니다.

권장 사항:

* 자주 사용 되는 많은 개체를 캐싱하는 **것이 좋습니다** . 큰 개체를 캐시 하면 비용이 많이 드는 할당이 방지 됩니다.
* [ArrayPool \<T>](/dotnet/api/system.buffers.arraypool-1) 를 사용 하 여 대량 배열을 저장 함으로써 풀 버퍼를 **수행** 합니다.
* [핫 코드 경로](#understand-hot-code-paths)에 단기 수명이 많은 개체를 할당 **하지 마세요** .

[Perfview](https://github.com/Microsoft/perfview) 및 검사에서 GC (가비지 수집) 통계를 검토 하 여 위와 같은 메모리 문제를 진단할 수 있습니다.

* 가비지 수집 일시 중지 시간입니다.
* 가비지 수집에 소요 되는 프로세서 시간의 비율입니다.
* 0 세대, 1, 2 세대의 가비지 컬렉션 수입니다.

자세한 내용은 [가비지 수집 및 성능](/dotnet/standard/garbage-collection/performance)을 참조 하세요.

## <a name="optimize-data-access-and-io"></a>데이터 액세스 및 i/o 최적화

데이터 저장소 및 다른 원격 서비스와의 상호 작용은 종종 ASP.NET Core 앱의 가장 느린 부분입니다. 데이터를 효율적으로 읽고 쓰는 것이 좋은 성능을 위해 중요 합니다.

권장 사항:

* 모든 데이터 액세스 Api를 비동기적 **으로 호출 합니다** .
* 필요한 것 보다 더 많은 데이터를 검색 **하지** 않습니다. 현재 HTTP 요청에 필요한 데이터만 반환 하는 쿼리를 작성 합니다.
* 약간 오래 된 데이터를 사용할 수 있는 경우 데이터베이스 또는 원격 서비스에서 검색 되는 자주 액세스 하는 데이터를 캐시 하 **는 것이 좋습니다.** 시나리오에 따라 [Memorycache](xref:performance/caching/memory) 또는 [microsoft.web.distributedcache](xref:performance/caching/distributed)를 사용 합니다. 자세한 내용은 <xref:performance/caching/response>를 참조하세요.
* 네트워크 왕복 **을 최소화 합니다** . 목표는 여러 호출이 아닌 단일 호출에서 필요한 데이터를 검색 하는 것입니다.
* 읽기 전용 용도로 데이터에 액세스할 때 Entity Framework Core에서 [추적 안 함 쿼리](/ef/core/querying/tracking#no-tracking-queries) **를 사용 합니다** . EF Core 추적 되지 않는 쿼리 결과를 보다 효율적으로 반환할 수 있습니다.
* LINQ 쿼리 (예:, 또는 문 포함) **를 필터링 하** 고 집계 `.Where` 하 여 `.Select` `.Sum` 데이터베이스에서 필터링을 수행 하도록 합니다.
* EF Core에서 클라이언트의 일부 쿼리 연산자를 확인 하므로 비효율적인 쿼리 **실행이 발생할** 수 있습니다. 자세한 내용은 [클라이언트 평가 성능 문제](/ef/core/querying/client-eval#client-evaluation-performance-issues)를 참조 하세요.
* 컬렉션에 대해 프로젝션 쿼리를 사용 **하지 마십시오. 이렇게** 하면 "N + 1" SQL 쿼리가 실행 될 수 있습니다. 자세한 내용은 [상관 하위 쿼리 최적화](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)를 참조 하세요.

대규모 앱의 성능을 향상 시킬 수 있는 방법에 대해서는 [EF High 성능](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 을 참조 하세요.

* [DbContext 풀링](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [명시적으로 컴파일된 쿼리](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

코드 베이스를 커밋하기 전에 이전 고성능 방법의 영향을 측정 하는 것이 좋습니다. 컴파일된 쿼리의 추가적인 복잡성은 성능 향상을 정당화 하지 못할 수 있습니다.

[Application Insights](/azure/application-insights/app-insights-overview) 또는 프로 파일링 도구를 사용 하 여 데이터에 액세스 하는 데 걸린 시간을 검토 하 여 쿼리 문제를 검색할 수 있습니다. 또한 대부분의 데이터베이스는 자주 실행 되는 쿼리와 관련 하 여 통계를 사용할 수 있도록 합니다.

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory를 사용 하 여 HTTP 연결 풀

[Httpclient](/dotnet/api/system.net.http.httpclient) 는 인터페이스를 구현 하지만 `IDisposable` 다시 사용 하도록 설계 되었습니다. 닫힌 `HttpClient` 인스턴스 `TIME_WAIT` 는 짧은 시간 동안 소켓을 열린 상태로 둡니다. 개체를 만들고 삭제 하는 코드 경로가 `HttpClient` 자주 사용 되는 경우 앱에서 사용 가능한 소켓이 고갈 될 수 있습니다. [Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 는이 문제에 대 한 솔루션으로 ASP.NET Core 2.1에서 도입 되었습니다. 성능 및 안정성을 최적화 하기 위해 풀링 HTTP 연결을 처리 합니다.

권장 사항:

* 인스턴스를 직접 만들고 삭제 **하지 마십시오** `HttpClient` .
* [Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) **를 사용 하 여** 인스턴스를 검색 `HttpClient` 합니다. 자세한 내용은 [HttpClientFactory를 사용 하 여 복원 력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)을 참조 하세요.

## <a name="keep-common-code-paths-fast"></a>자주 공용 코드 경로 유지

모든 코드를 빠르게 만들려고 합니다. 자주 호출 되는 코드 경로를 최적화 하는 것이 가장 중요 합니다. 여기에는 다음이 포함됩니다.

* 앱의 요청 처리 파이프라인에 있는 미들웨어 구성 요소, 특히 미들웨어는 파이프라인 초기에 실행 됩니다. 이러한 구성 요소는 성능에 큰 영향을 줍니다.
* 요청 마다 또는 요청 마다 여러 번 실행 되는 코드입니다. 예를 들어 사용자 지정 로깅, 권한 부여 처리기 또는 임시 서비스 초기화가 있습니다.

권장 사항:

* 장기 실행 작업에는 사용자 지정 미들웨어 구성 요소를 사용 **하지 마십시오** .
* [Visual Studio 진단 도구](/visualstudio/profiling/profiling-feature-tour) 또는 [perfview](https://github.com/Microsoft/perfview))와 같은 성능 프로 파일링 도구 **를 사용 하 여** [핫 코드 경로](#understand-hot-code-paths)를 식별 합니다.

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP 요청 외부에서 장기 실행 작업 완료

ASP.NET Core 앱에 대 한 대부분의 요청은 컨트롤러 또는 페이지 모델에서 필요한 서비스를 호출 하 고 HTTP 응답을 반환 하 여 처리할 수 있습니다. 장기 실행 작업을 포함 하는 일부 요청의 경우 전체 요청-응답 프로세스를 비동기식으로 만드는 것이 좋습니다.

권장 사항:

* 일반 HTTP 요청 처리의 일부로 장기 실행 작업이 완료 될 때까지 기다리지 **마세요** .
* [백그라운드 서비스](xref:fundamentals/host/hosted-services) 를 사용 하 여 장기 실행 요청을 처리 하거나 [Azure 함수](/azure/azure-functions/)를 사용 하 여 out-of-process를 처리 하는 **것이 좋습니다** . Out-of-process 작업을 완료 하는 작업은 특히 CPU를 많이 사용 하는 작업에 유용 합니다.
* 와 같은 실시간 통신 옵션 **을 사용** 하 여 [SignalR](xref:signalr/introduction) 비동기적으로 클라이언트와 통신 합니다.

## <a name="minify-client-assets"></a>클라이언트 자산 축소

복잡 한 프런트 엔드가 있는 ASP.NET Core 앱은 종종 많은 JavaScript, CSS 또는 이미지 파일을 제공 합니다. 초기 로드 요청의 성능은 다음과 같은 방법으로 향상 시킬 수 있습니다.

* 번들은 여러 파일을 하나로 결합 합니다.
* 축소-공백과 주석을 제거 하 여 파일 크기를 줄입니다.

권장 사항:

* 번들 및 축소 클라이언트 자산에 대 한 ASP.NET Core [기본 제공 지원을](xref:client-side/bundling-and-minification) **사용 합니다** .
* 복합 클라이언트 자산 관리를 위해 다른 타사 도구 (예: [Webpack](https://webpack.js.org/)) **를 고려 합니다** .

## <a name="compress-responses"></a>응답 압축

 응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다. 페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다. 자세한 내용은 [응답 압축](xref:performance/response-compression)을 참조 하세요.

## <a name="use-the-latest-aspnet-core-release"></a>최신 ASP.NET Core 릴리스 사용

ASP.NET Core의 새 릴리스에는 성능 향상이 포함 되어 있습니다. .NET Core 및 ASP.NET Core의 최적화는 일반적으로 최신 버전이 이전 버전을 내지만 것을 의미 합니다. 예를 들어 .NET Core 2.1에는 컴파일된 정규식에 대 한 지원과 [범위 \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)에서의 benefitted 추가 되었습니다. ASP.NET Core 2.2에는 HTTP/2에 대 한 지원이 추가 되었습니다. [ASP.NET Core 3.0](xref:aspnetcore-3.0) 은 메모리 사용을 줄이고 처리량을 향상 시키는 많은 향상 된 기능을 추가 합니다. 성능이 우선 되는 경우 ASP.NET Core의 현재 버전으로 업그레이드 하는 것이 좋습니다.

## <a name="minimize-exceptions"></a>예외 최소화

예외는 드물게 발생 합니다. 예외 throw 및 catch는 다른 코드 흐름 패턴을 기준으로 속도가 느립니다. 따라서 일반적인 프로그램 흐름을 제어 하는 데는 예외를 사용 하지 않아야 합니다.

권장 사항:

* 특히 [핫 코드 경로](#understand-hot-code-paths)에서 일반적인 프로그램 흐름의 수단으로 throw 또는 catch 예외를 사용 **하지 마십시오** .
* 응용 프로그램에 논리 **를 포함 하** 여 예외를 발생 시키는 조건을 검색 하 고 처리 합니다.
* 비정상적인 또는 예기치 않은 조건에 대 한 예외 **를 throw 하거나** catch 합니다.

Application Insights와 같은 앱 진단 도구는 응용 프로그램에서 성능에 영향을 줄 수 있는 일반적인 예외를 식별 하는 데 도움이 될 수 있습니다.

## <a name="performance-and-reliability"></a>성능 및 안정성

다음 섹션에서는 성능 팁과 알려진 안정성 문제 및 해결 방법을 제공 합니다.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/Httpresponse.cache 본문에서 동기 읽기 또는 쓰기 방지

ASP.NET Core의 모든 i/o는 비동기입니다. 서버는 `Stream` 동기 및 비동기 오버 로드를 모두 포함 하는 인터페이스를 구현 합니다. 스레드 풀 스레드를 차단 하지 않도록 비동기를 사용 하는 것이 좋습니다. 스레드를 차단 하면 스레드 풀이 고갈 될 수 있습니다.

**이 작업을 수행 하지 마십시오.** 다음 예제에서는를 사용 합니다 <xref:System.IO.StreamReader.ReadToEnd*> . 결과를 대기 하는 현재 스레드를 차단 합니다. 이는 [async를 통한 동기화](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)의 예입니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

위의 코드에서는 `Get` 전체 HTTP 요청 본문을 메모리에 동기적으로 읽습니다. 클라이언트에 느린 업로드가 있으면 앱이 비동기를 통해 동기화 됩니다. Kestrel은 동기 읽기를 지원 **하지** 않기 때문에 앱은 async를 통해 동기화 됩니다.

**다음 작업을 수행 합니다.** 다음 예제에서는를 사용 하 <xref:System.IO.StreamReader.ReadToEndAsync*> 고 읽는 동안 스레드를 차단 하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

위의 코드는 전체 HTTP 요청 본문을 메모리에 비동기적으로 읽습니다.

> [!WARNING]
> 요청이 클 경우 전체 HTTP 요청 본문을 메모리로 읽으면 OOM (메모리 부족) 조건이 발생할 수 있습니다. OOM은 서비스 거부를 유발할 수 있습니다.  자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.

**다음 작업을 수행 합니다.** 다음 예제는 버퍼링 되지 않은 요청 본문을 사용 하 여 완전 비동기입니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

위의 코드는 요청 본문을 c # 개체로 비동기식으로 역직렬화 합니다.

## <a name="prefer-readformasync-over-requestform"></a>요청을 통해 ReadFormAsync를 선호 합니다.

`HttpContext.Request.Form` 대신 `HttpContext.Request.ReadFormAsync`을(를) 사용합니다.
`HttpContext.Request.Form` 는 다음과 같은 경우에만 안전 하 게 읽을 수 있습니다.

* 을 호출 하 여 폼을 읽은 `ReadFormAsync` 경우
* 캐시 된 형식 값을 사용 하 여 읽고 있습니다. `HttpContext.Request.Form`

**이 작업을 수행 하지 마십시오.** 다음 예에서는를 사용 `HttpContext.Request.Form` 합니다.  `HttpContext.Request.Form`[비동기를 통한 동기화](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) 를 사용 하 고 스레드 풀 고갈를 발생 시킬 수 있습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**다음 작업을 수행 합니다.** 다음 예제에서는 `HttpContext.Request.ReadFormAsync` 를 사용 하 여 양식 본문을 비동기식으로 읽습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>대량 요청 본문 또는 응답 본문을 메모리로 읽지 않습니다.

.NET에서 85 KB 보다 큰 모든 개체 할당은[LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)(large object heap)에서 끝납니다. 큰 개체는 다음과 같은 두 가지 방법으로 비용이 많이 듭니다.

* 새로 할당 된 큰 개체의 메모리를 지워야 하므로 할당 비용이 높습니다. CLR은 새로 할당 된 모든 개체의 메모리를 지우도록 보장 합니다.
* LOH는 나머지 힙을 사용 하 여 수집 됩니다. LOH에는 전체 [가비지 수집](/dotnet/standard/garbage-collection/fundamentals) 또는 [Gen2 컬렉션이](/dotnet/standard/garbage-collection/fundamentals#generations)필요 합니다.

이 [블로그 게시물](https://adamsitnik.com/Array-Pool/#the-problem) 에서는 문제 간략하게을 설명 합니다.

> 많은 개체가 할당 되 면 Gen 2 개체로 표시 됩니다. 작은 개체의 경우에는 Gen 0이 아닙니다. LOH에서 메모리가 부족 한 경우 GC는 LOH 뿐만 아니라 전체 관리 되는 힙을 정리 합니다. 따라서 LOH를 포함 하 여 Gen 0, Gen 1 및 Gen 2를 정리 합니다. 이를 전체 가비지 수집 이라고 하며 가장 시간이 많이 걸리는 가비지 수집입니다. 많은 응용 프로그램의 경우 허용할 수 있습니다. 하지만, 평균 웹 요청을 처리 하는 데 큰 메모리 버퍼가 거의 필요 하지 않은 고성능 웹 서버에 대해서는 명확 하지 않습니다 (소켓에서 읽기, 압축 풀기, JSON & 추가).

단일 또는에 대량 요청 또는 응답 본문을 저장 하는 Naively `byte[]` `string` .

* LOH의 공간이 빠르게 부족 해질 수 있습니다.
* 전체 Gc를 실행 하 여 앱에 대 한 성능 문제를 일으킬 수 있습니다.

## <a name="working-with-a-synchronous-data-processing-api"></a>동기 데이터 처리 API 작업

동기 읽기 및 쓰기를 지 원하는 serializer/직렬 변환기를 사용 하는 경우 (예:  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* 데이터를 직렬 변환기/역직렬화로 전달 하기 전에 비동기적으로 메모리에 버퍼링 합니다.

> [!WARNING]
> 요청이 크면 OOM (메모리 부족) 조건이 발생할 수 있습니다. OOM은 서비스 거부를 유발할 수 있습니다.  자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.

ASP.NET Core 3.0는 <xref:System.Text.Json> 기본적으로 JSON serialization에 사용 됩니다. <xref:System.Text.Json>:

* JSON을 비동기적으로 읽고 씁니다.
* UTF-8 텍스트에 최적화되어 있습니다.
* 일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>IHttpContextAccessor를 필드에 저장 하지 마십시오.

[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) 는 `HttpContext` 요청 스레드에서 액세스 될 때 활성 요청의를 반환 합니다. 는 `IHttpContextAccessor.HttpContext` 필드 또는 변수에 저장 **하면 안 됩니다** .

**이 작업을 수행 하지 마십시오.** 다음 예에서는를 `HttpContext` 필드에 저장 한 다음 나중에 사용 하려고 시도 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

위의 코드는 생성자에서 null 또는 잘못 된를 캡처하는 경우가 많습니다 `HttpContext` .

**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.

* 를 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 필드에 저장 합니다.
* `HttpContext`는 필드를 올바른 시간에 사용 하 고를 확인 `null` 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>여러 스레드에서 HttpContext에 액세스 하지 않음

`HttpContext` 는 스레드로부터 안전 *하지 않습니다* . `HttpContext`여러 스레드에서 병렬로 액세스 하면 중단, 충돌 및 데이터 손상과 같은 정의 되지 않은 동작이 발생할 수 있습니다.

**이 작업을 수행 하지 마십시오.** 다음 예제에서는 세 개의 병렬 요청을 만들고 나가는 HTTP 요청 전후에 들어오는 요청 경로를 로깅합니다. 요청 경로는 여러 스레드에서 동시에 액세스할 수 있습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**다음 작업을 수행 합니다.** 다음 예에서는 세 개의 병렬 요청을 만들기 전에 들어오는 요청에서 모든 데이터를 복사 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>요청이 완료 된 후에 HttpContext를 사용 하지 마십시오.

`HttpContext` 는 ASP.NET Core 파이프라인에 활성 HTTP 요청이 있는 경우에만 유효 합니다. 전체 ASP.NET Core 파이프라인은 모든 요청을 실행 하는 대리자의 비동기 체인입니다. `Task`이 체인에서 반환 된이 완료 되 면이 `HttpContext` 재활용 됩니다.

**이 작업을 수행 하지 마십시오.** 다음 예에서는를 사용 하 여 `async void` 첫 번째에 도달할 때 HTTP 요청이 완료 되도록 합니다 `await` .

* ASP.NET Core 앱에서 **항상** 잘못 된 관행입니다.
* HTTP 요청이 완료 된 후에에 액세스 합니다 `HttpResponse` .
* 프로세스를 중단 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**다음 작업을 수행 합니다.** 다음 예제에서는 `Task` 프레임 워크에 대해를 반환 하므로 작업이 완료 될 때까지 HTTP 요청이 완료 되지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>백그라운드 스레드에서 HttpContext를 캡처하지 마십시오.

**이 작업을 수행 하지 마십시오.** 다음 예제에서는 속성에서를 캡처하는 방법을 보여 줍니다 `HttpContext` `Controller` . 작업 항목에서 다음을 수행할 수 있기 때문에 잘못 된 사례입니다.

* 요청 범위 외부에서를 실행 합니다.
* 잘못 된를 읽으려고 했습니다 `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.

* 요청 하는 동안 백그라운드 작업에 필요한 데이터를 복사 합니다.
* 는 컨트롤러에서 아무것도 참조 하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

백그라운드 작업은 호스팅된 서비스로 구현 되어야 합니다. 자세한 내용은 [호스티드 서비스를 사용하는 백그라운드 작업](xref:fundamentals/host/hosted-services)을 참조하세요.

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>백그라운드 스레드에서 컨트롤러에 삽입 된 서비스를 캡처하지 마십시오.

**이 작업을 수행 하지 마십시오.** 다음 예제에서는 `DbContext` 작업 매개 변수에서를 캡처하는 방법을 보여 줍니다 `Controller` . 이는 잘못 된 방법입니다.  작업 항목은 요청 범위 외부에서 실행 될 수 있습니다. `ContosoDbContext`이 요청으로 범위가 지정 되 면이 발생 `ObjectDisposedException` 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.

* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>백그라운드 작업 항목에서 범위를 만들기 위해를 삽입 합니다. `IServiceScopeFactory` 는 단일 항목입니다.
* 백그라운드 스레드에서 새 종속성 주입 범위를 만듭니다.
* 는 컨트롤러에서 아무것도 참조 하지 않습니다.
* `ContosoDbContext`들어오는 요청에서를 캡처하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

강조 표시 된 코드는 다음과 같습니다.

* 백그라운드 작업의 수명 범위를 만들고 해당 작업에서 서비스를 확인 합니다.
* `ContosoDbContext`는 올바른 범위에서를 사용 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>응답 본문이 시작 된 후 상태 코드 또는 헤더를 수정 하지 마십시오.

ASP.NET Core는 HTTP 응답 본문을 버퍼링 하지 않습니다. 처음 응답을 쓸 때:

* 헤더는 해당 본문의 청크와 함께 클라이언트에 전송 됩니다.
* 더 이상 응답 헤더를 변경할 수 없습니다.

**이 작업을 수행 하지 마십시오.** 다음 코드는 응답이 이미 시작 된 후 응답 헤더를 추가 하려고 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

위의 코드에서 `context.Response.Headers["test"] = "test value";` 가 응답에 기록 되는 경우에서 예외를 throw `next()` 합니다.

**다음 작업을 수행 합니다.** 다음 예에서는 헤더를 수정 하기 전에 HTTP 응답이 시작 되었는지 확인 합니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**다음 작업을 수행 합니다.** 다음 예제에서는를 사용 하 여 `HttpResponse.OnStarting` 응답 헤더가 클라이언트에 플러시될 때까지 헤더를 설정 합니다.

응답을 시작 하지 않은 경우 응답 헤더를 쓰기 직전에 호출 되는 콜백을 등록할 수 있는지 확인 합니다. 응답이 시작 되지 않은 경우를 확인 하는 중:

* 헤더를 적시에 추가 하거나 재정의 하는 기능을 제공 합니다.
* 파이프라인의 다음 미들웨어에 대 한 지식이 필요 하지 않습니다.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>응답 본문에 대 한 쓰기를 이미 시작한 경우 next ()를 호출 하지 마십시오.

구성 요소는 응답을 처리 하 고 조작할 수 있는 경우에만 호출 될 것으로 예상 됩니다.

## <a name="use-in-process-hosting-with-iis"></a>IIS를 사용 하 여 in-process 호스팅 사용

In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다. In-process 호스트는 루프백 어댑터를 통해 요청이 프록시 되지 않으므로 out-of-process 호스팅을 통해 향상 된 성능을 제공 합니다. 루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환 하는 네트워크 인터페이스입니다. IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.

프로젝트는 ASP.NET Core 3.0 이상에서 in-process 호스팅 모델로 기본 됩니다.

자세한 내용은 [IIS를 사용 하 여 Windows의 호스트 ASP.NET Core](xref:host-and-deploy/iis/index) 를 참조 하세요.