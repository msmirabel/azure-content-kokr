<properties
 pageTitle="Azure VM에서 HPC 팩 헤드 노드 만들기 | Microsoft Azure"
 description="Azure 포털 및 리소스 관리자 배포 모델을 사용하여 Azure VM에 Microsoft HPC 팩 헤드 노드를 만드는 방법을 알아봅니다."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# 마켓플레이스 이미지를 사용하여 Azure VM에 HPC 팩 클러스터의 헤드 노드 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


이 문서에서는 Azure 마켓플레이스에서 [Microsoft HPC 팩 가상 컴퓨터 이미지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)를 사용하여 Azure 포털로 HPC 클러스터의 헤드 노드를 만드는 방법을 보여 줍니다. 이 HPC 팩 VM 이미지는 HPC 팩 2012 R2 업데이트 3이 미리 설치된 Windows Server 2012 R2 Datacenter를 기준으로 합니다. Azure에서 HPC 팩의 개념 증명 배포에 대해 이 헤드 노드를 사용합니다. 그런 다음 계산 리소스를 HPC 워크로드를 실행하는 클러스터에 추가할 수 있습니다.


![HPC 팩 헤드 노드][headnode]

>[AZURE.TIP]Azure에서 전체 HPC 팩 클러스터를 프로덕션 배포하는 경우 [HPC 팩 IaaS 배포 스크립트](virtual-machines-hpcpack-cluster-powershell-script.md) 또는 [Windows 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) 템플릿과 같은 리소스 관리자 템플릿 등의 자동화된 방법을 사용하는 것이 좋습니다. 추가 템플릿에 대한 자세한 내용은 [Azure의 HPC 팩 클러스터 옵션](virtual-machines-hpcpack-cluster-options.md)을 참조하세요.

## 고려 사항 계획

* **Active Directory 도메인** - VM에서 HPC 서비스를 시작하기 전에 Azure에서 HPC 팩 헤드 노드를 Active Directory 도메인에 연결해야 합니다. 이 문서에서 설명한 것처럼 개념 증명 배포의 경우 HPC 서비스를 시작하기 전에 도메인 컨트롤러인 헤드 노드에 대해 만드는 VM을 승격할 수 있습니다. VM을 연결하는 Azure에 별도의 도메인 컨트롤러 및 포리스트를 배포하는 방법도 있습니다.

* **Azure 가상 네트워크** - 이 문서에서 설명한 것처럼 Azure 포털에서 리소스 관리자 배포 모델을 사용하여 HPC 팩 헤드 노드를 배포할 때 Azure VNet(가상 네트워크)을 지정하거나 만듭니다. 나중에 클러스터 계산 노드 VM을 HPC 클러스터에 추가하거나 헤드 노드를 기존 Active Directory 도메인에 연결하는 경우 VNet이 필요합니다.

    
## 헤드 노드를 만드는 단계

다음은 Azure 포털에서 리소스 관리자 배포를 사용하여 HPC 팩 헤드 노드용 Azure VM을 만들기 위한 높은 수준의 단계입니다.


1. 별도 도메인 컨트롤러 VM으로 Azure에 새 Active Directory 포리스트를 만들려는 경우 [리소스 관리자 템플릿](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/)을 사용하는 방법이 있습니다. 간단한 개념 증명 배포의 경우 이 단계를 생략하고 다음 단계에 설명된 대로 헤드 노드 VM 자체를 도메인 컨트롤러로 구성해도 됩니다.
    
2. [Azure 포털](https://portal.azure.com)에서 Azure 마켓플레이스의 HPC 팩 2012 R2 이미지를 선택합니다. 이 작업을 수행하려면 **새로 만들기**를 클릭하고 **HPC 팩**용 마켓플레이스를 검색합니다. **Windows Server 2012 R2에 있는 HPC 팩 2012 R2**를 선택합니다.

3. **Windows Server 2012 R2에 있는 HPC 팩 2012 R2** 페이지에서 **리소스 관리자** 배포 모델을 선택한 다음 **만들기**를 클릭합니다.

    ![HPC 팩 이미지][marketplace]

4. 포털을 사용하여 설정을 구성하고 VM을 만듭니다. 자세한 단계는 자습서 [Azure 포털에서 Windows 가상 컴퓨터 만들기](virtual-machines-windows-tutorial.md)를 따르세요. 처음 배포하는 경우 일반적으로 많은 기본 설정 또는 권장 설정을 적용할 수 있습니다.

    **VNet 고려 사항**

   * **설정**에서 새 VNet을 만드는 경우 개인 네트워크 주소 범위(예: 10.0.0.0/16)와 서브넷 주소 범위(예: 10.0.0.0/24)를 지정합니다.
    
4. VM을 만든 다음 VM이 실행되면 [VM에 연결합니다](virtual-machines-log-on-windows-server-preview.md). 

5. VM을 기존 도메인 포리스트에 연결하거나 VM 자체에 새 도메인 포리스트를 만듭니다.

    **Active Directory 도메인 고려 사항**

    * 기존 도메인 포리스트가 있는 Azure VNet에 VM을 만든 경우 표준 서버 관리자 또는 Windows PowerShell 도구를 사용하여 도메인 포리스트에 연결합니다. 그런 다음 다시 시작합니다.

    * 기존 도메인 포리스트가 없는 VNet에 VM을 만든 경우 도메인 컨트롤러로 승격합니다. 이 작업을 수행하려면 표준 서버 관리자 또는 Windows PowerShell 도구를 사용하여 설치하고 Active Directory 도메인 서비스 역할을 구성합니다. 자세한 단계를 보려면 [새 Windows Server 2012 Active Directory 포리스트 설치](https://technet.microsoft.com/library/jj574166.aspx)를 참조하세요.

5. VM이 실행되고 Active Directory 포리스트에 연결되면 헤드 노드에서 HPC 팩 서비스를 시작합니다. 다음을 수행합니다.

    a. 로컬 관리자 그룹의 구성원인 도메인 계정을 사용하여 VM에 연결합니다. 예를 들어 헤드 노드 VM을 만들 때 설정한 관리자 계정을 사용할 수 있습니다.

    b. 기본 헤드 노드 구성의 경우 관리자 권한으로 Windows PowerShell을 시작하고 다음을 입력합니다.

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    HPC 팩 서비스가 시작하는 데 몇 분이 소요될 수 있습니다.

    추가 헤드 노드 구성 옵션을 보려면 `get-help HPCHNPrepare.ps1`을 입력합니다.


## 다음 단계

* 이제 HPC 팩 클러스터의 헤드 노드를 사용할 수 있습니다. 예를 들어 HPC 클러스터 관리자를 시작하고 [배포 할 일 모음](https://technet.microsoft.com/library/jj884141.aspx)을 완료합니다.
* 클라우드 서비스에 [Azure 버스트 노드](virtual-machines-hpcpack-cluster-node-burst.md)를 추가하여 필요 시 클러스터 계산 용량을 늘립니다. 

* 클러스터에서 테스트 워크로드를 실행합니다. 예를 보려면 HPC 팩 [시작하기 가이드](https://technet.microsoft.com/library/jj884144)를 참조하세요.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0211_2016-->