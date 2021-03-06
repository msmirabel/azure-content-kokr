<properties
	pageTitle="Azure 배치 서비스 기본 사항 | Microsoft Azure"
	description="대규모 병렬 및 HPC 워크로드의 경우 Azure 배치 서비스를 사용하는 방법에 대해 알아봅니다."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="marsma"/>

# Azure 배치의 기본 사항

Azure 배치를 통해 클라우드에서 효율적으로 대규모 병렬 및 HPC(고성능 컴퓨팅) 응용 프로그램을 실행할 수 있습니다. 가상 컴퓨터의 관리된 컬렉션에서 실행되는 계산 집약적인 작업을 예약하는 플랫폼 서비스이며 작업의 요구를 충족하도록 계산 리소스의 규모를 자동으로 조정할 수 있습니다.

배치 서비스를 통해 대규모 배치 작업을 실행하는 Azure 계산 리소스를 프로그래밍 방식으로 정의합니다. 요청 시 또는 일정에 따라 이러한 작업을 실행할 수 있으며 HPC 클러스터, 개별 가상 컴퓨터, 가상 네트워크 또는 작업 스케줄러를 수동으로 관리할 필요가 없습니다.

## 배치에 대한 사용 사례

배치는 대용량의 유사 태스크를 실행하여 원하는 결과를 얻을 수 있는 *배치 처리* 또는 *배치 컴퓨팅*에 사용되는 관리된 Azure 서비스입니다. 배치 컴퓨팅은 대량의 데이터를 정기적으로 처리, 변환 및 분석하는 조직에서 가장 일반적으로 사용합니다.

배치는 본질적으로 병렬("처치 곤란 병렬"이라고도 함) 응용 프로그램 및 워크로드에서 잘 작동합니다. 본질적으로 병렬 워크로드는 여러 컴퓨터에서 동시에 작업을 수행하는 여러 태스크로 쉽게 분할됩니다.

![병렬 태스크][1]<br/>

일반적으로 이 기술을 사용하여 처리되는 워크로드의 몇 가지 예는 다음과 같습니다.

* 재무 위험 모델링
* 기후 및 수문학 데이터 분석
* 이미지 렌더링, 분석 및 처리
* 미디어 인코딩 및 트랜스코딩
* 유전자 서열 분석
* 엔지니어링 스트레스 분석
* 소프트웨어 테스트

또한 배치는 끝에 감소 단계가 있는 병렬 계산뿐만 아니라 [메시지 전달 인터페이스(MPI)](batch-mpi.md) 응용 프로그램 등 보다 복잡한 HPC 워크로드도 수행할 수 있습니다.

Azure에서 배치 및 다른 HPC 솔루션 간의 비교는 [배치 및 HPC 솔루션](batch-hpc-solutions.md)을 참조하세요.

>[AZURE.NOTE] 이 때에 배치는 Windows Server 기반 가상 컴퓨터에서 실행하는 워크로드만 지원합니다.

## 배치를 사용하는 개발

병렬 워크로드 처리를 위해 Azure 배치를 사용하는 솔루션을 빌드하는 경우 배치 API를 사용하여 프로그래밍 방식으로 수행합니다. 배치 API를 사용하여 계산 노드(가상 컴퓨터)의 풀을 만들고 관리하며 해당 노드에서 실행할 작업 및 태스크를 예약합니다. 사용자가 작성하는 클라이언트 응용 프로그램 또는 서비스는 배치 API를 사용하여 배치 서비스와 통신합니다. 조직의 대규모 워크로드를 효율적으로 처리하거나 고객에게 서비스 프런트 엔드를 제공할 수 있으므로 요청 시 또는 일정에 따라 작업 및 태스크를 단일, 수백 또는 수천 개의 노드에서 실행할 수 있습니다. 또한 [Azure 데이터 팩터리][data_factory]와 같은 도구에서 관리하는 대규모 워크플로의 일부로 배치를 사용할 수도 있습니다.

> [AZURE.TIP] 배치 API에서 제공하는 기능을 좀더 자세히 알아볼 준비가 되면 [Azure 배치 기능 개요](batch-api-basics.md)를 확인하세요.

### 필요한 Azure 계정

배치 솔루션을 개발할 경우 Microsoft Azure에서는 다음 계정을 사용합니다.

- **Azure 계정 및 구독** - Azure 구독이 없는 경우 [MSDN 구독자 혜택][msdn_benefits]을 활성화하거나 [무료 Azure 계정][free_account]을 등록할 수 있습니다. 계정을 만들면 기본 구독이 생성됩니다.

