<properties
	pageTitle="RBAC: 기본 제공 역할 | Microsoft Azure"
	description="이 항목에서는 역할 기반 액세스 제어(RBAC)에 대한 기본 제공 역할에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/21/2016"
	ms.author="kgremban"/>

#RBAC: 기본 제공 역할

## 기본 제공 역할

Azure 역할 기반 액세스 제어(RBAC)에는 사용자, 그룹 및 서비스에 할당할 수 있는 다음 기본 제공 역할이 포함되었습니다. 기본 제공 역할의 정의는 수정할 수 없습니다. 향후 Azure RBAC 릴리스에서는 Azure 리소스에 대해 수행할 수 있는 작업 목록에서 작업 집합을 작성하여 사용자 지정 역할을 정의할 수 있도록 할 예정입니다.

역할 정의의 **작업** 및 **작업 안 함** 속성을 보려면 아래 링크를 클릭합니다. **작업** 속성은 Azure 리소스에 허용되는 작업을 지정합니다. 작업 문자열에는 와일드카드 문자를 사용할 수 있습니다. 역할 정의의 **작업 안 함** 속성 허용된 작업에서 제외해야 하는 작업을 지정합니다.


| 역할 이름 | 설명 |
| --------- | ----------- |
| [API 관리 서비스 참여자](#api-management-service-contributor) | API 관리 서비스를 관리할 수 있음 |
| [Application Insights 구성 요소 참여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있음 |
| [자동화 운영자](#automation-operator) | 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음 |
| [BizTalk 참여자](#biztalk-contributor) | BizTalk 서비스를 관리할 수 있음 |
| [ClearDB MySQL DB 참여자](#cleardb-mysql-db-contributor) | ClearDB MySQL 데이터베이스를 관리할 수 있음 |
| [참여자](#contributor) | 액세스를 제외한 모든 것을 관리할 수 있음 |
| [데이터 팩터리 참여자](#data-factory-contributor) | 데이터 팩터리를 관리할 수 있음 |
| [DevTest Lab 사용자](#devtest-lab-user) | 모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료할 수 있음 |
| [문서 DB 계정 참여자](#document-db-account-contributor) | 문서 DB 계정을 관리할 수 있음 |
| [지능형 시스템 계정 참여자](#intelligent-systems-account-contributor) | 지능형 시스템 계정을 관리할 수 있음 |
| [네트워크 참여자](#network-contributor) | 모든 네트워크 리소스를 관리할 수 있음 |
| [NewRelic APM 계정 참여자](#newrelic-apm-account-contributor) | NewRelic 응용 프로그램 성능 관리 계정 만들기 및 응용 프로그램을 관리할 수 있음 |
| [소유자](#owner) | 액세스를 제외한 모든 것을 관리할 수 있음 |
| [판독기](#reader) | 모든 항목을 볼 수 있지만 변경할 수는 없음 |
| [Redis 캐시 참여자](#redis-cache-contributor]) | Redis 캐시를 관리할 수 있음 |
| [스케줄러 작업 컬렉션 참여자](#scheduler-job-collections-contributor) | 스케줄러 작업 컬렉션을 관리할 수 있음 |
| [검색 서비스 참여자](#search-service-contributor) | 검색 서비스를 관리할 수 있음 |
| [보안 관리자](#security-manager) | 보안 구성 요소, 보안 정책 및 가상 컴퓨터를 관리할 수 있음 |
| [SQL DB 참여자](#sql-db-contributor) | 해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음 |
| [SQL 보안 관리자](#sql-security-manager) | SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음 |
| [SQL Server 참여자](#sql-server-contributor) | 해당 보안 관련 정책을 제외한 SQL 서버 및 데이터베이스를 관리할 수 있음 |
| [클래식 저장소 계정 참여자](#classic-storage-account-contributor) | 클래식 저장소 계정을 관리할 수 있음 |
| [저장소 계정 참여자](#storage-account-contributor) | 저장소 계정을 관리할 수 있음 |
| [사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있음 |
| [클래식 가상 컴퓨터 참여자](#classic-virtual-machine-contributor) | 클래식 가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [가상 컴퓨터 참여자](#virtual-machine-contributor) | 가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [클래식 네트워크 참여자](#classic-network-contributor) | 클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음 |
| [웹 계획 참여자](#web-plan-contributor) | 웹 계획을 관리할 수 있음 |
| [웹 사이트 참여자](#website-contributor) | 웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음 |

### API 관리 서비스 참여자
API 관리 서비스를 관리할 수 있음

| **actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | API 관리 서비스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### Application Insights 구성 요소 참여자
Application Insights 구성 요소를 관리할 수 있음

| **actions** | |
| ------- | ------ |
| Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |
| Microsoft.Insights/webtests/* | 웹 테스트 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 자동화 운영자
작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | 자동화 계정 읽기 |
| Microsoft.Automation/automationAccounts/runbooks/read | 자동화 Runbook 읽기 |
| Microsoft.Automation/automationAccounts/schedules/read | 자동화 계정 일정 읽기 |
| Microsoft.Automation/automationAccounts/schedules/write | 자동화 계정 일정 쓰기 |
| Microsoft.Automation/automationAccounts/jobs/read | 자동화 계정 작업 읽기 |
| Microsoft.Automation/automationAccounts/jobs/write | 자동화 계정 작업 쓰기 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | 자동화 계정 작업 중지 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | 자동화 계정 작업 일시 중단 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | 자동화 계정 작업 다시 시작 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | 자동화 계정 작업 일정 읽기 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | 자동화 계정 작업 일정 읽기 |

### BizTalk 참여자
BizTalk 서비스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | BizTalk 서비스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### ClearDB MySQL DB 참여자
ClearDB MySQL 데이터베이스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | ClearDB MySQL 데이터베이스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 참여자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| * | 모든 유형의 리소스 만들기 및 관리 |

| **not actions** | |
| ------- | ------ |
| Microsoft.Authorization/*/Write | 역할 및 역할 할당을 만들 수 없음 |
| Microsoft.Authorization/*/Delete | 역할 및 역할 할당을 삭제할 수 없음 |

### 데이터 팩터리 참여자
데이터 팩터리를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | 데이터 팩터리 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### DevTest Lab 사용자
모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료할 수 있음

| **actions** ||
| ------- | ------ |
| */read | 모든 유형의 리소스 읽기 | | Microsoft.DevTestLab/labs/labStats/action | 랩 통계 읽기 | | Microsoft.DevTestLab/Environments/* | 환경 만들기 및 관리 |
| Microsoft.DevTestLab/labs/createEnvironment/action | 랩 환경 만들기 |
| Microsoft.Compute/virtualMachines/start/action | 가상 컴퓨터 시작 |
| Microsoft.Compute/virtualMachines/restart/action | 가상 컴퓨터 다시 시작 |
| Microsoft.Compute/virtualMachines/deallocate/action | 가상 컴퓨터 할당 취소 |
| Microsoft.Storage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |
| Microsoft.Network/virtualNetworks/join/action | 가상 네트워크 연결 |
| Microsoft.Network/loadBalancers/join/action | 부하 분산 장치 연결 |
| Microsoft.Network/publicIPAddresses/link/action | 공용 IP 주소에 연결 |
| Microsoft.Network/networkInterfaces/link/action | 네트워크 인터페이스에 연결 |
| Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스 작성 |

### 문서 DB 계정 참여자
문서 DB 계정을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | DocumentDB 계정 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 지능형 시스템 계정 참여자
지능형 시스템 계정을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | 지능형 시스템 계정 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 네트워크 참여자
모든 네트워크 리소스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Network/* | 네트워크 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### NewRelic APM 계정 참여자
NewRelic 응용 프로그램 성능 관리 계정 만들기 및 응용 프로그램을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | NewRelic 응용 프로그램 성능 관리 계정 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 소유자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| * | 모든 유형의 리소스 만들기 및 관리 |

### 판독기
모든 항목을 볼 수 있지만 변경할 수는 없음

| **actions** ||
| ------- | ------ |
| **/read | 암호를 제외한 모든 유형의 리소스 읽기 |

### Redis 캐시 참여자
Redis 캐시를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Cache/redis/* | Redis 캐시 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 스케줄러 작업 컬렉션 참여자
스케줄러 작업 컬렉션을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | 스케줄러 작업 컬렉션 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 검색 서비스 참여자
검색 서비스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | 검색 서비스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 보안 관리자
보안 구성 요소, 보안 정책 및 가상 컴퓨터를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | 클래식 네트워크에 대한 구성 정보 읽기 |
| Microsoft.ClassicCompute/*/read | 클래식 계산 가상 컴퓨터에 대한 구성 정보 읽기 |
| Microsoft.ClassicCompute/virtualMachines/*/write | 가상 컴퓨터에 대한 구성 작성 |
| Microsoft.Security/* | 보안 구성 요소 및 정책 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### SQL DB 참여자
해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | SQL Server 읽기 |
| Microsoft.Sql/servers/databases/* | SQL 데이터베이스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **not actions** | |
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 보안 메트릭을 편집할 수 없음 |

### SQL 보안 관리자
SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | SQL Server 읽기 |
| Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/read | SQL 데이터베이스 읽기 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
| Microsoft.Authorization/*/read | Microsoft 권한 부여 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Sql/servers/databases/schemas/read | SQL 서버 데이터베이스 스키마 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/read | SQL 서버 데이터베이스 테이블 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | SQL 서버 데이터베이스 테이블 열 읽기 |

### SQL Server 참여자
해당 보안 관련 정책을 제외한 SQL 서버 및 데이터베이스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | SQL 서버 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여|
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **not actions** | |
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭을 편집할 수 없음 |

### 클래식 저장소 계정 참여자
클래식 저장소 계정을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resources/read | 구독 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 저장소 계정 참여자
저장소 계정을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
| Microsoft.Authorization/*/read | 모든 권한 부여 읽기 |
| Microsoft.Resources/subscriptions/resources/read | 구독 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 사용자 액세스 관리자
Azure 리소스에 대한 사용자 액세스를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| */read | 암호를 제외한 모든 유형의 리소스 읽기 | 
| Microsoft.Authorization/* | 읽기 권한 부여 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 클래식 가상 컴퓨터 참여자
클래식 가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | 클래식 저장소 계정 읽기 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | 저장소 계정 디스크 읽기 |
| Microsoft.ClassicStorage/storageAccounts/images/read | 저장소 계정 이미지 읽기 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.ClassicNetwork/reservedIps/read | 예약된 IP 주소 읽기 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 가상 네트워크 연결 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 예약된 IP 연결 |
| Microsoft.ClassicCompute/domainNames/* | 클래식 계산 도메인 이름 만들기 및 관리 |
| Microsoft.ClassicCompute/virtualMachines/* | 가상 컴퓨터 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 가상 컴퓨터 참여자
가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Storage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크 서브넷 연결 |
| Microsoft.Network/loadBalancers/read | 부하 분산 장치 읽기 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙 연결 |
| Microsoft.Network/publicIPAddresses/read | 네트워크 공용 IP 주소 읽기 |
| Microsoft.Network/publicIPAddresses/join/action | 네트워크 공용 IP 주소 연결 |
| Microsoft.Network/networkSecurityGroups/read | 네트워크 보안 그룹 읽기 |
| Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹 연결 |
| Microsoft.Network/networkInterfaces/* | 네트워크 인터페이스 만들기 및 관리 |
| Microsoft.Network/locations/* | 네트워크 위치 만들기 및 관리 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | 네트워크 응용 프로그램 게이트웨이 백 엔드 주소 풀 연결 |
| Microsoft.Compute/virtualMachines/* | 가상 컴퓨터 만들기 및 관리 |
| Microsoft.Compute/availabilitySets/* | 계산 가용성 집합 만들기 및 관리 |
| Microsoft.Compute/locations/* | 계산 위치 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 클래식 네트워크 참여자
클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | 클래식 네트워크 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 웹 계획 참여자
웹 계획을 관리할 수 있음

| **actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | 서버 팜 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### 웹 사이트 참여자
웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음

| **actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | 서버 팜 읽기 |
| Microsoft.Web/serverFarms/join/action | 서버 팜 연결 |
| Microsoft.Web/sites/* | 웹 사이트 만들기 및 관리 |
| Microsoft.Web/certificates/* | 웹 사이트 인증서 만들기 및 관리 |
| Microsoft.Web/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트 읽기 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 구독 리소스 그룹 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 구독 리소스 그룹 리소스 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 구독 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |

## RBAC 항목
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0128_2016-->