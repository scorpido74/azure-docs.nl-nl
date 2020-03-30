---
title: Data Factory-functies en systeemvariabelen
description: Biedt een lijst met Azure Data Factory-functies en systeemvariabelen
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73667653"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Functies en systeemvariabelen
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Systeemvariabelen in Gegevensfabriek](../control-flow-system-variables.md)als u de huidige versie van de service Data Factory gebruikt.

In dit artikel vindt u informatie over functies en variabelen die worden ondersteund door Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory-systeemvariabelen

| Naam variabele | Beschrijving | Objectbereik | JSON-scope- en use cases |
| --- | --- | --- | --- |
| WindowStart |Begin van het tijdsinterval voor het huidige activiteitsrunvenster |activiteit |<ol><li>Geef query's voor gegevensselectie op. Zie connectorartikelen waarnaar wordt verwezen in het artikel [Gegevensverplaatsingsactiviteiten.](data-factory-data-movement-activities.md)</li> |
| WindowEnd |Einde van het tijdsinterval voor het huidige activiteitsrunvenster |activiteit |hetzelfde als WindowStart. |
| SliceStart |Begin van het tijdsinterval voor de productie van gegevenssegment |activiteit<br/>Dataset |<ol><li>Geef dynamische mappaden en bestandsnamen op tijdens het werken met [Azure Blob-](data-factory-azure-blob-connector.md) en [Bestandssysteemgegevenssets.](data-factory-onprem-file-system-connector.md)</li><li>Geef invoerafhankelijkheden op met gegevensfabrieksfuncties in het verzamelen van activiteitsinvoer.</li></ol> |
| SliceEnd |Einde van het tijdsinterval voor het huidige gegevenssegment. |activiteit<br/>Dataset |hetzelfde als SliceStart. |

> [!NOTE]
> Momenteel vereist de gegevensfabriek dat het schema dat in de activiteit is opgegeven, precies overeenkomt met het schema dat is opgegeven in de beschikbaarheid van de uitvoergegevensset. Daarom worden WindowStart, WindowEnd en SliceStart en SliceEnd altijd toegewezen aan dezelfde periode en één uitvoersegment.
> 

### <a name="example-for-using-a-system-variable"></a>Voorbeeld voor het gebruik van een systeemvariabele
In het volgende voorbeeld worden jaar, maand, dag en tijd van **SliceStart** geëxtraheerd in afzonderlijke variabelen die worden gebruikt door **mapPath-** en **fileName-eigenschappen.**

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory-functies
U functies in de gegevensfabriek gebruiken, samen met systeemvariabelen voor de volgende doeleinden:

1. Query's voor gegevensselectie opgeven (zie verbindingsartikelen waarnaar wordt verwezen in het artikel [Activiteiten gegevensverplaatsing.](data-factory-data-movement-activities.md)
   
   De syntaxis om een gegevensfabriekfunctie aan te roepen is: ** $$ \<functie>** voor gegevensselectiequery's en andere eigenschappen in de activiteit en gegevenssets.  
2. Het opgeven van invoerafhankelijkheden met gegevensfabrieksfuncties in het verzamelen van activiteitsinvoer.
   
    $$ is niet nodig voor het opgeven van invoerafhankelijkheidsexpressies.     

