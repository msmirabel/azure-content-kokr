 <properties 
	pageTitle="IntelliJ IDEA용 HDInsight 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 | Microsoft Azure" 
	description="독립 실행형 Spark 응용 프로그램을 만들어 HDInsight Spark 클러스터에서 실행하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출(Linux)

이 문서에서는 Scala로 작성된 Spark 응용 프로그램을 개발하고 IntelliJ IDEA용 HDInsight 플러그 인을 사용하여 이를 HDInsight Spark 클러스터에 제출하는 데 대한 단계별 참고 자료를 제공합니다. 몇 가지 방법으로 이 플러그 인을 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 응용 프로그램을 개발 및 제출하려면
* Azure HDInsight Spark 클러스터 리소스에 액세스하려면
* Scala Spark 응용 프로그램을 로컬로 개발 및 실행하려면

>[AZURE.IMPORTANT] 이 도구를 사용하면 Linux의 HDInsight Spark 클러스터용 응용 프로그램만 만들고 제출할 수 있습니다.


**필수 구성 요소**

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
* IntelliJ IDEA. 이 문서에서는 버전 15.0.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다. 


## IntelliJ IDEA용 Scala 플러그 인 설치

IntelliJ IDEA 설치가 Scala 플러그 인 활성화를 묻지 않은 경우 IntelliJ IDEA를 시작하고 다음 단계를 진행하여 플러그 인을 설치합니다.

1. IntelliJ IDEA를 시작하고 시작 화면에서 **구성**을 클릭한 다음 **플러그 인**을 클릭합니다.

	![scala 플러그 인 활성화](./media/hdinsight-apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

2. 다음 화면에서 왼쪽 아래 모서리에서 **JetBrains 플러그 인 설치**를 클릭합니다. 열리는 **JetBrains 플러그 인 찾아보기** 대화 상자에서Scala를 검색한 다음 **설치**를 클릭합니다.

	![scala 플러그 인 설치](./media/hdinsight-apache-spark-intellij-tool-plugin/install-scala-plugin.png)

3. 플러그 인이 성공적으로 설치되면 IDE를 다시 시작할 것인지 묻는 메시지가 나타납니다. 지금은 건너뛸 수 있습니다.

## IntelliJ IDEA용 HDInsight 도구 플러그 인 설치

1. IntelliJ IDEA 시작 화면으로 돌아오면 **구성**을 클릭한 다음 **플러그 인**을 다시 클릭합니다.

2. 다음 화면에서 왼쪽 아래 모서리의 **Browse Repositories**(리포지토리 찾아보기)를 클릭합니다. **Browse Repositories**(리포지토리 찾아보기) 대화 상자가 열리면 **HDInsight**를 찾아 **Microsoft Azure HDInsight Tools for IntelliJ**(IntelliJ용 Microsoft Azure HDInsight 도구)를 선택한 다음 **설치**를 클릭합니다.

3. 메시지가 나타나면 **Restart IntelliJ IDEA**(IntelliJ IDEA 다시 시작) 단추를 클릭하여 IDE를 다시 시작합니다.

## HDInsight Spark 클러스터에서 Spark Scala 응용 프로그램 실행

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **Spark on HDInsight (Scala)**(HDInsight의 Spark(Scala))를 선택합니다.
	* **다음**을 클릭합니다.

2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

	* 프로젝트 이름과 프로젝트 위치를 제공합니다.
	* **프로젝트 SDK**의 경우 7보다 높은 Java 버전을 제공해야 합니다.
	* **Scala SDK**의 경우 **만들기**를 클릭하고 **다운로드**를 클릭한 다음 사용할 Scala의 버전을 선택합니다. **2.11.x 버전은 사용하지 마세요**. 이 샘플에서는 버전 **2.10.6**을 사용합니다.
	
		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* **Spark SDK**의 경우 [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 SDK를 다운로드하여 사용합니다.

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* **마침**을 클릭합니다.

3. 클러스터에서 실행되는 코드가 최종적으로 포함되는 아티팩트(패키지에 포함된 jar)를 만들도록 프로젝트 구조를 정의합니다.

	1. **파일** 메뉴에서 **프로젝트 구조**를 클릭합니다.
	2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭한 다음 더하기 기호를 클릭합니다. 팝업 대화 상자에서 **JAR**을 클릭한 다음 **비어 있음**을 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. JAR 파일의 이름(예: **MyClusterApp**)을 입력합니다. Available Elements(사용 가능한 요소) 창에서 **'MyClusterApp' compile output**('MyClusterApp' 컴파일 출력)을 마우스 오른쪽 단추로 클릭하고 **Put into Output Root**(출력 루트에 배치)를 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)
	
	4. **적용**을 클릭한 다음 **확인**을 클릭합니다.

4. 응용 프로그램 소스 코드 적용

	1. **프로젝트 탐색기**에서 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala class**(Scala 클래스)를 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. **Create New Scala Class**(새 Scala 클래스 만들기) 대화 상자에서 이름을 제공하고 **종류**에 대해 **개체**를 선택하고 **확인**을 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. **MyClusterApp.scala** 파일에 다음 코드를 붙여넣습니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut**에 씁니다.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext
			
			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)
			
			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
			
			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
			
			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }
			
			}

