<properties
	pageTitle="템플릿 구현 모범 사례의 상황별 예제"
	description="모범 사례를 보여주는 Azure 리소스 관리자 템플릿 예제를 나타냅니다."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mmercuri"/>

# 템플릿 구현 모범 사례의 상황별 예제

이 항목은 Azure 리소스 관리자 템플릿을 구현하는 방법에 대한 7가지 상황별 예제를 제공합니다. 이 예제에 나와 있는 원칙에 대한 개요를 보려면 [Azure 리소스 관리자 템플릿 설계의 모범 사례](best-practices-resource-manager-design-templates.md)를 참조하세요.

이 항목은 더 큰 백서의 일부입니다. 전체 문서를 읽으려면 [세계 클래스 ARM 템플릿 고려 사항 및 입증된 사례](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World 클래스 ARM 템플릿 - 고려 사항 및 입증된 사례.pdf)를 다운로드합니다.

## 기능 범위 템플릿을 종단간 솔루션 범위 템플으로 이동

기능 범위 템플릿에 대한 개발 패턴은 앞서 공유하였습니다. 기능 범위 템플릿만 사용하는 경우와 종단간 범위 솔루션 템플릿의 일부로 사용하는 경우에 고려 사항에 차이가 있는지가 궁금할 수 있습니다.

예를 들어 SQL Server를 기능으로 배포한 템플릿에 중점을 둔 기술이 있다면, 이 기술을 독립적으로 사용하는 경우에 대한 고려 사항과 웹 응용 프로그램을 지원하기 위해 SQL Server 서버를 사용하는 광범위한 종단간 솔루션 범위 템플릿의 일부로 사용하는 경우에 대한 고려 사항은 무엇일까요.

이 시나리오를 살펴볼 때는 관련이 될만한 리소스의 수를 살펴보는 것이 적절합니다. 강력한 구현의 경우 기능 범위 템플릿은 하나의 SQL Server가 설치된 단일 VM과 저장소 계정만 포함하지는 않습니다. 강력한 기능 범위 템플릿은 고가용성을 위해 배포된 SQL Server과 함께 복수의 VM을 배포합니다. Analysis Services와 같은 일부 기능에 대해 토폴로지와 함께 배포된 Active Directory를 토폴로지에 포함시킬 수도 있습니다.

이 시나리오에 대한 두 가지 주요 고려 사항에는 SQL Server가 어떻게 사용될지에 대한 수명 주기와 시나리오에 적용하고자 하는 RBAC가 포함됩니다. 특히 SQL Server 서버가 솔루션의 나머지 부분과 함께 업데이트되고 삭제될까요 아니면 솔루션이나 솔루션의 다른 부분에 따라서 수명 주기가 달라질까요. 수명 주기가 달라진다면 다른 리소스 그룹에 배치하는 것을 고려해야 합니다.

또 다른 고려 사항은 RBAC를 SQL Server 기능 범위 솔루션 템플릿에 적용하고자 하는 방식입니다. 토폴로지 내에서 RBAC를 적용하고자 하는 방식에 따라서 다른 리소스 그룹을 기반으로 세부 사항이 연결되도록 선택할 수 있습니다. 리소스 수준에서 RBAC를 적용할 수 있지만 SQL Server 기능 범위 솔루션 템플릿에 대한 리소스의 수가 주어진다면 RBAC가 적용되는 고유 리소스 그룹이 고려 사항이 되어야 합니다.

또 다른 고려 사항은 자체적으로 리소스를 만들고 있는지와 "BYOR(Bring Your Own Resources)"을 허용하는지를 확인하기 위해 SQL Server 기능 범위 솔루션 템플릿을 평가하는 것입니다. BYOR(Bring Your Own Resources) 모델에서 기능 범위 솔루션 템플릿은 이전에 존재했던 리소스를 템플릿에 재사용하도록 허용하며, 이에 대한 일반적인 예에는 저장소 계정, 가상 네트워크 또는 가용성 집합이 있습니다. BYOR 접근 방식이 기능 범위 템플릿에 없는 경우에는 선택적 리소스 템플릿에 대해 이 문서의 앞쪽에 정의되어 있는 접근 방식을 사용하여 템플릿을 변경할 수 있습니다. 이런 경우 사용자의 종단간 솔루션 범위 템플릿에는 공통 리소스와 함께 공유 리소스 템플릿이 포함되며 기능 범위 템플릿은 이런 리소스를 선택적으로 지원하도록 확장됩니다. 이렇게 하면 보다 나은 기능 범위 솔루션 템플릿을 만들 수 있고 이 템플릿을 독립적으로 사용하거나 컴퍼지션의 일부로 사용할 수 있습니다.

