<properties
	pageTitle="JavaScript 웹앱 Application Insights | Microsoft Azure"
	description="페이지 보기 및 세션 수와 웹 클라이언트 데이터를 가져오고 사용 패턴을 추적합니다. JavaScript 웹 페이지의 예외 및 성능 문제를 감지합니다."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# 웹 페이지용 Application Insights

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

웹 페이지 또는 앱의 성능 및 사용 현황에 대해 알아봅니다. 페이지 스크립트에 Visual Studio Application Insights를 추가하면 페이지 로드 및 AJAX 호출, 브라우저 예외 및 AJAX 실패의 개수 및 세부 정보뿐만 아니라 사용자 및 세션 수의 타이밍을 얻을 수 있습니다. 이러한 모든 요소를 페이지, 클라이언트 OS 및 브라우저 버전, 지리적 위치 및 기타 차원으로 분할할 수 있습니다. 또한 실패 횟수 또는 느린 페이지 로딩에 대한 경고를 설정할 수도 있습니다.

Application Insights와 다른 웹 페이지를 사용할 수 있습니다. 간단한 JavaScript만 추가하면 됩니다. 웹 서비스가 [Java](app-insights-java-get-started.md) 또는 [ASP.NET](app-insights-asp-net.md)인 경우, 서버 및 클라이언트의 원격 분석을 통합할 수 있습니다.

