---
title: Berichten over Azure Service Bus - wachtrijen, onderwerpen en abonnementen
description: In dit artikel vindt u een overzicht van berichtenentiteiten van Azure Service Bus (wachtrij, onderwerpen en abonnementen).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259511"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-wachtrijen, -onderwerpen en -abonnementen

Microsoft Azure Service Bus ondersteunt een reeks cloudgebaseerde, op berichten gerichte middleware-technologieën, waaronder betrouwbare berichtenwachtrijen en duurzame berichten over publiceren/abonneren. Deze "brokered" messaging-mogelijkheden kunnen worden gezien als ontkoppelde berichtenfuncties die publicatie-abonneren, tijdelijke ontkoppeling en taakverdelingsscenario's ondersteunen met behulp van de servicebus-berichtenworkload. Ontkoppelde communicatie heeft veel voordelen; clients en servers kunnen bijvoorbeeld naar behoefte verbinding maken en hun bewerkingen asynchroon uitvoeren.

De berichtenentiteiten die de kern vormen van de berichtenmogelijkheden in Service Bus zijn wachtrijen, onderwerpen en abonnementen en regels/acties.

## <a name="queues"></a>Wachtrijen

Wachtrijen bieden *First In, First Out* (FIFO) bericht levering aan een of meer concurrerende consumenten. Dat wil zeggen dat ontvangers doorgaans berichten ontvangen en verwerken in de volgorde waarin ze aan de wachtrij zijn toegevoegd, en slechts één berichtdat de consument elk bericht ontvangt en verwerkt. Een belangrijk voordeel van het gebruik van wachtrijen is het bereiken van "tijdelijke ontkoppeling" van toepassingscomponenten. Met andere woorden, de producenten (afzenders) en consumenten (ontvangers) hoeven niet tegelijkertijd berichten te verzenden en te ontvangen, omdat berichten blijvend in de wachtrij worden opgeslagen. Bovendien hoeft de producent niet te wachten op een antwoord van de consument om door te gaan met het verwerken en verzenden van berichten.

Een gerelateerd voordeel is "load leveling", waardoor producenten en consumenten berichten tegen verschillende tarieven kunnen verzenden en ontvangen. In veel toepassingen varieert de systeembelasting in de loop van de tijd; de verwerkingstijd die nodig is voor elke werkeenheid is echter meestal constant. Het intermedialeren van berichtenproducenten en consumenten met een wachtrij betekent dat de verbruikende toepassing alleen hoeft te worden ingericht om de gemiddelde belasting te kunnen verwerken in plaats van piekbelasting. De lengte van de wachtrij neemt toe of af, al naargelang het binnenkomende verkeer. Deze mogelijkheid bespaart direct geld met betrekking tot de hoeveelheid infrastructuur die nodig is om de toepassingsbelasting te onderhouden. Naarmate de belasting toeneemt, kunnen meer werkprocessen worden toegevoegd om uit de wachtrij te lezen. Elk bericht worden door slechts één van de werkprocessen verwerkt. Bovendien zorgt deze pull-based load balancing voor een optimaal gebruik van de werknemerscomputers, zelfs als de werknemerscomputers verschillen met betrekking tot verwerkingskracht, omdat ze berichten op hun eigen maximale snelheid trekken. Dit patroon wordt vaak het patroon van de "concurrerende consument" genoemd.

Het gebruik van wachtrijen tussen berichtenproducenten en consumenten zorgt voor een inherente losse koppeling tussen de componenten. Omdat producenten en consumenten zich niet bewust zijn van elkaar, kan een consument worden opgewaardeerd zonder enig effect op de producent.

### <a name="create-queues"></a>Wachtrijen maken

U maakt wachtrijen met de [Azure-portal,](service-bus-quickstart-portal.md) [PowerShell-](service-bus-quickstart-powershell.md) [, CLI-](service-bus-quickstart-cli.md)of [Resource Manager-sjablonen](service-bus-resource-manager-namespace-queue.md). Vervolgens verzendt en ontvangt u berichten met een [QueueClient-object.](/dotnet/api/microsoft.azure.servicebus.queueclient)

