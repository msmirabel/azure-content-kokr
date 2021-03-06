<properties
	pageTitle="스트레치 데이터베이스 개요 | Microsoft Azure"
	description="스트레치 데이터베이스는 기록 데이터를 투명하고 안전하게 Microsoft Azure 클라우드로 마이그레이션하는 방법에 대해 알아봅니다."
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

# 스트레치 데이터베이스 개요

스트레치 데이터베이스는 기록 데이터를 투명하고 안전하게 Microsoft Azure 클라우드로 마이그레이션합니다.

## 스트레치 데이터베이스의 장점은 무엇입니까?
스트레치 데이터베이스는 다음과 같은 이점을 제공합니다.

**콜드 데이터에 비용 효율적인 가용성을 제공** SQL Server 스트레치 데이터베이스를 통해 콜드 및 웜 트랜잭션 데이터를 SQL Server에서 Microsoft Azure로 동적으로 확장합니다. 일반적인 콜드 데이터 저장소와 달리 데이터가 항상 온라인 상태이며 쿼리할 수 있습니다. 고객 주문 내역과 같은 큰 테이블에 대한 뱅크를 차단하지 않고 긴 데이터 보존 일정을 제공할 수 있습니다. 값비싼 온-프레미스 저장소를 확장하는 대신 저렴한 비용의 Azure의 이점을 활용합니다. 가격 책정 계층을 선택하고 Azure 포털에서 설정을 구성하여 가격 및 데이터 액세스 속도에 대한 제어를 관리합니다. 필요한 만큼 규모를 확장하거나 축합니다. 자세한 내용은 [SQL 데이터베이스 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/) 페이지를 참조하세요.

**쿼리 또는 응용 프로그램 변경 불필요** 온-프레미스이든 클라우드로 확장이든 관계 없이 원활하게 SQL Server 데이터에 액세스합니다. 데이터를 저장하고 SQL Server가 백그라운드에서 데이터 이동을 처리하는 위치를 결정하는 정책을 설정합니다. 전체 테이블은 항상 온라인 상태이며 쿼리할 수 있습니다. 그리고 스트레치 데이터베이스는 기존 쿼리 또는 응용 프로그램에 변경할 필요가 없습니다. 데이터의 위치는 응용 프로그램에 대해 완전히 투명합니다.

**온-프레미스 데이터 유지 관리 간소화** 온-프레미스 유지 관리와 데이터의 저장소를 줄입니다. 데이터의 클라우드 부분에 대한 백업이 자동으로 실행됩니다. 온-프레미스 데이터에 대한 백업을 더 빠르게 실행하고 유지 관리 기간 내에 완료합니다. 온-프레미스 저장소 요구량이 크게 감소합니다. Azure 저장소는 온-프레미스 SSD에 추가하는 비용의 80%까지 절약할 수 있습니다.

**마이그레이션 중에도 데이터 보호** 가장 중요한 응용 프로그램을 클라우드로 안전하게 확장하기 때문에 안심할 수 있습니다. SQL Server의 상시 암호화는 데이터 이동 시 암호화를 제공합니다. 행 수준 보안(RLS) 및 기타 고급 SQL Server 보안 기능 또한 스트레치 데이터베이스와 함께 데이터를 보호합니다.

## 스트레치 데이터베이스의 기능은 무엇입니까?
SQL Server 인스턴스, 데이터베이스 및 하나 이상의 테이블에 대해 스트레치 데이터베이스를 사용하고 나면 기록 데이터를 Azure로 자동으로 마이그레이션하기 시작합니다.

-   기록 데이터를 별도 테이블에 저장하는 경우 전체 테이블을 마이그레이션할 수 있습니다.

-   테이블이 기록 및 현재 데이터를 포함하는 경우 필터 조건자를 지정하여 마이그레이션할 행을 선택할 수 있습니다.

스트레치 데이터베이스는 마이그레이션 중에 오류가 발생할 경우 데이터가 손실되지 않도록 합니다. 또한 마이그레이션하는 동안 발생할 수 있는 연결 문제를 처리하기 위한 재시도 논리도 있습니다. 동적 관리 뷰를 통해 마이그레이션 상태를 확인합니다.

로컬 서버에서 문제를 해결하거나 사용 가능한 네트워크 대역폭을 최대화하기 위해 데이터 마이그레이션을 일시 중지시킬 수 있습니다.

기존 쿼리 및 클라이언트 앱을 변경할 필요가 없습니다. 데이터 마이그레이션 중에도 로컬 및 원격 데이터에 원활하게 계속 액세스할 수 있습니다. 원격 쿼리에 대한 대기 시간이 다소 있지만 기록 데이터를 쿼리할 때만 이 대기 시간이 발생합니다.

