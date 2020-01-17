---
title: Gids voor probleem oplossing-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van Azure Event Hubs-berichtuitzonderingen en voorgestelde acties.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 26056e9b52ea319856505db837c67dc68b2f4aa6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157284"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Gids voor probleem oplossing voor Azure Event Hubs
Dit artikel bevat enkele van de .NET-uitzonde ringen die worden gegenereerd door Event Hubs .NET Framework Api's en ook andere tips voor het oplossen van problemen. 

## <a name="event-hubs-messaging-exceptions---net"></a>Uitzonde ringen voor Event Hubs berichten-.NET
In deze sectie vindt u de .NET-uitzonde ringen die worden gegenereerd door .NET Framework-Api's. 

### <a name="exception-categories"></a>Uitzondering categorieën

De Event Hubs .NET-Api's genereren uitzonde ringen die kunnen vallen in de volgende categorieën, samen met de bijbehorende actie die u kunt uitvoeren om ze te herstellen.

1. Fout met de code gebruiker: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Algemene actie: probeert op te lossen van de code voordat u doorgaat.
2. Setup/configuratiefout: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Algemene actie: de configuratie controleren en wijzig indien nodig.
3. Tijdelijke uitzonderingen: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Algemene actie: Voer de bewerking opnieuw uit of gebruikers een melding ontvangen.
4. Andere uitzonderingen: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Algemene actie: specifiek zijn voor het uitzonderingstype. Raadpleeg de tabel in de volgende sectie. 

### <a name="exception-types"></a>Uitzonderingstypen
De volgende tabel bevat de uitzonderingstypen berichten, en hun oorzaken en opmerkingen bij de voorgestelde actie die u kunt ondernemen.

