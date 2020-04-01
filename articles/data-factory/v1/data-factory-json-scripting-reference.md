---
title: Azure Data Factory - NASLAG-scripting VOOR JSON
description: Biedt JSON-schema's voor entiteiten in gegevensfabrieken.
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
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346600"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - NASLAG-scripting VOOR JSON
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory.


In dit artikel vindt u JSON-schema's en voorbeelden voor het definiëren van Azure Data Factory-entiteiten (pijplijn, activiteit, gegevensset en gekoppelde service).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pijplijn
De structuur op hoog niveau voor een pijplijndefinitie is als volgt:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Volgende tabel beschrijft de eigenschappen binnen de JSON-definitie van pijplijn:

| Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
| name | Naam van de pijplijn. Een naam opgeven die de actie vertegenwoordigt die de activiteit of pijplijn heeft geconfigureerd om te doen<br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letternummer,\_of een underscore ( )</li><li>Volgende tekens zijn niet toegestaan: "",+,','?",,/',<',>',"",","","&,"""""","","","","\\</li></ul> |Ja |
| description |Tekst waarin wordt beschreven waarvoor de activiteit of pijplijn wordt gebruikt | Nee |
| activities | Bevat een lijst met activiteiten. | Ja |
| start |Begindatumtijd voor de pijplijn. Moet in [ISO-formaat](https://en.wikipedia.org/wiki/ISO_8601)zijn . Bijvoorbeeld: 2014-10-14T16:32:41. <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier is een `2016-02-27T06:00:00**-05:00`voorbeeld: , dat is 6 AM EST.<br/><br/>De eigenschappen begin en eind geven samen een actieve periode voor de pijplijn op. Uitvoersegmenten worden alleen geproduceerd met in deze actieve periode. |Nee<br/><br/>Als u een waarde opgeeft voor de eigenschap end, moet u de waarde opgeven voor de eigenschap start.<br/><br/>De begin- en eindtijden kunnen beide leeg zijn om een pijplijn te maken. U moet beide waarden opgeven om een actieve periode in te stellen waarop de pijplijn moet worden uitgevoerd. Als u de begin- en eindtijden bij het maken van een pijplijn niet opgeeft, u deze later instellen met de cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| beëindigen |Einddatum voor de pijplijn. Indien opgegeven moet in ISO-formaat. Bijvoorbeeld: 2014-10-14T17:32:41 <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier is een `2016-02-27T06:00:00**-05:00`voorbeeld: , dat is 6 AM EST.<br/><br/>Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u 9999-09-09 op als waarde voor de eigenschap end. |Nee <br/><br/>Als u een waarde opgeeft voor de eigenschap start, moet u de waarde opgeven voor de eigenschap end.<br/><br/>Zie notities voor de **eigenschap start.** |
| isOnderbrokened |Als ingesteld op true wordt de pijplijn niet uitgevoerd. Standaardwaarde = false. U deze eigenschap gebruiken om deze eigenschap in te schakelen of uit te schakelen. |Nee |
| pipelineMode |De methode voor het plannen wordt uitgevoerd voor de pijplijn. Toegestane waarden zijn: gepland (standaard), eenmalig.<br/><br/>'Gepland' geeft aan dat de pijplijn wordt uitgevoerd met een opgegeven tijdsinterval op basis van de actieve periode (begin- en eindtijd). 'Eenmalig' geeft aan dat de pijplijn slechts één keer wordt uitgevoerd. Eenmalige pijplijnen die eenmaal zijn gemaakt, kunnen momenteel niet worden gewijzigd/bijgewerkt. Zie [Eenmalige pijplijn](data-factory-create-pipelines.md#onetime-pipeline) voor meer informatie over eenmalige instelling. |Nee |
| vervaldatumTijd |Duur van de tijd na de aanmaak waarvoor de pijplijn geldig is en moet blijven beplaatsd. Als er geen actieve, mislukte of lopende uitvoeringen zijn, wordt de pijplijn automatisch verwijderd zodra deze de vervaldatum heeft bereikt. |Nee |


## <a name="activity"></a>Activiteit
De structuur op hoog niveau voor een activiteit binnen een pijplijndefinitie (activiteitenelement) is als volgt:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

Volgende tabel beschrijft de eigenschappen binnen de activiteit JSON-definitie:

| Label | Beschrijving | Vereist |
| --- | --- | --- |
| name |De naam van de activiteit. Een naam opgeven die de actie vertegenwoordigt die is geconfigureerd om te doen<br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letternummer,\_of een underscore ( )</li><li>Volgende tekens zijn niet toegestaan: "",+,','?",,/',<',>',"",","","&,"""""","","","","\\</li></ul> |Ja |
| description |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt. |Nee |
| type |Hiermee geeft u het type activiteit op. Zie de secties [DATA STORES](#data-stores) en DATA [TRANSFORMATION ACTIVITIES](#data-transformation-activities) voor verschillende soorten activiteiten. |Ja |
| Ingangen |Invoertabellen die door de activiteit worden gebruikt<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nee voor HDInsightStreaming- en SqlServerStoredProcedure-activiteiten <br/> <br/> Ja voor alle anderen |
| Uitgangen |Uitvoertabellen die door de activiteit worden gebruikt.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |De naam van de gekoppelde service die door de activiteit wordt gebruikt. <br/><br/>Een activiteit kan vereisen dat u de gekoppelde service opgeeft die is gekoppeld aan de vereiste rekenomgeving. |Ja voor HDInsight-activiteiten, Azure Machine Learning-activiteiten en Opgeslagen procedureactiviteit. <br/><br/>Nee voor alle andere |
| typeProperties |Eigenschappen in de sectie typeEigenschappen zijn afhankelijk van het type activiteit. |Nee |
| policy |Beleidsregels die van invloed zijn op het runtimegedrag van de activiteit. Als dit niet is opgegeven, worden standaardbeleidsregels gebruikt. |Nee |
| scheduler |De eigenschap 'scheduler' wordt gebruikt om de gewenste planning voor de activiteit te definiëren. De subeigenschappen zijn dezelfde als die in de [eigenschap beschikbaarheid in een gegevensset.](data-factory-create-datasets.md#dataset-availability) |Nee |

### <a name="policies"></a>Beleidsregels
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

### <a name="typeproperties-section"></a>typeEigenschappen, sectie
De sectie typeEigenschappen is voor elke activiteit anders. Transformatieactiviteiten hebben alleen de typeeigenschappen. Zie de sectie [ACTIVITEITEN VOOR GEGEVENSTRANSFORMATIE activiteiten](#data-transformation-activities) in dit artikel voor JSON-voorbeelden die transformatieactiviteiten in een pijplijn definiëren.

**Kopieeractiviteit** heeft twee subsecties in de sectie tekstEigenschappen: **bron** en **gootsteen**. Zie de sectie [GEGEVENSopslag](#data-stores) in dit artikel voor JSON-voorbeelden die laten zien hoe u een gegevensarchief als bron en/of gootsteen gebruiken.

### <a name="sample-copy-pipeline"></a>Voorbeeld van kopieerpijplijn
De volgende voorbeeldpijplijn bevat een activiteit van het type **Copy** in de sectie **activities**. In dit voorbeeld [kopieert](data-factory-data-movement-activities.md) de activiteit Kopiëren gegevens uit een Azure Blob-opslag naar een Azure SQL-database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Houd rekening met de volgende punten:

* In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**.
* De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**.
* In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type.

Zie de sectie [GEGEVENSopslag](#data-stores) in dit artikel voor JSON-voorbeelden die laten zien hoe u een gegevensarchief als bron en/of gootsteen gebruiken.

Zie [Zelfstudie: Gegevens kopiëren van Blob-opslag naar SQL-database voor](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)een volledige walkthrough van het maken van deze pijplijn.

### <a name="sample-transformation-pipeline"></a>Voorbeeld van pijplijn voor transformatie
De volgende voorbeeldpijplijn bevat een activiteit van het type **HDInsightHive** in de sectie **activities**. In dit voorbeeld transformeert de [HDInsight Hive-activiteit](data-factory-hive-activity.md) gegevens uit een Azure-blobopslag door een Hive-scriptbestand uit te voeren op een Azure HDInsight Hadoop-cluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
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
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Houd rekening met de volgende punten:

* In het gedeelte activities is er slechts één activiteit waarvan **type** is ingesteld op **HDInsightHive**.
* Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **AzureStorageLinkedService**) en in de map **script** van de container **adfgetstarted**.
* De sectie **definieert** wordt gebruikt om de runtime-instellingen op te geven `${hiveconf:inputtable}`die `${hiveconf:partitionedtable}`worden doorgegeven aan het hive-script als Netelconfiguratiewaarden (bijvoorbeeld , ).

Zie de sectie [ACTIVITEITEN VOOR GEGEVENSTRANSFORMATIE activiteiten](#data-transformation-activities) in dit artikel voor JSON-voorbeelden die transformatieactiviteiten in een pijplijn definiëren.

Zie [Zelfstudie: Uw eerste pijplijn maken om gegevens te verwerken met hadoopcluster](data-factory-build-your-first-pipeline.md)voor een volledige walkthrough van het maken van deze pijplijn.

## <a name="linked-service"></a>Gekoppelde service
De structuur op hoog niveau voor een gekoppelde servicedefinitie is als volgt:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Volgende tabel beschrijft de eigenschappen binnen de activiteit JSON-definitie:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| name | Naam van de gekoppelde service. | Ja |
| eigenschappen - type | Type van de gekoppelde service. Bijvoorbeeld: Azure Storage, Azure SQL Database. |
| typeProperties | De sectie typeEigenschappen bevat elementen die verschillen voor elke gegevensarchief of rekenomgeving. Zie sectie gegevensopslag voor alle gekoppelde services en [rekenomgevingen](#compute-environments) voor alle compute linked services |

## <a name="dataset"></a>Gegevensset
Een gegevensset in Azure Data Factory wordt als volgt gedefinieerd:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| name | Naam van de gegevensset. Zie [Azure Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor naamgevingsregels. |Ja |N.v.t. |
| type | Type van de gegevensset. Geef een van de typen op die worden ondersteund door Azure Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). Zie de sectie [GEGEVENSopslag](#data-stores) voor alle gegevensopslag en gegevenssettypen die worden ondersteund door Data Factory. |
| Structuur | Schema van de gegevensset. Het bevat kolommen, hun types, enz. | Nee |N.v.t. |
| typeProperties | Eigenschappen die overeenkomen met het geselecteerde type. Zie [sectie GEGEVENSOPSLAG](#data-stores) voor ondersteunde typen en hun eigenschappen. |Ja |N.v.t. |
| external | Booleaanse vlag om aan te geven of een gegevensset expliciet wordt geproduceerd door een pijplijn in gegevensfabrieken of niet. |Nee |false |
| availability | Hiermee definieert u het verwerkingsvenster of het snijmodel voor de productie van de gegevensset. Zie [Artikel Planning en Uitvoering](data-factory-scheduling-and-execution.md) voor meer informatie over het model voor het snijden in de gegevensset. |Ja |N.v.t. |
| policy |Hiermee definieert u de criteria of de voorwaarde waaraan de werksetsegmenten moeten voldoen. <br/><br/>Zie sectie Gegevenssetbeleid voor meer informatie. |Nee |N.v.t. |

Elke kolom in de **structuursectie** bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de kolom. |Ja |
| type |Gegevenstype van de kolom.  |Nee |
| Cultuur |.NET-gebaseerde cultuur die moet worden gebruikt `Datetime` wanneer `Datetimeoffset`het type is opgegeven en .NET-type is of . De standaardwaarde is `en-us`. |Nee |
| formaat |Opmaaktekenreeks die moet worden gebruikt wanneer `Datetime` tekst `Datetimeoffset`is opgegeven en .NET-type is of . |Nee |

In het volgende voorbeeld bevat `slicetimestamp`de `projectname`gegevensset drie kolommen , en `pageviews` ze zijn van type: Tekenreeks, Tekenreeks en Decimale respectievelijk.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

In de volgende tabel worden eigenschappen beschreven die u gebruiken in de **sectie beschikbaarheid:**

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijdseenheid op voor de productie van gegevenssetsslicen.<br/><br/><b>Ondersteunde frequentie</b>: Minuut, uur, dag, week, maand |Ja |N.v.t. |
| interval |Hiermee geeft u een vermenigvuldiger voor frequentie op<br/><br/>"Frequency x interval" bepaalt hoe vaak het segment wordt geproduceerd.<br/><br/>Als u de gegevensset per uur moet snijden, stelt u <b>Frequentie</b> in op <b>Uur</b>en <b>interval</b> op <b>1</b>.<br/><br/><b>Opmerking:</b>Als u Frequentie als minuut opgeeft, raden we u aan het interval in te stellen op niet minder dan 15 |Ja |N.v.t. |
| stijl |Hiermee geeft u op of het segment aan het begin/einde van het interval moet worden geproduceerd.<ul><li>Begininterval</li><li>EndofInterval</li></ul><br/><br/>Als frequentie is ingesteld op Maand en de stijl is ingesteld op EndOfInterval, wordt het segment op de laatste dag van de maand geproduceerd. Als de stijl is ingesteld op StartOfInterval, wordt het segment op de eerste dag van de maand geproduceerd.<br/><br/>Als de frequentie is ingesteld op Dag en de stijl is ingesteld op EndOfInterval, wordt het segment in het laatste uur van de dag geproduceerd.<br/><br/>Als de frequentie is ingesteld op Uur en de stijl is ingesteld op EndOfInterval, wordt het segment aan het einde van het uur geproduceerd. Voor een segment voor een periode van 13.00 tot 14.00 uur wordt het segment bijvoorbeeld om 14.00 uur geproduceerd. |Nee |EndofInterval |
| anchorDateTime anchorDateTime |Hiermee definieert u de absolute positie in de tijd die door scheduler wordt gebruikt om de grenzen van het segment van de gegevensset te berekenen. <br/><br/><b>Opmerking:</b>Als de AnchorDateTime datumdelen heeft die gedetailleerder zijn dan de frequentie, worden de meer gedetailleerde onderdelen genegeerd. <br/><br/>Als het <b>interval</b> bijvoorbeeld <b>elk uur</b> is (frequentie: uur en interval: 1) en de <b>AnchorDateTime</b> <b>minuten en seconden</b> bevat, worden de <b>minuten- en secondendelen</b> van de AnchorDateTime genegeerd. |Nee |01/01/0001 |
| offset |Tijdspanne waarmee het begin en einde van alle gegevenssetsegmenten worden verschoven. <br/><br/><b>Opmerking:</b>Als zowel anchorDateTime als offset zijn opgegeven, is het resultaat de gecombineerde verschuiving. |Nee |N.v.t. |

In de volgende sectie beschikbaarheid wordt aangegeven dat de uitvoergegevensset per uur (of) invoerset per uur beschikbaar is:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

In **de beleidssectie** in de definitie van gegevenssets worden de criteria of de voorwaarde gedefinieerd waaraan de werksetsegmenten moeten voldoen.

| Policy Name | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimaal FormaatMB |Valideert dat de gegevens in een **Azure-blob** voldoen aan de minimumgroottevereisten (in megabytes). |Azure Blob |Nee |N.v.t. |
| minimumRijen |Valideert dat de gegevens in een **Azure SQL-database** of een **Azure-tabel** het minimumaantal rijen bevatten. |<ul><li>Azure SQL Database</li><li>Azure Table</li></ul> |Nee |N.v.t. |

**Voorbeeld:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Tenzij een gegevensset wordt geproduceerd door Azure Data Factory, moet deze worden gemarkeerd als **extern**. Deze instelling is over het algemeen van toepassing op de ingangen van de eerste activiteit in een pijplijn, tenzij activiteit of pijplijnketen wordt gebruikt.

| Name | Beschrijving | Vereist | Standaardwaarde |
| --- | --- | --- | --- |
| gegevensVertraging |Tijd om de controle op de beschikbaarheid van de externe gegevens voor het opgegeven segment uit te stellen. Als de gegevens bijvoorbeeld elk uur beschikbaar zijn, kan de controle om te zien dat de externe gegevens beschikbaar zijn en het bijbehorende segment Gereed is, worden vertraagd door gegevens te gebruikenVertraging.<br/><br/>Geldt alleen voor de huidige tijd.  Als het nu bijvoorbeeld 13:00 uur is en deze waarde 10 minuten is, begint de validatie om 13:10 uur.<br/><br/>Deze instelling heeft geen invloed op segmenten in het verleden (segmenten met Slice End Time + dataDelay < Now) worden zonder vertraging verwerkt.<br/><br/>Tijd groter dan 23:59 uur `day.hours:minutes:seconds` moet worden opgegeven met behulp van de indeling. Als u bijvoorbeeld 24 uur wilt opgeven, gebruikt u geen 24:00:00; in plaats daarvan 1.00:00:00 gebruiken. Als u 24:00:00 uur gebruikt, wordt het behandeld als 24 dagen (24.00:00:00). Geef 1 dag en 4 uur lang 1:04:00:00 op. |Nee |0 |
| retryInterval |De wachttijd tussen een fout en de volgende poging opnieuw proberen. Als een poging mislukt, is de volgende poging na opnieuw proberenInterval. <br/><br/>Als het nu 13:00 uur is, beginnen we met de eerste poging. Als de duur om de eerste validatiecontrole te voltooien 1 minuut is en de bewerking is mislukt, is de volgende poging om 1:00 + 1 min (duur) + 1 min (retry interval) = 1:02 PM. <br/><br/>Voor plakjes in het verleden is er geen vertraging. De nieuwe poging gebeurt onmiddellijk. |Nee |00:01:00 (1 minuut) |
| tryTime-out opnieuw proberen |De time-out voor elke poging opnieuw proberen.<br/><br/>Als deze eigenschap is ingesteld op 10 minuten, moet de validatie binnen 10 minuten zijn voltooid. Als het langer duurt dan 10 minuten om de validatie uit te voeren, is het opnieuw proberen een keer uit te gaan.<br/><br/>Als alle pogingen voor de validatie een time-out hebben, wordt het segment gemarkeerd als TimedOut. |Nee |00:10:00 uur (10 minuten) |
| maximumRetry |Aantal keren om te controleren op de beschikbaarheid van de externe gegevens. De toegestane maximumwaarde is 10. |Nee |3 |


## <a name="data-stores"></a>GEGEVENSOPSLAG
De [sectie Gekoppelde service](#linked-service) bevat beschrijvingen voor JSON-elementen die gemeenschappelijk zijn voor alle soorten gekoppelde services. In deze sectie vindt u informatie over JSON-elementen die specifiek zijn voor elk gegevensarchief.

In de sectie [Gegevensset](#dataset) worden beschrijvingen gegeven voor JSON-elementen die gemeenschappelijk zijn voor alle typen gegevenssets. In deze sectie vindt u informatie over JSON-elementen die specifiek zijn voor elk gegevensarchief.

De sectie [Activiteit](#activity) bevat beschrijvingen voor JSON-elementen die gemeenschappelijk zijn voor alle soorten activiteiten. In deze sectie vindt u informatie over JSON-elementen die specifiek zijn voor elk gegevensarchief wanneer deze wordt gebruikt als bron/gootsteen in een kopieeractiviteit.

Klik op de koppeling voor de winkel waarin u geïnteresseerd bent om de JSON-schema's voor gekoppelde service, gegevensset en de bron/gootsteen voor de kopieeractiviteit te bekijken.

| Categorie | Gegevensarchief
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL-gegevensmagazijn](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Databases** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB MongoDB](#mongodb) |
| **Bestand** |[Amazon S3](#amazon-s3) |
| &nbsp; |[File System](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Anderen** |[HTTP (HTTP)](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[SalesForce](#salesforce) |
| &nbsp; |Webtabel |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Gekoppelde service
Er zijn twee typen gekoppelde services: Azure Storage linked service en Azure Storage SAS linked service.

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Als u uw Azure-opslagaccount wilt koppelen aan een gegevensfabriek met behulp van de **accountsleutel,** maakt u een gekoppelde Azure Storage-service. Als u een gekoppelde Azure Storage-service wilt definiëren, stelt u het **type** gekoppelde service in op **AzureStorage.** Vervolgens u de volgende eigenschappen opgeven in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met Azure-opslag voor de eigenschap connectionString. |Ja |

##### <a name="example"></a>Voorbeeld

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>SAS Linked-service voor Azure Storage
Met de gekoppelde Azure Storage SAS-service u een Azure Storage-account koppelen aan een Azure-gegevensfabriek met behulp van een SAS (Shared Access Signature). Het biedt de gegevensfabriek beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. Als u uw Azure-opslagaccount wilt koppelen aan een gegevensfabriek met Behulp van Gedeelde toegangshandtekening, maakt u een SAS-gekoppelde service voor Azure Storage. Als u een SAS-gekoppelde service voor Azure Storage wilt definiëren, stelt u het **type** gekoppelde service in op **AzureStorageSas**. Vervolgens u de volgende eigenschappen opgeven in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| sasUri sasUri |Geef URI voor gedeelde toegangshandtekening op de Azure Storage-bronnen zoals blob, container of tabel. |Ja |

##### <a name="example"></a>Voorbeeld

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Zie het artikel Azure [Blob Storage-connector](data-factory-azure-blob-connector.md#linked-service-properties) voor meer informatie over deze gekoppelde services.

### <a name="dataset"></a>Gegevensset
Als u een Azure Blob-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureBlob**. Geef vervolgens de volgende Azure Blob-specifieke eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de container en map in de blobopslag. Voorbeeld: myblobcontainer\myblobfolder\ |Ja |
| fileName |Naam van de blob. fileName is optioneel en hoofdlettergevoelig.<br/><br/>Als u een bestandsnaam opgeeft, werkt de activiteit (inclusief Kopiëren) op de specifieke Blob.<br/><br/>Wanneer bestandsnaam niet is opgegeven, bevat Kopiëren alle Blobs in de mapPath voor invoergegevensset.<br/><br/>Wanneer fileName niet is opgegeven voor een uitvoergegevensset, wordt de naam `Data.<Guid>.txt` van het gegenereerde bestand in de volgende indeling weergegeven: (bijvoorbeeld: ::Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy is een optionele eigenschap. U deze gebruiken om een dynamische mapPath en bestandsnaam op te geven voor tijdreeksgegevens. MapPath kan bijvoorbeeld worden geparameteriseerd voor elk uur aan gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Zie het artikel [azure blob-connector](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie.

### <a name="blobsource-in-copy-activity"></a>BlobSource in kopieeractiviteit
Als u gegevens kopieert uit een Azure Blob Storage, stelt u het **brontype** van de kopieeractiviteit in op **BlobSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True (standaardwaarde), Onwaar |Nee |

#### <a name="example-blobsource"></a>Voorbeeld: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink in kopieeractiviteit
Als u gegevens kopieert naar een Azure Blob Storage, stelt u het **gootsteentype** van de kopieeractiviteit in op **BlobSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee definieert u het kopieergedrag wanneer de bron BlobSource of FileSystem is. |<b>BehoudenHiërarchie:</b>behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><br/><b>FlattenHierarchy:</b>alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben een automatisch gegenereerde naam. <br/><br/><b>MergeFiles (standaard):</b> hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam/blob is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. anders zou worden automatisch gegenereerde bestandsnaam. |Nee |

#### <a name="example-blobsink"></a>Voorbeeld: Blobsink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [azure blob-connector](data-factory-azure-blob-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure Data Lake Store-service wilt definiëren, stelt u het type gekoppelde service in op **AzureDataLakeStore**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzureDataLakeStore** | Ja |
| dataLakeStoreUri | Geef informatie op over het Azure Data Lake Store-account. Het is in het `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`volgende formaat: of . | Ja |
| subscriptionId | Azure-abonnements-ID waartoe Data Lake Store behoort. | Vereist voor gootsteen |
| resourceGroupName | De naam van azure-bronnengroep waartoe De Data Lake Store behoort. | Vereist voor gootsteen |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja (voor serviceprincipal-verificatie) |
| servicePrincipalKey | Geef de sleutel van de toepassing op. | Ja (voor serviceprincipal-verificatie) |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja (voor serviceprincipal-verificatie) |
| autorisatie | Klik op De knop **Autoriseren** in de **editor voor gegevensfabrieksonderdelen** en voer uw referentie in die de automatisch gegenereerde autorisatie-URL aan deze eigenschap toewijst. | Ja (voor verificatie van gebruikersreferenties)|
| Sessionid | OAuth-sessie-ID van de OAuth-autorisatiesessie. Elke sessie-ID is uniek en mag slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u Data Factory Editor gebruikt. | Ja (voor verificatie van gebruikersreferenties) |

#### <a name="example-using-service-principal-authentication"></a>Voorbeeld: serviceprincipal-verificatie gebruiken
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Voorbeeld: verificatie van gebruikersreferenties gebruiken
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Zie het artikel [over Azure Data Lake Store-connector](data-factory-azure-datalake-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een azure data lake store-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureDataLakeStore**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| folderPath |Pad naar de container en map in het Azure Data Lake-archief. |Ja |
| fileName |Naam van het bestand in de Azure Data Lake-winkel. fileName is optioneel en hoofdlettergevoelig. <br/><br/>Als u een bestandsnaam opgeeft, werkt de activiteit (inclusief Kopiëren) in het specifieke bestand.<br/><br/>Wanneer bestandsnaam niet is opgegeven, bevat Kopiëren alle bestanden in de mapPath voor invoergegevensset.<br/><br/>Wanneer fileName niet is opgegeven voor een uitvoergegevensset, wordt de naam `Data.<Guid>.txt` van het gegenereerde bestand in de volgende indeling weergegeven: (bijvoorbeeld: ::Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy is een optionele eigenschap. U deze gebruiken om een dynamische mapPath en bestandsnaam op te geven voor tijdreeksgegevens. MapPath kan bijvoorbeeld worden geparameteriseerd voor elk uur aan gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel [over Azure Data Lake Store-connector](data-factory-azure-datalake-connector.md#dataset-properties) voor meer informatie.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-bron in kopieeractiviteit
Als u gegevens kopieert uit een Azure Data Lake Store, stelt u het **brontype** van de kopieeractiviteit in op **AzureDataLakeStoreSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

**AzureDataLakeStoreSource** ondersteunt de volgende **sectie EigenschappentypeProperties:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True (standaardwaarde), Onwaar |Nee |

#### <a name="example-azuredatalakestoresource"></a>Voorbeeld: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [over Azure Data Lake Store-connector](data-factory-azure-datalake-connector.md#copy-activity-properties) voor meer informatie.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store Sink in kopieeractiviteit
Als u gegevens kopieert naar een Azure Data Lake Store, stelt u het **gootsteentype** van de kopieeractiviteit in op **AzureDataLakeStoreSink**en geeft u de volgende eigenschappen op in de **gootsteensectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee geeft u het kopieergedrag op. |<b>BehoudenHiërarchie:</b>behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><br/><b>FlattenHierarchy:</b>alle bestanden uit de bronmap worden gemaakt in het eerste niveau van de doelmap. De doelbestanden worden gemaakt met automatisch gegenereerde naam.<br/><br/><b>MergeFiles</b>: voegt alle bestanden uit de bronmap samen tot één bestand. Als de bestandsnaam/blob is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. anders zou worden automatisch gegenereerde bestandsnaam. |Nee |

#### <a name="example-azuredatalakestoresink"></a>Voorbeeld: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [over Azure Data Lake Store-connector](data-factory-azure-datalake-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure Cosmos DB-service wilt definiëren, stelt u het **type** gekoppelde service in op **DocumentDb**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de Azure Cosmos DB-database. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Zie het artikel [azure cosmos DB-connector](data-factory-azure-documentdb-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure Cosmos DB-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op DocumentDbCollection**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| collectionNaam |Naam van de Azure Cosmos DB-verzameling. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Zie het artikel [azure cosmos DB-connector](data-factory-azure-documentdb-connector.md#dataset-properties) voor meer informatie.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB-verzamelingbron in kopieeractiviteit
Als u gegevens kopieert van een Azure Cosmos DB, stelt u het **brontype** van de kopieeractiviteit in **op DocumentDbCollectionSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| query |Geef de query op om gegevens te lezen. |Querytekenreeks die wordt ondersteund door Azure Cosmos DB. <br/><br/>Voorbeeld: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Als deze niet is opgegeven, wordt de SQL-instructie uitgevoerd:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciaal teken om aan te geven dat het document is genest |Elk karakter. <br/><br/>Azure Cosmos DB is een NoSQL-archief voor JSON-documenten, waar geneste structuren zijn toegestaan. Azure Data Factory stelt de gebruiker in staat om hiërarchie aan te duiden via nestingSeparator, wat '' is. in de bovenstaande voorbeelden. Met de scheidingsteken genereert de kopieeractiviteit het object "Naam" met drie onderliggende elementen Eerste, Midden en Laatste, volgens 'Name.First', 'Name.Middle' en 'Name.Last' in de tabeldefinitie. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB Collection Sink in kopieeractiviteit
Als u gegevens kopieert naar Azure Cosmos DB, stelt u het **gootsteentype** van de kopieeractiviteit in **op DocumentDbCollectionSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| nestingSeparator |Een speciaal teken in de naam van de bronkolom om aan te geven dat genest document nodig is. <br/><br/>Bijvoorbeeld hierboven: `Name.First` in de uitvoertabel produceert de volgende JSON-structuur in het Cosmos DB-document:<br/><br/>"Naam": {<br/>    "Ten eerste": "John"<br/>}, |Teken dat wordt gebruikt voor het scheiden van geneste niveaus.<br/><br/>Standaardwaarde `.` is (stip). |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. <br/><br/>Standaardwaarde `.` is (stip). |
| writeBatchSize |Aantal parallelle aanvragen voor Azure Cosmos DB-service om documenten te maken.<br/><br/>U de prestaties bij het kopiëren van gegevens van/naar Azure Cosmos DB verfijnen met behulp van deze eigenschap. U betere prestaties verwachten wanneer u writeBatchSize verhoogt omdat er meer parallelle aanvragen voor Azure Cosmos DB worden verzonden. U moet echter voorkomen dat throttling die de foutmelding kan gooien: "Verzoeksnelheid is groot".<br/><br/>Beperking wordt bepaald door een aantal factoren, waaronder de grootte van documenten, het aantal termen in documenten, indexeringsbeleid van doelverzameling, enz. Voor kopieerbewerkingen u een betere verzameling (bijvoorbeeld S3) gebruiken om de meeste doorvoer beschikbaar te hebben (2.500 aanvraageenheden per seconde). |Geheel getal |Nee (standaard: 5) |
| writeBatchTimeout |Wacht de tijd voordat de bewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Zie het artikel [azure cosmos DB-connector](data-factory-azure-documentdb-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure SQL Database-service wilt definiëren, stelt u het **type** gekoppelde service in op **AzureSqlDatabase**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de instantie Azure SQL Database voor de eigenschap connectionString. |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Zie het artikel [Azure SQL-connector](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure SQL Database-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureSqlTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de Azure SQL Database-instantie waarnaar de gekoppelde service verwijst. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie het artikel [Azure SQL-connector](data-factory-azure-sql-connector.md#dataset-properties) voor meer informatie.

### <a name="sql-source-in-copy-activity"></a>SQL-bron in kopieeractiviteit
Als u gegevens uit een Azure SQL-database kopieert, stelt u het **brontype** van de kopieeractiviteit in op **SqlSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel [Azure SQL-connector](data-factory-azure-sql-connector.md#copy-activity-properties) voor meer informatie.

### <a name="sql-sink-in-copy-activity"></a>SQL Sink in kopieeractiviteit
Als u gegevens kopieert naar Azure SQL Database, stelt u het **sinktype** van de kopieeractiviteit in op **SqlSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef een query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Geef een kolomnaam op die activiteit kopieert om te vullen met automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer u opnieuw wordt uitgevoerd. |Kolomnaam van een kolom met gegevenstype binaire(32). |Nee |
| sqlWriterStoredProcedureName |Naam van de opgeslagen procedure die upserts (updates/inserts) gegevens in de doeltabel. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| sqlWriterTableType sqlWriterTableType |Geef een tabeltypenaam op die in de opgeslagen procedure moet worden gebruikt. Als kopieeractiviteit de gegevens die worden verplaatst beschikbaar zijn in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Een tabeltypenaam. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [Azure SQL-connector](data-factory-azure-sql-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure SQL Data Warehouse-service wilt definiëren, stelt u het **type** gekoppelde service in op **AzureSqlDW**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de instantie Azure SQL Data Warehouse voor de eigenschap connectionString. |Ja |



#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Zie het artikel [azure SQL Data Warehouse-connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure SQL Data Warehouse-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureSqlDWTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de Azure SQL Data Warehouse-database waarnaar de gekoppelde service verwijst. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel [azure SQL Data Warehouse-connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) voor meer informatie.

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-bron in kopieeractiviteit
Als u gegevens kopieert uit Azure SQL Data Warehouse, stelt u het **brontype** van de kopieeractiviteit in op **SqlDWSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [azure SQL Data Warehouse-connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) voor meer informatie.

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW-sink in kopieeractiviteit
Als u gegevens kopieert naar Azure SQL Data Warehouse, stelt u het **sinktype** van de kopieeractiviteit in op **SqlDWSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef een query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. |Een queryverklaring. |Nee |
| allowPolyBase |Geeft aan of polybase (indien van toepassing) moet worden gebruikt in plaats van bulkinsert-mechanisme. <br/><br/> **Het gebruik van PolyBase is de aanbevolen manier om gegevens in SQL Data Warehouse te laden.** |True <br/>False (standaard) |Nee |
| polyBaseInstellingen |Een groep eigenschappen die kan worden opgegeven wanneer de eigenschap **allowPolybase** is ingesteld op **true**. |&nbsp; |Nee |
| rejectValue |Hiermee geeft u het aantal of het percentage rijen op dat kan worden geweigerd voordat de query mislukt. <br/><br/>Meer informatie over de afwijsopties van PolyBase vindt u in de sectie **Argumenten** van het onderwerp [TRANSact-SQL maken.](https://msdn.microsoft.com/library/dn935021.aspx) |0 (standaard), 1, 2, ... |Nee |
| rejectType |Hiermee geeft u op of de optie rejectValue is opgegeven als een letterlijke waarde of een percentage. |Waarde (standaard), Percentage |Nee |
| rejectSampleValue |Hiermee bepaalt u het aantal rijen dat moet worden opgehaald voordat de PolyBase het percentage afgewezen rijen opnieuw berekent. |1, 2, ... |Ja, als **rejectType** **percentage** is |
| useTypeDefault |Hiermee geeft u op hoe ontbrekende waarden in afgebakende tekstbestanden moeten worden verwerkt wanneer PolyBase gegevens uit het tekstbestand ophaalt.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie Argumenten in [EXTERNE BESTANDSINDELING MAKEN (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx) |True, False (standaard) |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [azure SQL Data Warehouse-connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Gekoppelde service
Als u een azure cognitive search-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **AzureSearch**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| url | URL voor de zoekservice. | Ja |
| sleutel | Beheerderssleutel voor de zoekservice. | Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Zie het artikel [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een azure cognitive search-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureSearchIndex**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet zijn ingesteld op **AzureSearchIndex**.| Ja |
| indexNaam | Naam van de zoekindex. Data Factory maakt de index niet. De index moet bestaan in Azure Cognitive Search. | Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Zie het artikel [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#dataset-properties) voor meer informatie.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Cognitive Search Index Sink in Copy Activity
Als u gegevens kopieert naar een zoekindex, stelt u het **gootsteentype** van de kopieeractiviteit in op **AzureSearchIndexSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| -------- | ----------- | -------------- | -------- |
| Schrijfgedrag | Hiermee geeft u op of een document al in de index moet worden samengevoegd of vervangen. | Samenvoegen (standaard)<br/>Uploaden| Nee |
| Schrijfbatchgrootte | Uploadt gegevens in de zoekindex wanneer de buffergrootte writeBatchSize bereikt. | 1 tot 1000. De standaardwaarde is 1000. | Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-table-storage"></a>Azure-tabelopslag

### <a name="linked-service"></a>Gekoppelde service
Er zijn twee typen gekoppelde services: Azure Storage linked service en Azure Storage SAS linked service.

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Als u uw Azure-opslagaccount wilt koppelen aan een gegevensfabriek met behulp van de **accountsleutel,** maakt u een gekoppelde Azure Storage-service. Als u een gekoppelde Azure Storage-service wilt definiëren, stelt u het **type** gekoppelde service in op **AzureStorage.** Vervolgens u de volgende eigenschappen opgeven in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet zijn ingesteld op: **AzureStorage** |Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met Azure-opslag voor de eigenschap connectionString. |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>SAS Linked-service voor Azure Storage
Met de gekoppelde Azure Storage SAS-service u een Azure Storage-account koppelen aan een Azure-gegevensfabriek met behulp van een SAS (Shared Access Signature). Het biedt de gegevensfabriek beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. Als u uw Azure-opslagaccount wilt koppelen aan een gegevensfabriek met Behulp van Gedeelde toegangshandtekening, maakt u een SAS-gekoppelde service voor Azure Storage. Als u een SAS-gekoppelde service voor Azure Storage wilt definiëren, stelt u het **type** gekoppelde service in op **AzureStorageSas**. Vervolgens u de volgende eigenschappen opgeven in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet zijn ingesteld op: **AzureStorageSas** |Ja |
| sasUri sasUri |Geef URI voor gedeelde toegangshandtekening op de Azure Storage-bronnen zoals blob, container of tabel. |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Zie het artikel Azure [Table Storage-connector](data-factory-azure-table-connector.md#linked-service-properties) voor meer informatie over deze gekoppelde services.

### <a name="dataset"></a>Gegevensset
Als u een azure-tabelgegevensset wilt definiëren, stelt u het **type** gegevensset in op **AzureTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie Azure Table Database waarnaar gekoppelde service verwijst. |Ja. Wanneer een tabelNaam is opgegeven zonder azureTableSourceQuery, worden alle records uit de tabel gekopieerd naar de bestemming. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoet, gekopieerd naar de bestemming. |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel Azure [Table Storage-connector](data-factory-azure-table-connector.md#dataset-properties) voor meer informatie over deze gekoppelde services.

### <a name="azure-table-source-in-copy-activity"></a>Azure-tabelbron in kopieeractiviteit
Als u gegevens kopieert uit Azure Table Storage, stelt u het **brontype** van de kopieeractiviteit in op **AzureTableSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableSourceQuery |Gebruik de aangepaste query om gegevens te lezen. |Azure-tabelquerytekenreeks. Zie voorbeelden in de volgende sectie. |Nee. Wanneer een tabelNaam is opgegeven zonder azureTableSourceQuery, worden alle records uit de tabel gekopieerd naar de bestemming. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoet, gekopieerd naar de bestemming. |
| azureTableSourceIgnoreTableNotFound |Geef aan of slik de uitzondering van de tabel niet bestaan. |TRUE<br/>FALSE |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel Azure [Table Storage-connector](data-factory-azure-table-connector.md#copy-activity-properties) voor meer informatie over deze gekoppelde services.

### <a name="azure-table-sink-in-copy-activity"></a>Azure-tabelsink in kopieeractiviteit
Als u gegevens kopieert naar Azure Table Storage, stelt u het **gootsteentype** van de kopieeractiviteit in op **AzureTableSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standaardpartitiesleutelwaarde die door de gootsteen kan worden gebruikt. |Een tekenreekswaarde. |Nee |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitiesleutels. Als dit niet is opgegeven, wordt AzureTableDefaultPartitionKeyValue gebruikt als partitiesleutel. |Een kolomnaam. |Nee |
| azuretablerowkeynaam |Geef de naam op van de kolom waarvan de kolomwaarden als rijsleutel worden gebruikt. Als dit niet is opgegeven, gebruikt u een GUID voor elke rij. |Een kolomnaam. |Nee |
| azureTableInsertType |De modus om gegevens in te voegen in de Azure-tabel.<br/><br/>Met deze eigenschap bepaalt u of bestaande rijen in de uitvoertabel met overeenkomende partitie- en rijsleutels hun waarden hebben vervangen of samengevoegd. <br/><br/>Zie [Entiteit invoegen of samenvoegen](https://msdn.microsoft.com/library/azure/hh452241.aspx) en [Entiteitsonderwerpen invoegen of vervangen](https://msdn.microsoft.com/library/azure/hh452242.aspx) voor meer informatie over hoe deze instellingen werken (samenvoegen en vervangen). <br/><br> Deze instelling is van toepassing op rijniveau, niet op tabelniveau, en geen van beide opties verwijdert rijen in de uitvoertabel die niet in de invoer bestaan. |samenvoegen (standaard)<br/>vervangen |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de Azure-tabel in wanneer de writeBatchSize of writeBatchTimeout wordt geraakt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Hiermee voegt u gegevens in de Azure-tabel in wanneer de writeBatchSize of writeBatchTimeout wordt geraakt |Tijdspanne<br/><br/>Voorbeeld: "00:20:00" (20 minuten) |Nee (Standaard-opslagclientstandaardtime-outwaarde 90 sec) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel Azure [Table Storage-connector](data-factory-azure-table-connector.md#copy-activity-properties) voor meer informatie over deze gekoppelde services.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Amazon Redshift-service wilt definiëren, stelt u het **type** gekoppelde service in op **AmazonRedshift**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |IP-adres of hostnaam van de Amazon Redshift-server. |Ja |
| poort |Het nummer van de TCP-poort dat de Amazon Redshift-server gebruikt om naar clientverbindingen te luisteren. |Nee, standaardwaarde: 5439 |
| database |Naam van de Amazon Redshift database. |Ja |
| gebruikersnaam |Naam van de gebruiker die toegang heeft tot de database. |Ja |
| wachtwoord |Wachtwoord voor het gebruikersaccount. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Zie het artikel over amazon Redshift-connector voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Amazon Redshift-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op RelationalTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de Amazon Redshift-database waarnaar de gekoppelde service verwijst. |Nee (als **query** van **RelationalSource** is opgegeven) |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie het artikel over amazon Redshift-connector voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens van Amazon Redshift kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Zie het artikel over amazon Redshift-connector voor meer informatie.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde IBM DB2-service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesDB2**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de DB2-server. |Ja |
| database |Naam van de DB2-database. |Ja |
| schema |Naam van het schema in de database. De schemanaam is hoofdlettergevoelig. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de DB2-database. Mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u basis- of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises DB2-database. |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Zie het artikel over ibm DB2-connector voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een DB2-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op Relationaltable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie DB2-database waarnaar gekoppelde service verwijst. De tabelNaam is hoofdlettergevoelig. |Nee (als **query** van **RelationalSource** is opgegeven)

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel over ibm DB2-connector voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens van IBM DB2 kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `"query": "select * from "MySchema"."MyTable""`. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Zie het artikel over ibm DB2-connector voor meer informatie.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Gekoppelde service
Als u een MySQL-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesMySql**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de MySQL-server. |Ja |
| database |Naam van de MySQL-database. |Ja |
| schema |Naam van het schema in de database. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de MySQL-database. Mogelijke waarden `Basic`zijn: . |Ja |
| userName |Geef de gebruikersnaam op om verbinding te maken met de MySQL-database. |Ja |
| wachtwoord |Geef een wachtwoord op voor het opgegeven gebruikersaccount. |Ja |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises MySQL-database. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Zie Het artikel [van de MySQL-connector](data-factory-onprem-mysql-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een MySQL-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op Relationaltable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie MySQL Database waarnaar de gekoppelde service verwijst. |Nee (als **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie Het artikel [van de MySQL-connector](data-factory-onprem-mysql-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens uit een MySQL-database kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |


#### <a name="example"></a>Voorbeeld
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie Het artikel [van de MySQL-connector](data-factory-onprem-mysql-connector.md#copy-activity-properties) voor meer informatie.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Gekoppelde service
Als u een oracle-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesOracle**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| driverType | Geef op welk stuurprogramma u moet gebruiken om gegevens van/naar Oracle Database te kopiëren. Toegestane waarden zijn **Microsoft** of **ODP** (standaard). Zie Ondersteunde versie en installatiesectie over stuurprogrammadetails. | Nee |
| Connectionstring | Geef informatie op die nodig is om verbinding te maken met de instantie Oracle Database voor de eigenschap connectionString. | Ja |
| gatewayNaam | Naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Oracle-server |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie het artikel [over oracle-connector](data-factory-onprem-oracle-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Oracle-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **OracleTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de Oracle Database waarnaar de gekoppelde service verwijst. |Nee (als **oracleReaderQuery** van **OracleSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie het artikel [over oracle-connector](data-factory-onprem-oracle-connector.md#dataset-properties) voor meer informatie.

### <a name="oracle-source-in-copy-activity"></a>Oracle-bron in kopieeractiviteit
Als u gegevens uit een Oracle-database kopieert, stelt u het **brontype** van de kopieeractiviteit in op **OracleSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| oracleReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable` <br/><br/>Als deze niet is opgegeven, wordt de SQL-instructie uitgevoerd:`select * from MyTable` |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [over oracle-connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) voor meer informatie.

### <a name="oracle-sink-in-copy-activity"></a>Oracle Sink in kopieeractiviteit
Als u gegevens kopieert naar am Oracle-database, stelt u het **gootsteentype** van de kopieeractiviteit in op **OracleSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: 00:30:00 (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 100) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef een query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Geef kolomnaam op voor Activiteit kopiëren om te vullen met automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen bij het opnieuw uitvoeren. |Kolomnaam van een kolom met gegevenstype binaire(32). |Nee |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel [over oracle-connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) voor meer informatie.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Gekoppelde service
Als u een PostgreSQL-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesPostgreSql**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de PostgreSQL-server. |Ja |
| database |Naam van de PostgreSQL-database. |Ja |
| schema |Naam van het schema in de database. De schemanaam is hoofdlettergevoelig. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de PostgreSQL-database. Mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u basis- of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises PostgreSQL-database. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Zie [PostgreSQL-connectorartikel](data-factory-onprem-postgresql-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een PostgreSQL-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op Relationaltable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie PostgreSQL Database waarnaar de gekoppelde service verwijst. De tabelNaam is hoofdlettergevoelig. |Nee (als **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie [PostgreSQL-connectorartikel](data-factory-onprem-postgresql-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens uit een PostgreSQL-database kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: "query": "selecteer \"*\"uit MySchema . \"MyTable\"". |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [PostgreSQL-connectorartikel](data-factory-onprem-postgresql-connector.md#copy-activity-properties) voor meer informatie.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde SAP Business Warehouse-service (BW) wilt definiëren, stelt u het **type** gekoppelde service in op **SapBw**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | Naam van de server waarop de SAP BW-instantie zich bevindt. | tekenreeks | Ja
systeemAantal | Systeemnummer van het SAP BW-systeem. | Tweecijferig getal met de cimaal weergegeven als een tekenreeks. | Ja
clientId | Client-ID van de client in het SAP W-systeem. | Driecijferig decimaal getal weergegeven als een tekenreeks. | Ja
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Ja
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Ja
gatewayNaam | Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises SAP BW-instantie. | tekenreeks | Ja
versleuteldCredential | De gecodeerde referentietekenreeks. | tekenreeks | Nee

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie het artikel [over SAP Business Warehouse-connectorvoor](data-factory-sap-business-warehouse-connector.md#linked-service-properties) meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SAP BW-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op RelationalTable**. Er worden geen typespecifieke eigenschappen ondersteund voor de SAP BW-gegevensset van type **RelationalTable**.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie het artikel [over SAP Business Warehouse-connectorvoor](data-factory-sap-business-warehouse-connector.md#dataset-properties) meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens kopieert uit SAP Business Warehouse, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de MDX-query op om gegevens uit de instantie SAP BW te lezen. | MDX-query. | Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Zie het artikel [over SAP Business Warehouse-connectorvoor](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) meer informatie.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Gekoppelde service
Als u een SAP HANA-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **SapHana**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | Naam van de server waarop de SAP HANA-instantie zich bevindt. Als uw server een aangepaste `server:port`poort gebruikt, geeft u op . | tekenreeks | Ja
authenticationType | Type verificatie. | Tekenreeks. "Basic" of "Windows" | Ja
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Ja
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Ja
gatewayNaam | Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het on-premises SAP HANA-exemplaar. | tekenreeks | Ja
versleuteldCredential | De gecodeerde referentietekenreeks. | tekenreeks | Nee

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Zie voor meer informatie het artikel [OVER SAP HANA-connector.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Gegevensset
Als u een SAP HANA-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op RelationalTable**. Er worden geen typespecifieke eigenschappen ondersteund voor de SAP HANA-gegevensset van type **RelationalTable**.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie voor meer informatie het artikel [OVER SAP HANA-connector.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens kopieert uit een SAP HANA-gegevensarchief, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de SQL-query op om gegevens uit de SAP HANA-instantie te lezen. | SQL-query. | Ja |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Zie voor meer informatie het artikel [OVER SAP HANA-connector.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Gekoppelde service
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een on-premises SQL Server-database te koppelen aan een gegevensfabriek. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de on-premises SQL Server-gekoppelde service.

In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor sql server-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Verbindingsgegevens opgevenTekenreeksgegevens die nodig zijn om verbinding te maken met de on-premises SQL Server-database met SQL-verificatie of Windows-verificatie. |Ja |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises SQL Server-database. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u Windows-verificatie gebruikt. Voorbeeld: **gebruikersnaam\\domeinnaam**. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |

U referenties versleutelen met de cmdlet **Nieuw-AzDataFactoryEncryptValue** en deze gebruiken in de verbindingstekenreeks zoals weergegeven in het volgende voorbeeld **(eigenschap EncryptedCredential):**

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Voorbeeld: JSON voor het gebruik van SQL-verificatie

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Voorbeeld: JSON voor het gebruik van Windows-verificatie

Als gebruikersnaam en wachtwoord zijn opgegeven, gebruikt gateway deze om zich voor te doen als het opgegeven gebruikersaccount om verbinding te maken met de on-premises SQL Server-database. Anders maakt gateway rechtstreeks verbinding met de SQL Server met de beveiligingscontext van Gateway (het opstartaccount).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie [SQL Server-connectorartikel](data-factory-sqlserver-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SQL Server-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **SqlServerTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel of weergave in de SQL Server Database-instantie waarnaar de gekoppelde service verwijst. |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [SQL Server-connectorartikel](data-factory-sqlserver-connector.md#dataset-properties) voor meer informatie.

### <a name="sql-source-in-copy-activity"></a>Sql-bron in kopieeractiviteit
Als u gegevens uit een SQL Server-database kopieert, stelt u het **brontype** van de kopieeractiviteit in op **SqlSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. Kan verwijzen naar meerdere tabellen uit de database waarnaar wordt verwezen door de invoergegevensset. Als dit niet is opgegeven, wordt de SQL-instructie uitgevoerd: kies uit MyTable. |Nee |
| sqlReaderStoredProcedureName |Naam van de opgeslagen procedure die gegevens uit de brontabel leest. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de kopieeractiviteit deze query uit tegen de SQL Server Database-bron om de gegevens te krijgen.

U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de structuursectie zijn gedefinieerd, gebruikt om een selectiequery te bouwen die wordt uitgevoerd tegen de SQL Server-database. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName**gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **tableName** in de gegevensset JSON. Er zijn echter geen validaties uitgevoerd tegen deze tabel.


#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

In dit voorbeeld wordt **sqlReaderQuery** opgegeven voor de SqlSource. Met de kopieeractiviteit wordt deze query uitgevoerd op basis van de SQL Server-databasebron om de gegevens op te halen. U ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als de opgeslagen procedure parameters neemt). De sqlReaderQuery kan verwijzen naar meerdere tabellen binnen de database waarnaar wordt verwezen door de invoergegevensset. Het is niet beperkt tot alleen de tabel set als tableName typeProperty van de gegevensset.

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de structuursectie zijn gedefinieerd, gebruikt om een selectiequery te bouwen die wordt uitgevoerd tegen de SQL Server Database. Als de gegevenssetdefinitie de structuur niet heeft, worden alle kolommen geselecteerd uit de tabel.

Zie [SQL Server-connectorartikel](data-factory-sqlserver-connector.md#copy-activity-properties) voor meer informatie.

### <a name="sql-sink-in-copy-activity"></a>Sql Sink in kopieeractiviteit
Als u gegevens kopieert naar een SQL Server-database, stelt u het **sinktype** van de kopieeractiviteit in op **SqlSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript sqlWriterCleanupScript |Geef query op voor Activiteit kopiëren om zodanig uit te voeren dat gegevens van een specifiek segment worden opgeschoond. Zie voor meer informatie de sectie herhaalbaarheid. |Een queryverklaring. |Nee |
| sliceIdentifierColumnName |Geef kolomnaam op voor Activiteit kopiëren om te vullen met automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen bij het opnieuw uitvoeren. Zie voor meer informatie de sectie herhaalbaarheid. |Kolomnaam van een kolom met gegevenstype binaire(32). |Nee |
| sqlWriterStoredProcedureName |Naam van de opgeslagen procedure die upserts (updates/inserts) gegevens in de doeltabel. |Naam van de opgeslagen procedure. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure. |Naam/waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| sqlWriterTableType sqlWriterTableType |Geef de naam van de tabeloptekst op die in de opgeslagen procedure moet worden gebruikt. Als kopieeractiviteit de gegevens die worden verplaatst beschikbaar zijn in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Een tabeltypenaam. |Nee |

#### <a name="example"></a>Voorbeeld
De pijplijn bevat een kopieeractiviteit die is geconfigureerd om deze invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [SQL Server-connectorartikel](data-factory-sqlserver-connector.md#copy-activity-properties) voor meer informatie.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Sybase-service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesSybase**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de Sybase-server. |Ja |
| database |Naam van de Sybase-database. |Ja |
| schema |Naam van het schema in de database. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de Sybase-database. Mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u basis- of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises Sybase-database. |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Zie [Artikel sybase-connector](data-factory-onprem-sybase-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Sybase-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op RelationalTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie Sybase Database waarnaar de gekoppelde service verwijst. |Nee (als **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [Artikel sybase-connector](data-factory-onprem-sybase-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens uit een Sybase-database kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **tabelNaam** van **de gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [Artikel sybase-connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) voor meer informatie.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Gekoppelde service
Als u een teradata-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesTeradata**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de Teradata-server. |Ja |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de Teradata-database. Mogelijke waarden zijn: Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u basis- of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises Teradata-database. |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Zie [artikel teradata-connector](data-factory-onprem-teradata-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Teradata Blob-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op RelationalTable**. Momenteel worden er geen typeeigenschappen ondersteund voor de gegevensset Teradata.

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [artikel teradata-connector](data-factory-onprem-teradata-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens uit een Teradata-database kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Zie [artikel teradata-connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) voor meer informatie.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Gekoppelde service
Als u een door Cassandra gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesCassandra**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/><br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om tegelijkertijd verbinding te maken met alle servers. |Ja |
| poort |De TCP-poort die de Cassandra-server gebruikt om naar clientverbindingen te luisteren. |Nee, standaardwaarde: 9042 |
| authenticationType |Basic of Anoniem |Ja |
| gebruikersnaam |Geef de gebruikersnaam op voor het gebruikersaccount. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wachtwoord voor het gebruikersaccount op. |Ja, als authenticationType is ingesteld op Basic. |
| gatewayNaam |De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Cassandra-database. |Ja |
| versleuteldCredential |Referentie versleuteld door de gateway. |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie het artikel [over de Cassandra-connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Cassandra-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **CassandraTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| sleutelruimte |Naam van de keyspace of het schema in de Cassandra-database. |Ja (Als **query** voor **CassandraSource** niet is gedefinieerd). |
| tableName |Naam van de tabel in de Cassandra database. |Ja (Als **query** voor **CassandraSource** niet is gedefinieerd). |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel [over de Cassandra-connector](data-factory-onprem-cassandra-connector.md#dataset-properties) voor meer informatie.

### <a name="cassandra-source-in-copy-activity"></a>Cassandra Bron in Kopieeractiviteit
Als u gegevens van Cassandra kopieert, stelt u het **brontype** van de kopieeractiviteit in op **CassandraSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query of CQL-query. Zie [CQL-verwijzing](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Wanneer u SQL-query gebruikt, geeft u **de naam keyspace name.table** op om de tabel weer te geven die u wilt opvragen. |Nee (als tabelNaam en sleutelruimte op de gegevensset zijn gedefinieerd). |
| consistentieNiveau |Het consistentieniveau geeft aan hoeveel replica's op een leesaanvraag moeten reageren voordat gegevens naar de clienttoepassing worden teruggekeerd. Cassandra controleert het opgegeven aantal replica's op gegevens om aan het leesverzoek te voldoen. |EEN, TWEE, DRIE, QUORUM, ALLES, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Zie [Gegevensconsistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie. |Nee. Standaardwaarde is ÉÉN. |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [over de Cassandra-connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) voor meer informatie.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde MongoDB-service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesMongoDB**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |IP-adres of hostnaam van de MongoDB-server. |Ja |
| poort |TCP-poort die de MongoDB-server gebruikt om naar clientverbindingen te luisteren. |Optioneel, standaardwaarde: 27017 |
| authenticationType |Basic, of Anoniem. |Ja |
| gebruikersnaam |Gebruikersaccount om toegang te krijgen tot MongoDB. |Ja (als basisverificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. |Ja (als basisverificatie wordt gebruikt). |
| Bronauth |Naam van de MongoDB-database die u wilt gebruiken om uw referenties te controleren op verificatie. |Optioneel (als basisverificatie wordt gebruikt). standaard: gebruikt het beheerdersaccount en de database die is opgegeven met de eigenschap databaseName. |
| Databasenaam |Naam van de MongoDB-database die u wilt openen. |Ja |
| gatewayNaam |Naam van de gateway die toegang heeft tot het gegevensarchief. |Ja |
| versleuteldCredential |Referentie versleuteld door gateway. |Optioneel |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie [MongoDB-connectorartikel](data-factory-on-premises-mongodb-connector.md#linked-service-properties) voor meer informatie

### <a name="dataset"></a>Gegevensset
Als u een MongoDB-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **MongoDbCollection**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| collectionNaam |Naam van de collectie in de MongoDB-database. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Zie [MongoDB-connectorartikel](data-factory-on-premises-mongodb-connector.md#dataset-properties) voor meer informatie

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB Bron in Copy Activiteit
Als u gegevens van MongoDB kopieert, stelt u het **brontype** van de kopieeractiviteit in op **MongoDbSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **verzamelingNaam** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [MongoDB-connectorartikel](data-factory-on-premises-mongodb-connector.md#copy-activity-properties) voor meer informatie

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Amazon S3-service wilt definiëren, stelt u het **type** gekoppelde service in op **AwsAccessKey**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| accessKeyID |ID van de geheime toegangssleutel. |tekenreeks |Ja |
| secretAccessKey |De geheime toegangssleutel zelf. |Versleutelde geheime tekenreeks |Ja |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Zie voor meer informatie [het artikel over amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Gegevensset
Als u een Amazon S3-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **AmazonS3**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| bucketName |De Naam van de S3-emmer. |Tekenreeks |Ja |
| sleutel |De S3-objecttoets. |Tekenreeks |Nee |
| Voorvoegsel |Voorvoegsel voor de S3-objecttoets. Objecten waarvan de toetsen met dit voorvoegsel beginnen, zijn geselecteerd. Geldt alleen wanneer de sleutel leeg is. |Tekenreeks |Nee |
| versie |De versie van Het object s3 als S3-versie is ingeschakeld. |Tekenreeks |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee | |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. De ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee | |


> [!NOTE]
> bucketName + toets geeft de locatie aan van het S3-object waar de emmer de hoofdcontainer voor S3-objecten is en de sleutel het volledige pad naar het S3-object is.

#### <a name="example-sample-dataset-with-prefix"></a>Voorbeeld: voorbeeldgegevensset met voorvoegsel

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Voorbeeld: voorbeeldgegevensset (met versie)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Voorbeeld: Dynamische paden voor S3
In het voorbeeld gebruiken we vaste waarden voor sleutel- en bucketName-eigenschappen in de Amazon S3-gegevensset.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

U Data Factory de sleutel en bucketName dynamisch laten berekenen tijdens runtime met behulp van systeemvariabelen zoals SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

U hetzelfde doen voor het voorvoegsel van een Amazon S3-gegevensset. Zie [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md) voor een lijst met ondersteunde functies en variabelen.

Zie voor meer informatie [het artikel over amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteembron in kopieeractiviteit
Als u gegevens van Amazon S3 kopieert, stelt u het **brontype** van de kopieeractiviteit in op **FileSystemSource**en geeft u de volgende eigenschappen op in de **bronsectie:**


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u op of u s3-objecten opnieuw onder de map wilt weergeven. |waar/onwaar |Nee |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Zie voor meer informatie [het artikel over amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Bestandssysteem


### <a name="linked-service"></a>Gekoppelde service
U een on-premises bestandssysteem koppelen aan een Azure-gegevensfabriek met de **on-premises bestandsservergekoppelde** service. In de volgende tabel vindt u beschrijvingen voor JSON-elementen die specifiek zijn voor de on-premises bestandsservergekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |Controleer of de eigenschap type is ingesteld op **OnPremisesFileServer**. |Ja |
| host |Hiermee geeft u het hoofdpad op van de map die u wilt kopiëren. Gebruik het escape character ' \ ' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden. |Ja |
| userid |Geef de id op van de gebruiker die toegang heeft tot de server. |Nee (als u versleuteld referentie kiest) |
| wachtwoord |Geef het wachtwoord voor de gebruiker op (userid). |Nee (als u versleuteld referentiekiest |
| versleuteldCredential |Geef de versleutelde referenties op die u krijgen door de cmdlet Nieuw-AzDataFactoryEncryptValue uit te voeren. |Nee (als u ervoor kiest om userid en wachtwoord op te geven in platte tekst) |
| gatewayNaam |Hiermee geeft u de naam op van de gateway die Gegevensfabriek moet gebruiken om verbinding te maken met de on-premises bestandsserver. |Ja |

#### <a name="sample-folder-path-definitions"></a>Paddefinities van voorbeeldmappen

| Scenario | Host in gekoppelde servicedefinitie | folderPath in gegevenssetdefinitie |
| --- | --- | --- |
| Lokale map op de datamanagementgatewaymachine: <br/><br/>Voorbeelden: D:\\ \* of D:\map\submap\\* |D:\\ \\ (voor Data Management Gateway 2.0 en latere versies) <br/><br/> localhost (voor eerdere versies dan Data Management Gateway 2.0) |. of\\\\mapsubmap (voor Gegevensbeheer Gateway 2.0 en latere versies) \\ \\ <br/><br/>D:\\ \\ of\\\\D: mapsubmap\\\\(voor gatewayversie onder 2.0) |
| Gedeelde map op afstand: <br/><br/>\\ \\Voorbeelden: submap\\\\ \* myserver\\share\\\\of \\ \\myserver share folder\\* |\\\\\\\\myservershare\\\\ |. of mapsubmap\\\\ \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Voorbeeld: gebruikersnaam en wachtwoord gebruiken in platte tekst

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Voorbeeld: versleutelde referenties gebruiken

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie [Artikel Bestandssysteemconnector](data-factory-onprem-file-system-connector.md#linked-service-properties)voor meer informatie .

### <a name="dataset"></a>Gegevensset
Als u een gegevensset Bestandssysteem wilt definiëren, stelt u het **type** gegevensset in **op FileShare**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Hiermee geeft u het subpad naar de map op. Gebruik het escape character '\' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, is de naam van het gegenereerde bestand in de volgende indeling: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de mapPath te selecteren in plaats van alle bestanden. <br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeeld 1: "fileFilter": "*.log"<br/>Voorbeeld 2: "fileFilter": 2016-1-?. txt"<br/><br/>Houd er rekening mee dat fileFilter van toepassing is op een invoerfileShare-gegevensset. |Nee |
| partitionedBy |U partitionedBy gebruiken om een dynamische mapPath/fileName op te geven voor tijdreeksgegevens. Een voorbeeld is folderPath parameterized voor elk uur van gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**; en ondersteunde niveaus zijn: **Optimaal** en **snelste**. zie [Bestands- en compressie-indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nee |

> [!NOTE]
> U fileName en fileFilter niet tegelijkertijd gebruiken.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [Artikel Bestandssysteemconnector](data-factory-onprem-file-system-connector.md#dataset-properties)voor meer informatie .

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteembron in kopieeractiviteit
Als u gegevens kopieert vanuit Bestandssysteem, stelt u het **brontype** van de kopieeractiviteit in op **FileSystemSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie [Artikel Bestandssysteemconnector](data-factory-onprem-file-system-connector.md#copy-activity-properties)voor meer informatie .

### <a name="file-system-sink-in-copy-activity"></a>Bestandsysteem sink in kopieeractiviteit
Als u gegevens kopieert naar bestandssysteem, stelt u het **gootsteentype** van de kopieeractiviteit in op **FileSystemSink**en geeft u de volgende eigenschappen op in de **sectie sink:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee definieert u het kopieergedrag wanneer de bron BlobSource of FileSystem is. |**Hiërarchie behouden:** Hiermee behoudt u de bestandshiërarchie in de doelmap. Dat wil zeggen dat het relatieve pad van het bronbestand naar de bronmap hetzelfde is als het relatieve pad van het doelbestand naar de doelmap.<br/><br/>**Afvlakkenhiërarchie:** Alle bestanden uit de bronmap worden gemaakt in het eerste niveau van de doelmap. De doelbestanden worden gemaakt met een automatisch gegenereerde naam.<br/><br/>**MergeFiles:** Hiermee worden alle bestanden uit de bronmap samengevoegd tot één bestand. Als de bestandsnaam/blobnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. |Nee |

auto-

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [Artikel Bestandssysteemconnector](data-factory-onprem-file-system-connector.md#copy-activity-properties)voor meer informatie .

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Gekoppelde service
Als u een FTP-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **FtpServer**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| host |Naam of IP-adres van de FTP-server |Ja |&nbsp; |
| authenticationType |Verificatietype opgeven |Ja |Basic, Anoniem |
| gebruikersnaam |Gebruiker die toegang heeft tot de FTP-server |Nee |&nbsp; |
| wachtwoord |Wachtwoord voor de gebruiker (gebruikersnaam) |Nee |&nbsp; |
| versleuteldCredential |Versleutelde referenties voor toegang tot de FTP-server |Nee |&nbsp; |
| gatewayNaam |Naam van de Data Management Gateway om verbinding te maken met een on-premises FTP-server |Nee |&nbsp; |
| poort |Poort waarop de FTP-server luistert |Nee |21 |
| inschakelenSsl |Opgeven of FTP via SSL/TLS-kanaal moet worden gebruikt |Nee |waar |
| enableServerCertificateValidation |Opgeven of serverTLS/SSL-certificaatvalidatie moet worden ingeschakeld bij het gebruik van FTP via SSL/TLS-kanaal |Nee |waar |

#### <a name="example-using-anonymous-authentication"></a>Voorbeeld: Anonieme verificatie gebruiken

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Voorbeeld: gebruikersnaam en wachtwoord gebruiken in platte tekst voor basisverificatie

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Voorbeeld: Poort gebruiken, ssl inschakelen, servercertificaatvalidatie inschakelen

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Voorbeeld: versleutelde credential gebruiken voor verificatie en gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Zie artikel [FTP-connector](data-factory-ftp-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een FTP-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op FileShare**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad naar de map. Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, wordt de naam van het gegenereerde bestand in de volgende deze indeling weergegeven: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de mapPath te selecteren in plaats van alle bestanden.<br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeelden 1:`"fileFilter": "*.log"`<br/>Voorbeeld 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter is van toepassing op een bestandssetsetvan invoer. Deze eigenschap wordt niet ondersteund met HDFS. |Nee |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische mapPath op te geven, bestandsnaam voor tijdreeksgegevens. Bijvoorbeeld, mapPath parameterized voor elk uur van de gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**; en ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |
| gebruikBinaryTransfer |Geef op of u de binaire overdrachtsmodus gebruikt. Geldt voor binaire modus en valse ASCII. Standaardwaarde: True. Deze eigenschap kan alleen worden gebruikt wanneer gekoppeld gekoppeld servicetype van type is: FtpServer. |Nee |

> [!NOTE]
> bestandsnaam en fileFilter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie artikel [FTP-connector](data-factory-ftp-connector.md#dataset-properties) voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteembron in kopieeractiviteit
Als u gegevens kopieert vanaf een FTP-server, stelt u het **brontype** van de kopieeractiviteit in op **FileSystemSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Zie artikel [FTP-connector](data-factory-ftp-connector.md#copy-activity-properties) voor meer informatie.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Gekoppelde service
Als u een hdfs-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **Hdfs**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **Hdfs** |Ja |
| URL |URL naar de HDFS |Ja |
| authenticationType |Anoniem, of Windows. <br><br> Als u **Kerberos-verificatie** voor HDFS-connector wilt gebruiken, raadpleegt u deze sectie om uw on-premises omgeving dienovereenkomstig in te stellen. |Ja |
| userName |Gebruikersnaam voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| wachtwoord |Wachtwoord voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de HDFS. |Ja |
| versleuteldCredential |[Nieuw-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) uitvoer van de toegangsreferenties. |Nee |

#### <a name="example-using-anonymous-authentication"></a>Voorbeeld: Anonieme verificatie gebruiken

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Voorbeeld: Windows-verificatie gebruiken

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie Het hdfs-connectorartikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een HDFS-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op FileShare**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de map. Voorbeeld: `myfolder`<br/><br/>Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Geef bijvoorbeeld voor map\submap\\\\de submap map op en geef\\\\voor d:\samplemap d: samplemap op.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, wordt de naam van het gegenereerde bestand in de volgende deze indeling weergegeven: <br/><br/>`Data.<Guid>.txt`(bijvoorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische mapPath op te geven, bestandsnaam voor tijdreeksgegevens. Voorbeeld: mapPath parameterized voor elk uur aan gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

> [!NOTE]
> bestandsnaam en fileFilter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie Het hdfs-connectorartikel voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteembron in kopieeractiviteit
Als u gegevens van HDFS kopieert, stelt u het **brontype** van de kopieeractiviteit in op **FileSystemSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie Het hdfs-connectorartikel voor meer informatie.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Gekoppelde service
Als u een Gekoppelde SFTP-service wilt definiëren, stelt u het **type** gekoppelde service in op **Sftp**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| host | Naam of IP-adres van de SFTP-server. |Ja |
| poort |Poort waarop de SFTP-server luistert. De standaardwaarde is: 21 |Nee |
| authenticationType |Geef verificatietype op. Toegestane waarden: **Basic**, **SshPublicKey**. <br><br> Raadpleeg het gebruik van basisverificatie [en het gebruik van ssh-verificatiesecties voor openbare sleutels](#using-ssh-public-key-authentication) op respectievelijk meer eigenschappen en JSON-voorbeelden. |Ja |
| skipHostKeyValidation | Geef op of u de validatie van hostsleutels wilt overslaan. | Nee. De standaardwaarde: false |
| hostKeyFingerprint | Geef de vingerafdruk van de hosttoets op. | Ja als `skipHostKeyValidation` het is ingesteld op vals.  |
| gatewayNaam |Naam van de Data Management Gateway om verbinding te maken met een on-premises SFTP-server. | Ja als u gegevens kopieert van een on-premises SFTP-server. |
| versleuteldCredential | Versleutelde referenties voor toegang tot de SFTP-server. Automatisch gegenereerd wanneer u basisverificatie (gebruikersnaam + wachtwoord) of SshPublicKey-verificatie (gebruikersnaam + privésleutelpad of -inhoud) opgeeft in de wizard Copy of in het pop-updialoogvenster ClickOnce. | Nee. Alleen toepassen bij het kopiëren van gegevens vanaf een on-premises SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Voorbeeld: Basisverificatie gebruiken

Als u basisverificatie `authenticationType` `Basic`wilt gebruiken, stelt u de volgende eigenschappen in naast de algemene eigenschappen van de SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruiker die toegang heeft tot de SFTP-server. |Ja |
| wachtwoord | Wachtwoord voor de gebruiker (gebruikersnaam). | Ja |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Voorbeeld: **Basisverificatie met versleutelde referenties**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**SSH-verificatie met openbare sleutels gebruiken:**

Als u basisverificatie `authenticationType` `SshPublicKey`wilt gebruiken, stelt u de volgende eigenschappen in naast de algemene eigenschappen van de SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam |Gebruiker die toegang heeft tot de SFTP-server |Ja |
| privateKeyPath | Geef een absoluut pad op naar het privésleutelbestand waartoe gateway toegang heeft. | Geef de `privateKeyPath` `privateKeyContent`or op . <br><br> Alleen toepassen bij het kopiëren van gegevens vanaf een on-premises SFTP-server. |
| privateKeyContent | Een geserialiseerde reeks van de persoonlijke sleutelinhoud. De wizard Kopiëren kan het privésleutelbestand lezen en de inhoud van de privésleutel automatisch extraheren. Als u een ander gereedschap/SDK gebruikt, gebruikt u in plaats daarvan de eigenschap privateKeyPath. | Geef de `privateKeyPath` `privateKeyContent`or op . |
| Passphrase | Geef de wachtwoordzin/het wachtwoord op om de privésleutel te decoderen als het sleutelbestand wordt beschermd door een wachtwoordzin. | Ja als het privésleutelbestand wordt beschermd door een wachtwoordzin. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Voorbeeld: **SshPublicKey-verificatie met behulp van inhoud met privésleutels**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Zie [artikel SFTP-connector](data-factory-sftp-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SFTP-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op FileShare**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad naar de map. Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Zie Voorbeeld definities van gekoppelde service en gegevenssets voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, wordt de naam van het gegenereerde bestand in de volgende deze indeling weergegeven: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de mapPath te selecteren in plaats van alle bestanden.<br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeelden 1:`"fileFilter": "*.log"`<br/>Voorbeeld 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter is van toepassing op een bestandssetsetvan invoer. Deze eigenschap wordt niet ondersteund met HDFS. |Nee |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische mapPath op te geven, bestandsnaam voor tijdreeksgegevens. Bijvoorbeeld, mapPath parameterized voor elk uur van de gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |
| gebruikBinaryTransfer |Geef op of u de binaire overdrachtsmodus gebruikt. Geldt voor binaire modus en valse ASCII. Standaardwaarde: True. Deze eigenschap kan alleen worden gebruikt wanneer gekoppeld gekoppeld servicetype van type is: FtpServer. |Nee |

> [!NOTE]
> bestandsnaam en fileFilter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie [artikel SFTP-connector](data-factory-sftp-connector.md#dataset-properties) voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteembron in kopieeractiviteit
Als u gegevens kopieert uit een SFTP-bron, stelt u het **brontype** van de kopieeractiviteit in op **FileSystemSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |



#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Zie [artikel SFTP-connector](data-factory-sftp-connector.md#copy-activity-properties) voor meer informatie.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Gekoppelde service
Als u een HTTP-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **Http**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| url | URL baseren op de webserver | Ja |
| authenticationType | Hiermee geeft u het verificatietype op. Toegestane waarden zijn: **Anoniem**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Raadpleeg secties onder deze tabel over meer eigenschappen en JSON-voorbeelden voor deze verificatietypen. | Ja |
| enableServerCertificateValidation | Opgeven of serverTLS/SSL-certificaatvalidatie moet worden ingeschakeld als de bron HTTPS-webserver is | Nee, standaard is waar |
| gatewayNaam | Naam van de Data Management Gateway om verbinding te maken met een on-premises HTTP-bron. | Ja als u gegevens kopieert van een on-premises HTTP-bron. |
| versleuteldCredential | Versleutelde referenties om toegang te krijgen tot het HTTP-eindpunt. Automatisch gegenereerd wanneer u de verificatiegegevens configureert in de wizard Kopiëren of in het dialoogvenster ClickOnce. | Nee. Alleen toepassen bij het kopiëren van gegevens vanaf een on-premises HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Voorbeeld: Basis-, Digest- of Windows-verificatie gebruiken
Stel `authenticationType` `Basic`in `Digest`als `Windows`, of , en geef de volgende eigenschappen op naast de hierboven geïntroduceerde ALGEMENE HTTP-connector:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruikersnaam om toegang te krijgen tot het HTTP-eindpunt. | Ja |
| wachtwoord | Wachtwoord voor de gebruiker (gebruikersnaam). | Ja |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Voorbeeld: ClientCertificate-verificatie gebruiken

Als u basisverificatie `authenticationType` `ClientCertificate`wilt gebruiken, stelt u de volgende eigenschappen in naast de hierboven geïntroduceerde HTTP-connectoralgemene eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| embeddedCertData | De Base64-gecodeerde inhoud van binaire gegevens van het PFX-bestand (Personal Information Exchange). | Geef de `embeddedCertData` `certThumbprint`or op . |
| certThumbprint | De duimafdruk van het certificaat dat is geïnstalleerd in de cert-winkel van uw gatewaymachine. Alleen toepassen bij het kopiëren van gegevens uit een on-premises HTTP-bron. | Geef de `embeddedCertData` `certThumbprint`or op . |
| wachtwoord | Wachtwoord gekoppeld aan het certificaat. | Nee |

Als u `certThumbprint` verificatie gebruikt en het certificaat is geïnstalleerd in de persoonlijke winkel van de lokale computer, moet u de leestoestemming voor de gatewayservice verlenen:

1. Microsoft Management Console (MMC) starten. Voeg de module **Certificaten** toe die is gericht op de **lokale computer**.
2. **Certificaten**uitvouwen , **Persoonlijk**en klik op **Certificaten**.
3. Klik met de rechtermuisknop op het certificaat in de persoonlijke winkel en selecteer **Alle taken**->**Privésleutels beheren...**
3. Voeg op het tabblad **Beveiliging** het gebruikersaccount toe waaronder De Gegevensbeheergatewayhostservice wordt uitgevoerd met de leestoegang tot het certificaat.

**Voorbeeld: clientcertificaat gebruiken:** Deze gekoppelde service koppelt uw gegevensfabriek aan een on-premises HTTP-webserver. Het maakt gebruik van een clientcertificaat dat is geïnstalleerd op de machine met Data Management Gateway geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Voorbeeld: clientcertificaat gebruiken in een bestand
Deze gekoppelde service koppelt uw gegevensfabriek aan een on-premises HTTP-webserver. Het maakt gebruik van een client certificaat bestand op de machine met Data Management Gateway geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Zie [http-connectorartikel](data-factory-http-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een HTTP-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **Http**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| relativenurl | Een relatieve URL naar de bron die de gegevens bevat. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van gekoppelde service. <br><br> Als u dynamische URL wilt construeren, u functies `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`Data Factory en [systeemvariabelen](data-factory-functions-variables.md)gebruiken, Voorbeeld:. | Nee |
| requestMethode | Http-methode. Toegestane waarden zijn **GET** of **POST**. | Nee. De standaardwaarde is `GET`. |
| extra Kopteksten | Aanvullende HTTP-aanvraagkoppen. | Nee |
| requestBody | Instantie voor HTTP-aanvraag. | Nee |
| formaat | Als u de gegevens gewoon **wilt ophalen uit HTTP-eindpunt as-is** zonder deze te ontleeden, slaat u deze indelingsinstellingen over. <br><br> Als u de HTTP-antwoordinhoud tijdens de kopie wilt ontleden, worden de volgende opmaaktypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

#### <a name="example-using-the-get-default-method"></a>Voorbeeld: de methode GET (standaard) gebruiken

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Voorbeeld: de methode POST gebruiken

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Zie [http-connectorartikel](data-factory-http-connector.md#dataset-properties) voor meer informatie.

### <a name="http-source-in-copy-activity"></a>HTTP-bron in kopieeractiviteit
Als u gegevens kopieert uit een HTTP-bron, stelt u het **brontype** van de kopieeractiviteit in **op HttpSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| httpRequestTime-out | De time-out (TimeSpan) voor het HTTP-verzoek om een antwoord te krijgen. Het is de time-out om een antwoord te krijgen, niet de time-out om reactiegegevens te lezen. | Nee. Standaardwaarde: 00:01:40 |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [http-connectorartikel](data-factory-http-connector.md#copy-activity-properties) voor meer informatie.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Gekoppelde service
Als u een oData-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in op **OData**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| url |Url van de OData-service. |Ja |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de OData-bron. <br/><br/> Voor cloud OData zijn mogelijke waarden Anoniem, Basic en OAuth (opmerking Azure Data Factory ondersteunt momenteel alleen Op Azure Active Directory gebaseerde OAuth). <br/><br/> Voor on-premises OData zijn mogelijke waarden Anoniem, Basic en Windows. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u Basisverificatie gebruikt. |Ja (alleen als u basisverificatie gebruikt) |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Ja (alleen als u basisverificatie gebruikt) |
| geautoriseerdeCredential |Als u OAuth gebruikt, klikt u op De knop **Autoriseren** in de wizard Gegevensfabriekskopie of Editor en voert u uw referentie in, waarna de waarde van deze eigenschap automatisch wordt gegenereerd. |Ja (alleen als u OAuth-verificatie gebruikt) |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises OData-service. Geef alleen op als u gegevens kopieert van on-premises OData-bron. |Nee |

#### <a name="example---using-basic-authentication"></a>Voorbeeld - Basisverificatie gebruiken
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Voorbeeld - Anonieme verificatie gebruiken

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Voorbeeld : Windows-verificatie gebruiken die toegang krijgt tot on-premises OData-bron

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Voorbeeld - OAuth-verificatie gebruiken die toegang krijgt tot cloud-OData-bron
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Zie [OData-connectorartikel](data-factory-odata-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een OData-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **ODataResource**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| path |Pad naar de OData-bron |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Zie [OData-connectorartikel](data-factory-odata-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens kopieert uit een OData-bron, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Voorbeeld | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |"?$select=Naam, Beschrijving&$top=5" |Nee |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Zie [OData-connectorartikel](data-factory-odata-connector.md#copy-activity-properties) voor meer informatie.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Gekoppelde service
Als u een ODBC-gekoppelde service wilt definiëren, stelt u het **type** gekoppelde service in **op OnPremisesOdbc**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Connectionstring |Het gedeelte van de niet-toegangsreferenties van de verbindingstekenreeks en een optionele versleutelde referentie. Zie voorbeelden in de volgende secties. |Ja |
| referenties |Het gedeelte toegangsreferenties van de verbindingstekenreeks die is opgegeven in de waardenotatie van stuurprogramma's. Voorbeeld: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met het ODBC-gegevensarchief. Mogelijke waarden zijn: Anoniem en Basic. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u Basisverificatie gebruikt. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het ODBC-gegevensarchief. |Ja |

#### <a name="example---using-basic-authentication"></a>Voorbeeld - Basisverificatie gebruiken

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Voorbeeld : Basisverificatie gebruiken met versleutelde referenties
U de referenties versleutelen met de cmdlet [Nieuw-AzDataFactoryEncryptValue.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Voorbeeld: Anonieme verificatie gebruiken

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie [ODBC-connectorartikel](data-factory-odbc-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een ODBC-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op Relationaltable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in het ODBC-gegevensarchief. |Ja |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [ODBC-connectorartikel](data-factory-odbc-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens kopieert uit een ODBC-gegevensarchief, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Bijvoorbeeld: `select * from MyTable`. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [ODBC-connectorartikel](data-factory-odbc-connector.md#copy-activity-properties) voor meer informatie.

## <a name="salesforce"></a>SalesForce


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Salesforce-service wilt definiëren, stelt u het **type** gekoppelde service in op **Salesforce**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| omgevingUrl | Geef de URL van Salesforce-instantie op. <br><br> - Standaard is\/"https: /login.salesforce.com". <br> - Om gegevens uit sandboxhttps://test.salesforce.comte kopiëren, geeft u " "op" op. <br> - Als u gegevens uit een aangepast domein wilt kopiëren, geeft u bijvoorbeeld "https://[domain].my.salesforce.com" op. |Nee |
| gebruikersnaam |Geef een gebruikersnaam op voor het gebruikersaccount. |Ja |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount. |Ja |
| securityToken |Geef een beveiligingstoken op voor het gebruikersaccount. Zie [Beveiligingstoken opvragen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het resetten/krijgen van een beveiligingstoken. Zie [Beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligingstokens in het algemeen. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Zie het artikel [over salesforce-connectoren](data-factory-salesforce-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Salesforce-gegevensset wilt definiëren, stelt u het **type** gegevensset in **op Relationaltable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in Salesforce. |Nee (als een **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel [over salesforce-connectoren](data-factory-salesforce-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in kopieeractiviteit
Als u gegevens uit Salesforce kopieert, stelt u het **brontype** van de kopieeractiviteit in **op RelationalSource**en geeft u de volgende eigenschappen op in de **bronsectie:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-92-query of [SOQL-query (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Bijvoorbeeld `select * from MyTable__c`. |Nee (als de **tabelNaam** van de **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Het "__c" deel van de API-naam is nodig voor elk aangepast object.

Zie het artikel [over salesforce-connectoren](data-factory-salesforce-connector.md#copy-activity-properties) voor meer informatie.

## <a name="web-data"></a>Webgegevens

### <a name="linked-service"></a>Gekoppelde service
Als u een webgekoppelde service wilt definiëren, stelt u het **type** gekoppelde service op **web**in en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| URL |URL naar de webbron |Ja |
| authenticationType |Anonieme. |Ja |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Zie Artikel [Webtabel-connector](data-factory-web-table-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een webgegevensset wilt definiëren, stelt u het **type** gegevensset in op **WebTable**en geeft u de volgende eigenschappen op in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |type van de gegevensset. moet worden ingesteld op **WebTable** |Ja |
| path |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van gekoppelde service. |
| Index |De index van de tabel in de resource. Zie Index van een tabel in een HTML-paginasectie opvragen voor stappen om index van een tabel in een HTML-pagina op te halen. |Ja |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie Artikel [Webtabel-connector](data-factory-web-table-connector.md#dataset-properties) voor meer informatie.

### <a name="web-source-in-copy-activity"></a>Webbron in kopieeractiviteit
Als u gegevens uit een webtabel kopieert, stelt u het **brontype** van de kopieeractiviteit in op **WebSource**. Op dit moment, wanneer de bron in kopieeractiviteit van type **WebSource**is, worden er geen extra eigenschappen ondersteund.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie Artikel [Webtabel-connector](data-factory-web-table-connector.md#copy-activity-properties) voor meer informatie.

## <a name="compute-environments"></a>COMPUTE-OMGEVINGEN
In de volgende tabel worden de compute-omgevingen weergegeven die worden ondersteund door Data Factory en de transformatieactiviteiten die erop kunnen worden uitgevoerd. Klik op de koppeling voor de compute waarin u geïnteresseerd bent om de JSON-schema's voor gekoppelde service te bekijken om deze te koppelen aan een gegevensfabriek.

| Compute-omgeving | Activiteiten |
| --- | --- |
| [On-demand HDInsight-cluster](#on-demand-azure-hdinsight-cluster) of [uw eigen HDInsight-cluster](#existing-azure-hdinsight-cluster) |[.NET aangepaste activiteit](#net-custom-activity), [Hive activiteit](#hdinsight-hive-activity), [Pig activiteit](#hdinsight-pig-activity), [MapReduce activiteit](#hdinsight-mapreduce-activity), Hadoop streaming activiteit, Spark [activiteit](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET aangepaste activiteit](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning Batch Execution Activity](#machine-learning-batch-execution-activity), Machine Learning Update [Resource Activity](#machine-learning-update-resource-activity) Machine Learning |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure SQL Data Warehouse](#azure-sql-data-warehouse), SQL [Server](#sql-server-stored-procedure) |[Opgeslagen procedure](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>On-demand Azure HDInsight-cluster
De Azure Data Factory-service kan automatisch een Windows/Linux-gebaseerd HDInsight-cluster op basis van vraag maken om gegevens te verwerken. Het cluster wordt gemaakt in dezelfde regio als het opslagaccount (eigenschap linkedServiceName in het JSON) dat aan het cluster is gekoppeld. U de volgende transformatieactiviteiten uitvoeren op deze gekoppelde service: [.NET aangepaste activiteit](#net-custom-activity), [Hive-activiteit](#hdinsight-hive-activity), [Varkensactiviteit](#hdinsight-pig-activity), [MapReduce-activiteit](#hdinsight-mapreduce-activity), Hadoop-streamingactiviteit, [Spark-activiteit](#hdinsight-spark-activity).

### <a name="linked-service"></a>Gekoppelde service
In de volgende tabel vindt u beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een on-demand HDInsight-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **HDInsightOnDemand**. |Ja |
| clusterSize |Aantal werknemers/gegevensknooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofdknooppunten samen met het aantal werknemersknooppunten dat u voor deze eigenschap opgeeft. De knooppunten zijn van grootte Standard_D3 die 4 cores heeft, dus een\*cluster met 4 werknemersknooppunten neemt\*24 cores (4 4 = 16 cores voor werknemersknooppunten, plus 2 4 = 8 cores voor hoofdknooppunten). Zie [Hadoop-clusters op basis van Linux maken in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over de Standard_D3-laag. |Ja |
| timetolive timetolive |De toegestane idle tijd voor het on-demand HDInsight-cluster. Hiermee geeft u op hoe lang het on-demand HDInsight-cluster in leven blijft na voltooiing van een activiteitsrun als er geen andere actieve taken in het cluster zijn.<br/><br/>Als een activiteitsrun bijvoorbeeld 6 minuten duurt en timetolive is ingesteld op 5 minuten, blijft het cluster 5 minuten in leven na de 6 minuten van het verwerken van de activiteit. Als een andere activiteitsrun wordt uitgevoerd met het venster van 6 minuten, wordt deze verwerkt door hetzelfde cluster.<br/><br/>Het maken van een on-demand HDInsight-cluster is een dure bewerking (kan even duren), dus gebruik deze instelling als dat nodig is om de prestaties van een gegevensfabriek te verbeteren door een on-demand HDInsight-cluster opnieuw te gebruiken.<br/><br/>Als u de timetolive-waarde instelt op 0, wordt het cluster verwijderd zodra de activiteit wordt uitgevoerd in verwerkt. Aan de andere kant, als u een hoge waarde instelt, kan het cluster onnodig inactief blijven, wat resulteert in hoge kosten. Daarom is het belangrijk dat u de juiste waarde instelt op basis van uw behoeften.<br/><br/>Meerdere pijplijnen kunnen dezelfde instantie van het on-demand HDInsight-cluster delen als de eigenschapswaarde timetolive op de juiste manier is ingesteld |Ja |
| versie |Versie van het HDInsight-cluster. Zie ondersteunde [HDInsight-versies in Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)voor meer informatie. |Nee |
| linkedServiceName |Azure Storage-gekoppelde service die wordt gebruikt door het on-demand cluster voor het opslaan en verwerken van gegevens. <p>Momenteel u geen on-demand HDInsight-cluster maken dat een Azure Data Lake Store als opslag gebruikt. Als u de resultaatgegevens van HDInsight-verwerking wilt opslaan in een Azure Data Lake Store, gebruikt u een kopieeractiviteit om de gegevens uit de Azure Blob Storage naar de Azure Data Lake Store te kopiëren.</p>  | Ja |
| extraLinkedServiceNames |Hiermee geeft u extra opslagaccounts op voor de hdinsight-gekoppelde service, zodat de Data Factory-service deze namens u kan registreren. |Nee |
| osType |Type besturingssysteem. Toegestane waarden zijn: Windows (standaard) en Linux |Nee |
| hcatalogLinkedServiceName |De naam van azure SQL-gekoppelde service die naar de HCatalog-database wijst. Het on-demand HDInsight-cluster wordt gemaakt met behulp van de Azure SQL-database als metastore. |Nee |

### <a name="json-example"></a>Json voorbeeld
De volgende JSON definieert een Linux-gebaseerde on-demand HDInsight linked service. De Data Factory-service maakt automatisch een **HDInsight-cluster op Basis van Linux** bij het verwerken van een gegevenssegment.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Zie Artikel [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor meer informatie.

## <a name="existing-azure-hdinsight-cluster"></a>Bestaand Azure HDInsight-cluster
U een Azure HDInsight-gekoppelde service maken om uw eigen HDInsight-cluster te registreren bij Data Factory. U de volgende activiteiten voor gegevenstransformatie uitvoeren op deze gekoppelde service: [.NET aangepaste activiteit](#net-custom-activity), [Hive-activiteit](#hdinsight-hive-activity), [Varkensactiviteit](#hdinsight-pig-activity), [MapReduce-activiteit](#hdinsight-mapreduce-activity), Hadoop-streamingactiviteit, [Spark-activiteit](#hdinsight-spark-activity).

### <a name="linked-service"></a>Gekoppelde service
In de volgende tabel vindt u beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een gekoppelde Azure HDInsight-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **HDInsight**. |Ja |
| clusterUri |De URI van het HDInsight-cluster. |Ja |
| gebruikersnaam |Geef de naam op van de gebruiker die moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. |Ja |
| wachtwoord |Geef het wachtwoord voor het gebruikersaccount op. |Ja |
| linkedServiceName | Naam van de gekoppelde Azure Storage-service die verwijst naar de Azure blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Momenteel u geen gekoppelde Azure Data Lake Store-service voor deze eigenschap opgeven. U hebt mogelijk toegang tot gegevens in de Azure Data Lake Store vanuit Hive/Pig-scripts als het HDInsight-cluster toegang heeft tot de Data Lake Store. </p>  |Ja |

Zie [ondersteunde HDInsight-versies](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)voor versies van HDInsight-clusters.

#### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
U een Azure Batch-gekoppelde service maken om een batchgroep met virtuele machines (VM's) te registreren bij een gegevensfabriek. U aangepaste .NET-activiteiten uitvoeren met Azure Batch of Azure HDInsight. U een [.NET-aangepaste activiteit](#net-custom-activity) uitvoeren op deze gekoppelde service.

### <a name="linked-service"></a>Gekoppelde service
In de volgende tabel vindt u beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een gekoppelde Azure Batch-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **AzureBatch**. |Ja |
| accountName |Naam van het Azure Batch-account. |Ja |
| Accesskey |Toegangssleutel voor het Azure Batch-account. |Ja |
| poolNaam |Naam van de pool van virtuele machines. |Ja |
| linkedServiceName |Naam van de gekoppelde Azure Storage-service die is gekoppeld aan deze gekoppelde Azure Batch-service. Deze gekoppelde service wordt gebruikt voor het faseren van bestanden die nodig zijn om de activiteit uit te voeren en de activiteitsuitvoeringslogboeken op te slaan. |Ja |


#### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
U maakt een Azure Machine Learning-gekoppelde service om een eindpunt voor batchscoring voor Machine Learning te registreren bij een gegevensfabriek. Twee gegevenstransformatieactiviteiten die kunnen worden uitgevoerd op deze gekoppelde service: [Machine Learning Batch Execution Activity](#machine-learning-batch-execution-activity), Machine Learning Update Resource [Activity](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Gekoppelde service
In de volgende tabel vindt u beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een gekoppelde Azure Machine Learning-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Type |De eigenschap type moet worden ingesteld op: **AzureML**. |Ja |
| mlEindpunt |De URL voor batchscores. |Ja |
| apiKey |De API van het gepubliceerde werkruimtemodel. |Ja |

#### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
U maakt een **azure Data Lake Analytics-gekoppelde** service om een Azure Data Lake Analytics-computeservice te koppelen aan een Azure-gegevensfabriek voordat u de [U-SQL-activiteit van Data Lake Analytics](data-factory-usql-activity.md) in een pijplijn gebruikt.

### <a name="linked-service"></a>Gekoppelde service

In de volgende tabel vindt u beschrijvingen voor de eigenschappen die worden gebruikt in de JSON-definitie van een gekoppelde Azure Data Lake Analytics-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Type |De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. |Ja |
| accountName |De naam van het Azure Data Lake Analytics-account. |Ja |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |Nee |
| autorisatie |Autorisatiecode wordt automatisch opgehaald nadat u op de knop **Autoriseren** hebt geklikt in de Data Factory Editor en de OAuth-aanmelding hebt voltooid. |Ja |
| subscriptionId |Azure-abonnements-ID |Nee (Als dit niet is opgegeven, wordt het abonnement van de gegevensfabriek gebruikt). |
| resourceGroupName |Naam van Azure-resourcegroep |Nee (Als deze niet is opgegeven, wordt de resourcegroep van de gegevensfabriek gebruikt). |
| Sessionid |sessie-ID van de OAuth-autorisatiesessie. Elke sessie-ID is uniek en mag slechts één keer worden gebruikt. Wanneer u de Data Factory Editor gebruikt, wordt deze id automatisch gegenereerd. |Ja |


#### <a name="json-example"></a>Json voorbeeld
In het volgende voorbeeld wordt een JSON-definitie gegeven voor een gekoppelde Azure Data Lake Analytics-service.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server-opslagprocedure

U maakt een SQL Server-gekoppelde service en gebruikt deze met de [activiteit Opgeslagen procedure](data-factory-stored-proc-activity.md) om een opgeslagen procedure van een Data Factory-pijplijn aan te roepen.

### <a name="linked-service"></a>Gekoppelde service
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een on-premises SQL Server-database te koppelen aan een gegevensfabriek. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de on-premises SQL Server-gekoppelde service.

In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor sql server-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Verbindingsgegevens opgevenTekenreeksgegevens die nodig zijn om verbinding te maken met de on-premises SQL Server-database met SQL-verificatie of Windows-verificatie. |Ja |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises SQL Server-database. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op als u Windows-verificatie gebruikt. Voorbeeld: **gebruikersnaam\\domeinnaam**. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. |Nee |

U referenties versleutelen met de cmdlet **Nieuw-AzDataFactoryEncryptValue** en deze gebruiken in de verbindingstekenreeks zoals weergegeven in het volgende voorbeeld **(eigenschap EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Voorbeeld: JSON voor het gebruik van SQL-verificatie

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Voorbeeld: JSON voor het gebruik van Windows-verificatie

Als gebruikersnaam en wachtwoord zijn opgegeven, gebruikt gateway deze om zich voor te doen als het opgegeven gebruikersaccount om verbinding te maken met de on-premises SQL Server-database. Anders maakt gateway rechtstreeks verbinding met de SQL Server met de beveiligingscontext van Gateway (het opstartaccount).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie [SQL Server-connectorartikel](data-factory-sqlserver-connector.md#linked-service-properties) voor meer informatie.

## <a name="data-transformation-activities"></a>ACTIVITEITEN VOOR GEGEVENSTRANSFORMATIE

Activiteit | Beschrijving
-------- | -----------
[HDInsight Hive-activiteit](#hdinsight-hive-activity) | De HDInsight Hive-activiteit in een Data Factory-pijplijn voert Hive-query's uit op uw eigen of on-demand HDInsight-cluster op basis van Windows/Linux.
[HDInsight Pig activiteit](#hdinsight-pig-activity) | De HDInsight Pig-activiteit in een Data Factory-pijplijn voert Pig-query's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster.
[HDInsight MapReduce Activity](#hdinsight-mapreduce-activity) | De HDInsight MapReduce activiteit in een Data Factory-pijplijn voert MapReduce-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster.
[HDInsight-streamingactiviteit](#hdinsight-streaming-activity) | De HDInsight Streaming Activity in een Data Factory-pijplijn voert Hadoop Streaming-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster.
[HDInsight Spark-activiteit](#hdinsight-spark-activity) | De HDInsight Spark-activiteit in een Data Factory-pijplijn voert Spark-programma's uit op uw eigen HDInsight-cluster.
[Machine Learning-batchuitvoeringsactiviteit](#machine-learning-batch-execution-activity) | Met Azure Data Factory u eenvoudig pijplijnen maken die gebruikmaken van een gepubliceerde Azure Machine Learning-webservice voor voorspellende analyses. Met behulp van de batchuitvoeringsactiviteit in een Azure Data Factory-pijplijn u een Machine Learning-webservice aanroepen om voorspellingen te doen over de gegevens in batch.
[Machine Learning-activiteit resources bijwerken](#machine-learning-update-resource-activity) | Na verloop van tijd moeten de voorspellende modellen in de Machine Learning-scoringsexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. Nadat u klaar bent met omscholing, wilt u de scorewebservice bijwerken met het omgeschoold Machine Learning-model. U de resourceactiviteit bijwerken gebruiken om de webservice bij te werken met het nieuw getrainde model.
[Opgeslagen procedureactiviteit](#stored-procedure-activity) | U de activiteit Opgeslagen procedure in een Data Factory-pijplijn gebruiken om een opgeslagen procedure aan te roepen in een van de volgende gegevensopslag: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database in uw onderneming of een Azure VM.
[Data Lake Analytics U-SQL-activiteit](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL Activity voert een U-SQL-script uit op een Azure Data Lake Analytics-cluster.
[.NET aangepaste activiteit](#net-custom-activity) | Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, u een aangepaste activiteit maken met uw eigen gegevensverwerkingslogica en de activiteit in de pijplijn gebruiken. U de aangepaste .NET-activiteit zo configureren dat deze wordt uitgevoerd met een Azure Batch-service of een Azure HDInsight-cluster.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor hiveactiviteit. De eigenschap type voor de activiteit moet zijn: **HDInsightHive**. U moet eerst een HDInsight-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op HDInsightHive:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| uit |De inline van het Hive-script opgeven |Nee |
| scriptpad |Sla het Hive-script op in een Azure blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap 'script' of 'scriptPath'. Beide kunnen niet samen worden gebruikt. De bestandsnaam is hoofdlettergevoelig. |Nee |
| Definieert |Parameters opgeven als sleutel-/waardeparen voor verwijzing in het Hive-script met behulp van 'hiveconf' |Nee |

Deze typeeigenschappen zijn specifiek voor de Hive-activiteit. Andere eigenschappen (buiten de sectie typeEigenschappen) worden ondersteund voor alle activiteiten.

### <a name="json-example"></a>Json voorbeeld
De volgende JSON definieert een HDInsight Hive-activiteit in een pijplijn.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Zie Het artikel [Van de Activiteit van de bijenkorf](data-factory-hive-activity.md) voor meer informatie.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor varkensactiviteit. De eigenschap type voor de activiteit moet zijn: **HDInsightPig**. U moet eerst een HDInsight-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op HDInsightPig:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| uit |De inline van het varkensscript opgeven |Nee |
| scriptpad |Sla het Pig-script op in een Azure blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap 'script' of 'scriptPath'. Beide kunnen niet samen worden gebruikt. De bestandsnaam is hoofdlettergevoelig. |Nee |
| Definieert |Parameters opgeven als sleutel-/waardeparen voor verwijzingen in het script Varken |Nee |

Deze typeeigenschappen zijn specifiek voor de varkensactiviteit. Andere eigenschappen (buiten de sectie typeEigenschappen) worden ondersteund voor alle activiteiten.

### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Zie Artikel Varkensactiviteit voor meer informatie.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor mapreduceactiviteit. De eigenschap type voor de activiteit moet zijn: **HDInsightMapReduce**. U moet eerst een HDInsight-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op HDInsightMapReduce:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| jarLinkedService | Naam van de gekoppelde service voor de Azure Storage die het JAR-bestand bevat. | Ja |
| jarFilePath | Pad naar het JAR-bestand in de Azure Storage. | Ja |
| Classname | Naam van de hoofdklasse in het JAR-bestand. | Ja |
| Argumenten | Een lijst met door komma's gescheiden argumenten voor het MapReduce-programma. Bij runtime ziet u een paar extra argumenten (bijvoorbeeld: mapreduce.job.tags) uit het MapReduce-framework. Als u uw argumenten wilt onderscheiden met de argumenten MapReduce, u overwegen zowel de optie als de waarde te gebruiken als argumenten zoals weergegeven in het volgende voorbeeld (-s, --invoer, --uitvoer enz., zijn opties die onmiddellijk worden gevolgd door hun waarden) | Nee |

### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Zie [Artikel Activiteit toewijzen](data-factory-map-reduce.md) voor meer informatie.

## <a name="hdinsight-streaming-activity"></a>HDInsight-streamingactiviteit
U de volgende eigenschappen opgeven in een JSON-definitie voor hadoopstreamingactiviteit. De eigenschap type voor de activiteit moet zijn: **HDInsightStreaming**. U moet eerst een HDInsight-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op HDInsightStreaming:

| Eigenschap | Beschrijving |
| --- | --- |
| Mapper | Naam van de mapper uitvoerbaar. In het voorbeeld is cat.exe de mapper uitvoerbaar.|
| Reducer | Naam van de verkleiner uitvoerbaar. In het voorbeeld is wc.exe de reducer uitvoerbaar. |
| input | Invoerbestand (inclusief locatie) voor de mapper. In het `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`voorbeeld: : adfsample is de blobcontainer, voorbeeld/gegevens/Gutenberg is de map en davinci.txt is de blob. |
| output | Uitvoerbestand (inclusief locatie) voor de reducer. De uitvoer van de taak Hadoop Streaming wordt geschreven naar de locatie die voor deze eigenschap is opgegeven. |
| bestandspaden | Paden voor de mapper en reducer executables. In het voorbeeld: "adfsample/example/apps/wc.exe", adfsample is de blobcontainer, voorbeeld/apps is de map en wc.exe is de uitvoerbare. |
| fileLinkedService | Gekoppelde Azure Storage-service die de Azure-opslag vertegenwoordigt die de bestanden bevat die zijn opgegeven in de sectie bestandspaden. |
| Argumenten | Een lijst met door komma's gescheiden argumenten voor het MapReduce-programma. Bij runtime ziet u een paar extra argumenten (bijvoorbeeld: mapreduce.job.tags) uit het MapReduce-framework. Als u uw argumenten wilt onderscheiden met de argumenten MapReduce, u overwegen zowel de optie als de waarde te gebruiken als argumenten zoals weergegeven in het volgende voorbeeld (-s, --invoer, --uitvoer enz., zijn opties die onmiddellijk worden gevolgd door hun waarden) |
| getDebugInfo | Een optioneel element. Wanneer deze is ingesteld op Mislukken, worden de logboeken alleen gedownload bij een storing. Wanneer het is ingesteld op Alles, worden logboeken altijd gedownload, ongeacht de uitvoeringsstatus. |

> [!NOTE]
> U moet een uitvoergegevensset opgeven voor de hadoopstreamingactiviteit voor de eigenschap **uitvoer.** Deze gegevensset kan slechts een dummy-gegevensset zijn die nodig is om het pijplijnschema te besturen (per uur, per dag, enz.). Als de activiteit geen invoer vereist, u het opgeven van een invoergegevensset voor de activiteit voor de eigenschap **inputs** overslaan.

## <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Zie het artikel [Over streamingactiviteit](data-factory-hadoop-streaming-activity.md) voor meer informatie.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor Spark-activiteit. De eigenschap type voor de activiteit moet zijn: **HDInsightSpark**. U moet eerst een HDInsight-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op HDInsightSpark:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| rootPath | De Azure Blob-container en -map die het Spark-bestand bevat. De bestandsnaam is hoofdlettergevoelig. | Ja |
| entryFilePath | Relatief pad naar de hoofdmap van de Spark-code/-pakket. | Ja |
| Classname | Java/Spark-hoofdklasse van toepassing | Nee |
| Argumenten | Een lijst met opdrachtregelargumenten voor het Spark-programma. | Nee |
| proxyGebruiker | Het gebruikersaccount dat zich moet voordoen om het Spark-programma uit te voeren | Nee |
| sparkConfig | Spark-configuratie-eigenschappen. | Nee |
| getDebugInfo | Hiermee geeft u op wanneer de Spark-logboekbestanden worden gekopieerd naar de Azure-opslag die wordt gebruikt door hdinsight-cluster (of) die is opgegeven door sparkJobLinkedService. Toegestane waarden: Geen, Altijd of Mislukt. Standaardwaarde: Geen. | Nee |
| sparkJobLinkedService | De gekoppelde Azure Storage-service met het Spark-taakbestand, afhankelijkheden en logboeken.  Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag die is gekoppeld aan het HDInsight-cluster gebruikt. | Nee |

### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Houd rekening met de volgende punten:

- De **eigenschap type** is ingesteld op **HDInsightSpark.**
- De **rootPath** is ingesteld op **\\adfspark pyFiles** waar adfspark de Azure Blob-container is en pyFiles is prima map in die container. In dit voorbeeld is de Azure Blob Storage degene die is gekoppeld aan het Spark-cluster. U het bestand uploaden naar een andere Azure-opslag. Als u dit doet, maakt u een azure storage-gekoppelde service om dat opslagaccount te koppelen aan de gegevensfabriek. Geef vervolgens de naam van de gekoppelde service op als waarde voor de eigenschap **sparkJobLinkedService.** Zie Spark-activiteitseigenschappen voor meer informatie over deze eigenschap en andere eigenschappen die worden ondersteund door de Spark-activiteit.
- De **entryFilePath** is ingesteld op de **test.py**, dat is het python-bestand.
- De eigenschap **getDebugInfo** is ingesteld op **Altijd,** wat betekent dat de logbestanden altijd worden gegenereerd (succes of mislukking).

    > [!IMPORTANT]
    > We raden u aan deze eigenschap niet in te stellen op Altijd in een productieomgeving, tenzij u een probleem oplost.
- De sectie **uitvoer** heeft één uitvoergegevensset. U moet een uitvoergegevensset opgeven, zelfs als het vonkprogramma geen uitvoer produceert. De uitvoergegevensset stuurt de planning voor de pijplijn aan (per uur, per dag, enz.).

Zie Het artikel [Spark-activiteit](data-factory-spark.md) voor meer informatie over de activiteit.

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning-batchuitvoeringsactiviteit
U de volgende eigenschappen opgeven in een JSON-definitie voor batchuitvoeringsactiviteit van Azure Machine Learning-studio. De eigenschap type voor de activiteit moet zijn: **AzureMLBatchExecution**. U moet eerst een azure Machine Learning-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op AzureMLBatchExecution:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
webServiceInput | De gegevensset die moet worden doorgegeven als invoer voor de webservice Azure Machine Learning studio. Deze gegevensset moet ook worden opgenomen in de ingangen voor de activiteit. |Gebruik webServiceInput of webServiceInputs. |
webServiceInputs | Geef gegevenssets op die moeten worden doorgegeven als invoer voor de webservice Azure Machine Learning studio. Als de webservice meerdere ingangen gebruikt, gebruikt u de eigenschap webServiceInputs in plaats van de eigenschap webServiceInput te gebruiken. Gegevenssets waarnaar wordt verwezen door de **webServiceInputs** moeten ook worden opgenomen in de **activiteitsinvoer**. | Gebruik webServiceInput of webServiceInputs. |
webServiceOutputs | De gegevenssets die zijn toegewezen als uitvoer voor de webservice Azure Machine Learning studio. De webservice retourneert uitvoergegevens in deze gegevensset. | Ja |
globalParameters | Geef in deze sectie waarden op voor de parameters van de webservice. | Nee |

### <a name="json-example"></a>Json voorbeeld
In dit voorbeeld heeft de activiteit de gegevensset **MLSqlInput** als invoer en **MLSqlOutput** als uitvoer. De **MLSqlInput** wordt doorgegeven als invoer voor de webservice met behulp van de eigenschap **webServiceInput** JSON. De **MLSqlOutput** wordt doorgegeven als een uitvoer naar de webservice met behulp van de eigenschap JSON van **webServiceOutputs.**

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

In het voorbeeld JSON gebruikt de geïmplementeerde Azure Machine Learning Web-service een lezer en een writer-module om gegevens van/naar een Azure SQL-database te lezen/schrijven. Deze webservice legt de volgende vier parameters bloot: databaseservernaam, databasenaam, servergebruikersnaam en wachtwoord voor servergebruikersaccount.

> [!NOTE]
> Alleen ingangen en uitvoer van de AzureMLBatchExecution-activiteit kunnen als parameters worden doorgegeven aan de webservice. MlSqlInput is bijvoorbeeld in het bovenstaande JSON-fragment een invoer voor de AzureMLBatchExecution-activiteit, die wordt doorgegeven als invoer naar de webservice via de parameter webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning-activiteit resources bijwerken
U de volgende eigenschappen opgeven in een JSON-definitie van Azure Machine Learning-studio Update Resource Activity. De eigenschap type voor de activiteit moet zijn: **AzureMLUpdateResource**. U moet eerst een azure Machine Learning-gekoppelde service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op AzureMLUpdateResource:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
getraindModelName | Naam van het omgeschoold model. | Ja |
trainedModelDatasetName | Gegevensset die naar het iLearner-bestand wijst, wordt geretourneerd door de omscholingsbewerking. | Ja |

### <a name="json-example"></a>Json voorbeeld
De pijplijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. De batchuitvoeringsactiviteit van azure Machine Learning-studio neemt de trainingsgegevens als invoer en produceert een iLearner-bestand als uitvoer. De activiteit beroept zich op de trainingswebservice (trainingsexperiment dat als webservice wordt weergegeven) met de invoertrainingsgegevens en ontvangt het ilearner-bestand van de webservice. De tijdelijke aanduidingBlob is slechts een uitvoergegevensset voor dummy's die vereist is door de Azure Data Factory-service om de pijplijn uit te voeren.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor U-SQL-activiteit. De eigenschap type voor de activiteit moet zijn: **DataLakeAnalyticsU-SQL**. U moet een gekoppelde Azure Data Lake Analytics-service maken en de naam ervan opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op DataLakeAnalyticsU-SQL:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| scriptPath |Pad naar map die het U-SQL-script bevat. De naam van het bestand is hoofdlettergevoelig. |Nee (als u script gebruikt) |
| scriptLinkedService |Gekoppelde service die de opslag met het script koppelt aan de gegevensfabriek |Nee (als u script gebruikt) |
| uit |Geef inline script op in plaats van scriptPath en scriptLinkedService op te geven. Bijvoorbeeld: "script": "CREATE DATABASE test". |Nee (als u scriptPath en scriptLinkedService gebruikt) |
| mateParallelïsme |Het maximum aantal knooppunten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. |Nee |
| priority |Hiermee bepaalt u welke taken van alle taken die in de wachtrij staan, als eerste moeten worden geselecteerd. Hoe lager het getal, hoe hoger de prioriteit. |Nee |
| parameters |Parameters voor het U-SQL-script |Nee |

### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Zie [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)voor meer informatie.

## <a name="stored-procedure-activity"></a>Opgeslagen procedureactiviteit
U de volgende eigenschappen opgeven in een JSON-definitie voor opgeslagen procedureactiviteit. De eigenschap type voor de activiteit moet zijn: **SqlServerStoredProcedure**. U moet een van de volgende gekoppelde services maken en de naam van de gekoppelde service opgeven als waarde voor de eigenschap **linkedServiceName:**

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op SqlServerStoredProcedure:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| storedProcedureName |Geef de naam op van de opgeslagen procedure in de Azure SQL-database of Azure SQL Data Warehouse die wordt vertegenwoordigd door de gekoppelde service die de uitvoertabel gebruikt. |Ja |
| storedProcedureParameters |Waarden opgeven voor opgeslagen procedureparameters. Als u null voor een parameter moet doorgeven, gebruikt u de syntaxis: "param1": null (alle kleine letters). Zie het volgende voorbeeld voor meer informatie over het gebruik van deze eigenschap. |Nee |

Als u een invoergegevensset opgeeft, moet deze beschikbaar zijn (in de status 'Klaar') om de opgeslagen procedureactiviteit uit te voeren. De invoergegevensset kan niet worden verbruikt in de opgeslagen procedure als parameter. Het wordt alleen gebruikt om de afhankelijkheid te controleren voordat de opgeslagen procedureactiviteit wordt gestart. U moet een uitvoergegevensset opgeven voor een opgeslagen procedureactiviteit.

Uitvoergegevensset geeft het **schema** voor de opgeslagen procedureactiviteit (per uur, per week, maandelijks, enz.) aan. De uitvoergegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL Database of een Azure SQL Data Warehouse of een SQL Server Database waarin u de opgeslagen procedure wilt uitvoeren. De uitvoergegevensset kan dienen als een manier om het resultaat van de opgeslagen procedure voor latere verwerking door een andere activiteit[(chaining activiteiten)](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)in de pijplijn te halen. Data Factory schrijft echter niet automatisch de uitvoer van een opgeslagen procedure naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoerset verwijst. In sommige gevallen kan de uitvoergegevensset een **dummy-gegevensset**zijn, die alleen wordt gebruikt om het schema voor het uitvoeren van de opgeslagen procedureactiviteit op te geven.

### <a name="json-example"></a>Json voorbeeld

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Zie Artikel [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md) voor meer informatie.

## <a name="net-custom-activity"></a>Aangepaste .NET-activiteit
U de volgende eigenschappen opgeven in een JSON-definitie voor .NET-activiteit. De eigenschap type voor de activiteit moet zijn: **DotNetActivity**. U moet een azure HDInsight-gekoppelde service of een gekoppelde Azure Batch-service maken en de naam van de gekoppelde service opgeven als waarde voor de eigenschap **linkedServiceName.** De volgende eigenschappen worden ondersteund in de sectie **typeEigenschappen** wanneer u het type activiteit instelt op DotNetActiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| Naam van vergadering | Naam van de assemblage. In het voorbeeld is dit: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Naam van de klasse die de IDotNetActivity-interface implementeert. In het voorbeeld is dit: **MyDotNetActivityNS.MyDotNetActivity** waarbij MyDotNetActivityNS de naamruimte is en MyDotNetActivity de klasse is.  | Ja |
| PackageLinkedService | Naam van de gekoppelde Azure Storage-service die verwijst naar de blob-opslag die het aangepaste zip-bestand voor activiteit bevat. In het voorbeeld is dit: **AzureStorageLinkedService**.| Ja |
| PackageFile | Naam van het zip-bestand. In het voorbeeld is dit: **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| extendedProperties extendedProperties extendedProperties extended | Uitgebreide eigenschappen die u definiëren en doorgeven aan de .NET-code. In dit voorbeeld wordt de variabele **SliceStart** ingesteld op een waarde op basis van de variabele SliceStart-systeem. | Nee |

### <a name="json-example"></a>Json voorbeeld

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Zie [Aangepaste activiteiten gebruiken in](data-factory-use-custom-activities.md) het artikel Data Factory voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies:

- [Zelfstudie: een pijplijn maken met een kopieeractiviteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Zelfstudie: een pijplijn maken met een bijenkorfactiviteit](data-factory-build-your-first-pipeline.md)
