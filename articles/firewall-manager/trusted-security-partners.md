---
title: Wat zijn Azure Firewall Manager security partner providers?
description: Meer informatie over leveranciers van Azure Firewall Manager-beveiligings partners
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563604"
---
# <a name="what-are-security-partner-providers"></a>Wat zijn beveiligingspartnerproviders?

Met *aanbieders van beveiligings partners* in azure firewall Manager kunt u uw vertrouwde, eersteklas SECaaS-aanbiedingen (Security as a Service) van derden gebruiken om de Internet toegang voor uw gebruikers te beveiligen.

Met een snelle configuratie kunt u een hub met een ondersteunde beveiligings partner beveiligen en Internet verkeer van uw virtuele netwerken (VNets) of vertakkings locaties binnen een regio routeren en filteren. U kunt dit doen met geautomatiseerde route beheer zonder door de gebruiker gedefinieerde routes (Udr's) in te stellen en te beheren.

U kunt beveiligde hubs implementeren die zijn geconfigureerd met de beveiligings partner van uw keuze in meerdere Azure-regio's om connectiviteit en beveiliging voor uw gebruikers overal ter wereld in deze regio's te krijgen. Met de mogelijkheid om de aanbieding van de beveiligings partner voor Internet/SaaS-toepassings verkeer te gebruiken en Azure Firewall voor privé verkeer in de beveiligde hubs, kunt u nu beginnen met het bouwen van uw beveiligings rand op Azure die dicht bij uw wereld wijd gedistribueerde gebruikers en toepassingen ligt.

De ondersteunde beveiligings partners zijn **ZScaler**, **Check Point** (preview) en **iboss** (preview).

![Beveiligingspartnerproviders](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Belang rijke scenario's

U kunt de beveiligings partners gebruiken voor het filteren van Internet verkeer in de volgende scenario's:

- Virtual Network (VNet) naar Internet

   Maak gebruik van geavanceerde op de gebruiker gerichte internetbeveiliging voor uw cloudworkloads die in Azure worden uitgevoerd.

- Vertakking op Internet

   Maak gebruik van uw Azure-connectiviteit en wereld wijde distributie om eenvoudig NSaaS-filters van derden voor branch toe te voegen aan Internet scenario's. U kunt uw wereld wijde doorvoer netwerk en beveiligings rand maken met behulp van Azure Virtual WAN.

De volgende scenario's worden ondersteund:
- VNet/vertakking met Internet via een beveiligings partner provider en het andere verkeer (spoke to spoke, spoke to Branch, Branch to spoke) via Azure Firewall.
- VNet/vertakking naar Internet via de provider van de beveiligings partner

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Aanbevolen procedures voor het filteren van Internet verkeer in beveiligde virtuele hubs

Internet verkeer omvat doorgaans webverkeer. Maar omvat ook verkeer dat is bestemd voor SaaS-toepassingen, zoals Office 365 (O365) en open bare Azure PaaS-services zoals Azure Storage, Azure SQL, enzovoort. Hieronder vindt u best practice aanbevelingen voor het verwerken van verkeer naar deze services:

### <a name="handling-azure-paas-traffic"></a>Azure PaaS-verkeer verwerken
 
- Gebruik Azure Firewall voor beveiliging als uw verkeer voornamelijk uit Azure PaaS bestaat en de toegang tot bronnen voor uw toepassingen kan worden gefilterd met behulp van IP-adressen, FQDN-codes, service tags of FQDN-Tags.

- Gebruik een partner oplossing van een derde partij in uw hubs als uw verkeer toegang tot SaaS-toepassingen heeft of u gebruikers bewuste filters moet gebruiken (bijvoorbeeld voor uw VDI-werk belastingen (Virtual Desktop Infrastructure)) of als u geavanceerde mogelijkheden voor Internet filtering nodig hebt.

![Alle scenario's voor Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365-verkeer (O365) verwerken

In wereld wijd gedistribueerde Branch-locatie scenario's moet u Office 365-verkeer rechtstreeks door sturen naar de vertakking voordat u het resterende Internet verkeer naar uw Azure beveiligde hub verzendt.

Voor Office 365 zijn netwerk latentie en prestaties essentieel voor een succes volle gebruikers ervaring. Om deze doel stellingen te bereiken rond optimale prestaties en gebruikers ervaring, moeten klanten Office 365 direct en lokaal ESC implementeren voordat ze de rest van Internet verkeer via Azure kunnen routeren.

[Office 365-netwerk verbindings principes](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) bellen voor belang rijke Office 365-netwerk verbindingen die lokaal worden gerouteerd van de gebruikers vertakking of het mobiele apparaat en rechtstreeks via internet naar het dichtstbijzijnde micro soft-netwerk punt.

Office 365-verbindingen worden bovendien versleuteld voor privacy en gebruiken efficiënte, eigen protocollen om prestatie redenen. Dit maakt het praktisch en nadelige invloed op deze verbindingen met traditionele beveiligings oplossingen op netwerk niveau. Daarom raden klanten ten zeerste aan Office 365-verkeer rechtstreeks vanuit filialen te verzenden voordat ze het rest verkeer verzenden via Azure. Micro soft heeft een partnerschap gemaakt met verschillende providers van de SD-WAN-oplossing die zijn geïntegreerd met Azure en Office 365, en maakt het eenvoudig voor klanten om Office 365 direct en lokale Internet-groepen in te scha kelen. Zie [Hoe kan ik mijn O365-beleid instellen via Virtual WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Implementeer een beveiligings partner die wordt aangeboden in een beveiligde hub met behulp van Azure firewall Manager](deploy-trusted-security-partner.md).
