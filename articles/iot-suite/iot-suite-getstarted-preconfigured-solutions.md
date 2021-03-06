<properties
	pageTitle="미리 구성된 솔루션 시작 | Microsoft Azure"
	description="미리 구성된 Azure IoT Suite 솔루션을 배포하는 방법을 알아보려면 이 자습서를 따릅니다."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="dobett"/>

# 자습서: 미리 구성된 IoT 솔루션 시작

## 소개

Azure IoT Suite의 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 여러 Azure IoT 서비스를 결합하여 일반적인 IoT 비즈니스 시나리오를 구현하는 종합적인 솔루션을 제공합니다.

이 자습서는 미리 구성된 *원격 모니터링* 솔루션을 프로비전하는 방법을 보여 줍니다. 또한 미리 구성된 원격 모니터링 솔루션의 기본 기능을 안내합니다.

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [AZURE.NOTE]  계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][lnk_free_trial]을 참조하세요.

## 미리 구성된 원격 모니터링 솔루션 프로비전

1.  Azure 계정 자격 증명을 사용하여 [azureiotsuite.com][lnk-azureiotsuite]에 로그온한 다음 **+**를 클릭하여 새 솔루션을 만듭니다.

    > [AZURE.NOTE] 솔루션을 프로비전하는 데 필요한 사용 권한에 문제가 있는 경우 [azureiotsuite.com 사이트에 대한 사용 권한](iot-suite-permissions.md) 지침을 살펴보세요.

2.  **원격 모니터링** 타일에서 **선택**을 클릭합니다.

3.  미리 구성된 원격 모니터링 솔루션에 **솔루션 이름**을 입력합니다.

4.  솔루션을 프로비전하는 데 사용할 **지역** 및 **구독**을 선택합니다.

5.  **솔루션 만들기**를 클릭하여 프로비전 프로세스를 시작합니다. 일반적으로 이 작업을 실행하는 데 몇 분 정도 걸립니다.

## 프로비전 프로세스가 완료될 때까지 대기

1. **프로비전** 상태인 솔루션 타일을 클릭합니다.
 
2. Azure 서비스가 Azure 구독에 배포될 때 **프로비전 상태**입니다.

3. 프로비전이 완료되면 **준비** 상태로 바뀝니다.

4. 타일을 클릭하면 오른쪽 창에 솔루션의 세부 정보가 표시됩니다.

목록에는 없지만 솔루션에 대해 참조하고 싶은 세부 정보가 있나요? [사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)에 기능 제안을 보내주세요.

## 원격 모니터링 솔루션 대시보드 보기

솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 예를 들어 원격 분석 보기, 장치 추가 및 규칙 구성 작업을 수행할 수 있습니다.

1.  프로비전이 완료되고 미리 구성된 솔루션에 대한 타일이 **준비**를 가리키면 **시작**을 클릭하여 새 탭에서 원격 모니터링 솔루션 포털을 엽니다.

    ![][img-launch-solution]

2.  기본적으로 솔루션 포털에 *솔루션 대시보드*가 표시됩니다. 왼쪽 메뉴를 사용하여 다른 뷰를 선택할 수 있습니다.

    ![][img-dashboard]

## 솔루션 장치 목록 보기

장치 목록에 솔루션에 등록된 모든 장치가 표시됩니다. 장치 메타데이터 보기 및 편집, 장치 추가 및 제거, 장치에 명령 보내기 작업을 수행합니다.

1.  왼쪽 메뉴에서 **장치**를 클릭하여 해당 솔루션에 대한 *장치 목록*을 표시합니다.

    ![][img-devicelist]

2.  장치 목록은 프로비전 프로세스에서 생성된 네 개의 시뮬레이션된 장치를 보여줍니다.

3.  장치 목록의 장치를 클릭하면 해당 장치의 세부 정보를 볼 수 있습니다.

    ![][img-devicedetails]

## 장치에 명령 보내기

장치 세부 정보 창에 장치가 지원하는 모든 명령이 표시되고 이 창에서 특정 장치에 명령을 보낼 수 있습니다.

1.  선택한 장치에 대한 장치 세부 정보 창에서 **명령**을 클릭합니다.

    ![][img-devicecommands]

2.  명령 목록에서 **PingDevice**를 선택합니다.

3.  **명령 전송**을 클릭합니다.

4.  명령 기록에서 명령 상태를 볼 수 있습니다.

    ![][img-pingcommand]

## 시뮬레이션된 새 장치 추가

1.  다시 장치 목록으로 이동합니다.

2.  왼쪽 아래 모서리에서 **+ 장치 추가**를 클릭하여 새 장치를 추가합니다.

    ![][img-adddevice]

3.  **시뮬레이션된 장치** 타일에서 **새 장치 추가**를 클릭합니다.

    ![][img-addnew]

4.  **나만의 장치 ID 정의**를 선택하고 **mydevice\_01**처럼 고유한 장치 ID 이름을 입력합니다.

5.  **만들기**를 클릭합니다.

    ![][img-definedevice]

6. 3단계 **시뮬레이션된 장치 추가**에서 **완료**를 클릭하여 장치 목록으로 돌아갑니다.

7.  장치 목록에서 장치가 **실행 중**인 것을 볼 수 있습니다.

    ![][img-runningnew]

## 새 물리적 장치 추가

솔루션에 새 물리적 장치를 추가하려면 [미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결][lnk-connecting-devices]을 참조하세요.

## 솔루션 규칙 보기 및 편집

미리 구성된 솔루션이 SampleDevice001에 대한 두 개의 규칙을 프로비전합니다. 이 규칙은 온도 또는 습도 값이 임계값을 초과하는 경우 대시보드의 **경보 기록** 타일을 통해 알려줍니다.

1.  솔루션 대시보드로 돌아가서 **경보 기록** 타일을 봅니다.

    ![][img-alarmhistory]

2.  **AlarmTemp** 규칙이 이러한 경보를 트리거합니다.

3.  왼쪽 메뉴에서 **규칙**을 클릭하여 해당 솔루션에 대한 규칙을 살펴봅니다.

    ![][img-rules]

5.  규칙 목록에서 **온도** 규칙을 클릭하여 규칙 속성을 봅니다.

6.  규칙을 수정하려면 규칙 속성 창에서 **편집**을 클릭합니다.

    ![][img-displayrule]

7.  **임계값**을 30이 되도록 변경하고 다른 모든 속성을 동일하게 유지합니다.

8.  **규칙 저장 및 보기**를 클릭합니다.

    ![][img-editrule]

9.  **솔루션 대시보드**의 **경보 기록** 테이블로 돌아가서 업데이트된 규칙의 결과로 동작이 어떻게 바뀌는지 관찰합니다.

    ![][img-newhistory]
    
완료되면 [azureiotsuite.com][lnk-azureiotsuite] 사이트의 Azure 구독에서 미리 구성된 솔루션을 삭제할 수 있고 그러면 미리 구성된 솔루션을 만든 경우 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

## 다음 단계

이제 미리 구성된 작업 솔루션을 구축했으므로 다음 연습으로 이동할 수 있습니다.

-   [미리 구성된 솔루션 사용자 지정에 대한 지침][lnk-customize]
-   [예측 정비 사전 구성 솔루션 개요][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md

<!----HONumber=AcomDC_0309_2016-->