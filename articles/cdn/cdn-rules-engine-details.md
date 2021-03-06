<properties
	pageTitle="Azure CDN(콘텐츠 배달 네트워크) 규칙 엔진 일치 조건 및 기능 세부사항"
	description="이 항목에서는 Azure CDN(콘텐츠 배달 네트워크) 규칙 엔진에 대해 제공되는 일치 조건 및 기능에 대해 자세히 설명합니다."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# CDN 규칙 엔진 일치 조건 및 기능 세부사항

이 항목에서는 Azure CDN(콘텐츠 배달 네트워크) [규칙 엔진](cdn-rules-engine.md)에 대해 제공되는 일치 조건 및 기능에 대해 자세히 설명합니다.

> [AZURE.NOTE] 규칙 엔진에는 프리미엄 CDN 계층이 필요합니다. 표준 및 프리미엄 CDN 계층의 기능에 대한 자세한 내용은 [Azure 콘텐츠 배달 네트워크 개요](cdn-overview.md)를 참조하세요.

## 일치 조건

일치 조건은 기능 집합에 대해 수행할 특정 요청 유형을 식별합니다.

예를 들어 특정 위치에서 콘텐츠에 대한 요청, 특정 IP 주소 또는 국가에서 생성된 요청을 헤더 정보로 필터링하는 데 사용할 수 있습니다.

### 항상

항상 일치 조건은 모든 요청에 기본 기능 집합을 적용하도록 설계되었습니다.

### 장치

장치 일치 조건은 속성에 따라 모바일 장치에서 생성되는 요청을 식별합니다.

### 위치

이 일치 조건은 요청자의 위치에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
AS 숫자 | 특정 네트워크에서 발생하는 요청을 식별합니다.
국가 | 지정된 국가에서 발생하는 요청을 식별합니다.


### 원본

이 일치 조건은 CDN 저장소 또는 고객 원본 서버를 가리키는 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
CDN 원본 | CDN 저장소에 저장된 콘텐츠에 대한 요청을 식별합니다.
고객 원본 | 특정 고객 원본 서버에 저장된 콘텐츠에 대한 요청을 식별합니다.


### 요청

이 일치 조건은 해당 속성에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
클라이언트 IP 주소 | 특정 IP 주소에서 발생하는 요청을 식별합니다.
쿠키 매개 변수 | 지정된 값에 대한 각 요청과 관련된 쿠키를 확인합니다.
쿠키 매개 변수 Regex | 지정된 정규식에 대한 각 요청과 관련된 쿠키를 확인합니다.
에지 Cname | 특정 에지 CNAME을 가리키는 요청을 식별합니다.
참조 도메인 | 지정된 호스트 이름에서 참조된 요청을 식별합니다.
요청 헤더 리터럴 | 지정된 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 헤더 Regex | 지정된 정규식과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 헤더 와일드카드 | 지정된 패턴과 일치하는 값으로 설정된 지정된 헤더를 포함하는 요청을 식별합니다.
요청 메서드 | 해당 HTTP 메서드로 요청을 식별합니다.
요청 스키마 | 해당 HTTP 프로토콜로 요청을 식별합니다.

### URL

이 일치 조건은 해당 URL에 따라 요청을 식별하도록 설계되었습니다.

이름 | 목적
-----|--------
URL 경로 디렉터리 | 해당 상대 경로로 요청을 식별합니다.
URL 경로 확장 | 해당 파일 확장명으로 요청을 식별합니다.
URL 경로 파일 이름 | 해당 파일 이름으로 요청을 식별합니다.
URL 경로 리터럴 | 요청의 상대 경로를 지정된 값과 비교합니다.
URL 경로 Regex | 요청의 상대 경로를 지정된 정규식과 비교합니다.
URL 경로 와일드카드 | 요청의 상대 경로를 지정된 패턴과 비교합니다.
URL 쿼리 리터럴 | 요청의 쿼리 문자열을 지정된 값과 비교합니다.
URL 쿼리 매개 변수 | 지정된 패턴과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 Regex | 지정된 정규식과 일치하는 값으로 설정된 지정된 쿼리 문자열 매개 변수를 포함하는 요청을 식별합니다.
URL 쿼리 와일드카드 | 지정된 값을 요청의 쿼리 문자열에 대해 비교합니다.

