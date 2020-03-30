---
title: Overzicht van het azure Firewall Manager Preview-overzicht
description: Meer informatie over Azure Firewall Manager-beleidsregels
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77445019"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Overzicht van het azure Firewall Manager Preview-overzicht

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Firewallbeleid is een Azure-bron die NAT-, netwerk- en toepassingsregelverzamelingen en Threat Intelligence-instellingen bevat. Het is een globale bron die kan worden gebruikt in meerdere Azure Firewall-exemplaren in Beveiligde virtuele hubs en virtuele hubnetwerken. Beleid werkt in verschillende regio's en abonnementen.

![Azure Firewall Manager-beleid](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Beleidsvorming en -associatie

Een beleid kan op meerdere manieren worden gemaakt en beheerd, waaronder de Azure-portal, REST API, sjablonen, Azure PowerShell en CLI.

U ook bestaande regels migreren vanuit Azure Firewall met behulp van de portal of Azure PowerShell om beleid te maken. Zie [Azure Firewall-configuraties migreren naar Azure Firewall-beleid (voorbeeld) voor](migrate-to-policy.md)meer informatie. 

Beleidsregels kunnen worden gekoppeld aan een of meer virtuele hubs of VNets. De firewall kan zich in elk abonnement bevinden dat is gekoppeld aan uw account en in elke regio.

## <a name="hierarchical-policies"></a>Hiërarchisch beleid

Nieuw beleid kan vanaf nul worden gemaakt of overgenomen van bestaand beleid. Met Overerving kan DevOps lokaal firewallbeleid maken bovenop het basisbeleid van de organisatie.

Beleid dat is gemaakt met niet-lege bovenliggende beleidsregels, neemt alle regelverzamelingen over van het bovenliggende beleid. Netwerkregelverzamelingen die zijn overgenomen van een bovenliggend beleid, worden altijd geprioriteerd boven netwerkregelverzamelingen die zijn gedefinieerd als onderdeel van een nieuw beleid. Dezelfde logica geldt ook voor verzamelingen van toepassingsregels. Verzamelingen van netwerkregels worden echter altijd verwerkt vóór verzamelingen van toepassingsregels, ongeacht overerving.

De modus Bedreigingsinformatie wordt ook overgenomen van het bovenliggende beleid. U uw threat Intelligence-modus instellen op een andere waarde om dit gedrag te overschrijven, maar u deze niet uitschakelen. Het is alleen mogelijk om te overschrijven met een strengere waarde. Als uw bovenliggende beleid bijvoorbeeld is ingesteld op **alleen waarschuwen,** u dit lokale beleid configureren **op Waarschuwen en weigeren**.

NAT-regelverzamelingen worden niet overgenomen omdat ze specifiek zijn voor een bepaalde firewall.

Met overerving worden wijzigingen in het bovenliggende beleid automatisch toegepast op het bijbehorende firewallonderliggendbeleid.

## <a name="traditional-rules-and-policies"></a>Traditionele regels en beleidsmaatregelen

Azure Firewall ondersteunt zowel traditionele regels als beleidsregels. In de volgende tabel worden beleidsregels en regels vergeleken:


|         |Beleid  |Regels  |
|---------|---------|---------|
|Contains     |NAT-, netwerk-, toepassingsregels en instellingen voor bedreigingsinformatie|NAT-, netwerk- en toepassingsregels |
|Beschermt     |Virtuele hubs en virtuele netwerken|Alleen virtuele netwerken|
|Portalervaring     |Centraal beheer met Firewall Manager|Zelfstandige firewall-ervaring|
|Ondersteuning voor meerdere firewalls     |Firewallbeleid is een afzonderlijke bron die kan worden gebruikt voor alle firewalls|Handmatig export- en importregels, of met beheeroplossingen van derden |
|Prijzen     |Gefactureerd op basis van firewall associatie. Zie [Prijzen](#pricing).|Gratis|
|Ondersteunde implementatiemechanismen     |Portal, REST API, sjablonen, Azure PowerShell en CLI|Portal, REST API, sjablonen, PowerShell en CLI. |
|Releasestatus     |Openbare preview|Algemene beschikbaarheid|

## <a name="pricing"></a>Prijzen

Beleidsregels worden gefactureerd op basis van firewall-associaties. Een beleid met nul of één firewall associatie is gratis. Een beleid met meerdere firewall-associaties wordt tegen een vast tarief gefactureerd. Zie [Azure Firewall Manager Pricing](https://azure.microsoft.com/pricing/details/firewall-manager/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Uw cloudnetwerk beveiligen met Azure Firewall Manager Preview met de Azure-portal](secure-cloud-network.md)voor meer informatie over het implementeren van een Azure Firewall.