![스트레치 데이터베이스 개요][StretchOverviewImage1]

## 스트레치 데이터베이스는 어떤 도움을 줍니까?
다음 문을 만들 수 있는 경우 스트레치 데이터베이스가 요구 사항을 충족하고 문제를 해결하는 데 도움이 될 수 있습니다.

|의사 결정자인 경우|DBA인 경우|
|------------------------------|-------------------|
|장시간 트랜잭션 데이터를 유지해야 합니다.|테이블 크기가 통제 수준을 벗어나고 있습니다.|
|종종 기록 데이터를 쿼리할 해야 합니다.|사용자들이 기록 데이터에 액세스하지만 거의 사용하지는 않는다고 합니다.|
|이전 앱을 비롯한 앱이 있지만, 업데이트하고 싶지 않습니다.|저장소를 계속 구입하고 추가해야 합니다.|
|저장소에 대한 비용을 절약하고 싶습니다.|SLA 내의 대규모 테이블을 백업하거나 복원할 수 없습니다.|

## 어떤 종류의 데이터베이스 및 테이블이 스트레치 데이터베이스가 될 수 있습니까?
스트레치 데이터베이스는 일반적으로 대용량의 기록 데이터가 적은 수의 테이블에 저장되는 트랜잭션 데이터베이스를 위한 것입니다. 이들 테이블에는 수십억 개 이상의 행이 있을 수 있습니다.

