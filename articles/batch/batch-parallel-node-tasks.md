<properties
	pageTitle="병렬 작업 사용으로 배치 노드 최대화 | Microsoft Azure"
	description="Azure 배치 풀의 각 노드에서 동시 작업을 실행하는 동안 더 적은 수의 계산 노드를 통해 효율성은 높이고 비용은 낮춥니다."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />
	
<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/22/2016"
	ms.author="marsma" />
	
# 동시 노드 작업으로 Azure Batch 계산 리소스 사용 극대화

이 문서에서는 Azure 배치 풀 내에서 각 계산 노드에 대해 두 개 이상의 작업을 동시에 실행하는 방법을 배웁니다. 풀의 계산 노드에서 동시 작업 실행을 활성화하여 풀 안에서 더 적은 수의 노드로 리소스 사용량을 극대화할 수 있습니다. 일부 작업의 경우 이렇게 하면 시간과 비용을 절약할 수 있습니다.

일부 시나리오에서는 단일 작업에 모든 노드 리소스를 할당 가능한 것이 장점으로 작용하지만 대부분의 상황에서는 여러 작업이 리소스를 공유하는 것이 유용합니다.

 - 작업이 데이터를 공유할 수 있을 때 **데이터 전송을 최소화**. 이 시나리오에서는 적은 수의 노드에 공유 데이터를 복사하고 각 노드에 병렬로 작업을 실행하여 데이터 전송 요금을 크게 줄일 수 있습니다. 특히 각 노드로 복사할 데이터를 지역 간에 전송해야 하는 경우 적용됩니다.

 - 실행 중 가변 시점에 단기간 동안 작업을 위해 대용량의 메모리가 필요한 경우 **메모리 사용량 최대화** 이러한 급증을 효율적으로 처리하기 위해 많은 메모리를 가진 적지만 크기가 큰 계산 노드를 사용할 수 있습니다. 이러한 노드에는 각 노드에서 병렬로 실행되는 여러 작업이 있지만 각 작업은 시간을 두고 노드의 풍부한 메모리를 활용합니다.

 - 풀 안에서 노드 내 통신이 필요한 경우 **노드 숫자 제한 완화**. 현재 노드 간 통신에 대해 구성된 풀은 계산 노드가 50개로 제한됩니다. 따라서 이런 풀의 각 노드가 병렬로 작업을 실행할 수 있다면 더 많은 수의 작업을 동시에 실행할 수 있습니다.

 - 최초로 컴퓨팅 환경을 Azure로 이동하는 경우 등 **온-프레미스 계산 클러스터 복제**. 해당 구성이 현재 계산 노드 당 여러 작업을 실행하는 경우 최대 노드 작업 수를 늘려서 기존 물리적 구성을 더 긴밀하게 미러링할 수 있습니다.

## 예제 시나리오

병렬 작업 실행의 장점을 보여주는 예제는 다음과 같습니다. 작업 응용 프로그램에 Standard\_D1 노드 크기가 적합하는 것과 같은 CPU 및 메모리 요구 사항이 있다고 가정해 봅니다. 하지만 주어진 시간에 작업을 실행하기 위해 이러한 노드가 1,000개 필요합니다.

1CPU 코어가 있는 Standard\_D1 노드를 사용하는 대신 각각 16코어가 있는 Standard\_D14 노드를 통해 병렬 작업 실행을 구현할 수 있습니다. 이 경우 *사용되는 노드 수가 1/16로 줄기 때문에* 필요한 노드 수는 1000개가 아니라 63개입니다. 이렇게 하면 각 노드에서 대규모 응용 프로그램 파일이나 참조 데이터가 필요할 때 작업 실행 시간과 효율성을 크게 높일 수 있습니다.

## 병렬 작업 실행 사용

풀 수준에서 병렬 작업 실행을 위한 배치 솔루션에 있는 계산 노드를 구성합니다. 배치 .NET 라이브러리를 사용하는 경우 풀을 만들 때 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 속성을 설정합니다. 배치 REST API를 사용하는 경우 풀을 만들 때 요청 본문에서 [maxTasksPerNode][maxtasks_rest] 요소를 설정합니다.

Azure 배치를 사용하면 노드 코어의 최대 4배수의 노드 마다 최대 작업을 설정할 수 있습니다. 예를 들어, 풀이 노드 크기 “Large”로 구성되었다면(4코어) `maxTasksPerNode`는 16으로 설정될 수 있습니다. 각 노드 크기에 대한 코어의 수의 자세한 내용은 [클라우드 서비스에 대한 크기](./../cloud-services/cloud-services-sizes-specs.md)를 참조하세요. 서비스 제한에 대한 자세한 내용은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

