---
title: Gegevens kopiëren en transformeren in Azure SQL Database
description: Informatie over het kopiëren van gegevens van en naar Azure SQL Database en het transformeren van gegevens in Azure SQL Database met behulp van Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: ad802521fe4202b8c5e27a82e0adf142dfa69228
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929648"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Gegevens in Azure SQL Database kopiëren en transformeren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-sql-connector.md)
> * [Huidige versie](connector-azure-sql-database.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure SQL Database, en om gegevens in Azure SQL Database te transformeren met behulp van gegevens stromen. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure SQL Database-Connector wordt ondersteund voor de volgende activiteiten:

- De tabel [copy-activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Voor kopieer activiteit ondersteunt deze Azure SQL Database-Connector de volgende functies:

- Kopiëren van gegevens met behulp van SQL-verificatie en Azure Active Directory (Azure AD) verificatie van het toepassings token met een service-principal of beheerde identiteiten voor Azure-resources.
- Als bron, waarbij gegevens worden opgehaald met behulp van een SQL-query of een opgeslagen procedure.
- Het toevoegen van gegevens aan een doel tabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren als een sink.

>[!NOTE]
>Azure SQL Database [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) wordt nu niet ondersteund door deze connector. U kunt een [algemene ODBC-Connector](connector-odbc.md) en een SQL Server ODBC-stuur programma gebruiken via een zelf-hostende Integration runtime. Volg [deze richt lijnen voor het](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) downloaden van ODBC-stuur Programma's en het Connection String van configuraties.

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van de Azure Data Factory Integration runtime, configureert u een [azure SQL Server-firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) zodat Azure-Services toegang hebben tot de server.
> Als u gegevens kopieert met behulp van een zelf-hostende Integration runtime, configureert u de Azure SQL Server firewall zodanig dat het juiste IP-bereik is toegestaan. Dit bereik bevat het IP-adres van de computer dat wordt gebruikt om verbinding te maken met Azure SQL Database.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Azure Data Factory entiteiten die specifiek zijn voor een Azure SQL Database-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

Deze eigenschappen worden ondersteund voor een Azure SQL Database gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **AzureSqlDatabase**. | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het Azure SQL Database-exemplaar voor de **Connections Tring** -eigenschap. <br/>Markeer dit veld als **SecureString** om het veilig op te slaan in azure Data Factory. U kunt ook een wacht woord of Service-Principal-sleutel in Azure Key Vault plaatsen. Als de SQL-verificatie wordt uitgevoerd, haalt u de `password` configuratie uit de connection string. Zie voor meer informatie het JSON-voor beeld dat volgt op de tabel en [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als **SecureString** om het veilig op te slaan in azure Data Factory of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| tenant | Geef de Tenant gegevens op, zoals de domein naam of Tenant-ID, waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja, wanneer u Azure AD-verificatie gebruikt met een Service-Principal |
| connectVia | Deze [Integration runtime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als dat niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. | Nee |

Verwijzen respectievelijk naar de volgende secties over de vereisten en JSON-voorbeelden, voor andere verificatietypen:

- [SQL-verificatie](#sql-authentication)
- [Verificatie van Azure AD-toepassings token: Service-Principal](#service-principal-authentication)
- [Verificatie van Azure AD-toepassings tokens: beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Als u op een fout met de fout code UserErrorFailedToConnectToSqlServer en een bericht ziet dat de sessie limiet voor de data base XXX is en is bereikt, voegt u `Pooling=false` toe aan uw connection string en probeert u het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voorbeeld van de gekoppelde service die gebruikmaakt van SQL-verificatie

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Wacht woord in Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Voer de volgende stappen uit om een Azure AD-toepassings token verificatie op basis van een service-principal te gebruiken:

1. [Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) vanuit de Azure Portal. Noteer de naam van de toepassing en de volgende waarden voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. [Richt een Azure Active Directory beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) in voor uw Azure-SQL Server op de Azure portal als u dit nog niet hebt gedaan. De Azure AD-beheerder moet een Azure AD-gebruiker of Azure AD-groep zijn, maar kan geen Service-Principal zijn. Deze stap wordt uitgevoerd. in de volgende stap kunt u een Azure AD-identiteit gebruiken om een Inge sloten database gebruiker voor de service-principal te maken.

3. [Maak Inge sloten database gebruikers](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) voor de Service-Principal. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SQL Server Management Studio, met een Azure AD-identiteit die ten minste een machtiging voor een gebruiker heeft gewijzigd. Voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Verleen de Service-Principal machtigingen die voor SQL-gebruikers of anderen gelden. Voer de volgende code. Zie [dit document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)voor meer opties.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Een Azure SQL Database gekoppelde service configureren in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van de gekoppelde service die gebruikmaakt van verificatie van service-principal

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit voor Azure SQL Database authenticatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data base met behulp van deze identiteit.

Voer de volgende stappen uit om beheerde identiteits verificatie te gebruiken.

1. [Richt een Azure Active Directory beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) in voor uw Azure-SQL Server op de Azure portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep toewijst met beheerde identiteit een beheerdersrol, slaat u stap 3 en 4 over. De beheerder heeft volledige toegang tot de data base.

2. [Inge sloten database gebruikers maken](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) voor de Azure Data Factory beheerde identiteit. Maak verbinding met de data base van of naar waarnaar u gegevens wilt kopiëren met behulp van hulpprogram ma's als SQL Server Management Studio, met een Azure AD-identiteit die ten minste een machtiging voor een gebruiker heeft gewijzigd. Voer de volgende T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Verleen de Data Factory beheerde identiteit de benodigde machtigingen zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code. Zie [dit document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)voor meer opties.

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Een Azure SQL Database gekoppelde service configureren in Azure Data Factory.

**Voorbeeld**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Zie [gegevens sets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

De volgende eigenschappen worden ondersteund voor Azure SQL Database-gegevensset:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **AzureSqlTable**. | Ja |
| schema | De naam van het schema. |Nee voor bron, Ja voor sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, Ja voor sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table`voor nieuwe werk belasting. | Nee voor bron, Ja voor sink |

#### <a name="dataset-properties-example"></a>Voorbeeld van de gegevensset-eigenschappen

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure SQL Database bron en Sink.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database als bron

Als u gegevens wilt kopiëren uit Azure SQL Database, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **AzureSqlSource**. Het type SqlSource wordt nog steeds ondersteund voor compatibiliteit met eerdere versies. | Ja |
| sqlReaderQuery | Deze eigenschap maakt gebruik van de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. | Nee |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure. | Nee |
| storedProcedureParameters | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn de naam of waarde-paren. De namen en het hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. | Nee |

**Punten om te noteren:**

- Als **sqlReaderQuery** is opgegeven voor **AzureSqlSource**, voert de Kopieer activiteit deze query uit op basis van de Azure SQL database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven als voor de opgeslagen procedure para meters worden gebruikt.
- Als u **sqlReaderQuery** of **sqlReaderStoredProcedureName**niet opgeeft, worden de kolommen die zijn gedefinieerd in de sectie ' Structure ' van de JSON van de gegevensset gebruikt voor het maken van een query. De query `select column1, column2 from mytable` worden uitgevoerd op basis van Azure SQL Database. Als de definitie van de gegevensset niet de structuur ' Structure ' bevat, worden alle kolommen geselecteerd in de tabel.

#### <a name="sql-query-example"></a>Voorbeeld van de SQL-query

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database als Sink

> [!TIP]
> Meer informatie over het ondersteunde schrijf gedrag, configuraties en aanbevolen procedures voor het [laden van gegevens in Azure SQL database](#best-practice-for-loading-data-into-azure-sql-database).

Als u gegevens wilt kopiëren naar Azure SQL Database, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van Kopieer activiteit:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **AzureSqlSink**. Het type SqlSink wordt nog steeds ondersteund voor compatibiliteit met eerdere versies. | Ja |
| writeBatchSize | Het aantal rijen dat *per batch*in de SQL-tabel moet worden ingevoegd.<br/> De toegestane waarde is **geheel getal** (aantal rijen). Standaard bepaalt Azure Data Factory dynamisch de juiste Batch grootte op basis van de Rijgrootte. | Nee |
| writeBatchTimeout | De wacht tijd waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt.<br/> De toegestane waarde is **timespan**. Een voor beeld is ' 00:30:00 ' (30 minuten). | Nee |
| preCopyScript | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit voordat u gegevens naar Azure SQL Database schrijft. Het wordt slechts één keer per Kopieer bewerking aangeroepen. Gebruik deze eigenschap voor het opschonen van de vooraf geladen gegevens. | Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die definieert hoe bron gegevens in een doel tabel worden toegepast. <br/>Deze opgeslagen procedure wordt *per batch aangeroepen*. Voor bewerkingen die slechts één keer worden uitgevoerd en niets te doen met bron gegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de eigenschap `preCopyScript`. | Nee |
| storedProcedureTableTypeParameterName |De parameter naam van het tabel type dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType |De naam van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Met de Kopieer activiteit worden de gegevens in een tijdelijke tabel met dit tabel type beschikbaar gemaakt. Met de opgeslagen procedure code kunt u vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam-en waardeparen. Namen en hoofdlettergebruik van parameters moeten overeenkomen met de naam en het hoofdlettergebruik van de opgeslagen-procedureparameters. | Nee |
| tableOption | Hiermee wordt aangegeven of de Sink-tabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bron schema. Het automatisch maken van tabellen wordt niet ondersteund wanneer Sink de opgeslagen procedure opgeeft of een gefaseerde kopie is geconfigureerd in de Kopieer activiteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens, zoals Azure SQL Database Dtu's voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op om het uit te scha kelen. | Nee (standaard is `false`) |

**Voor beeld 1: gegevens toevoegen**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Aanbevolen procedure voor het laden van gegevens in Azure SQL Database

Wanneer u gegevens naar Azure SQL Database kopieert, hebt u mogelijk een ander schrijf gedrag nodig:

- [Toevoegen](#append-data): mijn bron gegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): mijn bron gegevens hebben zowel toevoegingen als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil elke keer een hele dimensie tabel opnieuw laden.
- [Schrijven met aangepaste logica](#write-data-with-custom-logic): Ik heb extra verwerking nodig vóór de laatste invoeging in de doel tabel.

Raadpleeg de respectieve gedeelten over het configureren van Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Het toevoegen van gegevens is het standaard gedrag van deze Azure SQL Database Sink-connector. Azure Data Factory voert een bulksgewijze invoer uit om uw tabel efficiënt te schrijven. U kunt de bron en Sink dienovereenkomstig configureren in de Kopieer activiteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens hebt om te kopiëren, gebruikt u de volgende methode om een upsert te maken: 

- U moet eerst een [Data Base-scoped tijdelijke tabel](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) gebruiken om alle records bulksgewijs te laden met behulp van de Kopieer activiteit. Omdat bewerkingen voor tijdelijke tabellen met een database bereik niet worden geregistreerd, kunt u miljoenen records in enkele seconden laden.
- Voer een opgeslagen procedure-activiteit uit in Azure Data Factory om een instructie [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) of insert/update toe te passen. Gebruik de tijdelijke tabel als bron om alle updates uit te voeren of als één trans actie in te voegen. Op deze manier wordt het aantal Retouren en logboek bewerkingen gereduceerd. Aan het einde van de opgeslagen procedure-activiteit kan de tijdelijke tabel worden afgekapt zodat deze klaar is voor de volgende upsert-cyclus.

In Azure Data Factory kunt u bijvoorbeeld een pijp lijn maken met een **Kopieer activiteit** die is gekoppeld aan een **opgeslagen procedure activiteit**. De eerste kopie kopieert gegevens uit uw bron archief naar een Azure SQL Database tijdelijke tabel, bijvoorbeeld **# #UpsertTempTable**, als de tabel naam in de gegevensset. Vervolgens roept de laatste een opgeslagen procedure aan om bron gegevens van de tijdelijke tabel samen te voegen in de doel tabel en de tijdelijke tabel op te schonen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Definieer in uw data base een opgeslagen procedure met SAMENVOEG logica, zoals in het volgende voor beeld, dat verwijst naar de vorige opgeslagen procedure activiteit. Stel dat het doel de **marketing** tabel is met drie kolommen: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** .

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

**Optie 2:** U kunt er ook voor kiezen om [een opgeslagen procedure binnen de Kopieer activiteit](#invoke-a-stored-procedure-from-a-sql-sink)aan te roepen. Met deze benadering wordt elke rij in de bron tabel uitgevoerd in plaats van bulksgewijs invoegen als de standaard benadering in de Kopieer activiteit, wat niet van toepassing is op grootschalige upsert.

### <a name="overwrite-the-entire-table"></a>De volledige tabel overschrijven

U kunt de eigenschap **preCopyScript** in de Sink van de Kopieer activiteit configureren. In dit geval voert Azure Data Factory voor elke Kopieer activiteit die wordt uitgevoerd eerst het script uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de volledige tabel wilt overschrijven met de meest recente gegevens, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen voor het schrijven van gegevens met aangepaste logica zijn vergelijkbaar met die in de sectie [Upsert-gegevens](#upsert-data) . Wanneer u extra verwerking wilt Toep assen voordat de laatste invoeging van bron gegevens in de doel tabel, op grote schaal, een van de volgende twee dingen doen:

- Laden naar een tijdelijke tabel met data base-scope en vervolgens een opgeslagen procedure aanroepen. 
- Een opgeslagen procedure aanroepen tijdens het kopiëren.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Een opgeslagen procedure aanroepen vanuit een SQL-Sink

Wanneer u gegevens naar Azure SQL Database kopieert, kunt u ook een door de gebruiker opgegeven opgeslagen procedure met aanvullende para meters configureren en aanroepen. De functie opgeslagen procedure maakt gebruik van [para meters met tabel waarden](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Als u een opgeslagen procedure aanroept, wordt de gegevensrij per rij verwerkt in plaats van met behulp van een bulk bewerking. dit wordt niet aanbevolen voor grootschalige kopieën. Meer informatie over [Best practices voor het laden van gegevens in Azure SQL database](#best-practice-for-loading-data-into-azure-sql-database).

U kunt een opgeslagen procedure gebruiken wanneer ingebouwde Kopieer mechanismen het doel niet behouden. Een voor beeld hiervan is wanneer u een extra verwerking wilt Toep assen vóór het uiteindelijke invoegen van bron gegevens in de doel tabel. Sommige extra verwerkings voorbeelden zijn wanneer u kolommen wilt samen voegen, aanvullende waarden wilt opzoeken en in meer dan één tabel wilt invoegen.

In het volgende voor beeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert te maken in een tabel in Azure SQL Database. Stel dat de invoer gegevens en de provinciale **marketing** tabel drie kolommen hebben: **ProfileID**, **State**en **Category**. Voer de upsert uit op basis van de kolom **ProfileID** en pas deze alleen toe voor een specifieke categorie met de naam ProductA.

1. Definieer in uw data base het tabel type met de naam **sqlWriterTableType**. Het schema van het tabel type is hetzelfde als het schema dat wordt geretourneerd door de invoer gegevens.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over de [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.

## <a name="data-type-mapping-for-azure-sql-database"></a>Toewijzing van gegevens type voor Azure SQL Database

Wanneer gegevens worden gekopieerd van of naar Azure SQL Database, worden de volgende toewijzingen gebruikt van Azure SQL Database gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| Azure SQL Database gegevens type | Azure Data Factory tussentijds gegevens type |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleaans |
| char |String, Char[] |
| date |Datum/tijd |
| Datetime |Datum/tijd |
| datetime2 |Datum/tijd |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Enkelvoudig |
| rowversion |Byte[] |
| smalldatetime |Datum/tijd |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| tekst |String, Char[] |
| tijd |TimeSpan |
| tijdstempel |Byte[] |
| tinyint |byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Voor gegevens typen die worden toegewezen aan het type van de tijdelijke decimalen, Azure Data Factory op dit moment de precisie Maxi maal 28 ondersteunen. Als u gegevens hebt die een grotere precisie hebben dan 28, kunt u overwegen om te converteren naar een teken reeks in SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Als u meer wilt weten over de eigenschappen, controleert u de [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md##supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
