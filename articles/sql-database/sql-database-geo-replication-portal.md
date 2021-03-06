<properties 
    pageTitle="Azure 포털로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성 | Microsoft Azure" 
    description="Azure 포털을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="02/23/2016"
    ms.author="sstein"/>

# Azure 포털로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


이 문서에서는 [Azure 포털](http://portal.azure.com)을 사용하여 SQL 데이터베이스에 대한 지역에서 복제를 구성하는 방법을 보여 줍니다.

지역에서 복제를 사용하면 서로 다른 데이터 센터 위치(지역)에 최대 4개의 복제본(보조) 데이터베이스를 생성할 수 있습니다. 보조 데이터베이스를 데이터 센터 정전 또는 주 데이터베이스에 연결하지 못하는 경우 사용할 수 있습니다.

지역에서 복제는 Standard 및 Premium 데이터베이스에서만 사용할 수 있습니다.

Standard 데이터베이스는 읽을 수 없는 보조를 하나를 가질 수 있으며 권장되는 영역을 사용해야 합니다. Premium 데이터베이스는 사용 가능한 지역 중에서 최대 4개의 읽기 가능한 보조를 가질 수 있습니다.


지역에서 복제를 구성하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스 - 다른 지리적 영역으로 복제하려는 주 데이터베이스입니다.



## 보조 데이터베이스 추가

다음 단계에서는 지역에서 복제 파트너 관계에 새 보조 데이터베이스를 만듭니다.

보조를 추가하려면 구독 소유자 또는 공동 소유자여야 합니다.

보조 데이터베이스는 주 데이터베이스와 동일한 이름을 포함하며 기본적으로 동일한 수준의 서비스입니다. 보조 데이터베이스는 읽을 수 있거나(Premium 계층만) 읽을 수 없을 수 있으며 단일 데이터베이스 또는 탄력적 데이터베이스가 될 수 있습니다. 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요. 보조가 만들어지고 시드된 후 데이터는 주 데이터베이스에서 새로운 보조 데이터베이스로 복제되기 시작합니다.

> [AZURE.NOTE] 파트너 데이터베이스가 이미 있는 경우(예: 이전 지역에서 복제 관계를 종료한 결과) 명령이 실패합니다.




### 보조 추가

1. [Azure 포털](http://portal.azure.com)에서 지역에서 복제를 위해 설치하려는 데이터베이스를 찾습니다.
2. SQL 데이터베이스 블레이드에서 **모든 설정** > **지역에서 복제**를 선택합니다.
3. 보조 데이터베이스를 만들 지역을 선택합니다. Premium 데이터베이스는 보조로 어떤 지역이든 사용할 수 있지만 Standard 데이터베이스는 권장되는 지역을 사용해야 합니다.


    ![보조 추가][1]


4. **보조 유형**(**Readable** 또는 **Non-readable**)을 구성합니다. Premium 데이터베이스만 읽기 가능한 보조를 포함할 수 있으며 Standard 보조 데이터베이스는 **Non-readable**로만 설정할 수 있습니다.
5. 보조 데이터베이스를 위한 서버를 선택 또는 구성합니다.

    ![보조 만들기][3]

5. 필요에 따라 탄력적 데이터베이스 풀에 보조 데이터베이스를 추가할 수 있습니다.

       - **탄력적 데이터베이스 풀**을 클릭하고 대상 서버에서 안에 보조 데이터베이스를 만들 풀을 선택합니다. 이 워크플로에서는 새 풀을 만들지 않으므로 대상 서버에 풀이 이미 있어야 합니다.

6. **만들기**를 클릭하여 보조를 추가합니다.
 
6. 보조 데이터베이스가 만들어지고 시드 프로세스가 시작됩니다.
 
    ![시드][6]

7. 시드 프로세스가 완료되면 보조 데이터베이스가 해당 상태를 표시합니다(읽을 수 없음).

    ![보조 준비][9]



## 보조 데이터베이스 제거

이 작업은 보조 데이터베이스에 대한 복제를 영구적으로 종료하고 보조의 역할을 일반적인 읽기-쓰기 데이터베이스로 변경합니다. 보조 데이터베이스에 대한 연결이 끊어진 경우 명령이 성공하지만 연결이 복원된 후에야 보조는 읽기-쓰기가 됩니다.

1. [Azure 포털](http://portal.azure.com)에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. SQL 데이터베이스 블레이드에서 **모든 설정** > **지역에서 복제**를 선택합니다.
3. **보조** 목록에서 지역에서 복제 파트너 관계에서 제거할 데이터베이스를 선택합니다.
4. **복제 중지**를 클릭합니다.

    ![보조 제거][7]


5. **복제 중지**를 클릭하면 확인 창이 열리고 **예**를 클릭하여 지역에서 복제 파트너 관계에서 데이터베이스를 제거합니다(복제에 포함되지 않은 읽기-쓰기 데이터베이스로 설정).


    ![제거 확인][8]



## 장애 조치(Failover) 시작

보조 데이터베이스가 주 데이터베이스가 되도록 전환할 수 있습니다.

1. [Azure 포털](http://portal.azure.com)에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. SQL 데이터베이스 블레이드에서 **모든 설정** > **지역에서 복제**를 선택합니다.
3. **보조** 목록에서 새로운 주 데이터베이스가 될 데이터베이스를 선택합니다.
4. **장애 조치(Failover)**를 클릭합니다.

    ![장애 조치(Failover)][10]

명령은 다음 워크플로 수행합니다.

1. 일시적으로 복제가 동기 모드로 전환됩니다. 이로 인해 처리되지 않은 모든 트랜잭션이 보조 데이터베이스로 플러시됩니다. 

2. 지역에서 복제 파트너 관계에서 두 데이터베이스의 주 및 보조 역할을 전환합니다.

계획된 장애 조치(failover)의 경우 이 시퀀스는 데이터 손실이 발생하지 않음을 보장합니다. 역할이 전환되는 동안 두 데이터베이스를 모두 사용할 수 없는 (0-25초의 순서로) 짧은 기간이 있습니다. 전체 작업은 정상적인 상황에서 완료하는데 1분 미만이 걸려야 합니다.

   

## 다음 단계

- [지역에서 복제를 사용하여 비즈니스 연속성을 위한 클라우드 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)


## 추가 리소스

- [새 지역에서 복제 기능에 대한 주요 내용](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [지역에서 복제를 사용하여 비즈니스 연속성을 위한 클라우드 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0224_2016-->