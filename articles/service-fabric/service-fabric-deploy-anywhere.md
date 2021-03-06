<properties
   pageTitle="Windows Server 및 Linux에서 Azure 서비스 패브릭을 사용하여 원하는 위치에 배포 | Microsoft Azure"
   description="서비스 패브릭 클러스터는 Windows Server 및 Linux에서 실행됩니다. 즉, Windows Server 또는 Linux를 실행할 수 있는 모든 위치에 서비스 패브릭 응용 프로그램을 배포 및 호스트할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/10/2016"
   ms.author="chackdan"/>

# 서비스 패브릭을 사용하여 Windows Server 또는 Linux에서 “원하는 위치에 배포”
"원하는 위치에 배포" 기능이 추가된 Azure 서비스 패브릭을 사용하면 Windows Server 또는 Linux를 실행 중인 컴퓨터 또는 VM에서 서비스 패브릭 클러스터를 만들 수 있습니다. 다시 말해 온-프레미스든 클라우드 공급자든 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 서비스 패브릭 응용 프로그램을 배포하고 실행할 수 있습니다.

 서비스 패브릭은 서비스 패브릭 클러스터를 만들 수 있는 설치 패키지를 제공합니다. 원하는 위치에 배포 기능의 주요 장점은 응용 프로그램이 실행되는 위치를 선택하기 때문에 서비스 패브릭을 사용하여 응용 프로그램을 빌드할 때 공급업체 교착 상태가 없다는 것입니다. 또한 이 기능을 사용하면 고객 기반을 확장할 수 있는 가능성이 높아집니다. 고객이 응용 프로그램을 실행하려는 환경에 대한 요구 사항이 다양할 수 있기 때문입니다. 예를 들어 의료 및 금융 산업의 고객은 자동차 또는 여행 산업의 고객과 요구 사항이 다를 것입니다.

이 기능에 대한 Technical Preview는 2016년 1분기에 출시될 예정입니다.

## 지원되는 운영 체제
* Windows Server 2012 * Windows Server 2012 R2 * Windows Server 2016 * Linux 운영 체제를 실행하는 컴퓨터 또는 VM에서 클러스터를 만들 수 있습니다.

## 지원되는 프로그래밍 언어
* C# * Java 프로그래밍 언어를 사용하여 서비스 패브릭 응용 프로그램을 작성할 수 있습니다.

## 클러스터 만들기 및 구성
서비스 패브릭에서 다운로드할 수 있는 설치 패키지를 제공합니다. 이 패키지를 다운로드한 후에는 구성 파일을 변경하여 클러스터에 대한 설정을 지정해야 합니다. 클러스터 설정을 편집한 다음 설치 스크립트를 실행하여 클러스터 설정에서 지정한 컴퓨터에 클러스터 스패닝을 만듭니다.

설치 프로세스에 대한 정확한 정보는 2016년 1분기에 출시되는 이 기능의 미리 보기를 참조하세요.

## 클라우드 배포와 온-프레미스 배포 비교
서비스 패브릭 클러스터 온-프레미스를 만드는 프로세스는 VM 집합이 있는 클라우드를 선택하여 거기에 클러스터를 만드는 프로세스와 비슷합니다. VM을 프로비전하는 초기 단계는 사용하는 클라우드 또는 온-프레미스 환경에 의해 제어됩니다. VM을 준비하고 VM 간에 네트워크 연결을 설정한 후에는 마찬가지 방법으로 패브릭 패키지를 설정하고, 클러스터 설정을 편집하고, 클러스터 만들기 및 관리 스크립트를 실행합니다. 이렇게 하면 새 호스팅 환경을 대상으로 선택할 때 서비스 패브릭 클러스터 운영 및 관리에 대한 지식과 경험이 전달됩니다.

## 원하는 위치에 배포 기능의 이점
* 공급업체 종속성이 없기 때문에 응용 프로그램이 실행될 위치를 선택할 수 있습니다.
* 서비스 패브릭 응용 프로그램을 한 번 작성한 다음 변경 없이 또는 최소한의 변경으로 여러 호스팅 환경에서 실행할 수 있습니다.
* 호스팅 플랫폼 간에 서비스 패브릭 응용 프로그램 빌드에 대한 지식이 전달됩니다.
* 배포 환경 간에 서비스 패브릭 클러스터 실행에 대한 운영 경험이 전달됩니다.
* 호스팅 환경 제약을 받지 않아 고객 도달 범위가 넓습니다.
* 데이터 센터 또는 클라우드에 블랙아웃이 발생하면 서비스를 다른 배포 환경으로 이동할 수 있으므로 안정성이 강화되고 대규모로 가동이 중단되는 일이 없습니다.

## Azure의 서비스 패브릭 관리 클러스터가 원하는 위치에 배포 기능을 사용하여 생성 및 관리되는 서비스 패브릭 클러스터보다 나은 점
Azure에서 서비스 패브릭 클러스터를 실행하면 원하는 위치에 배포 옵션을 사용할 때보다 몇 가지 이점이 있습니다. 따라서 클러스터 실행 위치가 크게 중요하지 않은 경우 Azure에서 클러스터를 실행하는 것이 좋습니다. Azure에서는 클러스터의 작업 및 관리를 더 용이하게 해주는 다음과 같은 다른 Azure 기능 및 서비스와의 통합을 제공합니다.

* **Azure 포털:** Azure 포털을 사용하면 쉽게 클러스터를 만들고 관리할 수 있습니다.
* **Azure 리소스 관리자:** Azure 리소스 관리자를 사용하면 클러스터에서 사용하는 모든 리소스를 하나의 단위로 쉽게 관리할 수 있기 때문에 비용 추적 및 청구가 간소화됩니다.
* **Azure 리소스** 서비스 패브릭 클러스터는 ARM 리소스이므로 Azure의 다른 ARM 리소스와 같이 모델링할 수 있습니다.
* **진단:** Azure에서는 Azure 진단과 Operational Insights가 통합됩니다.
* **자동 크기 조정:** Azure의 클러스터는 자동 크기 조정 기능이 기본적으로 제공됩니다. 원하는 위치에 배포 기능을 사용하는 다른 환경에서는 사용자 고유 자동 크기 조정 기능을 빌드하거나 서비스 패브릭에서 클러스터 크기 조정을 위해 제공하는 API를 사용하여 수동으로 조정해야 합니다.

<!---HONumber=AcomDC_0224_2016-->