Zie de [snelstarts](service-bus-quickstart-portal.md) voor elke methode om snel te leren hoe u een wachtrij maakt en vervolgens berichten van en naar de wachtrij verzendt en ontvangt. Zie [Aan de slag met wachtrijen voor servicebus's](service-bus-dotnet-get-started-with-queues.md)voor een uitgebreidere zelfstudie over het gebruik van wachtrijen.

Zie het [voorbeeld basicsendontvangengebruikclientclient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) op GitHub voor een werkend voorbeeld.

### <a name="receive-modes"></a>Modi ontvangen

U twee verschillende modi opgeven waarin Service Bus berichten ontvangt: *ReceiveAndDelete* of *PeekLock*. In de [modus Ontvangen en verwijderen](/dotnet/api/microsoft.azure.servicebus.receivemode) is de ontvangstbewerking single-shot; dat wil zeggen, wanneer Service Bus ontvangt het verzoek van de consument, het markeert het bericht als zijnde verbruikt en retourneert het naar de toepassing van de consument. **ReceiveAndDelete-modus** is het eenvoudigste model en werkt het beste voor scenario's waarin de toepassing kan tolereren dat een bericht niet wordt verwerkt als er een fout optreedt. Als u dit scenario wilt begrijpen, moet u rekening houden met een scenario waarin de consument de ontvangstaanvraag uitgeeft en vervolgens vastloopt voordat u het verwerkt. Omdat Service Bus markeert het bericht als wordt verbruikt, wanneer de toepassing opnieuw wordt opgestart en begint te consumeren berichten weer, zal het gemist het bericht dat werd verbruikt voorafgaand aan de crash.

In de [PeekLock-modus](/dotnet/api/microsoft.azure.servicebus.receivemode) wordt de ontvangstbewerking tweetraps, waardoor toepassingen kunnen worden ondersteund die ontbrekende berichten niet kunnen tolereren. Wanneer Service Bus het verzoek ontvangt, wordt het volgende bericht gevonden dat moet worden verbruikt, wordt het vergrendeld om te voorkomen dat andere consumenten het ontvangen en stuurt het vervolgens terug naar de toepassing. Nadat de toepassing is voltooid met de verwerking van het bericht (of het betrouwbaar opslaat voor toekomstige verwerking), voltooit het de tweede fase van het ontvangstproces door [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) op het ontvangen bericht te bellen. Wanneer Service Bus de **CompleteAsync-aanroep** ziet, wordt het bericht als verbruikt.

Als de toepassing het bericht om de een of andere reden niet kan verwerken, kan het de [AbandonAsync-methode](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) in het ontvangen bericht (in plaats van [CompleteAsync)](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)aanroepen. Met deze methode kan Service Bus het bericht ontgrendelen en beschikbaar maken om opnieuw te worden ontvangen, hetzij door dezelfde consument, hetzij door een andere concurrerende consument. Ten tweede is er een time-out gekoppeld aan het slot en als de toepassing het bericht niet verwerkt voordat de vergrendelingstime-out verloopt (bijvoorbeeld als de toepassing crasht), ontgrendelt Service Bus het bericht en maakt het beschikbaar om opnieuw te worden ontvangen (in wezen het uitvoeren van een [AbandonAsync-bewerking](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) standaard).

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de **CompleteAsync-aanvraag** wordt uitgegeven, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit proces wordt vaak *ten minste eenmaal* verwerken genoemd; dat wil zeggen, elk bericht wordt minstens één keer verwerkt. In bepaalde situaties kan hetzelfde bericht echter opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan tolereren, is in de toepassing aanvullende logica vereist om duplicaten te detecteren, die kunnen worden bereikt op basis van de eigenschap [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) van het bericht, die constant blijft tijdens leveringspogingen. Deze functie staat bekend als *Exactly Once* processing.

## <a name="topics-and-subscriptions"></a>Onderwerpen en abonnementen

In tegenstelling tot wachtrijen, waarin elk bericht wordt verwerkt door een enkele consument, *onderwerpen* en *abonnementen* bieden een een-op-veel vorm van communicatie, in een *publiceren / abonneren* patroon. Handig voor het schalen naar grote aantallen ontvangers, elk gepubliceerd bericht wordt beschikbaar gesteld aan elk abonnement geregistreerd met het onderwerp. Berichten worden naar een onderwerp verzonden en geleverd aan een of meer gekoppelde abonnementen, afhankelijk van filterregels die per abonnement kunnen worden ingesteld. De abonnementen kunnen extra filters gebruiken om de berichten die ze willen ontvangen te beperken. Berichten worden naar een onderwerp verzonden op dezelfde manier als ze naar een wachtrij worden verzonden, maar berichten worden niet rechtstreeks van het onderwerp ontvangen. In plaats daarvan worden ze ontvangen van abonnementen. Een onderwerpabonnement lijkt op een virtuele wachtrij die kopieën ontvangt van de berichten die naar het onderwerp worden verzonden. Berichten worden ontvangen van een abonnement identiek aan de manier waarop ze worden ontvangen van een wachtrij.

Ter vergelijking: de functionaliteit voor het verzenden van berichten van een wachtrij wordt rechtstreeks toegewezen aan een onderwerp en de functionaliteit voor het ontvangen van berichten wordt toegewezen aan een abonnement. Deze functie houdt onder andere in dat abonnementen dezelfde patronen ondersteunen die eerder in deze sectie zijn beschreven met betrekking tot wachtrijen: concurrerende consument, tijdelijke ontkoppeling, taaknivellering en taakverdeling.

### <a name="create-topics-and-subscriptions"></a>Onderwerpen en abonnementen maken

Het maken van een onderwerp is vergelijkbaar met het maken van een wachtrij, zoals beschreven in de vorige sectie. Vervolgens verzendt u berichten via de klasse [TopicClient.](/dotnet/api/microsoft.azure.servicebus.topicclient) Als u berichten wilt ontvangen, maakt u een of meer abonnementen op het onderwerp. Net als in wachtrijen worden berichten ontvangen van een abonnement met een [object SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) in plaats van een [QueueClient-object.](/dotnet/api/microsoft.azure.servicebus.queueclient) Maak de abonnementsclient, geef de naam van het onderwerp, de naam van het abonnement en (optioneel) de ontvangstmodus als parameters door.

Zie het [voorbeeld BasicSendReceiveUsingAbonnementClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) op GitHub voor een volledig werkend voorbeeld.

### <a name="rules-and-actions"></a>Regels en acties

In veel scenario's moeten berichten met specifieke kenmerken op verschillende manieren worden verwerkt. Als u deze verwerking wilt inschakelen, u abonnementen configureren om berichten te vinden met de gewenste eigenschappen en vervolgens bepaalde wijzigingen in deze eigenschappen uit te voeren. Terwijl Service Bus-abonnementen alle berichten zien die naar het onderwerp worden verzonden, u alleen een subset van deze berichten naar de virtuele abonnementswachtrij kopiëren. Deze filtering wordt uitgevoerd met abonnementsfilters. Dergelijke wijzigingen worden *filteracties*genoemd. Wanneer een abonnement wordt gemaakt, u een filterexpressie leveren die werkt op de eigenschappen van het bericht, zowel de systeemeigenschappen (bijvoorbeeld **Label)** als aangepaste toepassingseigenschappen (bijvoorbeeld **StoreName**.) De SQL-filterexpressie is in dit geval optioneel. zonder SQL-filterexpressie wordt elke filteractie uitgevoerd die op een abonnement is gedefinieerd, op alle berichten voor dat abonnement.

Zie het [voorbeeld TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) op GitHub voor een volledig werkend voorbeeld.

Zie de documentatie voor de klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) en [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) voor meer informatie over mogelijke filterwaarden.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voorbeelden van het gebruik van servicebusberichten:

* [Overzicht van servicebusberichten](service-bus-messaging-overview.md)
* [Snelstart: Berichten verzenden en ontvangen met behulp van Azure Portal en .NET](service-bus-quickstart-portal.md)
* [Zelfstudie: voorraad bijwerken met Azure Portal en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)


