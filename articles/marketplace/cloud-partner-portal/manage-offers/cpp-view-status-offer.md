---
title: Bekijk de status van marktplaatsaanbiedingen | Azure Marketplace
description: Bekijk de status van aanbiedingen op de Azure- en AppSource Marketplaces met behulp van de Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275965"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Bekijk de publicatiestatus van Azure Marketplace- en AppSource-aanbiedingen

Nadat u een aanbieding hebt gemaakt, en vooral tijdens het publicatieproces, u de status van uw aanbieding bekijken in de Cloud Partner Portal.  De algemene publicatiestatus is beschikbaar op de [**pagina's Alle aanbiedingen**](../portal-tour/cpp-all-offers-page.md) en [**goedkeuringen**](../portal-tour/cpp-approvals-page.md) van de portal.  Voor elke aanbieding moet een van de volgende statusindicatoren worden weergegeven.  

|            Status              |   Beschrijving                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Aanbieding is gemaakt, maar het publicatieproces is nog niet begonnen.            |
| **Publiceren in uitvoering**        | Offer werkt zich een weg door de stappen van het publicatieproces.   |
| **Publiceren is mislukt**             | Er is een kritiek probleem ontdekt tijdens validatie of beoordeling door Microsoft. |
| **Publicatie geannuleerd**           | De uitgever heeft het publicatieproces van de aanbieding geannuleerd.  Deze status onttrekt een bestaand aanbod niet in de markt. | 
| **In afwachting van publisher afmelden** | Aanbieding is beoordeeld door Microsoft en wacht nu op een definitieve verificatie door de uitgever. |
| **Geschrapt**                   | Een eerder gepubliceerde aanbieding op de marktplaats is verwijderd.      | 
|  |  |


## <a name="publishing-status-details"></a>Statusgegevens publiceren 

Meer details over de status van een aanbieding tijdens het publicatieproces vindt u op het tabblad **Status** van de pagina **Nieuwe aanbieding.**  Op deze pagina worden alle publicatiestappen voor dat aanbiedingstype weergegeven.  *Houd er rekening mee dat het aantal en de specifieke stappen vaak verschillen tussen aanbiedingstypen.*  Op deze pagina worden ook openstaande problemen weergegeven die zijn opgeworpen door de validatie- en beoordelingsstappen van Microsoft, waarvoor vaak actie van de uitgever vereist is voordat het publicatieproces kan worden voortgezet.  In de volgende afbeelding wordt bijvoorbeeld het tabblad **Status** voor een nieuwe virtuele machineaanbieding weergegeven. 

![Tabblad Status voor VM-aanbieding](./media/vm-offer-pub-steps1.png)

Het volgende voorbeeld **statustabblad** voor een consultingservice, met een gerapporteerde fout in de instellingen voor leadbeheer.  Omdat leadbeheer vereist is voor consultingservices, moet deze fout worden gecorrigeerd voordat publicatie kan worden voortgezet.

![Tabblad Status voor consultingservice die fout weergeeft](./media/consulting-service-error.png)

De laatste voorbeeldstatus van een Azure-toepassing toont een kritiek microsoft-beoordelingsprobleem.  Het bevat een hot link naar het Azure DevOps-item dat gedetailleerde informatie over dit beoordelingsprobleem bevat.  Zie [Azure-toepassingsaanbieding publiceren](cpp-publish-offer.md)voor meer informatie .

![Tabblad Status voor Azure-app met beoordelingsprobleem](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Volgende stappen

Als u openstaande problemen wilt oplossen of de aanbiedingsinstellingen wilt bijwerken, moet u [een aanbieding bijwerken.](./cpp-update-offer.md) 
