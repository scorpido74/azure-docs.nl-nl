---
title: Onderwerpen en abonnementen van Azure Service Bus gebruiken met Java
description: In deze quickstart schrijft u Java-code om berichten naar een Azure Service Bus-onderwerp te verzenden en vervolgens berichten van abonnementen op dat onderwerp te ontvangen.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a08a071466f4f10c1364cefdda7c9c136e1e1ef5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79137985"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-java"></a>Snelstart: Service Bus-onderwerpen en abonnementen gebruiken met Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In deze quickstart schrijft u Java-code om berichten naar een Azure Service Bus-onderwerp te verzenden en vervolgens berichten van abonnementen op dat onderwerp te ontvangen. 

## <a name="prerequisites"></a>Vereisten

1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Volg stappen in de [Quickstart: gebruik de Azure-portal om een ServiceBus-onderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om de volgende taken uit te voeren:
    1. Een **naamruimte**voor servicebus maken .
    2. Haal de **verbindingstekenreeks**.
    3. Maak een **onderwerp** in de naamruimte.
    4. Maak **drie abonnementen op** het onderwerp in de naamruimte.
3. [Azure SDK voor Java][Azure SDK for Java].

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Zorg ervoor dat u de [Azure SDK voor Java][Azure SDK for Java] hebt geïnstalleerd voordat u dit voorbeeld maakt. Als u Eclipse gebruikt, u de [Azure Toolkit voor Eclipse][Azure Toolkit for Eclipse] installeren die de Azure SDK voor Java bevat. Vervolgens u de **Microsoft Azure-bibliotheken voor Java** aan uw project toevoegen:

![Microsoft Azure-bibliotheken voor Java toevoegen aan uw Eclipse-project](media/service-bus-java-how-to-use-topics-subscriptions/eclipse-azure-libraries-java.png)

Je moet ook de volgende JARs toevoegen aan het Java Build Path:

- gson-2.6.2.jar
- commons-cli-1.4.jar
- proton-j-0.21.0.jar

Voeg een klasse toe met een **hoofdmethode** en voeg de volgende `import` instructies toe boven aan het Java-bestand:

```java
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;
import static java.nio.charset.StandardCharsets.*;
import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Function;
import org.apache.commons.cli.*;
import org.apache.commons.cli.DefaultParser;
```

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Werk de **hoofdmethode** bij om een **Object TopicClient** te maken en roep een helpermethode op die asynchroon voorbeeldberichten naar het onderwerp ServiceBus verzendt.

> [!NOTE] 
> - Vervang `<NameOfServiceBusNamespace>` door de naam van de Service Bus-naamruimte. 
> - Vervang `<AccessKey>` door de toegangssleutel voor uw naamruimte.

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        // TODO Auto-generated method stub

        TopicClient sendClient;
        String connectionString = "Endpoint=sb://<NameOfServiceBusNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<AccessKey>";
        sendClient = new TopicClient(new ConnectionStringBuilder(connectionString, "BasicTopic"));       
        sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
    }

    static CompletableFuture<Void> sendMessagesAsync(TopicClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {
                        }.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("Message sending: Id = %s\n", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\tMessage acknowledged: Id = %s\n", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet op het aantal berichten in een onderwerp, maar er is een limiet op de totale grootte van de berichten die in het bezit zijn van een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
Werk de **hoofdmethode** bij om drie **SubscriptionClient-objecten** voor drie abonnementen te maken en een helpermethode aan te roepen die asynchroon berichten ontvangt vanuit het onderwerp ServiceBus. De voorbeeldcode gaat ervan uit dat u een onderwerp met de naam **BasicTopic** hebt gemaakt en drie abonnementen met de naam **Subscription1**, **Subscription2**en **Subscription3**. Als u er verschillende namen voor hebt gebruikt, werkt u de code bij voordat u deze test. 

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        SubscriptionClient subscription1Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription1"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription2Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription2"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription3Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription3"), ReceiveMode.PEEKLOCK);        

        registerMessageHandlerOnClient(subscription1Client);
        registerMessageHandlerOnClient(subscription2Client);
        registerMessageHandlerOnClient(subscription3Client);
    }
    
    static void registerMessageHandlerOnClient(SubscriptionClient receiveClient) throws Exception {

        // register the RegisterMessageHandler callback
        IMessageHandler messageHandler = new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                // receives message is passed to callback
                if (message.getLabel() != null &&
                        message.getContentType() != null &&
                        message.getLabel().contentEquals("Scientist") &&
                        message.getContentType().contentEquals("application/json")) {

                    byte[] body = message.getBody();
                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                    System.out.printf(
                            "\n\t\t\t\t%s Message received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            receiveClient.getEntityPath(),
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                }
                return receiveClient.completeAsync(message.getLockToken());
            }
            
            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
            }
        };

 
        receiveClient.registerMessageHandler(
                    messageHandler,
                    // callback invoked when the message handler has an exception to report
                // 1 concurrent call, messages aren't auto-completed, auto-renew duration
                new MessageHandlerOptions(1, false, Duration.ofMinutes(1)));

    }
}
```

## <a name="run-the-program"></a>Het programma uitvoeren
Voer het programma uit om de uitvoer te zien die vergelijkbaar is met de volgende uitvoer:

```java
Message sending: Id = 0
Message sending: Id = 1
Message sending: Id = 2
Message sending: Id = 3
Message sending: Id = 4
Message sending: Id = 5
Message sending: Id = 6
Message sending: Id = 7
Message sending: Id = 8
Message sending: Id = 9
    Message acknowledged: Id = 0
    Message acknowledged: Id = 9
    Message acknowledged: Id = 7
    Message acknowledged: Id = 8
    Message acknowledged: Id = 5
    Message acknowledged: Id = 6
    Message acknowledged: Id = 3
    Message acknowledged: Id = 2
    Message acknowledged: Id = 4
    Message acknowledged: Id = 1

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]
```

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie [Wachtrijen voor servicebus, onderwerpen en abonnementen][Service Bus queues, topics, and subscriptions]voor meer informatie.

[Azure SDK for Java]: https://docs.microsoft.com/java/api/overview/azure/
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
