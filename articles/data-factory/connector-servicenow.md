---
title: Gegevens kopiëren van ServiceNow met Azure Data Factory | Microsoft Docs
description: Leer hoe u gegevens kopiëren van ServiceNow naar ondersteunde sink-gegevensopslag met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: a76baf65b2dc7d0cdb444b79e697930188417748
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089492"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Gegevens kopiëren van ServiceNow met Azure Data Factory

In dit artikel bevat een overzicht over het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren van ServiceNow. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze ServiceNow-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit ServiceNow kopiëren naar een ondersteunde sink-gegevensopslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Azure Data Factory biedt een ingebouwde stuurprogramma als connectiviteit wilt inschakelen, dus hoeft u stuurprogramma voor gebruik van deze connector handmatig installeren.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten meer informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke met ServiceNow-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor ServiceNow gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **ServiceNow** | Ja |
| endpoint | Het eindpunt van de ServiceNow-server (`http://<instance>.service-now.com`).  | Ja |
| authenticationType | Het verificatietype te gebruiken. <br/>Toegestane waarden zijn: **Basic**, **OAuth2** | Ja |
| username | De gebruikersnaam die wordt gebruikt voor verbinding met de ServiceNow-server voor Basic en OAuth2-verificatie.  | Ja |
| password | Het wachtwoord dat overeenkomt met de naam van de gebruiker voor Basic en OAuth2-verificatie. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| clientId | De client-ID voor OAuth2-verificatie.  | Nee |
| clientSecret | Het clientgeheim voor OAuth2-verificatie. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| useEncryptedEndpoints | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met behulp van HTTPS. De standaardwaarde is true.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam van de in het certificaat van de server zodat deze overeenkomen met de hostnaam van de server wanneer u verbinding maakt via SSL vereist. De standaardwaarde is true.  | Nee |
| usePeerVerification | Hiermee geeft u op of u wilt controleren of de identiteit van de server wanneer u verbinding maakt via SSL. De standaardwaarde is true.  | Nee |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund voor ServiceNow-gegevensset.

Om gegevens te kopiëren van ServiceNow, stel de eigenschap type van de gegevensset in **ServiceNowObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **ServiceNowObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in de activiteitbron is opgegeven) |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door ServiceNow-bron.

### <a name="servicenow-as-source"></a>ServiceNow als bron

Om gegevens te kopiëren van ServiceNow, stelt u het brontype in de kopieeractiviteit naar **ServiceNowSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **ServiceNowSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Actual.alm_asset"`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

Let op het volgende bij het opgeven van het schema en de kolom voor ServiceNow in de query en **verwijzen naar [tips voor betere prestaties](#performance-tips) op kopiëren prestaties implicatie**.

- **Schema:** Geef het schema als `Actual` of `Display` in de ServiceNow-query die u kunt dat nu als de parameter van `sysparm_display_value` als waar of onwaar bij het aanroepen van [ServiceNow restful-API's](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolom:** voor de werkelijke waarde onder de naam van de kolom `Actual` schema is `[column name]_value`, terwijl voor de weergegeven waarde onder `Display` schema is `[column name]_display_value`. Houd er rekening mee de kolomnaam moet toewijzen aan het schema wordt gebruikt in de query.

**Voorbeeld query:** 
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

### <a name="schema-to-use"></a>Schema moet worden gebruikt

ServiceNow is 2 verschillende schema's, een is **"Werkelijke"** die feitelijke gegevens worden geretourneerd, is de andere **'Weergeven'** die de waarden van de gegevens retourneert. 

Hebt u een filter in de query, gebruikt u 'De werkelijke' schema die is de prestaties beter kopiëren. Bij het uitvoeren van query's volgens het schema "Werkelijke", ServiceNow systeemeigen ondersteuning bieden voor filter tijdens het ophalen van de gegevens retourneert alleen de gefilterde resultatenset dat bij het opvragen van "weergaveschema" ADF alle gegevens op te halen en intern filter toepassen.

### <a name="index"></a>Index

Index van de tabel ServiceNow kan helpen de queryprestaties verbeteren, raadpleeg dan [maakt u een tabelindex](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
