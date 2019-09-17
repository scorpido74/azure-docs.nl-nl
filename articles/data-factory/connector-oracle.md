---
title: Gegevens kopiëren van en naar Oracle met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens van ondersteunde bron archieven naar een Oracle-data base of van Oracle naar ondersteunde Sink-archieven, met behulp van Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 318014ec10bda0fa0ead9787067bb30f57707930
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008579"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Oracle met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-oracle-connector.md)
> * [Huidige versie](connector-oracle.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar een Oracle-Data Base te kopiëren. Het is gebaseerd op het overzicht van de [Kopieer activiteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Oracle-Connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een Oracle-data base kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar een Oracle-data base. Zie voor een lijst met gegevensarchieven die worden ondersteund als gegevensbronnen of PUT voor de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Deze Oracle-connector ondersteunt met name:

- De volgende versies van een Oracle-Data Base:
    - Oracle 18c R1 (18,1) en hoger
    - Oracle 12c R1 (12,1) en hoger
    - Oracle 11g R1 (11,1) en hoger
    - Oracle 10g R1 (10,1) en hoger
    - Oracle 9i R2 (9,2) en hoger
    - Oracle 8i R3 (8.1.7) en hoger
    - Oracle Database Cloud Exadata-service
- Kopiëren van gegevens met behulp van basis-of OID-verificaties.
- Parallelle kopieën van een Oracle-bron. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor meer informatie.

> [!Note]
> Oracle-proxy server wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integration runtime biedt een ingebouwd Oracle-stuur programma. Daarom hoeft u niet hand matig een stuur programma te installeren wanneer u gegevens kopieert van en naar Oracle.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Oracle-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De gekoppelde Oracle-Service ondersteunt de volgende eigenschappen:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op **Oracle**. | Ja |
| connectionString | Hiermee geeft u de gegevens op die nodig zijn om verbinding te maken met het Oracle Database-exemplaar. <br/>Markeer dit veld als een `SecureString` om het veilig op te slaan in Data Factory. U kunt ook een wacht woord in azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. <br><br>**Ondersteund verbindings type**: U kunt de **Oracle-sid** of de **Oracle-Service naam** gebruiken om uw data base te identificeren:<br>-Als u SID gebruikt:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Als u de service naam gebruikt:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Ja |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Indien niet opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

>[!TIP]
>Als er een fout bericht wordt weer geven, ' ORA-01025: De UPI-para meter valt buiten het bereik, en uw Oracle-versie `WireProtocolMode=1` is 8i, Voeg aan uw connection string toe. Probeer het opnieuw.

Als u versleuteling wilt inschakelen voor de Oracle-verbinding, hebt u twee opties:

-   Als u **Triple-des Encryption (3DES) en Advanced Encryption Standard (AES)** wilt gebruiken, gaat u naar Oracle Advanced Security (OAS) en configureert u de versleutelings instellingen op de Oracle-server. Zie deze [Oracle-documentatie](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)voor meer informatie. De Oracle Application Development Framework-connector (ADF) onderhandelt automatisch de versleutelings methode voor het gebruik van het account dat u configureert in OAS bij het tot stand brengen van een verbinding met Oracle.

-   **SSL**gebruiken:

    1.  De SSL-certificaat gegevens ophalen. Haal de Distinguished Encoding Rules (DER) gecodeerde certificaat gegevens van uw SSL-certificaat op en sla de uitvoer op (-----begin certificaat... -----Van het eind certificaat) als een tekst bestand.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Voorbeeld:** Haal certificaat gegevens uit DERcert. CER op en sla de uitvoer op in cert. txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Bouw de `keystore` of `truststore`op. Met de volgende opdracht maakt `truststore` u het bestand met of zonder wacht woord in PKCS-12-indeling.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Voorbeeld:** Maak een pkcs12/pfx-profiel `truststore` -bestand met de naam MyTrustStoreFile met een wacht woord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Plaats het `truststore` bestand op de zelf-hostende IR-computer. Plaats bijvoorbeeld het bestand op C:\MyTrustStoreFile.
    4.  Configureer in azure Data Factory de Oracle-Connection String met `EncryptionMethod=1` en de bijbehorende `TrustStore` / `TrustStorePassword`waarde. Bijvoorbeeld `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Voorbeeld:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: wacht woord opslaan in Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
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
## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Deze sectie bevat een lijst met eigenschappen die door de Oracle-gegevensset worden ondersteund. Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren van en naar Oracle, stelt u de eigenschap type van `OracleTable`de gegevensset in op. De volgende eigenschappen worden ondersteund.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de DataSet moet worden ingesteld op `OracleTable`. | Ja |
| Schema | De naam van het schema. |Nee voor bron, Ja voor sink  |
| table | De naam van de tabel/weer gave. |Nee voor bron, Ja voor sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en`table`voor nieuwe werk belasting. | Nee voor bron, Ja voor sink |

**Voorbeeld:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Oracle-bron en Sink. Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle als bron

>[!TIP]
>Als u gegevens van Oracle efficiënt wilt laden met behulp van gegevens partitioneren, kunt u meer informatie uit een [parallelle kopie van Oracle](#parallel-copy-from-oracle)gebruiken.

Als u gegevens van Oracle wilt kopiëren, stelt u het bron type in de `OracleSource`Kopieer activiteit in op. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden `OracleSource`ingesteld op. | Ja |
| oracleReaderQuery | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u gepartitioneerde belasting inschakelt, moet u alle bijbehorende ingebouwde partitie parameters in uw query koppelen. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor voor beelden. | Nee |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens van Oracle. <br>Toegestane waarden zijn: **Geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, `None`niet), is de mate van parallelle uitvoering om gegevens uit een Oracle-data base gelijktijdig te laden, [`parallelCopies`](copy-activity-performance.md#parallel-copy) bepaald door de instelling in de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen wanneer de partitie optie `None`niet is. | Nee |
| partitionNames | De lijst met fysieke partities die moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie `PhysicalPartitionsOfTable`is. Als u een query gebruikt om de bron gegevens op te halen `?AdfTabularPartitionName` , Hook in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | Nee |
| partitionColumnName | Geef de naam op van de bron kolom **in een geheel getal** dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als partitie kolom. <br>Toep assen wanneer de partitie optie `DynamicRange`is. Als u een query gebruikt om de bron gegevens op te halen `?AdfRangePartitionColumnName` , Hook in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie `DynamicRange`is. Als u een query gebruikt om de bron gegevens op te halen `?AdfRangePartitionUpbound` , Hook in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie `DynamicRange`is. Als u een query gebruikt om de bron gegevens op te halen `?AdfRangePartitionLowbound` , Hook in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | Nee |

**Voor beeld: gegevens kopiëren met behulp van een basis query zonder partitie**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle als Sink

Als u gegevens wilt kopiëren naar Oracle, stelt u het sink-type in `OracleSink`de Kopieer activiteit in op. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld `OracleSink`op. | Ja |
| writeBatchSize | Voegt gegevens in de SQL-tabel in wanneer de buffer `writeBatchSize`grootte bereikt.<br/>Toegestane waarden zijn integer (aantal rijen). |Nee (de standaard waarde is 10.000) |
| writeBatchTimeout | De wacht tijd voor het volt ooien van de batch INSERT-bewerking voordat er een time-out optreedt.<br/>Toegestane waarden zijn time span. Een voor beeld is 00:30:00 (30 minuten). | Nee |
| preCopyScript | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar Oracle worden geschreven. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Parallelle kopie van Oracle

De Data Factory Oracle-Connector biedt ingebouwde gegevenspartitionering voor het parallel kopiëren van gegevens van Oracle. U kunt opties voor gegevens partities vinden op het tabblad **bron** van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-oracle/connector-oracle-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op de Oracle-bron om gegevens te laden per partitie. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit de Oracle-data base.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Oracle-data base laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Voorgestelde instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel met fysieke partities.          | **Partitie optie**: Fysieke partities van tabel. <br><br/>Tijdens de uitvoering worden de fysieke partities automatisch door Data Factory gedetecteerd en worden de gegevens gekopieerd op partities. |
| Volledige belasting van een grote tabel, zonder fysieke partities, met een kolom met gehele getallen voor het partitioneren van gegevens. | **Partitie opties**: Partitie met dynamisch bereik.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als u niets opgeeft, wordt de kolom primaire sleutel gebruikt. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met fysieke partities. | **Partitie optie**: Fysieke partities van tabel.<br>**Query**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Partitie naam**: Geef de partitie naam (en) op waaruit de gegevens moeten worden gekopieerd. Als deze niet wordt opgegeven, detecteert Data Factory automatisch de fysieke partities op de tabel die u in de Oracle-gegevensset hebt opgegeven.<br><br>Tijdens de uitvoering Data Factory vervangen `?AdfTabularPartitionName` door de daad werkelijke partitie naam en verzonden naar Oracle. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl een kolom integer voor partitionering van gegevens is. | **Partitie opties**: Partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U kunt de kolom met het gegevens type geheel getal partitioneren.<br>**Bovengrens van partities** en **kleinere partities**: Geef op of u wilt filteren op partitie kolom om alleen gegevens op te halen tussen het onderste en het bovenliggende bereik.<br><br>Tijdens de uitvoering Data Factory vervangt `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, en `?AdfRangePartitionLowbound` met de werkelijke kolom naam en waardeparen voor elke partitie, en verzonden naar Oracle. <br>Als uw partitie kolom "ID" bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met een parallelle kopie ingesteld als 4, Data Factory worden gegevens opgehaald met vier partities. Hun Id's liggen respectievelijk tussen [1, 20], [21, 40], [41, 60] en [61, 80]. |

**Voor beeld: query met fysieke partitie**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Toewijzing van gegevens type voor Oracle

Wanneer u gegevens van en naar Oracle kopieert, zijn de volgende toewijzingen van toepassing. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Oracle-gegevens type | Data Factory tussentijdse gegevenstype |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(alleen ondersteund op Oracle 10g en hoger) |
| CHAR |Tekenreeks |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (als precisie > 28) |
| INTEGER |Decimal, String (als precisie > 28) |
| OMVANG |Tekenreeks |
| LONG RAW |Byte[] |
| NCHAR |Tekenreeks |
| NCLOB |Tekenreeks |
| NUMBER |Decimal, String (als precisie > 28) |
| NVARCHAR2 |Tekenreeks |
| RAW |Byte[] |
| ROWID |Tekenreeks |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Tekenreeks |
| TIMESTAMP WITH TIME ZONE |Tekenreeks |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Tekenreeks |
| XML |Tekenreeks |

> [!NOTE]
> De gegevens typen INTERVAL jaar tot maand en INTERVAL dag tot seconde worden niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
