<properties
	pageTitle="Windows에서 TinyTDS와 함께 Ruby를 사용하여 SQL 데이터베이스에 연결"
	description="Ruby 코드 샘플을 제공하면 Windows에서 실행하여 Azure SQL 데이터베이스에 연결할 수 있습니다."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Windows에서 Ruby를 사용하여 SQL 데이터베이스에 연결


<!--
Older Selector technique, with dynamic drop-down lists.
 [ A ZURE . I NCLUDE [s ql-database-develop-includes-selector-language-platform-depth](../../inclu des/sql-database-develop-includes-selector-language-platform-depth.m d)]
-->

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Windows 8.1을 실행 중인 Windows 컴퓨터에서 실행하여 Azure SQL 데이터베이스 데이터베이스에 연결하는 Ruby 코드 샘플을 표시합니다.

## 필수 조건

###필요한 모듈 설치

터미널을 열고 다음을 설치합니다.

**1) Ruby:** 컴퓨터에 Ruby가 없는 경우 설치합니다. 새 Ruby 사용자의 경우 Ruby 2.1.X 설치 관리자를 사용하는 것이 좋습니다. 안정적인 언어와 호환 및 업데이트되는 패키지(gem)의 광범위한 목록을 제공합니다. [Ruby 다운로드 페이지로 이동](http://rubyinstaller.org/downloads/)하여 적절한 2.1.x 설치 관리자를 다운로드합니다. 예를 들어 64비트 컴퓨터에 있는 경우 **Ruby 2.1.6(x64)** 설치 관리자를 다운로드합니다. <br/><br/>설치 관리자를 다운로드한 후 다음을 수행합니다.


- 파일을 두 번 클릭하여 설치 관리자를 시작합니다.

- 언어를 선택하고 약관에 동의합니다.

- 설치 설정 화면에서 *PATH에 Ruby 실행 파일 추가* 및 *Ruby 설치에 .rb 및.rbw 파일 연결* 옆에 있는 확인란을 둘 다 선택합니다.


**2) DevKit:** [RubyInstaller 페이지](http://rubyinstaller.org/downloads/)에서 DevKit를 다운로드합니다.

다운로드가 완료되면 다음을 수행합니다.


- 파일을 두 번 클릭합니다. 파일을 추출할 위치를 묻는 메시지가 표시됩니다.

- "..." 단추를 클릭하고 "C:\\DevKit"를 선택합니다. "새 폴더 만들기"를 클릭하여 이 폴더를 먼저 만들어야 합니다.

- "확인", "추출"을 차례로 클릭하여 파일을 추출합니다.


이제 명령 프롬프트를 열고 다음 명령을 입력합니다.

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

이제 Ruby 및 RubyGem이 완전히 작동합니다.


**3) TinyTDS:** C:\\DevKit로 이동하여 터미널에서 다음 명령을 실행합니다. 그러면 컴퓨터에 TinyTDS가 설치됩니다.

	gem inst tiny_tds --pre

### SQL 데이터베이스

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.


## 1단계: 연결 정보 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 2단계: 연결

[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 함수는 SQL 데이터베이스에 연결하는 데 사용됩니다.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## 3단계: 쿼리 실행

다음 코드를 복사하여 빈 파일에 붙여넣습니다. 이름을 test.rb로 지정합니다. 그런 다음 명령 프롬프트에서 다음 명령을 입력하여 실행합니다.

	ruby test.rb

코드 샘플에서 [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) 함수는 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색하는 데 사용됩니다. 이 함수는 쿼리를 허용하고 결과 집합을 반환합니다. 이 결과 집합은 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)를 사용하여 반복됩니다.

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## 4단계: 행 삽입

이 예제에서는 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 문을 안전하게 실행하고, [SQL 삽입](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 취약성으로부터 응용 프로그램을 보호하는 매개 변수를 전달하며, 자동으로 생성된 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 검색하는 방법을 보여 줍니다.

TinyTDS와 Azure를 함께 사용하려면 여러 `SET` 문을 실행하여 현재 세션에서 특정 정보를 처리 하는 방법을 변경하는 것이 좋습니다. 권장되는 `SET` 문이 코드 샘플에 제공됩니다. 예를 들어 `SET ANSI_NULL_DFLT_ON`에서는 열의 Null 허용 여부 상태가 명시적으로 지정되지 않은 경우에도 Null 값을 허용하도록 새 열을 만들 수 있습니다.

Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 형식에 맞게 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 함수를 사용하여 해당 날짜/시간 형식으로 캐스팅합니다.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end

<!---HONumber=AcomDC_0107_2016-->