---
title: Wat is Azure Firewall Manager preview?
description: Meer informatie over Azure Firewall Manager-functies
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366271"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Wat is Azure Firewall Manager preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager preview is een beveiligings beheer service die voorziet in een centraal beveiligings beleid en route beheer voor beveiligings verbindingen in de Cloud. 

Firewall Manager kan beveiligings beheer bieden voor twee typen netwerk architectuur:

- **beveiligde virtuele hub**

   Een [virtuele WAN-hub van Azure](../virtual-wan/virtual-wan-about.md#resources) is een door micro soft beheerde resource waarmee u eenvoudig hub-en spoke-architecturen kunt maken. Wanneer beveiligings-en routerings beleidsregels aan een dergelijke hub zijn gekoppeld, wordt deze een *[beveiligde virtuele hub](secured-virtual-hub.md)* genoemd. 
- **hub-netwerk**

   Dit is een standaard virtuele netwerk van Azure dat u zelf maakt en beheert. Wanneer het beveiligings beleid is gekoppeld aan een dergelijke hub, wordt het een *hub virtueel netwerk*genoemd. Op dit moment wordt alleen Azure Firewall beleid ondersteund. U kunt virtuele spoke-netwerken met uw werkbelasting servers en-services. U kunt ook firewalls beheren in zelfstandige virtuele netwerken die niet zijn gekoppeld aan een spoke.

Zie [Wat zijn de opties voor de Azure firewall Manager-architectuur?](vhubs-and-vnets.md)voor een gedetailleerde vergelijking van *beveiligde virtuele hub* -en *hub-netwerk* architecturen.

![Firewall-Manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Preview-functies Azure Firewall Manager

Azure Firewall Manager-preview biedt de volgende functies:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implementatie en configuratie van centrale Azure Firewall

U kunt meerdere Azure Firewall exemplaren die verschillende Azure-regio's en-abonnementen omvatten, centraal implementeren en configureren. 

### <a name="hierarchical-policies-global-and-local"></a>Hiërarchisch beleid (globaal en lokaal)

U kunt Azure Firewall Manager-preview gebruiken om Azure Firewall beleid centraal te beheren op meerdere beveiligde virtuele hubs. Uw centrale IT-teams kunnen globale firewall beleidsregels maken voor het afdwingen van firewall beleid voor de hele organisatie. Lokaal ontworpen firewall beleid staat een DevOps self-service model toe voor een betere flexibiliteit.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Geïntegreerd met beveiliging-as-a-service van derden voor geavanceerde beveiliging

Naast Azure Firewall kunt u SECaaS-providers (Security as a Service) van derden integreren om extra netwerk beveiliging te bieden voor uw VNet-en filiaal-Internet verbindingen.

Deze functie is alleen beschikbaar voor implementaties met beveiligde virtuele hub.

- V2I-verkeer (VNet to Internet) filteren

   - Het uitgaande virtuele netwerk verkeer filteren met uw favoriete beveiligings provider van derden.
   - Maak gebruik van geavanceerde gebruikers bewuste Internet bescherming voor uw Cloud werkbelastingen die worden uitgevoerd op Azure.

- B2I-verkeer (branch to Internet) filteren

   Maak gebruik van uw Azure-connectiviteit en wereld wijde distributie om eenvoudig filters van derden voor vertakking toe te voegen aan Internet scenario's.

Zie [Wat zijn vertrouwde beveiligings partners van Azure firewall Manager (preview)](trusted-security-partners.md) voor meer informatie over vertrouwde beveiligings providers?

### <a name="centralized-route-management"></a>Gecentraliseerd route beheer

Stuur eenvoudig verkeer naar uw beveiligde hub voor filteren en logboek registratie zonder dat u hand matig door de gebruiker gedefinieerde routes (UDR) op spoke Virtual Networks hoeft in te stellen. 

Deze functie is alleen beschikbaar voor implementaties met beveiligde virtuele hub.

U kunt providers van derden gebruiken voor het filteren van B2I-verkeer (branch to Internet), naast elkaar met Azure Firewall voor Branch to VNet (B2V), VNet naar VNet (V2V) en VNet naar Internet (V2I). U kunt ook providers van derden gebruiken voor het filteren van V2I-verkeer zolang Azure Firewall niet vereist is voor B2V of V2V. 

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Azure Firewall-beleid kan worden gebruikt in verschillende regio's. U kunt bijvoorbeeld een beleid in VS West maken en dit in VS-Oost gebruiken. 

## <a name="known-issues"></a>Bekende problemen

Azure Firewall Manager preview heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
|Beperkingen van de filtering van derden.|Het V2I van verkeers filters met externe providers wordt niet ondersteund met Azure Firewall B2V en V2V.|Onderzoeken|
|Het splitsen van verkeer wordt momenteel niet ondersteund.|Het splitsen van Office 365-en Azure open bare PaaS-verkeer wordt momenteel niet ondersteund. Als zodanig is het selecteren van een provider van een derde partij voor V2I of B2I ook alle Azure open bare PaaS-en Office 365-verkeer via de partner service verzonden.|Het splitsen van verkeer op de hub wordt onderzocht.
|Eén beveiligde virtuele hub per regio.|U kunt niet meer dan één beveiligde virtuele hub per regio hebben.|Meerdere virtuele Wan's maken in een regio.|
|Basis beleid moet zich in dezelfde regio bevinden als het lokale beleid.|Maak al uw lokale beleids regels in dezelfde regio als het basis beleid. U kunt nog steeds een beleid Toep assen dat is gemaakt in een van de regio's op een beveiligde hub vanuit een andere regio.|Onderzoeken|
|Communicatie tussen de hub werkt niet met een beveiligde virtuele hub|Beveiligde virtuele hub naar beveiligde virtuele hub-communicatie wordt nog niet ondersteund.|Onderzoeken|
|Alle beveiligde virtuele hubs die hetzelfde virtuele WAN delen, moeten zich in dezelfde resource groep bezitten.|Dit gedrag is vandaag afgestemd op virtuele WAN-hubs.|Maak meerdere virtuele Wan's zodat beveiligde virtuele hubs kunnen worden gemaakt in verschillende resource groepen.|
|IP-groepen worden niet ondersteund in firewall-beleid.|IP-groepen bevinden zich in de open bare preview-versie en worden momenteel alleen ondersteund met traditionele firewall regels.|De oplossing wordt uitgevoerd.
|Cloud Solution Provider (CSP)-abonnementen worden niet ondersteund.|CSP- [abonnementen](https://azure.microsoft.com/offers/ms-azr-0145p/) worden op dit moment niet ondersteund.|Onderzoeken

## <a name="next-steps"></a>Volgende stappen

- Overzicht van de [implementatie van Azure firewall Manager preview](deployment-overview.md) bekijken
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).