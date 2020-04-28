---
title: Azure Event Grid-probleemoplossings gids
description: Dit artikel bevat een lijst met fout codes, fout berichten, beschrijvingen en aanbevolen acties.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645069"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemen met Azure Event Grid oplossen
Deze hand leiding bevat een lijst met Azure Event Grid fout codes, fout berichten, beschrijvingen en aanbevolen acties die u moet uitvoeren wanneer u deze fouten ontvangt. 

## <a name="error-code-400"></a>Foutcode 400
| Foutcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. onjuiste aanvraag<br/>400 | De naam van het onderwerp moet tussen de 3 en 50 tekens lang zijn. | De naam van het aangepaste onderwerp moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de naam van het onderwerp. De naam mag ook niet beginnen met de volgende gereserveerde woorden: <ul><li>Microsoft</li><li>EventGrid</li><li>Systeem</li></ul> | Kies een andere onderwerpnaam die voldoet aan de vereisten voor de onderwerpnaam. |
| Http status code. onjuiste aanvraag<br/>400 | De domein naam moet tussen de 3 en 50 tekens lang zijn. | De lengte van de domein naam moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de naam van het onderwerp. De naam mag ook niet beginnen met de volgende gereserveerde woorden:<ul><li>Microsoft</li><li>EventGrid</li><li>Systeem</li> | Kies een andere domein naam die voldoet aan de vereisten voor de domein naam. |
| Http status code. onjuiste aanvraag<br/>400 | Ongeldige verval tijd. | De verloop tijd voor het gebeurtenis abonnement bepaalt wanneer het gebeurtenis abonnement buiten gebruik wordt gesteld. Deze waarde moet in de toekomst een geldige datum/tijd-waarde zijn.| Zorg ervoor dat de verloop tijd van het gebeurtenis abonnement in een geldige datum-en tijd notatie is ingesteld op de toekomst. |

## <a name="error-code-409"></a>Fout code: 409
| Foutcode | Foutbericht | Beschrijving | Aanbevolen actie |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. conflict <br/>409 | Er bestaat al een onderwerp met de opgegeven naam. Kies een andere naam voor het onderwerp.   | De naam van het aangepaste onderwerp moet uniek zijn in één Azure-regio om een juiste publicatie bewerking te kunnen garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het onderwerp. |
| Http status code. conflict <br/> 409 | Het domein met de opgegeven bestaat al. Kies een andere domein naam. | De domein naam moet uniek zijn in één Azure-regio om een juiste publicatie bewerking te kunnen garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het domein. |
| Http status code. conflict<br/>409 | De quotum limiet is bereikt. Zie [Azure Event grid limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)voor meer informatie over deze limieten.  | Elk Azure-abonnement heeft een limiet voor het aantal Azure Event Grid resources dat kan worden gebruikt. Sommige of alle van deze quota zijn overschreden en er kunnen geen resources meer worden gemaakt. |    Controleer het gebruik van uw huidige resources en verwijder deze die niet nodig zijn. Als u nog steeds uw quotum wilt verhogen, stuurt u een e-mail [aeg@microsoft.com](mailto:aeg@microsoft.com) naar het exacte aantal benodigde resources. |


## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
