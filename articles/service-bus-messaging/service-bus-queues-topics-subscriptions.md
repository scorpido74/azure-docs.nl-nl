---
title: Azure Service Bus berichten-wacht rijen, onderwerpen en abonnementen
description: Dit artikel bevat een overzicht van Azure Service Bus Messa ging-entiteiten (wacht rijen, onderwerpen en abonnementen).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: deeebf56d6e2f4ccfac37c70170a0d1cb4d272a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119170"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-wachtrijen, -onderwerpen en -abonnementen

Microsoft Azure Service Bus biedt ondersteuning voor een set Cloud, op berichten gebaseerde middleware-technologieën, waaronder betrouw bare Message Queuing en duurzame berichten over publiceren/abonneren. Deze ' brokered ' Messa ging-mogelijkheden kunnen worden beschouwd als losgekoppelde berichten functies die ondersteuning bieden voor publiceren-abonneren, tijdelijke loskoppelingen en taakverdelings scenario's met behulp van de werk belasting van de Service Bus berichten. Ontkoppelde communicatie heeft veel voordelen; clients en servers kunnen bijvoorbeeld naar behoefte verbinding maken en hun bewerkingen asynchroon uitvoeren.

De Messa ging-entiteiten die de kern vormen van de mogelijkheden voor berichten in Service Bus zijn wacht rijen, onderwerpen en abonnementen, en regels/acties.

## <a name="queues"></a>Wachtrijen

Wacht rijen bieden de aflevering van het FIFO-bericht ( *First in, first out* ) aan een of meer concurrerende consumenten. Dat wil zeggen dat ontvangers berichten doorgaans ontvangen en verwerken in de volg orde waarin ze aan de wachtrij zijn toegevoegd en dat er slechts één bericht verbruiker elk bericht ontvangt en verwerkt. Een belang rijk voor deel van het gebruik van wacht rijen is het behalen van tijdelijke ontkoppeling van toepassings onderdelen. Met andere woorden, de producenten (afzenders) en consumenten (ontvangers) hoeven niet tegelijkertijd berichten te verzenden en te ontvangen, omdat berichten blijvend in de wachtrij worden opgeslagen. Bovendien hoeft de producent niet te wachten op een antwoord van de consument om berichten te kunnen verwerken en te verzenden.

Een verwant voor deel is ' load Leveling ', waarmee producenten en consumenten berichten kunnen verzenden en ontvangen tegen verschillende tarieven. In veel toepassingen varieert de systeem belasting in de loop van de tijd. de verwerkings tijd die voor elke werk eenheid is vereist, is meestal echter constant. Door de verwerking van bericht producenten en consumenten met een wachtrij houdt dit in dat de verbruikte toepassing alleen moet worden ingericht om de gemiddelde belasting te kunnen verwerken in plaats van piek belasting. De lengte van de wachtrij neemt toe of af, al naargelang het binnenkomende verkeer. Deze functie bespaart direct geld met betrekking tot de hoeveelheid infra structuur die nodig is om de belasting van de toepassing te onderhouden. Naarmate de belasting toeneemt, kunnen er meer werk processen worden toegevoegd om uit de wachtrij te lezen. Elk bericht worden door slechts één van de werkprocessen verwerkt. Daarnaast kunt u met deze pull-taak verdeling optimaal gebruik maken van de werk computers, zelfs als de worker-computers verschillen met betrekking tot de verwerkings kracht, omdat deze berichten op basis van hun eigen maximum snelheid ophalen. Dit patroon wordt vaak het patroon ' concurrerende consument ' genoemd.

Door gebruik te maken van wacht rijen tussen bericht producenten en consumenten, biedt een inherente, soepele koppeling tussen de onderdelen. Omdat producenten en consumenten niet op de hoogte zijn van elkaar, kan een consument worden bijgewerkt zonder dat dit van invloed is op de producent.

### <a name="create-queues"></a>Wacht rijen maken

U kunt wacht rijen maken met behulp van de sjablonen [Azure Portal](service-bus-quickstart-portal.md), [Power shell](service-bus-quickstart-powershell.md), [cli](service-bus-quickstart-cli.md)en [Resource Manager](service-bus-resource-manager-namespace-queue.md). U kunt vervolgens berichten verzenden en ontvangen met behulp van een [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) -object.

