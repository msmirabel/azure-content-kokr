<properties
	pageTitle="HDInsight Linux 클러스터에서 Hadoop으로 Hue 사용 | Microsoft Azure"
	description="HDInsight Linux에서 Hadoop 클러스터로 Hue를 설치하고 사용하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="nitinme"/>

# HDInsight Hadoop 클러스터에 Hue 설치 및 사용

HDInsight Linux 클러스터에서 Hue를 설치하고 터널링을 사용하여 Hue로 요청을 라우팅하는 방법을 알아봅니다.

## Hue 정의

Hue는 Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 응용 프로그램 집합입니다. Hue를 사용하여 Hadoop 클러스터(HDInsight 클러스터의 경우, WASB)와 연결된 저장소를 찾아보고 Hive 작업 및 Pig 스크립트 등을 실행할 수 있습니다. HDInsight Hadoop 클러스터에서 Hue 설치는 다음 구성 요소를 지원합니다.

* Beeswax Hive 편집기
* Pig
* 메타스토어 관리자
* Oozie
* FileBrowser (WASB 기본 컨테이너로 전달)
* 작업 브라우저


## 스크립트 동작을 사용하여 Hue 설치

[https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh) 스크립트 작업을 사용하여 HDInsight 클러스터에서 Hue를 설치합니다. 이 섹션에서는 Azure 클래식 포털을 사용하여 클러스터를 프로비전할 때 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE] 또한 이 스크립트를 사용하여 클러스터를 만드는 데 Azure PowerShell 또는 HDInsight.NET SDK를 사용할 수도 있습니다. 이 방법을 사용하는 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)의 단계를 사용하여 클러스터를 프로비전하기 시작하지만 프로비전을 완료하지 마세요.

	> [AZURE.NOTE] HDInsight 클러스터에 Hue를 설치하려면 권장 헤드 노드 크기는 A4(8개 코어, 14GB 메모리) 이상입니다.

2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래와 같은 정보를 제공합니다.

	![색상에 대한 스크립트 작업 매개 변수 제공](./media/hdinsight-hadoop-hue-linux/hue_script_action.png "색상에 대한 스크립트 작업 매개 변수 제공")

	* __이름__: 스크립트 동작의 이름을 입력합니다.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
	* __헤드__: 이 옵션을 선택합니다.
	* __작업자__: 비워둡니다.
	* __ZOOKEEPER__: 비워둡니다.
	* __PARAMETERS__: 이 필드는 공백으로 둡니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 블레이드의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

4. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)에 설명된 대로 클러스터를 계속 프로비전합니다.

## HDInsight 클러스터로 Hue 사용

SSH 터널링이 실행되면 클러스터에서 Hue를 액세스하는 유일한 방법입니다. SSH를 통한 터널링을 사용하면 트래픽은 Hue가 실행되는 클러스터의 헤드 노드로 직접 이동할 수 있습니다. 클러스터가 프로비전을 완료하면 다음 단계를 수행하여 HDInsight Linux 클러스터에서 Hue를 사용합니다.

1. [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 다른 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)에 있는 정보를 사용하여 클라이언트 시스템에서 HDInsight 클러스터로 SSH 터널을 만들고 SSH 터널을 프록시로 사용하도록 웹 브라우저를 구성합니다.

2. SSH 터널을 생성하고 프록시 트래픽이 통과하는 브라우저를 구성했으면, 헤드 노드의 호스트 이름을 찾아야 합니다. 다음 단계를 수행하여 Ambari에서 이 정보를 가져오십시오.

    1. 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. 메시지가 표시되면 관리자 사용자 이름 및 암호를 사용하여 사이트에 인증합니다.
    
    2. 페이지 위쪽에 있는 메뉴에서 __호스트__를 선택합니다.
    
    3. __hn0__으로 시작하는 항목을 선택합니다. 페이지가 열릴 때 호스트 이름이 위쪽에 표시됩니다. 호스트 이름의 형식은 __hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__입니다. 이 호스트 이름은 Hue에 연결할 때 사용해야만 합니다.

