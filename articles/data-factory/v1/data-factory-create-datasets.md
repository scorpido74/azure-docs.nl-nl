---
title: Gegevenssets maken in Azure Data Factory
description: Meer informatie over het maken van gegevenssets in Azure Data Factory, met voorbeelden die eigenschappen zoals offset en anchorDateTime gebruiken.
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
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260434"
---
# <a name="datasets-in-azure-data-factory"></a>Gegevenssets in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-create-datasets.md)
> * [Versie 2 (huidige versie)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Gegevenssets in V2](../concepts-datasets-linked-services.md)als u de huidige versie van de service Data Factory gebruikt.

In dit artikel wordt beschreven wat gegevenssets zijn, hoe ze zijn gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory-pijplijnen. Het bevat details over elke sectie (bijvoorbeeld structuur, beschikbaarheid en beleid) in de wijziging JSON-definitie. Het artikel bevat ook voorbeelden **offset**voor het gebruik van de verschuivings-, **anchorDateTime-** en **stijleigenschappen** in een JSON-definitie van de gegevensset.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md) voor een overzicht als u nieuw bent in Data Factory. Als u geen praktische ervaring hebt met het maken van gegevensfabrieken, u een beter begrip krijgen door de [zelfstudie voor gegevenstransformatie](data-factory-build-your-first-pipeline.md) en de [zelfstudie voor gegevensverplaatsing te](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)lezen.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijplijn** is een logische groepering van **activiteiten** die samen een taak uitvoeren. Met activiteiten in een pijplijn definieert u welk acties moeten worden uitgevoerd voor uw gegevens. U bijvoorbeeld een kopieeractiviteit gebruiken om gegevens van een on-premises SQL Server naar Azure Blob-opslag te kopiëren. Vervolgens u een Hive-activiteit gebruiken waarop een Hive-script wordt uitgevoerd op een Azure HDInsight-cluster om gegevens uit Blob-opslag te verwerken om uitvoergegevens te produceren. Ten slotte u een tweede kopieeractiviteit gebruiken om de uitvoergegevens naar Azure SQL Data Warehouse te kopiëren, waarop bi-rapportageoplossingen (business intelligence) worden gebouwd. Zie [Pijplijnen en activiteiten in Azure Data Factory](data-factory-create-pipelines.md)voor meer informatie over pijplijnen en activiteiten.

Een activiteit kan nul of meer **invoergegevenssets**nemen en een of meer uitvoergegevenssets produceren. Een invoergegevensset vertegenwoordigt de invoer voor een activiteit in de pijplijn en een uitvoergegevensset vertegenwoordigt de uitvoervoor de activiteit. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Een Azure Blob-gegevensset geeft bijvoorbeeld de blobcontainer en -map op in blobopslag waaruit de pijplijn de gegevens moet lezen.

Voordat u een gegevensset maakt, maakt u een **gekoppelde service** om uw gegevensarchief te koppelen aan de gegevensfabriek. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Gegevenssets identificeren gegevens in de gekoppelde gegevensarchieven, zoals SQL-tabellen, bestanden, mappen en documenten. Een azure storage-gekoppelde service koppelt bijvoorbeeld een opslagaccount aan de gegevensfabriek. Een Azure Blob-gegevensset vertegenwoordigt de blobcontainer en de map die de te verwerken invoerblobs bevat.

Hier is een voorbeeldscenario. Als u gegevens uit Blob-opslag wilt kopiëren naar een SQL-database, maakt u twee gekoppelde services: Azure Storage en Azure SQL Database. Maak vervolgens twee gegevenssets: Azure Blob-gegevensset (die verwijst naar de gekoppelde azure-opslagservice) en Azure SQL Table-gegevensset (die verwijst naar de gekoppelde azure-databaseservice). De gekoppelde Azure Storage- en Azure SQL Database-services bevatten verbindingstekenreeksen die Data Factory tijdens runtime gebruikt om verbinding te maken met respectievelijk uw Azure Storage en Azure SQL Database. De Azure Blob-gegevensset geeft de blobcontainer en blobmap op die de invoerblobs in uw Blob-opslag bevat. De Azure SQL Table-gegevensset geeft de SQL-tabel in uw SQL-database op waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram worden de relaties tussen pijplijn, activiteit, gegevensset en gekoppelde service in Gegevensfabriek weergegeven:

