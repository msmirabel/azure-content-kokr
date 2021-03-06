<properties 
	pageTitle="서비스 버스 분할 큐 및 항목을 위한 AMQP 1.0 지원 | Microsoft Azure" 
	description="서비스 버스분할 큐 및 항목과 함께 고급 메시지 큐 프로토콜(AMQP) 1.0 사용에 대해 알아보십시오." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="hillaryc"/>

# 서비스 버스 분할된 큐 및 토픽을 위한 AMQP 1.0 지원 

Azure 서비스 버스는 이제 서비스 버스 **분할된 큐 및 토픽**을 위한 **AMQP**(고급 메시지 큐 프로토콜) 1.0을 지원합니다.

**AMQP**는 개방형 표준 메시지 큐 프로토콜로, 서로 다른 프로그래밍 언어를 사용하여 크로스 플랫폼 응용 프로그램을 개발할 수 있습니다. 서비스 버스의 AMQP 지원에 대한 일반적인 내용은 [서비스 버스의 AMQP 1.0 지원](service-bus-amqp-overview.md)을 참조하세요.

*분할된 엔터티*로도 알려진 **분할된 큐 및 토픽**은 기존의 분할되지 않은 큐 및 토픽에 비해 더 높은 가용성, 안정성 및 처리량을 제공합니다. 분할된 엔터티에 대한 자세한 내용은 [분할된 메시징 엔터티](service-bus-partitioning.md)를 참조하세요.

분할된 큐 및 토픽과 통신하기 위한 프로토콜로 AMQP 1.0을 추가하면 서비스 버스 분할된 엔터티에서 제공된 더 높은 가용성, 안정성, 처리량의 이점을 이용할 수 있는 상호 운용 가능한 응용 프로그램을 구축할 수 있습니다.

## 분할된 큐가 있는 AMQP 사용

큐는 임시 분리, 부하 평준화, 부하 분산 및 느슨한 결합을 필요로 하는 시나리오에 유용합니다. 큐에서 게시자는 큐에 메시지를 보내고 소비자는 메시지를 한 번만 받을 수 있는 상황에서 큐로부터 메시지를 수신합니다. 이 작업의 좋은 예는 재고 시스템으로, POS(point-of-sale) 터미널이 데이터를 재고 관리 시스템에 직접 게시하지 않고 큐에 게시합니다. 재고 관리 시스템은 재고 보충을 관리하기 위해 언제든지 데이터를 사용하게 됩니다. 이는 항상 온라인 상태일 필요가 없는 재고 관리 시스템을 포함하여 몇 가지 이점이 있습니다. 서비스 버스 큐에 대한 자세한 내용은 [서비스 버스 큐를 사용하는 응용 프로그램 만들기](service-bus-create-queues.md)를 참조하세요.

이러한 큐는 여러 메시지 브로커 및 메시징 저장소에 걸쳐 분할되기 때문에 분할된 큐는 응용 프로그램에 대한 가용성, 안정성 및 처리량을 더욱 높입니다.

### 분할된 큐 만들기

[Azure 클래식 포털][] 및 서비스 버스 SDK를 사용하여 분할된 큐를 만들 수 있습니다. 분할된 큐를 만들려면 [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) 속성을 [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) 인스턴스에서 **true**로 설정합니다. 다음 코드에서는 서비스 버스 SDK를 사용하여 분할된 큐를 만드는 방법을 보여줍니다.
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### AMQP를 사용하여 메시지 보내기 및 받기

AMQP를 프로토콜로 사용하여 분할된 큐에서 메시지를 보내고 받는 작업은 아래 코드 조각에 나타난 대로 [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) 속성을 [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx)로 설정하여 수행됩니다.

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## 분할된 토픽이 있는 AMQP 사용

큐와 마찬가지로, 토픽은 임시 분리, 부하 평준화, 부하 분산 및 느슨한 결합을 필요로 하는 시나리오에 유용합니다. 큐와는 달리, 토픽은 동일한 메시지의 복사본이 여러 구독자에게 라우팅할 수 있습니다. 항목에서, 게시자는 항목에 메시지를 보내고 소비자는 구독으로부터 메시지를 수신합니다. 재고 시스템의 예에서 시스템 POS(point-of-sale) 터미널은 데이터를 토픽에 게시합니다. 그러면 재고 관리 시스템은 구독으로부터 메시지를 받습니다. 또한 모니터링 시스템은 다른 구독으로부터 동일한 메시지를 받을 수 있습니다. 서비스 버스 토픽에 대한 자세한 내용은 [서비스 버스 토픽 및 구독을 사용하는 응용 프로그램 만들기](service-bus-create-topics-subscriptions.md)를 참조하세요.

이러한 토픽과 해당 구독이 여러 메시지 브로커 및 메시징 저장소에 걸쳐 분할되기 때문에 분할된 토픽은 응용 프로그램에 대한 가용성, 안정성 및 처리량을 더욱 높입니다.

### 분할된 토픽 만들기

[Azure 클래식 포털][] 및 서비스 버스 SDK를 통해 분할된 토픽을 만들 수 있습니다. 분할된 토픽을 만들려면 [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) 속성을 [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) 인스턴스에서 **true**로 설정합니다. 다음 코드에서는 서비스 버스 SDK를 사용하여 분할된 토픽을 만드는 방법을 보여 줍니다.
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### AMQP를 사용하여 메시지 보내기 및 받기

AMQP를 프로토콜로 사용하여 분할된 토픽 구독에서 메시지를 보내고 받는 작업은 아래 코드 조각에 나타난 대로 [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) 속성을 [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx)로 설정하여 수행됩니다.

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## 다음 단계

메시징 엔터티 분할 방법에 대한 자세한 내용은 다음 추가 정보를 참조하세요.

*    [분할된 메시징 엔터티](service-bus-partitioning.md)
*    [OASIS AMQP(Advanced Message Queuing Protocol) 버전 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [서비스 버스의 AMQP 1.0 지원](service-bus-amqp-overview.md)
*    [서비스 버스 및 AMQP 1.0과 함께 JMS(Java Message Service) API를 사용하는 방법](service-bus-java-how-to-use-jms-api-amqp.md)
*    [서비스 버스 .NET API와 함께 AMQP 1.0을 사용하는 방법](service-bus-dotnet-advanced-message-queuing.md)

[Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->