2. SSH 터널을 만들고 이를 통해 트래픽을 프록시하도록 브라우저를 구성했으면 브라우저를 사용하여 http://HOSTNAME:8888에서 Hue 포털을 엽니다. HOSTNAME을 이전 단계의 Ambari에서 얻은 이름으로 바꿉니다.

    > [AZURE.NOTE] 처음으로 로그인할 때 Hue 포털에 로그인할 계정을 만들라는 메시지가 표시됩니다. 여기에서 지정한 자격 증명은 포털로 제한되며 클러스터를 프로비전하는 동안 지정한 관리자 또는 SSH 사용자 자격 증명과 관련이 없습니다.

	![Hue 포털에 로그인](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Hue 포털에 자격 증명 지정")

### Hive 쿼리 실행

1. Hue 포털에서 **쿼리 편집기**를 클릭하고 **하이브**를 클릭하여 하이브 편집기를 엽니다.

	![Hive 사용](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Hive 사용")

2. **지원** 탭의 **데이터베이스**에서 **hivesampletable**이 표시되어야 합니다. HDInsight에서 모든 Hadoop 클러스터로 제공되는 예제 테이블입니다. 스크린 캡처에 표시된 것처럼 오른쪽 창에서 예제 쿼리를 입력하면 **결과** 탭에서 출력이 표시됩니다.

	![Hive 쿼리 실행](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Hive 쿼리 실행")

	**차트** 탭을 사용하여 결과를 시각적으로 표시할 수 있습니다.

### 클러스터 저장소 찾아보기

1. Hue 포털에서 메뉴 모음의 오른쪽 위에 있는 **파일 브라우저**를 클릭합니다.

2. 기본적으로 **/user/myuser** 디렉터리에서 파일 브라우저를 엽니다. 경로에서 사용자 디렉터리 바로 앞 슬래시를 클릭하여 클러스터와 연결된 Azure 저장소 컨테이너의 루트로 이동합니다.

	![파일 브라우저 사용](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "파일 브라우저 사용")

3. 파일 또는 폴더를 마우스 오른쪽 단추로 클릭하여 사용 가능한 작업을 참조하세요. 오른쪽 모서리에서 **업로드** 단추를 사용하여 현재 디렉터리에 파일을 업로드합니다. **새로 만들기** 단추를 사용하여 새 파일 또는 디렉터리를 만듭니다.

> [AZURE.NOTE] Hue 파일 브라우저는 HDInsight 클러스터와 연결된 기본 컨테이너의 콘텐츠만을 표시할 수 있습니다. 클러스터와 연결된 모든 추가 저장소 계정/컨테이너는 파일 브라우저를 사용하여 액세스할 수 없습니다. 그러나 클러스터와 관련된 추가 컨테이너는 항상 Hive 작업에 액세스할 수 있습니다. 예를 들어 하이브 편집기에 명령 `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net`을 입력하는 경우 추가 컨테이너의 내용도 볼 수 있습니다. 이 명령에서 **newcontainer**는 클러스터와 연결된 기본 컨테이너가 아닙니다.

## 중요 고려 사항

1. Hue를 설치할 때 사용된 스크립트는 클러스터의 헤드 노드 0에만 설치합니다.

2. 설치하는 동안 구성을 업데이트하기 위해 여러 Hadoop 서비스(HDFS, YARN, MR2, Oozie)를 다시 시작합니다. 스크립트가 Hue의 설치를 완료한 후에 다른 Hadoop 서비스를 시작하려면 시간이 걸릴 수 있습니다. 처음에 Hue의 성능이 달라질 수 있습니다. 모든 서비스를 시작하면 Hue는 완벽하게 작동합니다.

3.	Hue는 Hive의 현재 기본값인 Tez 작업을 인식하지 못합니다. MapReduce를 Hive 실행 엔진으로 사용하려는 경우 스크립트를 업데이트하여 스크립트에서 다음 명령을 사용합니다.

		set hive.execution.engine=mr;

4.	Linux 클러스터를 사용하면 리소스 관리자를 헤드 노드1에서 실행할 동안 서비스가 헤드 노드0에서 실행되는 시나리오를 사용할 수 있습니다. Hue를 사용하여 클러스터에서 실행 중인 작업의 세부 정보를 보려면 이러한 시나리오에 오류가 발생할 수 있습니다. 그러나 작업이 완료되었을 때 작업 세부 정보를 볼 수 있습니다.

	![Hue 포털 오류](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Hue 포털 오류")

	이는 알려진 문제 때문입니다. 두 번째 해결책으로, 활성 리소스 관리자 또한 헤드 노드0에서 실행되도록 Ambari를 수정합니다.

5.	`wasb://`을 사용하여 HDInsight 클러스터가 Azure 저장소를 사용하는 동안 Hue는 WebHDFS를 이해합니다. 따라서 스크립트 동작에 사용할 사용자 지정 스크립트는 WASB와 통신을 위한 WebHDFS와 호환 가능한 서비스인 WebWasb를 설치합니다. 따라서 Hue 포털이 HDFS가 제대로 있다고 하더라도(**파일 브라우저**로 마우스를 이동할 때처럼) WASB로 해석되어야 합니다.


## 다음 단계

- [HDInsight 클러스터에 Spark 설치 및 사용](hdinsight-hadoop-spark-install-linux.md) - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Spark를 설치하고 사용하는 방법에 대한 지침을 제공합니다. Spark는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다.

- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

- [HDInsight 클러스터에 R 설치](hdinsight-hadoop-r-scripts-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 R을 설치합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=AcomDC_0204_2016-->