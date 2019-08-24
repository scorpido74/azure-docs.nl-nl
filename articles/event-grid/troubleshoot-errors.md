---
title: Azure Event Grid-probleemoplossings gids
description: Dit artikel bevat een lijst met fout codes, fout berichten, beschrijvingen en aanbevolen acties.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 84a227d832c45bdce6f16578b9c52edbc171a5f8
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984496"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemen met Azure Event Grid oplossen
Deze hand leiding bevat een lijst met Azure Event Grid fout codes, fout berichten, beschrijvingen en aanbevolen acties die u moet uitvoeren wanneer u deze fouten ontvangt. 

## <a name="error-code-409"></a>Fout code: 409
| Foutcode | Foutbericht | Description | Aanbevolen actie |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. conflict <br/>409 | Er bestaat al een {0} onderwerp met de naam. Kies een andere naam voor het onderwerp. | De naam van het aangepaste onderwerp moet uniek zijn in één Azure-regio om een juiste publicatie bewerking te kunnen garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het onderwerp. |
| Http status code. conflict <br/> 409 | Er bestaat al een {0} domein met de naam. Kies een andere domein naam. | De domein naam moet uniek zijn in één Azure-regio om een juiste publicatie bewerking te kunnen garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het domein. |
| Http status code. conflict<br/>409 | De quotum limiet is bereikt. Zie [Azure Event grid limieten](../azure-subscription-service-limits.md#event-grid-limits)voor meer informatie over deze limieten.  | Elk Azure-abonnement heeft een limiet voor het aantal Azure Event Grid resources dat kan worden gebruikt. Sommige of alle van deze quota zijn overschreden en er kunnen geen resources meer worden gemaakt. |  Controleer het gebruik van uw huidige resources en verwijder deze die niet nodig zijn. Als u nog steeds uw quotum wilt verhogen, stuurt u een e-mail [aeg@microsoft.com](mailto:aeg@microsoft.com) naar het exacte aantal benodigde resources. |

## <a name="error-code-400"></a>Fout code: 400
| Foutcode | Foutbericht | Description | Aanbeveling |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. onjuiste aanvraag<br/>400 | De naam van het onderwerp moet tussen de 3 en 50 tekens lang zijn. | De naam van het aangepaste onderwerp moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de naam van het onderwerp. De naam mag ook niet beginnen met de volgende gereserveerde woorden: <ul><li>Microsoft</li><li>EventGrid</li><li>Systeem</li></ul> | Kies een andere onderwerpnaam die voldoet aan de vereisten voor de onderwerpnaam. |
| Http status code. onjuiste aanvraag<br/>400 | De domein naam moet tussen de 3 en 50 tekens lang zijn. | De lengte van de domein naam moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de naam van het onderwerp. De naam mag ook niet beginnen met de volgende gereserveerde woorden:<ul><li>Microsoft</li><li>EventGrid</li><li>Systeem</li> | Kies een andere domein naam die voldoet aan de vereisten voor de domein naam. |
| Http status code. onjuiste aanvraag<br/>400 | Ongeldige verval tijd. | De verloop tijd voor het gebeurtenis abonnement bepaalt wanneer het gebeurtenis abonnement buiten gebruik wordt gesteld. Deze waarde moet in de toekomst een geldige datum/tijd-waarde zijn.| Zorg ervoor dat de verloop tijd van het gebeurtenis abonnement in een geldige datum-en tijd notatie is ingesteld op de toekomst. |

## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een ondersteunings [ticket](https://azure.microsoft.com/support/options/)openen. 
