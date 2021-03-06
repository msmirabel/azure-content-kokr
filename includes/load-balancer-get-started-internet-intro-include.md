부하 분산 장치를 사용하여 Azure에서 작업에 고가용성을 제공할 수 있습니다. Azure 부하 분산 장치는 부하 분산 장치 집합에 정의된 클라우드 서비스 또는 가상 컴퓨터의 정상 서비스 인스턴스 간에 들어오는 트래픽을 배포하는 계층-4(TCP, UDP) 유형의 부하 분산 장치입니다.
 
부하 분산 장치를 구성할 수 있습니다.

- 들어오는 인터넷 트래픽을 가상 컴퓨터(VM)에 부하 분산합니다. 이 시나리오에서 부하 분산 장치를 [인터넷 연결 부하 분산 장치](../articles/load-balancer/load-balancer-internet-overview.md)라고 합니다.
- 가상 네트워크(VNet)의 VM 간, 클라우드 서비스의 VM 간 또는 크로스-프레미스 가상 네트워크의 온-프레미스 컴퓨터와 VM 간에 트래픽을 부하 분산합니다. 이 시나리오에서 부하 분산 장치를 [내부 부하 분산 장치(ILB)](../articles/load-balancer/load-balancer-internal-overview.md)라고 합니다.
- 	외부 트래픽을 특정 VM 인스턴스로 전달합니다.

<!---HONumber=AcomDC_0224_2016-->