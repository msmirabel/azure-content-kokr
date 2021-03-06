<properties 
	pageTitle="탄력적 데이터베이스 작업 도구를 제거하는 방법" 
	description="탄력적 데이터베이스 작업 도구를 제거하는 방법" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="ddove; sidneyh"/>

#탄력적 데이터베이스 작업 구성 요소 제거
포털 또는 PowerShell을 사용하여 **탄력적 데이터베이스 작업** 구성 요소를 제거할 수 있습니다.

##Azure 포털을 사용하여 탄력적 데이터베이스 작업 구성 요소 제거

1. [Azure 포털](https://ms.portal.azure.com/)을 엽니다.
2. **탄력적 데이터베이스 작업** 구성 요소를 포함하는 구독, 즉 탄력적 데이터베이스 작업 구성 요소가 설치된 구독으로 이동합니다.
3. **찾아보기**를 클릭하고 **리소스 그룹**을 클릭합니다.
4. "\_\_ElasticDatabaseJob"이라는 이름의 리소스 그룹을 선택합니다.
5. 해당 리소스 그룹을 삭제합니다.

##PowerShell을 사용하여 탄력적 데이터베이스 작업 구성 요소 제거

1.	Microsoft Azure PowerShell 명령 창을 시작하고 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x 폴더 아래의 tools 하위 디렉터리로 이동합니다. **cd tools**를 입력합니다.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	.\\UninstallElasticDatabaseJobs.ps1 PowerShell 스크립트를 실행합니다.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

또는 간단히 구성 요소 설치에 사용된 위치에서 기본값을 가정하여 다음 스크립트를 실행합니다.

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## 다음 단계

탄력적 데이터베이스 작업을 다시 설치하려면 [탄력적 데이터베이스 작업 서비스 설치](sql-database-elastic-jobs-service-installation.md)를 참조하세요.

탄력적 데이터베이스 작업의 개요는 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

<!--Image references-->

<!---HONumber=AcomDC_0224_2016-->