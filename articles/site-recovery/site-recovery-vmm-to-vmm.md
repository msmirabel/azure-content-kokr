<properties
	pageTitle="보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제 | Microsoft Azure"
	description="이 문서에서는 Azure Site Recovery로 보조 VMM 사이트에 VMM 클라우드의 Hyper-V VM을 복제하는 방법을 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="raynew"/>

# 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)을 확인하세요.

## 개요

이 문서에서는 Azure Site Recovery를 사용하여 VMM 클라우드에서 관리되는 Hyper-V 호스트 서버의 Hyper-V 가상 컴퓨터를 보조 VMM 사이트에 복제하는 방법을 설명합니다.

문서에는 필수 조건이 포함되어 있으며, 사이트 복구 자격 증명 모음을 설정하고, 원본 및 대상 VMM 서버에 Azure Site Recovery 공급자를 설치하며, 서버를 자격 증명 모음에 등록하고, VMM 클라우드에 대한 보호 설정을 구성하며, Hyper-V VM에 대해 보호를 사용하도록 설정하는 방법을 보여줍니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## 아키텍처

아래 그림에서는 조정 및 복제를 위해 Azure Site Recovery에서 사용되는 다양한 통신 채널을 보여줍니다.

![E2E 토폴로지](./media/site-recovery-vmm-to-vmm/e2e-topology.png)

## 시작하기 전에

다음 필수 조건이 충족되었는지 확인합니다.

**필수 구성 요소** | **세부 정보** 
--- | ---
**Azure**| [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/). 
**VMM** | 하나 이상의 VMM 서버가 필요합니다.<br/><br/>VMM 서버에서 최신 누적 업데이트를 포함하는 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/>단일 VMM 서버에 보호를 설정하려는 경우 서버에 두 개 이상의 클라우드를 구성해야 합니다.<br/><br/>두 VMM 서버에 보호를 배포하려는 경우 각 서버에 보호하려는 기본 VMM 서버에 하나 이상의 클라우드를 구성해야 하고 보호 및 복구에 사용하려는 보조 VMM 서버에 하나의 클라우드를 구성해야 합니다.<br/><br/>모든 VMM 클라우드에 Hyper-V 용량 프로필을 설정해야 합니다.<br/><br/>보호할 원본 클라우드에는 하나 이상의 VMM 호스트 그룹이 포함되어야 합니다.<br/><br/>Keith Mayer 블로그의 [연습: System Center 2012 SP1 VMM에서 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)에서 VMM 클라우드 설정에 대해 자세히 알아봅니다.
**Hyper-V** | 기본 및 보조 VMM 호스트 그룹에서 하나 이상의 Hyper-V 호스트 서버가 필요하고 각 Hyper-V 호스트 서버에 하나 이상의 가상 컴퓨터가 필요합니다.<br/><br/>호스트 및 대상 Hyper-V 서버는 Hyper-V 역할을 하는 Windows Server 2012 이상을 실행해야 하고 최신 업데이트가 설치되어 있어야 합니다.<br/><br/>VM이 포함된 보호하려는 모든 Hyper-V 서버는 VMM 클라우드에 있어야 합니다.<br/><br/>클러스터에서 Hyper-V를 실행하고 있다면 고정 IP 주소 기반 클러스터가 있는 경우 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. Aidan Finn의 블로그 항목에서 [자세히 알아봅니다](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters).
**네트워크 매핑** | 장애 조치(Failover) 후에 복제된 가상 컴퓨터가 보조 Hyper-V 호스트 서버에 최적으로 배치되고 적절한 VM 네트워크에 연결할 수 있도록 네트워크 매핑을 구성할 수 있습니다. 네트워크 매핑을 구성하지 않으면 장애 조치(failover) 후 복제본 VM이 네트워크에 연결되지 않습니다.<br/><br/>배포 중에 네트워크 매핑을 설정하려면 원본 Hyper-V 호스트 서버의 가상 컴퓨터가 VMM VM 네트워크에 연결되어 있는지 확인합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.<br/<br/>복구에 사용하는 보조 VMM 서버의 대상 클라우드에 해당 VM 네트워크가 구성되어 있어야 하며, 이 네트워크는 다시 대상 클라우드와 연결된 해당 논리 네트워크에 연결되어야 합니다.<br/><br/>네트워크 매핑에 대해 [자세히 알아봅니다](site-recovery-network-mapping.md).
**저장소 매핑** | 기본적으로 원본 Hyper-V 호스트 서버의 가상 컴퓨터를 대상 Hyper-V 호스트 서버로 복제하는 경우 복제된 데이터가 Hyper-V 관리자에서 대상 Hyper-V 호스트에 대해 표시된 기본 위치에 저장됩니다. 복제된 데이터가 저장된 위치에 대해 더 제어하려면 저장소 매핑을 구성할 수 있습니다<br/><br/> 저장소 매핑을 구성하려면 배포를 시작하기 전에 원본 및 대상 VMM 서버에서 저장소 분류를 설정해야 합니다. [자세히 알아봅니다](site-recovery-storage-mapping.md).