Als u snel wilt weten hoe u een wachtrij maakt, vervolgens berichten van en naar de wachtrij verzendt en ontvangt, raadpleegt u de [Quick](service-bus-quickstart-portal.md) starts voor elke methode. Zie [aan de slag met Service Bus wachtrijen](service-bus-dotnet-get-started-with-queues.md)voor een uitgebreidere zelf studie over het gebruik van wacht rijen.

Zie het BasicSendReceiveUsingQueueClient-voor [beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) voor github voor een werkend voor beeld.

### <a name="receive-modes"></a>Ontvangst modi

U kunt twee verschillende modi opgeven waarin Service Bus berichten ontvangt: *ReceiveAndDelete* of *PeekLock*. In de [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -modus is de receive-bewerking één keer; dat wil zeggen, wanneer Service Bus de aanvraag van de consument ontvangt, wordt het bericht gemarkeerd als verbruikt en wordt het naar de consumenten toepassing geretourneerd. De **ReceiveAndDelete** -modus is het eenvoudigste model en werkt het beste voor scenario's waarin de toepassing een bericht niet kan verwerken als er een fout optreedt. Als u meer inzicht wilt hebben in dit scenario, kunt u het beste een scenario waarin de consumer de ontvangst aanvraag uitgeeft, afhandelen voordat deze wordt verwerkt. Omdat Service Bus het bericht markeert als verbruikt wanneer de toepassing opnieuw wordt gestart en opnieuw bezig is met het uitvoeren van berichten, heeft het bericht dat voor het vastlopen is verbruikt, gemist.

In de modus [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) wordt de receive-bewerking twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Wanneer Service Bus de aanvraag ontvangt, wordt het volgende bericht weer gegeven dat moet worden gebruikt, wordt dit vergrendeld om te voor komen dat andere gebruikers het ontvangen, en wordt het vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) aan te roepen op het ontvangen bericht. Als Service Bus de **CompleteAsync** -aanroep ziet, wordt het bericht gemarkeerd als verbruikt.

