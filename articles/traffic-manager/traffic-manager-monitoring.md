<properties 
   pageTitle="트래픽 관리자 모니터링"
   description="이 문서에서는 트래픽 관리자 모니터링을 이해하고 구성할 수 있습니다."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# 트래픽 관리자 모니터링 정보

Azure 트래픽 관리자는 클라우드 서비스 및 웹 사이트를 포함한 끝점을 모니터링하여 사용 가능한 지 확인합니다. 제대로 모니터링하려면 트래픽 관리자 프로필에서 지정한 순서대로 모든 끝점에 동일한 방식으로 설정해야 합니다. 모니터링을 구성하면 트래픽 관리자가 Azure 클래식 포털의 끝점과 프로필 상태를 표시합니다. 트래픽 관리자 프로필에 대한 구성 페이지의 Azure 클래식 포털에서 모니터링 설정을 구성할 수 있습니다. 다음 설정을 지정할 수 있습니다.

- **프로토콜** – HTTP 또는 HTTPS를 선택합니다. HTTPS 모니터링은 SSL 인증서가 유효한 지 여부를 확인하지 않으며 인증서가 존재한다는 것만 확인합니다.

- **포트** – 요청에 사용되는 포트를 선택합니다. 표준 HTTP 및 HTTPS 포트 중에서 선택할 수 있습니다.

- **상대 경로 및 파일 이름** – 모니터링 시스템이 액세스하려는 파일 이름과 경로를 지정합니다. 슬래시 "/"는 상대 경로로 유효한 항목이며 파일이 루트 디렉터리에 있음(기본값)을 의미합니다.

## 모니터링 상태 정보

Azure 트래픽 관리자는 Azure 클래식 포털의 프로필과 서비스 상태를 표시합니다. 프로필 및 끝점 모두에 대한 상태 열에는 최신 모니터링 상태가 표시 됩니다. 이 상태를 통해 트래픽 관리자 모니터링 설정에 따른 프로필 상태를 이해할 수 있습니다. 프로필이 정상 상태 이면 DNS 쿼리는 프로필의 트래픽 라우팅 설정(라운드 로빈, 성능 또는 장애 조치(Failover))에 따라 서비스에 배포 됩니다. 트래픽 관리자 모니터링 시스템이 모니터 상태에서 변경을 감지하면 자체적으로 Azure 클래식 포털의 상태 항목을 업데이트합니다. 상태 변경을 새로 고치는 데 최대 5분이 걸릴 수 있습니다.

### 끝점 모니터 상태

아래 표의 끝점 모니터 상태에는 끝점 상태 검색 결과 , 사용자 프로필 및 끝점 구성을 조합한 결과입니다.

|프로필 상태|끝점 상태|끝점 모니터 상태(API 및 포털)|참고|
|---|---|---|---|
|사용 안 함|사용|비활성|사용할 수 없는 프로필은 모니터링되지 않습니다. 하지만 사용할 수 없는 프로필 내의 끝점 상태는 관리할 수 있습니다.|
|&lt;일부&gt;|사용 안 함|사용 안 함|사용할 수 없는 프로필은 모니터링되지 않습니다. 하지만 사용할 수 없는 프로필 내의 끝점 상태는 관리할 수 있습니다.|
|사용|사용|온라인|끝점은 모니터링되며 정상 상태입니다.|
|사용|사용|성능 저하됨|끝점은 모니터링되며 비정상 상태입니다.|
|사용|사용|CheckingEndpoint|끝점이 모니터링되지만 첫 번째 검사 결과가 아직 수신되지 않았습니다. 새 끝점을 프로필에 방금 추가했거나 끝점이나 프로필을 방금 사용 가능하도록 설정한 경우 임시 상태입니다.|
|사용|사용|중지됨|기본 클라우드 서비스나 웹 사이트가 실행되고 있지 않습니다.|

### 프로필 모니터 상태

아래 표의 프로필 모니터 상태는 끝점 모니터 상태와 구성된 프로필 상태 조합의 결과입니다.

