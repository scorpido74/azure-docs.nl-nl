---
title: Cloudyn-rapporten gebruiken in Azure | Microsoft Docs
description: In dit artikel wordt het doel beschreven van de Cloudyn-rapporten die deel uitmaken van de Cloudyn-portal, zodat u ze effectief kunt gebruiken.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: b3563e08963b0b32542dcbece2529c350981f557
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200007"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Rapporten die beschikbaar zijn in de Cloudyn-portal

In dit artikel wordt het doel beschreven van de Cloudyn-rapporten die deel uitmaken van de Cloudyn-portal. Ook wordt beschreven hoe u de rapporten effectief kunt gebruiken. De meeste rapporten zijn intuïtief en hebben een uniforme vormgeving. De meeste acties die u in één rapport kunt uitvoeren, kunt u ook in andere rapporten uitvoeren. Raadpleeg [Begrip van kostenrapporten](understanding-cost-reports.md) voor een overzicht van het gebruik van Cloudyn-rapporten, onder andere hoe u planningsrapporten kunt aanpassen of opslaan.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Azure Cost Management](../cost-management-billing-overview.md) voor meer informatie.

## <a name="report-types"></a>Rapporttypen

Er zijn drie soorten Cloudyn-rapporten:

- Rapporten over een tijdsperiode. Bijvoorbeeld het rapport Kosten in de loop van de tijd. Rapporten over een tijdsperiode geven een tijdreeks met gegevens weer voor een geselecteerd interval met een vooraf gedefinieerde resolutie, en geven een wekelijkse resolutie weer voor de afgelopen twee maanden. U kunt groeperen en filteren om in te zoomen op verschillende gegevenspunten.
  - Rapporten met overuren kunnen u helpen trends te bekijken, en pieken of afwijkingen te detecteren.
- Analyserapporten. Bijvoorbeeld het rapport Kostenanalyse. In deze rapporten worden samengevoegde gegevens weergegeven, gedurende een periode die u definieert. U kunt deze gegevens groeperen en filteren.
  - Analyserapporten kunnen u helpen bij het weergeven van pieken en het bepalen van de hoofdoorzaken van afwijkingen, en om een nauwkeurige uitsplitsing van uw gegevens te bekijken.
- Rapporten in tabelvorm. U kunt elk rapport weergeven als een tabel, maar sommige rapporten kunnen alleen maar als tabel worden weergegeven. Deze rapporten bieden u gedetailleerde lijsten met items.
  - Aanbevelingen zijn rapporten in tabelvorm - er zijn geen visualisaties voor aanbevelingen. U kunt aanbevelingsresultaten echter visualiseren. Bijvoorbeeld besparingen in de loop van de tijd.
  - Rapporten in tabelvorm zijn handig als actielijsten of voor het exporteren van gegevens voor verdere verwerking. Bijvoorbeeld een terugstortingsrapport.

In kostenrapporten worden de _werkelijke_ of _afgeschreven_ kosten weergegeven.

In rapporten met werkelijke kosten worden de betalingen weergegeven die zijn gedaan tijdens het geselecteerde tijdsbestek. Alle eenmalige kosten, zoals RI-aankopen (gereserveerde instanties), worden in rapporten met werkelijke kosten bijvoorbeeld weergegeven als kostenpieken.

In rapporten met afgeschreven kosten worden eenmalige kosten verspreid over een periode waarop ze van toepassing zijn. Eenmalige kosten voor RI-aankopen worden bijvoorbeeld verspreid over de reserveringstermijn, en worden niet weergegeven als een piek. De weergave met afgeschreven kosten is de enige manier om werkelijke trends te bekijken en kostenramingen te maken.

In sommige gevallen wordt de aflossing weergegeven als een afzonderlijk rapport. Voorbeelden hiervan zijn onder andere het rapport Kostenanalyse en het rapport Analyse van afgeschreven kosten. In andere gevallen is de aflossing een rapportbeleid, zoals het rapport Kostentoewijzing en het rapport Kostenanalyse.

U kunt voor elk rapport een periodieke levering plannen. Voor kostenrapporten kan een drempelwaarde worden ingesteld, waardoor ze handig zijn voor waarschuwingen.

## <a name="cost-analysis-vs-cost-allocation"></a>Kostenanalyse versus kostentoewijzing

In de rapporten voor _Kostenanalyse_ worden de factureringsgegevens van uw cloudproviders weergegeven. Met behulp van deze rapporten kunt u verschillende gegevenssegmenten groeperen en uitsplitsen die zijn gespecificeerd in het factureringsbestand. In de rapporten is gedetailleerde kostennavigatie mogelijk in de onbewerkte factureringsgegevens van uw cloudleverancier.

