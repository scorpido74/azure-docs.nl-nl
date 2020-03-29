---
title: Handleiding voor probleemoplossing - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u een lijst met uitzonderingen op Azure Event Hubs voor berichten en voorgestelde acties.
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
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309775"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Handleiding voor probleemoplossing voor Azure Event Hubs
In dit artikel worden enkele .NET-uitzonderingen die worden gegenereerd door Gebeurtenishubs .NET Framework API's en andere tips voor het oplossen van problemen. 

## <a name="event-hubs-messaging-exceptions---net"></a>Uitzonderingen op gebeurtenishubs - .NET
In deze sectie worden de .NET-uitzonderingen weergegeven die worden gegenereerd door .NET Framework API's. 

### <a name="exception-categories"></a>Uitzonderingscategorieën

De Gebeurtenishubs .NET API's genereren uitzonderingen die in de volgende categorieën kunnen vallen, samen met de bijbehorende actie die u ondernemen om ze op te lossen.

1. Gebruikerscoderingsfout: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Algemene actie: probeer de code vast te stellen voordat u verdergaat.
2. Installatie-/configuratiefout: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Algemene actie: bekijk uw configuratie en wijzig indien nodig.
3. Tijdelijke uitzonderingen: [Microsoft.servicebus.messaging.messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.servicebus.messaging.serverBusyException](#serverbusyexception), [Microsoft.azure.eventhubs.serverBusyException](#serverbusyexception), [Microsoft.servicebus.messaging.messagingcommunicationexception](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Algemene actie: probeer de bewerking opnieuw of stel gebruikers op de hoogte.
4. Andere uitzonderingen: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.serviceBus.messaging.messagelocklostexception](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.servicebus.messaging.sessionlocklostexception](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Algemene actie: specifiek voor het uitzonderingstype; zie de tabel in de volgende sectie. 

### <a name="exception-types"></a>Uitzonderingstypen
In de volgende tabel worden berichtenuitzonderingtypen en de bijbehorende oorzaken weergegeven en worden de voorgestelde actie met notities weergegeven.

| Uitzonderingstype | Beschrijving/oorzaak/voorbeelden | Voorgestelde actie | Opmerking over automatische/onmiddellijke nieuwe poging |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [Time-outUitzondering](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet binnen de gestelde tijd gereageerd op de gevraagde bewerking, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). De server heeft mogelijk de gevraagde bewerking voltooid. Deze uitzondering kan optreden als gevolg van netwerk- of andere infrastructuurvertragingen. |Controleer de systeemstatus op consistentie en probeer indien nodig opnieuw.<br /> Zie [Time-outUitzondering](#timeoutexception). | Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [Ongeldig OperationEelUitzondering](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De gevraagde gebruikersbewerking is niet toegestaan binnen de server of service. Zie het uitzonderingsbericht voor meer informatie. Hiermee genereert [Voltooien](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) bijvoorbeeld deze uitzondering als het bericht is ontvangen in de modus Ontvangen [en verwijderen.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Controleer de code en de documentatie. Controleer of de aangevraagde bewerking geldig is. | Opnieuw proberen zal niet helpen. |
| [BewerkingGeannuleerde uitzondering](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Er wordt geprobeerd een bewerking in te roepen op een object dat al is gesloten, afgebroken of verwijderd. In zeldzame gevallen is de omgevingstransactie al verwijderd. | Controleer de code en controleer of deze niet wordt aangesproken op bewerkingen op een verwijderd object. | Opnieuw proberen zal niet helpen. |
| [OngeautoriseerdeAccessUitzondering](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Het [TokenProvider-object](/dotnet/api/microsoft.servicebus.tokenprovider) kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die nodig zijn om de bewerking uit te voeren. | Zorg ervoor dat de tokenprovider is gemaakt met de juiste waarden. Controleer de configuratie van de Access Control-service. | Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [ArgumentUitzondering](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentoutofrangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Een of meer argumenten die aan de methode worden geleverd, zijn ongeldig. De URI die wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken,](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bevat padsegment(en). Het URI-schema dat wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) is ongeldig. De waarde van de woning is groter dan 32 KB. | Controleer de belcode en controleer of de argumenten correct zijn. | Opnieuw proberen zal niet helpen. |
| [Microsoft.servicebus.messagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entiteit die aan de bewerking is gekoppeld, bestaat niet of is verwijderd. | Controleer of de entiteit bestaat. | Opnieuw proberen zal niet helpen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Client kan geen verbinding met Event Hub tot stand brengen. |Zorg ervoor dat de opgegeven hostnaam correct is en dat de host bereikbaar is. | Opnieuw proberen kan helpen als er met tussenpozen verbindingsproblemen zijn. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Service kan de aanvraag op dit moment niet verwerken. | Client kan een bepaalde tijd wachten en de bewerking vervolgens opnieuw proberen. <br /> Zie [ServerBusyException](#serverbusyexception). | Client kan na bepaalde interval opnieuw proberen. Als een nieuwe poging resulteert in een andere uitzondering, controleert u het gedrag opnieuw proberen van die uitzondering. |
| [MessagingUitzondering](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Algemene messaging-uitzondering die in de volgende gevallen kan worden gegenereerd: er wordt geprobeerd een [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) te maken met behulp van een naam of pad dat deel uitmaakt van een ander entiteitstype (bijvoorbeeld een onderwerp). Er wordt geprobeerd een bericht te verzenden dat groter is dan 1 MB. De server of service heeft een fout opgelopen tijdens de verwerking van de aanvraag. Zie het uitzonderingsbericht voor meer informatie. Deze uitzondering is meestal een tijdelijke uitzondering. | Controleer de code en zorg ervoor dat alleen serializable objecten worden gebruikt voor de berichtbody (of gebruik een aangepaste serializer). Controleer de documentatie voor de ondersteunde waardetypen van de eigenschappen en gebruik alleen ondersteunde typen. Controleer de eigenschap [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Als het **waar**is, u de bewerking opnieuw proberen. | Gedrag opnieuw proberen is ongedefinieerd en helpt mogelijk niet. |
| [MessagingEntityBestaatException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Probeer een entiteit te maken met een naam die al door een andere entiteit in die servicenaamruimte wordt gebruikt. | Verwijder de bestaande entiteit of kies een andere naam voor de entiteit die moet worden gemaakt. | Opnieuw proberen zal niet helpen. |
| [QuotaOverschreedException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | De berichtenentiteit heeft de maximaal toegestane grootte bereikt. Deze uitzondering kan optreden als het maximum aantal ontvangers (dat is 5) al is geopend op een per-consument groep niveau. | Maak ruimte in de entiteit door berichten van de entiteit of de subwachtrijen te ontvangen. <br /> Zie [QuotaExceededException](#quotaexceededexception) | Opnieuw proberen kan helpen als berichten in de tussentijd zijn verwijderd. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Aanvraag voor een runtime-bewerking op een uitgeschakelde entiteit. |Activeer de entiteit. | Opnieuw proberen kan helpen als de entiteit tussentijds is geactiveerd. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Een berichtpayload overschrijdt de limiet van 1 MB. Deze limiet van 1 MB is voor het totale bericht, dat systeemeigenschappen en eventuele .NET-overhead kan bevatten. | Verklein de grootte van de laadlading van het bericht en probeer de bewerking opnieuw. |Opnieuw proberen zal niet helpen. |

### <a name="quotaexceededexception"></a>QuotaOverschreedException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

Deze uitzondering kan optreden als het maximum aantal ontvangers (5) al is geopend op groepsniveau per consument.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs heeft een limiet van 20 consumentengroepen per Event Hub. Wanneer u probeert meer te maken, ontvangt u een [QuotumOverschrijdinguitzondering](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>Time-outUitzondering
Een [time-outuitzondering](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een door de gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

Voor gebeurtenishubs wordt de time-out opgegeven als onderdeel van de verbindingstekenreeks of via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Het foutbericht zelf kan variëren, maar bevat altijd de time-outwaarde die is opgegeven voor de huidige bewerking. 

#### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout: onjuiste configuratie of een tijdelijke servicefout.

1. **Onjuiste configuratie** De time-out van de bewerking is mogelijk te klein voor de bedrijfstoestand. De standaardwaarde voor de bewerkingstime-out in de client-SDK is 60 seconden. Controleer of uw code de waarde heeft ingesteld op iets te klein. De toestand van het netwerk- en CPU-gebruik kan van invloed zijn op de tijd die nodig is om een bepaalde bewerking te voltooien, zodat de time-out van de bewerking niet op een kleine waarde moet worden ingesteld.
2. **Tijdelijke servicefout** Soms kan de Event Hubs-service vertragingen ondervinden bij het verwerken van aanvragen; bijvoorbeeld tijdens perioden van veel verkeer. In dergelijke gevallen u uw bewerking opnieuw proberen na een vertraging, totdat de bewerking is geslaagd. Als dezelfde bewerking na meerdere pogingen nog steeds mislukt, gaat u naar de [site met azure-servicestatus](https://azure.microsoft.com/status/) om te zien of er bekende serviceonderbrekingen zijn.

### <a name="serverbusyexception"></a>ServerBusyException ServerBusyException ServerBusyException ServerBusy

Een [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) of [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) geeft aan dat een server overbelast is. Er zijn twee relevante foutcodes voor deze uitzondering.

#### <a name="error-code-50002"></a>Foutcode 50002

Deze fout kan optreden om een van de twee redenen:

1. De belasting wordt niet gelijkmatig verdeeld over alle partities op de gebeurtenishub en één partitie raakt de beperking van de lokale doorvoereenheid.
    
    Oplossing: Het herzien van de distributiestrategie voor partities of het proberen van [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan helpen.

2. De naamruimte van gebeurtenishubs heeft niet voldoende doorvoereenheden (u het scherm **Metrische gegevens** controleren in het venster Naamruimte van gebeurtenishubs in de [Azure-portal](https://portal.azure.com) om te bevestigen). Het portaal toont geaggregeerde (1 minuut) informatie, maar we meten de doorvoer in real time - dus het is slechts een schatting.

    Oplossing: Het verhogen van de doorvoereenheden op de naamruimte kan helpen. U deze bewerking uitvoeren op de portal, in het **venster Schaal** van het naamruimtescherm van gebeurtenishubs. U [ook Automatisch opblazen](event-hubs-auto-inflate.md)gebruiken.

#### <a name="error-code-50001"></a>Foutcode 50001

Deze fout mag zelden optreden. Dit gebeurt wanneer de containerrunningcode voor uw naamruimte weinig CPU heeft - niet meer dan een paar seconden voordat de load balancer van Gebeurtenishubs begint.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Beperken voor aanroepen naar de methode GetRuntimeInformation
Azure Event Hubs ondersteunt maximaal 50 oproepen per seconde naar de GetRuntimeInfo per seconde. U een uitzondering ontvangen die vergelijkbaar is met de volgende nadat de limiet is bereikt:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemen met connectiviteit, certificaten of time-outs
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaat/time-out voor alle services onder *.servicebus.windows.net. 

- Bladeren naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Het helpt bij het controleren of u IP-filtering of virtuele netwerk- of certificaatketenproblemen hebt (het meest voorkomende bij het gebruik van java SDK).

    Een voorbeeld van een geslaagd bericht:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Een voorbeeld van foutfoutbericht:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Voer de volgende opdracht uit om te controleren of een poort op de firewall is geblokkeerd. Gebruikte poorten zijn 443 (HTTPS), 5671 (AMQP) en 9093 (Kafka). Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Hier is de voorbeeldopdracht die controleert of de 5671-poort is geblokkeerd.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Op Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Wanneer er met tussenpozen verbindingsproblemen zijn, voert u de volgende opdracht uit om te controleren of er gevallen pakketten zijn. Deze opdracht probeert om 25 verschillende TCP-verbindingen elke 1 seconde met de service tot stand te brengen. Vervolgens u controleren hoeveel van hen zijn geslaagd/mislukt en u ook de latentie van de TCP-verbinding zien. U `psping` de tool downloaden vanaf [hier.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    U gelijkwaardige opdrachten gebruiken als u andere `tnc` `ping`hulpprogramma's gebruikt, zoals, enzovoort. 
- Verkrijg een netwerktracering als de vorige stappen niet helpen en analyseren met behulp van tools zoals [Wireshark.](https://www.wireshark.org/) Neem indien nodig contact op met [Microsoft Support.](https://support.microsoft.com/) 

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
