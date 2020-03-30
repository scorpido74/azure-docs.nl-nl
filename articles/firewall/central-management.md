---
title: Centraal beheer van Azure Firewall
description: Meer informatie over centraal beheer van Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444548"
---
# <a name="azure-firewall-central-management"></a>Centraal beheer van Azure Firewall

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Als u meerdere firewalls beheert, weet u dat voortdurend veranderende firewallregels het moeilijk maken om ze synchroon te houden. Centrale IT-teams hebben een manier nodig om basisfirewallbeleid te definiëren en af te dwingen op meerdere bedrijfseenheden. Tegelijkertijd willen DevOps-teams hun eigen lokale afgeleide firewallbeleid maken voor een betere wendbaarheid.

Azure Firewall Manager Preview kan helpen deze problemen op te lossen.


## <a name="azure-firewall-manager-preview"></a>Proefversie van Azure Firewall Manager

Azure Firewall Manager Preview is een netwerkbeveiligingsbeheerservice die centraal beveiligingsbeleid en routebeheer biedt voor cloudgebaseerde beveiligingsperimeters. Het maakt het voor Enterprise IT-teams gemakkelijk om regels op netwerk- en toepassingsniveau voor verkeersfiltering in meerdere Azure Firewall-instanties centraal te definiëren. U verschillende Azure-regio's en -abonnementen omvatten in hub- en spoke-architecturen voor verkeersbeheer en -beveiliging. Het biedt DevOps ook een betere flexibiliteit met afgeleid lokaal firewallbeveiligingsbeleid dat binnen organisaties wordt geïmplementeerd.

### <a name="firewall-policy"></a>Firewallbeleid

Een Firewall-beleid is een Azure-bron die NAT-, netwerk- en toepassingsregelverzamelingen en Threat Intelligence-instellingen bevat. Het is een globale bron die kan worden gebruikt in meerdere Azure Firewall-exemplaren in *Beveiligde virtuele hubs* en virtuele *hubnetwerken.* Nieuw beleid kan vanaf nul worden gemaakt of overgenomen van bestaand beleid. Met Overerving kan DevOps lokaal firewallbeleid maken bovenop het basisbeleid van de organisatie. Beleid werkt in verschillende regio's en abonnementen.
 
U Firewallbeleid en koppelingen maken met Azure Firewall Manager. U echter ook een beleid maken en beheren met REST API, sjablonen, Azure PowerShell en CLI. Zodra u een beleid hebt gemaakt, u het koppelen aan een firewall in een virtuele WAN-hub, waardoor het een *beveiligde virtuele hub* en/of een firewall in een virtueel netwerk is, waardoor het Hub Virtual Network *is.*

### <a name="pricing"></a>Prijzen

Beleidsregels worden gefactureerd op basis van firewall-associaties. Een beleid met nul of één firewall associatie is gratis. Een beleid met meerdere firewall-associaties wordt tegen een vast tarief gefactureerd. Zie [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/)voor meer informatie.

## <a name="azure-firewall-management-partners"></a>Azure Firewall Management-partners

De volgende toonaangevende oplossingen van derden ondersteunen azure firewall centraal beheer met behulp van standaard Azure REST API's. Elk van deze oplossingen heeft zijn eigen unieke kenmerken en kenmerken:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure Firewall [Manager Preview voor](../firewall-manager/overview.md) meer informatie over Azure Firewall Manager Preview?