저장소 계정이 종단간 솔루션 범위 템플릿으로부터 전달되어야 하는지를 평가하는 경우에는 RBAC 역시 재평가되어야 합니다. 구체적으로 말해, RBAC가 이 특정 리소스에 반드시 적용되도록 해야 하나요? 그렇다면 리소스가 전달될 때 이것을 적용할 것으로 예상되는 경우에 신뢰 수준은 솔루션 블록에만 배치되지 않고 독립적으로 사용되는 경우 기능 범위 템플릿에 이것을 선택적으로 제공하고자 하는 모든 사용자에게 배치됩니다. RBAC가 중요한 경우에는 기능 범위 솔루션 템플릿 내에서 이것을 선택적인 템플릿으로 만들지 아니면 기능 범위 솔루션 템플릿 내에서 필요한 RBAC와 함께 생성을 요청할지를 고려해야 합니다.

여러 리소스 그룹에 이것을 배치하기로 결정이 되면 리소스 간의 관계를 정의하기 위해(리소스 그룹에 리소스가 걸쳐있는 경우에도) 리소스 링크를 사용할 수도 있습니다.

## 복수의 기능 범위 템플릿을 포함하는 종단간 솔루션 범위 템플릿 만들기

이것은 주로 이전 사례의 상위 집합입니다. 이 시나리오에서는 조직에 단일 솔루션 블록으로 모으고자 하는 Kafka, Apache Hadoop, Apache Spark, 및 Apache Storm 같은 데이터 기술에 대한 복수의 기능 범위 솔루션 템플릿이 있습니다. 그 결과로 생성된 컴퍼지션은 기능 범위 솔루션 템플릿은 물론 특정 서브넷 할당이 있는 가상 네트워크와 공유 저장소를 사용합니다.

필요한 기능 범위 템플릿 외에 솔루션을 위한 추가적인 리소스(예: 기능 범위 템플릿을 함께 붙여서 구성하기 위한 스크립트)가 필요합니다.

이런 경우 공유 가상 네트워크 및 공유 저장소 계정이 있는지 식별됩니다. 이를 돕기 위해 종단간 솔루션 범위 템플릿의 공유 리소스 템플릿에 해당 항목을 추가해야 하고 기능 범위 템플릿에서 BYOR(Bring Your Own Resources) 접근 방식이 지원되도록 해야 합니다. 그렇지 않으면 이전 사례의 설명과 같이 기능 범위 템플릿에서 이 작업을 수용하도록 수정해야 합니다.

추가하게 될 부가적인 리소스는 개별적인 기능 범위 템플릿을 만드는데 사용되는 패턴의 상위 집합을 따르도록 합니다. 이 경우 공유 리소스 템플릿, 선택적 리소스 템플릿, 멤버 노드 템플릿, 새 리소스에 필요한 상태 구성(스크립트, Chef, Puppet, Powershell DSC)을 추가합니다. 종속성이 있으면 배포의 병렬 처리(및 속도)에 영향을 줄 수 있는 이탈 종속성의 가능성을 없애기 위하여 가능한 경우 dependsOn과 암시적인 참조를 사용하도록 최적화합니다. 리소스의 수명 주기, RBAC 고려 사항, 다른 리소스 그룹에 배치되어야 하는지를 판단하기 위한 종속성 등도 고려합니다.

공유 저장소 계정과 같은 공유 리소스를 추가하는 경우에는 리소스 잠금이 필요한지도 평가해야 합니다. 이것은 우발적인 삭제를 방지하는데 도움이 됩니다.

새 리소스를 추가하는 경우에는 종단간 솔루션 범위 템플릿에 추가되는 리소스가 기능 범위 템플릿으로 격리될 수 있는지도 조사해야 합니다. 그렇다면 재사용과 테스트에 이점을 제공할 수 있는 추가적인 분해를 촉진하는 것에 관해 강력히 고려해야 합니다.

솔루션 블록에서 통합하는 경우, 다음 고려 사항은 이전 예제에서 확인했듯이 개별적인 기능 범위 솔루션 템플릿의 수명 주기가 광범위한 솔루션의 그것과 다른지를 확인하는 것과 RBAC 요구 사항에 이러한 항목을 분리된 리소스 그룹으로 나누는 것이 필요할지를 확인하는 것입니다.

