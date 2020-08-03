---
title: "Zelfstudie: Aan de slag met de Log Analytics-query's"
description: Leer in deze zelfstudie hoe u Azure Monitor-logboekquery's schrijft en beheert met behulp van Log Analytics in Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 522a13c6de87dd8074b76f95670baf79fc96d419
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075345"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Zelfstudie: Aan de slag met de Log Analytics-query's

In deze zelfstudie staat beschreven hoe u Log Analytics kunt gebruiken om Azure Monitor-logboekquery's te schrijven, uit te voeren en te beheren in Azure Portal. Log Analytics-query's kunnen worden gebruikt om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te verkrijgen op basis van uw gegevens. 

In deze zelfstudie leert u hoe u Log Analytics gebruikt voor de volgende zaken:

> [!div class="checklist"]
> * Inzicht in het schema voor logboekgegevens
> * Eenvoudige query's schrijven en uitvoeren en het tijdsbereik voor query's wijzigen
> * Queryresultaten filteren, sorteren en groeperen
> * Visuals van queryresultaten weergeven, wijzigen en delen
> * Query's en resultaten opslaan, laden, exporteren en kopiëren

Zie [Overzicht van logboekquery's in Azure Monitor](log-query-overview.md) voor meer informatie over logboekquery's.<br/>
Zie [Aan de slag met logboekquery's in Azure Monitor](get-started-queries.md) voor een gedetailleerde zelfstudie over het schrijven van logboekquery's.

## <a name="open-log-analytics"></a>Log Analytics openen
Als u Log Analytics wilt gebruiken, moet u zijn aangemeld bij een Azure-account. Als u geen Azure-account hebt, [kunt u er een gratis maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Als u het merendeel van de stappen in deze zelfstudie wilt voltooien, kunt u [deze demo-omgeving](https://portal.loganalytics.io/demo) gebruiken. Hierin vindt u talloze voorbeeldgegevens. In de demo-omgeving kunt u geen query's opslaan of resultaten vastmaken aan een dashboard.

U kunt ook uw eigen omgeving gebruiken als u Azure Monitor gebruikt voor het verzamelen van logboekgegevens op ten minste één Azure-resource. Als u een Log Analytics-werkruimte wilt openen, selecteert u **Logboeken** in het linkernavigatiedeelvenster van Azure Monitor. 

## <a name="understand-the-schema"></a>Informatie over het schema
 
Een *schema* is een verzameling tabellen die onder logische categorieën zijn gegroepeerd. Het demoschema heeft verschillende categorieën, afkomstig van bewakingsoplossingen. Zo bevat de categorie **LogManagement** Windows- en Syslog-gebeurtenissen, prestatiegegevens en heartbeats van agents.

De schematabellen worden weergegeven op het tabblad **Tabellen** van de Log Analytics-werkruimte. De tabellen bevatten kolommen, elk met een gegevenstype dat wordt aangegeven met het pictogram naast de naam van de kolom. De tabel **Event** bevat bijvoorbeeld tekstkolommen als **Computer** en numerieke kolommen als **EventCategory**.

![Schema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Eenvoudige query's schrijven en uitvoeren

Log Analytics wordt geopend in de **queryeditor** met een nieuwe, lege query.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Een query schrijven

Azure Monitor-logboekquery's maken gebruik van een versie van de Kusto-querytaal. Query's kunnen beginnen met een tabelnaam of de opdracht [search](/azure/kusto/query/searchoperator). 

Met de volgende query worden alle records opgehaald uit de tabel **Event**:

```Kusto
Event
```

Het sluisteken (|) scheidt opdrachten, dus de uitvoer van de eerste opdracht is de invoer van de volgende opdracht. Aan één query kunt u een willekeurig aantal opdrachten toevoegen. Met de volgende query worden de records opgehaald uit de tabel **Event** en worden deze vervolgens doorzocht op de term **error** in een eigenschap:

```Kusto
Event 
| search "error"
```

Met één regeleinde zijn query's gemakkelijker te lezen. Meer dan één regeleinde splitst de query in afzonderlijke query's.

U kunt dezelfde query ook op andere manier schrijven:

```Kusto
search in (Event) "error"
```

In het tweede voorbeeld wordt met de opdracht **search** alleen gezocht naar records in de tabel **Events** voor de term **error**.

In Log Analytics worden query's standaard beperkt tot een tijdsbereik van de afgelopen 24 uur. Als u een ander tijdsbereik wilt instellen, kunt u een expliciet filter, **TimeGenerated**, aan de query toevoegen of het besturingselement **Tijdsbereik** gebruiken.

### <a name="use-the-time-range-control"></a>Besturingselement Tijdsbereik gebruiken
Als u het besturingselement **Tijdsbereik** wilt gebruiken, selecteert u het in de bovenste balk en selecteert u vervolgens een waarde in de vervolgkeuzelijst. U kunt ook **Aangepast** selecteren om een aangepast tijdsbereik te maken.

![Tijdkiezer](media/get-started-portal/time-picker.png)

- De waarden voor het tijdsbereik zijn in UTC. Dit kan afwijken van uw lokale tijdzone.
- Als met de query expliciet een filter voor **TimeGenerated** wordt ingesteld, toont het besturingselement voor de tijdkiezer **Instellen in query**. Dit wordt uitgeschakeld om een conflict te voorkomen.

### <a name="run-a-query"></a>Een query uitvoeren
Als u een query wilt uitvoeren, plaatst u de cursor ergens in de query en selecteert u **Uitvoeren** in de bovenste balk of drukt u op **Shift**+**Enter**. De query wordt uitgevoerd totdat er een lege regel wordt gevonden.

## <a name="filter-results"></a>Resultaten filteren
Log Analytics beperkt de resultaten tot maximaal 10.000 records. Een algemene query als `Event` retourneert te veel resultaten om nuttig te kunnen zijn. U kunt queryresultaten filteren door de tabelelementen in de query te beperken of door expliciet een filter aan de resultaten toe te voegen. Filteren via de tabelelementen retourneert een nieuwe resultatenset, terwijl een expliciet filter van toepassing is op de bestaande resultatenset.

### <a name="filter-by-restricting-table-elements"></a>Filteren door tabelelementen te beperken
Ga als volgt te werk als u resultaten van de `Event`-query wilt filteren op **Error**-gebeurtenissen door tabelelementen in de query te beperken:

1. Selecteer in de queryresultaten de vervolgkeuzepijl naast een record die **error** bevat in de kolom **EventLevelName**. 
   
1. Beweeg de muisaanwijzer boven de uitgebreide details en selecteer het beletselteken ( **...** ) naast **EventLevelName**. Selecteer vervolgens **Error opnemen**. 
   
   ![Filter aan query toevoegen](media/get-started-portal/add-filter.png)
   
1. U ziet dat de query in de **queryeditor** nu is gewijzigd in:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecteer **Uitvoeren** om de nieuwe query uit te voeren.

### <a name="filter-by-explicitly-filtering-results"></a>Filteren door resultaten expliciet te filteren
Ga als volgt te werk als u de resultaten van de `Event`-query wilt filteren op **Error**-gebeurtenissen door de queryresultaten te filteren:

1. Selecteer in de queryresultaten het **Filter**-pictogram naast de kolomkop **EventLevelName**. 
   
1. Selecteer **Is gelijk aan** in het eerste veld van het pop-upvenster en voer in het volgende veld *error* in. 
   
1. Selecteer **Filter**.
   
   ![Filteren](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Kolommen sorteren, groeperen en selecteren
Als u queryresultaten wilt sorteren op een specifieke kolom, zoals **TimeGenerated [UTC]** , selecteert u de kolomkop. Selecteer de kop opnieuw om tussen oplopende en aflopende volgorde te schakelen.

![Kolom sorteren](media/get-started-portal/sort-column.png)

U kunt resultaten ook ordenen op groepen. Als u resultaten op een specifieke kolom wilt groeperen, sleept u de kolomkop naar de balk boven de resultatentabel met het label **Sleep een kolomkop en zet deze hier neer als u gegevens op basis van die kolom wilt groeperen**. Als u subgroepen wilt maken, sleept u de andere kolommen naar de bovenste balk. U kunt de hiërarchie en de sortering van de groepen en subgroepen in de balk herschikken.

![Groepen](media/get-started-portal/groups.png)

Als u kolommen in de resultaten wilt verbergen of weergeven, selecteert u boven de tabel **Kolommen** en selecteert of deselecteert u de gewenste kolommen in de vervolgkeuzelijst.

![Kolommen selecteren](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Grafieken weergeven en wijzigen
U kunt queryresultaten ook in een visuele opmaak bekijken. Voer de volgende query in als voorbeeld:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

De resultaten worden standaard weergegeven in een tabel. Selecteer **Grafiek** boven de tabel om de resultaten te bekijken in een grafische weergave.

![Staafdiagram](media/get-started-portal/bar-chart.png)

De resultaten worden weergegeven in een gestapeld staafdiagram. Selecteer andere opties, zoals **Gestapelde kolom** of **Cirkel** om andere weergaven van de resultaten te tonen.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

U kunt de eigenschappen van de weergave, zoals x- en y-assen of voorkeuren voor groeperen en splitsen, handmatig wijzigen via de werkbalk.

Met behulp van de operator [render](/azure/kusto/query/renderoperator) kunt u de voorkeursweergave ook instellen in de query zelf.

## <a name="pin-results-to-a-dashboard"></a>Resultaten vastmaken aan een dashboard

Als u een tabel of grafiek met resultaten vanuit Log Analytics wilt vastmaken aan een gedeeld Azure-dashboard, selecteert u in de bovenste balk **Vastmaken aan dashboard**. 

![Vastmaken aan dashboard](media/get-started-portal/pin-dashboard.png)

In het deelvenster **Vastmaken aan een ander dashboard** selecteert of maakt u een gedeeld dashboard waaraan u resultaten kunt vastmaken. Selecteer vervolgens **Toepassen**. De tabel of grafiek wordt weergegeven op het geselecteerde Azure-dashboard.

![Aan dashboard vastgemaakte grafiek](media/get-started-portal/pin-dashboard2.png)

Een tabel of grafiek die u vastmaakt aan een gedeeld dashboard kent de volgende vereenvoudigingen: 

- De gegevens zijn beperkt tot de afgelopen veertien dagen.
- In een tabel worden maximaal vier kolommen en de bovenste zeven rijen weer gegeven.
- In grafieken met veel discrete categorieën worden minder gevulde categorieën automatisch gegroepeerd in de enkelvoudige bin **Overige**.

## <a name="save-load-or-export-queries"></a>Query's opslaan, laden of exporteren

Wanneer u een query hebt gemaakt, kunt u de query of de resultaten opslaan of met anderen delen. 

### <a name="save-queries"></a>Query's opslaan

Een query opslaan:

1. Selecteer **Opslaan** in de bovenste balk.
   
1. Geef in het dialoogvenster **Opslaan** de query een **naam** met behulp van de tekens a–z, A–Z, 0-9, spatie, afbreekstreepje, onderstrepingsteken, punt, haakje of sluisteken. 
   
1. Selecteer of de query moet worden opgeslagen als een **query** of als een **functie**. Functies zijn query's waarnaar andere query's kunnen verwijzen. 
   
   Als u een query als een functie wilt opslaan, geeft u een **functiealias**op. Dit is een korte naam die andere query's kunnen gebruiken om deze query aan te roepen.
   
1. Als u in een Log Analytics-werkruimte zit, geeft u een **Categorie** op voor **Queryverkenner** om voor de query te gebruiken. (Categorieën zijn niet beschikbaar voor Application Insights-query’s.)
   
1. Selecteer **Opslaan**.
   
   ![Functie opslaan](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Query's laden
Als u een opgeslagen query wilt laden, selecteert u in de rechterbovenhoek **Queryverkenner**. Het deelvenster **Queryverkenner** wordt geopend, waarin alle query's per categorie worden vermeld. Vouw de categorieën uit of voer op de zoekbalk een querynaam in. Selecteer vervolgens een query om deze in de **queryeditor** te laden. U kunt een query als **favoriet** markeren door de ster naast de naam van de query te selecteren.

![Queryverkenner](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Query's exporteren en delen
Als u een query wilt exporteren, selecteert u **Exporteren** in de bovenste balk en selecteert u vervolgens **Exporteren naar CSV - alle kolommen**, **Exporteren naar CSV - weergegeven kolommen** of **Exporteren naar Power BI (M Query)** in de vervolgkeuzelijst.

De volgende video laat zien hoe u Log Analytics integreert met Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Als u een koppeling naar een query wilt delen, selecteert u **Koppeling kopiëren** in de bovenste balk en selecteert u vervolgens **Koppeling naar query kopiëren**, **Querytekst kopiëren** of **Queryresultaten kopiëren** om naar het klembord te kopiëren. U kunt de querykoppeling verzenden naar anderen die toegang hebben tot dezelfde werkruimte.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende zelfstudie voor meer informatie over het schrijven van Azure Monitor-logboekquery's.
> [!div class="nextstepaction"]
> [Azure Monitor-logboekquery's schrijven](get-started-queries.md)
