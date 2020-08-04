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
ms.date: 08/03/2020
ms.openlocfilehash: caa132475df6481db7228a1ef7e18026b12cf38f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534325"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens in azure Synapse Analytics (voorheen Azure SQL Data Warehouse) kopiëren en transformeren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Huidige versie](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Synapse Analytics en gegevens stroom te gebruiken om gegevens te transformeren in Azure Data Lake Storage Gen2. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Synapse Analytics-connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Voor kopieer activiteiten ondersteunt deze Azure Synapse Analytics-connector deze functies:

- Gegevens kopiëren met behulp van SQL-verificatie en Azure Active Directory (Azure AD) verificatie van het toepassings token met een service-principal of beheerde identiteiten voor Azure-resources.
- Haal als bron gegevens op met behulp van een SQL-query of opgeslagen procedure.
- Laad gegevens met behulp van [poly base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) of Copy- [instructie](#use-copy-statement) (preview) of bulksgewijze insert als sink. We raden u aan om een poly base-of COPY-instructie (preview) te geven voor betere Kopieer prestaties. De connector biedt ook ondersteuning voor het automatisch maken van een doel tabel als deze niet bestaat op basis van het bron schema.

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van Azure Data Factory Integration Runtime, configureert u een [firewall regel op server niveau](../azure-sql/database/firewall-configure.md) zodat Azure-Services toegang hebben tot de [logische SQL-Server](../azure-sql/database/logical-servers.md).
> Als u gegevens kopieert met behulp van een zelf-hostende Integration runtime, moet u de firewall zo configureren dat het juiste IP-bereik is toegestaan. Dit bereik bevat het IP-adres van de computer dat wordt gebruikt om verbinding te maken met Azure Synapse Analytics.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Gebruik poly Base om gegevens te laden in azure Synapse Analytics om de beste prestaties te krijgen. De sectie [poly Base gebruiken voor het laden van gegevens in azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) bevat details. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die Data Factory entiteiten definiëren die specifiek zijn voor een Azure Synapse Analytics-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde Azure Synapse Analytics-service:

| Eigenschap            | Beschrijving                                                  | Vereist                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | De eigenschap type moet worden ingesteld op **AzureSqlDW**.             | Yes                                                          |
| Verbindings    | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Synapse Analytics-exemplaar voor de **Connections Tring** -eigenschap. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook een wacht woord/Service-Principal-sleutel in Azure Key Vault plaatsen en als de SQL-verificatie de `password` configuratie uit de Connection String halen. Zie het JSON-voor beeld onder de tabel en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Yes                                                          |
| servicePrincipalId  | Geef de client-ID van de toepassing op.                         | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| tenant              | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja, wanneer u Azure AD-verificatie gebruikt met een service-principal. |
| connectVia          | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No                                                           |

Raadpleeg de volgende secties over respectievelijk de vereisten en JSON-voor beelden voor verschillende verificatie typen:

- [SQL-verificatie](#sql-authentication)
- Verificatie van Azure AD-toepassings token: [Service-Principal](#service-principal-authentication)
- Verificatie van Azure AD-toepassings tokens: [beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Als u de fout code ' UserErrorFailedToConnectToSqlServer ' aanmeldt en er een bericht wordt weer gegeven als ' de sessie limiet voor de data base is XXX en is bereikt. ', voegt u toe `Pooling=false` aan uw Connection String en probeert u het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voor beeld van een gekoppelde service dat gebruikmaakt van SQL-verificatie

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

Voer de volgende stappen uit om verificatie van Azure AD-toepassings tokens op basis van de service-principal te gebruiken:

1. **[Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** vanuit de Azure Portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

   - Toepassings-id
   - Toepassings sleutel
   - Tenant-id

2. **[Richt een Azure Active Directory beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** in voor uw server in de Azure portal als u dat nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de data base.

3. **[Maak Inge sloten database gebruikers](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** voor de Service-Principal. Maak verbinding met het Data Warehouse van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SSMS, met een Azure AD-identiteit die ten minste een gebruikers machtiging heeft gewijzigd. Voer de volgende T-SQL uit:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Verleen de Service-Principal machtigingen die** voor SQL-gebruikers of anderen gelden. Voer de volgende code uit of Raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voor beeld van een gekoppelde service die gebruikmaakt van Service-Principal-verificatie

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke Factory vertegenwoordigt. U kunt deze beheerde identiteit voor Azure Synapse Analytics-verificatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data warehouse met behulp van deze identiteit.

Als u beheerde identiteits verificatie wilt gebruiken, voert u de volgende stappen uit:

1. **[Richt een Azure Active Directory beheerder](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** in voor uw server op de Azure portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de data base.

2. **[Inge sloten database gebruikers maken](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** voor de Data Factory beheerde identiteit. Maak verbinding met het Data Warehouse van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SSMS, met een Azure AD-identiteit die ten minste een gebruikers machtiging heeft gewijzigd. Voer de volgende T-SQL uit.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Verleen de Data Factory beheerde identiteit de benodigde machtigingen** zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit of Raadpleeg [hier](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)meer opties. Als u poly Base wilt gebruiken om de gegevens te laden, leest u de [vereiste database machtiging](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Een gekoppelde Azure Synapse Analytics-service configureren** in azure Data Factory.

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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

De volgende eigenschappen worden ondersteund voor Azure Synapse Analytics-gegevensset:

| Eigenschap  | Beschrijving                                                  | Vereist                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | De eigenschap **type** van de DataSet moet worden ingesteld op **AzureSqlDWTable**. | Yes                         |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

### <a name="dataset-properties-example"></a>Voor beeld van eigenschappen van gegevensset

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

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Synapse Analytics-bron en Sink.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics als de bron

Als u gegevens wilt kopiëren uit Azure Synapse Analytics, stelt u de eigenschap **type** in de bron voor het kopiëren van de activiteit in op **SqlDWSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap                     | Beschrijving                                                  | Vereist |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **SqlDWSource**. | Yes      |
| sqlReaderQuery               | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. De laatste SQL-instructie moet een instructie SELECT in de opgeslagen procedure zijn. | No       |
| storedProcedureParameters    | Para meters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-of waardeparen. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | No       |
| isolationLevel | Hiermee geeft u het vergrendelings gedrag van de trans actie voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **snap shot**. Als u niets opgeeft, wordt het standaard isolatie niveau van de data base gebruikt. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | No |

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics als Sink

Azure Data Factory ondersteunt drie manieren om gegevens te laden in SQL Data Warehouse.

![Kopieer opties voor SQL DW-sinks](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Poly Base gebruiken](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [De instructie COPY gebruiken (preview)](#use-copy-statement)
- Bulksgewijs invoegen gebruiken

De snelste en meest schaal bare manier om gegevens te laden, is via [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) of de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview).

Als u gegevens wilt kopiëren naar Azure SQL Data Warehouse, stelt u het sink-type in de Kopieer activiteit in op **SqlDWSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **SqlDWSink**. | Yes                                           |
| allowPolyBase     | Hiermee wordt aangegeven of poly Base moet worden gebruikt voor het laden van gegevens in SQL Data Warehouse. `allowCopyCommand`en `allowPolyBase` kan niet beide zijn ingesteld op ' True '. <br/><br/>Zie [poly Base gebruiken om gegevens te laden in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sectie voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **True** en **False** (standaard). | Nee.<br/>Toep assen bij het gebruik van poly base.     |
| polyBaseSettings  | Een groep eigenschappen die kan worden opgegeven wanneer de `allowPolybase` eigenschap is ingesteld op **True**. | Nee.<br/>Toep assen bij het gebruik van poly base. |
| allowCopyCommand | Hiermee wordt aangegeven of een [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview) moet worden gebruikt om gegevens te laden in SQL Data Warehouse. `allowCopyCommand`en `allowPolyBase` kan niet beide zijn ingesteld op ' True '. <br/><br/>Zie de [instructie Copy gebruiken om gegevens te laden in Azure SQL Data Warehouse](#use-copy-statement) sectie voor beperkingen en Details.<br/><br/>Toegestane waarden zijn **True** en **False** (standaard). | Nee.<br>Toep assen bij het gebruik van COPY. |
| copyCommandSettings | Een groep eigenschappen die kan worden opgegeven wanneer `allowCopyCommand` eigenschap is ingesteld op True. | Nee.<br/>Toep assen bij het gebruik van COPY. |
| writeBatchSize    | Het aantal rijen dat in de SQL-tabel **per batch**moet worden ingevoegd.<br/><br/>De toegestane waarde is een **geheel getal** (aantal rijen). Standaard bepaalt Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.     |
| writeBatchTimeout | Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt.<br/><br/>De toegestane waarde is **time span**. Voor beeld: "00:30:00" (30 minuten). | Nee.<br/>Toep assen bij het gebruik van bulksgewijs invoegen.        |
| preCopyScript     | Geef een SQL-query op die moet worden uitgevoerd voordat er gegevens naar Azure SQL Data Warehouse worden geschreven in elke uitvoering. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | No                                            |
| tableOption | Hiermee wordt aangegeven of [de Sink-tabel automatisch](copy-activity-overview.md#auto-create-sink-tables) moet worden gemaakt als deze niet bestaat op basis van het bron schema. Toegestane waarden zijn: `none` (standaard), `autoCreate` . |No |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens, zoals SQL Data Warehouse Dwu's voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op dat u deze functie wilt uitschakelen. | Nee (standaard instelling `false` ) |

#### <a name="sql-data-warehouse-sink-example"></a>Voor beeld van SQL Data Warehouse Sink

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Poly Base gebruiken voor het laden van gegevens in Azure SQL Data Warehouse

Het gebruik van [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) is een efficiënte manier om een grote hoeveelheid gegevens in azure Synapse Analytics te laden met een hoge door voer. U ziet een grote toename in de door voer door poly Base te gebruiken in plaats van het standaard BULKINSERT-mechanisme. Zie voor een overzicht met een use-case [1 TB laden in azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Als uw bron gegevens zich in **Azure Blob, Azure data Lake Storage gen1 of Azure data Lake Storage Gen2**bevindt en de **indeling poly base-compatibel is**, kunt u de Kopieer activiteit gebruiken om direct poly Base te activeren, zodat Azure SQL Data Warehouse de gegevens van de bron kan ophalen. Zie voor meer informatie **[direct kopiëren met poly base](#direct-copy-by-using-polybase)**.
- Als uw brongegevens archief en-indeling niet oorspronkelijk worden ondersteund door poly Base, gebruikt u in plaats daarvan de functie **[voor gefaseerde kopie door gebruik te maken van poly base](#staged-copy-by-using-polybase)** . De functie voor gefaseerd kopiëren biedt u ook een betere door voer. De gegevens worden automatisch geconverteerd naar een indeling die compatibel is met poly Base, de gegevens worden opgeslagen in Azure Blob Storage en vervolgens worden poly base aangeroepen om gegevens te laden in SQL Data Warehouse.

> [!TIP]
> Meer informatie over [Best practices voor het gebruik van poly base](#best-practices-for-using-polybase).

De volgende poly base-instellingen worden ondersteund onder `polyBaseSettings` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Hiermee geeft u het aantal of percentage rijen op dat kan worden afgewezen voordat de query mislukt.<br/><br/>Meer informatie over de afwijzings opties van poly Base vindt u in de sectie argumenten van een [externe tabel maken (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Toegestane waarden zijn 0 (standaard), 1, 2, enzovoort. | No                                            |
| rejectType        | Hiermee wordt aangegeven of de **rejectValue** -optie een letterlijke waarde of een percentage is.<br/><br/>Toegestane waarden zijn **Value** (standaard) en **percentage**. | No                                            |
| rejectSampleValue | Bepaalt het aantal rijen dat moet worden opgehaald voordat poly base het percentage geweigerde rijen opnieuw berekent.<br/><br/>Toegestane waarden zijn 1, 2, enzovoort. | Ja, als de **rejectType** een **percentage**is. |
| useTypeDefault    | Hiermee geeft u op hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie argumenten in [externe BESTANDS indeling maken (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Toegestane waarden zijn **True** en **False** (standaard).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Direct kopiëren door poly Base te gebruiken

SQL Data Warehouse poly Base ondersteunt direct Azure Blob, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Als uw bron gegevens voldoen aan de criteria die in deze sectie worden beschreven, gebruikt u poly Base om rechtstreeks vanuit de brongegevens opslag naar Azure Synapse Analytics te kopiëren. Als dat niet het geval is, gebruikt u de [gefaseerde kopie met poly base](#staged-copy-by-using-polybase).

> [!TIP]
> Als u gegevens efficiënt wilt kopiëren naar SQL Data Warehouse, kunt u meer te weten komen uit [Azure Data Factory het nog eenvoudiger en handig maken om inzichten van gegevens te ontdekken wanneer u data Lake Store met SQL Data Warehouse gebruikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Als niet aan de vereisten wordt voldaan, worden de instellingen door Azure Data Factory gecontroleerd en wordt automatisch teruggeleid naar het BULKINSERT-mechanisme voor het verplaatsen van gegevens.

1. De **gekoppelde bron service** heeft de volgende typen en verificatie methoden:

    | Ondersteund type brongegevens archief                             | Ondersteund type bron verificatie                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Verificatie van account sleutels, beheerde identiteits verificatie |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Verificatie van service-principal                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Verificatie van account sleutels, beheerde identiteits verificatie |

    >[!IMPORTANT]
    >Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken: Raadpleeg de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Meer informatie over de vereiste configuraties in Data Factory van een [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2 beheerde identiteits verificatie](connector-azure-data-lake-storage.md#managed-identity) sectie.

2. De **indeling van de bron gegevens** is van **Parquet**, **Orc**of **tekst met scheidings tekens**, met de volgende configuraties:

   1. Mappad bevat geen filter voor joker tekens.
   2. De bestands naam is leeg of verwijst naar één bestand. Als u de naam van het Joker teken opgeeft in de Kopieer activiteit, kan deze alleen zijn `*` of `*.*` .
   3. `rowDelimiter`is **standaard**, **\n**, **\r\n**of **\r**.
   4. `nullValue`is standaard **ingesteld op '** "' ('"), en `treatEmptyAsNull` wordt als standaard waarde ingevuld of ingesteld op True.
   5. `encodingName`is standaard ingesteld op **UTF-8**.
   6. `quoteChar`, `escapeChar` en `skipLineCount` niet opgegeven. Poly base-ondersteuning koptekst rij overs Laan, die in ADF kan worden geconfigureerd `firstRowAsHeader` .
   7. `compression`kan **geen compressie**, **gzip**of **Deflate**zijn.

3. Als uw bron een map is, `recursive` moet u de Kopieer activiteit instellen op True.

4. `wildcardFolderPath`, `wildcardFilename` , `modifiedDateTimeStart` `modifiedDateTimeEnd` en `additionalColumns` zijn niet opgegeven.

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

### <a name="staged-copy-by-using-polybase"></a>Gefaseerde kopie door gebruik te maken van poly base

Als uw bron gegevens niet systeem eigen compatibel zijn met poly Base, kunt u gegevens kopiëren met behulp van een tussentijds Azure Blob Storage-exemplaar (het kan geen Azure-Premium Storage zijn). In dit geval worden de gegevens in Azure Data Factory automatisch geconverteerd om te voldoen aan de vereisten voor gegevens formaat van poly base. Vervolgens wordt poly base aangeroepen om gegevens te laden in SQL Data Warehouse. Ten slotte ruimt de app de tijdelijke gegevens op uit de Blob-opslag. Zie [gefaseerde kopie](copy-activity-performance-features.md#staged-copy) voor meer informatie over het kopiëren van gegevens via een staging-exemplaar voor Azure Blob Storage.

Als u deze functie wilt gebruiken, maakt u een [gekoppelde azure Blob Storage-service](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar het Azure Storage-account met de tussenliggende Blob-opslag. Geef vervolgens de `enableStaging` `stagingSettings` Eigenschappen en op voor de Kopieer activiteit, zoals in de volgende code wordt weer gegeven.

>[!IMPORTANT]
>Als uw staging-Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken. Zie de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)voor meerwaarde ring. Meer informatie over de vereiste configuraties in Data Factory van [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity).

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

### <a name="best-practices-for-using-polybase"></a>Aanbevolen procedures voor het gebruik van poly base

De volgende secties bevatten aanbevolen procedures, naast de methoden die worden genoemd in [Best Practices for Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Vereiste database machtiging

Als u poly Base wilt gebruiken, moet de gebruiker die gegevens laadt in SQL Data Warehouse de [machtiging ' besturings element '](https://msdn.microsoft.com/library/ms191291.aspx) hebben voor de doel database. Een manier om dat te doen, is om de gebruiker toe te voegen als lid van de rol **db_owner** . Meer informatie over hoe u dit doet in het [overzicht van SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limieten voor Rijgrootte en gegevens typen

Poly base-belastingen zijn beperkt tot rijen die kleiner zijn dan 1 MB. Het kan niet worden gebruikt om te laden naar VARCHR (MAX), NVARCHAR (MAX) of VARBINARY (MAX). Zie [SQL Data Warehouse service-capaciteits limieten](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)voor meer informatie.

Als de bron gegevens rijen hebben die groter zijn dan 1 MB, wilt u de bron tabellen wellicht verticaal splitsen in meerdere kleinen. Zorg ervoor dat de maximale grootte van elke rij de limiet niet overschrijdt. De kleinere tabellen kunnen vervolgens worden geladen door poly Base te gebruiken en samen te voegen in azure Synapse Analytics.

Voor gegevens met een brede kolom kunt u ook gebruikmaken van niet-poly Base om de gegevens te laden met behulp van ADF, door de instelling ' poly base toestaan ' uit te scha kelen.

#### <a name="sql-data-warehouse-resource-class"></a>Resource klasse SQL Data Warehouse

Wijs een grotere resource klasse toe aan de gebruiker die gegevens laadt in SQL Data Warehouse via Poly Base om de best mogelijke door voer te krijgen.

#### <a name="polybase-troubleshooting"></a>Poly base-probleem oplossing

**Laden naar decimale kolom**

Als de bron gegevens in tekst indeling of andere niet-poly base-compatibele archieven (met gefaseerde kopie en poly base) staan en een lege waarde bevat die in SQL Data Warehouse decimale kolom moet worden geladen, kunt u de volgende fout raken:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

De oplossing bestaat uit het opheffen van de selectie van de optie**type standaard gebruiken**(als onwaar) in Sink voor kopieer activiteit-> poly base-instellingen. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" is een poly base systeem eigen configuratie, waarmee wordt aangegeven hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.

**`tableName`in azure Synapse Analytics**

De volgende tabel bevat voor beelden van de manier waarop u de eigenschap **TableName** in de JSON-gegevensset kunt opgeven. Er worden verschillende combi Naties van schema-en tabel namen weer gegeven.

| DB-schema | Tabelnaam | **TableName** JSON-eigenschap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable of dbo. MyTable of [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable of [dbo1]. MyTable          |
| dbo       | Mijn. table   | [Mijn. table] of [dbo]. [Mijn. tabel]            |
| dbo1      | Mijn. table   | [dbo1]. [Mijn. tabel]                         |

Als de volgende fout wordt weer gegeven, is het probleem mogelijk de waarde die u hebt opgegeven voor de eigenschap **TableName** . Zie de voor gaande tabel voor de juiste manier om waarden op te geven voor de JSON-eigenschap **TableName** .

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolommen met standaard waarden**

Op dit moment accepteert de functie poly base in Data Factory alleen hetzelfde aantal kolommen als in de doel tabel. Een voor beeld is een tabel met vier kolommen waarvan een van deze is gedefinieerd met een standaard waarde. De invoer gegevens moeten nog vier kolommen hebben. Een invoer-gegevensset met drie kolommen resulteert in een fout die vergelijkbaar is met het volgende bericht:

```
All columns of the table must be specified in the INSERT BULK statement.
```

De NULL-waarde is een speciale vorm van de standaard waarde. Als de kolom null-waarden bevat, kunnen de invoer gegevens in de BLOB voor die kolom leeg zijn. Maar dit kan niet ontbreken in de invoer gegevensset. Poly base voegt NULL toe voor ontbrekende waarden in azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>De instructie COPY gebruiken om gegevens in Azure SQL Data Warehouse te laden (preview)

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
    >Als uw Azure Storage is geconfigureerd met het VNet-service-eind punt, moet u beheerde identiteits verificatie gebruiken: Raadpleeg de [gevolgen van het gebruik van VNet-service-eind punten met Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Meer informatie over de vereiste configuraties in Data Factory van een [Azure Blob-beheerde identiteits verificatie](connector-azure-blob-storage.md#managed-identity) en [Azure data Lake Storage Gen2 beheerde identiteits verificatie](connector-azure-data-lake-storage.md#managed-identity) sectie.

2. Indelings instellingen zijn met het volgende:

   1. Voor **Parquet**: `compression` kan **geen compressie**, **Snappy**of **gzip**zijn.
   2. Voor **Orc**: `compression` kan **geen compressie**, **```zlib```** of **Snappy**zijn.
   3. Voor **tekst met scheidings tekens**:
      1. `rowDelimiter`is expliciet ingesteld als **één teken** of als '**\r\n**', de standaard waarde wordt niet ondersteund.
      2. `nullValue`is standaard ingesteld op een **lege teken reeks** ("").
      3. `encodingName`is standaard ingesteld op **UTF-8 of UTF-16**.
      4. `escapeChar`moet hetzelfde zijn als `quoteChar` , en is niet leeg.
      5. `skipLineCount`is standaard ingesteld op 0.
      6. `compression`kan **geen compressie** of **gzip**zijn.

3. Als uw bron een map is, `recursive` moet u in de Kopieer activiteit de waarde true instellen en `wildcardFilename` moet dit zijn `*` . 

4. `wildcardFolderPath`, `wildcardFilename` (andere dan `*` ), `modifiedDateTimeStart` `modifiedDateTimeEnd` en `additionalColumns` zijn niet opgegeven.

De volgende instellingen voor de Kopieer instructie worden ondersteund onder `allowCopyCommand` in de Kopieer activiteit:

| Eigenschap          | Beschrijving                                                  | Vereist                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| Standaard waarde | Hiermee geeft u de standaard waarden voor elke doel kolom in SQL DW op.  De standaard waarden in de eigenschap overschrijven de standaard beperking die is ingesteld in het Data Warehouse en de identiteits kolom kan geen standaard waarde hebben. | No |
| additionalOptions | Aanvullende opties die worden door gegeven aan de SQL DW COPY-instructie, worden rechtstreeks in de with-component in een [copy-instructie](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)opgenomen. Quote de waarde waar nodig om uit te lijnen met de vereisten voor het kopiëren van de instructie. | No |

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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u tabellen lezen en ernaar schrijven vanuit Azure Synapse Analytics. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen.

### <a name="source-transformation"></a>Bron transformatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **bron opties** van de bron transformatie.

**Invoer** Selecteer of u uw bron op een tabel (equivalent van) wilt aanwijzen ```Select * from <table-name>``` of voer een aangepaste SQL-query in.

**Fase ring inschakelen** Het wordt nadrukkelijk aanbevolen om deze optie te gebruiken in productie werkbelastingen met Synapse DW-bronnen. Wanneer u een gegevens stroom activiteit met Synapase-bronnen vanuit een pijp lijn uitvoert, wordt u door ADF gevraagd naar een opslag account voor de faserings locatie en wordt deze gebruikt voor het laden van gegevens. Het is het snelste mechanisme voor het laden van gegevens uit Synapse DW.

**Query**: als u in het invoer veld query selecteert, voert u een SQL-query in voor uw bron. Deze instelling overschrijft elke tabel die u in de gegevensset hebt gekozen. **Order by** -componenten worden hier niet ondersteund, maar u kunt een volledige Select from-instructie instellen. U kunt ook door de gebruiker gedefinieerde tabel functies gebruiken. **Select * from udfGetData ()** is een UDF in SQL die een tabel retourneert. Met deze query wordt een bron tabel geproduceerd die u in uw gegevens stroom kunt gebruiken. Het gebruik van query's is ook een uitstekende manier om rijen te verminderen voor het testen of voor Zoek opdrachten.

SQL-voor beeld:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batch grootte**: Voer een batch grootte in om grote hoeveel heden gegevens in Lees bewerkingen te segmenteren. In gegevens stromen gebruikt ADF deze instelling om in te stellen Spark in cache opslaan. Dit is een optie veld waarin de standaard waarden van Spark worden gebruikt als deze leeg blijven.

**Isolatie niveau**: de standaard waarde voor SQL-bronnen in de toewijzings gegevens stroom is niet-vastgelegd. U kunt het isolatie niveau hier wijzigen in een van deze waarden:

- Doorgevoerde lezen
- Lezen niet-doorgevoerd
- Herhaal bare Lees bewerking
- Serialiseerbaar *-geen (isolatie niveau negeren)

![Isolatie niveau](media/data-flow/isolationlevel.png "Isolatie niveau")

### <a name="sink-transformation"></a>Sink-trans formatie

Instellingen die specifiek zijn voor Azure Synapse Analytics, zijn beschikbaar op het tabblad **instellingen** van de Sink-trans formatie.

**Update methode:** Hiermee wordt bepaald welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan. Als u rijen wilt bijwerken, upsert of verwijderen, moet u een alter-Row trans formatie voor deze acties labelen. Voor updates, upsert en verwijderen moet een sleutel kolom of-kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Tabel actie:** Hiermee wordt bepaald of alle rijen van de doel tabel opnieuw moeten worden gemaakt of verwijderd voordat er wordt geschreven.

- Geen: er wordt geen actie uitgevoerd voor de tabel.
- Opnieuw maken: de tabel wordt verwijderd en opnieuw gemaakt. Vereist als er dynamisch een nieuwe tabel wordt gemaakt.
- Afkappen: alle rijen uit de doel tabel worden verwijderd.

**Fase ring inschakelen:** Hiermee wordt bepaald of [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) moet worden gebruikt bij het schrijven naar Azure Synapse Analytics

**Batch grootte**: bepaalt hoeveel rijen er worden geschreven in elke Bucket. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens.

**SQL-scripts vooraf en post**: Voer SQL-scripts met meerdere regels in die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base worden geschreven

![scripts voor SQL-verwerking vooraf en na](media/data-flow/prepost1.png "SQL-verwerkings scripts")

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Toewijzing van gegevens type voor Azure Synapse Analytics

Wanneer u gegevens kopieert vanuit of naar Azure Synapse Analytics, worden de volgende toewijzingen gebruikt vanuit gegevens typen van Azure Synapse Analytics om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteiten het bron schema en het gegevens type aan de Sink toewijzen.

>[!TIP]
>Raadpleeg de [tabel gegevens typen in het Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) -artikel over ondersteunde gegevens typen van SQL DW en de tijdelijke oplossingen voor niet-ondersteunde.

| Azure Synapse Analytics-gegevens type    | Data Factory tussentijds gegevens type |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binair                                | Byte []                         |
| bit                                   | Booleaans                        |
| char                                  | Teken reeks, char []                 |
| date                                  | DateTime                       |
| Datum/tijd                              | DateTime                       |
| datetime2                             | DateTime                       |
| Date time offset                        | Date time offset                 |
| Decimal                               | Decimal                        |
| FILESTREAM-kenmerk (varbinary (max)) | Byte []                         |
| Float                                 | Dubbel                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | Teken reeks, char []                 |
| numeriek                               | Decimal                        |
| nvarchar                              | Teken reeks, char []                 |
| werkelijk                                  | Enkelvoudig                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| tijd                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte []                         |
| varchar                               | Teken reeks, char []                 |

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
