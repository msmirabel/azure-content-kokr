<properties
   pageTitle="Apache Ambari REST API를 사용하여 HDInsight 클러스터 모니터링 및 관리 | Microsoft Azure"
   description="Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari REST API를 사용하는 방법을 배웁니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/08/2016"
   ms.author="larryfr"/>

#Ambari REST API를 사용하여 HDInsight 클러스터 관리

[AZURE.INCLUDE [ambari 선택기](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari는 손쉬운 웹 UI 및 REST API 사용을 제공하여 Hadoop 클러스터의 관리 및 모니터링을 간소화합니다. Ambari는 Linux 기반 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다. 이 문서에서 클러스터 노드의 완전히 정규화된 도메인 이름 찾기 또는 클러스터에서 사용되는 기본 저장소 계정 찾기와 같은 일반적인 작업을 수행하여 Ambari REST API를 통한 작업의 기본 사항을 설명합니다.

> [AZURE.NOTE]이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에만 적용됩니다. Windows 기반 HDInsight 클러스터의 경우 모니터링 기능의 하위 집합만 Ambari REST API를 통해 사용할 수 있습니다. [Ambari API를 사용하여 HDInsight에서 Windows 기반 Hadoop 모니터링](hdinsight-monitor-use-ambari-api.md)를 참조하세요.

##필수 조건

* [cURL](http://curl.haxx.se/): cURL은 명령줄에서 REST API와 함께 작동하도록 사용할 수 있는 크로스 플랫폼 유틸리티입니다. 이 문서에서 Ambari REST API와 통신하는데 사용됩니다.
* [jq](https://stedolan.github.io/jq/): jq는 JSON 문서 작업에 대한 크로스 플랫폼 명령줄 유틸리티입니다. 이 문서에서 Ambari REST API에서 반환된 JSON 문서를 구문 분석하는데 사용됩니다.

##<a id="whatis"></a>Ambari 정의

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a>에서는 Hadoop 클러스터 프로비전, 관리 및 모니터링에 사용할 수 있는 편리한 웹 UI를 제공하여 쉽게 Hadoop을 관리할 수 있습니다. 개발자는 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a>를 사용하여 자신의 응용 프로그램에 이러한 기능을 통합할 수 있습니다.

Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다.

##REST API

HDInsight에서 Ambari REST API에 대한 기본 URI는 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME이며 여기서 __CLUSTERNAME__은 클러스터의 이름입니다.

> [AZURE.IMPORTANT]HDInsight에서 Ambari에 대한 연결에는 HTTPS가 필요합니다. 또한 관리자 계정 이름(기본값: __admin__)과, 클러스터를 만들 때 제공한 암호를 사용하여 Ambari에 인증해야 합니다.

다음은 cURL을 사용하여 REST API에 대한 GET 요청을 수행하는 예제입니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
이를 실행하는 경우 __암호__를 클러스터에 대한 관리자 암호로 __CLUSTERNAME__을 클러스터 이름으로 대체하면 다음과 비슷한 정보로 시작하는 JSON 문서를 받게 됩니다.

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

이는 JSON이므로 JSON 파서를 사용하여 데이터를 검색하는 것이 일반적으로 더 쉽습니다. 예를 들어 경고의 개수를 검색하려는 경우(__"Host/host\_status/ALERT"__ 요소에 포함됨) 다음을 사용하여 값에 직접 액세스할 수 있습니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
이는 JSON 문서를 검색한 다음 jq에 출력을 파이프합니다. `'.Clusters.health_report."Host/host_status/ALERT"'`은(는) 검색하려는 JSON 문서 내에서 요소를 나타냅니다.

> [AZURE.NOTE]__Host/host\_status/ALERT__ 요소는 '/'가 요소 이름의 일부임을 나타내는 따옴표로 묶여 있습니다. jq 사용에 대한 자세한 내용은 [jq 웹사이트](https://stedolan.github.io/jq/)를 참조하세요.

##예: 클러스터 노드의 FQDN 가져오기

HDInsight에서 작업할 때 클러스터 노드의 정규화된 도메인 이름(FQDN)에 대해 알아야 할 수도 있습니다. 다음을 사용하여 클러스터의 다양한 노드에 대한 FQDN을 쉽게 검색할 수 있습니다.

* __헤드 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __작업자 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

각각은 해당 노드에서 실행되는 구성 요소를 쿼리한 다음 해당 노드에 대한 FQDN을 포함하는 `host_name` 요소 검색의 동일한 패턴을 따릅니다.

반환 문서의 `host_components` 요소는 여러 항목을 포함합니다. `.host_components[]`을(를) 사용한 다음 요소 내에서 경로 지정은 각 항목을 반복하고 특정 경로에서 값을 끌어냅니다. 첫 번째 FQDN 항목과 같은 하나의 값만 원하는 경우 컬렉션으로 항목을 반환하고 특정 항목을 선택할 수 있습니다.

    jq '[.host_components[].HostRoles.host_name][0]'

컬렉션에서 첫 번째 FQDN이 반환됩니다.

##예: 기본 저장소 계정 및 컨테이너 가져오기

HDInsight 클러스터를 만드는 경우 Azure 저장소 계정 및 Blob 컨테이너를 클러스터에 대한 기본 저장소로 사용해야 합니다. 클러스터를 만든 후 Ambari를 사용하여 이 정보를 검색할 수 있습니다. 예를 들어 프로그래밍 방식으로 데이터를 컨테이너에 직접 쓰려는 경우입니다.

다음은 클러스터 기본 저장소의 WASB URI를 검색합니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 할 수도 있습니다.

다음과 유사한 값을 반환하며 여기서 __CONTAINER__는 기본 컨테이너이고 __ACCOUNTNAME__은 Azure 저장소 계정 이름입니다.

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

그런 다음 [Azure CLI](../xplat-cli-install.md)로 이 정보를 사용하여 컨테이너에서 데이터를 업로드하거나 다운로드할 수 있습니다. 예:

1. 저장소 계정에 대한 리소스 그룹을 가져옵니다. __ACCOUNTNAME__을 Ambari에서 검색한 저장소 계정 이름으로 대체합니다.

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    계정에 대한 리소스 그룹 이름을 반환합니다.
    
    > [AZURE.NOTE]이 명령에서 아무 것도 반환되지 않는 경우 Azure CLI를 Azure 리소스 관리자 모드로 변경하고 명령을 다시 실행해야 합니다. Azure 리소스 관리자 모드로 전환하려면 다음 명령을 사용합니다.
    >
    > `azure config mode arm`
    
2. 저장소 계정에 대한 키를 가져옵니다. __GROUPNAME__을 이전 단계의 리소스 그룹으로 대체합니다. __ACCOUNTNAME__을 저장소 계정 이름으로 대체합니다.

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    계정에 대한 기본 키를 반환합니다.
    
3. 업로드 명령을 사용하여 컨테이너에 파일을 저장합니다.

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    __ACCOUNTNAME__을 저장소 계정 이름으로 대체합니다. __ACCOUNTKEY__를 이전에 검색된 키로 대체합니다. __FILEPATH__는 업로드하려는 파일에 대한 경로이며 __BLOBPATH__는 컨테이너의 경로입니다.

    예를 들어 wasb://example/data/filename.txt에서 HDInsight에 파일을 표시하려면 __BLOBPATH__는 `example/data/filename.txt`이(가) 됩니다.

##다음 단계

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.

> [AZURE.NOTE]HDInsight 클라우드 서비스에 의해 관리되는 일부 Ambari 기능(예: 클러스터에서 호스트 추가 또는 제거 또는 새 서비스 추가)은 사용할 수 없습니다.

<!---HONumber=AcomDC_0114_2016-->