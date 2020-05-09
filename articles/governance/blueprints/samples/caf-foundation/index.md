---
title: Overzicht van het CAF Foundation-blauwdrukvoorbeeld
description: Overzicht en architectuur van het Cloud Adoption Framework (CAF) voor Azure Foundation-blauwdrukvoorbeeld
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 3291fdc299652d2b22bff89f5b1dadbdc064e561
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901304"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Overzicht van Microsoft Cloud Adoption Framework voor het Azure Foundation-blauwdrukvoorbeeld

Met Microsoft CAF (Cloud Adoption Framework) voor de Azure Foundation-blauwdruk wordt een kernset met infrastructuurresources en besturingselementen voor beleid geïmplementeerd, bedoeld voor uw Azure-toepassing op het eerste productieniveau. Deze basisblauwdruk is gebaseerd op het aanbevolen patroon dat kan worden gevonden in CAF.

## <a name="architecture"></a>Architectuur

Met het CAF Foundation-blauwdrukvoorbeeld worden aanbevolen infrastructuurresources geïmplementeerd in Azure. Deze kunnen in organisaties worden gebruikt om de basisbesturingselementen werkend te krijgen die nodig zijn om de cloudomgeving te beheren. Met dit voorbeeld worden resources, beleid en sjablonen geïmplementeerd en afgedwongen die een organisatie helpen om zorgeloos aan de slag te gaan met Azure.

:::image type="content" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="CAF Foundation, afbeelding geeft aan wat wordt geïnstalleerd als onderdeel van CAF-richtlijnen voor het maken van een basis om aan de slag te gaan met Azure" border="false":::

Deze implementatie omvat verschillende Azure-services die worden gebruikt om een veilige, volledig bewaakte basis op bedrijfsniveau te bieden. De omgeving bestaat uit de volgende elementen:

- Een [Azure Key Vault](../../../../key-vault/general/overview.md)-exemplaar om de geheimen te bewaren voor de virtuele machines die in de omgeving met gedeelde services worden geïmplementeerd
- Implementeer [Log Analytics](../../../../azure-monitor/overview.md). Dit wordt geïmplementeerd om te garanderen dat er op een centrale locatie logboeken worden bijgehouden voor alle acties en services zodra u begint met de beveiligde implementatie in [Opslagaccounts](../../../../storage/common/storage-introduction.md) voor diagnostisch logboekregistratie
- Implementeer [Azure Security Center](../../../../security-center/security-center-intro.md) (standaardversie). Dit biedt bedreigingsbeveiliging voor uw gemigreerde workloads
- Met de blauwdruk wordt ook [Azure-beleid](../../../policy/overview.md) gedefinieerd en geïmplementeerd, voor 
  - Taggen (CostCenter) wordt toegepast op resourcegroepen
  - Resources bijvoegen in resourcegroep met de CostCenter-tag
  - Toegestane Azure-regio voor resources en resourcegroepen
  - Toegestane opslagaccount-SKU's (kiezen tijden het implementeren)
  - Toegestane Azure-VM-SKU's (kiezen tijdens het implementeren)
  - Vereisen dat Network Watcher wordt geïmplementeerd 
  - Veilige overdrachtversleuteling voor Azure-opslagaccount vereisen
  - Resourcetypen weigeren (kiezen tijdens implementeren)  
- Initiatieven
  - Bewaken in Azure Security Center inschakelen (89 beleidsregels)

Al deze elementen voldoen aan de aanbevolen procedures zoals gepubliceerd in [Azure Architecture Center - Referentiearchitecturen](/azure/architecture/reference-architectures/).

> [!NOTE]
> Met CAF Foundation wordt een basisarchitectuur aangelegd voor workloads.
> Naast dit fundament moet u ook de workloads zelf implementeren.

Zie [Microsoft Cloud Adoption Framework voor Azure - Ready](/azure/cloud-adoption-framework/ready/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het CAF Foundation-blauwdrukvoorbeeld doorgenomen.

> [!div class="nextstepaction"]
> [CAF Foundation-blauwdruk - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
