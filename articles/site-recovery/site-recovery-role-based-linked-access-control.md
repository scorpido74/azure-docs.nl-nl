---
title: Toegangs beheer op basis van rollen beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u op rollen gebaseerd toegangs beheer (RBAC) toepast om Azure Site Recovery toegang te beheren.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083763"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Site Recovery toegang beheren met op rollen gebaseerd toegangs beheer (RBAC)

Met toegangs beheer op basis van rollen (RBAC) van Azure kunt u toegang krijgen tot het verfijnen. Met RBAC kunt u verantwoordelijkheden binnen uw team scheiden en alleen specifieke toegangs machtigingen verlenen aan gebruikers als dat nodig is om specifieke taken uit te voeren.

Azure Site Recovery biedt drie ingebouwde rollen om Site Recovery beheer bewerkingen te beheren. Meer informatie over [ingebouwde Azure RBAC-rollen](../role-based-access-control/built-in-roles.md)

* [Site Recovery-inzender](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor nood herstel beheerders die herstel na nood gevallen voor toepassingen of volledige organisaties kunnen inschakelen en beheren.
* [Site Recovery-operator](../role-based-access-control/built-in-roles.md#site-recovery-operator): deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan de replicatie niet in-of uitschakelen, kluizen maken of verwijderen, een nieuwe infra structuur registreren of toegangs rechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een nood herstel operator die virtuele machines of toepassingen kan uitvoeren wanneer deze door eigen aren van toepassingen en IT-beheerders wordt aangeraden in een werkelijke of gesimuleerde nood situatie, zoals een DR-analyse. De herstel oplossing van de nood geval kan de DR-operator opnieuw beveiligen en failback uitvoeren voor de virtuele machines.
* [Site Recovery-lezer](../role-based-access-control/built-in-roles.md#site-recovery-reader) - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt voor IT-leidinggevenden die de huidige status van de beveiliging kunnen controleren en indien nodig ondersteunings tickets moeten activeren.

Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u [aangepaste rollen bouwen](../role-based-access-control/custom-roles.md) in Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Benodigde machtigingen voor het inschakelen van replicatie voor nieuwe virtuele machines
Wanneer een nieuwe virtuele machine wordt gerepliceerd naar Azure met behulp van Azure Site Recovery, worden de toegangs niveaus van de bijbehorende gebruiker gevalideerd om ervoor te zorgen dat de gebruiker over de vereiste machtigingen beschikt om de Azure-resources te gebruiken die aan Site Recovery zijn verstrekt.

Om replicatie voor een nieuwe virtuele machine in te scha kelen, moet een gebruiker beschikken over:
* Machtiging voor het maken van een virtuele machine in de geselecteerde resource groep
* Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
* Machtiging voor het schrijven naar het geselecteerde opslag account

Een gebruiker heeft de volgende machtigingen nodig om de replicatie van een nieuwe virtuele machine te volt ooien.

> [!IMPORTANT]
>Zorg ervoor dat relevante machtigingen worden toegevoegd volgens het implementatie model (Resource Manager/klassiek) dat wordt gebruikt voor de implementatie van resources.

> [!NOTE]
> Als u replicatie inschakelt voor een virtuele Azure-machine en u Site Recovery wilt toestaan om updates te beheren, terwijl u replicatie inschakelt, kunt u ook een nieuw Automation-account maken. in dat geval hebt u toestemming nodig voor het maken van een Automation-account in dezelfde u hebt ook een abonnement als kluis.

| **Resourcetype** | **Implementatie model** | **Hebt** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Klassiek | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Netwerk | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassiek | Micro soft. ClassicNetwork/virtualNetworks/lezen |
|  |  | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klassiek | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resourcegroep | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Overweeg het gebruik van de [ingebouwde rollen](../role-based-access-control/built-in-roles.md) ' virtual machine contributor ' en ' klassieke virtuele machine contributor ' voor respectievelijk Resource Manager en klassieke implementatie modellen.

## <a name="next-steps"></a>Volgende stappen
* [Op rollen gebaseerd Access Control](../role-based-access-control/role-assignments-portal.md): aan de slag met RBAC in de Azure Portal.
* Meer informatie over het beheren van toegang met:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Access Control probleem oplossing op basis van rollen](../role-based-access-control/troubleshooting.md): suggesties ophalen voor het oplossen van veelvoorkomende problemen.
