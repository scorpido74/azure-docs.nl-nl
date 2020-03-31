---
title: Gegevens kopiëren vanuit ServiceNow
description: Meer informatie over het kopiëren van gegevens van ServiceNow naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926171"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Gegevens van ServiceNow kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van ServiceNow te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze ServiceNow-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van ServiceNow kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de ServiceNow-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor serviceNow-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **ServiceNow** | Ja |
| endpoint | Het eindpunt van de ServiceNow-server (`http://<instance>.service-now.com`).  | Ja |
| authenticationType | Het te gebruiken verificatietype. <br/>Toegestane waarden zijn: **Basic**, **OAuth2** | Ja |
| gebruikersnaam | De gebruikersnaam die wordt gebruikt om verbinding te maken met de ServiceNow-server voor Basis- en OAuth2-verificatie.  | Ja |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruikersnaam voor Basis- en OAuth2-verificatie. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| clientId | De client-ID voor OAuth2-verificatie.  | Nee |
| clientGeheim | Het clientgeheim voor OAuth2-authenticatie. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| gebruikVersleutelde eindpunten | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam in het certificaat van de server moet overeenkomen met de hostnaam van de server wanneer deze verbinding maakt via SSL. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee geeft u op of u de identiteit van de server moet verifiëren wanneer u verbinding maakt via SSL. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset ServiceNow.

Als u gegevens van ServiceNow wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **ServiceNowObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet zijn ingesteld op: **ServiceNowObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Bron ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow als bron

Als u gegevens van ServiceNow wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **ServiceNowSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **ServiceNowSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Actual.alm_asset"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

Noteer het volgende bij het opgeven van het schema en de kolom voor ServiceNow in query en **raadpleeg [Prestatietips](#performance-tips) voor de implicatie van kopieerprestaties**.

- **Schema:** geef het `Actual` `Display` schema op als of in de ServiceNow-query, die u zien als de parameter van true of false bij het aanroepen van `sysparm_display_value` [ServiceNow-rustgevende API's](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolom:** de kolomnaam voor `Actual` de `[column name]_value`werkelijke waarde onder `Display` schema `[column name]_display_value`is , terwijl voor weergavewaarde onder schema is . Let op de kolomnaam moet worden toegewezen aan het schema dat in de query wordt gebruikt.

**Voorbeeldquery:** 
 `SELECT col_value FROM Actual.alm_asset` OF 
`SELECT col_display_value FROM Display.alm_asset`

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Tips voor prestaties

### <a name="schema-to-use"></a>Schema te gebruiken

ServiceNow heeft 2 verschillende schema's, een is **"Werkelijke"** die werkelijke gegevens retourneert, de andere is **"Display"** die de weergavewaarden van gegevens retourneert. 

Als u een filter in uw query hebt, gebruikt u het schema 'Actueel' dat de prestaties van de kopie beter heeft. Bij het opvragen tegen het 'Werkelijke' schema ondersteunt ServiceNow native filter bij het ophalen van de gegevens om alleen de gefilterde resultaatset terug te sturen, terwijl ADF bij het opvragen van het schema 'Weergeven' alle gegevens ophaalt en het filter intern toepast.

### <a name="index"></a>Index

ServiceNow-tabelindex kan helpen bij het verbeteren van de queryprestaties, verwijzen naar [Een tabelindex maken](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
