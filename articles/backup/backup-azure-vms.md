<properties
	pageTitle="Azure 가상 컴퓨터 백업 | Microsoft Azure"
	description="Azure 가상 컴퓨터 백업에 대한 이 절차를 사용하여 가상 컴퓨터 검색, 등록 및 백업합니다."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="가상 컴퓨터 백업; 가상 컴퓨터 백업 백업; 백업 및 재해 복구; VM 백업"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Azure 가상 컴퓨터 백업
이 문서 기존 Azure 가상 컴퓨터(VM)를 백업하는 방법에 대한 절차를 제공하여 귀사의 백업 및 재해 복구 정책에 따라 VM을 보호합니다.

우선 Azure 가상 컴퓨터를 백업하기 전에 몇 가지 주의 사항이 있습니다. 아직 수행하지 않은 경우 계속 진행하기 전에 [필수 구성 요소](backup-azure-vms-prepare.md)를 완료하여 VM 백업에 대한 환경을 준비합니다.

자세한 내용은 [Azure에서 VM 백업 인프라 계획](backup-azure-vms-introduction.md) 및 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)에 대한 문서를 참조하세요.

Azure 가상 컴퓨터 백업에는 3가지 주요 단계가 포함됩니다.

![Azure IaaS VM를 백업하는 세 단계](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE] 가상 컴퓨터 백업은 로컬 프로세스입니다. 한 지역에서 다른 지역의 백업 자격 증명 모음에 가상 컴퓨터를 백업할 수 없습니다. 따라서 백업해야 하는 VM이 있는 모든 Azure 지역의 경우, 해당 지역에 1개 이상의 백업 저장소가 만들어져야 합니다.

## 1단계 - Azure 가상 컴퓨터 검색
검색 프로세스는 항상 첫 번째 단계로 실행하여 구독에 추가된 새 가상 컴퓨터를 식별해야 합니다. 프로세스는 클라우드 서비스 이름 및 지역과 같은 추가 정보와 함께 구독의 가상 컴퓨터 목록을 Azure에 쿼리합니다.

1. Azure 포털의 **복구 서비스**에 있는 백업 저장소로 이동하여 **등록된 항목**을 클릭합니다.

2. 드롭다운 메뉴에서 **Azure 가상 컴퓨터**를 선택합니다.

    ![워크로드 선택](./media/backup-azure-vms/discovery-select-workload.png)

3. 페이지 맨 아래에서 **검색**을 클릭합니다.
 ![검색 단추](./media/backup-azure-vms/discover-button-only.png)

    검색 프로세스는 가상 컴퓨터를 표로 정리하는 동안 몇 분이 걸릴 수 있습니다. 화면 맨 아래에 있는 알림은 프로세스가 실행되고 있다는 것을 알려줍니다.

    ![VM 검색](./media/backup-azure-vms/discovering-vms.png)

    프로세스가 완료되면 알림이 변경됩니다.

    ![검색 완료](./media/backup-azure-vms/discovery-complete.png)

##  2단계 - Azure 가상 컴퓨터 등록
Azure 가상 컴퓨터를 등록하여 Azure 백업 서비스와 연결합니다. 일반적으로 일회성 작업입니다.

1. Azure 포털의 **복구 서비스**에 있는 백업 저장소로 이동한 다음, **등록된 항목**을 클릭합니다.

2. 드롭다운 메뉴에서 **Azure 가상 컴퓨터**를 선택합니다.

    ![워크로드 선택](./media/backup-azure-vms/discovery-select-workload.png)

3. 페이지의 맨 아래에서 **등록**을 클릭합니다.
 ![등록 단추](./media/backup-azure-vms/register-button-only.png)

4. **등록 항목** 바로 가기 메뉴에서 등록하려는 가상 컴퓨터를 선택합니다. 동일한 이름을 가진 가상 컴퓨터가 두 개 이상 있는 경우 클라우드 서비스를 사용하여 가상 컴퓨터를 구분합니다.

    >[AZURE.TIP] 한 번에 여러 가상 컴퓨터를 등록할 수 있습니다.

    선택한 각 가상 컴퓨터에 대한 작업이 만들어집니다.

5. 알림에서 **작업 보기**를 클릭하여 **작업** 페이지로 이동합니다.

    ![작업 등록](./media/backup-azure-vms/register-create-job.png)

    또한 등록 작업의 상태와 함께 가상 컴퓨터가 등록된 항목 목록에 나타납니다.

    ![등록 상태 1](./media/backup-azure-vms/register-status01.png)

    작업이 완료되면 상태가 *등록된* 상태를 반영하도록 변경됩니다.

    ![등록 상태 2](./media/backup-azure-vms/register-status02.png)

## 3단계 - Azure 가상 컴퓨터 보호
이제 가상 컴퓨터에 대한 백업 및 보존 정책을 설정할 수 있습니다. 단일 보호 작업을 사용하여 여러 가상 컴퓨터를 보호할 수 있습니다.

2015년 5월 이후에 만든 Azure 백업 자격 증명 모음은 자격 증명 모음에 기본 제공되는 기본 정책을 사용합니다. 이 기본 정책을 30일의 기본 보존 기간 및 하루 한 번 백업 일정과 함께 제공됩니다.

1. Azure 포털의 **복구 서비스**에 있는 백업 저장소로 이동한 다음, **등록된 항목**을 클릭합니다.
2. 드롭다운 메뉴에서 **Azure 가상 컴퓨터**를 선택합니다.

    ![포털에서 워크로드 선택](./media/backup-azure-vms/select-workload.png)