- **배치 계정** - 응용 프로그램이 배치 서비스와 상호 작용하는 경우 계정 이름, 계정의 URL 및 액세스 키가 자격 증명으로 사용됩니다. 풀, 계산 노드, 작업 및 태스크와 같은 모든 배치 리소스는 배치 계정과 연관됩니다. Azure 포털에서 [Azure 배치 계정을 만들고 관리](batch-account-create-portal.md)할 수 있습니다.

- **저장소 계정** - 배치는 [Azure 저장소][azure_storage]에 있는 파일에 대한 작업을 기본적으로 지원합니다. 태스크에서 실행하는 프로그램, 프로그램에서 처리하는 데이터, 태스크에서 생성하는 출력 데이터의 저장소 등 거의 모든 배치 시나리오에서 파일 준비를 위해 Azure 저장소를 사용합니다. 저장소 계정을 만들려면 [Azure 저장소 계정 정보](./../storage/storage-create-storage-account.md)를 참조하세요.

### 배치 개발 라이브러리 및 도구

Azure 배치를 사용하여 솔루션을 빌드하려면 배치 .NET 클라이언트 라이브러리, PowerShell을 사용하거나 직접 REST API 호출을 실행할 수도 있습니다. 이러한 도구 중 일부 또는 전부를 사용하여 배치에서 작업을 실행하는 클라이언트 응용 프로그램 및 서비스를 개발합니다.

- [배치 .NET][api_net] 클라이언트 라이브러리 - 대부분의 배치 솔루션은 [NuGet을 통해 제공][api_net_nuget]되는 배치 .NET 클라이언트 라이브러리를 사용하여 빌드됩니다.

- [배치 관리 .NET][api_net_mgmt] 클라이언트 라이브러리 - 마찬가지로 [NuGet을 통해 제공][api_net_mgmt_nuget]되며 배치 관리 .NET 클라이언트 라이브러리를 사용하여 클라이언트 응용 프로그램 또는 서비스에서 배치 계정을 프로그래밍 방식으로 관리합니다.

- [배치 REST][batch_rest] API - 배치 REST API는 배치 .NET 클라이언트 라이브러리와 동일한 모든 기능을 제공합니다. 사실 배치 .NET 라이브러리 자체는 배치 서비스와 상호 작용하기 위해 내부적으로 배치 REST API를 사용합니다.

- [배치 PowerShell cmdlet][batch_ps] - [Azure PowerShell](./../powershell-install-configure.md) 모듈의 Azure 배치 cmdlet을 사용하여 PowerShell과 함께 배치 리소스를 관리할 수 있습니다.

- [Azure 배치 탐색기][batch_explorer] - 배치 탐색기는 [GitHub에서 제공][github_samples]되는 배치 .NET 예제 응용 프로그램 중 하나입니다. 배치 솔루션을 개발 및 디버깅하는 동안 Visual Studio 2013 또는 2015로 WPF(Windows Presentation Foundation) 응용 프로그램을 빌드하고 사용하여 배치 계정의 리소스를 검색 및 관리합니다. 작업, 풀 및 태스크 세부 정보를 보고 계산 노드에서 파일을 다운로드하거나 배치 탐색기 인터페이스에서 몇 번의 클릭으로 얻을 수 있는 RDP(원격 데스크톱) 파일을 사용하여 원격으로 노드에 연결합니다.

- [Microsoft Azure 저장소 탐색기][storage_explorer] - 엄격히 말해 Azure 배치 도구는 아니지만 저장소 탐색기는 배치 솔루션을 개발 및 디버깅하는 동안 유용할 수 있는 또 다른 도구입니다.

## 시나리오: 병렬 워크로드 규모 확장

배치 서비스와 상호 작용하기 위해 배치 API를 사용하는 일반적인 솔루션에는 본질적으로 계산 노드 풀에서 병렬 작업(예: 3D 장면을 위한 이미지 렌더링)의 규모를 확장하는 것이 포함됩니다. 이 계산 노드 풀은 작업을 렌더링하기 위해 수십, 수백 또는 수천 개의 코어를 제공하는 "렌더 팜"일 수 있습니다.

다음 다이어그램에서는 병렬 워크로드를 실행하기 위해 배치를 사용하는 클라이언트 응용 프로그램 또는 호스티드 서비스가 있는 일반적인 배치 워크플로를 보여 줍니다.

![배치 솔루션 워크플로][2]

이 일반적인 시나리오에서 응용 프로그램 또는 서비스는 다음 단계를 수행하여 Azure 배치에서 계산 워크로드를 처리합니다.

