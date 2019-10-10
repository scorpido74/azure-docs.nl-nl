---
title: Splunk om de logboek query te Azure Monitor | Microsoft Docs
description: Help voor gebruikers die bekend zijn met Splunk in learning Azure Monitor-logboek query's.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 03a0d755cf6d099f07a7c6d853e1d747908eec05
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177638"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk naar Azure Monitor-logboek query

Dit artikel is bedoeld ter ondersteuning van gebruikers die bekend zijn met Splunk om de Kusto-query taal te leren kennen om logboek query's te schrijven in Azure Monitor. Er worden direct vergelijkingen tussen de twee gemaakt om inzicht te krijgen in de belangrijkste verschillen en overeenkomsten waarbij u uw bestaande kennis optimaal kunt benutten.

## <a name="structure-and-concepts"></a>Structuur en concepten

In de volgende tabel worden de concepten en gegevens structuren tussen Splunk en Azure Monitor-logboeken vergeleken.

 | Concept  | Splunk | Azure Monitor |  Opmerking
 | --- | --- | --- | ---
 | Implementatie-eenheid  | cluster |  cluster |  Azure Monitor kunnen wille keurige query's op meerdere clusters worden uitgevoerd. Splunk niet. |
 | Gegevens caches |  buckets  |  Cache-en bewaar beleid |  Hiermee bepaalt u de periode en het cache niveau voor de gegevens. Deze instelling heeft rechtstreeks gevolgen voor de prestaties van query's en kosten voor de implementatie. |
 | Logische partitie van gegevens  |  TabIndex  |  database  |  Logische schei ding van de gegevens toestaan. Met beide implementaties kunnen vakbonden samen voegen en deel nemen aan deze partities. |
 | Meta gegevens van gestructureerde gebeurtenissen | N/A | Tabel |  Splunk heeft niet het concept dat wordt weer gegeven in de Zoek taal van de meta gegevens van gebeurtenissen. Azure Monitor Logboeken bevat het concept van een tabel, die kolommen bevat. Elk gebeurtenis exemplaar is toegewezen aan een rij. |
 | Gegevens record | gebeurtenislog | rijkoppen |  Alleen terminologie wijzigen. |
 | Gegevens record kenmerk | Aan |  Kolominvoercel |  In Azure Monitor is dit vooraf gedefinieerd als onderdeel van de tabel structuur. In Splunk heeft elke gebeurtenis een eigen set velden. |
 | Dergelijke | param1 |  param1 |  Azure Monitor gegevens typen zijn explicieter, omdat ze zijn ingesteld voor de kolommen. Beide hebben de mogelijkheid om dynamisch te werken met gegevens typen en een ongeveer gelijkwaardige verzameling gegevens sets, waaronder JSON-ondersteuning. |
 | Query's en zoek opdrachten  | Opdracht | query |  Concepten zijn in wezen hetzelfde als die van zowel Azure Monitor als Splunk. |
 | Opname tijd van gebeurtenis | Systeem tijd | ingestion_time() |  In Splunk haalt elke gebeurtenis een systeem tijds tempel van het tijdstip waarop de gebeurtenis is geïndexeerd. In Azure Monitor kunt u een beleid definiëren met de naam ingestion_time dat een systeem kolom beschrijft waarnaar kan worden verwezen via de functie ingestion_time (). |

## <a name="functions"></a>Functies

De volgende tabel bevat functies in Azure Monitor die gelijk zijn aan Splunk-functies.

|Splunk | Azure Monitor |Opmerking
|---|---|---
|strcat | strcat()| i |
|split  | splitsen () | i |
|if     | IFF ()   | i |
|tonumber | todouble()<br>tolong()<br>toint() | i |
|hoofd<br>Onderliggende |toupper()<br>tolower()|i |
| vervangen | replace () | i<br> Houd er ook rekening mee dat de para meters verschillen als `replace()` drie para meters in beide producten heeft. |
| substr | subtekenreeks () | i<br>Houd er ook rekening mee dat Splunk gebruikmaakt van op één gebaseerde indices. Azure Monitor op nul gebaseerde indexen. |
| tolower |  tolower() | i |
| toupper | toupper() | i |
| overeen met | komt overeen met regex |  twee  |
| reguliere | komt overeen met regex | In Splunk is `regex` een operator. In Azure Monitor is dit een relationele operator. |
| searchmatch | == | In Splunk, `searchmatch` kan zoeken naar de exacte teken reeks.
| wille keurige | ASELECT ()<br>ASELECT (n) | De functie Splunk retourneert een getal tussen nul en 2<sup>31</sup>-1. Azure Monitor ' retourneert een getal tussen 0,0 en 1,0, of als een para meter is gegeven, tussen 0 en n-1.
| nu | now() | i
| relative_time | totimespan() | i<br>In Azure Monitor is Splunk equivalent van relative_time (datetimeVal, offsetVal) datetimeVal + totimespan (offsetVal).<br>@No__t-0 wordt bijvoorbeeld <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) in Splunk wordt de functie aangeroepen met de operator `eval`. In Azure Monitor wordt het gebruikt als onderdeel van `extend` of `project`.<br>(2) in Splunk wordt de functie aangeroepen met de operator `eval`. In Azure Monitor kan het worden gebruikt met de operator `where`.