5. HDInsight Spark 클러스터에서 응용 프로그램 실행

	1. **프로젝트 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **Submit Spark Application to HDInsight**(HDInsight에 Spark 응용 프로그램 제출)를 선택합니다.

		![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Azure 구독 자격 증명을 입력하라는 메시지가 표시됩니다. **Spark Submission**(Spark 제출) 대화 상자에 다음 값을 제공합니다.

		![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* **Spark 클러스터(Linux만 해당)**의 경우 응용 프로그램을 실행하려는 HDInsight Spark 클러스터를 선택합니다.

		* **Build Artifacts**(빌드 아티팩트) 드롭다운 목록에는 이전 단계에서 지정된 JAR 이름이 표시됩니다.

		* **Main class name**(기본 클래스 이름) 텍스트 상자에서 줄임표(![줄임표](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))를 클릭하고 응용 프로그램 소스 코드에서 기본 클래스를 선택한 다음 **확인**을 클릭합니다.

			![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
	
		* 이 예제의 응용 프로그램 코드에는 명령줄 인수가 필요하지 않고 JAR 또는 파일을 참조하지 않으므로 나머지 텍스트 상자를 비워둘 수 있습니다.
	
		* **Submit**를 클릭합니다.

	3. 창 아래쪽의 **Spark Submission**(Spark 제출) 탭에 진행 상태가 표시되기 시작합니다.

	다음 섹션에서는 IntelliJ IDEA용 HDInsight 플러그 인을 사용하여 작업 출력에 액세스하는 방법을 배웁니다.


## IntelliJ용 HDInsight 플러그 인을 사용하여 HDInsight Spark 클러스터 액세스 및 관리

HDInsight 플러그 인을 사용하여 다양한 작업을 수행할 수 있습니다.

### 클러스터에 대한 저장소 컨테이너 액세스

1. **보기** 메뉴에서 **도구 창**을 가리킨 다음 **HDInsight Explorer**(HDInsight 탐색기)를 클릭합니다. 메시지가 표시되면 Azure 구독에 액세스할 자격 증명을 입력합니다.

2. **HDInsight** 루트 노드를 확장하여 사용할 수 있는 HDInsight Spark 클러스터의 목록을 표시합니다.

3. 클러스터 이름을 확장하여 클러스터에 대한 저장소 계정 및 기본 저장소 컨테이너를 표시합니다.

	![클러스터 저장소 액세스](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. 클러스터와 연결된 저장소 컨테이너 이름을 클릭합니다. 오른쪽 창에 **HVACOut** 폴더가 표시됩니다. 폴더를 두 번 클릭하여 열면 **part-*** 파일이 나타납니다. 이러한 파일 중 하나를 열어 응용 프로그램의 출력을 표시합니다.

### Spark 기록 서버 액세스

1. **HDInsight Explorer**(HDInsight 탐색기)에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **Open Spark History UI**(Spark 기록 UI 열기)를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

2. Spark 기록 서버 대시보드에서 응용 프로그램 이름을 사용하여 방금 실행을 마친 응용 프로그램을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("MyClusterApp")`을 사용하여 응용 프로그램 이름을 설정했습니다. 그러므로 Spark 응용 프로그램의 이름은 **MyClusterApp**입니다.

### Ambari 포털 시작

**HDInsight Explorer**(HDInsight 탐색기)에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **Open Cluster Management Portal (Ambari)**(클러스터 관리 포털 열기(Ambari))을 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

### Azure 구독 관리

기본적으로 HDInsight 플러그 인에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 클러스터에 액세스하려는 구독을 지정할 수 있습니다. **HDInsight Explorer**(HDInsight 탐색기)에서 **HDInsight** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 클릭합니다. 대화 상자에서 액세스하지 않으려는 구독의 확인란 선택을 취소하고 **닫기**를 클릭합니다. Azure 구독에서 로그오프하려는 경우 **로그아웃**을 클릭할 수도 있습니다.


## 로컬로 Spark Scala 응용 프로그램 실행

IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 워크스테이션에서 Spark Scala 응용 프로그램을 로컬로 실행할 수 있습니다. 일반적으로 이러한 응용 프로그램은 저장소 컨테이너와 같은 클러스터 리소스에 액세스할 필요가 없으므로 로컬로 실행하고 테스트할 수 있습니다.

### 필수 요소

Windows 컴퓨터에서 로컬 Spark Scala 응용 프로그램을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에 설명된 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없는 경우 발생합니다. 이 오류를 해결하려면 [여기에서 실행 파일을 다운로드](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\\WinUtils\\bin** 등의 위치에 저장해야 합니다. 그런 다음 환경 변수 **HADOOP\_HOME**을 추가하고 변수 값을 **C\\WinUtils**로 설정합니다.

### 로컬 Spark Scala 응용 프로그램 실행	 

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **Spark on HDInsight (Scala)**(HDInsight의 Spark(Scala))를 선택합니다.
	* **다음**을 클릭합니다.

2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

	* 프로젝트 이름과 프로젝트 위치를 제공합니다.
	* **프로젝트 SDK**의 경우 7보다 높은 Java 버전을 제공해야 합니다.
	* **Scala SDK**의 경우 **만들기**를 클릭하고 **다운로드**를 클릭한 다음 사용할 Scala의 버전을 선택합니다. **2.11.x 버전은 사용하지 마세요**. 이 샘플에서는 버전 **2.10.6**을 사용합니다.
	
		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* **Spark SDK**의 경우 [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 SDK를 다운로드하여 사용합니다.
	* **마침**을 클릭합니다.

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-local-project-details.png)

3. 클러스터에서 실행되는 코드가 최종적으로 포함되는 아티팩트(패키지에 포함된 jar)를 만들도록 프로젝트 구조를 정의합니다.

	1. **파일** 메뉴에서 **프로젝트 구조**를 클릭합니다.
	2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭한 다음 더하기 기호를 클릭합니다. 팝업 대화 상자에서 **JAR**을 클릭한 다음 **비어 있음**을 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. JAR 파일의 이름(예: **MyLocalApp**)을 입력합니다. Available Elements(사용 가능한 요소) 창에서 **'MyLocalApp' compile output**('MyLocalApp' 컴파일 출력)을 마우스 오른쪽 단추로 클릭하고 **Put into Output Root**(출력 루트에 배치)를 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-local-jar-2.png)
	
	4. **적용**을 클릭한 다음 **확인**을 클릭합니다.

4. 응용 프로그램 소스 코드 적용

	1. **프로젝트 탐색기**에서 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala class**(Scala 클래스)를 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code.png)

	2. **Create New Scala Class**(새 Scala 클래스 만들기) 대화 상자에서 이름을 제공하고 **종류**에 대해 **개체**를 선택하고 **확인**을 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code-object.png)

	3. **MyLocalApp.scala** 파일에 다음 코드를 붙여넣습니다. 이 코드는 컴퓨터의 샘플 입력 텍스트 파일을 읽고 해당 텍스트 파일에서 "a" 및 "b" 문자가 포함된 줄 수를 인쇄합니다.


			import org.apache.spark.SparkContext
			import org.apache.spark.SparkContext._
			import org.apache.spark.SparkConf
			
			object MyLocalApp {
			  def main(args: Array[String]) {
			    val logFile = "C:/users/nitinme/Desktop/commands.txt" // Should be some file on your system
			    val conf = new SparkConf().setAppName("MyLocalApp").setMaster("local[2]")
			    val sc = new SparkContext(conf)
			    val logData = sc.textFile(logFile, 2).cache()
			    val numAs = logData.filter(line => line.contains("a")).count()
			    val numBs = logData.filter(line => line.contains("b")).count()
			    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
			  }
			}

5. 워크스테이션에서 로컬로 응용 프로그램을 실행합니다. **실행** 메뉴에서 **'MyLocalApp' 실행**을 클릭합니다. 아래쪽의 **실행** 탭에 다음과 같은 출력이 표시됩니다.

		...
		...
		Lines with a: 4, Lines with b: 4
		...
		...
		16/02/01 15:04:05 INFO SparkContext: Successfully stopped SparkContext
		16/02/01 15:04:05 INFO ShutdownHookManager: Shutdown hook called
		16/02/01 15:04:05 INFO ShutdownHookManager: Deleting directory C:\Users\nitinme\AppData\Local\Temp\spark-618dee33-45a3-4bce-a8fc-bf85663133b3
		
		Process finished with exit code 0


## HDInsight 도구 플러그 인을 사용하도록 기존 IntelliJ IDEA 응용 프로그램을 변환합니다.

또한 IntelliJ IDEA에서 만들어진 기존 Spark Scala 응용 프로그램을 HDInsight 도구 플러그 인과 호환되도록 변환할 수도 있습니다. 이렇게 하면 도구를 사용하여 HDInsight Spark 클러스터에 응용 프로그램을 제출할 수 있습니다. 다음 단계를 사용하여 이 작업을 수행할 수 있습니다.

1. IntelliJ IDEA를 사용하여 만들어진 기존 Spark Scala 응용 프로그램의 경우 관련된 .iml 파일을 엽니다.
2. 루트 수준에 다음과 같은 **module** 요소가 표시됩니다.

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. **module** 요소가 다음과 같이 표시되도록 요소를 편집하여 `UniqueKey="HDInsightTool"`을 추가합니다.

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. 변경 내용을 저장합니다. 이제 응용 프로그램이 HDInsight 도구 플러그 인과 호환됩니다. 프로젝트 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 이 테스트를 수행할 수 있습니다. 팝업 메뉴에 **Submit Spark Application to HDInsight**(HDInsight에 Spark 응용 프로그램 제출) 옵션이 표시됩니다.

## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### 도구 및 확장

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0302_2016-->