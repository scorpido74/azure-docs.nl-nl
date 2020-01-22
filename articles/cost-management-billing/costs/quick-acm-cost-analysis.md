---
title: 'Quick Start: Azure-kosten verkennen met kosten analyse | Microsoft Docs'
description: Deze snelstart helpt u kostenanalyse te gebruiken om de kosten van Azure voor uw bedrijf te verkennen en te analyseren.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f053b30d344e5372617a5bf98c087056c4fe2911
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294147"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Quick Start: kosten verkennen en analyseren met kosten analyse

Voordat u de kosten van Azure goed kunt beheren en optimaliseren, moet u de oorsprong van de kosten in uw bedrijf weten. Het is ook handig om te weten hoeveel geld van uw services kosten en in de ondersteuning van de omgevingen en systemen. Inzicht in de volle breedte van alle kosten is noodzakelijk om nauwkeurig de uitgavenpatronen van uw bedrijf te leren kennen. U kunt bestedings patronen gebruiken om kosten beheer mechanismen, zoals budgetten, af te dwingen.

In deze snelstart gebruikt u kostenanalyse om de kosten van Azure voor uw bedrijf te verkennen en te analyseren. U kunt de totale kosten per bedrijf weergeven, zodat u begrijpt waar de kosten worden opgebouwd en uitgavenpatronen kunt identificeren. U kunt de totale kosten bekijken om geschatte kostentrends per maand, per kwartaal en zelfs per jaar naast een budget te leggen. Een budget helpt bij het in acht nemen van financiële beperkingen. En een budget wordt gebruikt om dagelijkse en maandelijkse kosten te bekijken om onregelmatigheden in de uitgaven te isoleren. Plus, u kunt de gegevens van het huidige rapport downloaden voor verdere analyse of om in een extern systeem te gebruiken.

In deze snelstartgids leert u de volgende zaken:

- Kosten in kostenanalyse beoordelen
- Kostenweergaven aanpassen
- Gegevens van kostenanalyse downloaden


## <a name="prerequisites"></a>Vereisten

Kosten analyse ondersteunt verschillende soorten Azure-account typen. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Zie [toegang tot gegevens toewijzen](../../cost-management/assign-access-acm-data.md)voor meer informatie over het toewijzen van toegang tot Azure Cost Management gegevens.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kosten in kostenanalyse beoordelen

Als u de kosten voor de kosten analyse wilt controleren, opent u het bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **kosten analyse** in het menu. Gebruik **Bereik** om over te schakelen naar een ander bereik in kostenanalyse. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

Het bereik dat u selecteert, wordt overal in Cost Management gebruikt voor het samen voegen van gegevens en het beheren van de toegang tot kosten gegevens. Wanneer u gebruikmaakt van bereiken, moet u geen meervoudige selectie maken. In plaats daarvan selecteert u een groter bereik, waar anderen mee rollen, en filtert u vervolgens op de geneste bereiken die u nodig hebt. Deze aanpak is belang rijk om te begrijpen omdat sommige personen mogelijk geen toegang hebben tot één bovenliggend bereik, die betrekking hebben op meerdere geneste bereiken.

De analyse weergave voor initiële kosten omvat de volgende gebieden.

