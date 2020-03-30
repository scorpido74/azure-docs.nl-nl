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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245380"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

> [!NOTE]
> Op 31 juli 2019 is een subset van beveiligingsoplossingen met pensioen gegaan. Zie Functies van het [Pension of Security Center (juli 2019) voor](security-center-features-retirement-july2019.md#menu_solutions)meer informatie en alternatieve diensten.

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. Voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partneroplossingen. Voor oplossingen zoals antimalware en kwetsbaarheidsbeoordeling kan Security Center de agent op uw virtuele machines inrichten. Voor firewall-apparaten kan Security Center een groot deel van de vereiste netwerkconfiguratie verzorgen.
- **Geïntegreerde detecties**: Beveiligingsgebeurtenissen van partneroplossingen worden automatisch verzameld, samengevoegd en weergegeven als onderdeel van waarschuwingen en incidenten van het Beveiligingscentrum. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde statuscontrole en beheer**: klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om alle partneroplossingen in één oogopslag te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Momenteel zijn geïntegreerde beveiligingsoplossingen onder meer kwetsbaarheidsbeoordeling door [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en Microsoft Application Gateway Web-toepassingsfirewall.

> [!NOTE]
> Security Center installeert de Microsoft Monitoring Agent niet op virtuele apparaten van partners omdat de meeste beveiligingsleveranciers externe agents die op hun apparaten worden uitgevoerd, verbieden.
>
>

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U ook andere beveiligingsgegevensbronnen verbinden, waaronder computers die on-premises of in andere clouds worden uitgevoerd.

![Integratie van partneroplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Meld u aan bij [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. Ga naar het menu van **Microsoft Azure** en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend.

3. Selecteer in het menu van Security Center de optie **Beveiligingsoplossingen**.

   ![Overzicht van Security Center](./media/security-center-partner-integration/overview.png)

In **beveiligingsoplossingen**u de status van geïntegreerde Azure-beveiligingsoplossingen bekijken en basisbeheertaken uitvoeren.

### <a name="connected-solutions"></a>Verbonden oplossingen

De sectie **Verbonden oplossingen** bevat beveiligingsoplossingen die momenteel zijn verbonden met Security Center. Het toont ook de gezondheidstoestand van elke oplossing.  

![Verbonden oplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

De status van een partneroplossing kan zijn:

* Gezond (groen) - geen gezondheidsproblemen.
* Ongezond (rood) - er is een gezondheidsprobleem dat onmiddellijke aandacht vereist.
* Gezondheidsproblemen (oranje) - de oplossing is gestopt met het melden van de gezondheid.
* Niet gemeld (grijs) - de oplossing heeft nog niets gemeld en er zijn geen gezondheidsgegevens beschikbaar. De status van een oplossing kan niet worden gerapporteerd als deze onlangs is verbonden en nog steeds wordt geïmplementeerd.

> [!NOTE]
> Als er geen statusgegevens beschikbaar zijn, geeft Security Center de datum en tijd van de laatste gebeurtenis weer om aan te geven of de oplossing rapporteert of niet. Als er geen gezondheidsgegevens beschikbaar zijn en er in de afgelopen 14 dagen geen waarschuwingen zijn ontvangen, geeft Security Center aan dat de oplossing niet in orde is of niet wordt gemeld.
>
>

1. Selecteer **WEERGAVE** voor meer informatie en opties zoals:

   - **Oplossingsconsole**. Opent de managementervaring voor deze oplossing.
   - **Koppelingvm**. Hiermee opent u de pagina Koppelingstoepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Oplossing verwijderen**.
   - **Configureren**.

   ![Details van partneroplossingen](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligingsoplossingen die in Azure worden uitgevoerd, maar die niet zijn verbonden met het Beveiligingscentrum en geeft de oplossingen weer in de sectie **Gedetecteerde oplossingen.** Deze oplossingen omvatten Azure-oplossingen, zoals [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)en partneroplossingen.

> [!NOTE]
> De Standard-laag van Security Center is op abonnementsniveau vereist voor de functie Gedetecteerde oplossingen. Zie [Prijzen](security-center-pricing.md) voor meer informatie over de prijsniveaus.
>
>

Selecteer **CONNECT** onder een oplossing om te integreren met Security Center en ontvang de melding van beveiligingswaarschuwingen.

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Gegevens exporteren naar een SIEM

> [!NOTE]
> Zie [Beveiligingswaarschuwingen en aanbevelingen exporteren (Voorbeeld)](continuous-export.md)voor meer informatie over een eenvoudigere methode (momenteel in preview) voor het exporteren van gegevens naar een SIEM. De nieuwe methode gebruikt Activiteitslogboek niet als bemiddelaar en maakt directe export mogelijk van Security Center naar Event Hubs (en vervolgens naar uw SIEM), het ondersteunt ook de export van beveiligingsaanbevelingen.


U uw SM's of andere bewakingshulpprogramma's configureren om Azure Security Center-gebeurtenissen te ontvangen.

Alle gebeurtenissen van Azure Security Center worden gepubliceerd in het Azure [Activity-logboek](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)van Azure Monitor. Azure Monitor gebruikt [een geconsolideerde pijplijn](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) om de gegevens te streamen naar een gebeurtenishub, waar deze vervolgens in uw bewakingstool kan worden getrokken.

In de volgende secties wordt beschreven hoe u configureren dat gegevens naar een gebeurtenishub worden gestreamd. De stappen gaan ervan uit dat Azure Security Center al is geconfigureerd in uw Azure-abonnement.

### <a name="high-level-overview"></a>Overzicht op hoog niveau

![Overzicht op hoog niveau](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Wat zijn de Azure-beveiligingsgegevens die zijn blootgesteld aan SIEM?

In deze versie stellen we de [beveiligingswaarschuwingen bloot.](../security-center/security-center-managing-and-responding-alerts.md) In de komende releases zullen we de gegevensset verrijken met beveiligingsaanbevelingen.

### <a name="how-to-set-up-the-pipeline"></a>De pijplijn instellen

#### <a name="create-an-event-hub"></a>Een Event Hub maken

Maak voordat u begint [een naamruimte voor gebeurtenishubs](../event-hubs/event-hubs-create.md) , de bestemming voor al uw bewakingsgegevens.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Het Azure-activiteitenlogboek streamen naar gebeurtenishubs

Zie het volgende [activiteitenlogboek voor artikelstromen naar gebeurtenishubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Een SIEM-connector voor partners installeren 

Als u uw bewakingsgegevens routert naar een gebeurtenishub met Azure Monitor, u eenvoudig integreren met siem- en bewakingstools van partners.

Zie het volgende artikel voor de lijst met [ondersteunde SM's](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Voorbeeld voor het opvragen van gegevens 

Hier volgen enkele Splunk-query's die u gebruiken om waarschuwingsgegevens op te halen:

| **Beschrijving van query** | **Query** |
|----|----|
| All Alerts| index=hoofdMicrosoft.Security/locaties/waarschuwingen|
| Het aantal bewerkingen op hun naam samenvatten| index=hoofdbrontype="amal:security" \| tabel \| operationName stats count by operationName|
| Informatie over waarschuwingen ontvangen: tijd, naam, status, id en abonnement | index=hoofdMicrosoft.Security/locaties/waarschuwingen \| \_tabeltijd, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Zie het volgende artikel voor meer informatie over Beveiligingscentrum:

* [Beveiligingsstatus controleren in Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.