<properties
   pageTitle="Azure SQL 데이터베이스 T-SQL에서 지원되지 않는 기능 | Microsoft Azure"
   description="Azure SQL 데이터베이스에서 완전히 지원되지 않는 TRANSACT-SQL 문"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/18/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL 데이터베이스 TRANSACT-SQL의 차이점


응용 프로그램에서 의존하는 TRANSACT-SQL 기능은 대부분 Microsoft SQL Server와 Azure SQL 데이터베이스 모두에서 지원됩니다. 응용 프로그램에 대해 지원되는 기능의 일부 목록은 다음과 같습니다.

- 데이터 형식
- 연산자
- 문자열, 산술, 논리 및 커서 함수

그러나 Azure SQL 데이터베이스는 **마스터** 데이터베이스에 대한 종속성에서 기능을 격리하도록 설계되어 있습니다. 따라서 많은 서버 수준 활동이 SQL 데이터베이스에 적합하지 않으며 지원되지 않습니다. 이 항목에서는 SQL 데이터베이스에서 완전히 지원되지 않는 기능을 자세히 설명합니다.

또한 SQL Server에서 사용되지 않는 기능은 일반적으로 SQL 데이터베이스에서도 지원되지 않습니다.

## SQL 데이터베이스 V12로 업그레이드

이 항목에서는 무료 SQL 데이터베이스 V12로 업그레이드할 경우 SQL 데이터베이스에서 사용할 수 있는 기능에 대해 설명합니다. V12에 대한 자세한 내용은 [SQL 데이터베이스 V12 새로운 기능](sql-database-v12-whats-new.md)을 참조하세요. SQL 데이터베이스 V12에서는 성능 및 관리 효율성이 향상되고 추가 기능이 지원됩니다. 추가된 기능은 완전히 지원되는 기능 및 부분적으로 지원되는 기능으로 구분되어 아래에 나열되어 있습니다.

## SQL 데이터베이스 V12에서 부분적으로 지원되는 기능

SQL 데이터베이스 V12에서는 해당 SQL Server 2016 TRANSACT-SQL 문에 있는 인수가 일부는 지원되고 일부는 지원되지 않습니다. 예를 들어 CREATE PROCEDURE 문은 사용할 수 있지만 CREATE PROCEDURE의 WITH ENCRYPTION 옵션은 사용할 수 없습니다. 각 문의 지원되는 영역에 대한 자세한 내용은 연결된 구문 항목을 참조하세요.

- 데이터베이스: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV는 사용할 수 있는 기능에 대해 일반 공급됩니다.
- 함수: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- 로그인: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- 저장 프로시저: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- 테이블: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- 형식(사용자 지정): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- 사용자: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- 보기: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## SQL 데이터베이스에서 지원되지 않는 기능

- 시스템 개체의 데이터 정렬
- 연결 관련: 끝점 문, ORIGINAL\_DB\_NAME. 로그인 또는 포함된 데이터베이스 사용자에 대해 Windows 인증을 사용할 수 없습니다.
- 세 개 또는 네 개의 부분으로 된 이름을 사용하여 데이터베이스 쿼리를 교차합니다. (읽기 전용 데이터베이스 간 쿼리는 [탄력적 데이터베이스 쿼리](sql-database-elastic-query-overview.md)를 사용하여 지원됩니다.)
- 데이터베이스 간 소유권 체인, TRUSTWORTHY 설정
- 데이터 수집기
- 데이터베이스 다이어그램
- 데이터베이스 메일
- DATABASEPROPERTY(대신 DATABASEPROPERTYEX 사용)
- EXECUTE AS 로그인
- 암호화: 확장 가능 키 관리
- 이벤트: 이벤트, 이벤트 알림, 쿼리 알림
- 데이터베이스 파일 배치, 크기 및 Microsoft Azure에서 자동으로 관리되는 데이터베이스 파일과 관련된 기능
- Microsoft Azure 계정을 통해 관리되는 고가용성과 관련된 기능: 백업, 복원, AlwaysOn, 데이터베이스 미러링, 로그 전달, 복구 모드. 자세한 내용은 Azure SQL 데이터베이스 백업 및 복원을 참조하세요.
- 로그 판독기를 사용하는 기능: 복제, 데이터 캡처 변경
- SQL Server 에이전트 또는 MSDB 데이터베이스를 사용하는 기능: 작업, 경고, 운영자, 정책 기반 관리, 데이터베이스 메일, 중앙 관리 서버
- FILESTREAM
- 함수: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
- 전역 임시 테이블
- 하드웨어 관련 서버 설정: 메모리, 작업자 스레드 수, CPU 선호도, 추적 플래그 등. 대신 서비스 수준을 사용합니다.
- HAS\_DBACCESS
- KILL STATS JOB
- 연결된 서버, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, 3개 및 4개 부분으로 이루어진 이름
- 마스터/대상 서버
- .NET Framework [CLR과 SQL Server 통합](http://msdn.microsoft.com/library/ms254963.aspx)
- 리소스 관리자
- 의미 체계 검색
- 서버 자격 증명
- 서버 수준 항목: 서버 역할, IS\_SRVROLEMEMBER, sys.login\_token. 서버 수준 사용 권한은 사용할 수 없지만 일부는 데이터베이스 수준 사용 권한으로 대체됩니다. 일부 서버 수준 DMV는 사용할 수 없지만 일부는 데이터베이스 수준 DMV로 대체됩니다.
- 서버가 없는 express: localdb, 사용자 인스턴스
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure 옵션 및 RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server 감사(대신 SQL 데이터베이스 감사 사용)
- SQL Server Profiler
- SQL Server 추적
- 추적 플래그
- TRANSACT-SQL 디버깅
- 트리거: 서버 범위 또는 로그온 트리거
- USE 문


## 전체 TRANSACT-SQL 참조

TRANSACT-SQL 문법, 사용법 및 예제에 대한 자세한 내용은 SQL Server 온라인 설명서의 [TRANSACT-SQL 참조(데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx)를 참조하세요.

### '적용 대상' 태그 정보

Transact-SQL 참조에는 현재까지 SQL Server 버전 2008과 관련된 항목이 포함되어 있습니다. 항목 제목 아래에는 일반적으로 SQL Server 버전과 다른 제품 이름이 나열된 "적용 대상" 줄이 있습니다. "적용 대상" 태그에는 종종 Azure SQL 데이터베이스도 나열됩니다. "적용 대상"에 Azure SQL 데이터베이스가 나열되지 않은 경우 항목 콘텐츠는 Azure SQL 데이터베이스에 적용되지 않습니다. 경우에 따라 "적용 대상" 줄에 여러 제품이 나열되고, 각 제품에 항목이 해당 제품에 적용되는지 여부를 나타내는 작은 아이콘이 있을 수 있습니다.

 예를 들어 가용성 그룹은 SQL Server 2012에서 도입되었습니다. **가용성 그룹 만들기** 항목에는 SQL Server 2008, SQL Server 2008 R2 또는 Azure SQL 데이터베이스에 적용되지 않으므로 **SQL Server(SQL Server 2012부터 현재 버전까지)**에 적용된다고 표시됩니다.

경우에 따라 항목의 일반 제목이 제품에 사용될 수 있지만 제품 간에 약간의 차이가 있습니다. 차이점은 항목의 중간점에 적절히 표시됩니다.

<!---HONumber=AcomDC_0224_2016-->