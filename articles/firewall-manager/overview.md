---
title: Wat is Azure Firewall Manager preview?
description: Meer informatie over Azure Firewall Manager-functies
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: df649eab1f0e2946078f8efd3cdd6ab68c3b7938
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580193"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Wat is Azure Firewall Manager preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager preview is een beveiligings beheer service die voorziet in een centraal beveiligings beleid en route beheer voor beveiligings verbindingen in de Cloud. Het werkt met [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources), een door micro soft beheerde resource waarmee u eenvoudig hub-en spoke-architecturen kunt maken. Wanneer beveiligings-en routerings beleidsregels aan een dergelijke hub zijn gekoppeld, wordt deze een *[beveiligde virtuele hub](secured-virtual-hub.md)* genoemd. 

![Firewall-Manager](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Preview-functies Azure Firewall Manager

Azure Firewall Manager-preview biedt de volgende functies:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implementatie en configuratie van centrale Azure Firewall

U kunt meerdere Azure Firewall exemplaren die verschillende Azure-regio's en-abonnementen omvatten, centraal implementeren en configureren. 

### <a name="hierarchical-policies-global-and-local"></a>Hiërarchisch beleid (globaal en lokaal)

U kunt Azure Firewall Manager-preview gebruiken om Azure Firewall beleid centraal te beheren op meerdere beveiligde virtuele hubs. Uw centrale IT-teams kunnen globale firewall beleidsregels maken voor het afdwingen van firewall beleid voor de hele organisatie. Lokaal ontworpen firewall beleid staat een DevOps self-service model toe voor een betere flexibiliteit.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Geïntegreerd met beveiliging-as-a-service van derden voor geavanceerde beveiliging

Naast Azure Firewall kunt u SECaaS-providers (Security as a Service) van derden integreren om extra netwerk beveiliging te bieden voor uw VNet-en filiaal-Internet verbindingen.

- V2I-verkeer (VNet to Internet) filteren

   - Het uitgaande virtuele netwerk verkeer filteren met uw favoriete beveiligings provider van derden.
   - Maak gebruik van geavanceerde gebruikers bewuste Internet bescherming voor uw Cloud werkbelastingen die worden uitgevoerd op Azure.

- B2I-verkeer (branch to Internet) filteren

   Maak gebruik van uw Azure-connectiviteit en wereld wijde distributie om eenvoudig filters van derden voor vertakking toe te voegen aan Internet scenario's.

Zie [Wat zijn vertrouwde beveiligings partners van Azure firewall Manager (preview)](trusted-security-partners.md) voor meer informatie over vertrouwde beveiligings providers?

### <a name="centralized-route-management"></a>Gecentraliseerd route beheer

Stuur eenvoudig verkeer naar uw beveiligde hub voor filteren en logboek registratie zonder dat u hand matig door de gebruiker gedefinieerde routes (UDR) op spoke Virtual Networks hoeft in te stellen. U kunt providers van derden gebruiken voor het filteren van B2I-verkeer (branch to Internet), naast elkaar met Azure Firewall voor Branch to VNet (B2V), VNet naar VNet (V2V) en VNet naar Internet (V2I). U kunt ook providers van derden gebruiken voor het filteren van V2I-verkeer zolang Azure Firewall niet vereist is voor B2V of V2V. 

## <a name="region-availability"></a>Regionale beschikbaarheid

De volgende regio's worden ondersteund voor de open bare Preview:

- Europa-west, Europa-noord, Frankrijk-centraal, Frankrijk-zuid, UK-zuid, UK-west
- Australië-oost, Australië-centraal, Australië-centraal 2, Australië-zuidoost
- Canada - centraal
- VS-Oost, VS-West, VS-Oost 2, Zuid-Centraal VS, VS-West 2, VS-centraal, Noord-Centraal VS, West-Centraal VS

Azure Firewall-beleid kan alleen worden gemaakt in deze regio's, maar ze kunnen worden gebruikt in verschillende regio's. U kunt bijvoorbeeld een beleid in VS West maken en dit in VS-Oost gebruiken. 

## <a name="known-issues"></a>Bekende problemen

Azure Firewall Manager preview heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
|Hand matig gemaakte centrale VNets wordt niet ondersteund|Azure Firewall Manager ondersteunt momenteel netwerken die zijn gemaakt met virtuele hubs. Het gebruik van uw eigen hand matig gemaakte hub VNet wordt nog niet ondersteund.|Gebruik voor Taan Azure Firewall-beheer met hub-en spoke-netwerken die zijn gemaakt met virtuele hubs.<br>Momenteel wordt onderzocht.
|Beperkingen van de filtering van derden|Het V2I van verkeers filters met externe providers wordt niet ondersteund met Azure Firewall B2V en V2V.|Momenteel wordt onderzocht.|
|Splitsen van verkeer wordt momenteel niet ondersteund|Het splitsen van Office 365-en Azure open bare PaaS-verkeer wordt momenteel niet ondersteund. Als zodanig is het selecteren van een provider van een derde partij voor V2I of B2I ook alle Azure open bare PaaS-en Office 365-verkeer via de partner service verzonden.|Het splitsen van verkeer op de hub wordt momenteel onderzocht.
|Eén hub per regio|U kunt niet meer dan één hub per regio hebben|Meerdere virtuele Wan's maken in een regio.|
|Basis beleid moet zich in dezelfde regio bevinden als het lokale beleid|Maak al uw lokale beleids regels in dezelfde regio als het basis beleid. U kunt nog steeds een beleid Toep assen dat is gemaakt in een van de regio's op een beveiligde hub vanuit een andere regio.|Momenteel wordt onderzocht.|

## <a name="next-steps"></a>Volgende stappen

- Overzicht van de [implementatie van Azure firewall Manager preview](deployment-overview.md) bekijken
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).