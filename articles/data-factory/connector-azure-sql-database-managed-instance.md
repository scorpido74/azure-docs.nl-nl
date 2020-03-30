---
title: Gegevens kopiëren van en naar Azure SQL Database Managed Instance
description: Meer informatie over het verplaatsen van gegevens van en naar Azure SQL Database Managed Instance met Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238742"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure SQL Database Managed Instance met Azure Data Factory

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van en naar Azure SQL Database Managed Instance te kopiëren. Het bouwt voort op het artikel [Overzicht van de activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van de kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure SQL Database Managed Instance-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)

U gegevens uit Azure SQL Database Managed Instance kopiëren naar elk ondersteund sinkdataarchief. U ook gegevens uit elk ondersteund brongegevensarchief naar de beheerde instantie kopiëren. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen en sinks door de kopieeractiviteit.

Met name deze Azure SQL Database Managed Instance-connector ondersteunt:

- Gegevens kopiëren met SQL-verificatie en Azure AD-verificatie (Azure AD) Application token-verificatie met een serviceprincipal of beheerde identiteiten voor Azure-resources.
- Als bron u gegevens ophalen met behulp van een SQL-query of een opgeslagen procedure.
- Als gootsteen, het toevoegen van gegevens aan een doeltabel of het aanroepen van een opgeslagen procedure met aangepaste logica tijdens het kopiëren.

>[!NOTE]
>Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) wordt nu niet ondersteund door deze connector. Om te werken, u een [generieke ODBC-connector](connector-odbc.md) en een SQL Server ODBC-stuurprogramma gebruiken via een self-hosted integratieruntime. Volg [deze richtlijnen](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) met ODBC-stuurprogramma-download- en verbindingstekenreeksconfiguraties.

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot het [openbare eindpunt](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)van het Azure SQL Database Managed Instance, u een Azure Data Factory-beheerde Azure-integratieruntime gebruiken. Zorg ervoor dat u het openbare eindpunt inschakelt en ook openbaar eindpuntverkeer in de netwerkbeveiligingsgroep toestaat, zodat Azure Data Factory verbinding kan maken met uw database. Zie voor meer informatie [deze leidraad.](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)

