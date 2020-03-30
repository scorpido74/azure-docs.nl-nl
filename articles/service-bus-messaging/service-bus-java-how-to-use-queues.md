---
title: Wachtrijen voor Azure Service Bus gebruiken met Java
description: In deze zelfstudie leert u hoe u Java-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een Azure Service Bus-wachtrij.
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
ms.openlocfilehash: ac6bc8f78bd3d526e68dba3e81825a28a9ac47f7
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80294130"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Snelstart: Azure Service Bus-wachtrijen met Java gebruiken om berichten te verzenden en te ontvangen

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
In deze zelfstudie leert u hoe u Java-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een Azure Service Bus-wachtrij. 

> [!NOTE]
> U Java-samples vinden op GitHub in de [azure-service-bus repository.](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Als u geen wachtrij hebt om mee te werken, voert u stappen uit in de [Azure-portal gebruiken om een wachtrijartikel voor servicebus te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het snelle **overzicht** van wachtrijen voor **servicebussen.** 
    2. Een **naamruimte**voor servicebus maken . 
    3. Haal de **verbindingstekenreeks**.
    4. Een wachtrij voor servicebussen **maken**.
3. [Azure SDK voor Java][Azure SDK for Java]installeren . 


## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Zorg ervoor dat u de [Azure SDK voor Java][Azure SDK for Java] hebt geïnstalleerd voordat u dit voorbeeld maakt. 

Als u Eclipse gebruikt, u de [Azure Toolkit voor Eclipse][Azure Toolkit for Eclipse] installeren die de Azure SDK voor Java bevat. Vervolgens u de **Microsoft Azure-bibliotheken voor Java** aan uw project toevoegen. Zie De Azure Toolkit voor [IntelliJ installeren](/azure/java/intellij/azure-toolkit-for-intellij-installation)als u IntelliJ gebruikt. 

![Microsoft Azure-bibliotheken voor Java toevoegen aan uw Eclipse-project](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Voeg de `import` volgende instructies toe aan de bovenkant van het Java-bestand:

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
Als u berichten naar een wachtrij voor servicebussen wilt verzenden, wordt een **QueueClient-object** geinstantieerd en worden berichten asynchroon verzonden. In de volgende code ziet u hoe u een bericht verzendt voor een wachtrij die via de portal is gemaakt.

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

Berichten die naar wachtrijen voor servicebus worden verzonden en ontvangen, zijn exemplaren van de klasse [Message.](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) Berichtobjecten hebben een set standaardeigenschappen (zoals Label en TimeToLive), een woordenboek dat wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen vast te houden en een hoofd tekst met willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door elk serializable object door te geven aan de constructor van het bericht, en de juiste serialisator wordt vervolgens gebruikt om het object te serialiseren. U ook een **java bieden. Io. Object InputStream.**


Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij
De primaire manier om berichten uit een wachtrij te ontvangen, is door een **ServiceBusContract-object** te gebruiken. Ontvangen berichten kunnen werken in twee verschillende modi: **ReceiveAndDelete** en **PeekLock**.

Wanneer u de modus **Ontvangen en verwijderen** gebruikt, is ontvangen een bewerking met één schot - dat wil zeggen wanneer Service Bus een leesverzoek ontvangt voor een bericht in een wachtrij, markeert het bericht als verbruikt en retourneert het naar de toepassing. **ReceiveAndDelete-modus** (de standaardmodus) is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt in het geval van een storing. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt.
Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, het bericht gemist dat voorafgaand aan de crash is verbruikt.

In **de PeekLock-modus** wordt ontvangen een tweetrapsbewerking, waardoor toepassingen kunnen worden ondersteund die ontbrekende berichten niet kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing is voltooid met de verwerking van het bericht (of het betrouwbaar opslaat voor toekomstige verwerking), voltooit het de tweede fase van het ontvangstproces door **volledige()** op het ontvangen bericht te bellen. Wanneer Service Bus de **volledige()** aanroep ziet, wordt het bericht als verbruikt weergegeven en wordt het uit de wachtrij verwijderd. 

In het volgende voorbeeld wordt uitgelegd hoe berichten kunnen worden ontvangen en verwerkt met de **PeekLock-modus** (niet de standaardmodus). Het onderstaande voorbeeld maakt gebruik van het callback-model met `TestQueue`een geregistreerde berichthandler en verwerkt berichten wanneer ze in onze . Deze modus roept **automatisch voltooid()** aan als de callback normaal terugkeert en oproepen **verlaten()** als de callback een uitzondering plaatst. 

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
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing om de een of andere reden het bericht niet kan verwerken, kan deze de methode **verlaten()** op clientobject aanroepen met het vergrendelingstoken van het ontvangen bericht dat is verkregen via **getLockToken()**. Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in de wachtrij, en als de toepassing het bericht niet verwerkt voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te ontvangen.

In het geval dat de toepassing crasht na het verwerken van het bericht, maar voordat de **volledige()** aanvraag wordt uitgegeven, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met behulp van de **getMessageId-methode** van het bericht, die constant blijft tijdens leveringspogingen.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van wachtrijen voor servicebus hebt geleerd, raadpleegt u [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

Raadpleeg het [Java Developer Center](https://azure.microsoft.com/develop/java/) voor meer informatie.

[Azure SDK for Java]: /azure/java/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
