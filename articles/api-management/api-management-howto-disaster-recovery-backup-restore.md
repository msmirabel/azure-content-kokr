<properties 
	pageTitle="Azure API 관리에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법" 
	description="Azure API 관리에서 백업 및 복원을 사용하여 재해 복구를 수행하는 방법에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="sdanie"/>

# Azure API 관리에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법

Azure API 관리를 통해 API를 게시 및 관리하면 기존에는 직접 디자인, 구현 및 관리해야 했던 대부분의 내결함성 및 인프라 기능을 활용할 수 있습니다. Azure 플랫폼은 매우 적은 비용으로 상당한 잠재적 오류를 완화합니다.

API 관리 서비스를 호스트하는 지역에 영향을 주는 가용성 문제로부터 서비스를 복구하려면 언제든지 다른 지역에서 서비스를 다시 구축할 준비가 되어 있어야 합니다. 가용성 목표와 복구 시간 목표에 따라서는 하나 이상의 지역에서 백업 서비스를 예약하고 해당 구성과 콘텐츠를 활성 서비스와 동기화 상태로 유지 관리할 수 있습니다. 서비스 백업 및 복원 기능은 재해 복구 전략을 구현하는 데 필요한 구성 요소를 제공합니다.

이 가이드에서는 Azure 리소스 관리자 요청을 인증하는 방법과 API 관리 서비스 인스턴스를 백업 및 복원하는 방법을 보여줍니다.

>[AZURE.NOTE] 재해 복구를 위한 API 관리 서비스 인스턴스를 백업 및 복원하는 프로세스는 스테이징과 같은 시나리오에 대한 API 관리 서비스 인스턴스를 복제하는 데 사용할 수도 있습니다.
>
>각 백업은 7일 후 만료되니 유의하세요. 7일의 만료 기간이 만료된 후 백업을 복원하려고 하면 `Cannot restore: backup expired` 메시지와 함께 복원이 실패합니다.

## Azure 리소스 관리자 요청 인증

