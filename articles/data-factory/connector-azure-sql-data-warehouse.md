---
title: Gegevens kopiëren naar en van Azure SQL Data Warehouse met behulp van Azure Data Factory | Microsoft Docs
description: Leer hoe u gegevens uit ondersteunde bron winkels naar Azure SQL Data Warehouse of SQL Data Warehouse voor ondersteunde sink stores kopiëren met behulp van Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 29f5b9b704bcf4648e9c24516d8eff5429a0ce1d
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009960"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure SQL Data Warehouse met behulp van Azure Data Factory 
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Huidige versie](connector-azure-sql-data-warehouse.md)

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar Azure SQL Data Warehouse. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure SQL Data Warehouse-connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Specifiek, ondersteunt deze Azure SQL Data Warehouse-connector deze functies:

- Gegevens kopiëren met behulp van SQL-verificatie en tokenverificatie van Azure Active Directory (Azure AD)-toepassing met een service-principal of beheerde identiteiten voor Azure-resources.
- Als een bron ophalen van gegevens met behulp van een SQL-query of een opgeslagen procedure.
- Als een sink gegevens laden met behulp van PolyBase of een bulksgewijs invoegen. We raden PolyBase voor betere kopieerprestaties.

> [!IMPORTANT]
> Als u gegevens kopiëren met behulp van Azure Data Factory Integration Runtime, configureert u een [Azure SQL-serverfirewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) zodat Azure-services toegang hebben tot de server.
> Als u gegevens kopiëren met behulp van de zelf-hostende integratieruntime, configureert u de firewall van de Azure SQL-server zodat het juiste IP-adresbereik. Dit bereik omvat IP van de machine die wordt gebruikt om te verbinden met Azure SQL Database.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Voor het bereiken van optimale prestaties, gebruikt u PolyBase om gegevens te laden in Azure SQL Data Warehouse. De [gebruik PolyBase om gegevens te laden in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie vindt u informatie. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse minder dan 15 minuten met Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die specifieke Data Factory-entiteiten met een Azure SQL Data Warehouse-connector definiëren.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor een Azure SQL Data Warehouse gekoppelde service:

| Eigenschap            | Description                                                  | Vereist                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | De eigenschap type moet worden ingesteld op **AzureSqlDW**.             | Ja                                                          |
| connectionString    | Geef de informatie die nodig zijn voor het verbinding maken met de Azure SQL Data Warehouse-exemplaar voor de **connectionString** eigenschap. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook een wacht woord/Service-Principal-sleutel in azure Key Vault plaatsen en als de SQL- `password` verificatie de configuratie uit de Connection String halen. Zie het JSON-voor beeld onder de tabel en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja                                                          |
| servicePrincipalId  | Opgeven van de toepassing client-ID.                         | Ja, als u Azure AD-verificatie met een service-principal. |
| servicePrincipalKey | Geef de sleutel van de toepassing. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja, als u Azure AD-verificatie met een service-principal. |
| tenant              | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. U kunt het ophalen van de muis in de rechterbovenhoek van de Azure-portal. | Ja, als u Azure AD-verificatie met een service-principal. |
| connectVia          | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt Azure Integration Runtime of een zelf-hostende integratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als niet is opgegeven, wordt de standaard Azure Integration Runtime. | Nee                                                           |

Verwijzen respectievelijk naar de volgende secties over de vereisten en JSON-voorbeelden, voor andere verificatietypen:

- [SQL-verificatie](#sql-authentication)
- Verificatie van Azure AD-toepassings token: [Service/principal](#service-principal-authentication)
- Verificatie van Azure AD-toepassings token: [Beheerde identiteiten voor Azure-resources](#managed-identity)

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
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

5. **Configureren van een Azure SQL Data Warehouse gekoppelde service** in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van de gekoppelde service die gebruikmaakt van verificatie van service-principal

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

Een data factory, kan worden gekoppeld aan een [beheerde identiteit voor de Azure-resources](data-factory-service-identity.md) die staat voor de specifieke factory. U kunt deze beheerde identiteit voor Azure SQL Data Warehouse authenticatie gebruiken. De aangewezen factory kunt openen en gegevens kopiëren van of naar uw data warehouse met behulp van deze identiteit.

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

5. **Configureren van een Azure SQL Data Warehouse gekoppelde service** in Azure Data Factory.

**Voorbeeld:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Azure SQL Data Warehouse-gegevensset.

De volgende eigenschappen worden ondersteund om gegevens te kopiëren van of naar Azure SQL Data Warehouse:

| Eigenschap  | Description                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De **type** eigenschap van de gegevensset moet worden ingesteld op **AzureSqlDWTable**. | Ja                         |
| Schema | De naam van het schema. |Nee voor bron, Ja voor sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, Ja voor sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en`table`voor nieuwe werk belasting. | Nee voor bron, Ja voor sink |

#### <a name="dataset-properties-example"></a>Voorbeeld van de gegevensset-eigenschappen

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Azure SQL Data Warehouse-bron en sink.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse als de bron

Om gegevens te kopiëren van Azure SQL Data Warehouse, stel de **type** eigenschap in de bron van Kopieeractiviteit naar **SqlDWSource**. De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap                     | Description                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De **type** eigenschap van de Kopieeractiviteit-bron moet worden ingesteld op **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Gebruik de aangepaste SQL-query om gegevens te lezen. Voorbeeld: `select * from MyTable`. | Nee       |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure. | Nee       |
| storedProcedureParameters    | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn de naam of waarde-paren. Namen en hoofdlettergebruik van parameters moeten overeenkomen met de naam en het hoofdlettergebruik van de opgeslagen-procedureparameters. | Nee       |

### <a name="points-to-note"></a>Die u moet weten

- Als de **sqlReaderQuery** is opgegeven voor de **SqlSource**, de Kopieeractiviteit deze query wordt uitgevoerd op basis van de bron van de Azure SQL Data Warehouse de gegevens op te halen. Of u kunt een opgeslagen procedure opgeven. Geef de **sqlReaderStoredProcedureName** en **storedProcedureParameters** als de opgeslagen procedure parameters nodig heeft.
- Als u niet of opgeven **sqlReaderQuery** of **sqlReaderStoredProcedureName**, de kolommen die zijn gedefinieerd in de **structuur** sectie van de gegevensset JSON worden gebruikt om een query maken. `select column1, column2 from mytable` wordt uitgevoerd op Azure SQL Data Warehouse. Als de definitie van de gegevensset niet over de **structuur**, alle kolommen zijn geselecteerd uit de tabel.

#### <a name="sql-query-example"></a>Voorbeeld van de SQL-query

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

#### <a name="stored-procedure-example"></a>Voorbeeld van de opgeslagen procedure

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

### <a name="stored-procedure-definition"></a>Definitie van de opgeslagen procedure

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse als sink

Om gegevens te kopiëren naar Azure SQL Data Warehouse, stelt u het sink-type in de Kopieeractiviteit naar **SqlDWSink**. De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **sink** sectie:

| Eigenschap          | Description                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De **type** eigenschap van de Copy-activiteit-sink moet zijn ingesteld op **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Hiermee wordt aangegeven of het gebruik van PolyBase, indien van toepassing, in plaats van het mechanisme voor BULKINSERT. <br/><br/> Het is raadzaam om gegevens te laden in SQL Data Warehouse met behulp van PolyBase. Zie de [gebruik PolyBase om gegevens te laden in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie voor beperkingen en meer informatie.<br/><br/>Toegestane waarden zijn **waar** en **False** (standaard). | Nee                                            |
| polyBaseSettings  | Een groep met eigenschappen die kunnen worden opgegeven wanneer de **allowPolybase** eigenschap is ingesteld op **waar**. | Nee                                            |
| rejectValue       | Hiermee geeft u het getal of het percentage van de rijen die kunnen worden afgewezen voordat de query is mislukt.<br/><br/>Meer informatie over de PolyBase-weigeringsopties in de sectie argumenten van [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Toegestane waarden zijn 0 (standaardinstelling), 1, 2, enzovoort. | Nee                                            |
| rejectType        | Hiermee geeft u op of de **rejectValue** optie is een letterlijke waarde of een percentage.<br/><br/>Toegestane waarden zijn **waarde** (standaard) en **Percentage**. | Nee                                            |
| rejectSampleValue | Bepaalt het aantal rijen om op te halen voordat PolyBase berekent het percentage van geweigerde rijen opnieuw.<br/><br/>Toegestane waarden zijn 1, 2, enzovoort. | Ja, als de **rejectType** is **percentage**. |
| useTypeDefault    | Hiermee geeft u ontbrekende waarden in de tekstbestanden verwerken als PolyBase worden gegevens opgehaald uit het tekstbestand.<br/><br/>Meer informatie over deze eigenschap in de sectie argumenten [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Toegestane waarden zijn **waar** en **False** (standaard).<br><br> | Nee                                            |
| writeBatchSize    | Het aantal rijen dat in de SQL-tabel **per batch**moet worden ingevoegd. Geldt alleen wanneer PolyBase wordt niet gebruikt.<br/><br/>De toegestane waarde is **geheel getal** (aantal rijen). Standaard bepaalt Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. | Nee                                            |
| writeBatchTimeout | Wachttijd voor de bewerking voor het invoegen van batch worden voltooid voordat er een optreedt time-out. Geldt alleen wanneer PolyBase wordt niet gebruikt.<br/><br/>De toegestane waarde is **timespan**. Voorbeeld: "00:30:00" (30 minuten). | Nee                                            |
| preCopyScript     | Geef een SQL-query voor de Kopieeractiviteit om uit te voeren voordat het schrijven van gegevens in Azure SQL Data Warehouse in elke uitvoering. Gebruik deze eigenschap voor het opschonen van de vooraf geladen gegevens. | Nee                                            |
| tableOption | Hiermee wordt aangegeven of de Sink-tabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer de gefaseerde kopie is geconfigureerd in de Kopieer activiteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens, zoals SQL Data Warehouse Dwu's voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, `true` geeft u op dat u deze functie wilt uitschakelen. | Nee (standaard instelling `false`) |

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

Meer informatie over het gebruik van PolyBase te laden efficiënt SQL Data Warehouse in de volgende sectie.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Gebruik van PolyBase om gegevens te laden in Azure SQL Data Warehouse

Met behulp van [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is een efficiënte manier om een grote hoeveelheid gegevens in Azure SQL Data Warehouse laden met hoge doorvoer. Met behulp van PolyBase in plaats van het standaardmechanisme voor BULKINSERT ziet u een grote toename in de doorvoer. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Als uw bron gegevens zich in **Azure Blob, Azure data Lake Storage gen1 of Azure data Lake Storage Gen2**bevindt en de **indeling poly base-compatibel is**, kunt u de Kopieer activiteit gebruiken om direct poly Base te activeren, zodat Azure SQL Data Warehouse de gegevens van de bron kan ophalen. Zie voor meer informatie,  **[directe kopiëren met behulp van PolyBase](#direct-copy-by-using-polybase)** .
* Als de bron-gegevensopslag en -indeling wordt niet oorspronkelijk ondersteund door PolyBase, gebruikt u de **[gefaseerd kopiëren met behulp van PolyBase](#staged-copy-by-using-polybase)** functie in plaats daarvan. De functie gefaseerd kopiëren biedt u ook betere doorvoer. De gegevens wordt automatisch geconverteerd in PolyBase-compatibele indeling. En de gegevens worden opgeslagen in Azure Blob-opslag. Vervolgens worden de gegevens in SQL Data Warehouse geladen.

>[!TIP]
>Meer informatie over [Best practices voor het gebruik van poly base](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Directe kopiëren met behulp van PolyBase

SQL Data Warehouse poly Base ondersteunt direct Azure Blob, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, gebruikt u poly Base om rechtstreeks vanuit het brongegevens archief naar Azure SQL Data Warehouse te kopiëren. Gebruik anders [gefaseerd kopiëren met behulp van PolyBase](#staged-copy-by-using-polybase).

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
   2. De bestands naam is leeg of verwijst naar één bestand. Als u de naam van het Joker teken opgeeft in de Kopieer activiteit, `*` kan `*.*`deze alleen zijn of.
   3. `rowDelimiter`is **standaard**, **\n**, **\r\n**of **\r**.
   4. `nullValue`is standaard **ingesteld op '** "' ('"), en `treatEmptyAsNull` wordt als standaard waarde ingevuld of ingesteld op True.
   5. `encodingName`is standaard ingesteld op **UTF-8**.
   6. `quoteChar`, `escapeChar` en`skipLineCount` niet opgegeven. Poly base-ondersteuning koptekst rij overs Laan, die `firstRowAsHeader` in ADF kan worden geconfigureerd.
   7. `compression` kan **geen compressie**, **GZip**, of **Deflate**.

3. Als uw bron een map is, `recursive` moet u de Kopieer activiteit instellen op True.

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

Wanneer de brongegevens niet voldoet aan de criteria in de vorige sectie, gegevens kopiëren via een exemplaar tussentijdse staging Azure Blob-opslag inschakelen Azure Premium Storage kan niet. In dit geval Azure Data Factory wordt automatisch uitgevoerd transformaties op de gegevens om te voldoen aan de vereisten voor het opmaken van gegevens van PolyBase. Vervolgens deze gebruikmaakt van PolyBase om gegevens te laden in SQL Data Warehouse. Ten slotte opschonen het van uw tijdelijke gegevens uit de blob-opslag. Zie [gefaseerd kopiëren](copy-activity-performance.md#staged-copy) voor meer informatie over het kopiëren van gegevens via een gefaseerde installatie exemplaar van de Azure Blob-opslag.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account met de tussenliggende Blob-opslag. Geef vervolgens de `enableStaging` eigenschappen `stagingSettings` en op voor de Kopieer activiteit, zoals in de volgende code wordt weer gegeven.

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

## <a name="best-practices-for-using-polybase"></a>Aanbevolen procedures voor het gebruik van PolyBase

De volgende secties vindt u aanbevolen procedures naast die worden vermeld in [aanbevolen procedures voor Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Machtiging vereist database

Voor het gebruik van PolyBase, moet de gebruiker die gegevens in SQL Data Warehouse laadt hebben ["bevoegdheid"](https://msdn.microsoft.com/library/ms191291.aspx) voor de doeldatabase. Één manier om te realiseren dat is het toevoegen van de gebruiker als lid van de **db_owner** rol. Leer hoe u dat doen in de [overzicht van SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Typ limieten rijgrootte en gegevens

PolyBase-loads zijn beperkt tot de rijen die kleiner is dan 1 MB. Het kan niet worden gebruikt om te laden naar VARCHR (MAX), NVARCHAR (MAX) of VARBINARY (MAX). Zie voor meer informatie, [capaciteitslimieten voor SQL Data Warehouse-service](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Wanneer de brongegevens rijen die groter is dan 1 MB bevat, is het raadzaam om te splitsen verticaal de brontabellen in verschillende kleine netwerken. Zorg ervoor dat de maximale grootte van elke rij de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen met behulp van PolyBase en samengevoegd in Azure SQL Data Warehouse.

Voor gegevens met een brede kolom kunt u ook gebruikmaken van niet-poly Base om de gegevens te laden met behulp van ADF, door de instelling ' poly base toestaan ' uit te scha kelen.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resourceklasse

Voor het bereiken van de best mogelijke doorvoer, een grotere resourceklasse aan de gebruiker die gegevens in SQL Data Warehouse met PolyBase laadt te toewijzen.

### <a name="polybase-troubleshooting"></a>Poly base-probleem oplossing

**Laden naar decimale kolom**

Als de bron gegevens in tekst indeling of andere niet-poly base-compatibele archieven (met gefaseerde kopie en poly base) staan en een lege waarde bevat die in SQL Data Warehouse decimale kolom moet worden geladen, kunt u de volgende fout raken:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

De oplossing bestaat uit het opheffen van de selectie van de optie**type standaard gebruiken**(als onwaar) in Sink voor kopieer activiteit-> poly base-instellingen. '[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)' is een poly base-systeem eigen configuratie, waarmee wordt aangegeven hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand. 

**`tableName`in Azure SQL Data Warehouse**

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

De waarde NULL is een speciale vorm van de standaardwaarde. Als de kolom toegestaan is, is de ingevoerde gegevens in de blob voor die kolom kan niet leeg zijn. Maar deze niet meer worden ontbreekt in de invoergegevensset. PolyBase voegt NULL voor ontbrekende waarden in Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over de [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>De gegevenstypetoewijzing voor Azure SQL Data Warehouse

Wanneer u gegevens van of naar Azure SQL Data Warehouse kopieert, worden de volgende toewijzingen van Azure SQL Data Warehouse-gegevenstypen gebruikt om Azure Data Factory tussentijdse gegevenstypen. Zie [schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe Copy Activity in het schema en de gegevens van een brontype toegewezen aan de sink.

>[!TIP]
>Raadpleeg de [tabel gegevens typen in Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) artikel over ondersteunde gegevens typen van SQL DW en de tijdelijke oplossingen voor niet-ondersteunde.

| Azure SQL Data Warehouse-gegevenstype    | Data Factory tussentijdse gegevenstype |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de Kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md##supported-data-stores-and-formats).
