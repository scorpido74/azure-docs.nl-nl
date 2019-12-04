---
title: Gids voor probleem oplossing-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een lijst met uitzonde ringen en voorgestelde acties van Azure Event Hubs Messa ging.
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
ms.date: 12/03/2019
ms.author: shvija
ms.openlocfilehash: bea59ff29579c5d009a87c8d1564db4c0baf6e69
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793265"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Gids voor probleem oplossing voor Azure Event Hubs
Dit artikel bevat enkele van de .NET-uitzonde ringen die worden gegenereerd door Event Hubs .NET Framework Api's en ook andere tips voor het oplossen van problemen. 

## <a name="event-hubs-messaging-exceptions---net"></a>Uitzonde ringen voor Event Hubs berichten-.NET
In deze sectie vindt u de .NET-uitzonde ringen die worden gegenereerd door .NET Framework-Api's. 

### <a name="exception-categories"></a>Uitzonderings Categorieën

De Event Hubs .NET-Api's genereren uitzonde ringen die kunnen vallen in de volgende categorieën, samen met de bijbehorende actie die u kunt uitvoeren om ze te herstellen.

1. Gebruikers coderings fout: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. runtime. serialisatie. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Algemene actie: Probeer de code te corrigeren voordat u doorgaat.
2. Setup/configuratie fout: [micro soft. ServiceBus. Messa ging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [micro soft. Azure. Event hubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Algemene actie: Controleer uw configuratie en wijzig deze indien nodig.
3. Tijdelijke uitzonde ringen: [micro soft. ServiceBus. Messa ging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [micro soft. ServiceBus.](#serverbusyexception)Messa ging. ServerBusyException, [micro soft. Azure. Event hubs. ServerBusyException](#serverbusyexception), [micro soft. ServiceBus. Messa ging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Algemene actie: Probeer de bewerking opnieuw uit te voeren of gebruikers te informeren.
4. Andere uitzonde ringen: [System. Trans actions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](#timeoutexception), [micro soft. ServiceBus. Messa ging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [micro soft. ServiceBus. Messa ging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Algemene actie: specifiek voor het uitzonderings type; Raadpleeg de tabel in de volgende sectie. 

### <a name="exception-types"></a>Uitzonderings typen
De volgende tabel bevat een lijst met uitzonderings typen van berichten, en de oorzaken ervan, en de aanbevolen actie voor notities die u kunt uitvoeren.

| Uitzonderings type | Beschrijving/oorzaak/voor beelden | Aanbevolen actie | Opmerking over automatische/directe nieuwe poging |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet gereageerd op de aangevraagde bewerking binnen de opgegeven tijd, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). De server heeft de aangevraagde bewerking mogelijk voltooid. Deze uitzonde ring kan zich voordoen als gevolg van vertragingen in het netwerk of andere infra structuur. |Controleer de systeem status op consistentie en probeer het opnieuw als dat nodig is.<br /> Zie [TimeoutException](#timeoutexception). | In sommige gevallen kan het nodig zijn om het opnieuw te proberen. Voeg logica voor opnieuw proberen toe aan code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De aangevraagde gebruikers bewerking is niet toegestaan binnen de server of service. Zie het uitzonderings bericht voor meer informatie. Als u bijvoorbeeld [volt ooien](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) , wordt deze uitzonde ring gegenereerd als het bericht in de [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -modus is ontvangen. | Controleer de code en de documentatie. Controleer of de aangevraagde bewerking geldig is. | Opnieuw proberen wordt niet geholpen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Er wordt geprobeerd een bewerking aan te roepen voor een object dat al is gesloten, afgebroken of verwijderd. In zeldzame gevallen is de ambient trans actie al verwijderd. | Controleer de code en zorg ervoor dat deze geen bewerkingen aanroept op een verwijderd object. | Opnieuw proberen wordt niet geholpen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Het [token provider](/dotnet/api/microsoft.servicebus.tokenprovider) -object kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die vereist zijn om de bewerking uit te voeren. | Zorg ervoor dat de token provider is gemaakt met de juiste waarden. Controleer de configuratie van de Access Control Service. | In sommige gevallen kan het nodig zijn om het opnieuw te proberen. Voeg logica voor opnieuw proberen toe aan code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Een of meer argumenten die zijn opgegeven voor de methode, zijn ongeldig. De URI die is geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bevat een of meer paden. Het URI-schema dat is opgegeven aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) , is ongeldig. De waarde van de eigenschap is groter dan 32 KB. | Controleer de aanroepende code en controleer of de argumenten juist zijn. | Opnieuw proberen wordt niet geholpen. |
| [Micro soft. ServiceBus. Messa ging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Micro soft. Azure. Event hubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | De entiteit die aan de bewerking is gekoppeld, bestaat niet of is verwijderd. | Controleer of de entiteit bestaat. | Opnieuw proberen wordt niet geholpen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | De client kan geen verbinding maken met Event hub. |Controleer of de opgegeven hostnaam juist is en of de host bereikbaar is. | Opnieuw proberen kan helpen als er onregelmatige verbindings problemen zijn. |
| [Micro soft. ServiceBus. Messa ging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Micro soft. Azure. Event hubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | De aanvraag kan op dit moment niet door de service worden verwerkt. | De client kan gedurende een bepaalde tijd wachten en vervolgens de bewerking opnieuw uitvoeren. <br /> Zie [ServerBusyException](#serverbusyexception). | Client kan na een bepaald interval opnieuw proberen. Als een nieuwe poging resulteert in een andere uitzonde ring, controleert u het gedrag voor opnieuw proberen van deze uitzonde ring. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Generieke Messa ging-uitzonde ring die kan worden gegenereerd in de volgende gevallen: er wordt geprobeerd een [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) te maken met een naam of pad dat tot een ander type entiteit behoort (bijvoorbeeld een onderwerp). Er is een poging gedaan om een bericht te verzenden dat groter is dan 1 MB. Er is een fout opgetreden op de server of service tijdens het verwerken van de aanvraag. Zie het uitzonderings bericht voor meer informatie. Deze uitzonde ring is doorgaans een tijdelijke uitzonde ring. | Controleer de code en zorg ervoor dat alleen serialiseerbare objecten worden gebruikt voor de hoofd tekst van het bericht (of een aangepaste serialisatiefunctie te gebruiken). Raadpleeg de documentatie voor de ondersteunde typen waarden van de eigenschappen en gebruik alleen ondersteunde typen. Controleer de eigenschap [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Als dit het **geval**is, kunt u de bewerking opnieuw proberen. | Het gedrag voor opnieuw proberen is niet gedefinieerd en kan mogelijk niet worden geholpen. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Poging een entiteit te maken met een naam die al wordt gebruikt door een andere entiteit in die service naam ruimte. | Verwijder de bestaande entiteit of kies een andere naam voor de entiteit die u wilt maken. | Opnieuw proberen wordt niet geholpen. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | De bericht entiteit heeft de Maxi maal toegestane grootte bereikt. Deze uitzonde ring kan zich voordoen als het maximum aantal ontvangers (5) al is geopend op een groeps niveau per gebruiker. | Maak ruimte in de entiteit door berichten van de entiteit of de bijbehorende subwachtrijen te ontvangen. <br /> Zie [QuotaExceededException](#quotaexceededexception) | Opnieuw proberen kan helpen als er in de tussen tijd berichten zijn verwijderd. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Aanvraag voor een runtime-bewerking op een uitgeschakelde entiteit. |Activeer de entiteit. | Opnieuw proberen kan helpen als de entiteit is geactiveerd in de tussentijds. |
| [Micro soft. ServiceBus. Messa ging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Micro soft. Azure. Event hubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Een bericht lading overschrijdt de limiet van 1 MB. Deze limiet van 1 MB is voor het totale bericht, dat systeem eigenschappen en eventuele .NET-overhead kan bevatten. | Verminder de grootte van de bericht lading en voer de bewerking opnieuw uit. |Opnieuw proberen wordt niet geholpen. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

Deze uitzonde ring kan zich voordoen als het maximum aantal ontvangers (5) al is geopend op een groeps niveau per gebruiker.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs heeft een limiet van 20 consumenten groepen per Event hub. Wanneer u probeert meer te maken, ontvangt u een [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een door de gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

Voor Event Hubs wordt de time-out opgegeven als onderdeel van de connection string of via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Het fout bericht zelf kan variëren, maar het bevat altijd de time-outwaarde die voor de huidige bewerking is opgegeven. 

#### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout: onjuiste configuratie of een tijdelijke service fout.

1. **Onjuiste configuratie** De time-out van de bewerking is mogelijk te klein voor de operationele voor waarde. De standaard waarde voor de time-out van de bewerking in de client-SDK is 60 seconden. Controleer of de waarde is ingesteld op een te klein nummer. De voor waarde van het netwerk en het CPU-gebruik kan van invloed zijn op de tijd die nodig is om een bepaalde bewerking te volt ooien. Daarom moet de time-out van de bewerking niet worden ingesteld op een kleine waarde.
2. **Tijdelijke service fout** Soms kan de Event Hubs-service vertraging ondervinden bij het verwerken van aanvragen. bijvoorbeeld tijdens peri Oden met veel verkeer. In dergelijke gevallen kunt u de bewerking na een vertraging opnieuw proberen totdat de bewerking is voltooid. Als dezelfde bewerking na meerdere pogingen nog steeds mislukt, gaat u naar de [Azure-service status site](https://azure.microsoft.com/status/) om te zien of er sprake is van bekende service storingen.

### <a name="serverbusyexception"></a>ServerBusyException

Een [micro soft. ServiceBus. Messa ging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) of [micro soft. Azure. Event hubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) geeft aan dat een server overbelast is. Er zijn twee relevante fout codes voor deze uitzonde ring.

#### <a name="error-code-50002"></a>Fout code 50002

Deze fout kan een van de volgende twee oorzaken hebben:

1. De belasting wordt niet gelijkmatig verdeeld over alle partities op het Event Hub en de ene partitie is afhankelijk van de limiet van de lokale doorvoer eenheid.
    
    Oplossing: als u de strategie voor partitie distributie wijzigt of als u probeert [EventHubClient. send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) te gebruiken, kan dit helpen.

2. De Event Hubs naam ruimte heeft onvoldoende doorvoer eenheden (u kunt het scherm **metrische gegevens** controleren in het venster Event hubs naam ruimte in de [Azure Portal](https://portal.azure.com) om te bevestigen). In de portal wordt een samengevoegde (1 minuut) informatie weer gegeven, maar we meten de door Voer in realtime, zodat het slechts een schatting is.

    Oplossing: het verhogen van de doorvoer eenheden van de naam ruimte kan helpen. U kunt deze bewerking uitvoeren op de portal, in het venster **schalen** van het scherm Event hubs naam ruimte. U kunt ook [automatisch verg Roten](event-hubs-auto-inflate.md)gebruiken.

#### <a name="error-code-50001"></a>Fout code 50001

Deze fout moet zelden optreden. Het gebeurt wanneer de container code voor uw naam ruimte op de CPU laag is, niet meer dan een paar seconden voordat de Event Hubs load balancer begint.

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
- Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Zorg ervoor dat deze ongeveer 1 minuut wordt uitgevoerd om te weten of de verbindingen gedeeltelijk zijn geblokkeerd. U kunt het `psping`-hulp programma [hier](/sysinternals/downloads/psping)downloaden.

    ```shell
    psping.exe -t -q ehedhdev.servicebus.windows.net:9354 -nobanner     
    ```
    U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tnc`, `ping`, enzovoort. 
- Haal een netwerk tracering op als de vorige stappen niet helpen en analyseren of neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/). 

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
