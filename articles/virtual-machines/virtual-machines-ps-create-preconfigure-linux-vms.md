<properties
	pageTitle="Azure PowerShell을 사용하여 Windows VM 만들기 | Microsoft Azure"
	description="Azure PowerShell을 사용하여 Linux VM을 만들고 미리 구성하는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Azure PowerShell을 사용하여 Linux 가상 컴퓨터 만들기 및 미리 구성

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.
 
이 단계는 Azure PowerShell 명령 집합을 생성하는 방식으로 빈칸 채우기를 사용하여 Linux 가상 컴퓨터를 만드는 방법을 보여줍니다. 이 접근 방식은 Azure PowerShell을 처음 접하거나 성공적인 구성을 위해 지정할 값만 알기를 원하는 경우에 유용할 수 있습니다.

명령 블록을 텍스트 파일이나 PowerShell ISE에 복사해 넣은 후 변수 값을 채우고 < and > 문자를 제거하여 명령 집합을 빌드하게 됩니다. 최종 결과의 개념은 이 문서 끝의 두 [예제](#examples)를 참조하세요.

Windows 기반 가상 컴퓨터에 대한 관련 문서는 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기](virtual-machines-ps-create-preconfigure-windows-vms.md)를 참조하세요.

## Azure PowerShell 설치

아직 설치하지 않은 경우에는 [Azure PowerShell을 설치하고 구성](powershell-install-configure.md)합니다. 그런 다음 Azure PowerShell 명령 프롬프트를 엽니다.

## 구독 및 저장소 계정 설정

Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 Azure 구독 및 저장소 계정을 설정합니다.

**Get-AzureSubscription** 명령의 출력에 표시된 **SubscriptionName** 속성에서 올바른 구독 이름을 가져올 수 있습니다.

Select-AzureSubscription 명령을 실행한 후 **Get-AzureStorageAccount** 명령의 출력에 표시된 **Label** 속성에서 올바른 저장소 계정 이름을 가져올 수 있습니다.

< and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## 사용할 이미지 찾기

다음으로, 사용할 이미지에 대한 ImageFamily 값을 결정해야 합니다. 다음 명령을 사용하여 사용 가능한 ImageFamily 값 목록을 가져올 수 있습니다.

	Get-AzureVMImage | select ImageFamily -Unique

다음은 Linux 기반 컴퓨터의 ImageFamily 값에 대한 몇 가지 예입니다.

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

선택한 텍스트 편집기 또는 PowerShell ISE(통합 스크립팅 환경)의 새 인스턴스를 엽니다. 새 텍스트 파일 또는 ImageFamily 값을 대체하는 PowerShell ISE에 다음을 복사합니다.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## 이름, 크기를 지정하고 선택적으로 가용성 집합을 지정합니다.

다음 두 명령 블록 중 하나를 선택하여 명령 집합을 시작합니다(필수).

**옵션 1**: 가상 컴퓨터 이름 및 크기를 지정합니다.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**옵션 2**: 이름, 크기 및 가용성 집합 이름을 지정합니다.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D-, DS- 또는 G-시리즈 가상 컴퓨터에 대한 InstanceSize 값은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](https://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.


## 사용자 액세스 보안 옵션 설정

**옵션 1**: 초기 Linux 사용자 이름 및 암호를 지정합니다(필수). 강력한 암호를 선택합니다. 암호 강도를 확인하려면 [암호 검사기: 강력한 암호 사용](https://www.microsoft.com/security/pc-security/password-checker.aspx)을 참조하세요.

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**옵션 2**: 구독에 이미 배포된 SSH 키 쌍 집합을 지정합니다.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)을 참조하세요.

**옵션 3**: 구독에 이미 배포된 SSH 공개 키 쌍 목록을 지정합니다.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Linux 기반 가상 컴퓨터에 대한 추가적인 사전 구성 옵션은 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)에서 **Linux** 매개 변수 집합에 대한 구문을 참조하세요.


## 선택 사항: 고정 DIP 할당

선택적으로 가상 컴퓨터에 특정 IP 주소(고정 DIP라고 함)를 할당합니다.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

