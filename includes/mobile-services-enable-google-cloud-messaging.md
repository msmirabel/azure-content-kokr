
1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명으로 로그인합니다. 
 
2. **프로젝트 만들기**를 클릭하고 프로젝트 이름을 입력한 후에 **만들기**를 클릭합니다. 요청된 경우 SMS 확인을 수행하고 **Create**를 다시 클릭합니다.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 새 **프로젝트 이름**을 입력하고 **프로젝트 만들기**를 클릭합니다.

3. **Projects** 섹션에 있는 프로젝트 번호를 기록해 둡니다. 이 값을 클라이언트의 *PROJECT\_ID* 변수로 설정해야 합니다.

4. 프로젝트 대시보드에서 **Google API 사용** > **Android용 Cloud Messaging**를 클릭하고 다음 페이지에서 **API 사용**을 클릭합니다.

5. API 관리자에서 **자격 증명** > **자격 증명 추가** > **API 키**를 클릭합니다.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. **새 키 만들기**에서 **서버 키**를 클릭하고 사용자의 키에 대한 이름을 입력한 다음 **만들기**를 클릭합니다.

7. **API key** 값을 기록해 둡니다.

	이 API 키를 사용하여 Azure 에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

<!---HONumber=AcomDC_1203_2015-->