마지막으로 리소스 간에 링크를 정의하고 쿼리할 수 있도록 설정할지를 고려합니다. 그렇다면 리소스 링크를 사용하여 종단간 범위 솔루션 템플릿 전체에(여러 개의 리소스 그룹에 걸쳐있는 경우에도) 이런 설정이 가능합니다.

## 부분적인 켜기/끄기 패턴으로 종단간 솔루션 범위 템플릿 만들기

이 시나리오는 이전 시나리오의 변형입니다. 이 경우 고객은 하루에 걸쳐 고정된 간격으로 온-프레미스 시스템으로부터 데이터를 추출합니다. 수신 데이터를 처리할 데이터 파이프라인과 데이터를 쿼리에 항상 사용할 수 있도록 하는 관계형 데이터 저장소가 있습니다. 클라우드가 종량제 모델이기 때문에 고객은 데이터가 처리용으로 제공되는 경우에만 해당 기간 동안 데이터 파이프라인이 작동하도록 만들고자 합니다.

데이터 파이프라인의 일부로 SQL Server가 있으며, 처리된 데이터를 수신하고 쿼리에 사용할 수 있도록 만드는 일을 합니다. 고객은 파이프라인의 수집 및 처리 부분은 정해진 일정에 따라 켜고 끄기를 원하면서도 SQL Server는 항상 사용할 수 있기를 바랍니다.

이 시나리오에는 수명 주기에서 명백한 차이로 보이면서 고객이 제기하지 않았지만 평가가 필요한 잠재적인 추가 고려 사항이 있습니다.

설명에 따라 다른 리소스를 만들고 삭제하는 동안 SQL Server 배포는 유지됩니다. 처음에는 함께 배포되었다가 템플릿의 다른 멤버가 다른 수명 주기로 소멸되고 만들어집니다. 이것은 다른 리소스 그룹으로 격리되거나 SQL Server 리소스에 대해 리소스 잠금이 적용된 상태로 동일한 리소스 그룹에 남겨질 수 있습니다. 앞서 예제에서 설명한 것과 같이 특히 SQL Server는 큰 리소스 집합으로 표시될 가능성이 있기 때문에 자체 리소스 그룹으로 분리하는 것이 적절할 수 있습니다.

다른 고려 사항은 고객이 데이터 파이프라인의 나머지 부분은 일정에 따라 켜지고 꺼지기를 원한다고 하면서 보고 시스템의 일관적이지 않은 동작은 고려하지 않고 있다는 것입니다. 예약되어 있는 타사의 데이터 배달이 항상 정확한 것은 아닙니다. 일정 시간 동안 연결이 안될 수도 있고 로컬 또는 클라우드 기반 서버의 시계에 편차가 있을 수도 있고, 시간 변경이 예상대로 이루어지거나 그렇지 못할 수도 있습니다. 수집 메커니즘이 켜기/끄기 패턴에서도 사용되어야 할지를 평가해야 합니다. 만약 그렇다면 해당 수명 주기가 처리 구성 요소의 수명 주기보다 큰지도 평가해야 합니다.

Azure 데이터 팩터리 또는 이벤트 허브와 같이 관리되는 서비스를 사용하는 경우 운영 모델 및 관련 청구 접근 방식을 통해 데이터를 수집하여 저장소에 배치하는데 즉시 사용할 수 있도록 설정되기 때문에 이것은 문제가 되지 않습니다. 가상 컴퓨터에 배포하는 기술에 Kafka와 같은 다른 기술을 사용하는 경우 수명 주기를 생성하는 방법과 수집에 필요한 관련 저장소 계정을 살펴봐야 합니다. 이로 인해 수집 및 처리 리소스가 수명 주기를 기반으로 다른 리소스 그룹에 배치될 수 있습니다.

## 구독 내에서 고유한 환경 지원

서비스를 효과적으로 제공하기 위하여 많은 조직에게는 반드시 충족해야 하는 배율 집합, 청구 격리, 책임 격리 및 지리적 격리가 있습니다. Azure에 대한 서비스를 설계하는 경우 이러한 요구를 충족하기 위하여 오랫동안 접근 방식에 사용해 온 구독 분할이 있을 수 있습니다.

리소스 관리자는 구독 내에서 배포될 수 있는 정해진 유형의 리소스 수에 대한 제약 조건을 완화하고 리소스 그룹, RBAC 및 감사를 적용합니다. 조직은 이러한 조합을 통해 분할을 위해 리소스 그룹을 사용하고 요구 사항을 충족시키고 수행해야 하는 구독 분할(이 있다면)의 양을 줄일 수 있습니다.

