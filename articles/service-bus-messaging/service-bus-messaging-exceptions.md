---
title: Azure Service Bus - messaging uitzonderingen | Microsoft Documenten
description: In dit artikel vindt u een lijst met uitzonderingen op Azure Service Bus-berichten en worden acties voorgesteld die moeten worden uitgevoerd wanneer de uitzondering optreedt.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880785"
---
# <a name="service-bus-messaging-exceptions"></a>Uitzonderingen voor servicebusberichten
In dit artikel worden de .NET-uitzonderingen weergegeven die worden gegenereerd door .NET Framework API's. 

## <a name="exception-categories"></a>Uitzonderingscategorieën
De messaging-API's genereren uitzonderingen die kunnen vallen in de volgende categorieën, samen met de bijbehorende actie die u ondernemen om te proberen ze op te lossen. De betekenis en oorzaken van een uitzondering kunnen variëren afhankelijk van het type berichtenentiteit:

1. Gebruikerscoderingsfout ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Algemene actie: probeer de code vast te stellen voordat u verdergaat.
2. Installatie-/configuratiefout[(Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Algemene actie: bekijk uw configuratie en wijzig indien nodig.
3. Tijdelijke uitzonderingen[(Microsoft.ServiceBus.messaging.messagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.servicebus.messaging.serverBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.servicebus.messaging.messagingcommunicationexception](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Algemene actie: probeer de bewerking opnieuw of stel gebruikers op de hoogte. De `RetryPolicy` klasse in de client-SDK kan worden geconfigureerd om opnieuw berichten automatisch te verwerken. Zie [Richtlijnen opnieuw proberen](/azure/architecture/best-practices/retry-service-specific#service-bus)voor meer informatie.
4. Andere uitzonderingen ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.serviceBus.messaging.messagelocklostexception](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.servicebus.messaging.sessionlocklostexception](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Algemene actie: specifiek voor het uitzonderingstype; zie de tabel in de volgende sectie: 

## <a name="exception-types"></a>Uitzonderingstypen
In de volgende tabel worden berichtenuitzonderingtypen en de bijbehorende oorzaken weergegeven en worden de voorgestelde actie met notities weergegeven.

| **Uitzonderingstype** | **Beschrijving/oorzaak/voorbeelden** | **Voorgestelde actie** | **Opmerking over automatische/onmiddellijke nieuwe poging** |
| --- | --- | --- | --- |
| [Time-outUitzondering](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet binnen de gestelde tijd gereageerd op de gevraagde bewerking, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). De server heeft mogelijk de gevraagde bewerking voltooid. Dit kan gebeuren als gevolg van netwerk- of andere infrastructuurvertragingen. |Controleer de systeemstatus op consistentie en probeer indien nodig opnieuw. Zie [Uitzonderingen op time-outs](#timeoutexception). |Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [Ongeldig OperationEelUitzondering](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De gevraagde gebruikersbewerking is niet toegestaan binnen de server of service. Zie het uitzonderingsbericht voor meer informatie. Hiermee genereert U bijvoorbeeld [deze](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) uitzondering als het bericht is ontvangen in de modus Ontvangen [en verwijderen.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Controleer de code en de documentatie. Controleer of de aangevraagde bewerking geldig is. |Opnieuw proberen helpt niet. |
| [BewerkingGeannuleerde uitzondering](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Er wordt geprobeerd een bewerking in te roepen op een object dat al is gesloten, afgebroken of verwijderd. In zeldzame gevallen is de omgevingstransactie al verwijderd. |Controleer de code en controleer of deze niet wordt aangesproken op bewerkingen op een verwijderd object. |Opnieuw proberen helpt niet. |
| [OngeautoriseerdeAccessUitzondering](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Het [TokenProvider-object](/dotnet/api/microsoft.servicebus.tokenprovider) kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die nodig zijn om de bewerking uit te voeren. |Zorg ervoor dat de tokenprovider is gemaakt met de juiste waarden. Controleer de configuratie van de Access Control-service. |Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [ArgumentUitzondering](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentoutofrangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Een of meer argumenten die aan de methode worden geleverd, zijn ongeldig.<br /> De URI die wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken,](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) bevat padsegment(en).<br /> Het URI-schema dat wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) is ongeldig. <br />De waarde van de woning is groter dan 32 KB. |Controleer de belcode en controleer of de argumenten correct zijn. |Opnieuw proberen helpt niet. |
| [MessagingEntityNotfoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entiteit die aan de bewerking is gekoppeld, bestaat niet of is verwijderd. |Controleer of de entiteit bestaat. |Opnieuw proberen helpt niet. |
| [MessageNotfoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Probeer een bericht met een bepaald volgnummer te ontvangen. Dit bericht is niet gevonden. |Zorg ervoor dat het bericht nog niet is ontvangen. Controleer de wachtrij voor dode letteren om te zien of het bericht is opgelost. |Opnieuw proberen helpt niet. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Client is niet in staat om een verbinding met Service Bus vast te stellen. |Zorg ervoor dat de opgegeven hostnaam correct is en dat de host bereikbaar is. |Opnieuw proberen kan helpen als er met tussenpozen verbindingsproblemen zijn. |
| [ServerBusyException ServerBusyException ServerBusyException ServerBusy](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Service kan de aanvraag op dit moment niet verwerken. |Client kan een bepaalde tijd wachten en de bewerking vervolgens opnieuw proberen. |Client kan na bepaalde interval opnieuw proberen. Als een nieuwe poging resulteert in een andere uitzondering, controleert u het gedrag opnieuw proberen van die uitzondering. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Vergrendelingstoken dat aan het bericht is gekoppeld, is verlopen of het vergrendelingstoken wordt niet gevonden. |Gooi het bericht weg. |Opnieuw proberen helpt niet. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Het slot dat aan deze sessie is gekoppeld, gaat verloren. |Het object [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) afbreken. |Opnieuw proberen helpt niet. |
| [MessagingUitzondering](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Algemene messaging-uitzondering die in de volgende gevallen kan worden gegooid:<p>Er wordt geprobeerd een [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) te maken met een naam of pad dat bij een ander entiteitstype hoort (bijvoorbeeld een onderwerp).</p><p>Er wordt geprobeerd een bericht te verzenden dat groter is dan 256 KB. </p>De server of service heeft een fout opgelopen tijdens de verwerking van de aanvraag. Zie het uitzonderingsbericht voor meer informatie. Het is meestal een tijdelijke uitzondering.</p><p>De aanvraag is beëindigd omdat de entiteit wordt beperkt. Foutcode: 50001, 50002, 50008. </p> | Controleer de code en zorg ervoor dat alleen serializable objecten worden gebruikt voor de berichtbody (of gebruik een aangepaste serializer). <p>Controleer de documentatie voor de ondersteunde waardetypen van de eigenschappen en gebruik alleen ondersteunde typen.</p><p> Controleer de eigenschap [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Als het **waar**is, u de bewerking opnieuw proberen. </p>| Als de uitzondering te wijten is aan beperking, wacht u enkele seconden en probeert u de bewerking opnieuw. Gedrag opnieuw proberen is niet gedefinieerd en helpt mogelijk niet in andere scenario's.|
| [MessagingEntityBestaatException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Probeer een entiteit te maken met een naam die al door een andere entiteit in die servicenaamruimte wordt gebruikt. |Verwijder de bestaande entiteit of kies een andere naam voor de entiteit die moet worden gemaakt. |Opnieuw proberen helpt niet. |
| [QuotaOverschreedException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |De berichtenentiteit heeft de maximaal toegestane grootte bereikt of het maximum aantal verbindingen met een naamruimte is overschreden. |Maak ruimte in de entiteit door berichten van de entiteit of de subwachtrijen te ontvangen. Zie [QuotumOverschre'sUitzondering](#quotaexceededexception). |Opnieuw proberen kan helpen als berichten in de tussentijd zijn verwijderd. |
| [RegelactieUitzondering](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Servicebus retourneert deze uitzondering als u een ongeldige regelactie probeert te maken. Servicebus koppelt deze uitzondering aan een doodgeletterd bericht als er een fout optreedt tijdens het verwerken van de regelactie voor dat bericht. |Controleer de regelactie op juistheid. |Opnieuw proberen helpt niet. |
| [Filteruitzondering](/dotnet/api/microsoft.servicebus.messaging.filterexception) |ServiceBus retourneert deze uitzondering als u een ongeldig filter probeert te maken. Service Bus hecht deze uitzondering aan een doodgeletterd bericht als er een fout is opgetreden tijdens het verwerken van het filter voor dat bericht. |Controleer het filter op juistheid. |Opnieuw proberen helpt niet. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Probeer een sessie met een specifieke sessie-id te accepteren, maar de sessie is momenteel vergrendeld door een andere client. |Zorg ervoor dat de sessie wordt ontgrendeld door andere clients. |Opnieuw proberen kan helpen als de sessie is vrijgegeven in de tussentijd. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Te veel bewerkingen maken deel uit van de transactie. |Verminder het aantal bewerkingen dat deel uitmaakt van deze transactie. |Opnieuw proberen helpt niet. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Aanvraag voor een runtime-bewerking op een uitgeschakelde entiteit. |Activeer de entiteit. |Opnieuw proberen kan helpen als de entiteit tussentijds is geactiveerd. |
| [NoMatchingSubscriptionException NoMatchingSubscriptionException NoMatchingSubscriptionException NoMatching](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Servicebus retourneert deze uitzondering als u een bericht verzendt naar een onderwerp dat vooraf is gefilterd en geen van de filters overeenkomt. |Zorg ervoor dat er ten minste één filter overeenkomt. |Opnieuw proberen helpt niet. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Een berichtpayload overschrijdt de limiet van 256 KB. De limiet van 256 KB is de totale berichtgrootte, die systeemeigenschappen en elke .NET-overhead kan bevatten. |Verklein de grootte van de laadlading van het bericht en probeer de bewerking opnieuw. |Opnieuw proberen helpt niet. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |De omgevingstransactie (*Transaction.Current*) is ongeldig. Het kan zijn voltooid of afgebroken. Innerlijke uitzondering kan aanvullende informatie opleveren. | |Opnieuw proberen helpt niet. |
| [TransactionindoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Een bewerking wordt geprobeerd op een transactie die in twijfel wordt getrokken, of er wordt een poging gedaan om de transactie te plegen en de transactie wordt in twijfel getrokken. |Uw aanvraag moet deze uitzondering (als een speciaal geval) afhandelen, omdat de transactie mogelijk al is vastgelegd. |- |

## <a name="quotaexceededexception"></a>QuotaOverschreedException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

### <a name="queues-and-topics"></a>Wachtrijen en onderwerpen
Voor wachtrijen en onderwerpen is dit vaak de grootte van de wachtrij. De eigenschap foutbericht bevat meer details, zoals in het volgende voorbeeld:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

In het bericht staat dat het onderwerp de groottelimiet heeft overschreden, in dit geval 1 GB (de standaardgroottelimiet). 

### <a name="namespaces"></a>Naamruimten

Voor naamruimten kan [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) aangeven dat een toepassing het maximum aantal verbindingen met een naamruimte heeft overschreden. Bijvoorbeeld:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout: de wachtrij voor dode letters en niet-functionerende berichtontvangers.

1. **[Dode-letter wachtrij](service-bus-dead-letter-queues.md)** Een lezer slaagt er niet in om berichten te voltooien en de berichten worden teruggestuurd naar de wachtrij/onderwerp wanneer het slot verloopt. Het kan gebeuren als de lezer een uitzondering tegenkomt die voorkomt dat deze [BrokeredMessage.Complete aanroept.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) Nadat een bericht 10 keer is gelezen, wordt het standaard naar de wachtrij voor dode letters verplaatst. Dit gedrag wordt beheerd door de eigenschap [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) en heeft een standaardwaarde van 10. Als berichten zich opstapelen in de wachtrij voor dode letters, nemen ze ruimte in beslag.
   
    Als u het probleem wilt oplossen, leest en voltooit u de berichten uit de wachtrij voor dode letters, zoals u dat in elke andere wachtrij zou doen. U de methode [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) gebruiken om het wachtrijpad voor dode letters op te maken.
2. **Ontvanger gestopt**. Een ontvanger ontvangt geen berichten meer uit een wachtrij of abonnement. De manier om dit te identificeren is door te kijken naar de eigenschap [QueueDescription.MessageCountDetails,](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) die de volledige uitsplitsing van de berichten weergeeft. Als de eigenschap [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) hoog is of groeit, worden de berichten niet zo snel gelezen als ze worden geschreven.

## <a name="timeoutexception"></a>Time-outUitzondering
Een [time-outuitzondering](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een door de gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

U moet de waarde van de eigenschap [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) controleren, omdat het bereiken van deze limiet ook een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)kan veroorzaken.

### <a name="queues-and-topics"></a>Wachtrijen en onderwerpen
Voor wachtrijen en onderwerpen wordt de time-out opgegeven in de eigenschap [MessagingFactorySettings.OperationTimeout,](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) als onderdeel van de verbindingstekenreeks, of via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Het foutbericht zelf kan variëren, maar bevat altijd de time-outwaarde die is opgegeven voor de huidige bewerking. 

## <a name="next-steps"></a>Volgende stappen
Zie de [azure .NET API-verwijzing](/dotnet/api/overview/azure/service-bus)voor de volledige referentie van de Service Bus .NET API .
Zie de handleiding [voor probleemoplossing](service-bus-troubleshooting-guide.md) voor tips voor probleemoplossing
