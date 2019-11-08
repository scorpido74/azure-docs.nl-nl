---
title: Bekijk de status van Marketplace-aanbiedingen | Azure Marketplace
description: Bekijk de status van aanbiedingen op de Azure-en AppSource-markt plaatsen met behulp van de Cloud Partner-portal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826705"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>De publicatie status van Azure Marketplace-en AppSource-aanbiedingen bekijken

Nadat u een aanbieding hebt gemaakt en vooral tijdens het publicatie proces, kunt u de status van uw aanbieding bekijken in de Cloud Partner-portal.  De algemene publicatie status is beschikbaar op de pagina's [**alle aanbiedingen**](../portal-tour/cpp-all-offers-page.md) en [**goed keuringen**](../portal-tour/cpp-approvals-page.md) van de portal.  Voor elke aanbieding moet een van de volgende status indicatoren worden weer gegeven.  

|            Status              |   Beschrijving                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Het aanbod is gemaakt, maar het publicatie proces is niet gestart.            |
| **Publicatie wordt uitgevoerd**        | De aanbieding werkt aan de hand van de stappen van het publicatie proces.   |
| **Publiceren is mislukt**             | Er is een kritiek probleem ontdekt tijdens de validatie of door micro soft te controleren. |
| **Publiceren geannuleerd**           | De uitgever heeft het publicatie proces voor de aanbieding geannuleerd.  Met deze status wordt de opsomming van een bestaande aanbieding op Marketplace niet opgedaan. | 
| **Afmelden bij Uitgever** | Het aanbod is door micro soft gecontroleerd en wacht nu op een definitieve verificatie door de uitgever. |
| **Niet vermeld**                   | Een eerder gepubliceerde aanbieding in Marketplace is verwijderd.      | 
|  |  |


## <a name="publishing-status-details"></a>Status Details van publicatie 

Meer informatie over de status van een aanbieding tijdens het publicatie proces vindt u op het tabblad **status** van de pagina **nieuw aanbod** .  Op deze pagina vindt u alle publicatie stappen voor het type aanbieding.  *Houd er rekening mee dat het aantal en de specifieke stappen vaak verschillen tussen aanbiedings typen.*  Deze pagina geeft ook een overzicht van openstaande problemen die zijn opgetreden tijdens de validatie en controle stappen van micro soft, die vaak actie vereisen door de uitgever voordat het publicatie proces kan door gaan.  De volgende afbeelding toont bijvoorbeeld het tabblad **status** voor een nieuwe aanbieding van de virtuele machine. 

![Tabblad status voor de VM-aanbieding](./media/vm-offer-pub-steps1.png)

Het volgende voor beeld van het tabblad **status** voor een advies service, met een gemelde fout in de instellingen voor lead beheer.  Omdat Lead beheer is vereist voor advies Services, moet deze fout worden gecorrigeerd voordat de publicatie kan worden voortgezet.

![Tabblad status voor advies service met fout](./media/consulting-service-error.png)

De laatste voorbeeld status van een Azure-toepassing toont een kritiek micro soft Review-probleem.  Het bevat een koppeling naar het Azure DevOps-item dat gedetailleerde informatie over dit controle probleem bevat.  Zie voor meer informatie [Azure-toepassings aanbieding publiceren](cpp-publish-offer.md).

![Tabblad status voor de Azure-app met een beoordelings probleem](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Volgende stappen

Als u uitstaande problemen of instellingen voor het update aanbod wilt corrigeren, moet u [een aanbieding bijwerken](./cpp-update-offer.md). 
