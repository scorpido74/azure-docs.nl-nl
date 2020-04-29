---
title: Centraal beheer van Azure Firewall
description: Meer informatie over Centraal beheer van Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444548"
---
# <a name="azure-firewall-central-management"></a>Centraal beheer van Azure Firewall

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Als u meerdere firewalls beheert, weet u dat de firewall regels voortdurend worden gewijzigd, zodat deze moeilijk te synchroniseren zijn. Centrale IT-teams hebben een manier nodig om het base firewall-beleid te definiëren en af te dwingen voor meerdere bedrijfs eenheden. Terzelfder tijd willen DevOps teams hun eigen lokale afgeleide firewall-beleid maken voor een betere flexibiliteit.

Azure Firewall Manager-voor beeld kan helpen bij het oplossen van deze problemen.


## <a name="azure-firewall-manager-preview"></a>Azure Firewall Manager-preview

Azure Firewall Manager preview is een service voor het beheren van netwerk beveiliging die het centrale beveiligings beleid en route beheer biedt voor beveiligings verbindingen in de Cloud. Het maakt het eenvoudig voor IT-teams van ondernemingen om regels van netwerk-en toepassings niveau te definiëren voor het filteren van verkeer op meerdere exemplaren van Azure Firewall. U kunt verschillende Azure-regio's en-abonnementen onderverdelen in hub-en spoke-architecturen voor verkeer governance en beveiliging. Het biedt ook DevOps betere flexibiliteit met afgeleide lokale firewall beveiligings beleidsregels die worden geïmplementeerd in verschillende organisaties.

### <a name="firewall-policy"></a>Firewall beleid

Een firewall beleid is een Azure-resource die NAT-, netwerk-en toepassings regel verzamelingen en instellingen voor bedreigings informatie bevat. Het is een wereld wijde resource die kan worden gebruikt voor meerdere Azure Firewall instanties in *beveiligde virtuele hubs* en *hub virtuele netwerken*. Nieuwe beleids regels kunnen volledig worden gemaakt of worden overgenomen van bestaande beleids regels. Met overname kan DevOps lokaal firewall beleid maken op basis van de organisatie. Beleids regels werken in verschillende regio's en abonnementen.
 
U kunt Firewall beleid en-koppelingen maken met Azure Firewall Manager. U kunt echter ook een beleid maken en beheren met behulp van REST API, sjablonen, Azure PowerShell en CLI. Wanneer u een beleid hebt gemaakt, kunt u dit koppelen aan een firewall in een virtuele WAN-hub, waardoor het een *beveiligde virtuele hub* is en/of een firewall in een virtueel netwerk, waardoor IT- *hub Virtual Network*.

### <a name="pricing"></a>Prijzen

Het beleid wordt in rekening gebracht op basis van Firewall koppelingen. Een beleid met nul of één firewall koppeling is gratis. Een beleid met meerdere firewall koppelingen wordt gefactureerd tegen een vast bedrag. Zie [prijzen van Azure firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/)voor meer informatie.

## <a name="azure-firewall-management-partners"></a>Azure Firewall beheer partners

De volgende toonaangevende oplossingen van derden ondersteunen Azure Firewall centraal beheer met behulp van standaard-Azure REST Api's. Elk van deze oplossingen heeft zijn eigen unieke kenmerken en functies:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure firewall Manager preview?](../firewall-manager/overview.md) voor meer informatie over de preview-versie van Azure firewall Manager.