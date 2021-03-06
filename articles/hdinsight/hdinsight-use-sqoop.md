<properties
	pageTitle="HDInsight에서 Hadoop Sqoop 사용 | Microsoft Azure"
	description="워크스테이션에서 Azure PowerShell을 사용하여 Hadoop 클러스터와 Azure SQL 데이터베이스 간에 Sqoop 가져오기 및 내보내기를 실행하는 방법에 대해 알아봅니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="jgao"/>

#HDInsight에서 Hadoop과 Sqoop 사용(Windows)

[AZURE.INCLUDE [sqoop 선택기](../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight에서 Sqoop을 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 또는 SQL Server 데이터베이스 사이에서 가져오기 및 내보내기하는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계는 Windows 기반 또는 Linux 기반 HDInsight 클러스터와 사용할 수 있지만 이 단계에서만 Windows 클라이언트에서 작동합니다.
>
> Linux, OS X 또는 Unix 클라이언트 및 Linux 기반 HDInsight 서버를 사용하는 경우 [HDInsight에서 Hadoop과 함께 Sqoop 사용(SSH)](hdinsight-use-sqoop-mac-linux.md)을 참조하세요.

비구조적 및 반구조적 데이터(예: 로그 및 파일)를 처리하기 위해 당연히 Hadoop을 선택하지만 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 경우도 있습니다.

[Sqoop][sqoop-user-guide-1.4.4]은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 관계형 데이터베이스에 SQL Server 데이터베이스를 사용합니다.

HDInsight 클러스터에서 지원되는 Sqoop 버전을 보려면 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 1.0 이상 설치](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)를 참조하세요.

기존 Azure SQL 데이터베이스 또는 Microsoft SQL Server를 사용하기로 선택하는 경우

- **Azure SQL 데이터베이스**: 워크스테이션에서 액세스할 수 있도록 Azure SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스 사용 시작][sqldatabase-get-started]을 참조하세요. 

	> [AZURE.NOTE] 기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure Preview 포털에서 사용하도록 설정해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성][sqldatabase-create-configue]을 참조하세요.

- **SQL Server**: HDInsight 클러스터가 SQL Server와 같은 Azure의 가상 네트워크에 있으면 이 문서의 단계를 사용하여 SQL Server 데이터베이스에 대해 데이터 가져오기 및 내보내기를 수행할 수 있습니다.

	> [AZURE.NOTE] HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다.

	* 가상 네트워크를 만들고 구성하려면 [가상 네트워크 구성 작업](../services/virtual-machines/)을 참조하세요.

		* 데이터 센터에서 SQL Server를 사용할 때는 가상 네트워크를 *사이트 간* 또는 *지점 및 사이트 간*으로 구성해야 합니다.

			> [AZURE.NOTE] **지점 및 사이트 간** 가상 네트워크의 경우 SQL Server가 VPN 클라이언트 구성 응용 프로그램을 실행해야 합니다. 이 응용 프로그램은 Azure 가상 네트워크 구성의 **대시보드**에서 사용 가능합니다.

		* Azure 가상 컴퓨터에서 SQL Server를 사용할 때는 SQL Server를 호스트하는 가상 컴퓨터가 HDInsight와 같은 가상 네트워크의 멤버이면 모든 가상 네트워크 구성을 사용할 수 있습니다.

	* 가상 네트워크에 HDInsight 클러스터를 프로비전하려면 [사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

	> [AZURE.NOTE] SQL Server는 인증도 허용해야 합니다. 이 문서의 단계를 완료하려면 SQL 서버 로그인을 사용해야 합니다.
	
##시나리오 이해

HDInsight 클러스터는 일부 샘플 데이터와 함께 제공됩니다. 다음 두 예제를 사용합니다.

- */example/data/sample.log*에 있는 log4j 로그 파일. 이 파일에서 다음 로그가 추출됩니다.

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- */hive/warehouse/hivesampletable*에 있는 데이터 파일을 참조하는 *hivesampletable*이라는 이름의 Hive 테이블. 이 테이블에는 일부 모바일 장치 데이터가 포함되어 있습니다.

먼저 *sample.log*와 *hivesampletable*을 Azure SQL 데이터베이스 또는 SQL Server에 내보낸 후 모바일 장치 데이터를 포함하는 테이블을 다음 경로를 통해 HDInsight에 다시 가져옵니다.

	/tutorials/usesqoop/importeddata

## PowerShell을 사용하여 Sqoop 실행

이 섹션의 PowerShell 샘플은 다음 단계를 수행합니다.

1. Azure에 연결
2. Azure 리소스 그룹 만들기 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
3. Azure SQL 데이터베이스 서버, Azure SQL 데이터베이스 및 두 개의 테이블을 만듭니다. 

	SQL Server를 대신 사용하는 경우에는 다음 문을 사용하여 테이블을 만듭니다.
	
		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

	데이터베이스 및 테이블을 검사하는 가장 쉬운 방법은 Visual Studio를 사용하는 것입니다. Azure 포털을 사용하여 데이터베이스 서버와 데이터베이스를 검사할 수 있습니다.

4. HDInsight 클러스터 만들기

	클러스터를 검사하려면 Azure 포털 또는 Azure PowerShell을 사용하면 됩니다.

5. 원본 데이터 파일을 전처리합니다.

	이 자습서에서는 log4j 로그 파일(구분된 파일) 및 Hive 테이블을 Azure SQL 데이터베이스로 내보냅니다. 구분된 파일의 이름은 */example/data/sample.log*입니다. 자습서의 앞부분에 log4j 로그 샘플이 몇 개 있습니다. 로그 파일에는 일부 빈 줄과 일부 다음과 유사한 줄이 있습니다.
	
		java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
			at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
	
	이 데이터를 사용하는 다른 예제에서는 이 줄이 있어도 관계없지만 데이터를 Azure SQL 데이터베이스 또는 SQL Server로 가져오려면 이러한 예외를 제거해야 합니다. 빈 문자열이 있거나 Azure SQL 데이터베이스 테이블에 정의된 필드 수보다 요소 수가 더 적은 줄이 있는 경우 Sqoop 내보내기는 실패합니다. log4jlogs 테이블에는 7가지 문자열 형식 필드가 있습니다.

	이 절차를 통해 클러스터에서 새 파일(tutorials/usesqoop/data/sample.log)을 만듭니다. 수정한 데이터 파일을 검사하려면 Azure 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작][hdinsight-get-started]에는 파일을 다운로드하고 그 파일의 내용을 표시하는 Azure PowerShell 사용에 관한 코드 샘플이 있습니다.

