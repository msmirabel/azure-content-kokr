<properties
 pageTitle="IT 전문가를 위한 Azure IoT Hub 정보 | Microsoft Azure"
 description="포트 요구 사항 및 보안 배경과 같이 IT 전문가가 Azure IoT Hub 작업을 수행하도록 지원하는 정보입니다."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# IoT Hub에 대한 액세스 구성 및 관리

이 문서는 IoT 장치가 네트워크 인프라의 IoT Hub와 통신하는 환경을 구성하도록 IT 전문가에게 정보를 제공합니다.

## 네트워크 연결

장치는 다양한 프로토콜을 사용하여 Azure에서 IoT Hub와 통신할 수 있습니다. 일반적으로 프로토콜은 솔루션의 특정 요구 사항에 따라 선택됩니다. 다음 표에는 특정 프로토콜을 사용할 수 있는 장치에 대해 열려 있어야 하는 아웃바운드 포트가 나와 있습니다.

| 프로토콜 | 포트 |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| Websocket 통한 AMQP | 443 |
| MQTT | 8883 |

Azure 지역에 IoT Hub를 만들면 허브는 해당 허브의 수명 동안 동일한 IP 주소를 유지합니다. 그러나 재해 복구 시나리오에서 Microsoft가 IoT Hub를 다른 배율 단위로 이동하는 경우에는 새 IP 주소로 할당됩니다.

## IoT Hub 및 보안

IoT Hub에 등록된 장치만 해당 IoT Hub와 통신할 수 있습니다. 등록된 장치에 *DeviceConnect* 권한을 부여해야 합니다. 장치가 각 요청에서 장치의 고유한 ID를 캡슐화하는 토큰을 포함하여 자체적으로 식별하면, 허브는 토큰의 유효성과 장치에 블랙리스트가 있는지 확인합니다(*DeviceConnect* 권한 해지).

또한 IoT Hub에 있는 다른 관리 끝점에 대한 액세스는 권한 집합(*iothubowner*, *service*, *registryRead* 및 *registryReadWrite*)을 통해 제어합니다. IoT Hub에 연결하는 모든 클라이언트 관리 응용 프로그램은 적절한 권한을 가진 토큰을 포함해야 합니다.

## 다음 단계

이 문서는 IT 전문가 및 개발자를 위해 개발 및 테스트 환경을 구성하는 특정 정보를 포함합니다. Azure IoT Hub 서비스에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub Azure란?][lnk-iothub]
- [Azure IoT Hub 개발자 가이드의 보안 섹션][lnk-devguide]에서 IoT Hub의 토큰 및 권한 시스템에 대한 추가 정보를 제공합니다.
- [Azure 포털을 통해 IoT Hub 관리][lnk-manage-portal]에서 Azure 포털을 사용하여 IoT Hub를 관리하는 방법을 설명합니다.

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0204_2016-->