이 섹션은 이런 유형의 환경에 대한 요구 사항을 살펴보고 ARM을 사용하여 요구 사항을 충족하는 환경을 제공하는 방법에 대한 지침을 제공합니다.

### 격리 고려 사항

이 섹션은 환경, 청구, 및 지리적 격리에 대한 공통적인 고객 드라이버를 보다 자세히 알아봅니다.

#### 환경 격리

서비스 소유자는 다른 환경을 격리하고자 합니다. 각각의 환경을 격리시키면 환경에 대한 액세스를 확보할 수 있는 사람에 대해 보다 세분화된 제어가 가능해집니다. 개발 환경은 액세스할 수 있는 사람에 관해서는 보다 개방적으로 변하는 반면에 환경 범위가 프로덕션에 더 가깝게 이동할수록 사용자(자동화에 사용되는 사람 또는 시스템 계정)의 수가 감소되어 규정 준수에 도움이 되고 전반적인 위험이 최소화됩니다.

#### 청구 격리 – 서비스 개발 및 실행

비즈니스 소유자는 매출원가(COGS)와 운영비(OpEx)를 정확하게 반영하기 위하여 서비스를 연구하고 구축하는 비용과 서비스를 실행하는 비용을 분리할 필요가 있습니다.

앞서 언급한 환경 격리 상위 집합의 취지는 후자를 위해 프로덕션은 독립적인 상태를 유지하면서 전자를 위해 개인 및/또는 집계된 청구의 개발 및 테스트를 분리하는 것입니다.

#### 청구 격리 – 서비스 소비 비용에 대한 투명성 및 책임성 추가

청구 격리는 특정 팀에 의한 플랫폼 소비와 관련된 비용에 대해 투명성을 확보하고 적절한 수준의 책임성을 도입하는 것입니다.

클라우드는 탄력적이며 종량제 모델을 허용하지만 하드웨어를 확보하고 소유하는 비 클라우드(non-cloud) 모델 작업을 하던 일부 개발자에게는 친근하지 않습니다. 비 클라우드(non-cloud)의 경우 켤 수 있는 “컴퓨터”의 수에 대해 물리적인 한계가 있고 리소스를 사용하지 않을 때 꺼 놓거나 규모를 감축하는데 대한 인센티브가 한정되어 있습니다. 이러한 전용 하드웨어를 확보하는 일은 대부분의 경우 하드웨어를 활용하는 개발자에 의해 수행되지 않았습니다.

구독을 격리하고 구독에 대한 책임을 특정 팀에 할당하면서 서비스 소유자는 이런 유형의 구독 분할이 필요한 동작을 이끌어내고 적용시키는데 유익하다는 것을 알게 되었습니다.

#### 지리 기반 격리 – 특정 지역의 법에 의해 관리되는 배포

특정 지역을 대상으로 하는 서비스에 대해 규정 준수 고려 사항을 다루기 위해 배포 방식을 고려하도록 하는 요구 사항이 있는 경우가 있습니다.

서비스는 본질적으로 전역을 대상으로 하지만 특정 지역 내에 속하거나 특정 지역에 서비스를 제공하는 배포는 운영 요구 사항에 의해 관리되기도 합니다. 특정 국가 또는 국가 집합의 국민만을 포함하거나 특정한 백그라운드 검열 프로세스를 전달하는 경우 이런 서비스를 운영합니다.

지리적 격리는 새 플랫폼 서비스와 기능을 이용하는 것에 대한 이점을 제공합니다. 중국과 같은 일부 지역에서는 사용할 수 있는 플랫폼 서비스의 하위 집합만 있거나 플랫폼 서비스 배포가 지연되기도 합니다.

지리적 격리를 통해 새로운 또는 향상된 플랫폼 서비스와 기능을 사용할 수 있는 곳에서는 이를 이용하도록 서비스를 발전시킬 수 있습니다.

### 규정 준수 고려 사항

서비스는 여러 지역에 걸쳐 다양한 시장에 제공될 수 있습니다. 이러한 대상에는 응용 프로그램에 포함된 민감한 데이터나 프로세스가 있으며, 이를 보호하고 관련 업무를 감시하도록 설계된 관련 준수 규정이 있습니다.

#### 역할 및 의무 분리

