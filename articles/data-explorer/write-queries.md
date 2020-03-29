---
title: Query's schrijven voor Azure Data Explorer
description: In deze how-to leert u hoe u basisquery's en meer geavanceerde query's voor Azure Data Explorer uitvoeren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881184"
---
# <a name="write-queries-for-azure-data-explorer"></a>Query's schrijven voor Azure Data Explorer

In dit artikel leert u hoe u de querytaal in Azure Data Explorer gebruiken om basisquery's uit te voeren met de meest voorkomende operatoren. U krijgt ook blootstelling aan enkele van de meer geavanceerde functies van de taal.

## <a name="prerequisites"></a>Vereisten

U de query's in dit artikel op twee manieren uitvoeren:

- In het Azure Data *Explorer-helpcluster* dat we hebben opgezet om het leren te helpen.
    [Meld u aan bij het cluster](https://dataexplorer.azure.com/clusters/help/databases/samples) met een organisatie-e-mailaccount dat lid is van de Azure Active Directory.

- Op uw eigen cluster met de voorbeeldgegevens van StormEvents. Zie [Snelstart: Een Azure Data Explorer-cluster en -database maken](create-cluster-database-portal.md) en [voorbeeldgegevens opnemen in Azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Overzicht van de querytaal

Een querytaal in Azure Data Explorer is een alleen-lezen verzoek om gegevens te verwerken en resultaten te retourneren. De aanvraag wordt in platte tekst vermeld met behulp van een gegevensstroommodel dat is ontworpen om de syntaxis gemakkelijk te lezen, te schrijven en te automatiseren. De query maakt gebruik van schema-entiteiten die zijn georganiseerd in een hiërarchie die vergelijkbaar is met SQL: databases, tabellen en kolommen.

De query bestaat uit een reeks query-instructies,`;`afgebakend door een puntkomma ( ), met ten minste één instructie is een tabelvormige expressie-instructie, wat een instructie is die gegevens produceert die zijn gerangschikt in een tabelachtige maas van kolommen en rijen. De tabelexpressieinstructies van de query leveren de resultaten van de query op.

De syntaxis van de tabelexpressieinstructie heeft een tabelvormige gegevensstroom van de ene tabelqueryoperator naar de andere, te beginnen met gegevensbron (bijvoorbeeld een tabel in een database of een`|`operator die gegevens produceert) en vervolgens door een set gegevenstransformatieoperatoren stroomt die door het gebruik van de pipe ( ) scheidingsteken zijn gebonden.

De volgende query heeft bijvoorbeeld één instructie, een tabelexpressieinstructie. De instructie begint met een `StormEvents` verwijzing naar een tabel met de aangeroepen tabel (de database die deze tabel host, is hier impliciet en een deel van de verbindingsgegevens). De gegevens (rijen) voor die tabel worden `StartTime` vervolgens gefilterd op de waarde `State` van de kolom en vervolgens gefilterd op de waarde van de kolom. De query retourneert vervolgens het aantal 'overlevende' rijen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

In dit geval is het resultaat:

|Count|
|-----|
|   23|
| |

Zie de [naslaggids querytaal](https://aka.ms/kustolangref)voor meer informatie .

## <a name="most-common-operators"></a>Meest voorkomende operatoren

De operatoren in deze sectie zijn de bouwstenen voor het begrijpen van query's in Azure Data Explorer. De meeste query's die u schrijft, bevatten een aantal van deze operatoren.

Als u query's wilt uitvoeren in het Help-cluster: selecteer Klik om query boven elke query **uit te voeren.**

Ga als een opdeandere uitvoeren van query's op uw eigen cluster:

1. Kopieer elke query naar de webgebaseerde querytoepassing en selecteer vervolgens de query of plaats de cursor in de query.

1. Selecteer **Uitvoeren**boven aan de toepassing .

### <a name="count"></a>count

[**aantal:**](https://docs.microsoft.com/azure/kusto/query/countoperator)geeft als resultaat het aantal rijen in de tabel.

Met de volgende query wordt het aantal rijen in de tabel StormEvents geretourneerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>Nemen

[**nemen:**](https://docs.microsoft.com/azure/kusto/query/takeoperator)geeft als resultaat het opgegeven aantal rijen gegevens.

Met de volgende query worden vijf rijen uit de tabel StormEvents geretourneerd. De *trefwoordlimiet* is een alias voor *gebruik.*

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Er is geen garantie welke records worden geretourneerd, tenzij de brongegevens worden gesorteerd.

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Hiermee selecteert u een subset van kolommen.

Met de volgende query wordt een specifieke set kolommen geretourneerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>waarbij het volgende geldt

[**waar**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Hiermee filtert u een tabel naar de subset van rijen die voldoen aan een predicaat.

De volgende query filtert de gegevens op `EventType` en `State`.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sorteren

[**sorteren:**](https://docs.microsoft.com/azure/kusto/query/sortoperator)Sorteer de rijen van de invoertabel in volgorde op een of meer kolommen.

In de volgende query worden `DamageProperty`de gegevens in aflopende volgorde gesorteerd op .

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> De volgorde van de werkzaamheden is belangrijk. Probeer `take 5` zetten `sort by`voordat . Krijg je andere resultaten?

### <a name="top"></a>top

[**boven:**](https://docs.microsoft.com/azure/kusto/query/topoperator)geeft als resultaat de eerste *N-records* gesorteerd op de opgegeven kolommen.

De volgende query retourneert dezelfde resultaten als hierboven met één operator minder.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>uitbreiden

[**uit te breiden**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Berekent afgeleide kolommen.

Met de volgende query wordt een nieuwe kolom gemaakt door in elke rij een waarde te berekenen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Expressies kunnen alle gebruikelijke operatoren bevatten (+, -, *, /, %) en er is een reeks nuttige functies die u aanroepen.

### <a name="summarize"></a>Samenvatten

[**samenvatten:**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)hiermee worden groepen rijen samengevoegd.

In de volgende query wordt `State`het aantal gebeurtenissen geretourneerd door .

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

De **operator groepeert** rijen samen met dezelfde waarden in de component **per** component en gebruikt vervolgens de aggregatiefunctie (zoals **tellen)** om elke groep in één rij te combineren. In dit geval is er dus een rij voor elke status en een kolom voor het aantal rijen in die staat.

Er is een reeks aggregatiefuncties en u er meerdere in één **overzichtsoperator** gebruiken om verschillende berekende kolommen te produceren. U bijvoorbeeld het aantal stormen in elke staat en het unieke aantal stormen per staat krijgen en vervolgens **top** gebruiken om de meest door storm getroffen toestanden te krijgen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Het resultaat van een **samenvatten** bewerking heeft:

- Elke kolom genoemd **in door**

- Een kolom voor elke berekende expressie

- Een rij voor elke combinatie van op waarden

### <a name="render"></a>Render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Hiermee worden resultaten weergegeven als een grafische uitvoer.

In de volgende query wordt een kolomdiagram weergegeven.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

In de volgende query wordt een eenvoudig tijddiagram weergegeven.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

De volgende query telt gebeurtenissen op de tijd modulo een dag, binned in uren, en geeft een tijdgrafiek.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Met de volgende query worden meerdere dagelijkse reeksen in een tijddiagram vergeleken.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> De **render** operator is een client-side functie in plaats van een deel van de motor. Het is geïntegreerd in de taal voor gebruiksgemak. De webtoepassing ondersteunt de volgende opties: staafdiagram, kolomdiagram, cirkeldiagram, tijddiagram en lijndiagram. 

## <a name="scalar-operators"></a>Scalar operatoren

Deze sectie behandelt enkele van de belangrijkste scalaire operatoren.

### <a name="bin"></a>opslaglocatie()

[**opslaglocatie()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Hiermee worden waarden naar beneden afgerond op een geheel getal van een bepaalde opslaglocatiegrootte.

Met de volgende query wordt het aantal berekend met een bucketgrootte van één dag.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>geval()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Evalueert een lijst met predicaten en retourneert de eerste resultaatexpressie waarvan het predicaat is voldaan, of de uiteindelijke **andersexpressie.** U deze operator gebruiken om gegevens te categoriseren of te groeperen:

Met de volgende query `deaths_bucket` wordt een nieuwe kolom geretourneerd en worden de sterfgevallen op nummer groepeert.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Krijgt een overeenkomst voor een reguliere expressie uit een tekenreeks.

Met de volgende query worden specifieke kenmerkwaarden uit een tracering geëxtraheerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Deze query maakt gebruik van een **laatinstructie,** `MyData`die een naam (in dit geval ) bindt aan een expressie. Voor de rest van het bereik, waarin de **laatinstructie** wordt weergegeven (globaal bereik of in een functiebereik), kan de naam worden gebruikt om te verwijzen naar de gebonden waarde.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpreteert een tekenreeks als een JSON-waarde en geeft als dynamisch resultaat als dynamisch. Het is superieur aan het gebruik van de functie **extractjson()** wanneer u meer dan één element van een samengesteld JSON-object moet extraheren.

Met de volgende query worden de JSON-elementen uit een array geëxtraheerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Met de volgende query worden de JSON-elementen geëxtraheerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Met de volgende query worden de JSON-elementen met een dynamisch gegevenstype geëxtraheerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Trekt de gegeven tijdspanne af van de huidige UTC-kloktijd.

Met de volgende query worden gegevens van de afgelopen 12 uur geretourneerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**beginweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Geeft als resultaat het begin van de week met de datum, verschoven door een offset, indien verstrekt

De volgende query retourneert het begin van de week met verschillende verschuivingen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Deze query maakt gebruik van de **operator bereik,** die een tabel met waarden met één kolom genereert. Zie ook: [**beginvandag()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**beginvanweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**beginjaar()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**beginvanmaand()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), en [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>tussen()

[**tussen()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Komt overeen met de invoer die zich binnen het inclusieve bereik bevindt.

De volgende query filtert de gegevens op een bepaald datumbereik.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

De volgende query filtert de gegevens op een bepaald datumbereik, met de lichte variatie van drie dagen (`3d`) vanaf de begindatum.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabeloperatoren

Kusto heeft veel tabeloperatoren, waarvan sommige in andere delen van dit artikel worden behandeld. Hier zullen we ons richten op **parse**. 

### <a name="parse"></a>parse

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator): evalueert een tekenreeksexpressie en ontsluit de waarde ervan in een of meer berekende kolommen. Er zijn drie manieren om te ontleden: eenvoudig (de standaard), regex, en ontspannen.

De volgende query ontlijnt een tracering en haalt de relevante waarden uit met behulp van een standaard van eenvoudige parsing. De expressie (stringconstant genoemd) is een normale tekenreekswaarde en de overeenkomst is strikt: uitgebreide kolommen moeten overeenkomen met de vereiste typen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

De volgende query ontlijnt een tracering `kind = regex`en haalt de relevante waarden uit met behulp van . De StringConstant kan een regelmatige expressie zijn.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

De volgende query ontlijnt een tracering `kind = relaxed`en haalt de relevante waarden uit met behulp van . De StringConstant is een normale tekenreekswaarde en de overeenkomst is ontspannen: uitgebreide kolommen kunnen gedeeltelijk overeenkomen met de vereiste typen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Tijdreeksanalyse

### <a name="make-series"></a>make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): verzamelt groepen rijen samen zoals [samenvatten,](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)maar genereert een (tijd)serievector per elke combinatie van waarden.

Met de volgende query wordt een reeks tijdreeksen geretourneerd voor het aantal stormgebeurtenissen per dag. De query heeft betrekking op een periode van drie maanden voor elke status, waarbij ontbrekende opslaglocaties worden gevuld met de constante 0:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Zodra u een set (tijd)reeksen hebt gemaakt, u reeksfuncties toepassen om afwijkende vormen, seizoenspatronen en nog veel meer te detecteren.

Met de volgende query worden de drie hoogste statussen geëxtraheerd die de meeste gebeurtenissen op de specifieke dag hadden:

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Bekijk voor meer informatie de volledige lijst met [reeksfuncties](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Geavanceerde aggregaties

We behandelden basisaggregaties, zoals **tellen** en **samenvatten,** eerder in dit artikel. In dit gedeelte worden meer geavanceerde opties geïntroduceerd.

### <a name="top-nested"></a>top-geneste

[**topgeneste**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Produceert hiërarchische topresultaten, waarbij elk niveau een drill-down is op basis van eerdere niveauwaarden.

Deze operator is handig voor scenario's voor dashboardvisualisatie of wanneer het nodig is om een vraag als volgt te beantwoorden: "Zoek de top-N-waarden van K1 (met behulp van een aggregatie); voor elk van hen, vinden wat zijn de top-M waarden van K2 (met behulp van een andere aggregatie); ..."

Met de volgende query `State` wordt een hiërarchische `Sources`tabel geretourneerd met op het hoogste niveau, gevolgd door .

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() -plug-in

[**pivot() plug-in**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Draait een tabel door de unieke waarden uit één kolom in de invoertabel om te zetten in meerdere kolommen in de uitvoertabel. De operator voert aggregaties uit wanneer deze nodig zijn op de resterende kolomwaarden in de uiteindelijke uitvoer.

Met de volgende query wordt een filter gebruikt en worden de rijen in kolommen gedraaid.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): geeft als resultaat een schatting van het aantal verschillende waarden van een expressie in de groep. Gebruik [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) om alle waarden te tellen.

De volgende query `Source` `State`telt verschillend van .

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): geeft als resultaat een schatting van het aantal verschillende waarden van de expressie voor rijen waarvoor het predicaat wordt geëvalueerd naar true.

De volgende query telt `Source` de `DamageProperty < 5000`verschillende waarden van waar .

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Berekent de **telling uit** hyperlogresultaten (gegenereerd door [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) of [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

De volgende query gebruikt het HLL-algoritme om het aantal te genereren.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): hiermee wordt een rij in de groep gevonden die een expressie maximaliseert en geeft het de waarde van een andere expressie (of * als resultaat om de hele rij te retourneren).

De volgende query geeft de tijd van het laatste overstromingsrapport in elke status weer.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): geeft als resultaat een dynamische array (JSON) van de set afzonderlijke waarden die een expressie in de groep neemt.

De volgende query retourneert alle keren waarop een overstroming door elke status is gerapporteerd en maakt een array van de set van verschillende waarden.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-uit te breiden

[**mv-uitvouwen**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Hiermee breidt u verzameling(en) met meerdere waarde's uit een kolom met dynamisch getypttype uit, zodat elke waarde in de verzameling een afzonderlijke rij krijgt. Alle andere kolommen in een uitgevouwen rij worden gedupliceerd. Het is het tegenovergestelde van makelijst.

De volgende query genereert voorbeeldgegevens door een set te maken en deze vervolgens te gebruiken om de **mogelijkheden voor het uitvouwen van de MV aan** te tonen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentielen()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): geeft als resultaat een schatting voor het opgegeven [**percentiel**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) van de populatie die wordt gedefinieerd door een expressie. De nauwkeurigheid is afhankelijk van de dichtheid van de bevolking in de regio van het percentiel. Kan alleen worden gebruikt in de context van aggregatie binnen [**samen te vatten**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

De volgende query berekent percentiels voor stormduur.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

De volgende query berekent percentiels voor stormduur op status en normaliseert de gegevens met vijf minuten (`5m`).

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Gegevensset voor kruiswerk

In deze sectie worden elementen verwerkt waarmee u complexere query's maken, gegevens verzamelen in tabellen en query's uitvoeren in databases en clusters.

### <a name="let"></a>Laat

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Verbetert de modulariteit en het hergebruik. Met **de instructie let** u een potentieel complexe expressie in meerdere delen, elk gebonden aan een naam, samenvoegen en deze onderdelen samen samenstellen. Een **laatinstructie** kan ook worden gebruikt om door de gebruiker gedefinieerde functies en weergaven te maken (expressies boven tabellen waarvan de resultaten eruit zien als een nieuwe tabel). Expressies die gebonden zijn aan een **laatinstructie** kunnen van scalaire type, van tabeltype of door de gebruiker gedefinieerde functie (lambda's) zijn.

In het volgende voorbeeld wordt een tabeltypevariabele maakt en deze gebruikt in een volgende expressie.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Voeg de rijen van twee tabellen samen om een nieuwe tabel te vormen door waarden van de opgegeven kolom(en) van elke tabel af te voegen. Kusto ondersteunt een volledig scala aan jointypes: **fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

In het volgende voorbeeld worden twee tabellen samengevoegd met een inner join.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Gebruik **waar** en **projectoperatoren** om het aantal rijen en kolommen in de invoertabellen vóór de join te verminderen. Als de ene tafel altijd kleiner is dan de andere, gebruikt u deze als de linker (piped) kant van de join. De kolommen voor de joinmatch moeten dezelfde naam hebben. Gebruik de **projectoperator** indien nodig om de naam van een kolom in een van de tabellen te wijzigen.

### <a name="serialize"></a>Serialize

[**serialiseren:**](https://docs.microsoft.com/azure/kusto/query/serializeoperator)serialiseert de rijset, zodat u functies gebruiken waarvoor geserialiseerde gegevens nodig zijn, zoals **row_number().**

De volgende query slaagt omdat de gegevens zijn geserialiseerd.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

De rijset wordt ook beschouwd als geserialiseerd als het een resultaat is van: **sorteer-** **of top-** of **range** operators, optioneel gevolgd door **project**, **project-away**, **extend**, **where**, **parse,** **mv-expand,** of **take** operators.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Cross-database en cross-cluster query's

[Cross-database en cross-cluster query's:](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries)U een database op `database("MyDatabase").MyTable`hetzelfde cluster opvragen door deze te verwijzen als. U een database op een extern cluster `cluster("MyCluster").database("MyDatabase").MyTable`opvragen door ernaar te verwijzen als.

De volgende query wordt aangeroepen vanuit `MyCluster` één cluster en query's gegevens uit het cluster. Als u deze query wilt uitvoeren, gebruikt u uw eigen clusternaam en databasenaam.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Gebruikersanalyses

Deze sectie bevat elementen en query's die aantonen hoe eenvoudig het is om analyses van gebruikersgedrag in Kusto uit te voeren.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics-plug-in

[**activity_counts_metrics-plug-in**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Berekent nuttige activiteitsstatistieken (totale telwaarden, afzonderlijke telwaarden, verschillende tellingen van nieuwe waarden en geaggregeerde verschillende tellingen). Statistieken worden berekend voor elk tijdvenster, vervolgens worden ze vergeleken en samengevoegd naar en met alle vorige tijdvensters.

De volgende query analyseert de acceptatie van gebruikers door het aantal dagelijkse activiteiten te berekenen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>activity_engagement-plug-in

[**activity_engagement-plug-in:**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin)berekent de verhouding activiteitsbetrokkenheid op basis van id-kolom over een schuivend tijdlijnvenster. **activity_engagement plugin** kan worden gebruikt voor het berekenen van DAU, WAU en MAU (dagelijkse, wekelijkse en maandelijkse actieve gebruikers).

Met de volgende query wordt de verhouding van de totale afzonderlijke gebruikers die dagelijks een toepassing gebruiken, geretourneerd in vergelijking met de totale afzonderlijke gebruikers die de toepassing wekelijks gebruiken, in een afzonderlijk venster van zeven dagen.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Bij het berekenen van DAU/MAU wijzigt u de eindgegevens en de verplaatsingsvensterperiode (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics-plug-in

[**activity_metrics-plug-in**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Berekent nuttige activiteitsstatistieken (verschillende telwaarden, duidelijke telling van nieuwe waarden, retentiepercentage en verlooppercentage) op basis van het huidige periodevenster ten opzichte van het vorige periodevenster.

In de volgende query wordt de churn- en retentieratio voor een bepaalde gegevensset berekend.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics-plug-in

[**new_activity_metrics plug-in:**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin)Berekent nuttige activiteitsstatistieken (verschillende telwaarden, verschillende tellingen van nieuwe waarden, retentiepercentage en verlooppercentage) voor het cohort van nieuwe gebruikers. Het concept van deze plugin is vergelijkbaar met [**activity_metrics plugin,**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)maar richt zich op nieuwe gebruikers.

De volgende query berekent een retentie- en verlooppercentage met een week-over-weekvenster voor het nieuwe gebruikerscohort (gebruikers die in de eerste week zijn aangekomen).

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>session_count-plug-in

[**session_count-plug-in**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): berekent het aantal sessies op basis van id-kolom over een tijdlijn.

De volgende query retourneert het aantal sessies. Een sessie wordt als actief beschouwd als een gebruikers-id ten minste eenmaal wordt weergegeven in een tijdsbestek van 100-tijdslots, terwijl het terugkijkvenster van de sessie 41-tijdslots is.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>funnel_sequence-plug-in

[**funnel_sequence plug-in**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Berekent het aantal gebruikers dat een reeks toestanden heeft genomen; toont de verdeling van eerdere en volgende staten die hebben geleid tot of werden gevolgd door de volgorde.

De volgende query laat zien welke gebeurtenis gebeurt voor en na alle Tornado gebeurtenissen in 2007.

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion-plug-in

[**funnel_sequence_completion-plug-in**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Berekent de trechter van voltooide sequentiestappen binnen verschillende perioden.

De volgende query controleert de voltooiingstrechter van de reeks: `Hail -> Tornado -> Thunderstorm -> Wind` in "algemene"`[1h, 4h, 1d]`tijden van een uur, vier uur en één dag ( ).

**\[**[**Klik hier om query uit te voeren**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

Deze sectie behandelt [**functies:**](https://docs.microsoft.com/azure/kusto/query/functions)herbruikbare query's die op de server zijn opgeslagen. Functies kunnen worden aangeroepen door query's en andere functies (recursieve functies worden niet ondersteund).

> [!NOTE]
> U geen functies maken op het Help-cluster, dat alleen-lezen is. Gebruik uw eigen testcluster voor dit onderdeel.

In het volgende voorbeeld wordt een`MyState`functie gemaakt die een statusnaam ( ) als argument gebruikt.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Het volgende voorbeeld roept een functie aan, die gegevens voor de staat Texas krijgt.

```Kusto
MyFunction ("Texas")
| summarize count()
```

In het volgende voorbeeld wordt de functie verwijderd die in de eerste stap is gemaakt.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Volgende stappen

[Kusto-querytaalverwijzing](https://aka.ms/kustolangref)
