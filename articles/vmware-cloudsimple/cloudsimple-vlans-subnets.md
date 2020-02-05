---
title: VLAN'S en subnetten in azure VMware-oplossingen (AVS)
description: Meer informatie over VLAN'S en subnetten in de Privécloud van de AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d0ce15c782ae70e16f55a28ec8c4b70f3b080f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024888"
---
# <a name="vlans-and-subnets-overview"></a>Overzicht van VLAN'S en subnetten

AVS biedt een netwerk per regio waar uw AVS-service is geïmplementeerd. Het netwerk is één adres ruimte van de TCP-laag 3 waarvoor route ring standaard is ingeschakeld. Alle andere AVS-Clouds en-subnetten die in deze regio worden gemaakt, kunnen zonder extra configuratie met elkaar communiceren. U kunt gedistribueerde poort groepen maken op de vCenter met behulp van de VLAN'S.

![AVS-netwerk topologie](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Vlan's

Er wordt een VLAN (Layer 2-netwerk) gemaakt voor elke Privécloud in de Cloud. Het laag 2-verkeer blijft binnen de grens van een Privécloud, zodat u het lokale verkeer in de Privécloud van de cloud kunt isoleren. Een VLAN dat is gemaakt op de AVS-privécloud, kan worden gebruikt voor het maken van gedistribueerde poort groepen alleen in die AVS-privécloud. Een VLAN dat is gemaakt op een AVS-privécloud, wordt automatisch geconfigureerd op alle switches die zijn verbonden met de hosts van een automatische AVS-Cloud.

## <a name="subnets"></a>Subnetten

U kunt een subnet maken wanneer u een VLAN maakt door de adres ruimte van het subnet te definiëren. Een IP-adres uit de adres ruimte wordt toegewezen als een subnet gateway. Er wordt één adres ruimte van een persoonlijke laag 3 toegewezen per klant en regio. U kunt een niet-overlappende RFC 1918-adres ruimte configureren met uw on-premises netwerk of een virtueel Azure-netwerk in uw netwerk regio.

Alle subnetten kunnen standaard met elkaar communiceren, waardoor de configuratie overhead voor route ring tussen persoonlijke Clouds van AVS wordt verminderd. Oost-West-gegevens op verschillende Pc's in dezelfde regio blijven hetzelfde laag 3-netwerk en overdrachten via de lokale netwerk infrastructuur binnen de regio. Er is geen afwijkend vereist voor de communicatie tussen persoonlijke Clouds van de AVS in een regio. Deze aanpak elimineert de prestaties van het WAN/uitgangs niveau bij het implementeren van verschillende werk belastingen in verschillende AVS-Clouds.

## <a name="vspherevsan-subnets-cidr-range"></a>CIDR-bereik voor vSphere/vSAN-subnetten

Een AVS-Privécloud wordt gemaakt als een geïsoleerde VMware-stack (ESXi hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-Server. Beheer onderdelen worden geïmplementeerd in het netwerk dat is geselecteerd voor vSphere/vSAN-subnets CIDR. Het netwerk-CIDR-bereik is tijdens de implementatie onderverdeeld in verschillende subnetten.

* Minimum aantal vSphere/vSAN-subnetten CIDR-bereik voor voegsel: **/24**
* Maximum aantal vSphere/vSAN-subnetten voor voegsel van CIDR-bereik: **/21**

> [!IMPORTANT]
> IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de Cloud infrastructuur voor de Privécloud. Gebruik het IP-adres in dit bereik op geen enkele virtuele machine.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN subnetten CIDR-bereik limieten

Het selecteren van het CIDR-bereik van de vSphere/vSAN-subnetten heeft invloed op de grootte van de Privécloud van uw AVS. In de volgende tabel ziet u het maximum aantal knoop punten dat u kunt hebben op basis van de grootte van de vSphere/vSAN-subnetten CIDR.

| Opgegeven vSphere/vSAN-subnetten CIDR-voor voegsel lengte | Maximum aantal knoop punten |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-an-avs-private-cloud"></a>Systeemsubnetten die zijn gemaakt op een AVS-Privécloud

De volgende subnetten met beheer worden gemaakt wanneer u een Privécloud maakt.

* **Systeem beheer**. VLAN en subnet voor ESXi host-beheer netwerk, DNS-server, vCenter-Server.
* **VMotion**. VLAN en subnet voor het ESXi-netwerk van de host.
* **VSAN**. VLAN en subnet voor ESXi-hosts vSAN-netwerk.
* **NsxtEdgeUplink1**. VLAN en subnet voor VLAN-uplinks naar een extern netwerk.
* **NsxtEdgeUplink2**. VLAN en subnet voor VLAN-uplinks naar een extern netwerk.
* **NsxtEdgeTransport**. VLAN en subnet voor transport zones bepalen het bereik van laag-2-netwerken in NSX-T.
* **NsxtHostTransport**. VLAN en subnet voor host-transport zone.

### <a name="management-network-cidr-range-breakdown"></a>Specificatie van CIDR-bereik voor beheer netwerk

het opgegeven CIDR-bereik voor vSphere/vSAN-subnetten is onderverdeeld in meerdere subnetten. De volgende tabel toont een voor beeld van de uitsplitsing van toegestane voor voegsels. In het voor beeld wordt 192.168.0.0 gebruikt als het CIDR-bereik.

Voorbeeld:

| Opgegeven vSphere/vSAN-subnetten CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Systeem beheer | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T-host transport | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T-EDGE-Trans Port | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T EDGE Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T EDGE uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Volgende stappen

* [VLAN'S en subnetten maken en beheren](create-vlan-subnet.md)
