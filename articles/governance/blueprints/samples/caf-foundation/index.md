---
title: Voorbeelden - CAF Foundation-blauwdruk - Overzicht
description: Overzicht en architectuur van het CAF Foundation-blauwdrukvoorbeeld.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: b78f81e16afbd044306c6697e0d8823bdd02b81a
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232874"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Overzicht van Microsoft Cloud Adoption Framework voor het Azure Foundation-blauwdrukvoorbeeld

Met Microsoft CAF (Cloud Adoption Framework) voor de Azure Foundation-blauwdruk wordt een kernset met infrastructuurresources en besturingselementen voor beleid geïmplementeerd, bedoeld voor uw Azure-toepassing op het eerste productieniveau. Deze basisblauwdruk is gebaseerd op het aanbevolen patroon dat kan worden gevonden in CAF.

## <a name="architecture"></a>Architectuur

Met het CAF Foundation-blauwdrukvoorbeeld worden aanbevolen infrastructuurresources geïmplementeerd in Azure. Deze kunnen in organisaties worden gebruikt om de basisbesturingselementen werkend te krijgen die nodig zijn om de cloudomgeving te beheren. Met dit voorbeeld worden resources, beleid en sjablonen geïmplementeerd en afgedwongen die een organisatie helpen om zorgeloos aan de slag te gaan met Azure.

![CAF Foundation, afbeelding geeft aan wat wordt geïnstalleerd als onderdeel van CAF-richtlijnen voor het maken van een basis om aan de slag te gaan met Azure](../../media/caf-blueprints/caf-foundation-architecture.png)

Deze implementatie omvat verschillende Azure-services die worden gebruikt om een veilige, volledig bewaakte basis op bedrijfsniveau te bieden. De omgeving bestaat uit de volgende elementen:

- Een [Azure Key Vault](../../../../key-vault/key-vault-whatis.md)-exemplaar om de geheimen te bewaren voor de virtuele machines die in de omgeving met gedeelde services worden geïmplementeerd
- Implementeer [Log Analytics](../../../../azure-monitor/overview.md). Dit wordt geïmplementeerd om te garanderen dat er op een centrale locatie logboeken worden bijgehouden voor alle acties en services zodra u begint met de beveiligde implementatie in [Opslagaccounts](../../../../storage/common/storage-introduction.md) voor diagnostisch logboekregistratie
- Implementeer [Azure Security Center](../../../../security-center/security-center-intro.md) (standaardversie). Dit biedt bedreigingsbeveiliging voor uw gemigreerde workloads
- Met de blauwdruk wordt ook [Azure-beleid](../../../policy/overview.md) gedefinieerd en geïmplementeerd, voor 
  - Taggen (CostCenter) wordt toegepast op resourcegroepen
  - Resources bijvoegen in resourcegroep met de CostCenter-tag
  - Toegestane Azure-regio voor resources en resourcegroepen
  - Toegestane opslagaccount-SKU's (kiezen tijden het implementeren)
  - Toegestane Azure-VM-SKU’s (kiezen tijdens het implementeren)
  - Vereisen dat Network Watch wordt geïmplementeerd 
  - Veilige overdrachtversleuteling voor Azure-opslagaccount vereisen
  - Resourcetypen weigeren (kiezen tijdens implementeren)  
- Initiatieven
  - Bewaken in Azure Security Center inschakelen (89 beleidsregels)

Al deze elementen voldoen aan de aanbevolen procedures zoals gepubliceerd in [Azure Architecture Center - Referentiearchitecturen](/azure/architecture/reference-architectures/).

> [!NOTE]
> Met CAF Foundation wordt een basisarchitectuur aangelegd voor workloads.
> Naast dit fundament moet u ook de workloads zelf implementeren.

Zie [Microsoft Cloud Adoption Framework voor Azure - Ready](/azure/architecture/cloud-adoption/ready/azure-readiness-guide/govern-org-compliance?tabs=AzurePolicy) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het CAF Foundation-blauwdrukvoorbeeld doorgenomen.

> [!div class="nextstepaction"]
>  [CAF Foundation-blauwdruk - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).