| Type uitzondering | Beschrijving/oorzaak/voorbeelden | Voorgestelde actie | Houd er rekening mee automatisch/direct opnieuw proberen |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet gereageerd op de aangevraagde bewerking binnen de opgegeven tijd, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). De server kan de aangevraagde bewerking hebt voltooid. Deze uitzonde ring kan zich voordoen als gevolg van vertragingen in het netwerk of andere infra structuur. |Controleer de status voor de consistentie en probeer indien nodig.<br /> Zie [TimeoutException](#timeoutexception). | Opnieuw proberen kan helpen in sommige gevallen; Voeg logica voor opnieuw proberen toe aan code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De aangevraagde gebruikers bewerking is niet toegestaan binnen de server of service. Zie het bericht van uitzondering voor meer informatie. Bijvoorbeeld, [voltooid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) deze uitzondering wordt gegenereerd als het bericht is ontvangen [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modus. | Controleer de code en de documentatie. Zorg ervoor dat de aangevraagde bewerking is ongeldig. | Opnieuw proberen wordt niet geholpen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Er is een poging gedaan om aan te roepen een bewerking op een object dat al is gesloten, is afgebroken, of verwijderd. In zeldzame gevallen is de ambient transactie al verwijderd. | Controleer de code en zorg ervoor dat deze geen bewerkingen aanroept op een verwijderd object. | Opnieuw proberen wordt niet geholpen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Het [token provider](/dotnet/api/microsoft.servicebus.tokenprovider) -object kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die vereist zijn om de bewerking uit te voeren. | Zorg ervoor dat de tokenprovider wordt gemaakt met de juiste waarden. Controleer de configuratie van de Access Control Service. | Opnieuw proberen kan helpen in sommige gevallen; Voeg logica voor opnieuw proberen toe aan code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Een of meer argumenten doorgegeven aan de methode zijn ongeldig. De URI opgegeven [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) pad segmenten betreffende bedrijfsactiviteit bevat. Het URI-schema opgegeven [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) is ongeldig. De eigenschapswaarde is groter dan 32 KB. | Controleer de aanroepende code en controleer of dat de argumenten juist zijn. | Er kunnen niet opnieuw proberen. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entiteit die is gekoppeld aan de bewerking is niet aanwezig of is verwijderd. | Zorg ervoor dat de entiteit bestaat. | Er kunnen niet opnieuw proberen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Client kan geen verbinding maken met Event Hub. |Zorg ervoor dat de opgegeven hostnaam juist is en de host bereikbaar is. | Opnieuw proberen kan helpen als er onregelmatige connectiviteit. |
| [Micro soft. ServiceBus. Messa ging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Service is niet kan verwerken van de aanvraag op dit moment. | Client kan wachten op een bepaalde periode en voer de bewerking opnieuw uit. <br /> Zie [ServerBusyException](#serverbusyexception). | Client kan opnieuw proberen na bepaalde interval. Als u een nieuwe poging resulteert in een andere uitzondering, controleert u gedrag voor opnieuw proberen van de uitzondering. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Algemene uitzondering die kan worden gegenereerd in de volgende gevallen messaging: Er is geprobeerd om te maken een [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) met behulp van een naam of het pad die deel uitmaakt van een andere entiteit-type (bijvoorbeeld een onderwerp). Er is een poging gedaan om een bericht te verzenden dat groter is dan 1 MB. De server of -service is een fout opgetreden tijdens het verwerken van de aanvraag. Zie het bericht van uitzondering voor meer informatie. Deze uitzondering is doorgaans een tijdelijke uitzondering. | Controleer de code en ervoor te zorgen dat alleen serialiseerbare objecten worden gebruikt voor de hoofdtekst van bericht (of gebruik een aangepaste serializer). Raadpleeg de documentatie voor de typen ondersteunde waarde van de eigenschappen en alleen de gebruikstypen die worden ondersteund. Controleer de [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) eigenschap. Als het **waar**, kunt u de bewerking opnieuw. | Gedrag voor opnieuw proberen is niet gedefinieerd en niet kan helpen. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Poging tot het maken van een entiteit met een naam die al door een andere entiteit in die Servicenaamruimte gebruikt wordt. | Verwijder de bestaande entiteit of kies een andere naam voor de entiteit moet worden gemaakt. | Er kunnen niet opnieuw proberen. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | De Berichtentiteit heeft de maximale toegestane grootte bereikt. Deze uitzondering kan gebeuren als het maximum aantal ontvangers (dit is 5) is al geopend op het niveau van een per-consument. | Maak ruimte in de entiteit door het ontvangen van berichten van de entiteit of de subwachtrijen. <br /> Zie [QuotaExceededException](#quotaexceededexception) | Opnieuw proberen kan helpen als berichten in de tussentijd zijn verwijderd. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Aanvraag voor een Runtimebewerking op een uitgeschakelde entiteit. |Activeer de entiteit. | Opnieuw proberen kan helpen als de entiteit in de tussentijd is geactiveerd. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Een bericht lading overschrijdt de limiet van 1 MB. Deze limiet van 1 MB is voor het totale bericht, dat systeem eigenschappen en eventuele .NET-overhead kan bevatten. | Reduceer de grootte van de berichtnettolading van het en probeer het opnieuw. |Er kunnen niet opnieuw proberen. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

Deze uitzondering kan gebeuren als het maximum aantal ontvangers (5) is al geopend op het niveau van een per-consument.

#### <a name="event-hubs"></a>Event Hubs
Eventhubs heeft een limiet van 20 consumergroepen per Event Hub. Wanneer u probeert te maken, ontvangt u een [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

Voor Event Hubs, de time-out is opgegeven als onderdeel van de verbindingsreeks of via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Het foutbericht zelf kan afwijken, maar bevat altijd de time-outwaarde voor de huidige bewerking opgegeven. 

#### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee algemene oorzaken voor deze fout: onjuiste configuratie of een tijdelijke servicefout.

1. **Onjuiste configuratie** time-out voor de bewerking is mogelijk te klein voor de operationele voorwaarde. De standaardwaarde voor de time-out van de bewerking in de client-SDK is 60 seconden. Controleer of uw code heeft de waarde die is ingesteld op iets te klein. De voor waarde van het netwerk en het CPU-gebruik kan van invloed zijn op de tijd die nodig is om een bepaalde bewerking te volt ooien. Daarom moet de time-out van de bewerking niet worden ingesteld op een kleine waarde.
2. **Tijdelijke servicefout** soms de Event Hubs-service kan er vertragingen bij het verwerken van aanvragen, bijvoorbeeld tijdens perioden van intensief verkeer. In dergelijke gevallen kunt u de bewerking na een tijdje opnieuw proberen totdat de bewerking voltooid is. Als dezelfde bewerking wordt nog steeds na meerdere pogingen mislukt, gaat u naar de [Azure-service de status site](https://azure.microsoft.com/status/) om te zien of er bekende serviceonderbrekingen zijn.

### <a name="serverbusyexception"></a>ServerBusyException

Een [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) of [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) geeft aan dat een server overbelast is. Er zijn twee relevante foutcodes voor deze uitzondering.

#### <a name="error-code-50002"></a>Foutcode 50002

Deze fout kan optreden voor een van twee redenen:

1. De belasting wordt niet gelijkmatig verdeeld over alle partities op het Event Hub en de ene partitie is afhankelijk van de limiet van de lokale doorvoer eenheid.
    
    Oplossing: Herziening van de partitie distributiestrategie of probeert [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan helpen.

2. De Event Hubs naam ruimte heeft onvoldoende doorvoer eenheden (u kunt het scherm **metrische gegevens** controleren in het venster Event hubs naam ruimte in de [Azure Portal](https://portal.azure.com) om te bevestigen). In de portal wordt een samengevoegde (1 minuut) informatie weer gegeven, maar we meten de door Voer in realtime, zodat het slechts een schatting is.

    Oplossing: Het verhogen van de throughput units op de naamruimte kan helpen. U kunt dit doen met deze bewerking in de portal in de **schaal** venster van het scherm van Event Hubs-naamruimte. Of u kunt [automatisch vergroten](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Foutcode 50001

Deze fout moet zelden optreden. Dit gebeurt wanneer de uitvoering van code voor de naamruimte-container weinig CPU is – niet meer dan een paar seconden voordat de Event Hubs load balancer begint.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limiet voor aanroepen naar de methode GetRuntimeInformation
Azure Event Hubs ondersteunt Maxi maal 50 oproepen per seconde voor de GetRuntimeInfo per seconde. Er wordt een uitzonde ring weer gegeven die vergelijkbaar is met de volgende wanneer de limiet is bereikt:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemen met connectiviteit, certificaten of time-outs
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaat/time-out voor alle services onder *. servicebus.windows.net. 

- Blader naar of [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Het helpt te controleren of er problemen zijn met de IP-filtering of het virtuele netwerk of de certificaat keten (het meest gebruikelijk wanneer u Java SDK gebruikt).
- Voer de volgende opdracht uit om te controleren of een poort is geblokkeerd op de firewall. Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Bijvoorbeeld: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    Op Linux:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Met deze opdracht wordt geprobeerd 25 verschillende TCP-verbindingen elke 1 seconde met de service tot stand te brengen. vervolgens kunt u controleren hoeveel geslaagd/mislukt is en ook TCP-verbindings latentie weer geven. U kunt het `psping`-hulp programma [hier](/sysinternals/downloads/psping)downloaden.

    ```shell
    .\psping.exe -n 25 -i 1 -q yournamespace.servicebus.windows.net:5671 -nobanner     
    ```
    U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tnc`, `ping`, enzovoort. 
- Haal een netwerk tracering op als de vorige stappen niet helpen en analyseren of neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/). 

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
