<properties
pageTitle="논리 앱에 Yammer API 추가 | Microsoft Azure"
description="REST API 매개 변수를 사용하는 Yammer API 개요"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/23/2016"
ms.author="deonhe"/>

# Yammer API 시작

Yammer에 연결하여 엔터프라이즈 네트워크의 대화에 액세스합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [Yammer](../app-service-logic/app-service-logic-connector-yammer.md)를 클릭하세요.

Yammer를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Yammer에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 그룹의 새 메시지 또는 팔로잉의 피드가 있을 때 트리거를 사용합니다.
- 메시지 게시, 모든 메시지 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 새 메시지가 표시되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
Yammer에는 다음 트리거 및 작업이 포함됩니다.

트리거 | 동작
--- | ---
<ul><li>그룹에 새 메시지가 있을 때</li><li>내 팔로잉 피드에 새 메시지가 있을 때</li></ul>| <ul><li>모든 메시지 가져오기</li><li>그룹의 메시지 가져오기</li><li>내 팔로잉 피드에서 메시지 가져오기</li><li>메시지 게시</li><li>그룹에 새 메시지가 있을 때</li><li>내 팔로잉 피드에 새 메시지가 있을 때</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## Yammer에 대한 연결 만들기
Yammer API를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|Yammer 자격 증명 제공|

다음 단계를 따라 Yammer에 로그인하고 논리 앱의 Yammer **연결** 구성을 완료합니다.

1. **되풀이**를 선택합니다.
2. **빈도**를 선택하고 **간격**을 입력합니다.
3. **작업 추가**를 선택합니다. ![Yammer 구성][1]
4. 검색 상자에 Yammer를 입력하고 이름에 Yammer가 있는 모든 항목이 반환될 때까지 검색을 기다립니다.
5. **Yammer - 모든 메시지 가져오기**를 선택합니다.
6. **Yammer에 로그인**을 선택합니다. ![Yammer 구성][2]
7. Yammer 자격 증명을 제공하여 로그인하고 응용 프로그램에 권한을 부여합니다. ![Yammer 구성][3]  
8. 조직의 로그인 페이지로 리디렉션됩니다. Yammer를 논리 앱과 상호 작용하도록 **허용**합니다. ![Yammer 구성][4] 
9. 로그인한 후 **Yammer - 모든 메시지 가져오기** 섹션을 구성하고 필요한 다른 트리거 및 작업을 추가하여 완료하기 위해 논리 앱으로 돌아갑니다. ![Yammer 구성][5]  
10. 위의 메뉴 모음에서 **저장**을 선택하여 작업을 저장합니다.


>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Yammer REST API 참조
이 문서 적용 버전: 1.0


### 로그인한 사용자의 Yammer 네트워크에서 모든 공개 메시지 가져오기
Yammer 웹 인터페이스의 "모든" 대화에 해당합니다. ```GET: /messages.json```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|older\_then|정수|no|쿼리|없음|숫자 문자열로 지정된 메시지 ID보다 오래된 메시지를 반환합니다. 메시지의 페이지를 매길 때 유용합니다. 예를 들어 현재 20개의 메시지가 표시되어 있고 가장 오래된 메시지 번호가 2912인 경우 요청에 "?older\_than=2912"를 추가하면 현재 표시된 메시지 이전의 메시지 20개가 표시됩니다.|
|newer\_then|정수|no|쿼리|없음|숫자 문자열로 지정된 메시지 ID보다 최신인 메시지를 반환합니다. 새 메시지를 폴링할 때 사용됩니다. 현재 표시된 메시지 중 최근 메시지 번호가 3516인 경우 매개 변수 "?newer\_than=3516"과 함께 요청하면 페이지에 표시된 것과 중복된 메시지 사본을 가져오지 않습니다.|
|limit|정수|no|쿼리|없음|지정된 메시지 수만 반환합니다.|
|page|정수|no|쿼리|없음|지정된 페이지를 가져옵니다. 반환된 데이터가 제한보다 크면 이 필드를 사용하여 후속 페이지에 액세스할 수 있습니다.|


### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|408|요청 시간 초과|
|429|너무 많은 요청|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|503|Yammer 서비스를 사용할 수 없음|
|504|게이트웨이 시간 초과|
|기본값|작업이 실패했습니다.|


### 그룹 또는 모든 회사 피드에 메시지 게시
그룹 ID를 제공한 경우 메시지가 지정된 그룹 또는 모든 회사 피드에 게시됩니다. ```POST: /messages.json```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|input| |yes|body|없음|메시지 게시 요청|


### 응답

|이름|설명|
|---|---|
|201|작성자|



## 개체 정의

#### Message: Yammer 메시지

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|id|정수|no|
|content\_excerpt|string|no|
|sender\_id|정수|no|
|replied\_to\_id|정수|no|
|created\_at|string|no|
|network\_id|정수|no|
|message\_type|string|no|
|sender\_type|string|no|
|url|string|no|
|web\_url|string|no|
|group\_id|정수|no|
|body|정의되지 않음|no|
|thread\_id|정수|no|
|direct\_message|부울|no|
|client\_type|string|no|
|client\_url|string|no|
|language|string|no|
|notified\_user\_ids|array|no|
|privacy|string|no|
|liked\_by|정의되지 않음|no|
|system\_message|부울|no|

#### PostOperationRequest: yammer에 게시할 Yammer 커넥터에 대한 게시 요청을 나타냅니다.

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|body|string|yes|
|group\_id|정수|no|
|replied\_to\_id|정수|no|
|direct\_to\_id|정수|no|
|broadcast|부울|no|
|topic1|string|no|
|topic2|string|no|
|topic3|string|no|
|topic4|string|no|
|topic5|string|no|
|topic6|string|no|
|topic7|string|no|
|topic8|string|no|
|topic9|string|no|
|topic10|string|no|
|topic11|string|no|
|topic12|string|no|
|topic13|string|no|
|topic14|string|no|
|topic15|string|no|
|topic16|string|no|
|topic17|string|no|
|topic18|string|no|
|topic19|string|no|
|topic20|string|no|

#### MessageList: 메시지 목록

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|messages|array|no|


#### MessageBody: 메시지 본문

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|parsed|string|no|
|plain|string|no|
|rich|string|no|

#### LikedBy: 관심 있는 대상

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|count|정수|no|
|names|array|no|

#### YammmerEntity: 관심 있는 대상

| 이름 | 데이터 형식 | 필수 |
|---|---| --- | 
|type|string|no|
|id|정수|no|
|full\_name|string|no|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/create-api-yammer/connectionconfig1.png
[2]: ./media/create-api-yammer/connectionconfig2.png
[3]: ./media/create-api-yammer/connectionconfig3.png
[4]: ./media/create-api-yammer/connectionconfig4.png
[5]: ./media/create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0302_2016-->