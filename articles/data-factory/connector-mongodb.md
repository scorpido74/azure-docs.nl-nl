---
title: Gegevens van MongoDB kopiëren
description: Meer informatie over het kopiëren van gegevens van Mongo DB naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 3b6476b794d2e1b2e9a36aa26f35c247641d44e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418147"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Gegevens van MongoDB kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een MongoDB-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!IMPORTANT]
>ADF release deze nieuwe versie van MongoDB connector die betere native MongoDB ondersteuning biedt. Als u de vorige MongoDB-connector gebruikt in uw oplossing die wordt ondersteund als-is voor achterwaartse compatibiliteit, raadpleegt u het artikel [(MongoDB-connector) (legacy).](connector-mongodb-legacy.md)

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U gegevens uit de MongoDB-database kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Specifiek, deze MongoDB connector ondersteunt **versies tot 3.4**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor de MongoDB-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor mongoDB gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **MongoDbV2** |Ja |
| Connectionstring |Geef de MongoDB-verbindingstekenreeks `mongodb://[username:password@]host[:port][/[database][?options]]`op, bijvoorbeeld . Raadpleeg [mongoDB handleiding op verbindingstekenreeks](https://docs.mongodb.com/manual/reference/connection-string/) voor meer details. <br/><br /> U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details. |Ja |
| database | Naam van de database die u wilt openen. | Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. De volgende eigenschappen worden ondersteund voor de MongoDB-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MongoDbV2Collection** | Ja |
| collectionNaam |Naam van de collectie in de MongoDB-database. |Ja |

**Voorbeeld:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de MongoDB-bron.

### <a name="mongodb-as-source"></a>MongoDB als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **MongoDbV2Source** | Ja |
| filter | Hiermee geeft u het selectiefilter op met queryoperatoren. Als u alle documenten in een verzameling wilt retourneren, laat u deze parameter weg of geeft u een leeg document door ({}). | Nee |
| cursorMethods.project | Hiermee geeft u de velden op die moeten worden teruggevonden in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retourneren, laat u deze parameter weg. | Nee |
| cursorMethoden.sorteren | Hiermee geeft u de volgorde op waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nee |
| cursorMethods.limit | Hiermee geeft u het maximumaantal documenten op dat de server retourneert. Raadpleeg [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nee |
| cursorMethoden.overslaan | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en van waar MongoDB resultaten begint terug te sturen. Raadpleeg [cursor.overslaan()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nee |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden retourneren in elke batch van het antwoord van de instantie MongoDB. In de meeste gevallen heeft het wijzigen van de batchgrootte geen invloed op de gebruiker of de toepassing. Cosmos DB beperkt elke batch mag niet groter zijn dan 40 MB, wat de som is van het batchSize-aantal documenten, dus verlaag deze waarde als de grootte van uw document groot is. | Nee<br/>(de standaardinstelling is **100**) |

>[!TIP]
>ADF-ondersteuning voor het consumeren van BSON-document in **de strikte modus**. Zorg ervoor dat uw filterquery in de modus Strikt staat in plaats van in de Shell-modus. Meer beschrijving is te vinden op [MongoDB handleiding](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>JSON-documenten exporteren naar-is

U deze MongoDB-connector gebruiken om JSON-documenten te exporteren van een MongoDB-verzameling naar verschillende bestandenopslag of naar Azure Cosmos DB. Als u een dergelijke schema-agnostische kopie wilt bereiken, slaat u de sectie "structuur" (ook wel *schema*genoemd) in gegevensset en schematoewijzing in kopieeractiviteit over.

## <a name="schema-mapping"></a>Schematoewijzing

Als u gegevens van MongoDB wilt kopiëren naar tabelgoot, raadpleegt u [schematoewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
