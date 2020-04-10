---
title: Gegevens kopiëren en transformeren in Azure SQL Database
description: Meer informatie over het kopiëren van gegevens van en naar Azure SQL Database en het transformeren van gegevens in Azure SQL Database met Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 3a16a8263c80852127ca61db3c666ebf0f7f1e4c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011698"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure SQL Database met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-sql-connector.md)
> * [Huidige versie](connector-azure-sql-database.md)

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure SQL Database te kopiëren en gegevensstroom te gebruiken om gegevens in Azure SQL Database te transformeren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure SQL Database-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrixtabel](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)

Voor Kopieeractiviteit ondersteunt deze Azure SQL Database-connector de volgende functies:

- Gegevens kopiëren met SQL-verificatie en Azure AD-verificatie (Azure AD) Application token-verificatie met een serviceprincipal of beheerde identiteiten voor Azure-resources.
- Als bron u gegevens ophalen met behulp van een SQL-query of een opgeslagen procedure.
- Als gootsteen worden gegevens toegevoegd aan een doeltabel of een aanroepen van een opgeslagen procedure met aangepaste logica tijdens de kopie.

>[!NOTE]
>Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) wordt nu niet ondersteund door deze connector. Om te werken, u een [generieke ODBC-connector](connector-odbc.md) en een SQL Server ODBC-stuurprogramma gebruiken via een self-hosted integratieruntime. Volg [deze richtlijnen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) met ODBC-stuurprogramma-download- en verbindingstekenreeksconfiguraties.