**Weer gave van geaccumuleerde kosten**: vertegenwoordigt de vooraf gedefinieerde configuratie van de analyse weergave kosten. Elke weergave bevat instellingen voor het datumbereik, de granulariteit, een optie voor groeperen op en filters. In de standaardweergave worden geaccumuleerde kosten weergegeven voor de huidige factureringsperiode, maar u kunt ook andere ingebouwde weergaven gebruiken. Zie [kosten weergaven aanpassen](#customize-cost-views)voor meer informatie.

**Werkelijke kosten**: hier worden de totale verbruiks-en aankoop kosten voor de huidige maand weer gegeven, omdat deze zijn samengevoegd en op uw factuur worden weer gegeven.

**Prognose**: toont de totale geraamde kosten voor de periode die u kiest. (Forecast is een preview-versie.)

**Budget**: hier wordt de geplande bestedings limiet voor het geselecteerde bereik weer gegeven, indien beschikbaar.

**Gecumuleerde granulariteit**: toont de totale cumulatieve dagelijkse kosten, vanaf het begin van de facturerings periode. Nadat u een [budget hebt gemaakt](tutorial-acm-create-budgets.md) voor uw factureringsrekening of abonnement, kunt u snel uw uitgaventrend ten opzichte van het budget bekijken. Beweeg de muisaanwijzer over een datum om de opgebouwde kosten voor die dag te zien.

**Draai grafieken (ring)** : bieden dynamische draai tabellen, waarbij de totale kosten worden opgesplitst op basis van een gemeen schappelijke set standaard eigenschappen. Hierin worden de grootste tot kleinste kosten voor de huidige maand weergegeven. U kunt draaigrafieken op elk moment wijzigen door een andere pivot te selecteren. De kosten worden standaard ingedeeld op: service (metercategorie), locatie (regio) en onderliggend bereik. Inschrijvingsaccounts vallen bijvoorbeeld onder factureringsaccounts, resourcegroepen onder abonnementen en resources onder resourcegroepen.

![Oorspronkelijke weergave van Kostenanalyse in Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Kostenweergaven aanpassen

Kosten analyse heeft vier ingebouwde weer gaven die zijn geoptimaliseerd voor de meest voorkomende doel stellingen:

Weergeven | Antwoorden op vragen zoals
--- | ---
Samengevoegde kosten | Hoeveel heb ik deze maand tot nu toe besteed? Blijf ik binnen mijn budget?
Dagelijkse kosten | Zijn de kosten per dag gedurende de afgelopen 30 dagen toegenomen?
Kosten per service | Hoe verschilt mijn maandelijkse gebruik in de afgelopen drie facturen?
Kosten per resource | Welke resources zijn deze maand tot nu toe het duurst?

![Weer gave selecteren met een voorbeeld selectie voor deze maand](./media/quick-acm-cost-analysis/view-selector.png)

Er zijn echter veel gevallen waar u een meer gedetailleerde analyse nodig hebt. Het aanpassen begint bovenaan de pagina, met de selectie van de datum.

Kostenanalyse toont standaard de gegevens voor de huidige maand. Gebruik de datumkiezer om snel naar veelgebruikte datumbereiken over te schakelen. U kunt bijvoorbeeld kiezen uit de afgelopen zeven dagen, de afgelopen maand, het huidige jaar of een aangepast datumbereik. Bij abonnementen met betalen per gebruik kunt u ook gebruikmaken van datumbereiken op basis van uw factureringsperiode, die niet is gekoppeld aan de kalendermaand, zoals de huidige factureringsperiode of de laatste factuur. Gebruik de koppelingen **< vorige** en **volgende >** aan de bovenkant van het menu om naar respectievelijk de vorige of volgende periode te gaan. **< Voor gaande** gaat bijvoorbeeld van de **afgelopen 7 dagen** naar **8-14 dagen geleden** of **15-21 dagen geleden**.

![Datumselector met een voorbeeldselectie voor deze maand](./media/quick-acm-cost-analysis/date-selector.png)

Kostenanalyse toont standaard **opgebouwde** kosten. De geaccumuleerde kosten omvatten alle kosten voor elke dag plus de voorgaande dagen, zodat u over een weergave beschikt die continu wordt uitgebreid met uw dagelijks samengevoegde kosten. Deze weergave is geoptimaliseerd om u te laten zien hoe u het doet in verhouding met het budget voor het geselecteerde tijdsbereik.

Gebruik de weergave met het prognosediagram om mogelijke overschrijdingen van het budget vast te stellen. Wanneer er sprake is van een mogelijke budget overtreding, wordt geprojecteerde overuitgave rood weer gegeven. Er wordt ook een indicatorsymbool weergegeven in het diagram. Als u de muisaanwijzer boven het symbool houdt, wordt de geschatte datum van de budgetoverschrijding weergegeven.

![Voor beeld van potentiële budget schendingen](./media/quick-acm-cost-analysis/budget-breach.png)

Er is ook de **dagelijkse** weergave die de kosten voor elke dag toont. De dagelijkse weergave toont geen groeitrend. De weergave is ontworpen om u onregelmatigheden als pieken of dalen in de kosten per dag te laten zien. Als u een budget hebt geselecteerd, toont de dagelijkse weergave ook een schatting van uw dagelijkse budget.

Als uw dagelijkse kosten stelselmatig boven het geschatte dagelijkse budget uitkomen, kunt u ervan uitgaan dat u uw maandelijkse budget zult overschrijden. Het geschatte dagelijkse budget geeft u een beeld van uw budget op een lager niveau. Wanneer u schommelingen hebt in uw dagelijkse kosten dan is de vergelijking tussen het geschatte dagelijkse budget en uw maandelijkse budget minder nauwkeurig.

Hier volgt een dagelijkse weer gave van recente uitgaven waarvoor de bestedings prognose is ingeschakeld.
![dagweergave weer geven met voor beeld van dagelijkse kosten voor de huidige maand](./media/quick-acm-cost-analysis/daily-view.png)

Wanneer u de bestedings prognose uitschakelt, ziet u verwachte uitgaven niet voor toekomstige datums. Wanneer u de kosten voor de vorige Peri Oden bekijkt, worden er ook geen kosten weer gegeven.

Over het algemeen kunt u verwachten dat gegevens of meldingen binnen 8 tot 12 uur worden verbruikt.


**Groeperen op** algemene eigenschappen om de kosten te verlagen en de belangrijkste inzenders te identificeren. Als u wilt groeperen op resource Tags, selecteert u bijvoorbeeld de code sleutel waarop u wilt groeperen. De kosten worden opgesplitst op basis van elke code waarde, met een extra segment voor bronnen waarvoor deze tag niet is toegepast.

De meeste [ondersteuning voor Azure-bronnen](../../azure-resource-manager/management/tag-support.md). Sommige Tags zijn echter niet beschikbaar in Cost Management en facturering. Bovendien worden resourcegroeptags niet ondersteund. Ondersteuning voor Tags is van toepassing op het gebruik dat wordt gerapporteerd *nadat* het label is toegepast op de resource. Labels worden niet met terugwerkende kracht toegepast voor kosten tellingen.

Bekijk de [code beleidsregels met Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) video voor meer informatie over het gebruik van Azure tag Policy om de zicht baarheid van de kosten gegevens te verbeteren.

Hier volgt een overzicht van de Azure-service kosten voor de huidige maand.

![Gegroepeerde, dagelijks samengevoegde weergave met een voorbeeld van Azure-servicekosten van de afgelopen maand](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Standaard worden in kosten analyse alle verbruiks-en aankoop kosten weer gegeven wanneer ze worden samengevoegd en worden ze weer gegeven in uw factuur, ook wel **werkelijke kosten**genoemd. De weergave met de werkelijke kosten is ideaal voor het afstemmen van uw factuur. Pieken in de aankoopkosten kunnen echter verontrustend zijn wanneer u bedacht bent op afwijkingen in de kosten en andere wijzigingen in de kosten. Om pieken uit te platen die worden veroorzaakt door de kosten voor de reserve ring, schakelt u over naar **afgeschreven kosten**.

![Verander tussen de werkelijke en de afgeschreven kosten om de reserve ringen te bekijken over de hele periode en te worden toegewezen aan de resources die de reserve ring hebben gebruikt.](./media/quick-acm-cost-analysis/metric-picker.png)

Bij afgeschreven kosten worden reserveringsaankopen opgesplitst in dagelijkse eenheden en gespreid over de duur van de reserveringstermijn. In plaats van een $365-aankoop te bekijken op 1 januari, wordt elke dag van 1 januari tot en met 31 een $1-aankoop weer gegeven. Naast de standaardaflossing, worden deze kosten ook opnieuw toegewezen en gekoppeld aan de specifieke resources die de reservering hebben gebruikt. Als bijvoorbeeld de dagelijkse kosten van $1 zijn verdeeld over twee virtuele machines, ziet u de kosten van $2 0,50 voor de dag. Als een deel van de reserve ring voor de dag niet wordt gebruikt, ziet u de kosten voor $1 0,50 die zijn gekoppeld aan de betreffende virtuele machine en een extra bedrag van $0,50 kosten met een kosten type van `UnusedReservation`. Houd er rekening mee dat ongebruikte reserverings kosten alleen kunnen worden weer gegeven bij het bekijken van afgeschreven kosten.

Vanwege de wijziging in de wijze waarop de kosten worden weergegeven, is het belangrijk om te weten dat in de weergaven voor werkelijke kosten en afgeschreven kosten verschillende totaalaantallen worden vermeld. Over het algemeen nemen de totale kosten van maanden met een reserveringsaankoop af wanneer de afgeschreven kosten worden weergegeven en nemen deze in de maanden na een reserveringsaankoop toe. Afschrijving is alleen beschikbaar voor reserverings aankopen en is op dit moment niet van toepassing op Azure Marketplace-aankopen.

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

| Eigenschap | Wanneer gebruikt u dit? | Opmerkingen |
| --- | --- | --- |
| **Beschikbaarheids zones** | Onderbreek de AWS-kosten per beschikbaarheids zone. | Alleen van toepassing op AWS-bereiken en-beheer groepen. Azure-gegevens bevatten geen beschikbaarheids zone en worden weer gegeven als **niet van toepassing**. |
| **Facturerings periode** | Onderbreek PAYG kosten per maand (of worden) gefactureerd. | Gebruik **facturerings periode** om een nauw keurige weer gave van gefactureerde payg-kosten te krijgen. 2 extra dagen voor en na de facturerings periode toevoegen als er wordt gefilterd op een aangepast datum bereik. Het beperken van de exacte datums van de facturerings periode komt niet overeen met de factuur. De kosten van alle facturen in de facturerings periode worden weer gegeven. **Factuur-ID** gebruiken om te filteren op een specifieke factuur. Alleen van toepassing op PAYG-abonnementen omdat EA en MCA worden gefactureerd per kalender maand. EA/MCA-accounts kunnen kalender maanden in de datum kiezer of maandelijkse granulatie gebruiken om hetzelfde doel te bereiken. |
| **Kosten type** | Onderbreek de kosten voor gebruik, aankoop, terugbetaling en ongebruikte reserve ringen. | Reserverings aankopen en terugstortingen zijn alleen beschikbaar wanneer u werkelijke kosten gebruikt en niet wanneer u afgeschreven kosten gebruikt. Ongebruikte reserverings kosten zijn alleen beschikbaar wanneer u de afgeschreven kosten bekijkt. |
| **Department** | Kosten per EA-afdeling opsplitsen. | Alleen beschikbaar voor EA-en beheer groepen. PAYG-abonnementen hebben geen afdeling en worden weer gegeven als **niet van toepassing of niet** - **toegewezen**. |
| **Inschrijvings account** | Kosten opsplitsen van EA-account eigenaar. | Alleen beschikbaar voor EA-facturerings accounts,-afdelingen en-beheer groepen. PAYG-abonnementen hebben geen EA-inschrijvings accounts en worden weer gegeven als **niet-toepasselijk of niet** - **toegewezen**. |
| **Frequentie** | Op gebruik gebaseerde, eenmalige en terugkerende kosten opsplitsen. | |
| **Factuur-ID** | Kosten verdelen per gefactureerde factuur. | Niet-gefactureerde kosten hebben nog geen factuur-ID en de EA-kosten bevatten geen factuur Details en worden weer gegeven als **niet van toepassing**.  |
| **Verbinding** | Kosten opsplitsen met gebruiks meter. | Het gebruik van aankopen en Marketplace wordt weer gegeven als **niet van toepassing**. Raadpleeg het **kosten type** voor de aanschaf en het type van de **Uitgever** om Marketplace-kosten te identificeren. |
| **Bewerking** | Onderbreek de AWS-kosten per bewerking. | Alleen van toepassing op AWS-bereiken en-beheer groepen. Azure-gegevens omvatten geen bewerking en worden **in plaats daarvan** weer gegeven als **niet van toepassing** . |
| **Prijs model** | Verdeel kosten op aanvraag, reserve ring of spot gebruik. | Aankopen worden weer gegeven als **OnDemand**. Als u **niet van toepassing**is, groeperen op **reserve ring** om te bepalen of het gebruik een reserve ring of op aanvraag gebruik en een **toeslag type** is voor het identificeren van aankopen.
| **Provider** | Verdeel de kosten met AWS en Azure. | Alleen beschikbaar voor beheer groepen. |
| **Type Uitgever** | Onderbreek de kosten voor AWS, Azure en Marketplace. |  |
| **Boeking** | Verdeel de kosten per reserve ring. | Gebruik of aankopen die niet aan een reserve ring zijn gekoppeld, worden weer gegeven als **niet van toepassing**. Groeperen op **Uitgever type** om andere Azure-, AWS-of Marketplace-aankopen te identificeren. |
| **Resource** | Kosten per resource opsplitsen. | Aankopen worden weer gegeven als **niet van toepassing**, omdat ze worden toegepast op een EA/payg-facturerings account of een MCA-facturerings profiel niveau en niet zijn gekoppeld aan een specifieke resource. Groeperen op **Uitgever type** om andere Azure-, AWS-of Marketplace-aankopen te identificeren. |
| **Resourcegroep** | Kosten opsplitsen per resource groep. | Aankopen, Tenant bronnen die niet zijn gekoppeld aan abonnementen, abonnements resources die niet zijn geïmplementeerd voor een resource groep en klassieke resources hebben geen resource groep en worden weer gegeven als **andere**, **klassieke Services**, **$System**of **niet van toepassing**. |
| **Resourcetype** | Kosten opsplitsen op resource type. | Aankopen en klassieke Services hebben geen Azure Resource Manager resource type en worden weer gegeven als **andere**, **klassieke Services**of **niet van toepassing**. |
| **Resource locatie** | Kosten opsplitsen per locatie of regio. | Het gebruik van aankopen en Marketplace kan worden weer gegeven als niet- **toegewezen**, **onbekend**, niet- **toegewezen**of **niet van toepassing**. |
| **Service naam** of **meter categorie** | Onderbreek de kosten voor de Azure-service. | Het gebruik van aankopen en Marketplace wordt weer gegeven als **niet van toepassing of niet** - **toegewezen**. |
| **Service tier** of **meter subcategorie** | Kosten voor de subclassificatie van de Azure-gebruiks meter onderverdelen. | Het gebruik van aankopen en Marketplace wordt weer gegeven als **niet van toepassing of niet** - **toegewezen**. |
| **Abonnement** | Kosten opsplitsen van een Azure-abonnement en een gekoppeld AWS-account. | Inkopen en Tenant bronnen kunnen worden weer gegeven als **niet van toepassing**. |
| **Tag** | Kosten opsplitsen op label waarden voor een specifieke tag-sleutel. | Labels zijn niet beschikbaar voor aankopen, Tenant bronnen die niet zijn gekoppeld aan abonnementen, abonnements resources die niet zijn geïmplementeerd voor een resource groep of klassieke resources. Opmerking Sommige services bevatten geen tags in gebruiks gegevens. Meer informatie over [de ondersteuning van tags voor elk resource type](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Zie [inzicht in de termen die worden gebruikt in het Azure-gebruiks-en-kosten bestand](../understand/understand-usage.md)voor meer informatie over de voor waarden.


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

Als u werkelijke kosten nodig hebt om de aankopen weer te geven wanneer ze zijn samengevoegd **, wijzigt u**/**metrieke waarde** in **ActualCost**. Zie de documentatie van de API voor [query's](/rest/api/cost-management/query) en [UsageDetails](/rest/api/consumption/usageDetails) voor meer informatie over deze api's. Houd er rekening mee dat de gepubliceerde documenten voor de GA-versie zijn. Ze werken echter hetzelfde voor de API-versie *2019-04-01-preview* , buiten het nieuwe type/metrisch-kenmerk en gewijzigde eigenschaps namen. (Lees meer over de onderstaande eigenschaps namen.)

Cost Management-Api's werken in alle bereiken boven resources: resource groep, abonnement en beheer groep via Azure RBAC-toegang, EA-facturerings accounts (inschrijvingen), afdelingen en inschrijvings accounts via toegang tot de EA-Portal. Meer informatie over bereiken, waaronder het bepalen van uw bereik-ID of het beheren van de toegang, in [begrijpen en werken met scopes](understand-work-scopes.md).

## <a name="next-steps"></a>Volgende stappen

Ga door naar de eerste zelfstudie voor informatie over het maken en beheren van een budget.

> [!div class="nextstepaction"]
> [Budgetten maken en beheren](tutorial-acm-create-budgets.md)
