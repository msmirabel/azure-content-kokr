<properties
   pageTitle="SqlPackage를 사용하여 BACPAC 파일에서 SQL 데이터베이스로 가져오기"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, BACPAC 파일 가져오기, sqlpackage"
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

# SqlPackage를 사용하여 BACPAC 파일에서 SQL 데이터베이스로 가져오기

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

이 문서에서는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용하여 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 SQL 데이터베이스로 가져오는 방법을 보여 줍니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 최신 버전의 SQL Server Data Tools를 [다운로드](https://msdn.microsoft.com/library/mt204009.aspx)하여 이 유틸리티를 가져올 수도 있습니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 SQL 데이터베이스 서버를 프로비전하고 연결 정보를 알고 있으며 원본 데이터베이스가 호환됨을 확인한 경우를 가정합니다.

## SqlPackage를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기

아래 단계를 따라 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 호환되는 SQL Server 데이터베이스(또는 Azure SQL 데이터베이스)를 BACPAC 파일에서 가져옵니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 데이터베이스 서버를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 명령 프롬프트를 열고 sqlpackage.exe 명령줄 유틸리티가 들어 있는 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다.
2. 사용자 환경에 대해 다음 인수를 사용하여 다음 sqlpackage.exe 명령을 실행하세요.

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| 인수 | 설명 |
	|---|---|
	| < server_name > | 대상 서버 이름 |
	| < database_name > | 대상 데이터베이스 이름 |
	| < user_name > | 대상 서버의 사용자 이름 |
	| < password > | 사용자 암호 |
	| < source_file > | 가져올 BACPAC 파일의 파일 이름 및 위치 |

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_1223_2015-->