## 기능

기능은 일치 조건 집합으로 식별되는 요청 유형에 적용할 작업 유형을 정의합니다.

### Access

이러한 기능은 콘텐츠에 대한 액세스를 제어하도록 설계되었습니다.

이름 | 목적
-----|--------
액세스 거부 | 모든 요청이 403 사용 권한 없음 응답으로 거부되는지 여부를 결정합니다.
토큰 인증 | 요청에 토큰 기반 인증을 적용할지 여부를 결정합니다.
토큰 인증 거부 코드 | 토큰 기반 인증으로 인해 요청이 거부되는 경우 사용자에게 반환할 응답 유형을 결정합니다.
토큰 인증 URL 대/소문자 무시 | 토큰 기반 인증에서 URL 비교 시 대/소문자를 구분할지 결정합니다.
토큰 인증 매개 변수 | 토큰 기반 인증 쿼리 문자열 매개 변수 이름을 바꿔야 하는지 여부를 결정합니다.

### 구성

이러한 기능은 콘텐츠가 캐시되는 시기와 방식을 사용자 지정하기 위해 설계되었습니다.

이름 | 목적
-----|--------
대역폭 매개 변수 | 대역폭 제한 매개 변수(예: ec\_rate 및 ec\_prebuf)를 활성화할지 여부를 결정합니다.
대역폭 제한 | 에지 서버에서 제공하는 응답에 대한 대역폭을 제한합니다.
바이패스 캐시 | 요청에서 캐싱 기술을 활용할 수 있는지 여부를 결정합니다.
Cache-Control 헤더 처리 | 외부 Max-Age 기능이 활성 상태일 때 에지 서버에 의한 Cache-Control 헤더의 생성을 제어합니다.
Cache-Key 쿼리 문자열 | cache-key에서 요청과 관련된 쿼리 문자열 매개 변수를 포함 또는 제외할지 여부를 결정합니다.
Cache-Key 다시 쓰기 | 요청과 관련된 cache-key를 다시 씁니다.
전체 캐시 채우기 | 요청 결과, 에지 서버에서 캐시가 부분적으로 누락된 경우 수행할 작업을 결정합니다.
압축 파일 형식 | 서버에서 압축할 파일 형식을 정의합니다.
기본 내부 Max-Age | 에지 서버에서 원본 서버 캐시 유효성 재검사를 위한 기본 max-age 간격을 결정합니다.
만료 헤더 처리 | 외부 Max-Age 기능이 활성 상태일 때 에지 서버에 의한 만료 헤더의 생성을 제어합니다.
외부 Max-Age | 브라우저에서 에지 서버 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다.
강제 내부 Max-Age | 에지 서버에서 원본 서버 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다.
H.264 지원(HTTP 점진적 다운로드) | 콘텐츠를 스트리밍하는 데 사용할 수 있는 H.264 파일 형식의 유형을 결정합니다.
No-Cache 요청 부여 | HTTP 클라이언트의 no-cache 요청을 원본 서버에 전달할지 여부를 결정합니다.
원본 No-Cache 무시 | CDN이 원본 서버에서 제공되는 특정 지시문을 무시할지 여부를 결정합니다.
적절하지 않은 범위 무시 | 요청에서 416 요청한 범위가 적절하지 않음 상태 코드를 생성하는 경우 클라이언트로 반환할 응답을 결정합니다.
내부 Max-Stale | 에지 서버가 원본 서버로 캐시된 자산의 유효성 재검사를 할 수 없는 경우 에지 서버에서 캐시된 자산이 정상 만료 시간을 지나 얼마나 오래 제공될 수 있는지를 제어합니다.
부분 캐시 공유 | 요청에서 부분적으로 캐시된 콘텐츠를 생성할 수 있는지 여부를 결정합니다.
캐시된 콘텐츠 사전 유효성 검사 | TTL이 만료되기 전에 캐시된 콘텐츠로 미리 유효성 재검사할 수 있는지 여부를 결정합니다.
0바이트 캐시 파일 새로 고침 | 0바이트 캐시 자산에 대한 HTTP 클라이언트 요청이 에지 서버에 의해 처리되는 방식을 결정합니다.
캐시 가능한 상태 코드 집합 | 캐시된 콘텐츠가 발생할 수 있는 상태 코드 집합을 정의합니다.
오류 시 오래된 콘텐츠 배달 | 캐시 유효성 재검사 중이나 고객 원본 서버에서 요청된 콘텐츠를 검색할 때 만료되고 캐시된 콘텐츠를 배달할지 여부를 결정합니다.
유효성 재검사 중 기한 경과 | 유효성 재검사를 수행하는 동안 에지 서버가 오래된 클라이언트를 요청자에게 제공하도록 하여 성능을 개선합니다.
주석 | 주석 기능을 통해 규칙 내에 메모를 추가할 수 있습니다.

