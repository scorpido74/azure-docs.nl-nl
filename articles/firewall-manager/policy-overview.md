---
title: Overzicht van Azure Firewall Manager-voorbeeld beleid
description: Meer informatie over Azure Firewall Manager-beleid
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445019"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Overzicht van Azure Firewall Manager-voorbeeld beleid

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Firewall-beleid is een Azure-resource die is opgenomen in de regel verzamelingen NAT, netwerk en toepassing, evenals instellingen voor bedreigings informatie. Het is een wereld wijde resource die kan worden gebruikt voor meerdere Azure Firewall instanties in beveiligde virtuele hubs en hub virtuele netwerken. Beleids regels werken in verschillende regio's en abonnementen.

![Azure Firewall Manager-beleid](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Beleid maken en koppelen

Een beleid kan op meerdere manieren worden gemaakt en beheerd, met inbegrip van de Azure Portal, REST API, sjablonen, Azure PowerShell en CLI.

U kunt ook bestaande regels van Azure Firewall migreren met behulp van de portal of Azure PowerShell om beleid te maken. Zie [Azure firewall configuraties migreren naar Azure firewall-beleid (preview)](migrate-to-policy.md)voor meer informatie. 

Beleids regels kunnen worden gekoppeld aan een of meer virtuele hubs of VNets. De firewall kan zich in elk abonnement bevinden dat is gekoppeld aan uw account en in elke regio.

## <a name="hierarchical-policies"></a>Hiërarchische beleids regels

Nieuwe beleids regels kunnen volledig worden gemaakt of worden overgenomen van bestaande beleids regels. Met overname kan DevOps lokaal firewall beleid maken op basis van de organisatie.

Beleids regels die zijn gemaakt met niet-leeg bovenliggend beleid, nemen alle regel verzamelingen van het bovenliggende beleid over. Netwerk regel verzamelingen die zijn overgenomen van een bovenliggend beleid, worden altijd in prioriteit gegeven boven netwerk regel verzamelingen die zijn gedefinieerd als onderdeel van een nieuw beleid. Dezelfde logica is ook van toepassing op toepassings regel verzamelingen. Netwerk regel verzamelingen worden echter altijd vóór de verzameling van toepassings regels verwerkt, ongeacht de overname.

De modus voor bedreigings informatie wordt ook overgenomen van het bovenliggende beleid. U kunt de modus voor bedreigings informatie instellen op een andere waarde om dit gedrag te negeren, maar u kunt het niet uitschakelen. Het is alleen mogelijk om te overschrijven met een striktere waarde. Als uw bovenliggende beleid bijvoorbeeld is ingesteld op **waarschuwing**, kunt u dit lokale beleid configureren om te **waarschuwen en weigeren**.

De NAT-regel verzamelingen worden niet overgenomen omdat deze specifiek zijn voor een bepaalde firewall.

Met overname worden alle wijzigingen in het bovenliggende beleid automatisch toegepast op het bijbehorende onderliggende firewall beleid.

## <a name="traditional-rules-and-policies"></a>Traditionele regels en beleids regels

Azure Firewall ondersteunt zowel traditionele regels als beleids regels. De volgende tabel vergelijkt beleids regels en regels:


|         |Beleid  |Regels  |
|---------|---------|---------|
|Contains     |NAT-, netwerk-, toepassings regels en instellingen voor bedreigings informatie|NAT-, netwerk-en toepassings regels |
|Komt     |Virtuele hubs en virtuele netwerken|Alleen virtuele netwerken|
|Portalervaring     |Centraal beheer met firewall Manager|Zelfstandige firewall-ervaring|
|Ondersteuning voor meerdere firewalls     |Firewall beleid is een afzonderlijke bron die kan worden gebruikt in firewalls|Hand matig regels exporteren en importeren, of beheer oplossingen van derden gebruiken |
|Prijzen     |Gefactureerd op basis van de Firewall koppeling. Zie [prijzen](#pricing).|Gratis|
|Ondersteunde implementatie mechanismen     |Portal, REST API, sjablonen, Azure PowerShell en CLI|Portal, REST API, sjablonen, Power shell en CLI. |
|Releasestatus     |Openbare Preview|Algemene beschikbaarheid|

## <a name="pricing"></a>Prijzen

Het beleid wordt in rekening gebracht op basis van Firewall koppelingen. Een beleid met nul of één firewall koppeling is gratis. Een beleid met meerdere firewall koppelingen wordt gefactureerd tegen een vast bedrag. Zie [prijzen van Azure firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het implementeren van een Azure Firewall, raadpleegt u [zelf studie: uw Cloud netwerk beveiligen met Azure firewall Manager Preview met behulp van de Azure Portal](secure-cloud-network.md).
