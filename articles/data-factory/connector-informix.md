---
title: Gegevens kopiëren van IBM Informix-bronnen met behulp van Azure Data Factory
description: Informatie over het kopiëren van gegevens van IBM Informix-bronnen naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b4fb6662491443db5d10825635cad8496e56e7f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414966"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Gegevens kopiëren van en naar IBM Informix-gegevens archieven met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een IBM Informix-gegevens archief te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Informix-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)

U kunt gegevens van een Informix-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

## <a name="prerequisites"></a>Vereisten

Als u deze Informix-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer het Informix-ODBC-stuur programma voor het gegevens archief op de Integration Runtime computer. U kunt bijvoorbeeld het stuur programma IBM INFORMIX Informix (64-bits) gebruiken.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een Informix-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de aan Informix gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Informix** | Ja |
| Verbindings | De ODBC-connection string het referentie deel niet uitsluiten. U kunt de connection string opgeven of de systeem-DSN (gegevens bron naam) gebruiken die u op de Integration Runtime machine hebt ingesteld (u moet nog steeds het referentie deel opgeven in de gekoppelde service). <br> U kunt ook een wacht woord in azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie.| Ja |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met het Informix-gegevens archief.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. | Ja |
| userName | Geef de gebruikers naam op als u basis verificatie gebruikt. | Nee |
| wachtwoord | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| referenties | Het deel van de toegangs referentie van de connection string dat is opgegeven in de eigenschaps waarde-indeling van het stuur programma. Dit veld markeren als SecureString. | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Hierbij**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Informix-gegevensset.

Als u gegevens wilt kopiëren uit Informix, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **InformixTable** | Ja |
| tableName | De naam van de tabel in de Informix. | Nee voor bron (als "query" in activiteits bron is opgegeven);<br/>Ja voor Sink |

**Voorbeeld**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die door de Informix-bron worden ondersteund.

### <a name="informix-as-source"></a>Informix als bron

Als u gegevens wilt kopiëren uit Informix, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **InformixSource** | Ja |
| query | Gebruik de aangepaste query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Hierbij**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