3. 페이지 맨 아래에 있는 **보호**를 클릭합니다.

    **보호 항목 마법사**가 나타납니다. 이 마법사는 등록되고 보호되지 않은 가상 컴퓨터만을 나열합니다. 여기서 보호하려는 가상 컴퓨터를 선택합니다.

    동일한 이름으로 둘 이상의 가상 컴퓨터가 있는 경우 가상 컴퓨터 사이에서 구별하기 위해 클라우드 서비스를 사용합니다.

    >[AZURE.TIP] 한 번에 여러 가상 컴퓨터를 보호할 수 있습니다.

    ![규모로 보호 구성](./media/backup-azure-vms/protect-at-scale.png)

4. **백업 일정**을 선택하여 선택한 가상 컴퓨터를 백업합니다. 정책의 기존 집합에서 선택하거나 새로 정의할 수 있습니다.

    각 백업 정책 정책에는 해당 정책과 연관된 여러 가상 컴퓨터가 있을 수 있습니다. 그러나 가상 컴퓨터는 특정 시간에 한 정책에만 연관될 수 있습니다.

    ![새 정책으로 보호](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] 백업 정책은 예약된 백업의 보존 체계를 포함합니다. 기존 백업 정책을 선택하면 다음 단계에서 보존 옵션을 수정할 수 없습니다.

5. **보존 범위**를 선택하여 백업과 연관시킵니다.

    ![유연한 보존으로 보호](./media/backup-azure-vms/policy-retention.png)

    보존 정책은 백업을 저장하는 시간을 지정합니다. 백업이 수행되는 시기에 따라 다른 보존 정책을 지정할 수 있습니다. 예를 들어 각 분기 말에 수행된 백업 지점은 감사를 위해 더 오랜 기간 동안 유지되어야 하는 반면, 매일 수행된 백업 지점(작업 복구 지점 역할)은 90일 동안 유지되어야 합니다.

    ![가상 컴퓨터는 복구 지점으로 백업됨](./media/backup-azure-vms/long-term-retention.png)

    이 예제 이미지에서

    - **일 단위 보존 정책**: 매일 수행된 백업이 30일 동안 저장됩니다.
    - **주 단위 보존 정책**: 매주 일요일에 수행된 백업이 104주 동안 유지됩니다.
    - **월 단위 보존 정책**: 매달 마지막 주 일요일에 수행된 백업이 120개월 동안 유지됩니다.
    - **월 단위 보존 정책**: 매년 1월 첫째 주 일요일에 수행된 백업이 99년 동안 유지됩니다.

    작업은 선택한 가상 컴퓨터 각각에 대해 보호 정책을 구성하고 가상 컴퓨터를 해당 정책과 연결합니다.

6. **작업**을 클릭하고 적합한 필터를 선택하여 **보호 구성** 작업 목록을 봅니다.

    ![보호 작업 구성](./media/backup-azure-vms/protect-configureprotection.png)

## 초기 백업
가상 컴퓨터가 정책으로 보호되면, 이는 *보호됨 - (초기 백업 보류 중)* 상태로 **보호된 항목** 탭 아래에 표시됩니다. 기본적으로 첫 번째 예약된 백업은 *초기 백업*입니다.

보호를 구성한 후에 즉시 초기 백업을 트리거하려면.

1. **보호 되는 항목** 페이지 아래쪽에서 **지금 백업**을 클릭합니다.

    Azure 백업 서비스는 초기 백업 작업에 대한 백업 작업을 만듭니다.

2. **작업** 탭을 클릭하여 작업 목록을 봅니다.

    ![진행 중인 백업](./media/backup-azure-vms/protect-inprogress.png)

>[AZURE.NOTE] 백업 작업의 일부로 Azure 백업 서비스는 각 가상 컴퓨터에서 백업 확장에 대한 명령을 발행하여 모든 쓰기를 플러시하고 일관된 스냅숏을 찍습니다.

초기 백업이 완료되면 **보호된 항목** 탭에서 가상 컴퓨터의 상태가 *보호됨*으로 표시됩니다.

![가상 컴퓨터는 복구 지점으로 백업됨](./media/backup-azure-vms/protect-backedupvm.png)

## 백업 상태 및 세부 정보 보기
보호되면 가상 컴퓨터 수가 **대시보드** 페이지 요약에서도 증가합니다. 또한 **대시보드** 페이지에서 지난 24시간 내의 *성공* 및 *실패*한 작업 수 및 아직 *진행 중*인 작업 수를 보여줍니다. 한 범주를 클릭하면 **작업** 페이지에서 해당 범주에 대해 자세히 살펴볼 수 있습니다.

![대시보드 페이지에서 백업 상태](./media/backup-azure-vms/dashboard-protectedvms.png)

대시보드의 값은 24시간마다 한 번 새로 고쳐집니다.

## 문제 해결 오류
가상 컴퓨터를 백업하는 동안 문제를 실행하는 경우 도움말은 이 [문제해결](backup-azure-vms-troubleshoot.md) 지침을 살펴봅니다.

## 다음 단계

- [가상 컴퓨터 관리 및 모니터링](backup-azure-manage-vms.md)
- [가상 컴퓨터 복원](backup-azure-restore-vms.md)

<!---HONumber=AcomDC_0302_2016-->