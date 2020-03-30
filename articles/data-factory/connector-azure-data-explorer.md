---
title: Gegevens kopiëren naar of vanuit Azure Data Explorer
description: Meer informatie over het kopiëren van gegevens naar of vanuit Azure Data Explorer met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 4c265cb0cdc665ef52f4dc6e69440e83c22db449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460972"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Gegevens kopiëren naar of vanuit Azure Data Explorer met Azure Data Factory

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens naar of vanuit [Azure Data Explorer](../data-explorer/data-explorer-overview.md)te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel, dat een algemeen overzicht van kopieeractiviteit biedt.

>[!TIP]
>Voor Azure Data Factory- en Azure Data Explorer-integratie in het algemeen, leest u meer over [Azure Data Explorer integreren met Azure Data Factory](../data-explorer/data-factory-integration.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Explorer-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Data Explorer. U ook gegevens uit Azure Data Explorer kopiëren naar een ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die de kopieeractiviteit als bronnen of zinkt ondersteunt.

>[!NOTE]
>Het kopiëren van gegevens van of naar Azure Data Explorer via een on-premises gegevensarchief met behulp van zelfgehoste uitvoering van integratie wordt ondersteund in versie 3.14 en hoger.

Met de Azure Data Explorer-connector u het volgende doen:

* Kopieer gegevens met Azure Active Directory -verificatie (Azure AD) met een **serviceprincipal**.
* Als bron u gegevens ophalen met behulp van een KQL-query (Kusto).
* Als gootsteen worden gegevens toegevoegd aan een doeltabel.

## <a name="getting-started"></a>Aan de slag

>[!TIP]
>Zie [Gegevens kopiëren van/naar Azure Data Explorer met Azure Data Factory](../data-explorer/data-factory-load-data.md) en Bulk kopiëren vanuit een database naar Azure Data [Explorer](../data-explorer/data-factory-template.md)voor een walkthrough van azure data explorer.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Azure Data Explorer-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De Azure Data Explorer-connector maakt gebruik van serviceprincipal-verificatie. Volg deze stappen om een serviceprincipal te krijgen en machtigingen toe te kennen:

1. Registreer een toepassingsentiteit in Azure Active Directory door de stappen te volgen in [Uw toepassing registreren met een Azure AD-tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassingssleutel
    - Tenant-id

2. Geef de serviceprincipal de juiste machtigingen in Azure Data Explorer. Zie [Machtigingen voor Azure Data Explorer-database beheren](../data-explorer/manage-database-permissions.md) voor gedetailleerde informatie over rollen en machtigingen en over het beheren van machtigingen. In het algemeen moet u:

    - **Als bron,** ten minste de rol van de **databaseviewer** toekennen aan uw database
    - **Als sink,** verlenen ten minste de **Database innameor** rol aan uw database

>[!NOTE]
>Wanneer u de gebruikersinterface van Data Factory gebruikt om te schrijven, wordt uw gebruikersaccount gebruikt om Azure Data Explorer-clusters, databases en tabellen weer te geven. Voer de naam handmatig in als u geen toestemming hebt voor deze bewerkingen.

De volgende eigenschappen worden ondersteund voor de gekoppelde Azure Data Explorer-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet zijn ingesteld op **AzureDataExplorer**. | Ja |
| endpoint | URL van eindpunt van het Azure Data `https://<clusterName>.<regionName>.kusto.windows.net`Explorer-cluster, met de indeling als . | Ja |
| database | Naam van de database. | Ja |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Dit staat bekend als "Authority ID" in [Kusto verbindingstekenreeks](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Haal deze op door de muisaanwijzer in de rechterbovenhoek van de Azure-portal te zweven. | Ja |
| servicePrincipalId | Geef de client-id van de toepassing op. Dit staat bekend als "AAD application client ID" in [Kusto connection string](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Dit staat bekend als "AAD-toepassingssleutel" in [kusto-verbindingstekenreeks](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [zoek naar beveiligde gegevens die zijn opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |

**Gekoppelde serviceeigenschappen bijvoorbeeld:**

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

Zie [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie worden eigenschappen weergegeven die de azure data explorer-gegevensset ondersteunt.

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap typeeigenschap van de gegevensset in op **AzureDataExplorerTable**.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet zijn ingesteld op **AzureDataExplorerTable**. | Ja |
| tabel | De naam van de tabel waarnaar de gekoppelde service verwijst. | Ja voor gootsteen; Nee voor bron |

**Voorbeeld van de eigenschappen van gegevensset:**

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

Zie [Pijplijnen en activiteiten in Azure Data Factory](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die azure data explorer-bronnen en sinks ondersteunen.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als bron

Als u gegevens uit Azure Data Explorer wilt kopiëren, stelt u de **eigenschap type** in de activiteitsbron kopiëren in op **AzureDataExplorer.** De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de kopieeractiviteitsbron moet zijn ingesteld op: **AzureDataExplorerSource** | Ja |
| query | Een alleen-lezen verzoek gegeven in een [KQL-formaat](/azure/kusto/query/). Gebruik de aangepaste KQL-query als referentie. | Ja |
| queryTime-out | De wachttijd voordat de queryaanvraag een time-out heeft. De standaardwaarde is 10 min (00:10:00); toegestane maximale waarde is 1 uur (01:00:00). | Nee |
| noTruncation noTruncation | Hiermee geeft u aan of de ingestelde geretourneerde resultaten moeten worden afgekapt. Standaard wordt het resultaat afgekapt na 500.000 records of 64 megabyte (MB). Afkappen wordt sterk aanbevolen om het juiste gedrag van de activiteit te garanderen. |Nee |

>[!NOTE]
>Standaard heeft de Bron van Azure Data Explorer een groottelimiet van 500.000 records of 64 MB. Als u alle records zonder afgekapt `set notruncation;` wilt ophalen, u aan het begin van de query opgeven. Zie [Querylimieten voor](https://docs.microsoft.com/azure/kusto/concepts/querylimits)meer informatie .

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

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer als gootsteen

Als u gegevens wilt kopiëren naar Azure Data Explorer, stelt u de eigenschap type in de inhoudsgroep voor kopieeractiviteiten in op **AzureDataExplorerSink**. De volgende eigenschappen worden ondersteund in de sectie copy activity **sink:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de inhoudsgroep voor kopiëren moet worden ingesteld op: **AzureDataExplorerSink**. | Ja |
| ingestionMappingName | Naam van een vooraf gemaakte [toewijzing](/azure/kusto/management/mappings#csv-mapping) op een Kusto-tabel. Als u de kolommen van bron naar Azure Data Explorer (die van toepassing is op [alle ondersteunde bronopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats), inclusief CSV/JSON/Avro-indelingen), wilt toewijzen, u de [toewijzing](copy-activity-schema-and-type-mapping.md) van de kolomtoewijzing voor kopieeractiviteiten (impliciet op naam of expliciet als geconfigureerd) en/of Azure Data Explorer-toewijzingen gebruiken. | Nee |
| additionalProperties additionalProperties additionalProperties additionalProperties | Een eigenschappenzak die kan worden gebruikt voor het opgeven van een van de inname-eigenschappen die nog niet zijn ingesteld door de Azure Data Explorer Sink. In het bijzonder kan het handig zijn voor het opgeven van innametags. Meer informatie over [azure data Explore-gegevensopnamedocument](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html). | Nee |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
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

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Zie [Opzoekactiviteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

* Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die de kopieeractiviteit in Azure Data Factory ondersteunt als bronnen en zinkt.

* Meer informatie over het [kopiëren van gegevens uit Azure Data Factory naar Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