![Relatie tussen pijplijn, activiteit, gegevensset, gekoppelde services](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Gegevensset JSON
Een gegevensset in Data Factory wordt als volgt gedefinieerd in JSON-indeling:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| name |Naam van de gegevensset. Zie [Azure Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor naamgevingsregels. |Ja |N.v.t. |
| type |Type van de gegevensset. Geef een van de typen op die worden ondersteund door Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). <br/><br/>Zie [Gegevenssettype](#Type)voor meer informatie . |Ja |N.v.t. |
| Structuur |Schema van de gegevensset.<br/><br/>Zie Structuur [van gegevenssets voor](#Structure)meer informatie . |Nee |N.v.t. |
| typeProperties | De typeeigenschappen zijn verschillend voor elk type (bijvoorbeeld: Azure Blob, Azure SQL-tabel). Zie [Gegevenssettype](#Type)voor meer informatie over de ondersteunde typen en hun eigenschappen. |Ja |N.v.t. |
| external | Booleaanse vlag om aan te geven of een gegevensset expliciet wordt geproduceerd door een pijplijn in gegevensfabrieken of niet. Als de invoergegevensset voor een activiteit niet wordt geproduceerd door de huidige pijplijn, stelt u deze vlag in op true. Stel deze vlag in op de true voor de invoergegevensset van de eerste activiteit in de pijplijn.  |Nee |false |
| availability | Hiermee definieert u het verwerkingsvenster (bijvoorbeeld per uur of per dag) of het snijmodel voor de productie van de gegevensset. Elke eenheid gegevens die wordt verbruikt en geproduceerd door een activiteitsrun, wordt een gegevenssegment genoemd. Als de beschikbaarheid van een uitvoergegevensset is ingesteld op dagelijks (frequentie - Dag, interval - 1), wordt dagelijks een segment geproduceerd. <br/><br/>Zie Beschikbaarheid van gegevenssets voor meer informatie. <br/><br/>Zie het artikel [Planning en uitvoering](data-factory-scheduling-and-execution.md) voor meer informatie over het model voor het snijden in de gegevensset. |Ja |N.v.t. |
| policy |Hiermee definieert u de criteria of de voorwaarde waaraan de werksetsegmenten moeten voldoen. <br/><br/>Zie de sectie [Gegevenssetbeleid](#Policy) voor meer informatie. |Nee |N.v.t. |

## <a name="dataset-example"></a>Voorbeeld van gegevensset
In het volgende voorbeeld vertegenwoordigt de gegevensset een tabel met de naam **MyTable** in een SQL-database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Houd rekening met de volgende punten:

* **type** is ingesteld op AzureSqlTable.
* de eigenschap **tableName-type** (specifiek voor AzureSqlTable-type) is ingesteld op MyTable.
* **linkedServiceName** verwijst naar een gekoppelde service van het type AzureSqlDatabase, die is gedefinieerd in het volgende JSON-fragment.
* **beschikbaarheidsfrequentie** is ingesteld op Dag en **interval** is ingesteld op 1. Dit betekent dat het datasetsegment dagelijks wordt geproduceerd.

**AzureSqlLinkedService** wordt als volgt gedefinieerd:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

In het vorige JSON-fragment:

* **type** is ingesteld op AzureSqlDatabase.
* **Eigenschap van het** type connectionString geeft informatie op om verbinding te maken met een SQL-database.

Zoals u zien, definieert de gekoppelde service hoe u verbinding maakt met een SQL-database. De gegevensset definieert welke tabel wordt gebruikt als invoer en uitvoer voor de activiteit in een pijplijn.

> [!IMPORTANT]
> Tenzij een gegevensset door de pijplijn wordt geproduceerd, moet deze worden gemarkeerd als **extern**. Deze instelling is over het algemeen van toepassing op invoer van de eerste activiteit in een pijplijn.

## <a name="dataset-type"></a><a name="Type"></a>Gegevenssettype
Het type gegevensset is afhankelijk van het gegevensarchief dat u gebruikt. Zie de volgende tabel voor een lijst met gegevensarchieven die worden ondersteund door Data Factory. Klik op een gegevensarchief voor meer informatie over het maken van een gekoppelde service en een gegevensset voor dat gegevensarchief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensopslag met * kunnen on-premises of op Azure-infrastructuur als service (IaaS) zijn. Voor deze gegevensopslag moet u [Data Management Gateway](data-factory-data-management-gateway.md)installeren.

In het voorbeeld in de vorige sectie wordt het type gegevensset ingesteld op **AzureSqlTable**. Voor een Azure Blob-gegevensset wordt het type gegevensset ingesteld op **AzureBlob,** zoals wordt weergegeven in de volgende JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Gegevenssetstructuur
De **structuursectie** is optioneel. Het definieert het schema van de gegevensset door een verzameling namen en gegevenstypen kolommen te bevatten. U gebruikt de structuursectie om tekstinformatie te verstrekken die wordt gebruikt om typen om te zetten en kolommen van de bron naar de bestemming te toewijzen. In het volgende voorbeeld bevat de `slicetimestamp` `projectname`gegevensset `pageviews`drie kolommen: , , en . Ze zijn respectievelijk van het type String, String en Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Elke kolom in de structuur bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de kolom. |Ja |
| type |Gegevenstype van de kolom.  |Nee |
| Cultuur |. NET-gebaseerde cultuur die moet worden gebruikt wanneer `Datetime` `Datetimeoffset`het type een .NET-type is: of . De standaardwaarde is `en-us`. |Nee |
| formaat |Tekenreeks Opmaak die moet worden gebruikt wanneer `Datetime` `Datetimeoffset`het type een .NET-type is: of . |Nee |

Met de volgende richtlijnen u bepalen wanneer structuurgegevens moeten worden opgenomen en wat u moet opnemen in de **structuursectie.**

* **Voor gestructureerde gegevensbronnen**geeft u de structuursectie alleen op als u wilt dat kolommen met kaartbron kolommen laten zinken en de namen ervan niet hetzelfde zijn. Dit soort gestructureerde gegevensbron slaat gegevensschema en typeinformatie op, samen met de gegevens zelf. Voorbeelden van gestructureerde gegevensbronnen zijn sql server, Oracle en Azure-tabel.
  
    Aangezien tekstinformatie al beschikbaar is voor gestructureerde gegevensbronnen, mag u geen tekstinformatie opnemen wanneer u de structuursectie opneemt.
* **Voor schema's op leesgegevensbronnen (met name Blob-opslag)** u ervoor kiezen om gegevens op te slaan zonder schema of typegegevens op te slaan met de gegevens. Neem voor dit soort gegevensbronnen structuur op wanneer u bronkolommen wilt toewijzen om kolommen te laten zinken. Voeg ook structuur toe wanneer de gegevensset een invoer is voor een kopieeractiviteit en gegevenstypen brongegevensset moeten worden geconverteerd naar native typen voor de gootsteen.
    
    Data Factory ondersteunt de volgende waarden voor het verstrekken van tekstinformatie in structuur: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset en Timespan**. Deze waarden zijn CLS-compatibel met Common Language Specification (CLS). NET-gebaseerde typewaarden.

Data Factory voert automatisch typeconversies uit bij het verplaatsen van gegevens van een brongegevensarchief naar een sink datastore.

## <a name="dataset-availability"></a>Beschikbaarheid van gegevenssets
De **sectie beschikbaarheid** in een gegevensset definieert het verwerkingsvenster (bijvoorbeeld per uur, dagelijks of wekelijks) voor de gegevensset. Zie Plannen en uitvoeren voor meer informatie over [activiteitsvensters.](data-factory-scheduling-and-execution.md)

In de volgende sectie beschikbaarheid wordt aangegeven dat de uitvoergegevensset elk uur wordt geproduceerd of dat de invoergegevensset elk uur beschikbaar is:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Als de pijplijn de volgende begin- en eindtijden heeft:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

De uitvoergegevensset wordt elk uur geproduceerd binnen de begin- en eindtijden van de pijplijn. Daarom zijn er vijf dataset segmenten geproduceerd door deze pijplijn, een voor elke activiteit venster (12 am - 1 am, 1 am - 2 AM, 2 AM - 3 AM, 3 AM - 4 AM, 4 AM - 5 AM).

In de volgende tabel worden eigenschappen beschreven die u gebruiken in de sectie beschikbaarheid:

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijdseenheid op voor de productie van gegevenssetsslicen.<br/><br/><b>Ondersteunde frequentie</b>: Minuut, uur, dag, week, maand |Ja |N.v.t. |
| interval |Hiermee geeft u een vermenigvuldiger voor frequentie op.<br/><br/>"Frequency x interval" bepaalt hoe vaak het segment wordt geproduceerd. Als u bijvoorbeeld de gegevensset per uur moet snijden, stelt u <b>de frequentie</b> in op <b>Uur</b>en <b>interval</b> op <b>1</b>.<br/><br/>Houd er rekening mee dat als u **de frequentie** als **minuut opgeeft,** u het interval moet instellen op niet minder dan 15. |Ja |N.v.t. |
| stijl |Hiermee geeft u op of het segment aan het begin of einde van het interval moet worden geproduceerd.<ul><li>Begininterval</li><li>EndofInterval</li></ul>Als **de frequentie** is ingesteld op **Maand**en **de stijl** is ingesteld op **EndOfInterval,** wordt het segment op de laatste dag van de maand geproduceerd. Als **de stijl** is ingesteld op **StartOfInterval,** wordt het segment op de eerste dag van de maand geproduceerd.<br/><br/>Als **de frequentie** is ingesteld op **Dag**en **de stijl** is ingesteld op **EndOfInterval,** wordt het segment in het laatste uur van de dag geproduceerd.<br/><br/>Als **de frequentie** is ingesteld op **Uur**en **de stijl** is ingesteld op **EndOfInterval,** wordt het segment aan het einde van het uur geproduceerd. Voor een segment voor de periode van 13.00 tot 14.00 uur wordt het segment bijvoorbeeld om 14.00 uur geproduceerd. |Nee |EndofInterval |
| anchorDateTime anchorDateTime |Hiermee definieert u de absolute positie in de tijd die door de planner wordt gebruikt om de grenzen van het segment van de gegevensset te berekenen. <br/><br/>Houd er rekening mee dat als deze eigenschap datumdelen heeft die gedetailleerder zijn dan de opgegeven frequentie, de meer gedetailleerde onderdelen worden genegeerd. Als het **interval** bijvoorbeeld **elk uur** is (frequentie: uur en interval: 1) en de **anchorDateTime** **minuten en seconden**bevat, worden de minuten- en secondendelen van **anchorDateTime** genegeerd. |Nee |01/01/0001 |
| offset |Tijdspanne waarmee het begin en einde van alle gegevenssetsegmenten worden verschoven. <br/><br/>Houd er rekening mee dat als zowel **anchorDateTime** als **offset** zijn opgegeven, het resultaat de gecombineerde verschuiving is. |Nee |N.v.t. |

### <a name="offset-example"></a>compensatievoorbeeld
Standaard beginnen dagelijkse`"frequency": "Day", "interval": 1`( ) segmenten om 12 uur (middernacht) Gecoördineerde universele tijd (UTC). Als u wilt dat de begintijd 6 AM UTC-tijd is, stelt u de verschuiving in zoals weergegeven in het volgende fragment:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime,voorbeeld
In het volgende voorbeeld wordt de gegevensset eenmaal per 23 uur geproduceerd. Het eerste segment begint op het tijdstip dat is `2017-04-19T08:00:00` opgegeven door **anchorDateTime**, ingesteld op (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>offset/stijlvoorbeeld
De volgende gegevensset is maandelijks en wordt geproduceerd op de 3e`3.08:00:00`van elke maand om 8:00 uur (

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Gegevenssetbeleid
In de **beleidssectie** in de gegevenssetdefinitie worden de criteria of de voorwaarde gedefinieerd waaraan de gegevenssetsegmenten moeten voldoen.

### <a name="validation-policies"></a>Validatiebeleid
| Beleidsnaam | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimaal FormaatMB |Valideert dat de gegevens in **Azure Blob-opslag** voldoen aan de minimale groottevereisten (in megabytes). |Azure Blob Storage |Nee |N.v.t. |
| minimumRijen |Valideert dat de gegevens in een **Azure SQL-database** of een **Azure-tabel** het minimumaantal rijen bevatten. |<ul><li>Azure SQL-database</li><li>Azure Table</li></ul> |Nee |N.v.t. |

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

**minimumRijen:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externe gegevenssets
Externe gegevenssets worden niet geproduceerd door een lopende pijplijn in de gegevensfabriek. Als de gegevensset is gemarkeerd als **extern,** kan het **beleid Van ExternGegevens** worden gedefinieerd om het gedrag van de beschikbaarheid van het gegevenssetsegment te beïnvloeden.

Tenzij een gegevensset wordt geproduceerd door Data Factory, moet deze worden gemarkeerd als **extern**. Deze instelling is over het algemeen van toepassing op de ingangen van de eerste activiteit in een pijplijn, tenzij activiteit of pijplijnketen wordt gebruikt.

| Name | Beschrijving | Vereist | Standaardwaarde |
| --- | --- | --- | --- |
| gegevensVertraging |De tijd om de controle op de beschikbaarheid van de externe gegevens voor het opgegeven segment uit te stellen. U bijvoorbeeld een uurcontrole uitstellen met deze instelling.<br/><br/>De instelling is alleen van toepassing op de huidige tijd. Als het nu bijvoorbeeld 13:00 uur is en deze waarde 10 minuten is, begint de validatie om 13:10 uur.<br/><br/>Houd er rekening mee dat deze instelling geen invloed heeft op segmenten in het verleden. Segmenten met **Slice End Time** + **dataDelay** < **Now** worden zonder vertraging verwerkt.<br/><br/>Tijden groter dan 23:59 uur moeten `day.hours:minutes:seconds` worden opgegeven met behulp van de indeling. Als u bijvoorbeeld 24 uur wilt opgeven, gebruikt u geen 24:00:00. Gebruik in plaats daarvan 1.00:00:00. Als u 24:00:00 uur gebruikt, wordt het behandeld als 24 dagen (24.00:00:00). Geef 1 dag en 4 uur lang 1:04:00:00 op. |Nee |0 |
| retryInterval |De wachttijd tussen een storing en de volgende poging. Deze instelling is van toepassing op de huidige tijd. Als de vorige poging is mislukt, is de volgende poging na de **periode opnieuw proberenInterval.** <br/><br/>Als het nu 13:00 uur is, beginnen we met de eerste poging. Als de duur om de eerste validatiecontrole te voltooien 1 minuut is en de bewerking is mislukt, is de volgende poging om 1:00 + 1min (duur) + 1min (interval opnieuw proberen) = 1:02 PM. <br/><br/>Voor plakjes in het verleden is er geen vertraging. De nieuwe poging gebeurt onmiddellijk. |Nee |00:01:00 (1 minuut) |
| tryTime-out opnieuw proberen |De time-out voor elke poging opnieuw proberen.<br/><br/>Als deze eigenschap is ingesteld op 10 minuten, moet de validatie binnen 10 minuten zijn voltooid. Als het langer duurt dan 10 minuten om de validatie uit te voeren, is het opnieuw proberen een keer uit te gaan.<br/><br/>Als alle pogingen voor de validatietime-out, wordt het segment gemarkeerd als **TimedOut**. |Nee |00:10:00 uur (10 minuten) |
| maximumRetry |Het aantal keren om te controleren op de beschikbaarheid van de externe gegevens. De maximaal toegestane waarde is 10. |Nee |3 |


## <a name="create-datasets"></a>Gegevenssets maken
U gegevenssets maken met een van deze hulpprogramma's of SDK's:

- De wizard Kopiëren
- Visual Studio
- PowerShell
- Azure Resource Manager-sjabloon
- REST API
- .NET API

Bekijk de volgende zelfstudies voor stapsgewijze instructies voor het maken van pijplijnen en gegevenssets met behulp van een van deze hulpprogramma's of SDK's:

- [Een pijplijn met een transformatieactiviteit voor gegevens bouwen](data-factory-build-your-first-pipeline.md)
- [Een pijplijn maken met een activiteit voor gegevensverplaatsing](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Nadat een pijplijn is gemaakt en geïmplementeerd, u uw pijplijnen beheren en bewaken met behulp van de Azure-portalblades of de app Monitoring en Beheer. Bekijk de volgende onderwerpen voor stapsgewijze instructies:

- [Pijplijnen bewaken en beheren met Azure-portalblades](data-factory-monitor-manage-pipelines.md)
- [Pijplijnen bewaken en beheren met de app Monitoring and Management](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Scoped-gegevenssets
U gegevenssets maken die zijn beperkt tot een pijplijn met behulp van de eigenschap **gegevenssets.** Deze gegevenssets kunnen alleen worden gebruikt door activiteiten binnen deze pijplijn, niet door activiteiten in andere pijplijnen. In het volgende voorbeeld wordt een pijplijn gedefinieerd met twee gegevenssets (InputDataset-rdc en OutputDataset-rdc) die in de pijplijn moeten worden gebruikt.

> [!IMPORTANT]
> Scoped-gegevenssets worden alleen ondersteund met eenmalige pijplijnen (waarbij **pipelineMode** is ingesteld op **OneTime).** Zie [Eenmalige pijplijn](data-factory-create-pipelines.md#onetime-pipeline) voor meer informatie.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Volgende stappen
- Zie Pijplijnen maken voor meer informatie over [pijplijnen.](data-factory-create-pipelines.md)
- Zie Plannen en uitvoeren in Azure Data [Factory](data-factory-scheduling-and-execution.md)voor meer informatie over hoe pijplijnen worden gepland en uitgevoerd.
