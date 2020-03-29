---
title: De status van Azure CDN-resources controleren| Microsoft Documenten
description: Meer informatie over het bewaken van de status van uw Azure CDN-resources met Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593441"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>De status van Azure CDN-resources bewaken
  
Azure CDN-bronstatus is een subset van [azure-bronstatus](../resource-health/resource-health-overview.md).  U azure-bronstatus gebruiken om de status van CDN-resources te controleren en bruikbare richtlijnen te ontvangen om problemen op te lossen.

>[!IMPORTANT] 
>Azure CDN-bronstatus is momenteel alleen verantwoordelijk voor de status van wereldwijde CDN-leverings- en API-mogelijkheden.  Azure CDN-bronstatus controleert geen afzonderlijke CDN-eindpunten.
>
>De signalen die de status van Azure CDN-bronnen voeden, kunnen tot 15 minuten vertraging oplopen.

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN-bronstatus zoeken

1. Blader in de [Azure-portal](https://portal.azure.com)naar uw CDN-profiel.

2. Klik op de knop **Instellingen.**

    ![Knop Instellingen](./media/cdn-resource-health/cdn-profile-settings.png)

3. Klik *onder Ondersteuning + probleemoplossing*op **Resourcestatus**.

    ![CDN-resourcestatus](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>U cdn-bronnen ook vinden die worden vermeld in de tegel *Resourcestatus* in het *ondersteuningsblad Help + ondersteuning.*  U snel naar *Help + ondersteuning* door te klikken op de omcirkelde? **?** in de rechterbovenhoek van het portaal.
>
> ![Help en ondersteuning](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifieke berichten

Statussen met betrekking tot Azure CDN-bronstatus vindt u hieronder.

|Bericht | Aanbevolen actie |
|---|---|
|Een of meer CDN-eindpunten zijn mogelijk gestopt, verwijderd of verkeerd geconfigureerd | Een of meer CDN-eindpunten zijn mogelijk gestopt, verwijderd of verkeerd geconfigureerd.|
|De CDN-beheerservice is momenteel niet beschikbaar | Kijk hier terug voor statusupdates; Als het probleem blijft bestaan na de verwachte oplossingstijd, neemt u contact op met de ondersteuning.|
|Uw CDN-eindpunten worden mogelijk getroffen door lopende problemen met een aantal van onze CDN-providers | Kijk hier terug voor statusupdates; Gebruik het hulpprogramma Problemen gebruiken om te leren hoe u uw oorsprong en CDN-eindpunt testen; Als het probleem blijft bestaan na de verwachte oplossingstijd, neemt u contact op met de ondersteuning. |
|De wijzigingen in de configuratie van uw CDN-eindpunten ondervinden helaas vertragingen bij de doorgifte | Kijk hier terug voor statusupdates; Neem contact op met de ondersteuning als uw configuratiewijzigingen niet volledig zijn doorgevoerd.|
|Er zijn momenteel problemen met het laden van de aanvullende portal | Kijk hier terug voor statusupdates; Als het probleem blijft bestaan na de verwachte oplossingstijd, neemt u contact op met de ondersteuning.|
Er zijn momenteel problemen met een aantal van onze CDN-providers | Kijk hier terug voor statusupdates; Als het probleem blijft bestaan na de verwachte oplossingstijd, neemt u contact op met de ondersteuning. |

## <a name="next-steps"></a>Volgende stappen

- [Een overzicht van azure-bronstatus lezen](../resource-health/resource-health-overview.md)
- [Problemen met CDN-compressie oplossen](./cdn-troubleshoot-compression.md)
- [Problemen met 404 fouten oplossen](./cdn-troubleshoot-endpoint.md)