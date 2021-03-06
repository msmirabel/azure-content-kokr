<properties
	pageTitle="DocumentDB용 NoSQL Node.js 자습서 | Microsoft Azure"
	description="DocumentDB Node.js SDK를 사용하여 노드 데이터베이스 및 콘솔 응용 프로그램을 만드는 NoSQL Node.js 자습서입니다. DocumentDB는 JSON에 대한 NoSQL 데이터베이스입니다."
    keywords="node.js 자습서, 노드 데이터베이스"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="02/19/2016"
	ms.author="anhoh"/>

# NoSQL Node.js 자습서: DocumentDB Node.js 콘솔 응용 프로그램  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

DocumentDB Node.js SDK용 Node.js 자습서를 시작합니다. 이 자습서를 따라하면 노드 데이터베이스를 포함하여 DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

다음에 대해 설명합니다.

- DocumentDB 계정 만들기 및 연결
- 응용 프로그램 설정
- 노드 데이터베이스 만들기
- 컬렉션 만들기
- JSON 문서 만들기
- 컬렉션 쿼리
- 노드 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution)를 참조하세요.

Node.js 자습서를 완료한 후에 이 페이지 위쪽 및 아래쪽에 있는 응답 단추를 통해 의견을 보내주세요. 직접 연락을 받고 싶은 경우 설명에 메일 주소를 포함하세요.

이제 시작하겠습니다.

## Node.js 자습서의 필수 조건

다음 항목이 있는지 확인합니다.

