---
title: Gegevens kopiëren naar of van Azure Data Explorer met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens naar of van Azure Data Explorer met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010254"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Data Explorer met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van of naar [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Explorer-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van elk ondersteund bron gegevens archief kopiëren naar Azure Data Explorer. U kunt ook gegevens uit Azure Data Explorer kopiëren naar een ondersteunde Sink-gegevens opslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als gegevensbronnen of PUT voor de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md) tabel.

>[!NOTE]
>Het kopiëren van gegevens naar/van Azure Data Explorer van/naar on-premises gegevens opslag via zelf-hostende Integration Runtime wordt ondersteund sinds versie 3,14.

Met de Azure Data Explorer-connector kunt u het volgende doen:

* Gegevens kopiëren met behulp van Azure Active Directory (Azure AD) verificatie van het toepassings token met een **Service-Principal**.
* Haal als bron gegevens op met behulp van een KQL (Kusto)-query.
* Gegevens toevoegen aan een doel tabel als sink.

## <a name="getting-started"></a>Aan de slag

>[!TIP]
>Zie voor een overzicht van het gebruik van Azure Data Explorer-connector [gegevens kopiëren naar/van azure Data Explorer met behulp van Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Data Explorer-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De Azure Data Explorer-connector maakt gebruik van Service-Principal-verificatie. Volg deze stappen om een service-principal op te halen en machtigingen te verlenen:

1. Registreren van een Toepassingsentiteit in Azure Active Directory (Azure AD) door [uw toepassing registreren bij een Azure AD-tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Verleen de service-principal de juiste machtigingen in azure Data Explorer. Raadpleeg [Azure Data Explorer-database machtigingen beheren](../data-explorer/manage-database-permissions.md) met gedetailleerde informatie over de rollen en machtigingen, en lees meer over het beheren van machtigingen. Over het algemeen moet u

    - Geef ten minste de rol van **Data Base-Viewer** voor uw Data Base op **als bron**.
    - Verleen ten minste de rol van **Data Base-opname** **als Sink**voor uw data base.

>[!NOTE]
>Wanneer u een ADF-gebruikers interface gebruikt om te schrijven, wordt uw aanmeldings gebruikers account gebruikt voor het weer geven van Azure Data Explorer clusters, data bases en tabellen. Voer de naam hand matig in als u niet gemachtigd bent om een dergelijke bewerking uit te voeren.

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Data Explorer-service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap **type** moet worden ingesteld op **AzureDataExplorer** | Ja |
| endpoint | Eind punt-URL van het Azure Data Explorer-cluster, met `https://<clusterName>.<regionName>.kusto.windows.net`de indeling als. | Ja |
| database | De naam van de data base. | Ja |
| tenant | De tenantgegevens (domain name of tenant-ID) opgeven in uw toepassing zich bevindt. Dit is wat u normaal gesp roken kent als '**Authority-id**' in [Kusto Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). U kunt deze ophalen door met de muis in de rechter bovenhoek van de Azure Portal te bewegen. | Ja |
| servicePrincipalId | Opgeven van de toepassing client-ID. Dit is wat u normaal gesp roken weet als '**Aad-toepassings client-id**' in [Kusto Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Dit is wat u normaal gesp roken weet als '**Aad-toepassings sleutel**' in [Kusto Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Dit veld als markeert een **SecureString** voor het veilig opslaan in de Data Factory of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Voor beeld van eigenschappen van gekoppelde service:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Azure Data Explorer-gegevensset.

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap type van de gegevensset in op **AzureDataExplorerTable**.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap **type** moet worden ingesteld op **AzureDataExplorerTable** | Ja |
| table | De naam van de tabel waarnaar de gekoppelde service verwijst. | Ja voor Sink; Nee voor bron |

**Voor beeld van eigenschappen van gegevensset**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door Azure Data Explorer bron en Sink.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als bron

Als u gegevens wilt kopiëren uit Azure Data Explorer, stelt u de eigenschap **type** in de bron voor het kopiëren van de activiteit in op **AzureDataExplorerSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **source** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap **type** van de bron van de Kopieer activiteit moet worden ingesteld op: **AzureDataExplorerSource** | Ja |
| query | Een alleen-lezen aanvraag in een [KQL-indeling](/azure/kusto/query/). Gebruik de aangepaste KQL-query als referentie. | Ja |
| queryTimeout | De wacht tijd voordat de query aanvraag een time-out heeft. De standaard waarde is 10 minuten (00:10:00); de toegestane maximum waarde is 1 uur (01:00:00). | Nee |
| noTruncation | Hiermee wordt aangegeven of de geretourneerde resultatenset moet worden afgekapt. Standaard wordt het resultaat afgekapt na 500.000 records of 64 MB. Afkap ping wordt ten zeerste aanbevolen voor het juiste gedrag van de activiteit. |Nee |

>[!NOTE]
>Azure Data Explorer source heeft standaard een maximale grootte van 500.000 records of 64 MB. Als u alle records zonder afkap Ping wilt ophalen, kunt u `set notruncation;` aan het begin van de query opgeven. Raadpleeg de [query limieten](https://docs.microsoft.com/azure/kusto/concepts/querylimits) voor meer informatie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer als Sink

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap type in de Sink van de Kopieer activiteit in op **AzureDataExplorerSink**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **sink** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op: **AzureDataExplorerSink** | Ja |
| ingestionMappingName | Naam van een vooraf gemaakte **[toewijzing](/azure/kusto/management/mappings#csv-mapping)** in een Kusto-tabel. Als u de kolommen van de bron wilt toewijzen aan Azure Data Explorer-die van toepassing is op **[alle ondersteunde bron archieven/-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)** , waaronder CSV/JSON/Avro-indelingen, enzovoort, kunt u de [kolom toewijzing](copy-activity-schema-and-type-mapping.md) Copy activity (impliciet op naam of expliciet als geconfigureerd) gebruiken en /or Azure Data Explorer-toewijzingen. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

* Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).

* Meer informatie over [het kopiëren van gegevens van Azure Data Factory naar Azure Data Explorer](/azure/data-explorer/data-factory-load-data).