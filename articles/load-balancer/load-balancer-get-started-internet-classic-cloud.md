<properties 
   pageTitle="클라우드 서비스를 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치 만들기 시작 | Microsoft Azure"
   description="클라우드 서비스를 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/30/2015"
   ms.author="joaoma" />

# 클라우드 서비스를 위한 인터넷 연결 부하 분산 장치 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-cli.md).

클라우드 서비스는 자동으로 부하 분산 장치를 사용하여 구성되며 서비스 모델을 통해 사용자 지정될 수 있습니다.

## 서비스 정의 파일을 사용하여 부하 분산 장치 만들기
 
Azure SDK for .NET 2.5를 사용하여 클라우드 서비스를 업데이트할 수 있습니다. 클라우드 서비스에 대한 끝점 설정은 [service definition](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef 파일에서 수행합니다.

다음 예제에서는 클라우드 배포에 대한 servicedefinition.csdef 파일이 구성되는 방식을 보여 줍니다.

클라우드 배포에 의해 생성된 .csdef 파일의 코드 조각을 검사하면 포트 10000, 10001 및 10002에서 HTTP 포트를 사용하도록 구성된 외부 끝점을 확인할 수 있습니다.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




## 클라우드 서비스에 대한 부하 분산 장치 상태 확인


다음은 상태 프로브의 예입니다.

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

부하 분산 장치는 끝점의 정보와 프로브의 정보를 결합하여 서비스 상태를 쿼리하는 데 사용할 수 있는 http://{DIP of VM}:80/Probe.aspx 형식의 URL을 만듭니다.

서비스는 같은 IP 주소에서 주기적 프로브를 검색합니다. 이는 가상 컴퓨터가 실행되는 노드의 호스트에서 들어오는 상태 프로브 요청입니다. 부하 분산 장치에서 서비스가 정상이라고 가정하려면 서비스가 HTTP 200 상태 코드로 응답해야 합니다. 다른 모든 HTTP 상태 코드(예: 503)는 직접 순환에서 가상 컴퓨터를 제거합니다.

또한 프로브 정의는 프로브 빈도를 제어합니다. 위 사례에서 부하 분산 장치는 5초마다 끝점을 검색합니다. 10초(2개 프로브 간격) 동안 긍정적인 응답이 수신되지 않으면 프로브가 다운된 것으로 간주되며 가상 컴퓨터가 순환에서 제거됩니다. 마찬가지로, 서비스가 순환에서 제거되고 긍정적인 응답이 수신되면 서비스가 즉시 순환에 다시 배치됩니다. 서비스가 정상 상태와 비정상 상태 간에 변동되는 경우 서비스가 많은 프로브 동안 정상 상태로 유지될 때까지 부하 분산 장치에서 서비스를 다시 순환에 배치하는 것을 지연할 수 있습니다.

자세한 내용은 [상태 프로브](https://msdn.microsoft.com/library/azure/jj151530.aspx)에 대한 서비스 정의 스키마를 참조하세요.

## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-internal-getstarted.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->