>[AZURE.IMPORTANT] 백업 및 복원을 위한 REST API는 Azure 리소스 관리자를 사용하며 API 관리 엔터티를 관리하기 위한 REST API와는 다른 인증 메커니즘입니다. 이 섹션의 단계에는 Azure 리소스 관리자 요청을 인증하는 방법을 설명합니다. 자세한 내용은 [Azure 리소스 관리자 요청 인증](http://msdn.microsoft.com/library/azure/dn790557.aspx)을 참조하세요.

Azure 리소스 관리자를 사용하여 리소스에서 수행하는 모든 작업은 다음 단계를 사용하여 Azure Active Directory에서 인증되어야 합니다.

-	응용 프로그램을 Azure Active Directory 테넌트에 추가합니다.
-	추가한 응용 프로그램에 대한 권한을 설정합니다.
-	Azure 리소스 관리자에 대한 요청을 인증하기 위한 토큰을 가져옵니다.

첫번째 단계는 Azure Active Directory 응용 프로그램을 만드는 것입니다. API 관리 서비스 인스턴스를 포함하는 구독을 사용하여 [Azure 클래식 포털](http://manage.windowsazure.com/)에 로그인하고 기본 Azure Active Directory에 대한 **응용 프로그램** 탭으로 이동합니다.

>[AZURE.NOTE] Azure Active Directory 기본 디렉토리에 사용자의 계정이 표시되지않는 경우, 계정에 필요한 사용 권한을 부여하려면 Azure 구독의 관리자에게 문의하세요. 기본 디렉터리 찾기에 대한 내용은 [기본 디렉토리 찾기](../virtual-machines/resource-group-create-work-id-from-persona.md/#locate-your-default-directory-in-the-azure-portal)를 참조하세요.

![Azure Active Directory 응용 프로그램 만들기][api-management-add-aad-application]

**추가**, **조직에서 개발 중인 응용 프로그램 추가**를 선택하고 **네이티브 클라이언트 응용 프로그램**을 선택합니다. 설명하는 이름을 입력하고 다음 화살표를 클릭합니다. **리디렉션 URI**로 `http://resources`와 같은 자리 표시자 URL을 입력하고, 필수 필드지만 값은 나중에 사용되지 않습니다. 응용 프로그램을 저장하려면이 확인란을 클릭합니다.

응용 프로그램이 저장되면, **구성**을 클릭하고 **다른 응용 프로그램에 권한 추가** 섹션을 아래로 스크롤하고 **응용 프로그램 추가**를 클릭합니다.

![권한 추가][api-management-aad-permissions-add]

**Windows** **Azure 서비스 관리 API**를 선택하고 이 확인란을 클릭하여 응용 프로그램을 추가합니다.

![권한 추가][api-management-aad-permissions]

새로 추가된 **Windows** **Azure 서비스 관리 API** 응용 프로그램 확인란 옆의 **위임된 권한**을 클릭하고 ** Azure 서비스 관리 액세스(미리 보기)**의 상자를 선택하고 **저장**을 클릭합니다.

![권한 추가][api-management-aad-delegated-permissions]

백업 및 복원을 생성하는 API를 호출하기 전에 토큰을 가져와야 합니다. 다음 예제는 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) nuget 패키지를 사용하여 토큰을 검색합니다.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System;

	namespace GetTokenResourceManagerRequests
	{
        class Program
	    {
	        static void Main(string[] args)
	        {
	            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
	            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

	            if (result == null) {
	                throw new InvalidOperationException("Failed to obtain the JWT token");
	            }

	            Console.WriteLine(result.AccessToken);

	            Console.ReadLine();
	        }
    	}
	}

다음 지침을 사용하여 `{tentand id}`, `{application id}`, 및 `{redirect uri}`을 바꿉니다.

`{tenant id}`를 방금 만든 Azure Active Directory 응용 프로그램의 테넌트 ID로 바꿉니다. **끝점 보기**를 클릭하여 ID에 액세스할 수 있습니다.

![끝점][api-management-aad-default-directory]

![끝점][api-management-endpoint]

Azure Active Directory 응용 프로그램의 **구성** 탭의 **리디렉션 Uri** 섹션에서 **클라이언트 ID** 및 URL을 사용하여 `{application id}` 및 `{redirect uri}`를 대체합니다.

![리소스][api-management-aad-resources]

값이 지정되면 코드 예제에서는 다음 예와 유사한 토큰을 반환해야 합니다.

![신뢰][api-management-arm-token]

다음 섹션에서 설명하는 백업 및 복원 작업을 호출하기 전에, REST 호출에 대한 권한 부여 요청 헤더를 설정합니다.

	request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>API 관리 서비스 백업
API 관리 서비스를 백업하려면 다음 HTTP 요청을 실행합니다.

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

설명:

* `subscriptionId` - 백업할 API 관리 서비스를 포함하는 구독의 ID입니다.
* `resourceGroupName` - 'Api-Default-{service-region}' 형식의 문자열입니다. 여기서 `service-region`은 백업할 API 관리 서비스가 호스트되는 Azure 지역(예: `North-Central-US`)을 식별합니다.
* `serviceName` - 백업을 만드는 API 관리 서비스를 만들 때 지정하는 이름입니다.
* `api-version` - `2014-02-14`로 바꿉니다.

요청 본문에서 대상 Azure 저장소 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 지정합니다.

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

백업은 오랫동안 실행되는 작업으로, 완료되려면 몇 분이 걸릴 수 있습니다. 요청이 정상적으로 실행되어 백업 프로세스가 시작되면 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 백업이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK`이면 백업 작업이 정상적으로 완료된 것입니다.

**참고**:

- 요청 본문에 지정된 **Container**가 **있어야 합니다**.
* 백업이 진행 중인 동안에는 SKU 업그레이드/다운그레이드, 도메인 이름 변경 등의 **서비스 관리 작업을 수행하면 안 됩니다**. 
* 백업 복원은 생성 시점부터 **7일 동안만 보장**됩니다. 
* 분석 보고서를 만드는 데 사용되는 **사용 현황 데이터**는 백업에 **포함되지 않습니다**. [Azure API 관리 REST API][]를 사용하여 분석 보고서를 주기적으로 검색한 다음 안전하게 보관하세요.
* 서비스 백업을 수행하는 빈도는 복구 지점 목표에 영향을 줍니다. 영향을 최소화하려면 정기 백업을 구현함과 동시에 API 관리 서비스에 대한 중요 변경을 수행한 후 요청 시 백업도 수행하는 것이 좋습니다.
* 백업 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성을 **변경**하는 경우 **해당 내용이 백업에 포함되지 않아 손실될 수 있습니다**.

## <a name="step2"> </a>API 관리 서비스 복원
이전에 만든 백업에서 API 관리 서비스를 복원하려면 다음 HTTP 요청을 실행합니다.

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

설명:

* `subscriptionId` - 백업을 복원할 API 관리 서비스를 포함하는 구독의 ID입니다.
* `resourceGroupName` - 'Api-Default-{service-region}' 형식의 문자열입니다. 여기서 `service-region`은 백업을 복원할 API 관리 서비스가 호스트되는 Azure 지역(예: `North-Central-US`)을 식별합니다.
* `serviceName` - 백업을 복원할 API 관리 서비스를 만들 때 지정한 이름입니다.
* `api-version` - `2014-02-14`로 바꿉니다.

요청 본문에서 백업 파일 위치(Azure 저장소 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름)를 지정합니다.

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

복원은 오랫동안 실행되는 작업으로, 완료되려면 30분 이상이 걸릴 수 있습니다. 요청이 정상적으로 실행되어 복원 프로세스가 시작되면 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 복원이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK`이면 복원 작업이 정상적으로 완료된 것입니다.

>[AZURE.IMPORTANT] 백업을 복원할 서비스의 **SKU**는 복원하려는 백업된 서비스의 SKU와 **일치**해야 합니다.
>
>복원 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성에 적용된 **변경 내용**을 **덮어쓸 수 있습니다**.

## 다음 단계
백업/복원 프로세스의 서로 다른 두 연습에 대한 다음 Microsoft 블로그를 체크아웃합니다.

-	[Azure API 관리 계정 복제](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
	-	이 문서를 작성 하는 데 도움을 주신 Gisela에게 감사드립니다.
-	[Azure API 관리: 백업 및 복원 구성](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
	-	Stuart에서 구체화된 접근 방식은 공식 지침과 일치하지 않지만 매우 흥미롭습니다.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API 관리 REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 

<!---HONumber=AcomDC_0302_2016-->