---
title: VPN Gateway Classic naar Resource Manager Migratie | Microsoft Documenten
description: Deze pagina geeft een overzicht van de VPN Gateway Classic naar Resource Manager migratie.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064764"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway klassieker naar Resource Manager migratie
VPN-gateways kunnen nu worden gemigreerd van klassiek naar Resource Manager-implementatiemodel. U meer lezen over [de functies en voordelen](../azure-resource-manager/management/overview.md)van Azure Resource Manager. In dit artikel beschrijven we hoe u migreren van klassieke implementaties naar een nieuwer resourcebeheermodel. 

VPN-gateways worden gemigreerd als onderdeel van VNet-migratie van klassiek naar Resource Manager. Deze migratie gebeurt één VNet per keer. Er is geen extra vereiste op het gebied van tools of vereisten voor migratie. Migratiestappen zijn identiek aan bestaande VNet-migratie en worden gedocumenteerd op [de migratiepagina van IaaS-bronnen](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Er is geen downtime van gegevenspaden tijdens migratie en dus zouden bestaande workloads blijven functioneren zonder verlies van on-premises connectiviteit tijdens de migratie. Het openbare IP-adres dat is gekoppeld aan de VPN-gateway verandert niet tijdens het migratieproces. Dit houdt in dat u uw on-premises router niet opnieuw hoeft te configureren zodra de migratie is voltooid.  

Het model in Resource Manager is anders dan het klassieke model en bestaat uit virtuele netwerkgateways, lokale netwerkgateways en verbindingsbronnen. Deze vertegenwoordigen de VPN-gateway zelf, de lokale site die op locatie adresruimte en connectiviteit tussen de twee respectievelijk. Zodra de migratie is voltooid, zijn uw gateways niet beschikbaar in een klassiek model en moeten alle beheerbewerkingen op virtuele netwerkgateways, lokale netwerkgateways en verbindingsobjecten worden uitgevoerd met behulp van het Resource Manager-model.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
De meest voorkomende VPN-connectiviteitsscenario's worden gedekt door klassieke naar Resource Manager-migratie. De ondersteunde scenario's omvatten -

* Punt naar site-connectiviteit
* Site-naar-site-connectiviteit met VPN-gateway die is verbonden met on-premises locatie
* VNet-connectiviteit tussen twee VNet's met VPN-gateways
* Meerdere VNets verbonden met dezelfde locatie op locatie
* Connectiviteit met meerdere terreinen
* Geforceerde tunneling ingeschakeld VNets

Scenario's die niet worden ondersteund, zijn -  

* VNet met zowel ExpressRoute Gateway als VPN Gateway wordt momenteel niet ondersteund.
* Transitscenario's waarbij VM-extensies zijn verbonden met on-premises servers. Transit VPN-connectiviteit beperkingen zijn hieronder beschreven.

> [!NOTE]
> CIDR-validatie in Resource Manager-model is strenger dan het model in het klassieke model. Zorg er voordat u migreert ervoor dat klassieke adresbereiken voldoen aan de geldige CIDR-indeling voordat u met de migratie begint. CIDR kan worden gevalideerd met behulp van een gemeenschappelijke CIDR validators. VNet of lokale sites met ongeldige CIDR-bereiken wanneer gemigreerd zou resulteren in mislukte status.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet naar VNet-connectiviteitsmigratie
VNet naar VNet connectiviteit in de klassieke werd bereikt door het creëren van een lokale site vertegenwoordiging van de aangesloten VNet. Klanten moesten twee lokale sites maken die de twee VNets vertegenwoordigden die met elkaar verbonden moesten worden. Deze werden vervolgens aangesloten op de overeenkomstige VNets met behulp van IPsec tunnel om de connectiviteit tussen de twee VNets vast te stellen. Dit model heeft uitdaging voor beheerbaarheid, omdat elke adresbereikwijzigingen in één VNet ook moeten worden gehandhaafd in de overeenkomstige lokale sitevertegenwoordiging. In resourcemanagermodel is deze tijdelijke oplossing niet langer nodig. De verbinding tussen de twee VNets kan direct worden bereikt met behulp van 'Vnet2Vnet' verbindingstype in Connection resource. 

![Schermafbeelding van VNet-migratie naar VNet-migratie.](./media/vpn-gateway-migration/migration1.png)

Tijdens VNet-migratie detecteren we dat de verbonden entiteit met de huidige VPN-gateway van VNet een andere VNet is en zorgen we ervoor dat zodra de migratie van beide VNets is voltooid, u niet langer twee lokale sites ziet die de andere VNet vertegenwoordigen. Het klassieke model van twee VPN-gateways, twee lokale sites en twee verbindingen tussen hen wordt omgezet in Resource Manager-model met twee VPN-gateways en twee verbindingen van het type Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Transit VPN-connectiviteit
U VPN-gateways in een topologie zo configureren dat on-premises connectiviteit voor een VNet wordt bereikt door verbinding te maken met een andere VNet die rechtstreeks is verbonden met on-premises. Dit is transit VPN-connectiviteit waarbij exemplaren in de eerste VNet zijn verbonden met on-premises bronnen via doorvoer naar de VPN-gateway in verbonden VNet die rechtstreeks is verbonden met on-premises. Om deze configuratie in het klassieke implementatiemodel te bereiken, moet u een lokale site maken met geaggregeerde voorvoegsels die zowel de verbonden VNet- als de on-premises adresruimte vertegenwoordigen. Deze representatieve lokale site wordt vervolgens verbonden met het VNet om transitconnectiviteit te bereiken. Dit klassieke model heeft ook vergelijkbare beheersbaarheidsuitdagingen, omdat elke wijziging in het on-premises adresbereik ook moet worden gehandhaafd op de lokale site die het aggregaat van VNet en on-premises vertegenwoordigt. De introductie van BGP-ondersteuning in door Resource Manager ondersteunde gateways vereenvoudigt de beheerbaarheid, omdat de verbonden gateways routes van on-premises kunnen leren zonder handmatige wijzigingen aan voorvoegsels.

![Schermafbeelding van het scenario voor transitroutering.](./media/vpn-gateway-migration/migration2.png)

Aangezien we VNet transformeren naar VNet-connectiviteit zonder dat lokale sites nodig zijn, verliest het transitscenario on-premises connectiviteit voor VNet die indirect is verbonden met on-premises. Het verlies van connectiviteit kan op de volgende twee manieren worden beperkt, nadat de migratie is voltooid - 

* Schakel BGP in op VPN-gateways die met elkaar zijn verbonden en met on-premises. Het inschakelen van BGP herstelt de connectiviteit zonder enige andere configuratiewijziging, omdat routes worden geleerd en geadverteerd tussen VNet-gateways. Houd er rekening mee dat de BGP-optie alleen beschikbaar is op standaard en hogere SKU's.
* Maak een expliciete verbinding van getroffen VNet met de lokale netwerkgateway die on-premises locatie vertegenwoordigt. Dit vereist ook een wijziging van de configuratie op de on-premises router om de IPsec-tunnel te maken en te configureren.

## <a name="next-steps"></a>Volgende stappen
Nadat je meer te weten bent gekomen over ondersteuning voor vpn-gatewaymigratie, ga je naar [platformondersteunde migratie van IaaS-bronnen van klassiek naar Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) om aan de slag te gaan.

