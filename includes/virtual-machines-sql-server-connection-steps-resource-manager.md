### 공용 IP 주소에 대한 DNS 레이블 구성

인터넷에서 SQL Server 데이터베이스 엔진에 연결하려면 먼저 공용 IP 주소에 대한 DNS 레이블을 구성합니다. 동일한 가상 네트워크 내에서 또는 로컬로 SQL Server 인스턴스에 연결하려는 경우 이 단계가 필요하지 않습니다.

DNS 레이블을 만들려면 먼저 포털에서 **가상 컴퓨터**를 선택합니다. SQL Server VM을 선택하여 속성을 가져옵니다.

1. 가상 컴퓨터 블레이드에서 **공용 IP 주소**를 선택합니다.

	![공용 IP 주소](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. 공용 IP 주소에 대한 속성에서 **구성**을 확장합니다.

3. DNS 레이블 이름을 입력합니다. IP 주소로 직접 연결하는 대신 이름으로 SQL Server VM에 연결하는 데 사용할 수 있는 A 레코드입니다.

	![dns 레이블](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### 다른 컴퓨터에서 데이터베이스 엔진에 연결
 
1. 인터넷에 연결된 컴퓨터에서 SSMS(SQL Server Management Studio)를 엽니다.

2. **서버에 연결** 또는 **데이터베이스 엔진에 연결** 대화 상자의 **서버 이름** 상자에 가상 컴퓨터의 DNS 이름(이전 작업에서 확인됨)을 입력합니다.

3. **인증** 상자에 **SQL Server 인증**을 선택합니다.

5. **로그인** 상자에 올바른 SQL 로그인 이름을 입력합니다.

6. **암호** 상자에 로그인 암호를 입력합니다.

7. **Connect**를 클릭합니다.

	![ssms 연결](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!---HONumber=AcomDC_0107_2016-->