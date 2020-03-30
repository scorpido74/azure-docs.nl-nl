---
title: Gegevens verplaatsen van SAP Business Warehouse met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit SAP Business Warehouse met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281052"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Gegevens verplaatsen van SAP Business Warehouse met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-sap-business-warehouse-connector.md)
> * [Versie 2 (huidige versie)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [SAP Business Warehouse-connector in V2](../connector-sap-business-warehouse.md)als u de huidige versie van de datafabriekservice gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens te verplaatsen vanuit een on-premises SAP Business Warehouse (BW). Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een on-premises SAP Business Warehouse-gegevensarchief kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een SAP Business Warehouse naar andere datastores, maar niet voor het verplaatsen van gegevens van andere datastores naar een SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Deze connector ondersteunt SAP Business Warehouse versie 7.x. Het ondersteunt het kopiëren van gegevens van InfoCubes en QueryCubes (inclusief BEx-query's) met Behulp van MDX-query's.

Installeer de volgende onderdelen om de verbinding met de SAP BW-instantie in te schakelen:
- **Data Management Gateway**: Data Factory-service ondersteunt het verbinden met on-premises datastores (inclusief SAP Business Warehouse) met behulp van een component genaamd Data Management Gateway. Zie [Gegevens opslaan tussen on-premises gegevensarchief naar het cloudgegevensarchief](data-factory-move-data-between-onprem-and-cloud.md) verplaatsen voor meer informatie over datamanagementgateway en stapsgewijze instructies voor het instellen van de gateway. Gateway is vereist, zelfs als het SAP Business Warehouse wordt gehost in een Azure IaaS virtuele machine (VM). U de gateway op dezelfde VM installeren als het gegevensarchief of op een andere virtuele machine, zolang de gateway verbinding kan maken met de database.
- **SAP NetWeaver-bibliotheek** op de gatewaymachine. U de SAP Netweaver-bibliotheek ophalen bij uw SAP-beheerder of rechtstreeks van het [SAP Software Download Center.](https://support.sap.com/swdc) Zoek naar de **SAP Note #1025361** om de downloadlocatie voor de meest recente versie te krijgen. Zorg ervoor dat de architectuur voor de SAP NetWeaver-bibliotheek (32-bits of 64-bits) overeenkomt met uw gateway-installatie. Installeer vervolgens alle bestanden die zijn opgenomen in de SAP NetWeaver RFC SDK volgens de SAP Note. De SAP NetWeaver-bibliotheek is ook opgenomen in de SAP Client Tools-installatie.

> [!TIP]
> Zet de dlls uit de NetWeaver RFC SDK in de map system32.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises Cassandra-gegevensarchief met behulp van verschillende hulpprogramma's/API's. 

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren. 
- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. 

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. 
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. 

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeeld voor](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) een voorbeeld met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een on-premises SAP Business Warehouse te kopiëren. 

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor een SAP BW-gegevensarchief:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde service van SAP Business Warehouse (BW).

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | Naam van de server waarop de SAP BW-instantie zich bevindt. | tekenreeks | Ja
systeemAantal | Systeemnummer van het SAP BW-systeem. | Tweecijferig getal met de cimaal weergegeven als een tekenreeks. | Ja
clientId | Client-ID van de client in het SAP W-systeem. | Driecijferig decimaal getal weergegeven als een tekenreeks. | Ja
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Ja
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Ja
gatewayNaam | Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises SAP BW-instantie. | tekenreeks | Ja
versleuteldCredential | De gecodeerde referentietekenreeks. | tekenreeks | Nee

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. Er worden geen typespecifieke eigenschappen ondersteund voor de SAP BW-gegevensset van type **RelationalTable**. 


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron in kopieeractiviteit van type **RelationalSource** is (waaronder SAP BW) bevat, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de MDX-query op om gegevens uit de instantie SAP BW te lezen. | MDX-query. | Ja |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-voorbeeld: Gegevens van SAP Business Warehouse kopiëren naar Azure Blob
In het volgende voorbeeld vindt u voorbeeld-JSON-definities die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) In dit voorbeeld ziet u hoe u gegevens uit een on-premises SAP Business Warehouse kopieert naar een Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.  

> [!IMPORTANT]
> Dit voorbeeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de gegevensfabriek. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [SapBw](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert gegevens van een SAP Business Warehouse-exemplaar naar een Azure-blob per uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Als eerste stap, het instellen van de data management gateway. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse gekoppelde service
Deze gekoppelde service koppelt uw SAP BW-exemplaar aan de gegevensfabriek. De eigenschap type is ingesteld op **SapBw.** De sectie typeEigenschappen biedt verbindingsgegevens voor de INSTANTIE SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Met deze gekoppelde service wordt uw Azure Storage-account gekoppeld aan de gegevensfabriek. De eigenschap type is ingesteld op **AzureStorage**. De sectie typeEigenschappen biedt verbindingsgegevens voor het Azure Storage-account.

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

### <a name="sap-bw-input-dataset"></a>SAP BW-invoergegevensset
Deze gegevensset definieert de SAP Business Warehouse-gegevensset. U stelt het type gegevensset Gegevensfabriek in **op RelationalTable**. Momenteel geeft u geen typespecifieke eigenschappen op voor een SAP BW-gegevensset. De query in de definitie Activiteit kopiëren geeft aan welke gegevens moeten worden gelezen vanuit de instantie SAP BW. 

Als u externe eigenschap op true instelt, wordt de service Gegevensfabriek geïnformeerd dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

Frequentie- en intervaleigenschappen bepalen het schema. In dit geval worden de gegevens elk uur uit de SAP BW-instantie gelezen. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset
Met deze gegevensset wordt de uitvoerset Azure Blob-gegevensset gedefinieerd. De eigenschap type is ingesteld op AzureBlob. De sectie typeEigenschappen geeft aan waar de gegevens die zijn gekopieerd uit de SAP BW-instantie worden opgeslagen. De gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pijplijn met kopieeractiviteit
De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **RelationalSource** (voor SAP BW-bron) en wordt **het gootsteentype** ingesteld op **BlobSink**. De query die is opgegeven voor de **eigenschap query** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Typetoewijzing voor SAP BW
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens van SAP BW worden de volgende toewijzingen gebruikt van SAP BW-typen naar .NET-typen.

Gegevenstype in het ABAP-woordenboek | .NET-gegevenstype
-------------------------------- | --------------
ACCP (ACCP) |  Int
Char | Tekenreeks
Clnt | Tekenreeks
CURR (CURR) | Decimal
CUKY CUKY | Tekenreeks
Dec | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG LANG | Tekenreeks
LCHR (LCHR) | Tekenreeks
LRAW (LRAW) | Byte
PREC (PREC) | Int16
Quan | Decimal
Raw | Byte
RAWSTRING RAWSTRING | Byte
Tekenreeks | Tekenreeks
Eenheid | Tekenreeks
DATS (DATS) | Tekenreeks
NUMC (NUMC) | Tekenreeks
TIMS TIMS | Tekenreeks

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.


## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
