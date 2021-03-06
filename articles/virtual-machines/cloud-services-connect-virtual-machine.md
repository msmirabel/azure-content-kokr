<properties
	pageTitle="클라우드 서비스에서 VM 연결 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든 가상 컴퓨터를 Azure 클라우드 서비스 또는 가상 네트워크에 연결합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# 클래식 배포 모델을 사용하여 만든 가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스에 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


클래식 배포 모델을 사용하여 만든 가상 컴퓨터는 항상 클라우드 서비스에 배치됩니다. 클라우드 컴퓨터는 컨테이너 역할을 하며 인터넷을 통해 가상 컴퓨터에 액세스할 수 있도록 고유한 공용 DNS 이름, 공용 IP 주소 및 끝점 집합을 제공합니다. 클라우드 서비스는 가상 네트워크에 있을 수 있지만 요구 사항은 아닙니다.

가상 네트워크에 없는 클라우드 서비스를 *독립 실행형* 클라우드 서비스라고 합니다. 독립 실행형 클라우드 서비스의 가상 컴퓨터는 다른 가상 컴퓨터의 공용 DNS 이름을 사용하여 다른 가상 컴퓨터와 통신만 할 수 있고 인터넷을 통해 트래픽을 이동시킵니다. 클라우드 서비스가 가상 네트워크에 있는 경우 해당 클라우드 서비스의 가상 컴퓨터는 인터넷을 통해 트래픽을 보내지 않고도 가상 네트워크에 있는 다른 모든 가상 컴퓨터와 통신할 수 있습니다.

같은 독립 실행형 클라우드 서비스에 가상 컴퓨터를 배치하는 경우에는 계속 부하 분산 및 가용성 집합을 사용할 수 있습니다. 자세한 내용은 [가상 컴퓨터 부하 분산](load-balance-virtual-machines.md) 및 [가상 컴퓨터의 가용성 관리](virtual-machines-manage-availability.md)를 참조하세요. 그러나 서브넷에서 가상 컴퓨터를 구성하거나 독립 실행형 클라우드 서비스를 온-프레미스 네트워크에 연결할 수는 없습니다. 예를 들면 다음과 같습니다.

![독립 실행형 클라우드 서비스의 가상 컴퓨터](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

가상 컴퓨터를 가상 네트워크에 둔 경우 부하 분산 및 가용성 집합에 사용할 클라우드 서비스 수를 결정할 수 있습니다. 또한 온-프레미스 네트워크와 동일한 방법으로 서브넷에서 가상 컴퓨터를 구성하고 가상 컴퓨터를 온-프레미스 네트워크에 연결할 수 있습니다. 예를 들면 다음과 같습니다.

![가상 네트워크의 가상 컴퓨터](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

가상 네트워크는 Azure에서 가상 컴퓨터를 연결하는 데 권장되는 방법입니다. 응용 프로그램의 각 계층을 별도의 클라우드 서비스에 구성하는 것이 가장 좋습니다. 그러나 구독당 클라우드 서비스 수를 최대 200개의 범위 내에서 유지하기 위해 여러 응용 프로그램 계층의 일부 가상 컴퓨터를 동일한 클라우드 서비스에 통합해야 할 수도 있습니다. 이 제한 및 다른 제한을 검토하려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](azure-subscription-service-limits.md)을 참조하세요.

## 가상 네트워크에서 VM 연결

가상 네트워크에서 가상 컴퓨터를 연결하려면

1.	[Azure 포털](virtual-networks-create-vnet-classic-pportal.md)에서 가상 네트워크를 만듭니다.
2.	가용성 집합 및 부하 분산에 대한 디자인을 반영하기 위해 배포에 대한 클라우드 서비스의 집합을 만듭니다. Azure 클래식 포털에서 각 클라우드 서비스에 대해 **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 클릭합니다.
3.	각각의 새 가상 컴퓨터를 만들려면 **새로 만들기 > 계산 > 가상 컴퓨터 > 갤러리에서**를 클릭합니다. VM에 대한 올바른 클라우드 서비스 및 가상 네트워크를 선택합니다. 클라우드 서비스가 가상 네트워크에 이미 연결된 경우 해당 이름이 이미 선택되어 있을 것입니다.

![가상 컴퓨터에 대한 클라우드 서비스 선택](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## 독립 실행형 클라우드 서비스에서 VM 연결

독립 실행형 클라우드 서비스에서 가상 컴퓨터를 연결하려면

1.	[Azure 클래식 포털](http://manage.windowsazure.com)에서 클라우드 서비스를 만듭니다. **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 클릭합니다. 또는 첫 번째 가상 컴퓨터를 만들 때 배포에 대한 클라우드 서비스를 만들 수 있습니다.

2.	가상 컴퓨터를 만들 때 이전 단계에서 만든 클라우드 서비스의 이름을 선택합니다.

	![기존 클라우드 서비스에 가상 컴퓨터 추가](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##리소스
[가상 컴퓨터 부하 분산](load-balance-virtual-machines.md)

[가상 컴퓨터의 가용성 관리](virtual-machines-manage-availability.md)

가상 컴퓨터를 만들고 나서 서비스 및 워크로드에 데이터를 저장할 위치가 포함되도록 데이터 디스크를 추가하는 것이 좋습니다. 다음 중 하나를 참조하세요.

[Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-linux-how-to-attach-disk.md)

[Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](storage-windows-attach-disk.md)

<!---HONumber=AcomDC_0211_2016-->