<properties
   pageTitle="ARM 템플릿을 사용하여 가상 네트워크 만들기 | Microsoft Azure"
   description="ARM(Azure 리소스 관리자)에서 ARM 템플릿을 사용하여 가상 네트워크를 만드는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos"/>

# ARM 템플릿을 사용하여 가상 네트워크를 만들기

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 VNet을 만드는 방법을 설명합니다. [클래식 배포 모델에서 가상 네트워크를 만들](virtual-networks-create-vnet-classic-pportal.md) 수도 있습니다.

GitHub에서 기존 ARM 템플릿을 다운로드한 후 수정하고 GitHub, PowerShell 및 Azure CLI에서 템플릿을 배포하는 방법에 알아봅니다.

변경하지 않고 GitHub에서 직접 ARM 템플릿을 배포하는 경우 [github에서 템플릿 배포](#deploy-the-arm-template-by-using-click-to-deploy)로 건너뜁니다.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=AcomDC_0211_2016-->