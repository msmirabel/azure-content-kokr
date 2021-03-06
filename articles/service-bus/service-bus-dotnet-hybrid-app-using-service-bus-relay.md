<properties
	pageTitle="하이브리드 온-프레미스/클라우드 응용 프로그램(.NET) | Microsoft Azure"
	description="Azure 서비스 버스 릴레이를 사용하여 .NET 온-프레미스/클라우드 하이브리드 응용 프로그램을 만드는 방법에 대해 알아봅니다."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Azure 서비스 버스 릴레이를 사용하는 .NET 온-프레미스/클라우드 하이브리드 응용 프로그램

## 소개

Visual Studio 2013 및 무료로 제공되는 Azure SDK for .NET을 사용하면 Microsoft Azure로 하이브리드 클라우드 응용 프로그램을 개발하는 일이 간단합니다. 이 문서에서는 이전에 Azure를 사용한 경험이 없는 사람을 대상으로 합니다. 30분 이내에 여러 Azure 리소스를 사용하는 응용 프로그램을 클라우드에서 실행할 수 있습니다.

다음 내용을 배웁니다.

-   웹 서비스를 만들거나 기존 웹 서비스를 웹 솔루션에서 사용할 수 있도록 변경하는 방법
-   Azure 서비스 버스 릴레이를 사용하여 Azure 응용 프로그램과 다른 위치에서 호스트되는 웹 서비스 사이에 데이터를 공유하는 방법

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 하이브리드 솔루션에 유용한 서비스 버스 릴레이

비즈니스 솔루션은 일반적으로 새로운 고유 비즈니스 요구 사항에 부합하도록 작성된 사용자 지정 코드와 이미 있는 솔루션 및 시스템에서 제공하는 기존 기능의 조합으로 구성됩니다.

솔루션 설계자는 확장 요구 사항을 더 간편하게 처리하고 운영 비용을 낮추기 위해 클라우드를 사용하기 시작했습니다. 클라우드를 사용하면 솔루션의 구성 요소로 활용할 기존 서비스 자산이 회사 방화벽 내부에 위치하게 되며 클라우드 솔루션에서 이 자산에 쉽게 액세스할 수 없게 됩니다. 다수의 내부 서비스는 회사 네트워크 가장자리에서 쉽게 노출될 수 있는 방식으로 빌드되거나 호스트되지 않습니다.

