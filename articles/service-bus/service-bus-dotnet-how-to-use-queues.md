<properties
    pageTitle="서비스 버스 큐를 사용하는 방법(.NET) | Microsoft Azure"
    description="Azure에서 서비스 버스 큐를 사용하는 방법에 대해 알아봅니다. 코드 샘플은 .NET API를 사용하여 C#으로 작성되었습니다."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="01/26/2016"
    ms.author="sethm"/>

# 서비스 버스 큐를 사용하는 방법

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 문서에서는 서비스 버스 큐를 사용하는 방법을 설명합니다. 샘플은 C#으로 작성되었으며 .NET API를 사용합니다. 여기서 다루는 시나리오에는 큐 만들기와 메시지 보내기 및 받기가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## 서비스 버스 NuGet 패키지 추가

[서비스 버스 **NuGet** 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다. NuGet Visual Studio 확장을 사용하면 Visual Studio 및 Visual Studio Express에서 라이브러리와 도구를 쉽게 설치 및 업데이트할 수 있습니다. 서비스 버스 NuGet 패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다.

응용 프로그램에서 NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
2.  "서비스 버스"를 검색하고 **Microsoft Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

    ![][7]

이제 서비스 버스에 대한 코드를 작성할 준비가 되었습니다.

## 서비스 버스 연결 문자열 설정

서비스 버스는 연결 문자열을 사용하여 끝점과 자격 증명을 저장합니다. 하드 코딩하는 대신 구성 파일에 연결 문자열을 저장할 수 있습니다.

- Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(.csdef 및 .cscfg 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
- Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: Web.config 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 문서의 뒷부분에 표시된 대로 [CloudConfigurationManager.GetSetting][GetSetting] 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 [Azure 클래식 포털][]에서 구성 설정을 동적으로 변경할 수 있게 해줍니다. 예를 들어 다음 예에 표시된 대로 서비스 정의(.csdef) 파일에 `Setting` 레이블을 추가합니다.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
그런 다음, 다음 예제와 같이 서비스 구성(.cscfg) 파일에서 값을 지정합니다.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

이전 섹션에서 설명한 대로 Azure 클래식 포털에서 검색된 SAS(공유 액세스 서명) 키 이름 및 키 값을 사용합니다.

### 웹 사이트 또는 Azure 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: **Web.config**)을 사용하는 것이 좋습니다. 다음과 같이 `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

이전 섹션에서 설명한 대로 Azure 클래식 포털에서 검색된 SAS 이름 및 키 값을 사용합니다.

## 큐 만들기

[NamespaceManager][] 클래스를 사용하여 서비스 버스 큐에 대한 관리 작업을 수행할 수 있습니다. 이 클래스는 큐를 만들고 열거하고 삭제하는 메서드를 제공합니다.

이 예제에서는 Azure [CloudConfigurationManager][] 클래스를 사용하여 [NamespaceManager][] 개체를 생성합니다. 연결 문자열은 서비스 버스 서비스 네임스페이스의 기준 주소와 관리 권한이 있는 적절한 SAS 자격 증명으로 구성됩니다. 이 연결 문자열은 다음 예제와 같은 형태입니다.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

이전 섹션의 구성 설정이 제공될 경우 다음 예를 사용합니다.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

큐의 속성을 조정할 수 있게 해 주는 [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) 메서드 오버로드가 있습니다. 예를 들어 큐에 전송되는 메시지에 적용할 기본 TTL(time-to-live) 값을 설정할 수 있습니다. 이러한 설정은 [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) 클래스를 사용하여 적용됩니다. 다음 예제에서는 최대 크기가 5GB이고 기본 메시지 TTL이 1분인 `TestQueue`(이)라는 큐를 만드는 방법을 보여 줍니다.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] [NamespaceManager][] 개체의 [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) 메서드를 사용하여 서비스 네임스페이스 내에 지정된 이름의 큐가 이미 있는지 확인할 수 있습니다.

## 큐에 메시지 보내기

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 연결 문자열을 사용하여 [QueueClient][] 개체를 만듭니다.

아래 코드는 [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API 호출을 사용하여 위에서 만든 `TestQueue` 큐에 대한 [QueueClient][] 개체를 만드는 방법을 보여 줍니다.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

서비스 버스 큐로 보내고 받은 메시지는 [BrokeredMessage][] 클래스 인스턴스입니다. [BrokeredMessage][] 개체에는 표준 속성 집합(예: [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 및 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 [BrokeredMessage][] 개체 생성자에 직렬화된 개체를 전달하여 메시지 본문을 설정할 수 있으며, 적절한 **DataContractSerializer**를 사용하여 개체를 직렬화합니다. 또는 **System.IO.Stream** 개체를 제공할 수 있습니다.

다음 예제에서는 이전 코드 예제에서 얻은 `TestQueue` [QueueClient][] 개체에 테스트 메시지 5개를 보내는 방법을 보여 줍니다.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

서비스 버스 큐는 [메시지 크기를 최대 256KB](service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas)까지 지원합니다.(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 크기는 최대 64KB임) 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 분할을 사용하는 경우 상한이 더 높습니다. 자세한 내용은 [메시징 엔터티 분할](service-bus-partitioning.md)을 참조하세요.

## 큐에서 메시지를 받는 방법

[QueueClient][] 개체를 사용하여 큐에서 메시지를 받는 것이 좋습니다. [QueueClient][] 개체는 [ReceiveAndDelete 및 PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 큐의 메시지에 대한 읽기 요청을 받으면 메시지를 사용 중인 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete**는 가장 단순한 모델이며, 오류 이벤트 시 응용 프로그램이 메시지를 처리하지 않아도 되는 안전한 시나리오에 가장 적합합니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스가 메시지를 이용되는 것으로 표시했기 때문에 응용 프로그램이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

**PeekLock** 모드(기본 모드)에서는 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 [Complete][]를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스가 [Complete][] 호출을 확인한 경우 메시지를 사용한 것으로 표시하고 큐에서 제거합니다.

다음 예제에서는 기본 **PeekLock** 모드를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 다른 [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 값을 지정하기 위해 [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx)에 다른 오버로드를 사용할 수 있습니다. 다음 예제는 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 콜백을 사용하여 `TestQueue`에 도착한 메시지를 처리합니다

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

다음 예제에서는 [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 개체를 사용하여 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 콜백을 구성합니다. [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx)는 받은 메시지에서 [Complete][]를 호출할 시점을 수동으로 제어할 수 있도록 **false**로 설정되어 있습니다. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx)은 호출 시간이 초과되기 전에 클라이언트가 최대 1분간 메시지를 대기하고 메시지를 확인하기 위해 새로 호출할 수 있도록 1분으로 설정됩니다. 이 속성 값은 클라이언트가 메시지를 검색하지 않는 유료 호출하는 횟수를 줄입니다.

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 메서드 대신 [Abandon][] 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 [완료][] 요청이 실행되기 전에 충돌하는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지하는 메시지의 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 속성을 사용합니다.

## 다음 단계

이제 서비스 버스 큐의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보세요.

-   [큐, 토믹 및 구독][]의 서비스 버스 메시징 엔터티에 대해 자세히 알아봅니다.
-   [서비스 버스 조정된 메시징 .NET 자습서][]를 사용하여 서비스 버스 큐로 메시지를 보내고 받는 작동하는 응용 프로그램을 만듭니다.
-   [Azure 샘플][]에서 서비스 버스 샘플을 다운로드하거나 [서비스 버스 샘플 개요][]를 참조하세요.

  [Azure 클래식 포털]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [큐, 토믹 및 구독]: service-bus-queues-topics-subscriptions.md
  [서비스 버스 조정된 메시징 .NET 자습서]: service-bus-brokered-tutorial-dotnet.md
  [Azure 샘플]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [서비스 버스 샘플 개요]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx
  [Abandon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx
  [완료]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx

<!---HONumber=AcomDC_0128_2016-->