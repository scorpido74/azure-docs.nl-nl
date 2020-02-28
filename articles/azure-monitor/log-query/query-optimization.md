---
title: Logboek query's in Azure Monitor optimaliseren
description: Aanbevolen procedures voor het optimaliseren van logboek query's in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2019
ms.openlocfilehash: 19b0ce154fc19015f7faa17e339c9df259206365
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670811"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Logboek query's in Azure Monitor optimaliseren
Azure Monitor logboeken maakt gebruik van [Azure Data Explorer (ADX)](/azure/data-explorer/) om logboek gegevens op te slaan en query's uit te voeren voor het analyseren van die gegevens. Het maakt, beheert en onderhoudt de ADX-clusters en optimaliseert deze voor de werk belasting van uw logboek analyse. Wanneer u een query uitvoert, wordt deze geoptimaliseerd en doorgestuurd naar het juiste ADX-cluster waarin de werkruimte gegevens worden opgeslagen. Zowel Azure Monitor-Logboeken als Azure Data Explorer maakt gebruik van veel automatische optimalisatie mechanismen voor query's. Automatische optimalisaties bieden een aanzienlijke Boost, maar in sommige gevallen kunt u de query prestaties aanzienlijk verbeteren. In dit artikel worden de prestatie overwegingen en verschillende technieken uitgelegd om ze op te lossen.

De meeste technieken zijn gebruikelijk voor query's die rechtstreeks worden uitgevoerd op Azure Data Explorer en Azure Monitor-logboeken, maar er zijn verschillende unieke overwegingen voor Azure Monitor logboeken die hier worden beschreven. Zie voor meer tips voor Azure Data Explorer optimalisatie de [Aanbevolen procedures voor query's](/azure/kusto/query/best-practices).

Geoptimaliseerde query's:

- Voer sneller uit, verminder de totale duur van de uitvoering van de query.
- Hebben een kleinere kans om te beperken of te worden afgewezen.

U moet in het bijzonder aandacht schenken aan query's die worden gebruikt voor recurrente en burstie, zoals Dash boards, waarschuwingen, Logic Apps en Power BI. De impact van een ingrijpende query in deze gevallen is aanzienlijk.

## <a name="query-performance-pane"></a>Het deel venster query prestaties
Nadat u een query in Log Analytics hebt uitgevoerd, klikt u op de pijl-omlaag boven de query resultaten om het deel venster query prestaties weer te geven met de resultaten van verschillende prestatie-indica toren voor de query. Deze prestatie-indica toren worden beschreven in de volgende sectie.