[Microsoft Azure](https://azure.com)를 구독해야 합니다. 팀에 조직 구독이 있는 경우 자신의 Microsoft 계정을 여기에 추가해도 되는지 소유자에게 문의합니다. 무료 가격 책정 계층이 있으므로 개발 및 소규모 사용에는 별도의 비용이 없습니다.


## 웹 페이지용 Application Insights 설치

이미 있을 수 있습니다. 앱이 새로운 ASP.NET 프로젝트이고 새 프로젝트 대화 상자 Visual Studio에 Application Insights를 추가하도록 선택한 경우, 스크립트를 추가한 다음 모든 설정을 완료합니다.

그렇지 않은 경우 다음과 같이 코드 조각을 웹 페이지에 추가해야 합니다.

### Application Insights 리소스 열기

Application Insights 리소스는 페이지의 성능 및 사용 현황에 대한 데이터가 표시되는 위치입니다.

[Azure 포털](https://portal.azure.com)에 로그인합니다.

응용 프로그램의 서버측에 대한 모니터링을 이미 설정한 경우 리소스가 있습니다.

![찾아보기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-javascript/01-find.png)

계정이 없는 경우 계정을 만듭니다.

![새로 만들기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-javascript/01-create.png)


*벌써 질문이 있나요?* [리소스 만들기에 대해 자세히 알아보세요](app-insights-create-new-resource.md).


### 앱 또는 웹 페이지에 SDK 스크립트를 추가합니다.

빠른 시작에서 웹 페이지용 스크립트를 가져옵니다.

![앱 개요 블레이드에서 빠른 시작, 내 웹 페이지를 모니터링할 코드 가져오기를 선택합니다. 스크립트를 복사합니다.](./media/app-insights-javascript/02-monitor-web-page.png)

추적하려는 모든 페이지의 `<head>` 태그 바로 앞의 이 스크립트를 삽입합니다. 웹 사이트에 마스터 페이지가 있는 경우 이 페이지에 스크립트를 넣을 수 있습니다. 예:

* ASP.NET MVC 프로젝트에서는 `View\Shared\_Layout.cshtml`에 추가합니다.
* SharePoint 사이트의 경우 제어판에서 [사이트 설정/마스터 페이지](app-insights-sharepoint.md)를 엽니다.

스크립트에는 Application Insights 리소스에 데이터를 전달하는 계측 키가 포함됩니다.

([스크립트에 대한 자세한 설명.](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(잘 알려진 웹 페이지 프레임워크를 사용하는 경우 Application Insights 어댑터를 찾아보세요. 예를 들어 [AngularJS 모듈](http://ngmodules.org/modules/angular-appinsights)이 있습니다.)*


## 자세한 구성

몇 가지 [매개 변수](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)을 설정할 수 있지만 대부분의 경우에서 설정할 필요가 없습니다. 예를 들어 페이지 보기당 보고된 Ajax 호출 수를 사용하지 않거나 제한하거나(트래픽 감소), 일괄 처리하지 않고 파이프라인을 통해 원격 분석을 빠르게 이동시키는 디버그 모드를 설정할 수 있습니다.

이러한 매개 변수를 설정하려면 코드 조각에서 이 줄을 찾고 쉼표로 구분하여 항목을 추가합니다.

    })({
      instrumentationKey: "..."
      // Insert here
    });

[사용 가능한 매개 변수](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)는 다음을 포함합니다.

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>앱 실행

웹앱을 실행하고, 잠깐 사용하여 원격 분석을 생성하고, 잠시 기다립니다. 개발 컴퓨터에서 **F5** 키를 사용하여 실행하거나 사용자가 실행할 수 있도록 게시할 수 있습니다.

웹앱에서 Application Insights로 보내는 원격 분석을 확인하려면 브라우저의 디버깅 도구(대부분의 브라우저는 **F12** 키)를 사용합니다. 데이터가 dc.services.visualstudio.com으로 전송됩니다.

## 브라우저 성능 데이터 탐색

사용자의 브라우저에서 집계된 성능 데이터를 표시하도록 브라우저 블레이드를 엽니다.

![Portal.azure.com에서 앱의 리소스 열고 설정, 브라우저를 클릭합니다.](./media/app-insights-javascript/03.png)


*아직 아무 데이터도 없나요? 페이지 위쪽에서 **새로 고침**을 클릭합니다. 여전히 아무 데이터도 없나요? [문제 해결](app-insights-troubleshoot-faq.md)을 참조하세요.*

브라우저 블레이드는 미리 설정된 필터와 차트를 선택할 수 있는 [메트릭 탐색기 블레이드](app-insights-metrics-explorer.md)입니다. 원하는 경우 시간 범위, 필터 및 차트 구성을 편집하고 즐겨찾기로 결과를 저장할 수 있습니다. **기본값 복원**을 클릭하여 원래 블레이드 구성으로 돌아갑니다.

## 페이지 로드 성능

맨 위에 있는 것이 페이지 로드 시간의 분할된 차트입니다. 차트의 전체 높이는 사용자의 브라우저의 앱에서 페이지를 로드하고 페이지를 표시하는 평균 시간을 나타냅니다. 레이아웃 및 실행 스크립트를 포함하여 모든 동기 로드 이벤트가 처리될 때까지 브라우저가 초기 HTTP 요청을 보낼 때부터 시간이 측정됩니다. AJAX 호출로부터 웹 파트를 로드하는 등의 비동기 작업은 포함되지 않습니다.

차트는 총 페이지 로드 시간을 [W3C에서 정의한 표준 타이밍](http://www.w3.org/TR/navigation-timing/#processing-model)으로 분할한 것입니다.

![](./media/app-insights-javascript/08-client-split.png)

*네트워크 연결* 시간은 가끔 예상보다 짧습니다. 브라우저에서 서버로 보내는 모든 요청의 평균값이기 때문입니다. 서버에 대한 활성 연결이 이미 있기 때문에 연결 시간이 0인 개별 요청이 많습니다.

### 로드가 느립니까?

느린 페이지 로드는 사용자가 가장 불만족을 느끼는 부분입니다. 차트가 느린 페이지 로드를 나타내는 경우, 일부 진단 조사를 쉽게 수행할 수 있습니다.

차트는 앱에서 모든 페이지를 로드하는 평균 시간을 보여줍니다. 이 문제가 특정 페이지에 국한되는지 확인하려면, 블레이드 아래의 페이지 URL별로 분류된 표를 참조하세요.

![](./media/app-insights-javascript/09-page-perf.png)

페이지 보기 수 및 표준 편차를 확인합니다. 페이지 수가 매우 낮은 경우 사용자에게 큰 문제가 되지 않습니다. 높은 표준 편차(자체 평균 비교 시)는 개별 측정값 간의 차이가 많음을 나타냅니다.

**한 URL 및 한 페이지 보기에서 확대합니다.** 해당 URL에 대해서만 필터링된 브라우저 차트의 블레이드를 보려면 페이지 이름을 클릭한 다음, 페이지 보기의 인스턴스를 클릭합니다.

![](./media/app-insights-javascript/35.png)

`...`을(를) 클릭하여 해당 이벤트에 대한 속성의 전체 목록을 보거나 Ajax 호출 및 관련된 이벤트를 검사합니다. 느린 Ajax 호출은 동기화할 때 전체 페이지 로드 시간에 영향을 줍니다. 관련된 이벤트에는 동일한 URL에 대한 서버 요청을 포함합니다(웹 서버에서 Application Insights를 설정한 경우).


**시간에 따른 페이지 성능** 브라우저 블레이드로 돌아와서 특정 시간에 최대치가 있는지 확인하기 위해 페이지 보기 로드 시간 표를 꺾은선형 차트로 변경합니다.

![표의 머리글을 클릭하고 새 차트 종류를 선택합니다.](./media/app-insights-javascript/10-page-perf-area.png)

**다른 차원으로 분류** 특정 브라우저, 클라이언트 OS 또는 사용자 거주지에 따라 페이지 로드 시간이 느려질 수 있습니까? 새 차트를 추가하고 **Group-by** 차원을 실험해봅니다.

![](./media/app-insights-javascript/21.png)


## AJAX 성능

웹 페이지의 모든 AJAX 호출이 정상적으로 수행되고 있는지 확인합니다. 종종 페이지의 부분들을 비동기적으로 채우는 데 사용 됩니다. 전체 페이지가 신속하게 로드되더라도 사용자는 빈 웹 부분을 응시하면서 데이터가 표시되기를 기다리는 데 불만을 가질 수 있습니다.

웹 페이지에서 이루어진 AJAX 호출은 브라우저 블레이드에 종속성으로 표시됩니다.

블레이드의 상단 부분에 있는 요약 차트가 있습니다.

![](./media/app-insights-javascript/31.png)

그리고 상세한 표가 아래에 있습니다.

![](./media/app-insights-javascript/33.png)

특정 세부 정보를 보려면 행을 클릭합니다.


> [AZURE.NOTE] 블레이드에서 브라우저 필터를 삭제하면 서버와 AJAX 종속성이 이 차트에 포함됩니다. 필터를 다시 구성하려면 기본값 복원을 클릭합니다.

**실패한 Ajax 호출을 분석하려면** 종속성 실패 그리드로 아래로 스크롤한 다음, 보려는 특정 인스턴스의 행을 클릭합니다.

![](./media/app-insights-javascript/37.png)

Ajax 호출에 대한 전체 원격 분석을 하려면 `...`을(를) 클릭합니다.

### Ajax 호출이 보고되지 않았습니까?

Ajax 호출은 웹 페이지의 스크립트에서 이루어진 HTTP 호출을 포함합니다. 보고된 호출이 없는 경우, 코드 조각이 `disableAjaxTracking` 또는 `maxAjaxCallsPerView` [매개 변수](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)를 설정하지 않았는지 확인합니다.

## 브라우저 예외

브라우저 블레이드에는 예외 요약 차트가 있고 좀 더 아래에는 예외 형식의 표가 있습니다.

![](./media/app-insights-javascript/39.png)

보고된 브라우저 예외가 없는 경우, 코드 조각이 `disableExceptionTracking` [매개 변수](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)를 설정하지 않았는지 확인합니다.

## 개별 페이지 보기 이벤트 검사

일반적으로 페이지 보기 원격 분석은 Application Insights에서 분석하며 모든 사용자에 대해 계산된 평균이 포함된 누적 보고서만 표시됩니다. 그러나 디버깅을 위해 개별 페이지 보기 이벤트를 확인할 수도 있습니다.

진단 검색 블레이드에서 필터를 페이지 보기로 설정합니다.

![](./media/app-insights-javascript/12-search-pages.png)

보다 자세한 정보를 확인하려면 원하는 이벤트를 선택합니다. 세부 정보 페이지에서 더 자세한 정보를 보려면 "..."를 클릭합니다.

> [AZURE.NOTE] [검색](app-insights-diagnostic-search.md)을 사용하는 경우 전체 단어가 일치해야 합니다. "Abou"와 "bout"은 "About"과 일치하지 않지만 "Abou*"는 일치합니다. 또한 검색 용어는 와일드로 시작할 수 없습니다. 예를 들어 "*bou"로 검색해도 "About"는 검색되지 않습니다.

> [진단 검색에 대해 자세히 알아보기](app-insights-diagnostic-search.md)

### 페이지 보기 속성

* **페이지 보기 기간** 페이지를 로드하고 스크립트 실행을 시작하는 데 걸리는 시간입니다. 특히, 시작 페이지와 트랙 페이지 보기의 실행을 로드하는 사이의 간격입니다. 스크립트의 초기화 후 일반적인 위치에서 trackPageView를 이동한 경우 다른 값이 반영 됩니다.


## 사용자 지정 페이지 수

기본적으로는 새 페이지를 클라이언트 브라우저로 로드할 때마다 페이지 수가 계산됩니다. 그러나 추가 페이지 보기를 계산에 포함할 수도 있습니다. 예를 들어 탭에 콘텐츠가 표시되는 페이지에서 사용자가 탭을 전환할 때 페이지 수를 계산하도록 지정할 수 있습니다. 또는 페이지의 JavaScript 코드가 브라우저 URL은 변경하지 않고 새 콘텐츠를 로드할 수도 있습니다.

클라이언트 코드의 적절한 지점에 다음과 같은 JavaScript 호출을 삽입합니다.

    appInsights.trackPageView(myPageName);

페이지 이름은 URL과 같은 문자를 포함할 수 있지만 "#" 또는 "?" 뒤의 모든 문자는 무시됩니다.



## 사용 추적


사용자가 앱으로 어떤 작업을 수행하려고 하는지 확인하고 싶나요?

* [사용 추적에 대해 알아보기](app-insights-web-track-usage.md)
* [사용자 지정 이벤트 및 메트릭 API에 대해 자세히 알아보세요](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a> 비디오: 사용 현황 추적

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 다음 단계

* [사용 현황 추적](app-insights-web-track-usage.md)
* [사용자 지정 이벤트 및 메트릭](app-insights-api-custom-events-metrics.md)
* [빌드 - 측정 - 학습](app-insights-overview-usage.md)

<!----HONumber=AcomDC_0224_2016-->