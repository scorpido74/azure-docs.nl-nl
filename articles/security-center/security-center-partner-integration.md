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
ms.openlocfilehash: ce8aa0229d9a9b873a489209065ada588adbffff
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257745"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

> [!NOTE]
> Er is een subset van beveiligings oplossingen ingetrokken op 31 juli 2019. Zie voor meer informatie en alternatieve Services de [buiten gebruiks telling van Security Center-functies (2019 juli)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. Voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partner oplossingen. Met Security Center kan op virtuele machines de benodigde agent worden ingericht voor oplossingen zoals het beoordelen van antimalware en beveiligingsproblemen. Voor firewallapparaten kan in Security Center een groot gedeelte van de vereiste netwerkconfiguratie worden uitgevoerd.
- **Geïntegreerde detecties**: Beveiligingsgebeurtenissen van partneroplossingen worden automatisch verzameld, samengevoegd en weergegeven als meldingen en incidenten in Security Center. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde status controle en-beheer**: Klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om in één oogopslag alle partneroplossingen te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Geïntegreerde beveiligings oplossingen omvatten momenteel de evaluatie van beveiligings problemen met [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en micro soft Application Gateway Web Application firewall.

> [!NOTE]
> Security Center installeert de Microsoft Monitoring Agent niet op virtuele apparaten van partners omdat de meeste leveranciers van beveiligingsoplossingen het niet toestaan dat externe agents worden uitgevoerd op hun apparaat.
>
>

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U kunt ook verbinding maken met andere beveiligings gegevens bronnen, waaronder computers die on-premises of in andere Clouds worden uitgevoerd.

![Integratie van partneroplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Meld u aan bij [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. Ga naar het menu van **Microsoft Azure** en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend.

3. Selecteer in het menu van Security Center de optie **Beveiligingsoplossingen**.

   ![Overzicht van Security Center](./media/security-center-partner-integration/overview.png)

Bij **beveiligingsoplossingen** kunt u informatie bekijken over de status van de geïntegreerde Azure-beveiligingsoplossingen en algemene beheertaken uitvoeren.

### <a name="connected-solutions"></a>Verbonden oplossingen

Het gedeelte **Verbonden oplossingen** bevat beveiligingsoplossingen die momenteel zijn verbonden met Security Center en informatie over de status van elke oplossing.  

![Verbonden oplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

De status van een partner oplossing kan zijn:

* In orde (groen): er is geen status probleem.
* Niet in orde (rood): er is een probleem met de status waarvoor onmiddellijke aandacht is vereist.
* Status problemen (oranje): de oplossing is gestopt met het rapporteren van de status.
* Niet gerapporteerd (grijs): de oplossing heeft nog niets gerapporteerd, de status van een oplossing kan niet worden weer gegeven als deze onlangs is verbonden en nog steeds wordt geïmplementeerd, of er zijn geen status gegevens beschikbaar.

> [!NOTE]
> Als de status gegevens niet beschikbaar zijn, worden Security Center de datum en tijd weer gegeven van de laatste ontvangen gebeurtenis om aan te geven of de oplossing al dan niet rapporteert. Als er geen status gegevens beschikbaar zijn en er geen waarschuwingen worden ontvangen in de afgelopen 14 dagen, geeft Security Center aan dat de oplossing een slechte status heeft of niet rapporteert.
>
>

1. Selecteer **weer geven** voor meer informatie en opties, waaronder:

   - **Oplossings console**. Hiermee opent u de beheer ervaring voor deze oplossing.
   - **Virtuele machine koppelen**. Hiermee opent u de Blade koppelings toepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Verwijder de oplossing**.
   - **Configureren**.

   ![Details van partneroplossingen](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligingsoplossingen die worden uitgevoerd in Azure maar niet zijn verbonden met Security Center, en geeft deze oplossingen weer in het gedeelte **Gedetecteerde oplossingen**. Hierbij gaat het zowel om Azure-oplossingen, zoals [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), als om partneroplossingen.

> [!NOTE]
> De Standard-laag van Security Center is op abonnementsniveau vereist voor de functie Gedetecteerde oplossingen. Ga naar [deze pagina](security-center-pricing.md) voor meer informatie over de prijzen van Security Center.
>
>

Selecteer **CONNECT** onder een oplossing om met Security Center te integreren en meldingen over beveiligingswaarschuwingen te ontvangen.

![Gedetecteerde oplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Gegevens exporteren naar een SIEM

Verwerkte gebeurtenissen die zijn geproduceerd door Azure Security Center worden gepubliceerd in het Azure- [activiteiten logboek](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), een van de logboek typen die beschikbaar zijn via Azure monitor. Azure Monitor biedt een geconsolideerde pijp lijn voor de route ring van uw bewakings gegevens in een SIEM-hulp programma. Dit wordt gedaan door het streamen van gegevens naar een event hub, waar deze vervolgens kunnen worden opgehaald in een partner programma.

Deze pipe maakt gebruik van de [enkelvoudige pijp lijn van Azure monitoring](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) om toegang te krijgen tot de bewakings gegevens van uw Azure-omgeving. Zo kunt u eenvoudig Siem's-en controle hulpprogramma's instellen om de gegevens te verbruiken.

In de volgende secties wordt beschreven hoe u gegevens kunt configureren om te streamen naar een Event Hub. Bij de stappen wordt ervan uitgegaan dat u al Azure Security Center hebt geconfigureerd in uw Azure-abonnement.

Overzicht

![Overzicht op hoog niveau](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Wat zijn de Azure-beveiligings gegevens die beschikbaar zijn voor SIEM?

In deze versie stellen we de [beveiligings waarschuwingen beschikbaar.](../security-center/security-center-managing-and-responding-alerts.md) In aanstaande releases verrijken we de gegevensset met beveiligings aanbevelingen.

### <a name="how-to-setup-the-pipeline"></a>De pijp lijn instellen

#### <a name="create-an-event-hub"></a>Een Event Hub maken

Voordat u begint, moet u [een event hubs naam ruimte maken](../event-hubs/event-hubs-create.md). Deze naam ruimte en Event hub is het doel voor al uw bewakings gegevens.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Het Azure-activiteiten logboek streamen naar Event Hubs

Raadpleeg het volgende artikel stream- [activiteiten logboek naar Event hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Een partner SIEM-connector installeren 

Door uw bewakings gegevens te routeren naar een event hub met Azure Monitor kunt u eenvoudig integreren met SIEM-en controle hulpprogramma's van de partner.

Raadpleeg de volgende koppeling voor een overzicht van de [ondersteunde siem's](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Voor beeld voor het opvragen van gegevens 

Hier volgt een aantal Splunk-query's die u kunt gebruiken om waarschuwings gegevens op te halen:

| **Beschrijving van de query** | **Query** |
|----|----|
| Alle waarschuwingen| index=main Microsoft.Security/locations/alerts|
| Aantal bewerkingen samenvatten op naam| index = hoofd source type = "Amal: Security \| " tabel \| bewerkings naam statistieken per operationname|
| Waarschuwings gegevens ophalen: Tijd, naam, status, ID en abonnement | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Zie de volgende artikelen voor meer informatie over Security Center:

* [Beveiligingsstatus controleren in Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Krijg antwoorden op veelgestelde vragen over het gebruik van Security Center.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