역할과 의무의 분리는 내부 정책을 준수하기 위하여 내부 서비스가 지켜야 하는 주요 요구 사항입니다. 많은 상용 서비스에도 정부 및 업계 규정 지침을 준수하려면 이것이 요구됩니다. 서비스는 특정한 상황 하에서 권한이 부여된 역할에 대해 서비스 및 서비스의 기본 리소스에 대한 액세스를 제한해야 합니다. 많은 서비스는 RBAC 및 감사라는 두 가지 기능을 제공하기 위해 스캐폴딩을 빌드합니다.

#### 역할 기반 액세스 제어(RBAC) 사용 사례

규정 준수 시나리오에서는 특정 리소스에 대한 액세스를 제약하는 것이 중요합니다.

예를 들어 건강 정보, 재무 데이터, 세금 기록 등과 같이 규정 준수가 합당한 복수의 시나리오에 걸쳐 민감한 데이터를 살펴보는 경우 데이터를 액세스할 수 있고, 볼 수 있고, 조작할 수 있는 사람의 수를 상위 조직의 비즈니스를 수행하기 위해 액세스가 필요한 사람만으로 제한하는 것이 중요합니다.

RBAC는 특정 개인, 시스템 또는 그룹에 대해 식별된 조건 하에서 특정 리소스에 대한 액세스를 제공합니다.

#### 감사

RBAC에 의해 제공되는 한정된 액세스 외에, 조직은 리소스 액세스 및 리소스와의 상호 작용에 대한 감사를 수행해야 합니다.

### Azure 리소스 관리자를 사용하여 구현하기

이전에는 조직이 이러한 목표를 달성하기 위해 구독 분할을 사용했을 것입니다. 가능하기는 하지만 이상적이지는 않습니다. 구독 생성이 사실상 상거래 작업이기 때문에 서비스 관리 API는 새 구독을 자동으로 만들거나 삭제하기 위한 메커니즘을 노출하지 않으며 구독은 수동으로 생성되어야 합니다. 생성되는 구독의 수는 상당히 증가할 수 있으며(Microsoft의 상용 서비스와 같이 매우 큰 서비스에 대해) 그 수치는 천 개를 초과하는 구독으로 이어질 수 있습니다. 결과적으로 조직에 대한 구독을 만들고 관리하기 위하여 사용자 지정 스캐폴딩을 만들게 되기도 합니다.

리소스 관리자를 사용하면 구독 내에 복수의 환경을 배포하는 것이 훨씬 더 간단합니다. 이전 모델에 있었던 고정된 용량과 기능을 완화시키고, 리소스 제약으로 인한 분할의 필요성을 매우 감소시킵니다.

환경은 특정한 RBAC를 가질 수 있는 리소스 그룹 내에 배치될 수 있고 환경 격리를 제공할 수 있도록 합니다. 지리적 격리가 필요한 시나리오의 경우 리소스 그룹을 활용하여 이것을 수행할 수도 있습니다. 리소스 그룹은 지역을 포괄하기 때문에 하나 이상의 지역에 대한 격리가 수행될 수 있습니다.

청구 격리를 제공하기 위하여 청구 롤업 및 요약된 보기에 사용될 수 있는 리소스와 리소스 그룹에 대해 태그를 적용할 수 있습니다. 환경 유형(연구, 교육, 개발, 테스트, 프러덕션), 설명이 가능한 조직이나 개인(“인사”, “경리”, “John Smith”, “Jane Jones”)의 정의하기 위해 태그를 사용할 수 있습니다.

감사 요구 사항은 박스 기능 중에서 기본적인 Azure 리소스 관리자 집합의 일부로 제공되며 중심적인 위치에서 볼 수 있습니다.

최종 소비자는 환경 및 리소스에 대한 인증 및 역할 기반 액세스 컨트롤에 사용되는 Azure Active Directory에 등록된 계정을 갖습니다.

#### 밀도를 위한 최적화

리소스 제한이 Azure 리소스 관리자에서 완화되지만 여전히 제한은 존재합니다. 자체적으로 환경을 만드는 것 외에도 구독 내에서 환경의 밀도를 달성하는 것도 살펴봐야 합니다. 환경을 제공하는 것은 개인 또는 조직에 대해 용량을 제공하는 것이고 어떤 “티셔츠 크기”를 제공하려는지 평가해야 합니다. 특히, 필요한 리소스에 관해 특대, 대, 중, 소 고객 사이의 변형을 확인합니다.

보다 높은 밀도를 달성하기 위해 다른 티셔츠 크기에 대해 다른 구독을 사용하도록 선택할 수 있습니다. 예를 들어 구독 내에 1000개의 소형 티셔츠 크기 환경, 500개의 중형 배포, 100개의 대형 배포, 10개의 초대형 배포를 수용할 수 있습니다. 복수의 구독을 갖기 위해 청구되는 비용이 없기 때문에 최대 밀도를 제공하기 위해 다른 크기를 다른 구독으로 격리할 수 있습니다. 이것은 구독의 수를 비교적 많지 않고 관리하게 쉽게 유지하면서 수행할 수 있습니다.

