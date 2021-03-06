<properties
	pageTitle="PowerApps 엔터프라이즈에 Office 365 Outlook API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 Office 365 Outlook API 만들기 또는 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 조직의 앱 서비스 환경에서 새 Office 365 Outlook API 만들기

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][14]

3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][15]

4. **PowerApps 서비스**에서 **API 관리**를 선택합니다. ![등록된 API 찾아보기][1]

5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다. ![API 추가][2]

6. API에 대한 설명이 포함된 **이름**을 입력합니다.
	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **Office 365 Outlook**을 선택합니다. ![Office 365 Outlook API 선택][3]

8. **설정 - 필수 설정 구성**을 선택합니다. ![Office 365 Outlook API 설정 구성][4]

9. Office 365 AAD(Azure Active Directory) 응용 프로그램의 *앱 키* 및 *앱 암호*를 입력합니다. 없는 경우 이 항목의 "PowerApps에서 사용할 AAD 앱 등록" 섹션을 참조하여 필요한 키와 암호 값을 만듭니다.
 
	> [AZURE.IMPORTANT]**리디렉션 URL**을 저장합니다. 이 항목 뒷부분에서 이 값이 필요할 수 있습니다.

10. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 Office 365 Outlook API가 앱 서비스 환경에 추가됩니다.


## 옵션: PowerApps Office 365 API에서 사용할 AAD 앱 등록

키와 암호 값을 가진 기존 AAD 앱이 없는 경우 다음 단계를 사용하여 응용 프로그램을 만들고 필요한 값을 가져옵니다.

1. [Azure 포털][5]을 엽니다.

2. **찾아보기**를 선택한 다음 **Active Directory**를 선택합니다.

	>[AZURE.NOTE]그러면 Azure 클래식 포털에서 Active Directory가 열립니다.

3. 조직의 테넌트 이름을 선택합니다. ![Azure Active Directory 시작][6]

4. **응용 프로그램** 탭을 선택한 다음 **추가**를 선택합니다. ![AAD 테넌트 응용 프로그램][7]

5. **응용 프로그램 추가**에서:

	a) 응용 프로그램에 대한 **이름**을 입력합니다. b) 응용 프로그램 종류를 **웹**으로 그대로 유지합니다. c) **다음**을 선택합니다.

	![AAD 응용 프로그램 추가 - 앱 정보][8]

6. **앱 속성**에서:

	a) 응용 프로그램의 **로그온 URL**을 입력합니다. PowerApps에 대해 AAD로 인증하기 때문에 로그온 URL을 \__https://login.windows.net_으로 설정합니다. b) 앱에 대해 올바른 **앱 ID URI**를 입력합니다. c) **확인**을 선택합니다.

	![AAD 응용 프로그램 추가 - 앱 속성][9]

7. 성공적으로 완료되면 새 AAD 앱으로 리디렉션됩니다. **구성**을 선택합니다. ![Contoso AAD 앱][10]

8. _OAuth 2_ 섹션의 **회신 URL**을 이 항목의 Azure 포털에서 새 Office 365 Outlook API를 추가했을 때 받은 리디렉션 URL로 설정합니다. 그런 다음 **응용 프로그램 추가**를 선택합니다. ![Contoso AAD 앱 구성][11]

9. **다른 응용 프로그램에 대한 권한** 창에서 **Office 365 Exchange Online**을 선택하고 **확인**을 선택합니다. ![Contoso 앱 대리자][12]

10. 구성 페이지로 돌아가 보면 _Office 365 Exchange Online_이 _다른 응용 프로그램에 대한 권한_ 목록에 추가되었습니다.

11. _Office 365 Exchange Online_에 대해 **위임된 권한**을 선택하고 다음과 같은 권한을 선택합니다.

	- 사용자 연락처 읽기 및 쓰기
	- 사용자 연락처 읽기
	- 사용자 달력 읽기 및 쓰기
	- 사용자 달력 읽기
	- 사용자로 메일 보내기
	- 사용자 메일 읽기 및 쓰기
	- 사용자 메일 읽기

	![Contoso 앱 대리자 권한][13]

새 Azure Active Directory 앱이 만들어졌습니다. 이 앱을 Azure 포털의 Office 365 Outlook API 구성에서 사용할 수 있습니다.

## 요약 및 다음 단계
이 항목에서는 PowersApps 엔터프라이즈에 Office 365 Outlook API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->