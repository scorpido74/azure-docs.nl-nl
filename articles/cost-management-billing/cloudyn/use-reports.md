---
title: Cloudyn-rapporten gebruiken in Azure | Microsoft Docs
description: Dit artikel wordt beschreven voor het doel van de Cloudyn-rapporten die zijn opgenomen in de Cloudyn-portal kunt u ze effectief te gebruiken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 139ad67035a96fb67bd96c4abb14ce9e1f26bf4f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989057"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Rapporten die beschikbaar zijn in de Cloudyn-portal

Dit artikel wordt beschreven voor het doel van de Cloudyn-rapporten die zijn opgenomen in de Cloudyn-portal. Ook wordt beschreven hoe u de rapporten effectief kunt gebruiken. De meeste rapporten zijn intuïtief en een uniform uiterlijk hebben. De meeste van de acties die u in één rapport doen kunt, kunt u ook doen in andere rapporten. Zie voor een overzicht over het gebruik van Cloudyn-rapporten, waaronder over het aanpassen en opslaan of plannen van rapporten, [Understanding kostenrapportages](understanding-cost-reports.md).

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Azure Cost Management](../cost-management-billing-overview.md) voor meer informatie.

## <a name="report-types"></a>Rapporttypen

Er zijn drie typen van Cloudyn-rapporten:

- Te veel tijd rapporten. Bijvoorbeeld, het rapport Cost Over Time. Te veel tijd rapporten weergeven van een tijdreeks van gegevens tijdens een interval van de geselecteerde met een resolutie van vooraf gedefinieerde en een wekelijkse resolutie weergeven voor de laatste twee maanden. U kunt groeperen en filteren om in te zoomen op verschillende gegevenspunten.
  - Te veel tijd rapporten kunt u trends weergeven en pieken en afwijkingen te detecteren.
- Van analyserapporten. Bijvoorbeeld het rapport kosten analyse. Deze rapporten weergeven cumulatieve gegevens gedurende een periode van dat u definieert en groeperen en filteren op de gegevens toestaan.
  - Analyse van rapporten kunt u weergeven van pieken en anomaliedetectie-oorzaken te bepalen en om u een gedetailleerde uitsplitsing van uw gegevens weer te geven.
- Rapporten in tabelvorm. U kunt een rapport als een tabel weergeven, maar sommige rapporten worden weergegeven alleen als een tabel. Deze rapporten bieden gedetailleerde van lijsten met items.
  - Aanbevelingen zijn in tabelvorm rapporten: Er zijn geen visualisaties voor aanbevelingen. U kunt echter aanbeveling resultaten te visualiseren. Bijvoorbeeld, besparingen na verloop van tijd.
  - In tabelvorm rapporten zijn nuttig als u een lijst met acties of voor gegevens exporteren voor verdere verwerking. Bijvoorbeeld: een rapport terugstorting.

Kostenrapporten aantonen _werkelijke_ of _afgeschreven_ kosten.

Rapport met werkelijke kosten worden de betalingen gedurende de geselecteerde periode weergegeven. Bijvoorbeeld, worden alle eenmalige kosten zoals aankopen van gereserveerde instanties (RI) weergegeven in het rapport met werkelijke kosten als pieken in de kosten.

Afgeschreven Kostenrapporten verspreiden eenmalige kosten gedurende een periode van waarop ze van toepassing. Bijvoorbeeld eenmalige kosten voor de aankoop van gereserveerde instanties worden verdeeld over de reserveringstermijn en worden niet weergegeven als een piek. De afgeschreven weergave is de enige manier om te zien waar trends en verwachte kosten.

In sommige gevallen wordt de afschrijving weergegeven als een afzonderlijke rapport. Voorbeelden hiervan zijn de kosten voor analyse en Amortized Cost Analysis rapporten. In andere gevallen is afschrijving een rapport-beleid, zoals de kostentoewijzing en Cost Analysis rapporten.

U kunt een rapport voor de levering van periodieke plannen. Kosten rapporten kunnen een drempel instellen, zodat ze handig voor waarschuwingen.

## <a name="cost-analysis-vs-cost-allocation"></a>Kostenanalyse versus kostentoewijzing

_Kosten van analysis_ rapporten facturering gegevens van uw cloud-providers worden weergegeven. Met behulp van de rapporten, kunt u groeperen en inzoomen op verschillende gegevenssegmenten gespecificeerde uit het bestand facturering. De rapporten bieden gedetailleerde kosten navigatie voor onbewerkte factureringsgegevens van de cloudleverancier van uw.