SQL Server 2016의 임시 테이블 기능을 사용하는 경우, 연결된 기록 테이블의 일부나 전부를 Azure의 비용 효율적인 저장소로 마이그레이션하기 위해 스트레치 데이터베이스를 사용합니다. 자세한 내용은 [시스템 버전 임시 테이블에서 기록 데이터 보존 관리](https://msdn.microsoft.com/library/mt637341.aspx)를 참조하세요.

SQL Server 2016 업그레이드 관리자의 기능인 스트레치 데이터베이스 관리자를 사용하여 스트레치 데이터베이스용 데이터베이스 및 테이블을 식별합니다. 자세한 내용은 [스트레치 데이터베이스용 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)을 참조하세요. 잠재적인 차단 문제에 대한 자세한 내용은 [스트레치 데이터베이스에 대한 영역 제한 사항 및 차단 문제](sql-server-stretch-database-limitations.md)를 참조하세요.

## <a name="FAQ"></a>스트레치 데이터베이스에 대한 질문과 대답
**스트레치 데이터베이스는 &lt;SQL Server 기능 이름&gt;과 함께 작동합니까?**
-   스트레치에 테이블을 부적합하게 만드는 SQL Server 기능 목록은 [스트레치 데이터베이스에 대한 영역 제한 사항 및 차단 문제](sql-server-stretch-database-limitations.md)를 참조하세요.

-   필요에 따라 SQL Server 2016 업그레이드 관리자를 다운로드하고 스트레치 데이터베이스 관리자를 실행하여 스트레치 데이터베이스로 사용할 수 있는 데이트베이스와 테이블을 식별합니다. 자세한 내용은 [스트레치 데이터베이스용 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)을 참조하세요.

**또 다른 로컬 SQL Server 인스턴스를 스트레치 데이터베이스의 대상을 사용할 수 있습니까?** 아니요. 스트레치 데이터베이스는 원격 끝점으로서 또 다른 로컬 SQL Server 인스턴스를 지원하지 않습니다.

**스트레치를 사용하지 않고 마이그레이션된 데이터를 로컬 테이블로 다시 이동시킬 수 있습니까?** 예. 자세한 내용은 [스트레치 데이터베이스를 사용하지 않고 원격 데이터 다시 가져오기](sql-server-stretch-database-disable.md)를 참조하세요.

## 용어
**로컬 데이터베이스** 온-프레미스 SQL Server 데이터베이스입니다.

**원격 끝점** Microsoft Azure에 있으며 데이터베이스의 원격 데이터를 포함하는 위치입니다.

**로컬 데이터** 데이터베이스에 있는 테이블의 스트레치 데이터베이스 구성에 따라 Azure로 이동하지 않을 스트레치 데이터베이스를 사용하는 데이터베이스의 데이터입니다.

**적격 데이터** 데이터베이스에 있는 테이블의 스트레치 데이터베이스 구성에 따라 Azure로 아직 이동하지 않았지만, 곧 이동될 스트레치 데이터베이스를 사용하는 데이터베이스의 데이터입니다.

**원격 데이터** Azure로 이미 이동한 스트레치 데이터베이스를 사용하는 데이터베이스의 데이터입니다.

## 아키텍처
스트레치 데이터베이스는 Microsoft Azure의 리소스를 활용하여 보관 데이터 저장소 및 쿼리 처리의 부하를 줄입니다.

데이터베이스에서 스트레치 데이터베이스를 사용하면 온-프레미스 SQL Server에 보안 연결 서버 정의가 만들어집니다. 이 연결 서버 정의는 대상으로서 원격 끝점을 가집니다. 데이터베이스의 테이블에서 스트레치 데이터베이스를 사용하면 원격 리소스를 프로비전하고 마이그레이션을 사용하는 경우 해당 데이터를 마이그레이션합니다.

스트레치 데이터베이스를 사용한 테이블에 대한 쿼리는 로컬 데이터베이스와의 원격 끝점에 대해 자동으로 실행됩니다. 스트레치 데이터베이스는 쿼리를 다시 작성하여 원격 데이터에 대한 쿼리를 실행하기 위해 Azure의 처리 능력을 활용합니다. 이러한 재작성을 새 쿼리 계획의 "원격 쿼리" 연산자로 볼 수 있습니다.

![스트레치 데이터베이스 아키텍처][StretchOverviewImage2]

## 보안 및 권한

### SQL Server 인스턴스에 대해 스트레치 데이터베이스의 사용 여부 지정
스트레치 데이터베이스에 대해 데이터베이스를 구성하려면 먼저 sp\_configure를 사용하는 "원격 데이터 보관" 인스턴스 수준 구성 옵션을 변경해야 합니다. 이 작업에는 sysadmin 또는 serveradmin 권한이 필요합니다. 이 옵션을 사용하면 스트레치 데이터베이스에 대한 데이터베이스를 구성하고, 데이터를 마이그레이션하며, 원격 끝점에서 데이터를 쿼리할 수 있습니다.

### 데이터베이스 또는 테이블에 대해 스트레치 데이터베이스의 사용 여부 지정
스트레치 데이터베이스에 대해 데이터베이스를 구성하려면 CONTROL DATABASE 권한이 있어야 합니다. 또한 원격 끝점에 대한 관리자 자격 증명을 제공해야 합니다.

스트레치 데이터베이스의 테이블을 구성하려면, 테이블에 ALTER 권한이 있어야 하고 데이터베이스가 스트레치 데이터베이스에 대해 이미 구성되어 있어야 합니다.

### 데이터 액세스
시스템 프로세스만이 스트레치 데이터베이스 뒤에 있는 연결 서버 정의를 액세스할 수 있습니다. 사용자 로그인은 원격 끝점에 대한 연결 서버 정의를 통해 쿼리를 실행할 수 없습니다.

스트레치 데이터베이스는 기존 데이터베이스의 권한 모델을 변경하지 않습니다. 사용자 로그인은 로컬 데이터베이스를 통해 스트레치 데이터베이스를 사용하여 테이블의 데이터를 쿼리할 수 있습니다. 로컬 데이터베이스는 다른 개체에 대해서와 동일한 방법으로 사용자가 시작하는 모든 작업에 대해 권한 검사를 수행합니다. 스트레치 데이터베이스를 사용하여 테이블 액세스 권한이 부여된 경우, 데이터가 실제로 상주하는 위치에 관계 없이 권한을 부여 받은 모든 내용에 액세스할 수 있습니다.

![스트레치 데이터베이스 데이터 액세스 모델][StretchOverviewImage3]

## 스트레치 데이터베이스 시험 사용
**AdventureWorks 샘플 데이터베이스를 통해 스트레치 데이터베이스를 시험 사용합니다.** AdventureWorks 샘플 데이터베이스를 얻으려면 [여기](https://www.microsoft.com/download/details.aspx?id=49502)에서 최소한의 데이터베이스 파일과 샘플 및 스크립트 파일을 다운로드합니다. 샘플 데이터베이스를 SQL Server 2016의 인스턴스에 복원한 후, 샘플 파일의 압축을 풀고 스트레치 DB 폴더에서 스트레치 DB 샘플 파일을 엽니다. 이 파일에서 스크립트를 실행하여 스트레치 데이터베이스 사용 전후 데이터가 사용하는 공간을 확인하고, 데이터 마이그레이션의 진행 상황을 추적하며, 그리고 데이터 마이그레이션 도중 및 이후에도 계속 기존 데이터를 쿼리하고 새 데이터를 삽입할 수 있는지 확인합니다.

## 다음 단계
**스트레치 데이터베이스의 후보인 데이터베이스 및 테이블을 식별합니다.** SQL Server 2016 업그레이드 관리자를 다운로드하고 스트레치 데이터베이스 관리자를 실행하여 스트레치 데이터베이스의 후보인 데이터베이스 및 테이블을 식별합니다. 스트레치 데이터베이스 관리자는 차단 문제도 식별합니다. 자세한 내용은 [스트레치 데이터베이스용 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)을 참조하세요.

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0302_2016-->