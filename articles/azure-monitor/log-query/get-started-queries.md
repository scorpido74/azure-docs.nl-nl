---
title: Aan de slag met logboek query's in Azure Monitor | Microsoft Docs
description: In dit artikel vindt u een zelf studie voor het schrijven van logboek query's in Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: d9116ba1b43959402223e0cbd1e4f729e053b9b6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894306"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Aan de slag met logboek query's in Azure Monitor


> [!NOTE]
> U moet aan de [slag met Azure Monitor Log Analytics](get-started-portal.md) volt ooien voordat u deze zelf studie voltooit.

> [!NOTE]
> U kunt deze oefening in uw eigen omgeving door lopen als u gegevens verzamelt van ten minste één virtuele machine. Als dat niet het geval is, gebruikt u onze [demo omgeving](https://portal.loganalytics.io/demo), die veel voorbeeld gegevens bevat.


In deze zelf studie leert u hoe u logboek query's schrijft in Azure Monitor. U leert het volgende:

- Query structuur begrijpen
- Query resultaten sorteren
- Query resultaten filteren
- Een tijds bereik opgeven
- Selecteren welke velden moeten worden meegenomen in de resultaten
- Aangepaste velden definiëren en gebruiken
- Resultaten samen voegen en groeperen

Zie [aan de slag met Azure Monitor Log Analytics](get-started-portal.md)voor een zelf studie over het gebruik van log Analytics in het Azure Portal.<br>
Zie [overzicht van logboek query's in azure monitor](log-query-overview.md)voor meer informatie over logboek query's in azure monitor.

## <a name="writing-a-new-query"></a>Een nieuwe query schrijven
Query's kunnen beginnen met een tabel naam of met de *Zoek* opdracht. U moet beginnen met een tabel naam omdat hiermee een duidelijk bereik voor de query wordt gedefinieerd en de query prestaties en relevantie van de resultaten worden verbeterd.

> [!NOTE]
> De Kusto-query taal die door Azure Monitor wordt gebruikt, is hoofdletter gevoelig. Taal trefwoorden worden meestal in kleine letters geschreven. Wanneer u namen van tabellen of kolommen in een query gebruikt, moet u ervoor zorgen dat u de juiste aanvraag gebruikt, zoals wordt weer gegeven in het deel venster schema.

### <a name="table-based-queries"></a>Query's op basis van een tabel
Azure Monitor organiseert logboek gegevens in tabellen, die elk bestaan uit meerdere kolommen. Alle tabellen en kolommen worden weer gegeven in het deel venster schema in Log Analytics in de analyse Portal. Identificeer een tabel waarin u geïnteresseerd bent en bekijk vervolgens een stukje gegevens:

```Kusto
SecurityEvent
| take 10
```

De query die hierboven wordt weer gegeven, retourneert tien resultaten uit de *SecurityEvent* -tabel, in een bepaalde volg orde. Dit is een zeer veelvoorkomende manier om een tabel in een oogopslag te bekijken en inzicht te krijgen in de structuur en inhoud ervan. Laten we eens kijken hoe het is gebouwd:

* De query begint met de tabel naam *SecurityEvent* : dit onderdeel definieert het bereik van de query.
* Het sluis teken (|) scheidt opdrachten, dus de uitvoer van de eerste in de invoer van de volgende opdracht. U kunt elk gewenst aantal elementen in de pipes toevoegen.
* Na de pipe is de opdracht **nemen** , die een specifiek aantal wille keurige records uit de tabel retourneert.

We kunnen de query ook daad werkelijk uitvoeren zonder `| take 10` toe te voegen, die nog steeds geldig is, maar kan resulteren in 10.000 resultaten.

### <a name="search-queries"></a>Zoekquery 's
Zoek query's zijn minder gestructureerd en zijn over het algemeen geschikt voor het zoeken van records die een specifieke waarde bevatten in een van de kolommen:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Met deze query wordt in de *SecurityEvent* -tabel gezocht naar records die de woord groep ' cryptographic ' bevatten. Van deze records worden 10 records geretourneerd en weer gegeven. Als we het `in (SecurityEvent)` gedeelte weglaten en `search "Cryptographic"`gewoon worden uitgevoerd, gaat de zoek opdracht over op *alle* tabellen, wat langer duurt en minder efficiënt is.

> [!WARNING]
> Zoek query's zijn meestal trager dan query's op basis van een tabel, omdat ze meer gegevens moeten verwerken. 

## <a name="sort-and-top"></a>Sorteren en bovenaan
Hoewel het handig is om een aantal records te verkrijgen **, worden de** resultaten geselecteerd en weer gegeven in een bepaalde volg orde. Als u een geordende weer gave wilt krijgen, kunt u **sorteren** op de voorkeurs kolom:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Het kan echter ook te veel resultaten retour neren, maar dit kan enige tijd duren. Met de bovenstaande query wordt *de hele* SecurityEvent-tabel gesorteerd op de kolom TimeGenerated. De analyse Portal beperkt vervolgens de weer gave om alleen 10.000 records weer te geven. Deze benadering is natuurlijk niet optimaal.

De beste manier om alleen de meest recente 10 records op te halen, is het gebruik van **Top**, waarmee de hele tabel op de server wordt gesorteerd en de bovenste records worden geretourneerd:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Aflopend is de standaard sorteer volgorde, waardoor het argument **DESC** doorgaans niet wordt wegge laten. De uitvoer ziet er als volgt uit:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: filteren op een voor waarde
Filters, zoals aangegeven door hun naam, filteren de gegevens op een specifieke voor waarde. Dit is de meest voorkomende manier om de query resultaten te beperken tot relevante informatie.

Als u een filter aan een query wilt toevoegen, gebruikt u de operator **where** gevolgd door een of meer voor waarden. Met de volgende query worden bijvoorbeeld alleen *SecurityEvent* -records geretourneerd waarbij _niveau_ gelijk is aan _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Wanneer u filter voorwaarden schrijft, kunt u de volgende expressies gebruiken:

| Expressie | Beschrijving | Voorbeeld |
|:---|:---|:---|
| == | Gelijkheid controleren<br>(hoofdletter gevoelig) | `Level == 8` |
| =~ | Gelijkheid controleren<br>(niet hoofdletter gevoelig) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| ! =, < > | Ongelijkheid controleren<br>(beide expressies zijn identiek) | `Level != 4` |
| *en*, *of* | Vereist tussen omstandigheden| `Level == 16 or CommandLine != ""` |

Als u wilt filteren op meerdere voor waarden, kunt u **en**gebruiken:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

of pipe meerdere **waar** elementen één na de andere liggen:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Waarden kunnen verschillende typen hebben, dus u moet deze mogelijk converteren om een vergelijking op het juiste type uit te voeren. Zo is de kolom SecurityEvent *niveau* van het type teken reeks, dus moet u deze converteren naar een numeriek type, bijvoorbeeld *int* of *Long*, voordat u numerieke Opera tors kunt gebruiken: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Een tijds bereik opgeven

### <a name="time-picker"></a>Tijd kiezer
De tijd kiezer bevindt zich naast de knop uitvoeren en geeft aan dat er alleen records in de afgelopen 24 uur worden opgevraagd. Dit is het standaard tijd bereik dat wordt toegepast op alle query's. Als u alleen records van het afgelopen uur wilt ophalen, selecteert u _vorig uur_ en voert u de query opnieuw uit.

![Tijdkiezer](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tijd filter in query
U kunt ook uw eigen tijds bereik definiëren door een tijd filter toe te voegen aan de query. Het is het beste om het tijd filter direct na de tabel naam te plaatsen: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

In het bovenstaande filter `ago(30m)` ' 30 minuten geleden ' betekent dat deze query alleen records retourneert van de laatste 30 minuten. Andere tijds eenheden zijn dagen (2D), minuten (25m) en seconden (10).


## <a name="project-and-extend-select-and-compute-columns"></a>Project en uitbreiden: kolommen selecteren en berekenen
**Project** gebruiken om specifieke kolommen te selecteren die in de resultaten moeten worden meegenomen:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

In het vorige voor beeld wordt deze uitvoer gegenereerd:

![Resultaten van query project](media/get-started-queries/project.png)

U kunt **project** ook gebruiken om de namen van kolommen te wijzigen en nieuwe te definiëren. In het volgende voor beeld wordt project gebruikt om het volgende te doen:

* Selecteer alleen de *computer* en de oorspronkelijke *TimeGenerated* -kolommen.
* Wijzig de naam van de kolom *Activity* in *EventDetails*.
* Maak een nieuwe kolom met de naam *Event code*. De functie **subtekenreeks ()** wordt gebruikt om alleen de eerste vier tekens uit het veld activiteit op te halen.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**uitbreiden** houdt alle oorspronkelijke kolommen in de resultatenset bij en definieert aanvullende waarden. De volgende query maakt gebruik van **uitbreiden** om de kolom *Event code* toe te voegen. Houd er rekening mee dat deze kolom niet wordt weer gegeven aan het einde van de tabel, waardoor u de details van een record moet uitvouwen om deze weer te geven.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Samenvatten: statistische groepen rijen
Gebruik **samenvatten** om groepen records te identificeren, volgens een of meer kolommen en pas aggregaties hierop toe. Het meest voorkomende gebruik van **een samen vatting** *is het aantal dat*de resultaten in elke groep retourneert.

De volgende query controleert alle *prestatie* records van het afgelopen uur, gegroepeerd op *object naam*en telt de records in elke groep: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Soms is het zinvol om groepen te definiëren op basis van meerdere dimensies. Elke unieke combi natie van deze waarden definieert een afzonderlijke groep:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Een ander algemeen gebruik is het uitvoeren van wiskundige of statistische berekeningen op elke groep. In het volgende voor beeld wordt de gemiddelde *CounterValue* voor elke computer berekend:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Helaas hebben de resultaten van deze query geen betekenis omdat we verschillende prestatie meter items combi neren. Om dit duidelijker te maken, moeten we het gemiddelde voor elke combi natie van *CounterName* en *computer*berekenen:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Een overzicht van een kolom tijd
Het groeperen van resultaten kan ook worden gebaseerd op een tijd kolom of een andere doorlopende waarde. U hoeft alleen maar een samen vatting te maken van `by TimeGenerated`, hoewel er groepen worden gemaakt voor elke enkele milliseconde gedurende het tijds bereik, omdat dit unieke waarden zijn. 

Als u groepen wilt maken op basis van doorlopende waarden, kunt u het bereik het beste met behulp van **bin**opsplitsen in beheer bare eenheden. De volgende query analyseert de *prestatie* records die het vrije geheugen (*beschik bare Mbytes*) meten op een specifieke computer. De gemiddelde waarde van elke periode van één uur wordt berekend in de afgelopen 7 dagen:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Als u de uitvoer helderder wilt maken, selecteert u deze om weer te geven als een tijd diagram met daarin het beschik bare geheugen in de loop van de tijd:

![Query geheugen na verloop van tijd](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [schrijven van zoek query's](search-queries.md)