Als u toegang wilt krijgen tot het privéeindpunt voor Azure SQL Database Managed Instance, stelt u een [zelfgehoste nawerking stoeien van de integratie](create-self-hosted-integration-runtime.md) in die toegang heeft tot de database. Als u de zelf gehoste runtime voor integratie indient in hetzelfde virtuele netwerk als uw beheerde instantie, moet u ervoor zorgen dat uw runtime-machine voor integratie zich in een ander subnet bevindt dan uw beheerde instantie. Als u uw zelf gehoste integratieruntime indient in een ander virtueel netwerk dan uw beheerde instantie, u een virtueel netwerkpeering of een virtueel netwerk gebruiken voor virtuele netwerkverbindingen. Zie [Uw toepassing verbinden met Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om Azure Data Factory-entiteiten te definiëren die specifiek zijn voor de Azure SQL Database Managed Instance-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde azure SQL Database Managed Instance-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureSqlMI**. | Ja |
| Connectionstring |Deze eigenschap geeft de **verbindingsgegevens** op die nodig is om verbinding te maken met de beheerde instantie met SQL-verificatie. Zie de volgende voorbeelden voor meer informatie. <br/>De standaardpoort is 1433. Als u Azure SQL Database Managed Instance gebruikt met een openbaar eindpunt, geeft u poort 3342 expliciet op.<br> U ook een wachtwoord in Azure Key Vault plaatsen. Als het SQL-verificatie is, haalt u de `password` configuratie uit de verbindingstekenreeks. Zie het VOORBEELD JSON na de tabel en [Store-referenties in Azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie. |Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Azure Data Factory of [een verwijzing naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| tenant | Geef de tenantgegevens op, zoals de domeinnaam of tenant-id, waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja, wanneer u Azure AD-verificatie gebruikt met een serviceprincipal |
| connectVia | Deze [integratieruntime](concepts-integration-runtime.md) wordt gebruikt om verbinding te maken met het gegevensarchief. U een runtime voor zelfgehoste integratie of een runtime voor Azure-integratie gebruiken als uw beheerde instantie een openbaar eindpunt heeft en Azure Data Factory toegang geeft tot deze runtime. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Ja |

Raadpleeg voor verschillende verificatietypen respectievelijk de volgende secties over vereisten en JSON-voorbeelden:

- [SQL-verificatie](#sql-authentication)
- [Azure AD-toepassingstokenverificatie: serviceprincipal](#service-principal-authentication)
- [Azure AD-toepassingstokenverificatie: beheerde identiteiten voor Azure-resources](#managed-identity)

### <a name="sql-authentication"></a>SQL-verificatie

**Voorbeeld 1: SQL-verificatie gebruiken**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. Volg de stappen voor [het inrichten van een Azure Active Directory-beheerder voor uw beheerde instantie](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Maak een Azure Active Directory-toepassing](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) vanuit de Azure-portal. Noteer de naam van de toepassing en de volgende waarden die de gekoppelde service definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

3. [Aanmeldingen maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) voor de beheerde identiteit van Azure Data Factory. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde instantie met een SQL Server-account dat een **sysadmin**is. Voer in **hoofddatabase** de volgende T-SQL uit:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Opgenomen databasegebruikers](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) maken voor de beheerde identiteit van Azure Data Factory. Maak verbinding met de database van of waarnaar u gegevens wilt kopiëren, voer de volgende T-SQL uit: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Geef de door Data Factory beheerde identiteit machtigingen toe zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Een gekoppelde Azure SQL Database Managed Instance-service configureren in Azure Data Factory.

**Voorbeeld: serviceprincipal-verificatie gebruiken**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Een gegevensfabriek kan worden gekoppeld aan een [beheerde identiteit voor Azure-resources](data-factory-service-identity.md) die de specifieke gegevensfabriek vertegenwoordigt. U deze beheerde identiteit gebruiken voor Azure SQL Database Managed Instance-verificatie. De aangewezen fabriek kan met deze identiteit toegang krijgen tot gegevens van of naar uw database en deze kopiëren.

Voer deze stappen uit om beheerde identiteitsverificatie te gebruiken.

1. Volg de stappen voor [het inrichten van een Azure Active Directory-beheerder voor uw beheerde instantie](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Aanmeldingen maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) voor de beheerde identiteit van Azure Data Factory. Maak in SQL Server Management Studio (SSMS) verbinding met uw beheerde instantie met een SQL Server-account dat een **sysadmin**is. Voer in **hoofddatabase** de volgende T-SQL uit:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Opgenomen databasegebruikers](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) maken voor de beheerde identiteit van Azure Data Factory. Maak verbinding met de database van of waarnaar u gegevens wilt kopiëren, voer de volgende T-SQL uit: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Geef de door Data Factory beheerde identiteit machtigingen toe zoals u dat normaal doet voor SQL-gebruikers en anderen. Voer de volgende code uit. Zie [dit document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)voor meer opties.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Een gekoppelde Azure SQL Database Managed Instance-service configureren in Azure Data Factory.

**Voorbeeld: gebruikt beheerde identiteitsverificatie**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel gegevenssets voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Azure SQL Database Managed Instance.

Als u gegevens wilt kopiëren van en naar Azure SQL Database Managed Instance, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureSqlMITable**. | Ja |
| schema | Naam van het schema. |Nee voor bron, Ja voor gootsteen  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor gootsteen  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee voor bron, Ja voor gootsteen |

**Voorbeeld**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Azure SQL Database Managed Instance.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance als bron

Als u gegevens wilt kopiëren uit Azure SQL Database Managed Instance, worden de volgende eigenschappen ondersteund in de sectie bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op **SqlMISource**. | Ja |
| sqlReaderQuery |Deze eigenschap gebruikt de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `select * from MyTable`. |Nee |
| sqlReaderStoredProcedureName |Deze eigenschap is de naam van de opgeslagen procedure die gegevens uit de brontabel leest. De laatste SQL-instructie moet een SELECT-instructie in de opgeslagen procedure zijn. |Nee |
| storedProcedureParameters |Deze parameters zijn voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- of waardeparen. De namen en de omhulsel van de parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. |Nee |
| Isolationlevel | Hiermee geeft u het transactievergrendelingsgedrag voor de SQL-bron op. De toegestane waarden zijn: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Raadpleeg [dit document](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) voor meer informatie. | Nee |

**Houd rekening met de volgende punten:**

- Als **sqlReaderQuery** is opgegeven voor **SqlMISource,** voert de kopieeractiviteit deze query uit op de beheerde instantiebron om de gegevens te krijgen. U ook een opgeslagen procedure opgeven door **sqlReaderStoredProcedureName** en **storedProcedureParameters op te** geven als de opgeslagen procedure parameters bevat.
- Als u de eigenschap **sqlReaderQuery** of **sqlReaderStoredProcedureName** niet opgeeft, worden de kolommen die zijn gedefinieerd in de sectie 'structuur' van de gegevensset JSON gebruikt om een query te maken. De `select column1, column2 from mytable` query wordt uitgevoerd ten opzichte van de beheerde instantie. Als de gegevenssetdefinitie geen 'structuur' heeft, worden alle kolommen geselecteerd uit de tabel.

**Voorbeeld: Een SQL-query gebruiken**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance als een sink

> [!TIP]
> Meer informatie over het ondersteunde schrijfgedrag, de configuraties en de aanbevolen procedures van [De beste procedures voor het laden van gegevens in Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Als u gegevens wilt kopiëren naar Azure SQL Database Managed Instance, worden de volgende eigenschappen ondersteund in de sectie logboeken voor kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteitmoet worden ingesteld op **SqlMISink**. | Ja |
| writeBatchSize |Aantal rijen dat *per batch*moet worden ingevoegd in de SQL-tabel .<br/>Toegestane waarden zijn gehele getallen voor het aantal rijen. Azure Data Factory bepaalt standaard dynamisch de juiste batchgrootte op basis van de rijgrootte.  |Nee |
| writeBatchTimeout |Deze eigenschap geeft de wachttijd op voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br/>Toegestane waarden zijn voor de tijdspanne. Een voorbeeld is '00:30:00', dat is 30 minuten. |Nee |
| preCopyScript |Met deze eigenschap wordt een SQL-query opgegeven voor de kopieeractiviteit die moet worden uitgevoerd voordat gegevens naar de beheerde instantie worden geschreven. Het wordt slechts één keer per exemplaar aangeroepen. U deze eigenschap gebruiken om vooraf geladen gegevens op te schonen. |Nee |
| sqlWriterStoredProcedureName | De naam van de opgeslagen procedure die bepaalt hoe brongegevens in een doeltabel kunnen worden toegepast. <br/>Deze opgeslagen procedure wordt *per partij ingeroepen.* Voor bewerkingen die slechts één keer worden uitgevoerd en die niets te maken `preCopyScript` hebben met brongegevens, bijvoorbeeld verwijderen of afkappen, gebruikt u de eigenschap. | Nee |
| storedProcedureTableTypeParameterName |De parameternaam van het tabeltype dat is opgegeven in de opgeslagen procedure.  |Nee |
| sqlWriterTableType sqlWriterTableType |De tabeltypenaam die moet worden gebruikt in de opgeslagen procedure. Met de kopieeractiviteit worden de gegevens die worden verplaatst beschikbaar in een tijdelijke tabel met dit tabeltype. Opgeslagen procedurecode kan vervolgens de gegevens die worden gekopieerd samenvoegen met bestaande gegevens. |Nee |
| storedProcedureParameters |Parameters voor de opgeslagen procedure.<br/>Toegestane waarden zijn naam- en waardeparen. Namen en omhulsel van parameters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedureparameters. | Nee |
| tabelOptie | Hiermee geeft u op of de gootsteentabel automatisch moet worden gemaakt als deze niet bestaat op basis van het bronschema. Het maken van automatische tabel wordt niet ondersteund wanneer de opslagprocedure is opgegeven of gefaseerde kopie is geconfigureerd in kopieeractiviteit. Toegestane waarden zijn: `none` (standaard), `autoCreate`. |Nee |

**Voorbeeld 1: Gegevens toevoegen**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Voorbeeld 2: Een opgeslagen procedure aanroepen tijdens het kopiëren**

Meer informatie van [Een opgeslagen procedure aanroepen vanuit een SQL MI-sink](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Aanbevolen procedures voor het laden van gegevens in Azure SQL Database Managed Instance

Wanneer u gegevens kopieert naar Azure SQL Database Managed Instance, hebt u mogelijk ander schrijfgedrag nodig:

- [Toevoegen](#append-data): Mijn brongegevens hebben alleen nieuwe records.
- [Upsert](#upsert-data): Mijn brongegevens hebben zowel inserts als updates.
- [Overschrijven](#overwrite-the-entire-table): Ik wil elke keer de hele dimensietabel opnieuw laden.
- [Schrijf met aangepaste logica:](#write-data-with-custom-logic)Ik heb extra verwerking nodig voordat de uiteindelijke invoeging in de doeltabel. 

Bekijk de respectievelijke secties voor het configureren in Azure Data Factory en aanbevolen procedures.

### <a name="append-data"></a>Gegevens toevoegen

Toevoegende gegevens is het standaardgedrag van deze Azure SQL Database Managed Instance sink-connector. Azure Data Factory doet een bulkinsert om efficiënt naar uw tabel te schrijven. U de bron configureren en dienovereenkomstig zinken in de kopieeractiviteit.

### <a name="upsert-data"></a>Upsert-gegevens

**Optie 1:** Wanneer u een grote hoeveelheid gegevens moet kopiëren, gebruikt u de volgende benadering om een upsert uit te voeren: 

- Gebruik eerst een [tijdelijke tabel](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) om alle records in bulk te laden met behulp van de kopieeractiviteit. Omdat bewerkingen tegen tijdelijke tabellen niet worden geregistreerd, u miljoenen records in seconden laden.
- Voer een opgeslagen procedureactiviteit uit in Azure Data Factory om een [instructie MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) of INSERT/UPDATE toe te passen. Gebruik de tijdelijke tabel als bron om alle updates of inserts uit te voeren als één transactie. Op deze manier wordt het aantal retourvluchten en logoperaties verminderd. Aan het einde van de opgeslagen procedureactiviteit kan de tijdelijke tabel worden afgekapt om klaar te zijn voor de volgende upsert-cyclus.

Als voorbeeld u in Azure Data Factory een pijplijn maken met een **kopieeractiviteit** die is gekoppeld aan een **activiteit Opgeslagen procedure**. De eerste kopieën van gegevens uit uw bronarchief naar een tijdelijke tabel, bijvoorbeeld **##UpsertTempTable,** als de tabelnaam in de gegevensset. Vervolgens beroept de laatste zich op een opgeslagen procedure om brongegevens uit de tijdelijke tabel samen te voegen in de doeltabel en de tijdelijke tabel op te schonen.

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

**Optie 2:** U er ook voor kiezen om [een opgeslagen procedure in een kopieeractiviteit aan](#invoke-a-stored-procedure-from-a-sql-sink)te roepen. Met deze benadering wordt elke rij in de brontabel uitgevoerd in plaats van bulkinsertals standaardbenadering in de kopieeractiviteit te gebruiken, wat niet geschikt is voor grootschalige upsert.

### <a name="overwrite-the-entire-table"></a>De hele tabel overschrijven

U de eigenschap **preCopyScript** configureren in een kopieeractiviteitsgoot. In dit geval voert Azure Data Factory voor elke kopieeractiviteit die wordt uitgevoerd, het script eerst uit. Vervolgens wordt de kopie uitgevoerd om de gegevens in te voegen. Als u bijvoorbeeld de hele tabel met de meest recente gegevens wilt overschrijven, geeft u een script op om eerst alle records te verwijderen voordat u de nieuwe gegevens uit de bron laadt.

### <a name="write-data-with-custom-logic"></a>Gegevens schrijven met aangepaste logica

De stappen om gegevens met aangepaste logica te schrijven zijn vergelijkbaar met die beschreven in de [sectie Upsert-gegevens.](#upsert-data) Wanneer u extra verwerking moet toepassen voordat brongegevens definitief worden ingevoegd in de doeltabel, u op grote schaal een van de twee dingen doen: 

- Laden naar een tijdelijke tabel en vervolgens een aanroepen van een opgeslagen procedure.
- Beroep doen op een opgeslagen procedure tijdens het kopiëren.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Een opgeslagen procedure aanroepen vanuit een SQL-sink

Wanneer u gegevens kopieert naar Azure SQL Database Managed Instance, u ook een door de gebruiker opgegeven opgeslagen procedure configureren en aanroepen met aanvullende parameters. De functie opgeslagen procedure maakt gebruik van [parameters met tabelwaarde.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> Een beroep doen op een opgeslagen procedure verwerkt de gegevensrij voor rij in plaats van met behulp van een bulkbewerking, die we niet aanbevelen voor grootschalige kopiëren. Meer informatie over [De beste praktijk voor het laden van gegevens in Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

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

3. Definieer in Azure Data Factory de sectie **SQL MI-sink** in de kopieeractiviteit als volgt:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Toewijzing van gegevenstype voor Azure SQL Database Managed Instance

Wanneer gegevens worden gekopieerd van en naar Azure SQL Database Managed Instance, worden de volgende toewijzingen gebruikt van Azure SQL Database Managed Instance-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteitskaarten van het bronschema en het gegevenstype naar de gootsteen worden toegewezen.

| Gegevenstype Azure SQL Database Managed Instance | Interim-gegevenstype Azure Data Factory |
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

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
