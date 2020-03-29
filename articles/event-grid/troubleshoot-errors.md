---
title: Azure-gebeurtenisraster - handleiding voor probleemoplossing
description: In dit artikel vindt u een lijst met foutcodes, foutberichten, beschrijvingen en aanbevolen acties.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645069"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemen met Azure Event Grid-fouten oplossen
Deze handleiding voor probleemoplossing biedt u een lijst met foutcodes van Azure Event Grid, foutberichten, beschrijvingen en aanbevolen acties die u moet uitvoeren wanneer u deze fouten ontvangt. 

## <a name="error-code-400"></a>Foutcode 400
| Foutcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ----------- | -------------- | 
| HttpstatusCode.BadRequest<br/>400 | De naam van het onderwerp moet tussen de 3 en 50 tekens lang zijn. | De aangepaste lengte van de onderwerpnaam moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het '-' teken zijn toegestaan in de onderwerpnaam. De naam mag ook niet beginnen met de volgende gereserveerde woorden: <ul><li>Microsoft</li><li>EventGrid (EventGrid)</li><li>Systeem</li></ul> | Kies een andere onderwerpnaam die voldoet aan de vereisten voor de onderwerpnaam. |
| HttpstatusCode.BadRequest<br/>400 | De domeinnaam moet tussen de 3 en 50 tekens lang zijn. | De lengte van de domeinnaam moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het '-' teken zijn toegestaan in de onderwerpnaam. De naam mag ook niet beginnen met de volgende gereserveerde woorden:<ul><li>Microsoft</li><li>EventGrid (EventGrid)</li><li>Systeem</li> | Kies een andere domeinnaam die voldoet aan de vereisten voor domeinnamen. |
| HttpstatusCode.BadRequest<br/>400 | Ongeldige vervaldatum. | De vervaldatum voor het gebeurtenisabonnement bepaalt wanneer het gebeurtenisabonnement wordt ingegaan. Deze waarde moet in de toekomst een geldige Datumtijdwaarde zijn.| Controleer of de vervaldatum van het gebeurtenisabonnement in een geldige Datumtijd-indeling is en dat deze in de toekomst zal zijn. |

## <a name="error-code-409"></a>Foutcode: 409
| Foutcode | Foutbericht | Beschrijving | Aanbevolen actie |
| ---------- | ------------- | ----------- | -------------- | 
| HttpstatusCode.Conflict <br/>409 | Het onderwerp met de gespecificeerde naam bestaat reeds. Kies een andere onderwerpnaam.   | De aangepaste onderwerpnaam moet uniek zijn in één Azure-gebied om een correcte publicatiebewerking te garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het onderwerp. |
| HttpstatusCode.Conflict <br/> 409 | Domein met het opgegeven bestaat al. Kies een andere domeinnaam. | De domeinnaam moet uniek zijn in één Azure-gebied om een correcte publicatiebewerking te garanderen. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het domein. |
| HttpstatusCode.Conflict<br/>409 | Quotumlimiet bereikt. Zie [Azure Event Grid-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)voor meer informatie over deze limieten.  | Elk Azure-abonnement heeft een limiet voor het aantal Azure Event Grid-resources dat het kan gebruiken. Een deel of het geheel van dit quotum was overschreden en er konden geen middelen meer worden gecreëerd. |    Controleer uw huidige bronnengebruik en verwijder alle resources die niet nodig zijn. Als u uw quotum nog moet verhogen, stuurt u een e-mail naar [aeg@microsoft.com](mailto:aeg@microsoft.com) het exacte aantal benodigde resources. |


## <a name="next-steps"></a>Volgende stappen
Als je meer hulp nodig hebt, plaats je je probleem op het [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) of open je een [ondersteuningsticket.](https://azure.microsoft.com/support/options/) 