6. 데이터 파일을 Azure SQL 데이터베이스로 내보냅니다.

	원본 파일은 tutorials/usesqoop/data/sample.log입니다. 데이터를 내보낸 테이블은 log4jlogs라고 합니다.
	
	> [AZURE.NOTE] 연결 문자열 정보를 제외하면 이 섹션의 단계는 Azure SQL 데이터베이스 또는 SQL Server에 대해 작동합니다. 이러한 단계는 다음 구성을 사용하여 테스트했습니다.
	>
	> * **Azure 가상 네트워크 지점 및 사이트 간 구성**: 개인 데이터 센터에서 HDInsight 클러스터를 SQL Server에 연결하는 가상 네트워크입니다. 자세한 내용은 [관리 포털에서 지점 및 사이트 간 VPN 구성](../vpn-gateway/vpn-gateway-point-to-site-create.md)을 참조하세요.
	> * **Azure HDInsight 3.1**: 가상 네트워크에서 클러스터를 만드는 방법에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.
	> * **SQL Server 2014**: 인증을 허용하고 VPN 클라이언트 구성 패키지를 실행하여 가상 네트워크에 안전하게 연결할 수 있도록 구성됩니다.

7. Hive 테이블을 Azure SQL 데이터베이스로 내보냅니다.

8. mobiledata 테이블을 HDInsight 클러스터로 가져옵니다.

	수정한 데이터 파일을 검사하려면 Azure Preview 포털, Azure 저장소 탐색기 도구 또는 Azure PowerShell을 사용할 수 있습니다. [HDInsight 시작][hdinsight-get-started]에는 파일을 다운로드하고 그 파일의 내용을 표시하는 Azure PowerShell 사용에 관한 코드 샘플이 있습니다.


