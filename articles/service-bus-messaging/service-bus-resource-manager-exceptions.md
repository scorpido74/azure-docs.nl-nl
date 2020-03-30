---
title: Uitzonderingen op Azure Service Bus Resource Manager | Microsoft Documenten
description: Lijst met uitzonderingen voor servicebus die zijn opgedoken door Azure Resource Manager en voorgestelde acties.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978276"
---
# <a name="service-bus-resource-manager-exceptions"></a>Uitzonderingen op Service Bus Resource Manager

In dit artikel worden uitzonderingen weergegeven die zijn gegenereerd bij interactie met Azure Service Bus met Azure Resource Manager - via sjablonen of directe oproepen.

> [!IMPORTANT]
> Dit document wordt regelmatig bijgewerkt. Kijk terug voor updates.

Hieronder vindt u de verschillende uitzonderingen/fouten die via Azure Resource Manager worden weergegeven.

## <a name="error-bad-request"></a>Fout: Slecht verzoek

'Slecht verzoek' houdt in dat de aanvraag die door de Resource Manager is ontvangen, is mislukt.

| Foutcode | Foutsubcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Onjuiste aanvraag | 40000 | Subcode=40000. De eigenschap *'eigenschapnaam'* kan niet worden ingesteld bij het maken van een wachtrij omdat de naamruimte *'naamruimtenaam'* de 'Basisrij' gebruikt. Deze bewerking wordt alleen ondersteund in de laag 'Standaard' of 'Premium'. | Op Azure Service Bus Basic Tier kunnen de onderstaande eigenschappen niet worden ingesteld of bijgewerkt - <ul> <li> VereistDubbeleDetectie </li> <li> Automatisch verwijderen </li> <li>VereistSessie</li> <li>DefaultMessageTimeToLive DefaultMessageTimeToLive DefaultMessageTimeToLive DefaultMessage </li> <li> DuplicaatHistoryTimeWindow </li> <li> InschakelenExpress </li> <li> Doorsturen </li> <li> Onderwerpen </li> </ul> | Overweeg een upgrade van Basic naar Standard- of Premium-laag om deze functionaliteit te gebruiken. |
| Onjuiste aanvraag | 40000 | Subcode=40000. De waarde voor de eigenschap 'vereistDubbeledetectie' van een bestaande wachtrij(of onderwerp) kan niet worden gewijzigd. | Dubbele detectie moet zijn ingeschakeld/uitgeschakeld op het moment van het maken van de entiteit. De parameter voor dubbele detectieconfiguratie kan niet worden gewijzigd na het maken. | Als u dubbele detectie wilt inschakelen voor een eerder gemaakte wachtrij/onderwerp, u een nieuwe wachtrij/onderwerp maken met dubbele detectie en vervolgens doorsturen van de oorspronkelijke wachtrij naar de nieuwe wachtrij/onderwerp. |
| Onjuiste aanvraag | 40000 | Subcode=40000. De opgegeven waarde 16384 is ongeldig. De eigenschap 'MaxSizeInMegabytes' moet een van de volgende waarden zijn: 1024;2048;3072;4096;5120. | De waarde MaxSizeInMegabytes is ongeldig. | Zorg ervoor dat de MaxSizeInMegabytes een van de volgende is - 1024, 2048, 3072, 4096, 5120. |
| Onjuiste aanvraag | 40000 | Subcode=40000. Partitionering kan niet worden gewijzigd voor Wachtrij/onderwerp. | Partitionering kan niet worden gewijzigd voor entiteit. | Maak een nieuwe entiteit (wachtrij of onderwerp) en schakel partities in. | 
| Onjuiste aanvraag | geen | De naamruimte *'naamruimtenaam'* bestaat niet. | De naamruimte bestaat niet binnen uw Azure-abonnement. | Als u deze fout wilt oplossen, probeert u de onderstaande <ul> <li> Controleer of het Azure-abonnement correct is. </li> <li> Controleer of de naamruimte bestaat. </li> <li> Controleer of de naamruimtenaam correct is (geen spelfouten of null-tekenreeksen). </li> </ul> | 
| Onjuiste aanvraag | 40400 | Subcode=40400. De entiteit voor het automatisch doorsturen van de bestemming bestaat niet. | De bestemming voor de entiteit autoforwarding-bestemming bestaat niet. | De doelentiteit (wachtrij of onderwerp) moet bestaan voordat de bron wordt gemaakt. Probeer opnieuw na het maken van de doelentiteit. |
| Onjuiste aanvraag | 40000 | Subcode=40000. De meegeleverde sluistijd overschrijdt het toegestane maximum van '5' minuten. | De tijd waarvoor een bericht kan worden vergrendeld, moet tussen 1 minuut (minimum) en 5 minuten (maximum) liggen. | Zorg ervoor dat de meegeleverde vergrendelingstijd tussen 1 min en 5 minuten ligt. |
| Onjuiste aanvraag | 40000 | Subcode=40000. Zowel de eigenschap DelayedPersistence als requiresDuplicateDetection kunnen niet samen worden ingeschakeld. | Entiteiten met dubbele detectie ingeschakeld op hen moeten persistent zijn, zodat persistentie niet kan worden uitgesteld. | Meer informatie over [dubbele detectie](duplicate-detection.md) |
| Onjuiste aanvraag | 40000 | Subcode=40000. De waarde voor de eigenschap RequiresSession van een bestaande wachtrij kan niet worden gewijzigd. | Ondersteuning voor sessies moet worden ingeschakeld op het moment van het maken van entiteiten. Eenmaal gemaakt, u sessies op een bestaande entiteit (wachtrij of abonnement) niet meer in- of uitschakelen | Een nieuwe wachtrij (of abonnement) verwijderen en opnieuw maken met de eigenschap 'Vereistsessie'. |
| Onjuiste aanvraag | 40000 | Subcode=40000. 'URI_PATH' bevat karakter(s) die niet zijn toegestaan door de Service Bus. Entiteitssegmenten kunnen alleen letters, cijfers, perioden(.), koppeltekens(-) en underscores(_) bevatten. | Entiteitssegmenten kunnen alleen letters, cijfers, perioden(.), koppeltekens(-) en underscores(_) bevatten. Alle andere tekens zorgen ervoor dat het verzoek mislukt. | Zorg ervoor dat er geen ongeldige tekens in het URI-pad staan. |


