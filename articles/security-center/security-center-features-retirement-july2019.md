---
title: Buiten gebruik stellen van Security Center-functies (2019 juli) | Microsoft Docs
description: In dit artikel worden de functies beschreven in Security Center die zijn ingetrokken op 31 juli 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: b4367bacf13e121220f12b6587c5ad60ad59d031
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910575"
---
> [!NOTE]
> In dit document vindt u een overzicht van de functies die sinds 31 juli 2019 van Azure Security Center zijn buiten gebruik gesteld.
>
>


# <a name="retirement-of-security-center-features-july-2019"></a>Buiten gebruik stellen van Security Center-functies (2019 juli)

We hebben een aantal [verbeteringen](https://azure.microsoft.com/updates/?product=security-center) aangebracht in de zes maanden Azure Security Center tot en met 2019.
Met deze verbeterde mogelijkheden hebben we op 31 juli 2019 enkele redundante functies en gerelateerde Api's verwijderd van Security Center.

De meeste van deze onderdelen die buiten gebruik worden gesteld, kunnen worden vervangen door nieuwe functionaliteit in Azure Security Center of Azure Log Analytics. Andere functies kunnen worden geïmplementeerd met behulp van [Azure Sentinel (preview)](https://azure.microsoft.com/services/azure-sentinel/).

Buiten gebruik gestelde Security Center-functies zijn:

- [Gebeurtenissen dashboard](#menu_events)
- [Invoer menu opdracht](#menu_search)
- [De koppeling klassieke identiteits & toegang weer geven op identiteit en toegang (preview)](#menu_classicidentity)
- [Knop voor toewijzing van beveiligings gebeurtenissen op de kaart Security Alerts (preview)](#menu_securityeventsmap)
- [Aangepaste waarschuwings regels (preview-versie)](#menu_customalerts)
- [De knop onderzoeken in beveiligings waarschuwingen voor bedreigingen beveiliging](#menu_investigate)
- [Een subset van beveiligings oplossingen](#menu_solutions)
- [Beveiligings configuraties voor beveiligings beleid bewerken](#menu_securityconfigurations)
- [Dash board voor beveiliging en controle (oorspronkelijk gebruikt in OMS-Portal) voor Log Analytics-werk ruimten](#menu_securityomsdashboard)

In dit artikel vindt u gedetailleerde informatie over elke buiten gebruik gestelde functie en de stappen die u kunt nemen om de vervangings functies te implementeren.

## Gebeurtenissen dashboard<a name="menu_events"></a>

Security Center maakt gebruik van micro soft Monitoring Agent voor het verzamelen van verschillende aan beveiliging gerelateerde configuraties en gebeurtenissen van uw computers. Deze gebeurtenissen worden opgeslagen in uw werk ruimten. Met het [Events-dash board](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) kunt u deze gegevens bekijken en u een toegangs punt geven om log Analytics.

Het dash board gebeurtenissen dat werd weer gegeven toen u een werk ruimte heb geselecteerd, zijn buiten gebruik gesteld:

![Gebeurtenissendashboard][2]

### <a name="events-dashboard---the-new-experience"></a>Gebeurtenissen Dashboard: de nieuwe ervaring

We raden u aan de systeem eigen mogelijkheden van Azure Log Analytics te gebruiken om de belangrijkste gebeurtenissen in uw werk ruimten te bekijken.

Als u aangepaste gebeurtenissen in Security Center hebt gemaakt, zijn deze toegankelijk. Ga in log Analytics naar **Selecteer werk ruimte** > **opgeslagen Zoek opdrachten**. Uw gegevens gaan verloren of worden niet gewijzigd. Systeem eigen gebeurtenissen zijn ook beschikbaar in hetzelfde scherm in Log Analytics.

![Opgeslagen Zoek opdrachten in werk ruimte][3]

## Invoer menu opdracht<a name="menu_search"></a>

Azure Security Center maakt momenteel gebruik van Azure Monitor logboeken zoeken om uw beveiligings gegevens op te halen en te analyseren. Dit scherm fungeert als een venster voor het Log Analytics van de zoek pagina en stelt gebruikers in staat Zoek query's uit te voeren op hun geselecteerde werk ruimte. Zie [Azure Security Center Search](https://docs.microsoft.com/azure/security-center/security-center-search)voor meer informatie. Dit zoek venster is buiten gebruik gesteld:

![Zoek pagina][4]

### <a name="search-menu-entry---the-new-experience"></a>Zoek menu-item: de nieuwe ervaring

We raden u aan de Azure Log Analytics systeem eigen mogelijkheden te gebruiken om Zoek query's uit te voeren op uw werk ruimten. Ga naar Azure Log Analytics en selecteer **Logboeken**.

![Pagina Log Analytics logboeken][5]

## Klassieke identiteits & toegang (preview)<a name="menu_classicidentity"></a>

Met de klassieke identiteit & toegang in Security Center wordt momenteel een dash board met identiteits-en toegangs gegevens in Log Analytics weer gegeven. Dit dash board weer geven:

1. Selecteer **klassieke identiteit & toegang weer geven**.

   ![Pagina identiteit][6]

1. Bekijk het **dash board voor identiteits & toegang**.

    ![Pagina identiteit-selectie van werk ruimte][7]

1. Selecteer een werk ruimte om het **identiteits & Access** -dash board te openen in log Analytics om identiteits-en toegangs gegevens weer te geven in uw werk ruimte.

   ![Identiteits pagina-dash board][8]

Alle drie de schermen die in de voor gaande stappen zijn weer gegeven, zijn buiten gebruik gesteld. Uw gegevens blijven beschikbaar in de beveiligings oplossing van Log Analytics en zijn niet gewijzigd of verwijderd.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klassieke identiteits & toegang (preview): de nieuwe ervaring

Het Log Analytics-dash board heeft inzichten op één werk ruimte weer gegeven. Systeem eigen Security Center biedt echter inzicht in alle abonnementen en alle werk ruimten die eraan zijn gekoppeld. U hebt toegang tot een eenvoudig te gebruiken weer gave waarmee u zich kunt richten op wat belang rijk is met de aanbevelingen die zijn afgestemd op basis van hun beveiligde Score.

U kunt alle functies van de **identiteit & Access** -dash board in log Analytics bereiken door **identiteit & Access (Preview)** in Security Center te selecteren.

![Pagina identiteit-klassieke ervaring buiten gebruik stellen][9]

## Toewijzing van beveiligings gebeurtenissen<a name="menu_securityeventsmap"></a>

Security Center biedt u een [overzicht van beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) waarmee u beveiligings Risico's kunt identificeren. Met de knop **naar de map met beveiligings gebeurtenissen** in deze kaart opent u een dash board waarmee u onbewerkte beveiligings gebeurtenissen kunt weer geven in de geselecteerde werk ruimte.

We hebben de knop **Ga naar beveiligings gebeurtenissen toewijzen** en het dash board per werk ruimte verwijderd.

![Toewijzing van beveiligings waarschuwingen-knop][10]

Wanneer u de knop **naar beveiligings gebeurtenissen toewijzen** selecteert, opent u het dash board bedreigings informatie. Het dash board voor bedreigings informatie is buiten gebruik gesteld.

![Het dashboard Bedreigingsinformatie][11]

Wanneer u een werk ruimte kiest om het bijbehorende Threat Intelligence-dash board weer te geven, opent u het scherm Security Alerts (preview) in Log Analytics. Dit scherm is buiten gebruik gesteld.

![Toewijzing van beveiligings waarschuwingen in Log Analytics][12]

De bestaande gegevens blijven beschikbaar in de beveiligings oplossing van Log Analytics en zijn niet gewijzigd of verwijderd.

### <a name="security-events-map---the-new-experience"></a>Toewijzing van beveiligings gebeurtenissen-de nieuwe ervaring

We raden u aan de functie voor waarschuwings toewijzing te gebruiken die is ingebouwd in Security Center: **Toewijzing van beveiligings waarschuwingen (preview-versie)** . Deze functie biedt een geoptimaliseerde ervaring en werkt in alle abonnementen en aan de gekoppelde werk ruimten. Het biedt u een weer gave op hoog niveau in uw omgeving en is niet gericht op één werk ruimte.

## Aangepaste waarschuwings regels (preview-versie)<a name="menu_customalerts"></a>

We hebben [de aangepaste meldings ervaring](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) op 30 juni 2019 buiten gebruik gesteld, omdat de onderliggende infra structuur buiten gebruik is gesteld. Na afloop van de datum van pensionering worden er geen aangepaste beveiligings waarschuwingen meer gegenereerd.
We raden u aan [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) in te scha kelen en uw aangepaste waarschuwingen daar opnieuw te maken. U kunt ook waarschuwingen maken met Azure Monitor logboek waarschuwingen.

Aangepaste waarschuwingen maken met Azure Sentinel:

1. [Open Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) en selecteer de werk ruimte waar uw aangepaste waarschuwingen worden opgeslagen
1. **Analyse** selecteren in het menu
1. Volg de instructies in de volgende [zelf studie](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) over het maken van aangepaste waarschuwingen in azure Sentinel

Als u niet geïnteresseerd bent in het gebruik van Azure Sentinel, kunt u uw waarschuwingen maken met Azure Monitor logboek waarschuwingen. Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) en [logboek waarschuwingen in azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)voor instructies.

![Aangepaste waarschuwingen][13]

Zie [aangepaste waarschuwings regels in azure Security Center (preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)voor meer informatie over het buiten gebruik stellen van aangepaste waarschuwingen.

## Onderzoek van beveiligings waarschuwingen<a name="menu_investigate"></a>

Met [de functie voor onderzoek](https://docs.microsoft.com/azure/security-center/security-center-investigation) in Security Center kunt u een potentieel beveiligings incident sorteren. Met deze functie kunt u het bereik van een incident begrijpen en de hoofd oorzaak volgen. Deze functie is van Security Center verwijderd omdat deze is vervangen door een verbeterde ervaring in [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Beveiligingsincident][14]

Wanneer u de knop **onderzoeken** selecteert in het scherm **beveiliging incident** , opent u het dash board onderzoek (preview) in log Analytics. Het dash board voor onderzoek is buiten gebruik gesteld.

De bestaande gegevens blijven beschikbaar in de beveiligings oplossing van Log Analytics en zijn niet gewijzigd of verwijderd.

![Dash board voor onderzoek in Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Onderzoek: de nieuwe ervaring

We raden u aan om over te stappen op [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) voor een rijke onderzoek ervaring. Azure Sentinel biedt krachtige zoek-en query hulpprogramma's voor het zoeken naar beveiligings Risico's in de gegevens bronnen van uw organisatie.

## Subset van beveiligings oplossingen<a name="menu_solutions"></a>

Security Center kunt [geïntegreerde beveiligings oplossingen inschakelen in azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). We hebben de volgende partner oplossingen buiten gebruik gesteld van Security Center. Deze oplossingen zijn ingeschakeld in [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) samen met een aantal extra gegevens bronnen.

- [Firewall-en Web Application Firewall oplossingen van de volgende generatie](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integratie van beveiligings oplossingen die de algemene gebeurtenis indeling (CEF) ondersteunen](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Na het buiten gebruik stellen kunt u geen van de typen oplossingen die in de voor gaande lijst worden vermeld, toevoegen of wijzigen, hetzij via de gebruikers interface of de API. Azure Security Center vindt geen nieuwe exemplaren van deze partner oplossingen meer.

Als u bestaande verbonden oplossingen hebt, raden we u aan om over te stappen op Azure Sentinel.

![Oplossingen voor Security Centers][16]

## Beveiligings configuraties voor beveiligings beleid bewerken<a name="menu_securityconfigurations"></a>

Azure Security Center bewaakt beveiligingsconfiguraties door een set van [meer dan 150 aanbevolen regels](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) toe te passen om het besturingssysteem te beveiligen. Deze regels zijn van toepassing op firewalls, controle, wachtwoord beleid en meer. Als er wordt geconstateerd dat een computer een kwetsbare configuratie heeft, wordt er door Security Center een beveiligingsaanbeveling gegenereerd. In het [scherm beveiligings configuratie bewerken](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) kunnen klanten de standaard configuratie voor de beveiliging van besturings systemen aanpassen in Security Center.

Deze preview-functie is buiten gebruik gesteld. Ga als [volgt](https://aka.ms/ascresetsecurityconfigurations) te werk om de standaard waarden na afloop van de eind datum van de beveiligings configuraties te herstellen via de API of Power shell

![Beveiligingsconfiguraties bewerken][17]

### <a name="edit-security-configurations---the-new-experience"></a>Beveiligings configuraties bewerken: de nieuwe ervaring

We willen Security Center in staat stellen om de [gast configuratie agent](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)te ondersteunen. Met deze update kunt u veel rijkere functies instellen, waaronder ondersteuning voor meer besturings systemen en integratie van Azure in-gast beleid voor gast configuraties. Nadat deze wijzigingen zijn ingeschakeld, hebt u ook de mogelijkheid om configuraties op schaal te beheren en ze automatisch toe te passen op nieuwe resources.

## Beveiligings-en controle dashboard voor Log Analytics-werk ruimten<a name="menu_securityomsdashboard"></a>

Het dash board beveiliging en controle werd oorspronkelijk gebruikt in de OMS-Portal. In Log Analytics biedt het dash board een overzicht per werk ruimte van belang rijke beveiligings gebeurtenissen en bedreigingen, een bedreigings informatie kaart en een evaluatie van de identiteit en toegang van beveiligings gebeurtenissen die zijn opgeslagen in de werk ruimte. Het dash board is verwijderd. Zoals we al aanbevolen zijn in de gebruikers interface van het dash board, raden we u aan om over te stappen op Azure Security Center.

![Log Analytics Security-dash board][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Beveiligings-en controle Dashboard: de nieuwe ervaring

We raden u aan om over te scha kelen naar Azure Security Center. Het biedt hetzelfde beveiligings overzicht voor meerdere abonnementen en de werk ruimten die eraan zijn gekoppeld, plus een uitgebreidere functieset.

U kunt de oorspronkelijke Log Analytics query's ophalen die het dash board beveiliging en controle in de [github-opslag plaats](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) voor Security Center vullen.

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