In sommige rapporten voor _Kostenanalyse_ zijn de kosten niet gegroepeerd op resourcetags. En op tags gebaseerde factureringsgegevens worden alleen weergegeven in rapporten nadat u kosten hebt toegewezen door een kostenmodel te maken met behulp van [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Rapporten voor _Kostentoewijzing_ zijn beschikbaar nadat u een kostenmodel hebt gemaakt met behulp van [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). In Cloudyn worden kosten- en factureringsgegevens verwerkt. Deze gegevens worden _vergeleken_ met de gebruiks- en taggegevens van uw cloudaccounts. Cloudyn heeft toegang tot uw gebruiksgegevens om de gegevens te kunnen vergelijken. Als u accounts hebt waarvoor referenties ontbreken, worden deze aangeduid als _Niet-gecategoriseerde resources_.

## <a name="dashboards"></a>Dashboards

Dashboards in de Cloudyn bieden rapportweergaven op hoog niveau. Dashboards bestaan uit widgets, en elke widget is in feite een miniatuur van een rapport. Wanneer u [rapporten aanpast](understanding-cost-reports.md#save-and-schedule-reports), slaat u ze op in Mijn rapporten. Vervolgens worden de rapporten toegevoegd aan het dashboard. Raadpleeg [Belangrijke metrische kostengegevens weergeven met dashboards](dashboards.md) voor meer informatie over dashboards.

## <a name="budget-information-in-reports"></a>Budgetgegevens in rapporten

In veel Cloudyn-rapporten worden budgetgegevens weergegeven nadat u handmatig een budget hebt gemaakt. Er worden dus geen budgetgegevens weergegeven totdat u een budget maakt. Raadpleeg [Instellingen voor budgetbeheer](#budget-management-settings) voor meer informatie.

## <a name="reports-and-reporting-features"></a>Rapporten en rapportagefuncties

Cloudyn bevat de volgende rapporten en rapportagefuncties.

### <a name="cost-navigator-report"></a>Rapport Kostennavigator

Het rapport Kostennavigator biedt een snelle manier om uw factureringsverbruik te bekijken met behulp van een dashboardweergave. Het rapport bevat een subset filters en basisweergaven waarmee u onmiddellijk een overzicht krijgt van de kosten in de organisatie. Kosten worden weergegeven op datum. Omdat het rapport is bedoeld als een eerste weergave van de kosten, is het niet zo flexibel of zo uitgebreid als veel andere rapporten of aangepaste dashboards die u zelf maakt.

In hoofdweergaven van het rapport wordt standaard het volgende weergegeven:

- Kosten in de loop van de tijd met daarin een staafdiagramweergave van een werkweek. U kunt het **Datumbereik**  wijzigen om het staafdiagram met datumbereik te wijzigen.
- Uitgaven per service, met behulp van een cirkeldiagram.
- Resourcecategorieën per tag, met behulp van een cirkeldiagram.
- Uitgaven per kostenentiteit, met behulp van een cirkeldiagram.
- Totale kosten per datum, in een lijstweergave.

### <a name="cost-analysis-report"></a>Kostenanalyserapport

Het rapport Kostenanalyse is een berekening van showback en terugstorting, op basis van uw beleid. Nadat alle toewijzingsregels zijn toegepast op uw kosten, wordt uw cloudverbruik tijdens een geselecteerd tijdsbestek samengevoegd. Zo worden bijvoorbeeld de kosten per tag berekend, de kosten van resources zonder tag opnieuw toegewezen, en optioneel het gebruik van gereserveerde instanties toegewezen.

Beleid dat is ingesteld in [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) wordt gebruikt in het rapport Kostenanalyse, en de resultaten worden vervolgens gecombineerd met informatie uit de onbewerkte gegevens van de cloudleverancier.

Hoe wordt dit rapport berekend? In de Cloudyn-service wordt ervoor gezorgd dat bij de toewijzing de integriteit van elk gekoppeld account behouden blijft, door _accountaffiniteit_toe te passen. Met affiniteit wordt gegarandeerd dat er geen kosten voor een specifieke service aan een account worden toegewezen, wanneer dit account niet gebruikmaakt van deze service. De toegerekende kosten in dit account blijven in het betreffende account en worden niet berekend op basis van toewijzingsbeleid. Stel dat u vijf gekoppelde accounts hebt. Als slechts drie deze accounts gebruikmaken van opslagservices, worden de kosten van opslagservices alleen toegewezen aan tags in deze drie accounts.

Gebruik het rapport Kostenanalyse om:

- De terugstorting/showback in uw organisatie te berekenen
- Al uw kosten te categoriseren
- Een samengevoegde weergave van uw volledige implementatie weer te geven voor een specifiek tijdsbestek.
- Kosten weer te geven per tagcategorie, op basis van beleid dat is gemaakt in het kostenmodel.

Het rapport Kostenanalyse gebruiken:

1. Selecteer een datumbereik.
2. Voeg tags toe, indien nodig.
3. Voeg groepen toe.
4. Kies een kostenmodel dat u eerder hebt gemaakt.

### <a name="cost-over-time-report"></a>Rapport met overuren

In het rapport Kosten in de loop van de tijd worden de resultaten van de kostentoewijzing weergegeven als tijdreeks. Op deze manier kunt u trends in de gaten houden en onregelmatigheden in de implementatie detecteren. In wezen worden de kosten weergegeven verspreid over een gedefinieerde periode. Het rapport omvat de belangrijkste bijdragers aan uw kosten, inclusief lopende kosten en eenmalige kosten voor gereserveerde instanties die tijdens een geselecteerde periode worden uitgegeven. Beleid dat is ingesteld in [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) wordt gebruikt in dit rapport.

Gebruik het rapport Kosten in de loop van de tijd om:

- Wijzigingen in de loop van de tijd te bekijken, en te zien welke invloeden van de ene op de andere dag veranderen (of van het ene datumbereik op het volgende).
- Kosten in de loop van de tijd te analyseren voor een specifiek exemplaar.
- Te begrijpen waarom er een kostentoename was voor een specifiek exemplaar.

Het rapport Kosten in de loop van de tijd gebruiken:

1. Selecteer een datumbereik.
2. Voeg tags toe, indien nodig.
3. Voeg groepen toe.
4. Kies een kostenmodel dat u eerder hebt gemaakt.
5. Selecteer werkelijke kosten of afgeschreven kosten.
6. Kies of u toewijzingsregels wilt toepassen om onbewerkte factureringsgegevens te bekijken of de kostenweergave opnieuw te berekenen.

### <a name="actual-cost-analysis-report"></a>Rapport Werkelijke kostenanalyse

In het rapport Werkelijke kostenanalyse worden de providerkosten zonder wijzigingen weergegeven. De belangrijkste bijdragers aan uw kosten worden weergegeven, inclusief lopende kosten en eenmalige kosten.

U kunt het rapport gebruiken om de kostengegevens voor uw abonnementen te bekijken. In het rapport worden Azure-abonnementen weergegeven als **accountnaam** en **accountnummer**. In **gekoppelde accounts** worden AWS-abonnementen weergegeven. Als u de kosten per abonnement wilt bekijken, uitgesplitst per account, selecteert u onder **Groepen** het type abonnement dat u hebt.

Gebruik het rapport Werkelijke kostenanalyse om:

- De onbewerkte providerkosten die zijn besteed tijdens een opgegeven tijdsbestek, te analyseren en te controleren.
- Een drempelwaarschuwing te plannen.
- Ongewijzigde kosten te analyseren die zijn gemaakt voor uw accounts en entiteiten.

### <a name="actual-cost-over-time-report"></a>Rapport Werkelijke kosten in de loop van de tijd

Het rapport Werkelijke kosten in de loop van de tijd is een standaardrapport voor kostenanalyse, waarin de kosten worden verdeeld over een gedefinieerde tijdresolutie. In het rapport worden de kosten in de loop van de tijd weergegeven, zodat u trends in de gaten kunt houden en onregelmatigheden in de kosten kunt detecteren. In het rapport worden de belangrijkste bijdragers aan uw kosten weergegeven, inclusief lopende kosten en eenmalige kosten voor gereserveerde instanties die tijdens een geselecteerde periode worden uitgegeven.

Gebruik het rapport Werkelijke kosten in de loop van de tijd om:

- Kostentrends in de loop van de tijd te zien.
- Onregelmatigheden in de kosten te zoeken.
- Alle kostengerelateerde vragen met betrekking tot cloudproviders te zoeken.

### <a name="amortized-cost-reports"></a>Rapporten met afgeschreven kosten

In deze set rapporten met afgeschreven kosten worden lineaire servicekosten weergegeven die niet zijn gebaseerd op gebruik, of eenmalig te betalen kosten. De kosten in deze rapporten worden gelijkmatig verspreid over hun levensduur. Eenmalige kosten kunnen omvatten:

- Jaarlijkse kosten voor ondersteuning
- Jaarlijkse kosten voor beveiligingsonderdelen
- Kosten voor aankoop van gereserveerde instanties
- Bepaalde Azure Marketplace-items

In het factureringsbestand worden eenmalige kosten gemarkeerd wanneer de begin- en einddatum (tijdsstempel) van het serviceverbruik gelijke waarden zijn. Deze kosten worden in de Cloudyn-service vervolgens herkend als eenmalige kosten die worden afgeschreven. Andere op verbruik gebaseerde services met gebruikskosten op aanvraag worden niet afgeschreven.

Rapporten met afgeschreven kosten omvatten:

- Analyses van afgeschreven kosten
- Afgeschreven kosten in de loop van de tijd

### <a name="cost-analysis-report"></a>Kostenanalyserapport

Het rapport Kostenanalyse biedt inzicht in uw cloudverbruik en besteding tijdens een geselecteerd tijdsbestek. Beleid dat is ingesteld in [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) wordt gebruikt in het rapport Kostenanalyse.

Hoe wordt dit rapport berekend in Cloudyn?

Cloudyn zorgt ervoor dat bij de toewijzing de integriteit van elk gekoppeld account behouden blijft, door _accountaffiniteit_toe te passen. Met affiniteit wordt gegarandeerd dat er ook geen kosten voor een specifieke service aan een account worden toegewezen, wanneer dit account niet gebruikmaakt van deze service. De toegerekende kosten in dit account blijven in het betreffende account en worden niet berekend op basis van toewijzingsbeleid. Stel dat u vijf gekoppelde accounts hebt. Als slechts drie deze accounts gebruikmaken van opslagservices, worden de kosten van opslagservices alleen toegewezen aan tags in deze drie accounts.

Gebruik het rapport Kostenanalyse om:

- Een samengevoegde weergave van uw volledige implementatie weer te geven voor een specifiek tijdsbestek.
- Kosten weer te geven per tagcategorie, op basis van beleid dat is gemaakt in het kostenmodel.

### <a name="cost-over-time-report"></a>Rapport met overuren

In het rapport Kosten in de loop van de tijd wordt de besteding in de loop van de tijd weergegeven, zodat u trends en onregelmatigheden in de implementatie kunt ontdekken. In wezen worden de kosten weergegeven verspreid over een gedefinieerde periode. Het rapport omvat de belangrijkste bijdragers aan uw kosten, inclusief lopende kosten en eenmalige kosten voor gereserveerde instanties die tijdens een geselecteerde periode worden uitgegeven. Beleid dat is ingesteld in [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) wordt gebruikt in dit rapport.

Gebruik het rapport Kosten in de loop van de tijd om:

- Wijzigingen in de loop van de tijd te bekijken, en te zien welke invloeden van de ene op de andere dag veranderen (of van het ene datumbereik op het volgende).
- Kosten in de loop van de tijd te analyseren voor een specifiek exemplaar.
- Te begrijpen waarom er een kostentoename was voor een specifiek exemplaar.

### <a name="custom-charges-report"></a>Rapport Aangepaste kosten

Enterprise- en CSP-gebruikers bieden hun externe of interne klanten vaak toegevoegde services, naast hun eigen cloudresourceverbruik. Aangepaste kosten voor toegevoegde services of kortingen die worden toegevoegd aan de facturerings- of terugstortingsrapporten van de klant, definieert u als aangepaste regelitems.

Aangepaste servicekosten reflecteren services die normaal gesproken niet worden weergegeven op een factuur. De aangepaste kosten die u maakt, worden vervolgens weergegeven in kostenrapporten.

*Aangepaste kosten zijn geen aangepaste prijzen*. In de lijst met aangepaste kosten worden niet de verschillende tarieven weergegeven die u mogelijk in rekening brengt. AWS-factureringskosten worden bijvoorbeeld exact weergegeven zoals ze in rekening worden gebracht.

Aangepaste kosten maken:

1. Klik in **Aangepaste kosten** op **Nieuwe toevoegen**. Het dialoogvenster _Nieuwe aangepaste kosten toevoegen_ wordt weergegeven.
2. Voer in **Providernaam** de naam van de provider in.
3. Voer in **Servicenaam** het type service in.
4. Voeg in **Beschrijving** een beschrijving toe voor de aangepaste kosten.
5. Voer in **Type** het geselecteerde **Percentage** in, en selecteer vervolgens in de vervolgkeuzelijst Services de services die als aangepaste kosten moeten worden opgenomen in de kostenrapporten.
6. Selecteer in **Betaling** of het gaat om Eenmalige kosten of Terugkerende kosten. Als het om Terugkerende kosten gaat, selecteert u Afgeschreven als u wilt dat de kosten worden afgeschreven, en selecteert u het aantal maanden.
7. Als bij **Ingangsdatum** de optie Eenmalige kosten is geselecteerd, voert u bij **Datum** de datum in waarop de kosten zijn betaald. Als Terugkerende kosten is geselecteerd, voert u het datumbereik in, inclusief de begindatum en de einddatum voor de kosten.
8. Selecteer in **Entiteitenstructuur** de entiteiten waarop u de kosten wilt toepassen, en selecteer vervolgens **Aan**.

_Wanneer er kosten zijn toegewezen aan een entiteit, kunnen gebruikers dit niet wijzigen. Kosten die door een beheerder zijn toegevoegd aan een bovenliggende entiteit, hebben het kenmerk Alleen-lezen._

Aangepaste kosten weergeven:

Aangepaste kosten worden weergegeven in kostenrapporten. Open bijvoorbeeld het rapport Werkelijke kostenanalyse, en selecteer vervolgens onder **Uitgebreide filters** de optie **Zelfstandig**. Filter vervolgens op **Aangepaste kosten**.

### <a name="cost-allocation-360"></a>Kostentoewijzing 360

U gebruikt Kostentoewijzing 360 om aangepaste modellen voor kostentoewijzing te maken, voor het toewijzen van kosten aan verbruikte cloudresources. In veel rapporten wordt informatie uit aangepaste kostenmodellen weergegeven die u hebt gemaakt met aangepaste kostenmodellen. En sommige rapporten bevatten alleen informatie nadat u een aangepast kostenmodel hebt gemaakt met kostentoewijzing.

Zie voor meer informatie over het maken van aangepaste kostenmodellen [Zelfstudie: kosten beheren met Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Rapport Kosten versus Budget in de loop van de tijd

In het rapport Kosten versus Budget in de loop van de tijd kunt u de belangrijkste bijdragers aan uw kosten vergelijken met uw budget. Het toegewezen budget wordt weergegeven in het rapport, zodat u uw budgetverbruik in de loop van de tijd kunt bekijken (bent u boven het budget of onder het budget gekomen, of klopt het precies?) Met de velden Weergeven/Verbergen boven aan het rapport kunt u ervoor kiezen om de kosten, het budget, de samengevoegde kosten, en het totale budget weer te geven.

### <a name="current-month-projected-cost-report"></a>Rapport Geschatte kosten voor de huidige maand

In het rapport Geschatte kosten voor de huidige maand wordt inzicht geboden in uw huidige kostenoverzicht (maand tot heden). In dit rapport worden de kosten van het begin van de maand, van de vorige maand, en de totale geschatte kosten voor de huidige maand weergegeven. De geschatte kosten voor de huidige maand worden berekend als som van de bijgewerkte maandelijkse kosten, en een raming op basis van de kosten die de afgelopen 30 dagen zijn gecontroleerd.

Gebruik het rapport Geschatte kosten voor de huidige maand om:

- Maandelijkse kosten per service te schatten
- Maandelijkse kosten per account te schatten

### <a name="annual-projected-cost-report"></a>Rapport Jaarlijkse geschatte kosten

Met het rapport Jaarlijkse geschatte kosten kunt u de jaarlijks geschatte kosten bekijken op basis van eerdere bestedingstrends. Hierin worden de totale geschatte kosten weergegeven voor de komende 12 maanden. De ramingen worden gemaakt met behulp van een trendfunctie die wordt geëxtrapoleerd over de komende 12 maanden, op basis van de kosten van de laatste 30 dagen van gebruik.

### <a name="budget-management-settings"></a>Instellingen voor budgetbeheer

Met budgetbeheer kunt u een budget instellen voor uw boekjaar.

Een budget toevoegen aan een entiteit:

1. Selecteer op de pagina Budget Management, onder **Entities**, de entiteit waarvoor u het budget wilt maken.
2. Selecteer in het budgetjaar het jaar waarvoor u het budget wilt maken.
3. Stel in elke maand uw budget in, en klik vervolgens op **Opslaan**.

Een bestand voor het jaarlijkse budget importeren:

1. Selecteer **Export** onder **Actions** om een lege CSV-sjabloon te downloaden en deze te gebruiken als basis voor het budget.
2. Vul het CSV-bestand in met uw budgetgegevens en sla het bestand lokaal op.
3. Selecteer **Import** onder **Actions**.
4. Selecteer het opgeslagen bestand en klik vervolgens op **OK**.

Als u het voltooide budget wilt exporteren als een CSV-bestand, selecteert u **Export** onder **Actions** om het bestand te downloaden.

Wanneer dit is voltooid, wordt uw budget weergegeven in het rapport Kostenanalyse, en in het rapport Kosten versus Budget Over Time. U kunt rapporten ook plannen op basis van budgetdrempelwaarden.

### <a name="azure-resource-explorer-report"></a>Rapport Azure Resource Explorer

In het rapport Azure Resource Explorer wordt een bulklijst weergegeven met alle beschikbare Azure-resources in Cloudyn. Om het rapport efficiënt te kunnen gebruiken moeten uitgebreide metrische gegevens zijn ingeschakeld voor uw Azure-accounts. Uitgebreide metrische gegevens bieden Cloudyn toegang tot uw Azure-VM’s. Raadpleeg [Uitgebreide metrische gegevens voor virtuele Azure-machines toevoegen](azure-vm-extended-metrics.md) voor meer informatie.

### <a name="azure-resources-over-time-report"></a>Rapport Azure-resources in de loop van de tijd

In het rapport Azure-resources in de loop van de tijd wordt een uitsplitsing weergegeven van alle resources die actief zijn in een bepaalde periode. Om het rapport efficiënt te kunnen gebruiken moeten uitgebreide metrische gegevens zijn ingeschakeld voor uw Azure-accounts. Uitgebreide metrische gegevens bieden Cloudyn toegang tot uw Azure-VM’s. Raadpleeg [Uitgebreide metrische gegevens voor virtuele Azure-machines toevoegen](azure-vm-extended-metrics.md) voor meer informatie.

### <a name="instance-explorer-report"></a>Rapport Instantieverkenner

Het rapport Instantieverkenner wordt gebruikt om de verschillende metrische gegevens voor activa van uw virtuele machines weer te geven. U kunt inzoomen op specifieke instanties om informatie te bekijken, zoals:
- Uitvoeringsintervallen voor instanties
- Levenscyclus in de geselecteerde periode
- CPU-gebruik
- Netwerkinvoer
- Uitvoerverkeer
- Actieve schijven

In het rapport Instantieverkenner worden alle actieve intervallen verzameld binnen het gedefinieerde datumbereik, en dienovereenkomstig samengevoegd. Als u elk van de actieve uitvoeringsintervallen tijdens het datumbereik wilt bekijken, vouwt u de instantie uit. De kosten van elke instantie worden berekend voor het geselecteerde datumbereik, op basis van prijslijsten voor AWS en Azure. Er zijn geen kortingen toegepast. U kunt extra velden aan het rapport toevoegen met behulp van de velden Weergeven/Verbergen.

Gebruik het rapport Instantieverkenner om:

- De geschatte kosten per machine te berekenen.
- Een volledige lijst te maken, inclusief samengevoegde activiteitsuren, met alle machines die actief zijn tijdens een tijdsbereik.
- Een lijst te maken per cloudserviceprovider of account.
- Machines weer te geven die tijdens een bepaald tijdsbereik zijn gemaakt of beëindigd.
- Alle machines weer te geven die momenteel zijn gestopt.
- De tags van elke machine weer te geven.

### <a name="instances-over-time-report"></a>Rapport Instanties in de loop van de tijd

Met behulp van het rapport Instanties in de loop van de tijd kunt u het maximum aantal machines zien dat actief is tijdens elke geselecteerde tijdsbereik. Als de gedefinieerde resolutie per week of per maand is, zijn de resultaten het maximum aantal machines dat actief is op een bepaalde dag in de betreffende maand. Selecteer een datumbereik om de filters te selecteren die u wilt weergeven in het rapport.

### <a name="instance-utilization-over-time-report"></a>Rapport Gebruik van instanties in de loop van de tijd

In dit rapport wordt een uitsplitsing gegeven van CPU- of geheugengebruik in de loop van de tijd, voor al uw instanties.

### <a name="compute-power-cost-over-time-report"></a>Rapport Rekenkracht in de loop van de tijd

Het rapport Rekenkracht in de loop van de tijd biedt een uitsplitsing van de rekenkracht tijdens een opgegeven datumbereik. Hoewel in andere rapporten het aantal actieve machines of de uitvoeringsuren worden weergegeven, biedt dit rapport een overzicht van uren voor kerngeheugens, rekeneenheden of GB RAM.

Gebruik het rapport om:

- De rekenkracht tijdens een opgegeven datumbereik te controleren.
- Rekentijden weer te geven op basis van modellen voor kostentoewijzing.

Dit rapport is gekoppeld aan het beleid [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Resultaten worden daarom weergegeven op basis van gedefinieerde tags en uw geselecteerde kostenbeleid. Als u geen beleid hebt gemaakt, worden er geen resultaten weergegeven.

### <a name="compute-power-average-cost-over-time-report"></a>Rapport Gemiddelde rekenkracht in de loop van de tijd

U gebruikt het rapport Gemiddelde rekenkracht in de loop van de tijd om meer te zien dan alleen de kosten voor elke actieve machine. In het rapport worden de gemiddelde kosten per uur weergegeven voor kerngeheugens, rekeneenheden of GB RAM. Het rapport biedt inzicht in de efficiëntie van de implementatie.

Dit rapport is gekoppeld aan het beleid [Kostentoewijzing 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Resultaten worden daarom weergegeven op basis van gedefinieerde tags en uw geselecteerde kostenbeleid. Als u geen beleid hebt gemaakt, worden er geen resultaten weergegeven.

### <a name="s3-cost-over-time-report"></a>Rapport S3-kosten in de loop van de tijd

Het rapport S3-kosten in de loop van de tijd biedt een uitsplitsing van Amazon Simple Storage-servicekosten (S3) per bucket overuren, voor een opgegeven tijdsbestek. Het rapport helpt u de buckets te vinden die uw belangrijkste kostenbijdragers zijn, en in het rapport worden trends in uw S3-gebruik en -besteding weergegeven.

### <a name="s3-distribution-of-cost-report"></a>Rapport S3-distributie van kosten

Gebruik het rapport om de S3-kosten voor de afgelopen maand te analyseren, per bucket en opslagklasse. U kunt het cirkeldiagram gebruiken om de zichtbaarheidsdrempelwaarde in te stellen. U kunt ook de tabelweergave gebruiken om subtotalen te zien.

### <a name="s3-bucket-properties-report"></a>Rapport Eigenschappen van S3-bucket

Gebruik het rapport om de eigenschappen van de S3-bucket weer te geven. U kunt het cirkeldiagram gebruiken om de zichtbaarheidsdrempelwaarde in te stellen. U kunt ook de tabelweergave gebruiken om subtotalen te zien.

### <a name="rds-instances-over-time-report"></a>Rapport RDS-instanties in de loop van de tijd

Gebruik het rapport om een uitsplitsing weer te geven van alle Amazon RDS-instanties (relationele databaseservice) die actief zijn tijdens de opgegeven periode.

### <a name="rds-active-instances-report"></a>Rapport Actieve RDS-instanties

Gebruik het rapport om actieve RDS-instanties te analyseren. Vouw in het rapport het regelitem uit om aanvullende informatie te bekijken.

### <a name="azure-reserved-instances-report"></a>Rapport Gereserveerde Azure-instanties

Het rapport Gereserveerde Azure-instanties biedt u één weergave van al uw gereserveerde Azure-instanties. In dit rapport wordt elke aankoop weergegeven als één regelitem. Het rapport bevat ook details over deze aankoop, zoals het account dat is gekocht, het type aankoop en het instantietype, het resterende aantal dagen, enzovoort. U kunt rapportgegevens weergeven of verbergen met behulp van de velden Weergeven/Verbergen.

Gebruik het rapport Gereserveerde Azure-instanties om het volgende te bekijken:

- Een lijst met alle reserveringen per inkoopdatum.
- Resterende tijd tot de RI verloopt.
- Eenmalige kosten.
- Het account waarmee de RI’s zijn aangeschaft, en wanneer dit plaatsvond.

### <a name="aws-reserved-instances-report"></a>Rapport Gereserveerde AWS-instanties

Het rapport Gereserveerde AWS-instanties biedt u één weergave van al uw gereserveerde AWS-instanties. In dit rapport wordt elke aankoop weergegeven als één regelitem. Daarnaast ziet u details over deze aankoop, zoals het account dat is gekocht, het type aankoop en het instantietype, het resterende aantal dagen, enzovoort. U kunt rapportgegevens weergeven of verbergen met behulp van de velden Weergeven/Verbergen.

Gebruik het rapport Gereserveerde AWS-instanties om het volgende te bekijken:

- Een lijst met alle reserveringen per inkoopdatum.
- Resterende tijd tot de RI verloopt.
- Eenmalige kosten.
- Oorspronkelijke aankoop-id (reserverings-id).
- Het account waarmee de RI’s zijn aangeschaft, en wanneer dit plaatsvond.

### <a name="ec2-ri-buying-recommendations-report"></a>Rapport Aanbevelingen voor EC2 RI-aankoop

Het model op aanvraag vormt de basis voor het verbruik van cloudresources. Bij dit model worden alleen kosten voor resources in rekening gebracht wanneer deze daadwerkelijk worden gebruikt. Er zijn geen toezeggingen vooraf: u betaalt alleen voor de resources die u gebruikt, en wanneer u deze gebruikt.

AWS biedt een alternatief prijsmodel voor de EC2-services (Elastic Compute-cloud): de RI (gereserveerde instantie). Dit prijsmodel biedt gebruikers de garantie dat ze over de capaciteit kunnen beschikken wanneer ze deze nodig hebben, voor de duur van de RI. De RI biedt aanzienlijke kortingen ten opzichte van de prijzen op aanvraag. Op hun beurt doen gebruikers vooraf een toezegging voor het gebruik van een virtuele instantie. De toezegging is gekoppeld aan een specifieke familie, grootte en beschikbaarheidszone (AZ), en een specifiek besturingssysteem, voor de duur van de toezegging (één of drie jaar). Met de RI kan in AWS een efficiënte planning worden gemaakt voor toekomstige capaciteiten, en wordt tegelijkertijd een klanttoezegging verkregen voor gebruik van de services.

Er zijn drie betalingsopties voor RI’s. Deze zijn allemaal vooraf:

- Gehele afbetaling op dag 0 (meeste korting)
- Geen betaling vooraf: hierbij worden de kosten voor een RI maandelijks afbetaald voor de duur van de RI (minste korting)
- Gedeeltelijke betaling vooraf: hierbij wordt een kwart of de helft vooraf betaald, en wordt de rest maandelijks afbetaald (bijna net zoveel korting als bij gehele afbetaling)

Met Cloudyn wordt de bedrijfstijd van elke machine gedurende de afgelopen 30 dagen geëvalueerd. Cloudyn raadt aan om RI’s te kopen wanneer het, op het huidige bedrijfstijdniveau, voordeliger is om de machine uit te voeren met een RI.

Het rapport bevat de reden voor de gedane aanbevelingen om jaarlijks het meeste geld te besparen. Een van de aanbevelingen is om op aanvraag te vervangen door RI’s. U kunt RI’s rechtstreeks vanuit het rapport aanschaffen.

Elk tabblad wordt geopend als een volledig rapport. Voorbeelden van belangrijke secties in tabbladen:

- **Impact van EC2 RI-aankoop**: deze sectie biedt een simulatie van het verschil tussen op aanvraag versus gereserveerde instanties. Klik op **Inzoomen** om het volledige rapport Impact van EC2 RI-aankoop te zien, met de filters al gedefinieerd voor uw aanbeveling. In dit rapport wordt de impact van alle potentiële RI-aankopen weergegeven. U kunt de verwachte gemiddelde bedrijfstijd aanpassen om te zien wat u bespaart als u gereserveerde EC2-instanties koopt.

- **Analyse van besparingen**: in deze sectie ziet u de mogelijke besparingen en de maand waarin de besparingen worden bereikt wanneer u de aanbevelingen van Cloudyn opvolgt. De werkelijke besparingen en het percentage dat is bespaard, zijn rood gemarkeerd.

- **Vergelijking van EC2 RI-typen**: in deze sectie wordt het belangrijkste rendement van de aanbevolen implementatie van Cloudyn benadrukt, inclusief alle relevante opties. Voor de resultaten in dit rapport wordt ervan uitgegaan dat de machine is uitgevoerd met een bedrijfstijd van 100%. Klik op **Inzoomen** om het gedetailleerde rapport te openen.

- **Instanties in de loop van de tijd**: in deze sectie wordt een uitsplitsing weergegeven van alle instanties die zijn gekoppeld aan de aanbeveling, OnDemand, Reserved Instances en Spot. Klik op **Inzoomen** om het gedetailleerde rapport te openen.
- **Break-evenpunten**: in deze sectie wordt een tabel weergegeven met alle mogelijke aanbevolen implementaties, het rendement en de maand waarin dit heeft plaatsgevonden. Klik op **Inzoomen** om het gedetailleerde rapport te openen.

### <a name="ec2-reservations-over-time-report"></a>Rapport EC2-reserveringen in de loop van de tijd

In het rapport EC2-reserveringen in de loop van de tijd wordt de status bijgehouden van uw gebruik van de aangeschafte EC2 RI’s. U kunt de resolutie van het rapport instellen op uur, dag of week.

Gebruik het rapport om:

- Aangeschafte reserveringen weer te geven die wel en niet zijn gebruikt.
- Per uur in te zoomen op de resolutie, om het RI-verbruik per uur te zien.

### <a name="savings-over-time-report"></a>Rapport Besparingen in de loop van de tijd

Gebruik het rapport Besparingen in de loop van de tijd om de besparingen weer te geven die zijn behaald door het gebruik van gereserveerde instanties, alsook de Spot-instanties. In het rapport wordt het rendement weergegeven dat in de loop van de tijd is behaald met RI-aankopen.

Als u de besparingen vanwege RI’s wilt bekijken, groepeert u de resultaten op **Prijsmodel** en selecteert u **Reservering**. Als u de besparingen met RI’s wilt zien die zijn behaald met een specifiek account of instantietype, voegt u de relevante groepering en het relevante filter toe aan het account of instantietype.

Als u de besparingen wilt zien die zijn behaald door gebruik van een Spot-instantie, filtert u het **Prijsmodel** op **Spot**. Het standaardfilter voor dit rapport is RI- en Spot-instanties.

### <a name="rds-ri-buying-recommendations-report"></a>Rapport Aanbevelingen voor RDS RI-aankoop

In het rapport Aanbevelingen voor RDS RI-aankoop wordt aanbevolen wanneer u RDS RI’s moet gebruiken in plaats van instanties op aanvraag.

Elk tabblad wordt geopend als een volledig rapport. Voorbeelden van belangrijke secties in tabbladen:

- **Impact van RDS RI-aankoop**: deze sectie biedt een simulatie van het verschil tussen op aanvraag versus gereserveerde instanties. Klik op **Inzoomen** om het volledige rapport Impact van RDS RI-aankoop te zien, met de filters al gedefinieerd voor uw aanbeveling. In dit rapport kunt u de impact zien van alle potentiële RI-aankopen.  U kunt de verwachte gemiddelde bedrijfstijd aanpassen en de mogelijke besparing zien wanneer u RI’s aanschaft.
- **Analyse van besparingen**: in deze sectie ziet u de mogelijke besparingen en de maand waarin de besparingen worden bereikt wanneer u de aanbevelingen van Cloudyn opvolgt. De werkelijke besparingen en het percentage dat is bespaard, zijn rood gemarkeerd.

- **Vergelijking van RDS RI-typen**: in deze sectie wordt het belangrijkste rendement van de aanbevolen implementatie benadrukt, inclusief alle relevante opties. Voor de resultaten in dit rapport wordt ervan uitgegaan dat de machine is uitgevoerd met een bedrijfstijd van 100%. Klik op **Inzoomen** om het gedetailleerde rapport te openen voor de geselecteerde machine.
- **Instanties in de loop van de tijd**: in deze sectie wordt een uitsplitsing weergegeven van alle instanties die zijn gekoppeld aan de aanbeveling, OnDemand, Reserved Instances en Spot. Klik op **Inzoomen** om het gedetailleerde rapport te openen.

- **Break-evenpunten**: in deze sectie wordt een tabel weergegeven met alle mogelijke aanbevolen implementaties, en het rendement en de maand waarin deze heeft plaatsgevonden. Klik op **Inzoomen** om het gedetailleerde rapport te openen.

### <a name="rds-reservations-over-time-report"></a>Rapport RDS-reserveringen in de loop van de tijd

Gebruik het rapport RDS-reserveringen in de loop van de tijd om een uitsplitsing te zien van zowel uw gebruikte als niet-gebruikte reserveringen tijdens een specifieke periode.

### <a name="reserved-instance-purchase-impact-report"></a>Rapport Impact van RI-aankoop

Met het rapport Impact van EC2 RI-aankoop kunt u de kosten voor gereserveerde instanties versus de kosten op aanvraag in de loop van de tijd simuleren. Dit rapport kan u helpen betere aankoopbeslissingen te nemen. Pas de filters aan, zoals gemiddelde uitvoering, termijn, platform, en meer, om weloverwogen beslissingen te maken, wanneer u overweegt om RI’s aan te schaffen.

### <a name="cost-effective-sizing-recommendations-report"></a>Rapport Aanbevelingen voor rendabel aanpassen van de grootte

Het rapport Aanbevelingen voor rendabel aanpassen van de grootte biedt resultaten voor AWS en Azure. Voor AWS-gebruikers: uw RI-aankopen worden in overweging genomen, en machines die worden uitgevoerd als RI’s, zijn niet opgenomen in de resultaten. Dit rapport biedt een lijst met te weinig gebruikte instanties die in aanmerking komen voor inkrimping. Aanbevelingen zijn gebaseerd op uw gebruiks- en prestatiegegevens in de afgelopen 30 dagen. In elke aanbeveling vindt u een lijst met kandidaten voor inkrimping, de reden voor de inkrimping, en een koppeling om de volledige details en metrische prestatiegegevens van de instantie te bekijken. En wanneer op basis van relevante aanbevelingen wordt geadviseerd om over te stappen op instantietypen van een nieuwere generatie.

U kunt de lijst met instantie-id’s die zijn aanbevolen voor inkrimping, niet downloaden vanuit dit rapport. Als u instantie-id’s wilt downloaden, gebruikt u het rapport Alle aanbevelingen voor aanpassen van de grootte.

Bekijk het volgende voorbeeld van inkrimping:

U hebt zes actieve m3.xlarge-instanties. In een Cloudyn-analyse ziet u dat voor vijf van deze instanties weinig CPU wordt gebruikt. U kunt overwegen in te krimpen.

In Kostenimpact wordt de impact op de kosten berekend. In dit voorbeeld ziet u, door het regelitem uit te vouwen, dat de huidige prijs voor één m3.xlarge-instantie (Linux/Unix) $ 0,266 per uur is, en dat één m3.large-instantie (Linux/Unix) $ 0,133 per uur kost. De jaarlijkse kosten voor vijf m3.xlarge-instanties die worden uitgevoerd met 100% gebruik, zijn dus $ 11.651. De jaarlijkse kosten voor vijf m3.xlarge-instanties die worden uitgevoerd met 100% gebruik, zijn $ 5.825. De mogelijke besparing is $ 5.825.

Als u redenen voor rendabele aanpassingen van de grootte wilt bekijken, klikt u op het plusteken (+) om het regelitem uit te vouwen. In **Details**:

- In de sectie **Reden voor aanbeveling** ziet u de huidige implementatie en het aantal instanties dat wordt aanbevolen voor inkrimping.
- In de sectie **Kostenimpact** ziet u de berekening die wordt gebruikt om mogelijke besparingen te bepalen.
- In de sectie **Mogelijke jaarlijkse besparingen** ziet u de mogelijke jaarlijkse besparingen bij een inkrimping zoals aanbevolen in Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Rapport Alle aanbevelingen voor aanpassen van de grootte

Dit rapport biedt een lijst met te weinig gebruikte instanties die in aanmerking komen voor inkrimping. De aanbevelingen zijn gebaseerd op uw gebruiks- en prestatiegegevens in de afgelopen 30 dagen. In elke aanbeveling kunt u de volledige details en metrische prestatiegegevens van de instantie bekijken.

Als u gereserveerde AWS-instanties hebt aangeschaft, bevat dit rapport resultaten voor alle actieve instanties, inclusief instanties die worden uitgevoerd als RI’s.

Gebruik het rapport Aanbevelingen voor rendabele aanpassingen van de grootte om:

- Een lijst met alle instanties te zien die in aanmerking komen voor inkrimping.
- Een rapportlijst te exporteren met hierin de namen en id's van instanties.

Klik op **+** om de details uit te vouwen voor meer informatie over aanbevelingen voor een specifieke instantie. De sectie Aanbevelingsdetails biedt een overzicht van de aanbevelingen.

De sectie **Tags** biedt de lijst met tagsleutels en -waarden voor de geselecteerde instantie. Gebruik Tags in het linkerdeelvenster om de sectie te filteren.

In de sectie **CPU-gebruik** ziet u het CPU-gebruik voor de instantie in de afgelopen maand, per dag.

Klik op de grafiek om het rapport Instantie-CPU in de loop van de tijd te openen, en zoom in voor een uitsplitsing van de instanties.

- Gebruik de **velden Weergeven/Verbergen** om velden toe te voegen of te verwijderen: Tijdstempel, Gemiddeld CPU-gebruik, Minimaal CPU-gebruik, Maximaal CPU-gebruik.
- Gebruik **Datumbereik** om een datum of datumbereik in te voeren, en in te zoomen op een specifieke instantie-id.
- Gebruik **Uitgebreide filters** om alle instantie-id’s of een specifieke instantie-id weer te geven
- Klik op **Inzoomen** om het rapport CPU-gebruik te openen

Als het exemplaar al 30 dagen niet is gecontroleerd, worden onvolledige gegevens weergegeven.

De sectie **Geheugengebruik (GB)** biedt informatie over het gebruikte geheugen. Voor AWS-gebruikers: metrische geheugengegevens zijn niet automatisch beschikbaar en moeten per instantie worden toegevoegd via AWS. AWS brengt kosten bij u in rekening om metrische geheugengegevens voor EC2-instanties in te schakelen.

In de sectie **Geheugengebruik (%)** wordt het percentage gebruikt geheugen weergegeven.

In de sectie **Invoerverkeer voor netwerk** wordt een momentopname in de loop van de tijd weergegeven voor netwerkverkeer, gemiddeld en maximaal, voor de geselecteerde instantie. Beweeg de muisaanwijzer over de regels om de datum en het maximale verkeer op het desbetreffende tijdstip te bekijken. Klik op **Inzoomen** om het rapport Invoerverkeer voor netwerk te openen.

In de sectie **Uitvoerverkeer voor netwerk** wordt een momentopname weergegeven voor uitvoerverkeer voor het netwerk, voor de geselecteerde instantie. Beweeg de muisaanwijzer over de regels om de datum en het maximale verkeer op het desbetreffende tijdstip te bekijken. Klik op **Inzoomen** om het rapport Uitvoerverkeer voor netwerk te openen.

### <a name="instance-metrics-explorer-report"></a>Rapport Verkenner voor metrische instantiegegevens

In het rapport Verkenner voor metrische instantiegegevens worden per instantie de metrische gegevens voor cloudprestaties weergegeven. Gebruik het rapport om instanties te bekijken die te veel of te weinig worden gebruikt, op basis van CPU, geheugen, en de drempelwaarden voor metrische netwerkgegevens.

De prestaties in meerdere clouds per instantie bekijken:

1. Selecteer in **Datumbereik** een datum waarvoor u de prestaties wilt bekijken.
2. Selecteer in **Tags** eventuele tags die u wilt bekijken.
3. Selecteer in **Filters** de filters die u wilt weergeven in het rapport.
4. Pas in **Uitgebreide filters** de drempelwaarden voor het rapport aan voor:
    - Gemiddeld CPU-gebruik
    - Maximaal CPU-gebruik
    - Gemiddeld geheugengebruik
    - Maximaal geheugengebruik
5. Klik in **Uitgebreide filters** op **Weergeven**, en selecteer vervolgens het type instantie dat moet worden weergegeven.

De metrische gegevens van een specifiek instantie in de loop van de tijd weergeven:

- Ga naar het rapport Verkenner voor metrische instantiegegevens, en klik op **+** om de details te zien.

### <a name="rds-sizing-recommendations-report"></a>Rapport RDS-aanbevelingen voor aanpassen van de grootte

In het rapport RDS-aanbevelingen voor aanpassen van de grootte biedt RDS aanbevelingen voor het aanpassen van de grootte, om uw cloudgebruik te optimaliseren. Het rapport biedt een lijst met te weinig gebruikte instanties die in aanmerking komen voor inkrimping. De aanbevelingen van Cloudyn zijn gebaseerd op de gebruiks- en prestatiegegevens van de afgelopen 30 dagen. U kunt aanbevelingen filteren op Accountnaam, Regio, Instantietype en Status.

### <a name="sizing-threshold-manager-report"></a>Rapport Beheer van drempelwaarden voor aanpassen van de grootte

Ingebouwde aanbevelingen voor het aanpassen van de grootte in Cloudyn worden berekend met behulp van een complex algoritme, om nauwkeurige suggesties voor het aanpassen van de grootte te bieden. U kunt de drempelwaarden voor aanbevelingen voor inkrimping aanpassen.

Aanbevelingen voor handmatig wijzigen van de drempelwaarden voor aanpassen van de grootte:

1. Pas naar wens de volgende drempelwaarden aan in Beheer van drempelwaarden voor aanpassen van de grootte:
    - Gemiddeld CPU-gebruik (percentage)
    - Maximaal CPU-gebruik (percentage)
    - Gemiddeld geheugengebruik (percentage)
    - Maximaal geheugengebruik (percentage)
3. Klik op **Toepassen** om de wijzigingen op te slaan.
4. Wijzigingen worden onmiddellijk toegepast op al uw aanbevelingen.

Standaarddrempelwaarden herstellen:

- Klik in Beheer van drempelwaarden voor aanpassen van de grootte op **Standaardwaarden herstellen**.

### <a name="compute-instance-types-report"></a>Rapport Typen rekeninstanties

Gebruik het rapport Typen rekeninstanties om:

- Instantietypen te bekijken per Service, Familie, API-naam en Naam.
- Details te bekijken, zoals CPU, ECU, RAM en Netwerk.

U kunt de **Zoekfunctie** gebruiken om specifieke regelitems te zoeken.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Begrip van kostenrapporten](understanding-cost-reports.md) voor meer informatie over het gebruik van rapporten, zoals het aanpassen of opslaan en plannen van rapporten.
- Raadpleeg [Belangrijke metrische kostengegevens weergeven met dashboards](dashboards.md) voor meer informatie over de dashboards die deel uitmaken van Cloudyn, en over het maken van eigen aangepaste dashboards.
