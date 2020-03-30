---
title: Parse-tekstgegevens in Azure Monitor-logboeken | Microsoft Documenten
description: Beschrijft verschillende opties voor het ontleden van logboekgegevens in Azure Monitor-records wanneer de gegevens worden ingenomen en wanneer deze in een query worden opgehaald, waarbij de relatieve voordelen voor elk worden vergeleken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672443"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Parse-tekstgegevens in Azure Monitor-logboeken
Sommige logboekgegevens die door Azure Monitor worden verzameld, bevatten meerdere gegevens in één eigenschap. Door deze gegevens in meerdere eigenschappen te ontzetten, u deze gemakkelijker gebruiken in query's. Een veelvoorkomend voorbeeld is een [aangepast logboek](../../log-analytics/log-analytics-data-sources-custom-logs.md) dat een volledige logboekvermelding met meerdere waarden in één eigenschap verzamelt. Door afzonderlijke eigenschappen voor de verschillende waarden te maken, u op elk van deze waarden zoeken en aggregeren.

In dit artikel worden verschillende opties beschreven voor het ontleden van logboekgegevens in Azure Monitor wanneer de gegevens worden ingenomen en wanneer deze in een query worden opgehaald, waarbij de relatieve voordelen voor elk worden vergeleken.


## <a name="parsing-methods"></a>Parsingmethoden
U gegevens ontschepen op het moment dat de gegevens worden verzameld of op querytijd bij het analyseren van de gegevens met een query. Elke strategie heeft unieke voordelen zoals hieronder beschreven.

### <a name="parse-data-at-collection-time"></a>Parse-gegevens bij het verzamelen
Wanneer u gegevens onteert tijdens het verzamelen, configureert u [Aangepaste velden](../../log-analytics/log-analytics-custom-fields.md) die nieuwe eigenschappen in de tabel maken. Query's hoeven geen ontwijslogica op te nemen en gebruiken deze eigenschappen gewoon als elk ander veld in de tabel.

Voordelen van deze methode zijn onder meer:

- Eenvoudiger om de verzamelde gegevens op te vragen, omdat u geen parse-opdrachten in de query hoeft op te nemen.
- Betere queryprestaties omdat de query geen parsing hoeft uit te voeren.
 
Nadelen aan deze methode zijn onder andere:

- Moet vooraf worden gedefinieerd. Kan geen gegevens bevatten die al zijn verzameld.
- Als u de parsing-logica wijzigt, is deze alleen van toepassing op nieuwe gegevens.
- Minder parsingopties dan beschikbaar in query's.
- Verhoogt de latentietijd voor het verzamelen van gegevens.
- Fouten kunnen moeilijk te hanteren zijn.


### <a name="parse-data-at-query-time"></a>Parse-gegevens tijdens query's
Wanneer u gegevens onteert tijdens querytijd, neemt u logica op in uw query om gegevens in meerdere velden te ontlopen. De werkelijke tabel zelf wordt niet gewijzigd.

Voordelen van deze methode zijn onder meer:

- Geldt voor alle gegevens, inclusief gegevens die al zijn verzameld.
- Wijzigingen in de logica kunnen onmiddellijk worden toegepast op alle gegevens.
- Flexibele parsingopties, inclusief vooraf gedefinieerde logica voor bepaalde gegevensstructuren.
 
Nadelen aan deze methode zijn onder andere:

- Vereist complexere query's. Dit kan worden beperkt door functies te gebruiken [om een tabel te simuleren.](#use-function-to-simulate-a-table)
- Moet ontweringlogica repliceren in meerdere query's. Kan enige logica delen door middel van functies.
- Kan overhead maken bij het uitvoeren van complexe logica tegen zeer grote recordsets (miljarden records).

## <a name="parse-data-as-its-collected"></a>Parse-gegevens tijdens het verzamelen
Zie [Aangepaste velden maken in Azure Monitor](../platform/custom-fields.md) voor meer informatie over het ontwijsmaken van gegevens terwijl deze worden verzameld. Hiermee worden aangepaste eigenschappen in de tabel gemaakt die kunnen worden gebruikt door query's, net als elke andere eigenschap.

## <a name="parse-data-in-query-using-patterns"></a>Parse-gegevens in query met patronen
Wanneer de gegevens die u wilt ontlopen kunnen worden geïdentificeerd door een patroon dat in records wordt herhaald, u verschillende operatoren in de [Kusto-querytaal](/azure/kusto/query/) gebruiken om het specifieke stuk gegevens in een of meer nieuwe eigenschappen te extraheren.

### <a name="simple-text-patterns"></a>Eenvoudige tekstpatronen

Gebruik de [parse-operator](/azure/kusto/query/parseoperator) in uw query om een of meer aangepaste eigenschappen te maken die uit een tekenreeksexpressie kunnen worden geëxtraheerd. U geeft het te identificeren patroon op en de namen van de te maken eigenschappen. Dit is vooral handig voor gegevens met tekenreeksen met sleutelwaarde met een formulier dat lijkt op _key=value._