Sommige _analysis kosten_ rapporten niet kosten groeperen op resourcetags. En factureringsgegevens op basis van een tag wordt alleen weergegeven in rapporten nadat u bij het toewijzen van kosten door het maken van een kosten model met [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

_Kostentoewijzing_ rapporten zijn beschikbaar nadat u hebt gemaakt een kosten model met [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn kosten-facturering en -gegevens worden verwerkt en _komt overeen met_ de gegevens in de gebruiks- en tag gegevens van uw accounts in de cloud. Cloudyn vereist zodat deze overeenkomt met de gegevens, toegang tot uw gegevens over het gebruik. Hebt u accounts die referenties ontbreken, ze zijn gelabeld als _niet-gecategoriseerde resources_.

## <a name="dashboards"></a>Dashboards

Dashboards in Cloudy bieden een weergave op hoog niveau van rapporten. Dashboards bestaan uit widgets en elke widget is in feite de miniatuur van een rapport. Wanneer u [pas rapporten](understanding-cost-reports.md#save-and-schedule-reports), u ze opslaat in Mijn rapporten en ze worden toegevoegd aan het dashboard. Zie voor meer informatie over dashboards [kosten voor belangrijke metrische gegevens over met dashboards weergeven](dashboards.md).

## <a name="budget-information-in-reports"></a>Budgetgegevens in rapporten

Veel Cloudyn-rapporten weergeven budgetgegevens nadat u deze handmatig hebt gemaakt. Rapporten weergegeven niet zodanig budgetgegevens wanneer u een budget gemaakt. Zie voor meer informatie, [Budget beheerinstellingen](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Rapporten en reporting-functies

Cloudyn bevat de volgende rapporten en rapportage functies.

### <a name="cost-navigator-report"></a>Rapport van kosten Navigator

Het rapport Cost Navigator is een snelle manier om uw facturering gebruik aan de hand van de weergave van een dashboard weer te geven. Er is een subset van filters en basic weergaven om direct een overzichtsweergave van de organisatie kosten weer te geven. Kosten worden weergegeven op datum. Omdat het rapport is bedoeld als een eerste weergave van uw kosten, het is niet zo flexibel of als uitgebreide als veel andere rapporten of aangepaste dashboards die u zelf maakt.

Standaard primaire weergaven in het rapport weergeven:

- Kosten gedurende een periode van een week balk grafiek weergeven. U kunt wijzigen de **datumbereik** datum bereik staafdiagram wijzigen.
- De uitgaven van de service met behulp van een cirkeldiagram.
- De categorisatie van de resource op label, met behulp van een cirkeldiagram.
- De uitgaven van kostentiteiten, met behulp van een cirkeldiagram.
- Kosten totaal, per datum in een lijst weergeven.

### <a name="cost-analysis-report"></a>Kostenanalyserapport

Het rapport Cost Analysis is een berekening van de showback en chargeback, op basis van uw beleid. Uw cloud-verbruik gedurende een geselecteerde periode na alle toewijzingsregels die worden toegepast op de kosten worden. Bijvoorbeeld, het op label, worden de kosten berekend klikt, gebeurt toewijzing van de kosten van de niet-gecodeerde resources en (optioneel) het gebruik van gereserveerde instanties worden toegewezen.

De beleidsregels die zijn ingesteld [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) worden gebruikt in de kostenanalyse rapport en de resultaten worden vervolgens gecombineerd met gegevens uit de onbewerkte gegevens van de cloudleverancier van uw.

Hoe wordt dit rapport berekend? De Cloudyn-service zorgt ervoor dat toewijzing behoudt de integriteit van elke gekoppelde account door toe te passen _affiniteit account_. Affiniteit zorgt ervoor dat een account die geen gebruik maakt van een specifieke service beschikt niet over de kosten van deze service is toegewezen. De kosten voor dat account in dat account blijven en niet worden berekend door de beleidsregels voor brontoewijzing. Bijvoorbeeld, wellicht u vijf gekoppelde accounts. Als er slechts drie hiervan storage-services gebruiken, klikt u vervolgens de kosten voor storage-services alleen toegewezen voor de labels in de drie accounts.

Het rapport Cost Analysis te gebruiken:

- Uw organisatie terugstorting/showback berekenen
- Alle uw kosten categoriseren
- Een samengevoegde weergave van uw volledige implementatie voor een specifiek tijdsbestek.
- Kosten weergeven door de tag categorieën op basis van beleid dat is gemaakt in het kostenmodel.

Het rapport Cost Analysis gebruiken:

1. Selecteer een datumbereik.
2. Tags toevoegen, indien nodig.
3. Groepen toevoegen.
4. Kies een kostenmodel dat u eerder hebt gemaakt.

### <a name="cost-over-time-report"></a>Rapport met overuren

De kosten op tijd rapport geeft de resultaten van de kostentoewijzing als tijdreeksen. Hiermee kunt u te zien van trends en onregelmatigheden detecteren in uw implementatie. Hier ziet u in feite kosten verdeeld zijn over een opgegeven periode. Het rapport bevat de belangrijkste kostenposten, met inbegrip van doorlopende kosten en de gereserveerde instantie eenmalige kosten die gedurende een geselecteerde periode worden besteed. Beleid instellen in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) in dit rapport worden gebruikt.

Het rapport Cost Over Time te gebruiken:

- Wijzigingen voor tijd en welke invloed wijzigen van één dag (of datumbereik) in het volgende te zien.
- Analyseer de kosten na verloop van tijd voor een specifiek exemplaar.
- Begrijpen waarom er een stijging van de kosten voor een specifiek exemplaar.

Het rapport Cost Over Time gebruiken:

1. Selecteer een datumbereik.
2. Tags toevoegen, indien nodig.
3. Groepen toevoegen.
4. Kies een kostenmodel dat u eerder hebt gemaakt.
5. Selecteer de werkelijke kosten of afgeschreven kosten.
6. Kies of u wilt de van toewijzingsregels toepassen op onbewerkte facturering gegevensweergave weergeven of berekend aan kosten weergeven.

### <a name="actual-cost-analysis-report"></a>Rapport met werkelijke kostenanalyse

Het rapport Actual Cost Analysis toont provider kosten zonder wijzigingen. Hier ziet u de belangrijkste kostenposten, met inbegrip van doorlopende kosten en eenmalige kosten.

U kunt het rapport gebruiken om gegevens over de kosten voor uw abonnementen weer te geven. In het rapport, Azure-abonnementen worden weergegeven als **accountnaam** en **accountnummer**. **Gekoppelde accounts** AWS-abonnementen weergeven. Om weer te geven onder per abonnementskosten, een uitsplitsing van de voor elk account **groepen**, selecteer het type abonnement dat u hebt.

Het rapport Actual Cost Analysis te gebruiken:

- Analyseren en controleren van onbewerkte provider kosten besteed aan het gedurende een opgegeven periode.
- Plannen van een drempelwaarde voor waarschuwing.
- Analyseer ongewijzigd kosten in rekening gebracht door uw accounts en entiteiten.

### <a name="actual-cost-over-time-report"></a>Rapport met werkelijke kosten gedurende een periode

Het rapport Actual Cost Over Time is een standaardkosten analyserapport kosten distribueren over een vastgesteld tijdvenster resolutie. Het rapport bevat uitgaven gedurende een bepaalde periode kunt u trends observeren en de bestedingslimiet onregelmatigheden detecteren. Dit rapport geeft de belangrijkste kostenposten, met inbegrip van doorlopende kosten en de gereserveerde instantie eenmalige kosten die gedurende een geselecteerde periode worden besteed.

Het rapport Actual Cost Over Time te gebruiken:

- Zie kostentrends na verloop van tijd.
- Onregelmatigheden niet vinden in de kosten.
- Alle kosten met betrekking tot vragen met betrekking tot cloudproviders gevonden.

### <a name="amortized-cost-reports"></a>Afgeschreven Kostenrapporten

Deze reeks afgeschreven kosten rapporten linearisatie bevat niet-gebruik op basis van servicekosten of eenmalige kosten van leveranciers en hun kosten verdeeld na verloop van tijd gelijkmatig gedurende hun levensduur. Bijvoorbeeld zijn eenmalige kosten:

- Jaarlijkse kosten van ondersteuning
- Jaarlijkse kosten van beveiliging onderdeel
- Kopen van gereserveerde instanties kosten
- Sommige items voor de Azure Marketplace

In het bestand facturering eenmalige kosten worden gekenmerkt wanneer het verbruik beginnen en eindigen datums (tijdstempel) gelijk zijn aan waarden hebben. De Cloudyn-service herkent deze vervolgens zoals eenmalige kosten die worden afgeschreven. Andere services op basis van gebruik met on-demand-gebruikskosten worden niet afgeschreven.

Afgeschreven Kostenrapporten zijn onder andere:

- Afgeschreven kostenanalyse
- Afgeschreven kosten na verloop van tijd

### <a name="cost-analysis-report"></a>Kostenanalyserapport

Het rapport Cost Analysis biedt inzicht in uw cloud-gebruik en uitgaven gedurende een geselecteerde periode. De beleidsregels die zijn ingesteld de [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) worden gebruikt in het rapport Cost Analysis.

Hoe wordt dit rapport in Cloudyn berekend?

Cloudyn zorgt ervoor dat toewijzing van de integriteit van elke gekoppelde account door toe te passen behouden _affiniteit account_. Affiniteit zorgt ervoor dat een account die geen gebruik maakt van een specifieke service ook de kosten van deze service is toegewezen aan deze beschikt niet over. De kosten voor dat account in dat account blijven en niet worden berekend door de beleidsregels voor brontoewijzing. Bijvoorbeeld, wellicht u vijf gekoppelde accounts. Als er slechts drie hiervan storage-services gebruiken, klikt u vervolgens de kosten voor storage-services alleen toegewezen voor de labels in de drie accounts.

Het rapport Cost Analysis te gebruiken:

- Een samengevoegde weergave van uw volledige implementatie voor een specifiek tijdsbestek.
- Kosten weergeven door de tag categorieën op basis van beleid dat is gemaakt in het kostenmodel.

### <a name="cost-over-time-report"></a>Rapport met overuren

Het rapport Cost Over Time weergegeven uitgaven gedurende een periode, zodat u trends en u ziet dat onregelmatigheden in uw implementatie kunt herkennen. Hier ziet u in feite kosten verdeeld zijn over een opgegeven periode. Het rapport bevat de belangrijkste kostenposten, met inbegrip van doorlopende kosten en de gereserveerde instantie eenmalige kosten die gedurende een geselecteerde periode worden besteed. Beleid instellen in [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) in dit rapport worden gebruikt.

Het rapport Cost Over Time te gebruiken:

- Wijzigingen voor tijd en welke invloed wijzigen van één dag (of datumbereik) in het volgende te zien.
- Analyseer de kosten na verloop van tijd voor een specifiek exemplaar.
- Begrijpen waarom er een stijging van de kosten voor een specifiek exemplaar.

### <a name="custom-charges-report"></a>Aangepaste kosten-rapport

Enterprise- en CSP-gebruikers vinden vaak zelf die extra services levert aan hun klanten externe of interne naast hun eigen resourceverbruik cloud. U definiëren aangepaste kosten voor extra services of kortingen die zijn toegevoegd aan de facturering van de klant of de doorberekeningsrapporten als aangepaste regels.

Aangepaste service kosten zijn de services die normaal gesproken worden niet in een factuur weergegeven. De aangepaste kosten die u maakt, worden vervolgens in Kostenrapporten weergegeven.

*Aangepaste kosten niet zijn aangepaste prijzen*. De lijst met aangepaste kosten wordt niet weergegeven in de verschillende tarieven die u mogelijk worden kosten in rekening gebracht. AWS facturering kosten worden bijvoorbeeld weergegeven net zoals ze worden in rekening gebracht.

Maken van een aangepaste kosten in rekening gebracht:

1. In **aangepaste kosten**, klikt u op **nieuwe toevoegen**. De _nieuwe aangepaste kosten toevoegen_ in het dialoogvenster wordt weergegeven.
2. In **providernaam**, voer de naam van de provider.
3. In **servicenaam**, geeft u het type van de service.
4. In **beschrijving**, Voeg een beschrijving op voor de aangepaste kosten in rekening gebracht.
5. In **Type**, voer de select **Percentage** en selecteer vervolgens in de vervolgkeuzelijst Services, de services moeten worden opgenomen als aangepaste kosten in rekening gebracht in de Kostenrapporten.
6. In **betaling**, selecteer deze optie als de kosten een eenmalig bedrag of terugkerende kosten is. Als de kosten voor een terugkerende kosten, selecteert u Amortized als u wilt dat de kosten om te worden afgeschreven en selecteer het aantal maanden.
7. In **datums**, als een eenmalige kosten is ingeschakeld, in **ingangsdatum**, voer de datum in de kosten wordt betaald. Als terugkerende kosten is geselecteerd, voert u het datumbereik met inbegrip van de begindatum en de einddatum van de kosten in rekening gebracht.
8. In de **structuur entiteiten**, selecteer de entiteiten die u wilt de kosten van toepassing en selecteer vervolgens **op**.

_Wanneer er kosten worden toegewezen aan een entiteit, kunnen gebruikers deze niet wijzigen. Kosten die door een beheerder aan een bovenliggende entiteit worden toegevoegd, zijn alleen-lezen._

Aangepaste kosten weergeven:

Aangepaste kosten worden in Kostenrapporten weergegeven. Open het rapport Actual Cost Analysis, bijvoorbeeld, klikt u vervolgens onder **Extended Filters**, selecteer **zelfstandige**. Vervolgens filteren op **aangepaste kosten**.

### <a name="cost-allocation-360"></a>Cost Allocation 360

U Cost Allocation 360 gebruiken om aangepaste cost allocation modellen als u wilt kosten toewijzen aan verbruikte cloudresources te maken. Veel rapporten weergegeven gegevens uit aangepaste kostenmodellen die u hebt gemaakt met aangepaste kostenmodellen. En sommige rapporten worden alleen informatie weergegeven nadat u een aangepaste kostenmodel met kostentoewijzing hebt gemaakt.

Zie voor meer informatie over het maken van aangepaste kostenmodellen [zelfstudie: kosten beheren met behulp van Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Rapport over kosten versus budget gedurende een periode

Met het rapport kosten versus budget over tijd kunt u de inzenders van de belangrijkste kosten vergelijken met uw budget. Het toegewezen budget weergegeven in het rapport, zodat u uw budget (boven/onder/par) gebruik na verloop van tijd kunt bekijken. Velden weergeven/verbergen met aan de bovenkant van het rapport, kunt u selecteren voor weergave kosten, budget, totale kosten en totaal budget.

### <a name="current-month-projected-cost-report"></a>Huidige Month Projected Cost-rapport

Het rapport Current Month Projected Cost biedt inzicht in uw huidige maand tot heden kosten samenvatting. Dit rapport geeft de kosten vanaf het begin van maand van de vorige maand en de totale geschatte kosten voor de huidige maand. De huidige maand geschatte kosten worden berekend als de som van de maandelijkse kosten die up-to-date en een projectie op basis van de kosten bewaakt in de afgelopen 30 dagen.

Het rapport Current Month Projected Cost te gebruiken:

- Project maandelijkse kosten per service
- Project maandelijkse kosten per account

### <a name="annual-projected-cost-report"></a>Rapport met jaarlijkse geschatte kosten

Het rapport met jaarlijkse geschatte kosten kunt u om jaarlijkse geschatte kosten op basis van trends in de vorige uitgaven weer te geven. Hier ziet u de volgende 12 maanden van de totale geschatte kosten. De prognoses worden gemaakt met behulp van een functie trend geëxtrapoleerd in de volgende 12 maanden, op basis van de kosten die zijn gekoppeld aan de afgelopen 30 dagen van het gebruik van.

### <a name="budget-management-settings"></a>Instellingen voor het beheer van budget

Budget Management kunt u een budget instellen voor het fiscale jaar.

Een budget toevoegen aan een entiteit:

1. Op de pagina budgetbeheer onder **entiteiten**, selecteer de entiteit waarin u wilt maken van het budget.
2. Selecteer in het budgetjaar, het jaar waarin u wilt maken van het budget.
3. Stel uw budget in elke maand, en vervolgens op **opslaan**.

Voor het importeren van een bestand voor de jaarlijkse budget:

1. Onder **acties**, selecteer **exporteren** voor het downloaden van een lege sjabloon CSV moet worden gebruikt als de basis voor het budget.
2. Vul in het CSV-bestand met uw budget-fragmenten en lokaal opslaan.
3. Onder **acties**, selecteer **importeren**.
4. Selecteer uw opgeslagen bestand en klik vervolgens op **OK**.

Uw voltooide budget onder exporteren als een CSV-bestand, **acties**, selecteer **exporteren** om het bestand te downloaden.

Wanneer u klaar bent, wordt uw budget weer gegeven in de rapporten over de kosten analyse en in het rapport kosten versus budget gedurende een periode. U kunt ook rapporten op basis van budgetdrempels plannen.

### <a name="azure-resource-explorer-report"></a>Azure Resource Explorer-rapport

De Azure Resource Explorer-rapport bevat een bulk-lijst van alle Azure-resources beschikbaar zijn in Cloudyn. Als u wilt effectief gebruikt u het rapport, moeten metrische gegevens die zijn ingeschakeld op uw Azure-accounts hebt uitgebreid. Uitgebreide metrische gegevens geven Cloudyn toegang tot uw Azure VM's. Zie voor meer informatie, [toevoegen metrische gegevens voor virtuele machines van Azure uitgebreid](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Rapport van Azure-Resources gedurende een periode

Azure-Resources gedurende een periode dit rapport bevat een overzicht van alle resources die worden uitgevoerd gedurende een bepaalde periode. Als u wilt effectief gebruikt u het rapport, moeten metrische gegevens die zijn ingeschakeld op uw Azure-accounts hebt uitgebreid. Uitgebreide metrische gegevens geven Cloudyn toegang tot uw Azure VM's. Zie voor meer informatie, [toevoegen metrische gegevens voor virtuele machines van Azure uitgebreid](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Verkenner-instantierapport

Het exemplaar Explorer-rapport wordt gebruikt om verschillende metrische gegevens voor de activa van uw virtuele machines weer te geven. U kunt inzoomen in specifieke exemplaren om informatie te bekijken, zoals:
- Exemplaar met intervallen
- Levenscyclus van in de geselecteerde periode
- CPU-gebruik
- Netwerk-invoer
- Uitvoer-verkeer
- Actieve schijven

Het rapport exemplaar Explorer verzamelt alle actieve intervallen binnen het bereik van de opgegeven datum en verzamelt gegevens dienovereenkomstig. Als u wilt weergeven van de actieve intervallen in het datumbereik dat, vouw het exemplaar. De kosten van elke instantie worden berekend voor de datum bereik is geselecteerd op basis van AWS en Azure prijzen. Er is geen kortingen zijn toegepast. U kunt extra velden toevoegen aan de lijst met velden weergeven/verbergen.

Exemplaar Explorer-rapport te gebruiken:

- De geschatte kosten per machine berekenen.
- Maak een volledige lijst, met inbegrip van geaggregeerde actieve uur, van alle machines die in een periode actief waren.
- Een lijst door cloud serviceprovider of een account maken.
- Machines weergeven die zijn gemaakt of beëindigd in een periode.
- Alle momenteel gestopt machines weergeven.
- De labels van elke machine weergeven.

### <a name="instances-over-time-report"></a>Instanties gedurende een periode rapport

Met behulp van het rapport instanties gedurende een periode, ziet u het maximum aantal machines dat actief waren elk tijdens het geselecteerde tijdsbereik. Als de resolutie van de gedefinieerde per week of maand is, worden de resultaten het maximum aantal machines actief zijn op elke dag gedurende die maand. Selecteer een datumbereik om de filters die u wilt weergeven in het rapport te selecteren.

### <a name="instance-utilization-over-time-report"></a>Gebruik gedurende een periode-instantierapport

Dit rapport bevat een overzicht van de CPU of geheugen gebruik na verloop van tijd voor al uw instanties.

### <a name="compute-power-cost-over-time-report"></a>COMPUTE-kracht Cost Over Time-rapport

De Compute-kracht Over Time-rapport bevat een verdeling van compute-kracht gedurende een opgegeven datumbereik. Hoewel andere rapporten weergeven voor het aantal actieve machines of de runtime-uur, wordt dit rapport bevat Core-uren, Compute unit-uren of GB RAM-uren.

Het rapport te gebruiken:

- Controleer de compute-kracht binnen een opgegeven datumbereik.
- Tijden weergeven berekenen op basis van cost allocation modellen.

Dit rapport is gekoppeld aan uw [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beleid, zodat de resultaten worden weergegeven op basis van de gedefinieerde tagging en het beleid uw geselecteerde kosten-beleid. Wanneer u hebt geen een beleid hebt gemaakt, en vervolgens de resultaten worden niet weergegeven.

### <a name="compute-power-average-cost-over-time-report"></a>COMPUTE-kracht gemiddelde Cost Over Time-rapport

U de Compute-kracht gemiddelde Cost Over Time-rapport gebruiken om meer dan alleen de kosten van elke actieve machine weer te geven. Dit rapport bevat de gemiddelde kosten per instantie-uur, core-uur, compute-eenheidsuur en GB RAM-uur. Het rapport biedt inzicht in de efficiëntie van uw implementatie.

Dit rapport is gekoppeld aan uw [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beleid, zodat de resultaten worden weergegeven op basis van de gedefinieerde tagging en het beleid uw geselecteerde kosten-beleid. Wanneer u hebt geen een beleid hebt gemaakt, en vervolgens de resultaten worden niet weergegeven.

### <a name="s3-cost-over-time-report"></a>S3 Cost Over Time-rapport

Het rapport Cost Over Time S3 bevat een verdeling van de Amazon Simple Storage-Service (S3) kosten per bucket na verloop van tijd voor een opgegeven periode. Het rapport kunt u zoeken naar de buckets die de stuurprogramma's van uw belangrijkste kosten en laat trends zien in de S3-gebruik en uitgaven.

### <a name="s3-distribution-of-cost-report"></a>Rapport met S3 verdeling van kosten

Het rapport gebruiken voor het analyseren van de S3-kosten voor de afgelopen maand door bucket en opslag-klasse. U kunt de weergave cirkeldiagram gebruiken om in te stellen de zichtbaarheidsdrempel voor. Of u kunt de tabelweergave gebruiken om te zien van subtotalen.

### <a name="s3-bucket-properties-report"></a>Eigenschappen van de S3-Bucket-rapport

Het rapport gebruiken om eigenschappen van de S3-bucket weer te geven. U kunt de weergave cirkeldiagram gebruiken om in te stellen de zichtbaarheidsdrempel voor. Of u kunt de tabelweergave gebruiken om te zien van subtotalen.

### <a name="rds-instances-over-time-report"></a>Rapport van de extern bureaublad-services-instanties gedurende een periode

Het rapport gebruiken om een overzicht van alle Amazon Relational Database Service (RDS)-exemplaren die worden uitgevoerd tijdens de opgegeven periode weer te geven.

### <a name="rds-active-instances-report"></a>Rapport over actieve exemplaren van extern bureaublad-services

Het rapport gebruiken voor het analyseren van actieve exemplaren van extern bureaublad-services. Vouw in het rapport uit het regelitem om aanvullende informatie weer te geven.

### <a name="azure-reserved-instances-report"></a>Rapport voor Azure gereserveerde instanties

Het rapport gereserveerde instanties voor Azure biedt u een weergave van alle gereserveerde instanties van uw Azure. Dit rapport geeft elke aankoop weer als een eigen regelitem is. Dit rapport bevat ook informatie over die aankoop, zoals het account dat u die hebt aangeschaft, het type van de aankoop en Instantietype, enzovoort resterende dagen. U kunt weergeven of verbergen van gegevens met behulp van de velden weergeven/verbergen.

Het rapport voor Azure gereserveerde instanties gebruiken om weer te geven:

- Een lijst met alle reserveringen kopen op datum.
- Totdat de gereserveerde instantie verloopt resterende tijd.
- Eenmalige kosten.
- Het account dat de aangeschafte gereserveerde instanties, en wanneer.

### <a name="aws-reserved-instances-report"></a>Rapport van gereserveerde AWS-instanties

Het rapport gereserveerde AWS-instanties biedt dat u met een weergave van alle AWS gereserveerde instanties. Dit rapport dat geeft elke aankoop is een eigen regelitem en meer informatie over die aankoop, zoals het account dat u die hebt aangeschaft, het type van de aankoop en Instantietype, enzovoort resterende dagen. U kunt weergeven of verbergen van gegevens met behulp van de velden weergeven/verbergen.

Gebruik het rapport gereserveerde AWS-instanties om weer te geven:

- Een lijst met alle reserveringen kopen op datum.
- Totdat de gereserveerde instantie verloopt resterende tijd.
- Eenmalige kosten.
- Oorspronkelijke aankoop-ID (Reserverings-ID).
- Het account dat gereserveerde instanties aangeschaft en wanneer.

### <a name="ec2-ri-buying-recommendations-report"></a>Rapport EC2 RI Buying Recommendations

De basis van resourceverbruik cloud is het model op aanvraag, waar resources kosten wanneer gebruikt. Er zijn geen verplichtingen vooraf te betalen, u betaalt alleen voor wat u gebruikt, wanneer u deze gebruikt.

AWS biedt een alternatief prijsmodel voor de elastische Cloud Compute (EC2)-services: de gereserveerde instanties (RI). Gebruikers van dit prijsmodel garandeert de capaciteit wanneer ze deze nodig voor de duur van de gereserveerde instantie hebt. De gereserveerde instantie biedt aanzienlijke prijskortingen op on-demand-tarieven. Hierna vooraf gebruikers een toezegging voor het gebruik van een virtuele-exemplaar. De toezegging is gebonden aan een specifieke familie, grootte, binnen een beschikbaarheidszone (AZ) en besturingssysteem, gedurende de periode van toezegging (één of drie jaar). De gereserveerde instantie kunt AWS efficiënt plannen toekomstige capaciteit, evenals garantie voor klant toezegging voor het gebruik van de services krijgen.

Drie betalingsopties voor gereserveerde instanties, die alle vooraf zijn:

- Som bulksgewijs op dag 0, biedt de meeste korting
- Geen kosten vooraf - bieden in waarmee de kosten van gereserveerde instantie wordt betaald in termijnen van maandelijkse in de loop van de gereserveerde instantie, de laagste korting
- Gedeeltelijk kosten vooraf, in welke ¼ - ½ van de prijs, vooraf betaalde en de rest in maandelijkse supplementen, met een korting van lager, sluit maar, aan het tarief voor alle vooraf

Cloudyn evalueert de uptime van elke computer voor de afgelopen 30 dagen. Cloudyn raadt het kopen van gereserveerde instanties wanneer deze kosteneffectiever om uit te voeren van de machine met een gereserveerde instantie op het niveau van de huidige actieve tijdsduur.

Dit rapport bevat de reden voor de aanbevelingen voor de meeste besparen ten opzichte van het jaar. De aanbevelingen voorgesteld vervangen van on-demand instanties met gereserveerde instanties. U kunt gereserveerde instanties kopen rechtstreeks vanuit het rapport.

Elk tabblad wordt geopend als een volledig rapport. Belangrijkste secties in tabbladen zijn onder andere:

- **EC2 RI Purchase Impact** -in deze sectie biedt een simulatie van het verschil tussen on-demand versus gereserveerde instanties. Klik op **inzoomen**om het volledige EC2 RI Purchase Impact rapport met de filters die al op de aanbeveling is gedefinieerd. Dit rapport bevat de impact van aanschaf van alle potentiële RI-aankopen. U kunt de verwachte gemiddelde uptime om te zien van de mogelijke opslaan bij de aankoop van gereserveerde EC2-instanties kunt aanpassen.

- **Opslaan van analyse** -deze sectie bevat de potentiële besparingen als gevolg en de maand de besparingen zijn actualized bij Cloudyn aanbevelingen. De daadwerkelijke besparingen en het percentage opgeslagen zijn in rood gemarkeerd.

- **EC2 RI Type vergelijking** -in deze sectie ligt de nadruk op het rendement op investering opleveren belangrijke functies van aanbevolen implementatie van Cloudyn, met inbegrip van alle relevante opties. De resultaten in dit rapport wordt ervan uitgegaan dat de machine wordt uitgevoerd op 100% uptime. Klik op **inzoomen** om het gedetailleerde rapport te openen.

- **Instanties gedurende een periode** -deze sectie vindt u een overzicht van alle exemplaren die zijn gekoppeld aan de aanbeveling, OnDemand, gereserveerde instanties en positie. Klik op **inzoomen** om het gedetailleerde rapport te openen.
- **Break-even punten** -deze sectie vindt u een tabel met alle de mogelijke aanbevolen implementaties en het rendement op investering opleveren en de maand wanneer het rendement op investering opleveren optreedt. Klik op **inzoomen** om het gedetailleerde rapport te openen.

### <a name="ec2-reservations-over-time-report"></a>Rapport EC2 reserveringen gedurende een periode

Het rapport EC2 reserveringen gedurende een periode houdt de status van uw gebruik van uw gekochte EC2 RI's. U kunt de resolutie van het rapport instellen op uur, dag of week.

Het rapport te gebruiken:

- Reserveringen die zijn aangeschaft en die worden gebruikt en niet gebruikt worden weergegeven.
- Zoom op de oplossing per uur voor gebruik van gereserveerde instanties per uur.

### <a name="savings-over-time-report"></a>Rapport van de besparingen gedurende een periode

Het rapport besparingen gedurende een periode gebruiken om de besparingen bereikt met behulp van gereserveerde instanties, evenals spot-exemplaren weer te geven. Dit rapport bevat het rendement op investering opleveren bereikt gedurende een periode die voortvloeien uit RI-aankopen.

Als u wilt weergeven besparingen door gereserveerde instanties, de resultaten door groeperen **prijsmodel** en selecteer **reservering**. Als u wilt weergeven van gereserveerde instanties besparingen als gevolg van een specifiek account of een Instantietype, moet u de relevante groepering en filter toevoegen aan het type account of het exemplaar.

Als u wilt zien besparingen van positie-exemplaar gebruikt, filteren de **prijsmodel** naar **positie**. Het standaardfilter voor dit rapport is gereserveerde instanties en Spot Instances.

### <a name="rds-ri-buying-recommendations-report"></a>Extern bureaublad-services RI Buying Recommendations-rapport

Extern bureaublad-services RI Buying Recommendations rapport raadt u aan bij het gebruik van gereserveerde instanties voor extern bureaublad-services in plaats van on-demand instanties.

Elk tabblad wordt geopend als een volledig rapport. Belangrijkste secties in tabbladen zijn onder andere:

- **Extern bureaublad-services RI Purchase Impact** -in deze sectie biedt een simulatie van het verschil tussen de on-demand versus gereserveerde instanties. Klik op **inzoomen** om te zien van het volledige rapport van de extern bureaublad-services RI Purchase Impact met de filters die al op de aanbeveling is gedefinieerd. Dit rapport kunt u het effect van aanschaf van alle potentiële RI-aankopen.  U kunt aanpassen van de verwachte gemiddelde uptime en het potentieel opslaan door het kopen van gereserveerde instanties bekijkt.
- **Opslaan van analyse** : deze sectie bevat de potentiële besparingen als gevolg en de maand de besparingen zijn actualized bij Cloudyn aanbevelingen. De daadwerkelijke besparingen en het percentage opgeslagen zijn in rood gemarkeerd.

- **Extern bureaublad-services RI Type vergelijking** -in deze sectie ligt de nadruk op het rendement op investering opleveren belangrijke functies van de aanbevolen implementatie, met inbegrip van alle relevante opties. De resultaten in dit rapport wordt ervan uitgegaan dat de machine wordt uitgevoerd op 100% uptime. Klik op **inzoomen** om het gedetailleerde rapport voor de geselecteerde computer te openen.
- **Instanties gedurende een periode** : deze sectie vindt u een overzicht van alle exemplaren die zijn gekoppeld aan de aanbeveling, OnDemand, gereserveerde instanties en positie. Klik op **inzoomen** om het gedetailleerde rapport te openen.

- **Break-even punten** : deze sectie vindt u een tabel met alle de mogelijke aanbevolen implementaties en het rendement op investering opleveren en de maand wanneer het rendement op investering opleveren optreedt. Klik op **inzoomen** om het gedetailleerde rapport te openen.

### <a name="rds-reservations-over-time-report"></a>Rapport van de extern bureaublad-services reserveringen gedurende een periode

Het rapport extern bureaublad-services-reservering gedurende een periode gebruiken om een uitsplitsing van uw gebruikt en niet-gebruikte reserveringen in de opgegeven periode weer te geven.

### <a name="reserved-instance-purchase-impact-report"></a>Reserved Instance Purchase Impact rapport

Het rapport EC2 RI Purchase Impact kunt u de kosten van gereserveerde instanties en op aanvraag kosten simuleren na verloop van tijd. U kunt beter aankopen beslissingen te nemen. De filters, zoals gemiddelde uitvoeringstijd, term, platform en anderen, zodat weloverwogen beslissingen te nemen wanneer u rekening houden met gereserveerde instanties aankopen aanpassen.

### <a name="cost-effective-sizing-recommendations-report"></a>Rendabele Sizing Recommendations-rapport

Het rapport voordelige Sizing Recommendations bevat de resultaten voor AWS en Azure. Uw aankopen in de gereserveerde instanties in aanmerking worden genomen voor AWS-gebruikers, en de resultaten bevatten geen machines die worden uitgevoerd als gereserveerde instanties. Dit rapport bevat een lijst met weinig gebruikte instanties die kandidaten om te krimpen. Aanbevelingen zijn gebaseerd op de gegevens van uw gebruik en de prestaties van de afgelopen 30 dagen. In elke aanbeveling is een lijst met kandidaten om te krimpen, de rechtvaardiging te krimpen en een koppeling om meer informatie weer te geven en maatstaven voor prestaties van het exemplaar. En wanneer relevante aanbevelingen advies om nieuwere generatie exemplaartypen te wijzigen.

U kunt de lijst met de exemplaar-id's die worden aanbevolen voor krimpen vanuit dit rapport niet downloaden. Als u wilt downloaden exemplaar-id's, gebruikt u het rapport alle Sizing Recommendations.

Houd rekening met het volgende downsizing-voorbeeld:

U hebt zes exemplaren van m3.xlarge die worden uitgevoerd. Analyse van Cloudyn laat zien dat vijf hiervan minder CPU-gebruik hebben. U kunt verkleinen alvorens ze te.

In de Impact kosten, wordt de impact van de kosten berekend. In dit voorbeeld wordt ziet door het uitbreiden van het regelitem, u dat de huidige prijs voor één m3.xlarge exemplaar (Linux/Unix) kosten $0.266 per uur en één m3.large exemplaar (Linux/Unix) kosten $0.133 per uur. Dus, is de jaarlijkse kosten $11,651 voor vijf m3.xlarge-exemplaren die worden uitgevoerd op 100% gebruik. De jaarlijkse kosten is $5,825 voor vijf m3.large-exemplaren die worden uitgevoerd op 100% gebruik. De potentiële besparingen zijn $5,825.

Klik om te bekijken van de redenen voor goedkope schaaloplossingen + om uit te breiden het regelitem. In **Details**:

- De **aanbeveling reden** sectie vindt u de huidige implementatie en het aantal exemplaren aangeraden te krimpen.
- De **Impact van de kosten** sectie vindt u de berekening die wordt gebruikt om te bepalen, potentiële besparingen.
- De **jaarlijkse besparingen** sectie vindt u de jaarlijkse besparingen als verkleinen alvorens te per aanbeveling van Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Alle Sizing Recommendations-rapport

Dit rapport bevat een lijst met weinig gebruikte instanties die kandidaten om te krimpen. De aanbevelingen zijn gebaseerd op de gegevens van uw gebruik en de prestaties van de afgelopen 30 dagen. In elke aanbeveling vindt u meer informatie en maatstaven voor prestaties van het exemplaar.

Als u gereserveerde AWS-exemplaren hebt aangeschaft, bevat dit rapport resultaten voor alle actieve exemplaren, met inbegrip van exemplaren die worden uitgevoerd als gereserveerde instanties.

Het rapport alle Sizing Recommendations te gebruiken:

- Een overzicht van alle exemplaren die kandidaten om te krimpen.
- Een rapportenlijst met namen van exemplaren en -id's exporteren.

Als u wilt weergeven van details van de aanbeveling voor een specifiek exemplaar, klikt u op **+** om uit te breiden de details. De Details van de aanbeveling-sectie biedt een overzicht van de aanbeveling.

De **Tags** sectie bevat de lijst met de tagsleutels en waarden voor het geselecteerde exemplaar. Tags gebruiken in het linkerdeelvenster naar de sectie filteren.

De **CPU-gebruik** sectie vindt u het CPU-gebruik voor het exemplaar in de afgelopen maand per dag.

Klik op de grafiek als u wilt inzoomen en het exemplaar CPU via tijd rapport openen om te zien van een uitsplitsing van de exemplaren.

- Gebruik **velden weergeven/verbergen** toevoegen of verwijderen van velden: Timestamp, CPU van de Avg, Min CPU, maximaal CPU.
- Gebruik **datumbereik** in te voeren van een datum of datumbereik en Zoom in op een specifieke InstanceID.
- Gebruik **Extended Filters** Alles weergeven of een specifiek exemplaar-ID
- Klik op **inzoomen** om de CPU-gebruik rapport te openen

Als het exemplaar nog niet zijn bewaakt gedurende 30 dagen, wordt onvolledige gegevens weergegeven.

De **geheugen gebruik (GB)** sectie bevat informatie over het geheugen gebruikt. Voor AWS-gebruikers, geheugen-metrische gegevens zijn niet automatisch beschikbaar en moet per exemplaar via AWS worden toegevoegd. AWS brengt u metrische gegevens voor de EC2-instanties die geheugen inschakelen.

De **geheugengebruik (%)** sectie vindt u het percentage gebruikt geheugen.

De **invoer netwerkverkeer** sectie vindt u een momentopname van een na verloop van tijd van het netwerkverkeer, gemiddelde en maximale, voor het geselecteerde exemplaar. Beweeg de muisaanwijzer over de regels om te zien van de datum en de maximale verkeer voor die tijd. Klik op **inzoomen** om het netwerk invoer verkeer rapport te openen.

De **uitvoer netwerkverkeer** sectie vindt u een momentopname van het netwerkverkeer van de uitvoer voor het geselecteerde exemplaar. Beweeg de muisaanwijzer over de regels om te zien van de datum en de maximale verkeer voor die tijd. Klik op **inzoomen** om het netwerkverkeer van de uitvoer-rapport te openen.

### <a name="instance-metrics-explorer-report"></a>Metrics Explorer-instantierapport

Het exemplaar Metrics Explorer-rapport bevat cross-cloud metrische prestatiegegevens per exemplaar. Gebruik het rapport om weer te geven van exemplaren die worden boven of onder gebruikt, is afhankelijk van CPU, geheugen en metrische drempels netwerk.

Cross-cloud-prestaties per exemplaar weergeven:

1. In **datumbereik**, selecteer een datumbereik waarvoor u wilt weergeven van prestaties.
2. In **Tags**, selecteert u alle tags die u wilt weergeven.
3. In **Filters**, selecteert u de filters die u wilt weergeven in het rapport.
4. In **Extended Filters**, aanpassen van de rapport-drempelwaarden voor:
    - Gemiddelde CPU
    - Maximaal CPU
    - Gem. geheugen
    - Maximaal geheugen
5. In **Extended Filters**, klikt u op **weergeven** en selecteer vervolgens het type van de exemplaren om weer te geven.

Metrische gegevens voor een specifiek exemplaar na verloop van tijd weergeven:

- Ga naar het exemplaar Metrics Explorer rapport en klik op **+** details om weer te geven.

### <a name="rds-sizing-recommendations-report"></a>Extern bureaublad-services Sizing Recommendations-rapport

Het rapport extern bureaublad-services Sizing Recommendations biedt extern bureaublad-services formaat aanbevelingen voor het optimaliseren van uw cloudgebruik. Het biedt een lijst met weinig gebruikte instanties die kandidaten om te krimpen. Cloudyn aanbevelingen zijn gebaseerd op de gegevens van het gebruik en de prestaties van de afgelopen 30 dagen. Aanbevelingen per accountnaam, regio, Type exemplaar en de Status, kunt u filteren.

### <a name="sizing-threshold-manager-report"></a>Grootte drempelwaarde Manager-rapport

Cloudyn van ingebouwde formaat aanbevelingen worden berekend met behulp van een complexe algoritme voor nauwkeurige schaling suggesties. U kunt de drempelwaarden voor downsizing aanbevelingen aanpassen.

Handmatig aanpassen drempelwaarde formaat aanbevelingen:

1. In Sizing drempelwaarde Manager pas de volgende drempelwaarden als u wilt:
    - Gemiddelde CPU-percentage
    - Maximale CPU-percentage
    - Gemiddeld geheugen %
    - % Van de maximale hoeveelheid geheugen
3. Klik op **toepassen** wijzigingen op te slaan.
4. Wijzigingen worden direct toegepast op al uw aanbevelingen.

Standaarddrempelwaarden herstellen:

- Klik in Sizing drempelwaarde Manager op **standaardwaarden herstellen**.

### <a name="compute-instance-types-report"></a>COMPUTE Exemplaartypen rapport

Het rapport Exemplaartypen te gebruiken:

- Exemplaartypen weergeven door de Service, familie, API-naam en de naam.
- Bekijk details, zoals CPU, ECU, RAM en netwerk.

U kunt **zoeken** om specifieke regel items te vinden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van rapporten, met inbegrip van het aanpassen of opslaan en plannen van rapporten, Zie [Understanding kostenrapportages](understanding-cost-reports.md).
- Meer informatie over de dashboards die zijn opgenomen in Cloudyn en over hoe u uw eigen aangepaste dashboards maken, Zie [kosten voor belangrijke metrische gegevens over met dashboards weergeven](dashboards.md).
