---
title: Plannen en uitvoeren met gegevensfabriek
description: Meer informatie over plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281065"
---
# <a name="data-factory-scheduling-and-execution"></a>Planning en uitvoering van gegevensfabriek
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de gegevensfabriekservice gebruikt, raadpleegt u [de uitvoering van pijplijnen en activeert](../concepts-pipeline-execution-triggers.md) u het artikel.

In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. In dit artikel wordt ervan uitgegaan dat u de basisbeginselen van toepassingsmodelconcepten van Data Factory begrijpt, waaronder activiteit, pijplijnen, gekoppelde services en gegevenssets. Zie de volgende artikelen voor basisconcepten van Azure Data Factory:

* [Inleiding tot Data Factory](data-factory-introduction.md)
* [Pijplijnen](data-factory-create-pipelines.md)
* [Gegevenssets](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Begin- en eindtijden van de pijplijn
Een pijplijn is alleen actief tussen **de begintijd** en **de eindtijd.** Het wordt niet uitgevoerd vóór de begintijd of na de eindtijd. Als de pijplijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin- en eindtijd. Als een pijplijn moet worden uitgevoerd, mag deze niet worden onderbroken. U vindt deze instellingen (start, einde, onderbroken) in de pijplijndefinitie: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Zie het artikel [pijplijnen maken voor](data-factory-create-pipelines.md) meer informatie over deze eigenschappen. 


## <a name="specify-schedule-for-an-activity"></a>Schema voor een activiteit opgeven
Het is niet de pijplijn die wordt uitgevoerd. Het zijn de activiteiten in de pijplijn die worden uitgevoerd in de algemene context van de pijplijn. U een terugkerend schema voor een activiteit opgeven met behulp van het **gedeelte scheduler** van activiteit JSON. U bijvoorbeeld een activiteit als volgt als volgt plannen:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Zoals in het volgende diagram wordt weergegeven, maakt het opgeven van een schema voor een activiteit een reeks tuimelende vensters met in de begin- en eindtijden van de pijplijn. Tumbling ramen zijn een reeks van vaste grootte niet-overlappende, aaneengesloten tijdsintervallen. Deze logische tumbling vensters voor een activiteit worden genoemd **activiteit vensters**.

![Voorbeeld van activiteitsplanner](media/data-factory-scheduling-and-execution/scheduler-example.png)

De eigenschap **scheduler** voor een activiteit is optioneel. Als u deze eigenschap opgeeft, moet deze overeenkomen met de cadans die u opgeeft in de definitie van uitvoergegevensset voor de activiteit. Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. Daarom moet u een uitvoergegevensset maken, zelfs als de activiteit geen uitvoer oplevert. 

## <a name="specify-schedule-for-a-dataset"></a>Schema voor een gegevensset opgeven
Een activiteit in een Data Factory-pijplijn kan nul of meer **invoergegevenssets** nemen en een of meer uitvoergegevenssets produceren. Voor een activiteit u de cadans opgeven waarop de invoergegevens beschikbaar zijn of de uitvoergegevens worden geproduceerd met behulp van de **beschikbaarheidssectie** in de gegevenssetdefinities. 

**Frequentie** in de **beschikbaarheidssectie** geeft de tijdseenheid aan. De toegestane waarden voor frequentie zijn: minuut, uur, dag, week en maand. De **eigenschap interval** in de sectie beschikbaarheid geeft een vermenigvuldiger voor frequentie op. Bijvoorbeeld: als de frequentie is ingesteld op Dag en interval is ingesteld op 1 voor een uitvoergegevensset, worden de uitvoergegevens dagelijks geproduceerd. Als u de frequentie als minuut opgeeft, raden we u aan het interval in te stellen op niet minder dan 15. 

In het volgende voorbeeld zijn de invoergegevens elk uur beschikbaar`"frequency": "Hour", "interval": 1`en worden de uitvoergegevens per uur geproduceerd ( ). 

**Invoergegevensset:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Uitvoergegevensset**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Momenteel **wordt de uitvoergegevensset weergegeven in de planning**. Met andere woorden, de planning die is opgegeven voor de uitvoergegevensset wordt gebruikt om een activiteit uit te voeren tijdens runtime. Daarom moet u een uitvoergegevensset maken, zelfs als de activiteit geen uitvoer oplevert. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. 

In de volgende pijplijndefinitie wordt de eigenschap **scheduler** gebruikt om planning voor de activiteit op te geven. Deze eigenschap is optioneel. Momenteel moet de planning voor de activiteit overeenkomen met het schema dat is opgegeven voor de uitvoergegevensset.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

In dit voorbeeld wordt de activiteit elk uur uitgevoerd tussen de begin- en eindtijden van de pijplijn. De uitvoergegevens worden elk uur geproduceerd voor vensters van drie uur (08.00 - 9.00 uur, 9.00 - 10.00 uur en 10.00 - 11.00 uur). 

Elke eenheid gegevens die wordt verbruikt of geproduceerd door een activiteitsrun, wordt een **gegevenssegment genoemd.** In het volgende diagram ziet u een voorbeeld van een activiteit met één invoergegevensset en één uitvoergegevensset: 

![Beschikbaarheidsplanner](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Het diagram toont de gegevenssegmenten per uur voor de invoer- en uitvoergegevensset. Het diagram toont drie invoersegmenten die klaar zijn voor verwerking. De activiteit van 10-11 uur is in volle gang en produceert het uitvoersegment 10-11 AM. 

U hebt toegang tot het tijdsinterval dat is gekoppeld aan het huidige segment in de gegevensset JSON met variabelen: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) en [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Op dezelfde manier hebt u toegang tot het tijdsinterval dat is gekoppeld aan een activiteitsvenster met behulp van VensterStart en WindowEnd. De planning van een activiteit moet overeenkomen met het schema van de uitvoergegevensset voor de activiteit. Daarom zijn de slicestart- en sliceendwaarden respectievelijk gelijk aan de waarden WindowStart en WindowEnd. Zie [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md#data-factory-system-variables) voor meer informatie over deze variabelen.  

U deze variabelen voor verschillende doeleinden gebruiken in uw activiteit JSON. U ze bijvoorbeeld gebruiken om gegevens te selecteren uit invoer- en uitvoergegevenssets die tijdreeksgegevens weergeven (bijvoorbeeld: 8.00 tot 9.00 uur). In dit voorbeeld worden ook **WindowStart** en **WindowEnd** gebruikt om relevante gegevens voor een activiteitsrun te selecteren en deze te kopiëren naar een blob met de juiste **mapPath**. De **mapPath** is geparameteriseerd om een aparte map voor elk uur te hebben.  

In het voorgaande voorbeeld is het schema dat is opgegeven voor invoer- en uitvoergegevenssets hetzelfde (per uur). Als de invoergegevensset voor de activiteit beschikbaar is op een andere frequentie, bijvoorbeeld om de 15 minuten, wordt de activiteit die deze uitvoergegevensset produceert nog steeds één keer per uur uitgevoerd omdat de uitvoergegevensset de activiteitsplanning aanstuurt. Zie [Modelgegevenssets met verschillende frequenties voor](#model-datasets-with-different-frequencies)meer informatie.

## <a name="dataset-availability-and-policies"></a>Beschikbaarheid en beleid voor gegevenssets
U hebt het gebruik van frequentie- en intervaleigenschappen gezien in de beschikbaarheidssectie van de definitie van gegevenssets. Er zijn een paar andere eigenschappen die van invloed zijn op de planning en uitvoering van een activiteit. 

### <a name="dataset-availability"></a>Beschikbaarheid van gegevenssets 
In de volgende tabel worden eigenschappen beschreven die u gebruiken in de **sectie beschikbaarheid:**

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijdseenheid op voor de productie van gegevenssetsslicen.<br/><br/><b>Ondersteunde frequentie</b>: Minuut, uur, dag, week, maand |Ja |N.v.t. |
| interval |Hiermee geeft u een vermenigvuldiger voor frequentie op<br/><br/>"Frequency x interval" bepaalt hoe vaak het segment wordt geproduceerd.<br/><br/>Als u de gegevensset per uur moet snijden, stelt u <b>Frequentie</b> in op <b>Uur</b>en <b>interval</b> op <b>1</b>.<br/><br/><b>Opmerking:</b>Als u Frequentie als minuut opgeeft, raden we u aan het interval in te stellen op niet minder dan 15 |Ja |N.v.t. |
| stijl |Hiermee geeft u op of het segment aan het begin/einde van het interval moet worden geproduceerd.<ul><li>Begininterval</li><li>EndofInterval</li></ul><br/><br/>Als frequentie is ingesteld op Maand en de stijl is ingesteld op EndOfInterval, wordt het segment op de laatste dag van de maand geproduceerd. Als de stijl is ingesteld op StartOfInterval, wordt het segment op de eerste dag van de maand geproduceerd.<br/><br/>Als de frequentie is ingesteld op Dag en de stijl is ingesteld op EndOfInterval, wordt het segment in het laatste uur van de dag geproduceerd.<br/><br/>Als de frequentie is ingesteld op Uur en de stijl is ingesteld op EndOfInterval, wordt het segment aan het einde van het uur geproduceerd. Voor een segment voor een periode van 13.00 tot 14.00 uur wordt het segment bijvoorbeeld om 14.00 uur geproduceerd. |Nee |EndofInterval |
| anchorDateTime anchorDateTime |Hiermee definieert u de absolute positie in de tijd die door scheduler wordt gebruikt om de grenzen van het segment van de gegevensset te berekenen. <br/><br/><b>Opmerking:</b>Als de AnchorDateTime datumdelen heeft die gedetailleerder zijn dan de frequentie, worden de meer gedetailleerde onderdelen genegeerd. <br/><br/>Als het <b>interval</b> bijvoorbeeld <b>elk uur</b> is (frequentie: uur en interval: 1) en de <b>AnchorDateTime</b> <b>minuten en seconden</b>bevat, worden de <b>minuten- en secondendelen</b> van de AnchorDateTime genegeerd. |Nee |01/01/0001 |
| offset |Tijdspanne waarmee het begin en einde van alle gegevenssetsegmenten worden verschoven. <br/><br/><b>Opmerking:</b>Als zowel anchorDateTime als offset zijn opgegeven, is het resultaat de gecombineerde verschuiving. |Nee |N.v.t. |

### <a name="offset-example"></a>compensatievoorbeeld
Standaard beginnen dagelijkse`"frequency": "Day", "interval": 1`( ) segmenten om 12 uur UTC-tijd (middernacht). Als u wilt dat de begintijd 6 AM UTC-tijd is, stelt u de verschuiving in zoals weergegeven in het volgende fragment: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime,voorbeeld
In het volgende voorbeeld wordt de gegevensset eenmaal per 23 uur geproduceerd. Het eerste segment begint op het tijdstip dat is `2017-04-19T08:00:00` opgegeven door de anchorDateTime, ingesteld op (UTC-tijd).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>offset/stijlvoorbeeld
De volgende gegevensset is een maandelijkse gegevensset en wordt geproduceerd op`3.08:00:00`de 3e van elke maand om 8:00 uur (

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Gegevenssetbeleid
Een gegevensset kan een validatiebeleid hebben gedefinieerd dat aangeeft hoe de gegevens die door een segmentuitvoering worden gegenereerd, kunnen worden gevalideerd voordat deze klaar is voor consumptie. In dergelijke gevallen wordt de uitvoersegmentstatus gewijzigd in **Wachten** met een substatus van **Validatie**nadat het segment is voltooid. Nadat de segmenten zijn gevalideerd, wordt de segmentstatus gewijzigd in **Gereed**. Als er een gegevenssegment is geproduceerd, maar niet is geslaagd voor de validatie, worden activiteitssegmenten uitgevoerd die afhankelijk zijn van dit segment. [Bewaken en beheren van pijplijnen](data-factory-monitor-manage-pipelines.md) dekt de verschillende statussen van gegevenssegmenten in Data Factory.

In **de beleidssectie** in de definitie van gegevenssets worden de criteria of de voorwaarde gedefinieerd waaraan de werksetsegmenten moeten voldoen. In de volgende tabel worden eigenschappen beschreven die u gebruiken in de **beleidssectie:**

| Policy Name | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimaal FormaatMB | Valideert dat de gegevens in een **Azure-blob** voldoen aan de minimumgroottevereisten (in megabytes). |Azure Blob |Nee |N.v.t. |
| minimumRijen | Valideert dat de gegevens in een **Azure SQL-database** of een **Azure-tabel** het minimumaantal rijen bevatten. |<ul><li>Azure SQL Database</li><li>Azure Table</li></ul> |Nee |N.v.t. |

#### <a name="examples"></a>Voorbeelden
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRijen**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Zie [Artikel Gegevenssets maken voor](data-factory-create-datasets.md) meer informatie over deze eigenschappen en voorbeelden. 

## <a name="activity-policies"></a>Activiteitbeleid
Beleidsregels zijn van invloed op het verloop van een activiteit, met name wanneer het segment van een tabel wordt verwerkt. De volgende tabel bevat de details.

| Eigenschap | Toegestane waarden | Standaardwaarde | Beschrijving |
| --- | --- | --- | --- |
| Concurrency |Geheel getal <br/><br/>Maximale waarde: 10 |1 |Aantal gelijktijdige uitvoeringen van de activiteit.<br/><br/>Het bepaalt het aantal parallelle activiteitsuitvoeringen dat op verschillende segmenten kan plaatsvinden. Als een activiteit bijvoorbeeld een grote set beschikbare gegevens moet doorlopen, versnelt een grotere gelijktijdigheidswaarde de gegevensverwerking. |
| uitvoeringPrioriteitOrder |NieuwsteEerste<br/><br/>OudsteEerste |OudsteEerste |Hiermee bepaalt u de volgorde van gegevenssegmenten die worden verwerkt.<br/><br/>Bijvoorbeeld, als u 2 segmenten (een gebeurt om 16.00 uur, en een andere om 17.00 uur), en beide zijn in afwachting van uitvoering. Als u de executionPriorityOrder als NieuwsteEerste instelt, wordt het segment om 17.00 uur eerst verwerkt. Als u de uitvoeringPrioriteitORder als OldestFIrst instelt, wordt het segment om 16.00 uur verwerkt. |
| retry |Geheel getal<br/><br/>Maximale waarde kan 10 zijn |0 |Het aantal nieuwe pogingen vóór de gegevensverwerking voor het segment wordt gemarkeerd als Mislukt. Activiteitsuitvoering voor een gegevenssegment wordt opnieuw geprobeerd tot het opgegeven aantal opnieuw proberen. De poging wordt zo snel mogelijk na de storing uitgevoerd. |
| timeout |TimeSpan |00:00:00 |Time-out voor de activiteit. Voorbeeld: 00:10:00 (impliceert time-out 10 minuten)<br/><br/>Als een waarde niet is opgegeven of 0 is, is de time-out oneindig.<br/><br/>Als de verwerkingstijd van gegevens op een segment de time-outwaarde overschrijdt, wordt deze geannuleerd en probeert het systeem de verwerking opnieuw te proberen. Het aantal nieuwe pogingen is afhankelijk van de eigenschap opnieuw proberen. Wanneer een time-out optreedt, wordt de status ingesteld op TimedOut. |
| Vertraging |TimeSpan |00:00:00 |Geef de vertraging op voordat de gegevensverwerking van het segment wordt gestart.<br/><br/>De uitvoering van activiteit voor een gegevenssegment wordt gestart nadat de vertraging de verwachte uitvoeringstijd is voorbijgegaan.<br/><br/>Voorbeeld: 00:10:00 (impliceert vertraging van 10 minuten) |
| longRetry |Geheel getal<br/><br/>Maximale waarde: 10 |1 |Het aantal lange pogingen om opnieuw te proberen voordat de slice-uitvoering is mislukt.<br/><br/>longRetry pogingen worden gespreid door longRetryInterval. Dus als u een tijd tussen pogingen opnieuw proberen moet opgeven, gebruikt u longRetry. Als zowel Opnieuw proberen als longRetry zijn opgegeven, omvat elke longRetry-poging opnieuw proberen pogingen en het maximum aantal pogingen is Opnieuw proberen * longRetry.<br/><br/>Als we bijvoorbeeld de volgende instellingen in het activiteitenbeleid hebben:<br/>Opnieuw proberen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Stel dat er slechts één segment moet worden uitgevoerd (status is Wachten) en dat de activiteitsuitvoering elke keer mislukt. Aanvankelijk zouden er 3 opeenvolgende uitvoeringspogingen zijn. Na elke poging wordt de segmentstatus opnieuw geprobeerd. Nadat de eerste 3 pogingen voorbij zijn, zou de segmentstatus LongRetry zijn.<br/><br/>Na een uur (dat wil zeggen, longRetryInteval's waarde), zou er een andere set van 3 opeenvolgende uitvoeringspogingen. Daarna is de segmentstatus mislukt en worden er geen pogingen meer geprobeerd. Vandaar dat er in totaal 6 pogingen werden gedaan.<br/><br/>Als een uitvoering slaagt, is de segmentstatus gereed en worden er geen pogingen meer geprobeerd.<br/><br/>longRetry kan worden gebruikt in situaties waarin afhankelijke gegevens op niet-deterministische tijden aankomen of de algehele omgeving schilferig is waaronder gegevensverwerking plaatsvindt. In dergelijke gevallen, het doen van pogingen een na de ander kan niet helpen en doen na een interval van tijd resulteert in de gewenste output.<br/><br/>Woord van voorzichtigheid: stel geen hoge waarden in voor longRetry of longRetryInterval. Hogere waarden impliceren doorgaans andere systemische problemen. |
| longRetryInterval |TimeSpan |00:00:00 |De vertraging tussen lange pogingen om opnieuw te proberen |

Zie Artikel [Pijplijnen voor](data-factory-create-pipelines.md) meer informatie. 

## <a name="parallel-processing-of-data-slices"></a>Parallelle verwerking van gegevenssegmenten
U in het verleden de begindatum voor de pijplijn instellen. Wanneer u dit doet, berekent Data Factory automatisch (terugvullingen) alle gegevenssegmenten in het verleden en begint deze te verwerken. Bijvoorbeeld: als u een pijplijn maakt met begindatum 2017-04-01 en de huidige datum 2017-04-10 is. Als de cadans van de uitvoergegevensset dagelijks is, begint Data Factory met het direct verwerken van alle segmenten van 2017-04-01 tot 2017-04-09 omdat de begindatum in het verleden is. Het segment van 2017-04-10 is nog niet verwerkt omdat de waarde van de eigenschap stijl in de sectie beschikbaarheid standaard EndOfInterval is. Het oudste segment wordt eerst verwerkt als de standaardwaarde van executionPriorityOrder is OldestFirst. Zie de beschikbaarheidssectie van de [gegevensset voor](#dataset-availability) een beschrijving van de eigenschap stijl. Zie de sectie [Activiteitenbeleid](#activity-policies) voor een beschrijving van de sectie executionPriorityOrder. 

U back-gevulde gegevenssegmenten configureren die parallel worden verwerkt door de eigenschap **gelijktijdigheid** in te stellen in het **beleidsgedeelte** van de activiteit JSON. Deze eigenschap bepaalt het aantal parallelle activiteitsuitvoeringen dat op verschillende segmenten kan plaatsvinden. De standaardwaarde voor de eigenschap gelijktijdigheid is 1. Daarom wordt één segment standaard verwerkt. De maximale waarde is 10. Wanneer een pijplijn een grote set beschikbare gegevens moet doorlopen, versnelt een grotere gelijktijdigheidswaarde de gegevensverwerking. 

## <a name="rerun-a-failed-data-slice"></a>Een mislukt gegevenssegment opnieuw uitvoeren
Wanneer er een fout optreedt tijdens het verwerken van een gegevenssegment, u achterhalen waarom de verwerking van een segment is mislukt met Azure-portalblades of Monitor en App beheren. Zie [Pijplijnen bewaken en beheren met Azure-portalblades](data-factory-monitor-manage-pipelines.md) of [de app Monitoring and Management](data-factory-monitor-manage-app.md) voor meer informatie.

Denk aan het volgende voorbeeld, waarin twee activiteiten worden weergegeven. Activiteit1 en Activiteit 2. Activity1 verbruikt een stukje Gegevensset1 en produceert een segment van Gegevensset2, dat wordt verbruikt als input van Activity2 om een deel van de uiteindelijke gegevensset te produceren.

![Mislukt segment](./media/data-factory-scheduling-and-execution/failed-slice.png)

Het diagram laat zien dat van de drie recente segmenten, was er een storing produceren van de 9-10 AM slice voor Dataset2. Data Factory houdt automatisch de afhankelijkheid bij voor de gegevensset van de tijdreeks. Als gevolg hiervan start het niet de activiteit run voor de 9-10 AM downstream slice.

Met monitoring- en beheertools voor Gegevensfabriek u inzoomen op de diagnostische logboeken voor het mislukte segment om eenvoudig de hoofdoorzaak voor het probleem te vinden en op te lossen. Nadat u het probleem hebt opgelost, u de activiteitsrun eenvoudig starten om het mislukte segment te produceren. Zie [Pijplijnen controleren en beheren met Azure-portalblades](data-factory-monitor-manage-pipelines.md) of de app Monitoring and [Management](data-factory-monitor-manage-app.md)voor meer informatie over het opnieuw uitvoeren en begrijpen van statusovergangen voor gegevenssegmenten.

Nadat u het segment 9-10 AM voor **Gegevensset2**opnieuw hebt uitgevoerd, start Data Factory de run voor het segment 9-10 AM afhankelijk van de uiteindelijke gegevensset.

![Mislukt segment opnieuw uitvoeren](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijplijn
Een pijplijn kan echter meer dan één activiteit hebben. Als u meerdere activiteiten in een pijplijn hebt en de uitvoer van een activiteit geen invoer van een andere activiteit is, kunnen de activiteiten parallel worden uitgevoerd als invoergegevenssegmenten voor de activiteiten gereed zijn.

U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. De activiteiten kunnen zich in dezelfde pijplijn of in verschillende pijplijnen bevinden. De tweede activiteit wordt alleen uitgevoerd wanneer de eerste activiteit is voltooid.

Houd bijvoorbeeld rekening met het volgende geval waarin een pijplijn twee activiteiten heeft:

1. Activiteit A1 waarvoor externe invoergegevensset D1 vereist is en uitvoergegevensset D2 produceert.
2. Activiteit A2 waarvoor invoer vereist is van gegevensset D2 en die uitvoergegevensset D3 produceert.

In dit scenario bevinden activiteiten A1 en A2 zich in dezelfde pijplijn. De activiteit A1 wordt uitgevoerd wanneer de externe gegevens beschikbaar zijn en de geplande beschikbaarheidsfrequentie is bereikt. De activiteit A2 wordt uitgevoerd wanneer de geplande segmenten van D2 beschikbaar zijn en de geplande beschikbaarheidsfrequentie is bereikt. Als er een fout optreedt in een van de segmenten in gegevensset D2, wordt A2 niet uitgevoerd voor dat segment totdat deze beschikbaar is.

De diagramweergave met beide activiteiten in dezelfde pijplijn ziet eruit als het volgende diagram:

![Ketenactiviteiten in dezelfde pijplijn](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Zoals eerder vermeld, kunnen de activiteiten zich in verschillende pijpleidingen behouden. In een dergelijk scenario ziet de diagramweergave er als volgt uit:

![Ketenactiviteiten in twee pijpleidingen](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zie de sectie kopie opeenvolgend in de bijlage voor een voorbeeld.

## <a name="model-datasets-with-different-frequencies"></a>Modeldatasets met verschillende frequenties
In de voorbeelden waren de frequenties voor invoer- en uitvoergegevenssets en het venster activiteitsplanning hetzelfde. Sommige scenario's vereisen de mogelijkheid om output te produceren op een andere frequentie dan de frequenties van een of meer ingangen. Data Factory ondersteunt het modelleren van deze scenario's.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Voorbeeld 1: Een dagelijks uitvoerrapport produceren voor invoergegevens die elk uur beschikbaar zijn
Overweeg een scenario waarin u invoermeetgegevens hebt van sensoren die elk uur beschikbaar zijn in Azure Blob-opslag. U wilt een dagelijks totaalrapport produceren met statistieken zoals gemiddelde, maximum en minimum voor de dag met [gegevensfabrieknetactiviteit](data-factory-hive-activity.md).

U dit scenario als volgt modelleren met Data Factory:

**Invoergegevensset**

De invoerbestanden per uur worden voor de opgegeven dag in de map verwijderd. Beschikbaarheid voor invoer is ingesteld op **uur** (frequentie: uur, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uitvoergegevensset**

Elke dag wordt er één uitvoerbestand gemaakt in de map van de dag. Beschikbaarheid van de uitvoer is ingesteld op **dag** (frequentie: dag en interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Activiteit: bijenkorfactiviteit in een pijplijn**

Het hivescript ontvangt de juiste *DateTime-informatie* als parameters die de variabele **WindowStart** gebruiken zoals weergegeven in het volgende fragment. Het hivescript gebruikt deze variabele om de gegevens uit de juiste map voor de dag te laden en de aggregatie uit te voeren om de uitvoer te genereren.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

In het volgende diagram wordt het scenario weergegeven vanuit het oogpunt van gegevensafhankelijkheid.

![Gegevensafhankelijkheid](./media/data-factory-scheduling-and-execution/data-dependency.png)

Het uitvoersegment voor elke dag is afhankelijk van 24 segmenten per uur van een invoergegevensset. Data Factory berekent deze afhankelijkheden automatisch door de invoergegevenssegmenten op te zoeken die in dezelfde periode vallen als het uitvoersegment dat moet worden geproduceerd. Als een van de 24 invoersegmenten niet beschikbaar is, wacht Data Factory tot het invoersegment klaar is voordat de dagelijkse activiteit wordt gestart.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Voorbeeld 2: Afhankelijkheid opgeven met expressies en Data Factory-functies
Laten we eens kijken naar een ander scenario. Stel dat u een bijenkorfactiviteit hebt die twee invoergegevenssets verwerkt. Een van hen heeft dagelijks nieuwe gegevens, maar een van hen krijgt elke week nieuwe gegevens. Stel dat u een join over de twee ingangen wilt doen en elke dag een uitvoer wilt produceren.

De eenvoudige aanpak waarbij Data Factory automatisch de juiste invoersegmenten uitzoekt om te verwerken door uit te lijnen op de periode van de uitvoergegevensslice werkt niet.

U moet opgeven dat voor elke activiteit sto;tot verwerking het gegevenssegment van vorige week moet worden gebruikt voor de wekelijkse invoergegevensset. U gebruikt Azure Data Factory-functies zoals weergegeven in het volgende fragment om dit gedrag te implementeren.

**Invoer1: Azure blob**

De eerste invoer is de Azure-blob die dagelijks wordt bijgewerkt.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Invoer2: Azure-blob**

Input2 is de Azure-blob die wekelijks wordt bijgewerkt.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Uitvoer: Azure-blob**

Elke dag wordt er één uitvoerbestand gemaakt in de map voor de dag. Beschikbaarheid van de uitvoer is ingesteld op **dag** (frequentie: Dag, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Activiteit: bijenkorfactiviteit in een pijplijn**

De korf activiteit neemt de twee ingangen en produceert een output slice elke dag. U het uitvoersegment van elke dag opgeven om afhankelijk te zijn van het invoersegment van de vorige week voor wekelijkse invoer als volgt.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Zie [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md) voor een lijst met functies en systeemvariabelen die Data Factory ondersteunt.

## <a name="appendix"></a>Bijlage

### <a name="example-copy-sequentially"></a>Voorbeeld: achtereenvolgens kopiëren
Het is mogelijk om meerdere kopieerbewerkingen achter elkaar uit te voeren op een sequentiële /geordende manier. U bijvoorbeeld twee kopieeractiviteiten in een pijplijn (CopyActivity1 en CopyActivity2) hebben met de volgende gegevenssets voor invoergegevensuitvoer:   

CopyActivity1

Invoer: gegevensset. Uitvoer: Gegevensset2.

CopyActivity2

Invoer: Gegevensset2.  Uitvoer: Gegevensset3.

CopyActivity2 wordt alleen uitgevoerd als de CopyActivity1 is uitgevoerd en Gegevensset2 beschikbaar is.

Hier is de voorbeeldpijplijn JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

In het voorbeeld wordt de uitvoergegevensset van de eerste kopieeractiviteit (Gegevensset2) opgegeven als invoer voor de tweede activiteit. Daarom wordt de tweede activiteit alleen uitgevoerd wanneer de uitvoergegevensset van de eerste activiteit klaar is.  

In het voorbeeld kan CopyActivity2 een andere invoer hebben, zoals Gegevensset3, maar u geeft Gegevensset2 op als invoer voor CopyActivity2, zodat de activiteit niet wordt uitgevoerd totdat CopyActivity1 is voltooid. Bijvoorbeeld:

CopyActivity1

Invoer: Gegevensset1. Uitvoer: Gegevensset2.

CopyActivity2

Ingangen: Dataset3, Dataset2. Uitvoer: Gegevensset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

In het voorbeeld worden twee invoergegevenssets opgegeven voor de tweede kopieeractiviteit. Wanneer meerdere ingangen zijn opgegeven, wordt alleen de eerste invoergegevensset gebruikt voor het kopiëren van gegevens, maar andere gegevenssets worden gebruikt als afhankelijkheden. CopyActivity2 zou pas beginnen nadat aan de volgende voorwaarden is voldaan:

* CopyActivity1 is voltooid en Dataset2 is beschikbaar. Deze gegevensset wordt niet gebruikt bij het kopiëren van gegevens naar Gegevensset4. Het fungeert alleen als een planningsafhankelijkheid voor CopyActivity2.   
* Gegevensset3 is beschikbaar. Deze gegevensset vertegenwoordigt de gegevens die naar de bestemming worden gekopieerd. 
