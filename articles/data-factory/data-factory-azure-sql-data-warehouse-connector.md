<properties 
	pageTitle="Azure SQL 데이터 웨어하우스 간 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터를 이동하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터 이동

이 문서에서는 Azure 데이터 팩터리의 복사 작업을 사용하여 Azure SQL 데이터 웨어하우스에서 다른 데이터 저장소로 데이터를 이동하고 다른 데이터 저장소에서 Azure SQL 데이터 웨어하우스로 데이터를 이동할 수 있는 방법을 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 원본을 사용하는 데이터 이동 및 SQL 데이터 웨어하우스에 대한 싱크의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

다음 샘플은 Azure SQL 데이터 웨어하우스 및 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores) 문서에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.

> [AZURE.NOTE] 
Azure 데이터 팩터리 서비스에 대한 개요는 [Azure 데이터 팩터리 소개](data-factory-introduction.md)를 참조하세요.
> 
> 이 문서에서는 JSON 예제를 제공하지만 데이터 팩터리를 만들기 위한 단계별 지침을 제공하지 않습니다. Azure 데이터 팩터리에서 복사 작업을 사용하는 단계별 지침이 있는 빠른 연습은 [자습서: Azure Blob에서 Azure SQL 데이터베이스에 데이터 복사](data-factory-get-started.md)를 참조하세요.


## 샘플: Azure SQL 데이터 웨어하우스에서 Azure Blob에 데이터 복사

아래 샘플은 다음을 보여줍니다.

1. [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다. 
3. [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다. 
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure SQL 데이터 웨어하우스의 테이블에서 blob로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 데이터 웨어하우스 연결된 서비스:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL 데이터 웨어하우스 입력 데이터 집합:**

샘플은 Azure SQL 데이터 웨어하우스에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn" 라는 열이 포함되었다고 가정합니다.
 
"외부":"true" 설정 및 externalData 정책 지정은 데이터 팩터리 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **SqlDWSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

> [AZURE.NOTE] 위의 예에서 **sqlReaderQuery**는 SqlDWSource에 지정됩니다. 복사 작업은 데이터를 가져오는 Azure SQL 데이터 웨어하우스 원본에 대해 이 쿼리를 실행합니다.
>  
> 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장된 프로시저를 지정할 수 있습니다.(저장된 프로시저가 매개 변수를 사용하는 경우)
>  
> sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열은 쿼리를 작성하는 데 사용되어 Azure SQL 데이터 웨어하우스에 대해 실행합니다.(mytable에서 column1, column2 선택) 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

## 샘플: Azure Blob에서 Azure SQL 데이터 웨어하우스에 데이터 복사

아래 샘플은 다음을 보여줍니다.

1.	[AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 형식의 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 및 [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.


샘플은 Azure blob에서 Azure SQL 데이터 웨어하우스의 테이블로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 데이터 웨어하우스 연결된 서비스:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에서 생성되지 않았음을 알립니다.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure SQL 데이터 웨어하우스 출력 데이터 집합:**

샘플은 Azure SQL 데이터 웨어하우스의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure SQL 데이터 웨어하우스의 테이블을 만들어야 합니다. 새 행은 매시간 테이블에 추가됩니다.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**복사 작업을 포함하는 파이프라인**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **SqlDWSink**로 설정됩니다.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

자세한 설명은 Azure SQL 데이터 웨어하우스 설명서의 [Azure Data Factory를 사용하여 데이터 로드](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) 문서를 참조하세요.

## Azure SQL 데이터 웨어하우스 연결된 서비스 속성

다음 테이블은 Azure SQL 데이터 웨어하우스 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **AzureSqlDW**로 설정되어야 합니다. | 예
**connectionString** | connectionString 속성에 대한 Azure SQL 데이터 웨어하우스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예

참고: [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성해야 합니다. 데이터베이스 서버를 구성하여 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 해야 합니다. 또한 Azure SQL 데이터 웨어하우스에 데이터를 복사하는 경우 데이터 팩터리 게이트웨이 컴퓨터에 대 한 적절 한 IP 주소 범위를 구성 해야 데이터 소스 온-프레미스에서에서 Azure 등 외부에서 보내는 데이터를 Azure SQL 데이터 웨어하우스에 합니다.

## Azure SQL 데이터 웨어하우스 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.(SQL Azure, Azure Blob, Azure 테이블 등)

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AzureSqlDWTable** 형식의 데이터 집합에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | 연결된 서비스가 참조하는 Azure SQL 데이터 웨어하우스 데이터베이스에 있는 테이블의 이름입니다. | 예 |

## Azure SQL 데이터 웨어하우스 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

**참고:** 복사 작업은 하나의 입력을 사용하여 하나의 출력을 생성합니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

### SqlDWSource

원본이 **SqlDWSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: select * from MyTable. | 아니요 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. | 저장 프로시저의 이름입니다. | 아니요 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다. | 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아니요 |

**sqlReaderQuery**가 SqlDWSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL 데이터 웨어하우스 원본에 대해 이 쿼리를 실행합니다.

또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열은 쿼리를 작성하는 데 사용되어 Azure SQL 데이터 웨어하우스에 대해 실행합니다.(mytable에서 column1, column2 선택) 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

#### SqlDWSource 예제

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**저장 프로시저 정의:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink**는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 | Integer(단위 = 행 수). | 아니요(기본값 = 10000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. | (단위 = timespan) 예: "00:30:00"(30분). | 아니요 | 
| sqlWriterCleanupScript | 사용자는 데이터의 특정 조각을 정리할 수 있도록 실행하는 복사 작업에 쿼리를 지정했습니다. 자세한 내용은 아래 반복성 섹션을 참조하세요. | 쿼리 문입니다. | 아니요 |
| sliceIdentifierColumnName | 사용자는 자동 생성된 조각 식별자로 채워진 복사 작업에 열 이름을 지정하여 다시 실행하는 경우 특정 조각의 데이터를 정리하는 데 사용합니다. 자세한 내용은 아래 반복성 섹션을 참조하세요. | 이진(32) 데이터 형식이 있는 열의 열 이름입니다. | 아니요 |

#### SqlDWSink 예제


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
    }


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure SQL 데이터 웨어하우스에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 자동 형식 변환인 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

SQL Azure, SQL server, Sybase에서 데이터를 이동하는 경우 SQL 형식에서 .NET 유형에 그리고 그 반대로 다음 매핑을 사용합니다.

매핑은 [ADO.NET에 대한 SQL Server 데이터 형식 매핑](https://msdn.microsoft.com/library/cc716729.aspx)과 같습니다.

| SQL Server 데이터베이스 엔진 형식 | .NET Framework 형식 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 10진수 | 10진수 |
| FILESTREAM 특성(varbinary(max)) | Byte |
| Float | Double |
| 이미지 | Byte | 
| int | Int32 | 
| money | 10진수 |
| nchar | String, Char |
| ntext | String, Char |
| numeric | 10진수 |
| nvarchar | String, Char |
| real | 단일 |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 10진수 | 
| sql\_variant | 개체 * |
| 텍스트 | String, Char |
| 실시간 | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0218_2016-->