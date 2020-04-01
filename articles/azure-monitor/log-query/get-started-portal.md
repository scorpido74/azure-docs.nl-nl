---
title: "Zelfstudie: Aan de slag met query's voor Logboekanalyse"
description: Lees in deze zelfstudie hoe u Azure Monitor-logboekquery's schrijft en beheert met Behulp van Log Analytics in de Azure-portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80055470"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Zelfstudie: Aan de slag met query's voor Logboekanalyse

In deze zelfstudie ziet u hoe u Log Analytics gebruiken voor het schrijven, uitvoeren en beheren van Azure Monitor-logboekquery's in de Azure-portal. U Logboekanalysequery's gebruiken om naar termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten uit uw gegevens te bieden. 

In deze zelfstudie leert u hoe u Log Analytics gebruiken om:

> [!div class="checklist"]
> * Het logboekgegevensschema begrijpen
> * Eenvoudige query's schrijven en uitvoeren en het tijdsbereik voor query's wijzigen
> * Queryresultaten filteren, sorteren en groeperen
> * Visuals van queryresultaten weergeven, wijzigen en delen
> * Query's en resultaten opslaan, laden, exporteren en kopiëren

Zie [Overzicht van logboekquery's in Azure Monitor](log-query-overview.md)voor meer informatie over logboekquery's.<br/>
Zie [Aan de slag met logboekquery's in Azure Monitor](get-started-queries.md)voor een gedetailleerde zelfstudie over het schrijven van logboekquery's.

## <a name="open-log-analytics"></a>Logboekanalyse openen
Als u Log Analytics wilt gebruiken, moet u zijn aangemeld bij een Azure-account. Als u geen Azure-account hebt, [maakt u er gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Om de meeste stappen in deze zelfstudie te voltooien, u [deze demo-omgeving](https://portal.loganalytics.io/demo)gebruiken, die veel voorbeeldgegevens bevat. Met de demo-omgeving u geen query's opslaan of resultaten vastmaken aan een dashboard.

U ook uw eigen omgeving gebruiken als u Azure Monitor gebruikt om logboekgegevens te verzamelen op ten minste één Azure-bron. Als u een werkruimte Logboekanalyse wilt openen, selecteert u **logboeken**in de linkernavigatie van Azure Monitor . 

## <a name="understand-the-schema"></a>Informatie over het schema
Een *schema* is een verzameling tabellen gegroepeerd onder logische categorieën. Het Demo-schema bevat verschillende categorieën van monitoringoplossingen. De categorie **LogManagement** bevat bijvoorbeeld Windows- en Syslog-gebeurtenissen, prestatiegegevens en agentheartbeats.

De schematabellen worden weergegeven op het tabblad **Tabellen** van de werkruimte Log Analytics. De tabellen bevatten kolommen, elk met een gegevenstype dat wordt weergegeven door het pictogram naast de kolomnaam. De tabel **Gebeurtenis** bevat bijvoorbeeld tekstkolommen zoals **Computer** en numerieke kolommen zoals **Gebeurteniscategorie**.

![Schema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Basisquery's schrijven en uitvoeren

Log Analytics wordt geopend met een nieuwe lege query in de **queryeditor**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Een query schrijven
Azure Monitor-logboekquery's gebruiken een versie van de Kusto-querytaal. Query's kunnen beginnen met een tabelnaam of een [zoekopdracht.](/azure/kusto/query/searchoperator) 

Met de volgende query worden alle records uit de **gebeurtenistabel** opgehaald:

```Kusto
Event
```

Het pipe -teken (|) scheidt opdrachten, zodat de uitvoer van de eerste opdracht de invoer van de volgende opdracht is. U een willekeurig aantal opdrachten aan één query toevoegen. Met de volgende query worden de records uit de **gebeurtenistabel** opgehaald en wordt deze vervolgens doorzocht naar de **termfout** in een eigenschap:

```Kusto
Event 
| search "error"
```

Een regeleinde maakt query's gemakkelijker te lezen. Meer dan één regeleinde splitst de query op in afzonderlijke query's.

Een andere manier om dezelfde query te schrijven is:

```Kusto
search in (Event) "error"
```

In het tweede voorbeeld zoekt de **zoekopdracht** alleen records in de tabel **Gebeurtenissen** voor de term **fout**.

Log Analytics beperkt query's standaard tot een tijdsbereik van de afgelopen 24 uur. Als u een ander tijdsbereik wilt instellen, u een expliciet **TimeGenerated-filter** aan de query toevoegen of het **besturingselement Tijdbereik** gebruiken.

### <a name="use-the-time-range-control"></a>Het besturingselement Tijdbereik gebruiken
Als u het besturingselement **Tijdbereik** wilt gebruiken, selecteert u deze in de bovenste balk en selecteert u een waarde in de vervolgkeuzelijst of selecteert **u Aangepast** om een aangepast tijdbereik te maken.

![Tijdkiezer](media/get-started-portal/time-picker.png)

- Tijdbereikwaarden bevinden zich in UTC, wat anders kan zijn dan uw lokale tijdzone.
- Als de query expliciet een filter instelt voor **TimeGenerated,** wordt het tijdkiezerbesturingselement **Inquery instellen**weergegeven en is uitgeschakeld om een conflict te voorkomen.

### <a name="run-a-query"></a>Een query uitvoeren
Als u een query wilt uitvoeren, plaatst u de cursor ergens in de query en selecteert u **Uitvoeren** in de bovenste balk of drukt u op **Shift**+**Enter**. De query wordt uitgevoerd totdat er een lege regel wordt gevonden.

## <a name="filter-results"></a>Resultaten filteren
Log Analytics beperkt de resultaten tot maximaal 10.000 records. Een algemene `Event` query zoals retourneert te veel resultaten om nuttig te zijn. U queryresultaten filteren door de tabelelementen in de query te beperken of door expliciet een filter toe te voegen aan de resultaten. Als u door de tabelelementen filtert, wordt een nieuwe resultaatset geretourneerd, terwijl een expliciet filter van toepassing is op de bestaande resultaatset.

### <a name="filter-by-restricting-table-elements"></a>Filteren door tabelelementen te beperken
Ga `Event` als u queryresultaten filtert naar **foutgebeurtenissen** door tabelelementen in de query te beperken:

1. Selecteer in de queryresultaten de vervolgkeuzepijl naast een record met **fout** in de kolom **EventLevelName.** 
   
1. Plaats in de uitgebreide details de bovenzet en selecteer de **...** naast **EventLevelName**en selecteer **Vervolgens 'Fout opnemen'**. 
   
   ![Filter toevoegen aan query](media/get-started-portal/add-filter.png)
   
1. De query in de **queryeditor** is nu gewijzigd in:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Selecteer **Uitvoeren** om de nieuwe query uit te voeren.

### <a name="filter-by-explicitly-filtering-results"></a>Filteren door resultaten expliciet te filteren
Ga als `Event` u de queryresultaten filteren op **foutgebeurtenissen** door de queryresultaten te filteren:

1. Selecteer in de queryresultaten het pictogram **Filter** naast de kolomkop **EventLevelName**. 
   
1. Selecteer in het eerste veld van het pop-upvenster **Is gelijk aan**en voer in het volgende veld *fout*in . 
   
1. Selecteer **Filter**.
   
   ![Filteren](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Kolommen sorteren, groeperen en selecteren
Als u queryresultaten wilt sorteren op een specifieke kolom, zoals **TimeGenerated [UTC],** selecteert u de kolomkop. Selecteer de kop opnieuw om te schakelen tussen oplopende en aflopende volgorde.

![Kolom sorteren](media/get-started-portal/sort-column.png)

Een andere manier om resultaten te organiseren is per groep. Als u resultaten wilt groeperen op een specifieke kolom, sleept u de kolomkop naar de balk boven de resultatentabel met het label **Sleep een kolomkop en zet u deze hier neer om te groeperen op die kolom**. Als u subgroepen wilt maken, sleept u andere kolommen naar de bovenste balk. U de hiërarchie en het sorteren van de groepen en subgroepen in de balk opnieuw rangschikken.

![Groepen](media/get-started-portal/groups.png)

Als u kolommen in de resultaten wilt verbergen of weergeven, selecteert u **Kolommen** boven de tabel en selecteert of schakelt u de gewenste kolommen uit de vervolgkeuzelijst uit of uit.

![Kolommen selecteren](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Grafieken weergeven en wijzigen
U queryresultaten ook in visuele indelingen bekijken. Voer als voorbeeld de volgende query in:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Standaard worden de resultaten weergegeven in een tabel. Selecteer **Grafiek** boven de tabel om de resultaten in een grafische weergave weer te geven.

![Staafdiagram](media/get-started-portal/bar-chart.png)

De resultaten worden weergegeven in een gestapeld staafdiagram. Selecteer andere opties zoals **Gestapelde kolom** of **cirkel** om andere weergaven van de resultaten weer te geven.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

U de eigenschappen van de weergave, zoals x- en y-assen of voorkeuren voor groeperen en splitsen, handmatig wijzigen vanaf de besturingselementbalk.

U ook de gewenste weergave in de query zelf instellen met behulp van de [renderoperator.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Resultaten vastmaken aan een dashboard
Als u een resultatentabel of grafiek van Log Analytics wilt vastmaken aan een gedeeld Azure-dashboard, selecteert u **Vastmaken aan dashboard** op de bovenste balk. 

![Vastmaken aan dashboard](media/get-started-portal/pin-dashboard.png)

Selecteer of maak in het **deelvenster Vastmaken aan een ander dashboard** een gedeeld dashboard waaraan u wilt **vastmaken**en selecteer Toepassen . De tabel of grafiek wordt weergegeven op het geselecteerde Azure-dashboard.

![Grafiek vastgemaakt aan dashboard](media/get-started-portal/pin-dashboard2.png)

Een tabel of grafiek die u vastmaakt aan een gedeeld dashboard, heeft de volgende vereenvoudigingen: 

- Gegevens zijn beperkt tot de afgelopen 14 dagen.
- Een tabel toont slechts maximaal vier kolommen en de bovenste zeven rijen.
- Grafieken met veel afzonderlijke categorieën groeperen automatisch minder bevolkte categorieën in één **andere** opslaglocatie.

## <a name="save-load-or-export-queries"></a>Query's opslaan, laden of exporteren
Zodra u een query hebt gemaakt, u de query of resultaten opslaan of delen met anderen. 

### <a name="save-queries"></a>Query's opslaan
Ga als lid van het nieuwe bedrijf naar een query:

1. Selecteer **Opslaan** op de bovenste balk.
   
1. Geef de query in het dialoogvenster **Opslaan** een **naam**met de tekens a-z, A–Z, 0-9, ruimte, koppelteken, underscore, periode, haakjes of pijp. 
   
1. Selecteer of u de query wilt opslaan als **query** of **functie**. Functies zijn query's waarnaar andere query's kunnen worden verwezen. 
   
   Als u een query als functie wilt opslaan, geeft u een **functiealias**op, een korte naam die andere query's kunnen gebruiken om deze query aan te roepen.
   
1. Geef een **categorie** op die **queryverkenner** voor de query gebruiken.
   
1. Selecteer **Opslaan**.
   
   ![Opslaan, functie](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Query's laden
Als u een opgeslagen query wilt laden, selecteert u **Queryverkenner** rechtsboven. Het deelvenster **Queryverkenner** wordt geopend en geeft alle query's per rubriek weer. Vouw de categorieën uit of voer een querynaam in de zoekbalk in en selecteer vervolgens een query om deze in de **queryeditor**te laden. U een query markeren als **favoriet** door de ster naast de querynaam te selecteren.

![Queryverkenner](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Query's exporteren en delen
Als u een query wilt exporteren, selecteert u **Exporteren** op de bovenste balk en selecteert u **Exporteren naar CSV - alle kolommen**, Exporteren naar CSV - weergegeven **kolommen**of Exporteren naar Power **BI (M-query)** in de vervolgkeuzelijst.

Als u een koppeling naar een query wilt delen, selecteert u **Koppeling kopiëren** op de bovenste balk en selecteert u Vervolgens Koppeling kopiëren **naar query,** **Querytekst kopiëren**of **Queryresultaten kopiëren** om naar het klembord te kopiëren. U de querykoppeling verzenden naar anderen die toegang hebben tot dezelfde werkruimte.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie voor meer informatie over het schrijven van Azure Monitor-logboekquery's.
> [!div class="nextstepaction"]
> [Azure Monitor-logboekquery's schrijven](get-started-queries.md)