서비스 버스 릴레이는 회사 네트워크 인프라에 대한 침입적인 변경 없이도 회사 범위 외부에 있는 솔루션에서 기존 WCF(Windows Communication Foundation) 웹 서비스에 안전하게 액세스할 수 있게 만드는 사용 사례를 위해 고안되었습니다. 이러한 서비스 버스 릴레이 서비스는 기존 환경 내에 계속 호스트되지만 클라우드 호스트 서비스 버스에 들어오는 세션 및 요청에 대한 수신을 위임합니다. 또한 서비스 버스는 [공유 액세스 서명](https://msdn.microsoft.com/library/dn170478.aspx)(SAS) 인증을 사용하여 이러한 서비스에 무단으로 액세스하지 못하도록 보호합니다.

## 솔루션 시나리오

이 자습서에서는 제품 재고 페이지에 제품 목록을 표시할 수 있는 ASP.NET MVC 웹 사이트를 만듭니다.

![][0]

이 자습서는 기존 온-프레미스 시스템에 제품 정보가 있으며 해당 시스템에 도달하는 데 서비스 버스 릴레이를 사용하는 개발자를 대상으로 합니다. 간단한 콘솔 응용 프로그램에서 실행되며 메모리 내 제품 집합에서 지원되는 웹 서비스에서 이를 시뮬레이션합니다. 개발자는 자신의 컴퓨터에서 이 콘솔 응용 프로그램을 실행하여 Azure에 웹 역할을 배포할 수 있습니다. 이렇게 함으로써, 개발자의 컴퓨터가 하나 이상의 방화벽 및 NAT(Network Address Translation) 계층 뒤에 위치하는 것이 거의 확실한 경우에도 Azure 데이터 센터에서 실행 중인 웹 역할이 실제로 개발자의 컴퓨터에 호출되는 것을 확인하게 됩니다.

다음은 완료된 웹 응용 프로그램의 시작 페이지 스크린샷입니다.

![][1]

## 개발 환경 설정

Azure 응용 프로그램 개발을 시작하려면 먼저 도구를 얻고 개발 환경을 설정해야 합니다.

1.  [도구 및 SDK 가져오기][]에서 .NET 용 Azure SDK를 설치합니다.

2. 	사용하고 있는 Visual Studio 버전에 대한 **SDK 설치**를 클릭합니다. 이 자습서의 단계에서는 Visual Studio 2013을 사용합니다.

	![][42]

4.  설치 관리자를 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.

    ![][2]

5.  **웹 플랫폼 설치 관리자**에서 **설치**를 클릭하여 설치를 계속합니다.

    ![][3]

6.  설치가 완료되면 앱을 개발하기 시작하는 데 필요한 내용이 모두 준비된 것입니다. SDK에는 Visual Studio에서 Azure 응용 프로그램을 쉽게 개발할 수 있는 도구가 포함되어 있습니다. Visual Studio가 설치되어 있지 않으면 SDK에서 무료로 제공되는 Visual Studio Express도 설치합니다.

## 서비스 네임스페이스 만들기

Azure에서 서비스 버스 기능 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

[Azure 클래식 포털][] 또는 Visual Studio 서버 탐색기를 사용하여 네임스페이스와 서비스 버스 메시징 엔터티를 관리할 수 있지만 포털 내부에서 새 네임스페이스를 만들 수는 없습니다.

### Azure 클래식 포털을 사용하여 네임스페이스 만들기

1.  [Azure 클래식 포털][]에 로그인합니다.

2.  포털의 왼쪽 탐색 창에서 **서비스 버스**를 클릭합니다.

3.  포털의 아래쪽 창에서 **만들기**를 클릭합니다.

    ![][5]

4.  **새 네임스페이스 추가** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다. ![][6]

5.  네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다(계산 리소스를 배포할 국가/지역과 같아야 함).

    > [AZURE.IMPORTANT] 응용 프로그램을 배포하도록 선택할 지역과 *같은 지역*을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6.	대화 상자의 다른 필드는 기본값으로 그대로 두고(**메시징** 및 **표준** 계층) 확인 표시를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

	![][38]

만든 네임스페이스가 Azure 클래식 포털에 표시되며, 활성화되는 데 약간의 시간이 걸립니다. 계속하기 전에 **활성** 상태가 될 때까지 기다리십시오.

## 네임스페이스에 대한 기본 관리 자격 증명 얻기

새 네임스페이스에 대해 메시징 엔터티를 만드는 것과 같은 관리 작업을 수행하려면 네임스페이스에 대한 자격 증명을 얻어야 합니다.

1.  주 창에서 서비스 네임스페이스의 이름을 클릭합니다.

	![][39]

2.  **Connection Information**을 클릭합니다.

	![][40]

3.  **Access 연결 정보** 창에서 SAS 키 및 키 이름이 포함된 연결 문자열을 찾습니다.

	![][45]

4.  이러한 자격 증명을 적어 두거나 클립보드에 복사해 둡니다.

## 온-프레미스 서버 만들기

먼저, (모의) 온-프레미스 제품 카탈로그 시스템을 빌드합니다. 매우 간단합니다. 통합하려는 전체 서비스 표면이 있는 실제 온-프레미스 제품 카탈로그 시스템을 나타내는 것으로 생각하면 됩니다.

이 프로젝트는 Visual Studio 콘솔 응용 프로그램으로 시작합니다. 프로젝트는 서비스 버스 NuGet 패키지를 사용하여 서비스 버스 라이브러리 및 구성 설정을 포함합니다. NuGet Visual Studio 확장을 사용하면 Visual Studio 및 Visual Studio Express에서 라이브러리와 도구를 쉽게 설치 및 업데이트할 수 있습니다. 서비스 버스 NuGet 패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다. NuGet 및 서비스 버스 패키지 사용에 대한 자세한 내용은 [NuGet 서비스 버스 패키지 사용][]을 참조하십시오.

### 프로젝트 만들기

1.  관리자 권한을 사용하여 Microsoft Visual Studio 2013 또는 Microsoft Visual Studio Express를 시작합니다. 관리자 권한으로 Visual Studio를 시작하려면 **Microsoft Visual Studio 2013(또는 Microsoft Visual Studio Express)**을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭합니다.

2.  Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

    ![][10]

3.  **설치된 템플릿**에서 **Visual C#** 아래에 있는 **콘솔 응용 프로그램**을 클릭합니다. **이름** 상자에서 이름을 **ProductsServer**로 입력합니다.

    ![][11]

4.  **확인**을 클릭하여 **ProductsServer** 프로젝트를 만듭니다.

5.  솔루션 탐색기에서 **ProductsServer**를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

6.  왼쪽의 **응용 프로그램** 탭을 클릭한 후 **.NET Framework 4** 또는 **.NET Framework 4.5**가 **대상 프레임워크**에 표시되는지 확인합니다. 그렇지 않는 경우 목록에서 선택한 후 프로젝트를 다시 로드하라는 메시지가 나타나면 **예**를 클릭합니다.

    ![][12]

7.  Visual Studio용 NuGet 패키지 관리자를 이미 설치한 경우 다음 단계로 건너뜁니다. 그렇지 않으면 [NuGet][]을 방문하여 [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)을 클릭합니다. 나타나는 메시지에 따라 NuGet 패키지 관리자를 설치한 후 Visual Studio를 다시 시작합니다.

7.  솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

8.  **NuGet** 대화 상자의 왼쪽 행에서 **온라인**을 클릭합니다.

9. 	오른쪽 열에서 **검색** 상자를 클릭하고 "**서비스 버스**"를 입력한 후 **Microsoft Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

    ![][13]

    필요한 클라이언트 어셈블리가 이제 참조됩니다.

9.  제품 계약의 새 클래스를 추가합니다. 솔루션 탐색기에서 **ProductsServer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다.

    ![][14]

10. **이름** 상자에서 이름을 **ProductsContract.cs**로 입력합니다. 그런 다음 **추가**를 클릭합니다.

11. **ProductsContract.cs**에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 서비스의 계약을 정의합니다.

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties.
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. Program.cs에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 프로필 서비스 및 그에 대한 호스트를 추가합니다.

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface.
            class ProductsService : IProducts
            {

                // Populate array of products for display on website.
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved.
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application.
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. 솔루션 탐색기에서 **App.config** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. **&lt;system.ServiceModel&gt;**의 내용을 다음 XML 코드로 바꿉니다. *yourServiceNamespace*를 서비스 네임스페이스의 이름으로 바꾸고 *yourKey*를 앞에서 Azure 클래식 포털에서 검색한 SAS 키로 바꿔야 합니다.

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. F6 키를 누르거나 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하여 응용 프로그램을 빌드한 후 지금까지 진행한 작업의 정확도를 확인합니다.

## ASP.NET MVC 응용 프로그램 만들기

이 섹션에서는 제품 서비스에서 검색한 데이터를 표시하는 간단한 ASP.NET 응용 프로그램을 빌드합니다.

### 프로젝트 만들기

1.  관리자 권한으로 Visual Studio 2013을 실행 중인지 확인합니다. 그렇지 않은 경우 관리자 권한으로 Visual Studio를 시작하려면 **Microsoft Visual Studio 2013(또는 Microsoft Visual Studio Express)**을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭합니다. 이 문서의 뒷부분에서 설명하는 Microsoft Azure 계산 에뮬레이터를 사용하려면 Visual Studio를 관리자 권한으로 실행해야 합니다.

2.  Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3.  **설치된 템플릿**에서 **Visual C#** 아래에 있는 **ASP.NET 웹 응용 프로그램**을 클릭합니다. 프로젝트의 이름을 **ProductsPortal**로 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![][15]

4.  **템플릿 선택** 목록에서 **MVC**를 클릭한 후 **확인**을 클릭합니다.

    ![][16]

5.  솔루션 탐색기에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다. **이름** 상자에서 이름을 **Product.cs**로 입력합니다. 그런 다음 **추가**를 클릭합니다.

    ![][17]

### 웹 응용 프로그램 수정

1.  Visual Studio의 Product.cs 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

        // Declare properties for the products inventory.
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  Visual Studio의 HomeController.cs 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory.
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  솔루션 탐색기에서 Views\\Shared 폴더를 확장합니다.

    ![][18]

4.  **\_Layout.cshtml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

5.  **내 ASP.NET 응용 프로그램**의 모든 항목을 **LITWARE 제품**으로 변경합니다.

6. **홈**, **정보** 및 **연락처** 링크를 제거합니다. 다음 예제에서 강조 표시된 코드를 삭제합니다.

	![][41]

7.  솔루션 탐색기에서 Views\\Home을 확장합니다.

    ![][20]

8.  **Index.cshtml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. 파일의 전체 내용을 다음 코드로 바꿉니다.

		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>

		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>
		}

		</table>

9.  지금까지 진행한 작업의 정확도를 확인하려면 **F6** 키를 누르거나 **Ctrl+Shift+B**를 눌러 프로젝트를 빌드하면 됩니다.


### 로컬에서 응용 프로그램 실행

응용 프로그램을 실행하여 작동하는지 확인합니다.

1.  **ProductsPortal**이 활성 프로젝트인지 확인합니다. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
2.  **Visual Studio**에서 F5 키를 누릅니다.
3.  응용 프로그램이 브라우저에 실행되는 것으로 나타나야 합니다.

    ![][21]

## 응용 프로그램의 Azure에 배포 준비 완료

Azure 클라우드 서비스 또는 Azure 웹 사이트에 응용 프로그램을 배포할 수 있습니다. 웹 사이트와 클라우드 서비스의 차이점에 대한 자세한 내용은 [Azure 실행 모델][executionmodels](영문)을 참조하세요. Azure 웹 사이트에 응용 프로그램을 배포하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포](https://azure.microsoft.com/develop/net/tutorials/get-started/)를 참조하세요. 이 섹션에서는 Azure 클라우드 서비스에 응용 프로그램을 배포하는 자세한 단계를 설명합니다.

클라우드 서비스에 응용 프로그램을 배포하기 위해 클라우드 서비스 프로젝트 배포 프로젝트를 솔루션에 추가합니다. 배포 프로젝트에는 클라우드에서 응용 프로그램을 올바르게 실행하는 데 필요한 구성 정보가 포함됩니다.

1.  응용 프로그램을 클라우드에 배포할 수 있게 만들려면 솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **변환**을 클릭한 다음 **Microsoft Azure 클라우드 서비스 프로젝트로 변환**을 클릭합니다.

    ![][22]

2.  응용 프로그램을 테스트하려면 F5 키를 누릅니다.

3.  Azure 계산 에뮬레이터가 시작됩니다. 계산 에뮬레이터는 Azure에서 실행되는 응용 프로그램을 로컬 컴퓨터를 사용하여 에뮬레이트합니다. 시스템 트레이를 보면 에뮬레이터가 시작되었는지 확인할 수 있습니다.

       ![][23]

4.  브라우저에 응용 프로그램이 로컬에서 실행 중인 것으로 여전히 표시되며, 응용 프로그램은 이전에 일반 ASP.NET MVC 4 응용 프로그램으로 실행할 때와 동일한 모양과 기능을 보입니다.

## 부분 연결

다음 단계는 온-프레미스 제품 서버를 ASP.NET MVC 응용 프로그램과 연결하는 것입니다.

1.  아직 열려 있지 않은 경우 "ASP.NET MVC 응용 프로그램 만들기" 섹션에서 만든 **ProductsPortal** 프로젝트를 Visual Studio에서 다시 엽니다.

2.  "온-프레미스 서버 만들기" 섹션의 단계와 비슷하게, 프로젝트 참조에 NuGet 패키지를 추가합니다. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

3.  "서비스 버스"를 검색하고 **Microsoft Azure 서비스 버스** 항목을 선택합니다. 그런 다음 설치를 완료하고 이 대화 상자를 닫습니다.

4.  솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **기존 항목**을 클릭합니다.

5.  **ProductsServer** 콘솔 프로젝트에서 **ProductsContract.cs** 파일로 이동합니다. ProductsContract.cs를 클릭하여 강조 표시합니다. **추가** 옆의 아래쪽 화살표를 클릭한 다음 **링크로 추가**를 클릭합니다.

	![][24]

6.  이제 Visual Studio 편집기에서 **HomeController.cs** 파일을 열고 네임스페이스 정의를 다음 코드로 바꿉니다. *yourServiceNamespace*를 서비스 네임스페이스의 이름으로 바꾸고 *yourKey*를 SAS 키로 바꿔야 합니다. 이렇게 하면 클라이언트에서 온-프레미스 서비스를 호출하여 호출 결과를 반환합니다.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory.
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication.
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory.
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  솔루션 탐색기에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.

8.  **ProductsServer** 프로젝트로 이동한 후 **ProductsServer.csproj** 솔루션 파일을 두 번 클릭하여 추가합니다.

9.  솔루션 탐색기에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

10. 왼쪽에서 **시작 프로젝트**를 클릭합니다. 오른쪽에서 **여러 시작 프로젝트**를 클릭합니다. **ProductsServer**, **ProductsPortal.Azure** 및 **ProductsPortal**이 이 순서대로 나타나고 **ProductsServer** 및 **ProductsPortal.Azure**의 동작으로 **시작**이 설정되어 있고 **ProductsPortal**의 동작으로 **없음**이 설정되어 있는지 확인합니다.

      ![][25]

11. **속성** 대화 상자에서 왼쪽에 있는 **ProjectDependencies**를 클릭합니다.

12. **프로젝트** 목록에서 **ProductsServer**를 클릭합니다. **ProductsPortal**의 선택이 해제되어 있고 **ProductsPortal.Azure**가 선택되었는지 확인합니다. 그런 다음 **확인**을 클릭합니다.

    ![][26]

## 응용 프로그램 실행

1.  Visual Studio의 **파일** 메뉴에서 **모두 저장**을 클릭합니다.

2.  F5를 눌러 응용 프로그램을 빌드 및 실행합니다. 온-프레미스 서버(**ProductsServer** 콘솔 응용 프로그램)가 먼저 시작된 후에 아래 스크린샷과 같이 **ProductsWeb** 응용 프로그램이 브라우저 창에서 시작되어야 합니다. 이제 제품 서비스 온-프레미스 시스템에서 검색된 제품 재고 목록 데이터가 표시됩니다.

    ![][1]

## Azure에 응용 프로그램 배포

1.  솔루션 탐색기에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Microsoft Azure에 게시**를 클릭합니다.

2.  모든 구독을 보려면 로그인해야 할 수도 있습니다.

    **Sign in to see more subscriptions**를 클릭하세요.

    ![][27]

3.  Microsoft 계정을 사용하여 로그인합니다.

8.  **다음**을 클릭합니다. 구독에 포함된 호스티드 서비스가 아직 없는 경우 호스티드 서비스를 만들라는 메시지가 나타납니다. 호스티드 서비스는 Azure 구독 내에서 응용 프로그램의 컨테이너 역할을 합니다. 응용 프로그램을 식별하는 이름을 입력하고 응용 프로그램을 최적화할 지역을 선택합니다. (이 지역에서 액세스하는 사용자가 경험하는 로드 시간이 빨라집니다.)

9.  응용 프로그램을 게시할 호스티드 서비스를 선택합니다. 나머지 설정은 아래 그림처럼 기본값을 유지합니다. **다음**을 클릭합니다.

    ![][33]

10. 마지막 페이지에서 **게시**를 클릭하여 배포 프로세스를 시작합니다.

    ![][34] 여기에는 5-7분 정도 걸립니다. 처음으로 게시하는 것이므로, Azure는 VM(가상 컴퓨터)을 프로비전하고, 보안을 강화하며, 응용 프로그램을 호스트하도록 VM에서 웹 역할을 만들고 이 웹 역할에 코드를 배포하며, 마지막으로 응용 프로그램을 공개할 수 있도록 부하 분산 장치 및 네트워킹을 구성합니다.

11. 게시가 진행되는 동안 이 작업을 **Azure Activity Log** 창에서 모니터링할 수 있습니다. 이 창은 일반적으로 Visual Studio 또는 Visual Web Developer의 아래쪽에 도킹되어 있습니다.

    ![][35]

12. 배포가 완료되면 모니터링 창에서 **웹 사이트 URL** 링크를 클릭하여 웹 사이트를 볼 수 있습니다.

    ![][36]

    웹 사이트는 온-프레미스 서버에 종속되므로, 웹 사이트가 제대로 작동하려면 **ProductsServer** 응용 프로그램을 로컬에서 실행해야 합니다. 클라우드 웹 사이트에서 요청을 수행할 때 다음 스크린샷에 표시된 "GetProducts called" 출력이 나타내는 것처럼 온-프레미스 콘솔 응용 프로그램에 요청이 들어오는 것을 확인하게 됩니다.

    ![][37]

웹 사이트와 클라우드 서비스의 차이점에 대한 자세한 내용은 [Azure 실행 모델][executionmodels](영문)을 참조하세요.

## 응용 프로그램 중지 및 삭제

다른 응용 프로그램을 빌드 및 배포할 수 있도록 이전에 배포한 응용 프로그램을 무료 750시간/월(31일/월) 동안의 서버 시간 내에 비활성화할 수 있습니다.

Azure는 사용된 서버 시간의 시간당 웹 역할 인스턴스 요금을 청구합니다. 서버 시간은 응용 프로그램이 배포된 다음에 사용되며 인스턴스가 실행되지 않고 중지된 상태인 경우에도 사용됩니다. 무료 계정에는 이 웹 역할 인스턴스를 호스트할 수 있도록 750시간/월(31일/월)의 전용 가상 컴퓨터 서버 시간이 포함되어 있습니다.

다음 단계에 따라 응용 프로그램을 중지 및 제거할 수 있습니다.

1.  [Azure 클래식 포털][]에 로그인하고, **클라우드 서비스**를 클릭한 다음 서비스의 이름을 클릭합니다.

2.  응용 프로그램을 일시 중단하려면 **대시보드** 탭을 클릭한 후 **중지**를 클릭합니다. **시작**을 클릭하여 다시 시작할 수 있습니다. Azure에서 응용 프로그램을 완전히 제거하여 복원할 수 있도록 하려면 **삭제**를 클릭합니다.

	![][43]

## 다음 단계  

서비스 버스에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure 서비스 버스][sbwacom]  
* [서비스 버스 큐를 사용하는 방법][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [도구 및 SDK 가져오기]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-42-webpi.png


  [Azure 클래식 포털]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [NuGet 서비스 버스 패키지 사용]: https://msdn.microsoft.com/library/azure/dn741354.aspx
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=AcomDC_0128_2016-->