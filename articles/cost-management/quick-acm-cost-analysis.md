---
title: 'Quick Start: Azure-kosten verkennen met kosten analyse | Microsoft Docs'
description: Deze snelstart helpt u kostenanalyse te gebruiken om de kosten van Azure voor uw bedrijf te verkennen en te analyseren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f2e1d19f69b426cee870d2ede489b7c458404704
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374777"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Quick Start: kosten verkennen en analyseren met kosten analyse

Voordat u de kosten van Azure goed kunt beheren en optimaliseren, moet u de oorsprong van de kosten in uw bedrijf weten. Het is ook handig om te weten hoeveel geld van uw services kosten en in de ondersteuning van de omgevingen en systemen. Inzicht in de volle breedte van alle kosten is noodzakelijk om nauwkeurig de uitgavenpatronen van uw bedrijf te leren kennen. U kunt bestedings patronen gebruiken om kosten beheer mechanismen, zoals budgetten, af te dwingen.

In deze snelstart gebruikt u kostenanalyse om de kosten van Azure voor uw bedrijf te verkennen en te analyseren. U kunt de totale kosten per bedrijf weergeven, zodat u begrijpt waar de kosten worden opgebouwd en uitgavenpatronen kunt identificeren. U kunt de totale kosten bekijken om geschatte kostentrends per maand, per kwartaal en zelfs per jaar naast een budget te leggen. Een budget helpt bij het in acht nemen van financiële beperkingen. En een budget wordt gebruikt om dagelijkse en maandelijkse kosten te bekijken om onregelmatigheden in de uitgaven te isoleren. Plus, u kunt de gegevens van het huidige rapport downloaden voor verdere analyse of om in een extern systeem te gebruiken.

In deze snelstart leert u de volgende zaken:

- Kosten in kostenanalyse beoordelen
- Kostenweergaven aanpassen
- Gegevens van kostenanalyse downloaden


## <a name="prerequisites"></a>Vereisten

Kosten analyse ondersteunt verschillende soorten Azure-account typen. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Zie [toegang tot gegevens toewijzen](assign-access-acm-data.md)voor meer informatie over het toewijzen van toegang tot Azure Cost Management gegevens.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kosten in kostenanalyse beoordelen

Als u de kosten voor de kosten analyse wilt controleren, opent u het bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **kosten analyse** in het menu. Gebruik **Bereik** om over te schakelen naar een ander bereik in kostenanalyse. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

Het bereik dat u selecteert, wordt overal in Cost Management gebruikt voor het samen voegen van gegevens en het beheren van de toegang tot kosten gegevens. Wanneer u gebruikmaakt van bereiken, moet u geen meervoudige selectie maken. In plaats daarvan selecteert u een groter bereik, waar anderen mee rollen, en filtert u vervolgens op de geneste bereiken die u nodig hebt. Deze aanpak is belang rijk om te begrijpen omdat sommige personen mogelijk geen toegang hebben tot één bovenliggend bereik, die betrekking hebben op meerdere geneste bereiken.

De analyse weergave voor initiële kosten omvat de volgende gebieden.

