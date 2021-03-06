<properties
   pageTitle="Reliable Services 프로그래밍 모델 고급 사용법 | Microsoft Azure"
   description="서비스에 유연성을 더해주는 서비스 패브릭의 신뢰할 수 있는 서비스 프로그래밍 모델 고급 사용법에 대해 알아보세요."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# 신뢰할 수 있는 서비스 프로그래밍 모델 고급 사용법
Azure 서비스 패브릭은 신뢰할 수 있는 상태 비저장 및 상태 저장 서비스의 작성과 관리를 단순화합니다. 이 가이드는 서비스에 대한 더 많은 제어와 유연성을 확보할 수 있는 신뢰할 수 있는 서비스 프로그래밍 모델의 고급 사용법에 대해 설명합니다. 이 가이드를 읽기 전에 [신뢰할 수 있는 서비스 프로그래밍 모델](service-fabric-reliable-services-introduction.md)에 대해 숙지하세요.

## 상태 비저장 서비스에 대한 기본 클래스
StatelessService 기본 클래스는 대부분의 상태 비저장 서비스에 충분한 RunAsync() 및 CreateCommunicationListener()를 제공합니다. StatelessServiceBase 클래스는 StatelessService의 기반이 되고 추가 서비스 수명 주기 이벤트를 노출합니다. 추가 제어 또는 유연성이 필요한 경우 StatelessServiceBase에서 파생시킬 수 있습니다. 자세한 내용은 [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) 및 [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx)에서 개발자 참조 설명서를 참조하세요.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize는 서비스 패브릭에서 호출하는 첫 번째 메서드입니다. 서비스 이름, 파티션 ID, 인스턴스 ID 및 코드 패키지 정보와 같은 서비스 초기화 정보가 제공됩니다. 여기에서는 복잡한 처리를 수행하지 않습니다. OnOpenAsync에서는 긴 초기화를 수행해야 합니다.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` 상태 비저장 서비스 인스턴스를 사용하려고 할 때 OnOpenAsync가 호출됩니다. 이때 확장된 서비스 초기화 작업을 시작할 수 있습니다.

- `Task OnCloseAsync(CancellationToken)` 상태 비저장 서비스 인스턴스가 정상적으로 종료되려고 할 때 OnCloseAsync가 호출됩니다. 이는 서비스의 코드를 업그레이드할 때, 로드 균형 조정으로 인해 서비스 인스턴스가 이동할 때 또는 일시적인 오류가 감지되었을 때 발생할 수 있습니다. OnCloseAsync를 사용하여 모든 리소스를 안전하게 닫거나, 백그라운드 프로세싱을 중지하거나, 외부 상태 저장을 완료하거나, 기존 연결을 종료할 수 있습니다.

- `void OnAbort()` 상태 비저장 서비스 인스턴스가 정상적으로 종료되려고 할 때 OnAbort가 호출됩니다. 이는 일반적으로 노드에서 영구 오류가 감지되거나, 내부 오류로 인해 서비스 패브릭에서 서비스 인스턴스 수명 주기를 안정적으로 관리할 수 없을 때 호출됩니다.

## 상태 저장 서비스에 대한 기본 클래스
StatefulService 기본 클래스는 대부분의 상태 저장 서비스에 충분합니다. 상태 비저장 서비스와 유사한 StatefulServiceBase 클래스는 StatefulService의 기반이 되고 추가 서비스 수명 주기 이벤트를 노출합니다. 또한 이를 사용하여 신뢰할 수 있는 사용자 지정 상태 제공자를 제공하고 필요에 따라 보조 복제본에서 통신 수신기를 지원할 수 있습니다. 추가 제어 또는 유연성이 필요한 경우 StatefulServiceBase에서 파생시킬 수 있습니다. 자세한 내용은 [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) 및 [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx)에서 개발자 참조 설명서를 참조하세요.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` 상태 저장 서비스가 역할을 변경할 경우(예: 주 또는 보조) OnChangeRoleAsync가 호출됩니다. 주 복제본에는 쓰기 상태가 지정됩니다(신뢰할 수 있는 컬렉션을 만들고 쓰도록 허용). 보조 복제본에는 읽기 상태가 지정됩니다(신뢰할 수 있는 기존 컬렉션에서 읽기만 가능). 보조 복제본에서 읽기 전용 유효성 검사, 보고서 생성 또는 데이터 마이닝을 수행하는 등의 역할 변경에 대한 응답으로 백그라운드 작업을 시작하거나 업데이트할 수 있습니다.

- `IStateProviderReplica CreateStateProviderReplica()` 상태 저장 서비스는 신뢰할 수 있는 상태 제공자가 있어야 합니다. StatefulService는 신뢰할 수 있는 컬렉션(예: 사전 및 큐)을 제공하는 ReliableStateManager 클래스를 사용합니다. 이 메서드를 재정의하여 ReliableStateManagerConfiguration을 해당 생성자에게 전달하는 방식으로 ReliableStateManager 클래스를 구성할 수 있습니다. 이렇게 하면 사용자 지정 상태 직렬 변환기를 제공하고 데이터가 손실되었을 경우 수행할 작업을 지정하며 복제자/상태 제공자를 구성할 수 있습니다.

또한 StatefulServiceBase는 StatelessServiceBase와 동일한 4개의 수명 주기 이벤트와 동일한 의미 체계 및 동일한 사용 사례를 제공합니다.

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 다음 단계
서비스 패브릭과 관련된 고급 항목을 보려면 다음 문서를 참조하세요.

- [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md)

- [서비스 패브릭 상태 소개](service-fabric-health-introduction.md)

- [문제 해결을 위해 시스템 상태 보고서 사용](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [배치 제약 조건 개요](service-fabric-placement-constraint.md)

<!---HONumber=AcomDC_0204_2016-->