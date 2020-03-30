---
title: Gegevens uit ODBC-bronnen kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit OData-bronnen naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: 6513cfc5432e969fc53aa72b075af194a064d178
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244366"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Gegevens van en naar ODBC-gegevensarchieven kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-odbc-connector.md)
> * [Huidige versie](connector-odbc.md)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar een ODBC-gegevensarchief te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze ODBC-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit ODBC-bron kopiëren naar een ondersteund sinkdataarchief of kopiëren van een ondersteund brongegevensarchief naar ODBC-sink. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Deze ODBC-connector ondersteunt specifiek het kopiëren van gegevens van/naar **odbc-compatibele gegevensarchieven** met **basis-** of **anonieme** verificatie. Een **64-bits ODBC-driver** is vereist.

## <a name="prerequisites"></a>Vereisten

Als u deze ODBC-connector wilt gebruiken, moet u het:

- Stel een Self-hosted Integration Runtime in. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.
- Installeer het 64-bits ODBC-stuurprogramma voor het gegevensarchief op de Integratieruntime-machine.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor odbc-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor ODBC-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Odbc** | Ja |
| Connectionstring | De verbindingstekenreeks met uitzondering van het referentiegedeelte. U de verbindingstekenreeks `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`met patroon als, of het systeem DSN (Gegevensbronnaam) `"DSN=<name of the DSN on IR machine>;"` gebruiken waarmee u de integratieruntime-machine instelt (u moet nog steeds het referentiegedeelte in gekoppelde service opgeven).<br>U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen.Raadpleeg [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details.| Ja |
| authenticationType | Type verificatie wordt gebruikt om verbinding te maken met het ODBC-gegevensarchief.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. | Ja |
| userName | Geef de gebruikersnaam op als u Basisverificatie gebruikt. | Nee |
| wachtwoord | Geef een wachtwoord op voor het gebruikersaccount dat u voor de userName hebt opgegeven. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| referenties | Het gedeelte toegangsreferenties van de verbindingstekenreeks die is opgegeven in de waardenotatie van stuurprogramma's. Bijvoorbeeld: `"RefreshToken=<secret refresh token>;"`. Markeer dit veld als een SecureString. | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld 1: basisverificatie gebruiken**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Voorbeeld 2: anonieme verificatie gebruiken**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door odbc-gegevensset.

Als u gegevens wilt kopiëren van/naar odbc-compatibele gegevensarchief, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **OdbcTable** | Ja |
| tableName | Naam van de tabel in het ODBC-gegevensarchief. | Nee voor bron (als 'query' in activiteitsbron is opgegeven);<br/>Ja voor gootsteen |

**Voorbeeld**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als deze is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door ODBC-bron.

### <a name="odbc-as-source"></a>ODBC als bron

Als u gegevens uit odbc-compatibele gegevensarchief wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet zijn ingesteld op: **OdbcSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

### <a name="odbc-as-sink"></a>ODBC als gootsteen

Als u gegevens wilt kopiëren naar ODBC-compatibele gegevensarchief, stelt u het gootsteentype in de kopieeractiviteit in op **OdbcSink**. De volgende eigenschappen worden ondersteund in de sectie copy activity **sink:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteit moet worden ingesteld op: **OdbcSink** | Ja |
| writeBatchTimeout |Wacht de tijd voordat de batchinvoegbewerking is voltooid voordat deze een time-out heeft.<br/>Toegestane waarden zijn: tijdspanne. Voorbeeld: "00:30:00" (30 minuten). |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de SQL-tabel in wanneer de buffergrootte writeBatchSize bereikt.<br/>Toegestane waarden zijn: geheel getal (aantal rijen). |Nee (standaard is 0 - automatisch gedetecteerd) |
| preCopyScript |Geef een SQL-query op voor Activiteit kopiëren om uit te voeren voordat gegevens in elke uitvoering in het gegevensarchief worden geschreven. U deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. |Nee |

> [!NOTE]
> Voor 'writeBatchSize', als deze niet is ingesteld (automatisch gedetecteerd), detecteert kopieeractiviteit eerst of het stuurprogramma batchbewerkingen ondersteunt en stel u deze in op 10000 als dit het wel is, of stel u deze in op 1 als dit niet het doet. Als u de waarde expliciet instelt, eert kopieeractiviteit de waarde en mislukt deze bij runtime als het stuurprogramma batchbewerkingen niet ondersteunt.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>SAP HANA gootsteen

>[!NOTE]
>Als u gegevens uit SAP HANA-gegevensarchief wilt kopiëren, raadpleegt u de native [SAP HANA-connector.](connector-sap-hana.md) Als u gegevens wilt kopiëren naar SAP HANA, volgt u deze instructie om odbc-connector te gebruiken. Let op: de gekoppelde services voor SAP HANA-connector en ODBC-connector zijn van verschillend type en kunnen dus niet opnieuw worden gebruikt.
>

U gegevens kopiëren naar SAP HANA-database met behulp van de generieke ODBC-connector.

Stel een Self-hosted Integration Runtime in op een machine met toegang tot uw gegevensarchief. De Integratieruntime maakt gebruik van de ODBC-driver voor SAP HANA om verbinding te maken met het gegevensarchief. Installeer daarom het stuurprogramma als deze nog niet op dezelfde machine is geïnstalleerd. Zie [sectie Voorwaarden](#prerequisites) voor meer informatie.

Controleer voordat u de SAP HANA-sink in een Data Factory-oplossing gebruikt, of de integratieruntijd verbinding kan maken met het gegevensarchief met behulp van instructies in [het oplossen van verbindingsproblemen.](#troubleshoot-connectivity-issues)

Maak een ODBC-gekoppelde service om een SAP HANA-gegevensarchief te koppelen aan een Azure-gegevensfabriek zoals weergegeven in het volgende voorbeeld:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Lees het artikel vanaf het begin voor een gedetailleerd overzicht van het gebruik van ODBC-gegevensarchieven als bron/gootsteengegevensopslag in een kopieerbewerking.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Verbindingsproblemen oplossen

Als u verbindingsproblemen wilt oplossen, gebruikt u het tabblad **Diagnostische gegevens** van **Configuratiebeheer voor integratieruntime**.

1. Runtime **Configuration Manager voor de uitvoering van integratie starten**.
2. Ga naar het tabblad **Diagnostische gegevens.**
3. Selecteer onder de sectie 'Testverbinding' het **type** gegevensarchief (gekoppelde service).
4. Geef de **verbindingstekenreeks** op die wordt gebruikt om verbinding te maken met het gegevensarchief, kies de **verificatie** en voer **gebruikersnaam,** **wachtwoord**en/of **referenties**in.
5. Klik **op Verbinding testen** om de verbinding met het gegevensarchief te testen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
