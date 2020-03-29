---
title: Splunk naar Azure Monitor-logboekquery | Microsoft Documenten
description: Help voor gebruikers die bekend zijn met Splunk in het leren van Azure Monitor-logboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397746"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk naar Azure Monitor-logboekquery

Dit artikel is bedoeld om gebruikers die bekend zijn met Splunk te helpen de Kusto-querytaal te leren om logboekquery's te schrijven in Azure Monitor. Er worden directe vergelijkingen gemaakt tussen de twee om de belangrijkste verschillen te begrijpen en ook overeenkomsten waar u uw bestaande kennis benutten.

## <a name="structure-and-concepts"></a>Structuur en concepten

In de volgende tabel worden concepten en gegevensstructuren tussen Splunk- en Azure Monitor-logboeken vergeleken.

 | Concept  | Splunk | Azure Monitor |  Opmerking
 | --- | --- | --- | ---
 | Implementatie-eenheid  | cluster |  cluster |  Azure Monitor maakt willekeurige cross cluster query's mogelijk. Splunk niet. |
 | Gegevenscaches |  Emmers  |  Beleid voor caching en retentie |  Hiermee bepaalt u het niveau van de periode en de caching voor de gegevens. Deze instelling heeft een directe invloed op de prestaties van query's en de kosten van de implementatie. |
 | Logische verdeling van gegevens  |  Index  |  database  |  Maakt een logische scheiding van de gegevens mogelijk. Beide implementaties maken het mogelijk vakbonden en toetreding tot over deze partities. |
 | Metagegevens van gestructureerde gebeurtenissen | N.v.t. | tabel |  Splunk heeft het concept niet blootgesteld aan de zoektaal van gebeurtenismetadata. Azure Monitor-logboeken hebben het concept van een tabel met kolommen. Elke gebeurtenisinstantie wordt toegewezen aan een rij. |
 | Gegevensrecord | Gebeurtenis | Rij |  Terminologie alleen veranderen. |
 | Kenmerk Gegevensrecord | veld |  kolom |  In Azure Monitor wordt dit vooraf gedefinieerd als onderdeel van de tabelstructuur. In Splunk heeft elk evenement zijn eigen velden. |
 | Typen | Datatype |  Datatype |  Azure Monitor-gegevenstypen zijn explicieter als ze zijn ingesteld op de kolommen. Beide hebben de mogelijkheid om dynamisch te werken met gegevenstypen en ongeveer gelijkwaardige set gegevenstypen, waaronder JSON-ondersteuning. |
 | Query's en zoekopdrachten  | zoeken | query |  Concepten zijn in wezen hetzelfde tussen zowel Azure Monitor als Splunk. |
 | Innametijd van gebeurtenissen | Systeemtijd | ingestion_time. |  In Splunk krijgt elke gebeurtenis een systeemtijdstempel van de tijd dat de gebeurtenis is geïndexeerd. In Azure Monitor u een beleid definiëren met de naam ingestion_time dat een systeemkolom blootlegt waarmee kan worden verwezen via de functie ingestion_time(). |

## <a name="functions"></a>Functions

In de volgende tabel worden functies in Azure Monitor opgegeven die gelijkwaardig zijn aan Splunk-functies.

|Splunk | Azure Monitor |Opmerking
|---|---|---
|strcat strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber tonumber | todouble()<br>tolong()<br>toint() | (1) |
|Bovenste<br>Lagere |toupper()<br>tolower()|(1) |
| vervangen | Replace() | (1)<br> Houd er `replace()` ook rekening mee dat, terwijl neemt drie parameters in beide producten, de parameters zijn verschillend. |
| Substr | subtekenreeks() | (1)<br>Merk ook op dat Splunk gebruik maakt van een-gebaseerde indices. Azure Monitor noteert nulgebaseerde indexen. |
| tolower |  tolower() | (1) |
| Toupper | toupper() | (1) |
| Overeenkomen met | komt overeen met regex |  (2)  |
| Regex | komt overeen met regex | In Splunk, `regex` is een operator. In Azure Monitor is het een relationele operator. |
| zoekmatch | == | In Splunk, `searchmatch` maakt het zoeken naar de exacte string.
| willekeurig | rand()<br>rand(n) | De functie van Splunk retourneert een getal van nul naar 2<sup>31</sup>-1. Azure Monitor retourneert een getal tussen 0,0 en 1,0 of als een opgegeven parameter, tussen 0 en n-1.
| nu | now() | (1)
| relative_time | totimespan() | (1)<br>In Azure Monitor is Splunk's equivalent van relative_time(datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Bijvoorbeeld, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> wordt <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) In Splunk wordt de functie `eval` bij de exploitant ingeroepen. In Azure Monitor wordt het `extend` gebruikt `project`als onderdeel van of .<br>(2) In Splunk wordt de functie `eval` bij de exploitant ingeroepen. In Azure Monitor kan het `where` worden gebruikt met de operator.


