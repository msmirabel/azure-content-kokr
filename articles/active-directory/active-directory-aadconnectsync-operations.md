<properties
   pageTitle="Azure AD Connect Sync: 운영 작업 및 고려 사항 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect Sync 및 이 구성 요소를 운영하기 위한 준비 방법에 대한 운영 작업을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Azure AD Connect Sync: 운영 작업 및 고려 사항
이 항목은 Azure AD Connect Sync에 대한 관리 작업을 설명하는 것을 목표로 합니다.

## 스테이징 모드
다음을 비롯한 여러 시나리오에 스테이징 모드를 사용할 수 있습니다.

-	고가용성.
-	새 구성 변경 내용을 테스트하고 배포합니다.
-	새 서버를 도입하고 이전 서비스를 제거합니다.

스테이징 모드에 있는 서버로 서버를 활성화하기 전에 구성을 변경하고 변경을 미리 볼 수 있습니다. 또한 프로덕션 환경에 모든 변경 사항을 적용하기 전에 필요한지를 확인하기 위해 전체 가져오기 및 동기화를 실행할 수 있습니다.

설치 중에 서버를 선택하여 **스테이징 모드**로 둘 수 있습니다. 서버가 가져오기 및 동기화에 대해 활성화되지만 내보내기는 수행되지 않습니다. 이러한 기능을 선택하더라도 스테이징 모드에 있는 서버는 암호 동기화를 실행하거나 비밀번호 쓰기 저장을 사용할 수 없습니다. 스테이징 모드를 해제하면 서버가 내보내기를 시작하고 경우 암호 동기화 및 비밀번호 쓰기 저장을 사용하도록 설정합니다.(활성화된 경우)

스테이징 모드에 있는 서버는 Active Directory 및 Azure AD에서 계속 변경 내용을 받습니다. 따라서 항상 최신 변경 내용의 복사본이 있고 빠르게 다른 서버의 책임을 넘겨 받습니다. 기본 서버의 구성을 변경하는 경우 스테이징 모드에서 서버에 동일한 변경 내용을 적용해야 합니다.

이전 동기화 기술에 대해 알고 있는 사용자의 경우 다른 서버에 자체 SQL 데이터베이스가 있으므로 스테이징 모드는 다릅니다. 이 경우 스테이징 모드 서버를 다른 데이터 센터에서 찾을 수 있습니다.

### 서버의 구성 확인
이 메서드를 적용 하려면 다음 단계를 수행합니다.

1. 준비
2. 가져오기 및 동기화
3. Verify
4. 활성 서버 전환

**준비**

