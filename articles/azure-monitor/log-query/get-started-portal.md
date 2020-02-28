---
title: Aan de slag met Azure Monitor Log Analytics | Microsoft Docs
description: In dit artikel vindt u een zelf studie over het gebruik van Log Analytics in de Azure Portal om query's te schrijven.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1cf1695db50e6aee2a5dae24ed5231fdda7c12de
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670233"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Aan de slag met Log Analytics in Azure Monitor

> [!NOTE]
> U kunt deze oefening in uw eigen omgeving door lopen als u gegevens verzamelt van ten minste één virtuele machine. Als dat niet het geval is, gebruikt u onze [demo omgeving](https://portal.loganalytics.io/demo), die veel voorbeeld gegevens bevat.

In deze zelf studie leert u hoe u Log Analytics in de Azure Portal kunt gebruiken om Azure Monitor logboek query's te schrijven. U leert het volgende:

- Log Analytics gebruiken om een eenvoudige query te schrijven
- Inzicht in het schema van uw gegevens
- Resultaten filteren, sorteren en groeperen
- Een tijds bereik Toep assen
- Grafieken maken
- Query's opslaan en laden
- Query's exporteren en delen

Zie [aan de slag met logboek query's in azure monitor](get-started-queries.md)voor een zelf studie over het schrijven van logboek query's.<br>
Zie [overzicht van logboek query's in azure monitor](log-query-overview.md)voor meer informatie over logboek query's.

## <a name="meet-log-analytics"></a>Voldoen aan Log Analytics
Log Analytics is een webprogramma dat wordt gebruikt om Azure Monitor-logboek query's te schrijven en uit te voeren. Open het door **Logboeken** te selecteren in het menu Azure monitor. Deze begint met een nieuwe lege query.

![Startpagina](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Firewall vereisten
Als u Log Analytics wilt gebruiken, moet uw browser toegang hebben tot de volgende adressen. Als uw browser toegang heeft tot de Azure Portal via een firewall, moet u toegang tot deze adressen inschakelen.

| Uri | IP | Poorten |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisch | 80,443 |
| api.loganalytics.io | Dynamisch | 80,443 |
| docs.loganalytics.io | Dynamisch | 80,443 |

## <a name="basic-queries"></a>Eenvoudige query's
Query's kunnen worden gebruikt om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van uw gegevens. Beginnen met een basis query:

```Kusto
Event | search "error"
```

Met deze query wordt de _gebeurtenis_ tabel doorzocht op records die de term _fout_ bevatten in een wille keurige eigenschap.

Query's kunnen beginnen met een tabel naam of een [Zoek](/azure/kusto/query/searchoperator) opdracht. Het bovenstaande voor beeld begint met de _gebeurtenis_tabel naam, waarmee alle records uit de gebeurtenis tabel worden opgehaald. Het sluis teken (|) scheidt opdrachten, waardoor de uitvoer van de eerste opdracht als de invoer van de volgende opdrachten fungeert. U kunt elk gewenst aantal opdrachten toevoegen aan één query.

Een andere manier om dezelfde query te schrijven, is:

```Kusto
search in (Event) "error"
```

In dit voor beeld is het bereik van de **Zoek opdracht** naar de _gebeurtenis_ tabel en worden alle records in de tabel doorzocht op de _fout_term.

## <a name="running-a-query"></a>Een query uitvoeren
Voer een query uit door op de knop **uitvoeren** te klikken of op **SHIFT + ENTER**te drukken. Houd rekening met de volgende details die bepalen welke code wordt uitgevoerd en welke gegevens worden geretourneerd:

- Regel einden: een enkele onderbreking zorgt ervoor dat uw query gemakkelijker te lezen is. Meerdere regel einden worden in afzonderlijke query's gesplitst.
- Cursor: plaats de cursor ergens in de query om deze uit te voeren. De huidige query wordt als code beschouwd totdat een lege regel wordt gevonden.
- Tijds bereik: een tijds bereik van de _laatste 24 uur_ wordt standaard ingesteld. Als u een ander bereik wilt gebruiken, gebruikt u de tijd kiezer of voegt u een expliciet tijds bereik filter toe aan uw query.


## <a name="understand-the-schema"></a>Informatie over het schema
Het schema is een verzameling tabellen die visueel worden gegroepeerd onder een logische categorie. Diverse categorieën zijn van bewakings oplossingen. De categorie _LogManagement_ bevat algemene gegevens, zoals Windows-en syslog-gebeurtenissen, prestatie gegevens en Heartbeats van agents.

![Schema](media/get-started-portal/schema.png)

In elke tabel worden gegevens geordend in kolommen met verschillende gegevens typen, zoals wordt aangegeven door de pictogrammen naast de naam van de kolom. Bijvoorbeeld, de _gebeurtenis_ tabel die in de scherm afbeelding wordt weer gegeven, bevat kolommen zoals een _computer_ die tekst is, _EventCategory_ die een getal is en _TimeGenerated_ de datum/tijd.

## <a name="filter-the-results"></a>De resultaten filteren
Begin met het ophalen van alles in de _gebeurtenis_ tabel.

```Kusto
Event
```

Log Analytics resultaten automatisch bereiken:

- Tijds bereik: standaard zijn query's beperkt tot de afgelopen 24 uur.
- Aantal resultaten: de resultaten zijn beperkt tot Maxi maal 10.000 records.

Deze query is zeer algemeen en er worden te veel resultaten geretourneerd om nuttig te zijn. U kunt de resultaten filteren door middel van de tabel elementen of door expliciet een filter toe te voegen aan de query. Het filteren van resultaten via de tabel elementen is van toepassing op de bestaande resultatenset, terwijl een filter op de query zelf een nieuwe gefilterde resultatenset retourneert, waardoor nauw keurigere resultaten kunnen worden verkregen.

### <a name="add-a-filter-to-the-query"></a>Een filter toevoegen aan de query
Er staat een pijl links van elke record. Klik op deze pijl om de details voor een bepaalde record openen.

Beweeg de muis aanwijzer boven een kolom naam voor de pictogrammen ' + ' en '-' om weer te geven. Als u een filter wilt toevoegen waarmee alleen records met dezelfde waarde worden geretourneerd, klikt u op het plus teken (+). Klik op-om records met deze waarde uit te sluiten en klik vervolgens op **uitvoeren** om de query opnieuw uit te voeren.

![Filter aan query toevoegen](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>De tabel elementen filteren
We gaan nu de aandacht richten op gebeurtenissen met een Ernst _fout_. Dit is opgegeven in een kolom met de naam _EventLevelName_. U moet naar rechts schuiven om deze kolom weer te geven.

Klik op het filter pictogram naast de kolom Titel en selecteer in het pop-upvenster waarden die _beginnen met_ de tekst _fout_:

![Filteren](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Resultaten sorteren en groeperen
De resultaten worden nu beperkt zodat er alleen fout gebeurtenissen van SQL Server worden opgenomen, die in de afgelopen 24 uur zijn gemaakt. De resultaten worden echter op geen enkele manier gesorteerd. Als u de resultaten wilt sorteren op een specifieke kolom, zoals _tijds tempel_ , klikt u op de kolom Titel. Met één klik kunt u in oplopende volg orde sorteren terwijl een tweede klik aflopend wordt gesorteerd.

![Kolom sorteren](media/get-started-portal/sort-column.png)

Een andere manier om de resultaten te organiseren is per groep. Als u resultaten wilt groeperen op een specifieke kolom, sleept u de kolomkop naar boven de andere kolommen. Als u subgroepen wilt maken, sleept u ook andere kolommen de bovenste balk.

![Groepen](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Kolommen selecteren om weer te geven
De resultaten tabel bevat vaak veel kolommen. Het kan voor komen dat sommige van de geretourneerde kolommen standaard niet worden weer gegeven of dat u een aantal kolommen wilt verwijderen die worden weer gegeven. Als u de kolommen wilt selecteren die u wilt weer geven, klikt u op de knop kolommen:

![Kolommen selecteren](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Een tijds bereik selecteren
Log Analytics past standaard het tijds bereik van de _laatste 24 uur_ toe. Als u een ander bereik wilt gebruiken, selecteert u een andere waarde door middel van de tijd kiezer en klikt u op **uitvoeren**. Naast de vooraf ingestelde waarden, kunt u de optie _aangepast tijds bereik_ gebruiken om een absoluut bereik voor uw query te selecteren.

![Tijd kiezer](media/get-started-portal/time-picker.png)

Wanneer u een aangepast tijds bereik selecteert, zijn de geselecteerde waarden in UTC. Dit kan afwijken van uw lokale tijd zone.

Als de query expliciet een filter voor _TimeGenerated_bevat, wordt in de titel van de tijd kiezer de _set in de query_weer gegeven. Hand matige selectie wordt uitgeschakeld om conflicten te voor komen.


## <a name="charts"></a>Diagrammen
Naast het retour neren van resultaten in een tabel, kunnen query resultaten in visuele indelingen worden weer gegeven. Gebruik de volgende query als voor beeld:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Standaard worden de resultaten weer gegeven in een tabel. Klik op _grafiek_ om de resultaten in een grafische weer gave te bekijken:

![Staaf diagram](media/get-started-portal/bar-chart.png)

De resultaten worden weer gegeven in een gestapeld staaf diagram. Klik op _gestapelde kolom_ en selecteer _cirkel_ om een andere weer gave van de resultaten weer te geven:

![Cirkel diagram](media/get-started-portal/pie-chart.png)

Verschillende eigenschappen van de weer gave, zoals x-en y-assen, of groeperings-en splits voorkeuren, kunnen hand matig worden gewijzigd vanuit de controle balk.

U kunt ook de gewenste weer gave in de query zelf instellen met behulp van de operator render.

### <a name="smart-diagnostics"></a>Slimme diagnostische gegevens
Als er sprake is van een plotselinge Prikker of stap in uw gegevens, ziet u mogelijk een gemarkeerd punt op de regel. Dit geeft aan dat _slimme diagnostische gegevens_ een combi natie van eigenschappen heeft geïdentificeerd waarmee de onverwachte wijziging wordt gefilterd. Klik op het punt om meer details te krijgen over het filter en om de gefilterde versie te bekijken. Zo kunt u zien wat de wijziging heeft veroorzaakt:

![Slimme diagnostische gegevens](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Vastmaken aan dashboard
Als u een diagram of tabel wilt vastmaken aan een van uw gedeelde Azure-Dash boards, klikt u op het speld pictogram. Houd er rekening mee dat dit pictogram wordt verplaatst naar de bovenkant van het Log Analytics venster, en dat verschilt van de onderstaande scherm afbeelding.

![Vastmaken aan dashboard](media/get-started-portal/pin-dashboard.png)

Bepaalde vereenvoudigingen worden toegepast op een grafiek wanneer u deze vastmaakt aan een dash board:

- Tabel kolommen en rijen: als u een tabel wilt vastmaken aan het dash board, moet deze vier of minder kolommen bevatten. Alleen de bovenste zeven rijen worden weer gegeven.
- Tijds beperking: Query's worden automatisch beperkt tot de afgelopen veer tien dagen.
- Beperking voor het aantal bakken: als u een grafiek met een groot aantal afzonderlijke opslag locaties weergeeft, worden minder gevulde opslag locaties automatisch gegroepeerd in één _andere_ opslag locatie.

## <a name="save-queries"></a>Query's opslaan
Wanneer u een nuttige query hebt gemaakt, wilt u deze mogelijk opslaan of met anderen delen. Het pictogram **Opslaan** bevindt zich op de bovenste balk.

U kunt de volledige query pagina of een enkele query opslaan als een functie. Functies zijn query's waarnaar ook kan worden verwezen door andere query's. Als u een query als een functie wilt opslaan, moet u een functie alias opgeven. Dit is de naam die wordt gebruikt om deze query aan te roepen wanneer ernaar wordt verwezen door andere query's.

![De functie opslaan](media/get-started-portal/save-function.png)

>[!NOTE]
>De volgende tekens worden ondersteund: `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` in het veld **naam** bij het opslaan of bewerken van de opgeslagen query.

Log Analytics query's worden altijd opgeslagen naar een geselecteerde werk ruimte en gedeeld met andere gebruikers van die werk ruimte.

## <a name="load-queries"></a>Query's laden
Het pictogram query Verkenner bevindt zich in de rechter bovenhoek. Hiermee worden alle opgeslagen query's per categorie weer gegeven. U kunt hiermee ook specifieke query's markeren als favorieten om ze in de toekomst snel te vinden. Dubbel klik op een opgeslagen query om deze toe te voegen aan het huidige venster.

![Query Verkenner](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exporteren en delen als koppeling
Log Analytics ondersteunt verschillende methoden voor exporteren:

- Excel: Sla de resultaten op als een CSV-bestand.
- Power BI: Exporteer de resultaten naar Power BI. Zie [Azure monitor logboek gegevens importeren in Power bi](../../azure-monitor/platform/powerbi.md) voor meer informatie.
- Een koppeling delen: de query zelf kan worden gedeeld als een koppeling die vervolgens kan worden verzonden en uitgevoerd door andere gebruikers die toegang hebben tot dezelfde werk ruimte.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [schrijven van Azure monitor-logboek query's](get-started-queries.md).
