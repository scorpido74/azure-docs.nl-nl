---
title: Azure Event Grid-probleemoplossings gids
description: Dit artikel bevat een lijst met fout codes, fout berichten, beschrijvingen en aanbevolen acties.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 3b09b431e827bed4e416913c88d23ee1eddaf17c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629011"
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

## <a name="troubleshoot-event-subscription-validation"></a>Problemen met validatie van gebeurtenis abonnementen oplossen

Bij het maken van een gebeurtenis abonnement, als er een fout bericht wordt `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`weer gegeven, geeft dit aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen door de volgende aspecten te controleren:

- Voer een HTTP POST naar uw webhook-URL uit met een voor beeld van een [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) -aanvraag tekst met behulp van Postman of krul of een soortgelijk hulp programma.
- Als uw webhook het handshake-mechanisme voor synchrone validatie implementeert, controleert u of de ValidationCode als onderdeel van het antwoord wordt geretourneerd.
- Als uw webhook een asynchroon validatie-handshake-mechanisme implementeert, controleert u of u het HTTP POST-bericht 200 OK retourneert.
- Als uw webhook 403 (verboden) in het antwoord retourneert, controleert u of uw webhook zich achter een Azure-toepassing gateway of Web Application firewall bevindt. Als dat het geval is, moet u deze firewall regels uitschakelen en opnieuw een HTTP-POST doen:

  920300 (aanvraag mist een Accept-header, we kunnen dit oplossen)

  942430 (beperkte SQL-teken anomalie detectie (argumenten): aantal speciale tekens is overschreden (12))

  920230 (meerdere URL-code ring gedetecteerd)

  942130 (SQL-injectie aanval: SQL-Tautology gedetecteerd.)

  931130 (mogelijke RFI-aanval (Remote File Include) = off-domein verwijzing/koppeling)


## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