> [AZURE.TIP] 풀에 [자동 크기 조정 수식][enable_autoscaling]을 구성할 때는 `maxTasksPerNode` 값을 고려해야 합니다. 예를 들어, `$RunningTasks`를 평가하는 수식은 노드당 작업 수 증가에 크게 영향을 받을 수 있습니다. 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

## 작업 배분

풀 내의 계산 노드가 작업을 동시에 실행할 수 있는 경우 풀 내 노드에서의 작업 배분하려는 방법을 지정하는 것이 중요합니다.

[CloudPool.TaskSchedulingPolicy][task_schedule] 속성을 사용하여 풀의 모든 노드에서 작업을 균등하게 할당하도록 지정할 수 있습니다.("확산") 또는 작업이 풀의 다른 노드로 할당되기 전에 최대한 많은 작업이 각 노드에 할당되도록 지정할 수 있습니다.("압축")

이 기능이 얼마나 중요한지 확인하기 위해 위의 예에서 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 값이 16으로 구성된 Standard\_D14 노드 풀을 고려해 보겠습니다. [CloudPool.TaskSchedulingPolicy][task_schedule]가 *Pack*인 [ComputeNodeFillType][fill_type]으로 구성된 경우, 각 노드의 모든 16개 코어의 사용량을 최대화하며 [풀 자동 크기 조정](./batch-automatic-scaling.md)을 통해 풀에서 사용되지 않는 노드(작업이 할당되지 않은 노드)를 솎아냅니다. 리소스 사용량을 최소화하고 비용을 절감합니다.

## Batch .NET 예

이 [배치 .NET][api_net] API 코드 조각은 노드 당 최대 4개의 작업이 있는 네 개의 대규모 노드를 포함하는 풀을 만드는 요청을 나타냅니다. 풀의 다른 노드로 작업을 할당하기 전에 각 노드를 채울 정책을 예약하는 작업을 지정합니다. 배치 .NET API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [BatchClient.PoolOperations.CreatePool][poolcreate_net]을 참조하세요.

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Batch REST 예

이 [배치 REST][api_rest] API 코드 조각은 노드당 최대 4개의 작업이 있는 두 대규모 노드를 포함하는 풀을 만드는 요청을 나타냅니다. REST API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [풀을 계정에 추가][maxtasks_rest]를 참조하세요.

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE] 풀을 만들 때만 `maxTasksPerNode` 요소 및 [MaxTasksPerComputeNode][maxtasks_net] 속성을 설정할 수 있습니다. 풀이 이미 만들어진 후에는 수정될 수 없습니다.

## 샘플 프로젝트 탐색

GitHub의 [ParallelNodeTasks][parallel_tasks_sample] 프로젝트를 확인합니다. [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 사용을 보여주는 작업 코드 예제입니다.

이 C# 콘솔 응용 프로그램은 [배치 .NET][api_net] 라이브러리를 사용하여 하나 이상의 계산 노드가 있는 풀을 만듭니다. 해당 노드에서 구성 가능한 다양한 작업을 실행하여 변수 부하를 시뮬레이션합니다. 응용 프로그램에서 출력은 각 작업을 실행하는 노드를 지정합니다. 또한 응용 프로그램은 작업 매개 변수 및 기간의 요약을 제공합니다. 아래는 예제 응용 프로그램의 두 가지 다른 실행에서의 출력을 요약하는 부분입니다.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

샘플 응용 프로그램의 최초 실행에서는 풀 내 단일 노드 및 노드 당 작업의 기본값 설정을 보여주며 작업 길이는 30분이 넘습니다.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

샘플의 두 번째 실행은 작업 기간에서 크게 감소합니다. 풀이 노드 당 4개의 작업으로 구성되었기 때문에 이를 사용하면 병렬 작업 실행이 거의 1/4 시간에서 작업을 완료할 수 있습니다.

> [AZURE.NOTE] 위 요약의 작업 기간에는 풀 생성 시간이 포함되지 않았습니다. 위의 각 작업은 이전에 만든 풀에 제출되며, 제출 시점에 계산 노드는 *Idle* 상태입니다.

## 배치 탐색기 열 지도

Azure 배치 [샘플 응용 프로그램][github_samples] 중 하나인 [Azure 배치 탐색기][batch_explorer]에는 작업 실행을 시각화하는 *열 지도* 기능이 있습니다. [ParallelTasks][parallel_tasks_sample] 샘플 응용 프로그램을 실행할 때 열 지도 기능을 사용하면 각 노드에서 병렬 작업의 실행을 쉽게 시각화할 수 있습니다.

![배치 탐색기 열 지도][1]

*현재 4가지 작업을 실행하는 각 노드와 4개 노드의 풀을 보여 주는 배치 탐색기 열 지도*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0128_2016-->