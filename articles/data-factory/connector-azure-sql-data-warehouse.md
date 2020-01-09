---
title: Gegevens kopiëren en transformeren in azure Synapse Analytics
description: Meer informatie over het kopiëren van gegevens van en naar Azure Synapse Analytics en het transformeren van gegevens in azure Synapse Analytics met behulp van Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 02fb46fe764c8e34b440a1a0388fc31f7615cfb3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440739"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens in azure Synapse Analytics (voorheen Azure SQL Data Warehouse) kopiëren en transformeren met behulp van Azure Data Factory 

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Huidige versie](connector-azure-sql-data-warehouse.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Synapse Analytics en gegevens stroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Synapse Analytics-connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Voor kopieer activiteiten ondersteunt deze Azure Synapse Analytics-connector deze functies:

- Gegevens kopiëren met behulp van SQL-verificatie en tokenverificatie van Azure Active Directory (Azure AD)-toepassing met een service-principal of beheerde identiteiten voor Azure-resources.
- Als een bron ophalen van gegevens met behulp van een SQL-query of een opgeslagen procedure.
- Laad gegevens met behulp van [poly base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) of Copy- [instructie](#use-copy-statement) (preview) of bulksgewijze insert als sink. We raden u aan om een poly base-of COPY-instructie (preview) te geven voor betere Kopieer prestaties.

> [!IMPORTANT]
> Als u gegevens kopiëren met behulp van Azure Data Factory Integration Runtime, configureert u een [Azure SQL-serverfirewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) zodat Azure-services toegang hebben tot de server.
> Als u gegevens kopiëren met behulp van de zelf-hostende integratieruntime, configureert u de firewall van de Azure SQL-server zodat het juiste IP-adresbereik. Dit bereik bevat het IP-adres van de computer dat wordt gebruikt om verbinding te maken met Azure Synapse Analytics.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Gebruik poly Base om gegevens te laden in azure Synapse Analytics om de beste prestaties te krijgen. De sectie [poly Base gebruiken voor het laden van gegevens in azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) bevat details. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een Azure Synapse Analytics-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Azure Synapse Analytics-service:

| Eigenschap            | Beschrijving                                                  | Verplicht                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | De eigenschap type moet worden ingesteld op **AzureSqlDW**.             | Ja                                                          |
| connectionString    | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Synapse Analytics-exemplaar voor de **Connections Tring** -eigenschap. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook het wacht woord/de sleutel van de Service-Principal in Azure Key Vault plaatsen, en als de SQL-verificatie de `password` configuratie uit de connection string haalt. Zie het JSON-voor beeld onder de tabel en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja                                                          |
| servicePrincipalId  | Opgeven van de toepassing client-ID.                         | Ja, als u Azure AD-verificatie met een service-principal. |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja, als u Azure AD-verificatie met een service-principal. |
| tenant              | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. U kunt het ophalen van de muis in de rechterbovenhoek van de Azure-portal. | Ja, als u Azure AD-verificatie met een service-principal. |
| connectVia          | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt Azure Integration Runtime of een zelf-hostende integratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. | Nee                                                           |

Verwijzen respectievelijk naar de volgende secties over de vereisten en JSON-voorbeelden, voor andere verificatietypen:

- [SQL-verificatie](#sql-authentication)
- Azure AD-toepassing-tokenverificatie: [Service-principal](#service-principal-authentication)
- Azure AD-toepassing-tokenverificatie: [beheerde identiteiten voor een Azure-resources](#managed-identity)

>[!TIP]
>Als u fout met foutcode als "UserErrorFailedToConnectToSqlServer" bereikt en wordt weergegeven, zoals 'de sessielimiet voor de database is XXX en is bereikt.', toe te voegen `Pooling=false` met de verbindingstekenreeks en probeer het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voorbeeld van de gekoppelde service die gebruikmaakt van SQL-verificatie

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Wacht woord in Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Volg deze stappen voor het gebruik van service-principal op basis van Azure AD-toepassing-tokenverificatie:

1. **[Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  vanuit Azure portal. Noteer de naam van de toepassing en de volgende waarden voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. **[Een Azure Active Directory-beheerder inrichten](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  voor uw Azure SQL-server in Azure portal als u dat nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de database.

3. **[Maak ingesloten databasegebruikers](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  voor de service-principal. Verbinding maken met het datawarehouse uit of die u kopiëren van gegevens wilt met behulp van hulpprogramma's zoals SSMS, met een Azure AD-identiteit ten minste heeft de machtiging ALTER elke gebruiker. Voer de volgende T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **De service-principal die nodig is machtigingen verlenen** zoals u gewend voor SQL-gebruikers of voor anderen bent. Voer de volgende code uit of Raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van de gekoppelde service die gebruikmaakt van verificatie van service-principal

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Beheerde identiteiten voor verificatie van de Azure-resources

Een data factory, kan worden gekoppeld aan een [beheerde identiteit voor de Azure-resources](data-factory-service-identity.md) die staat voor de specifieke factory. U kunt deze beheerde identiteit voor Azure Synapse Analytics-verificatie gebruiken. De aangewezen factory kunt openen en gegevens kopiëren van of naar uw data warehouse met behulp van deze identiteit.

Als u beheerde identiteits verificatie wilt gebruiken, voert u de volgende stappen uit:

1. **[Een Azure Active Directory-beheerder inrichten](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  voor uw Azure SQL-server in Azure portal als u dat nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de database.

2. **[Inge sloten database gebruikers maken](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** voor de Data Factory beheerde identiteit. Verbinding maken met het datawarehouse uit of die u kopiëren van gegevens wilt met behulp van hulpprogramma's zoals SSMS, met een Azure AD-identiteit ten minste heeft de machtiging ALTER elke gebruiker. Voer de volgende T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Verleen de Data Factory beheerde identiteit de benodigde machtigingen** zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit of Raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.

**Voorbeeld:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. 

De volgende eigenschappen worden ondersteund voor Azure Synapse Analytics-gegevensset:

| Eigenschap  | Beschrijving                                                  | Verplicht                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De **type** eigenschap van de gegevensset moet worden ingesteld op **AzureSqlDWTable**. | Ja                         |
| schema | De naam van het schema. |Nee voor bron, Ja voor sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, Ja voor sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table`voor nieuwe werk belasting. | Nee voor bron, Ja voor sink |

#### <a name="dataset-properties-example"></a>Voorbeeld van de gegevensset-eigenschappen

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Synapse Analytics-bron en Sink.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics als de bron

Als u gegevens wilt kopiëren uit Azure Synapse Analytics, stelt u de eigenschap **type** in de bron voor het kopiëren van de activiteit in op **SqlDWSource**. De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap                     | Beschrijving                                                  | Verplicht |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De **type** eigenschap van de Kopieeractiviteit-bron moet worden ingesteld op **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Gebruik de aangepaste SQL-query om gegevens te lezen. Voorbeeld: `select * from MyTable`. | Nee       |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure. | Nee       |
| storedProcedureParameters    | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn de naam of waarde-paren. Namen en hoofdlettergebruik van parameters moeten overeenkomen met de naam en het hoofdlettergebruik van de opgeslagen-procedureparameters. | Nee       |

**Voor beeld: SQL-query gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voor beeld: opgeslagen procedure gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Voor beeld van opgeslagen procedure:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics als Sink

Azure Data Factory ondersteunt drie manieren om gegevens te laden in SQL Data Warehouse.

![Kopieer opties voor SQL DW-sinks](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Poly Base gebruiken](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [De instructie COPY gebruiken (preview)](#use-copy-statement)
- Bulksgewijs invoegen gebruiken

De snelste en meest schaal bare manier om gegevens te laden, is via [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) of de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview).

Om gegevens te kopiëren naar Azure SQL Data Warehouse, stelt u het sink-type in de Kopieeractiviteit naar **SqlDWSink**. De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **sink** sectie:

| Eigenschap          | Beschrijving                                                  | Verplicht                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De **type** eigenschap van de Copy-activiteit-sink moet zijn ingesteld op **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Hiermee wordt aangegeven of poly Base moet worden gebruikt voor het laden van gegevens in SQL Data Warehouse. `allowCopyCommand` en `allowPolyBase` kunnen niet beide true zijn. <br/><br/>Zie [poly Base gebruiken om gegevens te laden in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **waar** en **False** (standaard). | Nee.<br/>Toep assen bij het gebruik van poly base.     |
| polyBaseSettings  | Een groep eigenschappen die kan worden opgegeven wanneer de eigenschap `allowPolybase` is ingesteld op **True**. | Nee.<br/>Toep assen bij het gebruik van poly base. |
| allowCopyCommand | Hiermee wordt aangegeven of een [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) moet worden gebruikt om gegevens te laden in SQL Data Warehouse. `allowCopyCommand` en `allowPolyBase` kunnen niet beide true zijn. <br/><br/>Zie de [instructie Copy gebruiken om gegevens te laden in Azure SQL Data Warehouse](#use-copy-statement) sectie voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **waar** en **False** (standaard). | Nee.<br>Toep assen bij het gebruik van COPY. |
| copyCommandSettings | Een groep eigenschappen die kan worden opgegeven wanneer `allowCopyCommand` eigenschap is ingesteld op TRUE. | Nee.<br/>Toep assen bij het gebruik van COPY. |
| writeBatchSize    | Het aantal rijen dat in de SQL-tabel **per batch**moet worden ingevoegd.<br/><br/>De toegestane waarde is **geheel getal** (aantal rijen). Standaard bepaalt Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.     |
| writeBatchTimeout | Wachttijd voor de bewerking voor het invoegen van batch worden voltooid voordat er een optreedt time-out.<br/><br/>De toegestane waarde is **timespan**. Voorbeeld: "00: 30:00 ' (30 minuten). | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.        |
| preCopyScript     | Geef een SQL-query voor de Kopieeractiviteit om uit te voeren voordat het schrijven van gegevens in Azure SQL Data Warehouse in elke uitvoering. Gebruik deze eigenschap voor het opschonen van de vooraf geladen gegevens. | Nee                                            |
| tableOption | Hiermee wordt aangegeven of de Sink-tabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer de gefaseerde kopie is geconfigureerd in de Kopieer activiteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens, zoals SQL Data Warehouse Dwu's voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op om het uit te scha kelen. | Nee (standaard is `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse sink-voorbeeld

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Gebruik van PolyBase om gegevens te laden in Azure SQL Data Warehouse

Het gebruik van [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is een efficiënte manier om een grote hoeveelheid gegevens in azure Synapse Analytics te laden met een hoge door voer. Met behulp van PolyBase in plaats van het standaardmechanisme voor BULKINSERT ziet u een grote toename in de doorvoer. Zie voor een overzicht met een use-case [1 TB laden in azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Als uw bron gegevens zich in **Azure Blob, Azure data Lake Storage gen1 of Azure data Lake Storage Gen2**bevindt en de **indeling poly base-compatibel is**, kunt u de Kopieer activiteit gebruiken om direct poly Base te activeren, zodat Azure SQL Data Warehouse de gegevens van de bron kan ophalen. Zie voor meer informatie,  **[directe kopiëren met behulp van PolyBase](#direct-copy-by-using-polybase)** .
* Als de bron-gegevensopslag en -indeling wordt niet oorspronkelijk ondersteund door PolyBase, gebruikt u de **[gefaseerd kopiëren met behulp van PolyBase](#staged-copy-by-using-polybase)** functie in plaats daarvan. De functie gefaseerd kopiëren biedt u ook betere doorvoer. De gegevens worden automatisch geconverteerd naar een indeling die compatibel is met poly Base, de gegevens worden opgeslagen in Azure Blob Storage. vervolgens wordt poly base aangeroepen om gegevens te laden in SQL Data Warehouse.

>[!TIP]
>Meer informatie over [Best practices voor het gebruik van poly base](#best-practices-for-using-polybase).

De volgende poly base-instellingen worden ondersteund onder `polyBaseSettings` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Verplicht                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Hiermee geeft u het getal of het percentage van de rijen die kunnen worden afgewezen voordat de query is mislukt.<br/><br/>Meer informatie over de PolyBase-weigeringsopties in de sectie argumenten van [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Toegestane waarden zijn 0 (standaardinstelling), 1, 2, enzovoort. | Nee                                            |
| rejectType        | Hiermee geeft u op of de **rejectValue** optie is een letterlijke waarde of een percentage.<br/><br/>Toegestane waarden zijn **waarde** (standaard) en **Percentage**. | Nee                                            |
| rejectSampleValue | Bepaalt het aantal rijen om op te halen voordat PolyBase berekent het percentage van geweigerde rijen opnieuw.<br/><br/>Toegestane waarden zijn 1, 2, enzovoort. | Ja, als de **rejectType** is **percentage**. |
| useTypeDefault    | Hiermee geeft u ontbrekende waarden in de tekstbestanden verwerken als PolyBase worden gegevens opgehaald uit het tekstbestand.<br/><br/>Meer informatie over deze eigenschap in de sectie argumenten [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Toegestane waarden zijn **waar** en **False** (standaard).<br><br> | Nee                                            |

### <a name="direct-copy-by-using-polybase"></a>Directe kopiëren met behulp van PolyBase

SQL Data Warehouse poly Base ondersteunt direct Azure Blob, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, gebruikt u poly Base om rechtstreeks vanuit de brongegevens opslag naar Azure Synapse Analytics te kopiëren. Gebruik anders [gefaseerd kopiëren met behulp van PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Als u gegevens efficiënt wilt kopiëren naar SQL Data Warehouse, kunt u meer te weten komen uit [Azure Data Factory het nog eenvoudiger en handig maken om inzichten van gegevens te ontdekken wanneer u data Lake Store met SQL Data Warehouse gebruikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Als aan de vereisten zijn niet voldaan, wordt Azure Data Factory controleert of de instellingen en automatisch terugvalt op het mechanisme BULKINSERT voor de verplaatsing van gegevens.

1. De **gekoppelde bron service** heeft de volgende typen en verificatie methoden:

    | Ondersteund type brongegevens archief                             | Ondersteund type bron verificatie                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Verificatie van account sleutels, beheerde identiteits verificatie |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Verificatie van service-principal                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Verificatie van account sleutels, beheerde identiteits verificatie |

    >[!IMPORTANT]
    >Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken: Raadpleeg de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Meer informatie over de vereiste configuraties in Data Factory van een [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2 beheerde identiteits verificatie](connector-azure-data-lake-storage.md#managed-identity) sectie.

2. De **indeling van de bron gegevens** is van **Parquet**, **Orc**of **tekst met scheidings tekens**, met de volgende configuraties:

   1. Mappad bevat geen filter voor joker tekens.
   2. De bestands naam is leeg of verwijst naar één bestand. Als u de naam van het Joker teken opgeeft in de Kopieer activiteit, kan deze alleen `*` of `*.*`zijn.
   3. `rowDelimiter` is **standaard**, **\n**, **\r\n**of **\r**.
   4. `nullValue` is standaard ingeschakeld of is ingesteld op een **lege teken reeks** (""), en `treatEmptyAsNull` is standaard ingesteld op True.
   5. `encodingName` is standaard ingesteld op of **UTF-8**.
   6. `quoteChar`, `escapeChar`en `skipLineCount` zijn niet opgegeven. Poly base-ondersteuning koptekst rij overs Laan, die als `firstRowAsHeader` in ADF kan worden geconfigureerd.
   7. `compression` kan **geen compressie**, **GZip**, of **Deflate**.

3. Als uw bron een map is, moet `recursive` in de Kopieer activiteit worden ingesteld op True.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`en `modifiedDateTimeEnd` zijn niet opgegeven.

>[!NOTE]
>Als uw bron een map is, noteert u met poly base bestanden ophalen uit de map en alle bijbehorende submappen en worden er geen gegevens opgehaald uit bestanden waarvoor de bestands naam begint met een onderstreping (_) of een punt (.), zoals [hier wordt beschreven: locatie argument](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Gefaseerd kopiëren met behulp van PolyBase

Als uw bron gegevens niet systeem eigen compatibel zijn met poly Base, kunt u gegevens kopiëren met behulp van een tussentijds Azure Blob Storage-exemplaar (het kan geen Azure-Premium Storage zijn). In dit geval worden de gegevens in Azure Data Factory automatisch geconverteerd om te voldoen aan de vereisten voor gegevens formaat van poly base. Vervolgens wordt poly base aangeroepen om gegevens te laden in SQL Data Warehouse. Ten slotte opschonen het van uw tijdelijke gegevens uit de blob-opslag. Zie [gefaseerd kopiëren](copy-activity-performance.md#staged-copy) voor meer informatie over het kopiëren van gegevens via een gefaseerde installatie exemplaar van de Azure Blob-opslag.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account met de tussenliggende Blob-opslag. Geef vervolgens de `enableStaging` en `stagingSettings` eigenschappen voor de Kopieer activiteit op, zoals wordt weer gegeven in de volgende code.

>[!IMPORTANT]
>Als uw staging-Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken. Zie de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)voor meerwaarde ring. Meer informatie over de vereiste configuraties in Data Factory van [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Aanbevolen procedures voor het gebruik van PolyBase

De volgende secties bevatten aanbevolen procedures, naast de methoden die worden genoemd in [Best Practices for Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Machtiging vereist database

Voor het gebruik van PolyBase, moet de gebruiker die gegevens in SQL Data Warehouse laadt hebben ["bevoegdheid"](https://msdn.microsoft.com/library/ms191291.aspx) voor de doeldatabase. Één manier om te realiseren dat is het toevoegen van de gebruiker als lid van de **db_owner** rol. Leer hoe u dat doen in de [overzicht van SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Typ limieten rijgrootte en gegevens

PolyBase-loads zijn beperkt tot de rijen die kleiner is dan 1 MB. Het kan niet worden gebruikt om te laden naar VARCHR (MAX), NVARCHAR (MAX) of VARBINARY (MAX). Zie voor meer informatie, [capaciteitslimieten voor SQL Data Warehouse-service](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Wanneer de brongegevens rijen die groter is dan 1 MB bevat, is het raadzaam om te splitsen verticaal de brontabellen in verschillende kleine netwerken. Zorg ervoor dat de maximale grootte van elke rij de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen door poly Base te gebruiken en samen te voegen in azure Synapse Analytics.

Voor gegevens met een brede kolom kunt u ook gebruikmaken van niet-poly Base om de gegevens te laden met behulp van ADF, door de instelling ' poly base toestaan ' uit te scha kelen.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resourceklasse

Voor het bereiken van de best mogelijke doorvoer, een grotere resourceklasse aan de gebruiker die gegevens in SQL Data Warehouse met PolyBase laadt te toewijzen.

#### <a name="polybase-troubleshooting"></a>Poly base-probleem oplossing

**Laden naar decimale kolom**

Als de bron gegevens in tekst indeling of andere niet-poly base-compatibele archieven (met gefaseerde kopie en poly base) staan en een lege waarde bevat die in SQL Data Warehouse decimale kolom moet worden geladen, kunt u de volgende fout raken:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

De oplossing bestaat uit het opheffen van de selectie van de optie**type standaard gebruiken**(als onwaar) in Sink voor kopieer activiteit-> poly base-instellingen. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" is een poly base systeem eigen configuratie, waarmee wordt aangegeven hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand. 

**`tableName` in azure Synapse Analytics**

De volgende tabel bevat voorbeelden van hoe u de **tableName** eigenschap in de JSON-gegevensset. Hier ziet u verschillende combinaties van schema- en tabelnamen.

| DB-Schema | Tabelnaam | **tableName** JSON-eigenschap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable of dbo.MyTable of [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable of [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] of [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Als u de volgende fout ziet, is het probleem mogelijk de waarde die u hebt opgegeven voor de **tableName** eigenschap. Zie de voorgaande tabel voor de juiste manier om op te geven van de waarden voor de **tableName** JSON-eigenschap.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolommen met standaard waarden**

Op dit moment accepteert de PolyBase-functie in Data Factory alleen een hetzelfde aantal kolommen in de doeltabel. Een voorbeeld is een tabel met vier kolommen waarin een van deze met de standaardwaarde is gedefinieerd. De ingevoerde gegevens moet nog steeds vier kolommen hebben. Een invoergegevensset drie kolommen levert een fout die vergelijkbaar is met het volgende bericht:

```
All columns of the table must be specified in the INSERT BULK statement.
```

De waarde NULL is een speciale vorm van de standaardwaarde. Als de kolom toegestaan is, is de ingevoerde gegevens in de blob voor die kolom kan niet leeg zijn. Maar deze niet meer worden ontbreekt in de invoergegevensset. Poly base voegt NULL toe voor ontbrekende waarden in azure Synapse Analytics.

## <a name="use-copy-statement"></a>De instructie COPY gebruiken om gegevens in Azure SQL Data Warehouse te laden (preview)

SQL Data Warehouse [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) biedt ondersteuning voor het laden van gegevens uit **Azure Blob en Azure data Lake Storage Gen2**. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, kunt u de instructie COPY gebruiken in ADF om gegevens te laden in Azure SQL Data Warehouse. Azure Data Factory controleert de instellingen en mislukt de uitvoering van de Kopieer activiteit als niet aan de criteria wordt voldaan.

>[!NOTE]
>Er wordt momenteel alleen Data Factory ondersteund voor het kopiëren van een COPY-instructie met compatibele bronnen die hieronder worden beschreven.

De instructie COPY gebruiken ondersteunt de volgende configuratie:

1. De **gekoppelde bron-service en-indeling** zijn met de volgende typen en verificatie methoden:

    | Ondersteund type brongegevens archief                             | Ondersteunde indeling           | Ondersteund type bron verificatie                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Tekst met scheidings tekens](format-delimited-text.md)             | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening, Service-Principal-verificatie, beheerde identiteits verificatie |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Verificatie van account sleutels, verificatie van de Shared Access-hand tekening |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tekst met scheidings tekens](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Account sleutel verificatie, Service-Principal-verificatie, beheerde identiteits verificatie |

    >[!IMPORTANT]
    >Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken: Raadpleeg de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Meer informatie over de vereiste configuraties in Data Factory van een [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2 beheerde identiteits verificatie](connector-azure-data-lake-storage.md#managed-identity) sectie.

2. Indelings instellingen zijn met het volgende:

   1. Voor **Parquet**: `compression` kan **geen compressie**, **Snappy**of **gzip**zijn.
   2. Voor **Orc**: `compression` kan **geen compressie**, **zlib**of **Snappy**zijn.
   3. Voor **tekst met scheidings tekens**:
      1. `rowDelimiter` expliciet is ingesteld als **één teken** of als ' **\r\n**', wordt de standaard waarde niet ondersteund.
      2. `nullValue` is standaard ingesteld op een **lege teken reeks** ("").
      3. `encodingName` is standaard ingeschakeld of ingesteld op **UTF-8 of UTF-16**.
      4. `escapeChar` moet hetzelfde `quoteChar`zijn en niet leeg zijn.
      5. `skipLineCount` is standaard ingesteld op 0.
      6. `compression` kan **geen compressie** of **gzip**zijn.

3. Als uw bron een map is, moet `recursive` in de Kopieer activiteit worden ingesteld op True.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`en `modifiedDateTimeEnd` zijn niet opgegeven.

De volgende instellingen voor de Kopieer instructie worden ondersteund onder `allowCopyCommand` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Verplicht                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| Standaard waarde | Hiermee geeft u de standaard waarden voor elke doel kolom in SQL DW op.  De standaard waarden in de eigenschap overschrijven de standaard beperking die is ingesteld in het Data Warehouse en de identiteits kolom kan geen standaard waarde hebben. | Nee |
| additionalOptions | Aanvullende opties die worden door gegeven aan de SQL DW COPY-instructie, worden rechtstreeks in de with-component in een [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)opgenomen. Quote de waarde waar nodig om uit te lijnen met de vereisten voor het kopiëren van de instructie. | Nee |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>De gegevenstypetoewijzing voor Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit Azure Synapse Analytics. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen.

### <a name="source-transformation"></a>Bron transformatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **bron opties** van de bron transformatie. 

**Invoer:** Selecteer of u uw bron op een tabel (equivalent van ```Select * from <table-name>```) wilt aanwijzen of voer een aangepaste SQL-query in.

**Query**: als u in het invoer veld query selecteert, voert u een SQL-query in voor uw bron. Deze instelling overschrijft elke tabel die u in de gegevensset hebt gekozen. **Order by** -componenten worden hier niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL die een tabel retourneert. Met deze query wordt een bron tabel geproduceerd die u in uw gegevens stroom kunt gebruiken. Het gebruik van query's is ook een uitstekende manier om rijen te verminderen voor het testen of voor Zoek opdrachten. 

* SQL-voor beeld: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batch grootte**: Voer een batch grootte in om grote hoeveel heden gegevens in Lees bewerkingen te segmenteren.

**Isolatie niveau**: de standaard waarde voor SQL-bronnen in de toewijzings gegevens stroom is niet-vastgelegd. U kunt het isolatie niveau hier wijzigen in een van deze waarden:
* Doorgevoerde lezen
* Lezen niet-doorgevoerd
* Herhaal bare Lees bewerking
* Serializable
* Geen (isolatie niveau negeren)

![Isolatie niveau](media/data-flow/isolationlevel.png "Isolatie niveau")

### <a name="sink-transformation"></a>Sink-trans formatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **instellingen** van de Sink-trans formatie.

**Update methode:** Hiermee wordt bepaald welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan. Als u rijen wilt bijwerken, upsert of verwijderen, moet u een alter-Row trans formatie voor deze acties labelen. Voor updates, upsert en verwijderen moet een sleutel kolom of-kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Tabel actie:** Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.
* Geen: er wordt geen actie uitgevoerd voor de tabel.
* Opnieuw maken: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.
* Afkappen: alle rijen uit de doel tabel worden verwijderd.

**Fase ring inschakelen:** Hiermee wordt bepaald of [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) moet worden gebruikt bij het schrijven naar Azure Synapse Analytics

**Batch grootte**: bepaalt hoeveel rijen er worden geschreven in elke Bucket. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens.

**SQL-scripts vooraf en post**: Voer SQL-scripts met meerdere regels in die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base worden geschreven

![scripts voor SQL-verwerking vooraf en na](media/data-flow/prepost1.png "SQL-verwerkings scripts")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Toewijzing van gegevens type voor Azure Synapse Analytics

Wanneer u gegevens kopieert vanuit of naar Azure Synapse Analytics, worden de volgende toewijzingen gebruikt vanuit gegevens typen van Azure Synapse Analytics om tussenliggende gegevens typen te Azure Data Factory. Zie [schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe Copy Activity in het schema en de gegevens van een brontype toegewezen aan de sink.

>[!TIP]
>Raadpleeg de [tabel gegevens typen in het Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) -artikel over ondersteunde gegevens typen van SQL DW en de tijdelijke oplossingen voor niet-ondersteunde.

| Azure Synapse Analytics-gegevens type    | Data Factory tussentijdse gegevenstype |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Booleaans                        |
| char                                  | String, Char[]                 |
| date                                  | Datum/tijd                       |
| Datetime                              | Datum/tijd                       |
| datetime2                             | Datum/tijd                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| installatiekopie                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Enkelvoudig                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datum/tijd                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| tijd                                  | TimeSpan                       |
| tinyint                               | byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de Kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md##supported-data-stores-and-formats).
