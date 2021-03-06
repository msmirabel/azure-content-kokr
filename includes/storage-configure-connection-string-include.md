## 저장소 연결 문자열 설정

Azure Storage Client Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 응용 프로그램에 직접 하드 코딩하지 않고 구성 파일에서 저장소 연결 문자열을 유지하는 것이 좋습니다. 연결 문자열을 저장할 수 있는 옵션은 다음과 같이 두 가지입니다.

- 응용프로그램이 Azure 클라우드 서비스에서 실행되는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장합니다. Azure 클라우드 서비스 구성에 대한 자세한 내용은 [클라우드 서비스 만들기 및 배포 방법](../articles/cloud-services/cloud-services-how-to-create-deploy.md)을 참조하세요.
- 응용프로그램이 Azure 가상 컴퓨터에서 실행하거나 Azure 외부에서 실행되는 .NET 응용프로그램을 빌드하는 경우 .NET 구성 시스템(예: `web.config` 또는 `app.config` 파일)을 사용하여 연결 문자열을 저장합니다.

이 가이드의 뒷부분에서 코드의 연결 문자열을 검색하는 방법을 보여 줍니다.

### Azure 클라우드 서비스에서 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. ![Visual Studio에서 클라우드 서비스 역할의 속성 선택][connection-string1]

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다. ![Visual Studio에서 클라우드 서비스 설정 추가][connection-string2]

    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다. ![연결 문자열 유형 설정][connection-string3]

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화 상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Microsoft Azure 저장소)를 대상으로 지정할지 또는 클라우드의 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 어떤 옵션을 사용하든 작동합니다.

	> [AZURE.NOTE] Azure 저장소와 연결하여 비용이 초래되지 않도록 저장소 에뮬레이터를 대상으로 할 수 있습니다. 그러나 클라우드에서 Azure 저장소 계정을 대상으로 하도록 선택하는 경우 이 자습서를 수행하는 비용은 무시됩니다.

	클라우드에서 저장소 계정을 대상으로 하는 경우에 해당 저장소 계정에 대한 기본 액세스 키를 입력합니다. Azure 관리 포털을 통해 기본 액세스 키를 복사 하는 방법을 알아보려면 [저장소 액세스 키 보기 및 복사](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)를 참조하세요.

	> [AZURE.NOTE] 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 키를 보호해야 합니다. 다른 사용자에게 배포하거나 다른 사람이 액세스할 수 있는 일반 텍스트 파일에 저장하지 마십시오. 손상되었다고 생각되면 관리 포털을 사용하여 키를 다시 생성합니다.
	
    ![대상 환경 선택][connection-string4]

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 친근한 이름으로 변경합니다. 이 가이드의 코드 뒷부분에서 이 연결 문자열을 참조합니다. ![연결 문자열 이름 변경][connection-string5]
	
### .NET 구성을 사용하여 연결 문자열 구성

Azure 클라우드 서비스가 아닌 응용프로그램을 작성하는 경우(이전 섹션 참조) .NET 구성 시스템을 사용하는 것이 좋습니다(예: `web.config` 또는 `app.config`). 여기에는 Azure 웹 사이트 또는 Azure 가상 컴퓨터와 Azure 외부에서 실행되도록 설계된 응용 프로그램이 포함됩니다. 다음과 같이 `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다. 저장소 계정의 이름으로 `account-name`을(를) 바꾸고를 계정 액세스 키로 `account-key`을(를) 바꿉니다.

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

예를 들어 구성 파일의 구성 설정은 다음과 유사합니다.

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!---HONumber=AcomDC_0218_2016-->