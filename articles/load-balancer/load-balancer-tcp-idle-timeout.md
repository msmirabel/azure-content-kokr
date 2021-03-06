<properties 
   pageTitle="부하 분산 장치 TCP 유휴 시간 제한 구성 | Microsoft Azure"
   description="부하 분산 장치 TCP 유휴 시간 제한 구성"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />

# 부하 분산 장치에 대한 TCP 유휴 시간 제한 설정을 변경하는 방법

기본 구성에서 Azure 부하 분산 장치의 '유휴 시간 제한' 설정은 4분입니다.

즉, tcp 또는 http 세션에서 시간 제한 값보다 오랫동안 활동이 없으면 클라이언트와 서비스 간의 연결이 유지되지 않습니다.

연결이 닫히면 클라이언트 응용 프로그램에 "기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다."와 같은 오류 메시지가 표시됩니다.

더 오랫동안 연결을 활성 상태로 유지하는 일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. [여기](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)서 .NET 예제를 확인할 수 있습니다.

연결에서 활동이 감지되지 않으면 패킷이 전송됩니다. 지속적인 네트워크 활동을 유지하면 유휴 시간 제한 값이 적중되지 않으며 연결이 장기간 유지됩니다.

아이디어는 기본 시간 제한 설정보다 짧은 간격으로 TCP 연결 유지를 구성하여 연결 끊김을 방지하거나 TCP 연결 세션에 대한 유휴 시간 제한 값을 늘려 연결 상태를 유지하는 것입니다.

TCP 연결 유지는 배터리가 제약 조건이 아닌 시나리오에서는 효과적이지만, 일반적으로 모바일 응용 프로그램에 유효한 옵션은 아닙니다. 모바일 응용 프로그램에서 TCP 연결 유지를 사용하면 장치 배터리가 더 빨리 방전됩니다.

이러한 시나리오를 지원하기 위해 구성 가능한 유휴 시간 제한에 대한 지원이 추가되었습니다. 이제 4분에서 30분 사이의 기간으로 설정할 수 있습니다. 이 설정은 인바운드 연결에서만 작동합니다.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## 가상 컴퓨터 및 클라우드 서비스의 유휴 시간 제한 설정을 변경하는 방법

- PowerShell 또는 서비스 관리 API를 통해 가상 컴퓨터의 끝점에 대해 TCP 시간 제한 구성
- PowerShell 또는 서비스 관리 API를 통해 부하 분산 끝점 집합에 대해 TCP 시간 제한 구성
- 인스턴스 수준 공용 IP의 TCP 유휴 시간 제한 구성
- 서비스 모델을 통해 웹/작업자 역할에 대해 TCP 시간 제한 구성
 

>[AZURE.NOTE]일부 명령은 최신 Azure PowerShell 패키지에만 있습니다. PowerShell 명령이 없는 경우 최신 PowerShell 패키지를 다운로드합니다.

 
### 인스턴스 수준 공용 IP의 TCP 유휴 시간 제한을 15분으로 구성

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes는 선택 사항이며 설정하지 않으면 기본 시간 제한은 4분으로 설정됩니다.

>[AZURE.NOTE]허용되는 시간 제한 범위는 4분에서 30분 사이입니다.
 
### 가상 컴퓨터에서 Azure 끝점을 만들 때 유휴 시간 제한 설정

끝점에 대한 시간 제한 설정을 변경하려면

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
유휴 시간 제한 구성 검색

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
 
### 부하 분산된 끝점 집합에 대한 TCP 시간 제한 설정

부하 분산된 끝점 집합에 끝점이 포함되어 있으면 부하 분산된 끝점 집합에 대해 TCP 시간 제한을 설정해야 합니다.

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### 클라우드 서비스에 대한 시간 제한 설정 변경

Azure SDK for .NET 2.4를 사용하여 클라우드 서비스를 업데이트할 수 있습니다.

.csdef에서 클라우드 서비스용 끝점 설정을 지정합니다. 클라우드 서비스 배포의 TCP 시간 제한을 업데이트하려면 배포를 업그레이드해야 합니다. 단, 공용 IP에 대해서만 TCP 시간 제한을 지정하는 경우는 예외입니다. 공용 IP 설정은 .cscfg에 포함되어 있으므로 배포 업데이트 및 업그레이드를 통해 업데이트할 수 있습니다.

아래에 .csdef에서 끝점 설정을 변경하는 예제가 나와 있습니다.

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

공용 IP의 시간 제한 설정에 대한 .cscfg 변경 내용은 다음과 같습니다.

	<NetworkConfiguration>
 	 <VirtualNetworkSite name="VNet"/>
 	 <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
 	 </AddressAssignments>
	</NetworkConfiguration>

## Rest API 예제

서비스 관리 API를 사용하여 TCP 유휴 시간 제한을 구성할 수 있습니다. 이때 x-ms-version 헤더는 버전 2014-06-01 이상으로 설정해야 합니다.
 
배포의 모든 가상 컴퓨터에서 지정한 부하 분산된 입력 끝점의 구성 업데이트
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## 다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](load-balancer-internet-getstarted.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

 

<!---HONumber=AcomDC_1125_2015-->