## <a name="operators"></a>Operators

De volgende secties bevatten voor beelden van het gebruik van verschillende Opera tors tussen Splunk en Azure Monitor.

> [!NOTE]
> Voor het doel van het volgende voor beeld wordt de veld _regel_ Splunk toegewezen aan een tabel in azure monitor en de standaard tijds tempel van Splunk wordt toegewezen aan de kolom logs Analytics _ingestion_time ()_ .

### <a name="search"></a>Search
In Splunk kunt u het sleutel woord `search` weglaten en een niet-geciteerde teken reeks opgeven. In Azure Monitor moet u elke query beginnen met `find`, een niet-geciteerde teken reeks is een kolom naam en de zoek waarde moet een teken reeks tussen aanhalings tekens zijn. 

| |  | |
|:---|:---|:---|
| Splunk | **opdracht** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
Azure Monitor logboek query's starten vanuit een tabellaire resultaatset waarin het filter is ingesteld. In Splunk is filteren de standaard bewerking op de huidige index. U kunt ook de operator `where` gebruiken in Splunk, maar dit wordt niet aanbevolen.

| |  | |
|:---|:---|:---|
| Splunk | **opdracht** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **positie** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>N gebeurtenissen/rijen voor inspectie ophalen 
Azure Monitor-logboek query's ondersteunen ook `take` als alias voor `limit`. Als de resultaten worden besteld in Splunk, worden de eerste n resultaten door `head` geretourneerd. In Azure Monitor is de limiet niet besteld, maar worden de eerste n rijen geretourneerd die worden gevonden.

| |  | |
|:---|:---|:---|
| Splunk | **horen** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **ondergrens** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Ophalen van de eerste n gebeurtenissen/rijen die zijn besteld door een veld/kolom
Voor de onderste resultaten gebruikt u in Splunk de `tail`. In Azure Monitor kunt u de bestel richting opgeven met `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **horen** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Boven** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>De resultatenset uitbreiden met nieuwe velden/kolommen
Splunk heeft ook een functie `eval`, die niet vergelijkbaar is met de operator `eval`. Zowel de operator `eval` in Splunk als de operator `extend` in Azure Monitor ondersteunen alleen scalaire functies en reken kundige Opera tors.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **uitbreidbaar** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Naam wijzigen 
Azure Monitor gebruikt de operator `project-rename` om de naam van een veld te wijzigen. met `project-rename` kan de query profiteren van alle indexen die vooraf zijn gebouwd voor een veld. Splunk heeft een operator `rename` om hetzelfde te doen.

| |  | |
|:---|:---|:---|
| Splunk | **domeinnaam** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **project-naam wijzigen** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Resultaten/projectie opmaken
Splunk lijkt geen operator te hebben die vergelijkbaar is met `project-away`. U kunt de gebruikers interface gebruiken om velden uit te filteren.

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-weg** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Aggregatie
Bekijk de [aggregaties in azure monitor logboek query's](aggregations.md) voor de verschillende aggregatie functies.

| |  | |
|:---|:---|:---|
| Splunk | **statistieken** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **samenvatten** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Koppelen
Deelname aan Splunk heeft belang rijke beperkingen. De subquery heeft een limiet van 10000 resultaten (ingesteld in het configuratie bestand voor de implementatie) en er is een beperkt aantal koppelings waarden.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sorteren
In Splunk moet u de operator `reverse` gebruiken om in oplopende volg orde te sorteren. Azure Monitor biedt ook ondersteuning voor het definiëren van het plaatsen van Null-waarden, aan het begin of aan het einde.

| |  | |
|:---|:---|:---|
| Splunk | **acties** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **sorteren op** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Meerdere waarden uitbreiden
Dit is een vergelijk bare operator in zowel Splunk als Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facetten met resultaten, interessante velden
In Log Analytics in de Azure Portal wordt alleen de eerste kolom weer gegeven. Alle kolommen zijn beschikbaar via de API.

| |  | |
|:---|:---|:---|
| Splunk | **bedragvelden** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **facetten** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>De-duplicaat
U kunt in plaats daarvan `summarize arg_min()` gebruiken om de volg orde van de gekozen record te herstellen.

| |  | |
|:---|:---|:---|
| Splunk | **ontdubbeling** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **samenvatten arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Volgende stappen

- Door loop een les over de [query's in het schrijf logboek in azure monitor](get-started-queries.md).
