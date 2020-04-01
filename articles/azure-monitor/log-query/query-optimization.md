---
title: Logboekquery's optimaliseren in Azure Monitor
description: Aanbevolen procedures voor het optimaliseren van logboekquery's in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411437"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Logboekquery's optimaliseren in Azure Monitor
Azure Monitor Logs gebruikt [Azure Data Explorer (ADX)](/azure/data-explorer/) om logboekgegevens op te slaan en query's uit te voeren voor het analyseren van die gegevens. Het maakt, beheert en onderhoudt de ADX-clusters voor u en optimaliseert ze voor uw logboekanalysewerk. Wanneer u een query uitvoert, wordt deze geoptimaliseerd en doorgestuurd naar het juiste ADX-cluster waarmee de werkruimtegegevens worden opgeslagen. Zowel Azure Monitor Logs als Azure Data Explorer maakt gebruik van veel automatische queryoptimalisatiemechanismen. Hoewel automatische optimalisaties een aanzienlijke boost bieden, zijn ze in sommige gevallen waarin u uw queryprestaties drastisch verbeteren. In dit artikel worden de prestatieoverwegingen en verschillende technieken uitgelegd om ze op te lossen.

De meeste technieken zijn gebruikelijk voor query's die rechtstreeks worden uitgevoerd op Azure Data Explorer en Azure Monitor Logs, hoewel er verschillende unieke Azure Monitor Logs overwegingen die hier worden besproken. Zie Aanbevolen [procedures voor query's](/azure/kusto/query/best-practices)voor meer optimalisatietips voor Azure Data Explorer.

Geoptimaliseerde query's zullen:

- Voer sneller uit en verkort de totale duur van de queryuitvoering.
- Hebben kleinere kans om te worden beperkt of afgewezen.

U moet bijzondere aandacht besteden aan query's die worden gebruikt voor terugkerend en bursty gebruik, zoals dashboards, waarschuwingen, Logic Apps en Power BI. De impact van een ineffectieve query is in deze gevallen aanzienlijk.

## <a name="query-performance-pane"></a>Deelvenster Queryprestaties
Nadat u een query hebt uitgevoerd in Log Analytics, klikt u op de pijl-omlaag boven de queryresultaten om het prestatievenster voor query's weer te geven waarin de resultaten van verschillende prestatie-indicatoren voor de query worden weergegeven. Deze prestatie-indicatoren worden elk beschreven in de volgende sectie.