|프로필 상태(구성된 대로)|끝점 모니터 상태|프로필 모니터 상태(API 및 포털)|참고|
|---|---|---|---|
|사용 안 함|&lt;일부&gt; 또는 정의된 끝점이 없는 프로필|사용 안 함|끝점이 모니터링되지 않습니다.|
|사용|하나 이상의 끝점의 상태가 “Degraded”입니다.|성능 저하됨|이것은 고객 작업이 필요하다는 플래그입니다.|
|사용|하나 이상의 끝점의 상태가 “Online”입니다. “Degraded” 끝점이 없습니다.|온라인|서비스가 트래픽을 받아들여 고객 작업이 필요하지 않습니다.|
|사용|하나 이상의 끝점의 상태가 “CheckingEndpoint”입니다. “Online” 또는 “Degraded”인 끝점이 없습니다.|CheckingEndpoints|전환 상태입니다. 일반적으로 프로필을 방금 사용 가능하도록 설정하고 끝점 상태가 검사 중인 경우에 발생합니다.|
|사용|프로필에 정의된 모든 끝점의 상태는 “Disabled” 또는 “Stopped”이거나 프로필에 정의된 끝점이 없습니다.|비활성|끝점이 활성 상태가 아니지만 프로필은 여전히 사용할 수 있습니다.|

## 모니터링 작동 방법

단일 클라우드 서비스의 모니터링 프로세스를 설명하는 예제 타임 라인이 아래에 표시됩니다. 이 시나리오는 다음을 표시합니다.

- 클라우드 서비스를 사용할 수 있으며 이 트래픽 관리자 프로필을 통해서만 트래픽을 수신합니다.

- 클라우드 서비스를 사용할 수 없게 됩니다.

- 클라우드 서비스는 DNS TTL(Time-to-Live)보다 긴 시간 동안 사용할 수 없습니다.

- 클라우드 서비스를 다시 사용할 수 있습니다.

- 클라우드 서비스에서 이 트래픽 관리자 프로필을 통해서만 트래픽을 다시 수신합니다.

![트래픽 관리자 모니터링 시퀀스](./media/traffic-manager-monitoring/IC697947.jpg)

**그림 1** – 모니터링 시퀀스 예. 다이어그램의 숫자는 아래 설명의 번호에 해당합니다.

1. **GET** - 트래픽 관리자 모니터링 시스템이 모니터링 설정에서 지정된 파일과 경로에서 GET을 수행합니다.
2. **200 OK** - 모니터링 시스템에서 10초 내에 HTTP 200 OK 메시지가 다시 표시됩니다. 이 메시지가 표시되면 클라우드 서비스를 사용할 수 있다고 가정합니다. 

>[AZURE.NOTE]트래픽 관리자는 반환된 메시지가 200 OK인 경우에만 끝점이 온라인이라고 간주합니다. 200 응답 이외의 응답이 수신되면 끝점을 사용할 수 없다고 가정하여 실패된 검사로 계산합니다. 실패한 검사의 문제 해결에 대한 자세한 내용은 [Azure 트래픽 관리자의 성능 저하 상태 문제해결](traffic-manager-troubleshooting-degraded.md)을 참조하세요.

3. **확인 간격 30초** - 이 검사는 30초마다 수행됩니다.
4. **클라우드 서비스를 사용할 수 없음** - 클라우드 서비스를 사용할 수 없게 됩니다. 트래픽 관리자는 다음 모니터링 검사까지 알 수 없습니다.
5. **모니터링 파일에 액세스 시도(4회)** - 모니터링 시스템이 GET을 수행하지만 10초 이하의 응답은 수신하지 않습니다. 30초 간격으로 3회 이상 시도합니다. 모니터링 시스템이 서비스를 사용할 수 없게 되는 시기를 감지하는 데 약 1.5분이 소요됨을 의미합니다. 그 중 한 번의 시도가 성공하면 시도 횟수가 다시 설정됩니다. 다이어그램에는 표시되지 않더라도 GET 후 10초 이상이 소요된 후 200 확인 메시지가 다시 표시되면 모니터링 시스템은 이 검사를 실패한 검사로 간주합니다.
6. **Degraded로 표시** - 행에서 네 번째 실패 후 모니터링 시스템은 사용할 수 없는 클라우드 서비스를 성능이 저하된 것으로 표시합니다. 

