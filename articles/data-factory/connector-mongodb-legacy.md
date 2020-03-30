---
title: Gegevens van MongoDB kopiëren met behulp van legacy
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
ms.openlocfilehash: 0bdd8d454b979250b57cf657d347309b99a86ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892555"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Gegevens van MongoDB kopiëren met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Huidige versie](connector-mongodb.md)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een MongoDB-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

>[!IMPORTANT]
>ADF release een nieuwe MongoDB connector die een betere native MongoDB ondersteuning in vergelijking met deze ODBC-gebaseerde implementatie biedt, verwijzen naar [MongoDB connector](connector-mongodb.md) artikel over details. Deze verouderde MongoDB-connector wordt ondersteund voor achterwaartse compatibiliteit, terwijl u voor elke nieuwe workload de nieuwe connector gebruikt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U gegevens uit de MongoDB-database kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Specifiek ondersteunt deze MongoDB-connector:

- MongoDB-versies **2.4, 2.6, 3.0, 3.2, 3.4 en 3.6**.
- Gegevens kopiëren met **basis-** of **anonieme** verificatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwde MongoDB driver, daarom hoeft u geen stuurprogramma handmatig te installeren bij het kopiëren van gegevens van MongoDB.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor de MongoDB-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor mongoDB gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **MongoDb** |Ja |
| server |IP-adres of hostnaam van de MongoDB-server. |Ja |
| poort |TCP-poort die de MongoDB-server gebruikt om naar clientverbindingen te luisteren. |Nee (standaard is 27017) |
| Databasenaam |Naam van de MongoDB-database die u wilt openen. |Ja |
| authenticationType | Type verificatie wordt gebruikt om verbinding te maken met de MongoDB-database.<br/>Toegestane waarden zijn: **Basic**en **Anoniem**. |Ja |
| gebruikersnaam |Gebruikersaccount om toegang te krijgen tot MongoDB. |Ja (als basisverificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja (als basisverificatie wordt gebruikt). |
| Bronauth |Naam van de MongoDB-database die u wilt gebruiken om uw referenties te controleren op verificatie. |Nee. Voor basisverificatie is standaard het gebruik van het beheerdersaccount en de database die is opgegeven met de eigenschap databaseName. |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met SSL. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is false.  | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. De volgende eigenschappen worden ondersteund voor de MongoDB-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MongoDbCollection** | Ja |
| collectionNaam |Naam van de collectie in de MongoDB-database. |Ja |

**Voorbeeld:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
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
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **MongoDbSource** | Ja |
| query |Gebruik de aangepaste SQL-92-query om gegevens te lezen. Selecteer bijvoorbeeld * in MyTable. |Nee (als 'collectionName' in de gegevensset is opgegeven) |

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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Wanneer u de SQL-query opgeeft, moet u de DatumTime-indeling in de gaten houden. Bijvoorbeeld: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` of om parameter te gebruiken`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema op gegevensfabriek

Azure Data Factory-service leidt schema af uit een MongoDB-verzameling met behulp van de **nieuwste 100 documenten** in de verzameling. Als deze 100 documenten geen volledig schema bevatten, kunnen sommige kolommen worden genegeerd tijdens de kopieerbewerking.

## <a name="data-type-mapping-for-mongodb"></a>Gegevenstypetoewijzing voor MongoDB

Bij het kopiëren van gegevens van MongoDB worden de volgende toewijzingen gebruikt van MongoDB-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| MongoDB-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| Binair |Byte |
| Booleaans |Booleaans |
| Date |DateTime |
| AantalDubbel |Double |
| NumberInt NumberInt |Int32 |
| AantalLang |Int64 |
| ObjectID |Tekenreeks |
| Tekenreeks |Tekenreeks |
| Uuid |GUID |
| Object |Genormaliseerd tot afvlakkolommen met "_" als geneste scheidingsteken |

> [!NOTE]
> Raadpleeg ondersteuning voor complexe typen met behulp van virtuele tabellen voor meer informatie over ondersteuning voor arrays met [virtuele tabellen.](#support-for-complex-types-using-virtual-tables)
>
> Momenteel worden de volgende MongoDB-gegevenstypen niet ondersteund: DBPointer, JavaScript, Max/Min-toets, Reguliere expressie, Symbool, Tijdstempel, Niet gedefinieerd.

## <a name="support-for-complex-types-using-virtual-tables"></a>Ondersteuning voor complexe typen met behulp van virtuele tabellen

Azure Data Factory gebruikt een ingebouwd ODBC-stuurprogramma om verbinding te maken met gegevens uit uw MongoDB-database en deze te kopiëren. Voor complexe typen zoals arrays of objecten met verschillende typen in de documenten normaliseert het stuurprogramma gegevens opnieuw in overeenkomstige virtuele tabellen. Als een tabel dergelijke kolommen bevat, genereert het stuurprogramma met name de volgende virtuele tabellen:

* Een **basistabel**die dezelfde gegevens bevat als de echte tabel, behalve de complexe typekolommen. De basistabel gebruikt dezelfde naam als de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke complexe typekolom, die de geneste gegevens uitbreidt. De virtuele tabellen worden benoemd met de naam van de echte tabel, een scheidingsteken "_" en de naam van de array of het object.

Virtuele tabellen verwijzen naar de gegevens in de echte tabel, zodat de bestuurder toegang heeft tot de gedenormaliseerde gegevens. U hebt toegang tot de inhoud van MongoDB-arrays door de virtuele tabellen op te vragen en lid te worden.

### <a name="example"></a>Voorbeeld

ExampleTable is hier bijvoorbeeld een MongoDB-tabel met één kolom met een array met objecten in elke cel : Facturen en één kolom met een array van Scalar-typen - Classificaties.

| _id | Naam van de klant | Facturen | Serviceniveau | Waarderingen |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", artikel:"broodrooster", prijs:"456", korting:"0,2"}, {invoice_id:"124", artikel:"oven", prijs: "1235", korting: "0.2"}] |Zilver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", artikel:"koelkast", prijs: "12543", korting: "0.0"}] |Goud |[1,2] |

Het stuurprogramma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De eerste virtuele tabel is de basistabel met de naam 'Voorbeeldtabel', weergegeven in het voorbeeld. De basistabel bevat alle gegevens van de oorspronkelijke tabel, maar de gegevens van de arrays zijn weggelaten en worden uitgebreid in de virtuele tabellen.

| _id | Naam van de klant | Serviceniveau |
| --- | --- | --- |
| 1111 |ABC |Zilver |
| 2222 |XYZ |Goud |

In de volgende tabellen worden de virtuele tabellen weergegeven die de oorspronkelijke arrays in het voorbeeld vertegenwoordigen. Deze tabellen bevatten de volgende gegevens:

* Een verwijzing naar de oorspronkelijke primaire sleutelkolom die overeenkomt met de rij van de oorspronkelijke array (via de kolom _id)
* Een indicatie van de positie van de gegevens binnen de oorspronkelijke array
* De uitgebreide gegevens voor elk element binnen de array

**Tabel "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Korting |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Broodrooster |456 |0,2 |
| 1111 |1 |124 |Oven |1235 |0,2 |
| 2222 |0 |135 |Koelkast |12543 |0,0 |

**Tabel "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
