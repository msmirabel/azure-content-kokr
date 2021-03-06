<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 사용자 지정 특성 | Microsoft Azure"
	description="Azure Active Directory B2C에서 사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집하는 방법"
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
	ms.date="01/04/2016"
	ms.author="swkrish"/>

#  Azure Active Directory B2C 미리 보기: 사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Azure Active Directory(Azure AD) B2C 디렉터리에는 지정된 이름, 성, 도시, 우편 번호 등 기본 제공 정보(특성) 집합이 함께 제공됩니다. 그러나 모든 소비자 지향 응용 프로그램에는 소비자로부터 수집하려는 정보에 대한 고유한 요구 사항이 있습니다. Azure AD B2C를 사용하면 각 소비자 계정에 저장된 특성 집합을 확장할 수 있습니다. [Azure 포털](https://portal.azure.com/)에 사용자 지정 특성을 만들고 아래와 같이 등록 정책에서 사용합니다. 또한 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 이러한 특성을 읽고 쓸 수 있습니다.

> [AZURE.NOTE]
사용자 지정 특성은 [Azure AD Graph API 디렉터리 스키마 확장](https://msdn.microsoft.com/library/azure/dn720459.aspx)을 사용합니다.

## 사용자 지정 특성 만들기

1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. **사용자 특성**을 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. 사용자 지정 특성에 **이름**을 제공하고 필요에 따라 **설명**을 제공합니다.(예: "ShoeSize") **만들기**를 클릭합니다.

    > [AZURE.NOTE]
    현재 "문자열" **데이터 형식**만 사용할 수 있습니다.

사용자 지정 특성은 **사용자 특성**의 목록 및 등록 정책의 사용에 사용 가능합니다.

## 등록 정책에 사용자 지정 특성 사용

1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. **등록 정책**을 클릭합니다.
3. 사용자의 등록 정책(예: "B2C\_1\_SiUp")을 클릭하여 엽니다. 블레이드 위쪽에서 **편집**을 클릭합니다.
4. **등록 특성**을 클릭하고 사용자 지정 특성을 선택합니다.(예: "ShoeSize") **확인**을 클릭합니다.
5. **응용 프로그램 클레임**을 클릭하고 사용자 지정 특성을 선택합니다. **확인**을 클릭합니다.
6. 블레이드 위쪽에서 **저장**을 클릭합니다.

정책에서 "지금 실행" 기능을 사용하여 고객 환경을 확인할 수 있습니다. 이제 소비자를 등록하는 동안 수집되는 특성의 목록에서 "ShoeSize"을 표시되고 응용 프로그램으로 다시 전송되는 토큰에 표시되야 합니다.

<!---HONumber=AcomDC_0224_2016-->