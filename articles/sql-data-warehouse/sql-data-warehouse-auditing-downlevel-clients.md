<properties 
   pageTitle="데이터 감사에 대한 SQL 데이터 웨어하우스 하위 수준 클라이언트 지원 | Microsoft Azure" 
   description="데이터 감사에 대한 SQL 데이터 웨어하우스 하위 수준 클라이언트 지원에 대해 알아보기" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-database" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/07/2016" 
   ms.author="mausher;barbkess;sonyama"/>
 
# SQL 데이터 웨어하우스 - 감사 및 동적 데이터 마스킹에 대한 하위 수준 클라이언트 지원 


[감사](sql-data-warehouse-auditing-get-started.md)는 TDS 리디렉션을 지원하는 SQL 클라이언트와 함께 작동합니다.

TDS 7.4를 구현하는 모든 클라이언트는 리디렉션도 지원해야 합니다. 이에 대한 예외에는 리디렉션 기능이 완전히 지원되지 않는 JDBC 4.0 및 리디렉션이 구현되지 않은 Node.JS용 Tedious가 포함됩니다.

TDS 버전 7.3 이하를 지원하는 "하위 클라이언트"의 경우, 연결 문자열에서 서버 FQDN을 수정해야 합니다.

연결 문자열에서 원래 서버 FQDN: <*서버 이름*>.database.windows.net

연결 문자열에서 수정된 서버 FQDN: <*서버 이름*>.database.**secure**.windows.net

"하위 클라이언트"의 일부 목록에는 다음이 포함됩니다.

- .NET 4.0 이하
- ODBC 10.0 이하
- JDBC(JDBC는 TDS 7.4를 지원하지만 TDS 리디렉션 기능은 완전히 지원되지 않음)
- Tedious(Node.JS용)

**주석:** 위의 서버 FDQN 수정은 각 데이터베이스에서 구성 단계에 대한 요구 없이 SQL 서버 수준 감사 정책의 적용에도 유용할 수 있습니다.

 

<!---HONumber=AcomDC_0114_2016-->