1. Azure AD Connect를 설치하고 **스테이징 모드**를 선택하고 설치 마법사의 마지막 페이지에서 **동기화 시작**의 선택을 취소합니다. 그러면 동기화 엔진을 수동으로 실행할 수 있습니다.![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. 시작 메뉴에서 로그오프/로그온하고 **동기화 서비스**를 선택합니다.

**가져오기 및 동기화**

1. **커넥터**를 선택하고 **Active Directory 도메인 서비스** 형식을 가진 첫 번째 커넥터를 선택합니다. **실행**을 클릭하고 **전체 가져오기** 및 **확인**을 선택합니다. 이 형식인 모든 커넥터에 이 작업을 수행합니다.
2. **Azure Active Directory(Microsoft)** 형식이 있는 커넥터를 선택합니다. **실행**을 클릭하고 **전체 가져오기** 및 **확인**을 선택합니다.
4. 커넥터가 여전히 선택되어 있는지 확인하고 **Active Directory 도메인 서비스** 형식인 각 커넥터의 경우 **실행**을 클릭하고 **델타 동기화** 및 **확인**을 선택합니다.
5. **Azure Active Directory (Microsoft)** 형식이 있는 커넥터를 선택합니다. **실행**을 클릭하고 **델타 동기화** 및 확인을 차례로 선택합니다.

이제 Azure AD 및 온-프레미스 AD에 스테이지된 내보내기 변경 사항이 있습니다.(Exchange 하이브리드 배포를 사용하는 경우) 다음 단계를 사용하면 디렉터리에 실제로 내보내기를 시작하기 전에 무엇이 변경될지를 검사할 수 있습니다.

**Verify**

1. cmd 프롬프트를 시작하고 `%Program Files%\Microsoft Azure AD Sync\bin`로 이동합니다.
2. 실행: `csexport "Name of Connector" %temp%\export.xml /f:x`<BR/> 동기화 서비스에서 커넥터의 이름을 찾을 수 있습니다. Azure AD에 "contoso.com – AAD"와 유사한 이름이 있습니다.
3. 실행: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. 이제 Microsoft Excel에서 검사할 수 있는 export.csv 라는 %temp%의 파일이 있습니다. 이 파일은 내보낼 수 있는 모든 변경 내용을 포함합니다.
5. 내보내려는 변경 사항이 예정될 때까지 데이터 또는 구성에 필요한 변경을 수행하고 이러한 단계(가져오기 및 동기화 및 확인)를 다시 실행합니다.

**export.csv 파일 이해**

파일은 대부분 따로 설명이 필요하지 않습니다. 콘텐츠를 이해하기 위한 일부 약어입니다.

- OMODT - 개체 수정 형식입니다. 개체 수준에서 작업이 추가, 업데이트 또는 삭제 중 어떤 것인지를 나타냅니다.
- AMODT - 특성 수정 형식입니다. 특성 수준에서 작업이 추가, 업데이트 또는 삭제 중 어떤 것인지를 나타냅니다.

특성 값은 다중값이면 모든 변경이 표시되지 않습니다. 추가 및 제거된 값의 수만 표시됩니다.

**활성 서버 전환**

1. Azure AD로 내보내지 않으므로 현재 활성 서버에서 서버를 해제하거나(DirSync/FIM Azure AD Sync) 스테이징 모드로 설정합니다.(Azure AD Connect)
2. **스테이징 모드** 서버에서 설치 마법사를 실행하고 **스테이징 모드**를 사용하지 않도록 설정합니다. ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## 재해 복구
구현 설계의 일부는 동기화 서버를 분실한 경우 재해 발생 시 수행할 작업을 계획합니다. 사용할 다른 모델이 있고 어떤 것을 사용하는지는 다음을 포함한 여러 요인에 따라 달라집니다.

-	가동 중지 시간 동안 Azure AD에서 개체를 변경하지 않는 오차는 무엇입니까?
-	암호 동기화를 사용하면 사용자가 온-프레미스를 변경할 경우 Azure AD에서 이전 암호를 사용해야 합니까?
-	비밀번호 쓰기 저장 등의 실시간 작업에 대한 종속성이 있습니까?

이러한 질문 및 조직의 정책에 대한 답변에 따라 다음 전략 중 하나를 구현할 수 있습니다.

-	필요할 때 다시 작성합니다.
-	**스테이징 모드**라고 하는 예비 대기 서버가 있습니다.
-	가상 컴퓨터를 사용합니다.

Azure AD Connect Sync는 SQL 데이터베이스에 종속되므로 SQL Express를 사용하지 않으면 Azure AD Connect에 포함된 SQL 고가용성 섹션도 검토해야 합니다.

### 필요할 때 다시 작성
실행 가능한 전략은 필요할 경우 서버를 다시 작성하려는 계획입니다. 대부분의 경우 동기화 엔진 설치와 초기 가져오기 및 동기화 수행은 몇 시간 안에 완료할 수 있습니다. 사용 가능한 예비 서버가 없는 경우 일시적으로 동기화 엔진을 호스팅하는 데 도메인 컨트롤러를 사용할 수 있습니다.

동기화 엔진 서버는 개체에 대한 상태를 저장하지 않으므로 Active Directory 및 Azure AD의 데이터에서 데이터베이스를 다시 빌드할 수 있습니다. **sourceAnchor** 특성을 온-프레미스 및 클라우드에서 개체를 조인하는데 사용합니다. 기존 온-프레미스 및 클라우드 개체로 서버를 다시 작성하는 경우 다시 설치하는 동기화 엔진은 해당 서버를 다시 일치시킵니다. 문서화 및 저장해야 할 사항은 필터링 및 동기화 규칙 등과 같은 서버의 구성 변경입니다. 동기화를 시작하기 전에 다시 적용해야 합니다.

### 스테이징 모드라는 예비 대기 서버가 있습니다.
더 복잡한 환경을 사용하는 경우 하나 이상의 대기 서버가 있는 것이 좋습니다. 설치 중에 서버를 사용하도록 설정하여 **스테이징 모드**로 둘 수 있습니다.

자세한 내용은 [스테이징 모드](#staging-mode)를 참조하세요.

### 가상 컴퓨터 사용
일반적이며 지원되는 메서드는 가상 컴퓨터에서 동기화 엔진을 실행하는 것입니다. 호스트에는 문제가 있는 경우 동기화 엔진 서버로 이미지를 다른 서버에 마이그레이션할 수 있습니다.

### SQL 고가용성
Azure AD Connect와 함께 제공하는 SQL Server Express를 사용하지 않는 경우 SQL Server에 대한 고가용성을 고려해야 합니다. 지원된 고가용성 솔루션은 클러스터링된 SQL입니다. 지원되지 않는 솔루션은 미러링 및 Always On을 포함합니다.

## 다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0128_2016-->