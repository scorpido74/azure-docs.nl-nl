---
title: Beveiligingsoplossingen integreren in Azure Security Center | Microsoft Docs
description: Leer hoe Azure Security Center kan worden geïntegreerd met partners om de algehele beveiliging van uw Azure-resources te verbeteren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758023"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. Voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partneroplossingen. Security Center kan de agent op uw virtuele machines inrichten voor oplossingen zoals antimalware en evaluatie van beveiligings problemen. Voor firewall apparaten kan Security Center een groot deel van de vereiste netwerk configuratie zijn.
- **Geïntegreerde detecties**: beveiligings gebeurtenissen van partner oplossingen worden automatisch verzameld, geaggregeerd en weer gegeven als onderdeel van Security Center waarschuwingen en incidenten. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde statuscontrole en beheer**: klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om alle partneroplossingen in één oogopslag te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Geïntegreerde beveiligings oplossingen omvatten momenteel de evaluatie van beveiligings problemen met [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en micro soft Application Gateway Web Application firewall.

> [!NOTE]
> Security Center installeert de Log Analytics agent niet op virtualisatie apparaten van de partner, omdat de meeste beveiligings leveranciers externe agents die op hun apparaten worden uitgevoerd, niet verbieden.

Voor meer informatie over de integratie van hulpprogram ma's voor het scannen van beveiligings problemen van Qualys, met inbegrip van een ingebouwde scanner die beschikbaar is voor klanten met een Standard-laag, raadpleegt u: 

- [Geïntegreerde scanner voor beveiligings problemen voor virtuele machines](built-in-vulnerability-assessment.md).
- [Het implementeren van een oplossing voor het scannen van een partner probleem](partner-vulnerability-assessment.md).

Security Center biedt ook een beveiligingslek met betrekking tot de analyse van uw:

* SQL-data bases-Zie [evaluatie van beveiligings problemen analyseren in het dash board evaluatie van beveiligings problemen](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure Container Registry-installatie kopieën-Zie [Azure container Registry integratie met Security Center (preview-versie)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U kunt ook verbinding maken met andere beveiligings gegevens bronnen, waaronder computers die on-premises of in andere Clouds worden uitgevoerd.

[![Integratie van partneroplossingen](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Open **Security Center**vanuit het [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Selecteer in het menu van Security Center **beveiligings oplossingen**.

Op de pagina **beveiligings oplossingen** kunt u de status van geïntegreerde Azure-beveiligings oplossingen bekijken en basis beheer taken uitvoeren.

### <a name="connected-solutions"></a>Verbonden oplossingen

De sectie **Connected Solutions** bevat beveiligings oplossingen die momenteel zijn verbonden met Security Center. Ook wordt de status van elke oplossing weer gegeven.  

![Verbonden oplossingen](./media/security-center-partner-integration/connected-solutions.png)

De status van een partner oplossing kan zijn:

* **In orde** (groen): er zijn geen problemen met de status.
* **Slecht** (rood): er is een probleem met de status dat onmiddellijke aandacht vereist.
* **Rapportage gestopt** (oranje): de oplossing is gestopt met het rapporteren van de status.
* **Niet gerapporteerd** (grijs): de oplossing heeft nog niets gerapporteerd en er zijn geen status gegevens beschikbaar. De status van een oplossing kan niet worden gerapporteerd als deze onlangs is verbonden en nog steeds wordt geïmplementeerd.

> [!NOTE]
> Als de status gegevens niet beschikbaar zijn, worden Security Center de datum en tijd weer gegeven van de laatste ontvangen gebeurtenis om aan te geven of de oplossing al dan niet rapporteert. Als er geen status gegevens beschikbaar zijn en er in de afgelopen 14 dagen geen waarschuwingen zijn ontvangen, geeft Security Center aan dat de oplossing slecht is of niet wordt gerapporteerd.
>
>

Selecteer **weer geven** voor meer informatie en opties, zoals:

   - **Solution console** : Hiermee opent u de beheer ervaring voor deze oplossing.
   - **Koppelings-VM** : Hiermee opent u de pagina koppelings toepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Oplossing verwijderen**
   - **Configureren**

   ![Details van partneroplossingen](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligings oplossingen die worden uitgevoerd in azure, maar zijn niet verbonden met Security Center en de oplossingen worden weer gegeven in de sectie **gedetecteerde oplossingen** . Deze oplossingen omvatten Azure-oplossingen, zoals [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)en partner oplossingen.

> [!NOTE]
> De Standard-laag van Security Center is op abonnementsniveau vereist voor de functie Gedetecteerde oplossingen. Bekijk de [prijzen](security-center-pricing.md) voor meer informatie over de prijs categorieën.
>

Selecteer **verbinding maken** onder een oplossing om te integreren met Security Center en op de hoogte te worden gesteld van beveiligings waarschuwingen.

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Raadpleeg de volgende artikelen voor verwante informatie:

* [Beveiligings waarschuwingen en aanbevelingen exporteren](continuous-export.md). Meer informatie over het instellen van een integratie met Azure Sentinel of andere SIEM.
* [Beveiligingsstatus controleren in Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.