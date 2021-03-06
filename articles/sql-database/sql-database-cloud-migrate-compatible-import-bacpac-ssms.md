<properties
   pageTitle="SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 배포, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# SSMS를 사용하여 BACPAC에서 SQL 데이터베이스로 가져오기

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

이 문서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 SQL 데이터베이스로 가져오는 방법을 보여 줍니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 논리 인스턴스를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 최신 버전의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

	 >[AZURE.IMPORTANT]Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. BACPAC가 생성되면 Azure SQL 데이터베이스 서버에 연결하고 **Databases** 폴더를 마우스 오른쪽 단추로 클릭하고 **데이터 계층 응용 프로그램 가져오기...**를 클릭합니다.

    ![데이터 계층 응용 프로그램 메뉴 항목 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	가져오기 마법사에서 방금 내보낸 BACPAC 파일을 선택하여 Azure SQL 데이터베이스에 새 데이터베이스를 만듭니다.

    ![설정 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Azure SQL DB에서 데이터베이스에 대해 **새 데이터베이스 이름**을 입력하고 **Microsoft Azure SQL 데이터베이스 버전**(서비스 계층), **최대 데이터베이스 크기**, **서비스 목표**(성능 수준)를 입력합니다.

    ![데이터베이스 설정](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	**다음**을 클릭한 다음 **마침**을 클릭하여 BACPAC 파일을 Azure SQL 데이터베이스 서버의 새 데이터베이스로 가져옵니다.

7. 개체 탐색기를 사용하여 Azure SQL 데이터베이스 서버에서 마이그레이션된 데이터베이스에 연결합니다.

8.	Azure 포털을 사용하여 데이터베이스와 해당 속성을 봅니다.

<!---HONumber=AcomDC_1223_2015-->