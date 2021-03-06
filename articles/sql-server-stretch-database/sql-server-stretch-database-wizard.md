<properties
	pageTitle="스트레치에 데이터베이스 사용 마법사 실행 | Microsoft Azure"
	description="스트레치에 데이터베이스 사용 마법사를 실행하여 스트레치 데이터베이스에 대해 데이터베이스를 구성하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 스트레치에 데이터베이스 사용 마법사 실행

스트레치 데이터베이스에 대해 데이터베이스를 구성하려면 스트레치에 데이터베이스 사용 마법사를 실행합니다. 이 항목에서는 마법사에서 입력해야 하는 정보 및 선택해야 하는 항목을 설명합니다.

스트레치 데이터베이스에 대한 자세한 내용은 [스트레치 데이터베이스](sql-server-stretch-database-overview.md)를 참조하세요.

## 마법사 시작

1.  SQL Server Management Studio의 개체 탐색기에서 스트레치를 사용하도록 설정할 데이터베이스를 선택합니다.

2.  마우스 오른쪽 단추를 클릭하고 **작업**, **스트레치**, **사용**을 차례로 선택하여 마법사를 시작합니다.

## <a name="Intro"></a>소개
마법사의 용도 및 필수 조건을 검토합니다.

## <a name="Tables"></a>테이블 선택
스트레치에 사용할 테이블을 선택합니다.

|열|설명|
|----------|---------------|
|(제목 없음)|선택한 테이블에서 스트레치를 사용하려면 이 열의 확인란을 선택합니다.|
|**Name**|테이블의 열 이름을 지정합니다.|
|(제목 없음)|이 열의 기호는 일반적으로 차단 문제로 인해 선택한 테이블에서 스트레치를 사용할 수 없음을 나타냅니다. 테이블에서 지원되지 않는 데이터 형식을 사용하기 때문일 수 있습니다. 기호 위에 마우스를 두면 도구 설명에 자세한 내용이 표시됩니다. 자세한 내용은 [스트레치 데이터베이스에 대한 노출 영역 제한 사항 및 차단 문제](sql-server-stretch-database-limitations.md)를 참조하세요.|
|**확대**|테이블이 이미 사용하도록 설정되었는지 여부를 나타냅니다.|
|**행**|테이블의 행 수를 지정합니다.|
|**크기(KB)**|테이블의 크기를 KB로 지정합니다.|
|**마이그레이션**|CTP 3.1~RC0의 경우 마법사를 사용하여 전체 테이블을 마이그레이션할 수만 있습니다. 기록 데이터와 현재 데이터를 모두 포함하는 테이블에서 마이그레이션할 행을 선택하는 조건자를 지정하려면 ALTER TABLE 문을 실행하여 마법사를 종료한 후 조건자를 지정합니다. 자세한 내용은 [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md) 또는 [ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)을 참조하세요.|

## <a name="Configure"></a>Azure 배포 구성

1.  Microsoft 계정을 사용하여 Microsoft Azure에 로그인합니다.

2.  스트레치 데이터베이스에 사용할 Azure 구독을 선택합니다.

3.  Azure 지역을 선택합니다. 새 서버를 만드는 경우 이 지역에 서버가 만들어집니다.

    대기 시간을 최소화하려면 SQL Server가 있는 Azure 지역을 선택합니다. 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

4.  기존 서버를 사용할지 또는 새 Azure 서버를 만들지 지정합니다.

    SQL Server의 Active Directory가 Azure Active Directory와 페더레이션된 경우 선택적으로 SQL Server에 대해 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신할 수 있습니다. 이 옵션에 대한 요구 사항은 [ALTER DATABASE SET 옵션(Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)을 참조하세요.

    -   **기존 서버**

        1.  기존 Azure 서버의 이름을 선택하거나 입력합니다.

        2.  인증 방법을 선택합니다.

            -   **SQL Server 인증**을 선택한 경우 새 로그인 및 암호를 만듭니다.

            -   SQL Server에 대해 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신하려면 **Active Directory 통합 인증**을 선택합니다.

    -   **새 서버 만들기**

        1.  서버 관리자의 로그인 및 암호를 만듭니다.

        2.  필요에 따라 SQL Server에 대해 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신할 수 있습니다.

## <a name="Credentials"></a>보안 자격 증명
데이터베이스 마스터 키를 만들 강력한 암호를 입력하거나, 데이터베이스 마스터 키가 이미 있는 경우 해당 암호를 입력합니다.

스트레치 데이터베이스가 원격 데이터베이스에 연결하는 데 사용하는 자격 증명을 보호하려면 데이터베이스 마스터 키가 있어야 합니다.

데이터베이스 마스터 키에 대한 자세한 내용은 [CREATE MASTER KEY(Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 및 [데이터베이스 마스터 키 만들기](https://msdn.microsoft.com/library/aa337551.aspx)를 참조하세요. 마법사에서 만드는 자격 증명에 대한 자세한 내용은 [CREATE DATABASE SCOPED CREDENTIAL(Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)을 참조하세요.

## <a name="Network"></a>IP 주소 선택
SQL Server의 공용 IP 주소를 사용하거나 IP 주소 범위를 입력하여 SQL Server에서 원격 Azure 서버와 통신할 수 있도록 해주는 Azure 방화벽 규칙을 만듭니다.

## <a name="Summary"></a>요약
입력한 값과 마법사에서 선택한 옵션을 검토합니다. 그런 다음 **마침**을 선택하여 스트레치를 사용하도록 설정합니다.

## <a name="Results"></a>결과
결과를 검토합니다.

필요에 따라 **모니터**를 선택하여 스트레치 데이터베이스 모니터에서 데이터 마이그레이션 상태 모니터링을 시작할 수 있습니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

## <a name="KnownIssues"></a>마법사 문제 해결
**스트레치 데이터베이스 마법사에 실패했습니다.** 스트레치 데이터베이스가 아직 서버 수준에서 사용하도록 설정되지 않은 경우 이를 사용하도록 설정하기 위해 시스템 관리자 권한 없이 마법사를 실행하면 마법사에 실패합니다. 시스템 관리자에게 로컬 서버 인스턴스에서 스트레치 데이터베이스를 사용하도록 설정해 달라고 요청한 다음 마법사를 다시 실행합니다. 자세한 내용은 [필수 조건: 서버에서 스트레치 데이터베이스를 사용하도록 설정할 수 있는 권한](sql-server-stretch-database-enable-database.md#EnableTSQLServer)을 참조하세요.

## 다음 단계
스트레치 데이터베이스에 추가 테이블 사용 데이터 마이그레이션 모니터링 및 스트레치 지원 데이터베이스 및 테이블 관리

-   [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md) - 추가 테이블 사용

-   [스트레치 데이터베이스 모니터링](sql-server-stretch-database-monitor.md) - 데이터 마이그레이션 상태 확인

-   [스트레치 데이터베이스 일시 중지 및 다시 시작](sql-server-stretch-database-pause.md)

-   [스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

-   [스트레치 지원 데이터베이스 백업 및 복원](sql-server-stretch-database-backup.md)

## 참고 항목
[데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-database.md) [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->