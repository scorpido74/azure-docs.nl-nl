---
title: Azure-toegangsbeheer op basis van azure-functie beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u RBAC (Role-based access control) toepassen om azure-sitehersteltoegang te beheren.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257574"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Toegang tot siteherstel beheren met op rollen gebaseerd toegangscontrolebeheer (RBAC)

Azure role-based access Control (RBAC) maakt fijnkorrelig toegangsbeheer voor Azure mogelijk. Met RBAC u verantwoordelijkheden binnen uw team scheiden en alleen specifieke toegangsmachtigingen verlenen aan gebruikers als dat nodig is om specifieke taken uit te voeren.

Azure Site Recovery biedt 3 ingebouwde rollen om beheerbewerkingen voor siteherstel te beheren. Meer informatie over [ingebouwde Azure RBAC-rollen](../role-based-access-control/built-in-roles.md)

* [Site Recovery-inzender](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor beheerders van noodherstelbeheerders die disaster recovery voor toepassingen of hele organisaties kunnen inschakelen en beheren, zoals het geval is.
* [Site Recovery-operator](../role-based-access-control/built-in-roles.md#site-recovery-operator): deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan replicatie niet in- of uitschakelen, kluizen maken of verwijderen, nieuwe infrastructuur registreren of toegangsrechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een operator voor noodherstel die virtuele machines of toepassingen kan failoveren wanneer deze wordt geïnstrueerd door toepassingseigenaren en IT-beheerders in een werkelijke of gesimuleerde rampsituatie, zoals een DR-boor. Na de oplossing van de ramp kan de DR-operator de virtuele machines opnieuw beschermen en failbacken.
* [Site Recovery-lezer](../role-based-access-control/built-in-roles.md#site-recovery-reader) - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt voor een IT-monitoring executive die de huidige stand van bescherming kan controleren en indien nodig ondersteuningstickets kan verhogen.

Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u hoe u aangepaste rollen in Azure [bouwen.](../role-based-access-control/custom-roles.md)

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Machtigingen die nodig zijn om replicatie voor nieuwe virtuele machines in te schakelen
Wanneer een nieuwe virtuele machine wordt gerepliceerd naar Azure met Azure Site Recovery, worden de toegangsniveaus van de gekoppelde gebruiker gevalideerd om ervoor te zorgen dat de gebruiker over de vereiste machtigingen beschikt om de Azure-bronnen te gebruiken die aan Site Recovery zijn geleverd.

Als u replicatie voor een nieuwe virtuele machine wilt inschakelen, moet een gebruiker het volgende hebben:
* Toestemming om een virtuele machine te maken in de geselecteerde resourcegroep
* Toestemming om een virtuele machine te maken in het geselecteerde virtuele netwerk
* Toestemming om naar het geselecteerde opslagaccount te schrijven

Een gebruiker heeft de volgende machtigingen nodig om de replicatie van een nieuwe virtuele machine te voltooien.

> [!IMPORTANT]
>Controleer of relevante machtigingen worden toegevoegd volgens het implementatiemodel (Resource Manager/ Klassiek) dat wordt gebruikt voor het implementeren van resources.

> [!NOTE]
> Als u replicatie inschakelt voor een Azure VM en siteherstel updates wilt laten beheren, u tijdens het inschakelen van replicatie ook een nieuw Automatiseringsaccount maken in welk geval u toestemming nodig hebt om een automatiseringsaccount in hetzelfde geval te maken abonnement als de kluis ook.

| **Resourcetype** | **Implementatiemodel** | **Machtiging** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/lezen |
|  |  | Microsoft.Compute/virtualMachines/lezen |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/verwijderen |
|  | Klassiek | Microsoft.ClassicCompute/domainNames/gelezen |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/lezen |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Netwerk | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/lezen |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassiek | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klassiek | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resourcegroep | Resource Manager | Microsoft.Resources/implementaties/* |
|  |  | Microsoft.Resources/abonnementen/resourceGroepen/gelezen |

Overweeg de [ingebouwde rollen](../role-based-access-control/built-in-roles.md) 'Virtual Machine Contributor' en 'Classic Virtual Machine Contributor' te gebruiken voor respectievelijk Resource Manager- en Classic-implementatiemodellen.

## <a name="next-steps"></a>Volgende stappen
* [Op rollen gebaseerd toegangsbeheer:](../role-based-access-control/role-assignments-portal.md)aan de slag met RBAC in de Azure-portal.
* Meer informatie over het beheren van toegang met:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Probleemoplossing voor toegangsbeheer op basis van rollen:](../role-based-access-control/troubleshooting.md)ontvang suggesties voor het oplossen van veelvoorkomende problemen.
