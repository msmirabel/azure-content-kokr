<properties
	pageTitle="Azure Mobile Engagement Android SDK 통합"
	description="Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="piyushjo" />


#릴리스 정보

##4\.1.5(2016/02/01)

- 안정성 향상

##4\.1.4(2016/01/26)

- 안정성 향상

##4\.1.3(2015/12/9)

- 안정성 향상

##4\.1.2(2015/11/25)

- 안정성 향상

##4\.1.1(2015/11/04)

- 안정성 향상

##4\.1.0(08/25/2015)

- Android M에 대한 새 권한 모델을 다룹니다.
- `AndroidManifest.xml`을 사용하는 대신 이제 런타임 시 위치 기능을 구성할 수 있습니다.
- 사용 권한 버그 수정: `ACCESS_FINE_LOCATION`을 사용하는 경우 `ACCESS_COARSE_LOCATION`이 더 이상 필요하지 않습니다.
- 안정성 향상

##4\.0.0(07/06/2015)

-   내부 프로토콜이 분석을 수행하고 더 안정적으로 푸시하도록 변경됩니다.
-   네이티브 푸시(GCM/ADM)가 이제 앱 알림에도 사용되므로 모든 푸시 캠페인 유형에 대한 네이티브 푸시 자격 증명을 구성해야 합니다.
-   큰 그림 알림을 수정합니다. 푸시된 후 10초만 표시되었습니다.
-   웹 보기에서 버그 수정: 링크를 클릭하면 기본 작업 URL도 실행되었습니다.
-   로컬 저장소 관리와 관련된 드문 충돌을 수정합니다.
-   동적 구성 문자열 관리를 수정합니다.
-   EULA를 업데이트합니다.

##3\.0.0(2015/02/17)

-   Azure Mobile Engagement의 최초 릴리스입니다.
-   appId 구성이 연결 문자열 구성으로 대체됩니다.
-   임의의 XMPP 엔터티에서 임의의 XMPP 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   장치 간에 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   보안이 개선되었습니다.
-   Google Play 및 SmartAd 추적을 제거했습니다.

<!---HONumber=AcomDC_0204_2016-->