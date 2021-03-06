<properties 
    pageTitle="탄력적 데이터베이스 풀을 사용하여 리소스 확장 | Microsoft Azure" 
    description="PowerShell을 사용하여 여러 데이터베이스를 관리하도록 탄력적 데이터베이스 풀을 만들어 Azure SQL 데이터베이스 리소스를 확장하는 방법에 대해 알아봅니다." 
	keywords="여러 데이터베이스, 확장"    
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="02/23/2016"
    ms.author="adamkr; sstein"/>

# 여러 SQL 데이터베이스에 대해 리소스를 확장하도록 PowerShell을 사용한 탄력적 데이터베이스 풀 만들기 

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

PowerShell cmdlet을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 만들어 여러 데이터베이스를 관리하는 방법에 대해 알아봅니다.

> [AZURE.NOTE] 탄력적 데이터베이스 풀은 현재 미리 보기 상태이며, SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. SQL 데이터베이스 V11 서버가 있는 경우 한 단계에서 [PowerShell을 사용하여 V12로 업그레이드 및 풀 만들기](sql-database-upgrade-server-portal.md)를 할 수 있습니다.

탄력적 데이터베이스 풀을 사용하면 여러 SQL 데이터베이스에 걸친 데이터베이스 리소스 및 관리를 확장할 수 있습니다.

이 문서에서는 Azure 구독을 제외하고 (V12 서버를 포함하여) 탄력적 데이터베이스 풀을 만들고 구성하는 데 필요한 모든 항목을 만드는 방법을 보여 줍니다. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.




Azure PowerShell을 사용하여 탄력적 데이터베이스 풀을 만드는 각각의 단계가 세분화되고 이해하기 쉽게 설명되어 있습니다. 단순히 명령의 요약 목록을 원하는 경우 이 문서의 아래쪽에 있는 **모든 항목 요약** 섹션을 참조하세요.


PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.




## 자격 증명 구성 및 구독 선택

이제 Azure 리소스 관리자 모듈을 실행하고 있으므로 탄력적 데이터베이스 풀을 만들고 구성하는 데 필요한 모든 cmdlet에 액세스할 수 있습니다. 먼저 Azure 계정에 대한 액세스를 설정해야 합니다. 다음을 실행하면 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

	Login-AzureRmAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이는 이전 단계에서 복사하거나, 또는 구독이 여러 개일 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독을 설정한 후 다음 cmdlet을 실행합니다.

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## 리소스 그룹, 서버, 방화벽 규칙 만들기

이제 Azure 구독에 대한 cmdlet 실행에 대한 액세스가 가능하므로 다음 단계는 탄력적 데이터베이스 풀을 여러 데이터베이스를 포함하도록 만들 서버가 포함된 리소스 그룹을 설정하는 것입니다. 다음 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다. **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations**를 실행하여 유효한 위치의 목록을 가져옵니다.

이미 리소스 그룹이 있다면 다음 단계로 진행하거나, 아래 명령을 실행하여 새 리소스 그룹을 만들 수 있습니다.

	New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"

### 서버 만들기 

탄력적 데이터베이스 풀은 Azure SQL 데이터베이스 서버 내부에서 만들어집니다. 이미 서버가 있다면 다음 단계로 진행하거나 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet을 실행하여 새 V12 서버를 만들 수 있습니다. ServerName을 사용자의 서버 이름으로 바꿉니다. 서버 이름은 Azure SQL Server에 대해 고유해야 하며 서버 이름이 이미 사용 중인 경우 오류가 발생할 수 있습니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다. 서버가 성공적으로 생성된 후 서버 세부 정보와 PowerShell 프롬프트가 표시됩니다. 명령을 편집하여 선택한 모든 유효한 위치를 사용할 수 있습니다.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

이 명령을 실행하면 **사용자 이름**과 **암호**를 묻는 창이 열립니다. 이는 Azure 자격 증명이 아니며, 새 서버에 만들려는 관리자 자격 증명이 될 사용자 이름과 암호를 입력해야 합니다.


### 서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 구성

서버에 액세스할 수 있도록 방화벽 규칙을 설정합니다. [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586.aspx) 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다.

서버에서 다른 Azure 서비스에 대한 액세스를 허용해야 하는 경우, 특별한 방화벽 규칙을 추가하고 모든 Azure 트래픽이 서버에 액세스할 수 있도록 하는 **-AllowAllAzureIPs** 스위치를 추가합니다.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하세요.


## 탄력적 데이터베이스 풀 및 탄력적 데이터베이스 만들기

