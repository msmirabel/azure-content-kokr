<properties
	pageTitle="Site Recovery를 사용하여 Amazon Web Services의 Windows 가상 컴퓨터를 Azure로 마이그레이션 | Microsoft Azure"
	description="Azure Site Recovery를 사용하여 Amazon 웹 서비스(AWA)에서Azure에 실행되는 Windows 가상 컴퓨터를 마이그레이션합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Azure Site Recovery를 사용하여 AWS(Amazon Web Services)의 Windows 가상 컴퓨터를 Azure로 마이그레이션


## 개요

Azure Site Recovery는 여러 배포에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

이 문서는 사이트 복구를 사용하는 방법을 설명하여 AWS에서 Azure로 Windows 인스턴스를 마이그레이션하거나 장애 조치합니다. 이 문서는 [온-프레미스 VMware 가상 컴퓨터 또는 물리적 서버와 Azure 간의 보호 설정](site-recovery-vmware-to-azure-classic-legacy.md)에서 설명한 단계 대부분을 사용합니다. 배포 중 각 단계의 자세한 지침에 대한 문서를 읽는 것이 좋습니다.

## 시작

시작하기 전에 필요한 사항:

- **구성 서버**: 구성 서버 역할을 하는 Azure 가상 컴퓨터입니다. 구성 서버는 온-프레미스 컴퓨터와 Azure 서버 간의 통신을 조정합니다.
- **마스터 대상 서버**: 마스터 대상 서버 역할을 하는 Azure 가상 컴퓨터입니다. 이 서버는 보호된 컴퓨터에서 복제된 데이터를 받고 유지합니다.
- **프로세스 서버**: Windows Server 2012 R2를 실행하는 가상 컴퓨터입니다. 보호된 가상 컴퓨터는 이 서버에 복제 데이터를 보냅니다.
- **EC2 VM 인스턴스**: 마이그레이션한 다음 보호하려는 인스턴스입니다.

- 이러한 구성 요소에 대한 자세한 정보는 [무엇이 필요하나요?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)를 읽습니다.
- 또한 시작하기 전에 [용량 계획](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning)에서 지침을 읽고 모든 [배포 필수 구성 요소](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start)가 제자리에 있는지 확인합니다.

## 배포 단계

1. [자격 증명 모음 만들기](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. Azure VM으로 [구성 서버 배포](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server)
3. Azure VM으로 [마스터 대상 서버 배포](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server)
4. [프로세스 서버를 배포합니다.](site-recovery-vmware-to-azure-classic-legacy.md#step-4-deploy-the-on-premises-process-server) 다음 사항에 유의하세요.

	- EC2 인스턴스로 동일한 서브넷/Amazon 가상 사설 클라우드에서 프로세스 서버를 배포해야 합니다. ![EC2 인스턴스](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- 배포한 후에 프로세스 서버가 마이그레이션할 EC2 인스턴스와 통신할 수 있는지 유효성을 검사합니다.
	- 보호하려는 각 VM은 모바일 서비스가 설치되어야 합니다. 이 서비스는 프로세스 서버에 데이터를 보냅니다. 모바일 서비스를 수동으로 설치 또는 푸시하고 VM에 대한 보호를 사용하는 경우 프로세스 서버에서 자동으로 설치합니다. EC2 인스턴스에서 마이그레이션할 방화벽 규칙을 구성하여 이 서비스의 강제 설치를 허용해야 합니다. EC2 인스턴스에 대한 보안 그룹에는 다음과 같은 규칙이 있어야 합니다.

		![방화벽 규칙](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- 사이트 복구 자격 증명 모음에서 구성 서버와 함께 프로세스 서버를 배포하고 등록한 후에 사이트 복구 콘솔에서 **구성 서버** 탭에서 표시되어야 합니다. 최대 15분까지 걸릴 수 있습니다.
	
		![프로세스 서버](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [최신 업데이트를 설치합니다.](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates) 이전에 설치한 모든 구성 요소가 서버 최신 상태인지 확인합니다.
6. [보호 그룹을 만듭니다](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group). 사이트 복구를 사용하여 마이그레이션된 인스턴스를 보호하기 시작하기 위해 보호 그룹을 설정해야 합니다. 그룹에 대한 복제 설정을 지정하하면 해당 그룹에 추가한 모든 인스턴스에 적용합니다. 
7. [가상 컴퓨터를 설정합니다](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect). 모바일 서비스를 (자동 또는 수동으로) 각 인스턴스에 설치해야 합니다.
8. [가상 컴퓨터의 보호를 활성화합니다](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection). 인스턴스를 보호 그룹에 추가하여 가상 컴퓨터에 대한 보호를 사용하도록 설정할 수 있습니다. 다음 사항에 유의하세요.

	- EC2 콘솔에서 얻을 수 있는 인스턴스의 개인 IP 주소를 사용하여 Azure에 마이그레이션할 EC2 인스턴스를 검색할 수 있습니다.
	-  사용자가 만든 보호 그룹에 대한 탭에서 컴퓨터 추가 > 물리적 컴퓨터 ![EC2 검색](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)를 클릭합니다.
	- 인스턴스의 개인 IP 주소를 지정합니다.
		- ![EC2 검색](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- 보호를 사용할 수 있으며 초기 복제는 보호 그룹에 대한 초기 복제 설정에 따라 실행됩니다.
9. [계획되지 않은 장애 조치를 실행합니다](site-recovery-failover-classic-legacy.md#run-an-unplanned-failover). 초기 복제가 완료된 후에 AWS에서 Azure에 계획되지 않은 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 AWS에서 Azure에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. 복구 계획을 [자세히 알아봅니다](site-recovery-create-recovery-plans.md).
		
## 다음 단계

의견이나 질문은 [사이트 복구 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시

<!---HONumber=AcomDC_0121_2016-->