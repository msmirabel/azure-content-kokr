<properties
   pageTitle="Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치 구성 시작 | Microsoft Azure"
   description="Azure 리소스 관리자에 대한 부하 분산 장치 규칙, NAT 규칙 및 프로브를 만드는 방법을 알아봅니다. 부하 분산 장치 리소스를 만드는 종단 간 프로세스를 보여 주는 단계별 지침입니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치 구성 시작


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)


아래 단계에서는 PowerShell과 함께 Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 보여 줍니다. Azure 리소스 관리자를 사용하면 인터넷 연결 부하 분산 장치를 만드는 항목이 개별적으로 구성된 다음 함께 사용되어 리소스를 만듭니다.

이 페이지에서는 부하 분산 장치를 만들기 위해 수행되는 개별 작업의 순서를 알아보고 부하 분산 장치를 만들기 위해 수행해야 하는 작업을 자세히 설명합니다.


## 인터넷 연결 부하 분산 장치를 만드는 데 필요한 항목은 무엇입니까?

부하 분산 장치를 만들기 전에 다음 항목을 구성해야 합니다.

- 프런트 엔드 IP 구성 - 들어오는 네트워크 트래픽의 부하가 분산될 수 있도록 프런트 엔드 IP 풀에 공용 IP 주소를 추가합니다. 

- 백 엔드 주소 풀 - 프런트 엔드 IP 풀에서 들어오는 부하 분산된 트래픽을 수신하는 네트워크 인터페이스를 구성합니다.

- 부하 분산 규칙 - 부하 분산 장치의 원본 및 로컬 포트 구성입니다.

- 프로브 - 가상 컴퓨터 인스턴스의 상태 프로브를 구성합니다.

- 인바운드 NAT 규칙 - 가상 컴퓨터 인스턴스 중 하나에 직접 액세스하는 포트 규칙을 구성합니다.

Azure 리소스 관리자의 분산 장치 구성 요소에 대한 자세한 내용은 [부하 분산 장치에 대한 Azure 리소스 관리자 지원](load-balancer-arm.md)에서 확인할 수 있습니다.

다음 단계에서는 두 가상 컴퓨터 간에 부하 분산 장치를 구성하는 방법을 보여 줍니다.


## Powershell을 사용 단계별 지침


### 부하 분산 장치에 대한 리소스 그룹 만들기


### 1단계
ARM cmdlet을 사용하려면 PowerShell 모드를 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell](powershell-azure-resource-manager.md) 사용을 참조하세요.


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### 2단계

Azure 계정에 로그인


    PS C:\> Add-AzureAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.


### 3단계

사용할 Azure 구독을 선택합니다.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

사용 가능한 구독 목록을 보려면 ‘Get-AzureSubscription’ cmdlet을 사용합니다.


### 4단계

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 부하 분산 장치를 만드는 모든 명령에서 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "NRP-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

## 프런트 엔드 IP 풀에 대한 공용 IP 주소 및 가상 네트워크 만들기


### 1단계

가상 네트워크 만들기:

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

가상 네트워크의 서브넷을 만들고 $backendSubnet에 할당합니다.

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

가상 네트워크를 만들고 NRPVNet 가상 네트워크에 lb-subnet-be 서브넷을 추가합니다.

### 2단계

프런트 엔드 IP 풀에서 사용할 공용 IP 주소 만들기:

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip 

>[AZURE.NOTE]공용 IP 주소 도메인 이름 레이블 속성은 부하 분산 장치의 FQDN에 대한 접두사가 됩니다.

## 프런트 엔드 IP 풀 및 백 엔드 주소 풀 만들기

들어오는 부하 분산 장치 네트워크 트래픽에 대한 프런트 엔드 IP 풀을 설정하고 부하 분산된 트래픽을 수신할 백 엔드 주소 풀을 설정합니다.

### 1단계 

공용 IP 변수($publicIP)를 사용하여 프런트 엔드 IP 풀을 만듭니다.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 


### 2단계 

프런트 엔드 IP 풀에서 들어오는 트래픽을 받는 데 사용되는 백 엔드 주소 풀을 설정합니다.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## LB 규칙, NAT 규칙, 프로브 및 부하 분산 장치 만들기

프런트 엔드 IP 풀과 백 엔드 주소 풀을 만든 후에는 부하 분산 장치 리소스에 속하는 규칙을 만들어야 합니다.

### 1단계

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


위 예제에서는 다음 항목을 만듭니다.

- 포트 3441로 들어오는 모든 트래픽을 포트 3389로 이동하는 NAT 규칙
- 포트 3442로 들어오는 모든 트래픽을 포트 3389로 이동하는 두 번째 NAT 규칙
- 공용 포트 80의 들어오는 모든 트래픽을 백 엔드 주소 풀의 로컬 포트 80으로 부하 분산하는 부하 분산 장치 규칙
- 경로 "HealthProbe.aspx"에 대한 상태를 확인하는 프로브 규칙



### 2단계

모든 개체(NAT 규칙, 부하 분산 장치 규칙, 프로브 구성)를 함께 추가하는 부하 분산 장치를 만듭니다.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## 네트워크 인터페이스 만들기

부하 분산 장치를 만든 후에는 들어오는 부하 분산된 네트워크 트래픽, NAT 규칙 및 프로브를 수신할 네트워크 인터페이스를 정의해야 합니다. 이 경우 네트워크 인터페이스는 개별적으로 구성되며 나중에 가상 컴퓨터에 할당할 수 있습니다.


### 1단계 


네트워크 인터페이스를 만들 리소스 가상 네트워크 및 서브넷 가져오기:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


이 단계에서는 부하 분산 장치 백 엔드 풀에 속하는 네트워크 인터페이스를 만들고 이 네트워크 인터페이스의 RDP에 대한 첫 번째 NAT 규칙을 연결합니다.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 2단계

LB-Nic2-BE라는 두 번째 네트워크 인터페이스 만들기:

이 단계에서는 두 번째 네트워크 인터페이스를 만들어 동일한 부하 분산 장치 백 엔드 풀에 할당하고 RDP에 대해 만든 두 번째 NAT 규칙을 연결합니다.

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


최종 결과는 다음과 같이 표시됩니다.


PS C:\> $backendnic1


	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### 3단계 

Add-AzureVMNetworkInterface 명령을 사용하여 가상 컴퓨터에 NIC를 할당합니다.

가상 컴퓨터를 만들고 NIC에 할당하는 단계별 지침은 [리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) 옵션 4 또는 5를 참조하세요.

## 기존 부하 분산 장치 업데이트


### 1단계

위의 예제에서 부하 분산 장치를 사용하여 부하 분산 장치 개체를 Get-AzureLoadBalancer를 사용하는 변수 $slb에 할당

	$slb=get-azureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

### 2단계

다음 예제에서는 프런트 엔드에 있는 포트 81과 백 엔드 풀의 포트 8181을 사용하여 새 인바운드 NAT 규칙을 기존 부하 분산 장치에 추가

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 3단계

Set-AzureLoadBalancer를 사용하여 새 구성 저장

	$slb | Set-AzureLoadBalancer

## 부하 분산 장치 제거하기

Remove-AzureLoadBalancer 명령을 사용하여 “NRP-RG”라는 리소스 그룹에서 이전에 생성한 "NRP-LB"라는 부하 분산 장치 삭제

	Remove-AzureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 선택적 스위치 -Force를 사용하여 삭제에 대한 프롬프트를 방지할 수 있습니다.


## 참고 항목

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0218_2016-->