Overweeg een aangepast logboek met gegevens in de volgende indeling.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Met de volgende query worden deze gegevens in afzonderlijke eigenschappen opgenomen. De regel met _het project_ wordt toegevoegd om alleen de berekende eigenschappen te retourneren en niet _RawData,_ de enige eigenschap die de volledige vermelding van het aangepaste logboek vasthoudt.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Hieronder volgt een ander voorbeeld dat de gebruikersnaam van een UPN in de _AzureActivity-tabel_ uitbreekt.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguliere expressies
Als uw gegevens kunnen worden geïdentificeerd met een reguliere expressie, u functies gebruiken [die reguliere expressies gebruiken](/azure/kusto/query/re2) om afzonderlijke waarden te extraheren. In het volgende voorbeeld wordt [extract](/azure/kusto/query/extractfunction) gebruikt om het _UPN-veld_ uit _AzureActivity-records_ te halen en vervolgens afzonderlijke gebruikers terug te sturen.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Om efficiënt ontsen op grote schaal mogelijk te maken, maakt Azure Monitor gebruik van een Re2-versie van reguliere expressies, die vergelijkbaar is, maar niet identiek is aan sommige van de andere reguliere expressievarianten. Raadpleeg de syntaxis van de [re2-expressie](https://aka.ms/kql_re2syntax) voor meer informatie.


## <a name="parse-delimited-data-in-a-query"></a>Parse-afgebakende gegevens in een query
Afgebakende gegevens scheiden velden met een gemeenschappelijk teken, zoals een komma in een CSV-bestand. Gebruik de [gesplitste](/azure/kusto/query/splitfunction) functie om afgebakende gegevens te ontzeggen met behulp van een door u opgegeven scheidingsteken. U dit gebruiken met [de operator uit te breiden](/azure/kusto/query/extendoperator) om alle velden in de gegevens terug te sturen of om afzonderlijke velden op te geven die in de uitvoer moeten worden opgenomen.

> [!NOTE]
> Aangezien split een dynamisch object retourneert, moeten de resultaten mogelijk expliciet worden gegoten naar gegevenstypen, zoals tekenreeksen die moeten worden gebruikt in operatoren en filters.

Overweeg een aangepast logboek met gegevens in de volgende CSV-indeling.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

De volgende query zou deze gegevens ontlasten en samenvatten door twee van de berekende eigenschappen. De eerste regel splitst de eigenschap _RawData_ in een tekenreeksarray. Elk van de volgende regels geeft een naam aan afzonderlijke eigenschappen en voegt deze toe aan de uitvoer met behulp van functies om ze om te zetten naar het juiste gegevenstype.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Parse vooraf gedefinieerde structuren in een query
Als uw gegevens zijn opgemaakt in een bekende structuur, u mogelijk een van de functies in de [Kusto-querytaal](/azure/kusto/query/) gebruiken voor het ontzeggen van vooraf gedefinieerde structuren:

- [Json](/azure/kusto/query/parsejsonfunction)
- [Xml](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Url](/azure/kusto/query/parseurlfunction)
- [URL-query](/azure/kusto/query/parseurlqueryfunction)
- [Bestandspad](/azure/kusto/query/parsepathfunction)
- [Gebruikersagent](/azure/kusto/query/parse-useragentfunction)
- [Versietekenreeks](/azure/kusto/query/parse-versionfunction)

In het volgende voorbeeld query wordt het veld _Eigenschappen_ van de _azureactivity-tabel,_ die is gestructureerd in JSON, geparsiseerd. Het slaat de resultaten op in een dynamische eigenschap genaamd _parsedProp_, die de individuele waarde met de naam in de JSON bevat. Deze waarden worden gebruikt om de queryresultaten te filteren en samen te vatten.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Deze parsingsfuncties kunnen processorintensief zijn, dus ze mogen alleen worden gebruikt wanneer uw query meerdere eigenschappen van de opgemaakte gegevens gebruikt. Anders zal eenvoudige patroon matching verwerking sneller zijn.

In het volgende voorbeeld ziet u de uitsplitsing van het TGT Preauth-type van de domeincontroller. Het type bestaat alleen in het veld EventData, een XML-tekenreeks, maar er zijn geen andere gegevens uit dit veld nodig. In dit geval wordt [parse](/azure/kusto/query/parseoperator) gebruikt om het vereiste stukje gegevens uit te kiezen.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Functie gebruiken om een tabel te simuleren
U meerdere query's hebben die dezelfde ontwering van een bepaalde tabel uitvoeren. Maak in dit geval [een functie](functions.md) die de ontleedde gegevens retourneert in plaats van de parsingslogica in elke query te repliceren. U de functiealias vervolgens gebruiken in plaats van de oorspronkelijke tabel in andere query's.

Overweeg het hierboven afgebakende voorbeeld van het aangepaste logboek van komma's. Als u de ontleedde gegevens in meerdere query's wilt gebruiken, maakt u een functie met de volgende query en slaat u deze op met de alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

U nu de alias _MyCustomCSVLog_ gebruiken in plaats van de werkelijke tabelnaam in query's zoals de volgende.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.
