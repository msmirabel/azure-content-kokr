<properties 
	pageTitle="Azure SQL 탄력적인 확장 FAQ | Microsoft Azure" 
	description="Azure SQL 데이터베이스의 탄력적인 확장에 대한 질문과 대답을 제공합니다." 
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
	ms.date="02/01/2016" 
	ms.author="sidneyh"/>

# 탄력적 데이터베이스 도구 FAQ 

#### 분할 및 분할 안 함 키당 단일 테넌트가 있는 경우 스키마 정보에 대한 분할 키를 채우려면 어떻게 해야 하나요?

스키마 정보 개체는 시나리오를 분할 병합하는 데만 사용됩니다. 응용 프로그램이 기본적으로 단일 테넌트인 경우에는 분할 병합 도구가 필요하지 않으므로 스키마 정보 개체를 채울 필요가 없습니다.

#### 데이터베이스를 프로비전했으며 분할된 데이터베이스 맵 관리자가 이미 있습니다. 이 새로운 데이터베이스를 분할로 등록하려면 어떻게 해야 하나요?

**[탄력적 데이터베이스 클라이언트 라이브러리를 사용하여 응용 프로그램에 분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md)**를 참조하세요.

#### 탄력적 데이터베이스 도구의 비용은 얼마인가요?

탄력적 데이터베이스 클라이언트 라이브러리 사용에는 비용이 발생하지 않습니다. 분할된 데이터베이스 및 분할된 데이터베이스 맵 관리자에 사용하는 Azure SQL 데이터베이스와 분할 병합 도구에 대해 프로비전된 웹/작업자 역할과 관련된 비용만 발생합니다.

#### 다른 서버에서 분할을 추가할 경우 내 자격 증명이 작동하지 않는 것은 무엇 때문인가요?
"사용자 ID=사용자 이름@서버 이름" 형식의 자격 증명을 사용하지 말고 단순히 "사용자 ID=사용자 이름"을 사용하세요. 또한 "사용자 이름" 로그인에 분할에 대한 권한이 있는지 확인하세요.

#### 내 응용 프로그램을 시작할 때마다 분할된 데이터베이스 맵 관리자를 만들고 분할을 채워야 하나요?

아니요, 분할된 데이터베이스 맵 관리자(예: **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**)는 한 번만 만들면 됩니다. 응용 프로그램 시작 시 응용 프로그램에서 **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** 호출을 사용해야 합니다. 이러한 호출은 응용 프로그램 도메인당 하나만 있어야 합니다.

#### 탄력적 데이터베이스 도구 사용과 관련된 질문이 있는 경우 답변을 받으려면 어떻게 해야 하나요? 

[Azure SQL 데이터베이스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)에서 문의하세요.

#### 분할 키를 사용하여 데이터베이스에 연결하는 경우 동일한 분할의 다른 분할 키에 대한 데이터도 쿼리할 수 있습니다. 의도한 동작인가요?

탄력적인 확장 API는 분할 키에 맞는 데이터베이스 연결을 제공하지만 분할 키 필터링 기능을 제공하지 않습니다. 필요한 경우 쿼리에 **WHERE** 절을 추가하여 범위를 제공된 분할 키로 제한하세요.

#### 내 분할 집합의 각 분할에 대해 다른 Azure 데이터베이스 버전을 사용할 수 있나요?

예, 분할은 개별 데이터베이스이므로 한 분할은 Premium Edition이고 다른 버전은 Standard Edition일 수 있습니다. 또한 분할 수명 동안 여러 번 분할 버전의 규모가 확장되거나 축소될 수 있습니다.

#### 분할 또는 병합 작업 중 분할 병합 도구에서 데이터베이스를 프로비전(또는 삭제)하나요? 

아니요. **분할** 작업의 경우 적절한 스키마를 가진 대상 데이터베이스가 있고 분할된 데이터베이스 맵 관리자에 등록되어 있어야 합니다. **병합** 작업의 경우 분할된 데이터베이스 맵 관리자에서 분할된 데이터베이스를 삭제한 후 데이터베이스를 삭제해야 합니다.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0204_2016-->