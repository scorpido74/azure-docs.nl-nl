---
title: Wat zijn de opties voor de Azure Firewall Manager-architectuur?
description: Vergelijk en contrast met behulp van hub Virtual Network of beveiligde virtuele hub-architecturen met Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444574"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Wat zijn de opties voor de Azure Firewall Manager-architectuur?

Azure Firewall Manager kan beveiligings beheer bieden voor twee typen netwerk architectuur:

- **beveiligde virtuele hub**

   Een [virtuele WAN-hub van Azure](../virtual-wan/virtual-wan-about.md#resources) is een door micro soft beheerde resource waarmee u eenvoudig hub-en spoke-architecturen kunt maken. Wanneer beveiligings-en routerings beleidsregels aan een dergelijke hub zijn gekoppeld, wordt deze aangeduid als een *[beveiligde virtuele hub](secured-virtual-hub.md)* . 
- **hub-netwerk**

   Dit is een standaard virtuele netwerk van Azure dat u zelf maakt en beheert. Wanneer het beveiligings beleid is gekoppeld aan een dergelijke hub, wordt het een *hub virtueel netwerk*genoemd. Op dit moment wordt alleen Azure Firewall beleid ondersteund. U kunt virtuele spoke-netwerken met uw werkbelasting servers en-services. U kunt ook firewalls beheren in zelfstandige virtuele netwerken die niet zijn gekoppeld aan een spoke.

## <a name="comparison"></a>Vergelijking

De volgende tabel vergelijkt deze twee architectuur opties en kan u helpen beslissen welke optie geschikt is voor de beveiligings vereisten van uw organisatie:


|  |**Hub-netwerk**|**Beveiligde virtuele hub**  |
|---------|---------|---------|
|**Onderliggende resource**     |Virtueel netwerk|Virtuele WAN-hub|
|**Hub & spoke**     |Maakt gebruik van peering op virtueel netwerk|Geautomatiseerd met hub Virtual Network-verbinding|
|**On-premises connectiviteit**     |VPN Gateway tot 10 Gbps en 30 S2S-verbindingen; ExpressRoute|Meer schaal bare VPN Gateway van 20 Gbps-en 1000 S2S-verbindingen; Express route|
|**Automatische filiaal connectiviteit met SDWAN**      |Niet ondersteund|Ondersteund|
|**Hubs per regio**     |Meerdere virtuele netwerken per regio|Eén virtuele hub per regio. Meerdere hubs mogelijk met meerdere virtuele Wan's|
|**Azure Firewall: meerdere open bare IP-adressen**      |Klant|Automatisch gegenereerd. Beschikbaar zijn door GA.|
|**Azure Firewall Beschikbaarheidszones**     |Ondersteund|Niet beschikbaar in de preview-versie. Beschikbaar zijn via GA|
|**Geavanceerde Internet beveiliging met beveiliging van derden als service partners**     |Door de klant tot stand gebrachte en beheerde VPN-verbinding met de partner service van de keuze|Geautomatiseerd via een vertrouwde beveiligings partner stroom en partner beheer ervaring|
|**Gecentraliseerd route beheer voor het routeren van verkeer naar de hub**     |Door de gebruiker gedefinieerde route door de klant|Ondersteund met behulp van BGP|
|**Web Application firewall op Application Gateway** |Ondersteund in Virtual Network|Momenteel ondersteund in spoke-netwerk|
|**Virtueel netwerk apparaat**|Ondersteund in Virtual Network|Momenteel ondersteund in spoke-netwerk|

## <a name="next-steps"></a>Volgende stappen

- Overzicht van de [implementatie van Azure firewall Manager preview](deployment-overview.md) bekijken
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).