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
ms.openlocfilehash: 0479918ad2afa99dcd9f18d3ba81f94a3e262e9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479378"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

> [!NOTE]
> Er is een subset van beveiligings oplossingen ingetrokken op 31 juli 2019. Zie voor meer informatie en alternatieve Services de [buiten gebruiks telling van Security Center-functies (2019 juli)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. De voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partneroplossingen. Security Center kan de agent op uw virtuele machines inrichten voor oplossingen zoals antimalware en evaluatie van beveiligings problemen. Voor firewall apparaten kan Security Center een groot deel van de vereiste netwerk configuratie zijn.
- **Geïntegreerde detectie**: beveiligingsgebeurtenissen van partneroplossingen worden automatisch verzameld, samengevoegd en weergegeven als waarschuwingen en incidenten in Security Center. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde statuscontrole en beheer**: klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om alle partneroplossingen in één oogopslag te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Geïntegreerde beveiligings oplossingen omvatten momenteel de evaluatie van beveiligings problemen met [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en micro soft Application Gateway Web Application firewall.

> [!NOTE]
> Security Center installeert micro soft Monitoring Agent niet op virtuele partners van de partner, omdat de meeste beveiligings leveranciers externe agents die op hun apparaten worden uitgevoerd, verbieden.
>
>

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U kunt ook verbinding maken met andere beveiligings gegevens bronnen, waaronder computers die on-premises of in andere Clouds worden uitgevoerd.

![Integratie van partneroplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. Ga naar het menu van **Microsoft Azure** en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend.

3. Selecteer in het menu van Security Center de optie **Beveiligingsoplossingen**.

   ![Overzicht van Security Center](./media/security-center-partner-integration/overview.png)

In **beveiligings oplossingen**ziet u de status van geïntegreerde Azure-beveiligings oplossingen en voert u basis beheer taken uit.

### <a name="connected-solutions"></a>Verbonden oplossingen

De sectie **Connected Solutions** bevat beveiligings oplossingen die momenteel zijn verbonden met Security Center. Ook wordt de status van elke oplossing weer gegeven.  

![Verbonden oplossingen](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

De status van een partner oplossing kan zijn:

* In orde (groen): er zijn geen problemen met de status.
* Slecht (rood): er is een probleem met de status dat onmiddellijke aandacht vereist.
* Status problemen (oranje): de oplossing is gestopt met het rapporteren van de status.
* Niet gerapporteerd (grijs): de oplossing heeft nog niets gerapporteerd en er zijn geen status gegevens beschikbaar. De status van een oplossing kan niet worden gerapporteerd als deze onlangs is verbonden en nog steeds wordt geïmplementeerd.

> [!NOTE]
> Als de status gegevens niet beschikbaar zijn, worden Security Center de datum en tijd weer gegeven van de laatste ontvangen gebeurtenis om aan te geven of de oplossing al dan niet rapporteert. Als er geen status gegevens beschikbaar zijn en er in de afgelopen 14 dagen geen waarschuwingen zijn ontvangen, geeft Security Center aan dat de oplossing slecht is of niet wordt gerapporteerd.
>
>

1. Selecteer **weer geven** voor meer informatie en opties, zoals:

   - **Oplossings console**. Hiermee opent u de beheer ervaring voor deze oplossing.
   - **Virtuele machine koppelen**. Hiermee opent u de pagina koppelings toepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Verwijder de oplossing**.
   - **Configureren**.

   ![Details van partneroplossingen](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligings oplossingen die worden uitgevoerd in azure, maar zijn niet verbonden met Security Center en de oplossingen worden weer gegeven in de sectie **gedetecteerde oplossingen** . Deze oplossingen omvatten Azure-oplossingen, zoals [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)en partner oplossingen.

> [!NOTE]
> De Standard-laag van Security Center is op abonnementsniveau vereist voor de functie Gedetecteerde oplossingen. Bekijk de [prijzen](security-center-pricing.md) voor meer informatie over de prijs categorieën.
>
>

Selecteer **verbinding maken** onder een oplossing om te integreren met Security Center en op de hoogte te worden gesteld van beveiligings waarschuwingen.

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Gegevens exporteren naar een SIEM

> [!NOTE]
> Zie [beveiligings waarschuwingen en aanbevelingen exporteren (preview)](continuous-export.md)voor meer informatie over een eenvoudigere methode (momenteel in Preview) voor het exporteren van gegevens naar een Siem. De nieuwe methode maakt geen gebruik van een activiteiten logboek als een interactie en maakt het mogelijk om direct te exporteren van Security Center naar Event Hubs (en vervolgens naar uw SIEM), maar biedt ook ondersteuning voor het exporteren van beveiligings aanbevelingen.


U kunt uw Siem's of andere controle hulpprogramma's configureren om Azure Security Center-gebeurtenissen te ontvangen.

Alle gebeurtenissen van Azure Security Center worden gepubliceerd in het Azure- [activiteiten logboek](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)van Azure monitor. Azure Monitor gebruikt [een geconsolideerde pijp lijn](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) voor het streamen van gegevens naar een event hub, waar deze vervolgens in uw bewakings programma kan worden opgenomen.

In de volgende secties wordt beschreven hoe u gegevens kunt configureren om te streamen naar een Event Hub. Bij de stappen wordt ervan uitgegaan dat u al Azure Security Center hebt geconfigureerd in uw Azure-abonnement.

### <a name="high-level-overview"></a>Overzicht

![Overzicht op hoog niveau](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Wat zijn de Azure-beveiligings gegevens die beschikbaar zijn voor SIEM?

In deze versie worden de [beveiligings waarschuwingen](../security-center/security-center-managing-and-responding-alerts.md) weer gegeven. In aanstaande releases verrijken we de gegevensset met beveiligings aanbevelingen.

### <a name="how-to-set-up-the-pipeline"></a>De pijp lijn instellen

#### <a name="create-an-event-hub"></a>Een Event Hub maken

Voordat u begint, [maakt u een event hubs naam ruimte](../event-hubs/event-hubs-create.md) : de bestemming voor al uw bewakings gegevens.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Het Azure-activiteiten logboek streamen naar Event Hubs

Raadpleeg het volgende artikel [Stream-activiteiten logboek om te Event hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Een partner SIEM-connector installeren 

Door uw bewakings gegevens te routeren naar een event hub met Azure Monitor kunt u eenvoudig integreren met SIEM-en controle hulpprogramma's van de partner.

Zie het volgende artikel voor de lijst met [ondersteunde siem's](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Voor beeld voor het opvragen van gegevens 

Hier volgen enkele Splunk-query's die u kunt gebruiken om waarschuwings gegevens op te halen:

| **Beschrijving van de query** | **Query** |
|----|----|
| Alle waarschuwingen| index=main Microsoft.Security/locations/alerts|
| Aantal bewerkingen samenvatten op naam| index = hoofd source type = "Amal: Security" \| tabel bewerkings naam \| aantal statistieken per operationname|
| Waarschuwings gegevens ophalen: tijd, naam, status, ID en abonnement | index = hoofd versie van micro soft. Security/locations/Alerts \| tabel \_tijd, eigenschappen. eventname, status, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Zie de volgende artikelen voor meer informatie over Security Center:

* [Beveiligingsstatus controleren in Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Krijg antwoorden op veelgestelde vragen over het gebruik van Security Center.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
