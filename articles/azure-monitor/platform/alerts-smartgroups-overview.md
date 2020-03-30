---
title: Slimme groepen
description: Slimme groepen zijn aggregaties van waarschuwingen die u helpen alerte ruis te verminderen
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665490"
---
# <a name="smart-groups"></a>Slimme groepen

Een gemeenschappelijke uitdaging voor de behandeling van waarschuwingen is zeven door het lawaai om uit te vinden wat er werkelijk toe doet - slimme groepen zijn bedoeld om de oplossing voor dat probleem.  

Slimme groepen worden automatisch gemaakt met behulp van machine learning-algoritmen om gerelateerde waarschuwingen te combineren die één probleem vertegenwoordigen.  Wanneer een waarschuwing wordt gemaakt, voegt het algoritme deze toe aan een nieuwe slimme groep of een bestaande slimme groep op basis van informatie zoals historische patronen, vergelijkbare eigenschappen en vergelijkbare structuur. Als bijvoorbeeld % CPU op meerdere virtuele machines in een abonnement tegelijkertijd pieken leidt tot veel individuele waarschuwingen, en als dergelijke waarschuwingen op elk moment in het verleden samen hebben plaatsgevonden, zullen deze waarschuwingen waarschijnlijk worden gegroepeerd in één smartgroep, wat suggereert dat een mogelijke gemeenschappelijke oorzaak. Dit betekent dat voor iemand die waarschuwingen probleemoplost, slimme groepen hen niet alleen in staat stellen om ruis te verminderen door gerelateerde waarschuwingen als één geaggregeerde eenheid te beheren, maar ook hen naar mogelijke gemeenschappelijke onderliggende oorzaken voor hun waarschuwingen leidt.

Momenteel houdt het algoritme alleen rekening met waarschuwingen van dezelfde monitorservice binnen een abonnement. Slimme groepen kunnen tot 99% van de waarschuwingsruis verminderen door deze consolidatie. U de reden bekijken waarom waarschuwingen in een groep zijn opgenomen op de pagina met gegevens voor slimme groepen.

U de details van slimme groepen bekijken en de status op dezelfde manier instellen als met waarschuwingen. Elke waarschuwing is lid van één en slechts één slimme groep. 

## <a name="smart-group-state"></a>Slimme groepsstatus

Slimme groepsstatus is een vergelijkbaar concept als de waarschuwingsstatus, waarmee u het oplossingsproces beheren op het niveau van een slimme groep. Vergelijkbaar met de waarschuwingsstatus, wanneer een slimme groep wordt gemaakt, heeft deze de **status Nieuw,** die kan worden gewijzigd in **Erkend** of **Gesloten**.

De volgende slimme groepsstaten worden ondersteund.

| Status | Beschrijving |
|:---|:---|
| Nieuw | Het probleem is net ontdekt en is nog niet beoordeeld. |
| Bevestigd | Een beheerder heeft de slimme groep beoordeeld en is ermee aan de slag gegaan. |
| Gesloten | Het probleem is opgelost. Nadat een slimme groep is gesloten, u deze opnieuw openen door deze te wijzigen in een andere status. |

[Meer informatie over het wijzigen van de status van uw slimme groep.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Als u de status van een slimme groep wijzigt, verandert de status van de waarschuwingen van het afzonderlijke lid niet.

## <a name="smart-group-details-page"></a>Pagina Slimme groepsdetails

De pagina Slimme groepsdetails wordt weergegeven wanneer u een slimme groep selecteert. Het geeft details over de slimme groep, inclusief de redenering die is gebruikt om de groep te maken, en stelt u in staat om de status ervan te wijzigen.
 
![Slimme groepsdetails](media/alerts-smartgroups-overview/smart-group-detail.png)


De pagina met slimme groepsdetails bevat de volgende secties.

| Sectie | Beschrijving |
|:---|:---|
| Waarschuwingen | Geeft een overzicht van de afzonderlijke waarschuwingen die zijn opgenomen in de slimme groep. Selecteer een waarschuwing om de pagina met waarschuwingsdetails te openen. |
| Geschiedenis | Geeft een overzicht van elke actie die door de slimme groep wordt uitgevoerd en eventuele wijzigingen die erin worden aangebracht. Dit is momenteel beperkt tot wijzigingen in de status en waarschuwing lidmaatschap wijzigingen. |

## <a name="smart-group-taxonomy"></a>Slimme groepstaxonomie

De naam van een slimme groep is de naam van de eerste waarschuwing. U een slimme groep niet maken of de naam wijzigen.

## <a name="next-steps"></a>Volgende stappen

- [Slimme groepen beheren](https://aka.ms/managing-smart-groups)
- [De status van uw waarschuwing en slimme groep wijzigen](https://aka.ms/managing-alert-smart-group-states)


