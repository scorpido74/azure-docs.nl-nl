---
title: Gegevens kopiëren van en naar SQL Server
description: Meer informatie over het verplaatsen van gegevens naar en van SQL Server Data Base die on-premises of in een Azure-VM is door gebruik te maken van Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2020
ms.openlocfilehash: df100d73bd137f0c471079af976cf657353fd184
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816810"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar SQL Server met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-sqlserver-connector.md)
> * [Huidige versie](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar een SQL Server-Data Base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SQL Server-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

U kunt gegevens uit een SQL Server-Data Base kopiëren naar elk ondersteund Sink-gegevens archief. Of u kunt gegevens van elk ondersteund brongegevens archief kopiëren naar een SQL Server-Data Base. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze SQL Server-connector ondersteunt met name:

- SQL Server versie 2005 en hoger.
- Gegevens kopiëren met behulp van SQL of Windows-verificatie.
- Als bron, waarbij gegevens worden opgehaald met behulp van een SQL-query of een opgeslagen procedure. U kunt er ook voor kiezen om een parallelle kopie van SQL Server bron te selecteren, de [parallelle kopie van SQL database](#parallel-copy-from-sql-database) sectie voor meer informatie.
- Als sink wordt er automatisch een doel tabel gemaakt als deze niet bestaat op basis van het bron schema. het toevoegen van gegevens aan een tabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren. 

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) wordt niet ondersteund.

>[!NOTE]
>SQL Server [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) wordt nu niet ondersteund door deze connector. U kunt dit probleem omzeilen door een [algemene ODBC-Connector](connector-odbc.md) en een SQL Server ODBC-stuur programma te gebruiken. Volg [deze richt lijnen voor het](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) downloaden van ODBC-stuur Programma's en het Connection String van configuraties.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de SQL Server-database connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de SQL Server gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **sqlserver**. | Ja |
| Verbindings |Geef **verbindings reeks** gegevens op die nodig zijn om verbinding te maken met de SQL Server-Data Base met behulp van SQL-verificatie of Windows-verificatie. Raadpleeg de volgende voor beelden.<br/>U kunt ook een wacht woord in Azure Key Vault plaatsen. Als de SQL-verificatie wordt uitgevoerd, haalt u de `password` configuratie uit het Connection String. Zie voor meer informatie het JSON-voor beeld dat volgt op de tabel en [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Geef een gebruikers naam op als u Windows-verificatie gebruikt. Een voor beeld is **domeinnaam domein \\ naam**. |Nee |
| wachtwoord |Geef een wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als **SecureString** om het veilig op te slaan in azure Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Nee |
| connectVia | Deze [Integration runtime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

>[!TIP]
>Als u op een fout met de fout code ' UserErrorFailedToConnectToSqlServer ' en een bericht ziet dat de sessie limiet voor de data base XXX is en is bereikt, voegt u toe `Pooling=false` aan uw Connection String en probeert u het opnieuw.

**Voor beeld 1: SQL-verificatie gebruiken**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: SQL-verificatie gebruiken met een wacht woord in Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 3: Windows-verificatie gebruiken**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de SQL Server-gegevensset worden ondersteund.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens van en naar een SQL Server-Data Base:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **SqlServerTable**. | Ja |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

**Voorbeeld**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die u kunt gebruiken om activiteiten te definiëren. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SQL Server bron en Sink.

### <a name="sql-server-as-a-source"></a>SQL Server als bron

>[!TIP]
>Als u gegevens van SQL Server efficiënt wilt laden met behulp van gegevens partitioneren, kunt u meer informatie uit de [parallelle kopie van SQL database](#parallel-copy-from-sql-database).

Als u gegevens wilt kopiëren uit SQL Server, stelt u het bron type in de Kopieer activiteit in op **SqlSource**. De volgende eigenschappen worden ondersteund in de sectie bron van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SqlSource**. | Ja |
| sqlReaderQuery |Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Deze eigenschap is de naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Deze para meters zijn voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-of waardeparen. De namen en het hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |Nee |
| isolationLevel | Hiermee geeft u het vergrendelings gedrag van de trans actie voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **snap shot**. Als u niets opgeeft, wordt het standaard isolatie niveau van de data base gebruikt. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens uit SQL Server. <br>Toegestane waarden zijn: **geen** (standaard), **PhysicalPartitionsOfTable**en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen niet `None` ), is de mate van parallelle uitvoering om gegevens van SQL Server gelijktijdig te laden, bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling van de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen wanneer de partitie optie niet is `None` . | Nee |
| ***Onder `partitionSettings` :*** | | |
| partitionColumnName | Geef de naam op van de bron kolom **in geheel getal of datum/tijd-type** dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de index of de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als de partitie kolom.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook  `?AdfDynamicRangePartitionCondition ` in de component WHERE. Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-database) voor een voor beeld. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom voor het splitsen van het partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.  <br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-database) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom voor het splitsen van een partitie bereik. Deze waarde wordt gebruikt om de partitie stride te bepalen, niet voor het filteren van de rijen in de tabel. Alle rijen in het tabel-of query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br>Toep assen wanneer de partitie optie is `DynamicRange` . Zie de sectie [parallel kopiëren van SQL database](#parallel-copy-from-sql-database) voor een voor beeld. | Nee |

**Punten om te noteren:**

- Als **sqlReaderQuery** is opgegeven voor **SqlSource**, voert de Kopieer activiteit deze query uit op basis van de SQL Server bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven als voor de opgeslagen procedure para meters worden gebruikt.
- Als u **sqlReaderQuery** of **sqlReaderStoredProcedureName**niet opgeeft, worden de kolommen die zijn gedefinieerd in de sectie ' Structure ' van de JSON van de gegevensset gebruikt voor het maken van een query. De query `select column1, column2 from mytable` wordt uitgevoerd op basis van de SQL Server. Als de definitie van de gegevensset niet de structuur ' Structure ' bevat, worden alle kolommen geselecteerd in de tabel.

**Voor beeld: SQL-query gebruiken**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voor beeld: een opgeslagen procedure gebruiken**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**De definitie van de opgeslagen procedure**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server als Sink

> [!TIP]
> Meer informatie over het ondersteunde schrijf gedrag, configuraties en aanbevolen procedures voor het [laden van gegevens in SQL Server](#best-practice-for-loading-data-into-sql-server).

Als u gegevens wilt kopiëren naar SQL Server, stelt u het sink-type in de Kopieer activiteit in op **SqlSink**. De volgende eigenschappen worden ondersteund in het gedeelte Sink van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SqlSink**. | Ja |
| preCopyScript |Met deze eigenschap geeft u een SQL-query op voor het uitvoeren van de Kopieer activiteit voordat u gegevens naar SQL Server schrijft. Het wordt slechts één keer per Kopieer bewerking aangeroepen. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. |Nee |
| tableOption | Hiermee wordt aangegeven of [de Sink-tabel automatisch](copy-activity-overview.md#auto-create-sink-tables) moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer Sink de opgeslagen procedure specificeert. Toegestane waarden zijn: `none` (standaard), `autoCreate` . |Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die definieert hoe bron gegevens in een doel tabel worden toegepast. <br/>Deze opgeslagen procedure wordt *per batch aangeroepen*. Voor bewerkingen die slechts één keer worden uitgevoerd en niets te doen met bron gegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de `preCopyScript` eigenschap.<br>Bekijk het voor beeld van [het aanroepen van een opgeslagen procedure vanuit een SQL-Sink](#invoke-a-stored-procedure-from-a-sql-sink). | Nee |
| storedProcedureTableTypeParameterName |De parameter naam van het tabel type dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType |De naam van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Met de Kopieer activiteit worden de gegevens in een tijdelijke tabel met dit tabel type beschikbaar gemaakt. Met de opgeslagen procedure code kunt u vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |Nee |
| storedProcedureParameters |Para meters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-en waardeparen. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | Nee |
| writeBatchSize |Het aantal rijen dat *per batch*in de SQL-tabel moet worden ingevoegd.<br/>Toegestane waarden zijn gehele getallen voor het aantal rijen. Standaard bepaalt Azure Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. |Nee |
| writeBatchTimeout |Met deze eigenschap geeft u de wacht tijd op waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt.<br/>Toegestane waarden zijn voor de time span. Een voor beeld 00:30:00 is 30 minuten. Als er geen waarde is opgegeven, wordt de time-out standaard ingesteld op ' 02:00:00 '. |Nee |

**Voor beeld 1: gegevens toevoegen**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Voor beeld 2: een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie over [het aanroepen van een opgeslagen procedure vanuit een SQL-Sink](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Parallelle kopie van SQL database

De SQL Server-connector in de Kopieer activiteit biedt ingebouwde gegevens partities voor het parallel kopiëren van gegevens. U kunt opties voor gegevens partities vinden op het tabblad **bron** van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-sql-server/connector-sql-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Kopieer activiteit parallelle query's uit op uw SQL Server bron om gegevens per partitie te laden. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en met elke query wordt een deel van de gegevens opgehaald uit uw SQL Server.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw SQL Server laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand kunt u het beste naar een map schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel met fysieke partities.        | **Partitie optie**: fysieke partities van tabel. <br><br/>Tijdens de uitvoering worden de fysieke partities automatisch door Data Factory gedetecteerd en worden de gegevens gekopieerd op partities. |
| Volledige belasting van een grote tabel, zonder fysieke partities, met een geheel getal of datum/tijd-kolom voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Partitie kolom** (optioneel): Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als u niets opgeeft, wordt de kolom index of primaire sleutel gebruikt.<br/>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in de tabel worden gepartitioneerd en gekopieerd. Indien niet opgegeven, kopieer activiteit automatische detectie van de waarden.<br><br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl een kolom met een geheel getal of datum/tijd is voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren.<br>De **bovengrens** van de partitie en de **ondergrens** van de partitie (optioneel): Geef op of u de partitie-stride wilt bepalen. Dit is niet voor het filteren van de rijen in de tabel, alle rijen in het query resultaat worden gepartitioneerd en gekopieerd. Als deze optie niet is opgegeven, wordt de waarde automatisch gedetecteerd met de Kopieer activiteit.<br><br>Tijdens de uitvoering wordt Data Factory vervangen `?AdfRangePartitionColumnName` door de werkelijke kolom naam en het waardebereik voor elke partitie, en verzonden naar SQL Server. <br>Als uw partitie kolom ' ID ' bijvoorbeeld waarden bereik van 1 tot 100 heeft en u de ondergrens instelt op 20 en de bovengrens als 80, met parallelle kopie als 4, Data Factory worden gegevens opgehaald met vier partities-Id's in bereik <= 20, [21, 50], [51, 80] en >= 81, respectievelijk. |

Aanbevolen procedures voor het laden van gegevens met de optie partitie:

1. Kies een onderscheidende kolom als partitie kolom (zoals primaire sleutel of unieke sleutel) om te voor komen dat gegevens scheef trekken. 
2. Als de tabel een ingebouwde partitie heeft, gebruikt u partitie optie fysieke partities van tabel om betere prestaties te krijgen.  
3. Als u Azure Integration Runtime gebruikt om gegevens te kopiëren, kunt u grotere '[Data Integration units (DIU)](copy-activity-performance-features.md#data-integration-units)' (>4) instellen om meer computer bronnen te gebruiken. Controleer de toepasselijke scenario's.
4. "[Mate van kopiëren van parallellisme](copy-activity-performance-features.md#parallel-copy)" bepalen van de partitie nummers, het instellen van dit getal is te groot. Dit kan de prestaties nadelig beïnvloeden, het is raadzaam om dit aantal in te stellen als (DIU of het aantal zelf-HOSTende IR-knoop punten) * (2 tot 4).

**Voor beeld: volledige belasting van een grote tabel met fysieke partities**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Aanbevolen procedure voor het laden van gegevens in SQL Server

Wanneer u gegevens naar SQL Server kopieert, hebt u mogelijk een ander schrijf gedrag nodig:

- [Toevoegen](#append-data): mijn bron gegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): mijn bron gegevens hebben zowel toevoegingen als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil de hele dimensie tabel elke keer opnieuw laden.
- [Schrijven met aangepaste logica](#write-data-with-custom-logic): Ik heb extra verwerking nodig vóór de laatste invoeging in de doel tabel.

Zie de betreffende secties voor informatie over het configureren van Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Het toevoegen van gegevens is het standaard gedrag van deze SQL Server Sink-connector. Azure Data Factory voert een bulksgewijze invoer uit om uw tabel efficiënt te schrijven. U kunt de bron en Sink dienovereenkomstig configureren in de Kopieer activiteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens te kopiëren hebt, kunt u alle records bulksgewijs laden in een faserings tabel met behulp van de Kopieer activiteit en vervolgens een opgeslagen procedure-activiteit uitvoeren om een instructie [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15) of insert/update toe te passen in één afbeelding. 

De Kopieer activiteit biedt momenteel geen systeem eigen ondersteuning voor het laden van gegevens in een tijdelijke data base-tabel. Er is een geavanceerde manier om deze in te stellen met een combi natie van meerdere activiteiten. Raadpleeg [SQL database scenario's voor bulksgewijs Upsert optimaliseren](https://github.com/scoriani/azuresqlbulkupsert). Hieronder ziet u een voor beeld van het gebruik van een permanente tabel als fase ring.

In Azure Data Factory kunt u bijvoorbeeld een pijp lijn maken met een **Kopieer activiteit** die is gekoppeld aan een **opgeslagen procedure activiteit**. De eerste kopie kopieert gegevens uit uw bron archief naar een SQL Server faserings tabel, bijvoorbeeld **UpsertStagingTable**, als de tabel naam in de gegevensset. Vervolgens roept de laatste een opgeslagen procedure aan om bron gegevens van de faserings tabel samen te voegen in de doel tabel en de faserings tabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw data base een opgeslagen procedure met SAMENVOEG logica, zoals in het volgende voor beeld, dat verwijst naar de vorige opgeslagen procedure activiteit. Stel dat het doel de **marketing** tabel is met drie kolommen: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Optie 2:** U kunt ervoor kiezen om [een opgeslagen procedure binnen de Kopieer activiteit](#invoke-a-stored-procedure-from-a-sql-sink)aan te roepen. Met deze benadering wordt elke batch (zoals bepaald door de `writeBatchSize` eigenschap) in de bron tabel uitgevoerd in plaats van bulksgewijs invoegen als de standaard benadering in de Kopieer activiteit.

### <a name="overwrite-the-entire-table"></a>De volledige tabel overschrijven

U kunt de eigenschap **preCopyScript** in een Sink voor kopieer activiteiten configureren. In dit geval voert Azure Data Factory voor elke Kopieer activiteit die wordt uitgevoerd eerst het script uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de volledige tabel wilt overschrijven met de meest recente gegevens, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen voor het schrijven van gegevens met aangepaste logica zijn vergelijkbaar met die in de sectie [Upsert-gegevens](#upsert-data) . Wanneer u extra verwerking wilt Toep assen voordat de laatste invoeging van bron gegevens in de doel tabel, kunt u laden naar een faserings tabel en vervolgens de opgeslagen procedure-activiteit aanroepen of een opgeslagen procedure aanroepen in Sink voor kopieer activiteiten om gegevens toe te passen.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Een opgeslagen procedure aanroepen vanuit een SQL-Sink

Wanneer u gegevens naar SQL Server Data Base kopieert, kunt u ook een door de gebruiker opgegeven opgeslagen procedure met aanvullende para meters voor elke batch van de bron tabel configureren en aanroepen. De functie opgeslagen procedure maakt gebruik van [para meters met tabel waarden](https://msdn.microsoft.com/library/bb675163.aspx).

U kunt een opgeslagen procedure gebruiken wanneer ingebouwde Kopieer mechanismen het doel niet behouden. Een voor beeld hiervan is wanneer u een extra verwerking wilt Toep assen vóór het uiteindelijke invoegen van bron gegevens in de doel tabel. Sommige extra verwerkings voorbeelden zijn wanneer u kolommen wilt samen voegen, aanvullende waarden wilt opzoeken en in meer dan één tabel wilt invoegen.

In het volgende voor beeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert te maken in een tabel in de SQL Server-Data Base. Stel dat de invoer gegevens en de provinciale **marketing** tabel drie kolommen hebben: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** en pas deze alleen toe voor een specifieke categorie met de naam ProductA.

1. Definieer in uw data base het tabel type met de naam **sqlWriterTableType**. Het schema van het tabel type is hetzelfde als het schema dat wordt geretourneerd door de invoer gegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieer in uw data base de opgeslagen procedure met de naam **sqlWriterStoredProcedureName**. De invoer gegevens van de opgegeven bron worden verwerkt en samen voegingen in de uitvoer tabel. De parameter naam van het tabel type in de opgeslagen procedure is hetzelfde als **TableName** gedefinieerd in de gegevensset.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definieert u de sectie **SQL-Sink** in de Kopieer activiteit als volgt:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Toewijzing van gegevens type voor SQL Server

Wanneer u gegevens van en naar SQL Server kopieert, worden de volgende toewijzingen gebruikt van SQL Server gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| SQL Server gegevens type | Azure Data Factory tussentijds gegevens type |
|:--- |:--- |
| bigint |Int64 |
| binair |Byte [] |
| bit |Boolean |
| char |Teken reeks, char [] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Date time offset |Date time offset |
| Decimaal |Decimaal |
| FILESTREAM-kenmerk (varbinary (max)) |Byte [] |
| Float |Dubbel |
| image |Byte [] |
| int |Int32 |
| money |Decimaal |
| nchar |Teken reeks, char [] |
| ntext |Teken reeks, char [] |
| numeriek |Decimaal |
| nvarchar |Teken reeks, char [] |
| werkelijk |Enkel |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimaal |
| sql_variant |Object |
| tekst |Teken reeks, char [] |
| tijd |TimeSpan |
| tijdstempel |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Tekenreeks |

>[!NOTE]
> Voor gegevens typen die worden toegewezen aan het type van de decimale waarde, wordt op dat moment de functie voor het kopiëren van de precisie ondersteund tot 28. Als u gegevens hebt die een grotere nauw keurigheid dan 28 vereisen, kunt u overwegen om te converteren naar een teken reeks in een SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Always Encrypted gebruiken

Wanneer u gegevens kopieert van/naar SQL Server met [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15), gebruikt u de [algemene ODBC-Connector](connector-odbc.md) en SQL Server ODBC-stuur programma via zelf-hostende Integration runtime. Deze SQL Server-connector biedt geen ondersteuning voor Always Encrypted nu. 

Met name:

1. Stel een zelf-hostende Integration Runtime in als u er nog geen hebt. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.

2. Down load hier het 64-bits ODBC-stuur programma voor SQL Server en Installeer [Dit](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)op de Integration runtime machine. Meer informatie over hoe dit stuur programma kan [Always encrypted gebruiken met het ODBC-stuur programma voor SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-ver15#using-the-azure-key-vault-provider).

3. Maak een gekoppelde service met een ODBC-type om verbinding te maken met uw SQL database. Als u SQL-verificatie wilt gebruiken, geeft u de ODBC-connection string op onder en selecteert u **basis** verificatie om de gebruikers naam en het wacht woord in te stellen.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Maak de gegevensset en kopieer activiteit met het ODBC-type dienovereenkomstig. Meer informatie in het artikel over [ODBC-connectors](connector-odbc.md) .

## <a name="troubleshoot-connection-issues"></a>Verbindingsproblemen oplossen

1. Configureer uw SQL Server-exemplaar om externe verbindingen te accepteren. Start **SQL Server Management Studio**, klik met de rechter muisknop op **Server**en selecteer **Eigenschappen**. Selecteer **verbindingen** in de lijst en schakel het selectie vakje **externe verbindingen met deze server toestaan** in.

    ![Externe verbindingen inschakelen](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zie [Configure the RAS server Configuration option](https://msdn.microsoft.com/library/ms191464.aspx)(Engelstalig) voor gedetailleerde stappen.

2. Start **SQL Server Configuration Manager**. Vouw **SQL Server netwerk configuratie** voor de gewenste instantie uit en selecteer **protocollen voor MSSQLSERVER**. Protocollen worden weer gegeven in het rechterdeel venster. Schakel TCP/IP in door met de rechter muisknop op **TCP/IP** te klikken en **inschakelen**te selecteren.

    ![TCP/IP inschakelen](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zie [een server netwerkprotocol in-of uitschakelen](https://msdn.microsoft.com/library/ms191294.aspx)voor meer informatie en alternatieve manieren om het TCP/IP-protocol in te scha kelen.

3. Dubbel klik in hetzelfde venster op **TCP/IP** om het venster **TCP/IP-eigenschappen** te openen.
4. Schakel over naar het tabblad **IP-adressen** . Schuif omlaag om de sectie **IPAll** weer te geven. Noteer de **TCP-poort**. De standaard waarde is **1433**.
5. Maak een **regel voor de Windows Firewall** op de computer om binnenkomend verkeer via deze poort toe te staan. 
6. **Verbinding controleren**: als u een volledig gekwalificeerde naam wilt gebruiken om verbinding te maken met SQL Server, gebruikt u SQL Server Management Studio vanaf een andere computer. Een voorbeeld is `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
