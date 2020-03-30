---
title: Zorgen voor hoge beschikbaarheid van toepassingen wanneer deze wordt uitgevoerd in VMware op Azure
description: Beschrijft CloudSimple functies met hoge beschikbaarheid om veelvoorkomende scenario's voor toepassingsfouten voor toepassingen die worden uitgevoerd in een CloudSimple Private Cloud aan te pakken
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025347"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Zorgen voor hoge beschikbaarheid van toepassingen wanneer deze wordt uitgevoerd in VMware op Azure

De CloudSimple-oplossing biedt een hoge beschikbaarheid voor uw toepassingen die worden uitgevoerd op VMware in de Azure-omgeving. In de volgende tabel worden foutscenario's en de bijbehorende functies met hoge beschikbaarheid weergegeven.

| Faalscenario | Toepassing beschermd? | Platform HA-functie | VMware HA-functie | Azure HA-functie |
------------ | ------------- | ------------ | ------------ | ------------- |
| Schijffout | JA | Snelle vervanging van mislukt knooppunt | [Informatie over het standaardopslagbeleid van vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fanfalen | JA | Redundante ventilatoren, snelle vervanging van mislukte knooppunt |  |  |
| NIC-storing | JA | Redundantnic, snelle vervanging van mislukte knooppunt
| Stroomstoring host | JA | Redundante voeding |  |  |
| ESXi-hostmislukt | JA | snelle vervanging van het mislukte knooppunt | [VMware vSphere hoge beschikbaarheid](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM-fout | JA | [Load balancers](load-balancers.md)  | [VMware vSphere hoge beschikbaarheid](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer voor stateloze VMware VM's |
| Bladschakelaarpoort mislukt | JA | Redundante NIC |  |  |
| Bladschakelaar mislukt | JA | Redundante bladschakelaars |  |  |
| Rackstoring | JA | Plaatsingsgroepen |  |  |
| Netwerkconnectiviteit met on-premises DC | JA  | Redundante netwerkdiensten |  | Redundante ER-circuits |
| Netwerkconnectiviteit met Azure | JA | |  | Redundante ER-circuits |
| Datacenterstoring | JA |  |  | Beschikbaarheidszones |
| Regionale mislukking | JA  |  |  | Azure-regio's |

Azure VMware Solution by CloudSimple biedt de volgende functies met hoge beschikbaarheid.

## <a name="fast-replacement-of-failed-node"></a>Snelle vervanging van mislukt knooppunt

De CloudSimple control plane software bewaakt continu de status van VMware clusters en detecteert wanneer een ESXi-knooppunt uitvalt. Vervolgens voegt het automatisch een nieuwe ESXi-host toe aan het getroffen VMware-cluster uit de pool van direct beschikbare knooppunten en haalt het mislukte knooppunt uit het cluster. Deze functionaliteit zorgt ervoor dat de reservecapaciteit in het VMware-cluster snel wordt hersteld, zodat de veerkracht van het cluster van vSAN en VMware HA wordt hersteld.

## <a name="placement-groups"></a>Plaatsingsgroepen

Een gebruiker die een Private Cloud maakt, kan een Azure-regio en een plaatsingsgroep binnen het geselecteerde gebied selecteren. Een plaatsingsgroep is een set knooppunten verspreid over meerdere racks, maar binnen hetzelfde netwerksegment van de wervelkolom. Knooppunten binnen dezelfde plaatsingsgroep kunnen elkaar bereiken met maximaal twee extra schakelhops. Een plaatsingsgroep bevindt zich altijd binnen één Azure-beschikbaarheidszone en omvat meerdere racks. Het CloudSimple-besturingsvlak verdeelt knooppunten van een Private Cloud over meerdere racks op basis van de beste inspanning. Knooppunten in verschillende plaatsingsgroepen worden gegarandeerd in verschillende racks geplaatst.

## <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheidszones zijn een aanbod met hoge beschikbaarheid dat uw toepassingen en gegevens beschermt tegen datacenterfouten. Beschikbaarheidszones zijn speciale fysieke locaties binnen een Azure-gebied. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Elke regio heeft één beschikbaarheidszone. Zie [Wat zijn beschikbaarheidszones in Azure voor](../availability-zones/az-overview.md)meer informatie.

## <a name="redundant-azure-expressroute-circuits"></a>Redundante Azure ExpressRoute-circuits

Datacenterconnectiviteit met Azure vNet met ExpressRoute heeft redundante circuits om een zeer beschikbare netwerkverbinding te bieden.

## <a name="redundant-networking-services"></a>Redundante netwerkdiensten

Alle CloudSimple-netwerkservices voor de Private Cloud (waaronder VLAN, firewall, openbare IP-adressen, internet en VPN) zijn ontworpen om zeer beschikbaar te zijn en in staat om de service SLA te ondersteunen.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer voor stateloze VMware VM's

Gebruikers kunnen een Azure Layer 7 Load Balancer voor de stateless web tier VM's plaatsen die in de VMware-omgeving worden uitgevoerd om een hoge beschikbaarheid voor de weblaag te bereiken.

## <a name="azure-regions"></a>Azure-regio's

Een Azure-regio is een set datacenters die zijn geïmplementeerd binnen een latentiegedefinieerde perimeter en zijn verbonden via een specifiek regionaal netwerk met lage latentie. Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions)voor meer informatie.
