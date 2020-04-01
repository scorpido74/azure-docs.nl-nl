---
title: Aan de slag met logboekquery's in Azure Monitor | Microsoft Documenten
description: In dit artikel vindt u een zelfstudie voor het schrijven van logboekquery's in Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670176"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Aan de slag met logboekquery's in Azure Monitor

> [!NOTE]
> U deze oefening in uw eigen omgeving doorlopen als u gegevens verzamelt van ten minste één virtuele machine. Zo niet, gebruik dan onze [Demo-omgeving,](https://portal.loganalytics.io/demo)die veel voorbeeldgegevens bevat.

In deze zelfstudie leert u logquery's schrijven in Azure Monitor. Het zal je leren hoe je:

- Querystructuur begrijpen
- Queryresultaten sorteren
- Queryresultaten filteren
- Een tijdsbereik opgeven
- Selecteren welke velden u wilt opnemen in de resultaten
- Aangepaste velden definiëren en gebruiken
- Geaggregeerde en groepsresultaten

Zie Aan de slag met Azure Monitor Log Analytics voor een zelfstudie over het gebruik van Loganalytics in de [Azure-portal.](get-started-portal.md)<br>
Zie [Overzicht van logboekquery's in Azure Monitor](log-query-overview.md)voor meer informatie over logboekquery's in Azure Monitor .

Volg samen met een videoversie van deze tutorial hieronder:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Een nieuwe query schrijven
Query's kunnen beginnen met een tabelnaam of de *zoekopdracht.* U moet beginnen met een tabelnaam, omdat hiermee een duidelijk bereik voor de query wordt gedefinieerd en zowel de queryprestaties als de relevantie van de resultaten worden verbeterd.

> [!NOTE]
> De Kusto-querytaal die door Azure Monitor wordt gebruikt, is hoofdlettergevoelig. Trefwoorden in de taal worden over met kleine letters geschreven. Wanneer u namen van tabellen of kolommen in een query gebruikt, moet u de juiste aanvraag gebruiken, zoals in het schemavenster wordt weergegeven.

### <a name="table-based-queries"></a>Query's op basis van tabel
Azure Monitor organiseert logboekgegevens in tabellen, elk samengesteld uit meerdere kolommen. Alle tabellen en kolommen worden weergegeven in het schemavenster in Log Analytics in de Analytics-portal. Identificeer een tabel waarin u geïnteresseerd bent en bekijk vervolgens een aantal gegevens:

```Kusto
SecurityEvent
| take 10
```

De bovenstaande query retourneert 10 resultaten uit de tabel *SecurityEvent,* in geen specifieke volgorde. Dit is een veel voorkomende manier om een blik op een tafel te werpen en de structuur en inhoud ervan te begrijpen. Laten we eens kijken hoe het is gebouwd:

* De query begint met de tabelnaam *SecurityEvent* - dit deel definieert het bereik van de query.
* Het pipe (|) teken scheidt opdrachten, zodat de uitvoer van de eerste in de invoer van de volgende opdracht. U een willekeurig aantal pipeteelementen toevoegen.
* Het volgen van de pijp is de **take** command, die een specifiek aantal willekeurige records uit de tabel retourneert.

We zouden de query zelfs `| take 10` zonder toevoeging kunnen uitvoeren - dat zou nog steeds geldig zijn, maar het kan tot 10.000 resultaten opleveren.

### <a name="search-queries"></a>Zoekquery 's
Zoekopdrachten zijn minder gestructureerd en over het algemeen meer geschikt voor het vinden van records die een specifieke waarde bevatten in een van hun kolommen:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Met deze query wordt in de *tabel SecurityEvent* gezocht naar records die de zinsnede 'Cryptographic' bevatten. Van deze records worden 10 records geretourneerd en weergegeven. Als we het `in (SecurityEvent)` onderdeel weglaten en gewoon uitvoeren, `search "Cryptographic"`zal de zoekopdracht gaan over *alle* tabellen, die langer zou duren en minder efficiënt.

> [!WARNING]
> Zoekopdrachten zijn doorgaans trager dan query's in tabelen omdat ze meer gegevens moeten verwerken. 

## <a name="sort-and-top"></a>Sorteren en boven
Hoewel **take** handig is om een paar records te krijgen, worden de resultaten geselecteerd en weergegeven in willekeurige volgorde. Als u een geordende weergave wilt krijgen, u **sorteren** op de voorkeurskolom:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Dat kan echter te veel resultaten opleveren en kan ook enige tijd duren. De bovenstaande query sorteert *de volledige* SecurityEvent-tabel op de kolom TimeGenerated. De Analytics-portal beperkt het scherm vervolgens tot slechts 10.000 records. Deze aanpak is natuurlijk niet optimaal.

De beste manier om alleen de laatste 10 records te krijgen is het gebruik **van de top,** die de hele tabel sorteert op de server kant en vervolgens de top records retourneert:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Aflopend is de standaardsorteervolgorde, dus we laten meestal het **argument desc** weg. De uitvoer ziet er als volgt uit:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Waar: filteren op voorwaarde
Filters, zoals aangegeven door hun naam, filteren de gegevens op een specifieke voorwaarde. Dit is de meest voorkomende manier om queryresultaten te beperken tot relevante informatie.

Als u een filter aan een query wilt toevoegen, gebruikt u de **operator waar** een of meer voorwaarden zijn gevolgd. De volgende query retourneert bijvoorbeeld alleen *SecurityEvent-records* waarbij _niveau_ gelijk is aan _8:_

```Kusto
SecurityEvent
| where Level == 8
```

Bij het schrijven van filtervoorwaarden u de volgende expressies gebruiken:

| Expressie | Beschrijving | Voorbeeld |
|:---|:---|:---|
| == | Controleer de gelijkheid<br>(hoofdlettergevoelig) | `Level == 8` |
| =~ | Controleer de gelijkheid<br>(case-ongevoelig) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Controleer ongelijkheid<br>(beide uitdrukkingen zijn identiek) | `Level != 4` |
| *en*, *of* | Vereist tussen de voorwaarden| `Level == 16 or CommandLine != ""` |

Als u op meerdere voorwaarden wilt filteren, u gebruik maken **van:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

of pijp meerdere **waar** elementen een na de ander:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Waarden kunnen verschillende typen hebben, dus u moet ze mogelijk casten om een vergelijking op het juiste type uit te voeren. De kolom SecurityEvent *Level* is bijvoorbeeld van type Tekenreeks, dus u moet deze naar een numeriek type casten, zoals *int* of *lang,* voordat u numerieke operatoren erop gebruiken:`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Een tijdsbereik opgeven

### <a name="time-picker"></a>Tijdkiezer
De tijdkiezer bevindt zich naast de knop Uitvoeren en geeft aan dat we alleen records van de afgelopen 24 uur opvragen. Dit is het standaardtijdbereik dat op alle query's wordt toegepast. Als u alleen records van het laatste uur wilt ophalen, selecteert u _Last hour_ en voert u de query opnieuw uit.

![Tijdkiezer](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tijdfilter in query
U ook uw eigen tijdsbereik definiëren door een tijdfilter aan de query toe te voegen. U het tijdfilter het beste direct na de tabelnaam plaatsen: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

In de bovenstaande `ago(30m)` tijd filter betekent "30 minuten geleden" dus deze query alleen records retourneert van de laatste 30 minuten. Andere tijdseenheden zijn dagen (2d), minuten (25m) en seconden (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Project en uitbreiden: kolommen selecteren en berekenen
Gebruik **project** om specifieke kolommen te selecteren die in de resultaten worden opgenomen:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Het voorgaande voorbeeld genereert deze uitvoer:

![Projectresultaten van query's](media/get-started-queries/project.png)

U **project** ook gebruiken om kolommen een andere naam te geven en nieuwe kolommen te definiëren. In het volgende voorbeeld wordt het volgende project gebruikt om het volgende te doen:

* Selecteer alleen de originele kolommen *Computer* en *TimeGenerated.*
* Wijzig de naam van de kolom *Activiteit* in *EventDetails*.
* Een nieuwe kolom met de naam *EventCode maken*. De functie **substring()** wordt gebruikt om alleen de eerste vier tekens uit het veld Activiteit te krijgen.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** houdt alle originele kolommen in de resultaatset en definieert extra kolommen. De volgende query wordt **gebruikt om** de kolom *EventCode* toe te voegen. Houd er rekening mee dat deze kolom mogelijk niet wordt weergegeven aan het einde van de tabelresultaten, in welk geval u de details van een record moet uitbreiden om deze te bekijken.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Samenvatten: groepen rijen samenvoegen
Gebruik **samenvatten** om groepen records te identificeren, op basis van een of meer kolommen, en voeg aggregaties toe op deze records. Het meest voorkomende gebruik van **samenvatten** is *tellen*, wat het aantal resultaten in elke groep retourneert.

In de volgende query worden alle *Perf-records* van het laatste uur beoordeeld, worden deze gegroepeerd op *ObjectName*en worden de records in elke groep geteld: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Soms is het zinvol om groepen te definiëren op meerdere dimensies. Elke unieke combinatie van deze waarden definieert een afzonderlijke groep:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Een ander gemeenschappelijk gebruik is het uitvoeren van wiskundige of statistische berekeningen op elke groep. Het volgende berekent bijvoorbeeld de gemiddelde *tegenwaarde* voor elke computer:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Helaas zijn de resultaten van deze query zinloos omdat we verschillende prestatietellers hebben gemengd. Om dit zinvoller te maken, moeten we het gemiddelde afzonderlijk berekenen voor elke combinatie van *CounterName* en *Computer:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Samenvatten op basis van een tijdkolom
Het groeperen van resultaten kan ook worden gebaseerd op een tijdkolom of een andere doorlopende waarde. Gewoon samenvatten `by TimeGenerated` zou echter groepen maken voor elke milliseconde over het tijdsbereik, omdat dit unieke waarden zijn. 

Als u groepen wilt maken op basis van doorlopende waarden, u het bereik het beste opsplitsen in beheerbare eenheden met behulp van **opslaglocatie.** De volgende query analyseert *Perf-records* die het vrije geheugen *(Beschikbare MBytes)* op een specifieke computer meten. Het berekent de gemiddelde waarde van elke periode van 1 uur over de laatste 7 dagen:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Als u de uitvoer duidelijker wilt maken, selecteert u deze weer te geven als een tijdgrafiek met het beschikbare geheugen in de loop van de tijd:

![Querygeheugen na verloop van tijd](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van tekenreeksgegevens in een logboekquery met [Tekenreeksen in Azure Monitor-logboekquery's](string-operations.md).
- Meer informatie over het aggregeren van gegevens in een logboekquery met [geavanceerde aggregaties in Azure Monitor-logboekquery's](advanced-aggregations.md).
- Meer informatie over het deelnemen aan gegevens uit meerdere tabellen met [joins in Azure Monitor-logboekquery's](joins.md).
- Documentatie over de volledige Kusto-querytaal in de [KQL-taalverwijzing.](/azure/kusto/query/)
