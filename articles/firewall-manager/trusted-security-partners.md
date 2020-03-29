---
title: Wat zijn azure Firewall Manager vertrouwde beveiligingspartners (voorbeeld)
description: Meer informatie over vertrouwde beveiligingspartners van Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436779"
---
# <a name="what-are-trusted-security-partners-preview"></a>Wat zijn vertrouwde beveiligingspartners (preview)?

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Met vertrouwde beveiligingspartners (preview)* in Azure Firewall Manager u uw vertrouwde, best-in-breed-, beveiligings-as-a-service-aanbiedingen (SECaaS) gebruiken om internettoegang voor uw gebruikers te beschermen.

Met een snelle configuratie u een hub beveiligen met een ondersteunde beveiligingspartner en internetverkeer routeren en filteren vanaf uw Virtuele Netwerken (VNets) of branchlocaties binnen een regio. Dit gebeurt met behulp van geautomatiseerd routebeheer, zonder het instellen en beheren van User Defined Routes (UDR's).

U beveiligde hubs implementeren die zijn geconfigureerd met de beveiligingspartner van uw keuze in meerdere Azure-regio's om connectiviteit en beveiliging voor uw gebruikers overal ter wereld in die regio's te krijgen. Met de mogelijkheid om het aanbod van de beveiligingspartner te gebruiken voor internet/SaaS-toepassingsverkeer en Azure Firewall voor privéverkeer in de beveiligde hubs, u nu beginnen met het bouwen van uw beveiligingsrand op Azure die dicht bij uw wereldwijd gedistribueerde gebruikers en toepassingen ligt.

Voor deze preview zijn de ondersteunde beveiligingspartners **ZScaler** en **iboss.** Ondersteunde regio's zijn WestCentralUS, NorthCentralUS, WestUS, WestUS2 en EastUS.

![Vertrouwde beveiligingspartners](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Belangrijkste scenario's

U de beveiligingspartners gebruiken om internetverkeer te filteren in volgende scenario's:

- Virtueel netwerk (VNet) naar internet

   Maak gebruik van geavanceerde internetbeveiliging die door de gebruiker is ingeschakeld voor uw cloudworkloads die op Azure worden uitgevoerd.

- Vertakking naar internet

   Maak gebruik van uw Azure-connectiviteit en wereldwijde distributie om eenvoudig NSaaS-filtering van derden toe te voegen voor branch- aan-internetscenario's. U uw wereldwijde transitnetwerk en beveiligingsrand bouwen met Azure Virtual WAN.

De volgende scenario's worden ondersteund:
-   VNet naar internet via een partneraanbod van derden.
-   Vertakking naar internet via een partneraanbod van derden.
-   Vertakking naar internet via een partneraanbod van derden, de rest van het privéverkeer (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) via Azure Firewall.

Het volgende scenario wordt niet ondersteund:

- VNet naar internet via een partneraanbod kan niet worden gecombineerd met Azure Firewall voor privéverkeer. Zie de volgende beperkingen.

## <a name="current-limitations"></a>Huidige beperkingen

- Voor VNet naar internet u het toevoegen van Azure Firewall voor privéverkeer en een partneraanbod voor internetverkeer niet mengen. U internetverkeer verzenden naar Azure Firewall of een beveiligingspartner van derden die deze aanbiedt in de beveiligde virtuele hub, maar niet beide. 
- U hoogstens één beveiligingspartner per virtuele hub implementeren. Als u de provider moet wijzigen, moet u de bestaande partner verwijderen en een nieuwe partner toevoegen.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Aanbevolen procedures voor internetverkeer filteren in beveiligde virtuele hubs

Internetverkeer omvat meestal webverkeer. Maar het omvat ook verkeer dat is bestemd voor SaaS-toepassingen zoals Office 365 (O365) en Azure public PaaS-services zoals Azure Storage, Azure Sql, enzovoort. Hieronder volgen aanbevelingen voor beste praktijken voor het afhandelen van verkeer naar deze services:

### <a name="handling-azure-paas-traffic"></a>Azure PaaS-verkeer verwerken
 
- Gebruik Azure Firewall voor bescherming als uw verkeer meestal uit Azure PaaS bestaat en de brontoegang voor uw toepassingen kan worden gefilterd met IP-adressen, FQDN's, Servicetags of FQDN-tags.

- Gebruik een partneroplossing van derden in uw hubs als uw verkeer bestaat uit Toegang tot SaaS-toepassingen of als u gebruikersbewuste filtering nodig hebt (bijvoorbeeld voor uw VDI-workloads (virtual desktop infrastructure) of als u geavanceerde internetfiltermogelijkheden nodig hebt.

![Alle scenario's voor Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365-verkeer (O365) verwerken

In wereldwijd gedistribueerde locatiescenario's voor filialen moet u Office 365-verkeer rechtstreeks naar de vestiging leiden voordat u het resterende internetverkeer verzendt met uw Azure beveiligde hub.

Voor Office 365 zijn netwerklatentie en prestaties essentieel voor een succesvolle gebruikerservaring. Om deze doelen te bereiken rond optimale prestaties en gebruikerservaring, moeten klanten Office 365 direct en lokaal escape implementeren voordat ze overwegen om de rest van het internetverkeer door Azure te routeren.

[In de principes voor netwerkconnectiviteit in Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) wordt opgeroepen om de belangrijkste Office 365-netwerkverbindingen lokaal te routeren vanaf de gebruikersvestiging of het mobiele apparaat en rechtstreeks via internet naar het dichtstbijzijnde Microsoft-netwerkpunt.

Bovendien zijn Office 365-verbindingen sterk versleuteld om privacy en gebruiken ze efficiënte, gepatenteerde protocollen om prestatieredenen. Dit maakt het onpraktisch en impactvol om deze verbindingen te onderwerpen aan traditionele beveiligingsoplossingen op netwerkniveau. Om deze redenen raden we klanten ten zeerste aan office 365-verkeer rechtstreeks vanuit filialen te verzenden, voordat ze de rest van het verkeer via Azure verzenden. Microsoft werkt samen met verschillende SD-WAN-oplossingsproviders, die integreren met Azure en Office 365 en het klanten gemakkelijk maken om directe en lokale internetbreakouts van Office 365 in te schakelen. Zie [Hoe stel ik mijn O365-beleid in via Virtual WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Volgende stappen

[Implementeer een vertrouwd beveiligingsaanbod in een beveiligde hub met Azure Firewall Manager.](deploy-trusted-security-partner.md)
