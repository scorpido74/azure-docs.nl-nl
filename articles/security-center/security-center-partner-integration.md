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
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758023"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. Voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partneroplossingen. Voor oplossingen zoals antimalware en kwetsbaarheidsbeoordeling kan Security Center de agent op uw virtuele machines inrichten. Voor firewall-apparaten kan Security Center een groot deel van de vereiste netwerkconfiguratie verzorgen.
- **Geïntegreerde detecties**: Beveiligingsgebeurtenissen van partneroplossingen worden automatisch verzameld, samengevoegd en weergegeven als onderdeel van waarschuwingen en incidenten van het Beveiligingscentrum. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde statuscontrole en beheer**: klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om alle partneroplossingen in één oogopslag te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Momenteel zijn geïntegreerde beveiligingsoplossingen onder meer kwetsbaarheidsbeoordeling door [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en Microsoft Application Gateway Web-toepassingsfirewall.

> [!NOTE]
> Security Center installeert de Log Analytics-agent niet op virtuele apparaten van partners omdat de meeste beveiligingsleveranciers externe agents die op hun apparaten worden uitgevoerd, verbieden.

Zie voor meer informatie over de integratie van hulpprogramma's voor het scannen van kwetsbaarheden van Qualys, waaronder een ingebouwde scanner die beschikbaar is voor klanten in standaardlagen: 

- [Geïntegreerde kwetsbaarheidsscanner voor virtuele machines](built-in-vulnerability-assessment.md).
- [Implementeren van een oplossing voor het scannen van kwetsbaarheden van partners.](partner-vulnerability-assessment.md)

Security Center biedt ook kwetsbaarheidsanalyse voor uw:

* SQL-databases - zie [Rapporten over kwetsbaarheidsbeoordeling verkennen in het dashboard voor kwetsbaarheidsbeoordeling](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Afbeeldingen van Azure Container Registry - zie [Azure Container Registry-integratie met Security Center (Voorbeeld)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U ook andere beveiligingsgegevensbronnen verbinden, waaronder computers die on-premises of in andere clouds worden uitgevoerd.

[![Integratie van partneroplossingen](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Open **Beveiligingscentrum**via de [Azure-portal](https://azure.microsoft.com/features/azure-portal/).

1. Selecteer **beveiligingsoplossingen**in het menu van Security Center.

Op de pagina **Beveiligingsoplossingen** ziet u de status van geïntegreerde Azure-beveiligingsoplossingen en voert u basisbeheertaken uit.

### <a name="connected-solutions"></a>Verbonden oplossingen

De sectie **Verbonden oplossingen** bevat beveiligingsoplossingen die momenteel zijn verbonden met Security Center. Het toont ook de gezondheidstoestand van elke oplossing.  

![Verbonden oplossingen](./media/security-center-partner-integration/connected-solutions.png)

De status van een partneroplossing kan zijn:

* **Gezond** (groen) - geen gezondheidsproblemen.
* **Ongezond** (rood) - er is een gezondheidsprobleem dat onmiddellijke aandacht vereist.
* **Gestopt met rapporteren** (oranje) - de oplossing is gestopt met het melden van de gezondheid.
* **Niet gemeld** (grijs) - de oplossing heeft nog niets gemeld en er zijn geen gezondheidsgegevens beschikbaar. De status van een oplossing kan niet worden gerapporteerd als deze onlangs is verbonden en nog steeds wordt geïmplementeerd.

> [!NOTE]
> Als er geen statusgegevens beschikbaar zijn, geeft Security Center de datum en tijd van de laatste gebeurtenis weer om aan te geven of de oplossing rapporteert of niet. Als er geen gezondheidsgegevens beschikbaar zijn en er in de afgelopen 14 dagen geen waarschuwingen zijn ontvangen, geeft Security Center aan dat de oplossing niet in orde is of niet wordt gemeld.
>
>

Selecteer **WEERGAVE** voor meer informatie en opties zoals:

   - **Oplossingsconsole** - Opent de beheerervaring voor deze oplossing.
   - **Koppelingvm** - Hiermee opent u de pagina Koppelingstoepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Oplossing verwijderen**
   - **Configureren**

   ![Details van partneroplossingen](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligingsoplossingen die in Azure worden uitgevoerd, maar die niet zijn verbonden met het Beveiligingscentrum en geeft de oplossingen weer in de sectie **Gedetecteerde oplossingen.** Deze oplossingen omvatten Azure-oplossingen, zoals [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)en partneroplossingen.

> [!NOTE]
> De Standard-laag van Security Center is op abonnementsniveau vereist voor de functie Gedetecteerde oplossingen. Zie [Prijzen](security-center-pricing.md) voor meer informatie over de prijsniveaus.
>

Selecteer **CONNECT** onder een oplossing om te integreren met Security Center en ontvang de melding van beveiligingswaarschuwingen.

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Zie voor gerelateerde informatie de volgende artikelen:

* [Beveiligingswaarschuwingen en aanbevelingen exporteren](continuous-export.md). Meer informatie over het instellen van een integratie met Azure Sentinel of een andere SIEM.
* [Beveiligingsstatus controleren in Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.