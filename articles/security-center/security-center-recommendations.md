---
title: Aanbevelingen voor beveiliging in Azure Security Center
description: In dit document u zien hoe aanbevelingen in Azure Security Center u helpen uw Azure-bronnen te beschermen en in overeenstemming te blijven met het beveiligingsbeleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245302"
---
# <a name="security-recommendations-in-azure-security-center"></a>Aanbevelingen voor beveiliging in Azure Security Center 
In dit onderwerp wordt uitgelegd hoe u de aanbevelingen in Azure Security Center weergeven en begrijpen om u te helpen uw Azure-bronnen te beschermen.

> [!NOTE]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit document is geen stapsgewijze handleiding.
>

## <a name="what-are-security-recommendations"></a>Wat zijn beveiligingsaanbevelingen?

Aanbevelingen zijn acties die u moet ondernemen om uw resources te beveiligen.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-bronnen om potentiële beveiligingsproblemen te identificeren. Het geeft u vervolgens aanbevelingen over hoe ze te verwijderen.

Elke aanbeveling geeft u de informatie:

- Een korte beschrijving van wat wordt aanbevolen.
- De saneringsstappen moeten worden uitgevoerd om de aanbeveling uit te voeren. <!-- In some cases, Quick Fix remediation is available. -->
- Welke resources hebben behoefte aan het uitvoeren van de aanbevolen actie op hen.
- De **impact van de Secure Score**, dat is het bedrag dat uw Secure Score zal stijgen als u deze aanbeveling implementeert.

## <a name="monitor-recommendations"></a>Aanbevelingen controleren<a name="monitor-recommendations"></a>

Security Center analyseert de beveiligingsstatus van uw resources om potentiële kwetsbaarheden te identificeren. De tegel **Aanbevelingen** onder **Overzicht** toont het totale aantal aanbevelingen dat door Security Center is geïdentificeerd.

![Overzicht van beveiligingscentrum](./media/security-center-recommendations/asc-overview.png)

1. Selecteer de **tegel Aanbevelingen** onder **Overzicht**. De lijst **Aanbevelingen** wordt geopend.

      ![Aanbevelingen bekijken](./media/security-center-recommendations/view-recommendations.png)

    U aanbevelingen filteren. Als u de aanbevelingen wilt filteren, selecteert u **Filter** op het blad **Aanbevelingen.** Het **filterblad** wordt geopend en u selecteert de ernst en statuswaarden die u wilt zien.

   * **AANBEVELINGEN**: De aanbeveling.
   * **SECURE SCORE IMPACT**: Een score gegenereerd door Security Center met behulp van uw beveiligingsaanbevelingen, en het toepassen van geavanceerde algoritmen om te bepalen hoe cruciaal elke aanbeveling is. Zie [Berekening van de beveiligde score voor](security-center-secure-score.md#secure-score-calculation)meer informatie .
   * **RESOURCE**: geeft een overzicht van de bronnen waarop deze aanbeveling van toepassing is.
   * **STATUSBALKEN**: Beschrijft de ernst van die specifieke aanbeveling:
       * **Hoog (rood):** er bestaat een beveiligingslek met een zinvolle bron (zoals een toepassing, een vm of een netwerkbeveiligingsgroep) en vereist aandacht.
       * **Medium (Oranje)**: Er bestaat een kwetsbaarheid en er zijn niet-kritieke of aanvullende stappen nodig om het te elimineren of een proces te voltooien.
       * **Laag (Blauw)**: Er bestaat een kwetsbaarheid die moet worden aangepakt, maar geen onmiddellijke aandacht vereist. (Standaard worden lage aanbevelingen niet weergegeven, maar u filteren op lage aanbevelingen als u ze wilt zien.) 
       * **Gezond (Groen)**:
       * **Niet beschikbaar (grijs)**:

1. Als u de details van elke aanbeveling wilt bekijken, klikt u op de aanbeveling.

    ![Aanbevelingsdetails](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Bekijk [klassieke en Resource Manager-implementatiemodellen](../azure-classic-rm.md) voor Azure-resources.
 
## <a name="next-steps"></a>Volgende stappen

In dit document maakte u kennis met beveiligingsaanbevelingen in Security Center. Ga als volgt te werk om te leren hoe u de aanbevelingen herstellen:

* [Aanbevelingen voor herstel:](security-center-remediate-recommendations.md) meer informatie over het configureren van beveiligingsbeleid voor uw Azure-abonnementen en resourcegroepen.
