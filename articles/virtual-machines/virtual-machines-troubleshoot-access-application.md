<properties
	pageTitle="VM의 응용 프로그램 액세스 문제 해결 | Microsoft Azure"
	description="Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 액세스 할수 없는 경우, 문제의 원인을 분리해 내기 위해 다음 단계를 사용하세요."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="dkshir"/>

# Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Azure 가상 컴퓨터에서 실행중인 응용 프로그램에 액세스 할 수 없는 경우, 이 문서는 문제의 원인을 찾아내고 해당 문제를 수정하기 위한 체계적인 방법을 설명합니다.

> [AZURE.NOTE]  Azure 가상 컴퓨터에 연결하는 방법에 대한 도움말은 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md) 또는 [Linux 기반 Azure 가상 컴퓨터에 대한 SSH(보안 셸) 연결 문제 해결](virtual-machines-troubleshoot-ssh-connections.md)을 참조하세요.

Azure 가상 컴퓨터에서 실행되는 응용 프로그램의 액세스 문제 해결에 대한 4개의 주요 영역이 있습니다.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	Azure 가상 컴퓨터에서 실행되는 응용 프로그램
2.	Azure 가상 컴퓨터.
3.	가상 컴퓨터(서비스 관리 API를 사용하여 만든 가상 컴퓨터용), 인바운드 NAT(리소스 관리자에서 만든 가상 컴퓨터용) 및 네트워크 보안 그룹을 포함하는 클라우드 서비스에 대한 Azure 끝점.
4.	인터넷 에지 장치

사이트-사이트 VPN 또는 ExpressRoute 연결을 통해 응용 프로그램에 액세스하는 클라이언트 컴퓨터에 대해, 문제를 일으키는 주요 영역은 응용 프로그램 및 Azure 가상 컴퓨터입니다. 문제 및 해당 보정의 원인을 확인하려면 다음 단계를 수행합니다.

## 1단계: 목표 가상 컴퓨터에서 해당 응용 프로그램에 액세스 할 수 있습니까?

응용 프로그램이 실행 중인 가상 컴퓨터에서 적합한 클라이언트 프로그램으로 응용 프로그램에 액세스를 시도하려면, 로컬 호스트 이름, 로컬 IP 주소 또는 루프백 주소 (127.0.0.1)를 사용합니다.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

예를 들면, 응용 프로그램이 웹 서버인 경우, 가상 컴퓨터에서 브라우저를 실행하고 해당 가상 컴퓨터에서 호스팅되는 웹 페이지에 액세스를 시도합니다.

