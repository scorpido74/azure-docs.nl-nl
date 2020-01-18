---
title: Gids voor probleem oplossing voor Azure Service Bus | Microsoft Docs
description: Lijst met uitzonde ringen en voorgestelde acties voor Service Bus berichten.
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
ms.date: 01/17/2020
ms.author: aschhab
ms.openlocfilehash: a795aa536e6e72b487abd18e60cfa52d6ab633ee
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264879"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Gids voor probleem oplossing voor Azure Service Bus
Dit artikel bevat enkele van de .NET-uitzonde ringen die worden gegenereerd door Service Bus .NET Framework Api's en ook andere tips voor het oplossen van problemen. 

## <a name="service-bus-messaging-exceptions"></a>Service Bus-berichtuitzonderingen
In deze sectie vindt u de .NET-uitzonde ringen die worden gegenereerd door .NET Framework-Api's. 

### <a name="exception-categories"></a>Uitzondering categorieën
De Messa ging Api's genereren uitzonde ringen die kunnen vallen in de volgende categorieën, samen met de bijbehorende actie die u kunt uitvoeren om ze te herstellen. De betekenis en oorzaken van een uitzonde ring kunnen variëren, afhankelijk van het type Messa ging-entiteit:

1. Gebruikers coderings fout ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. runtime. serialisatie. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Algemene actie: probeert op te lossen van de code voordat u doorgaat.
2. Setup/configuratie fout ([Microsoft. ServiceBus. Messa ging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Algemene actie: de configuratie controleren en wijzig indien nodig.
3. Tijdelijke uitzonde ringen ([Microsoft. ServiceBus. Messa ging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [micro soft. ServiceBus. Messa ging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [micro soft. ServiceBus. Messa ging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Algemene actie: Voer de bewerking opnieuw uit of gebruikers een melding ontvangen. De klasse `RetryPolicy` in de client-SDK kan zodanig worden geconfigureerd dat nieuwe pogingen automatisch worden verwerkt. Zie [richt lijnen voor opnieuw proberen](/azure/architecture/best-practices/retry-service-specific#service-bus)voor meer informatie.
4. Andere uitzonde ringen ([System. Trans actions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [micro soft. ServiceBus. Messa ging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [micro soft. ServiceBus. Messa ging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Algemene actie: specifiek voor het uitzonderings type; Raadpleeg de tabel in de volgende sectie: 

### <a name="exception-types"></a>Uitzonderingstypen
De volgende tabel bevat de uitzonderingstypen berichten, en hun oorzaken en opmerkingen bij de voorgestelde actie die u kunt ondernemen.

| **Uitzonderings type** | **Beschrijving/oorzaak/voor beelden** | **Aanbevolen actie** | **Opmerking over automatische/directe nieuwe poging** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet gereageerd op de aangevraagde bewerking binnen de opgegeven tijd, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). De server kan de aangevraagde bewerking hebt voltooid. Dit kan gebeuren vanwege vertragingen in het netwerk of andere infra structuur. |Controleer de status voor de consistentie en probeer indien nodig. Zie [time-outuitzonderingen](#timeoutexception). |Opnieuw proberen kan helpen in sommige gevallen; Voeg logica voor opnieuw proberen toe aan code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De aangevraagde gebruikers bewerking is niet toegestaan binnen de server of service. Zie het bericht van uitzondering voor meer informatie. Met de [functie complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) wordt deze uitzonde ring gegenereerd als het bericht is ontvangen in de [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -modus. |Controleer de code en de documentatie. Zorg ervoor dat de aangevraagde bewerking is ongeldig. |Nieuwe poging is niet meer nodig. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Er is een poging gedaan om aan te roepen een bewerking op een object dat al is gesloten, is afgebroken, of verwijderd. In zeldzame gevallen is de ambient transactie al verwijderd. |Controleer de code en zorg ervoor dat deze geen bewerkingen aanroept op een verwijderd object. |Nieuwe poging is niet meer nodig. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Het [token provider](/dotnet/api/microsoft.servicebus.tokenprovider) -object kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die vereist zijn om de bewerking uit te voeren. |Zorg ervoor dat de tokenprovider wordt gemaakt met de juiste waarden. Controleer de configuratie van de Access Control Service. |Opnieuw proberen kan helpen in sommige gevallen; Voeg logica voor opnieuw proberen toe aan code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Een of meer argumenten doorgegeven aan de methode zijn ongeldig.<br /> De URI opgegeven [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) pad segmenten betreffende bedrijfsactiviteit bevat.<br /> Het URI-schema opgegeven [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) is ongeldig. <br />De eigenschapswaarde is groter dan 32 KB. |Controleer de aanroepende code en controleer of dat de argumenten juist zijn. |Nieuwe poging is niet meer nodig. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |De entiteit die aan de bewerking is gekoppeld, bestaat niet of is verwijderd. |Zorg ervoor dat de entiteit bestaat. |Nieuwe poging is niet meer nodig. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Poging tot het ontvangen van een bericht met een bepaald Volg nummer. Dit bericht is niet gevonden. |Zorg ervoor dat het bericht nog niet is ontvangen. Controleer de deadletter-wachtrij om te zien of het bericht is deadlettered. |Nieuwe poging is niet meer nodig. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |De client kan geen verbinding tot stand brengen met Service Bus. |Zorg ervoor dat de opgegeven hostnaam juist is en de host bereikbaar is. |Opnieuw proberen kan helpen als er onregelmatige connectiviteit. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |De aanvraag kan op dit moment niet door de service worden verwerkt. |Client kan wachten op een bepaalde periode en voer de bewerking opnieuw uit. |Client kan opnieuw proberen na bepaalde interval. Als u een nieuwe poging resulteert in een andere uitzondering, controleert u gedrag voor opnieuw proberen van de uitzondering. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Het vergrendelings token dat aan het bericht is gekoppeld, is verlopen of het lock-token is niet gevonden. |Het bericht verwijderen. |Nieuwe poging is niet meer nodig. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |De vergren deling die aan deze sessie is gekoppeld, gaat verloren. |Het [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -object afbreken. |Nieuwe poging is niet meer nodig. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Generieke Messa ging-uitzonde ring die in de volgende gevallen kan worden gegenereerd:<br /> Er wordt geprobeerd een [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) te maken met behulp van een naam of pad dat tot een ander type entiteit behoort (bijvoorbeeld een onderwerp).<br />  Een poging is gedaan voor het verzenden van een bericht groter is dan 256 KB. De server of -service is een fout opgetreden tijdens het verwerken van de aanvraag. Zie het bericht van uitzondering voor meer informatie. Doorgaans is dit een tijdelijke uitzonde ring. |Controleer de code en ervoor te zorgen dat alleen serialiseerbare objecten worden gebruikt voor de hoofdtekst van bericht (of gebruik een aangepaste serializer). Raadpleeg de documentatie voor de typen ondersteunde waarde van de eigenschappen en alleen de gebruikstypen die worden ondersteund. Controleer de [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) eigenschap. Als de **waarde True**is, kunt u de bewerking opnieuw proberen. |Gedrag voor opnieuw proberen is niet gedefinieerd en niet kan helpen. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Poging tot het maken van een entiteit met een naam die al door een andere entiteit in die Servicenaamruimte gebruikt wordt. |Verwijder de bestaande entiteit of kies een andere naam voor de entiteit moet worden gemaakt. |Nieuwe poging is niet meer nodig. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |De bericht entiteit heeft de Maxi maal toegestane grootte bereikt of het maximum aantal verbindingen met een naam ruimte is overschreden. |Maak ruimte in de entiteit door het ontvangen van berichten van de entiteit of de subwachtrijen. Zie [QuotaExceededException](#quotaexceededexception). |Opnieuw proberen kan helpen als berichten in de tussentijd zijn verwijderd. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus deze uitzonde ring wordt geretourneerd als u probeert een ongeldige regel actie te maken. Service Bus koppelt deze uitzonde ring aan een deadlettered-bericht als er een fout optreedt tijdens het verwerken van de regel actie voor dat bericht. |Controleer de regel actie op juistheid. |Nieuwe poging is niet meer nodig. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus deze uitzonde ring wordt geretourneerd als u probeert een ongeldig filter te maken. Service Bus koppelt deze uitzonde ring aan een deadlettered-bericht als er een fout is opgetreden bij het verwerken van het filter voor dat bericht. |Controleer het filter op juistheid. |Nieuwe poging is niet meer nodig. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Poging een sessie met een specifieke sessie-ID te accepteren, maar de sessie is momenteel vergrendeld door een andere client. |Zorg ervoor dat de sessie wordt ontgrendeld door andere clients. |Opnieuw proberen kan helpen als de sessie in de tijdelijke situatie is vrijgegeven. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Te veel bewerkingen maken deel uit van de trans actie. |Verminder het aantal bewerkingen dat deel uitmaakt van deze trans actie. |Nieuwe poging is niet meer nodig. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Aanvraag voor een Runtimebewerking op een uitgeschakelde entiteit. |Activeer de entiteit. |Opnieuw proberen kan helpen als de entiteit in de tussentijd is geactiveerd. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus deze uitzonde ring wordt geretourneerd als u een bericht verzendt naar een onderwerp waarvoor vooraf filteren is ingeschakeld en er geen filters overeenkomen. |Zorg ervoor dat er ten minste één filter overeenkomt. |Nieuwe poging is niet meer nodig. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Een bericht lading overschrijdt de limiet van 256 KB. De limiet van 256 KB is de totale bericht grootte, die systeem eigenschappen en eventuele .NET-overhead kan bevatten. |Reduceer de grootte van de berichtnettolading van het en probeer het opnieuw. |Nieuwe poging is niet meer nodig. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |De ambient trans actie (*Trans Action. current*) is ongeldig. Mogelijk is deze voltooid of afgebroken. Binnenste uitzonde ring kan aanvullende informatie worden verstrekt. | |Nieuwe poging is niet meer nodig. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Er wordt een bewerking uitgevoerd op een trans actie die onzeker is, of er wordt geprobeerd de trans actie door te voeren en de trans actie wordt onzeker. |Uw toepassing moet deze uitzonde ring afhandelen (als speciaal geval), omdat de trans actie mogelijk al is doorgevoerd. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

#### <a name="queues-and-topics"></a>Wacht rijen en onderwerpen
Voor wacht rijen en onderwerpen is het vaak de grootte van de wachtrij. De eigenschap fout bericht bevat meer details, zoals in het volgende voor beeld:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

In het bericht wordt vermeld dat het onderwerp de maximale grootte heeft overschreden, in dit geval 1 GB (de standaard grootte). 

#### <a name="namespaces"></a>Naamruimten

Voor naam ruimten kan [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) aangeven dat het maximum aantal verbindingen met een naam ruimte is overschreden voor een toepassing. Bijvoorbeeld:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout: de wachtrij voor onbestelbare berichten en niet-functionerende bericht ontvangers.

1. **[Wachtrij met onbestelbare berichten](service-bus-dead-letter-queues.md)** Een lezer kan berichten niet volt ooien en de berichten worden teruggestuurd naar de wachtrij/het onderwerp wanneer de vergren deling verloopt. Dit kan gebeuren als de lezer een uitzonde ring tegen komt die verhindert dat deze [BrokeredMessage. complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)aanroept. Nadat een bericht 10 keer is gelezen, wordt het standaard naar de wachtrij voor onbestelbare berichten verplaatst. Dit gedrag wordt bepaald door de eigenschap [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) en heeft een standaard waarde van 10. Wanneer berichten in de wachtrij voor onbestelbare meldingen worden gestapeld, nemen ze ruimte in beslag.
   
    Om het probleem op te lossen, leest en voltooit u de berichten van de wachtrij voor onbestelbare meldingen, zoals u zou doen vanuit een andere wachtrij. U kunt de [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) -methode gebruiken om het wachtrij-pad voor onbestelbare berichten op te maken.
2. **Ontvanger gestopt**. Een ontvanger stopt met het ontvangen van berichten van een wachtrij of abonnement. De manier om dit te identificeren, is door te kijken naar de eigenschap [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , waarin de volledige uitsplitsing van de berichten wordt weer gegeven. Als de eigenschap [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) hoog of groei is, worden de berichten niet zo snel gelezen als ze worden geschreven.

### <a name="timeoutexception"></a>TimeoutException
Een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

U moet de waarde van de eigenschap [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) controleren, omdat deze limiet ook een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)kan veroorzaken.

#### <a name="queues-and-topics"></a>Wacht rijen en onderwerpen
Voor wacht rijen en onderwerpen wordt de time-out opgegeven in de eigenschap [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) , als onderdeel van de Connection String, of via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Het foutbericht zelf kan afwijken, maar bevat altijd de time-outwaarde voor de huidige bewerking opgegeven. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemen met connectiviteit, certificaten of time-outs
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaat/time-out voor alle services onder *. servicebus.windows.net. 

- Blader naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Het helpt te controleren of er problemen zijn met de IP-filtering of het virtuele netwerk of de certificaat keten (het meest gebruikelijk wanneer u Java SDK gebruikt).
- Voer de volgende opdracht uit om te controleren of een poort is geblokkeerd op de firewall. De gebruikte poorten zijn 443 (HTTPS), 5671 (AMQP) en 9354 (net Messa ging/SBMP). Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Hier volgt een voor beeld van de opdracht waarmee wordt gecontroleerd of de 5671-poort is geblokkeerd. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Op Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Met deze opdracht wordt geprobeerd 25 verschillende TCP-verbindingen elke 1 seconde te maken met de service. Vervolgens kunt u controleren of het aantal geslaagde/mislukte en ook TCP-verbindings latentie wordt weer geven. U kunt het `psping`-hulp programma [hier](/sysinternals/downloads/psping)downloaden.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tnc`, `ping`, enzovoort. 
- Verkrijg een netwerk tracering als de vorige stappen niet helpen en analyseren met behulp van hulpprogram ma's zoals [wireshark](https://www.wireshark.org/). Neem zo nodig contact op met [Microsoft ondersteuning](https://support.microsoft.com/) . 


## <a name="next-steps"></a>Volgende stappen

Zie de [Azure .net API-naslag](/dotnet/api/overview/azure/service-bus)informatie voor de volledige service bus .net API-referentie.

Raadpleeg de volgende artikelen voor meer informatie over [Service Bus](https://azure.microsoft.com/services/service-bus/):

* [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
* [Service Bus-architectuur](service-bus-architecture.md)

