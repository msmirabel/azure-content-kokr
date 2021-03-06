<properties
   pageTitle="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
   description="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell을 사용하여 SQL 데이터 웨어하우스 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Azure PowerShell cmdlet을 다운로드하여 실행합니다.

> [AZURE.NOTE]  SQL 데이터 웨어하우스와 함께 Microsoft Azure Powershell을 사용하기 위해 ARM cmdlet과 함께 Azure PowerShell의 최신 버전을 다운로드하고 설치해야 합니다. `Get-Module -ListAvailable -Name Azure`을 실행하여 사용 중인 버전을 확인할 수 있습니다. 이 문서는 Microsoft Azure PowerShell 버전 1.0.3을 기반으로 합니다.

PowerShell을 사용하여 설치하지 않은 경우 다운로드하고 구성해야 합니다.

1. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다.
2. 모듈을 실행하려면 시작 창에 **Windows PowerShell**을 입력합니다.
3. 이 cmdlet을 실행하여 Azure 리소스 관리자에 로그인합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법][]을 참조하세요.

	```
	Login-AzureRmAccount
	```

4. 현재 세션에 사용하려는 구독을 선택합니다.

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## SQL 데이터 웨어하우스 데이터베이스 만들기
SQL 데이터 웨어하우스를 배포하려면 New-AzureRmSQLDatabase cmdlet을 사용합니다. 명령을 실행하기 전에 다음과 같은 필수 조건을 갖추고 있는지 확인합니다.

### 필수 조건

- 데이터베이스를 호스팅하는 A V12 Azure SQL 서버
- SQL Server에 대한 리소스 그룹 이름을 알고 있습니다.

### 배포 명령

이 명령은 SQL 데이터 웨어하우스의 새 데이터베이스를 배포합니다.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

이 예제에서는 서비스 목표 수준 "DW400"으로 "mynewsqldw1"이라는 새 데이터베이스를 "mywesteuroperesgp1"이라는 리소스 그룹에 있는 "sqldwserver1"이라는 서버에 배포합니다.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

이 cmdlet에 대한 필요한 매개 변수는 다음과 같습니다.

 + **RequestedServiceObjectiveName**: "DWXXX" 형식의 요청 중인 DWU의 양입니다. 현재 지원되는 값은 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000입니다.
 + **DatabaseName**: 만들려는 SQL 데이터 웨어하우스의 이름입니다.
 + **ServerName**: 만들기에 사용하는 서버의 이름입니다(V12이어야 함).
 + **ResourceGroupName**: 사용 중인 리소스 그룹입니다. 구독에서 사용 가능한 리소스 그룹을 찾으려면 Get-AzureResource를 사용합니다.
 + **Edition**: SQL 데이터 웨어하우스를 만들 버전을 "DataWarehouse"로 설정해야 합니다.

명령 참조는 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx)를 참조하세요.

매개 변수 옵션은 [데이터베이스 만들기(Azure SQL 데이터 웨어하우스)](https://msdn.microsoft.com/library/mt204021.aspx)를 참조하세요.

## 다음 단계
SQL 데이터 웨어하우스에서 프로비전을 완료한 후에 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][]하는 방법을 확인할 수 있습니다.

SQL 데이터 웨어하우스를 프로그래밍 방식으로 관리하는 방법에 대한 자세한 내용은 [Powershell][] 또는 [REST API][] 설명서를 확인하세요.



<!--Image references-->

<!--Article references-->
[마이그레이션]: ./sql-data-warehouse-overview-migrate.md
[개발]: ./sql-data-warehouse-overview-develop/.md
[샘플 데이터를 로드]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Azure PowerShell 설치 및 구성하는 방법]: ./powershell-install-configure.md

<!----HONumber=AcomDC_0309_2016-->