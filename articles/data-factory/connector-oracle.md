---
title: Gegevens van en naar Oracle kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit ondersteunde bronwinkels naar een Oracle-database of van Oracle naar ondersteunde sinkstores met Behulp van Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: d37a9bd4cc29ee60f9833ffbcb5a2701a19bbaa7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416829"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Gegevens van en naar Oracle kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-onprem-oracle-connector.md)
> * [Huidige versie](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van en naar een Oracle-database te kopiëren. Het bouwt voort op het [overzicht van de kopieeractiviteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Oracle-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit een Oracle-database kopiëren naar elk ondersteund sinkdataarchief. U ook gegevens uit elk ondersteund brongegevensarchief kopiëren naar een Oracle-database. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Met name deze Oracle-connector ondersteunt:

- De volgende versies van een Oracle-database:
    - Oracle 19c R1 (19.1) en hoger
    - Oracle 18c R1 (18,1) en hoger
    - Oracle 12c R1 (12.1) en hoger
    - Oracle 11g R1 (11.1) en hoger
    - Oracle 10g R1 (10.1) en hoger
    - Oracle 9i R2 (9.2) en hoger
    - Oracle 8i R3 (8.1.7) en hoger
    - Oracle Database Cloud Exadata-service
- Parallel kopiëren vanuit een Oracle-bron. Zie de [sectie Parallelle kopie van Oracle](#parallel-copy-from-oracle) voor meer informatie.

> [!Note]
> Oracle-proxyserver wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

De ingebouwde runtime voor integratie biedt een ingebouwde Oracle-driver. Daarom hoeft u een stuurprogramma niet handmatig te installeren wanneer u gegevens van en naar Oracle kopieert.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Oracle-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De Oracle-gekoppelde service ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Oracle**. | Ja |
| Connectionstring | Hiermee geeft u de informatie op die nodig is om verbinding te maken met de instantie Oracle Database. <br/>U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details. <br><br>**Ondersteund verbindingstype:** u **Oracle SID** of Oracle **Service Name** gebruiken om uw database te identificeren:<br>- Als u SID gebruikt:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Als u servicenaam gebruikt:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Voor geavanceerde oracle native verbindingsopties u ervoor kiezen om een item toe te voegen aan [TNSNAMES. ORA-bestand](http://www.orafaq.com/wiki/Tnsnames.ora) op de Oracle-server en in adf Oracle-gekoppelde service, kies ervoor om het type Oracle Service Name-verbinding te gebruiken en de bijbehorende servicenaam te configureren. | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!TIP]
>Als er een fout optreedt met 'ORA-01025: UPI-parameter buiten bereik' `WireProtocolMode=1` en uw Oracle-versie 8i is, voegt u deze toe aan uw verbindingstekenreeks. Probeer het dan opnieuw.

Meer verbindingseigenschappen die u per aanvraag instellen in verbindingstekenreeks:

| Eigenschap | Beschrijving | Toegestane waarden |
|:--- |:--- |:--- |
| ArraySize (ArraySize) |Het aantal bytes dat de connector kan ophalen in één netwerkretour. B.v., `ArraySize=‭10485760‬`.<br/><br/>Grotere waarden verhogen de doorvoer door het aantal keren dat gegevens in het netwerk moeten worden opgehaald, te verminderen. Kleinere waarden verhogen de responstijd, omdat er minder vertraging is in afwachting van het verzenden van gegevens door de server. | Een geheel getal van 1 tot 4294967296 (4 GB). Standaardwaarde `60000`is . De waarde 1 definieert niet het aantal bytes, maar geeft de toewijzing van ruimte voor precies één rij gegevens aan. |

Als u versleuteling op de Verbinding van Oracle wilt inschakelen, hebt u twee opties:

-   Als u **Triple-DES Encryption (3DES) en Advanced Encryption Standard (AES)** wilt gebruiken, gaat u aan de kant van de Oracle-server naar Oracle Advanced Security (OAS) en configureert u de versleutelingsinstellingen. Zie deze [Oracle-documentatie](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)voor meer informatie. De ADF-connector (Oracle Application Development Framework) onderhandelt automatisch over de versleutelingsmethode om de connector te gebruiken die u in OAS configureert bij het tot stand brengen van een verbinding met Oracle.

-   Tls **gebruiken:**

    1.  Download de TLS/SSL-certificaatgegevens. Download de certificaatgegevens van je TLS/SSL-certificaat (Distinguished Encoding Rules) en sla de uitvoer op (----- Start Certificaat ... Certificaat -----) beëindigen als tekstbestand.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Voorbeeld:** Haal cert-informatie uit DERcert.cer en sla de uitvoer op cert.txt.

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
    
    2.  Bouw `keystore` de `truststore`of . Met de volgende `truststore` opdracht wordt het bestand, met of zonder wachtwoord, gemaakt in pkcs-12-indeling.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Voorbeeld:** Maak een PKCS12-bestand, `truststore` genaamd MyTrustStoreFile, met een wachtwoord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Plaats `truststore` het bestand op de zelf gehoste IR-machine. Plaats het bestand bijvoorbeeld op C:\MyTrustStoreFile.
    4.  Configureer in Azure Data Factory `EncryptionMethod=1` de Oracle-verbindingstekenreeks met en de bijbehorende `TrustStore` / `TrustStorePassword`waarde. Bijvoorbeeld `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Voorbeeld:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: wachtwoord opslaan in Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Oracle-gegevensset. Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

Als u gegevens van en naar Oracle wilt `OracleTable`kopiëren, stelt u de eigenschap type van de gegevensset in op . De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de `OracleTable`gegevensset moet worden ingesteld op . | Ja |
| schema | Naam van het schema. |Nee voor bron, Ja voor gootsteen  |
| tabel | Naam van de tabel/weergave. |Nee voor bron, Ja voor gootsteen  |
| tableName | Naam van de tabel/weergave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Voor nieuwe werkbelasting, gebruik `schema` en `table`. | Nee voor bron, Ja voor gootsteen |

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

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van Oracle. Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="oracle-as-source"></a>Oracle als bron

>[!TIP]
>Als u gegevens van Oracle efficiënt wilt laden door gegevenspartitionering te gebruiken, leest u meer uit [parallelle kopie van Oracle](#parallel-copy-from-oracle).

Als u gegevens van Oracle wilt kopiëren, `OracleSource`stelt u het brontype in de kopieeractiviteit in op . De volgende eigenschappen worden ondersteund in de **sectie** bron van kopieeractiviteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron `OracleSource`van de kopieeractiviteit moet worden ingesteld op . | Ja |
| oracleReaderQuery | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u partitioned load inschakelt, moet u alle bijbehorende ingebouwde partitieparameters in uw query aansluiten. Zie bijvoorbeeld de [sectie Parallelle kopie van Oracle.](#parallel-copy-from-oracle) | Nee |
| partitieOpties | Hiermee geeft u de opties voor gegevenspartitionering op die worden gebruikt om gegevens van Oracle te laden. <br>Toegestane waarden zijn: **Geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange**.<br>Wanneer een partitieoptie is ingeschakeld (dat wil zeggen), `None`wordt de mate van parallellisme [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) om tegelijkertijd gegevens uit een Oracle-database te laden, beheerd door de instelling voor de kopieeractiviteit. | Nee |
| partitieInstellingen | Geef de groep van de instellingen voor gegevenspartitionering op. <br>Toepassen wanneer de partitieoptie `None`niet is . | Nee |
| partitieNamen | De lijst met fysieke partities die gekopieerd moeten worden. <br>Toepassen wanneer de `PhysicalPartitionsOfTable`partitieoptie is . Als u een query gebruikt om `?AdfTabularPartitionName` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallelle kopie van Oracle.](#parallel-copy-from-oracle) | Nee |
| partitionColumnName | Geef de naam op van de bronkolom **in het gehele getaltype** die wordt gebruikt door bereikverdeling voor parallelle kopie. Als dit niet is opgegeven, wordt de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als partitiekolom. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionColumnName` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallelle kopie van Oracle.](#parallel-copy-from-oracle) | Nee |
| partitieUpperBound | De maximale waarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionUpbound` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallelle kopie van Oracle.](#parallel-copy-from-oracle) | Nee |
| partitionLowerBound | De minimumwaarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionLowbound` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallelle kopie van Oracle.](#parallel-copy-from-oracle) | Nee |

**Voorbeeld: gegevens kopiëren met behulp van een basisquery zonder partitie**

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

### <a name="oracle-as-sink"></a>Oracle als gootsteen

Als u gegevens naar Oracle wilt kopiëren, `OracleSink`stelt u het gootsteentype in de kopieeractiviteit in op . De volgende eigenschappen worden ondersteund in de sectie copy activity **sink.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteit `OracleSink`moet worden ingesteld op . | Ja |
| writeBatchSize | Hiermee voegt u gegevens in de `writeBatchSize`SQL-tabel in wanneer de buffergrootte is bereikt .<br/>Toegestane waarden zijn Integer (aantal rijen). |Nee (standaard is 10.000) |
| writeBatchTimeout | De wachttijd voor de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br/>Toegestane waarden zijn Timespan. Een voorbeeld is 00:30:00 (30 minuten). | Nee |
| preCopyScript | Geef een SQL-query op voor de kopieeractiviteit die moet worden uitgevoerd voordat u in elke run gegevens in Oracle schrijft. U deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | Nee |

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

De Data Factory Oracle-connector biedt ingebouwde gegevenspartitionering om gegevens van Oracle parallel te kopiëren. U opties voor gegevenspartitionering vinden op het tabblad **Bron** van de kopieeractiviteit.

![Schermafbeelding van partitieopties](./media/connector-oracle/connector-oracle-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit tegen uw Oracle-bron om gegevens te laden op partities. De parallelle graad wordt [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) gecontroleerd door de instelling voor de kopieeractiviteit. Als u bijvoorbeeld `parallelCopies` op vier instelt, genereert en voert Data Factory tegelijkertijd vier query's uit op basis van de opgegeven partitieoptie en -instellingen en haalt elke query een deel van de gegevens uit uw Oracle-database op.

U wordt voorgesteld om parallelle kopiëren met gegevenspartitionering in te schakelen, vooral wanneer u grote hoeveelheden gegevens uit uw Oracle-database laadt. De volgende zijn voorgestelde configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar het gegevensarchief in bestanden wordt aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan naar één bestand te schrijven.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van grote tabel, met fysieke partities.          | **Partitieoptie**: Fysieke partities van tabel. <br><br/>Tijdens de uitvoering detecteert Data Factory automatisch de fysieke partities en kopieert gegevens door partities. |
| Volledige belasting van grote tabel, zonder fysieke partities, terwijl met een gehele kolom voor gegevenspartitionering. | **Partitieopties**: Dynamische bereikpartitie.<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als dit niet is opgegeven, wordt de primaire sleutelkolom gebruikt. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met fysieke partities. | **Partitieoptie**: Fysieke partities van tabel.<br>**Query** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**Partitienaam:** geef de partitienaam(s) op om gegevens van te kopiëren. Als dit niet is opgegeven, detecteert Data Factory automatisch de fysieke partities op de tabel die u hebt opgegeven in de Oracle-gegevensset.<br><br>Tijdens de uitvoering `?AdfTabularPartitionName` vervangt Data Factory de werkelijke partitienaam en verzendt naar Oracle. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl met een gehele kolom voor gegevenspartitionering. | **Partitieopties**: Dynamische bereikpartitie.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U partitioneren tegen de kolom met integer gegevenstype.<br>**Hoofdgrens** en **partitie ondergrens**: Geef op als u wilt filteren op partitiekolom om alleen gegevens op te halen tussen het onderste en bovenste bereik.<br><br>Tijdens de uitvoering `?AdfRangePartitionColumnName`vervangt `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , en met de werkelijke kolomnaam en waardebereiken voor elke partitie, en verzendt naar Oracle. <br>Als uw partitiekolom 'ID' bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met parallelle kopie ingesteld als 4, haalt Data Factory gegevens op met 4 partities. Hun ID's liggen tussen [1,20], [21, 40], [41, 60] en [61, 80], respectievelijk. |

**Voorbeeld: query met fysieke partitie**

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

**Voorbeeld: query met dynamische bereikpartitie**

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

## <a name="data-type-mapping-for-oracle"></a>Gegevenstypetoewijzing voor Oracle

Wanneer u gegevens van en naar Oracle kopieert, zijn de volgende toewijzingen van toepassing. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| Oracle-gegevenstype | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| BFILE |Byte |
| Blob |Byte<br/>(alleen ondersteund op Oracle 10g en hoger) |
| Char |Tekenreeks |
| CLOB (CLOB) |Tekenreeks |
| DATE |DateTime |
| Float |Decimaal, Tekenreeks (als precisie > 28) |
| INTEGER |Decimaal, Tekenreeks (als precisie > 28) |
| Lange |Tekenreeks |
| LANG RAUW |Byte |
| Nchar |Tekenreeks |
| NCLOB (NCLOB) |Tekenreeks |
| Nummer |Decimaal, Tekenreeks (als precisie > 28) |
| NVARCHAR2 (NVARCHAR2) |Tekenreeks |
| Raw |Byte |
| RIJ-ID |Tekenreeks |
| Tijdstempel |DateTime |
| TIJDSTEMPEL MET LOKALE TIJDZONE |Tekenreeks |
| TIJDSTEMPEL MET TIJDZONE |Tekenreeks |
| NIET-ONDERTEKEND GEHEEL GETAL |Aantal |
| VARCHAR2 (VARCHAR2) |Tekenreeks |
| XML |Tekenreeks |

> [!NOTE]
> De gegevenstypen INTERVAL VAN JAAR TOT MAAND EN INTERVAL DAG TOT SECONDE worden niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