고려해야 하는 한가지 주요 사항은 고객이 티셔츠 크기를 늘리거나 변경하도록 허용할지 이며, 만약 허용한다면 어떻게 적용할지 입니다.

한가지 접근 방식은 고객이 기존 리소스 그룹 내에서 추가적인 용량을 확보하도록 허용하는 것입니다. 이것은 기술적으로 쉽게 적용될 수 있지만 밀도에 영향을 미칩니다. 모든 고객에 대해 뚜렷하게 크기를 정의하기 보다 밀도를 위한 최적화에 더 많은 오버헤드를 추가하는 일정 수준의 가변성을 적용합니다. 모든 소형 환경의 크기가 X인 경우 최적의 밀도를 위해 구독에 배치할 소형 환경의 수를 사전에 쉽게 계산할 수 있습니다. 고객이 환경을 사용자 지정하도록 허용하는 경우 그 결과는 환경에 대한 예측할 수 없는 수의 변형과 수량이며 X, X+1, X+2 등이 될 수 있습니다. 가변성이 이런 수준인 경우 차이를 수용하기 위하여 구독 내에 용량을 따로 확보해야 하기 때문에 더 낮은 밀도가 달성됩니다.

가능하기는 하지만 밀도는 더 낮아지고 관리에는 더 많은 오버헤드가 요구되기 때문에 일반적인 접근 방식만큼 이상적이지는 않습니다. 비교적 큰 규모의 환경에서는 더 유용한 옵션이 될 수 있습니다. 구독에 배치되는 대형 및 초대형 환경의 수가 적을수록 증가를 수용하기 위해 구독 내에서 이들을 더 적게 배치하도록 선택할 수 있습니다.

또 다른 접근 방식은 고객이 현재 크기의 환경을 삭제하고 크기가 다른 새 환경을 만드는 것입니다. 일부 시나리오에는 적절하지 않지만 개발 및 테스트 환경과 같이 임시로 사용되는 환경에는 유용합니다.

그 다음으로 쉬운 접근 방법은 고객이 더 큰 환경을 확보한 후 그 환경으로의 마이그레이션을 스스로 관리할 수 있도록 하는 것입니다. 예를 들어, 소형 환경에서 SQL Server를 개발하는 고객이 중형 환경을 구매하고 데이터와 사용자 지정 상태 전송을 직접 책임집니다.

대안적인 접근 방법은 한 가지 크기에서 다른 크기로의 전환을 수용하는 위치에 관리 서비스를 제공하는 것입니다. 더 복잡할 것이 확실하지만 워크로드와 고객에 따라서 제공할만한 기능이 될 수 있습니다.

## 부가적인 고객 정책 제약 조건을 포함하는 환경 제공

배포하는 환경에 대한 요구 사항과 정책에 부가적인 항목이 있는 경우가 있습니다. 구체적으로, 외부에 노출되는 포트를 제한하는 정책이 있고 환경에 대한 인바우드 및 아웃바운드 트래픽의 모니터링을 필요로 하는 정책이 있을 수 있습니다. 지원 가능성 및 비용을 고려하면 리소스와 최종 소비자가 만들고 업데이트하고 삭제할 수 있는 항목에 대한 제약이 있을 수도 있습니다. 환경을 제공하는 조직은 일반적으로 지원을 위하여 구독에 대한 액세스가 필요합니다.

이전 시나리오의 상위 집합으로, 주어진 리소스 유형을 만들 수 있는 대상과 그렇지 않은 대상에 대한 부가적인 제약 조건을 포함하는 특정 리소스가 추가로 요구됩니다.

사용자가 특정 리소스를 만들고 업데이트하고 삭제할 수 있는 자격은 역할 기반 액세스 제어를 사용하여 제한할 수 있습니다. 그 예로 네트워크 VNET과 최종 고객이 업데이트하거나 삭제할 수 없는 잠정적인 서브넷을 필요로 하는 조직이 있습니다.

리소스를 읽기 전용으로 만들거나 삭제할 수 없도록 설정하기 위해 리소스 잠금을 구현할 수 있습니다. 사용자나 서비스 주체가 리소스 또는 리소스 그룹에 대해 특정 작업을 수행할 수 있도록 하기 위해 RBAC가 사용될 수 있습니다.

