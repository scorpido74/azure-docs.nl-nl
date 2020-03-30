---
title: VLAN's en subnetten in Azure VMware-oplossing van CloudSimple
description: Meer informatie over VLAN's en subnetten in CloudSimple Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024888"
---
# <a name="vlans-and-subnets-overview"></a>Overzicht van VLAN's en subnetten

CloudSimple biedt een netwerk per regio waar uw CloudSimple-service wordt geïmplementeerd.  Het netwerk is één TCP Layer 3-adresruimte met standaard routering ingeschakeld.  Alle private clouds en subnetten die in deze regio zijn gemaakt, kunnen met elkaar communiceren zonder extra configuratie.  U gedistribueerde poortgroepen op het vCenter maken met behulp van de VLAN's.

![CloudSimple-netwerktopologie](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN's

Voor elke Private Cloud wordt een VLAN (Layer 2-netwerk) gemaakt.  Het Layer 2-verkeer blijft binnen de grens van een private cloud, zodat u het lokale verkeer binnen de Private Cloud isoleren.  Een VLAN die op de Private Cloud is gemaakt, kan alleen worden gebruikt om gedistribueerde poortgroepen in die Private Cloud te maken.  Een VLAN die is gemaakt op een Private Cloud, wordt automatisch geconfigureerd op alle switches die zijn verbonden met de hosts van een Private Cloud.

## <a name="subnets"></a>Subnetten

U een subnet maken wanneer u een VLAN maakt door de adresruimte van het subnet te definiëren. Een IP-adres uit de adresruimte wordt toegewezen als subnetgateway. Per klant en regio wordt één privélaagse laag3-adresruimte toegewezen. U elke RFC 1918 niet-overlappende adresruimte, met uw on-premises netwerk of Azure virtueel netwerk, in uw netwerkregio configureren.

Alle subnetten kunnen standaard met elkaar communiceren, waardoor de configuratieoverhead voor routering tussen Private Clouds wordt verminderd. Oost-westgegevens op pc's in dezelfde regio blijven in hetzelfde Layer 3-netwerk en worden overgedragen via de lokale netwerkinfrastructuur binnen de regio. Er is geen uitgang vereist voor communicatie tussen Private Clouds in een regio. Deze aanpak elimineert elke PRESTATIESanctie voor WAN/uitgangen bij het implementeren van verschillende workloads in verschillende Private Clouds.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN-subnetten CIDR-bereik

Een Private Cloud wordt gemaakt als een geïsoleerde VMware-stack (ESXi-hosts, vCenter, vSAN en NSX) die wordt beheerd door een vCenter-server.  Beheercomponenten worden geïmplementeerd in het netwerk dat is geselecteerd voor vSphere/vSAN-subnetten CIDR.  Het CIDR-netwerk-bereik is tijdens de implementatie verdeeld in verschillende subnetten.

* Minimaal vSphere/vSAN-subnetten CIDR-bereikvoorvoegsel: **/24**
* Maximaal vSphere/vSAN-subnetten CIDR-bereikvoorvoegsel: **/21**

> [!CAUTION]
> IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de Private Cloud-infrastructuur.  Gebruik het IP-adres in dit bereik niet op een virtuele machine.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN-subnetten CIDR-bereiklimieten

Het selecteren van de CIDR-bereikgrootte van vSphere/vSAN-subnetten heeft een impact op de grootte van uw Private Cloud.  In de volgende tabel ziet u het maximum aantal knooppunten dat u hebben op basis van de grootte van vSphere/vSAN-subnetten CIDR.

| Opgegeven cidr-voorvoegsellengte vSphere/vSAN | Maximum aantal knooppunten |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Beheersubnetten die zijn gemaakt op een Private Cloud

De volgende beheersubnetten worden gemaakt wanneer u een Private Cloud maakt.

* **Systeembeheer**. VLAN en subnet voor het beheernetwerk, DNS-server en vCenter-server van ESXi-hosts.
* **VMotion**. VLAN en subnet voor ESXi hosts' vMotion netwerk.
* **VSAN**. VLAN en subnet voor esxi hosts's vSAN netwerk.
* **NsxtEdgeUplink1**. VLAN en subnet voor VLAN uplinks naar een extern netwerk.
* **NsxtEdgeUplink2**. VLAN en subnet voor VLAN uplinks naar een extern netwerk.
* **NsxtEdgeTransport**. VLAN en subnet voor transportzones regelen het bereik van Layer 2-netwerken in NSX-T.
* **NsxtHostTransport**. VLAN en subnet voor host transport zone.

### <a name="management-network-cidr-range-breakdown"></a>Uitsplitsing van het CIDR-bereik van het beheernetwerk

vSphere/vSAN subnetten CIDR-bereik gespecificeerd is verdeeld in meerdere subnetten.  In de volgende tabel ziet u een voorbeeld van de uitsplitsing voor toegestane voorvoegsels.  In het voorbeeld wordt 192.168.0.0 gebruikt als CIDR-bereik.

Voorbeeld:

| Opgegeven vSphere/vSAN-subnetten CIDR/voorvoegsel | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Systeembeheer | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T Gastheer Vervoer | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Volgende stappen

* [VLAN's en subnetten maken en beheren](create-vlan-subnet.md)
