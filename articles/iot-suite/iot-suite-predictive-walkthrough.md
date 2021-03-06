<properties
 pageTitle="예측 유지 관리 연습 | Microsoft Azure"
 description="Azure IoT 예측 정비 사전 구성 솔루션에 대한 연습입니다."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="araguila"/>

# 미리 구성된 예측 유지 관리 솔루션 연습

## 소개

미리 구성된 IoT Suite 예측 유지 관리 솔루션은 오류가 발생할 가능성이 있는 경우 지점을 예측하는 비즈니스 시나리오에 대한 종단 간 솔루션입니다. 유지 관리를 최적화하는 등의 작업에 미리 구성된 솔루션을 활용할 수 있습니다. 솔루션은 공용 샘플 데이터 집합을 기반으로 하는 항공기 엔진의 남은 유용한 수명(RUL)을 예측하는 실험을 완료하는 [Azure 기계 학습][lnk_machine_learning]을 비롯한 주요 Azure IoT Suite 서비스를 결합합니다. 솔루션은 비즈니스 시나리오의 전체 구현을 시작점으로 제공하여 사용자의 특정 비즈니스 요구 사항을 충족하기 위해 IoT 솔루션의 이 형식을 계획하고 구현합니다.

## 논리 아키텍처

다음 다이어그램에서는 미리 구성된 솔루션의 논리적 구성 요소를 간략히 보여줍니다.

![][img-architecture]

파란색 항목은 미리 구성된 솔루션을 프로비전할 때 선택한 위치에 프로비전되는 Azure 서비스입니다. 미국 동부, 북유럽, 또는 동남 아시아 지역에서 미리 구성된 솔루션을 제공할 수 있습니다.

일부 리소스는 미리 구성된 솔루션을 프로비전하는 지역에서 사용할 수 없습니다. 다이어그램에서 주황색 항목은 선택한 영역에서 사용할 수 있는 가장 가까운 지역의 프로비전된 Azure 서비스를 나타냅니다.(예: 미국 중남부, 서부 유럽 또는 동남 아시아)

녹색 항목은 항공기 엔진을 나타내는 시뮬레이션된 장치입니다. 아래에서 이러한 시뮬레이션된 장치에 대해 자세히 알아볼 수 있습니다

회색 항목은 *장치 관리* 기능을 구현하는 구성 요소를 나타냅니다. 미리 구성된 예측 유지 관리 솔루션의 현재 릴리스에서는 이러한 리소스를 프로비전하지 않습니다. 장치 관리에 대한 자세한 내용은 [미리 구성된 솔루션 원격 모니터링][lnk-remote-monitoring]을 참조합니다.

## 시뮬레이션된 장치

미리 구성된 솔루션에서 시뮬레이션된 장치는 항공기 엔진을 나타냅니다. 솔루션은 단일 비행기에 매핑하는 2개의 엔진으로 프로비전됩니다. 각 엔진은 다음과 같은 4가지 형식의 원격 분석을 내보냅니다. 센서9, 센서11, 센서14 및 센서 15는 기계 학습 모델이 엔진에 대한 남은 유용한 수명(RUL)을 계산하는 데 필요한 데이터를 제공합니다. 시뮬레이션된 각 장치는 IoT Hub에 다음과 같은 원격 분석 메시지를 보냅니다.

*계수 주기*. 주기는 원격 분석 데이터가 비행 기간 동안 30분 마다 캡처된 2-10시간 사이의 가변 길이가 완료된 항공편을 나타냅니다.

*원격 분석*. 엔진 특성을 나타내는 4가지 센서가 있습니다. 센서9, 센서11, 센서14 및 센서15는 일반적으로 레이블이 지정됩니다. 이러한 4가지 센서는 RUL에 대한 기계 학습 모델에서 유용한 결과 얻기에 충분한 원격 분석을 나타냅니다. 이 모델은 실제 엔진 센서 데이터를 포함하는 공용 데이터 집합에서 만들어집니다. 원래 데이터 집합에서 모델을 만든 방법에 대한 자세한 내용은 [Cortana Analytics 갤러리 예측 유지 관리 템플릿][lnk-cortana-analytics]을 참조하세요.

시뮬레이션된 장치는 IoT Hub에서 보낸 다음과 같은 명령을 처리할 수 있습니다.