- 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
- [Node.js](https://nodejs.org/) 버전 v0.10.29 이상

## 1단계: DocumentDB 계정 만들기

DocumentDB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Node.js 응용 프로그램 설치](#SetupNode)로 건너뛸 수 있습니다.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> 2단계: Node.js 응용 프로그램 설치

1. 자주 사용하는 터미널을 엽니다.
2. Node.js 응용 프로그램을 저장하려는 폴더 또는 디렉터리를 찾습니다.
3. 다음 명령을 사용하여 두 개의 빈 JavaScript 파일을 만듭니다.
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. npm을 통해 documentdb 모듈을 설치합니다. 다음 명령을 사용합니다.
    * ```npm install documentdb --save```

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다.

##<a id="Config"></a> 3단계: 앱의 구성 설정

원하는 텍스트 편집기에서 ```config.js```을(를) 엽니다.

그런 다음 ```config```이라는 제목의 빈 개체를 만들고 DocumentDB 끝점 및 권한 부여 키에 ```config.endpoint``` 및 ```config.authKey``` 속성을 설정합니다. 이러한 구성은 모두 [Azure 포털](https://portal.azure.com)에서 찾을 수 있습니다.

![Node.js 자습서 - 활성 허브, DocumentDB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조키 값이 강조 표시된 DocumentDB 계정을 보여 주는 Azure 포털의 스크린샷 - 노드 데이터베이스][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

이제 ```config``` 개체에 ```database id```, ```collection id``` 및 ```JSON documents```을(를) 추가해 보겠습니다. ```config.endpoint``` 및 ```config.authKey``` 속성을 설정한 아래에서 다음 코드를 추가합니다. 데이터베이스에 저장하려는 데이터가 이미 있다면 문서 정의를 추가하는 대신 DocumentDB의 [데이터 마이그레이션 도구](documentdb-import-data.md)를 사용할 수 있습니다.

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

데이터베이스, 컬렉션 및 문서 정의는 DocumentDB ```database id```, ```collection id``` 및 문서 데이터의 역할을 합니다.

마지막으로 ```config``` 개체를 내보내므로 ```app.js``` 파일 내에서 참조할 수 있습니다.

    module.exports = config;

##<a id="Connect"></a>4단계: DocumentDB 계정에 연결

텍스트 편집기에서 빈 ```app.js``` 파일을 엽니다. ```documentdb``` 모듈 및 새로 만든 ```config``` 모듈을 가져옵니다.

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

다음으로 이전에 저장된 ```config.endpoint``` 및 ```config.authKey```을(를) 사용하여 새 DocumentClient를 만들려고 합니다.

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

DocumentDB 계정에 연결했으므로 DocumentDB 리소스와 함께 작동하는지 살펴보겠습니다.

## 5단계: 노드 데이터베이스 만들기
**DocumentClient** 클래스의 [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [데이터베이스](documentdb-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. ```config``` 개체에 지정된 ```id```을(를) 사용하여 app.js 파일에서 새 데이터베이스를 만들기 위해 함수를 추가합니다. 동일한 ```FamilyRegistry``` ID를 가진 데이터베이스가 이미 있는지 먼저 확인합니다. 파일이 존재하는 경우 새로 만드는 대신 해당 데이터베이스를 반환합니다.

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>6단계: 컬렉션 만들기  

> [AZURE.WARNING] **CreateDocumentCollectionAsync**는 가격의 의미가 포함된 새 S1 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.

**DocumentClient** 클래스의 [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [컬렉션](documentdb-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. 새로 만든 컬렉션은 [S1 성능 수준](documentdb-performance-levels.md)에 매핑됩니다. ```config``` 개체에 지정된 ```id```을(를) 사용하여 app.js 파일에서 새 컬렉션을 만들기 위해 함수를 추가합니다. 다시 동일한 ```FamilyCollection``` ID를 가진 컬렉션이 이미 있는지 확인합니다. 파일이 존재하는 경우 새로 만드는 대신 해당 컬렉션을 반환합니다.

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>7단계: 문서 만들기
**DocumentClient** 클래스의 [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 함수를 사용하여 [문서](documentdb-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 DocumentDB 문서를 삽입할 수 있습니다.

다음으로 ```config``` 개체에 저장된 JSON 데이터를 포함하는 문서를 만들기 위해 app.js에 함수를 추가합니다. 다시 동일한 ID를 가진 문서가 이미 있는지 확인합니다.

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

축하합니다. 이제 DocumentDB에서 데이터베이스, 컬렉션 및 문서를 만들기 위한 함수를 만들었습니다!

![Node.js 자습서 - 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램 - 노드 데이터베이스](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>8단계: DocumentDB 리소스 쿼리

DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](documentdb-sql-query.md)를 지원합니다. 다음 샘플 코드에서는 컬렉션에는 문서에 대해 실행할 수 있는 쿼리를 보여줍니다. ```app.js``` 파일에 다음 함수를 추가합니다. DocumentDB는 아래와 같이 SQL과 비슷한 쿼리를 지원합니다. 복잡한 쿼리 작성에 대한 자세한 내용은 [쿼리 놀이터](https://www.documentdb.com/sql/demo) 및 [쿼리 설명서](documentdb-sql-query.md)를 확인합니다.

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

다음 다이어그램에서는 만든 컬렉션에 대해 DocumentDB SQL 쿼리 구문을 호출하는 방법을 보여 줍니다.

![Node.js 자습서 - 쿼리의 의미와 범위를 보여 주는 다이어그램 - 노드 데이터베이스](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

DocumentDB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](documentdb-sql-query.md/#from-clause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. DocumentDB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

##<a id="DeleteDatabase"></a>9단계: 노드 데이터베이스 삭제

만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다. 다음 코드 조각을 추가하여 데이터베이스를 삭제할 수 있습니다.

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>10단계: 함께 배치

응용 프로그램에 필요한 모든 기능을 설정했으므로 호출해 보겠습니다!

함수 호출의 순서는 다음과 같습니다. * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *정리*

```app.js```에서 코드의 아래쪽에 다음 코드 조각을 추가합니다.

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a>11단계: Node.js 응용 프로그램 실행

이제 Node.js 응용 프로그램을 실행할 준비가 되었습니다.

터미널에서 ```app.js``` 파일을 찾고 다음 명령을 실행합니다. ```node app.js```

시작한 앱의 출력이 표시됩니다. 출력은 아래의 예제 텍스트와 일치해야 합니다.

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

축하합니다. Node.js 자습서를 만들고 완료했으며 첫 번째 DocumentDB 콘솔 응용 프로그램이 있습니다.

##<a id="GetSolution"></a> 전체 Node.js 자습서 솔루션 다운로드
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

-   [DocumentDB 계정][documentdb-create-account]
-   GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) 솔루션

npm을 통해 **documentdb** 모듈을 설치합니다. 다음 명령을 사용합니다. * ```npm install documentdb --save```

다음으로 ```config.js``` 파일에서 [3단계: 앱의 구성 설정](#Config)에 설명한 대로 config.endpoint 및 config.authKey 값을 업데이트합니다.

## 다음 단계

-   더 복잡한 Node.js 샘플을 찾으십니까? [DocumentDB를 사용하여 Node.js 응용 프로그램 빌드](documentdb-nodejs-application.md)를 참조하세요.
-	[DocumentDB 계정 모니터링](documentdb-monitor-accounts.md) 방법에 대해 자세히 알아봅니다.
-	[쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
-	[DocumentDB 설명서](../../services/documentdb/) 페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0224_2016-->