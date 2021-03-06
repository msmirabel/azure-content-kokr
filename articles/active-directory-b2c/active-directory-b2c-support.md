<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 지원 | Microsoft Azure"
	description="Azure Active Directory B2C에 대 한 지원 요청을 제출하는 방법"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: Azure Active Directory B2C에 대 한 지원 요청을 제출하는 방법

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

다음 단계를 사용하여 Azure 포털에서 Azure AD(Active Directory) B2C에 대한 지원 요청을 제출할 수 있습니다.

1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. B2C 테넌트를, 연결된 Azure 구독이 있는 다른 테넌트로 전환합니다. 일반적으로 후자는 직원 테넌트 또는 Azure 구독에 등록할 경우 만들어진 기본 테넌트입니다. Azure 구독과 Azure AD 테넌트 간의 관계에 대한 자세한 내용을 보려면 [이 문서](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad)를 참조하세요.

    ![지원 - 테넌트 전환](./media/active-directory-b2c-support/support-switch-dir.png)

3. 테넌트를 전환한 후 **도움말 + 지원**을 클릭합니다.

    ![지원 - 도움말 + 지원](./media/active-directory-b2c-support/support-support.png)

4. **새 지원 요청**을 클릭합니다.

    ![지원 - 새로 만들기](./media/active-directory-b2c-support/support-new.png)

5. **기본 사항** 블레이드에서 이러한 세부 정보를 사용하고 **다음**을 클릭합니다.

    - **문제점 유형**은 **기술**입니다.
	- 적합한 **구독**을 선택합니다.
    - **서비스** 아래 **Active Directory**를 선택합니다.
    - 적합한 **지원 계획**을 선택합니다. 아직 선택하지 않은 경우 [여기](https://azure.microsoft.com/ko-KR/support/plans/)에서 하나를 등록할 수 있습니다.

    ![지원 - 기본 사항](./media/active-directory-b2c-support/support-basics.png)

6. **문제** 블레이드에서 이러한 세부 정보를 사용하고 **다음**을 클릭합니다.

    - 적합한 **심각도** 수준을 선택합니다.
    - **문제 유형**은 **B2C 미리 보기**입니다.
    - 적합한 **범주**를 선택합니다.
	- **세부 정보** 필드에서 문제에 대해 설명합니다. B2C 테넌트 이름, 문제 설명, 오류 메시지, 상관 관계 ID(있는 경우) 등과 같은 세부 정보를 제공합니다.
    - **시간 프레임** 필드에서 문제가 발생했을 때의 날짜 및 시간(표준 시간대 포함)을 제공합니다.
    - **파일 업로드**에서 문제 해결에 도움이 될만한 모든 스크린샷과 파일을 업로드합니다.

    ![지원 - 문제](./media/active-directory-b2c-support/support-problem.png)

7. **연락처** 블레이드에서 연락처 정보를 추가합니다. **만들기**를 클릭합니다. *참고: 미리 보기가 제공되는 동안 Azure AD B2C는 영어만 지원합니다.*

    ![지원 - 연락처](./media/active-directory-b2c-support/support-contact.png)

8. 지원 요청을 제출한 후에 시작 보드에서 **도움말 + 지원** 및 **관리 지원 요청**을 차례로 클릭하여 모니터링할 수 있습니다.

## 알려진 문제: B2C 테넌트의 컨텍스트에서 지원 요청 제출하기

위에서 설명한 2단계를 누락하고 B2C 테넌트의 컨텍스트에서 지원 요청을 만들려면 다음과 같은 오류가 표시됩니다.

> [AZURE.IMPORTANT]
B2C 테넌트에서 새 Azure 구독에 등록하려 하지 마세요.

![지원 - 구독이 없음](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0218_2016-->