7. **클라우드 서비스에 대한 트래픽 감소** - 트래픽은 사용할 수 없는 클라우드 서비스로 계속 전송됩니다. 서비스를 사용할 수 없기 때문에 클라이언트에 오류가 발생합니다. 클라이언트 및 보조 DNS 서버에 사용할 수 없는 클라우드 서비스의 IP 주소에 대한 DNS 레코드가 캐싱되어 있습니다. 회사 도메인의 DNS 이름을 서비스의 IP 주소로 계속 확인합니다. 또한 보조 DNS 서버가 계속 사용할 수 없는 서비스의 DNS 정보를 제공할 수 있습니다. 클라이언트 및 보조 DNS 서버가 업데이트 되면 사용할 수 없는 서비스의 IP 주소에 대한 트래픽이 느려집니다. 모니터링 시스템이 30 초 간격으로 계속 검사합니다. 이 예제에서는 서비스가 응답 하지 않으며 사용할 수 없는 상태로 유지됩니다.
8. **클라우드 서비스에 대한 트래픽 중지** - 이 때까지 대부분의 DNS 서버와 클라이언트가 업데이트되어야 하며 사용할 수 없는 서비스에 대한 트래픽은 중지됩니다. 트래픽이 완전히 중지될 때까지 걸리는 최대 시간은 TTL 시간에 따라 달라집니다. 기본 DNS TTL은 300초(5분)입니다. 이 값을 사용하면, 5분 후에 클라이언트는 서비스 사용을 중지합니다. 모니터링 시스템이 계속 30 초 간격으로 검사하고 클라우드 서비스는 응답하지 않습니다.
9. **클라우드 서비스가 다시 온라인 상태가 되어 트래픽 수신** - 서비스를 사용할 수 있지만 트래픽 관리자는 모니터링 시스템이 검사할 때까지 알지 못합니다.
10. **서비스에 대한 트래픽 다시 시작** - 트래픽 관리자는 GET을 전송하고 10초 내에 200 OK를 수신합니다. 그런 다음 업데이트를 요청하면 클라우드 서비스의 DNS 이름을 DNS 서버에 전달하기 시작합니다. 결과적으로 트래픽이 서비스로 다시 한 번 전송되기 시작합니다.

## 중첩된 프로필에 대한 자식 및 부모 끝점 상태

다음 표에서 중첩된 프로필 및 minChildEndpoints 설정의 자식 및 부모 프로필에 대한 트래픽 관리자 모니터링의 동작을 설명합니다. 자세한 내용은 [트래픽 관리자란?](traffic-manager-overview.md)(영문)을 참조하세요.

|자식 프로필 모니터 상태|부모 끝점 모니터 상태|참고 사항|
|---|---|---|
|Disabled 사용자가 프로필을 사용할 수 없도록 설정했기 때문입니다.|중지됨|부모 끝점 상태는 Stopped이며 Disabled가 아닙니다. Disabled 상태는 부모 프로필에서 끝점을 사용할 수 없도록 설정했음을 표시하도록 예약되어 있습니다.|
|Degraded하나 이상의 자식 끝점이 Degraded 상태입니다.|온라인 상태, 자식 프로필에서 온라인 끝점 수가 minChildEndpoints.CheckingEndpoint 상태의 값 이상인 경우, Degraded 상태에서 자식 프로필에서 Online 및 CheckingEndpoint 끝점의 수가 minChildEndpoints.Otherwise의 값 이상인 경우입니다.|트래픽은 CheckingEndpoint 상태의 끝점으로 라우팅됩니다. minChildEndpoints가 너무 높게 설정된 경우 부모 끝점은 항상 저하됩니다.|
|Online하나 이상의 자식이 Online 상태이며 Degraded 상태는 없습니다.|위와 동일합니다.||
|CheckingEndpoints하나 이상은 ‘CheckingEndpoint’이며 ‘Online’ 또는 ‘Degraded’는 없습니다.|위와 동일합니다.||
|Inactive모든 끝점이 Disabled 또는 Stopped이거나 끝점이 없는 프로필입니다.|중지됨||

## 특정 경로와 파일 이름에 대한 모니터링을 구성하려면

1. 프로필에 포함하려는 각 끝점에 동일한 이름의 파일을 만듭니다.
2. 각 끝점에 대해 웹 브라우저를 사용하여 파일 액세스를 테스트합니다. URL은 특정 끝점의 도메인 이름(클라우드 서비스 또는 웹 사이트), 파일 경로 및 파일 이름으로 구성됩니다. 
3. Azure 클래식 포털에서 **모니터링 설정**의 **상대 경로와 파일 이름** 필드에서 경로와 파일 이름을 지정합니다.
4. 구성 변경을 마치면 페이지의 맨 아래에서 **저장**을 클릭합니다.

## 참고 항목

[프로필 만들기](traffic-manager-manage-profiles.md)

[끝점 추가](traffic-manager-endpoints.md)

[Azure 트래픽 관리자의 성능 저하 상태 문제해결](traffic-manager-troubleshooting-degraded.md)
 

<!---HONumber=AcomDC_1210_2015-->