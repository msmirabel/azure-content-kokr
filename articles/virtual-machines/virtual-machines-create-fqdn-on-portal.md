<properties
   pageTitle="Azure 포털에서 VM에 대한 FQDN 만들기 | Microsoft Azure"
   description="Azure 포털에서 가상 컴퓨터를 기반으로 한 리소스 관리자에 대한 정규화된 도메인 이름 또는 FQDN을 만드는 방법에 대해 알아봅니다."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Azure 포털에서 정규화된 도메인 이름 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


**리소스 관리자** 배포 모델을 사용하여 [Azure 포털](https://portal.azure.com)에서 가상 컴퓨터를 만들 때, 포털은 가상 컴퓨터에 대해 공용 IP 리소스를 만듭니다. 이 IP 주소를 사용하여 가상 컴퓨터에 원격으로 액세스할 수 있습니다. 기본적으로 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 가상 컴퓨터를 만들면 이름을 만드는 작업은 매우 쉽습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다.

문서에서는 **리소스 관리자**를 사용하여 포털에서 구독에 로그인하고 사용 가능한 이미지로 가상 컴퓨터를 만들었다고 가정합니다. 가상 컴퓨터가 실행되기 시작되면 다음 단계를 수행합니다.

1.  포털에서 가상 컴퓨터 설정을 보고 공용 IP 주소를 클릭합니다.

    ![locate ip resource](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  공용 IP에 대한 DNS 이름은 비어 있습니다. 공용 IP 블레이드에 대한 **모든 설정**을 클릭합니다.

    ![IP 설정](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  공용 IP 설정에서 **구성** 탭을 엽니다. 원하는 DNS 이름 레이블을 입력하고 이 구성을 **저장**합니다.

    ![enter dns name label](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    이제 공용 IP 리소스가 해당 블레이드에서 이 새 DNS 레이블을 표시합니다.

4.  공용 IP 블레이드를 닫고 포털에서 가상 컴퓨터 블레이드로 돌아갑니다. DNS 이름/FQDN이 공용 IP 리소스에 대한 IP 주소 옆에 나타나는지 확인합니다.

    ![FQDN is created](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    이제 이 DNS 이름을 사용하여 원격으로 가상 컴퓨터에 연결할 수 있습니다. 예를 들어 정규화된 도메인 이름이 `testdnslabel.centralus.cloudapp.azure.com`이고 사용자 이름이 `adminuser`인 Linux 가상 컴퓨터에 연결하는 경우 `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com`을 사용합니다.

<!---HONumber=AcomDC_0128_2016-->