![Het deel venster query prestaties](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Query prestatie-indica toren

De volgende query prestatie-indica toren zijn beschikbaar voor elke query die wordt uitgevoerd:

- [Totale CPU](#total-cpu): de totale reken kracht die wordt gebruikt om de query op alle reken knooppunten te verwerken. Dit is de tijd die wordt gebruikt voor het berekenen van computers, parseren en het ophalen van gegevens. 

- [Gegevens die worden gebruikt voor verwerkte query's](#data-used-for-processed-query): algemene gegevens die zijn geopend voor het verwerken van de query. Beïnvloed door de grootte van de doel tabel, gebruikte tijds periode, toegepaste filters en het aantal kolommen waarnaar wordt verwezen.

- [Tijds panne van de verwerkte query](#time-span-of-the-processed-query): het gat tussen de nieuwste en oudste gegevens die zijn gebruikt voor het verwerken van de query. Beïnvloed door het expliciete tijds bereik dat is opgegeven voor de query.

- [Ouderdom van verwerkte gegevens](#age-of-processed-data): het gat tussen nu en de oudste gegevens die zijn gebruikt voor het verwerken van de query. Dit is van invloed op de efficiëntie van het ophalen van gegevens.

- [Aantal werk ruimten](#number-of-workspaces): hoeveel werk ruimten tijdens de query verwerking zijn geopend vanwege impliciete of expliciete selectie.

- [Aantal regio's](#number-of-regions): hoeveel regio's zijn geopend tijdens de verwerking van query's op basis van impliciete of expliciete selectie van werk ruimten. Query's in meerdere regio's zijn veel minder efficiënte en prestatie indicatoren die deel uitmaken van de dekking.

- [Parallellisme](#parallelism): Hiermee wordt aangegeven hoeveel het systeem kan deze query op meerdere knoop punten kan uitvoeren. Alleen relevant voor query's met een hoog CPU-verbruik. Beïnvloed door het gebruik van specifieke functies en Opera tors.


## <a name="total-cpu"></a>Totale CPU
De werkelijke Compute-CPU die is belegd om deze query te verwerken op alle knoop punten in de query verwerking. Aangezien de meeste query's worden uitgevoerd op een groot aantal knoop punten, is dit doorgaans veel groter dan de duur die de query daad werkelijk heeft uitgevoerd. 

De verwerkings tijd van de query wordt uitgegeven op:
- Het ophalen van gegevens: het ophalen van oude gegevens zal meer tijd in beslag nemen dan het ophalen van recente gegevens.
- Gegevens verwerking: logica en evaluatie van de gegevens. 

In plaats van de tijd die wordt besteed aan de query verwerkings knooppunten, is er extra tijd nodig voor Azure Monitor logboeken: verificatie van de gebruiker en controleren of ze toegang krijgen tot deze gegevens, het gegevens archief zoeken, de query parseren en de query verwerking toewijzen punt. Deze tijd is niet opgenomen in de query totale CPU-tijd.

Sommige query opdrachten en-functies zijn in hun CPU-verbruik zwaar. Dit geldt met name voor opdrachten voor het parseren van JSON en XML of het extra heren van complexe reguliere expressies. Een dergelijke parsering kan expliciet plaatsvinden via [parse_json ()](/azure/kusto/query/parsejsonfunction) of [parse_xml ()](/azure/kusto/query/parse-xmlfunction) functions of impliciet wanneer naar dynamische kolommen wordt verwezen.

Deze functies verbruiken CPU in verhouding tot het aantal rijen dat wordt verwerkt. De meest efficiënte optimalisatie is het toevoegen van de voor waarden die zich voordoen in de query, waarmee zoveel mogelijk records kunnen worden gefilterd voordat de CPU-intensieve functie wordt uitgevoerd.

De volgende query's produceren bijvoorbeeld precies hetzelfde resultaat, maar de tweede is op de meest efficiënte manier als [voor waarde voor]() het parseren van veel records uitsluiten:

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
```

Query's die [where](/azure/kusto/query/whereoperator) -componenten bevatten in een geëvalueerde kolom in plaats van kolommen die fysiek aanwezig zijn in de gegevensset, verliezen de efficiëntie. Door te filteren op geëvalueerde kolommen voor komt u dat systeem optimaliseringen wanneer grote gegevens sets worden verwerkt.
De volgende query's produceren bijvoorbeeld precies hetzelfde resultaat, maar de tweede is efficiënter naarmate de [where](/azure/kusto/query/whereoperator) -voor waarde naar de ingebouwde kolom verwijst

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

Hoewel sommige aggregatie opdrachten zoals [Max ()](/azure/kusto/query/max-aggfunction), [Sum ()](/azure/kusto/query/sum-aggfunction), [Count (](/azure/kusto/query/count-aggfunction)) en [AVG ()](/azure/kusto/query/avg-aggfunction) een laag CPU-effect hebben vanwege hun logica, zijn andere complexer, en zijn er heuristische en schattingen opgenomen die ervoor zorgen dat ze efficiënt kunnen worden uitgevoerd. Zo maakt [DCount ()](/azure/kusto/query/dcount-aggfunction) gebruik van het HyperLogLog-algoritme om een nauw keurigheid te bieden voor het aantal grote sets gegevens, zonder dat elke waarde daad werkelijk wordt geteld. de percentiel functies voeren vergelijk bare benaderingen uit met behulp van het dichtstbijzijnde rang percentiel-algoritme. Enkele van de opdrachten bevatten optionele para meters om de impact te verminderen. De functie [makenset ()](/azure/kusto/query/makeset-aggfunction) heeft bijvoorbeeld een optionele para meter voor het definiëren van de maximale set grootte, die aanzienlijk van invloed is op de CPU en het geheugen.

Opdrachten voor [samen voegen](/azure/kusto/query/joinoperator?pivots=azuremonitor) en [samen vattingen](/azure/kusto/query/summarizeoperator) kunnen een hoog CPU-gebruik veroorzaken wanneer ze een grote set gegevens verwerken. Hun complexiteit is rechtstreeks gerelateerd aan het aantal mogelijke waarden, aangeduid als *kardinaliteit*, van de kolommen die worden gebruikt als de `by` in samenvatten of als de Joineigenschappen. Raadpleeg de documentatie artikelen en optimaliserings tips voor uitleg en Optima Lise ring van deelname en samenvatten.

De volgende query's produceren bijvoorbeeld precies hetzelfde resultaat omdat **CounterPath** altijd een-op-een is toegewezen aan **CounterName** en **object naam**. De tweede is efficiënter naarmate de aggregatie dimensie kleiner is:

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

Het CPU-verbruik kan ook worden beïnvloed door where-voor waarden of uitgebreide kolommen waarvoor intensieve computing nodig is. Alle triviale teken reeks vergelijkingen zoals [gelijk aan = =](/azure/kusto/query/datatypes-string-operators) en [startsWith](/azure/kusto/query/datatypes-string-operators) hebben ongeveer dezelfde CPU-impact, terwijl geavanceerde tekst overeenkomsten meer impact hebben. In het bijzonder is de operator [heeft](/azure/kusto/query/datatypes-string-operators) efficiënter dat de operator [contains](/azure/kusto/query/datatypes-string-operators) . Vanwege technieken voor het afhandelen van reeksen is het efficiënter om te zoeken naar teken reeksen die langer zijn dan vier tekens dan korte teken reeksen.

De volgende query's produceren bijvoorbeeld vergelijk bare resultaten, afhankelijk van het computer naamgevings beleid, maar de tweede is efficiënter:

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
> Deze indicator presenteert alleen CPU van het directe cluster. In een query met meerdere regio's zou het slechts een van de regio's vertegenwoordigen. In een query met meerdere werk ruimten zijn mogelijk niet alle werk ruimten inbegrepen.


## <a name="data-used-for-processed-query"></a>Gegevens die worden gebruikt voor verwerkte query's

Een kritieke factor bij het verwerken van de query is het volume van de gegevens die worden gescand en gebruikt voor de verwerking van query's. Azure Data Explorer maakt gebruik van agressieve optimalisaties die het gegevens volume aanzienlijk reduceren ten opzichte van andere gegevens platforms. Toch zijn er kritieke factoren in de query die van invloed kunnen zijn op het gebruikte gegevens volume.
In Azure Monitor-Logboeken wordt de kolom **TimeGenerated** gebruikt als een manier om de gegevens te indexeren. Het beperken van de waarden van de **TimeGenerated** om een bereik zo klein mogelijk te maken, vormen een aanzienlijke verbetering van de prestaties van query's door de hoeveelheid gegevens die moet worden verwerkt aanzienlijk te beperken.

Een andere factor die de verwerking van de gegevens toeneemt, is het gebruik van een groot aantal tabellen. Dit gebeurt meestal wanneer `search *` en `union *` opdrachten worden gebruikt. Met deze opdrachten wordt het systeem gedwongen om gegevens uit alle tabellen in de werk ruimte te evalueren en te scannen. In sommige gevallen kunnen er honderden tabellen in de werk ruimte zijn. Probeer zo veel mogelijk te voor komen met behulp van ' Search * ' of een zoek opdracht zonder het bereik te bereiken voor een specifieke tabel.

De volgende query's produceren bijvoorbeeld precies hetzelfde resultaat, maar de laatste is de meest efficiënte:

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

Een andere methode om het gegevens volume te verminderen, is om de [voor waarden van](/azure/kusto/query/whereoperator) de query te vervroegen. Het Azure Data Explorer-platform bevat een cache waarmee u kunt zien welke partities gegevens bevatten die relevant zijn voor een specifieke where-voor waarde. Als een query bijvoorbeeld `where EventID == 4624` bevat, zou de query alleen worden gedistribueerd naar knoop punten die partities met overeenkomende gebeurtenissen verwerken.

De volgende voorbeeld query's produceren precies hetzelfde resultaat, maar de tweede is efficiënter:

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

Omdat Azure Data Explorer een gegevens archief in kolommen is, is het ophalen van elke kolom onafhankelijk van de andere. Het aantal kolommen dat rechtstreeks wordt opgehaald, is van invloed op het algehele gegevens volume. U moet alleen de kolommen in de uitvoer insluiten die nodig zijn door de resultaten te [samenvatten](/azure/kusto/query/summarizeoperator) of de specifieke kolommen te [projecteren](/azure/kusto/query/projectoperator) . Azure Data Explorer heeft verschillende optimalisaties om het aantal opgehaalde kolommen te verminderen. Als wordt vastgesteld dat een kolom niet nodig is, bijvoorbeeld als er niet in de [samenvattings](/azure/kusto/query/summarizeoperator) opdracht naar wordt verwezen, wordt deze niet opgehaald.

Met de tweede query kan bijvoorbeeld drie keer meer gegevens worden verwerkt, omdat het niet mogelijk is om één kolom op te halen, maar drie:

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

## <a name="time-span-of-the-processed-query"></a>Tijds panne van de verwerkte query

Alle logboeken in Azure Monitor-logboeken worden gepartitioneerd op basis van de kolom **TimeGenerated** . Het aantal geopende partities is rechtstreeks gerelateerd aan de tijds Panne. Het verminderen van het tijds bereik is de meest efficiënte manier om de uitvoering van een prompt query's te voor gaan.

Het tijds bereik kan worden ingesteld met behulp van de tijds bereik kiezer in het Log Analytics scherm, zoals wordt beschreven in het [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](scope.md#time-range). Dit is de aanbevolen methode als het geselecteerde tijds bereik wordt door gegeven aan de back-end met behulp van de meta gegevens van de query. 

Een alternatieve methode is het expliciet toevoegen van een [where](/azure/kusto/query/whereoperator) -voor waarde voor **TimeGenerated** in de query. U moet deze methode gebruiken om er zeker van te zijn dat de tijds Panne vast is, zelfs wanneer de query wordt gebruikt vanuit een andere interface.
Zorg ervoor dat alle onderdelen van de query **TimeGenerated** filters hebben. Wanneer een query subquery's heeft om gegevens op te halen uit verschillende tabellen of dezelfde tabel, moet elk een eigen [where](/azure/kusto/query/whereoperator) -voor waarde bevatten.

Zo wordt in de volgende query, terwijl de **prestatie** tabel alleen voor de laatste dag wordt gescand, de **heartbeat** -tabel gescand voor alle bijbehorende geschiedenis. Dit kan Maxi maal twee jaar duren:

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

Een veelvoorkomende situatie waarbij een dergelijke fout optreedt wanneer [arg_max ()](/azure/kusto/query/arg-max-aggfunction) wordt gebruikt om het meest recente exemplaar te vinden. Bijvoorbeeld:

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

Dit kan eenvoudig worden opgelost door een tijd filter toe te voegen aan de binnenste query:

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

> [!IMPORTANT]
> Deze indicator is niet beschikbaar voor query's voor meerdere regio's.

## <a name="age-of-processed-data"></a>Ouderdom van verwerkte gegevens
Azure Data Explorer maakt gebruik van verschillende opslag lagen: lokale SSD-schijven in het geheugen en veel langzamere Azure-blobs. De nieuwere gegevens, hoe hoger is de kans dat deze wordt opgeslagen in een meer krachtige laag met een kleinere latentie, waardoor de duur en CPU van de query worden verminderd. Met uitzonde ring van de gegevens zelf, heeft het systeem ook een cache voor meta gegevens. De oudere gegevens, de mindere meta gegevens worden in de cache opgeslagen.

Hoewel voor sommige query's gebruik van oude gegevens vereist is, zijn er gevallen waarin oude gegevens per ongeluk worden gebruikt. Dit gebeurt wanneer query's worden uitgevoerd zonder tijds bereik in de meta gegevens op te geven en niet alle tabel verwijzingen filter bevatten in de kolom **TimeGenerated** . In deze gevallen scant het systeem alle gegevens die in die tabel zijn opgeslagen. Wanneer de gegevens retentie lang is, kan deze lange tijd bereiken en dus gegevens die net zo oud zijn als de Bewaar periode van de gegevens bedekken.

Dergelijke gevallen kunnen bijvoorbeeld:

- Het tijds bereik in Log Analytics niet instellen met een subquery die niet is beperkt. Zie bovenstaand voor beeld.
- Het gebruik van de API zonder de optionele para meters van het tijds bereik.
- Het gebruik van een client die geen tijds bereik afdwingt zoals de Power BI-connector.

Zie de voor beelden en opmerkingen in het gedeelte eerdere, zoals ze in dit geval ook relevant zijn.

## <a name="number-of-regions"></a>Aantal regio's
Er zijn verschillende situaties waarin één query kan worden uitgevoerd in verschillende regio's:

- Wanneer verschillende werk ruimten expliciet worden weer gegeven en deze zich in verschillende regio's bevinden.
- Wanneer een query in een resource bereik gegevens ophaalt en de gegevens worden opgeslagen in meerdere werk ruimten die zich in verschillende regio's bevinden.

Voor het uitvoeren van query's voor meerdere regio's moet het systeem serialisatie en overdracht in de back-end grote segmenten van tussenliggende gegevens hebben die meestal veel groter zijn dan de eind resultaten van de query. Het beperkt ook de mogelijkheid van het systeem voor het uitvoeren van optimalisaties, heuristiek en het gebruik van caches.
Als er geen echte reden is om al deze regio's te scannen, moet u het bereik aanpassen zodat het minder regio's beslaat. Als het bron bereik is geminimaliseerd, maar er nog steeds veel regio's worden gebruikt, kan dit worden veroorzaakt door een onjuiste configuratie. Audit logboeken en diagnostische instellingen worden bijvoorbeeld verzonden naar verschillende werk ruimten in verschillende regio's of er zijn meerdere configuraties voor Diagnostische instellingen. 

> [!IMPORTANT]
> Deze indicator is niet beschikbaar voor query's voor meerdere regio's.

## <a name="number-of-workspaces"></a>Aantal werk ruimten
Werk ruimten zijn logische containers die worden gebruikt om logboek gegevens te scheiden en te beheren. De back-end optimaliseert de locatie van de werk ruimte op fysieke clusters in de geselecteerde regio.

Het gebruik van meerdere werk ruimten kan voortkomen uit: 

- Waar verschillende werk ruimten expliciet worden weer gegeven.
- Wanneer een query in een resource bereik gegevens ophaalt en de gegevens worden opgeslagen in meerdere werk ruimten.
 
Voor het uitvoeren van query's in meerdere regio's en meerdere clusters moet het systeem een serialisatie hebben en overzetten in de back-end grote segmenten van tussenliggende gegevens die doorgaans veel groter zijn dan de eind resultaten van de query. Daarnaast beperkt het de systeem capaciteit om optimalisaties, heuristiek en het gebruik van caches uit te voeren.

> [!IMPORTANT]
> In sommige scenario's met meerdere werk ruimten zijn de CPU-en gegevens metingen niet nauw keurig en wordt de meting alleen voor een aantal werk ruimten weer gegeven.

## <a name="parallelism"></a>Parallelle uitvoering
Azure Monitor logboeken maakt gebruik van grote clusters van Azure Data Explorer om query's uit te voeren en deze clusters variëren op schaal. Het systeem schaalt automatisch de clusters volgens de locatie logica en capaciteit van de werk ruimte.

Om een query efficiënt uit te voeren, wordt deze gepartitioneerd en gedistribueerd naar Compute-knoop punten op basis van de gegevens die nodig zijn voor de verwerking ervan. Er zijn enkele situaties waarin het systeem dit niet efficiënt kan doen. Dit kan leiden tot een lange duur van de query. 

Query gedrag dat de parallelle uitvoering kan verminderen, omvat:

- Het gebruik van serialisatie en venster functies, zoals de [operator serialize](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [vorig ()](/azure/kusto/query/prevfunction)en [Row](/azure/kusto/query/rowcumsumfunction) . De functies time series en gebruikers analyse kunnen in enkele van deze gevallen worden gebruikt. Er kunnen ook inefficiënte serialisatie optreden als de volgende Opera tors niet aan het einde van de query worden gebruikt: [Range](/azure/kusto/query/rangeoperator), [Sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-   Gebruik van de aggregatie functie van [DCount ()](/azure/kusto/query/dcount-aggfunction) dwingt het systeem een centrale kopie van de afzonderlijke waarden te hebben. Wanneer de schaal van de gegevens hoog is, kunt u de optionele para meters van de functie DCount gebruiken om de nauw keurigheid te verlagen.
-   In veel gevallen verlaagt de operator voor [samen voegen](/azure/kusto/query/joinoperator?pivots=azuremonitor) de algehele parallelle uitvoering. Onderzoek wille keurige volg orde als alternatief wanneer de prestaties problemen veroorzaken.
-   In het geval van query's in een resource bereik kunnen de RBAC-controles voorafgaand aan uitvoering wellicht de voor keur hebben in situaties waarin er sprake is van een zeer groot aantal RBAC-toewijzingen. Dit kan leiden tot langere controles die resulteren in een lagere parallelle uitvoering. Bijvoorbeeld: er wordt een query uitgevoerd op een abonnement met duizenden resources en elke resource heeft veel roltoewijzingen op het niveau van de resource, niet op het abonnement of de resource groep.
-   Als een query kleine delen van gegevens verwerkt, is de parallelle kracht laag omdat het systeem deze niet verspreidt over veel reken knooppunten.



## <a name="next-steps"></a>Volgende stappen

- [Referentie documentatie voor de Kusto-query taal](/azure/kusto/query/).
