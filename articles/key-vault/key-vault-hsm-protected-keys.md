<properties
	pageTitle="Azure 주요 자격 증명 모음에 대해 HSM 보호된 키를 생성하고 전송하는 방법 | Microsoft Azure"
	description="이 문서를 통해 Azure 주요 자격 증명 모음에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="cabailey"/>
#Azure 주요 자격 증명 모음에 대해 HSM 보호된 키를 생성하고 전송하는 방법

##소개

보안을 강화하기 위해 Azure 주요 자격 증명 모음 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK(*Bring Your Own Key*)라고 합니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. Azure 주요 자격 증명 모음은 HSM의 Thales nShield 제품군을 사용하여 키를 보호합니다.

이 항목의 내용을 통해 Azure 주요 자격 증명 모음에서 사용할 고유의 HSM 보호된 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다.

이 기능은 Azure 중국에 사용할 수 없습니다.

>[AZURE.NOTE] Azure 주요 자격 증명 모음에 대한 자세한 내용은 [Azure 주요 자격 증명 모음이란?](key-vault-whatis.md)을 참조하세요.
>
>HSM 보호된 키를 위해 주요 자격 증명 모음 만들기가 포함된 자습서를 시작하려면 [Azure 주요 자격 증명 모음 시작](key-vault-get-started.md)을 참조하세요.

다음은 인터넷을 통해 HSM 보호된 키를 생성하고 전송하는 작업에 대한 추가 정보입니다.

- 공격에 대한 취약성을 줄이기 위해 오프라인 워크스테이션에서 키를 생성합니다.

- 키는 KEK(키 교환 키)로 암호화되어 Azure 주요 자격 증명 모음 HSM으로 전송될 때까지 암호화 상태를 유지합니다. 암호화된 버전의 키만 원래 워크스테이션을 벗어납니다.

- 도구 집합은 Azure 주요 자격 증명 모음 보안 영역에 키를 바인딩하는 테넌트 키에 대한 속성을 설정합니다. 따라서 Azure 주요 자격 증명 모음 HSM이 키를 받고 암호를 해독한 후에는 이 HSM만 사용할 수 있습니다. 키는 내보낼 수 없습니다. 이 바인딩은 Thales HSM에 의해 적용됩니다.

- 키 암호화에 사용되는 KEK(키 교환 키)는 Azure 주요 자격 증명 모음 HSM 내에서 생성되며 내보낼 수 없습니다. HSM은 HSM 외부에 클리어 버전의 KEK가 있을 수 없도록 강제합니다. 또한 도구 집합에는 KEK는 내보낼 수 없으며 Thales에서 제조한 정품 HSM 내에서 생성된 것이라는 Thales의 증명이 포함되어 있습니다.

- 도구 집합에는 Azure 주요 자격 증명 모음 보안 영역도 Thales에서 제조한 정품 HSM에서 생성된 것이라는 Thales의 증명이 포함되어 있습니다. 이를 통해 Microsoft가 정품 하드웨어를 사용하고 있다는 것을 입증합니다.

- Microsoft는 각 지역 별로 별도의 KEK 및 별도의 Security World를 사용하여 키를 암호화한 지역의 데이터 센터에서만 해당 키를 사용할 수 있도록 합니다. 예를 들어 유럽 고객의 키는 북아메리카 또는 아시아의 데이터 센터에서 사용할 수 없습니다.

##Thales HSM 및 Microsoft 서비스에 대한 추가 정보

Thales e-Security는 금융 서비스, 첨단 기술, 제조, 정부 및 기술 분야에서 데이터 암호화 및 사이버 보안 솔루션을 제공하는 선도적인 글로벌 기업입니다. 기업과 정부의 정보를 보호하는 지난 40년간 누적된 성과를 바탕으로, Thales 솔루션은 최대 에너지 및 항공 우주 기업 다섯 곳 중 네 곳과 22개 NATO 국가에서 사용되고 있으며 전세계 지불 거래의 80퍼센트 이상의 보안을 담당하고 있습니다.