Als de toepassing het bericht om de een of andere reden niet kan verwerken, kan dit de methode [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) aanroepen op het ontvangen bericht (in plaats van [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Met deze methode kan Service Bus het bericht ontgrendelen en het beschikbaar maken zodat het opnieuw kan worden ontvangen, hetzij door dezelfde consument hetzij door een andere concurrerende consument. Ten tweede is er een time-out gekoppeld aan de vergren deling en als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), Service Bus het bericht ontgrendelen en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen (in feite een [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) -bewerking uit te voeren).

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de **CompleteAsync** -aanvraag is verzonden, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit proces wordt vaak *minstens eenmaal* verwerkt. dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt. In bepaalde situaties kan hetzelfde bericht echter opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan verdragen, is er aanvullende logica vereist in de toepassing om dubbele waarden te detecteren. Dit kan worden bereikt op basis van de [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) -eigenschap van het bericht, die constant blijft tijdens de bezorgings pogingen. Deze functie wordt *precies eenmaal* verwerkt.

## <a name="topics-and-subscriptions"></a>Onderwerpen en abonnementen

In tegens telling tot wacht rijen, waarbij elk bericht wordt verwerkt door één Consumer, bieden *onderwerpen* en *abonnementen* een een-op-veel communicatie vorm, in een patroon voor *publiceren/abonneren* . Het is handig om te schalen naar een groot aantal ontvangers, waarna elk gepubliceerd bericht beschikbaar wordt gesteld aan elk abonnement dat is geregistreerd bij het onderwerp. Berichten worden verzonden naar een onderwerp en geleverd aan een of meer gekoppelde abonnementen, afhankelijk van filter regels die per abonnement kunnen worden ingesteld. De abonnementen kunnen extra filters gebruiken om de berichten te beperken die ze willen ontvangen. Berichten worden op dezelfde manier naar een onderwerp verzonden als naar een wachtrij, maar berichten worden niet rechtstreeks in het onderwerp ontvangen. In plaats daarvan worden ze ontvangen van abonnementen. Een onderwerps abonnement lijkt op een virtuele wachtrij die kopieën ontvangt van de berichten die naar het onderwerp worden verzonden. Berichten worden op dezelfde manier ontvangen van een abonnement als bij een wachtrij.

De functionaliteit voor het verzenden van berichten van een wachtrij wijst rechtstreeks naar een onderwerp en de functionaliteit voor het ontvangen van berichten die wordt toegewezen aan een abonnement. Deze functie betekent onder andere dat abonnementen ondersteuning bieden voor dezelfde patronen die eerder in deze sectie zijn beschreven, met betrekking tot wacht rijen: concurrerende consument, tijdelijke ontkoppeling, taak verdeling en taak verdeling.

### <a name="create-topics-and-subscriptions"></a>Onderwerpen en abonnementen maken

Het maken van een onderwerp is vergelijkbaar met het maken van een wachtrij, zoals beschreven in de vorige sectie. Vervolgens verzendt u een bericht met de [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) -klasse. Als u berichten wilt ontvangen, maakt u een of meer abonnementen op het onderwerp. Net als wacht rijen worden berichten ontvangen van een abonnement met behulp van een [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) -object in plaats van een [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) -object. Maak de abonnements-client, waarbij de naam van het onderwerp, de naam van het abonnement en (optioneel) de ontvangst modus als para meters worden door gegeven.

Zie voor een volledig werk voorbeeld het BasicSendReceiveUsingTopicSubscriptionClient-voor [beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) op github.

### <a name="rules-and-actions"></a>Regels en acties

In veel gevallen moeten berichten met specifieke kenmerken op verschillende manieren worden verwerkt. Als u deze verwerking wilt inschakelen, kunt u abonnementen configureren om berichten te vinden die gewenste eigenschappen hebben en vervolgens bepaalde wijzigingen aan die eigenschappen uit te voeren. Hoewel Service Bus-abonnementen alle berichten zien die naar het onderwerp worden verzonden, kunt u alleen een subset van die berichten kopiëren naar de wachtrij voor virtuele abonnementen. Dit filter wordt uitgevoerd met behulp van abonnements filters. Dergelijke wijzigingen worden *filter acties*genoemd. Wanneer een abonnement is gemaakt, kunt u een filter expressie opgeven die werkt met de eigenschappen van het bericht, zowel de systeem eigenschappen (bijvoorbeeld label) als aangepaste toepassings eigenschappen (bijvoorbeeld **Archief** **naam**). In dit geval is de SQL-filter expressie optioneel. zonder een SQL-filter expressie wordt elke filter actie die op een abonnement is gedefinieerd, uitgevoerd op alle berichten voor dat abonnement.

Zie voor een volledig werk voorbeeld het TopicSubscriptionWithRuleOperationsSample-voor [beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) op github.

Zie de documentatie voor de klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) en [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) voor meer informatie over mogelijke filter waarden.

## <a name="java-message-service-jms-20-entities-preview"></a>JMS (Java Message Service) 2,0-entiteiten (preview-versie)

Client toepassingen die verbinding maken met Azure Service Bus Premium en gebruikmaken van de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kunnen gebruikmaken van de onderstaande entiteiten.

### <a name="queues"></a>Wachtrijen

Wacht rijen in JMS zijn semantisch vergelijkbaar met de traditionele Service Bus wacht rijen die hierboven worden beschreven.

Als u een wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` -klasse-

```java
Queue createQueue(String queueName)
```

### <a name="topics"></a>Onderwerpen

De onderwerpen in JMS zijn semantisch vergelijkbaar met de traditionele Service Bus onderwerpen die hierboven worden besproken.

Gebruik de onderstaande methoden in de-klasse om een onderwerp te maken `JMSContext` :

```java
Topic createTopic(String topicName)
```

### <a name="temporary-queues"></a>Tijdelijke wacht rijen

Wanneer een client toepassing een tijdelijke entiteit vereist die voor de levens duur van de toepassing bestaat, kan deze gebruikmaken van tijdelijke wacht rijen. Deze worden gebruikt in het antwoord op de [aanvraag](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Als u een tijdelijke wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` -klasse-

```java
TemporaryQueue createTemporaryQueue()
```

### <a name="temporary-topics"></a>Tijdelijke onderwerpen

