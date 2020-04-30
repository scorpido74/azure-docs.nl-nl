---
title: "Zelf studie: aan de slag met Log Analytics query's"
description: Leer in deze zelf studie hoe u Azure Monitor logboek query's schrijft en beheert met behulp van Log Analytics in de Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80055470"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Zelf studie: aan de slag met Log Analytics query's

Deze zelf studie laat zien hoe u Log Analytics kunt gebruiken om Azure Monitor logboek query's te schrijven, uit te voeren en te beheren in de Azure Portal. U kunt Log Analytics query's gebruiken om te zoeken naar termen, trends te identificeren, patronen te analyseren en allerlei andere inzichten van uw gegevens te bieden. 

In deze zelf studie leert u hoe u Log Analytics kunt gebruiken voor het volgende:

> [!div class="checklist"]
> * Meer informatie over het schema voor logboek gegevens
> * Eenvoudige query's schrijven en uitvoeren en het tijds bereik voor query's wijzigen
> * Query resultaten filteren, sorteren en groeperen
> * Visuele elementen van query resultaten weer geven, wijzigen en delen
> * Query's en resultaten opslaan, laden, exporteren en kopiëren

Zie [overzicht van logboek query's in azure monitor](log-query-overview.md)voor meer informatie over logboek query's.<br/>
Zie [aan de slag met logboek query's in azure monitor](get-started-queries.md)voor een gedetailleerde zelf studie over het schrijven van logboek query's.

## <a name="open-log-analytics"></a>Log Analytics openen
Als u Log Analytics wilt gebruiken, moet u zijn aangemeld bij een Azure-account. Als u geen Azure-account hebt, [maakt u er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Als u de meeste stappen in deze zelf studie wilt volt ooien, kunt u [deze demo omgeving](https://portal.loganalytics.io/demo)gebruiken, die veel voorbeeld gegevens bevat. Met de demo omgeving kunt u geen query's opslaan of resultaten vastmaken aan een dash board.

U kunt ook uw eigen omgeving gebruiken als u Azure Monitor gebruikt voor het verzamelen van logboek gegevens op ten minste één Azure-resource. Als u een Log Analytics-werk ruimte wilt openen, selecteert u **Logboeken**in uw Azure monitor navigatie links. 

## <a name="understand-the-schema"></a>Informatie over het schema
Een *schema* is een verzameling tabellen die zijn gegroepeerd onder logische categorieën. Het demo schema heeft verschillende categorieën van het controleren van oplossingen. De categorie **LogManagement** bevat bijvoorbeeld Windows-en syslog-gebeurtenissen, prestatie gegevens en heartbeats voor agents.

De schema tabellen worden weer gegeven op het tabblad **tabellen** van de werk ruimte log Analytics. De tabellen bevatten kolommen, elk met een gegevens type dat wordt weer gegeven met het pictogram naast de naam van de kolom. De **gebeurtenis** tabel bevat bijvoorbeeld tekst kolommen zoals **computer** en numerieke kolommen zoals **EventCategory**.

![Schema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Eenvoudige query's schrijven en uitvoeren

Log Analytics wordt geopend met een nieuwe lege query in de **query-editor**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Een query schrijven
Azure Monitor-logboek query's gebruiken een versie van de Kusto-query taal. Query's kunnen beginnen met een tabel naam of een [Zoek](/azure/kusto/query/searchoperator) opdracht. 

Met de volgende query worden alle records opgehaald uit de **gebeurtenis** tabel:

```Kusto
Event
```

Het sluis teken (|) scheidt opdrachten, dus de uitvoer van de eerste opdracht is de invoer van de volgende opdracht. U kunt elk gewenst aantal opdrachten toevoegen aan één query. Met de volgende query worden de records opgehaald uit de **gebeurtenis** tabel en wordt deze vervolgens in een wille keurige eigenschap gezocht naar de term **fout** :

```Kusto
Event 
| search "error"
```

Met één regel afbreek bewerking worden query's gemakkelijker te lezen. Meer dan één regel afbreek de query splitst in afzonderlijke query's.

Een andere manier om dezelfde query te schrijven is:

```Kusto
search in (Event) "error"
```

In het tweede voor beeld zoekt de **Zoek** opdracht alleen records in de tabel **gebeurtenissen** voor de term **fout**.

Log Analytics beperkt query's standaard tot een tijds bereik van de afgelopen 24 uur. Als u een ander tijds bereik wilt instellen, kunt u een expliciet **TimeGenerated** -filter toevoegen aan de query of het besturings element **tijd bereik** gebruiken.

### <a name="use-the-time-range-control"></a>Het besturings element tijd bereik gebruiken
Als u het besturings element **tijd bereik** wilt gebruiken, selecteert u het in de bovenste balk en selecteert u een waarde in de vervolg keuzelijst of selecteert u **aangepast** om een aangepast tijds bereik te maken.

![Tijd kiezer](media/get-started-portal/time-picker.png)

- De waarden voor het tijds bereik zijn UTC. Dit kan afwijken van uw lokale tijd zone.
- Als met de query expliciet een filter voor **TimeGenerated**wordt ingesteld, wordt in het besturings element voor de tijd kiezer de **instelling in de query**weer gegeven. dit wordt uitgeschakeld om conflicten te voor komen.

### <a name="run-a-query"></a>Een query uitvoeren
Als u een query wilt uitvoeren, plaatst u de cursor ergens in de query en selecteert u **uitvoeren** in de bovenste balk **of drukt u**+op**Enter**. De query wordt uitgevoerd totdat er een lege regel wordt gevonden.

## <a name="filter-results"></a>Resultaten filteren
Log Analytics beperkt de resultaten tot Maxi maal 10.000 records. Een algemene query zoals `Event` retourneert te veel resultaten om nuttig te zijn. U kunt query resultaten filteren door de tabel elementen in de query te beperken of door expliciet een filter toe te voegen aan de resultaten. Filteren via de tabel elementen retourneert een nieuwe resultatenset, terwijl een expliciet filter van toepassing is op de bestaande resultatenset.

### <a name="filter-by-restricting-table-elements"></a>Filteren door tabel elementen te beperken
Query resultaten `Event` filteren op **fout** gebeurtenissen door tabel elementen in de query te beperken:

1. Selecteer in de query resultaten de vervolg keuze pijl naast een record met een **fout** in de kolom **EventLevelName** . 
   
1. Beweeg de muis aanwijzer in de uitgebreide details en selecteer de **...** naast **EventLevelName**en selecteer vervolgens **insluiten ' fout '**. 
   
   ![Filter aan query toevoegen](media/get-started-portal/add-filter.png)
   
1. U ziet dat de query in de **query-editor** nu is gewijzigd in:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecteer **uitvoeren** om de nieuwe query uit te voeren.

### <a name="filter-by-explicitly-filtering-results"></a>Filteren door expliciet resultaten te filteren
Als u de `Event` query resultaten wilt filteren op **fout** gebeurtenissen door de query resultaten te filteren:

1. Selecteer in de query resultaten het **filter** pictogram naast de kolomkop **EventLevelName**. 
   
1. Selecteer in het eerste veld van het pop-upvenster de optie **is gelijk aan**en voer in het volgende veld *fout*in. 
   
1. Selecteer **filter**.
   
   ![Filteren](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Kolommen sorteren, groeperen en selecteren
Als u query resultaten wilt sorteren op een specifieke kolom, zoals **TimeGenerated [UTC]**, selecteert u de kolomkop. Selecteer de kop opnieuw om tussen oplopende en aflopende volg orde te scha kelen.

![Kolom sorteren](media/get-started-portal/sort-column.png)

Een andere manier om de resultaten te organiseren is per groep. Als u resultaten wilt groeperen op basis van een specifieke kolom, sleept u de kolomkop naar de balk boven de tabel met resultaten met **een label sleept u een kolomkop en zet u deze hier neer op groep op die kolom**. Als u subgroepen wilt maken, sleept u de andere kolommen naar de bovenste balk. U kunt de volg orde van de hiërarchie en het sorteren van de groepen en subgroepen in de balk wijzigen.

![Groepen](media/get-started-portal/groups.png)

Als u kolommen in de resultaten wilt verbergen of weer geven, selecteert u **kolommen** boven de tabel en selecteert of deselecteert u de gewenste kolommen in de vervolg keuzelijst.

![Kolommen selecteren](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Grafieken weer geven en wijzigen
U kunt query resultaten ook in visuele indelingen bekijken. Voer de volgende query in als voor beeld:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Standaard worden resultaten weer gegeven in een tabel. Selecteer **diagram** boven de tabel om de resultaten weer te geven in een grafische weer gave.

![Staafdiagram](media/get-started-portal/bar-chart.png)

De resultaten worden weer gegeven in een gestapeld staaf diagram. Selecteer andere opties, zoals **gestapelde kolom** of **cirkel** , om andere weer gaven van de resultaten weer te geven.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

U kunt de eigenschappen van de weer gave, zoals x-en y-assen of groeperen en splitsen, hand matig vanuit de werk balk wijzigen.

U kunt ook de gewenste weer gave in de query zelf instellen met behulp van de operator [render](/azure/kusto/query/renderoperator) .

## <a name="pin-results-to-a-dashboard"></a>Resultaten vastmaken aan een dash board
Als u een tabel met resultaten of een grafiek van Log Analytics wilt vastmaken aan een gedeeld Azure-dash board, selecteert **u vastmaken aan dash board** in de bovenste balk. 

![Vastmaken aan dashboard](media/get-started-portal/pin-dashboard.png)

In het deel venster vastmaken **aan een ander dash board** selecteert of maakt u een gedeeld dash board voor vastmaken aan en selecteert u **Toep assen**. De tabel of grafiek wordt weer gegeven op het geselecteerde Azure-dash board.

![De grafiek is vastgemaakt aan het dash board](media/get-started-portal/pin-dashboard2.png)

Een tabel of diagram die u vastmaakt aan een gedeeld dash board, heeft de volgende vereenvoudiging: 

- De gegevens zijn beperkt tot de afgelopen veer tien dagen.
- In een tabel worden Maxi maal vier kolommen en de bovenste zeven rijen weer gegeven.
- Grafieken met veel discrete Categorieën groeperen minder gevulde categorieën in één **andere** opslag locatie.

## <a name="save-load-or-export-queries"></a>Query's opslaan, laden of exporteren
Wanneer u een query hebt gemaakt, kunt u de query of resultaten met anderen opslaan of delen. 

### <a name="save-queries"></a>Query's opslaan
Een query opslaan:

1. Selecteer **Opslaan** op de bovenste balk.
   
1. Geef in het dialoog venster **Opslaan** de query een **naam**met behulp van de tekens A – Z, a – z, 0-9, spatie, afbreek streepje, onderstrepings teken, punt, haakjes of pipe. 
   
1. Selecteer of u de query als een **query** of een **functie**wilt opslaan. Functies zijn query's waarnaar andere query's kunnen verwijzen. 
   
   Als u een query als een functie wilt opslaan, moet u een **functie alias**opgeven. Dit is een korte naam voor andere query's die moeten worden gebruikt om deze query aan te roepen.
   
1. Geef een **categorie** op die door **query Explorer** moet worden gebruikt voor de query.
   
1. Selecteer **Opslaan**.
   
   ![De functie opslaan](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Query's laden
Als u een opgeslagen query wilt laden, selecteert u in de rechter bovenhoek **query Verkenner** . Het deel venster **query Verkenner** wordt geopend, waarin alle query's per categorie worden weer gegeven. Vouw de categorieën uit of voer een query naam in op de zoek balk en selecteer vervolgens een query om deze in de **query-editor**te laden. U kunt een query als **favoriet** markeren door de ster naast de naam van de query te selecteren.

![Query Verkenner](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Query's exporteren en delen
Als u een query wilt exporteren, selecteert u **exporteren** in de bovenste balk en selecteert **u vervolgens exporteren naar CSV-alle kolommen**, **exporteren naar CSV-weer gegeven kolommen**of **exporteren naar Power bi (M query)** uit de vervolg keuzelijst.

Als u een koppeling naar een query wilt delen, selecteert u **koppeling kopiëren** op de bovenste balk en selecteert **u vervolgens koppeling naar query kopiëren**, **query tekst kopiëren**of **query resultaten** kopiëren om naar het klem bord te kopiëren. U kunt de query koppeling verzenden naar anderen die toegang hebben tot dezelfde werk ruimte.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelf studie voor meer informatie over het schrijven van Azure Monitor-logboek query's.
> [!div class="nextstepaction"]
> [Azure Monitor-logboek query's schrijven](get-started-queries.md)
