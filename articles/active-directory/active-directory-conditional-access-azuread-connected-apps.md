<properties
	pageTitle="SaaS 앱에 대한 Azure 조건부 액세스 미리 보기| Microsoft Azure"
	description="Azure AD의 조건부 액세스를 사용하면 응용 프로그램별 다단계 인증 액세스 규칙 및 신뢰할 수 있는 네트워크에 없는 사용자에 대한 액세스 차단 기능을 구성할 수 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="femila"/>

# Azure Conditional Access Preview for SaaS Apps

SaaS 앱에 대한 Azure 조건부 액세스는 공개 미리 보기에 제공됩니다. 미리 보기에서는 응용 프로그램별 다단계 인증 액세스 규칙 및 신뢰할 수 있는 네트워크에 없는 사용자에 대한 액세스 차단 기능을 구성할 수 있습니다.

응용 프로그램에 할당된 모든 사용자 또는 지정된 보안 그룹 내의 사용자에 제한적으로 다단계 인증 규칙을 적용할 수 있습니다. 조직 네트워크 내부에 있는 IP 주소에서 응용 프로그램에 액세스하는 경우에는 다단계 인증 요구 사항에서 제외될 수도 있습니다. 이러한 기능은 Azure Active Directory Premium 라이선스를 구입한 고객에게 제공됩니다.

## 시나리오 필수 조건
* Azure Active Directory Premium 구독

* 페더레이션 또는 관리되는 Azure Active Directory 테넌트

* 페더레이션 테넌트의 경우 MFA(다단계 인증)를 사용하도록 설정해야 합니다.

## 이 미리 보기 릴리스의 알려진 문제
이 미리 보기는 암호 Single-Sign On, 등록 및 개발된 LOB(기간 업무) 응용 프로그램 및 Azure AD 응용 프로그램 프록시를 사용하는 응용 프로그램인 사전 통합된 페더레이션 SaaS 응용 프로그램에 적용됩니다. 일부 응용 프로그램이 여전히 추가로 사용되고 있습니다.

##응용 프로그램별 액세스 규칙 구성

이 섹션에서는 응용 프로그램별 액세스 규칙을 구성하는 방법을 설명합니다.

1. 관리자 권한으로 Microsoft Azure 포털에 로그온합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. 디렉터리 탭에서 해당 디렉터리를 선택합니다.
4. **응용 프로그램** 탭을 선택합니다.
5. 규칙을 설정할 응용 프로그램을 선택합니다.
6. **구성** 탭을 선택합니다.
7. 액세스 규칙 섹션까지 아래로 스크롤합니다. 원하는 액세스 규칙을 선택합니다.
8. 규칙을 적용할 사용자를 지정합니다.
9. **사용**을 선택하여 정책을 사용하도록 설정합니다.

##액세스 규칙 이해

이 섹션에서는 Azure 조건부 응용 프로그램 액세스 미리 보기에서 지원되는 액세스 규칙에 대해 자세히 설명합니다.
### 액세스 규칙이 적용되는 사용자 지정

기본적으로 정책은 응용 프로그램에 대한 액세스 권한이 있는 모든 사용자에게 적용됩니다. 그러나 지정된 보안 그룹의 구성원인 사용자로만 정책을 제한할 수도 있습니다. **그룹 추가** 단추는 그룹 선택 대화 상자에서 액세스 규칙을 적용할 그룹을 하나 이상 선택하는 데 사용됩니다. 이 대화 상자는 선택한 그룹을 제거하는 데 사용할 수도 있습니다. 그룹에 적용할 규칙이 선택되면 액세스 규칙이 지정된 보안 그룹 중 하나에 속한 사용자에 대해서만 적용됩니다.

제외 옵션을 선택하고 하나 이상의 그룹을 지정하여 정책에서 보안 그룹을 명시적으로 제외할 수도 있습니다. 제외 목록에 있는 그룹의 구성원인 사용자는 액세스 규칙이 적용되는 그룹의 구성원인 경우에도 다단계 인증 요구 사항이 적용되지 않습니다. 아래 표시된 액세스 규칙의 경우 관리자 그룹의 모든 사용자가 응용 프로그램에 액세스할 때 다단계 인증을 사용해야 합니다.

![MFA를 사용하는 조건부 액세스 규칙 설정](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

##MFA를 사용하는 조건부 액세스 규칙
사용자가 사용자별 다단계 인증 기능을 사용하여 구성된 경우 사용자에 대한 이 설정이 앱 다단계 인증 규칙보다 우선 적용됩니다. 즉, 사용자별 다단계 인증에 대해 구성된 사용자는 응용 프로그램 다단계 인증 규칙에서 제외된 경우에도 다단계 인증을 수행해야 합니다. 다단계 인증 및 사용자별 설정에 대해 자세히 알아보세요.

###액세스 규칙 옵션
현재 미리 보기에서는 다음 옵션을 지원합니다.

* **다단계 인증 요구**: 이 옵션을 사용하면 액세스 규칙이 적용되는 사용자가 정책이 적용되는 응용 프로그램에 액세스하기 전에 다단계 인증을 완료해야 합니다.

* **직장이 아닐 때 다단계 인증 요구**: 이 옵션을 사용하면 신뢰할 수 있는 IP 주소에서 액세스하는 사용자가 다단계 인증을 수행하지 않아도 됩니다. 신뢰할 수 있는 IP 범위는 다단계 인증 설정 페이지에서 구성할 수 있습니다.

* **직장이 아닐 때 액세스 차단**: 이 옵션을 사용하면 신뢰할 수 있는 IP에서 액세스하는 사용자가 차단됩니다. 신뢰할 수 있는 IP 범위는 다단계 인증 설정 페이지에서 구성할 수 있습니다.

###규칙 상태 설정
액세스 규칙 상태를 통해 규칙을 켜고 끌 수 있습니다. 액세스 규칙이 꺼져 있으면 다단계 인증 요구 사항이 적용되지 않습니다.

### 액세스 규칙 평가

사용자가 OAuth 2.0, OpenID Connect, SAML 또는 WS-Federation을 사용하는 페더레이션된 응용 프로그램에 액세스할 때 액세스 규칙이 평가됩니다. 또한 액세스 규칙은 OAuth 2.0 및 OpenID Connect가 새로 고침 토큰을 사용하여 액세스 토큰을 얻을 때 평가됩니다. 새로 고침 토큰을 사용할 때 정책 평가에 실패하면 invalid\_grant 오류가 반환됩니다. 이 오류는 사용자가 클라이언트에 다시 인증해야 함을 나타냅니다. 다단계 인증을 제공하도록 페더레이션 서비스 구성

페더레이션된 테넌트의 경우 MFA(Multi-Factor Authentication)를 Azure Active Directory 또는 온-프레미스 AD FS 서버에서 수행할 수 있습니다.

기본적으로 MFA는 Azure Active Directory에서 호스트되는 모든 페이지에 발생합니다. 온-프레미스에서 MFA를 구성하려면 Windows PowerShell용 Azure AD 모듈을 사용하여 Azure Active Directory에서 –SupportsMFA 속성을 true로 설정해야 합니다.

다음 예제는 contoso.com 테넌트에서 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx)을 사용하여 온-프레미스 MFA를 사용하도록 설정하는 방법을 보여 줍니다.

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

이 플래그를 설정하는 것 외에도 페더레이션 테넌트 AD FS 인스턴스에서 다단계 인증을 수행하도록 구성해야 합니다. 온-프레미스에 Azure Multi-Factor Authentication을 배포하기 위한 지침을 따르세요.

##관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->