## 1단계: 사이트 복구 자격 증명 모음 만들기

1. 등록할 VMM 서버에서 [관리 포털](https://portal.azure.com)에 로그인합니다.

2. **데이터 서비스** > **복구 서비스**를 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.

3. **새로 만들기** > **빠른 생성**을 클릭합니다.

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **하위 지역**에서 자격 증명 모음에 대한 지리적 하위 지역을 선택합니다. 지원되는 하위 지역을 확인하려면 [Azure Site Recovery 가격 정보](http://go.microsoft.com/fwlink/?LinkId=389880)에서 지리적 가용성을 참조하세요.

6. **자격 증명 모음 만들기**를 클릭합니다.

	![자격 증명 모음 만들기](./media/site-recovery-vmm-to-vmm/create-vault.png)

상태 표시줄에서 자격 증명 모음이 만들어진 것을 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 **활성**으로 나열됩니다.

## 2단계: 자격 증명 모음 등록 키 생성

자격 증명 모음에 등록 키를 생성합니다. Azure Site Recovery 공급자를 다운로드하고 VMM 서버에 설치한 후 이 키를 사용하여 VMM 서버를 자격 증명 모음에 등록합니다.

1. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![빠른 시작 아이콘](./media/site-recovery-vmm-to-vmm/quick-start-icon.png)

2. 드롭다운 목록에서 **두 개의 온-프레미스 Hyper-V 사이트 사이**를 선택합니다.
3. **VMM 서버 준비**에서 **등록 키 파일 생성**을 클릭합니다. 키 파일은 자동으로 생성되고 생성된 날부터 5일간 유효합니다. VMM 서버에서 Azure 포털에 액세스하지 않는 경우 이 파일을 서버로 복사해야 합니다.

	![등록 키](./media/site-recovery-vmm-to-vmm/register-key.png)

## 3단계: Azure Site Recovery 공급자 설치

4. **빠른 시작** 페이지의 **VMM 서버 준비**에서 **VMM 서버에 설치할 Microsoft Azure 사이트 복구 공급자 다운로드**를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.

2. 원본 VMM 서버에서 이 파일을 실행합니다. VMM이 클러스터에 배포되고 공급자를 처음 설치하는 경우 활성 노드에 설치하고 설치를 완료하여 VMM 서버를 자격 증명 모음에 등록합니다. 그런 후에 다른 노드에 공급자를 설치합니다. 공급자를 업그레이드하는 경우 모두 동일한 공급자 버전을 실행해야 하므로 모든 노드에서 업그레이드해야 합니다.


3. 설치 관리자는 몇 가지 **사전 요구 사항 확인**을 수행하고 공급자 설정을 시작하기 위해 VMM 서비스를 중지하는 권한을 요청합니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.

4. **Microsoft 업데이트**에서 업데이트를 선택할 수 있습니다. 이 설정이 사용되면 공급자가 Microsoft 업데이트 정책에 따라 설치됩니다.

	![Microsoft 업데이트](./media/site-recovery-vmm-to-vmm/ms-update.png)

5. 설치 위치는 **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**으로 설정됩니다. 공급자 설치를 시작하려면 설치 단추를 클릭합니다.

	![InstallLocation](./media/site-recovery-vmm-to-vmm/install-location.png)

6. 공급자가 설치된 후 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

	![InstallComplete](./media/site-recovery-vmm-to-vmm/install-complete.png)

7. VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 **인터넷 연결**에서 지정합니다. 서버에 구성되어 있는 기본 인터넷 연결 설정을 사용하려면 **기존 프록시 설정과 연결**을 선택합니다.

	![인터넷 설정](./media/site-recovery-vmm-to-vmm/proxy-details.png)

	- 사용자 지정 프록시를 사용하려는 경우 공급자를 설치하기 전에 설정해야 합니다. 사용자 지정 프록시 설정을 구성하면 테스트가 실행되어 프록시 연결을 확인합니다.
	- 사용자 지정 프록시를 사용하지 않거나 기본 프록시에 인증이 필요한 경우 프록시 주소와 포트를 비롯한 프록시 정보를 입력해야 합니다.
	- 다음 URL은 VMM 서버 및 Hyper-V 호스트에서 액세스할 수 있어야 합니다.
		- **.hypervrecoverymanager.windowsazure.com
		- **.accesscontrol.windows.net
		- **.backup.windowsazure.com
		- **.blob.core.windows.net
		- **.store.core.windows.net
	- [Azure 데이터센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 프로토콜에 설명된 IP 주소를 허용합니다. 사용하려는 Azure 지역 및 미국 서부의 IP 범위를 허용해야 합니다.
	- 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. VMM 콘솔에서 VMM 실행 계정 설정을 수정할 수 있습니다. 이렇게 하려면 **설정** 작업 영역을 열고 **보안**을 확장한 다음 **실행 계정**을 클릭하고 DRAProxyAccount의 암호를 수정합니다. 이 설정이 적용되도록 VMM 서비스를 다시 시작해야 합니다.

8. **등록 키**에서 Azure 사이트 복구를 다운로드하고 VMM 서버에 복사했다는 것을 선택합니다.
9. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다. *다음*을 클릭합니다.

	![서버 등록](./media/site-recovery-vmm-to-vmm/vault-creds.png)

10.  암호화 설정은 VMM 클라우드의 Hyper-V VM을 Azure에 복제하는 경우에 사용됩니다. 보조 사이트에 복제하는 경우 사용되지 않습니다.

	![서버 등록](./media/site-recovery-vmm-to-vmm/encrypt.png)

11.  자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
12.  **클라우드 메타데이터 동기화**에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.
 
	![서버 등록](./media/site-recovery-vmm-to-vmm/friendly-name.png)

13.  **다음**을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 서버가 자격 증명 모음의 **서버** 페이지에서 **VMM 서버** 탭에 표시됩니다.


### 명령줄 설치

또한 다음 명령줄에서 Azure Site Recovery 공급자를 설치할 수 있습니다. 이 방법은 Windows Server 2012 R2용 Server CORE에 대한 공급자를 설치하는 데 사용할 수 있습니다.

1. 공급자 설치 파일 및 등록 키를 폴더로 다운로드합니다. 예: C:\\ASR.
2. System Center Virtual Machine Manager 서비스 중지
3. **관리자** 권한으로 명령 프롬프트에서 다음 명령을 실행하여 공급자 설치 관리자를 추출합니다.

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 다음을 실행하여 공급자를 설치합니다.

    	C:\ASR> setupdr.exe /i

5. 다음을 실행하여 공급자를 등록합니다.

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

매개 변수는 다음에 위치합니다.

 - **/Credentials** : 등록 키 파일이 있는 위치를 지정하는 필수 매개 변수입니다.  
 - **/FriendlyName** : Azure Site Recovery 포털에 나타나는 Hyper-V 호스트 서버의 이름에 대한 필수 매개 변수입니다.
 - **/EncryptionEnabled** : Azure에서 미사용 중인 가상 컴퓨터의 암호화해야 하는 경우 VMM-Azure 시나리오에서만 사용해야 하는 선택적 매개 변수입니다. 제공한 파일의 이름에 **.pfx** 확장자가 있는지 확인합니다.
 - **/proxyAddress** : 프록시 서버의 주소를 지정하는 선택적 매개 변수입니다.
 - **/proxyport** : 프록시 서버의 포트를 지정하는 선택적 매개 변수입니다.
 - **/proxyUsername** : (프록시가 인증을 필요로 하는 경우) 프록시 사용자 이름을 지정하는 선택적 매개 변수입니다.
 - **/proxyPassword** : (프록시가 인증을 필요로 하는 경우) 프록시 서버를 인증하기 위한 암호를 지정하는 선택적 매개 변수입니다.  

## 4단계: 클라우드 보호 설정 구성

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. 공급자를 설치할 때 **클라우드 데이터를 자격 증명 모음과 동기화** 옵션을 사용하도록 설정한 경우 VMM 서버의 모든 클라우드가 자격 증명 모음의 **보호된 항목** 탭에 표시됩니다. 그렇지 않은 경우 VMM 콘솔에서 클라우드 속성 페이지의 **일반** 탭을 통해 특정 클라우드를 Azure Site Recovery와 동기화할 수 있습니다.

![게시된 클라우드](./media/site-recovery-vmm-to-vmm/clouds-list.png)

1. 빠른 시작 페이지에서 **VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2. **VMM 클라우드** 탭에서 구성할 클라우드를 선택하고 **구성** 탭으로 이동합니다.
3. **대상**에서 **VMM**을 선택합니다.
4. **대상 위치**에서 복구에 사용할 클라우드를 관리하는 온-사이트 VMM 서버를 선택합니다.
4. **대상 클라우드**에서 원본 클라우드의 가상 컴퓨터 장애 조치(Failover)에 사용할 대상 클라우드를 선택합니다. 다음 사항에 유의하세요.

	- 보호할 가상 컴퓨터의 복구 요구 사항을 충족하는 대상 클라우드를 선택하는 것이 좋습니다.
	- 클라우드는 단일 클라우드 쌍에 기본 클라우드 또는 대상 클라우드로만 속할 수 있습니다.

5. **복사 빈도**에서 원본 위치와 대상 위치 간에 데이터를 동기화해야 하는 빈도를 지정합니다. 이 설정은 Hyper-V 호스트가 Windows Server 2012 R2에서 실행되는 경우에만 관련이 있습니다. 다른 서버의 경우에는 기본 설정인 5분이 사용됩니다.
6. **추가 복구 지점**에서 추가 복구 지점을 만들지 여부를 지정합니다. 기본값인 0은 주 가상 컴퓨터의 가장 최근 복구 지점만 복제본 호스트 서버에 저장됨을 나타냅니다. 여러 복구 지점을 사용하도록 설정하려면 각 복구 지점에 저장되는 스냅숏을 위한 추가 저장소가 필요합니다. 기본적으로 복구 지점은 매시간마다 만들어지므로 각 복구 지점에는 1시간 동안의 데이터가 포함됩니다. VMM 콘솔에서 가상 컴퓨터에 대해 할당하는 복구 지점 값은 Azure Site Recovery 콘솔에서 할당한 값보다 크거나 같아야 합니다.
7. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏을 만드는 빈도를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.

	![보호 설정 구성](./media/site-recovery-vmm-to-vmm/cloud-settings.png)

8. **데이터 전송 압축**에서 전송되는 복제된 데이터를 압축할지 여부를 지정합니다.
9. **인증**에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다. 작동하는 Kerberos 환경이 구성되어 있지 않으면 HTTPS를 선택합니다. Azure Site Recovery가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 구성할 필요가 없습니다. Kerberos를 선택하면 호스트 서버의 상호 인증에 Kerberos 티켓이 사용됩니다. 기본적으로 Hyper-V 호스트 서버의 Windows 방화벽에서 포트 8083과 8084(인증서용)가 열립니다. 이 설정은 Windows Server 2012 R2에서 실행 중인 Hyper-V 호스트 서버와만 관련이 있습니다.
10. **포트**에서 원본 및 대상 호스트 컴퓨터가 복제 트래픽을 수신하는 포트 번호를 수정합니다. 예를 들어 복제 트래픽에 대해 QoS(서비스 품질) 네트워크 대역폭 제한을 적용하려는 경우 설정을 수정할 수 있습니다. 포트가 다른 응용 프로그램에서 사용되지 않는지, 그리고 방화벽 설정에서 열려 있는지 확인합니다.
11. **복제 방법**에서 정기 복제가 시작되기 전에 원본 위치에서 대상 위치로 데이터의 초기 복제를 처리할 방법을 지정합니다.

	- **네트워크 이용**—네트워크를 이용하여 데이터를 복사하면 시간이 많이 걸리고 리소스가 많이 필요할 수 있습니다. 클라우드에 상대적으로 하드 디스크가 작은 가상 컴퓨터가 포함된 경우와 기본 사이트가 넓은 대역폭을 사용하여 보조 사이트에 연결된 경우 이 옵션을 사용하는 것이 좋습니다. 복사를 즉시 시작하도록 지정하거나 시간을 선택할 수 있습니다. 네트워크 복제를 사용하는 경우 사용률이 낮은 시간에 예약하는 것이 좋습니다.
	- **오프라인**—이 방법은 외부 미디어를 사용하여 초기 복제를 수행하도록 지정합니다. 이 방법은 네트워크 성능 저하를 방지하려는 경우나 지리적으로 멀리 떨어진 위치에 유용합니다. 이 방법을 사용하려면 원본 클라우드에서 내보내기 위치를 지정하고 대상 클라우드에서 가져오기 위치를 지정합니다. 가상 컴퓨터에 대한 보호를 설정할 때 가상 하드 디스크가 지정된 내보내기 위치에 복사됩니다. 이 하드 디스크를 대상 사이트에 보내고 가져오기 위치에 복사합니다. 시스템에서는 가져온 정보를 복제본 가상 컴퓨터에 복사합니다. 

12. 클라우드 속성의 가상 컴퓨터 탭에서 **가상 컴퓨터 보호 삭제** 옵션을 선택하여 가상 컴퓨터의 보호를 중지할 경우 복제 가상 컴퓨터가 삭제되도록 지정하려면 **복제 가상 컴퓨터 삭제**를 선택합니다. 이 설정이 사용되면 보호를 사용하지 않을 때 가상 컴퓨터가 Azure Site Recovery에서 제거되고, 가상 컴퓨터의 사이트 복구 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.

	![보호 설정 구성](./media/site-recovery-vmm-to-vmm/cloud-settings-replica.png)

설정을 저장하고 나면 작업이 생성되고 **작업** 탭에서 모니터링할 수 있습니다. VMM 원본 클라우드에 있는 모든 Hyper-V 호스트 서버가 복제 대상으로 구성됩니다. 클라우드 설정은 **구성** 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 클라우드를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다.

### 오프라인 초기 복제 준비

오프라인 초기 복제를 준비하려면 다음 작업을 수행해야 합니다.

- 원본 서버에서 데이터 내보내기가 수행되는 경로 위치를 지정합니다. NTFS에 대해 모든 권한을 할당하고 내보내기 경로의 VMM 서비스에 공유 권한을 할당합니다. 대상 서버에서 데이터 가져오기가 수행되는 경로 위치를 지정합니다. 이 가져오기 경로에 동일한 사용 권한을 할당합니다.
- 가져오기 또는 내보내기 경로가 공유되는 경우 공유가 있는 원격 컴퓨터의 VMM 서비스 계정에 대해 Administrator, Power User, Print Operator 또는 Server Operator 그룹 구성원 자격을 할당합니다.
- 실행 계정을 사용하여 호스트를 추가하는 경우 가져오기 및 내보내기 경로에서 VMM의 실행 계정에 읽기 및 쓰기 권한을 할당합니다.
- Hyper-V는 루프백 구성을 지원하지 않으므로 Hyper-V 호스트 서버로 사용되는 컴퓨터에는 가져오기 및 내보내기 공유가 있으면 안 됩니다.
- 보호할 가상 컴퓨터를 포함하는 Active Directory의 각 Hyper-V 호스트 서버에서 다음과 같이 제한 위임을 사용하도록 설정하고 가져오기 및 내보내기 경로가 있는 원격 컴퓨터를 신뢰하도록 구성합니다.
	1. 도메인 컨트롤러에서 **Active Directory 사용자 및 컴퓨터**를 엽니다.
	2. 콘솔 트리에서 **DomainName** > **컴퓨터**를 클릭합니다.
	3. Hyper-V 호스트 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
	4. **위임** 탭에서 **지정한 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**를 클릭합니다.
	5. **모든 인증 프로토콜 사용**을 클릭합니다.
	6. **추가** > **사용자 및 컴퓨터**를 클릭합니다.
	7. 내보내기 경로를 호스트하는 컴퓨터의 이름을 입력하고 **확인**을 클릭합니다. 사용 가능한 서비스 목록에서 Ctrl 키를 누른 채 **cifs** > **확인**을 클릭합니다. 가져오기 경로를 호스트하는 컴퓨터 이름에 대해 반복합니다. 필요에 따라 추가 Hyper-V 호스트 서버에 대해 반복합니다.

## 5단계: 네트워크 매핑 구성
1. 퀵스타트 페이지에서 **네트워크 매핑**을 클릭합니다.
2. 네트워크를 매핑할 원본 VMM 서버를 선택한 다음 네트워크를 매핑할 대상 VMM 서버를 선택합니다. 원본 네트워크 및 연결된 대상 네트워크의 목록이 표시됩니다. 현재 매핑되지 않은 네트워크에 대해서는 빈 값이 표시됩니다.
3. **원본 네트워크**에서 네트워크를 선택하고 **매핑**을 클릭합니다. 서비스에서 대상 서버의 VM 네트워크를 감지하고 표시합니다. 원본 및 대상 네트워크 이름 옆에 있는 정보 아이콘을 클릭하여 각 네트워크의 서브넷을 확인합니다.

	![네트워크 매핑 구성](./media/site-recovery-vmm-to-vmm/network-mapping1.png)

4. 대화 상자에서 대상 VMM 서버의 VM 네트워크 중 하나를 선택합니다.

	![대상 네트워크 선택](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

5. 대상 네트워크를 선택하면 원본 네트워크를 사용하는 보호된 클라우드가 표시됩니다. 보호에 사용되는 클라우드와 연결된 사용 가능한 대상 네트워크도 표시됩니다. 보호에 사용 중인 모든 클라우드에 사용할 수 있는 대상 네트워크를 선택하는 것이 좋습니다. 또는 VMM 서버로 가서 클라우드 속성을 수정하여 선택하려는 VM 네트워크에 해당하는 논리 네트워크를 추가합니다.
6. 확인 표시를 클릭하여 매핑 프로세스를 완료합니다. 매핑 프로세스를 추적하는 작업이 시작됩니다. **작업** 탭에서 작업을 확인할 수 있습니다.


## 6단계: 저장소 매핑 구성
기본적으로 원본 Hyper-V 호스트 서버의 가상 컴퓨터를 대상 Hyper-V 호스트 서버로 복제하는 경우 복제된 데이터가 Hyper-V 관리자에서 대상 Hyper-V 호스트에 대해 표시된 기본 위치에 저장됩니다. 복제된 데이터가 저장되는 위치를 제어하려는 경우 다음과 같이 저장소 매핑을 구성할 수 있습니다.



1. 원본 및 대상 VMM 서버 둘 다에서 저장소 분류를 정의합니다. [자세히 알아봅니다](https://technet.microsoft.com/library/gg610685.aspx). 원본 및 대상 클라우드의 Hyper-V 호스트 서버에서 분류를 사용할 수 있어야 합니다. 분류의 저장소 유형이 같을 필요는 없습니다. 예를 들어 SMB 공유가 포함된 원본 분류를 CSV가 포함된 대상 분류에 매핑할 수 있습니다.
2. 분류가 구현되었으면 매핑을 만들 수 있습니다. 이 작업을 수행하려면 **빠른 시작** 페이지 > **저장소 매핑**에서.
3. **저장소 탭** > **저장소 분류 매핑**을 클릭합니다.
4. **저장소 분류 매핑** 탭에서 원본 및 대상 VMM 서버의 분류를 선택합니다. 설정을 저장합니다.

	![대상 네트워크 선택](./media/site-recovery-vmm-to-vmm/storage-mapping.png)


## 7단계: 가상 컴퓨터 보호 사용
서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다.

1. 가상 컴퓨터가 있는 클라우드의 **가상 컴퓨터** 탭에서 **보호 사용** > **가상 컴퓨터 추가**를 클릭합니다.
2. 클라우드에 있는 가상 컴퓨터의 목록에서 보호할 가상 컴퓨터를 선택합니다.

	![가상 컴퓨터 보호 사용](./media/site-recovery-vmm-to-vmm/enable-protection.png)

3. **작업** 탭에서 초기 복제를 비롯하여 보호 사용 작업의 진행 상태를 추적합니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. 보호를 사용하도록 설정하고 가상 컴퓨터가 복제되고 나면 Azure에서 가상 컴퓨터를 볼 수 있습니다.

	![가상 컴퓨터 보호 작업](./media/site-recovery-vmm-to-vmm/vm-jobs.png)
	
>[AZURE.NOTE] 또한 VMM 콘솔에서 가상 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다. 가상 컴퓨터 속성의 **Azure Site Recovery** 탭에 있는 도구 모음에서 **보호 사용**을 클릭합니다.

### 기존 가상 컴퓨터 등록

Hyper-V 복제본을 사용하여 복제 중인 기존 가상 컴퓨터가 VMM에 있는 경우 Azure Site Recovery 보호를 위해 다음과 같이 등록해야 합니다.

1. 기본 클라우드와 보조 클라우드가 있는지 확인합니다. 기존 가상 컴퓨터를 호스트하는 Hyper-V 서버가 기본 클라우드에 있고 복제본 가상 컴퓨터를 호스트하는 Hyper-V 서버가 보조 클라우드에 있는지 확인합니다. 클라우드에 대한 보호 설정을 구성했는지 확인합니다. 이 설정은 현재 Hyper-V 복제본에 대해 구성된 설정과 일치해야 합니다. 그렇지 않으면 가상 컴퓨터 복제가 예상대로 작동하지 않을 수 있습니다.
2. 그런 다음 주 가상 컴퓨터에 보호를 사용하도록 설정합니다. Azure Site Recovery 및 VMM에서 동일한 복제본 호스트와 가상 컴퓨터가 검색되는지 확인하고, Azure Site Recovery에서 클라우드 구성 중 구성된 설정을 사용하여 복제를 다시 사용하고 설정합니다.


## 배포 테스트

배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다.

### 복구 계획 만들기

1. **복구 계획** 탭에서 **복구 계획 만들기**를 클릭합니다.
2. 복구 계획 이름, 원본 및 대상 VMM 서버를 지정합니다. 원본 서버에 장애 조치(Failover) 및 복구를 사용하도록 설정한 가상 컴퓨터가 있어야 합니다. **Hyper-V**를 선택하여 Hyper-V 복제가 구성된 클라우드만 표시합니다.

	![복구 계획 만들기](./media/site-recovery-vmm-to-vmm/recovery-plan1.png)

3. **가상 컴퓨터 선택**에서 복제 그룹을 선택합니다. 복제 그룹과 연관된 모든 가상 컴퓨터가 선택되고 복구 계획에 추가됩니다. 이러한 가상 컴퓨터는 복구 계획 기본 그룹인 그룹 1에 추가됩니다. 필요한 경우 그룹을 추가할 수 있습니다. 복제 후 가상 컴퓨터가 복구 계획 그룹의 순서에 따라 시작됩니다.

	![가상 컴퓨터 추가](./media/site-recovery-vmm-to-vmm/recovery-plan2.png)

만든 복구 계획은 **복구 계획** 탭의 목록에 표시됩니다.

###테스트 장애 조치(Failover) 실행

1. **복구 계획** 탭에서 계획을 선택하고 **테스트 장애 조치**를 클릭합니다.
2. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 이 옵션을 사용하도록 설정하면 장애 조치(Failover)된 복제본 가상 컴퓨터가 네트워크에 연결되지 않습니다. 여기서는 가상 컴퓨터가 올바로 장애 조치(Failover)되는지 테스트하지만 복제 네트워크 환경을 테스트하지는 않습니다. 다양한 네트워킹 옵션을 사용하는 방법에 대한 자세한 내용은 [테스트 장애 조치(failover) 실행](site-recovery-failover.md#run-a-test-failover) 방법을 참조하세요.
3. 테스트 가상 컴퓨터는 복제본 가상 컴퓨터가 있는 호스트와 동일한 호스트에 생성됩니다. 복제본 가상 컴퓨터가 있는 동일한 클라우드에 추가됩니다.

### 복구 계획 실행
복제 후에 복제본 가상 컴퓨터의 IP 주소는 주 가상 컴퓨터의 IP 주소와 동일하지 않을 수 있습니다. 가상 컴퓨터가 시작되면 사용 중인 DNS 서버가 업데이트됩니다. 또한 DNS 서버를 업데이트하는 스크립트를 추가하여 시기 적절한 업데이트를 보장할 수 있습니다.

#### IP 주소를 검색할 스크립트
다음 샘플 스크립트를 실행하여 IP 주소를 검색합니다.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### DNS를 업데이트할 스크립트
다음 샘플 스크립트에서 이전 샘플 스크립트를 사용하여 검색한 IP 주소를 지정하고 실행하여 DNS를 업데이트합니다.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 다음 단계

환경이 예상 대로 작동 중인지 확인하기 위해 테스트 장애 조치를 실행한 후에 여러 유형의 장애 조치에 대해 [알아봅니다](site-recovery-failover.md).


## 사이트 복구에 대한 개인 정보 보호

이 섹션에서는 Microsoft Azure Site Recovery 서비스("서비스")에 대한 추가 개인 정보 보호를 설명합니다. Microsoft Azure 서비스에 대한 개인 정보 취급 방침을 보려면 [Microsoft Azure 개인 정보 취급 방침](http://go.microsoft.com/fwlink/?LinkId=324899)을 참조하세요.

**기능: 등록**

- **수행하는 작업**: 가상 컴퓨터를 보호할 수 있도록 서버를 서비스에 등록합니다.
- **수집 정보**: 등록되면 서비스가 VMM 서버의 서비스 이름과 VMM 서버의 가상 컴퓨터 클라우드의 이름을 사용하여 재해 복구를 제공하도록 지정된 VMM 서버에서 관리 인증서 정보를 수집, 처리, 전송합니다.
- **정보 사용**:
	- 관리 인증서 - 등록된 VMM 서버를 식별하고 서비스에 액세스할 수 있도록 인증하는 데 사용됩니다. 서비스는 인증서의 공개 키 부분을 사용하여 등록된 VMM 서버만 액세스할 수 있는 토큰을 보호합니다. 서버는 서비스 기능에 액세스하기 위해 이 토큰을 사용해야 합니다.
	- VMM 서버 이름 - VMM 서버 이름은 클라우드가 있는 해당 VMM 서버를 식별하고 통신하는 데 필요합니다.
	- VMM 서버의 클라우드 이름 - 클라우드 이름은 아래에 설명된 서비스 클라우드 연결/연결 해제 기능을 사용할 때 필요합니다. 기본 데이터 센터의 클라우드를 복구 데이터 센터의 다른 클라우드와 연결하는 경우 복구 데이터 센터의 모든 클라우드 이름이 제공됩니다.

- **선택**: 이 정보는 사용자와 서비스가 Azure Site Recovery 보호를 제공할 VMM 서버를 식별하고 등록된 올바른 VMM 서버를 식별하는 데 도움이 되기 때문에 서비스 등록 프로세스의 핵심 부분입니다. 이 정보를 서비스로 보내지 않으려면 이 서비스를 사용하지 마세요. 서버를 등록한 후 나중에 등록 취소하려는 경우 서비스 포털(Azure 포털)에서 VMM 서버 정보를 삭제하면 됩니다.

**기능: Azure Site Recovery 보호 사용**

- **수행하는 작업**: VMM 서버에 설치된 Azure Site Recovery 공급자는 서비스와 통신하기 위한 통로입니다. 공급자는 VMM 프로세스에 호스트된 DLL(동적 연결 라이브러리)입니다. 공급자를 설치하면 VMM 관리자 콘솔에서 "데이터 센터 복구" 기능을 사용할 수 있습니다. 클라우드의 모든 새 가상 컴퓨터나 기존 가상 컴퓨터에서 가상 컴퓨터 보호에 도움이 되도록 "데이터 센터 복구"라는 속성을 사용하도록 설정할 수 있습니다. 이 속성이 설정되면 공급자가 가상 컴퓨터의 이름과 ID를 서비스로 보냅니다. 가상 보호는 Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제 기술을 통해 사용할 수 있습니다. 일반적으로 서로 다른 "복구" 데이터 센터에 있는 Hyper-V 호스트 간에 가상 컴퓨터 데이터가 복제됩니다.

- **수집 정보**: 서비스는 이름, ID, 가상 네트워크 및 가상 컴퓨터가 속하는 클라우드의 이름을 포함하는 가상 컴퓨터 메타데이터를 수집, 처리 및 전송합니다.

- **정보 사용**: 서비스는 위 정보를 사용하여 서비스 포털에 가상 컴퓨터 정보를 채웁니다.

- **선택**: 이 정보는 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 서비스로 보내지 않으려면 가상 컴퓨터에 대해 Azure Site Recovery 보호를 사용하도록 설정하지 마세요. 공급자가 서비스로 보내는 모든 데이터는 HTTPS를 통해 전송됩니다.

**기능: 복구 계획**

- **수행하는 작업**: 이 기능은 "복구" 데이터 센터에 대한 오케스트레이션 계획을 작성하는 데 도움이 됩니다. 복구 사이트에서 가상 컴퓨터 또는 가상 컴퓨터 그룹이 시작되는 순서를 정의할 수 있습니다. 각 가상 컴퓨터를 복구할 때 실행할 자동화된 스크립트나 수행할 수동 작업을 지정할 수도 있습니다. 다음 섹션에서 설명하는 장애 조치(Failover)는 일반적으로 조정된 복구에 대한 복구 계획 수준에서 트리거됩니다.

- **수집 정보**: 서비스는 가상 컴퓨터 메타데이터, 자동화 스크립트 및 수동 작업 노트의 메타데이터 등을 포함하여 복구 계획에 대한 메타데이터를 수집, 처리 및 전송합니다.

- **정보 사용**: 위에서 설명한 메타데이터는 서비스 포털에서 복구 계획을 작성하는 데 사용됩니다.

- **선택**: 이 정보는 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 서비스로 보내지 않으려면 이 서비스에서 복구 계획을 작성하지 마세요.

**기능: 네트워크 매핑**

- **수행하는 작업**: 이 기능을 사용하면 기본 데이터 센터에서 복구 데이터 센터로 네트워크 정보를 매핑할 수 있습니다. 복구 사이트에서 가상 컴퓨터를 복구할 때 이 매핑은 네트워크 연결을 설정하는 데 도움이 됩니다.

- **수집 정보**: 네트워크 매핑 기능의 일부로 서비스는 각 사이트(기본 사이트 및 데이터 센터)에 대한 논리적 네트워크의 메타데이터를 수집, 처리 및 전송합니다.

- **정보 사용**: 서비스는 메타데이터를 사용하여 서비스 포털을 채우고, 여기서 네트워크 정보를 매핑할 수 있습니다.

- **선택**: 이 정보는 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 서비스로 보내지 않으려면 네트워크 매핑 기능을 사용하지 마세요.

**기능: 장애 조치(Failover) - 계획됨, 계획되지 않음, 테스트**

- **수행하는 작업:** 이 기능은 VMM 관리되는 데이터 센터 간의 가상 컴퓨터 장애 조치(failover)에 도움이 됩니다. 장애 조치(Failover) 작업은 서비스 포털에서 사용자가 트리거합니다. 장애 조치(Failover)의 가능한 이유로 계획되지 않은 이벤트(예: 자연 재해), 계획된 이벤트(예: 데이터 센터 부하 분산), 테스트 장애 조치(Failover)(예: 복구 계획 예행 연습) 등이 있습니다.

VMM 서버의 공급자는 서비스에서 이벤트에 대한 알림을 받고 VMM 인터페이스를 통해 Hyper-V 호스트에서 장애 조치(Failover) 작업을 실행합니다. 일반적으로 서로 다른 "복구" 데이터 센터에서 실행되는 Hyper-V 호스트 간의 실제 가상 컴퓨터 장애 조치(Failover)는 Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제 기술을 통해 처리됩니다. 장애 조치(Failover)가 완료되면 "복구" 데이터 센터의 VMM 서버에 설치된 공급자가 성공 정보를 서비스로 보냅니다.

- **수집 정보**: 서비스는 위 정보를 사용하여 서비스 포털에 장애 조치(failover) 작업 상태 정보를 채웁니다.

- **정보 사용**: 서비스는 위 정보를 다음과 같이 사용합니다.

	- 관리 인증서 - 등록된 VMM 서버를 식별하고 서비스에 액세스할 수 있도록 인증하는 데 사용됩니다. 서비스는 인증서의 공개 키 부분을 사용하여 등록된 VMM 서버만 액세스할 수 있는 토큰을 보호합니다. 서버는 서비스 기능에 액세스하기 위해 이 토큰을 사용해야 합니다.
	- VMM 서버 이름 - VMM 서버 이름은 클라우드가 있는 해당 VMM 서버를 식별하고 통신하는 데 필요합니다.
	- VMM 서버의 클라우드 이름 - 클라우드 이름은 아래에 설명된 서비스 클라우드 연결/연결 해제 기능을 사용할 때 필요합니다. 기본 데이터 센터의 클라우드를 복구 데이터 센터의 다른 클라우드와 연결하는 경우 복구 데이터 센터의 모든 클라우드 이름이 제공됩니다.

- **선택**: 이 정보는 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 서비스로 보내지 않으려면 이 서비스를 사용하지 마세요.

<!----HONumber=AcomDC_0218_2016-->