Microsoft는 Thales과의 협력을 통해 HSM을 최첨단 상태로 향상시켰습니다. 이렇게 향상된 기능을 통해 사용자는 자신의 키에 대한 제어를 포기하지 않으면서 호스트된 서비스의 일반적인 이점을 누릴 수 있습니다. Microsoft에서 이러한 향상된 기능을 사용하여 HSM을 관리해 주므로 이 부분에 신경 쓰지 않아도 된다는 것이 특별한 장점입니다. 클라우드 서비스로써 Azure 주요 자격 증명 모음은 급박한 요청에도 크기 확장이 가능하기 때문에 조직의 급증하는 사용량을 맞출 수 있습니다. 그와 동시에 키는 Microsoft의 HSM 내에서 보호됩니다. 키를 생성하고 Microsoft의 HSM에 전송하기 때문에 키의 수명 주기 동안 사용자가 키에 대한 제어를 유지할 수 있습니다.

##Azure 주요 자격 증명 모음에 대한 BYOK(Bring Your Own Key) 구현

고유의 HSM 보호된 키를 생성한 다음 Azure 주요 자격 증명 모음으로 전송하는 경우(BYOK(Bring Your Own Key) 시나리오) 다음 내용 및 절차를 사용합니다.


##BYOK에 대한 필수 조건

Azure 주요 자격 증명 모음에 대해 BYOK(Bring Your Own Key)를 위한 필수 조건 목록은 다음 표를 참조하세요.

