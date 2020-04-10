---
title: Gegevens van Marketo kopiëren met Azure Data Factory (Voorbeeld)
description: Meer informatie over het kopiëren van gegevens van Marketo naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 40a16d559a96c88a864ef809d40d798b99746230
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992093"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Gegevens van Marketo kopiëren met Azure Data Factory (Voorbeeld)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Marketo te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

> [!IMPORTANT]
> Deze connector is momenteel in preview. Je het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Marketo-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Marketo kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

>[!NOTE]
>Deze Marketo connector is gebouwd bovenop de Marketo REST API. Houd er rekening mee dat de Marketo [gelijktijdige aanvraaglimiet](https://developers.marketo.com/rest-api/) heeft aan de servicezijde. Als u fouten maakt met de tekst "Error while trying to use REST API: Max rate limit '100' exceeded with in '20' secs (606)" or "Error while trying to use REST API: Concurrent access limit '10' reached (615)", overweeg dan om de gelijktijdige kopieeractiviteit te verminderen om het aantal aanvragen voor de service te verminderen.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Marketo-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde service van Marketo:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het type eigenschap moet worden ingesteld op: **Marketo** | Ja |
| endpoint | Het eindpunt van de Marketo-server. (123-ABC-321.mktorest.com)  | Ja |
| clientId | De klant-id van uw Marketo-service.  | Ja |
| clientGeheim | Het klantgeheim van uw Marketo-service. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| gebruikVersleutelde eindpunten | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee geeft u op of u de identiteit van de server moet verifiëren wanneer u verbinding maakt via TLS. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Marketo.

Als u gegevens uit Marketo wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **MarketoObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MarketoObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron Marketo.

### <a name="marketo-as-source"></a>Marketo als bron

Als u gegevens van Marketo wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **MarketoSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet zijn ingesteld op: **MarketoSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Activitiy_Types"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
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
