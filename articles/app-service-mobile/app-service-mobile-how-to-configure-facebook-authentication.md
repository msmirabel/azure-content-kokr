<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Facebook 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Facebook 인증을 구성하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/28/2016"
	ms.author="mahender"/>

# Facebook 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Facebook을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

이 토픽의 절차를 완료하려면 검증된 전자 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register"> </a>Facebook을 사용하여 응용 프로그램 등록

1. [Azure 포털]에 로그온한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Facebook 앱을 구성하는 데 사용합니다.

2. 다른 브라우저 창에서 [Facebook 개발자] 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.

3. (옵션) 아직 등록하지 않은 경우 **Apps** > **Register as a Developer**를 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.

4. **My Apps** > **Add a New App** > **Website** > **Skip and Create App ID**를 클릭합니다.

5. **Display Name**에 앱의 고유한 이름을 입력하고 앱에 대한 **Category**를 선택한 다음 **Create App ID**를 클릭하고 보안 검사를 완료합니다. 새 Facebook 앱에 대한 개발자 대시보드로 이동됩니다.

6. **App Secret** 필드에서 **Show**를 클릭하고 요청될 경우 암호를 제공한 다음 **App ID** 및 **App Secret** 값을 적어 둡니다. 나중에 이 값을 사용하여 Azure에서 응용 프로그램을 구성합니다.

	> [AZURE.IMPORTANT] 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

7. 왼쪽 탐색 모음에서 **Settings**를 클릭하고 **App Domains**에 모바일 앱의 **URL**을 입력한 다음 **Contact Email**을 입력합니다.

    ![][0]

8. 아래에 웹 사이트 섹션이 보이지 않으면 **Add Platform** > **Website**를 클릭하고 **Site URL** 필드에 모바일 앱의 **URL**을 입력한 다음 **Save Changes**를 클릭합니다.

9. **Advanced** 탭을 클릭하고 응용 프로그램의 **Redirect URI**를 **Valid OAuth redirect URIs**에 추가한 다음 **Save Changes**를 클릭합니다.

	> [AZURE.NOTE] 리디렉션 URI는 경로 _/.auth/login/facebook/callback_이 추가된 응용 프로그램의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/facebook/callback` HTTPS 체계를 사용 중인지 확인합니다.

10. 응용 프로그램을 등록하는 데 사용된 Facebook 계정이 앱의 관리자입니다. 지금은 관리자만 이 응용 프로그램에 로그인할 수 있습니다. 다른 Facebook 계정을 인증하려면 **App Review**를 클릭하고 **Make todolist-complete-nodejs public**을 사용하도록 설정하여 Facebook 인증을 사용한 일반 공용 액세스를 사용하도록 설정합니다.


## <a name="secrets"> </a>응용 프로그램에 Facebook 정보 추가

1. [Azure 포털]로 돌아가서 응용 프로그램으로 이동합니다. **설정** > **인증 / 권한 부여**를 클릭하고**앱 서비스 인증**이 **켜기**인지 확인합니다.

2. **Facebook**을 클릭하고 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣습니다. 필요한 경우 응용 프로그램에 필요한 모든 범위를 사용하도록 설정한 다음 **확인**을 클릭합니다.

    ![][1]

	기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

3. (옵션) Facebook에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Facebook**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Facebook에 리디렉션되어야 합니다.

4. 인증 구성이 완료되면 **저장**을 클릭합니다.

이제 앱에서 Facebook을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 개발자]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ko-KR/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->