**Weer gave van geaccumuleerde kosten**: vertegenwoordigt de vooraf gedefinieerde configuratie van de analyse weergave kosten. Elke weer gave bevat de instellingen datum bereik, granulatie, groeperen op en filter. In de standaard weergave worden geaccumuleerde kosten weer gegeven voor de huidige facturerings periode, maar u kunt wijzigen in andere ingebouwde weer gaven. Zie [kosten weergaven aanpassen](#customize-cost-views)voor meer informatie.

**Werkelijke kosten**: hier worden de totale verbruiks-en aankoop kosten voor de huidige maand weer gegeven, omdat deze zijn samengevoegd en op uw factuur worden weer gegeven.

**Prognose**: toont de totale geraamde kosten voor de periode die u kiest. (Forecast is een preview-versie.)

**Budget**: hier wordt de geplande bestedings limiet voor het geselecteerde bereik weer gegeven, indien beschikbaar.

**Gecumuleerde granulariteit**: toont de totale cumulatieve dagelijkse kosten, vanaf het begin van de facturerings periode. Nadat u een [budget hebt gemaakt](tutorial-acm-create-budgets.md) voor uw factureringsrekening of abonnement, kunt u snel uw uitgaventrend ten opzichte van het budget bekijken. Beweeg de muisaanwijzer over een datum om de opgebouwde kosten voor die dag te zien.

**Draai grafieken (ring)** : bieden dynamische draai tabellen, waarbij de totale kosten worden opgesplitst op basis van een gemeen schappelijke set standaard eigenschappen. Ze tonen de grootste tot kleinste kosten voor de huidige maand. U kunt draaigrafieken op elk moment wijzigen door een andere pivot te selecteren. De kosten worden standaard gecategoriseerd per service (meter categorie), locatie (regio) en onderliggend bereik. Inschrijvings accounts bevinden zich bijvoorbeeld onder facturerings accounts, resource groepen bevinden zich onder abonnementen en resources bevinden zich onder resource groepen.

![Oorspronkelijke weergave van Kostenanalyse in Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Kostenweergaven aanpassen

Kosten analyse heeft vier ingebouwde weer gaven die zijn geoptimaliseerd voor de meest voorkomende doel stellingen:

Weergeven | Antwoorden op vragen zoals
--- | ---
Geaccumuleerde kosten | Hoeveel heb ik tot nu toe deze maand gewerkt? Blijf ik binnen mijn budget?
Dagelijkse kosten | Zijn de kosten per dag voor de afgelopen 30 dagen verhoogd?
Kosten per service | Hoe verschilt mijn maandelijkse gebruik in de afgelopen drie facturen?
Kosten per resource | Welke resources zijn het meest in de buurt van deze maand?

![Weer gave selecteren met een voorbeeld selectie voor deze maand](./media/quick-acm-cost-analysis/view-selector.png)

Er zijn echter veel gevallen waar u een meer gedetailleerde analyse nodig hebt. Het aanpassen begint bovenaan de pagina, met de selectie van de datum.

Kostenanalyse toont standaard de gegevens voor de huidige maand. Gebruik de datum kiezer om snel naar algemene datumbereiken over te scha kelen. Voor beelden zijn de laatste zeven dagen, de afgelopen maand, het huidige jaar of een aangepast datum bereik. Betalen per gebruik-abonnementen omvatten ook datumbereiken op basis van uw facturerings periode, die niet is gekoppeld aan de kalender maand, zoals de huidige facturerings periode of de laatste factuur. Gebruik de koppelingen **< vorige** en **volgende >** aan de bovenkant van het menu om naar respectievelijk de vorige of volgende periode te gaan. **< Voor gaande** gaat bijvoorbeeld van de **afgelopen 7 dagen** naar **8-14 dagen geleden** of **15-21 dagen geleden**.

![Datumselector met een voorbeeldselectie voor deze maand](./media/quick-acm-cost-analysis/date-selector.png)

Kostenanalyse toont standaard **opgebouwde** kosten. Geaccumuleerde kosten omvatten alle kosten voor elke dag plus de vorige dagen, voor een voortdurend groeiende weer gave van uw dagelijkse cumulatieve kosten. Deze weergave is geoptimaliseerd om u te laten zien hoe u het doet in verhouding met het budget voor het geselecteerde tijdsbereik.

Gebruik de weer gave prognose diagram om mogelijke budget inbreuken te identificeren. Wanneer er sprake is van een mogelijke budget overtreding, wordt geprojecteerde overuitgave rood weer gegeven. Er wordt ook een indicator symbool weer gegeven in de grafiek. Als u de muis aanwijzer boven het symbool houdt, wordt de geschatte datum van de overtreding van het budget weer gegeven.

![Voor beeld van potentiële budget schendingen](./media/quick-acm-cost-analysis/budget-breach.png)

Er is ook de **dagelijkse** weergave die de kosten voor elke dag toont. De dagelijkse weergave toont geen groeitrend. De weergave is ontworpen om u onregelmatigheden als pieken of dalen in de kosten per dag te laten zien. Als u een budget hebt geselecteerd, wordt in de weer gave dagelijks ook een schatting van uw dagelijkse budget weer gegeven.

Wanneer uw dagelijkse kosten consistent zijn boven het geschatte dagelijkse budget, kunt u verwachten dat u uw maandelijkse budget overschrijdt. Het geschatte dagelijkse budget is een manier om u te helpen bij het visualiseren van uw budget op een lager niveau. Wanneer u schommelingen hebt in uw dagelijkse kosten dan is de vergelijking tussen het geschatte dagelijkse budget en uw maandelijkse budget minder nauwkeurig.

Hier volgt een dagelijkse weer gave van recente uitgaven waarvoor de bestedings prognose is ingeschakeld.
![Daily-weer gave met voor beeld van dagelijkse kosten voor de huidige maand @ no__t-1

Wanneer u de bestedings prognose uitschakelt, ziet u verwachte uitgaven niet voor toekomstige datums. Wanneer u de kosten voor de vorige Peri Oden bekijkt, worden er ook geen kosten weer gegeven.

Over het algemeen kunt u verwachten dat gegevens of meldingen binnen 8 tot 12 uur worden verbruikt.


**Groeperen op** algemene eigenschappen om de kosten te verlagen en de belangrijkste inzenders te identificeren. Als u wilt groeperen op resource Tags, selecteert u bijvoorbeeld de code sleutel waarop u wilt groeperen. De kosten worden opgesplitst op basis van elke code waarde, met een extra segment voor bronnen waarvoor deze tag niet is toegepast.

De meeste [ondersteuning voor Azure-bronnen](../azure-resource-manager/tag-support.md). Sommige Tags zijn echter niet beschikbaar in Cost Management en facturering. Bovendien worden resourcegroeptags niet ondersteund. Cost Management ondersteunt resourcetags alleen vanaf de datum waarop de tags rechtstreeks worden toegepast op de resource. Bekijk de [code beleidsregels met Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) video voor meer informatie over het gebruik van Azure tag Policy om de zicht baarheid van de kosten gegevens te verbeteren.

Hier volgt een overzicht van de Azure-service kosten voor de huidige maand.

![Gegroepeerde, dagelijks samengevoegde weergave met een voorbeeld van Azure-servicekosten van de afgelopen maand](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Standaard worden in kosten analyse alle verbruiks-en aankoop kosten weer gegeven wanneer ze worden samengevoegd en worden ze weer gegeven in uw factuur, ook wel **werkelijke kosten**genoemd. Het weer geven van de werkelijke kosten is ideaal voor het afstemmen van uw factuur. Het kopen van pieken in kosten kan echter worden gealarmd wanneer u een ogende aanhoudt voor het uitgeven van afwijkingen en andere wijzigingen in de kosten. Om pieken uit te platen die worden veroorzaakt door de kosten voor de reserve ring, schakelt u over naar **afgeschreven kosten**.

![Verander tussen de werkelijke en de afgeschreven kosten om de reserve ringen te bekijken over de hele periode en te worden toegewezen aan de resources die de reserve ring hebben gebruikt.](./media/quick-acm-cost-analysis/metric-picker.png)

Afgeschreven kosten worden berekend op basis van de reserverings aankopen in de dagelijkse segmenten en gespreid over de duur van de reserverings periode. In plaats van een $365-aankoop te bekijken op 1 januari, wordt elke dag van 1 januari tot en met 31 een $1-aankoop weer gegeven. Naast de eenvoudige aflossing, worden deze kosten ook opnieuw toegewezen en gekoppeld met behulp van de specifieke resources die de reserve ring hebben gebruikt. Als bijvoorbeeld de dagelijkse kosten van $1 zijn verdeeld over twee virtuele machines, ziet u de kosten van $2 0,50 voor de dag. Als een deel van de reserve ring voor de dag niet wordt gebruikt, ziet u de kosten voor $1 0,50 die zijn gekoppeld aan de betreffende virtuele machine en een extra bedrag van $0,50 kosten met een kosten type `UnusedReservation`. Houd er rekening mee dat ongebruikte reserverings kosten alleen kunnen worden weer gegeven bij het bekijken van afgeschreven kosten.

Als gevolg van de wijziging in de weer gave van de kosten, is het belang rijk te weten dat de werkelijke kosten en de afgeschreven kosten worden weer gegeven in verschillende totale aantallen. Over het algemeen nemen de totale kosten van maanden met een reserverings aankoop af bij het weer geven van afgeschreven kosten en worden maanden na een reserverings aankoop verhoogd. Afschrijving is alleen beschikbaar voor reserverings aankopen en is op dit moment niet van toepassing op Azure Marketplace-aankopen.

In de volgende afbeelding ziet u de namen van resource groepen. U kunt groeperen op label om de totale kosten per tag weer te geven of door de weer gave **kosten per resource** te gebruiken om alle labels voor een bepaalde resource te bekijken.

![Volledige gegevens van de huidige weergave, waarbij de resourcegroepnamen worden weergegeven](./media/quick-acm-cost-analysis/full-data-set.png)

Wanneer u kosten groepeert op basis van een specifiek kenmerk, worden de 10 kosten voor inzenders van het hoogste naar het laagste niveau weer gegeven. Als er meer dan 10 zijn, worden de meeste negen kosten voor mede werkers weer gegeven met een **andere** groep die alle resterende groepen vertegenwoordigt die zijn gecombineerd. Wanneer u op **Tags groepeert, wordt er een** niet-versleutelde groepen weer gegeven voor de kosten waarvoor de code sleutel niet is toegepast. **Untags** is altijd de laatste, zelfs als ongecodeerde kosten hoger zijn dan de kosten van het label. Ongecodeerde kosten maken deel uit van **anderen**, als er 10 of meer label waarden bestaan. Schakel over naar de tabel weergave en wijzig granulatie in **geen** om alle waarden weer te geven die zijn gerangschikt van hoogste naar laagste kosten.

Klassieke virtuele machines, netwerken en opslag resources delen geen gedetailleerde facturerings gegevens. Ze worden samengevoegd als **Klassieke services** wanneer kosten worden gegroepeerd.

In draai grafieken onder het hoofd diagram ziet u verschillende groeperingen, waarmee u de totale kosten voor de geselecteerde tijds periode en filters breder kunt weer geven. Selecteer een eigenschap of tag om geaggregeerde kosten voor een dimensie weer te geven.

![Voor beeld van draai grafieken](./media/quick-acm-cost-analysis/pivot-charts.png)

U kunt de volledige gegevensset weer geven voor elke weer gave. Welke selecties of filters worden toegepast, geldt voor de gegevens die worden weer gegeven. Als u de volledige gegevensset wilt zien, selecteert u de lijst **grafiek type** en selecteert u **tabel** weergave.

![Gegevens voor de huidige weergave in tabelvorm](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Over groeperings-en filter opties

De volgende tabel bevat een aantal van de meest voorkomende groeperings-en filter opties en wanneer u deze moet gebruiken.

| Eigenschap | Wanneer gebruikt u dit? |
| --- | --- |
| **Facturerings periode** | Kosten opsplitsen per factuur maand. Deze optie is belang rijk voor betalen per gebruik en voor ontwikkelings-en test abonnementen, die niet zijn gekoppeld aan kalender maanden. EA/MCA-accounts kunnen kalender maanden in de datum kiezer of maandelijkse granulatie gebruiken om hetzelfde doel te bereiken. |
| **Kosten type** | Onderbreek de kosten voor gebruik, aankoop, terugbetaling en ongebruikte reserve ringen. Reserverings aankopen en terugstortingen zijn alleen beschikbaar wanneer u actie kosten gebruikt en niet wanneer u afgeschreven kosten gebruikt. Ongebruikte reserverings kosten zijn alleen beschikbaar wanneer u de afgeschreven kosten bekijkt. |
| **Cloud** | Verdeel de kosten met AWS en Azure. AWS-kosten zijn alleen beschikbaar voor beheer groepen, externe facturerings accounts en externe abonnementen. |
| **Afdeling** / **factuur sectie** | Onderbreek de kosten per EA-afdeling of MCA-factuur gedeelte. Deze optie is alleen beschikbaar voor EA/MCA-facturerings accounts en MCA-facturerings profielen. |
| **Inschrijvings account** | Kosten opsplitsen van EA-account eigenaar. Deze optie is alleen beschikbaar voor EA-facturerings accounts en-afdelingen. |
| **Frequentie** | Op gebruik gebaseerde, eenmalige en terugkerende kosten opsplitsen. |
| **Verbinding** | Kosten onderverdelen op basis van de gebruiks meter van Azure. Deze optie is alleen beschikbaar voor Azure-gebruik. Alle aankopen en Marketplace-gebruik worden weer gegeven als **niet opgegeven of niet** - **toegewezen**. |
| **Type Uitgever** | Onderbreek de kosten voor AWS, Azure en Marketplace. |
| **Boeking** | Verdeel de kosten per reserve ring. Een gebruik dat geen reserve ring bevat, wordt weer gegeven als **niet opgegeven**. |
| **Resource** | Kosten per resource opsplitsen. Alle aankopen worden weer gegeven als **niet opgegeven**, omdat ze worden toegepast op een EA/payg-facturerings account of een MCA-facturerings profiel niveau.  |
| **Resourcegroep** | Kosten opsplitsen per resource groep. Deze optie is alleen beschikbaar voor niet-klassiek gebruik. Klassiek resource gebruik wordt weer gegeven als **andere**en aankopen worden weer gegeven als **niet opgegeven**. |
| **Resourcetype** | Kosten opsplitsen op resource type. Deze optie is alleen beschikbaar voor niet-klassiek gebruik. Klassiek resource gebruik wordt weer gegeven als **andere**en aankopen worden weer gegeven als **niet opgegeven**. |
| **Service naam** of **meter categorie** | Onderbreek de kosten voor de Azure-service. Deze optie is alleen beschikbaar voor Azure-gebruik. Alle aankopen en Marketplace-gebruik worden weer gegeven als **niet opgegeven of niet** - **toegewezen**. |
| **Service tier** of **meter subcategorie** | Kosten voor de subclassificatie van de Azure-gebruiks meter onderverdelen. Deze optie is alleen beschikbaar voor Azure-gebruik. Alle aankopen en Marketplace-gebruik worden weer gegeven als **niet opgegeven of niet** - **toegewezen**. |
| **Abonnement** | Kosten opsplitsen per abonnement. Alle aankopen worden weer gegeven als **niet opgegeven**. |
| **Tag** | Kosten opsplitsen op label waarden voor een specifieke tag-sleutel. |

Zie [inzicht in de termen die worden gebruikt in het Azure-gebruiks-en-kosten bestand](../billing/billing-understand-your-usage.md)voor meer informatie over de voor waarden.


## <a name="saving-and-sharing-customized-views"></a>Aangepaste weer gaven opslaan en delen

Sla aangepaste weer gaven op en deel ze met anderen door kosten analyse vast te maken aan het dash board van Azure Portal of door een koppeling naar de kosten analyse te kopiëren.

Als u de kosten analyse wilt vastmaken, selecteert u het speld pictogram in de rechter bovenhoek. Als u de kosten analyse vastmaakt, wordt alleen het hoofd diagram of de tabel weergave opgeslagen. Deel het dash board om anderen toegang tot de tegel te geven. Houd er rekening mee dat hiermee alleen de dashboard configuratie wordt gedeeld en andere gebruikers geen toegang tot de onderliggende gegevens verlenen. Als u geen toegang hebt tot kosten maar wel toegang hebt tot een gedeeld dash board, wordt het bericht ' toegang geweigerd ' weer gegeven.

Als u een koppeling naar de kosten analyse wilt delen, selecteert u **delen** boven aan de Blade. Er wordt een aangepaste URL weer gegeven, waarmee deze specifieke weer gave voor dit specifieke bereik wordt geopend. Als u geen kosten toegang hebt en deze URL ontvangt, wordt het bericht ' toegang geweigerd ' weer gegeven.

Raadpleeg voor meer informatie over het verlenen van toegang tot kosten voor elk ondersteund bereik [begrijpen en werken met scopes](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatisering en offline analyse

Er zijn momenten waarop u de gegevens moet downloaden voor verdere analyse, deze samen voegen met uw eigen gegevens of deze integreert in uw eigen systemen. Cost Management biedt een aantal verschillende opties. Als uitgangs punt, als u een overzicht op hoog niveau van ad hoc nodig hebt, zoals wat u in cost analysis krijgt, bouwt u de weer gave die u nodig hebt. Down load het vervolgens door **exporteren** te selecteren en **gegevens downloaden naar CSV** te selecteren of **gegevens te downloaden naar Excel**. De down load voor Excel biedt aanvullende context voor de weer gave die u hebt gebruikt voor het genereren van de down load, zoals Scope, query configuratie, totaal en gegenereerde datum.

Als u de volledige, niet-geaggregeerde gegevensset nodig hebt, kunt u deze downloaden van het facturerings account. Ga vervolgens naar **Cost Management en facturering**in de lijst met Services in het linkernavigatievenster van de portal. Selecteer uw facturerings account, indien van toepassing. Ga naar **gebruik en kosten**en selecteer vervolgens het **Download** pictogram voor de gewenste facturerings periode.

Maak een soort gelijke aanpak om de ontvangst van kosten gegevens te automatiseren. Gebruik de [query-API](/rest/api/cost-management/query) voor uitgebreidere analyses met dynamische filtering, groepering en aggregatie, of gebruik de [UsageDetails-API](/rest/api/consumption/usageDetails) voor de volledige, niet-geaggregeerde gegevensset. De versie van deze Api's voor algemene Beschik baarheid (GA) is 2019-01-01. Gebruik **2019-04-01-preview** om toegang te krijgen tot de preview van reserve ring en Marketplace-aankopen binnen deze api's.

Een voor beeld hiervan is een geaggregeerde weer gave van afgeschreven kosten die zijn uitgesplitst per toeslag type (verbruik, aankoop of terugbetaling), Publisher-type (Azure of Marketplace), resource groep (leeg voor inkoop) en reserve ring (leeg indien niet van toepassing).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

En als u de aggregatie niet nodig hebt en de voor keur geeft aan de volledige, onbewerkte gegevensset:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Als u werkelijke kosten nodig hebt voor het weer geven van aankopen wanneer ze zijn samengevoegd **, wijzigt u**/**metriek** naar **ActualCost**. Zie de documentatie van de API voor [query's](/rest/api/cost-management/query) en [UsageDetails](/rest/api/consumption/usageDetails) voor meer informatie over deze api's. Houd er rekening mee dat de gepubliceerde documenten voor de GA-versie zijn. Ze werken echter hetzelfde voor de API-versie *2019-04-01-preview* , buiten het nieuwe type/metrisch-kenmerk en gewijzigde eigenschaps namen. (Lees meer over de onderstaande eigenschaps namen.)

Cost Management-Api's werken in alle bereiken boven resources: resource groep, abonnement en beheer groep via Azure RBAC-toegang, EA-facturerings accounts (inschrijvingen), afdelingen en inschrijvings accounts via toegang tot de EA-Portal. Meer informatie over bereiken, waaronder het bepalen van uw bereik-ID of het beheren van de toegang, in [begrijpen en werken met scopes](understand-work-scopes.md).

## <a name="next-steps"></a>Volgende stappen

Ga door naar de eerste zelfstudie voor informatie over het maken en beheren van een budget.

> [!div class="nextstepaction"]
> [Budgetten maken en beheren](tutorial-acm-create-budgets.md)