|요구 사항|자세한 정보|
|---|---|
|Azure 구독|Azure 주요 자격 증명 모음을 만들려면 Azure 구독이 필요합니다. [무료 평가판 등록](../../../../pricing/free-trial)|
|HSM을 지원하는 Azure 주요 자격 증명 모음|Azure 주요 자격 증명 모음에 대한 서비스 계층 및 기능에 대한 자세한 내용은 [Azure 주요 자격 증명 모음 가격 책정](../../../../pricing/details/key-vault/) 웹 사이트를 참조하세요.|
|Thales HSM, 스마트 카드 및 지원 소프트웨어|Thales 하드웨어 보안 모듈에 대한 액세스 권한 및 Thales HSM의 기본 작동 지식이 있어야 합니다. 호환되는 모델 목록을 보거나, HSM이 없는 경우 구매하려면 [Thales 하드웨어 보안 모듈](https://www.thales-esecurity.com/msrms/buy)을 참조하세요.|
|다음 하드웨어 및 소프트웨어:<ol><li>Windows 7 이상의 Windows 운영 체제를 사용하는 오프라인 x64 워크스테이션 및 11.50 이상 버전의 Thales nShield 소프트웨어<br/><br/>이 워크스테이션이 Windows 7을 실행하는 경우 [Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)를 설치해야 합니다.</li><li>인터넷에 연결되고 Windows 7 이상의 Windows 운영 체제가 있는 워크스테이션</li><li>16MB 이상의 여유 공간이 있는 USB 드라이브 또는 기타 휴대용 저장 장치</li></ol>|보안상의 이유로 첫 번째 워크스테이션은 네트워크에 연결하지 않는 것이 좋습니다. 하지만 이 사항은 프로그램 방식으로 강제 적용되지는 않습니다.<br/><br/>아래 지침에서는 이 워크스테이션을 연결이 끊어진 워크스테이션으로 간주합니다.</p></blockquote><br/>또한 테넌트 키가 프로덕션 네트워크용인 경우 별도의 두 번째 워크스테이션을 사용하여 도구 집합을 다운로드하고 테넌트 키를 업로드하는 것이 좋습니다. 그러나 테스트 목적인 경우에는 첫 번째와 동일한 워크스테이션을 사용할 수 있습니다.<br/><br/>아래 지침에서는 이 두 번째 워크스테이션을 인터넷에 연결된 워크스테이션으로 간주합니다.</p></blockquote><br/>|

##키 생성 및 Azure 주요 자격 증명 모음에 전송

다음 5단계를 사용하여 키를 생성하고 Azure 주요 자격 증명 모음에 전송합니다.

- [1단계: 인터넷에 연결된 워크스테이션 준비](#step-1-prepare-your-internet-connected-workstation)
- [2단계: 연결이 끊어진 워크스테이션 준비](#step-2-prepare-your-disconnected-workstation)
- [3단계: 키 생성](#step-3-generate-your-key)
- [4단계: 전송할 키 준비](#step-4-prepare-your-key-for-transfer)
- [5단계: Azure 주요 자격 증명 모음에 키 전송](#step-5-transfer-your-key-to-azure-key-vault)

## 1단계: 인터넷에 연결된 워크스테이션 준비
이 첫 번째 단계는 인터넷에 연결된 워크스테이션에서 다음 절차를 수행합니다.


###1\.1단계: Azure PowerShell 설치

인터넷에 연결된 워크스테이션에서 Azure 주요 자격 증명 모음을 관리하기 위해 cmdlet이 포함된 Azure PowerShell 모듈을 다운로드하고 설치합니다. 이를 위해 0.8.13 이상 버전이 필요합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

###1\.2단계: Azure 구독 ID 얻기

Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.

		Add-AzureAccount
팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. 그런 다음 [Get-azuresubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) 명령을 사용합니다.

		Get-AzureSubscription
출력된 내용에서 Azure 주요 자격 증명 모음에 사용할 구독 ID를 찾습니다. 이 구독 ID는 나중에 필요합니다.

Azure PowerShell 창을 닫지 마세요.

###1\.3단계: Azure 주요 자격 증명 모음에 대한 BYOK 도구 집합 다운로드

Microsoft 다운로드 센터로 이동하여 해당 지리적 지역 또는 Azure 인스턴스에 대한 [Azure 주요 자격 증명 모음 BYOK 도구 집합을 다운로드](http://www.microsoft.com/download/details.aspx?id=45345)합니다.

|지리적 지역 또는 Azure의 인스턴스|패키지 이름|SHA-256 패키지 해시|
|---|---|---|
|북아메리카|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|유럽|KeyVault-BYOK-Tools-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|아시아|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|라틴 아메리카|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|
|일본|KeyVault-BYOK-Tools-Japan.zip|DB512CD9472FDE2FD610522847DF05E4D7CD49A296EE4A2DD74D43626624A113|
|오스트레일리아|KeyVault-BYOK-Tools-Australia.zip|8EBC69E58E809A67C036B50BB4F1130411AD87A7464E0D61A9E993C797915967|
|[Azure Government](../../../../features/gov/)|KeyVault-BYOK-Tools-USGovCloud.zip|4DE9B33990099E4197ED67D786316F628E5218FC1EB0C24DCAD8A1851FD345B8|

다운로드한 BYOK 도구 집합의 무결성을 확인하려면 Azure PowerShell 세션에서 [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet을 사용합니다.

	Get-FileHash KeyVault-BYOK-Tools-*.zip

도구 집합에는 다음이 포함되어 있습니다.

- 이름이 **BYOK-KEK-pkg-**로 시작하는 KEK(키 교환 키) 패키지
- 이름이 **BYOK-SecurityWorld-pkg-**로 시작하는 Security World 패키지
- 이름이 v**erifykeypackage.py**인 python 스크립트
- 이름이 **KeyTransferRemote.exe**인 명령줄 실행 파일 및 관련 DLL
- 이름이 **vcredist\_x64.exe**인 Visual C++ 재배포 가능 패키지

USB 드라이브 또는 기타 휴대용 저장소에 패키지를 복사합니다.

##2단계: 연결이 끊어진 워크스테이션 준비

이 두 번째 단계에서는 네트워크(인터넷 또는 내부 네트워크)에 연결되지 않은 워크스테이션에서 다음 절차를 수행합니다.


###2\.1단계: Thales HSM이 있는 연결이 끊어진 워크스테이션 준비

Windows 컴퓨터에 nCipher(Thales) 지원 소프트웨어를 설치한 다음 Thales HSM을 해당 컴퓨터에 연결합니다.

Thales 도구가 해당 경로(**%nfast\_home%\\bin** 및 **%nfast\_home%\\python\\bin**)에 있는지 확인합니다. 예를 들어 다음을 입력합니다.

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

자세한 내용은 Thales HSM에 포함된 사용자 가이드를 참조하세요.

###2\.2단계: 연결이 끊어진 워크스테이션에 BYOK 도구 집합 설치

USB 드라이브 또는 기타 휴대용 저장소에서 BYOK 도구 집합 패키지를 복사한 후 다음을 수행합니다.

1. 다운로드한 패키지에서 임의 폴더로 파일을 추출합니다.
2. 해당 폴더에서 vcredist\_x64.exe를 실행합니다.
3. 지침에 따라 Visual Studio 2012용 Visual C++ 런타임 구성 요소를 설치합니다.

##3단계: 키 생성

이 3단계에서는 연결이 끊어진 워크스테이션에서 다음 절차를 수행합니다.

###3\.1단계: 보안 영역 만들기

명령 프롬프트를 시작하고 Thales의 새 영역 프로그램을 실행합니다.

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

이 프로그램은 C:\\ProgramData\\nCipher\\Key Management Data\\local 폴더에 해당하는 %NFAST\_KMDATA%\\local\\world에 **Security World** 파일을 만듭니다. 쿼럼에 다른 값을 사용할 수 있지만, 이 예에서는 각각에 대해 3개의 빈 카드와 핀을 입력하라는 메시지가 표시됩니다. 그러면 임의의 두 카드에서 보안 영역에 대한 모든 권한을 제공합니다. 이러한 카드가 새 보안 영역에 대한 **관리자 카드 집합**이 됩니다.

그런 다음 아래 작업을 수행합니다.

- 영역 파일을 백업합니다. 영역 파일, 관리자 카드, 해당 핀을 보호하고 한 사람이 둘 이상의 카드에 액세스 권한을 가지지 않도록 합니다.

###3\.2단계: 다운로드한 패키지의 유효성 검사

이 단계는 선택 사항이지만 다음 사항을 확인할 수 있으므로 권장됩니다.

- 도구 집합에 포함된 키 교환 키가 정품 Thales HSM에서 생성되었습니다.
- 도구 집합에 포함된 Security World의 해시가 정품 Thales HSM에서 생성되었습니다.
- 키 교환 키를 내보낼 수 없습니다.

>[AZURE.NOTE]다운로드한 패키지의 유효성을 검사하려면 HSM이 연결되고 전원이 켜져 있어야 하며 그 안에 지금 만든 것 같은 보안 영역이 있어야 합니다.

다운로드한 패키지의 유효성을 검사하려면:

1.	해당 지리적 지역 또는 Azure 인스턴스에 따라 다음 중 하나를 입력하여 verifykeypackage.py 스크립트를 실행합니다.
	- 북아메리카:

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- 유럽:

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- 아시아:

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- 라틴 아메리카:

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- 일본:

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
	- 오스트레일리아:

			python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
	- [Azure Government](../../../../features/gov/)의 경우 Azure의 미국 정부 인스턴스를 사용합니다.

			python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1

	>[AZURE.TIP]Thales 소프트웨어에는 %NFAST\_HOME%\\python\\bin에 python이 포함되어 있습니다.

2.	다음 내용이 표시되는지 확인합니다. 이는 유효성 검사가 성공했다는 것입니다. **Result: SUCCESS**

이 스크립트는 서명자 체인의 유효성을 Thales 루트 키까지 검사합니다. 이 루트 키의 해시는 스크립트에 포함되어 있으며 해당 값은 **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**여야 합니다. [Thales 웹 사이트](http://www.thalesesec.com/)에서 이 값을 개별적으로 확인할 수도 있습니다.

이제 새 키를 만들 준비가 되었습니다.

###3\.3단계: 새 키 만들기

Thales **generatekey** 프로그램을 사용하여 키를 생성합니다.

다음 명령을 실행하여 키를 생성합니다.

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

이 명령을 실행할 때 다음 지침을 사용합니다.

- 매개 변수 *보호*는 다음과 같이 값 **모듈**에 설정되어야 합니다. 이렇게 하면 모듈 보호 키가 만들어집니다. BYOK 도구 집합은 OCS 보호 키를 지원하지 않습니다.

- **ident** 및 **plainname**의 *contosokey* 값을 임의의 문자열 값으로 바꿉니다. 관리 오버헤드를 최소화하고 오류 위험성을 줄이려면 두 가지에 동일한 값을 사용하는 것이 좋습니다. **ident** 값은 숫자, 대시 및 소문자만 포함해야 합니다.

- pubexp는 이 예에서 비어 있지만(기본값) 특정 값을 지정할 수 있습니다. 자세한 내용은 Thales 설명서를 참조하세요.

이 명령은 %NFAST\_KMDATA%\\local 폴더에 토큰화된 키 파일을 만듭니다. 이 파일은 이름이 **key\_simple\_**로 시작하며 명령에서 지정한 ident가 뒤에 붙습니다. 예들 들어 **key\_simple\_contosokey**입니다. 이 파일은 암호화된 키를 포함합니다.

안전한 위치에 이 토큰화된 키 파일을 백업합니다.

>[AZURE.IMPORTANT] 나중에 Azure 주요 자격 증명 모음에 키를 전송하는 경우 Microsoft에서는 이 키를 사용자에게 다시 내보낼 수 없으므로 키 및 보안 영역을 안전하게 백업하는 것이 매우 중요합니다. 키 백업에 대한 지침 및 모범 사례는 Thales에 문의하세요.

이제 Azure 주요 자격 증명 모음에 키를 전송할 준비가 되었습니다.

##4단계: 전송할 키 준비

이 4단계에서는 연결이 끊어진 워크스테이션에서 다음 절차를 수행합니다.

###4\.1단계: 축소된 권한을 가진 키의 복사본을 만듭니다.

키에 대한 권한을 축소하려면 명령 프롬프트에서 해당 지리적 지역 또는 Azure 인스턴스에 따라 다음 중 하나를 실행합니다.

- 북아메리카:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- 유럽:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- 아시아:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- 라틴 아메리카:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- 일본:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- 오스트레일리아:

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- [Azure Government](../../../../features/gov/)의 경우 Azure의 미국 정부 인스턴스를 사용합니다.

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1

이 명령을 실행할 때 *contosokey*를 [키 생성](#step-3-generate-your-key) 단계의 **3.3단계: 새 키 만들기**에서 지정한 값과 동일한 값으로 바꿉니다.

보안 영역 관리자 카드를 플러그인하라는 메시지가 표시됩니다.

명령이 완료되면 **Result: SUCCESS**가 표시되고 축소된 권한을 가진 키 복사본이 key\_xferacId\_<contosokey>라는 파일에 저장됩니다.

###4\.2단계: 키의 새 복사본 검사

필요에 따라 Thales 유틸리티를 실행하여 새 키에 최소한의 권한이 있는지 확인합니다.

- aclprint.py:

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
이러한 명령을 실행할 때 contosokey를 [키 생성](#step-3-generate-your-key) 단계의 **3.3단계: 새 키 만들기**에서 지정한 값과 동일한 값으로 바꿉니다.

###4\.3단계: Microsoft의 키 교환 키를 사용하여 키 암호화

지리적 지역 또는 Azure의 인스턴스에 따라 다음 명령 중 하나를 실행합니다.

- 북아메리카:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 유럽:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 아시아:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 라틴 아메리카:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 일본:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 오스트레일리아:

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- [Azure Government](../../../../features/gov/)의 경우 Azure의 미국 정부 인스턴스를 사용합니다.

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

이 명령을 실행할 때 다음 지침을 사용합니다.

- *contosokey*를 [키 생성](#step-3-generate-your-key) 단계의 **3.3단계: 새 키 만들기**에서 키를 생성하기 위해 사용한 식별자로 바꿉니다.

- *SubscriptionID*를 주요 자격 증명 모음이 포함된 Azure 구독 ID로 바꿉니다. [인터넷에 연결된 워크스테이션 준비](#step-1-prepare-your-internet-connected-workstation) 단계의 **1.2단계: Azure 구독 ID 가져오기**에서 이 값을 검색했습니다.

- *ContosoFirstHSMKey*를 출력 파일 이름에 사용할 레이블로 바꿉니다.

이 작업이 성공적으로 완료되면 **Result: SUCCESS**가 표시되고 현재 폴더에 TransferPackage-*ContosoFirstHSMkey*.byok라는 이름의 새 파일이 생성됩니다.

###4\.4단계: 인터넷에 연결된 워크스테이션에 키 전송 패키지 복사

USB 드라이브 또는 기타 휴대용 저장소를 사용하여 인터넷에 연결된 워크스테이션에 이전 단계의 출력 파일(KeyTransferPackage-ContosoFirstHSMkey.byok)을 복사합니다.

##5단계: Azure 주요 자격 증명 모음에 키 전송

이 마지막 단계에서는 인터넷에 연결된 워크스테이션에서 [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) cmdlet을 사용하여 연결이 끊어진 워크스테이션에서 Azure 주요 자격 증명 모음 HSM으로 복사한 키 전송 패키지를 업로드합니다.

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

업로드가 성공하면 방금 추가한 키의 속성이 표시됩니다.


##다음 단계

이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은 [Azure 주요 자격 증명 모음 시작](key-vault-get-started.md) 자습서에서 **HSM(하드웨어 보안 모듈)을 사용하려는 경우**를 참조하세요.

<!---HONumber=AcomDC_0204_2016-->