> [!IMPORTANT]
> Als u gegevens kopieert met de runtime van azure data factory-integratie, configureert u een [Azure SQL Server-firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zodat Azure-services toegang hebben tot de server.
> Als u gegevens kopieert met behulp van een zelf gehoste runtime voor integratie, configureert u de Azure SQL Server-firewall om het juiste IP-bereik toe te staan. Dit bereik omvat het IP-adres van de machine dat wordt gebruikt om verbinding te maken met Azure SQL Database.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om Azure Data Factory-entiteiten te definiëren die specifiek zijn voor een Azure SQL Database-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

Deze eigenschappen worden ondersteund voor een azure SQL Database-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet zijn ingesteld op **AzureSqlDatabase**. | Ja |
| Connectionstring | Geef informatie op die nodig is om verbinding te maken met de instantie Azure SQL Database voor de eigenschap **connectionString.** <br/>U ook een wachtwoord- of serviceprincipal-sleutel in Azure Key Vault plaatsen. Als het SQL-verificatie is, haalt u de `password` configuratie uit de verbindingstekenreeks. Zie het VOORBEELD JSON na de tabel en [Store-referenties in Azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie. | Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Azure Data Factory of [een verwijzing naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| tenant | Geef de tenantgegevens op, zoals de domeinnaam of tenant-id, waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| connectVia | Deze [integratieruntime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevensarchief. U de runtime voor Azure-integratie of een zelfgehoste nawerking van de integratie gebruiken als uw gegevensarchief zich in een privénetwerk bevindt. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee |

Raadpleeg voor verschillende verificatietypen respectievelijk de volgende secties over vereisten en JSON-voorbeelden:

- [SQL-verificatie](#sql-authentication)
- [Azure AD-toepassingstokenverificatie: serviceprincipal](#service-principal-authentication)
- [Azure AD-toepassingstokenverificatie: beheerde identiteiten voor Azure-resources](#managed-identity)

>[!TIP]
>Als u een fout hebt opgetreden met de foutcode 'UserErrorFailedToConnectToSqlServer' en een bericht als `Pooling=false` 'De sessielimiet voor de database is XXX en is bereikt', voeg dan toe aan uw verbindingstekenreeks en probeer het opnieuw.

### <a name="sql-authentication"></a>SQL-verificatie

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Voorbeeld van gekoppelde service dat SQL-verificatie gebruikt

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Wachtwoord in Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Voer de volgende stappen uit om een azure AD-toepassingstokenverificatie op basis van serviceprincipal te gebruiken:

1. [Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) vanuit de Azure-portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. [Inrichten van een Azure Active Directory-beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) voor uw Azure SQL Server op de Azure-portal als u dit nog niet hebt gedaan. De Azure AD-beheerder moet een Azure AD-gebruiker of Azure AD-groep zijn, maar het kan geen serviceprincipal zijn. Deze stap wordt uitgevoerd zodat u in de volgende stap een Azure AD-identiteit gebruiken om een opgenomen databasegebruiker voor de serviceprincipal te maken.

3. Voor de [serviceprincipal maken van opgenomen databasegebruikers.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) Maak verbinding met de database van of waarnaar u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SQL Server Management Studio, met een Azure AD-identiteit die ten minste ELKE GEBRUIKERSmachtiging wijzigt. Voer de volgende T-SQL uit: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Geef de serviceprincipal machtigingen toe zoals u dat normaal doet voor SQL-gebruikers of anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)voor meer opties.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Een azure SQL Database-gekoppelde service configureren in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Voorbeeld van gekoppelde service dat serviceprincipal-verificatie gebruikt

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke gegevensfabriek vertegenwoordigt. U deze beheerde identiteit gebruiken voor Azure SQL Database-verificatie. De aangewezen fabriek kan met deze identiteit toegang krijgen tot gegevens van of naar uw database en deze kopiëren.

Voer deze stappen uit om beheerde identiteitsverificatie te gebruiken.

1. [Inrichten van een Azure Active Directory-beheerder](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) voor uw Azure SQL Server op de Azure-portal als u dit nog niet hebt gedaan. De Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Als u de groep met beheerde identiteit een beheerdersrol verleent, slaat u de stappen 3 en 4 over. De beheerder heeft volledige toegang tot de database.

2. [Opgenomen databasegebruikers](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) maken voor de beheerde identiteit van Azure Data Factory. Maak verbinding met de database van of waarnaar u gegevens wilt kopiëren met behulp van hulpprogramma's zoals SQL Server Management Studio, met een Azure AD-identiteit die ten minste ELKE GEBRUIKERSmachtiging wijzigt. Voer de volgende T-SQL uit: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Geef de door Data Factory beheerde identiteit machtigingen toe zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)voor meer opties.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Een azure SQL Database-gekoppelde service configureren in Azure Data Factory.

**Voorbeeld**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Zie [Gegevenssets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn om gegevenssets te definiëren. 

De volgende eigenschappen worden ondersteund voor de azure SQL Database-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **AzureSqlTable**. | Ja |
| schema | Naam van het schema. |Nee voor bron, Ja voor gootsteen  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor gootsteen  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee voor bron, Ja voor gootsteen |

#### <a name="dataset-properties-example"></a>Voorbeeld van gegevensseteigenschappen

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

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Azure SQL Database.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL-database als bron

Als u gegevens uit Azure SQL Database wilt kopiëren, worden de volgende eigenschappen ondersteund in de sectie **kopieeractiviteitsbron:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de kopieeractiviteitsbron moet zijn ingesteld op **AzureSqlSource**. Het type SqlSource wordt nog steeds ondersteund voor achterwaartse compatibiliteit. | Ja |
| sqlReaderQuery | Deze eigenschap gebruikt de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. | Nee |
| sqlReaderStoredProcedureName | De naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. | Nee |
| storedProcedureParameters | Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- of waardeparen. De namen en de omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. | Nee |
| Isolationlevel | Hiermee geeft u het transactievergrendelingsgedrag voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |

**Aandachtspunten:**

- Als **sqlReaderQuery** is opgegeven voor **AzureSqlSource,** voert de kopieeractiviteit deze query uit op de Azure SQL Database-bron om de gegevens op te halen. U ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters op te** geven als de opgeslagen procedure parameters bevat.
- Als u **sqlReaderQuery** of **sqlReaderStoredProcedureName**niet opgeeft, worden de kolommen die zijn gedefinieerd in de sectie 'structuur' van de gegevensset JSON gebruikt om een query te construeren. De `select column1, column2 from mytable` query wordt uitgevoerd tegen Azure SQL Database. Als de gegevenssetdefinitie geen 'structuur' heeft, worden alle kolommen geselecteerd uit de tabel.

#### <a name="sql-query-example"></a>Voorbeeld van SQL-query

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

#### <a name="stored-procedure-example"></a>Voorbeeld van een opgeslagen procedure

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

### <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedures

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL-database als sink

> [!TIP]
> Meer informatie over het ondersteunde schrijfgedrag, configuraties en aanbevolen procedures van [Best practices voor het laden van gegevens in Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Als u gegevens wilt kopiëren naar Azure SQL Database, worden de volgende eigenschappen ondersteund in de sectie **logboeken voor kopieeractiviteit:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de inhoudsgroep voor kopiëren moet worden ingesteld op **AzureSqlSink**. "SqlSink"-type wordt nog steeds ondersteund voor achterwaartse compatibiliteit. | Ja |
| writeBatchSize | Aantal rijen dat *per batch*moet worden ingevoegd in de SQL-tabel .<br/> De toegestane waarde is **integer** (aantal rijen). Azure Data Factory bepaalt standaard dynamisch de juiste batchgrootte op basis van de rijgrootte. | Nee |
| writeBatchTimeout | De wachttijd voor de batch invoegen bewerking te voltooien voordat het time-out.<br/> De toegestane waarde is **tijdspanne.** Een voorbeeld is '00:30:00' (30 minuten). | Nee |
| preCopyScript | Geef een SQL-query op voor de kopieeractiviteit die moet worden uitgevoerd voordat gegevens in Azure SQL Database worden geschreven. Het wordt slechts één keer per exemplaar aangeroepen. Gebruik deze eigenschap om de vooraf geladen gegevens op te schonen. | Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die bepaalt hoe brongegevens in een doeltabel kunnen worden toegepast. <br/>Deze opgeslagen procedure wordt *per partij ingeroepen.* Voor bewerkingen die slechts één keer worden uitgevoerd en die niets te maken `preCopyScript` hebben met brongegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de eigenschap. | Nee |
| storedProcedureTableTypeParameterName |De parameternaam van het tabeltype dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType sqlWriterTableType |De tabeltypenaam die moet worden gebruikt in de opgeslagen procedure. Met de kopieeractiviteit worden de gegevens die worden verplaatst beschikbaar in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- en waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. | Nee |
| tabelOptie | Hiermee geeft u op of de gootsteentabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bronschema. Het maken van automatische tabel wordt niet ondersteund wanneer de opslagprocedure is opgegeven of gefaseerde kopie is geconfigureerd in kopieeractiviteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |
| disableMetricsCollection disableMetricsCollection disableMetricsCollection disableMetrics | Data Factory verzamelt statistieken zoals Azure SQL Database DTUs voor optimalisatie van kopieerprestaties en aanbevelingen. Als u zich met dit `true` gedrag bezighoudt, geeft u op om het uit te schakelen. | Nee (standaard `false`is) |

**Voorbeeld 1: Gegevens toevoegen**

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

**Voorbeeld 2: Een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie van [Een opgeslagen procedure aanroepen vanuit een SQL-sink](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Aanbevolen procedures voor het laden van gegevens in Azure SQL Database

Wanneer u gegevens kopieert naar Azure SQL Database, hebt u mogelijk ander schrijfgedrag nodig:

- [Toevoegen](#append-data): Mijn brongegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): Mijn brongegevens hebben zowel inserts als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil elke keer een hele dimensietabel herladen.
- [Schrijf met aangepaste logica:](#write-data-with-custom-logic)Ik heb extra verwerking nodig voordat de uiteindelijke invoeging in de doeltabel.

Raadpleeg de respectievelijke secties over configureren in Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Toevoegenvan gegevens is het standaardgedrag van deze Azure SQL Database sink connector. Azure Data Factory doet een bulkinsert om efficiënt naar uw tabel te schrijven. U de bron configureren en dienovereenkomstig zinken in de kopieeractiviteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens moet kopiëren, gebruikt u de volgende benadering om een upsert uit te voeren: 

- Gebruik eerst een [tijdelijke databasetabel](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) met bereik om alle records in bulk te laden met behulp van de kopieeractiviteit. Omdat bewerkingen tegen tijdelijke tabellen met databasebereik niet worden geregistreerd, u miljoenen records in seconden laden.
- Voer een opgeslagen procedureactiviteit uit in Azure Data Factory om een [instructie MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) of INSERT/UPDATE toe te passen. Gebruik de tijdelijke tabel als bron om alle updates of inserts uit te voeren als één transactie. Op deze manier wordt het aantal retourvluchten en logoperaties verminderd. Aan het einde van de opgeslagen procedureactiviteit kan de tijdelijke tabel worden afgekapt om klaar te zijn voor de volgende upsert-cyclus.

Als voorbeeld u in Azure Data Factory een pijplijn maken met een **kopieeractiviteit** die is gekoppeld aan een **activiteit Opgeslagen procedure**. De eerste kopieert gegevens uit uw bronarchief naar een tijdelijke tabel van Azure SQL Database, bijvoorbeeld **##UpsertTempTable**, als de tabelnaam in de gegevensset. Vervolgens beroept de laatste zich op een opgeslagen procedure om brongegevens uit de tijdelijke tabel samen te voegen in de doeltabel en de tijdelijke tabel op te schonen.

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

**Optie 2:** U er ook voor kiezen om [een opgeslagen procedure in de kopieeractiviteit aan](#invoke-a-stored-procedure-from-a-sql-sink)te roepen. Met deze benadering wordt elke batch `writeBatchSize` (zoals geregeld door de eigenschap) uitgevoerd in de brontabel in plaats van bulkinvoeging te gebruiken als de standaardbenadering in de kopieeractiviteit.

### <a name="overwrite-the-entire-table"></a>De hele tabel overschrijven

U de eigenschap **preCopyScript** configureren in de inhoudslijst voor kopiëren. In dit geval voert Azure Data Factory voor elke kopieeractiviteit die wordt uitgevoerd, het script eerst uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de hele tabel met de meest recente gegevens wilt overschrijven, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen om gegevens met aangepaste logica te schrijven zijn vergelijkbaar met die beschreven in de [sectie Upsert-gegevens.](#upsert-data) Wanneer u extra verwerking moet toepassen voordat brongegevens definitief worden ingevoegd in de doeltabel, u op grote schaal een van de twee dingen doen:

- Laden naar een tijdelijke tabel met databasebereik en vervolgens een opgeslagen procedure aanroepen. 
- Beroep doen op een opgeslagen procedure tijdens het kopiëren.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Een opgeslagen procedure aanroepen vanuit een SQL-sink

Wanneer u gegevens kopieert naar Azure SQL Database, u ook een door de gebruiker opgegeven opgeslagen procedure configureren en aanroepen met extra parameters op elke batch van de brontabel. De functie opgeslagen procedure maakt gebruik van [parameters met tabelwaarde.](https://msdn.microsoft.com/library/bb675163.aspx)

U een opgeslagen procedure gebruiken wanneer ingebouwde kopieermechanismen het doel niet dienen. Een voorbeeld hiervan is wanneer u extra verwerking wilt toepassen voordat brongegevens definitief worden ingevoegd in de doeltabel. Enkele extra voorbeelden van verwerking zijn wanneer u kolommen wilt samenvoegen, extra waarden wilt opzoeken en in meer dan één tabel wilt invoegen.

In het volgende voorbeeld ziet u hoe u een opgeslagen procedure gebruikt om een upsert uit te voeren in een tabel in Azure SQL Database. Stel dat de invoergegevens en de tabel **Sink Marketing** elk drie kolommen hebben: **ProfileID**, **Status**en **Categorie**. Doe de upsert op basis van de **ProfileID** kolom, en alleen toe te passen voor een specifieke categorie genaamd "ProductA".

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

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in de toewijzingsgegevensstroom transformeert, u lezen en schrijven naar tabellen vanuit Azure SQL Database. Zie voor meer informatie de [brontransformatie](data-flow-source.md) en [sinktransformatie](data-flow-sink.md) in kaartgegevensstromen.

### <a name="source-transformation"></a>Brontransformatie

Instellingen die specifiek zijn voor Azure SQL Database zijn beschikbaar op het tabblad **Bronopties** van de brontransformatie. 

**Invoer:** Selecteer of u uw bron op ```Select * from <table-name>```een tabel (equivalent van) richt of een aangepaste SQL-query invoert.

**Query:** Als u Query selecteert in het invoerveld, voert u een SQL-query in voor uw bron. Met deze instelling wordt elke tabel die u in de gegevensset hebt gekozen, overschreven. **Order** By-clausules worden hier niet ondersteund, maar u wel een volledige SELECT FROM-instructie instellen. U ook door de gebruiker gedefinieerde tabelfuncties gebruiken. **selecteer * uit udfGetData()** is een UDF in SQL die een tabel retourneert. Deze query produceert een brontabel die u gebruiken in uw gegevensstroom. Het gebruik van query's is ook een geweldige manier om rijen te verminderen voor het testen of voor zoekopdrachten. 

* SQL-voorbeeld:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchgrootte:** voer een batchgrootte in om grote gegevens in reads te splitsen.

**Isolatieniveau:** de standaardwaarde voor SQL-bronnen in de toewijzingsgegevensstroom wordt niet-vastgelegd gelezen. U het isolatieniveau hier wijzigen in een van deze waarden:
* Waarvoor u zich inzet
* Niet-vastgelegd lezen
* Herhaalbaar lezen
* Serializable
* None

![Isolatieniveau](media/data-flow/isolationlevel.png "Isolatieniveau")

### <a name="sink-transformation"></a>Gootsteentransformatie

Instellingen die specifiek zijn voor Azure SQL Database zijn beschikbaar op het tabblad **Instellingen** van de sinktransformatie.

**Updatemethode:** Hiermee bepaalt u welke bewerkingen zijn toegestaan op uw databasebestemming. De standaardinstelling is om alleen inserts toe te staan. Als u rijen wilt bijwerken, upsert of verwijdert, is een transformatie met een wijzigingsrij vereist om rijen voor die acties te taggen. Voor updates, upserts en deletes moet een sleutelkolom of kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

![Belangrijke kolommen](media/data-flow/keycolumn.png "Belangrijke kolommen")

De kolomnaam die u hier als sleutel kiest, wordt door ADF gebruikt als onderdeel van de volgende update, upsert, delete. Daarom moet u een kolom kiezen die bestaat in de sinkmapping. Als u de waarde niet naar deze toetskolom wilt schrijven, klikt u op 'Schrijfsleutelkolommen overslaan'.

**Tabelactie:** Hiermee bepaalt u of u alle rijen uit de doeltabel opnieuw wilt maken of verwijderen voordat u gaat schrijven.
* Geen: Er wordt geen actie ondernomen aan de tafel.
* Opnieuw maken: de tabel wordt gedropt en opnieuw gemaakt. Vereist als u dynamisch een nieuwe tabel maakt.
* Afgekapt: alle rijen uit de doeltabel worden verwijderd.

**Batchgrootte:** hiermee bepaalt u hoeveel rijen er in elke bucket worden geschreven. Grotere batchformaten verbeteren compressie en geheugenoptimalisatie, maar lopen risico op uitzonderingen met het geheugen bij het incacheren van gegevens.

**Pre- en Post SQL-scripts**: Voer multiline SQL-scripts in die worden uitgevoerd vóór (pre-processing) en na (post-processing) gegevens worden geschreven naar uw Sink-database

![pre- en postSQL-verwerkingsscripts](media/data-flow/prepost1.png "SQL-verwerkingsscripts")

## <a name="data-type-mapping-for-azure-sql-database"></a>Toewijzing van gegevenstype voor Azure SQL-database

Wanneer gegevens worden gekopieerd van of naar Azure SQL Database, worden de volgende toewijzingen gebruikt van Azure SQL Database-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| Gegevenstype Azure SQL Database | Interim-gegevenstype Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary varbinary |Byte |
| varchar |Tekenreeks, Char[] |
| xml |Xml |

>[!NOTE]
> Voor gegevenstypen die worden toegewezen aan het tussentijdse type Decimal, ondersteunt Azure Data Factory momenteel precisie tot 28. Als u gegevens hebt met een precisie die groter is dan 28, u overwegen om te converteren naar een tekenreeks in SQL-query.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Activiteitseigenschappen getMetadata

Voor meer informatie over de eigenschappen, controleert [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