### PowerShell 샘플

	# Prepare an Azure SQL database to be used by the Sqoop tutorial
	
	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure Subscription ID>"
	
	$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
	$sqlDatabasePassword = "<Enter a Password>"
	
	$httpUserName = "admin"  #HDInsight cluster username
	$httpPassword = "<Enter a Password>"
	
	# used for creating Azure service names
	$nameToken = "<Enter an alias>" 
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# Used for creating tables and clustered indexes
	$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		[t1] [nvarchar](50),
		[t2] [nvarchar](50),
		[t3] [nvarchar](50),
		[t4] [nvarchar](50),
		[t5] [nvarchar](50),
		[t6] [nvarchar](50),
		[t7] [nvarchar](50))"
	
	$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
	
	$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
	[clientid] [nvarchar](50),
	[querytime] [nvarchar](50),
	[market] [nvarchar](50),
	[deviceplatform] [nvarchar](50),
	[devicemake] [nvarchar](50),
	[devicemodel] [nvarchar](50),
	[state] [nvarchar](50),
	[country] [nvarchar](50),
	[querydwelltime] [float],
	[sessionid] [bigint],
	[sessionpagevieworder][bigint])"
	
	$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{Login-AzureRmAccount}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $credential `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	
	#endregion
	
	#region - Create tables
	Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jTable
	$ret = $cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateLog4jClusteredIndex
	$cmd.ExecuteNonQuery()
	
	# Create the mobiledata table and index
	$cmd.CommandText = $cmdCreateMobileTable
	$cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	
	#endregion
	
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $HDInsightClusterName `
		-Location $location `
		-ClusterType Hadoop `
		-OSType Windows `
		-ClusterSizeInNodes 2 `
		-HttpCredential $httpCredential `
		-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - pre-process the source file
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	# This procedure creates a new file with $destBlobName
	$sourceBlobName = "example/data/sample.log"
	$destBlobName = "tutorials/usesqoop/data/sample.log"
	
	# Define the connection string
	$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
	
	# Create block blob objects referencing the source and destination blob.
	$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
	$storageClient = $storageAccount.CreateCloudBlobClient();
	$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
	$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
	$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
	
	# Define a MemoryStream and a StreamReader for reading from the source file
	$stream = New-Object System.IO.MemoryStream
	$stream = $sourceBlob.OpenRead()
	$sReader = New-Object System.IO.StreamReader($stream)
	
	# Define a MemoryStream and a StreamWriter for writing into the destination file
	$memStream = New-Object System.IO.MemoryStream
	$writeStream = New-Object System.IO.StreamWriter $memStream
	
	# Pre-process the source blob
	$exString = "java.lang.Exception:"
	while(-Not $sReader.EndOfStream){
		$line = $sReader.ReadLine()
		$split = $line.Split(" ")
	
		# remove the "java.lang.Exception" from the first element of the array
		# for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		if ($split[0] -eq $exString){
			#create a new ArrayList to remove $split[0]
			$newArray = [System.Collections.ArrayList] $split
			$newArray.Remove($exString)
	
			# update $split and $line
			$split = $newArray
			$line = $newArray -join(" ")
		}
	
		# remove the lines that has less than 7 elements
		if ($split.count -ge 7){
			write-host $line
			$writeStream.WriteLine($line)
		}
	}
	
	# Write to the destination blob
	$writeStream.Flush()
	$memStream.Seek(0, "Begin")
	$destBlob.UploadFromStream($memStream)
	
	#endregion
	
	#region - export a log file from the cluster to the SQL database
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	$tableName_log4j = "log4jlogs"
	
	# Connection string for Azure SQL Database.
	# Comment if using SQL Server
	$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	# Connection string for SQL Server.
	# Uncomment if using SQL Server.
	#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
	$exportDir_log4j = "/tutorials/usesqoop/data"
	
	# Submit a Sqoop job
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
	
	#endregion
	
	#region - export a Hive table
	
	$tableName_mobile = "mobiledata"
	$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion
	
	#region - import a database
	
	$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
	
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion

## .NET SDK를 사용하여 Sqoop 실행

이 섹션에서는 C# 콘솔 응용 프로그램을 만들어 이 자습서의 앞 부분에서 만든 SQL 데이터베이스 테이블에 hivesampletable 내보냅니다.

**Sqoop 작업을 제출하려면**

1. Visual Studio 패키지 관리자 콘솔에서 다음 Nuget 명령을 실행하여 패키지를 가져옵니다.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Program.cs 파일에서 다음 using 문을 사용합니다.

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
3. Main() 함수에 다음 코드를 추가합니다. HDInsight .NET SDK 사용에 대한 일반적인 정보를 보려면 [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]을 참조하세요.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		var sqlDatabaseServerName = "<AzureSQLDatabaseServerName>";
		var sqlDatabaseLogin = "<AzureSQLDatabaseLogin>";
		var sqlDatabaseLoginPassword = "<AzureSQLDatabaseLoginPassword>";
		var sqlDatabaseDatabaseName = "<AzureSQLDatabaseDatabaseName>";
		
		var sqlDatabaseTableName = "log4jlogs";
		var exportDir = "/hive/warehouse/hivesampletable";

		var cmdExport = @"export";
		// Connection string for using Azure SQL Database.
		// Comment if using SQL Server
		cmdExport = cmdExport + @" --connect 'jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName +"'"; 
		// Connection string for using SQL Server.
		// Uncomment if using SQL Server
		//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
		cmdExport = cmdExport + @" --export-dir " + exportDir;
		cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
		
		HDInsightJobManagementClient _hdiJobManagementClient;
		var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

		var parameters = new SqoopJobSubmissionParameters
		{
		    UserName = ExistingClusterUsername,
		    Command = cmdExport
		};
		
		System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		System.Console.WriteLine("Validating that the response is as expected...");
		System.Console.WriteLine("Response status code is " + response.StatusCode);
		System.Console.WriteLine("Validating the response object...");
		System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		Console.WriteLine("Press ENTER to continue ...");
		Console.ReadLine();
4. **F5** 키를 눌러 프로그램을 실행합니다. 




##다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight와 함께 Oozie 사용][hdinsight-use-oozie]\: Oozie 워크플로에서 Sqoop 작업을 사용합니다.
- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]\: Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
- [HDInsight에 데이터 업로드][hdinsight-upload-data]\: HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.

[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0218_2016-->