1. 이러한 파일을 처리할 **입력 파일** 및 **응용 프로그램**을 Azure 저장소 계정에 업로드합니다. 입력 파일은 응용 프로그램이 처리할 모든 데이터가 될 수 있습니다(예: 금융 모델링 데이터 또는 트랜스코딩할 비디오 파일). 응용 프로그램 파일은 데이터를 처리하는 데 사용되는 모든 응용 프로그램일 수 있습니다(3D 렌더링 응용 프로그램 또는 미디어 트랜스코더).

2. 태스크를 실행할 가상 컴퓨터인 계산 노드로 구성된 배치 **풀**을 배치 계정에 만듭니다. [노드 크기](./../cloud-services/cloud-services-sizes-specs.md), 운영 체제, 노드가 풀에 참여할 때 설치할 응용 프로그램(1단계에서 업로드한 응용 프로그램)의 Azure 저장소 위치와 같은 속성을 지정합니다. 또한 [자동으로 규모를 확장](batch-automatic-scaling.md)하도록 풀을 구성할 수도 있습니다. 태스크에서 생성하는 워크로드에 맞게 풀에서 계산 노드 수를 동적으로 조정합니다.

3. 배치 **작업**을 만들어 계산 노드의 풀에서 워크로드를 실행합니다. 작업을 만들 때 배치 풀과 연결합니다.

4. 작업에 **태스크**를 추가합니다. 작업에 태스크를 추가하는 경우 배치 서비스는 풀의 계산 노드에서 실행할 태스크를 자동으로 예약합니다. 각 태스크는 입력 파일을 처리하기 위해 업로드한 응용 프로그램을 사용합니다.

    - 4a. 태스크가 실행되기 전에 처리할 데이터(입력 파일)을 할당된 계산 노드에 다운로드할 수 있습니다. 해당 노드에 응용 프로그램이 아직 설치되지 않은 경우(2단계 참조) 대신, 여기서 다운로드할 수 있습니다. 다운로드가 완료되면 할당된 노드에서 태스크가 실행됩니다.

5. 태스크가 실행됨에 따라 배치를 쿼리하여 작업 및 태스크의 진행 상태를 모니터링할 수 있습니다. 클라이언트 응용 프로그램 또는 서비스는 HTTPS를 통해 배치 서비스와 통신합니다. 수천 개의 계산 노드에서 실행되는 수천 개의 태스크를 모니터링할 수 있으므로 [배치 서비스를 효율적으로 쿼리](batch-efficient-list-queries.md)해야 합니다.

6. 태스크가 완료되면 결과 데이터를 Azure 저장소에 업로드할 수 있습니다. 계산 노드에서 직접 파일을 검색할 수도 있습니다.

7. 모니터링을 통해 작업의 태스크가 완료되었음을 감지하면 클라이언트 응용 프로그램 또는 서비스는 추가 처리 또는 평가를 위해 출력 데이터를 다운로드할 수 있습니다.

이 방법은 배치를 사용하는 한 가지 방법일 뿐이며 이 시나리오에서는 제공되는 기능 중 일부만 설명한다는 점을 명심하세요. 예를 들어, 각 계산 노드에서 [병렬로 여러 태스크](batch-parallel-node-tasks.md)를 실행할 수 있으며 [작업 준비 및 완료 태스크](batch-job-prep-release.md)를 사용하여 작업을 위한 노드를 준비하고 나중에 정리할 수 있습니다.

## 다음 단계

지금까지 배치 시나리오 예를 살펴보았으며 이제 서비스를 좀더 자세히 살펴보고 계산 집약적인 병렬 워크로드를 처리하는 데 어떻게 사용할 수 있는지 알아봅니다.

- [.NET용 Azure 배치 라이브러리 시작](batch-dotnet-get-started.md)에서는 위에서 설명한 기술을 수행하는 데 C# 및 배치 .NET 라이브러리를 사용하는 방법을 알아봅니다. 배치 서비스 사용 방법을 학습하는 과정의 첫걸음 중 하나입니다.

- 계산 집약적인 워크로드를 처리하기 위해 배치에서 제공하는 API 기능에 대한 자세한 내용은 [배치 기능 개요](batch-api-basics.md)를 확인하세요.

- 배치 탐색기 외에도 [GitHub의 기타 코드 샘플][github_samples]은 배치 .NET 라이브러리를 사용하여 수많은 배치 기능을 사용하는 방법을 보여 줍니다.

- 배치 작업을 학습하면서 사용 가능한 리소스는 [배치 학습 경로][learning_path]를 확인하세요.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0224_2016-->