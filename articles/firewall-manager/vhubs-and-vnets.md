---
title: Wat zijn de azure Firewall Manager-architectuuropties?
description: Vergelijk en contrastmet behulp van hub virtueel netwerk of beveiligde virtuele hub architecturen met Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444574"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Wat zijn de azure Firewall Manager-architectuuropties?

Azure Firewall Manager kan beveiligingsbeheer bieden voor twee typen netwerkarchitecturen:

- **beveiligde virtuele hub**

   Een [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) is een door Microsoft beheerde bron waarmee u eenvoudig hub- en spoke-architecturen maken. Wanneer beveiligings- en routeringsbeleid zijn gekoppeld aan een dergelijke hub, wordt het een *[beveiligde virtuele hub genoemd.](secured-virtual-hub.md)* 
- **virtueel netwerk hub**

   Dit is een standaard Virtueel Azure-netwerk dat u zelf maakt en beheert. Wanneer beveiligingsbeleid is gekoppeld aan een dergelijke hub, wordt het een *virtueel hubnetwerk genoemd.* Op dit moment wordt alleen azure firewallbeleid ondersteund. U peer spoke virtuele netwerken die uw workload servers en services bevatten. U firewalls ook beheren in zelfstandige virtuele netwerken die niet zijn gekeken naar een spaak.

## <a name="comparison"></a>Vergelijking

In de volgende tabel worden deze twee architectuuropties vergeleken en u bepalen welke opties geschikt zijn voor de beveiligingsvereisten van uw organisatie:


|  |**Hub van virtueel netwerk**|**Beveiligde virtuele hub**  |
|---------|---------|---------|
|**Onderliggende bron**     |Virtueel netwerk|Virtuele WAN-hub|
|**Hub & gesproken**     |Maakt gebruik van virtueel netwerkpeeren|Geautomatiseerd met behulp van virtuele netwerkverbinding hub|
|**On-prem-connectiviteit**     |VPN Gateway tot 10 Gbps en 30 S2S-verbindingen; ExpressRoute ExpressRoute|Meer schaalbare VPN Gateway up 20 Gbps en 1000 S2S-verbindingen; Express Route|
|**Geautomatiseerde branchconnectiviteit met SDWAN**      |Niet ondersteund|Ondersteund|
|**Hubs per regio**     |Meerdere virtuele netwerken per regio|Eén virtuele hub per regio. Meerdere hubs mogelijk met meerdere Virtual WANs|
|**Azure Firewall – meerdere openbare IP-adressen**      |Klant verstrekt|Automatisch gegenereerd. Beschikbaar zijn door GA.|
|**Beschikbaarheidszones voor Azure Firewall**     |Ondersteund|Niet beschikbaar in preview. Beschikbaar zijn via GA|
|**Geavanceerde internetbeveiliging met beveiliging van derden als servicepartners**     |Klant gevestigde en beheerde VPN-connectiviteit met partnerservice naar keuze|Geautomatiseerd via Trusted Security Partner flow en partner management ervaring|
|**Gecentraliseerd routebeheer om verkeer naar de hub te leiden**     |Door de klant beheerde door de gebruiker defined route|Ondersteund met BGP|
|**Web Application Firewall in Application Gateway** |Ondersteund in virtueel netwerk|Momenteel ondersteund in spoke netwerk|
|**Netwerk virtueel toestel**|Ondersteund in virtueel netwerk|Momenteel ondersteund in spoke netwerk|

## <a name="next-steps"></a>Volgende stappen

- Overzicht van [het overzicht van de implementatievan Azure Firewall Manager Preview controleren](deployment-overview.md)
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).