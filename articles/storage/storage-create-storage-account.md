<properties
	pageTitle="Azure 포털에서 저장소 계정을 만들거나, 관리하거나, 삭제하는 방법 | Microsoft Azure"
	description="Azure 포털에서 새 저장소 계정을 만들고 계정 액세스 키를 관리하거나 저장소 계정을 삭제합니다. 표준 및 프리미엄 저장소 계정에 대해 알아봅니다."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/14/2016"
	ms.author="robinsh"/>


# Azure 저장소 계정 정보

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## 개요

Azure 저장소 계정은 Azure 저장소의 Azure Blob, 큐, 테이블 및 파일 서비스에 대한 액세스 권한을 제공합니다. 저장소 계정은 Azure 저장소 데이터 개체에 고유한 네임스페이스를 제공합니다. 기본적으로 계정에 대한 데이터는 사용자 계정 소유자에만 사용할 수 있습니다.

저장소 계정에는 다음과 같은 두 종류가 있습니다.

- 표준 저장소 계정에는 Blob, 테이블, 큐 및 파일 저장소가 포함됩니다.
- 프리미엄 저장소 계정은 현재 Azure 가상 컴퓨터 디스크만 지원합니다. 프리미엄 저장소의 자세한 개요는 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage.md)를 참조하세요.

## 저장소 계정 사용 비용

Azure 저장소 사용 비용은 저장소 계정에 따라 청구됩니다. 저장소 비용은 저장소 용량, 복제 체계, 저장소 트랜잭션 및 데이터 송신의 네 가지 요소를 기반으로 합니다.

- 저장소 용량은 데이터를 저장하는 데 사용하는 저장소 계정 서비스 단위가 어느 정도인지를 나타냅니다. 단순히 데이터를 저장하는 비용은 저장하는 데이터의 양과 복제 방법에 따라 결정됩니다.
- 복제에 따라 한 번에 유지 관리되는 데이터의 복사본 수 및 위치가 결정됩니다.
- 트랜잭션은 Azure 저장소에 대한 모든 읽기 및 쓰기 작업을 나타냅니다.
- 데이터 송신은 Azure 지역 외부에서 전송되는 데이터를 나타냅니다. 저장소 계정의 데이터에 동일한 지역에서 실행되지 않는 응용 프로그램이 액세스하는 경우 해당 응용 프로그램이 클라우드 서비스인지 다른 유형의 응용 프로그램인지 여부에 상관없이 데이터 송신 요금이 부과됩니다. Azure 서비스의 경우 데이터 송신 요금을 줄이거나 제거하기 위해 동일한 데이터 센터의 데이터와 서비스를 그룹화하는 조치를 취할 수 있습니다.  

[Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage) 페이지는 저장소 용량, 복제 및 트랜잭션에 대한 자세한 가격 정보를 제공합니다. [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지는 데이터 송신에 대한 자세한 가격 정보를 제공합니다.

저장소 계정 용량 및 성능 목표에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

> [AZURE.NOTE] Azure 가상 컴퓨터를 만드는 경우 배포 위치에 저장소 계정이 아직 없으면 해당 위치에서 자동으로 저장소 계정이 만들어집니다. 따라서 가상 컴퓨터 디스크에 대한 저장소 계정을 만들기 위해 아래 단계를 수행할 필요가 없습니다. 저장소 계정 이름은 가상 컴퓨터 이름을 기반으로 합니다. 자세한 내용은 [Azure 가상 컴퓨터 설명서](https://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

## 저장소 계정 끝점

Azure 저장소에 저장되는 모든 개체에는 고유한 URL 주소가 있습니다. 저장소 계정 이름은 해당 주소의 하위 도메인을 구성합니다. 하위 도메인과 도메인 이름의 조합은 각 서비스와 관련되며 저장소 계정의 *끝점*을 구성합니다.

예를 들어 저장소 계정의 이름이 *mystorageaccount*일 경우 저장소 계정의 기본 끝점은 다음과 같습니다.

- Blob 서비스: http://*mystorageaccount*.blob.core.windows.net

- 테이블 서비스: http://*mystorageaccount*.table.core.windows.net

- 큐 서비스: http://*mystorageaccount*.queue.core.windows.net

- 파일 서비스: http://*mystorageaccount*.file.core.windows.net

저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 끝점에 추가하여 작성됩니다. 예를 들어 Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

사용자 지정 도메인 이름을 구성하여 저장소 계정에서 사용할 수도 있습니다. 클래식 저장소 계정의 경우 자세한 내용은 [Blob 저장소 끝점에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)을 참조하세요. ARM 저장소 계정의 경우 이 기능은 [Azure 포털](https://portal.azure.com)에 아직 추가되지 않았지만 PowerShell과 함께 구성할 수 있습니다. 자세한 내용은 [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) cmdlet을 참조하세요.

## 저장소 계정 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기** -> **데이터 + 저장소** -> **저장소 계정**을 선택합니다.

3. 배포 모델을 다음과 같이 선택합니다. **리소스 관리자** 또는 **클래식** **리소스 관리자**는 권장되는 배포 모델입니다. 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md)를 참조하세요.

4. 저장소 계정의 이름을 입력합니다.

	> [AZURE.NOTE] 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
	>  
	> 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 선택한 저장소 계정 이름이 이미 사용된 경우 Azure 포털에 표시됩니다.

	저장소 계정 이름이 Azure 저장소에서 개체를 처리하는 데 사용되는 방법에 대한 자세한 내용은 아래 [저장소 계정 끝점](#storage-account-endpoints)을 참조하세요.

5. 만들 저장소 계정 형식을 지정합니다. 저장소 계정 형식은 저장소 계정의 복제 방식 및 표준 저장소 계정 또는 프리미엄 저장소 계정 여부를 결정합니다.

	기본 저장소 계정 형식은 **Standard-RAGRS**이며 이는 읽기 액세스 지역 중복 복제를 사용하는 표준 저장소 계정입니다. 이 저장소 계정 형식은 주 지역에서 수백 마일이 떨어져 있는 보조 지역에 복제되고 보조 지역에 읽기 액세스를 제공합니다.

	Azure 저장소 복제 옵션에 대한 자세한 내용은 아래의 [Azure 저장소 복제](storage-redundancy.md)를 참조하세요. 표준 및 프리미엄 저장소 계정에 대한 자세한 내용은 [Microsoft Azure 저장소 소개](storage-introduction.md) 및 [프리미엄 저장소: Azure 가상 컴퓨터 워크로드에 대한 고성능 저장소](storage-premium-storage.md)를 참조하세요.

6. 저장소 계정에 대한 진단을 사용하려는지 여부를 나타냅니다. 진단은 저장소 분석 로깅 및 메트릭을 포함합니다.

7. Azure 구독이 두 개 이상인 경우 **구독** 필드가 표시됩니다. 새 저장소 계정을 만들려는 구독을 선택합니다.

8. 새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 관리](../azure-portal/resource-group-portal.md)를 참조하세요.

9. 저장소 계정에 대한 지리적 위치를 선택합니다.

10. **만들기**를 클릭하여 저장소 계정을 만들 수 있습니다.

## 저장소 액세스 키 다시 관리

저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

> [AZURE.NOTE] 저장소 액세스 키를 다른 사람과 공유하지 않는 것이 좋습니다. 액세스 키를 제공하지 않고 저장소 리소스에 대한 액세스를 허용하려는 경우에는 *공유 액세스 서명*을 사용할 수 있습니다. 공유 액세스 서명에서는 정의된 간격으로 지정된 권한을 사용하여 계정의 리소스에 액세스할 수 있습니다. 자세한 내용은 [공유 액세스 서명: SAS 모델 이해](storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

### 저장소 액세스 키 보기 및 복사

[Azure 포털](https://portal.azure.com)에서 저장소 계정으로 이동하고 **키** 아이콘을 클릭하여 계정 선택키를 보고, 복사하며 다시 생성합니다. 또한 **선택키** 블레이드는 응용 프로그램에서 사용하기 위해 복사할 수 있는 기본 및 보조 키를 사용하여 미리 구성된 연결 문자열을 포함합니다.

### 저장소 액세스 키 다시 생성

저장소 연결을 안전하게 유지하는 데 도움이 되도록 정기적으로 저장소 계정의 액세스 키를 변경하는 것이 좋습니다. 두 개의 액세스 키가 할당되므로 하나의 액세스 키를 다시 생성하는 동안 다른 액세스 키를 사용하여 저장소 계정에 대한 연결을 유지할 수 있습니다.

> [AZURE.WARNING] 액세스 키를 다시 생성하면 가상 컴퓨터, 미디어 서비스 및 저장소 계정에 종속된 모든 응용 프로그램에 영향을 줍니다. 액세스 키를 사용하여 저장소 계정에 액세스하는 모든 클라이언트에서 새 키를 사용하도록 업데이트해야 합니다.

**가상 컴퓨터** - 실행 중인 가상 컴퓨터가 저장소 계정에 포함된 경우 액세스 키를 다시 생성한 후 모든 가상 컴퓨터를 다시 배포해야 합니다. 다시 배포하지 않으려면 액세스 키를 다시 생성하기 전에 가상 컴퓨터를 종료합니다.

**미디어 서비스** - 저장소 계정에 종속된 미디어 서비스가 있는 경우 키를 다시 생성한 후 미디어 서비스와 액세스 키를 다시 동기화해야 합니다.

**응용 프로그램** - 저장소 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 키를 다시 생성하면 연결이 끊어집니다.

저장소 액세스 키를 회전하기 위한 프로세스는 다음과 같습니다.

1. 저장소 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 연결 문자열을 업데이트합니다.

2. 저장소 계정의 기본 액세스 키를 다시 생성합니다. **선택키** 블레이드에서 **키1 다시 생성**을 클릭하고 **예**를 클릭하여 새 키를 생성하려는 것을 확인합니다.

3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

4. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

## 저장소 계정 삭제

더 이상 사용하지 않는 저장소 계정을 제거하려면 [Azure 포털](https://portal.azure.com)의 저장소 계정으로 이동하고 **삭제**를 클릭합니다. 저장소 계정 삭제는 계정의 모든 데이터를 포함한 전체 계정을 삭제합니다.

> [AZURE.WARNING] 삭제된 저장소 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 또한 해당 계정의 리소스에 대해 true를 유지합니다. Blob, 테이블, 큐 또는 파일을 삭제하면 영구적으로 삭제됩니다.

Azure 가상 컴퓨터와 연결된 저장소 계정을 삭제하려면 먼저 가상 컴퓨터 디스크가 삭제되었는지 확인해야 합니다. 가상 컴퓨터 디스크를 먼저 삭제하지 않은 경우 저장소 계정을 삭제하려고 할 때 다음과 유사한 오류 메시지가 나타납니다.

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

저장소 계정이 클래식 배포 모델을 사용하는 경우 [클래식 Azure 포털](https://manage.windowsazure.com)의 이러한 단계를 수행하여 가상 컴퓨터 디스크를 제거할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)로 이동합니다.
2. 가상 컴퓨터 탭으로 이동합니다.
3. 디스크 탭을 클릭합니다.
4. 데이터 디스크를 선택한 다음 디스크 삭제를 클릭합니다.
5. 디스크 이미지를 삭제하려면 이미지 탭으로 이동하여 계정에 저장된 모든 이미지를 삭제합니다.

자세한 내용은 [Azure 가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

## 다음 단계

- [Azure 저장소 복제](storage-redundancy.md)
- [Azure 저장소 연결 문자열 구성](storage-configure-connection-string.md)
- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하세요.

<!---HONumber=AcomDC_0218_2016-->