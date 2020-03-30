---
title: Gegevenssets
description: Meer informatie over gegevenssets in Data Factory. Gegevenssets vertegenwoordigen invoer-/uitvoergegevens.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246264"
---
# <a name="datasets-in-azure-data-factory"></a>Gegevenssets in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-create-datasets.md)
> * [Huidige versie](concepts-datasets-linked-services.md)

In dit artikel wordt beschreven wat gegevenssets zijn, hoe ze zijn gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory-pijplijnen.

Zie [Inleiding tot Azure Data Factory](introduction.md) voor een overzicht als u nieuw bent in Data Factory.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijplijn** is een logische groepering van **activiteiten** die samen een taak uitvoeren. Met activiteiten in een pijplijn definieert u welk acties moeten worden uitgevoerd voor uw gegevens. Nu is een **gegevensset** een benoemde weergave van gegevens die eenvoudig verwijst of verwijst naar de gegevens die u in uw **activiteiten** wilt gebruiken als invoer en uitvoer. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Een Azure Blob-gegevensset benoemt bijvoorbeeld de blobcontainer en -map in de Blob-opslag van waaruit de activiteit de gegevens moet lezen.

Voordat u een gegevensset maakt, moet u een [**gekoppelde service**](concepts-linked-services.md) maken om uw gegevensarchief te koppelen aan de gegevensfabriek. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Bekijk het zo; de gegevensset vertegenwoordigt de structuur van de gegevens binnen de gekoppelde gegevensopslag en de gekoppelde service definieert de verbinding met de gegevensbron. Een azure storage-gekoppelde service koppelt bijvoorbeeld een opslagaccount aan de gegevensfabriek. Een Azure Blob-gegevensset vertegenwoordigt de blobcontainer en de map in dat Azure-opslagaccount met de invoerblobs die moeten worden verwerkt.

Hier is een voorbeeldscenario. Als u gegevens uit Blob-opslag wilt kopiëren naar een SQL-database, maakt u twee gekoppelde services: Azure Storage en Azure SQL Database. Maak vervolgens twee gegevenssets: Azure Blob-gegevensset (die verwijst naar de gekoppelde azure-opslagservice) en Azure SQL Table-gegevensset (die verwijst naar de gekoppelde azure-databaseservice). De gekoppelde Azure Storage- en Azure SQL Database-services bevatten verbindingstekenreeksen die Data Factory tijdens runtime gebruikt om verbinding te maken met respectievelijk uw Azure Storage en Azure SQL Database. De Azure Blob-gegevensset geeft de blobcontainer en blobmap op die de invoerblobs in uw Blob-opslag bevat. De Azure SQL Table-gegevensset geeft de SQL-tabel in uw SQL-database op waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram worden de relaties tussen pijplijn, activiteit, gegevensset en gekoppelde service in Gegevensfabriek weergegeven:

