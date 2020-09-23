---
title: Het hoofd dashboard of de pagina overzicht van Azure Security Center
description: Meer informatie over de functies van de pagina overzicht van Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934992"
---
# <a name="azure-security-centers-overview-page"></a>Overzichts pagina van Azure Security Center

Wanneer u Azure Security Center opent, ziet u de eerste pagina die wordt weer gegeven op de pagina overzicht. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Overzichtspagina van Security Cente":::

Ontdek en evalueer de beveiliging van uw workloads, en Identificeer en beperk Risico's, met behulp van de overzichts pagina van Security Center.

Het overzicht biedt een uniforme weer gave van de beveiligings postuur van uw hybride Cloud werkbelastingen. Daarnaast worden er beveiligings waarschuwingen, informatie over de dekking en meer weer gegeven.


## <a name="features-of-the-overview-page"></a>Functies van de pagina overzicht

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="De bovenste balk van de Security Center pagina overzicht":::

De **bovenste menu balk** biedt:
- **Abonnementen** : u kunt de lijst met abonnementen weer geven en filteren door deze knop te selecteren. Security Center past de weergave aan om het beveiligingspostuur van de geselecteerde abonnementen weer te geven.
- **Wat is er nieuw** : Hiermee opent u de [release opmerkingen](release-notes.md) zodat u op de hoogte kunt blijven van de nieuwe functies, fout oplossingen en afgeschafte functionaliteit.
- **Nummers op hoog niveau** voor de verbonden Cloud accounts, om de context van de informatie in de onderstaande hoofd tegels weer te geven. Evenals het aantal actieve aanbevelingen en waarschuwingen.
    Meer informatie over het verbinden van uw [AWS-accounts](quickstart-onboard-aws.md) en uw [GCP-projecten](quickstart-onboard-gcp.md).


In het midden van de pagina staan **vier centrale tegels**die zijn gekoppeld aan een speciaal dash board voor meer informatie:
- **Beveiligde Score** -Security Center worden uw resources, abonnementen en organisaties doorlopend beoordeeld op beveiligings problemen. Vervolgens worden alle bevindingen in één enkele score geaggregeerd, zodat u in een oogopslag uw huidige beveiligings situatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risico niveau is. [Meer informatie](secure-score-security-controls.md).
- **Naleving** -Security Center biedt inzicht in uw nalevings postuur op basis van doorlopende evaluaties van uw Azure-omgeving. Security Center analyseert risicofactoren in uw hybride cloudomgeving overeenkomstig best practices voor beveiliging. Deze evaluaties worden toegewezen aan besturings elementen voor naleving vanuit een ondersteunde set normen. [Meer informatie](security-center-compliance-dashboard.md).
- **Azure Defender** : dit is het Cloud werkbelasting platform (CWPP) geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure-en hybride werk belastingen. De tegel toont de dekking van uw verbonden resources (voor de momenteel geselecteerde abonnementen) en de recente waarschuwingen, met een kleur code op Ernst. [Meer informatie](azure-defender.md).
- **Inventaris** : de tegel toont het aantal niet-bewaakte vm's en een eenvoudige barometer van uw resources die worden bewaakt door Security CenterBen. [Meer informatie](asset-inventory.md).


Het deel venster **inzichten** biedt aangepaste items voor uw omgeving, waaronder:
- Uw meest aangevallen resources
- Uw beveiligings mechanismen met het hoogste potentieel om uw beveiligde score te verhogen
- De actieve aanbevelingen met de meeste resources die worden beïnvloed
- Recente blog berichten van Azure Security Center experts

## <a name="next-steps"></a>Volgende stappen

Op deze pagina is de pagina overzicht van Security Center geïntroduceerd. Zie voor verwante informatie:

- [Uw resources verkennen en beheren met Asset Inventory and management tools](asset-inventory.md)
- [Beveiligde Score in Azure Security Center](secure-score-security-controls.md)