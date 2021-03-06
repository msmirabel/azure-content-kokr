<properties
   pageTitle="VM 이미지 탐색 및 선택 | Microsoft Azure"
   description="리소스 관리자 배포 모델로 Azure 가상 컴퓨터를 만들 경우 이미지의 게시자, 제품 및 SKU를 확인하는 방법에 대해 알아보세요."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.




## 일반적으로 사용하는 이미지 테이블


| PublisherName | 제안 | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| CoreOS | CoreOS | 베타 |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Azure CLI

> [AZURE.NOTE]이 문서에서는 최근 설치된 Azure CLI 또는 Azure PowerShell을 사용하여 가상 컴퓨터 이미지를 탐색 및 선택하는 방법을 설명합니다. 전제 조건으로 리소스 관리자 모드를 변경해야 할 수 있습니다. Azure CLI로 `azure config mode arm`을(를) 입력하여 해당 모드를 입력합니다.

`azure vm quick-create`와 함께 사용하거나 리소스 그룹 템플릿 파일을 만들 이미지를 찾는 가장 쉽고 빠른 방법은 `azure vm image list` 명령을 호출하고 위치, 게시자 이름(대/소문자 구분 없음) 및 제안(제안을 알고 있는 경우)을 전달하는 것입니다. 예를 들어 다음 목록은 "UbuntuServer" 제안에 대한 게시자가 “Canonical"임을 알고 있는 경우의 간단한 예제일 뿐이며, 대부분의 목록은 매우 깁니다.

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240

**Urn** 열은 `azure vm quick-create`에 전달되는 양식입니다.

하지만 사용 가능한 항목은 아직 알 수 없습니다. 이 경우 `azure vm image list-publishers`를 사용하여 게시자를 검색한 다음 리소스 그룹에 사용할 데이터 센터 위치로 위치 프롬프트에 응답하여 이미지를 탐색할 수 있습니다. 예를 들어 다음은 미국 서부 위치의 모든 이미지 게시자를 나열합니다(소문자로 표시하고 표준 위치에서 공백을 제거하여 위치 인수 전달).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


이러한 목록은 매우 길 수 있으며 위의 예제 목록은 일부에 불과합니다. 미국 서부 위치의 이미지 게시자가 Canonical임을 알고 있다고 가정합니다. 이제 다음 예제와 같이 `azure vm image list-offers`를 호출하여 제안을 찾은 다음 프롬프트에 위치와 게시자를 전달할 수 있습니다.

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK

이제 미국 서부 지역에서 Canonical이 Azure에 **UbuntuServer** 제안을 게시함을 알고 있습니다. 하지만 SKU는 무엇입니까? SKU를 가져오려면 `azure vm image list-skus`를 호출하고 검색한 위치, 게시자 및 제안으로 프롬프트에 응답합니다.

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK

이제 이 정보로 맨 위쪽에 원래 호출을 호출하여 원하는 이미지를 정확하게 찾을 수 있습니다.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK

이제 사용할 이미지를 정밀하게 선택할 수 있습니다. 방금 찾은 URN 정보를 사용하여 가상 컴퓨터를 빠르게 만들거나 해당 URN 정보로 템플릿을 사용하려면 [Azure 리소스 관리자에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.

### 연습 동영상

이 비디오는 CLI를 사용하는 위의 단계를 보여줍니다.

[AZURE.VIDEO resource-groups-vm-searching-cli]


## PowerShell

PowerShell을 사용하여 `Switch-AzureMode AzureResourceManager`을(를) 입력합니다. 자세한 업데이트 및 구성 정보를 보려면 [리소스 관리를 이용한 Azure CLI 사용](xplat-cli-azure-resource-manager.md) 및 [Azure 리소스 관리자를 이용한 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

> [AZURE.NOTE]Azure PowerShell 모듈 1.0 이상에서 `Switch-AzureMode` cmdlet이 제거되었습니다. 해당 버전 이상에서 `Azure` 부분이 있는 아래 명령을 `AzureRm`으로 바꾸세요. Azure PowerShell 모듈 1.0 이전을 사용 중인 경우 아래 명령을 사용하지만 먼저 `Switch-AzureMode AzureResourceManager`해야 합니다.


Azure 리소스 관리자를 사용하여 새 가상 컴퓨터를 만들 때 다음 이미지 속성을 조합하여 이미지를 지정해야 하는 경우도 있습니다.

- 게시자
- 제안
- SKU

예를 들어 만들 가상 컴퓨터 유형을 지정해야 하는 리소스 그룹 템플릿 파일 또는 **Set-AzureVMSourceImage** PowerShell cmdlet에 대해 이러한 값이 필요합니다.

이러한 값을 결정해야 하는 경우 이미지를 탐색하여 다음의 값을 결정할 수 있습니다.

1. 이미지 게시자를 나열합니다.
2. 지정된 게시자에 제안을 나열합니다.
3. 지정된 제안에 SKU를 나열합니다.

PowerShell에서 이 작업을 수행하려면 먼저 Azure PowerShell의 리소스 관리자 모드로 전환합니다.

	Switch-AzureMode AzureResourceManager

위의 첫 번째 단계에 대해 이러한 명령을 사용하여 게시자를 나열합니다.

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

선택한 게시자 이름을 입력하고 이러한 명령을 실행합니다.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

선택한 제안 이름을 입력하고 이러한 명령을 실행합니다.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

**Get-AzureVMImageSku** 명령 표시에 새 가상 컴퓨터에 대한 이미지를 지정하는 데 필요한 모든 정보가 있습니다.

다음은 예제입니다.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName

	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

게시자가 "MicrosoftWindowsServer"인 경우:

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

	Offer
	-----
	WindowsServer

"WindowsServer" 제품인 경우:

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

이 목록에서 선택한 SKU 이름을 복사하면 이미지에 대한 게시자, 제안 및 SKU를 지정하는 데 필요한 **Set-AzureVMSourceImage** PowerShell cmdlet 또는 리소스 그룹 템플릿 파일에 대한 모든 정보가 제공됩니다.

### 연습 동영상

이 비디오는 PowerShell을 사용하는 위의 단계를 보여줍니다.

[AZURE.VIDEO resource-groups-vm-searching-posh]


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_1217_2015-->