In het volgende voorbeeld wordt de eigenschap **sqlReaderQuery** in een `Text.Format` JSON-bestand toegewezen aan een waarde die door de functie wordt geretourneerd. In dit voorbeeld wordt ook gebruik gemaakt van een systeemvariabele met de naam **WindowStart**, die de begintijd van het venster activiteitsrun vertegenwoordigt.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zie onderwerp [Tekenreeksen voor aangepaste datum- en tijdnotatie](https://msdn.microsoft.com/library/8kb3ddd4.aspx) waarin verschillende opmaakopties worden beschreven die u gebruiken (bijvoorbeeld: ay vs. yyyy). 

### <a name="functions"></a>Functions
In de volgende tabellen worden alle functies in Azure Data Factory weergegeven:

| Categorie | Functie | Parameters | Beschrijving |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Voegt Y-uren toe aan de opgegeven tijd X. <br/><br/>Voorbeeld: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Voegt Y-minuten toe aan X.<br/><br/>Voorbeeld: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(x) |X: Datumtijd |Hier wordt de begintijd voor het uur weergegeven door de uurcomponent van X. <br/><br/>Voorbeeld: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Voegt Y-dagen toe aan X. <br/><br/>Voorbeeld: 9/15/2013 12:00:00 uur + 2 dagen = 9/17/2013 12:00:00 uur.<br/><br/>U ook dagen aftrekken door Y als een negatief getal op te geven.<br/><br/>Bijvoorbeeld: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Voegt Y-maanden toe aan X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>U ook maanden aftrekken door Y als een negatief getal op te geven.<br/><br/>Bijvoorbeeld: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Voegt Y * 3 maanden toe aan X.<br/><br/>Voorbeeld: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Voegt Y * 7 dagen toe aan X<br/><br/>Voorbeeld: 9/15/2013 12:00:00 uur + 1 week = 9/22/2013 12:00:00 pm<br/><br/>U ook weken aftrekken door Y als een negatief getal op te geven.<br/><br/>Bijvoorbeeld: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Voegt Y-jaren toe aan X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>U ook jaren aftrekken door Y als een negatief getal op te geven.<br/><br/>Bijvoorbeeld: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Dag(X) |X: DateTime |Krijgt de dagcomponent van X.<br/><br/>Bijvoorbeeld: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(x) |X: DateTime |Krijgt de dag van de week component van X.<br/><br/>Bijvoorbeeld: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |Krijgt de dag in het jaar vertegenwoordigd door het jaar onderdeel van X.<br/><br/>Voorbeelden:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysinMonth(X) |X: DateTime |Wordt de dagen in de maand weergegeven door de maandcomponent van parameter X.<br/><br/>Bijvoorbeeld: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |Eindevan de dag(X) |X: DateTime |Hiermee wordt de datumtijd die het einde van de dag (dagcomponent) van X vertegenwoordigt.<br/><br/>Bijvoorbeeld: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |Eindemaand(X) |X: DateTime |Wordt het einde van de maand weergegeven door maandcomponent van parameter X. <br/><br/>Voorbeeld: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datumtijd die het einde van de maand september vertegenwoordigt) |
| Date |StartOfDay(x) |X: DateTime |Wordt het begin van de dag weergegeven door de dagcomponent van parameter X.<br/><br/>Bijvoorbeeld: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Van(X) |X: Tekenreeks |Parse string X tot een datumtijd. |
| DateTime |Teken(X) |X: DateTime |Krijgt de eigenschap teken van parameter X. Eén teek is gelijk aan 100 nanoseconden. De waarde van deze eigenschap vertegenwoordigt het aantal teken dat is verstreken sinds 12:00:00 middernacht, 1 januari 0001. |
| Tekst |Opmaak(X) |X: Tekenreeks, variabele |Hiermee maakt u `\\'` de `'` tekst op (gebruik combinatie om aan het teken te ontsnappen).|

> [!IMPORTANT]
> Wanneer u een functie binnen een andere **$$** functie gebruikt, hoeft u geen voorvoegsel te gebruiken voor de innerlijke functie. Bijvoorbeeld: $$Text.Format('PartitionKey \\eq\\'my_pkey_filter_value' en \\RowKey ge '{0: yyyy-MM-dd\\HH:mm:ss} '', Time.AddHours(SliceStart, -6)). Let in dit **$$** voorbeeld op dat voorvoegsel niet wordt gebruikt voor de functie **Time.AddHours.** 

#### <a name="example"></a>Voorbeeld
In het volgende voorbeeld worden invoer- en uitvoerparameters `Text.Format` voor de Hive-activiteit bepaald met behulp van de functie en de variabele van het SliceStart-systeem. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Voorbeeld 2

In het volgende voorbeeld wordt de parameter DateTime voor de activiteit Opgeslagen procedure bepaald met behulp van de tekst. Opmaak, functie en de variabele SliceStart. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Voorbeeld 3
Als u gegevens van de vorige dag wilt lezen in plaats van de dag die wordt weergegeven door de SliceStart, gebruikt u de functie AddDays zoals weergegeven in het volgende voorbeeld: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Zie onderwerp [Tekenreeksen voor aangepaste datum- en tijdnotatie](https://msdn.microsoft.com/library/8kb3ddd4.aspx) waarin verschillende opmaakopties worden beschreven die u gebruiken (bijvoorbeeld: yy vs. yyyy). 

