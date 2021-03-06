<properties
	pageTitle="VM에 Symantec Endpoint Protection 설치 | Microsoft Azure"
	description="기존 배포 모델을 사용하여 새로운 또는 기존 Azure VM에 Symantec Endpoint Protection 보안 확장을 설치하고 구성하는 방법에 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>

# Windows VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 문서에서는 Windows Server가 실행되는 새 VM(가상 컴퓨터) 또는 기존 VM에서 Symantec Endpoint Protection 클라이언트를 설치하고 구성하는 방법을 보여 줍니다. 이 제품은 바이러스 및 스파이웨어 보호, 방화벽, 침입 방지와 같은 서비스를 포함하는 전체 클라이언트입니다.

이 클라이언트는 VM 에이전트를 사용하여 보안 확장 프로그램으로 설치됩니다. 새 가상 컴퓨터에서 끝점 클라이언트와 함께 이 에이전트를 설치합니다. 이 에이전트가 없는 기존 가상 컴퓨터에서는 에이전트를 먼저 다운로드하여 설치해야 합니다. 이 문서에서는 두 상황을 모두 다룹니다.

온-프레미스 솔루션에 대해 Symantec에 기존 구독이 있는 경우 이를 사용하여 Azure 가상 컴퓨터를 보호할 수 있습니다. 아직 고객이 아닌 경우에는 평가판 구독에 등록할 수 있습니다. 이 솔루션에 대한 자세한 내용은 [Microsoft Azure 플랫폼의 Symantec Endpoint Protection][Symantec](영문)을 참조하세요. 이 페이지에는 Symantec 고객을 위한 라이선스 정보 및 이 클라이언트를 설치하는 방법에 대한 링크도 제공되어 있습니다.

## 새 가상 컴퓨터에 Symantec Endpoint Protection 설치

[갤러리에서][Portal] 옵션을 사용하여 가상 컴퓨터를 만들 경우 **Azure 클래식 포털**을 사용하여 VM 에이전트 및 Symantec 보안 확장을 설치할 수 있습니다. 이 방법을 사용하면 단일 가상 컴퓨터를 만들 때 Symantec의 보호 기능을 쉽게 추가할 수 있습니다.

이 **갤러리에서** 옵션은 가상 컴퓨터 설치에 도움이 되는 마법사를 엽니다. 마법사 마지막 페이지에서 VM 에이전트 및 Symantec 보안 확장을 설치합니다.

일반적인 지침은 [Windows Server를 실행하는 가상 컴퓨터 만들기][Create]를 참조하세요. 마법사 마지막 페이지가 되면 다음을 수행합니다.

1.	VM 에이전트에서 **VM 에이전트 설치**가 이미 선택되어 있어야 합니다.

2.	보안 확장에서 **Symantec Endpoint Protection**을 선택합니다.


	![VM 에이전트 및 Endpoint Protection Client 설치](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	페이지 아래쪽에 있는 확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.

## 기존 가상 컴퓨터에 Symantec Endpoint Protection 설치

이 작업을 수행하려면 다음이 필요합니다.

- Azure PowerShell 모듈 버전 0.8.2 이상이 작업 컴퓨터에 설치되어 있어야 합니다. **Get-Module azure | format-table version** 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다. 지침 및 최신 버전에 대한 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법][PS]을 참조하세요. Azure 구독에 로그인해야 합니다.

- Azure 가상 컴퓨터에서 VM 에이전트를 실행해야 합니다.

먼저, VM 에이전트가 가상 컴퓨터에 이미 설치되어 있는지 확인합니다. 클라우드 서비스 이름과 가상 컴퓨터 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

> [AZURE.TIP]클라우드 서비스 및 가상 컴퓨터 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 가상 컴퓨터에 대한 이름을 나열합니다.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

**write-host** 명령에서 **True**가 표시되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 표시되면 Azure 블로그 게시물 [VM 에이전트 및 확장 - 2부][Agent]에서 지침 및 다운로드 링크를 참조합니다.

VM 에이전트가 설치된 경우 이러한 명령을 실행하여 Symantec Endpoint Protection 에이전트를 설치합니다.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Symantec 보안 확장이 설치되고 최신 상태인지 확인하려면 다음을 수행합니다.

1.	가상 컴퓨터에 로그온합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]을 참조하세요.
2.	Windows Server 2008 R2의 경우, **시작 > Symantec Endpoint Protection**을 클릭합니다. Windows Server 2012 또는 Windows Server 2012 R2의 경우, 시작 화면에서 **Symantec**을 입력하고 **Symantec Endpoint Protection**을 클릭합니다.
3.	**Status-Symantec Endpoint Protection** 창의 **상태** 탭에서 필요한 경우 업데이트를 적용하거나 다시 시작합니다.

## 추가 리소스

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]

[Azure VM 확장 및 기능][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial-classic-portal.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_1203_2015-->