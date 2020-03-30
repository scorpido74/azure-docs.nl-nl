---
title: Openbare IP-adressen toewijzen na failover met Azure Site Recovery
description: Beschrijft hoe u openbare IP-adressen instelt met Azure Site Recovery en Azure Traffic Manager voor herstel en migratie na noodgevallen
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281949"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Openbare IP-adressen instellen na failover

Door openbare IP-adressen te gebruiken, zijn internetbronnen in staat inkomende communicatie voor Azure-resources te verwerken. Openbare IP-adressen maken het ook mogelijk dat Azure-resources uitgaande communicatie naar internet en openbare Azure-services kunnen afhandelen via een IP-adres dat is toegewezen aan de resource.
- Binnenkomende communicatie van internet naar de bron, zoals Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways en anderen. U nog steeds communiceren met bepaalde bronnen, zoals VM's, via internet, als een VM geen openbaar IP-adres heeft toegewezen, zolang de VM deel uitmaakt van een back-endpool van load balancer en de load balancer een openbaar IP-adres krijgt toegewezen.
- Uitgaande connectiviteit met het internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand communiceren naar het internet zonder dat er een openbaar IP-adres aan is toegewezen, maar het adres is standaard netwerkadres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar adres. Als u een openbaar IP-adres aan een resource toewijst, weet u welk IP-adres wordt gebruikt voor de uitgaande verbinding. Hoewel voorspelbaar, kan het adres veranderen, afhankelijk van de gekozen toewijzingsmethode. Zie [Een openbaar IP-adres maken](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie . Zie Uitgaande verbindingen begrijpen voor meer informatie over uitgaande verbindingen van [Azure-bronnen.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

In Azure Resource Manager is een openbaar IP-adres een bron met zijn eigen eigenschappen. U kunt sommige resources aan een openbare IP-adresresource koppelen, zoals:

* Netwerkinterfaces van virtuele machines
* Internetgerichte load balancers
* VPN-gateways
* Toepassingsgateways

In dit artikel wordt beschreven hoe u openbare IP-adressen gebruiken met Site recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Toewijzing van openbare IP-adres met herstelplan

Het openbare IP-adres van de productieaanvraag **kan niet worden bewaard bij failover**. Workloads die worden aangeboden als onderdeel van het failoverproces, moeten een Azure Public IP-bron toegewezen krijgen die beschikbaar is in de doelregio. Deze stap kan handmatig worden uitgevoerd of wordt geautomatiseerd met herstelplannen. Een herstelplan verzamelt machines in herstelgroepen. Het helpt u om een systematisch herstelproces te definiëren. U een herstelplan gebruiken om order op te leggen en de acties te automatiseren die nodig zijn bij elke stap, met Azure Automation-runbooks voor failover naar Azure of scripts.

De setup is als volgt:
- Maak een [herstelplan](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) en groepeer uw workloads indien nodig in het plan.
- Pas het abonnement aan door een stap toe te voegen om een openbaar IP-adres toe te voegen met [Azure Automation-runbooks-scripts](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) aan de mislukte VM.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Overschakelen naar openbare eindpunten met DNS-niveauroutering

Azure Traffic Manager maakt DNS-niveauroutering tussen eindpunten mogelijk en kan u helpen [bij het neerhalen van uw OTO's](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) voor een DR-scenario. 

Lees meer over failoverscenario's met Traffic Manager:
1. [On-premises naar Azure-failover](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) met Traffic Manager 
2. [Azure naar Azure failover](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) met Traffic Manager 

De setup is als volgt:
- Maak een [Traffic Manager-profiel](../traffic-manager/traffic-manager-create-profile.md).
- Maak gebruik van de **routeringsmethode Prioriteit** twee eindpunten : **Primair** voor bron en **Failover** voor Azure. **Primair** is toegewezen prioriteit 1 en **failover** is toegewezen prioriteit 2.
- Het **primaire** eindpunt kan [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) of [Extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) zijn, afhankelijk van of uw bronomgeving zich binnen of buiten Azure bevindt.
- Het **Failover-eindpunt** wordt gemaakt als een **Azure-eindpunt.** Gebruik een **statisch openbaar IP-adres,** omdat dit extern eindpunt is voor Traffic Manager in de rampgebeurtenis.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Traffic Manager met Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Meer informatie over [routeringsmethoden](../traffic-manager/traffic-manager-routing-methods.md)voor beheerbeheer .
- Meer informatie over [herstelplannen](site-recovery-create-recovery-plans.md) om failover van toepassingen te automatiseren.