응용 프로그램에 액세스할 수 있는 경우 [2단계](#step2)로 이동합니다.

응용 프로그램에 액세스할 수 없는 경우, 다음 사항을 확인합니다.

- 응용 프로그램이 목표 가상 컴퓨터에서 실행 중입니다.
- 응용 프로그램이 예상된 TCP 및 UDP 포트에서 수신 중입니다.

Windows 및 Linux 기반 가상 컴퓨터 둘 다에서 **netstat -a** 명령을 사용하여 활성 수신 포트를 표시합니다. 응용 프로그램이 수신해야 할 예상되는 포트에 대한 출력을 검토하세요. 응용 프로그램을 다시 시작하거나 필요에 따라 예상되는 포트를 사용하도록 구성하세요.

## <a id="step2"></a>2단계: 동일한 가상 네트워크상의 다른 가상 컴퓨터에서 응용 프로그램에 액세스할 수 있습니까?

가상 컴퓨터의 호스트 이름 또는 해당 Azure-할당 공용, 개인 또는 공급자 IP 주소를 사용하여 응용 프로그램을 실행 중인 가상 컴퓨터와 동일한 가상 네트워크에 있는 또 다른 가상 컴퓨터에서 응용 프로그램에 대한 액세스를 시도하세요. 서비스 관리 API를 사용하여 만든 가상 컴퓨터의 경우 클라우드 서비스의 공용 IP 주소를 사용하지 않습니다.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

예를 들면, 응용 프로그램이 웹 서버인 경우, 동일한 가상 네트워크에 있는 다른 가상 컴퓨터의 브라우져에서 웹 페이지 액세스를 시도하세요.

응용 프로그램에 액세스할 수 있는 경우 [3단계](#step3)로 이동합니다.

응용 프로그램에 액세스할 수 없는 경우, 다음 사항을 확인합니다.

- 대상 가상 컴퓨터의 호스트 방화벽이 인바운드 요청 및 아웃 바운드 트래픽을 허용 중입니다.
- 목표 가상 컴퓨터에서 실행되는 침입 탐지 또는 네트워크 모니터링 소프트웨어가 트래픽을 허용 중입니다.
- 네트워크 보안 그룹이 트래픽을 허용 중입니다.
- 예컨대 부하 분산 장치 또는 방화벽과 같은 시험 가상 컴퓨터 및 가상 컴퓨터 간의 경로에서 사용자의 가상 네트워크에서 실행 중인 개별 구성 요소가 트래픽을 허용 중입니다.

Windows 기반 가상 컴퓨터에서, 방화벽 규칙이 사용자의 응용 프로그램의 인바운드 및 아웃 바운드 트래픽을 제외할지 여부를 확인하려면 고급 보안이 포함된 Windows 방화벽을 사용하세요.

## <a id="step3"></a>3단계: 가상 네트워크 외부에 있지만 사용자 컴퓨터와 동일한 네트워크에 연결되지 않은 컴퓨터에서 사용자가 응용 프로그램에 액세스할 수 있나요?

가상 컴퓨터에서 응용 프로그램이 실행되고 있지만, 사용자의 원래 클라이언트 컴퓨터와 동일한 네트워크 상에 있지 않은 경우, 가상 네트워크 외부의 컴퓨터에서 응용 프로그램에 대한 액세스를 시도하세요.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

예를 들면, 응용 프로그램이 웹 서버인 경우, 가상 네트워크에 있지 않은 컴퓨터에서 실행 중인 브라우져에서 웹페이지 액세스를 시도합니다.

응용 프로그램에 액세스할 수 없는 경우, 다음 사항을 확인합니다.

- 가상 컴퓨터에 대한 끝점 구성에서 서비스 관리 API를 사용하여 만든 가상 컴퓨터에 대한 수신 트래픽을 허용하는지, 특히 프로토콜(TCP 또는 UDP), 공용 및 개인 포트 번호가 허용되어 있는지 확인합니다. 자세한 내용은 [가상 컴퓨터로 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)을 참조하세요.
- 서비스 관리 API를 사용하여 만든 가상 컴퓨터에 대해, 끝점의 액세스 제어 목록(ACL)이 인트라넷에서 들어오는 트래픽을 막지 않는지 확인합니다. 자세한 내용은 [가상 컴퓨터로 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)을 참조하세요.
- 가상 컴퓨터에 대한 끝점 구성에서 리소스 관리자에서 만든 가상 컴퓨터에 대한 수신 트래픽을 허용하는지, 특히 프로토콜(TCP 또는 UDP), 공용 및 개인 포트 번호가 허용되어 있는지 확인합니다.
- 네트워크 보안 그룹이 인바운드 요청 및 아웃 바운드 요청 트래픽을 허용하는지 확인합니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?](../virtual-network/virtual-networks-nsg.md)을 참조하세요.

가상 컴퓨터 또는 끝점이 부하 분산 집합의 구성원인 경우:

- 프로브 프로토콜(TCP 또는 UDP) 및 포트 번호가 올바른지 확인합니다.
- 프로브 프로토콜 및 포트가 부하 분산 집합 프로토콜 및 포트와 다른 경우:
	- 응용 프로그램이 프로브 프로토콜(TCP 또는 UDP) 및 포트 번호에서 수신 대기 중인지 확인합니다(대상 가상 컴퓨터에서 **netstat –a** 사용).
	- 대상 가상 컴퓨터의 호스트 방화벽이 인바운드 프로브 요청 및 아웃 바운드 프로브 응답 트래픽을 허용하는지 확인합니다.

응용 프로그램에 액세스 할 수 있는 경우, 인터넷 에지 장치가 다음을 허용하는지 확인합니다.

- 아웃 바운드 응용 프로그램이 클라이언트 컴퓨터에서 부터 Azure 가상 컴퓨터에 도달하는 트래픽 요청
- Azure 가상 컴퓨터에서 발생하는 인바운드 응용 프로그램 응답 트래픽

## 끝점 연결 문제 해결

원격 데스크톱 끝점과 같은 끝점에 연결할 때 문제가 발생하면 다음과 같은 일반적인 문제 해결 단계를 시도합니다.

- 가상 컴퓨터 다시 시작
- 끝점 다시 만들기
- 다른 위치에서 연결
- 가상 컴퓨터 크기 조정
- 가상 컴퓨터 다시 만들기

자세한 내용은 [끝점 연결 문제 해결(RDP/SSH/HTTP 등의 오류)](https://social.msdn.microsoft.com/Forums/azure/ko-KR/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)를 참조하세요.

## 다음 단계

이 문서에서 위의 단계를 실행하였지만 문제 해결을 위해 추가적인 도움이 필요한 경우, 다음을 수행합니다.

- 전 세계에 있는 Azure 전문가로 부터 도움을 받으세요. MSDN Azure 또는 스택 오버플로 포럼에 귀하의 문제를 제출하세요. 자세한 내용은 [Microsoft Azure 포럼](https://azure.microsoft.com/support/forums/)을 참조하세요.
- Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **기술 및 대금 청구 지원**에서 **지원 받기**를 클릭합니다.

## 추가 리소스

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=AcomDC_0204_2016-->