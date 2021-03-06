<properties 
	pageTitle="데이터를 만들어서 Blob 저장소의 Hive 테이블에 로드 | Microsoft Azure" 
	description="Hive 테이블을 만들어서 blob의 데이터를 Hive 테이블에 로드" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="hangzh;bradsev" />

 
#데이터를 만들어서 Azure blob 저장소의 Hive 테이블에 로드

## 소개
이 **문서**에서는 Hive 테이블을 만들고 Azure blob 저장소의 데이터를 로드하는 일반 Hive 쿼리를 보여 줍니다. 또한 Hive 테이블을 분할하고 ORC(Optimized Row Columnar) 형식을 사용하여 쿼리 성능을 개선하는 방법에 대한 지침도 제공됩니다.

이 **메뉴**는 CAP(Cortana 분석 프로세스) 중 데이터를 저장하고 처리할 수 있는 대상 환경으로 데이터를 수집하는 방법을 설명하는 항목에 연결됩니다.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## 필수 조건
이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.
 
* Azure 저장소 계정을 만들었습니다. 지침이 필요한 경우 [Azure 저장소 계정 만들기](../hdinsight-get-started.md#storage)를 참조하세요. 
* 사용자 지정된 Hadoop 클러스터에 HDInsight 서비스를 프로비전했습니다. 지침이 필요한 경우 [고급 분석을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md)을 참조하세요.
* 클러스터에 대한 원격 액세스를 설정하고, 로그인하고, Hadoop 명령줄 콘솔을 열었습니다. 지침이 필요한 경우 [Hadoop 클러스터의 헤드 노드에 액세스](machine-learning-data-science-customize-hadoop-cluster.md#headnode)를 참조하세요. 

## Azure Blob 저장소에 데이터 업로드
[고급 분석을 위한 Azure 가상 컴퓨터 설정](machine-learning-data-science-setup-virtual-machine.md)의 지침에 따라 Azure 가상 컴퓨터를 만드는 경우 이 스크립트 파일을 가상 컴퓨터의 *C:\\Users\\<사용자 이름>\\Documents\\Data Science Scripts* 디렉터리에 다운로드해야 합니다. 이러한 Hive 쿼리는 제출이 가능하도록 적절한 필드에서 사용자 데이터 스키마 및 Azure blob 저장소 구성을 연결하기만 하면 됩니다.

Hive 테이블의 데이터가 **압축되지 않은** 테이블 형식이고 Hadoop 클러스터에서 사용하는 저장소 계정의 기본 또는 추가 컨테이너에 데이터가 업로드된 것으로 가정합니다.

_NYC Taxi Trip Data_를 연습하고 싶다면 먼저 24개의 <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC Taxi Trip Data</a> 파일(Trip 파일 12개, Fare 파일 12개)을 다운로드하고, .csv 파일로 모든 파일의 **압축을 풀고**, [고급 분석 프로세스 및 기술을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md) 항목에 설명된 절차에 따라 작성된 기본 Azure 저장소 계정 또는 Azure 저장소 계정의 적절한 컨테이너에 해당 파일을 업로드해야 합니다. 저장소 계정의 기본 컨테이너에 .csv 파일을 업로드하는 프로세스는 이 [페이지](machine-learning-data-science-process-hive-walkthrough/#upload)에 나와 있습니다.

## <a name="submit"></a>Hive 쿼리를 제출하는 방법
다음을 사용하여 Hive 쿼리를 제출할 수 있습니다.

* 클러스터 헤드 노드의 Hadoop 명령줄
* IPython Notebook
* Hive 편집기
* Azure PowerShell 스크립트

Hive 쿼리는 SQL과 유사하므로, SQL에 익숙한 사용자에게는 <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">SQL-Hive 치트 시트</a>가 유용할 수 있습니다.

또한 Hive 쿼리를 제출할 때 Hive 쿼리에서 화면, 헤드 노드의 로컬 파일, Azure blob 등의 출력 대상을 제어할 수 있습니다.

### Hadoop 클러스터 헤드 노드에서 Hadoop 명령줄 콘솔을 통해

쿼리가 복잡한 경우 Hadoop 클러스터의 헤드 노드에서 바로 Hive 쿼리를 제출하면 일반적으로 Hive 편집기 또는 Azure PowerShell 스크립트를 사용하여 제출하는 것보다 반환 시간이 빠릅니다.

Hadoop 클러스터의 헤드 노드에 로그인하고, 헤드 노드 바탕 화면에서 Hadoop 명령줄을 열고, 명령을 입력합니다.

    cd %hive_home%\bin

사용자는 세 가지 방법으로 Hadoop 명령줄 콘솔에서 Hive 쿼리를 제출할 수 있습니다.

* Hadoop 명령줄에서 직접 제출
* .hql 파일을 사용하여 제출
* Hive 명령 콘솔에서 제출

#### Hadoop 명령줄에서 직접 Hive 쿼리 제공

사용자는 아래와 같이 명령을 실행하여

	hive -e "<your hive query>;

Hadoop 명령줄에서 직접 간단한 Hive 쿼리를 제출할 수 있습니다. 다음은 그 예제입니다. 빨간색 상자는 Hive 쿼리를 제출하는 명령을, 녹색 상자는 Hive 쿼리의 출력을 보여 줍니다.

![작업 영역 만들기](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### .hql 파일로 Hive 쿼리 제출

Hive 쿼리가 좀 더 복잡하고 줄이 여러 개인 경우 Hadoop 명령줄 또는 Hive 명령 콘솔에서 쿼리를 편집하는 방법은 실용적이지 않습니다. 대신 Hadoop 클러스터의 헤드 노드에서 텍스트 편집기를 사용하여 헤드 노드의 로컬 디렉터리에 있는 .hql 파일에 Hive 쿼리를 저장합니다. 그러면 다음과 같이 `hive` 명령에 `-f` 인수를 사용하여 .hql 파일의 Hive 쿼리를 제출할 수 있습니다.

	`hive -f "<path to the .hql file>"`


#### Hive 쿼리의 진행 상태 화면 인쇄 숨기기

기본적으로 Hadoop 명령줄 콘솔에서 Hive 쿼리가 제출되면 맵/감소 작업의 진행 상태가 화면에 인쇄됩니다. 맵/감소 작업의 진행 상태 화면 인쇄를 숨기려면 다음과 같이 명령줄에 `-S` 인수(대문자 구분)를 사용할 수 있습니다.

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Hive 명령 콘솔에서 Hive 쿼리를 제출합니다.

Hadoop 명령줄에서 `hive` 명령을 실행하여 Hive 명령 콘솔로 전환한 후 Hive 명령 콘솔의 **hive>** 프롬프트에서 Hive 쿼리를 제출하는 방법도 있습니다. 다음은 예제입니다.

![작업 영역 만들기](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

이 예제에서 두 빨간색 상자는 각각 Hive 명령 콘솔을 입력하는 데 사용된 명령과 Hive 명령 콘솔에서 제출된 Hive 쿼리를 보여 줍니다. 녹색 상자는 Hive 쿼리의 출력을 보여 줍니다.

이전 예제에서는 Hive 쿼리 결과가 화면에 바로 출력됩니다. 또한 사용자는 헤드 로드의 로컬 파일 또는 Azure blob에 출력을 작성할 수 있습니다. 그런 다음 사용자는 다른 도구를 사용하여 Hive 쿼리의 출력을 추가로 분석할 수 있습니다.

#### Hive 쿼리 결과를 로컬 파일에 출력합니다.

Hive 쿼리 결과를 헤드 노드의 로컬 디렉터리에 출력하려면 사용자는 다음과 같이 Hadoop 명령줄에서 Hive 쿼리를 제출해야 합니다.

	`hive -e "<hive query>" > <local path in the head node>`


#### Azure blob에 Hive 쿼리 결과 출력

사용자는 Hadoop 클러스터의 기본 컨테이너 내에 있는 Azure blob에 Hive 쿼리 결과를 출력할 수도 있습니다. 이렇게 하는 Hive 쿼리는 다음과 같습니다.

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

다음 예제에서 Hive 쿼리의 출력은 Hadoop 클러스터의 기본 컨테이너 내에 있는 blob 디렉터리 `queryoutputdir`에 작성됩니다. 이때 사용자는 blob 이름 없이 디렉터리 이름만 입력하면 됩니다. **wasb:///queryoutputdir/queryoutput.txt*처럼 디렉터리 이름과 Blob 이름을 모두 입력하면 오류가 발생합니다.

![작업 영역 만들기](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

Azure 저장소 탐색기 또는 그에 상응하는 도구를 사용하여 Hadoop 클러스터의 기본 컨테이너를 열면 blob 저장소에서 Hive 쿼리 출력을 볼 수 있습니다. 필터(빨간색 상자로 강조 표시됨)를 적용하여 이름에 지정된 문자가 포함된 blob만 검색할 수 있습니다.

![작업 영역 만들기](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Hive 편집기 또는 Azure PowerShell 명령을 통해

사용자는 웹 브라우저에

*https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor*

입력하여 쿼리 콘솔(Hive 편집기)을 사용할 수도 있습니다. 로그인하려면 Hadoop 클러스터 자격 증명을 입력하라는 메시지가 표시됩니다.

또는 [PowerShell을 사용하여 Hive 쿼리를 실행](../hdinsight/hdinsight-hadoop-use-hive-powershell.md)할 수 있습니다.


## <a name="create-tables"></a> Hive 데이터베이스 및 테이블 만들기

Hive 쿼리는 [Github 리포지토리](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)에서 공유되며 여기에서 다운로드할 수 있습니다.

다음은 Hive 테이블을 만드는 Hive 쿼리입니다.

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

다음은 사용자가 연결해야 하는 필드와 기타 구성에 대한 설명입니다.

- **&#60;database name>**: 사용자가 만들려고 하는 데이터베이스 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 *create database...* 쿼리를 생략할 수 있습니다. 
- **&#60;table name>**: 사용자가 지정된 데이터베이스 내에 만들려는 테이블 이름입니다. 사용자가 기본 데이터베이스를 사용하려는 경우 &#60;database name> 없이 *&#60;table name>*에서 직접 테이블을 참조할 수 있습니다.
- **&#60;field separator>**: 데이터 파일에서 Hive 테이블에 업로드할 필드를 구분하는 구분 기호입니다. 
- **&#60;line separator>**: 데이터 파일의 줄을 구분하는 구분 기호입니다. 
- **&#60;storage location>**: Hive 테이블의 데이터를 저장할 Azure 저장소 위치입니다. 사용자가 *LOCATION &#60;storage location>*을 지정하지 않으면 기본적으로 데이터베이스 및 테이블이 Hive 클러스터의 기본 컨테이너에 있는 *hive/warehouse/* 디렉터리에 저장됩니다. 사용자가 저장소 위치를 지정하려면 저장소 위치가 데이터베이스 및 테이블의 기본 컨테이너 내부에 있어야 합니다. 이 위치는 *'wasb:///&#60;directory 1>/'* 또는 *'wasb:///&#60;directory 1>/&#60;directory 2>/'* 등의 형식으로 클러스터 기본 컨테이너에 대한 상대 위치로 참조해야 합니다. 쿼리가 실행된 후 기본 컨테이너 내에 상대 디렉터리가 만들어집니다. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: 데이터 파일에 헤더 줄이 있으면 사용자는 *create table* 쿼리의 **끝**에 이 속성을 추가해야 합니다. 그렇지 않으면 헤더 줄이 테이블의 레코드로 로드됩니다. 데이터 파일에 헤더 줄이 없으면 쿼리에서 이 구성을 생략해도 됩니다. 

## <a name="load-data"></a>Hive 테이블에 데이터 로드
다음은 Hive 테이블에 데이터를 로드하는 Hive 쿼리입니다.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>**: Hive 테이블에 업로드할 Blob 파일이 HDInsight Hadoop 클러스터의 기본 컨테이너에 있으면 *&#60;path to blob data>*가 *'wasb:///&#60;directory in this container>/&#60;blob file name>'* 형식이어야 합니다. blob 파일이 HDInsight Hadoop 클러스터의 추가 컨테이너에 있을 수도 있습니다. 이 경우 *&#60;path to blob data>*는 *'wasb://&#60;container name>@&#60;storage account name>.blob.core.windows.net/&#60;blob file name>'* 형식이어야 합니다.

	>[AZURE.NOTE] Hive 테이블에 업로드할 blob 데이터가 Hadoop 클러스터에 대한 저장소 계정의 기본 또는 추가 컨테이너에 있어야 합니다. 그렇지 않으면 데이터에 액세스할 수 없기 때문에 *LOAD DATA* 쿼리가 실패합니다.


## <a name="partition-orc"></a>고급 항목: 분할된 테이블 및 ORC 형식으로 Hive 데이터 저장

데이터가 큰 경우 테이블을 분할하면 테이블의 파티션 몇 개만 검색하면 되는 쿼리의 속도가 향상됩니다. 예를 들어 웹 사이트의 로그 데이터를 날짜별로 분할하는 것이 합리적입니다.

Hive 테이블 분할 외에도 Hive 데이터를 ORC(Optimized Row Columnar) 형식으로 저장하는 방법 또한 도움이 됩니다. ORC 형식에 대한 자세한 내용은 <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ORC 파일을 사용하면 Hive에서 데이터를 읽고, 쓰고, 처리할 때 성능 향상</a>을 참조하세요.

### 분할된 테이블
다음은 분할된 테이블을 만들고 그 테이블에 데이터를 로드하는 Hive 쿼리입니다.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

분할된 테이블에 쿼리할 때 `where` 절의 **시작** 부분에 파티션 조건을 추가하면 검색 효율성이 크게 향상됩니다.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 형식으로 Hive 데이터 저장

사용자는 blob 저장소의 데이터를 ORC 형식으로 저장된 Hive 테이블에 바로 로드할 수 없습니다. Azure blob의 데이터를 ORC 형식으로 저장된 Hive 테이블에 로드하려면 다음 단계를 수행해야 합니다.

1. 외부 테이블 **STORED AS TEXTFILE**을 만들고 blob 저장소의 데이터를 이 테이블에 로드합니다.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 1단계의 외부 테이블과 동일한 스키마 및 필드 구분 기호를 사용하여 내부 테이블을 만들고 Hive 데이터를 ORC 형식으로 저장합니다.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 1단계의 외부 테이블에서 데이터를 선택하여 ORC 테이블에 삽입합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE] TEXTFILE 테이블 *&#60;database name>.&#60;external textfile table name>*에 파티션이 있으면 3단계의 `SELECT * FROM <database name>.<external textfile table name>` 명령에서는 반환된 데이터 집합의 필드로 파티션 변수를 선택합니다. *&#60;database name>.&#60;ORC table name>*에 삽입은 실패합니다. *&#60;database name>.&#60;ORC table name>*에는 테이블 스키마의 필드인 파티션 변수가 없기 때문입니다. 이 경우 사용자는 *&#60;database name>.&#60;ORC table name>*에 삽입할 필드를 다음과 같이 구체적으로 선택해야 합니다.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. 모든 데이터가 *&#60;database name>.&#60;ORC table name>*에 삽입된 후에는 다음 쿼리를 사용할 때 *&#60;external textfile table name>*을 삭제하는 것이 안전합니다.

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

이 절차를 모두 수행했다면 이제 ORC 형식의 데이터를 사용할 수 있는 테이블이 준비되었을 것입니다.


##튜닝 색션은 여기로 이동해야 합니다.

마지막 섹션에서는 사용자가 조정하여 Hive 쿼리 성능을 높일 수 있는 매개 변수에 대해 설명합니다.

<!---HONumber=AcomDC_0211_2016-->