특정 트래픽(예: 응용 프로그램의 계층 간 트래픽)이 필요한 경우, 처음에는 가상 네트워크 어플라이언스와 같은 중간 단계를 거친 후에 사용자 정의 경로를 사용해야 합니다.

가상 어플라이언스는 방화벽이나 NAT 장치와 같이 네트워크 트래픽을 처리하는 데 사용되는 응용 프로그램을 실행하는 VM일 뿐입니다. Azure에 가상 네트워크 어플라이언스를 제공하는 업체가 많이 있어서 이를 사용할 수도 있지만 조직의 자체적인 어플라이언스를 사용할 수도 있습니다.

“Bring Your Own” 어플라이언스를 사용하면 자사의 온-프레미스 환경에 사용될 수 있는 기존 코드를 재사용할 있습니다. 이 가상 어플라이언스 VM은 주소가 자신으로 지정되지 않은 들어오는 트래픽을 받을 수 있어야 합니다. VM이 다른 대상으로 주소가 지정된 트래픽을 받을 수 있도록 하려면 해당 VM에서 IP 전달을 사용하도록 설정해야 합니다.

이전 예제에서와 같이 리소스 수명 주기와 RBAC 제약 조건은 리소스 그룹 전략의 일부로 검토하고 고려해야 합니다.

## 내부의 위험 인물로부터 리소스 보호

위험 인물로부터 프로비전을 처리하는 리소스와 템플릿을 보호하는 것은 조직의 중요 사안입니다.

IT 직원 중에서 악의적인 소프트웨어 개발자나 구성원이 주요 정보를 수정하거나 빼낼 수(범죄를 목적으로 하는 위험 인물에게 전달될 수) 없도록 만들기를 원하는 은행이 그 일례입니다.

일반적인 엔터프라이즈 시나리오는 VM 배포를 작성 또는 업데이트할 수 있는 광범위한 개발자/작업자와 더불어, 배포된 워크로드 내의 중요한 비밀에 액세스할 수 있는 소규모의 신뢰할 수 있는 작업자 그룹을 만드는 것입니다.

ARM과 함께 Azure 키 자격 증명 모음을 사용하여 VM 암호 및 인증서를 오케스트레이션하고 저장할 수 있습니다.

가장 좋은 방법은 자격 증명(키 자료를 포함하는) 생성 및 VM 배포(자격 증명 모음에 포함된 키에 대한 URI 참조를 포함하는)를 위한 별도의 ARM 템플릿을 유지 관리하는 것입니다.

키 자격 증명 모음에 저장된 암호는 신뢰할 수 있는 작업자의 완전한 RBAC 제어 하에 있습니다. 신뢰할 수 있는 작업자가 퇴사하거나 회사 내 다른 그룹으로 이동하는 경우에는 자격 증명 모음에 생성된 키에 대한 더 이상의 액세스 권한이 부여되지 않습니다.

배포용 ARM 템플릿에만 암호에 대한 URI 참조가 포함되므로 코드, 구성 또는 소스 코드 리포지토리에는 실제 암호가 없게 됩니다. 이런 방식은 암호 피싱 기회를 줄이고 위험 인물이 정보를 변경할 수 있는 가능성을 제한합니다.

문서의 앞부분에서 언급했듯이 전역 키 자격 증명 모음은 없습니다. 키 자격 증명 모음은 항상 지역 단위이며 암호는 항상 VM에서 지역성(및 독립성)을 갖습니다.

이러한 접근 방식의 구현 사례는 이 문서의 앞쪽에 나오는 암호 및 인증서에 제공되어 있습니다.

## “Bring Your Own Subscription” 모델 사용

기업 IT, 시스템 통합 업체, 클라우드 서비스 공급업체는 "Bring Your Own Subscription" 모델을 고객에게 사용할 수 있습니다. 구체적으로 말하자면 최종 고객에게 서비스를 제공하여 해당 고객의 Azure 구독을 어느 정도 활용합니다.

이러한 접근 방식에는 다양한 변형이 있으며 각각의 요구 사항에는 아래 설명한 내용과 같이 미세한 차이가 있습니다.

### 계정 내에서 리소스 모니터링을 위해 타사 액세스 허용

모니터링 응용 프로그램이 있는 조직의 경우, 해당 응용 프로그램에서 사용할 데이터를 검색하기 위해 고객의 구독에 대한 읽기 전용 액세스가 필요합니다. 진행 기간 동안 읽기 전용 액세스를 필요로 할 것입니다. 액세스는 고객이 관리해야 하며, 모니터링 서비스를 제공하는 공급자와의 관계가 단절되면 액세스를 종료할 수 있어야 합니다.

