---
title: Gegevens uit Microsoft Access-bronnen kopiëren
description: Meer informatie over het kopiëren van gegevens uit Microsoft Access-bronnen naar ondersteunde sink-gegevensarchieven met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: d6110065e28b8f179cd1d113107fb3508e1c3e44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892547"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Gegevens van en naar Microsoft Access-gegevensarchieven kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een Microsoft Access-gegevensarchief te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Microsoft Access-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit de Microsoft Access-bron kopiëren naar een ondersteund sink-gegevensarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

## <a name="prerequisites"></a>Vereisten

Als u deze Microsoft Access-connector wilt gebruiken, moet u het:

- Stel een Self-hosted Integration Runtime in. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.
- Installeer het Microsoft Access ODBC-stuurprogramma voor het gegevensarchief op de machine Integration Runtime.

>[!NOTE]
>Microsoft Access 2016-versie van ODBC-stuurprogramma werkt niet met deze connector. Gebruik in plaats daarvan stuurprogrammaversie 2013 of 2010.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de Microsoft Access-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde Microsoft Access-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **MicrosoftAccess** | Ja |
| Connectionstring | De ODBC-verbindingstekenreeks met uitzondering van het referentiegedeelte. U de verbindingstekenreeks opgeven of de systeemnaam DSN (Gegevensbronnaam) gebruiken die u instelt op de integratieruntime-machine (u moet nog steeds het referentiegedeelte in de gekoppelde service opgeven).<br> U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen.Raadpleeg [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details.| Ja |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met het Microsoft Access-gegevensarchief.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. | Ja |
| userName | Geef de gebruikersnaam op als u Basisverificatie gebruikt. | Nee |
| wachtwoord | Geef een wachtwoord op voor het gebruikersaccount dat u voor de userName hebt opgegeven. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| referenties | Het gedeelte toegangsreferenties van de verbindingstekenreeks die is opgegeven in de waardenotatie van stuurprogramma's. Markeer dit veld als een SecureString. | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de microsoft Access-gegevensset.

Als u gegevens uit Microsoft Access wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet zijn ingesteld op: **MicrosoftAccessTable** | Ja |
| tableName | Naam van de tabel in microsoft access. | Nee voor bron (als 'query' in activiteitsbron is opgegeven);<br/>Ja voor gootsteen |

**Voorbeeld**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Microsoft Access-bron.

### <a name="microsoft-access-as-source"></a>Microsoft Access als bron

Als u gegevens uit het gegevensarchief van Microsoft Access wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op: **MicrosoftAccessSource** | Ja |
| query | Gebruik de aangepaste query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
