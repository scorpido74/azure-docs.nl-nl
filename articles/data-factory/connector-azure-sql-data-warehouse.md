---
title: Gegevens kopiëren en transformeren in Azure Synapse Analytics
description: Meer informatie over het kopiëren van gegevens van en naar Azure Synapse Analytics en het transformeren van gegevens in Azure Synapse Analytics met Behulp van Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417567"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) met Azure Data Factory 

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Huidige versie](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure Synapse Analytics te kopiëren en gegevensstroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure SQL Data Warehouse te kopiëren en gegevensstroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure Synapse Analytics te kopiëren en gegevensstroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Synapse Analytics-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrixtabel](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)

Voor kopieeractiviteit ondersteunt deze Azure Synapse Analytics-connector de volgende functies:

- Kopieer gegevens met SQL-verificatie en Azure AD-verificatie (Azure AD) Application token-verificatie met een serviceprincipal of beheerde identiteiten voor Azure-resources.
- Als bron u gegevens ophalen met behulp van een SQL-query of een opgeslagen procedure.
- Als gootsteen laadt u gegevens met [polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) of [copy-instructie](#use-copy-statement) (voorbeeld) of bulkinsert. We raden PolyBase of COPY-instructie (preview) aan voor betere kopieerprestaties.

> [!IMPORTANT]
> Als u gegevens kopieert met Azure Data Factory Integration Runtime, configureert u een [Azure SQL-serverfirewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) zodat Azure-services toegang hebben tot de server.
> Als u gegevens kopieert met behulp van een zelf gehoste runtime voor integratie, configureert u de Azure SQL-serverfirewall om het juiste IP-bereik toe te staan. Dit bereik omvat het IP-adres van de machine dat wordt gebruikt om verbinding te maken met Azure Synapse Analytics.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Gebruik PolyBase om gegevens in Azure Synapse Analytics te laden om de beste prestaties te bereiken. De [sectie PolyBase gebruiken om gegevens in Azure Synapse Analytics te laden,](#use-polybase-to-load-data-into-azure-sql-data-warehouse) bevat details. Zie 1 TB laden in [Azure Synapse Analytics in minder dan 15 minuten laden met Azure Data Factory](load-azure-sql-data-warehouse.md)voor een walkthrough met een use case.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die entiteiten in Gegevensfabriek definiëren die specifiek zijn voor een Azure Synapse Analytics-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor een gekoppelde Azure Synapse Analytics-service:

| Eigenschap            | Beschrijving                                                  | Vereist                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | De eigenschap type moet zijn ingesteld op **AzureSqlDW**.             | Ja                                                          |
| Connectionstring    | Geef de informatie op die nodig is om verbinding te maken met de instantie Azure Synapse Analytics voor de eigenschap **connectionString.** <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U ook de hoofdsleutel wachtwoord/service in Azure Key Vault `password` plaatsen en als sql-verificatie de configuratie uit de verbindingstekenreeks haalt. Zie het JSON-voorbeeld onder de tabel en [Store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. | Ja                                                          |
| servicePrincipalId  | Geef de client-id van de toepassing op.                         | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal. |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal. |
| tenant              | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal. |
| connectVia          | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of een self-hosted integration runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee                                                           |

Raadpleeg voor verschillende verificatietypen respectievelijk de volgende secties over vereisten en JSON-voorbeelden:

- [SQL-verificatie](#sql-authentication)
- Azure AD-toepassingstokenverificatie: [serviceprincipal](#service-principal-authentication)
- Azure AD-toepassingstokenverificatie: [beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Als u fout met foutcode als "UserErrorFailedToConnectToSqlServer" en bericht als "De sessielimiet voor `Pooling=false` de database is XXX en is bereikt" hit, voeg dan toe aan uw verbindingstekenreeks en probeer het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voorbeeld van gekoppelde service dat SQL-verificatie gebruikt

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

**Wachtwoord in Azure Key Vault:**

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

Voer de volgende stappen uit om azure AD-toepassingstokenverificatie op basis van serviceprincipal te gebruiken:

1. **[Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** vanuit de Azure-portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. **[Inrichten van een Azure Active Directory-beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** voor uw Azure SQL-server op de Azure-portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of Azure AD-groep zijn. Als u de groep met beheerde identiteit een beheerdersrol verleent, slaat u de stappen 3 en 4 over. De beheerder heeft volledige toegang tot de database.

3. Voor de **[serviceprincipal maken van opgenomen databasegebruikers.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** Maak verbinding met het gegevensmagazijn van of waarnaar u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SSMS, met een Azure AD-identiteit die ten minste ELKE GEBRUIKERSmachtiging WIJZIGt. Voer de volgende T-SQL uit:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Geef de serviceprincipal machtigingen toe** zoals u dat normaal doet voor SQL-gebruikers of anderen. Voer de volgende code uit of raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u PolyBase wilt gebruiken om de gegevens te laden, raadpleegt u de [vereiste databasetoestemming](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configureer een gekoppelde Azure Synapse Analytics-service** in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van gekoppelde service dat serviceprincipal-verificatie gebruikt

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten voor Azure-bronverificatie

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke fabriek vertegenwoordigt. U deze beheerde identiteit gebruiken voor Azure Synapse Analytics-verificatie. De aangewezen fabriek kan met deze identiteit toegang krijgen tot gegevens van of naar uw datawarehouse.

Voer de volgende stappen uit om beheerde identiteitsverificatie te gebruiken:

1. **[Inrichten van een Azure Active Directory-beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** voor uw Azure SQL-server op de Azure-portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of Azure AD-groep zijn. Als u de groep met beheerde identiteit een beheerdersrol verleent, slaat u de stappen 3 en 4 over. De beheerder heeft volledige toegang tot de database.

2. **[Contactpersonen voor databasegebruikers maken](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** voor de beheerde identiteit van de gegevensfabriek. Maak verbinding met het gegevensmagazijn van of waarnaar u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SSMS, met een Azure AD-identiteit die ten minste ELKE GEBRUIKERSmachtiging WIJZIGt. Voer de volgende T-SQL uit. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Geef de Data Factory Managed Identity machtigingen toe** zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit of raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u PolyBase wilt gebruiken om de gegevens te laden, raadpleegt u de [vereiste databasetoestemming](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configureer een gekoppelde Azure Synapse Analytics-service** in Azure Data Factory.

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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

De volgende eigenschappen worden ondersteund voor de Azure Synapse Analytics-gegevensset:

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De **eigenschap type** van de gegevensset moet worden ingesteld op **AzureSqlDWTable**. | Ja                         |
| schema | Naam van het schema. |Nee voor bron, Ja voor gootsteen  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor gootsteen  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee voor bron, Ja voor gootsteen |

#### <a name="dataset-properties-example"></a>Voorbeeld van gegevensseteigenschappen

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

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics als bron

Als u gegevens uit Azure Synapse Analytics wilt kopiëren, stelt u de **eigenschap type** in de bron Activiteit kopiëren in op **SqlDWSource.** De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De **eigenschap type** van de bron Kopieeractiviteit moet zijn ingesteld op **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `select * from MyTable`. | Nee       |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. | Nee       |
| storedProcedureParameters    | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- of waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. | Nee       |
| Isolationlevel | Hiermee geeft u het transactievergrendelingsgedrag voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |

**Voorbeeld: SQL-query gebruiken**

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

**Voorbeeld: met behulp van opgeslagen procedure**

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

**Opgeslagen monsterprocedure:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics als gootsteen

Azure Data Factory ondersteunt drie manieren om gegevens in SQL Data Warehouse te laden.

![SQL DW-sinkkopieeropties](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [PolyBase gebruiken](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Instructie KOPIËREN gebruiken (voorbeeld)](#use-copy-statement)
- Bulkinvoegsel gebruiken

De snelste en meest schaalbare manier om gegevens te laden is via [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) of de [COPY-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview).

Als u gegevens wilt kopiëren naar Azure SQL Data Warehouse, stelt u het sinktype in Activiteit kopiëren in **sqlDWsink**. De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De **eigenschap type** van de aanzet tot kopieeractiviteit moet worden ingesteld op **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Geeft aan of u PolyBase moet gebruiken om gegevens in SQL Data Warehouse te laden. `allowCopyCommand`en `allowPolyBase` kan niet zowel waar zijn. <br/><br/>Zie [PolyBase gebruiken om gegevens te laden in de](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie Azure SQL Data Warehouse voor beperkingen en details.<br/><br/>Toegestane waarden zijn **Waar** en **Onwaar** (standaard). | Nee.<br/>Toepassen bij het gebruik van PolyBase.     |
| polyBaseInstellingen  | Een groep eigenschappen die kan `allowPolybase` worden opgegeven wanneer de eigenschap is ingesteld op **true**. | Nee.<br/>Toepassen bij het gebruik van PolyBase. |
| allowCopyCommand | Geeft aan of [u copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (voorbeeld) moet gebruiken om gegevens in SQL Data Warehouse te laden. `allowCopyCommand`en `allowPolyBase` kan niet zowel waar zijn. <br/><br/>Zie [COPY-instructie gebruiken om gegevens te laden in](#use-copy-statement) de sectie Azure SQL Data Warehouse voor beperkingen en details.<br/><br/>Toegestane waarden zijn **Waar** en **Onwaar** (standaard). | Nee.<br>Toepassen bij gebruik van COPY. |
| copyCommandInstellingen | Een groep eigenschappen die kan `allowCopyCommand` worden opgegeven wanneer de eigenschap is ingesteld op TRUE. | Nee.<br/>Toepassen bij gebruik van COPY. |
| writeBatchSize    | Aantal rijen dat **per batch**moet worden ingevoegd in de SQL-tabel .<br/><br/>De toegestane waarde is **integer** (aantal rijen). Gegevensfabriek bepaalt standaard dynamisch de juiste batchgrootte op basis van de rijgrootte. | Nee.<br/>Toepassen bij het gebruik van bulk insert.     |
| writeBatchTimeout | Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br/><br/>De toegestane waarde is **tijdspanne.** Voorbeeld: "00:30:00" (30 minuten). | Nee.<br/>Toepassen bij het gebruik van bulk insert.        |
| preCopyScript     | Geef een SQL-query op voor Activiteit kopiëren voordat u in elke run gegevens in Azure SQL Data Warehouse schrijft. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | Nee                                            |
| tabelOptie | Hiermee geeft u op of de gootsteentabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bronschema. Het maken van automatische tabel wordt niet ondersteund wanneer gefaseerde kopie is geconfigureerd in kopieeractiviteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |
| disableMetricsCollection disableMetricsCollection disableMetricsCollection disableMetrics | Data Factory verzamelt metrics zoals SQL Data Warehouse DWUs voor optimalisatie van kopieerprestaties en aanbevelingen. Als u zich met dit `true` gedrag bezighoudt, geeft u op om het uit te schakelen. | Nee (standaard `false`is) |

#### <a name="sql-data-warehouse-sink-example"></a>Voorbeeld van SQL Data Warehouse-sink

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse

Het gebruik van [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is een efficiënte manier om een grote hoeveelheid gegevens te laden in Azure Synapse Analytics met een hoge doorvoer. U ziet een grote winst in de doorvoer door PolyBase te gebruiken in plaats van het standaard BULKINSERT-mechanisme. Zie 1 TB laden in [Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.

* Als uw brongegevens zich in **Azure Blob, Azure Data Lake Storage Gen1 of Azure Data Lake Storage Gen2**bevinden en de **indeling PolyBase-compatibel is,** u kopieeractiviteit gebruiken om PolyBase rechtstreeks aan te roepen om Azure SQL Data Warehouse de gegevens uit de bron te laten halen. Zie Direct **[kopiëren met PolyBase](#direct-copy-by-using-polybase)** voor meer informatie.
* Als uw brongegevensarchief en -indeling oorspronkelijk niet door PolyBase worden ondersteund, gebruikt u de gefaseerde kopie met de functie **[PolyBase.](#staged-copy-by-using-polybase)** De geënsceneerde kopie functie biedt u ook een betere doorvoer. Het converteert de gegevens automatisch in polybase-compatibele indeling, slaat de gegevens op in Azure Blob-opslag.en roept vervolgens PolyBase aan om gegevens te laden in SQL Data Warehouse.

>[!TIP]
>Meer informatie over [aanbevolen procedures voor het gebruik van PolyBase](#best-practices-for-using-polybase).

De volgende PolyBase-instellingen `polyBaseSettings` worden ondersteund onder in kopieeractiviteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Hiermee geeft u het aantal of het percentage rijen op dat kan worden geweigerd voordat de query mislukt.<br/><br/>Meer informatie over de afwijsopties van PolyBase vindt u in de sectie Argumenten van [EXTERNE TABEL MAKEN (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) <br/><br/>Toegestane waarden zijn 0 (standaard), 1, 2, enz. | Nee                                            |
| rejectType        | Hiermee geeft u op of de optie **afwijzenwaarde** een letterlijke waarde of een percentage is.<br/><br/>Toegestane waarden zijn **Waarde** (standaard) en **Percentage**. | Nee                                            |
| rejectSampleValue | Hiermee bepaalt u het aantal rijen dat moet worden opgehaald voordat PolyBase het percentage afgewezen rijen opnieuw berekent.<br/><br/>Toegestane waarden zijn 1, 2, enz. | Ja, als de **rejectType** **percentage**is . |
| useTypeDefault    | Hiermee geeft u op hoe ontbrekende waarden in afgebakende tekstbestanden moeten worden verwerkt wanneer PolyBase gegevens uit het tekstbestand ophaalt.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie Argumenten in [EXTERNE BESTANDSINDELING MAKEN (Transact-SQL).](https://msdn.microsoft.com/library/dn935026.aspx)<br/><br/>Toegestane waarden zijn **Waar** en **Onwaar** (standaard).<br><br> | Nee                                            |

### <a name="direct-copy-by-using-polybase"></a>Directe kopie met PolyBase

SQL Data Warehouse PolyBase ondersteunt rechtstreeks Azure Blob, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Als uw brongegevens voldoen aan de criteria die in deze sectie worden beschreven, gebruikt u PolyBase om rechtstreeks vanuit het brongegevensarchief naar Azure Synapse Analytics te kopiëren. Gebruik anders [Gefaseerde kopie met PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Als u gegevens efficiënt wilt kopiëren naar SQL Data Warehouse, u meer leren van [Azure Data Factory, waardoor het nog eenvoudiger en handiger is om inzichten uit gegevens te ontdekken wanneer u Data Lake Store gebruikt met SQL Data Warehouse.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Als niet aan de vereisten wordt voldaan, controleert Azure Data Factory de instellingen en valt het automatisch terug naar het BULKINSERT-mechanisme voor de gegevensverplaatsing.

1. De **brongekoppelde service** is voorzien van de volgende typen en verificatiemethoden:

    | Ondersteund brongegevensarchieftype                             | Ondersteund bronverificatietype                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Accountsleutelverificatie, beheerde identiteitsverificatie |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Verificatie van service-principal                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Accountsleutelverificatie, beheerde identiteitsverificatie |

    >[!IMPORTANT]
    >Als uw Azure Storage is geconfigureerd met VNet-serviceeindpunt, moet u beheerde identiteitsverificatie gebruiken : raadpleeg [de impact van het gebruik van VNet-serviceeindpunten met Azure-opslag](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Leer de vereiste configuraties in Data Factory van [Azure Blob - beheerde identiteitsverificatie](connector-azure-blob-storage.md#managed-identity) en [Azure Data Lake Storage Gen2 - beheerde identiteitsverificatie](connector-azure-data-lake-storage.md#managed-identity) sectie respectievelijk.

2. De **brongegevensindeling** is van **Parket,** **ORC**of **Afgebakende tekst**met de volgende configuraties:

   1. Mappad bevat geen wildcardfilter.
   2. De bestandsnaam is leeg of wijst naar één bestand. Als u de naam van het wildcardbestand `*` `*.*`opgeeft in kopieeractiviteit, kan dit alleen worden of .
   3. `rowDelimiter`is **standaard**, **\n,** **\r\n**of **\r**.
   4. `nullValue`wordt standaard achtergelaten of ingesteld op lege `treatEmptyAsNull` **tekenreeks** (""), en wordt als standaard gelaten of ingesteld op true.
   5. `encodingName`wordt als standaard achtergelaten of ingesteld op **utf-8**.
   6. `quoteChar`, `escapeChar`en `skipLineCount` zijn niet opgegeven. PolyBase-ondersteuning koptekstrij overslaan, die `firstRowAsHeader` kan worden geconfigureerd zoals in ADF.
   7. `compression`kan **geen compressie**zijn, **GZip**of **Leeglopen.**

3. Als uw bron een `recursive` map is, moet de kopieeractiviteit worden ingesteld op true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` en zijn niet gespecificeerd.

>[!NOTE]
>Als uw bron een map is, noteert u PolyBase bestanden uit de map en alle submappen en haalt deze geen gegevens op uit bestanden waarvoor de bestandsnaam begint met een onderstreping (_) of een periode (.), zoals hier gedocumenteerd [- argument LOCATIE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="staged-copy-by-using-polybase"></a>Gefaseerde kopie met PolyBase

Wanneer uw brongegevens niet native compatibel zijn met PolyBase, schakelt u gegevens kopiëren via een tussentijdse fasering Azure Blob-opslaginstantie in (het kan geen Azure Premium Storage zijn). In dit geval converteert Azure Data Factory de gegevens automatisch om te voldoen aan de vereisten voor gegevensindeling van PolyBase. Vervolgens wordt PolyBase aanroepen om gegevens in SQL Data Warehouse te laden. Ten slotte worden uw tijdelijke gegevens van de blob-opslag opgeschoond. Zie [Gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor meer informatie over het kopiëren van gegevens via een azure blob-opslagexemplaar met tijdelijke bestanden.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde Azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure-opslagaccount met de tussentijdse blobopslag. Geef vervolgens `enableStaging` `stagingSettings` de eigenschappen en eigenschappen op voor de kopieeractiviteit zoals weergegeven in de volgende code.

>[!IMPORTANT]
>Als uw Azure Storage met DeFasering is geconfigureerd met VNet-serviceeindpunt, moet u beheerde identiteitsverificatie gebruiken : raadpleeg [de impact van het gebruik van VNet-serviceeindpunten met Azure-opslag](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Meer informatie over de vereiste configuraties in Gegevensfabriek van [Azure Blob - beheerde identiteitsverificatie](connector-azure-blob-storage.md#managed-identity).

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

De volgende secties bieden best practices naast de aanbevolen procedures die worden genoemd in [Best practices voor Azure Synapse Analytics.](../synapse-analytics/sql/best-practices-sql-pool.md)

#### <a name="required-database-permission"></a>Vereiste databasemachtigingen

Als u PolyBase wilt gebruiken, moet de gebruiker die gegevens laadt in SQL Data Warehouse toestemming hebben voor ['CONTROL'](https://msdn.microsoft.com/library/ms191291.aspx) op de doeldatabase. Een manier om dat te bereiken is om de gebruiker toe te voegen als lid van de **db_owner** rol. Meer informatie over hoe u dat doen in het [SQL Data Warehouse-overzicht.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)

#### <a name="row-size-and-data-type-limits"></a>Rijgrootte en gegevenstypelimieten

PolyBase-belastingen zijn beperkt tot rijen kleiner dan 1 MB. Het kan niet worden gebruikt om te laden voor VARCHR(MAX), NVARCHAR (MAX) of VARBINARY (MAX). Zie [SQL Data Warehouse-servicecapaciteitslimieten](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)voor meer informatie .

Wanneer uw brongegevens rijen van meer dan 1 MB hebben, u de brontabellen verticaal splitsen in verschillende kleine. Zorg ervoor dat de grootste grootte van elke rij de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen met PolyBase en samengevoegd in Azure Synapse Analytics.

Als alternatief u voor gegevens met dergelijke brede kolommen niet-PolyBase gebruiken om de gegevens te laden met ADF, door de instelling 'PolyBase toestaan' uit te schakelen.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse-resourceklasse

Als u de best mogelijke doorvoer wilt bereiken, wijst u een grotere resourceklasse toe aan de gebruiker die gegevens laadt in SQL Data Warehouse via PolyBase.

#### <a name="polybase-troubleshooting"></a>PolyBase-probleemoplossing

**Laden naar kolom Achterdecimaal**

Als uw brongegevens in tekstnotatie of andere niet-PolyBase-compatibele opslag zijn (met gefaseerde kopie en PolyBase) en deze lege waarde bevatten die in de kolom SQL Data Warehouse Decimal moet worden geladen, u de volgende fout raken:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

De oplossing is om de optie **"Type standaard gebruik"**(als false) in de instellingen voor kopieeractiviteit -> PolyBase uit te zoeken. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" is een PolyBase native configuratie, die aangeeft hoe ontbrekende waarden in afgebakende tekstbestanden moeten worden verwerkt wanneer PolyBase gegevens uit het tekstbestand ophaalt. 

**`tableName`in Azure Synapse Analytics**

In de volgende tabel vindt u voorbeelden van het opgeven van de eigenschap **tableName** in de JSON-gegevensset. Het toont verschillende combinaties van schema en tabelnamen.

| DB-schema | Tabelnaam | **tabelNaam** Json, eigenschap               |
| --------- | ---------- | ----------------------------------------- |
| Dbo       | Mytable    | MyTable of dbo. MyTable of [dbo]. [Mijntafel] |
| dbo1 dbo1      | Mytable    | dbo1. MyTable of [dbo1]. [Mijntafel]          |
| Dbo       | Mijn.Tabel   | [Mijn.Tafel] of [dbo]. [Mijn.Tabel]            |
| dbo1 dbo1      | Mijn.Tabel   | [dbo1]. [Mijn.Tabel]                         |

Als u de volgende fout ziet, is het probleem mogelijk de waarde die u hebt opgegeven voor de eigenschap **tabelNaam.** Zie de vorige tabel voor de juiste manier om waarden op te geven voor de eigenschap **tabelName** JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolommen met standaardwaarden**

Momenteel accepteert de PolyBase-functie in Gegevensfabriek alleen hetzelfde aantal kolommen als in de doeltabel. Een voorbeeld is een tabel met vier kolommen waarin een van deze kolommen is gedefinieerd met een standaardwaarde. De invoergegevens moeten nog vier kolommen bevatten. Een invoerset met drie kolommen levert een fout op die vergelijkbaar is met het volgende bericht:

```
All columns of the table must be specified in the INSERT BULK statement.
```

De NULL-waarde is een speciale vorm van de standaardwaarde. Als de kolom nietig is verklaard, kunnen de invoergegevens in de blob voor die kolom leeg zijn. Maar het kan niet ontbreken in de invoergegevensset. PolyBase voegt NULL in voor ontbrekende waarden in Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Copy-instructie gebruiken om gegevens te laden in Azure SQL Data Warehouse (voorbeeld)

SQL Data Warehouse [COPY-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) ondersteunt rechtstreeks het laden van gegevens van **Azure Blob en Azure Data Lake Storage Gen2.** Als uw brongegevens voldoen aan de criteria die in deze sectie worden beschreven, u ervoor kiezen om de instructie COPY in ADF te gebruiken om gegevens in Azure SQL Data Warehouse te laden. Azure Data Factory controleert de instellingen en mislukt de kopieeractiviteit als niet aan de criteria wordt voldaan.

>[!NOTE]
>Momenteel data factory alleen ondersteuning kopie van COPY verklaring compatibele bronnen hieronder vermeld.

Het gebruik van de instructie COPY ondersteunt de volgende configuratie:

1. De **brongekoppelde service en -indeling** zijn voorzien van de volgende typen en verificatiemethoden:

    | Ondersteund brongegevensarchieftype                             | Ondersteunde indeling           | Ondersteund bronverificatietype                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Afgebakende tekst](format-delimited-text.md)             | Accountsleutelverificatie, verificatie van gedeelde toegangshandtekeningen, serviceprincipal-verificatie, beheerde identiteitsverificatie |
    | &nbsp;                                                       | [Parket](format-parquet.md)                    | Verificatie van accountsleutels, verificatie van handtekening voor gedeelde toegang |
    | &nbsp;                                                       | [Orc](format-orc.md)                        | Verificatie van accountsleutels, verificatie van handtekening voor gedeelde toegang |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Afgebakende tekst](format-delimited-text.md)<br/>[Parket](format-parquet.md)<br/>[Orc](format-orc.md) | Accountsleutelverificatie, serviceprincipal-verificatie, beheerde identiteitsverificatie |

    >[!IMPORTANT]
    >Als uw Azure Storage is geconfigureerd met VNet-serviceeindpunt, moet u beheerde identiteitsverificatie gebruiken : raadpleeg [de impact van het gebruik van VNet-serviceeindpunten met Azure-opslag](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Leer de vereiste configuraties in Data Factory van [Azure Blob - beheerde identiteitsverificatie](connector-azure-blob-storage.md#managed-identity) en [Azure Data Lake Storage Gen2 - beheerde identiteitsverificatie](connector-azure-data-lake-storage.md#managed-identity) sectie respectievelijk.

2. De opmaakinstellingen zijn met de volgende:

   1. Voor **Parket:** `compression` kan **geen compressie,** **Snappy**, of **GZip**.
   2. Voor **ORC**: `compression` kan geen **compressie**, of **```zlib```** **Snappy**.
   3. Voor **afgebakende tekst**:
      1. `rowDelimiter`is expliciet ingesteld als **één teken** of "**\r\n**", de standaardwaarde wordt niet ondersteund.
      2. `nullValue`wordt standaard achtergelaten of ingesteld op **lege tekenreeks** ("").
      3. `encodingName`wordt als standaard achtergelaten of ingesteld op **utf-8 of utf-16**.
      4. `escapeChar`moet hetzelfde `quoteChar`zijn als , en is niet leeg.
      5. `skipLineCount`wordt als standaard achtergelaten of ingesteld op 0.
      6. `compression`kan **geen compressie** of **GZip**zijn.

3. Als uw bron een `recursive` map is, moet de kopieeractiviteit worden ingesteld op true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` en zijn niet gespecificeerd.

De volgende copy-instructieinstellingen `allowCopyCommand` worden ondersteund onder kopieeractiviteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| standaardWaarden | Hiermee geeft u de standaardwaarden op voor elke doelkolom in SQL DW.  De standaardwaarden in de eigenschap overschrijven de standaarddrukset in het gegevensmagazijn en de identiteitskolom kan geen standaardwaarde hebben. | Nee |
| extraOpties | Aanvullende opties die direct worden doorgegeven aan sql DW COPY-instructie in de "Met"-component in [de instructie COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Quote de waarde indien nodig om af te stemmen met de COPY verklaring eisen. | Nee |

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
                    "type": "AzureBlobStorageReadSettings",
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


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Activiteitseigenschappen getMetadata

Voor meer informatie over de eigenschappen, controleert [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Toewijzing van gegevenstype voor Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in de toewijzingsgegevensstroom transformeert, u lezen en schrijven naar tabellen van Azure Synapse Analytics. Zie voor meer informatie de [brontransformatie](data-flow-source.md) en [sinktransformatie](data-flow-sink.md) in kaartgegevensstromen.

### <a name="source-transformation"></a>Brontransformatie

Instellingen die specifiek zijn voor Azure Synapse Analytics zijn beschikbaar op het tabblad **Bronopties** van de brontransformatie. 

**Invoer:** Selecteer of u uw bron op ```Select * from <table-name>```een tabel (equivalent van) richt of een aangepaste SQL-query invoert.

**Query:** Als u Query selecteert in het invoerveld, voert u een SQL-query in voor uw bron. Met deze instelling wordt elke tabel die u in de gegevensset hebt gekozen, overschreven. **Order** By-clausules worden hier niet ondersteund, maar u wel een volledige SELECT FROM-instructie instellen. U ook door de gebruiker gedefinieerde tabelfuncties gebruiken. **selecteer * uit udfGetData()** is een UDF in SQL die een tabel retourneert. Deze query produceert een brontabel die u gebruiken in uw gegevensstroom. Het gebruik van query's is ook een geweldige manier om rijen te verminderen voor het testen of voor zoekopdrachten. 

* SQL-voorbeeld:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchgrootte:** voer een batchgrootte in om grote gegevens in reads te splitsen. In gegevensstromen gebruikt ADF deze instelling om caches van spark-kolommen in te stellen. Dit is een optieveld dat Spark-standaardinstellingen gebruikt als deze leeg blijft.

**Isolatieniveau:** de standaardwaarde voor SQL-bronnen in de toewijzingsgegevensstroom wordt niet-vastgelegd gelezen. U het isolatieniveau hier wijzigen in een van deze waarden:
* Waarvoor u zich inzet
* Niet-vastgelegd lezen
* Herhaalbaar lezen
* Serializable
* None

![Isolatieniveau](media/data-flow/isolationlevel.png "Isolatieniveau")

### <a name="sink-transformation"></a>Gootsteentransformatie

Instellingen die specifiek zijn voor Azure Synapse Analytics zijn beschikbaar op het tabblad **Instellingen** van de sinktransformatie.

**Updatemethode:** Hiermee bepaalt u welke bewerkingen zijn toegestaan op uw databasebestemming. De standaardinstelling is om alleen inserts toe te staan. Als u rijen wilt bijwerken, upsert of verwijdert, is een transformatie met een wijzigingsrij vereist om rijen voor die acties te taggen. Voor updates, upserts en deletes moet een sleutelkolom of kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Tabelactie:** Hiermee bepaalt u of u alle rijen uit de doeltabel opnieuw wilt maken of verwijderen voordat u gaat schrijven.
* Geen: Er wordt geen actie ondernomen aan de tafel.
* Opnieuw maken: de tabel wordt gedropt en opnieuw gemaakt. Vereist als u dynamisch een nieuwe tabel maakt.
* Afgekapt: alle rijen uit de doeltabel worden verwijderd.

**Fasering inschakelen:** Hiermee bepaalt u of [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) al dan niet moet worden gebruikt bij het schrijven naar Azure Synapse Analytics

**Batchgrootte:** hiermee bepaalt u hoeveel rijen er in elke bucket worden geschreven. Grotere batchformaten verbeteren compressie en geheugenoptimalisatie, maar lopen risico op uitzonderingen met het geheugen bij het incacheren van gegevens.

**Pre- en Post SQL-scripts**: Voer multiline SQL-scripts in die worden uitgevoerd vóór (pre-processing) en na (post-processing) gegevens worden geschreven naar uw Sink-database

![pre- en postSQL-verwerkingsscripts](media/data-flow/prepost1.png "SQL-verwerkingsscripts")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Toewijzing van gegevenstype voor Azure Synapse Analytics

Wanneer u gegevens van of naar Azure Synapse Analytics kopieert, worden de volgende toewijzingen gebruikt van Azure Synapse Analytics-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe Activiteit kopiëren het bronschema en het gegevenstype naar de gootsteen brengt.

>[!TIP]
>Raadpleeg [tabelgegevenstypen in het Azure Synapse Analytics-artikel](../synapse-analytics/sql/develop-tables-data-types.md) over SQL DW-ondersteunde gegevenstypen en de tijdelijke oplossingen voor niet-ondersteunde typen.

| Azure Synapse Analytics-gegevenstype    | Tussentijds gegevenstype Data Factory |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binair                                | Byte                         |
| bit                                   | Booleaans                        |
| Char                                  | Tekenreeks, Char[]                 |
| date                                  | DateTime                       |
| Datum/tijd                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datumtijdverschuiving                        | Datumtijdverschuiving                 |
| Decimal                               | Decimal                        |
| FILESTREAM-kenmerk (varbinary(max.) | Byte                         |
| Drijvend                                 | Double                         |
| installatiekopie                                 | Byte                         |
| int                                   | Int32                          |
| Geld                                 | Decimal                        |
| Nchar                                 | Tekenreeks, Char[]                 |
| numeriek                               | Decimal                        |
| nvarchar                              | Tekenreeks, Char[]                 |
| real                                  | Enkel                         |
| rijversie                            | Byte                         |
| smalldatetijd                         | DateTime                       |
| smallint                              | Int16                          |
| kleingeld                            | Decimal                        |
| tijd                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary varbinary                             | Byte                         |
| varchar                               | Tekenreeks, Char[]                 |

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door Activiteit kopiëren in Azure Data Factory.