![Deelvenster Queryprestaties](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Prestatie-indicatoren voor query's

De volgende queryprestatie-indicatoren zijn beschikbaar voor elke query die wordt uitgevoerd:

- [Totale CPU:](#total-cpu)Algemene rekenkracht wordt gebruikt om de query te verwerken voor alle compute nodes. Het vertegenwoordigt tijd die wordt gebruikt voor het berekenen, ontwenen en ophalen van gegevens. 

- [Gegevens die worden gebruikt voor verwerkte](#data-used-for-processed-query)query's: algemene gegevens die zijn geopend om de query te verwerken. Beïnvloed door de grootte van de doeltabel, gebruikte tijdspanne, toegepaste filters en het aantal kolommen waarnaar wordt verwezen.

- [Tijdspanne van de verwerkte query:](#time-span-of-the-processed-query)de kloof tussen de nieuwste en de oudste gegevens die is geopend om de query te verwerken. Beïnvloed door het expliciete tijdsbereik dat voor de query is opgegeven.

- [Leeftijd van verwerkte gegevens](#age-of-processed-data): De kloof tussen nu en de oudste gegevens die is geopend om de query te verwerken. Het beïnvloedt sterk de efficiëntie van het ophalen van gegevens.

- [Aantal werkruimten:](#number-of-workspaces)hoeveel werkruimten zijn geopend tijdens de queryverwerking vanwege impliciete of expliciete selectie.

- [Aantal regio's](#number-of-regions): Hoeveel regio's zijn geopend tijdens de queryverwerking op basis van impliciete of expliciete selectie van werkruimten. Multi-regio query's zijn veel minder efficiënt en prestatie-indicatoren presenteren gedeeltelijke dekking.

- [Parallellisme:](#parallelism)geeft aan hoeveel het systeem in staat was om deze query op meerdere knooppunten uit te voeren. Alleen relevant voor query's met een hoog CPU-verbruik. Beïnvloed door het gebruik van specifieke functies en operatoren.


## <a name="total-cpu"></a>Totaal CPU
De werkelijke compute CPU die is geïnvesteerd om deze query te verwerken in alle queryverwerkingsknooppunten. Aangezien de meeste query's worden uitgevoerd op grote aantallen knooppunten, zal dit meestal veel groter zijn dan de duur die de query daadwerkelijk heeft uitgevoerd. 

Queryverwerkingstijd wordt besteed aan:
- Het ophalen van gegevens – het ophalen van oude gegevens kost meer tijd dan het ophalen van recente gegevens.
- Gegevensverwerking – logica en evaluatie van de gegevens. 

Anders dan de tijd die wordt besteed aan de queryverwerkingsknooppunten, is er extra tijd die wordt besteed door Azure Monitor Logs om: verifieer de gebruiker en controleer of ze toegang hebben tot deze gegevens, zoek het gegevensarchief, ontleed de query en wijs de queryverwerkingsknooppunten toe. Deze tijd is niet opgenomen in de query totale CPU-tijd.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Vroege filtering van records voorafgaand aan het gebruik van hoge CPU-functies

Sommige queryopdrachten en -functies zijn zwaar in hun CPU-verbruik. Dit geldt met name voor opdrachten die JSON en XML ontleden of complexe reguliere expressies extraheren. Een dergelijke parsing kan expliciet gebeuren via [parse_json()](/azure/kusto/query/parsejsonfunction) of [parse_xml()](/azure/kusto/query/parse-xmlfunction) functies of impliciet bij het verwijzen naar dynamische kolommen.

Deze functies verbruiken CPU in verhouding tot het aantal rijen dat ze verwerken. De meest efficiënte optimalisatie is om toe te voegen waar de omstandigheden vroeg in de query die kunnen filteren zo veel records mogelijk voordat de CPU-intensieve functie wordt uitgevoerd.

De volgende query's leveren bijvoorbeeld precies hetzelfde resultaat op, maar de tweede is veruit de meest efficiënte, omdat de [voorwaarde](/azure/kusto/query/whereoperator) voor het ontschepen veel records uitsluit:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Vermijd het gebruik van geëvalueerde waar clausules

Query's die bevatten [waar](/azure/kusto/query/whereoperator) clausules in een geëvalueerde kolom in plaats van op kolommen die fysiek aanwezig zijn in de gegevensset verliezen efficiëntie. Filteren op geëvalueerde kolommen voorkomt dat sommige systeemoptimalisaties worden verwerkt wanneer grote sets gegevens worden verwerkt.
De volgende query's leveren bijvoorbeeld precies hetzelfde resultaat op, maar de tweede is efficiënter als de [plaats waar](/azure/kusto/query/whereoperator) de voorwaarde verwijst naar ingebouwde kolom

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Effectieve aggregatieopdrachten en dimvermeldingen gebruiken om samen te vatten en lid te worden

Hoewel sommige aggregatieopdrachten zoals [max()](/azure/kusto/query/max-aggfunction), [som()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)en [avg()](/azure/kusto/query/avg-aggfunction) een lage CPU-impact hebben vanwege hun logica, zijn andere complexer en omvatten heuristieken en schattingen waarmee ze efficiënt kunnen worden uitgevoerd. [Dcount()](/azure/kusto/query/dcount-aggfunction) gebruikt bijvoorbeeld het HyperLogLog-algoritme om een nauwe schatting te geven van het aantal grote sets gegevens zonder elke waarde daadwerkelijk te tellen; de percentielfuncties doen vergelijkbare benaderingen met behulp van het dichtstbijzijnde rangpercentiele algoritme. Verschillende van de opdrachten bevatten optionele parameters om de impact ervan te verminderen. De functie [makeset()](/azure/kusto/query/makeset-aggfunction) heeft bijvoorbeeld een optionele parameter om de maximale ingestelde grootte te definiëren, wat een aanzienlijke invloed heeft op de CPU en het geheugen.

[Taken deelnemen](/azure/kusto/query/joinoperator?pivots=azuremonitor) en [samenvatten](/azure/kusto/query/summarizeoperator) kan leiden tot een hoog CPU-gebruik wanneer ze een grote set gegevens verwerken. Hun complexiteit is direct gerelateerd aan het aantal mogelijke waarden, aangeduid als *kardinaliteit*, van de kolommen die worden gebruikt als de `by` in samen te vatten of als de join attributen. Voor uitleg en optimalisatie van join en summarize, zie hun documentatie artikelen en optimalisatie tips.

De volgende query's leveren bijvoorbeeld precies hetzelfde resultaat op omdat **CounterPath** altijd één-op-één is toegewezen aan **CounterName** en **ObjectName**. De tweede is efficiënter omdat de aggregatiedimensie kleiner is:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Cpu-verbruik kan ook worden beïnvloed door de omstandigheden of uitgebreide kolommen die intensief computergebruik vereisen. Alle triviale string vergelijkingen zoals [gelijk ==](/azure/kusto/query/datatypes-string-operators) en [beginmet](/azure/kusto/query/datatypes-string-operators) hebben ongeveer dezelfde CPU impact, terwijl geavanceerde tekst wedstrijden hebben meer impact. Met name de [has](/azure/kusto/query/datatypes-string-operators) operator is efficiënter dan de [operator bevat.](/azure/kusto/query/datatypes-string-operators) Dankzij snaarbehandelingstechnieken is het efficiënter om te zoeken naar tekenreeksen die langer zijn dan vier tekens dan korte tekenreeksen.

De volgende query's leveren bijvoorbeeld vergelijkbare resultaten op, afhankelijk van het beleid voor computernaamgeving, maar de tweede query is efficiënter:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Deze indicator presenteert alleen CPU van de directe cluster. In multi-regio query, zou het vertegenwoordigen slechts een van de regio's. In query's met meerdere werkruimten bevat deze mogelijk niet alle werkruimten.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Vermijd volledige XML en JSON-ontleden wanneer snaarparsing werkt
Volledige parsing van een XML- of JSON-object kan hoge CPU- en geheugenbronnen verbruiken. In veel gevallen, wanneer slechts één of twee parameters nodig zijn en de XML- of JSON-objecten eenvoudig zijn, is het gemakkelijker om ze te ontleden als tekenreeksen met behulp van de [parse-operator](/azure/kusto/query/parseoperator) of andere [tekstparsingtechnieken](/azure/azure-monitor/log-query/parse-text). De prestatieverbetering zal belangrijker zijn naarmate het aantal records in het OBJECT XML of JSON toeneemt. Het is van essentieel belang wanneer het aantal records tientallen miljoenen bereikt.

De volgende query retourneert bijvoorbeeld exact dezelfde resultaten als de bovenstaande query's zonder volledige XML-parsing uit te voeren. Houd er rekening mee dat het een aantal veronderstellingen maakt over de XML-bestandsstructuur, zoals dat FilePath-element komt na FileHash en geen van hen heeft kenmerken. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Gegevens die worden gebruikt voor verwerkte query's

Een belangrijke factor bij de verwerking van de query is het volume van de gegevens die wordt gescand en gebruikt voor de queryverwerking. Azure Data Explorer maakt gebruik van agressieve optimalisaties die het gegevensvolume drastisch verminderen in vergelijking met andere gegevensplatforms. Toch zijn er kritieke factoren in de query die van invloed kunnen zijn op het gegevensvolume dat wordt gebruikt.

In Azure Monitor Logs wordt de kolom **TimeGenerated** gebruikt om de gegevens te indexeren. Als u de **TimeGenerated-waarden** beperkt tot een zo beperkt mogelijk bereik, wordt de queryprestaties aanzienlijk verbeterd door de hoeveelheid gegevens die moet worden verwerkt aanzienlijk te beperken.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Vermijd onnodig gebruik van zoek- en vakbondsoperatoren

Een andere factor die de gegevens die proces verhoogt, is het gebruik van een groot aantal tabellen. Dit gebeurt `search *` meestal `union *` wanneer en commando's worden gebruikt. Met deze opdrachten wordt het systeem gedwongen om gegevens uit alle tabellen in de werkruimte te evalueren en te scannen. In sommige gevallen zijn er mogelijk honderden tabellen in de werkruimte. Probeer zoveel mogelijk te vermijden met behulp van "zoeken *" of een zoekopdracht zonder scoping het naar een specifieke tabel.

De volgende query's leveren bijvoorbeeld precies hetzelfde resultaat op, maar de laatste is veruit het meest efficiënt:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Vroege filters toevoegen aan de query

Een andere methode om het gegevensvolume te verminderen is om te hebben [waar](/azure/kusto/query/whereoperator) de omstandigheden vroeg in de query. Het Azure Data Explorer-platform bevat een cache waarmee het laat weten welke partities gegevens bevatten die relevant zijn voor een specifieke plaats waar. Als een query bijvoorbeeld `where EventID == 4624` wordt bevat, wordt de query alleen gedistribueerd naar knooppunten die partities verwerken met overeenkomende gebeurtenissen.

De volgende voorbeeldquery's leveren precies hetzelfde resultaat op, maar het tweede resultaat is efficiënter:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Het aantal kolommen verminderen dat wordt opgehaald

Aangezien Azure Data Explorer een kolomgegevensarchief is, is het ophalen van elke kolom onafhankelijk van de andere kolommen. Het aantal kolommen dat wordt opgehaald, is rechtstreeks van invloed op het totale gegevensvolume. U moet alleen de kolommen opnemen in de uitvoer die nodig zijn door de resultaten [samen te vatten](/azure/kusto/query/summarizeoperator) of de specifieke kolommen te [projecteren.](/azure/kusto/query/projectoperator) Azure Data Explorer heeft verschillende optimalisaties om het aantal opgehaalde kolommen te verminderen. Als wordt bepaald dat een kolom niet nodig is, bijvoorbeeld als er niet naar wordt verwezen in de opdracht [Samenvatten,](/azure/kusto/query/summarizeoperator) wordt deze niet opgehaald.

De tweede query kan bijvoorbeeld drie keer meer gegevens verwerken, omdat deze niet één kolom, maar drie moet ophalen:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Tijdspanne van de verwerkte query

Alle logboeken in Azure Monitor-logboeken worden verdeeld volgens de kolom **TimeGenerated.** Het aantal partities dat wordt geopend, is direct gerelateerd aan de tijdspanne. Het verkorten van het tijdsbereik is de meest efficiënte manier om een snelle query-uitvoering te garanderen.

Het tijdsbereik kan worden ingesteld met de tijdsbereikkiezer in het scherm Logboekanalyse zoals beschreven in [Querybereik logboek en tijdbereik in Azure Monitor Log Analytics](scope.md#time-range). Dit is de aanbevolen methode omdat het geselecteerde tijdsbereik wordt doorgegeven aan de backend met behulp van de querymetagegevens. 

Een alternatieve methode is om expliciet een [voorwaarde](/azure/kusto/query/whereoperator) op **TimeGenerated** op te nemen in de query. U moet deze methode gebruiken omdat deze ervoor zorgt dat de tijdsspanne is opgelost, zelfs wanneer de query vanuit een andere interface wordt gebruikt.
U moet ervoor zorgen dat alle onderdelen van de query **TimeGenerated-filters** bevatten. Wanneer een query subquery's bevat die gegevens ophalen uit verschillende tabellen of dezelfde tabel, moet elk query zijn eigen [voorwaarde](/azure/kusto/query/whereoperator) bevatten.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Controleer of alle subquery's het TimeGenerated-filter hebben

In de volgende query wordt bijvoorbeeld de **perf-tabel** alleen voor de laatste dag gescand, maar wordt de **heartbeattabel** gescand op al zijn geschiedenis, die mogelijk maximaal twee jaar bedraagt:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Een veel voorkomend geval waarin een dergelijke fout optreedt, is wanneer [arg_max()](/azure/kusto/query/arg-max-aggfunction) wordt gebruikt om de meest recente gebeurtenis te vinden. Bijvoorbeeld:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Dit kan eenvoudig worden gecorrigeerd door een tijdfilter toe te voegen aan de interne query:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Een ander voorbeeld voor deze fout is bij het uitvoeren van de tijdbereik filtering net na een [unie](/azure/kusto/query/unionoperator?pivots=azuremonitor) over meerdere tabellen. Bij het uitvoeren van de unie moet elke subquery worden uitgevoerd. U [let](/azure/kusto/query/letstatement) statement gebruiken om de consistentie van de scoping te garanderen.

In de volgende query worden bijvoorbeeld alle gegevens in de *heartbeat-* en *perf-tabellen* gescant, niet alleen de laatste 1 dag:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Deze query moet als volgt worden opgelost:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Beperkingen voor het meten van tijdspanne

De meting is altijd groter dan de werkelijke tijd die is opgegeven. Als het filter op de query bijvoorbeeld 7 dagen is, scant het systeem mogelijk 7,5 of 8,1 dagen. Dit komt omdat het systeem de gegevens in brokken in variabele grootte verdeelt. Om ervoor te zorgen dat alle relevante records worden gescand, scant het de hele partitie die enkele uren en zelfs meer dan een dag kan bestrijken.

Er zijn verschillende gevallen waarin het systeem geen nauwkeurige meting van het tijdsbereik kan bieden. Dit gebeurt in de meeste gevallen waarin de query minder dan een dag of in query's met meerdere werkruimtes overspant.


> [!IMPORTANT]
> Deze indicator geeft alleen gegevens weer die in het directe cluster zijn verwerkt. In multi-regio query, zou het vertegenwoordigen slechts een van de regio's. In query's met meerdere werkruimten bevat deze mogelijk niet alle werkruimten.

## <a name="age-of-processed-data"></a>Leeftijd van verwerkte gegevens
Azure Data Explorer maakt gebruik van verschillende opslaglagen: in-memory, lokale SSD-schijven en veel tragere Azure Blobs. Hoe nieuwer de gegevens, hoe hoger de kans dat deze worden opgeslagen in een meer performante laag met kleinere latentie, waardoor de queryduur en CPU worden verminderd. Anders dan de gegevens zelf, het systeem heeft ook een cache voor metadata. Hoe ouder de gegevens, hoe minder kans de metadata in de cache.

Hoewel sommige query's het gebruik van oude gegevens vereisen, zijn er gevallen waarin oude gegevens per ongeluk worden gebruikt. Dit gebeurt wanneer query's worden uitgevoerd zonder tijdsbereik in hun metagegevens te verstrekken en niet alle tabelverwijzingen bevatten filter in de **kolom TimeGenerated.** In deze gevallen scant het systeem alle gegevens die in die tabel zijn opgeslagen. Wanneer de gegevensretentie lang is, kan het betrekking hebben op lange tijdsbereiken en dus gegevens die zo oud zijn als de bewaarperiode van gegevens.

Dergelijke gevallen kunnen bijvoorbeeld zijn:

- Het tijdsbereik in Log Analytics niet instellen met een subquery die niet beperkt is. Zie voorbeeld hierboven.
- De API gebruiken zonder de optionele parameters voor het tijdsbereik.
- Met behulp van een client die geen tijdsbereik zoals de Power BI-connector forceert.

Zie voorbeelden en notities in de pervious sectie als ze ook relevant zijn in dit geval.

## <a name="number-of-regions"></a>Aantal regio's
Er zijn verschillende situaties waarin één query in verschillende regio's kan worden uitgevoerd:

- Wanneer meerdere werkruimten expliciet worden vermeld en ze zich in verschillende regio's bevinden.
- Wanneer een query met resourcebereik gegevens ophaalt en de gegevens worden opgeslagen in meerdere werkruimten die zich in verschillende regio's bevinden.

Voor het uitvoeren van query's met meerdere regio's moet het systeem grote delen van tussenliggende gegevens die meestal veel groter zijn dan de uiteindelijke queryresultaten, serialiseren en overdragen. Het beperkt ook het vermogen van het systeem om optimalisaties uit te voeren, heuristiek en caches te gebruiken.
Als er geen echte reden is om al deze regio's te scannen, moet u het bereik aanpassen zodat het minder regio's bestrijkt. Als het bereik van de resource wordt geminimaliseerd, maar nog steeds veel regio's worden gebruikt, kan dit gebeuren als gevolg van verkeerde configuratie. Controleerlogboeken en diagnostische instellingen worden bijvoorbeeld verzonden naar verschillende werkruimten in verschillende regio's of er zijn meerdere configuratieconfiguraties voor diagnostische instellingen. 

> [!IMPORTANT]
> Wanneer een query in verschillende regio's wordt uitgevoerd, zijn de CPU- en gegevensmetingen niet nauwkeurig en vertegenwoordigen ze de meting alleen op een van de regio's.

## <a name="number-of-workspaces"></a>Aantal werkruimten
Werkruimten zijn logische containers die worden gebruikt om logboekgegevens te scheiden en te beheren. De backend optimaliseert plaatsingen van werkruimten op fysieke clusters binnen het geselecteerde gebied.

Het gebruik van meerdere werkruimten kan het gevolg zijn van: 

- Waar meerdere werkruimten expliciet worden vermeld.
- Wanneer een query met resourcebereik gegevens ophaalt en de gegevens in meerdere werkruimten worden opgeslagen.
 
Voor cross-region en cross-cluster uitvoering van query's moet het systeem grote delen van tussenliggende gegevens die meestal veel groter zijn dan de uiteindelijke queryresultaten, serialiseren en overdragen in de back-end. Het beperkt ook de systeemcapaciteit om optimaliseringen, heuristics uit te voeren en caches te gebruiken.

> [!IMPORTANT]
> In sommige scenario's met meerdere werkruimten zijn de CPU- en gegevensmetingen niet nauwkeurig en vertegenwoordigen ze de meting slechts voor enkele werkruimten.

## <a name="parallelism"></a>Parallellisme
Azure Monitor Logs gebruikt grote clusters van Azure Data Explorer om query's uit te voeren, en deze clusters variëren in schaal, waardoor mogelijk tot tientallen compute nodes worden uitgevoerd. Het systeem schaalt de clusters automatisch op basis van de logica en capaciteit van de plaatsing van de werkruimte.

Om een query efficiënt uit te voeren, wordt deze verdeeld en gedistribueerd naar compute-knooppunten op basis van de gegevens die nodig zijn voor de verwerking ervan. Er zijn situaties waarin het systeem dit niet efficiënt kan doen. Dit kan leiden tot een lange duur van de query. 

Querygedrag dat parallellisme kan verminderen, zijn onder andere:

- Gebruik van serialisatie- en vensterfuncties zoals de [serialisatieoperator](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)en de [rijfuncties.](/azure/kusto/query/rowcumsumfunction) In sommige van deze gevallen kunnen tijdreeksen en gebruikersanalysefuncties worden gebruikt. Inefficiënte serialisatie kan ook gebeuren als de volgende operatoren niet aan het einde van de query worden gebruikt: [bereik](/azure/kusto/query/rangeoperator), [sorteren](/azure/kusto/query/sortoperator), [orde](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    Gebruik van de aggregatiefunctie [dcount()](/azure/kusto/query/dcount-aggfunction) dwingt het systeem tot een centrale kopie van de afzonderlijke waarden. Wanneer de schaal van de gegevens hoog is, u overwegen de optionele parameters van de functie dcount te gebruiken om de nauwkeurigheid te verminderen.
-    In veel gevallen verlaagt de [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) operator het algehele parallellisme. Onderzoek shuffle join als een alternatief wanneer de prestaties problematisch is.
-    In query's met resourcebereik kunnen de RBAC-controles voor uitvoering blijven hangen in situaties waarin er een zeer groot aantal RBAC-toewijzingen is. Dit kan leiden tot langere controles die zouden leiden tot een lager parallellisme. Een query wordt bijvoorbeeld uitgevoerd op een abonnement waarbij er duizenden resources zijn en elke resource veel roltoewijzingen heeft in het resourceniveau, niet op het abonnement of de resourcegroep.
-    Als een query kleine stukjes gegevens verwerkt, is het parallellisme laag omdat het systeem deze niet over veel compute nodes verspreidt.



## <a name="next-steps"></a>Volgende stappen

- [Referentiedocumentatie voor de Kusto-querytaal](/azure/kusto/query/).