#### Azure 리소스 관리자를 사용하여 구현하기

구현에 대한 자세한 내용은 [여기](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)로 이동하여 ‘Developers Guide to Auth with the Azure Resource Manager API’(Azure 리소스 관리자 API를 사용한 권한 부여 개발자 가이드) 페이지에서 상세히 알아볼 수 있습니다. 이 문서는 단계 별 구현 지침은 물론 샘플 코드도 제공합니다.

### 소프트웨어 일회성 배포를 위한 타사 액세스 허용

또 다른 사례로 고객의 계정(배포를 위해 일정 기간 동안 쓰기 권한이 필요한)으로 자사의 소프트웨어 버전을 배포하고 구성하는 경우가 있습니다.

#### Azure 리소스 관리자를 사용하여 구현하기

이전 사례와 유사한 접근 방식을 따릅니다.

설치 요구 사항에 따라서 서비스 주체에게 할당되는 역할은 설치를 완료하는데 필요한 최소 수준의 액세스만 허용해야 하고 설치가 완료된 후에는 액세스가 즉시 해지되도록 해야 합니다.

### 데이터 저장소에 대한 고객 구독을 사용하기 위해 타사 액세스 허용

다른 예로, 조직 소유의 환경에서 소프트웨어를 실행하면서 저장소에 대한 고객의 계정을 사용해야 하는 경우가 있습니다. 이 방법을 통해 고객은 기능을 제공하는 엔터프라이즈 IT, 시스템 통합 업체, CSV에 대한 비용/청구 오버헤드를 추가하지 않으면서 Azure 기계 학습 또는 HDInsight 같은 타사의 기술을 재량에 따라 활용하면서도 자사의 데이터를 항상 관리할 수 있습니다. 관리하는 고객은 조직의 저장소 계정에 대한 지속적인 액세스를 필요로 하며 감사 정보에 액세스하기 위해 해당 정보에 대한 액세스를 갖습니다.

#### Azure 리소스 관리자를 사용하여 구현하기

다른 예제와 동일한 패턴을 사용하여 구현됩니다. 서비스 주체에게 저장소 리소스에 대한 액세스가 제공됩니다. 이 시나리오에서는 해당 역할에 저장소 계정에 대한 읽기 및 쓰기 권한이 요구되며 이런 액세스 수준이 적용되도록 기본 제공되는 참여자 역할이 서비스 주체에게 할당됩니다.

이 시나리오는 자사와 타사가 공유 저장소 계정에 함께 관여하기 때문에 저장소 계정이 우발적으로 삭제되지 않도록 해야 합니다. 시나리오의 이런 측면을 감안하여 스토리지 계정에 대해 리소스 잠금을 적용할 수 있습니다.

### 타사에 의한 서비스 관리 허용

다른 예로, 고객 구독 내의 소프트웨어를 배포하고 모니터링하고 관리해야 하는 경우가 있습니다. 소프트웨어가 배포되는 환경에 대해 고객이 변경할 수 있는(또는 보다 명시적으로 할 수 없는) 것에 관하여 고객에게 제약 조건이 있을 수 있습니다.

#### Azure 리소스 관리자를 사용하여 구현하기

이 섹션의 시작 부분에서 확인한 패턴의 상위 집합을 따릅니다. 구체적으로, 타사에서 사용하는 서비스 주체에게 리소스 그룹 내의 리소스에 대한 모든 권한이 제공됩니다.

또한 고객에 대한 제약 조건이 있기 때문에 고객의 사용자나 그룹에게는 환경을 활용하기에 적합한 권한이 부여됩니다. 이 섹션의 앞쪽에서 확인한 템플릿을 통해 수행될 수 있습니다.

마지막으로 특정 리소스가 우발적으로 삭제되지 않도록 해야 하는 경우가 있습니다. 이런 경우에는 보호가 필요한 리소스에 대해 리소스 잠금도 고려해야 합니다.

## 다음 단계

- 템플릿 작성에 대해 자세히 알아보려면 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- Azure 리소스 관리자에서 보안을 처리하는 방법에 대한 권장 사항을 보려면 [Azure 리소스 관리자에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.
- 템플릿 내부 및 외부로 상태를 공유하는 방법을 알아보려면 [Azure 리소스 관리자 템플릿에서 상태 공유](best-practices-resource-manager-state.md)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->