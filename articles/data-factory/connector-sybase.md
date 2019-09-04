---
title: Gegevens van Sybase kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van Sybase naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
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
ms.openlocfilehash: ab2035ec344e07d88e2ac4ffb19cb1b2361f2e92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277469"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Gegevens van Sybase kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-sybase-connector.md)
> * [Huidige versie](connector-sybase.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een Sybase-data base te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens van Sybase-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Deze Sybase-connector ondersteunt met name:

- SAP Sybase SQL Anywhere (ASA) **versie 16 en hoger**; IQ en ASE worden niet ondersteund.
- Kopiëren van gegevens met behulp van **basis** -of **Windows** -verificatie.

## <a name="prerequisites"></a>Vereisten

Als u deze Sybase-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelfgehoste Cloudintegratieruntime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer de [gegevens provider voor Sybase iAnywhere. data. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 of hoger op de Integration runtime machine.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Sybase-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Sybase-gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **Sybase** | Ja |
| server | De naam van de Sybase-server. |Ja |
| database | De naam van de Sybase-data base. |Ja |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met de Sybase-data base.<br/>Toegestane waarden zijn: **Basis**en **Windows**. |Ja |
| username | Geef de gebruikers naam op om verbinding te maken met de Sybase-data base. |Ja |
| password | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die door Sybase DataSet worden ondersteund.

Als u gegevens wilt kopiëren vanuit Sybase, worden de volgende eigenschappen ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **SybaseTable** | Ja |
| tableName | De naam van de tabel in de Sybase-data base. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u getypte gegevensset gebruikt `RelationalTable` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die door Sybase-bron worden ondersteund.

### <a name="sybase-as-source"></a>Sybase als bron

Als u gegevens wilt kopiëren van Sybase, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **SybaseSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u getypte bron gebruikt `RelationalSource` , wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-sybase"></a>Toewijzing van gegevens type voor Sybase

Bij het kopiëren van gegevens uit Sybase worden de volgende toewijzingen gebruikt van Sybase-gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

Sybase ondersteunt T-SQL-typen. Zie [Azure SQL database-connector-gegevens type toewijzing](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) voor een toewijzings tabel van SQL-typen om tussenliggende gegevens typen te Azure Data Factory.


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
