---
title: Azure Relay-uitzonderingen en hoe deze op te lossen | Microsoft Documenten
description: Lijst met uitzonderingen voor Azure Relay en voorgestelde acties die u uitvoeren om deze op te lossen.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60749033"
---
# <a name="azure-relay-exceptions"></a>Azure Relay-uitzonderingen

In dit artikel worden enkele uitzonderingen weergegeven die kunnen worden gegenereerd door de Azure Relay-API's. Deze verwijzing kan worden gewijzigd, dus kom terug voor updates.

## <a name="exception-categories"></a>Uitzonderingscategorieën

De Relay API's genereren uitzonderingen die in de volgende categorieën kunnen vallen. Ook worden voorgestelde acties voorgesteld die u uitvoeren om de uitzonderingen op te lossen.

*   **Gebruikerscoderingsfout**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Algemene actie:** Probeer de code vast te stellen voordat u verdergaat.
*   **Installatie-/configuratiefout**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Algemene actie**: Controleer uw configuratie. Wijzig indien nodig de configuratie.
*   **Tijdelijke uitzonderingen**: [Microsoft.ServiceBus.messaging.messagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.servicebus.messaging.serverBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.servicebus.messaging.messagingcommunicationexception](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Algemene actie**: Probeer de bewerking opnieuw of stel gebruikers op de hoogte.
*   **Andere uitzonderingen**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Algemene actie**: Specifiek voor het uitzonderingstype. Zie de tabel in de volgende sectie. 

## <a name="exception-types"></a>Uitzonderingstypen

In de volgende tabel worden berichtenuitzonderingtypen en de oorzaken ervan weergegeven. Het bevat ook voorgestelde acties die u uitvoeren om de uitzonderingen op te lossen.

| **Uitzonderingstype** | **Beschrijving** | **Voorgestelde actie** | **Opmerking over automatische of onmiddellijke nieuwe poging** |
| --- | --- | --- | --- |
| [Timeout](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |De server heeft niet binnen de gestelde tijd gereageerd op de gevraagde bewerking, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). De server heeft mogelijk de gevraagde bewerking voltooid. Dit kan gebeuren als gevolg van netwerk- of andere infrastructuurvertragingen. |Controleer de systeemstatus op consistentie en probeer het indien nodig opnieuw. Zie [Time-outUitzondering](#timeoutexception). |Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [Ongeldige bewerking](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |De gevraagde gebruikersbewerking is niet toegestaan binnen de server of service. Zie het uitzonderingsbericht voor meer informatie. |Controleer de code en de documentatie. Controleer of de aangevraagde bewerking geldig is. |Opnieuw proberen zal niet helpen. |
| [Bewerking geannuleerd](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Er wordt geprobeerd een bewerking in te roepen op een object dat al is gesloten, afgebroken of verwijderd. In zeldzame gevallen is de omgevingstransactie al verwijderd. |Controleer de code en controleer of deze niet wordt aangesproken op bewerkingen op een verwijderd object. |Opnieuw proberen zal niet helpen. |
| [Ongeautoriseerde toegang](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Het [TokenProvider-object](/dotnet/api/microsoft.servicebus.tokenprovider) kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die nodig zijn om de bewerking uit te voeren. |Zorg ervoor dat de tokenprovider is gemaakt met de juiste waarden. Controleer de configuratie van de Access Control-service. |Opnieuw proberen kan in sommige gevallen helpen; logica opnieuw proberen toevoegen aan code. |
| [Argument uitzondering](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument buiten bereik](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Een of meer van de volgende is opgetreden:<br />Een of meer argumenten die aan de methode worden geleverd, zijn ongeldig.<br /> De URI die wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) bevat een of meer padsegmenten.<br />Het URI-schema dat wordt geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Maken](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) is ongeldig. <br />De waarde van de woning is groter dan 32 KB. |Controleer de belcode en controleer of de argumenten correct zijn. |Opnieuw proberen zal niet helpen. |
| [Server bezet](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Service kan de aanvraag op dit moment niet verwerken. |De client kan een bepaalde tijd wachten en vervolgens de bewerking opnieuw proberen. |De client kan het opnieuw proberen na een specifiek interval. Als een nieuwe poging resulteert in een andere uitzondering, controleert u het gedrag opnieuw proberen van die uitzondering. |
| [Quotum overschreden](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |De berichtenentiteit heeft de maximaal toegestane grootte bereikt. |Maak ruimte in de entiteit door berichten van de entiteit of de subwachtrijen te ontvangen. Zie [QuotumOverschre'sUitzondering](#quotaexceededexception). |Opnieuw proberen kan helpen als berichten in de tussentijd zijn verwijderd. |
| [Berichtgrootte overschreden](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Een berichtpayload overschrijdt de limiet van 256 KB. Houd er rekening mee dat de limiet van 256 KB de totale berichtgrootte is. De totale berichtgrootte kan systeemeigenschappen en eventuele Microsoft .NET-overhead bevatten. |Verklein de grootte van de laadlading van het bericht en probeer de bewerking opnieuw. |Opnieuw proberen zal niet helpen. |

## <a name="quotaexceededexception"></a>QuotaOverschreedException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

Voor Relay wordt met deze uitzondering het [Systeem.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx)verpakt, wat aangeeft dat het maximum aantal listeners voor dit eindpunt is overschreden. Dit wordt aangegeven in de maximumwaarde **ListenersPerEndpoint** van het uitzonderingsbericht.

## <a name="timeoutexception"></a>Time-outUitzondering
Een [time-outuitzondering](https://msdn.microsoft.com/library/system.timeoutexception.aspx) geeft aan dat een door de gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

Controleer de waarde van de eigenschap [ServicePointManager.DefaultConnectionLimit.](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) Het bereiken van deze limiet kan ook leiden tot een [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Voor Relay ontvangt u mogelijk een time-outuitzondering wanneer u voor het eerst een verbinding met relayafzenders opent. Er zijn twee veelvoorkomende oorzaken voor deze uitzondering:

*   De [OpenTimeout-waarde](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) is mogelijk te klein (al is het zelfs met een fractie van een seconde).
* Een on-premises relaylistener reageert mogelijk niet (of er kunnen firewallregels worden ondervonden die luisteraars verbieden nieuwe clientverbindingen te accepteren) en de [OpenTimeout-waarde](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) is minder dan 20 seconden.

Voorbeeld:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout:

*   **Onjuiste configuratie**
    
    De time-out van de bewerking is mogelijk te klein voor de bedrijfstoestand. De standaardwaarde voor de bewerkingstime-out in de client-SDK is 60 seconden. Controleer of de waarde in uw code is ingesteld op iets te klein. Houd er rekening mee dat het CPU-gebruik en de conditie van het netwerk van invloed kunnen zijn op de tijd die nodig is om een bewerking te voltooien. Het is een goed idee om de time-out van de operatie niet in te stellen op een zeer kleine waarde.
*   **Tijdelijke servicefout**

    Af en toe kan de Relay-service vertragingen ondervinden bij het verwerken van aanvragen. Dit kan bijvoorbeeld gebeuren tijdens perioden van veel verkeer. Als dit gebeurt, probeert u de bewerking opnieuw na een vertraging totdat de bewerking is geslaagd. Als dezelfde bewerking na meerdere pogingen blijft mislukken, controleert u de statussite van de [Azure-service](https://azure.microsoft.com/status/) om te zien of er bekende serviceonderbrekingen zijn.

## <a name="next-steps"></a>Volgende stappen
* [Veelgestelde vragen over Azure Relay](relay-faq.md)
* [Een naamruimte voor relais maken](relay-create-namespace-portal.md)
* [Aan de slag met Azure Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met Azure Relay en Knooppunt](relay-hybrid-connections-node-get-started.md)