Net als tijdelijke wacht rijen bestaan er tijdelijke onderwerpen om publiceren/abonneren mogelijk te maken via een tijdelijke entiteit die bestaat voor de levens duur van de toepassing.

Als u een tijdelijk onderwerp wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
TemporaryTopic createTemporaryTopic()
```

### <a name="java-message-service-jms-subscriptions"></a>JMS-abonnementen (Java Message Service)

Hoewel dit in semantische richting lijkt op de hierboven beschreven abonnementen (op basis van een onderwerp en het publiceren/abonneren van semantiek inschakelen), introduceert de beschrijving van de Java-bericht service de concepten van **gedeelde**, niet- **gedeelde**, **duurzame** en **niet-duurzame** kenmerken van een bepaald abonnement.

> [!NOTE]
> De onderstaande abonnementen zijn beschikbaar in Azure Service Bus Premium-laag voor Preview-versies van client toepassingen die verbinding maken met Azure Service Bus met behulp van de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Voor de open bare Preview kunnen deze abonnementen niet worden gemaakt met behulp van de Azure Portal.
>

#### <a name="shared-durable-subscriptions"></a>Gedeelde duurzame abonnementen

Er wordt een gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing altijd actief verbruikt van het abonnement.

Aangezien dit een gedeeld abonnement is, kan elke toepassing die is geverifieerd om te ontvangen van Service Bus, worden ontvangen van het abonnement.

Als u een gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden voor de `JMSContext` klasse-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Het gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

#### <a name="unshared-durable-subscriptions"></a>Niet-gedeelde duurzame abonnementen

Net als bij een gedeeld duurzaam abonnement wordt een niet-gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing op elk moment actief gebruikmaakt van het abonnement.

Omdat dit echter een niet-gedeeld abonnement is, kan alleen de toepassing die het abonnement heeft gemaakt hiervan worden ontvangen.

Als u een niet-gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden van `JMSContext` klasse- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

#### <a name="shared-non-durable-subscriptions"></a>Gedeelde niet-duurzame abonnementen

Een gedeeld niet-duurzaam abonnement wordt gebruikt wanneer meerdere client toepassingen berichten van een enkel abonnement moeten ontvangen en verwerken, alleen tot ze er actief op worden verbruikt/ontvangen.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve gebruikers zijn.

Als u een gedeeld niet-duurzaam abonnement wilt maken, maakt u een `JmsConsumer` zoals wordt weer gegeven in de onderstaande methoden van de `JMSContext` klasse-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Het gedeelde niet-duurzame abonnement blijft bestaan tot er actieve consumers worden ontvangen.

#### <a name="unshared-non-durable-subscriptions"></a>Niet-verdeelde niet-duurzame abonnementen

Er wordt een niet-verwisselbaar niet-duurzaam abonnement gebruikt wanneer de client toepassing een bericht van een abonnement moet ontvangen en verwerken, alleen tot het systeem actief is. Er kan slechts één consument bestaan in dit abonnement, dat wil zeggen de client die het abonnement heeft gemaakt.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve consument op is.

Als u een niet-duurzaam abonnement wilt maken, maakt u een `JMSConsumer` zoals wordt weer gegeven in de onderstaande methoden van de JMSContext-klasse. 

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-duurzame niet-duurzaam abonnement blijft bestaan totdat er een actieve Consumer is die er een ontvangt.

#### <a name="message-selectors"></a>Bericht kiezer

Net zoals **filters en acties** bestaan voor gewone service bus abonnementen, bestaan er **bericht selectieers** voor JMS-abonnementen.

Bericht selectie vakjes kunnen worden ingesteld voor elk van de JMS-abonnementen en bestaan als filter voorwaarde voor de eigenschappen van de bericht header. Alleen berichten met header-eigenschappen die overeenkomen met de bericht kiezer-expressie worden geleverd. Een waarde van null of een lege teken reeks geeft aan dat er geen bericht kiezer is voor het JMS-abonnement/de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voor beelden van het gebruik van Service Bus Messa ging:

* [Overzicht van Service Bus berichten](service-bus-messaging-overview.md)
* [Snelstart: Berichten verzenden en ontvangen met behulp van Azure Portal en .NET](service-bus-quickstart-portal.md)
* [Zelfstudie: Voorraad bijwerken met Azure Portal en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)


