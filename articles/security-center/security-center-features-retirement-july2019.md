---
title: Kenmerken van het Security Center (juli 2019) | Microsoft Documenten
description: In dit artikel worden de functies in Security Center beschreven die op 31 juli 2019 zijn uitgeschakeld.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583221"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Functies van Security Center met pensioen (juli 2019)

> [!NOTE]
> In dit document wordt de lijst met functies beschreven die op 31 juli 2019 zijn verwijderd uit Azure Security Center.
>
>

In de zes maanden voorafgaand aan juli 2019 hebben we verschillende [verbeteringen](https://azure.microsoft.com/updates/?product=security-center) aangebracht in Azure Security Center.
Met deze verbeterde mogelijkheden hebben we op 31 juli 2019 een aantal redundante functies en gerelateerde API's uit Security Center verwijderd.

De meeste van deze functies kunnen worden vervangen door andere functionaliteit in Azure Security Center of Azure Log Analytics. Andere functies kunnen worden geïmplementeerd met [Azure Sentinel (preview).](https://azure.microsoft.com/services/azure-sentinel/)

Functies van het gepensioneerde beveiligingscentrum zijn onder andere:

- [Dashboard Gebeurtenissen](#menu_events)
- [Menu-invoer zoeken](#menu_search)
- [Klassieke link Identity & Access bekijken op identiteit en toegang (voorbeeld)](#menu_classicidentity)
- [Kaartkaart voor beveiligingsgebeurtenissen op de kaart Beveiligingswaarschuwingen (voorbeeld)](#menu_securityeventsmap)
- [Aangepaste waarschuwingsregels (voorbeeld)](#menu_customalerts)
- [Knop Onderzoeken in beveiligingswaarschuwingen voor bedreigingsbeveiliging](#menu_investigate)
- [Een subset van beveiligingsoplossingen](#menu_solutions)
- [Beveiligingsconfiguraties voor beveiligingsbeleid bewerken](#menu_securityconfigurations)
- [Beveiligings- en auditdashboard (oorspronkelijk gebruikt in OMS-portal) voor Log Analytics-werkruimten](#menu_securityomsdashboard)

In dit artikel vindt u gedetailleerde informatie over elke gepensioneerde functie en de stappen die u nemen om vervangende functies te implementeren.

## <a name="events-dashboard"></a>Dashboard Gebeurtenissen<a name="menu_events"></a>

Security Center gebruikt log analytics-agent om verschillende beveiligingsgerelateerde configuraties en gebeurtenissen van uw machines te verzamelen. Het slaat deze gebeurtenissen op in uw werkruimten. Met [het evenementendashboard](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) u deze gegevens bekijken en u een toegangspunt geven voor Log Analytics.

We hebben het evenementendashboard dat is weergegeven toen u een werkruimte hebt geselecteerd, buiten gebruik gesteld:

![Dashboard Gebeurtenissen][2]

### <a name="events-dashboard---the-new-experience"></a>Evenementen dashboard - de nieuwe ervaring

We hebben u aangemoedigd om de native mogelijkheden van Azure Log Analytics te gebruiken om opmerkelijke gebeurtenissen op uw werkruimten te bekijken.

Als u aangepaste opmerkelijke gebeurtenissen hebt gemaakt in Beveiligingscentrum, zijn deze toegankelijk. Ga in Logboekanalyse naar **Opgeslagen zoekopdrachten voor werkruimte** > **selecteren**. Uw gegevens worden niet verloren of gewijzigd. Native opmerkelijke gebeurtenissen zijn ook beschikbaar vanaf hetzelfde scherm in Log Analytics.

![Op zoeknaar in werkruimte opgeslagen][3]

## <a name="search-menu-entry"></a>Menu-invoer zoeken<a name="menu_search"></a>

Azure Security Center gebruikt momenteel Azure Monitor-logboeken om uw beveiligingsgegevens op te halen en te analyseren. Dit scherm fungeert als een venster op de zoekpagina Log Analytics en stelt gebruikers in staat om zoekopdrachten uit te voeren op hun geselecteerde werkruimte. Zie [Zoeken in Azure Security Center voor](https://docs.microsoft.com/azure/security-center/security-center-search)meer informatie . We hebben dit zoekvenster met pensioen gehaald:

![Zoekpagina][4]

### <a name="search-menu-entry---the-new-experience"></a>Zoekmenu-vermelding - de nieuwe ervaring

We raden u aan de native mogelijkheden van Azure Log Analytics te gebruiken om zoekopdrachten op uw werkruimten uit te voeren. Ga naar Azure Log Analytics en selecteer **Logboeken**.

![Logboekanalyselogboekenpagina][5]

## <a name="classic-identity--access-preview"></a>Toegang voor klassieke identiteit & (voorbeeld)<a name="menu_classicidentity"></a>

De Ervaring Classic Identity & Access in Security Center toont momenteel een dashboard met identiteits- en toegangsgegevens in Log Analytics. Ga als volgt te werk om dit dashboard te bekijken:

1. Selecteer **Klassieke identiteits- & Access weergeven**.

   ![Identiteitspagina][6]

1. Bekijk het **dashboard Identity & Access**.

    ![Identiteitspagina - selectie van werkruimtes][7]

1. Selecteer een werkruimte die het dashboard **Identiteit & Toegang** in Log Analytics opent om identiteit en toegang tot informatie in uw werkruimte weer te geven.

   ![Identiteitspagina - dashboard][8]

We hebben alle drie de schermen in de voorgaande stappen met pensioen laten gaan. Uw gegevens blijven beschikbaar in de beveiligingsoplossing Log Analytics en zijn niet gewijzigd of verwijderd.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) - de nieuwe ervaring

Het dashboard Log Analytics heeft inzichten weergegeven op één werkruimte. Native Security Center-mogelijkheden bieden echter inzicht in alle abonnementen en alle werkruimten die eraan zijn gekoppeld. U hebt toegang tot een gebruiksvriendelijke weergave waarmee u zich concentreren op wat belangrijk is met aanbevelingen die zijn gerangschikt op basis van hun beveiligde score.

Alle functies van het **dashboard Identity & Access** in Log Analytics kunnen worden bereikt door Identiteit & access **(Preview)** te selecteren in het Beveiligingscentrum.

![Identiteitspagina - klassieke ervaring pensioen][9]

## <a name="security-events-map"></a>Kaart voor beveiligingsgebeurtenissen<a name="menu_securityeventsmap"></a>

Security Center biedt u een beveiligingswaarschuwingen kaart om te helpen bij het identificeren van beveiligingsrisico's. Met de **kaartkaart Naar beveiligingsgebeurtenissen** in die kaart opent u een dashboard waarmee u onbewerkte beveiligingsgebeurtenissen op de geselecteerde werkruimte weergeven.

We hebben de **kaartknop Naar beveiligingsgebeurtenissen en** het dashboard per werkruimte verwijderd.

![Kaart voor beveiligingswaarschuwingen - knop][10]

Wanneer u de **kaartkaart Voor beveiligingsgebeurtenissen ga** selecteert, wordt het dashboard voor bedreigingsinformatie (nu met pensioen) geopend.

![Het dashboard Bedreigingsinformatie][11]

Wanneer u een werkruimte kiest om het dashboard voor bedreigingsinformatie weer te geven, hebt u het scherm (nu gepensioneerde) beveiligingswaarschuwingen geopend in Log Analytics.

![Beveiligingswaarschuwingen worden toegewezen in Log Analytics][12]

Uw bestaande gegevens blijven beschikbaar in de beveiligingsoplossing log Analytics en zijn niet gewijzigd of verwijderd.

### <a name="security-events-map---the-new-experience"></a>Kaart van beveiligingsgebeurtenissen - de nieuwe ervaring

We raden u aan de functionaliteit van de waarschuwingskaart te gebruiken die is ingebouwd in het beveiligingscentrum: **de kaart met beveiligingswaarschuwingen (Voorbeeld).** Deze functionaliteit biedt een geoptimaliseerde ervaring en werkt voor alle abonnementen en bijbehorende werkruimten. Het geeft u een overzicht op hoog niveau in uw omgeving en is niet gericht op één werkruimte.

## <a name="custom-alert-rules-preview"></a>Aangepaste waarschuwingsregels (voorbeeld)<a name="menu_customalerts"></a>

We [hebben de ervaring met aangepaste waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) op 30 juni 2019 met pensioen laten gaan omdat de onderliggende infrastructuur met pensioen is gegaan. Na de pensioendatum worden aangepaste beveiligingswaarschuwingen niet meer gegenereerd.
We raden u aan [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) in te schakelen en daar uw aangepaste waarschuwingen opnieuw te maken. U ook uw waarschuwingen maken met Azure Monitor-logboekwaarschuwingen.

Aangepaste waarschuwingen maken met Azure Sentinel:

1. [Azure Sentinel openen](https://portal.azure.com/#create/Microsoft.ASI/preview) en de werkruimte selecteren waar uw aangepaste waarschuwingen zijn opgeslagen
1. **Analytics selecteren** in het menu
1. Instructies volgen in de volgende [zelfstudie](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) over het maken van aangepaste waarschuwingen in Azure Sentinel

Als u geen Azure Sentinel wilt gebruiken, u uw waarschuwingen maken met Azure Monitor-logboekwaarschuwingen. Zie [Logboekwaarschuwingen maken, weergeven en beheren met Azure Monitor-](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) en [Logwaarschuwingen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)voor instructies.

![Aangepaste waarschuwingen][13]

Zie [Aangepaste waarschuwingsregels in Azure Security Center (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)voor meer informatie over het pensioen met aangepaste waarschuwingen.

## <a name="security-alerts-investigation"></a>Beveiligingswaarschuwingen onderzoek<a name="menu_investigate"></a>

De functie Onderzoek in Security Center helpt u bij het triage een mogelijk beveiligingsincident. Met de functie u de omvang van een incident begrijpen en de oorzaak ervan opsporen. We hebben deze functie uit het Beveiligingscentrum verwijderd omdat deze is vervangen door een verbeterde ervaring in [Azure Sentinel.](https://azure.microsoft.com/services/azure-sentinel/)

![Beveiligingsincident][14]

Wanneer u de knop **Onderzoeken** selecteert in een scherm **met beveiligingsincidenten,** opent u het onderzoeksdashboard (Voorbeeld) in Log Analytics. We hebben het onderzoeksdashboard met pensioen laten gaan.

Uw bestaande gegevens blijven beschikbaar in de beveiligingsoplossing log Analytics en zijn niet gewijzigd of verwijderd.

![Onderzoeksdashboard in Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Onderzoek - de nieuwe ervaring

We raden u aan om over te stappen op [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) voor een uitgebreide onderzoekservaring. Azure Sentinel biedt krachtige zoek- en querytools om te zoeken naar beveiligingsbedreigingen in de gegevensbronnen van uw organisatie.

## <a name="subset-of-security-solutions"></a>Subset van beveiligingsoplossingen<a name="menu_solutions"></a>

Security Center kan [geïntegreerde beveiligingsoplossingen in Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)inschakelen. We hebben de volgende partneroplossingen van Security Center teruggetrokken. Deze oplossingen zijn ingeschakeld in [Azure Sentinel,](https://azure.microsoft.com/services/azure-sentinel/) samen met een aantal extra gegevensbronnen.

- [Firewall- en webtoepassingsfirewalloplossingen van de volgende generatie](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integratie van beveiligingsoplossingen die de Common Event Format (CEF) ondersteunen](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Na uw pensionering u geen van de oplossingstypen toevoegen of wijzigen die in de vorige lijst worden genoemd, vanuit de gebruikersinterface of de API. Azure Security Center ontdekt geen nieuwe exemplaren meer van deze partneroplossingen.

Als u bestaande verbonden oplossingen hebt, raden we u aan om over te stappen naar Azure Sentinel.

![Oplossingen voor beveiligingscentra][16]

## <a name="edit-security-configurations-for-security-policies"></a>Beveiligingsconfiguraties voor beveiligingsbeleid bewerken<a name="menu_securityconfigurations"></a>

Azure Security Center bewaakt beveiligingsconfiguraties door een set van [meer dan 150 aanbevolen regels](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) toe te passen om het besturingssysteem te beveiligen. Deze regels hebben betrekking op firewalls, auditing, wachtwoordbeleid en meer. Als er wordt geconstateerd dat een computer een kwetsbare configuratie heeft, wordt er door Security Center een beveiligingsaanbeveling gegenereerd. Met het [beveiligingsconfiguratiescherm bewerken](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) kunnen klanten de standaardbeveiligingsconfiguratie van het besturingssysteem aanpassen in security center.

We hebben deze preview-functie met pensioen laten gaan. Als u uw beveiligingsconfiguraties na de pensioendatum wilt terugzetten naar hun standaardwaarden, doet u dit via de API of Powershell met behulp van de [volgende instructies.](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization)

![Beveiligingsconfiguraties bewerken][17]

### <a name="edit-security-configurations---the-new-experience"></a>Beveiligingsconfiguraties bewerken - de nieuwe ervaring

We zijn van plan om Security Center in te schakelen om de [gastconfiguratieagent](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)te ondersteunen. Een dergelijke update maakt een veel rijkere functieset mogelijk, inclusief ondersteuning voor meer besturingssystemen en integratie van Azure-in-guest-beleid voor gastconfiguraties. Nadat deze wijzigingen zijn ingeschakeld, hebt u ook de mogelijkheid om configuraties op schaal te beheren en deze automatisch toe te passen op nieuwe bronnen.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Beveiligings- en auditdashboard voor Log Analytics-werkruimten<a name="menu_securityomsdashboard"></a>

Het beveiligings- en auditdashboard werd oorspronkelijk gebruikt in de OMS-portal. In Log Analytics biedt het dashboard een overzicht per werkruimte van opmerkelijke beveiligingsgebeurtenissen en -bedreigingen, een kaart met bedreigingsinformatie en een identiteits- en toegangsbeoordeling van beveiligingsgebeurtenissen die in de werkruimte zijn opgeslagen. We hebben het dashboard verwijderd. Zoals we al hebben aanbevolen in de gebruikersinterface van het dashboard, raden we u aan om over te stappen naar Azure Security Center.

![Beveiligingsdashboard Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Beveiligings- en auditdashboard - de nieuwe ervaring

We raden u aan over te stappen naar Azure Security Center. Het biedt hetzelfde beveiligingsoverzicht voor meerdere abonnementen en de werkruimten die eraan zijn gekoppeld, plus een uitgebreidere functieset.

U de oorspronkelijke Log Analytics-query's opvragen krijgen die het beveiligings- en auditdashboard vullen in de [GitHub-opslagplaats](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) voor beveiligingscentrum.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Meer informatie over [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
