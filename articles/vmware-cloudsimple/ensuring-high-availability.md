---
title: Hoge Beschik baarheid van toepassingen garanderen bij uitvoering in VMware op Azure
description: Beschrijft functies van AVS voor hoge Beschik baarheid om veelvoorkomende scenario's voor toepassings fouten te verhelpen voor toepassingen die worden uitgevoerd in een AVS-privécloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b32f7f3f38098f935382cce46d8251340784b940
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025347"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Hoge Beschik baarheid van toepassingen garanderen bij uitvoering in VMware op Azure

De AVS-oplossing biedt hoge Beschik baarheid voor uw toepassingen die in VMware worden uitgevoerd in de Azure-omgeving. De volgende tabel geeft een overzicht van fout scenario's en de bijbehorende functies voor hoge Beschik baarheid.

| Fout scenario | Toepassing beveiligd? | Functie platform HA | VMware HA-functie | Functie Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Schijf fout | KLIKT | Snelle vervanging van een uitgevallen knoop punt | [Over het vSAN-standaard beleid voor opslag](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fout met ventilator | KLIKT | Redundante ventilatoren, snelle vervanging van een mislukt knoop punt |  |  |
| NIC-fout | KLIKT | Redundante NIC, snelle vervanging van een mislukt knoop punt
| Stroom storing host | KLIKT | Redundante voeding |  |  |
| ESXi-host-fout | KLIKT | snelle vervanging van een uitgevallen knoop punt | [VMware vSphere hoge Beschik baarheid](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM-fout | KLIKT | [Load balancers](load-balancers.md)  | [VMware vSphere hoge Beschik baarheid](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer voor stateless VMware-Vm's |
| Fout in knooppunt switch poort | KLIKT | Redundante NIC |  |  |
| Knooppunt switch is mislukt | KLIKT | Redundante Blade Switches |  |  |
| Rack fout | KLIKT | Plaatsingsgroepen |  |  |
| Netwerk verbinding met on-premises DC | KLIKT  | Redundante netwerk services |  | Redundante-circuits |
| Netwerk verbinding met Azure | KLIKT | |  | Redundante-circuits |
| Fout in Data Center | KLIKT |  |  | Beschikbaarheidszones |
| Regionale fout | KLIKT  |  |  | Azure-regio's |

De Azure VMware-oplossing per AVS biedt de volgende functies voor hoge Beschik baarheid.

## <a name="fast-replacement-of-failed-node"></a>Snelle vervanging van een uitgevallen knoop punt

De AVS-besturings vlak software bewaakt voortdurend de status van VMware-clusters en detecteert wanneer een ESXi-knoop punt mislukt. Vervolgens wordt er automatisch een nieuwe ESXi-host aan het betrokken VMware-cluster toegevoegd vanuit de groep van de gemakkelijk beschik bare knoop punten en neemt het knoop punt uit het cluster niet uit. Deze functionaliteit zorgt ervoor dat de reserve capaciteit in het VMware-cluster snel wordt hersteld, zodat de tolerantie van het cluster die wordt verschaft door vSAN en VMware HA, wordt hersteld.

## <a name="placement-groups"></a>Plaatsingsgroepen

Een gebruiker die een automatische AVS-Cloud maakt, kan een Azure-regio en een plaatsings groep in de geselecteerde regio selecteren. Een plaatsings groep is een verzameling knoop punten verdeeld over meerdere racks, maar binnen hetzelfde spin-netwerk segment. Knoop punten binnen dezelfde plaatsings groep kunnen elkaar bereiken met Maxi maal twee extra switch-hops. Een plaatsings groep is altijd binnen één Azure-beschikbaarheids zone en omvat meerdere racks. Het AVS-besturings vlak distribueert knoop punten van een automatische AVS-Cloud in meerdere racks op basis van de beste inspanningen. Knoop punten in verschillende plaatsings groepen worden gegarandeerd in verschillende racks geplaatst.

## <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheids zones zijn een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheids zones zijn speciale fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Elke regio heeft één beschikbaarheids zone. Zie [Wat zijn Beschikbaarheidszones in azure?](../availability-zones/az-overview.md)voor meer informatie.

## <a name="redundant-azure-expressroute-circuits"></a>Redundante Azure ExpressRoute-circuits

Data Center-connectiviteit met Azure vNet met behulp van ExpressRoute heeft redundante circuits om een netwerk connectiviteit met hoge Beschik baarheid te bieden.

## <a name="redundant-networking-services"></a>Redundante netwerk services

Alle AVS-netwerk services voor de cloud van de AVS (inclusief VLAN, firewall, open bare IP-adressen, Internet en VPN) zijn ontworpen om Maxi maal beschikbaar te zijn en ondersteuning te bieden voor de service-SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer voor stateless VMware-Vm's

Gebruikers kunnen een Azure Layer 7-Load Balancer voor de staatloze Vm's die worden uitgevoerd in de VMware-omgeving, plaatsen om hoge Beschik baarheid voor de weblaag te krijgen.

## <a name="azure-regions"></a>Azure-regio's

Een Azure-regio is een set data centers die zijn geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen regionaal netwerk met lage latentie. Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions)voor meer informatie.