### 헤더

이러한 기능은 요청자 또는 응답에서 헤더를 추가, 수정 또는 삭제하도록 설계되었습니다.

이름 | 목적
-----|--------
Age 응답 헤더 | Age 응답 헤더를 요청자에게 전송되는 응답에 포함할지 결정합니다.
디버그 캐시 응답 헤더 | 응답에 요청된 자산에 대한 캐시 정책 정보를 제공하는 X-EC-Debug 응답 헤더를 포함할 수 있는지 여부를 결정합니다.
클라이언트 요청 헤더 수정 | 요청에서 헤더를 덮어쓰기, 추가 또는 삭제합니다.
클라이언트 응답 헤더 수정 | 응답에서 헤더를 덮어쓰기, 추가 또는 삭제합니다.
클라이언트 IP 사용자 지정 헤더 설정 | 클라이언트를 요청하는 IP 주소가 사용자 지정 요청 헤더로 요청에 추가되도록 합니다.

### 로그

이러한 기능은 원시 로그 파일에 저장된 데이터를 사용자 지정하도록 설계되었습니다.

이름 | 목적
-----|--------
사용자 지정 로그 필드 1 | 원시 로그 파일의 사용자 지정 로그 필드에 할당할 콘텐츠와 형식을 결정합니다.
로그 쿼리 문자열 | 액세스 로그에 쿼리 문자열을 URL과 함께 저장할지 여부를 결정합니다.

### 최적화

이러한 기능은 요청에서 에지 최적화 프로그램이 제공하는 최적화를 수행할지 여부를 결정합니다.

이름 | 목적
-----|--------
에지 최적화 프로그램 | 에지 최적화 프로그램을 요청에 적용할 수 있는지 여부를 결정합니다.
에지 최적화 프로그램 - 구성 인스턴스화 | 사이트와 관련된 에지 최적화 프로그램 구성을 인스턴스화 또는 활성화합니다.


### 원본

이러한 기능은 CDN이 원본 서버와 통신하는 방법을 제어하도록 설계되었습니다.

이름 | 목적
-----|--------
최대 연결 유지 요청 | 연결이 닫히기 전에 연결을 유지할 최대 요청 수를 정의합니다.
프록시 특별 헤더 | 에지 서버에서 원본 서버로 전달할 CDN 특정 요청 헤더의 집합을 정의합니다.

### 특별 사항

이러한 기능은 고급 사용자만 사용해야 하는 고급 기능을 제공합니다.

이름 | 목적
-----|--------
캐시 가능한 HTTP 메서드 | 네트워크에서 캐시할 수 있는 추가 HTTP 메서드 집합을 결정합니다.
캐시 가능한 요청 본문 크기 | POST 응답을 캐시할 수 있는지 여부를 결정하는 임계값을 정의합니다.


### URL

이러한 기능을 통해 요청을 다른 URL로 리디렉션하거나 다시 작성할 수 있습니다.

이름 | 목적
-----|--------
리디렉션 추적 | 고객 원본 서버에서 반환된 Location 헤더에 정의된 호스트 이름으로 요청을 리디렉션할 수 있는지 여부를 결정합니다.
URL 리디렉션 | Location 헤더를 통해 요청을 리디렉션합니다.
URL 다시 쓰기 | 요청 URL을 다시 씁니다.

### 웹 응용 프로그램 방화벽

웹 응용 프로그램 방화벽 기능은 웹 응용 프로그램 방화벽으로 요청을 보호할지 여부를 결정합니다.

## 참고 항목
* [Azure CDN 개요](cdn-overview.md)
* [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-rules-engine.md)

<!---HONumber=AcomDC_0302_2016-->