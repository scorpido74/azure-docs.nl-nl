---
title: Het hoofddashboard of de overzichtspagina van Azure Security Center
description: Meer informatie over de functies van de overzichtspagina van Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cc52610eacc3916b7a8978cba17a1db3f3d50686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91447675"
---
# <a name="azure-security-centers-overview-page"></a>Overzichtspagina van Azure Security Center

Wanneer u Azure Security Center opent, wordt als eerste de overzichtspagina weergegeven. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Overzichtspagina van Security Cente":::

Ontdek en beoordeel de beveiliging van uw workloads en identificeer en beperk risico's, alles met behulp van de overzichtspagina van Security Center.

Het overzicht biedt een uniforme weergave van het beveiligingspostuur van uw hybride cloudworkloads. Bovendien worden er naast andere informatie beveiligingswaarschuwingen en informatie over de dekking weergegeven.


## <a name="features-of-the-overview-page"></a>Functies van de overzichtspagina

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Overzichtspagina van Security Cente":::

De **bovenste menubalk** biedt het volgende:
- **Abonnementen**: u kunt de lijst met abonnementen bekijken en filteren door deze knop te selecteren. Security Center past de weergave aan om het beveiligingspostuur van de geselecteerde abonnementen weer te geven.
- **Wat is er nieuw**: hiermee opent u de [opmerkingen bij de release](release-notes.md) zodat u op de hoogte blijft van nieuwe functies, foutoplossingen en afgeschafte functionaliteit.
- **Nummers op hoog niveau** voor de verbonden cloudaccounts, om de context van de informatie in de onderstaande hoofdtegels weer te geven. Ook wordt het aantal actieve aanbevelingen en waarschuwingen vermeld.
    Meer informatie over het verbinden van uw [AWS-accounts](quickstart-onboard-aws.md) en uw [GCP-projecten](quickstart-onboard-gcp.md).


Midden op de pagina bevinden zich **vier centrale tegels**, die elk gekoppeld zijn aan een speciaal dashboard voor meer informatie:
- **Veiligheidsscore**: het Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is. [Meer informatie](secure-score-security-controls.md).
- **Naleving**: Security Center biedt inzicht in uw nalevingspostuur op basis van continue evaluatie van uw Azure-omgeving. Security Center analyseert risicofactoren in uw hybride cloudomgeving overeenkomstig best practices voor beveiliging. Deze evaluaties worden vanuit een ondersteunde set standaarden toegewezen aan nalevingscontroles.[Meer informatie](security-center-compliance-dashboard.md).
- **Azure Defender**: dit is het Cloud Workload Protection Platform (CWPP) dat geïntegreerd is in Security Center voor geavanceerde, intelligente beveiliging van uw Azure- en hybride workloads. De tegel toont de dekking van uw verbonden resources (voor de momenteel geselecteerde abonnementen) en de recente waarschuwingen, met een kleurcode voor de mate van ernst. [Meer informatie](azure-defender.md).
- **Voorraad**: de tegel toont het aantal niet-bewaakte VM's en een eenvoudige barometer van uw resources die worden bewaakt door Security Center. [Meer informatie](asset-inventory.md).


Het deelvenster **Inzichten** biedt aangepaste items voor uw omgeving, waaronder:
- Uw resources die het vaakst zijn aangevallen
- Uw beveiligingsmechanismen met de grootste kans om uw beveiligingsscore te verhogen
- De actieve aanbevelingen met de resources die het meest worden beïnvloed
- Recente blogposts van Azure Security Center-experts

## <a name="next-steps"></a>Volgende stappen

Op deze pagina maakt u kennis met de overzichtspagina van Security Center. Zie voor verwante informatie:

- [Verken en beheer uw resources met hulpprogramma's voor assetvoorraad en -beheer](asset-inventory.md)
- [Beveiligingsscore in Azure Security Center](secure-score-security-controls.md)