| 명령 | 설명 |
|---------|-------------|
| StartTelemetry | 시뮬레이션의 상태를 제어합니다.<br/>원격 분석을 보내는 장치를 시작합니다. |
| StopTelemetry | 시뮬레이션의 상태를 제어합니다.<br/>원격 분석을 보내는 장치를 중지합니다. |

IoT Hub는 장치 명령 승인을 제공합니다.

## Azure 스트림 분석 작업

**작업: 원격 분석**은 두 가지 문을 사용하여 들어오는 장치 원격 분석 스트림에서 작동합니다. 첫 번째 문은 장치에서 모든 원격 분석을 선택하고 웹앱의 시각화되는 위치에서 Blob 저장소에 이 데이터를 보냅니다. 두 번째 문은 2분 슬라이딩 창을 통해 평균 센서 값을 계산하고 이벤트 허브를 통해 **이벤트 프로세서**로 이 데이터를 보냅니다.

## 이벤트 프로세서

**이벤트 프로세서**는 완료된 주기에 대한 평균 센서 값을 사용하고 기계 학습으로 학습된 모델을 노출하는 API에 해당 값을 전달하여 엔진에 대한 RUL를 계산합니다.

## Azure 기계 학습

원래 데이터 집합에서 모델을 만든 방법에 대한 자세한 내용은 [Cortana Analytics 갤러리 예측 유지 관리 템플릿][lnk-cortana-analytics]을 참조하세요.

## 탐색을 시작하겠습니다.

이 섹션은 솔루션의 구성 요소를 안내하고, 의도된 사용 사례에 설명하며, 예제를 제공합니다.

### 대시보드에서 예측 유지 관리

웹 응용 프로그램의 이 페이지는 PowerBI JavaScript 제어를 사용하여([PowerBI 시각 효과 리포지토리][lnk-powerbi]를 참조) 다음을 시각화합니다.

- Blob 저장소의 스트림 분석 작업에서 출력 데이터.
- 항공기 엔진 당 RUL 및 주기 수.

### 클라우드 솔루션의 동작 관찰

Azure 포털로 간 다음 선택한 솔루션 이름을 통해 리소스 그룹으로 이동함으로써 프로비전된 리소스를 볼 수 있습니다.

![][img-resource-group]

미리 구성된 솔루션을 프로비전할 때 기계 학습 작업 영역에 대한 링크가 포함된 전자 메일을 수신합니다. 또한 **준비** 상태일 때 프로비전된 솔루션에 대한 [azureiotsuite.com][lnk-azureiotsuite] 페이지에서 이 기계 학습 작업 영역으로 이동할 수 있습니다.

![][img-machine-learning]

솔루션 포털에서 샘플이 항공기 당 2개의 엔진, 엔진 당 4개의 센서가 있는 2대의 항공기를 나타내는 네 가지의 시뮬레이션된 장치를 사용하여 프로비전된 것을 볼 수 있습니다. 솔루션 포털로 먼저 이동할 때 시뮬레이션이 중지됩니다.

![][img-simulation-stopped]

**시뮬레이션 시작**을 클릭하여 대시보드를 채운 센서 기록, RUL, 주기 및 RUL를 확인할 수 있는 시뮬레이션을 시작합니다.

![][img-simulation-running]

RUL가 160(데모를 위해 선택한 대 한 임의의 임계값) 보다 작은 경우 솔루션 포털은 RUL 표시 옆에 경고 기호를 표시하고 항공기 엔진을 노란색 그림으로 칠합니다. RUL 값이 전반적으로 일반 하향 추세를 갖지만 아래 위로 요동치는 경향이 있습니다. 이는 다양한 주기 길이 및 모델 정확도로 인해 발생합니다.

![][img-simulation-warning]

전체 시뮬레이션이 148 주기를 완료하려면 약 35분이 걸립니다. 160 RUL 임계값은 처음 약 5분이 지나서 도달하고 두 엔진은 약 8분이 지나서 임계값에 도달합니다.

시뮬레이션은 148주기 동안 전체 데이터 집합을 실행하고 최종 RUL 및 주기 값에 도달합니다.

언제든 시뮬레이션을 중지할 수 있지만 **시뮬레이션 시작**을 클릭하면 데이터 집합의 처음부터 시뮬레이션을 재생합니다.

## 다음 단계

이제 수정하려는 미리 구성된 예측 유지 관리 솔루션을 실행했으면 [미리 구성된 사용자 지정 솔루션에 대한 지침][lnk-customize]을 참조하세요.

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!----HONumber=AcomDC_0309_2016-->