## <a name="operators"></a>Operators

In de volgende secties worden voorbeelden gegeven van het gebruik van verschillende operatoren tussen Splunk en Azure Monitor.

> [!NOTE]
> Voor het volgende voorbeeld wordt de _veldregel_ van Splunk toegewezen aan een tabel in Azure Monitor en de standaardtijdstempelkaarten van Splunk naar de kolom Logboekanalyse _ingestion_time()._

### <a name="search"></a>Search
In Splunk u `search` het trefwoord weglaten en een niet-geciteerde tekenreeks opgeven. In Azure Monitor moet u `find`elke query starten met een niet-geciteerde tekenreeks is een kolomnaam en moet de opzoekwaarde een geciteerde tekenreeks zijn. 

| |  | |
|:---|:---|:---|
| Splunk | **Zoek** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filteren
Azure Monitor-logboekquery's beginnen vanuit een tabelvormige resultaatset waar het filter is ingesteld. In Splunk is filteren de standaardbewerking op de huidige index. U `where` ook operator gebruiken in Splunk, maar het wordt niet aanbevolen.

| |  | |
|:---|:---|:---|
| Splunk | **Zoek** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **Waar** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>N-gebeurtenissen/rijen krijgen voor inspectie 
Azure Monitor-logboekquery's ondersteunen `take` `limit`ook als alias voor . In Splunk, als de `head` resultaten worden besteld, zal de eerste n resultaten terug. In Azure Monitor wordt limiet niet geordend, maar retourneert de eerste n-rijen die worden gevonden.

| |  | |
|:---|:---|:---|
| Splunk | **Hoofd** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Beperken** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>De eerste n-gebeurtenissen/rijen laten bestellen door een veld/kolom
Voor de onderste resultaten, in `tail`Splunk gebruik je . In Azure Monitor u `asc`de bestelrichting opgeven met.

| |  | |
|:---|:---|:---|
| Splunk | **Hoofd** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>De resultaatset uitbreiden met nieuwe velden/kolommen
Splunk heeft `eval` ook een functie, die niet `eval` vergelijkbaar is met de operator. Zowel `eval` de operator in Splunk als de `extend` operator in Azure Monitor ondersteunen alleen scalaire functies en rekenkundige operatoren.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Uitbreiden** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Naam wijzigen 
Azure Monitor `project-rename` gebruikt de operator om de naam van een veld te wijzigen. `project-rename`hiermee kan de query profiteren van eventuele indexen die vooraf zijn gebouwd voor een veld. Splunk heeft `rename` een operator om hetzelfde te doen.

| |  | |
|:---|:---|:---|
| Splunk | **Hernoemen** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projectnaam** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Resultaten/projectie opmaken
Splunk lijkt geen operator te `project-away`hebben die lijkt op . U de gebruikersinterface gebruiken om velden weg te filteren.

| |  | |
|:---|:---|:---|
| Splunk | **Tabel** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **Project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Aggregatie
Zie de [aggregaties in Azure Monitor-logboekquery's](aggregations.md) voor de verschillende aggregatiefuncties.

| |  | |
|:---|:---|:---|
| Splunk | **Stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Samenvatten** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Koppelen
Doe mee met Splunk heeft aanzienlijke beperkingen. De subquery heeft een limiet van 10000 resultaten (ingesteld in het configuratiebestand voor implementatie) en er is een beperkt aantal join-smaken.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Sorteren
In Splunk moet u de `reverse` operator gebruiken om in oplopende volgorde te sorteren. Azure Monitor ondersteunt ook het definiëren waar nulls te plaatsen, aan het begin of aan het einde.

| |  | |
|:---|:---|:---|
| Splunk | **Sorteren** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **order door** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Uitbreiden met meerdere waarden
Dit is een vergelijkbare operator in zowel Splunk als Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Resultaten facetten, interessante velden
In Log Analytics in de Azure-portal wordt alleen de eerste kolom weergegeven. Alle kolommen zijn beschikbaar via de API.

| |  | |
|:---|:---|:---|
| Splunk | **Velden** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Facetten** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Dupliceren
U kunt `summarize arg_min()` in plaats daarvan de volgorde omkeren van welke record wordt gekozen.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **samenvatten arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Volgende stappen

- Ga door een les over de [schrijflogboekquery's in Azure Monitor](get-started-queries.md).
