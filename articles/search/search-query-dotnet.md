<properties
    pageTitle=".NET SDK를 사용하여 Azure 검색 인덱스 쿼리 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 검색에서 검색 쿼리를 작성하고 검색 매개 변수를 사용하여 검색 결과를 필터링하고 정렬합니다."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# .NET SDK를 사용하여 Azure 검색 인덱스 쿼리
> [AZURE.SELECTOR]
- [개요](search-query-overview.md)
- [검색 탐색기](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST (영문)](search-query-rest-api.md)

이 문서는 [Azure 검색 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 인덱스를 쿼리하는 방법을 보여줍니다. 이 연습을 시작하기 전에 [Azure 검색 인덱스를 만들고](search-create-index-dotnet.md) [데이터로 채워야](search-import-data-dotnet.md) 합니다.

이 문서의 모든 샘플 코드는 C#으로 작성되었습니다. [GitHub에](http://aka.ms/search-dotnet-howto) 전체 소스 코드가 있습니다.

## I. Azure 검색 서비스의 쿼리 API 키 식별
Azure 검색 인덱스를 만들었으므로 .NET SDK를 사용하여 쿼리를 발급할 준비가 되었습니다. 먼저 프로비전한 검색 서비스에 대해 생성된 쿼리 API 키 중 하나가 있어야 합니다. .NET SDK는 서비스에 대한 모든 요청에 대해 이 API 키를 전송합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. 서비스의 API 키를 찾으려면 [Azure 포털](https://portal.azure.com/)에 로그인해야 합니다.
2. Azure 검색 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

  - 기본 및 보조 *관리 키*는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
  - *쿼리 키*는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스를 쿼리하기 위해 쿼리 키 중 하나를 사용할 수 있습니다. 또한 쿼리에 관리 키를 사용할 수 있지만 [최소 권한의 원칙](https://en.wikipedia.org/wiki/Principle_of_least_privilege)에 따라 응용 프로그램 코드에 쿼리 키를 사용해야 합니다.

## II. SearchIndexClient 클래스의 인스턴스 만들기
Azure 검색 .NET SDK로 쿼리를 발급하려면 `SearchIndexClient` 클래스의 인스턴스를 만들어야 합니다. 이 클래스에는 몇 가지 생성자가 있습니다. 그 중에 하나는 검색 서비스 이름, 인덱스 이름 및 `SearchCredentials` 개체를 매개 변수로 사용합니다. `SearchCredentials`은 API 키를 래핑합니다.

아래 코드에서는 응용 프로그램의 구성 파일(`app.config` 또는 `web.config`)에 저장된 검색 서비스 이름 및 API 키에 대한 값을 사용하여 ([.NET SDK를 사용하여 Azure 검색 인덱스 만들기](search-create-index-dotnet.md)에서 만든) "호텔" 인덱스에 새로운 `SearchIndexClient`을 만듭니다.

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`에는 `Documents` 속성이 있습니다. 이 속성은 Azure 검색 인덱스를 쿼리하는 데 필요한 모든 메서드를 제공합니다.

## III. 인덱스 쿼리
.NET SDK로 검색하는 작업은 `SearchIndexClient`에서 `Documents.Search` 메서드를 호출하는 것 만큼 간단합니다. 이 메서드는 쿼리를 구체화하는 데 사용할 수 있는 `SearchParameters` 개체와 함께 검색 텍스트를 비롯한 몇 가지 매개 변수를 사용합니다.

#### 쿼리 유형

Azure 검색은 매우 강력한 쿼리를 만드는 다양한 옵션을 제공합니다. 사용할 두 가지 기본 유형 쿼리는 `search` 및 `filter`입니다. `search` 쿼리는 인덱스의 _검색 가능한_ 모든 필드에서 하나 이상의 단어를 검색하고 Google 또는 Bing과 같은 검색 엔진이 작동하리라고 예상되는 방법으로 작동합니다. `filter` 쿼리는 인덱스의 _필터링 가능한_ 모든 필드에 걸쳐 부울 식을 계산합니다. `search` 쿼리와 달리 `filter` 쿼리는 필드의 정확한 내용과 일치하며 이는 문자열 필드에서 대/소문자를 구분한다는 것을 의미합니다.

검색 및 필터를 함께 또는 별도로 사용할 수 있습니다. 이러한 작업을 함께 사용하는 경우 필터가 전체 인덱스에 먼저 적용된 다음 필터의 결과에 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

검색 및 필터는 모두 `Documents.Search` 메서드를 사용하여 수행됩니다. 검색 쿼리는 `searchText` 매개 변수에 전달될 수 있는 반면 필터 식은 `SearchParameters` 클래스의 `Filter` 속성에 전달될 수 있습니다. 검색하지 않고 필터링하려면 `searchText` 매개 변수에 `"*"`를 전달합니다. 필터링하지 않고 검색하려면 `Filter` 속성을 설정하지 않고 그대로 두거나 `SearchParameters` 인스턴스에 전달하지 않아야 합니다.

필터 식에 대한 구문은 [OData 필터 언어](https://msdn.microsoft.com/library/azure/dn798921.aspx)의 하위 집합입니다. 검색 쿼리의 경우 [간단한 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx) 또는 [Lucene 쿼리 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 사용할 수 있습니다.

쿼리의 다른 매개 변수에 대해 자세히 알아보려면 [MSDN에서 .NET SDK 참조](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters.aspx)를 참조하세요. 아래에는 몇 가지 예제 쿼리가 있습니다.

#### 예제 쿼리

다음 샘플 코드는 [.NET SDK를 사용하여 Azure 검색 인덱스 만들기](search-create-index-dotnet.md#DefineIndex)에 정의된 "호텔" 인덱스를 쿼리하는 몇 가지 방법을 보여줍니다. 검색 결과와 함게 반환된 문서가 `Hotel` 클래스의 인스턴스이며 [.NET SDK를 사용하여 Azure 검색에서 데이터 가져오기](search-import-data-dotnet.md#HotelClass)에 정의되었습니다. 샘플 코드는 `WriteDocuments` 메서드를 사용하여 콘솔에 검색 결과를 출력합니다. 이 메서드는 다음 섹션에 설명되어 있습니다.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters = 
    new SearchParameters() 
    { 
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV. 검색 결과 처리
`Documents.Search` 메서드는 쿼리의 결과를 포함하는 `DocumentSearchResult` 개체를 반환합니다. 이전 섹션의 예제에서는 콘솔에 검색 결과 출력하기 위해 `WriteDocuments`이라는 메서드를 사용했습니다.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

다음은 이전 섹션의 쿼리 모양에 대한 결과이며 "호텔" 인덱스가 [.NET SDK를 사용하여 Azure 검색에서 데이터 가져오기](search-import-data-dotnet.md)의 샘플 데이터로 채워진다고 가정합니다.

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

위의 샘플 코드는 콘솔을 사용하여 검색 결과를 출력합니다. 마찬가지로 고유한 응용 프로그램에 검색 결과를 표시해야 합니다. ASP.NET MVC 기반 웹 응용 프로그램에서 검색 결과를 렌더링하는 방법의 예는 [GitHub에서 이 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)을 참조하세요.

<!----HONumber=AcomDC_0309_2016-->