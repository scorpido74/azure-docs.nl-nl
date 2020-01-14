---
title: Overzicht van het blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads
description: Overzicht en architectuur van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: d77029b93b1e77bdba85c5dd5608cbdc5d6543a5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920778"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Overzicht van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads

Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads biedt een aanvullende infrastructuur voor het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).
Aan de hand van deze blauwdruk kunnen klanten op de cloud gebaseerde architecturen implementeren die oplossingen bieden voor scenario's met accreditatie- en nalevingsvereisten.

Er zijn twee ISO 27001 conforme blauwdrukvoorbeelden: dit voorbeeld en het voorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).

> [!IMPORTANT]
> Dit voorbeeld is afhankelijk van de infrastructuur die wordt geïmplementeerd door het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md). Dat voorbeeld moet daarom eerst worden geïmplementeerd.

## <a name="architecture"></a>Architectuur

Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads implementeert een webomgeving op basis van een Platform-as-a-Service. De omgeving kan worden gebruikt voor het hosten van meerdere webtoepassingen, web-API's en SQL Database-exemplaren die voldoen aan de ISO 27001-standaarden. Dit blauwdrukvoorbeeld is afhankelijk van het blauwdrukvoorbeeld [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md).

![Ontwerp van het blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

Deze omgeving bestaat uit diverse Azure-services die samen een veilige, volledig bewaakte en direct inzetbare workloadinfrastructuur bieden op basis van de ISO 27001-standaarden. De omgeving bestaat uit de volgende elementen:

- Een rol op basis van [op rollen gebaseerd toegangsbeheer](../../../../role-based-access-control/overview.md), DevOps genoemd, met rechten om resources te implementeren en te beheren in de [Azure App Service Environment](../../../../app-service/environment/intro.md) die door het blauwdrukvoorbeeld wordt geïmplementeerd
- [Azure-beleid](../../../policy/overview.md) om vast te leggen welke services in de omgeving mogen worden geïmplementeerd en om te voorkomen dat er resources op basis van een openbaar IP-adres worden gemaakt
- Een virtueel netwerk met één subnet en een koppeling met een al bestaande omgeving met [gedeelde services](../iso27001-shared/index.md), waarbij al het verkeer wordt gedwongen de firewall voor [gedeelde services](../iso27001-shared/index.md) te passeren. Het virtuele netwerk hosts de volgende resources:
  - Een [Azure App Service Environment](../../../../app-service/environment/intro.md) die kan worden gebruikt voor het hosten van een of meer web-apps, web-API's of functies
  - Een [Azure Key Vault](../../../../key-vault/key-vault-overview.md)-exemplaar met een VNet-service-eindpunt, voor het opslaan van geheimen die worden gebruikt door in de workloadomgeving uitgevoerde toepassingen
  - Een [Azure SQL Database](../../../../sql-database/sql-database-technical-overview.md)-serverexemplaar met een VNet-service-eindpunt, voor het hosten van databases die worden gebruikt voor toepassingen in de workloadomgeving

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads doorgenomen. Lees nu de volgende artikelen voor meer informatie over het toewijzen van beheeropties en het implementeren van dit voorbeeld:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - beheeropties toewijzen](./control-mapping.md)
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).