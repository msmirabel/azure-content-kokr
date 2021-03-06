<properties
	pageTitle="포털에서 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 포털에서 필드 정의를 입력하여 클라우드 호스트된 검색 서비스인 Azure 검색에 인덱스를 추가합니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/08/2016"
	ms.author="heidist"/>

# Azure 포털에서 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST API](search-create-index-rest-api.md)

Azure 검색의 인덱스를 Azure 포털에서 만들어 신속하게 프로토타이핑할 수 있습니다. 포털은 개념 증명 테스트에 유용하지만 이를 사용하여 서비스에 배포된 인덱스의 스키마 정의 및 리소스 사용량을 확인할 수도 있습니다.

이 작업을 완료하려면 Azure 검색 서비스를 사용할 준비가 되어 있어야 합니다. 설정하는 데 도움이 필요한 경우 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. Azure 검색 서비스의 서비스 대시보드를 엽니다. 대시보드를 찾을 수 있는 몇 가지 방법이 있습니다.
	- 표시줄에서 **홈**을 클릭합니다. 홈 페이지에는 구독에 포함된 모든 서비스에 대한 타일이 있습니다. 타일을 클릭하면 서비스 대시보드가 열립니다.
	- 표시줄에서 **모두 찾아보기** > **필터 기준** > **서비스 검색**을 클릭하여 목록에서 검색 서비스를 찾습니다.

3. 서비스 대시보드의 위쪽에 **인덱스 추가** 명령이 포함된 명령 모음이 표시됩니다.

	가격 책정 계층을 선택합니다. 무료 버전을 사용하는 경우 최대 3개의 인덱스를 사용할 수 있습니다. 공간을 확보하기 위해 하나를 삭제해야 할 수도 있습니다.

     ![][1]

4. 인덱스를 삭제하려면 해당 인덱스를 클릭하여 블레이드를 엽니다. **삭제**를 클릭합니다.

     ![][2]

5. 포털에서 새 인덱스를 만들려면 **인덱스 추가**를 클릭하고 이름(예: *hotels*)을 지정합니다.

	인덱스를 만드는 데 1분 정도 걸릴 수 있지만 작업 준비가 완료되면 인덱스 목록에 표시됩니다.

6. **hotels**를 클릭하여 인덱스 정의 블레이드를 엽니다.

	포털에서 인덱스를 만들 때에는 필수 필드(id)가 자동으로 만들어집니다. 이는 각 문서를 고유하게 식별하는 데 사용되는 키 필드입니다. 키당 하나의 필드만 있으며(복합 키는 없음), 항상 문자열입니다.

	키 필드의 이름을 바꾸려면 인덱스를 만드는 동안 지금 이 단계에서 수행해야 합니다. 인덱스를 만든 후에는 필드 이름을 바꿀 수 없습니다.

	![][3]

7. 필드 이름을 편집하려면 필드 목록에서 오른쪽 화살표를 클릭합니다.

8. **id**를 **hotelId**로 바꿉니다.

9. 각 블레이드(필드 및 인덱스)에서 **확인**을 클릭하여 인덱스를 만듭니다.

## 필드 추가

Azure 검색에서는 searchable, facetable 및 filterable과 같은 인덱스 특성이 기본적으로 사용하도록 설정됩니다. 일반적으로 이러한 특성을 설정할 때는 의미 없는 검색 동작(예: 설명 정렬 또는 패싯)을 끕니다.

그러나 포털은 다릅니다. 포털에서는 필드별로 적용되는 모든 동작을 선택할 수 있도록 검색 동작이 기본적으로 해제됩니다.

1. **필드 추가/편집**을 클릭하여 필드를 더 추가합니다. 이 연습에서는 [Azure 검색에서 Fiddler를 사용하는 방법](search-fiddler.md) 문서에 설명된 *hotels* 인덱스를 다시 만듭니다.

	![][4]

2. 필드를 추가하고 구성하여 스키마를 완료합니다.

	![][5]

	필드 이름 및 형식에 대한 참조 정보는 [이름 지정 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx) 및 [지원되는 데이터 형식](https://msdn.microsoft.com/library/azure/dn798938.aspx)을 검토하세요.

    인덱스 특성은 다음 항목으로 구성됩니다.

	- **검색 가능**은 검색 결과에서 필드를 반환할 수 있는지 여부를 지정합니다.
	- **필터링 가능**을 선택하면 필드를**$filter** 쿼리에 사용할 수 있습니다.
	- **Sortable**을 선택하면 필드를 정렬 옵션으로 사용할 수 있습니다.
	- **Facetable**을 선택하면 자기 주도형 필터링을 위해 필드를 패싯 탐색 구조에 사용할 수 있습니다. 일반적으로 여러 문서(예를 들어 단일 제품 또는 서비스 범주에 속하는 여러 문서)를 그룹화하는 데 사용할 수 있는 반복되는 값을 포함한 필드는 패싯으로 가장 적합합니다.
	- **키**는 문서 조회에 사용하는 각 문서의 고유 ID입니다. 모든 인덱스에는 하나의 키가 있어야 합니다. 필드 한 개만 키가 될 수 있으며, 이 필드를 Edm.String으로 설정해야 합니다.
	- **검색 가능**은 필드를 전체 텍스트 검색 가능으로 표시합니다.

3. 원하지 않는 필드를 제거하려면 해당 필드를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

4. **확인**을 클릭하여 방금 정의한 인덱스를 저장한 다음 인덱스 추가 페이지에서 **확인**을 클릭하여 인덱스를 만듭니다.


## 다음 단계

인덱스가 정의되었지만 문서를 로드할 때까지 사용할 수는 없습니다. 테스트 목적으로 호텔 인덱스를 다시 생성한다고 가정하면, [Azure 검색과 함께 Fiddler를 사용하는 방법](search-fiddler.md)의 문서 로드 섹션에 나오는 지침을 사용하여 [Fiddler](search-fiddler.md)의 해당 색인에 대해 소수의 문서를 쉽게 로드할 수 있습니다. 그런 다음 해당 문서의 나머지 단계에 따라 일부 쿼리를 실행할 수 있습니다.

기본 인덱스에 익숙해지면 언어 분석기 또는 확인기를 추가하여 다국어 지원 또는 사전 입력 제안을 추가하는 것이 좋습니다. 두 기능 모두 인덱스 스키마에서 지정됩니다. 자세한 내용은 [언어 지원](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) 및 [인덱스 만들기](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=AcomDC_0211_2016-->