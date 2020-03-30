---
title: Gegevens van en naar SQL Server kopiëren
description: Meer informatie over het verplaatsen van gegevens van en naar SQL Server-database die on-premises of in een Azure VM is met Azure Data Factory.
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
ms.date: 03/12/2020
ms.openlocfilehash: e7f91d60a0e181690859da64e491c4c0fa1e1a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238723"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Gegevens van en naar SQL Server kopiëren met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-sqlserver-connector.md)
> * [Huidige versie](connector-sql-server.md)

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van en naar een SQL Server-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van de kopie activiteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SQL Server-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)

U gegevens uit een SQL Server-database kopiëren naar een ondersteund sinkdataarchief. U ook gegevens uit elk ondersteund brongegevensarchief kopiëren naar een SQL Server-database. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Deze SQL Server-connector ondersteunt met name:

- SQL Server-versies 2016, 2014, 2012, 2008 R2, 2008 en 2005.
- Gegevens kopiëren met SQL- of Windows-verificatie.
- Als bron u gegevens ophalen met behulp van een SQL-query of een opgeslagen procedure.
- Als gootsteen, het toevoegen van gegevens aan een doeltabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) wordt niet ondersteund.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) wordt nu niet ondersteund door deze connector. Om te werken, u een [algemene ODBC-connector](connector-odbc.md) en een SQL Server ODBC-stuurprogramma gebruiken. Volg [deze richtlijnen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) met ODBC-stuurprogramma-download- en verbindingstekenreeksconfiguraties.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de SQL Server-databaseconnector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de SQL Server-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **SqlServer**. | Ja |
| Connectionstring |**Verbindingsgegevens opgevenTekenreeksgegevens** die nodig zijn om verbinding te maken met de SQL Server-database met SQL-verificatie of Windows-verificatie. Raadpleeg de volgende voorbeelden.<br/>U ook een wachtwoord in Azure Key Vault plaatsen. Als het SQL-verificatie is, haalt u de `password` configuratie uit de verbindingstekenreeks. Zie het VOORBEELD JSON na de tabel en [Store-referenties in Azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie. |Ja |
| userName |Geef een gebruikersnaam op als u Windows-verificatie gebruikt. Een voorbeeld is **de gebruikersnaam van de\\domeinnaam**. |Nee |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. Markeer dit veld als **SecureString** om het veilig op te slaan in Azure Data Factory. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Nee |
| connectVia | Deze [integratieruntime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!TIP]
>Als u een fout hebt opgetreden met de foutcode 'UserErrorFailedToConnectToSqlServer' en een bericht als `Pooling=false` 'De sessielimiet voor de database is XXX en is bereikt', voeg dan toe aan uw verbindingstekenreeks en probeer het opnieuw.

**Voorbeeld 1: SQL-verificatie gebruiken**

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

**Voorbeeld 2: SQL-verificatie gebruiken met een wachtwoord in Azure Key Vault**

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

**Voorbeeld 3: Windows-verificatie gebruiken**

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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SQL Server-gegevensset.

Als u gegevens van en naar een SQL Server-database wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **SqlServerTable**. | Ja |
| schema | Naam van het schema. |Nee voor bron, Ja voor gootsteen  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor gootsteen  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee voor bron, Ja voor gootsteen |

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SQL Server-bron en -sink.

### <a name="sql-server-as-a-source"></a>SQL Server als bron

Als u gegevens uit SQL Server wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **SqlSource.** De volgende eigenschappen worden ondersteund in de sectie bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op **SqlSource**. | Ja |
| sqlReaderQuery |Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Deze eigenschap is de naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Deze parameters zijn voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- of waardeparen. De namen en de omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| Isolationlevel | Hiermee geeft u het transactievergrendelingsgedrag voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |

**Aandachtspunten:**

- Als **sqlReaderQuery** is opgegeven voor **SqlSource,** voert de kopieeractiviteit deze query uit op de SQL Server-bron om de gegevens te krijgen. U ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters op te** geven als de opgeslagen procedure parameters bevat.
- Als u **sqlReaderQuery** of **sqlReaderStoredProcedureName**niet opgeeft, worden de kolommen die zijn gedefinieerd in de sectie 'structuur' van de gegevensset JSON gebruikt om een query te construeren. De `select column1, column2 from mytable` query wordt uitgevoerd tegen de SQL Server. Als de gegevenssetdefinitie geen 'structuur' heeft, worden alle kolommen geselecteerd uit de tabel.

**Voorbeeld: SQL-query gebruiken**

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

**Voorbeeld: Een opgeslagen procedure gebruiken**

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

### <a name="sql-server-as-a-sink"></a>SQL Server als sink

> [!TIP]
> Meer informatie over het ondersteunde schrijfgedrag, configuraties en aanbevolen procedures van [Best practices voor het laden van gegevens in SQL Server.](#best-practice-for-loading-data-into-sql-server)

Als u gegevens wilt kopiëren naar SQL Server, stelt u het sinktype in de kopieeractiviteit in op **SqlSink**. De volgende eigenschappen worden ondersteund in de sectie copy activity sink:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteitmoet worden ingesteld op **SqlSink**. | Ja |
| writeBatchSize |Aantal rijen dat *per batch*moet worden ingevoegd in de SQL-tabel .<br/>Toegestane waarden zijn gehele getallen voor het aantal rijen. Azure Data Factory bepaalt standaard dynamisch de juiste batchgrootte op basis van de rijgrootte. |Nee |
| writeBatchTimeout |Deze eigenschap geeft de wachttijd op voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br/>Toegestane waarden zijn voor de tijdspanne. Een voorbeeld is "00:30:00" gedurende 30 minuten. Als er geen waarde is opgegeven, wordt de time-out standaard ingesteld op '02:00:00'. |Nee |
| preCopyScript |Met deze eigenschap wordt een SQL-query opgegeven voor de kopieeractiviteit die moet worden uitgevoerd voordat gegevens in SQL Server worden geschreven. Het wordt slechts één keer per exemplaar aangeroepen. U deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. |Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die bepaalt hoe brongegevens in een doeltabel kunnen worden toegepast. <br/>Deze opgeslagen procedure wordt *per partij ingeroepen.* Voor bewerkingen die slechts één keer worden uitgevoerd en die niets te maken `preCopyScript` hebben met brongegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de eigenschap. | Nee |
| storedProcedureTableTypeParameterName |De parameternaam van het tabeltype dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType sqlWriterTableType |De tabeltypenaam die moet worden gebruikt in de opgeslagen procedure. Met de kopieeractiviteit worden de gegevens die worden verplaatst beschikbaar in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- en waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. | Nee |
| tabelOptie | Hiermee geeft u op of de gootsteentabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bronschema. Het maken van automatische tabel wordt niet ondersteund wanneer de opslagprocedure is opgegeven of gefaseerde kopie is geconfigureerd in kopieeractiviteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |

**Voorbeeld 1: Gegevens toevoegen**

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Voorbeeld 2: Een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie van [Een opgeslagen procedure aanroepen vanuit een SQL-sink](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Aanbevolen procedures voor het laden van gegevens in SQL Server

Wanneer u gegevens kopieert naar SQL Server, hebt u mogelijk ander schrijfgedrag nodig:

- [Toevoegen](#append-data): Mijn brongegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): Mijn brongegevens hebben zowel inserts als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil elke keer de hele dimensietabel opnieuw laden.
- [Schrijf met aangepaste logica:](#write-data-with-custom-logic)Ik heb extra verwerking nodig voordat de uiteindelijke invoeging in de doeltabel.

Bekijk de respectievelijke secties voor het configureren in Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Het toevoegen van gegevens is het standaardgedrag van deze SQL Server-sinkconnector. Azure Data Factory doet een bulkinsert om efficiënt naar uw tabel te schrijven. U de bron configureren en dienovereenkomstig zinken in de kopieeractiviteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens moet kopiëren, gebruikt u de volgende benadering om een upsert uit te voeren: 

- Gebruik eerst een [tijdelijke tabel](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) om alle records in bulk te laden met behulp van de kopieeractiviteit. Omdat bewerkingen tegen tijdelijke tabellen niet worden geregistreerd, u miljoenen records in seconden laden.
- Voer een opgeslagen procedureactiviteit uit in Azure Data Factory om een [instructie MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) of INSERT/UPDATE toe te passen. Gebruik de tijdelijke tabel als bron om alle updates of inserts uit te voeren als één transactie. Op deze manier wordt het aantal retourvluchten en logoperaties verminderd. Aan het einde van de opgeslagen procedureactiviteit kan de tijdelijke tabel worden afgekapt om klaar te zijn voor de volgende upsert-cyclus.

Als voorbeeld u in Azure Data Factory een pijplijn maken met een **kopieeractiviteit** die is gekoppeld aan een **activiteit Opgeslagen procedure**. De voormalige kopieën van gegevens uit uw bronarchief naar een tijdelijke databasetabel, bijvoorbeeld **##UpsertTempTable,** als de tabelnaam in de gegevensset. Vervolgens beroept de laatste zich op een opgeslagen procedure om brongegevens uit de tijdelijke tabel samen te voegen in de doeltabel en de tijdelijke tabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw database een opgeslagen procedure met MERGE-logica, zoals het volgende voorbeeld, waarop wordt gewezen vanaf de vorige opgeslagen procedureactiviteit. Stel dat het doel de **tabel Marketing** is met drie kolommen: **ProfileID**, **Status**en **Categorie**. Doe de upsert op basis van de **kolom ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Optie 2:** U er ook voor kiezen om [een opgeslagen procedure in de kopieeractiviteit aan](#invoke-a-stored-procedure-from-a-sql-sink)te roepen. Met deze benadering wordt elke rij in de brontabel uitgevoerd in plaats van bulkinsertals standaardbenadering in de kopieeractiviteit te gebruiken, wat niet geschikt is voor grootschalige upsert.

### <a name="overwrite-the-entire-table"></a>De hele tabel overschrijven

U de eigenschap **preCopyScript** configureren in een kopieeractiviteitsgoot. In dit geval voert Azure Data Factory voor elke kopieeractiviteit die wordt uitgevoerd, het script eerst uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de hele tabel met de meest recente gegevens wilt overschrijven, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen om gegevens met aangepaste logica te schrijven zijn vergelijkbaar met die beschreven in de [sectie Upsert-gegevens.](#upsert-data) Wanneer u extra verwerking moet toepassen voordat brongegevens definitief worden ingevoegd in de doeltabel, u op grote schaal een van de twee dingen doen: 

- Laden naar een tijdelijke tabel en vervolgens een aanroepen van een opgeslagen procedure. 
- Beroep doen op een opgeslagen procedure tijdens het kopiëren.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Een opgeslagen procedure aanroepen vanuit een SQL-sink

Wanneer u gegevens kopieert naar een SQL Server-database, u ook een door de gebruiker opgegeven opgeslagen procedure met aanvullende parameters configureren en aanroepen. De functie opgeslagen procedure maakt gebruik van [parameters met tabelwaarde.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> Een beroep doen op een opgeslagen procedure verwerkt de gegevensrij voor rij in plaats van met behulp van een bulkbewerking, die we niet aanbevelen voor grootschalige kopiëren. Meer informatie over [De beste praktijk voor het laden van gegevens in SQL Server](#best-practice-for-loading-data-into-sql-server).

U een opgeslagen procedure gebruiken wanneer ingebouwde kopieermechanismen het doel niet dienen. Een voorbeeld hiervan is wanneer u extra verwerking wilt toepassen voordat brongegevens definitief worden ingevoegd in de doeltabel. Enkele extra verwerkingsvoorbeelden zijn wanneer u kolommen wilt samenvoegen, extra waarden wilt opzoeken en gegevens in meer dan één tabel wilt invoegen.

In het volgende voorbeeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert uit te voeren in een tabel in de SQL Server-database. Stel dat de invoergegevens en de tabel **Sink Marketing** elk drie kolommen hebben: **ProfileID**, **Status**en **Categorie**. Doe de upsert op basis van de **ProfileID** kolom, en alleen toe te passen voor een specifieke categorie genaamd "ProductA".

1. Definieer in uw database het tabeltype met dezelfde naam als **sqlWriterTableType.** Het schema van het tabeltype is hetzelfde als het schema dat wordt geretourneerd door uw invoergegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Definieer in uw database de opgeslagen procedure met dezelfde naam als **sqlWriterStoredProcedureName.** Het verwerkt invoergegevens van de opgegeven bron en wordt samengevoegd in de uitvoertabel. De parameternaam van het tabeltype in de opgeslagen procedure is dezelfde als **tabelNaam** die in de gegevensset is gedefinieerd.

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

3. Definieer in Azure Data Factory de sectie **SQL-sink** in de kopieeractiviteit als volgt:

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

## <a name="data-type-mapping-for-sql-server"></a>Toewijzing van gegevenstype voor SQL Server

Wanneer u gegevens van en naar SQL Server kopieert, worden de volgende toewijzingen gebruikt van SQL Server-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| SQL Server-gegevenstype | Interim-gegevenstype Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binair |Byte |
| bit |Booleaans |
| Char |Tekenreeks, Char[] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Datumtijdverschuiving |Datumtijdverschuiving |
| Decimal |Decimal |
| FILESTREAM-kenmerk (varbinary(max.) |Byte |
| Drijvend |Double |
| installatiekopie |Byte |
| int |Int32 |
| Geld |Decimal |
| Nchar |Tekenreeks, Char[] |
| ntekst |Tekenreeks, Char[] |
| numeriek |Decimal |
| nvarchar |Tekenreeks, Char[] |
| real |Enkel |
| rijversie |Byte |
| smalldatetijd |DateTime |
| smallint |Int16 |
| kleingeld |Decimal |
| sql_variant |Object |
| tekst |Tekenreeks, Char[] |
| tijd |TimeSpan |
| tijdstempel |Byte |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary varbinary |Byte |
| varchar |Tekenreeks, Char[] |
| xml |Xml |

>[!NOTE]
> Voor gegevenstypen die worden toegewezen aan het tussentijdse type Decimal, ondersteunt Azure Data Factory momenteel precisie tot 28. Als u gegevens hebt waarvoor een precisie vereist is die groter is dan 28, u overwegen om te converteren naar een tekenreeks in een SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Activiteitseigenschappen getMetadata

Voor meer informatie over de eigenschappen, controleert [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Verbindingsproblemen oplossen

1. Configureer uw SQL Server-exemplaar om externe verbindingen te accepteren. Start **SQL Server Management Studio,** klik met de rechtermuisknop op **server**en selecteer **Eigenschappen**. Selecteer **Verbindingen** in de lijst en schakel het selectievakje **Externe verbindingen met deze server toestaan** in.

    ![Externe verbindingen inschakelen](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Zie [De optie voor de configuratie van de rastoegangsserver configureren](https://msdn.microsoft.com/library/ms191464.aspx)voor gedetailleerde stappen.

2. **SQL Server Configuration Manager starten**. Vouw **SQL Server-netwerkconfiguratie** uit voor de instantie die u wilt en selecteer **Protocollen voor MSSQLSERVER**. Protocollen worden in het rechterdeelvenster weergegeven. Tcp/IP inschakelen door met de rechtermuisknop op **TCP/IP** te klikken en **Inschakelen te selecteren.**

    ![TCP/IP inschakelen](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Zie [Een servernetwerkprotocol inschakelen of uitschakelen](https://msdn.microsoft.com/library/ms191294.aspx)voor meer informatie en alternatieve manieren om TCP/IP-protocol in te schakelen.

3. Dubbelklik in hetzelfde venster op **TCP/IP** om het venster **TCP/IP-eigenschappen** te starten.
4. Ga naar het tabblad **IP-adressen.** Scrol naar beneden om de sectie **IPAll** te bekijken. Noteer de **TCP-poort**. De standaardinstelling is **1433**.
5. Maak een **regel voor de Windows Firewall** op de machine om binnenkomend verkeer via deze poort toe te staan. 
6. **Verbinding verifiëren:** Als u verbinding wilt maken met SQL Server met een volledig gekwalificeerde naam, gebruikt u SQL Server Management Studio vanaf een andere machine. Een voorbeeld is `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
