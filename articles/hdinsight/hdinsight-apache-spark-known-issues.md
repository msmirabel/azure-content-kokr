<properties 
	pageTitle="HDInsight에서 Apache Spark의 알려진 문제 | Microsoft Azure" 
	description="HDInsight에서 Apache Spark의 알려진 문제" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2016" 
	ms.author="nitinme"/>

# Azure HDInsight(Linux)에서 Apache Spark의 알려진 문제

이 문서는 HDInsight Spark 공개 미리 보기에 대한 모든 알려진 문제를 추적합니다.

##Livy 누수 대화형 세션
 
**증상:**

Livy가 여전히 활성 상태인 대화형 세션(Ambari에서 또는 헤드 노드 0 가상 컴퓨터 재부팅으로 인해)으로 시작하는 경우 대화형 작업 세션이 손실됩니다. 이로 인해 새 작업은 수락된 상태에 멈출 수 있으며 시작할 수 없습니다.

**해결 방법:**

다음 절차에 따라 문제를 해결합니다.

1. 헤드 노드로 ssh합니다. 
2. 다음 명령을 실행하여 Livy를 통해 시작한 대화형 작업의 응용 프로그램 ID를 찾습니다. 

        yarn application –list

    작업이 명시적 이름이 지정되지 않은 Livy 대화형 세션으로 시작된 경우 기본 작업 이름은 Livy가 되며 Jupyter 노트북으로 시작된 Livy 세션의 경우 작업 이름은 remotesparkmagics\_*로 시작됩니다.

3. 다음 명령을 실행하여 해당 작업을 중지합니다.

        yarn application –kill <Application ID>

새 작업 실행이 시작됩니다.

##Spark 기록 서버가 시작되지 않음 

**증상:**
 
클러스터가 만들어진 후 Spark 기록 서버가 자동으로 시작되지 않습니다.

**해결 방법:**

Ambari에서 기록 서버를 수동으로 시작합니다.

##더 큰 노트북을 로드하는 중 오류

**증상:**

더 큰 노트북을 로드할 때 **`Error loading notebook`** 오류가 표시될 수 있습니다.

**해결 방법:**

이 오류가 발생했다고 해서 데이터가 손상되거나 손실된 것은 아닙니다. 노트북은 여전히 `/var/lib/jupyter`의 디스크에 있으며 클러스터에 대한 SSH를 통해 액세스할 수 있습니다. 노트북을 사용자의 클러스터에서 로컬 컴퓨터에(SCP 또는 WinSCP를 사용하여) 백업으로 복사하여 노트북의 중요 데이터 손실을 방지할 수 있습니다. 그런 다음 포트 8001의 헤드 노드에 대한 SSH 터널을 통해 게이트웨이를 거치지 않고 Jupyter에 액세스할 수 있습니다. 여기에서 노트북의 출력을 지우고 다시 저장하여 노트북의 크기를 최소화할 수 있습니다.

나중에 이 오류가 발생하지 않도록 하려면 몇 가지 모범 사례를 따라야 합니다.

* 노트북 크기를 작게 유지하는 것이 중요합니다. Jupyter에 다시 전송된 Spark 작업의 출력은 노트북에 보관됩니다. RDD의 데이터 프레임에서 `.collect()`을(를) 실행하지 못하도록 하는 것이 Jupyter의 모범 사례입니다. 그 대신 RDD의 컨텐츠를 살펴보려면 출력이 너무 커지지 않도록 `.take()` 또는 `.sample()`의 실행을 고려합니다.
* 또한 노트북을 저장할 때 모든 출력을 지워서 크기를 줄입니다.



##노트북 초기 시작이 예상보다 오래 걸리는 경우 

**증상:**

Jupyter 노트북에서 Spark Magic을 사용한 첫 번째 문의 경우 1분 이상이 걸릴 수 있습니다.

**설명:**
 
이는 첫 번째 코드 셀이 실행될 때 발생합니다. 백그라운드에서 세션 구성이 시작되고 Spark, SQL 및 Hive 컨텍스트가 설정됩니다. 이러한 컨텍스트가 설정된 후 첫 번째 문이 실행되므로 문이 완료되는 데 시간이 오래 걸린 것 같은 느낌이 듭니다.

##세션 만들기에서 Jupyter 노트북 시간 제한

**증상:**

Spark 클러스터에 리소스가 부족할 때 Jupyter 노트북에서 Spark 및 Pyspark 커널은 세션을 만들려고 할 때 시간 초과가 됩니다.

**해결 방법:**

1. 다음과 같은 방법으로 Spark 클러스터의 리소스를 확보합니다.

    - Close and Halt 메뉴로 이동하거나 노트북 탐색기에서 종료를 클릭하여 다른 Spark 노트북을 중지합니다.
    - YARN에서 다른 Spark 응용 프로그램을 중지합니다.

2. 시작하려는 노트북을 다시 시작합니다. 이제 세션을 만들기 위한 충분한 리소스를 사용할 수 있습니다.

## Spark 로그 디렉터리에 대한 사용 권한 문제 

**증상:**
 
hdiuser가 spark-제출로 작업을 제출하는 경우 오류 java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log(사용 권한 거부됨)가 있고 드라이버 로그가 작성되지 않습니다.

**해결 방법:**
 
1. hdiuser를 Hadoop 그룹에 추가합니다. 
2. 클러스터를 만든 후에 /var/log/spark에 777 권한을 제공합니다. 
3. Ambari를 사용하여 777 권한으로 디렉터리가 되도록 spark 로그 위치를 업데이트합니다.  
4. sudo로 spark-제출을 실행합니다. 

##참고 항목

- [개요: Azure HDInsight에서 Apache Spark(Linux)](hdinsight-apache-spark-overview.md)
- [시작: Azure HDInsight(Linux)에서 Apache Spark 프로비전 및 Spark SQL을 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0224_2016-->