## <a name="error-code-429"></a>Foutcode: 429

Net als in HTTP geeft "Foutcode 429" aan dat er "te veel aanvragen" zijn. Dit houdt in dat de specifieke resource (naamruimte) wordt beperkt vanwege te veel aanvragen (of als gevolg van conflicterende bewerkingen) op die resource.

| Foutcode | Foutsubcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode=50004. De aanvraag is beëindigd omdat de naamruimte *van uw naamruimte* wordt beperkt. | Deze foutvoorwaarde wordt geraakt wanneer het aantal binnenkomende aanvragen de beperking van de resource overschrijdt. | Wacht een paar seconden en probeer het opnieuw. <br/> <br/> Meer informatie over de [quota en](service-bus-quotas.md) Azure [Resource Manager-aanvraaglimieten](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcode=40901. Een andere conflicterende operatie is in volle gang. | Een andere conflicterende bewerking is aan de gang op dezelfde resource/entiteit | Wacht tot de huidige bewerking is voltooid voordat u het opnieuw probeert. |
| 429 | 40900 | SubCode=40900. Conflict. U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. | Deze voorwaarde kan worden geraakt wanneer meerdere aanvragen worden gedaan om de bewerkingen op dezelfde entiteit (wachtrij, onderwerp, abonnement of regel) tegelijkertijd uit te voeren. | Wacht een paar seconden en probeer het opnieuw |
| 429 | 40901 | Aanvraag op *entiteitsnaam in* strijd met een ander verzoek | Een andere conflicterende bewerking is aan de gang op dezelfde resource/entiteit | Wachten tot de vorige bewerking is voltooid voordat u het opnieuw probeert |
| 429 | 40901 | Er loopt nog een updateverzoek voor de *entiteit 'entiteitsnaam'.* | Een andere conflicterende bewerking is aan de gang op dezelfde resource/entiteit | Wachten tot de vorige bewerking is voltooid voordat u het opnieuw probeert |
| 429 | geen | Er is een resourceconflict opgetreden. Een andere conflicterende operatie kan worden uitgevoerd. Als dit een nieuwe poging is voor mislukte bewerking, is het opschonen op de achtergrond nog in behandeling. Probeer het later opnieuw. | Deze voorwaarde kan worden geraakt wanneer er een lopende bewerking tegen dezelfde entiteit is. | Wacht tot de vorige bewerking is voltooid voordat u het opnieuw probeert. |


## <a name="error-code-not-found"></a>Foutcode: niet gevonden

Deze klasse van fouten geeft aan dat de resource niet is gevonden.

| Foutcode | Foutsubcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Niet gevonden | geen | Entiteit *'entiteitsnaam'* werd niet gevonden. | De entiteit waartegen de bewerking niet is gevonden. | Controleer of de entiteit bestaat en probeer de bewerking opnieuw. |
| Niet gevonden | geen | Niet gevonden. De operatie bestaat niet. | De bewerking die u probeert uit te voeren, bestaat niet. | Controleer de bewerking en probeer het opnieuw. |
| Niet gevonden | geen | De binnenkomende aanvraag wordt niet herkend als een naamruimtebeleidsaanvraag. | De binnenkomende aanvraaginstantie is null en kan dus niet worden uitgevoerd als een putverzoek. | Controleer de aanvraaginstantie om te controleren of deze niet niet null is. | 
| Niet gevonden | geen | De *'entiteitsnaam'* van de berichtenentiteit kan niet worden gevonden. | De entiteit tegen wie u de bewerking probeert uit te voeren, kan niet worden gevonden. | Controleer of de entiteit bestaat en probeer de bewerking opnieuw. |

## <a name="error-code-internal-server-error"></a>Foutcode: interne serverfout

Deze klasse van fouten geeft aan dat er een interne serverfout is opgetreden

| Foutcode | Foutsubcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Interne serverfout | 50000 | Subcode=50000. Interne serverfout| Kan gebeuren om verschillende redenen. Sommige van de symptomen zijn - <ul> <li> Client aanvraag / lichaam is beschadigd en leidt tot een fout. </li> <li> De clientaanvraag heeft een time-out vanwege verwerkingsproblemen op de service. </li> </ul> | Om dit op te lossen <ul> <li> Zorg ervoor dat de aanvragen parameters niet null of misvormd zijn. </li> <li> Probeer het verzoek opnieuw. </li> </ul> |

## <a name="error-code-unauthorized"></a>Foutcode: ongeautoriseerd

Deze klasse van fouten geeft het ontbreken van toestemming om de opdracht uit te voeren.

| Foutcode | Foutsubcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Niet geautoriseerd | geen | Ongeldige bewerking op de secundaire naamruimte. Secundaire naamruimte is alleen-lezen. | De bewerking is uitgevoerd tegen de secundaire naamruimte, die is ingesteld als een alleen-lezen naamruimte. | Probeer de opdracht opnieuw tegen de primaire naamruimte. Meer informatie over [secundaire naamruimte](service-bus-geo-dr.md) |
| Niet geautoriseerd | geen | MissingToken: De autorisatiekop is niet gevonden. | Deze fout treedt op wanneer de autorisatie nullof onjuiste waarden heeft. | Controleer of de tokenwaarde die in de autorisatiekop wordt vermeld, correct is en niet null. |