---
title: Uitzonde ringen voor Azure Service Bus Resource Manager | Microsoft Docs
description: Lijst met Service Bus-uitzonde ringen die door Azure Resource Manager en aanbevolen acties worden geoppereerd.
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
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329303"
---
# <a name="service-bus-resource-manager-exceptions"></a>Uitzonde ringen voor Service Bus Resource Manager

Dit artikel bevat een lijst met uitzonde ringen die worden gegenereerd bij het werken met Azure Service Bus met behulp van Azure Resource Manager-via-sjablonen of directe aanroepen.

> [!IMPORTANT]
> Dit document wordt regel matig bijgewerkt. Ga terug naar updates.

Hieronder vindt u de verschillende uitzonde ringen/fouten die worden weer gegeven via de Azure Resource Manager.

## <a name="error-bad-request"></a>Fout: Onjuiste aanvraag

"Onjuiste aanvraag" impliceert dat de aanvraag die door de Resource Manager is ontvangen, niet is gevalideerd.

| Foutcode | Fout subcode | Foutbericht | Description | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De eigenschaps *naam* kan niet worden ingesteld bij het maken van een wachtrij, omdat de naam ruimte *name* van de naam ruimte de laag Basic gebruikt. Deze bewerking wordt alleen ondersteund in de laag Standard of Premium. | Op Azure Service Bus Basic-laag kunnen de onderstaande eigenschappen niet worden ingesteld of bijgewerkt: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Onderwerpen </li> </ul> | U kunt een upgrade uitvoeren van Basic naar Standard of Premium om gebruik te maken van deze functionaliteit. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De waarde voor de eigenschap requiresDuplicateDetection van een bestaande wachtrij kan niet worden gewijzigd. | Duplicaten detectie moet zijn ingeschakeld/uitgeschakeld op het moment dat de entiteit wordt gemaakt. Nadat deze is gemaakt, kan de configuratie parameter voor duplicaten detectie niet worden gewijzigd. | Om duplicaten detectie in te scha kelen voor een eerder gemaakte wachtrij/abonnement, kunt u een nieuwe wachtrij maken met dubbele detectie en vervolgens van de oorspronkelijke wachtrij naar de nieuwe wachtrij sturen. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De opgegeven waarde 16384 is ongeldig. De eigenschap MaxSizeInMegabytes moet een van de volgende waarden hebben: 1024; 2048; 3072; 4096; 5120. | De MaxSizeInMegabytes-waarde is ongeldig. | Zorg ervoor dat de MaxSizeInMegabytes een van de volgende is: 1024, 2048, 3072, 4096, 5120. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. Partitioneren kan niet worden gewijzigd voor de wachtrij. | Partitioneren kan niet worden gewijzigd voor entiteit. | Maak een nieuwe entiteit en schakel partities in. | 
| Onjuiste aanvraag | geen | De naam ruimte *name* van de naam ruimte bestaat niet. | De naam ruimte bestaat niet in uw Azure-abonnement. | Voer de onderstaande stappen uit om deze fout op te lossen <ul> <li> Zorg ervoor dat het Azure-abonnement juist is. </li> <li> Zorg ervoor dat de naam ruimte bestaat. </li> <li> Controleer of de naam van de naam ruimte juist is (geen spel fouten of null-teken reeksen). </li> </ul> | 
| Onjuiste aanvraag | 40400 | Subcode = 40400. De doel entiteit voor automatisch door sturen bestaat niet. | De bestemming voor de doel entiteit voor automatisch door sturen bestaat niet. | De doel entiteit (wachtrij of onderwerp) moet bestaan voordat de bron wordt gemaakt. Probeer het opnieuw nadat de doel entiteit is gemaakt. |


## <a name="error-code-429"></a>Fout code: 429

| Foutcode | Fout subcode | Foutbericht | Description | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. De aanvraag is beëindigd omdat de naam ruimte van *uw naam ruimte* wordt beperkt. | Deze fout is opgetreden wanneer het aantal binnenkomende aanvragen de beperking van de resource overschrijdt. | Wacht een paar seconden en probeer het opnieuw. <br/> <br/> Meer informatie over de [quota](service-bus-quotas.md) en [limieten voor Azure Resource Manager aanvragen](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcode = 40901. Er wordt een andere conflicterende bewerking uitgevoerd. | Er wordt een andere conflicterende bewerking uitgevoerd voor dezelfde resource/entiteit | Wacht tot de huidige bewerking in uitvoering is voltooid en probeer het opnieuw. |
| 429 | 40900 | Subcode = 40900. Conflicteren. U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. | Deze voor waarde kan worden bereikt wanneer er meerdere aanvragen worden gedaan voor het uitvoeren van de bewerkingen op dezelfde entiteit (wachtrij, onderwerp, abonnement of regel) tegelijk | Wacht een paar seconden en probeer het opnieuw |
| 429 | geen | Er is een bron conflict opgetreden. Er wordt mogelijk een andere conflicterende bewerking uitgevoerd. Als dit een nieuwe poging is voor een mislukte bewerking, is het opruimen van de achtergrond nog in behandeling. Probeer het later opnieuw. | Deze voor waarde kan worden bereikt wanneer er een bewerking in behandeling is voor dezelfde entiteit. | Wacht tot de vorige bewerking is voltooid en probeer het opnieuw. |