다음 명령으로 특정 IP 주소를 사용할 수 있는지 확인할 수 있습니다.

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## 선택 사항: 특정 서브넷에 가상 컴퓨터를 할당합니다. 

Azure 가상 네트워크의 특정 서브넷에 가상 컴퓨터를 할당합니다.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## 선택 사항: 데이터 디스크 추가
	
가상 컴퓨터에 데이터 디스크를 추가하려면 다음 내용을 명령 집합에 추가합니다.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## 선택 사항: 기존 부하 분산에 가상 컴퓨터를 추가합니다. 

외부 트래픽에 대한 기존 부하 분산된 집합에 가상 컴퓨터를 추가하려면 다음 내용을 명령 집합에 추가합니다.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## 가상 컴퓨터 만들기 프로세스를 시작하는 방법 결정 

다음 명령 블록 중 하나를 선택하여 가상 컴퓨터 만들기 프로세스를 시작하려면 블록을 명령 집합에 추가합니다.

**옵션 1**: 기존 클라우드 서비스에서 가상 컴퓨터를 만듭니다.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

클라우드 서비스의 짧은 이름은 Azure 클래식 포털의 Azure 클라우드 서비스 목록에 표시된 이름 또는 Azure 포털의 리소스 그룹 목록에 표시된 이름입니다.

**옵션 2**: 기존 클라우드 서비스 및 가상 네트워크에서 가상 컴퓨터를 만듭니다.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 명령 집합 실행

텍스트 편집기 또는 PowerShell ISE에서 작성한 Azure PowerShell 명령 집합을 검토하고 변수를 모두 지정했는지 변수의 값이 올바른지 확인합니다. 또한 < and > 문자를 모두 제거했는지 확인합니다.

클립보드에 명령 집합을 복사한 다음 열려 있는 Azure PowerShell 명령 프롬프트를 마우스 오른쪽 버튼으로 클릭합니다. 그러면 명령 집합이 일련의 PowerShell 명령으로 실행되고 Azure 가상 컴퓨터가 만들어집니다.

가상 컴퓨터를 만든 후 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요.

명령 집합을 다시 사용하려면 다음을 수행합니다.

- 이 명령 집합을 PowerShell 스크립트 파일(*.ps1)로 저장
- Azure 클래식 포털의 **자동화** 섹션에서 이 명령 집합을 Azure 자동화 Runbook으로 저장합니다.

## <a id="examples"></a>예제

다음은 앞의 단계를 사용하여 Azure에서 Linux 기반 가상 컴퓨터를 만드는 Azure PowerShell 명령 집합을 작성하는 두 가지 예제입니다.

### 예제 1

다음과 같은 MySQL Server용 Linux 가상 컴퓨터를 처음 만드는 데 사용할 수 있는 PowerShell 명령 집합이 필요합니다.

- Ubuntu Server 12.10 이미지를 사용함
- 이름이 AZMYSQL1임
- 500GB의 추가 데이터 디스크가 있음
- 고정 IP 주소가 192.168.244.4임
- AZDatacenter 가상 네트워크의 BackEnd 서브넷에 있음
- Azure-TailspinToys 클라우드 서비스에 있음

다음은 이 가상 컴퓨터를 만드는 해당 Azure PowerShell 명령 집합입니다. 각 블록 사이의 빈 줄은 가독성을 위해 추가된 것입니다.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### 예 2

다음과 같은 Apache 서버용 Linux 가상 컴퓨터를 만드는 데 사용할 수 있는 PowerShell 명령 집합이 필요합니다.

- SUSE Linux Enterprise Server 12 이미지를 사용함
- 이름이 LOB1임
- 50GB의 추가 데이터 디스크가 있음
- 표준 웹 트래픽에 대한 LOBServers 부하 분산 장치 집합의 멤버임
- AZDatacenter 가상 네트워크의 FrontEnd 서브넷에 있음
- Azure-TailspinToys 클라우드 서비스에 있음

다음은 이 가상 컴퓨터를 만드는 해당 Azure PowerShell 명령 집합입니다.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 추가 리소스

[가상 컴퓨터 설명서](https://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure 가상 컴퓨터 FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure 가상 컴퓨터 개요](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)

[Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=AcomDC_0204_2016-->