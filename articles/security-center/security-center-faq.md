<properties
   pageTitle="Azure 보안 센터 FAQ(질문과 대답) | Microsoft Azure"
   description="이 FAQ는 Azure 보안 센터에 대한 질문에 답변합니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Azure 보안 센터 FAQ(질문과 대답)

이 FAQ는 증가된 가시성으로 위협을 예방, 감지 및 대응하고 Microsoft Azure 리소스의 보안을 제어하는 서비스인 Azure 보안 센터에 관한 질문에 답변합니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## 일반적인 질문

### Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

### Azure 보안 센터를 이용하려면 어떻게 해야 하나요?
Azure 보안 센터는 Microsoft Azure 구독을 사용하도록 설정되어 있으며 [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. [포털에 로그인](https://portal.azure.com)하여 **찾아보기**를 선택하고 **보안 센터**로 스크롤합니다. 대시보드에서 몇몇 보안 권장 사항을 즉시 볼 수 있습니다. 서비스는 Azure에서 해당 구성에 따라 일부 컨트롤의 보안 상태를 평가할 수 있기 때문입니다. 보안 모니터링, 권장 사항 및 경고 기능의 완전한 세트를 활용하려면 [데이터 수집을 사용하도록 설정](#data-collection)해야 합니다.

## 결제

### Azure 보안 센터에 대한 청구는 어떻게 작동합니까?
자세한 내용은 [보안 센터 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

## 데이터 수집

### 데이터 컬렉션을 사용하도록 설정하려면 어떻게 해야 하나요?<a name=data-collection></a>
보안 정책에서 Azure 구독에 대한 데이터 수집을 사용하도록 설정할 수 있습니다. 데이터 수집을 사용하도록 설정하려면 [Azure 포털](https://portal.azure.com)에 로그인하여 **찾아보기**, **보안 센터**, **보안 정책**을 차례로 선택합니다. **데이터 수집**을 **켜기**로 설정하고 데이터를 수집할 저장소 계정을 구성합니다(질문 “[내 데이터는 어디에 저장되나요?](#where-is-my-data-stored)” 참조). **데이터 수집**을 사용하도록 설정하면 구독에서 모든 지원되는 가상 컴퓨터에서 보안 구성 및 이벤트 정보를 자동으로 수집합니다.

### 데이터 수집을 사용하도록 설정하면 어떻게 될까요?
데이터 수집은 Azure 모니터링 에이전트 및 Azure 보안 모니터링 확장을 통해 사용하도록 설정됩니다. Azure 보안 모니터링 확장은 다양한 보안 관련 구성 및 이벤트를 이를 [Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW)으로 검색합니다. 또한 운영 체제는 모든 활동에 대한 이벤트 로그 이벤트를 발생시킵니다. Azure 모니터링 에이전트는 이벤트 로그 항목을 읽으며 ETW는 이를 추적하고 분석을 위해 저장소 계정에 복사합니다. 이는 보안 정책에 구성된 저장소 계정입니다. 저장소 계정에 대한 자세한 내용은 질문 "[내 데이터는 어디에 저장되나요?](#where-is-my-data-stored)"를 참조하세요.

### 모니터링 에이전트 또는 보안 모니터링 확장은 내 서버의 성능에 영향을 미치나요?
에이전트 및 확장은 시스템 리소스의 명목 양을 소비하며 성능에 거의 영향을 미치지 않습니다.

### 더 이상 데이터 수집을 사용하도록 설정하지 않으려면 어떻게 하나요?
보안 정책에서 구독에 대해 **데이터 수집**을 사용하지 않도록 설정할 수 있습니다. [Azure 포털에 로그인](https://portal.azure.com)하여 **찾아보기**를 선택하고 **보안 센터**, **보안 정책**을 차례로 선택합니다. 구독을 클릭하면 새 블레이드가 열리고 데이터 수집을 해제하는 옵션을 제공합니다. 위쪽 리본에서 **에이전트 삭제**를 선택하여 기존 가상 컴퓨터에서 에이전트를 삭제합니다.

### 내 데이터는 어디에 저장되나요?<a name=where-is-my-data-stored></a>
가상 컴퓨터를 실행 중인 각 영역에 대해 가상 컴퓨터에서 수집한 데이터가 저장되는 저장소 계정을 선택합니다. 이렇게 하면 쉽게 개인 정보 및 데이터 독립성과 같은 지리적 영역에 데이터를 유지할 수 있습니다. 보안 정책에서 구독에 대한 저장소 계정을 선택합니다. [Azure 포털에 로그인](https://portal.azure.com)하여 **찾아보기**를 선택하고 **보안 센터**, **보안 정책**을 차례로 선택합니다. 구독을 클릭하면 새 블레이드가 열립니다. **저장소 계정 선택**을 클릭하여 영역을 선택합니다. 수집된 데이터는 보안상의 이유로 다른 고객의 데이터와 논리적으로 격리됩니다.

Azure 저장소 및 저장소 계정에 대해 자세히 알아보려면 [저장소 문서](https://azure.microsoft.com/documentation/services/storage/) 및 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.

## Azure 보안 센터 사용

### 보안 정책이란?
보안 정책은 지정된 구독 내에서 리소스에 대해 권장되는 컨트롤 집합을 정의합니다. Azure 보안 센터에서 회사의 보안 요구 사항 및 응용 프로그램 유형 또는 각 구독의 데이터 민감도에 따라 Azure 구독에 대한 정책을 정의합니다.

예를 들어 개발 또는 테스트에 사용되는 리소스의 요구사항은 프로덕션 응용 프로그램에 사용되는 보안 요구 사항과 다릅니다. 마찬가지로 PII(Personally Identifiable Information) 같은 규제된 데이터를 가진 응용 프로그램에 대해서는 더 높은 수준의 보안이 필요할 수 있습니다. Azure 보안 센터에서 사용하도록 설정한 보안 정책에 따라 보안 권장 사항과 모니터링이 결정됩니다. 보안 정책에 대해 자세히 알아보려면 [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.

### 보안 정책을 누가 수정할 수 있나요?
보안 정책은 각 구독에 대해 구성됩니다. 보안 정책을 수정하려면 해당 구독의 소유자 또는 참여자여야 합니다.

보안 정책을 구성하는 방법을 자세히 알아보려면 [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md)을 참조하세요.

### 보안 권장 사항이란?
Azure 보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. 잠재적인 보안 취약성이 식별되면 권장 사항이 생성됩니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 예:

- 맬웨어 방지 프로그램을 프로비전하면 악성 소프트웨어를 식별하여 제거하는 데 도움이 됩니다.
- [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 및 가상 컴퓨터에 대한 트래픽 제어 규칙 구성
- 웹 응용 프로그램의 대상을 지정한 공격에 대해 방어하는 데 도움이 되는 웹 응용 프로그램 방화벽 프로비전
- 누락된 시스템 업데이트 배포
- 권장 기준과 일치하지 않는 OS 구성 해결

보안 정책에 사용하도록 설정된 권장 사항만 여기에 표시됩니다.

### 내 Azure 리소스의 현재 보안 상태를 확인하려면 어떻게 해야 하나요?
**보안 센터** 블레이드의 **리소스 상태** 타일에 현재 환경의 전체 보안 상태가 가상 컴퓨터, 웹 응용 프로그램 및 다른 리소스별로 요약되어 표시됩니다. 각 리소스에는 잠재적 보안 취약성이 식별되었는지 나타내는 표시기가 있습니다. 리소스 상태 타일을 클릭하 면 리소스가 표시되며 주의가 필요하거나 문제가 있을 수 있는 위치를 식별합니다.

### 보안 경고를 트리거하는 것은 무엇인가요?
Azure 보안 센터는 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 결합합니다. 위협이 감지되었을 때 보안 경고가 생성됩니다. 감지되는 사항의 예:

- 알려진 악성 IP 주소와 통신하는 손상된 가상 컴퓨터
- Windows 오류 보고를 사용 하여 감지된 고급 맬웨어
- 가상 컴퓨터에 대한 무작위 공격
- 맬웨어 방지 프로그램 또는 웹 응용 프로그램 방화벽 등과 같은 통합된 파트너 보안 솔루션에서의 보안 경고

### Azure 보안 센터에서 사용 권한은 어떻게 처리되나요?
Azure 보안 센터는 역할 기반 액세스를 지원합니다. 역할 기반 액세스 제어(RBAC)에 대해 자세히 알아보려면 [Azure Active Directory 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.

사용자가 Azure 보안 센터를 열 때 해당 사용자가 액세스 권한을 가지고 있는 리소스와 관련된 권장 사항 및 경고만이 표시됩니다. 즉, 사용자는 구독 또는 리소스가 속한 구독 또는 리소스 그룹에 대한 소유자, 참여자 또는 읽기 권한자 역할이 할당된 리소스와 관련된 항목만 볼 수 있습니다.

보안 정책을 편집하려면 해당 구독의 소유자 또는 참여자여야 합니다.

### 어떤 유형의 가상 컴퓨터가 지원되나요?
Azure 서비스 패브릭 클러스터의 일부인 가상 컴퓨터를 포함하여 [클래식 및 리소스 관리자 배포 모델](../azure-classic-rm.md)을 사용하여 만들어진 가상 컴퓨터가 지원됩니다.

액세스 제어 목록 권장 사항은 현재 가상 컴퓨터(클래식)에 적용됩니다. 네트워크 보안 그룹은 현재 가상 컴퓨터(리소스 관리자)에만 적용됩니다.

### Linux 가상 컴퓨터가 지원되나요?
Azure 보안 센터는 Linux 가상 컴퓨터(Ubuntu 버전 12.04, 14.04, 14.10, 15.04만 해당)에 대한 기본 모니터링을 제공합니다. 나중에 추가 보안 상태 모니터링 및 데이터 수집/분석뿐만 아니라 추가 Linux 배포판에 대한 지원도 사용할 수 있을 것입니다.

<!---HONumber=AcomDC_0224_2016-->