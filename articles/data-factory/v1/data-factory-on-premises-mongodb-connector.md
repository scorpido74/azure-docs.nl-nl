---
title: Gegevens van MongoDB verplaatsen
description: Meer informatie over het verplaatsen van gegevens uit de MongoDB-database met Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281338"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Gegevens van MongoDB verplaatsen met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-on-premises-mongodb-connector.md)
> * [Versie 2 (huidige versie)](../connector-mongodb.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [MongoDB-connector in V2](../connector-mongodb.md)als u de huidige versie van de Data Factory-service gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een on-premises MongoDB-database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een on-premises MongoDB-gegevensarchief kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een MongoDB-gegevensarchief naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een MongoDB-gegevensarchief.

## <a name="prerequisites"></a>Vereisten
Als u de Azure Data Factory-service wilt verbinden met uw on-premises MongoDB-database, moet u de volgende onderdelen installeren:

- Ondersteunde MongoDB-versies zijn: 2.4, 2.6, 3.0, 3.2, 3.4 en 3.6.
- Data Management Gateway op dezelfde machine die de database host of op een aparte machine om te voorkomen dat we concurreren om resources met de database. Data Management Gateway is een software die on-premises gegevensbronnen op een veilige en beheerde manier verbindt met cloudservices. Zie [artikel Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over Data Management Gateway. Zie [Gegevens verplaatsen van on-premises naar een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies voor het instellen van de gateway een gegevenspijplijn om gegevens te verplaatsen.

    Wanneer u de gateway installeert, installeert deze automatisch een Microsoft MongoDB ODBC-stuurprogramma dat wordt gebruikt om verbinding te maken met MongoDB.

    > [!NOTE]
    > U moet de gateway gebruiken om verbinding te maken met MongoDB, zelfs als deze wordt gehost in Azure IaaS VM's. Als u verbinding probeert te maken met een exemplaar van MongoDB dat in de cloud wordt gehost, u de gateway-instantie ook installeren in de IaaS VM.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises MongoDB-gegevensarchief met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeeld JSON-voorbeeld: Gegevens van MongoDB](#json-example-copy-data-from-mongodb-to-azure-blob) kopiëren naar Azure Blob.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor de MongoDB-bron:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde service **van OnPremisesMongoDB.**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesMongoDb** |Ja |
| server |IP-adres of hostnaam van de MongoDB-server. |Ja |
| poort |TCP-poort die de MongoDB-server gebruikt om naar clientverbindingen te luisteren. |Optioneel, standaardwaarde: 27017 |
| authenticationType |Basic, of Anoniem. |Ja |
| gebruikersnaam |Gebruikersaccount om toegang te krijgen tot MongoDB. |Ja (als basisverificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. |Ja (als basisverificatie wordt gebruikt). |
| Bronauth |Naam van de MongoDB-database die u wilt gebruiken om uw referenties te controleren op verificatie. |Optioneel (als basisverificatie wordt gebruikt). standaard: gebruikt het beheerdersaccount en de database die is opgegeven met de eigenschap databaseName. |
| Databasenaam |Naam van de MongoDB-database die u wilt openen. |Ja |
| gatewayNaam |Naam van de gateway die toegang heeft tot het gegevensarchief. |Ja |
| versleuteldCredential |Referentie versleuteld door gateway. |Optioneel |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **MongoDbCollection** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| collectionNaam |Naam van de collectie in de MongoDB-database. |Ja |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit daarentegen, variëren per activiteitstype. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron van het type **MongoDbSource is,** zijn de volgende eigenschappen beschikbaar in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-querytekenreeks. Selecteer bijvoorbeeld * in MyTable. |Nee (als **verzamelingNaam** van **gegevensset** is opgegeven) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-voorbeeld: Gegevens van MongoDB kopiëren naar Azure Blob
In dit voorbeeld worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Hierin wordt weergegeven hoe u gegevens van een on-premises MongoDB kopiëren naar een Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [OnPremisesMongoDb](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [MongoDbCollection](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [MongoDbSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens uit een queryresultaat in de MongoDB-database naar een blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Stel als eerste stap de datamanagementgateway in volgens de instructies in het artikel [Data Management Gateway.](data-factory-data-management-gateway.md)

**MongoDB gekoppelde service:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Gekoppelde Azure Storage-service:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**MongoDB-invoergegevensset:** Als u "extern" instelt: "true" informeert de service Data Factory dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Activiteit in een pijplijn kopiëren met MongoDB-bron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de bovenstaande invoer- en uitvoergegevenssets te gebruiken en wordt elk uur uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **MongoDbSource** en is **het gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schema op gegevensfabriek
Azure Data Factory-service leidt schema af uit een MongoDB-verzameling met behulp van de nieuwste 100 documenten in de verzameling. Als deze 100 documenten geen volledig schema bevatten, kunnen sommige kolommen worden genegeerd tijdens de kopieerbewerking.

## <a name="type-mapping-for-mongodb"></a>Typetoewijzing voor MongoDB
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens naar MongoDB worden de volgende toewijzingen gebruikt van MongoDB-typen naar .NET-typen.

| MongoDB-type | .NET Framework type |
| --- | --- |
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
> Raadpleeg ondersteuning [voor complexe typen met behulp van virtuele tabellen](#support-for-complex-types-using-virtual-tables) hieronder voor meer informatie over ondersteuning voor arrays met virtuele tabellen.

Momenteel worden de volgende MongoDB-gegevenstypen niet ondersteund: DBPointer, JavaScript, Max/Min-toets, Reguliere expressie, Symbool, Tijdstempel, Niet gedefinieerd

## <a name="support-for-complex-types-using-virtual-tables"></a>Ondersteuning voor complexe typen met behulp van virtuele tabellen
Azure Data Factory gebruikt een ingebouwd ODBC-stuurprogramma om verbinding te maken met gegevens uit uw MongoDB-database en deze te kopiëren. Voor complexe typen zoals arrays of objecten met verschillende typen in de documenten normaliseert het stuurprogramma gegevens opnieuw in overeenkomstige virtuele tabellen. Als een tabel dergelijke kolommen bevat, genereert het stuurprogramma met name de volgende virtuele tabellen:

* Een **basistabel**die dezelfde gegevens bevat als de echte tabel, behalve de complexe typekolommen. De basistabel gebruikt dezelfde naam als de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke complexe typekolom, die de geneste gegevens uitbreidt. De virtuele tabellen worden benoemd met de naam van de echte tabel, een scheidingsteken "_" en de naam van de array of het object.

Virtuele tabellen verwijzen naar de gegevens in de echte tabel, zodat de bestuurder toegang heeft tot de gedenormaliseerde gegevens. Zie Voorbeeld sectie hieronder details. U hebt toegang tot de inhoud van MongoDB-arrays door de virtuele tabellen op te vragen en lid te worden.

U de [wizard Kopiëren](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) gebruiken om intuïtief de lijst met tabellen in de MongoDB-database weer te geven, inclusief de virtuele tabellen, en een voorbeeld van de gegevens binnenin te bekijken. U ook een query maken in de wizard Kopiëren en valideren om het resultaat te bekijken.

### <a name="example"></a>Voorbeeld
Hieronder staat bijvoorbeeld 'ExampleTable' een MongoDB-tabel met één kolom met een array met objecten in elke cel : Facturen en één kolom met een array van Scalar-typen – Classificaties.

| _id | Naam van de klant | Facturen | Serviceniveau | Waarderingen |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", artikel:"broodrooster", prijs:"456", korting:"0,2"}, {invoice_id:"124", artikel:"oven", prijs: "1235", korting: "0.2"}] |Zilver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", artikel:"koelkast", prijs: "12543", korting: "0.0"}] |Goud |[1,2] |

Het stuurprogramma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De eerste virtuele tabel is de basistabel met de naam "ExampleTable", hieronder weergegeven. De basistabel bevat alle gegevens van de oorspronkelijke tabel, maar de gegevens van de arrays zijn weggelaten en worden uitgebreid in de virtuele tabellen.

| _id | Naam van de klant | Serviceniveau |
| --- | --- | --- |
| 1111 |ABC |Zilver |
| 2222 |XYZ |Goud |

In de volgende tabellen worden de virtuele tabellen weergegeven die de oorspronkelijke arrays in het voorbeeld vertegenwoordigen. Deze tabellen bevatten de volgende gegevens:

* Een verwijzing naar de oorspronkelijke primaire sleutelkolom die overeenkomt met de rij van de oorspronkelijke array (via de kolom _id)
* Een indicatie van de positie van de gegevens binnen de oorspronkelijke array
* De uitgebreide gegevens voor elk element binnen de array

Tabel "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Korting |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Broodrooster |456 |0,2 |
| 1111 |1 |124 |Oven |1235 |0,2 |
| 2222 |0 |135 |Koelkast |12543 |0,0 |

Tabel "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie [Gegevens verplaatsen tussen on-premises en cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies voor het maken van een gegevenspijplijn die gegevens van een on-premises gegevensopslag naar een Azure-gegevensarchief verplaatst.
