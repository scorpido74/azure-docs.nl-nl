---
title: Wat zijn vertrouwde beveiligings partners van Azure Firewall Manager (preview-versie)
description: Meer informatie over vertrouwde beveiligings partners van Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: cb8a2fdd14cfa7d361e3d78a64f3aaf60ea7676d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468565"
---
# <a name="what-are-trusted-security-partners-preview"></a>Wat zijn vertrouwde beveiligings partners (preview)?

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Met *vertrouwde beveiligings partners (preview)* in azure firewall Manager kunt u uw vertrouwde, eersteklas SECaaS-aanbiedingen (Security as a Service) van derden gebruiken om de Internet toegang voor uw gebruikers te beveiligen.

Met een snelle configuratie kunt u een hub met een ondersteunde beveiligings partner beveiligen en Internet verkeer van uw virtuele netwerken (VNets) of vertakkings locaties binnen een regio routeren en filteren. Dit wordt gedaan met behulp van geautomatiseerde route beheer zonder door de gebruiker gedefinieerde routes (Udr's) in te stellen en te beheren.

U kunt beveiligde hubs implementeren die zijn geconfigureerd met de beveiligings partner van uw keuze in meerdere Azure-regio's om connectiviteit en beveiliging voor uw gebruikers overal ter wereld in deze regio's te krijgen. Met de mogelijkheid om de aanbieding van de beveiligings partner voor Internet/SaaS-toepassings verkeer te gebruiken en Azure Firewall voor privé verkeer in de beveiligde hubs, kunt u nu beginnen met het bouwen van uw beveiligings rand op Azure die dicht bij uw wereld wijd gedistribueerde gebruikers ligt en toepassingen.

Voor deze preview zijn de ondersteunde beveiligings partners **ZScaler** en **iboss**. Ondersteunde regio's zijn WestCentralUS, NorthCentralUS, Westus, WestUS2 en Oost-Timor.

![Vertrouwde beveiligings partners](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Kernscenario's

U kunt de beveiligings partners gebruiken voor het filteren van Internet verkeer in de volgende scenario's:

- Virtual Network (VNet) naar Internet

   Maak gebruik van geavanceerde gebruikers bewuste Internet bescherming voor uw Cloud werkbelastingen die worden uitgevoerd op Azure.

- Vertakking op Internet

   Maak gebruik van uw Azure-connectiviteit en wereld wijde distributie om eenvoudig NSaaS-filters van derden voor branch toe te voegen aan Internet scenario's. U kunt uw wereld wijde doorvoer netwerk en beveiligings rand maken met behulp van Azure Virtual WAN.

De volgende scenario's worden ondersteund:
-   VNet met Internet via een partner van derden.
-   Vertakking met Internet via een partner van een derde partij.
-   Vertakking met Internet via een partner aanbieding van derden, de rest van het privé verkeer (spoke-to-spoke, spoke-to-branches, vertakking-naar-spoke) via Azure Firewall.

Het volgende scenario wordt niet ondersteund:

- VNet op Internet via een partner aanbieding kan niet worden gecombineerd met Azure Firewall voor privé verkeer. Zie de volgende beperkingen.

## <a name="current-limitations"></a>Huidige beperkingen

- Voor VNet op internet kunt u het toevoegen van Azure Firewall niet combi neren voor privé verkeer en een partner aanbod voor Internet verkeer. U kunt Internet verkeer naar Azure Firewall of een beveiligings partner van derden verzenden in de beveiligde virtuele hub, maar niet beide. 
- U kunt Maxi maal één beveiligings partner per virtuele hub implementeren. Als u de provider moet wijzigen, moet u de bestaande partner verwijderen en een nieuwe toevoegen.

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

Office 365-verbindingen worden bovendien sterk versleuteld voor privacy en gebruiken efficiënte, eigen protocollen om prestatie redenen. Dit maakt het praktisch en nadelige invloed op deze verbindingen met traditionele beveiligings oplossingen op netwerk niveau. Daarom raden klanten ten zeerste aan Office 365-verkeer rechtstreeks vanuit filialen te verzenden voordat ze het rest verkeer verzenden via Azure. Micro soft heeft een partnerschap gemaakt met verschillende providers van de SD-WAN-oplossing die zijn geïntegreerd met Azure en Office 365, en maakt het eenvoudig voor klanten om Office 365 direct en lokale Internet-groepen in te scha kelen. Zie [Hoe kan ik mijn O365-beleid instellen via Virtual WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview#how-do-i-set-my-o365-policies-via-virtual-wan) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

[Implementeer een vertrouwde beveiligings aanbieding in een beveiligde hub met behulp van Azure firewall Manager](deploy-trusted-security-partner.md).
