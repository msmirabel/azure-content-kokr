<properties
   pageTitle="리소스 분산 아키텍처| Microsoft Azure"
   description="서비스 패브릭 리소스 분산 장치의 아키텍처 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# 리소스 분산 아키텍처 개요

![리소스 분산 아키텍처][Image1]

Azure 서비스 패브릭의 리소스 분산 장치는 단일 중앙 집중식 리소스 분산 서비스 및 모든 노드에 존재하는 구성 요소로 구성됩니다. 개념적으로 서비스 패브릭 장애 조치(Failover) 관리자와 로컬 서비스 패브릭 노드로 각각 배치됩니다.

로컬 에이전트는 로컬 노드에서 실행되는 서비스로부터 부하 보고서를 수집 및 버퍼링하고, 이를 리소스 분산 장치 서비스에 보내며, 오류 및 기타 이벤트를 장애 조치(Failover) 관리자와 리소스 분산 장치(위의 1, 2)에 보고하는 일을 담당합니다. 리소스 분산 장치와 장애 조치(Failover) 관리자는 시스템에서 부하 이벤트 및 기타 이벤트에 응답하기 위해 공동 작업합니다. 이러한 이벤트에는 복제본이나 노드 실패, 부하 보고서와 만들어지고 삭제되는 서비스 및 응용 프로그램이 포함될 수 있습니다.

예를 들어 복제본이 실패하면 장애 조치(Failover) 관리자는 리소스 분산 장치가 여러 노드의 부하 데이터를 기반으로 대체 위치를 제안하도록 요청합니다. 마찬가지로, 새 서비스 요청을 받으면 장애 조치(Failover) 관리자는 해당 서비스를 배치할 위치에 대해 리소스 분산 장치의 권장을 요청합니다. 이러한 모든 경우에 리소스 분산 장치는 다양한 서비스 구성에 대한 변경 내용으로 응답합니다(3). 그런 다음 이러한 변경 내용은 장애 조치(Failover) 관리자에 의해 시행됩니다. 앞의 예제에서 장애 조치(Failover) 관리자는 새 복제본을 노드에 만들어 최상의 전체 균형을 이룹니다(4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

리소스 분산 기능:

- [클러스터 설명](service-fabric-resource-balancer-cluster-description.md)
- [서비스 설명](service-fabric-resource-balancer-service-description.md)
- [동적 부하 보고](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [사전 메트릭 압축](service-fabric-resource-balancer-proactive-metric-packing.md)
- [노드 버퍼 백분율](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->