리소스 그룹, 서버, 방화벽 규칙을 구성했으므로 이제 서버에 액세스할 수 있습니다. [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cmdlet을 사용하여 탄력적 데이터베이스 풀을 만듭니다. 이 명령은 총 400 eDTU를 공유하는 풀을 만듭니다. 풀에 있는 각 데이터베이스는 항상 10 eDTU(DatabaseDtuMin)를 사용할 수 있도록 보장됩니다. 풀에 있는 개별 데이터베이스는 최대 100 eDTU(DatabaseDtuMax)를 사용할 수 있습니다. 매개 변수 설명에 대한 자세한 내용은 [Azure SQL 데이터베이스 탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.


	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### 탄력적 데이터베이스 만들기 및 탄력적 데이터베이스 풀에 추가

이전 단계에서 만든 풀은 비어 있으며 포함된 탄력적 데이터베이스가 없습니다. 다음 섹션에서는 풀 내에 새 탄력적 데이터베이스를 만드는 방법 및 풀에 기존 데이터베이스를 추가하는 방법을 보여 줍니다.

*풀을 만든 후에 풀에서 탄력적인 새 데이터베이스를 만들고 기존 데이터베이스는 풀 내부 및 외부로 이동하는 데 Transact-SQL을 사용할 수 있습니다. 자세한 정보는 [탄력적 데이터베이스 풀 참조 - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)을 참조하세요.*

### 탄력적 데이터베이스 풀 내에 새 탄력적 데이터베이스 만들기

풀 내에서 직접 새 데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### 기존 데이터베이스를 탄력적 데이터베이스 풀 내로 이동

기존 데이터베이스를 풀로 이동하려면 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.


데모 목적으로 탄력적 데이터베이스 풀에 포함되지 않은 데이터베이스를 만듭니다.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

기존 데이터베이스를 탄력적 데이터베이스 풀로 이동합니다.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 탄력적 데이터베이스 풀의 성능 설정 변경


    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 탄력적 데이터베이스 및 탄력적 데이터베이스 풀 모니터링
탄력적 데이터베이스 풀은 여러 데이터베이스를 관리하는 노력을 확장할 수 있도록 돕는 메트릭 보고서를 제공합니다.


### 탄력적 데이터베이스 풀 관련 작업 상태 확인

생성 및 업데이트를 포함하여 탄력적 데이터베이스 풀 관련 작업의 상태를 추적할 수 있습니다.

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### 탄력적 데이터베이스를 탄력적 데이터베이스 풀 내외부로 이동하는 작업의 상태 확인

	Get-AzureRmSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### 탄력적 데이터베이스 풀에 대한 리소스 사용 메트릭 가져오기

원본 풀 한도의 백분율로 검색할 수 있는 메트릭:

* 평균 CPU 사용률 - cpu\_percent 
* 평균 IO 사용률 - data\_io\_percent 
* 평균 로그 사용률 - log\_write\_percent 
* 평균 메모리 사용률 - memory\_percent 
* 평균 eDTU 사용률(CPU/IO/로그 사용률의 최대값으로) – DTU\_percent 
* 최대 동시 사용자 요청 수(작업자) – max\_concurrent\_requests 
* 최대 동시 사용자 세션 수 – max\_concurrent\_sessions 
* 탄력적 풀의 총 저장소 크기 – storage\_in\_megabytes 


메트릭 세분성/보존 기간:

* 데이터는 5분의 세분성으로 반환됩니다.  
* 데이터 보존 기간은 14일입니다.  


이 cmdlet 및 API는 한 번의 호출로 검색할 수 있는 행의 수를 1000행(5분의 세분성에서 약 3일치 데이터)으로 제한합니다. 하지만 이 명령을 서로 다른 시작/종료 시간 간격으로 여러 번 호출하여 더 많은 데이터를 검색할 수 있습니다.


메트릭 검색:

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

호출을 반복하고 데이터를 추가하여 더 많은 날짜의 데이터 가져오기:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
테이블 형식 지정:

    $table = Format-MetricsAsTable $metrics 

CSV 파일로 내보내기:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### 탄력적 데이터베이스에 대한 리소스 사용 메트릭 가져오기

이 API는 다음과 같은 의미 체계 차이를 제외하고 독립 실행형 데이터베이스의 리소스 사용률을 모니터링하는 데 사용되는 현재(V12) API와 동일합니다.

* 이 API의 경우 메트릭이 해당 탄력적 데이터베이스 풀에 대한 databaseDtuMax(또는 CPU, IO 등 기반 메트릭에 대해 동등한 최대값) 집합당 백분율로 표현됩니다. 예를 들어 이 메트릭에서 50% 사용률은 특정 리소스 사용률이 부모 탄력적 데이터베이스 풀의 리소스에 대한 데이터베이스당 최대값 한도의 50%임을 나타냅니다. 

메트릭 가져오기:

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

필요할 경우 호출을 반복하고 데이터를 추가하여 다른 날짜의 데이터 가져오기:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

테이블 형식 지정:

    $table = Format-MetricsAsTable $metrics 

CSV 파일로 내보내기:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## 모든 항목 요약


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRmResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRmSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 다음 단계

탄력적 데이터베이스 풀을 만든 후에 탄력적 작업을 만들어 풀에 있는 탄력적 데이터베이스를 관리할 수 있습니다. 탄력적 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 T-SQL 스크립트를 실행할 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.


## 탄력적 데이터베이스 참조

API 및 오류 세부 정보를 포함하여 탄력적 데이터베이스 및 탄력적 데이터베이스 풀에 대한 자세한 내용은 [탄력적 데이터베이스 참조](sql-database-elastic-pool-reference.md)를 참조하세요.

<!---HONumber=AcomDC_0224_2016-->