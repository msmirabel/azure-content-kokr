<properties
	pageTitle="Azure 검색에서 데이터 가져오기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색에서 인덱스에 데이터를 업로드하는 방법"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Azure 검색으로 데이터 가져오기
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Azure 검색에서 서비스는 인덱스를 처리하거나 쿼리를 실행하거나 검색 결과를 공식화하는 데 사용되는 문서 및 정보를 제공하는 지속된 데이터(인덱스)에 대해 작동합니다. 인덱스를 채우려면 데이터를 로드하기 위해 푸시 또는 끌어오기 모델을 사용할 수 있습니다.

가져오기 전에 인덱스가 이미 존재해야 합니다. 자세한 내용은 [Azure 검색에 대 한 인덱스](search-what-is-an-index.md)를 참조하세요.

##인덱스에 데이터 푸시

이 방법은 인덱스 스키마에 맞는 기존 데이터 집합을 가져오고 검색 서비스에 게시하는 것을 의미합니다. 대기 시간 요구 사항이 매우 낮은 응용 프로그램의 경우(예: 재고 데이터베이스와 동기화되도록 작업을 검색해야 하는 경우) 푸시 모델이 유일한 옵션입니다.

인덱스에 데이터를 푸시하려면 REST API 또는.NET SDK를 사용할 수 있습니다. 현재 포털을 통해 데이터를 푸시할 수 있는 도구가 없습니다.

개별적으로 또는 배치에서 문서를 업로드하기 때문에 이 접근 방식은 끌어오기 모델 보다 더 유연합니다.(최대 배치 당 1000 또는 16MB, 모두 제한됨)

##데이터 끌어오기(탐색) 

끌어오기 모델은 지원되는 데이터 소스를 탐색하고 사용자에 대한 인덱스를 로드합니다. Azure 검색에서 이 기능은 *인덱서* 를 통해 구현되며 Azure SQL 데이터베이스, DocumentDB 및 Azure VM의 SQL Server에 사용할 수 있습니다. [인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)를 참조하여 Azure SQL 데이터의 업로드에 대해 알아봅니다.

인덱스에 데이터를 끌어오려면 포털, REST API 또는.NET SDK를 사용할 수 있습니다.

##데이터 집합 요구 사항

JSON 구조로 세우는 스키마 및 데이터 집합을 공식화하는 하는 경우 업로드하는 데이터의 형식에 제한이 없습니다.

데이터는 사용자 지정 응용 프로그램이 만들거나 사용하는 데이터베이스 또는 데이터 원본에서 비롯되어야 합니다. 예를 들어, 응용 프로그램이 온라인 소매 카탈로그인 경우 Azure 검색에 대해 만드는 인덱스는 제품 재고로부터 데이터 또는 응용 프로그램을 지원하는 판매 데이터베이스로부터 가져와야 합니다.

데이터 집합은 단일 테이블, 뷰, blob 컨테이너 또는 기타로부터 가져옵니다. Azure 검색에 데이터를 제공하는 데이터베이스 또는 noSQL 응용 프로그램에 데이터 구조를 만들어야 할 수 있습니다. 또는 Azure SQL 데이터베이스나 DocumentDB와 같은 특정 데이터 원본의 경우, Azure 검색에 데이터를 업로드하기 위해 데이터에 대 한 외부 테이블, 뷰 또는 blob 컨테이너를 크롤링하는 인덱서를 만들 수 있습니다.

##데이터 가져오기 방법 선택

|조건|권장되는 방법|
|------------|---------------|
|거의 실시간 데이터 동기화|인덱스에 업데이트를 푸시할 .NET 또는 REST API 코드입니다. 데이터 수집에 대한 끌어오기 접근 방식은 예약된 작업으로, 기본 데이터 원본의 빠른 변경 속도를 따라 잡을 만큼 빠르지 않습니다|
|Azure SQL 데이터베이스, DocumentDB 또는 Azure VM의 SQL Server|인덱서는 특정 데이터 원본 유형으로 분류됩니다. 기본 데이터 원본이 지원되는 데이터 원본 형식인 경우 인덱서는 인덱스를 로드하는 가장 쉬운 방법입니다. 데이터 새로 고침을 시간별 간격으로 예약할 수 있습니다. 포털 또는 코드에서 인덱서를 구성할 수 있습니다.|
|예약된 데이터 새로 고침|인덱서를 사용합니다(위 참조).|
|코드가 없는 프로토타입 또는 편집|포털에는 인덱서를 구성하는 데이터 가져오기 마법사가 있어, 기본 데이터 집합에 충분한 정보가 있는 경우 예비 스키마를 생성합니다. 마법사에는 예약된 데이터 새로 고침을 설정에 대한 옵션이 있습니다. 필요에 따라 언어 분석기 또는 CORS 옵션을 추가할 수 있습니다. 몇 가지 단점도 있습니다. 점수 매기기 프로필을 추가하거나 코드에서 사용하기 위해 포털에서 만든 스키마를 JSON 파일로 내보낼 수 없습니다.| 

<!---HONumber=AcomDC_0224_2016-->