![Relatie tussen pijplijn, activiteit, gegevensset, gekoppelde services](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Gegevensset JSON
Een gegevensset in Data Factory wordt gedefinieerd in de volgende JSON-indeling:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

Eigenschap | Beschrijving | Vereist |
-------- | ----------- | -------- |
name | Naam van de gegevensset. Zie [Azure Data Factory - Naamgevingsregels](naming-rules.md). |  Ja |
type | Type van de gegevensset. Geef een van de typen op die worden ondersteund door Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). <br/><br/>Zie [Gegevenssettypen](#dataset-type)voor meer informatie . | Ja |
Structuur | Schema van de gegevensset. Zie Schema [Gegevensset](#dataset-structure-or-schema)voor meer informatie . | Nee |
typeProperties | De typeeigenschappen zijn verschillend voor elk type (bijvoorbeeld: Azure Blob, Azure SQL-tabel). Zie [Gegevenssettype](#dataset-type)voor meer informatie over de ondersteunde typen en hun eigenschappen. | Ja |

### <a name="data-flow-compatible-dataset"></a>Gegevensstroomcompatibele gegevensset



Bekijk [ondersteunde gegevenssettypen](#dataset-type) voor een lijst met gegevenssettypen die compatibel zijn [met gegevensstroom.](concepts-data-flow-overview.md) Gegevenssets die compatibel zijn voor Data Flow vereisen fijnkorrelige gegevenssetdefinities voor transformaties. De JSON-definitie is dus iets anders. In plaats van een _structuureigenschap_ hebben gegevenssets die compatibel zijn met Gegevensstroom een _eigenschap schema._

In Data Flow worden gegevenssets gebruikt in bron- en sinktransformaties. De gegevenssets definiëren de basisgegevensschema's. Als uw gegevens geen schema hebben, u schemadrift gebruiken voor uw bron en gootsteen. Het schema in de gegevensset vertegenwoordigt het fysieke gegevenstype en de vorm.

Door het schema uit de gegevensset te definiëren, krijgt u de gerelateerde gegevenstypen, gegevensindelingen, bestandslocatie en verbindingsgegevens van de gekoppelde gekoppelde service. Metagegevens uit de gegevenssets worden in uw brontransformatie weergegeven als de *bronprojectie.* De projectie in de brontransformatie vertegenwoordigt de gegevensstroomgegevens met gedefinieerde namen en typen.

Wanneer u het schema van een gegevensstroomgegevensset importeert, selecteert u de knop **Schema importeren** en kiest u ervoor om te importeren uit de bron of uit een lokaal bestand. In de meeste gevallen importeert u het schema rechtstreeks vanuit de bron. Maar als u al een lokaal schemabestand hebt (een parketbestand of CSV met kopteksten), u Data Factory opdracht geven om het schema op dat bestand te baseren.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

Eigenschap | Beschrijving | Vereist |
-------- | ----------- | -------- |
name | Naam van de gegevensset. Zie [Azure Data Factory - Naamgevingsregels](naming-rules.md). |  Ja |
type | Type van de gegevensset. Geef een van de typen op die worden ondersteund door Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). <br/><br/>Zie [Gegevenssettypen](#dataset-type)voor meer informatie . | Ja |
schema | Schema van de gegevensset. Zie [Gegevensstroomcompatibele gegevenssets](#dataset-type)voor meer informatie . | Nee |
typeProperties | De typeeigenschappen zijn verschillend voor elk type (bijvoorbeeld: Azure Blob, Azure SQL-tabel). Zie [Gegevenssettype](#dataset-type)voor meer informatie over de ondersteunde typen en hun eigenschappen. | Ja |


## <a name="dataset-example"></a>Voorbeeld van gegevensset
In het volgende voorbeeld vertegenwoordigt de gegevensset een tabel met de naam MyTable in een SQL-database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Houd rekening met de volgende punten:

- type is ingesteld op AzureSqlTable.
- de eigenschap tableName-type (specifiek voor AzureSqlTable-type) is ingesteld op MyTable.
- linkedServiceName verwijst naar een gekoppelde service van het type AzureSqlDatabase, die is gedefinieerd in het volgende JSON-fragment.

## <a name="dataset-type"></a>Gegevenssettype
Er zijn veel verschillende typen gegevenssets, afhankelijk van het gegevensarchief dat u gebruikt. U vindt de lijst met gegevens die zijn opgeslagen, ondersteund door het [overzichtsartikel](connector-overview.md) van Data Factory. Klik op een gegevensarchief voor meer informatie over het maken van een gekoppelde service en een gegevensset voor dat gegevensarchief.

In het voorbeeld in de vorige sectie wordt het type gegevensset ingesteld op **AzureSqlTable**. Voor een Azure Blob-gegevensset wordt het type gegevensset ingesteld op **AzureBlob,** zoals wordt weergegeven in de volgende JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Gegevenssetstructuur of -schema
De **sectiegegevenssets structuursectie** of **schema** (Data Flow compatible) is optioneel. Het definieert het schema van de gegevensset door een verzameling namen en gegevenstypen kolommen te bevatten. U gebruikt de structuursectie om tekstinformatie te verstrekken die wordt gebruikt om typen om te zetten en kolommen van de bron naar de bestemming te toewijzen.

Elke kolom in de structuur bevat de volgende eigenschappen:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
name | Naam van de kolom. | Ja
type | Gegevenstype van de kolom. Data Factory ondersteunt de volgende tussentijdse gegevenstypen als toegestane waarden: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset en Timespan** | Nee
Cultuur | . NET-gebaseerde cultuur die moet worden gebruikt wanneer `Datetime` `Datetimeoffset`het type een .NET-type is: of . De standaardwaarde is `en-us`. | Nee
formaat | Tekenreeks Opmaak die moet worden gebruikt wanneer `Datetime` `Datetimeoffset`het type een .NET-type is: of . Raadpleeg [tekenreeksen voor aangepaste datum- en tijdnotatie](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) voor het opmaken van datumtijd. | Nee

### <a name="example"></a>Voorbeeld
Stel dat de bronblobgegevens in CSV-indeling zijn en drie kolommen bevatten: userid, name en lastlogindate. Ze zijn van het type Int64, String en Datetime met een aangepaste datumnotatie met verkorte Franse namen voor dag van de week.

Definieer de structuur van de Blob-gegevensset als volgt, samen met tekstdefinities voor de kolommen:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Richtlijnen

Met de volgende richtlijnen u begrijpen wanneer u structuurinformatie moet opnemen en wat u moet opnemen in de **structuursectie.** Meer informatie over hoe gegevensfabriek brongegevens in kaart brengt om te zinken en wanneer structuurgegevens van [Schema en typetoewijzing](copy-activity-schema-and-type-mapping.md)moeten worden opgegeven.

- **Geef de**structuursectie alleen op als u wilt dat kolommen met kaartbron kolommen laten zinken en de namen ervan niet hetzelfde zijn. Dit soort gestructureerde gegevensbron slaat gegevensschema en typeinformatie op, samen met de gegevens zelf. Voorbeelden van gestructureerde gegevensbronnen zijn SQL Server, Oracle en Azure SQL Database.<br/><br/>Aangezien tekstinformatie al beschikbaar is voor gestructureerde gegevensbronnen, mag u geen tekstinformatie opnemen wanneer u de structuursectie opneemt.
- **Voor no/weak schemagegevensbronnen, bijvoorbeeld tekstbestand in blobopslag,** moet structuur worden opgenomen wanneer de gegevensset een invoer is voor een kopieeractiviteit en moeten gegevenstypen van brongegevensset worden geconverteerd naar native typen voor de gootsteen. En voeg structuur toe wanneer u bronkolommen wilt toewijzen om kolommen te laten zinken..

## <a name="create-datasets"></a>Gegevenssets maken
U gegevenssets maken met een van deze hulpprogramma's of SDK's: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Azure Resource Manager Template en Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Huidige versie versus versie 1-gegevenssets

Hier volgen enkele verschillen tussen datafabriek en Data Factory versie 1-gegevenssets:

- De externe eigenschap wordt niet ondersteund in de huidige versie. Het wordt vervangen door een [trigger.](concepts-pipeline-execution-triggers.md)
- De eigenschappen voor beleid en beschikbaarheid worden niet ondersteund in de huidige versie. De begintijd voor een pijplijn is afhankelijk van [triggers.](concepts-pipeline-execution-triggers.md)
- Scoped-gegevenssets (gegevenssets gedefinieerd in een pijplijn) worden niet ondersteund in de huidige versie.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudie voor stapsgewijze instructies voor het maken van pijplijnen en gegevenssets met behulp van een van deze hulpprogramma's of SDK's.

- [Snelstartgids: een gegevensfactory maken met .NET](quickstart-create-data-factory-dot-net.md)
- [Snelstart: een gegevensfabriek maken met PowerShell](quickstart-create-data-factory-powershell.md)
- [Snelstart: een gegevensfabriek maken met REST API](quickstart-create-data-factory-rest-api.md)
- [Snelstart: een gegevensfabriek maken met Azure-portal](quickstart-create-data-factory-portal.md)
