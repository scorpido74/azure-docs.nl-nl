---
title: Veelgestelde vragen over de prijsprijzen voor Azure Network Performance Monitor | Microsoft Documenten
description: Veelgestelde vragen - Azure Network Performance Monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654389"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Prijswijzigingen voor Azure Network Performance Monitor

We hebben geluisterd naar uw feedback en onlangs een [nieuwe prijservaring](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) geïntroduceerd voor verschillende bewakingsservices in Azure. In dit artikel worden de prijswijzigingen met betrekking tot Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) vastgelegd in een gemakkelijk te lezen vraag- en antwoordindeling.

Network Performance Monitor bestaat uit drie componenten:
* [Prestatiemeter](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Service-eindpuntmonitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute-monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

In de volgende secties worden de prijswijzigingen voor de NPM-componenten uitgelegd.

## <a name="performance-monitor"></a>Prestatiemeter

**Hoe werd het gebruik van prestatiemeter gefactureerd in het oude model?**

De facturering voor NPM was gebaseerd op het gebruik en verbruik van twee componenten:
* **Knooppunten**: Alle synthetische transacties ontstaan en eindigen bij de knooppunten. Knooppunten worden ook wel agents of Microsoft Management Agents genoemd.
* **Gegevens**: De resultaten van de verschillende netwerktests worden opgeslagen in de werkruimte Log Analytics.

Onder het oude model werd de factuur berekend op basis van het aantal knooppunten en het volume van de gegenereerde gegevens. 

**Hoe wordt het gebruik van prestatiemeter in rekening gebracht onder het nieuwe model?**

De functie Prestatiemonitor in NPM wordt nu gefactureerd op basis van een combinatie van: 

* Subnetkoppelingen gecontroleerd
* Gegevensvolume

**Wat is een subnetlink?**

Performance Monitor bewaakt de connectiviteit tussen twee of meer locaties in het netwerk. De verbinding tussen een groep knooppunten of agents op een subnet en een groep knooppunten op een ander subnet wordt een subnetkoppeling genoemd.

**Ik heb twee subnetten (A en B), en ik heb verschillende agenten op elk subnet. Performance Monitor bewaakt de connectiviteit van alle agents op subnet A met alle agents op subnet B. Worden er kosten in rekening gebracht op basis van het aantal intersubnetverbindingen?**

Nee. Voor factureringsdoeleinden worden alle verbindingen van subnet A naar subnet B gegroepeerd in één subnetkoppeling. Je wordt gefactureerd voor één verbinding. Performance Monitor blijft de connectiviteit tussen verschillende agents op elk subnet monitoren.

**Wat zijn de kosten voor het bewaken van een subnetlink?**

Zie de sectie [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) voor de kosten van het bewaken van één subnetkoppeling voor de hele maand.

**Wat zijn de kosten voor gegevens die Performance Monitor genereert?**

De kosten voor inname (gegevens uploaden naar Log Analytics-werkruimte in Azure Monitor, verwerking en indexering) zijn beschikbaar op de [prijspagina](https://azure.microsoft.com/pricing/details/log-analytics/) voor Log Analytics in de sectie Gegevensopname. De kosten voor het bewaren van gegevens (dat wil zeggen gegevens die worden bewaard op de optie van de klant, na de eerste maand) is ook beschikbaar op de [prijspagina](https://azure.microsoft.com/pricing/details/log-analytics/), in de sectie Gegevensbewaring.


## <a name="expressroute-monitor"></a>ExpressRoute-monitor

**Wat zijn de kosten voor het gebruik van ExpressRoute Monitor?**

Kosten voor ExpressRoute Monitor worden gefactureerd op basis van de hoeveelheid gegevens die tijdens de monitoring worden gegenereerd. Zie 'Wat zijn de kosten voor gegevens die prestatiemeter genereert?'

**Ik gebruik ExpressRoute Monitor om meerdere ExpressRoute circuits te monitoren. Worden er kosten in rekening gebracht op basis van het aantal circuits dat wordt gecontroleerd?**

Er worden geen kosten in rekening gebracht op basis van het aantal circuits of het type peering (bijvoorbeeld privépeering, Microsoft-peering). Er worden kosten in rekening gebracht op basis van de hoeveelheid gegevens, zoals eerder uitgelegd.

**Wat is het volume van de gegevens die worden gegenereerd wanneer ExpressRoute één circuit bewaakt?**

Het volume van de gegevens die per maand worden gegenereerd wanneer ExpressRoute een privé-peeringverbinding controleert, is als volgt:

|Percentiel      |Gegevens/maand (MB)|
| :---:          |           ---:|
|50<sup>e</sup> |            192|
|60<sup>e</sup> |            256|
|70<sup>e</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Volgens deze tabel betalen klanten met een 50e percentiel voor 192 MB aan gegevens. Bij USD $2.30/GB voor de eerste maand, zijn de kosten die voor het controleren van een kring worden gemaakt USD $0.43 (= 192 * 2.30 / 1024).

**Wat zijn enkele redenen voor variaties in het volume van de gegevens?**

De hoeveelheid gegenereerde monitoringgegevens is afhankelijk van verschillende factoren, zoals:
* Aantal agenten. De nauwkeurigheid van foutisolatie neemt toe met een toename van het aantal agenten.
* Aantal hops op het netwerk.
* Aantal paden tussen de bron en de bestemming.

Klanten aan de hogere percentielen (in de vorige tabel) meestal controleren hun circuits vanuit verschillende uitkijkpunten op hun on-premises netwerk. Meerdere agents worden ook dieper in het netwerk geplaatst, verder van de edge router van de serviceprovider. De agents worden vaak geplaatst op verschillende gebruikerssites, vestigingen en racks in datacenters.

## <a name="service-endpoint-monitor"></a>Service-eindpuntmonitor

**Wat zijn de kosten voor het gebruik van Service Endpoint Monitor?**

Kosten voor het gebruik van Service Endpoint Monitor worden berekend op basis van:
* Aantal verbindingen
* Volume van de gegevens

**Wat is een verband?**

Een verbinding is een test van de bereikbaarheid naar één eindpunt (URL of netwerkservice) van één agent gedurende de hele maand. Het bewaken van een verbinding met bing.com van drie agents vormt bijvoorbeeld drie verbindingen.

**Wat zijn de kosten voor Service Endpoint Monitor?**

Raadpleeg de sectie [Verbindingsbewaking](https://azure.microsoft.com/pricing/details/network-watcher/) voor de kosten van het bewaken van een eindpunt voor de hele maand. De kosten voor gegevens zijn beschikbaar op de [prijspagina](https://azure.microsoft.com/pricing/details/log-analytics/) voor Log Analytics, in de sectie Gegevensopname.

## <a name="references"></a>Verwijzingen

[Veelgestelde vragen over de prijzen van Log Analytics:](https://azure.microsoft.com/pricing/details/log-analytics/)de faq-sectie bevat informatie over de gratis laag, de prijzen per knooppunt en andere prijsdetails.

