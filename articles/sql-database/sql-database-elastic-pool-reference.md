<properties
	pageTitle="SQL 데이터베이스에 대한 탄력적 데이터베이스 풀 참조 | Microsoft Azure" 
	description="이 참조 문서는 탄력적 데이터베이스 풀 문서 및 프로그래밍 기능 정보에 대한 링크와 세부 정보를 제공합니다."
	keywords="eDTU"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/17/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL 데이터베이스 탄력적 데이터베이스 풀 참조

수십, 수백 또는 수천 개의 데이터베이스를 다루는 SaaS 개발자라면 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 통해 전체 데이터베이스 그룹의 생성, 유지 관리, 성능 및 비용 관리 프로세스를 간소화할 수 있습니다.

## 탄력적 데이터베이스 풀을 만들고 관리하기 위한 필수 조건

- 탄력적 데이터베이스 풀은 Azure SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. V12로 업그레이드하고 데이터베이스를 풀로 직접 마이그레이션하려면 [Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-powershell.md)를 참조하세요.
- 탄력적 데이터베이스 풀 만들기 및 관리는 [Azure 포털](https://portal.azure.com), [PowerShell](sql-database-elastic-pool-powershell.md) 및 .NET 클라이언트 라이브러리(Azure 리소스 관리자만)를 사용하여 지원합니다. [클래식 포털](https://manage.windowsazure.com/) 및 서비스 관리 명령은 지원되지 않습니다.
- 또한 [Transact-SQL](#transact-sql)을 사용하여 새 탄력적 데이터베이스를 만들고 기존 데이터베이스를 탄력적인 데이터베이스 풀 내부 및 외부로 이동하도록 지원합니다.


## 문서 목록

다음은 탄력적 데이터베이스 및 탄력적 작업의 사용을 시작하는 데 도움이 되는 문서입니다.

| 문서 | 설명 |
| :-- | :-- |
| [SQL 데이터베이스 탄력적 데이터베이스 풀](sql-database-elastic-pool.md) | 탄력적 데이터베이스 풀 개요 |
| [가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md) | 탄력적 데이터베이스 풀 사용이 비용 효율적인지를 평가하는 방법 |
| [Azure 포털을 사용하여 SQL 데이터베이스 탄력적 데이터베이스 풀 만들기 및 관리](sql-database-elastic-pool-portal.md) | Azure 포털을 사용하여 탄력적 데이터베이스 풀을 만들고 관리하는 방법 |
| [PowerShell을 사용하여 SQL 데이터베이스 탄력적 데이터베이스 풀 만들기 및 관리](sql-database-elastic-pool-powershell.md) | PowerShell cmdlet을 사용하여 탄력적 데이터베이스 풀을 만들고 관리하는 방법 |
| [.NET용 Azure SQL 데이터베이스 라이브러리를 사용하여 SQL 데이터베이스 만들기 및 관리](sql-database-elastic-pool-csharp.md) | C#을 사용하여 탄력적 데이터베이스 풀을 만들고 관리하는 방법 |
| [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md) | 풀에 있는 전체 탄력적 데이터베이스에서 T-SQL 스크립트의 실행을 지원하는 탄력적 작업 서비스의 개요 |
| [탄력적 데이터베이스 작업 구성 요소 설치](sql-database-elastic-jobs-service-installation.md) | 탄력적 데이터베이스 작업 서비스를 설치하는 방법 |
| [SQL 데이터베이스 보안 설정](sql-database-security.md) | 탄력적 데이터베이스 작업 스크립트를 실행하려면 풀에 있는 모든 데이터베이스에 적절한 권한을 가진 사용자를 추가해야 합니다. |
| [탄력적 데이터베이스 작업 구성 요소를 제거하는 방법](sql-database-elastic-jobs-uninstall.md) | 탄력적 데이터베이스 작업 서비스를 설치할 때 발생하는 오류 복구 |



## 네임스페이스 및 끝점 세부 정보
탄력적 데이터베이스 풀은 Microsoft Azure SQL 데이터베이스에 있는 "ElasticPool" 유형의 Azure 리소스 관리자 리소스입니다.

- **네임스페이스**: Microsoft.Sql/ElasticPool
- REST API 호출에 대한 **관리 끝점**(리소스 관리자): https://management.azure.com



## 탄력적 데이터베이스 풀 속성

| 속성 | 설명 |
| :-- | :-- |
| creationDate | 풀이 만들어진 날짜입니다. |
| databaseDtuMax | 풀에 있는 단일 데이터베이스에서 사용할 수 있는 최대 eDTU 수입니다. 데이터베이스 eDTU 최대값은 리소스 보장이 아닙니다. eDTU 최대값은 풀에 있는 모든 데이터베이스에 적용됩니다. |
| databaseDtuMin | 풀에 있는 단일 데이터베이스에 보장되는 최소 eDTU 수입니다. 데이터베이스 eDTU 최소값은 0으로 설정할 수 있습니다. eDTU 최소값은 풀에 있는 모든 데이터베이스에 적용됩니다. 풀에 있는 데이터베이스 수와 데이터베이스 eDTU 최소값을 곱한 값은 풀 자체의 eDTU를 초과할 수 없습니다. |
| DTU | 풀에 있는 모든 데이터베이스에서 공유하는 eDTU 수입니다. |
| 버전 | 풀의 서비스 계층입니다. 풀에 있는 모든 데이터베이스에는 버전이 있습니다. |
| elasticPoolId | 풀의 인스턴스의 GUID입니다. |
| elasticPoolName | 풀의 이름입니다. 이름은 상위 서버에 대해 고유합니다. |
| 위치 | 풀이 만들어진 데이터 센터의 위치입니다. |
| state | 구독 청구서 지불이 이행되지 않은 경우 상태가 “비활성화”가 되며 그렇지 않으면 “Ready”가 됩니다. |
| storageMB | 풀의 저장소 용량 한도(단위 MB)입니다. 풀의 모든 데이터베이스에서 사용하는 저장소의 합계가 해당 풀의 제한을 초과할 수 없습니다. |


## 탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한


[AZURE.INCLUDE [탄력적 데이터베이스에 대한 SQL DB 서비스 계층 표](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]



## Azure 리소스 관리자 제한 사항

Azure SQL 데이터베이스 V12 서버는 리소스 그룹에 있습니다.

- 각 리소스 그룹에는 최대 800개의 서버가 포함될 수 있습니다.
- 각 서버에는 최대 800개의 탄력적 풀이 포함될 수 있습니다.


## 탄력적 풀 작업의 대기 시간

- 데이터베이스당 보장된 eDTU(databaseDtuMin) 또는 데이터베이스당 최대 eDTU(databaseDtuMax)를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
- 풀의 eDTU/저장소 용량 한도(storageMB) 변경은 풀에 있는 모든 데이터베이스에서 사용 중인 공간의 전체 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 풀 eDTU/저장소 용량 한도를 변경하는 예상 대기 시간은 3시간 이하입니다.



## PowerShell, REST API 및 .NET 클라이언트 라이브러리

탄력적 풀을 만들고 관리하는 데 몇 가지 PowerShell cmdlet 및 REST API 명령을 사용할 수 있습니다. 세부 정보 및 코드 예제는 [PowerShell을 사용한 SQL 데이터베이스 탄력적 데이터베이스 풀 만들기 및 관리](sql-database-elastic-pool-powershell.md)와 [C#을 사용한 SQL 데이터베이스 만들기 및 관리](sql-database-client-library.md)를 참조하세요.


| [PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) | [REST API 명령](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [탄력적 데이터베이스 풀 생성](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [탄력적 데이터베이스 풀의 성능 설정 설정](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [탄력적 데이터베이스 풀 삭제](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [탄력적 데이터베이스 풀 및 해당 속성 값 가져오기](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [탄력적 데이터베이스 풀 관련 작업 상태 가져오기](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [탄력적 데이터베이스 풀에서 데이터베이스 가져오기](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [풀 내부 및 외부에서 데이터베이스의 이동 상태 가져오기](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

Transact-SQL을 사용하여 다음 탄력적 데이터베이스 관리 작업을 수행할 수 있습니다.

| 작업 | 세부 정보 |
| :-- | :-- |
| (풀에 직접) 새 탄력적 데이터베이스를 만듭니다. | [CREATE DATABASE(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx) |
| 기존 데이터베이스를 풀 내부 및 외부로 이동 | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| 풀의 리소스 사용량 통계 가져오기 | [sys.elastic\_pool\_resource\_stats (Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/mt280062.aspx) |


## 대금 청구 및 가격 정보

탄력적 데이터베이스 풀은 다음 특성에 따라 대금이 청구됩니다.

- 탄력적 풀은 풀에 데이터베이스가 없더라도 생성 시부터 대금이 청구됩니다.
- 탄력적 풀은 시간당 대금이 청구됩니다. 이는 단일 데이터베이스의 성능 수준과 같은 계량 빈도입니다.
- 탄력적 풀이 새로운 크기의 eDTU로 조정된 경우, 크기 조정 작업이 완료될 때까지 새 eDTU 크기에 따른 대금이 청구되지 않습니다. 이는 독립 실행형 데이터베이스의 성능 수준을 변경하는 것과 동일한 패턴을 따릅니다.


- 탄력적 풀의 가격은 풀의 eDTU 수를 기준으로 책정됩니다. 탄력적 풀의 가격은 그 안의 탄력적 데이터베이스 사용률과 무관합니다.
- 가격은 (풀 eDTU 수)x(eDTU 당 단위 가격)으로 계산됩니다.

탄력적 풀의 단위 eDTU 가격은 동일한 서비스 계층에 있는 독립 실행형 데이터베이스의 단위 DTU 가격보다 높습니다. 자세한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

## 탄력적 데이터베이스 풀 오류

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | 탄력적 풀이 저장소 용량 한도에 도달했습니다. 탄력적 풀의 저장소 사용량은 (%d)MB를 초과할 수 없습니다. | MB 단위의 탄력적 풀 공간 한도. | 탄력적 풀이 저장소 용량 한도에 도달했을 때 데이터베이스에 데이터를 기록하려고 했습니다. | 가능하다면 탄력적 풀의 DTU를 늘려 저장소 용량 한도를 확장하거나, 탄력적 풀에 있는 개별 데이터베이스에서 사용하는 저장소를 줄이거나, 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 10929 | EX\_USER | %s의 최소 보장은 %d이며, 최대 한도는 %d이고, 해당 데이터베이스의 현재 사용량은 %d입니다. 하지만 현재 서버 사용량이 너무 많아 해당 데이터베이스에 대해 %d 이상의 요청을 지원할 수 없습니다. 도움이 필요하면 [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637)을 참조하세요. 그렇지 않으면 나중에 다시 시도하세요. | 데이터베이스당 DTU 최소값, 데이터베이스당 DTU 최대값 | 탄력적 풀에 있는 전체 데이터베이스의 동시 작업자(요청) 수 합계가 풀 한도를 초과하려고 했습니다. | 가능하다면 탄력적 풀의 DTU를 늘려 작업자 한도를 확대하거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40844 | EX\_USER | 서버 '%ls'에 있는 데이터베이스 '%ls'은(는) 탄력적 풀에 포함된 '%ls' 버전 데이터베이스이며, 연속 복사 관계를 가질 수 없습니다. | 데이터베이스 이름, 데이터베이스 버전, 서버 이름 | 탄력적 풀에 있는 프리미엄이 아닌 데이터베이스에 대해 StartDatabaseCopy 명령이 발급되었습니다. | 서비스 예정 |
| 40857 | EX\_USER | 서버: '%ls'에서 탄력적 풀을 찾을 수 없음, 탄력적 풀 이름: '%ls'. | 서버 이름, 탄력적 풀 이름 | 지정한 탄력적 풀이 지정한 서버에 존재하지 않습니다. | 유효한 탄력적 풀 이름을 입력하세요. |
| 40858 | EX\_USER | 탄력적 풀 '%ls'이(가) 서버 '%ls'에 이미 있습니다. | 탄력적 풀 이름, 서버 이름 | 지정한 탄력적 풀이 지정한 논리 서버에 이미 있습니다. | 새 탄력적 풀 이름을 입력하세요. |
| 40859 | EX\_USER | 탄력적 풀이 서비스 계층 '%ls'을(를) 지원하지 않습니다. | 탄력적 풀 서비스 계층 | 지정한 서비스 계층은 탄력적 풀 프로비저닝에 대해 지원되지 않습니다. | 기본 서비스 계층을 사용하려면 오류를 수정하거나 서비스 계층을 빈 상태로 두세요. |
| 40860 | EX\_USER | 탄력적 풀 '%ls' 및 서비스 목표'%ls'의 조합은 유효하지 않습니다. | 탄력적 풀 이름, 서비스 수준 목표 이름 | 서비스 목표가 'ElasticPool'로 지정된 경우에만 탄력적 풀과 서비스 목표를 함께 지정할 수 있습니다. | 탄력적 풀과 서비스 목표의 올바른 조합을 지정하세요. |
| 40861 | EX\_USER | 데이터베이스 버전 ‘%.*ls’은(는) 탄력적인 풀 서비스 계층 ‘%.*ls’와(과) 다를 수 없습니다. | 데이터베이스 버전, 탄력적인 풀 서비스 계층 | 데이터베이스 버전은 탄력적인 풀 서비스 계층과 다릅니다. | 탄력적인 풀 서비스 계층과 다른 데이터베이스 버전을 지정하지 마세요. 데이터베이스 버전은 지정될 필요가 없습니다. |
| 40862 | EX\_USER | 탄력적 풀 서비스가 지정된 경우 탄력적 풀 이름이 반드시 지정되어야 합니다. | 없음 | 탄력적 풀 서비스 개체는 탄력적 풀을 고유하게 식별하지 않습니다. | 탄력적 풀 서비스 개체를 사용하는 경우 탄력적 풀 이름을 지정하세요. |
| 40864 | EX\_USER | 탄력적 풀의 DTU는 서비스 계층’%.*ls’에 대하여 최고 (%d) DTU이어야 합니다. | 탄력적 풀의 DTU, 탄력적 풀 서비스 계층. | 탄력적 풀의 DTU를 최소 한도 아래로 설정하려고 했습니다. | 탄력적 풀의 DTU를 최소 한도 이상으로 다시 설정하세요. |
| 40865 | EX\_USER | 탄력적 풀의 DTU는 서비스 계층’%.*ls’에 대해 (%d) DTU를 초과할 수 없습니다. | 탄력적 풀의 DTU, 탄력적 풀 서비스 계층. | 탄력적 풀의 DTU를 최대 한도 위로 설정하려고 했습니다. | 탄력적 풀의 DTU를 최대 한도 미만으로 다시 설정하세요. |
| 40867 | EX\_USER | 데이터베이스당 DTU 최대값은 서비스 계층’%.*ls’에 대해 최소 (%d)이어야 합니다. | 데이터베이스당 DTU 최대값, 탄력적 풀 서비스 계층 | 데이터베이스당 DTU 최대값을 지원되는 한도 아래로 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40868 | EX\_USER | 데이터베이스당 DTU 최대값은 서비스 계층’%.*ls’에 대해 (%d)을(를) 초과할 수 없습니다. | 데이터베이스당 DTU 최대값, 탄력적 풀 서비스 계층. | 데이터베이스당 DTU 최대값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40870 | EX\_USER | 데이터베이스당 DTU 최소값은 서비스 계층’%.*ls’에 대해 (%d)을(를) 초과할 수 없습니다. | 데이터베이스당 DTU 최소값, 탄력적 풀 서비스 계층. | 데이터베이스당 DTU 최소값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40873 | EX\_USER | 데이터베이스 수 (%d) 및 데이터베이스당 DTU 최소값은 탄력적 풀의 DTU (%d)을(를) 초과할 수 없습니다. | 탄력적 풀에 포함된 데이터베이스 수, 데이터베이스당 DTU 최소값, 탄력적 풀의 DTU. | 탄력적 풀에 있는 데이터베이스의 DTU 최소값을 탄력적 풀의 DTU를 초과하는 값으로 지정하려고 했습니다. | 탄력적 풀의 DTU를 늘리거나, 데이터베이스당 DTU 최소값을 줄이거나, 탄력적 풀에 포함된 데이터베이스 수를 줄이는 것을 고려하세요. |
| 40877 | EX\_USER | 탄력적 풀에 데이터베이스가 포함되어 있으면 삭제할 수 없습니다. | 없음 | 탄력적 풀이 하나 이상의 데이터베이스를 포함하고 있으므로 삭제할 수 없습니다. | 삭제하려면 탄력적 풀에서 데이터베이스를 제거하세요. |
| 40881 | EX\_USER | 탄력적 풀 ‘%.*ls’이(가) 데이터베이스 개수 한도에 도달했습니다. 탄력적 풀에 대한 데이터베이스 개수 한도는 (%d) DTU를 가진 탄력적 풀에 대해 (%d)을(를) 초과할 수 없습니다. | 탄력적 풀의 이름, 탄력적 풀의 데이터베이스 개수 한도, 리소스 풀에 대한 eDTU. | 탄력적 풀의 데이터베이스 개수 한도에 도달했을 때 탄력적 풀에 데이터베이스를 생성 또는 추가하려고 했습니다. | 데이터베이스 한도를 늘리기 위해 가능하다면 탄력적 풀의 DTU를 늘리거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40889 | EX\_USER | 데이터베이스에 충분한 저장소 공간이 제공되지 않으므로 탄력적 풀 ‘%.*ls’에 대한 DTU 또는 저장소 용량 한도를 줄일 수 없습니다. | 탄력적 풀의 이름. | 아래로 탄력적 풀의 저장소 용량 한도를 저장소 사용량 아래로 줄이려고 했습니다. | 탄력적 풀에서 개별 데이터베이스의 저장소 사용량을 줄이거나 풀에서 데이터베이스를 제거하여 DTU 또는 저장소 용량 한도를 줄이는 것을 고려하세요. |
| 40891 | EX\_USER | 데이터베이스당 DTU 최소값 (%d)은(는) 데이터베이스당 DTU 최대값 (%d)을(를) 초과할 수 없습니다. | 데이터베이스당 DTU 최소값, 데이터베이스당 DTU 최대값. | 데이터베이스당 DTU 최대값 보다 높은 데이터베이스당 DTU 최소값을 설정하려고 했습니다. | 데이터베이스당 DTU 최소값이 데이터베이스당 DTU 최대값을 초과하지 않도록 확인하세요. |
| TBD | EX\_USER | 탄력적 풀에 있는 개별 데이터베이스에 대한 저장소 크기는 ‘%.*ls’ 서비스 계층 탄력적 풀이 허용하는 최대 크기를 초과할 수 없습니다. | 탄력적 풀 서비스 계층 | 데이터베이스의 최대 크기가 탄력적 풀 서비스 계층이 허용하는 최대 크기를 초과합니다. | 탄력적 풀 서비스 계정이 허용한 최대 크기의 한도 내에 있는 데이터베이스의 최대 크기를 설정하세요. |

<!-----HONumber=AcomDC_0218_2016-->