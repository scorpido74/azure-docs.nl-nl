---
title: Aanbevelingen voor beveiliging in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe aanbevelingen in Azure Security Center u helpen uw Azure-resources te beveiligen en te blijven voldoen aan het beveiligings beleid.
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
ms.openlocfilehash: 32b7f1d699c0d620d70614c441a8c18520c1b2d5
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201060"
---
# <a name="security-recommendations-in-azure-security-center"></a>Aanbevelingen voor beveiliging in Azure Security Center 
In dit onderwerp wordt uitgelegd hoe u de aanbevelingen in Azure Security Center kunt bekijken en begrijpen om u te helpen uw Azure-resources te beveiligen.

> [!NOTE]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit document is geen stapsgewijze hand leiding.
>

## <a name="what-are-security-recommendations"></a>Wat zijn beveiligings aanbevelingen?

Aanbevelingen zijn acties die u kunt uitvoeren om uw resources te beveiligen.

Security Center regel matig de beveiligings status van uw Azure-resources analyseren om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt verwijderen.

Elke aanbeveling biedt u het volgende:

- Een korte beschrijving van wat wordt aanbevolen.
- De herstels tappen die u moet uitvoeren om de aanbeveling te implementeren. <!-- In some cases, one-click remediation is available. -->
- Aan welke resources moet u de aanbevolen actie uitvoeren.
- De **impact van de beveiligde Score**, de mate waarin uw beveiligde Score meegaat als u deze aanbeveling implementeert.

## Aanbevelingen controleren<a name="monitor-recommendations"></a>

Security Center analyseert de beveiligings status van uw resources om mogelijke beveiligings problemen te identificeren. De tegel **aanbevelingen** onder **overzicht** toont het totale aantal aanbevelingen dat is geïdentificeerd door Security Center.

![Overzicht van Security Center](./media/security-center-recommendations/asc-overview.png)

1. Selecteer de **tegel aanbevelingen** onder **overzicht**. De lijst met **aanbevelingen** wordt geopend.

      ![Aanbevelingen weergeven](./media/security-center-recommendations/view-recommendations.png)

    U kunt de aanbevelingen filteren. Als u de aanbevelingen wilt filteren, selecteert u **filter** op de Blade **aanbevelingen** . De Blade **filter** wordt geopend en u selecteert de ernst en de status waarden die u wilt zien.

   * **AANBEVELINGEN**: De aanbeveling.
   * **EFFECT OP BEVEILIGDE SCORE**: Een score die wordt gegenereerd door Security Center met behulp van uw aanbevelingen voor beveiliging en het Toep assen van geavanceerde algoritmen om te bepalen hoe belang rijk elke aanbeveling is. Zie [Secure Score Calculation](security-center-secure-score.md#secure-score-calculation)(Engelstalig) voor meer informatie.
   * **RESOURCE**: Hier worden de resources vermeld waarop deze aanbeveling van toepassing is.
   * **STATUS BALKEN**:  Beschrijft de ernst van die specifieke aanbeveling:
       * **Hoog (rood)** : Er bestaat een beveiligings probleem met een zinvolle bron (zoals een toepassing, een virtuele machine of een netwerk beveiligings groep) en er is aandacht vereist.
       * **Middel groot (oranje)** : Er bestaat een beveiligingslek en er zijn niet-kritieke of extra stappen vereist om het te elimineren of om een proces te volt ooien.
       * **Laag (blauw)** : Er is een beveiligings probleem dat moet worden verholpen, maar dit vereist geen onmiddellijke aandacht. (Standaard worden er geen minimale aanbevelingen gepresenteerd, maar u kunt filteren op lage aanbevelingen als u deze wilt zien.) 
       * **In orde (groen)** :
       * **Niet beschikbaar (grijs)** :

1. Klik op de aanbeveling om de details van elke aanbeveling weer te geven.

    ![Details van de aanbeveling](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Zie [klassieke en Resource Manager-implementatie modellen](../azure-classic-rm.md) voor Azure-resources.
 
## <a name="next-steps"></a>Volgende stappen

In dit document hebt u kennis gemaakt met beveiligings aanbevelingen in Security Center. Voor meer informatie over het oplossen van de aanbevelingen:

* [Aanbevelingen herstellen](security-center-remediate-recommendations.md) : informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en resource groepen.
