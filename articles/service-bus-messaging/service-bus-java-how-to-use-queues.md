---
title: Azure Service Bus wacht rijen gebruiken met Java
description: In deze zelf studie leert u hoe u Java-toepassingen kunt maken om berichten te verzenden naar en berichten van een Azure Service Bus wachtrij te ontvangen.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 224a5ce0a2c8a7fc031f1ad3314e4d8889966433
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788294"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Snelstartgids: Azure Service Bus-wacht rijen gebruiken met Java om berichten te verzenden en te ontvangen

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In deze zelf studie leert u hoe u Java-toepassingen kunt maken om berichten te verzenden naar en berichten van een Azure Service Bus wachtrij te ontvangen. 

> [!NOTE]
> U kunt Java-voor beelden vinden op GitHub in de [Azure-service-bus-opslag plaats](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Als u geen wachtrij hebt om mee te werken, voert u de stappen in het [Azure Portal gebruik uit om een service bus wachtrij](service-bus-quickstart-portal.md) artikel te maken om een wachtrij te maken.
    1. Lees het kort **overzicht** van service bus- **wacht rijen**. 
    2. Maak een Service Bus **naam ruimte**. 
    3. Haal de **Connection String**op.
    4. Maak een Service Bus **wachtrij**.
3. Installeer de [Azure-SDK voor Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Zorg ervoor dat u de [Azure SDK voor Java][Azure SDK for Java] hebt geïnstalleerd voordat u dit voor beeld bouwt. 

Als u een eclips gebruikt, kunt u de [Azure-Toolkit voor eclipse][Azure Toolkit for Eclipse] installeren die de Azure SDK voor Java bevat. U kunt vervolgens de **Microsoft Azure bibliotheken voor Java** toevoegen aan uw project. Als u IntelliJ gebruikt, raadpleegt u [de Azure-Toolkit voor IntelliJ installeren](/azure/developer/java/toolkit-for-intellij/installation). 

![Microsoft Azure bibliotheken voor Java toevoegen aan uw eclips-project](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Voeg de volgende `import` -instructies toe aan de bovenkant van het Java-bestand:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u berichten naar een Service Bus wachtrij wilt verzenden, maakt uw toepassing een exemplaar van een **QueueClient** -object en worden berichten asynchroon verzonden. De volgende code laat zien hoe u een bericht verzendt voor een wachtrij die via de portal is gemaakt.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
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
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Berichten die worden verzonden naar en ontvangen van Service Bus-wacht rijen, zijn exemplaren van de [bericht](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) klasse. Bericht objecten hebben een aantal standaard eigenschappen (zoals label en TimeToLive), een woorden lijst die wordt gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van wille keurige toepassings gegevens. Een toepassing kan de hoofd tekst van het bericht instellen door elk serialiseerbaar object door te geven aan de constructor van het bericht en de juiste serialisatiefunctie wordt gebruikt om het object te serialiseren. U kunt ook een Java-weboplossing opgeven **. Latentie. InputStream** -object.


Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
De primaire manier om berichten uit een wachtrij te ontvangen, is door een **ServiceBusContract** -object te gebruiken. Ontvangen berichten kunnen in twee verschillende modi werken: **ReceiveAndDelete** en **PeekLock**.

Wanneer u de **ReceiveAndDelete** -modus gebruikt, ontvangt u een eenmalige bewerking. Wanneer service bus een lees aanvraag voor een bericht in een wachtrij ontvangt, wordt het bericht gemarkeerd als verbruikt en wordt het naar de toepassing geretourneerd. De **ReceiveAndDelete** -modus (de standaard modus) is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht in het geval van een fout kan verdragen. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt.
Omdat Service Bus het bericht heeft gemarkeerd als verbruikt, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

In de **PeekLock** -modus wordt ontvangen een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door het aanroepen van **complete ()** op het ontvangen bericht. Als Service Bus de aanroep **complete ()** ziet, wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd. 

In het volgende voor beeld ziet u hoe berichten kunnen worden ontvangen en verwerkt met behulp van de **PeekLock** -modus (niet de standaard modus). In het onderstaande voor beeld wordt het call back-model gebruikt met een geregistreerde bericht-handler en worden `TestQueue`berichten verwerkt die binnenkomen in onze. In deze modus wordt de **volledige ()** automatisch aangeroepen, omdat de retour aanroep normaal wordt geretourneerd en het aanroepen van **Abandon ()** als de retour aanroep een uitzonde ring genereert. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
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
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode **Abandon ()** aanroepen op het client object met het vergrendelings token van het ontvangen bericht dat is verkregen via **getLockToken ()**. Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt Service Bus het bericht automatisch ontgrendeld zodat het weer beschikbaar wordt.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de aanvraag **volledig ()** is uitgegeven, wordt het bericht opnieuw aan de toepassing verzonden wanneer het opnieuw wordt gestart. Dit wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de methode **getMessageId** van het bericht, dat constant blijft tijdens de bezorgings pogingen.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Service Bus wacht rijen hebt geleerd, raadpleegt u [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

Raadpleeg het [Java Developer Center](https://azure.microsoft.com/develop/java/) voor meer informatie.

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
