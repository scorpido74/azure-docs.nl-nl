---
title: Gegevens uit webtabel verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit een tabel in een webpagina met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265712"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Gegevens verplaatsen van een webtabelbron met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-web-table-connector.md)
> * [Versie 2 (huidige versie)](../connector-web-table.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Webtabelconnector in V2](../connector-web-table.md)als u de huidige versie van de service Data Factory gebruikt.

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van een tabel in een webpagina naar een ondersteund sink-gegevensarchief te verplaatsen. Dit artikel bouwt voort op het artikel over [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met kopieeractiviteit en de lijst met gegevensarchieven die worden ondersteund als bronnen/putten.

Gegevensfabriek ondersteunt momenteel alleen het verplaatsen van gegevens van een webtabel naar andere gegevensopslag, maar verplaatst geen gegevens van andere gegevensopslag naar een webtabelbestemming.

> [!IMPORTANT]
> Deze webconnector ondersteunt momenteel alleen het extraheren van tabelinhoud uit een HTML-pagina. Als u gegevens wilt ophalen uit een HTTP/s-eindpunt, gebruikt u in plaats daarvan [HTTP-connector.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Vereisten

Als u deze webtabelconnector wilt gebruiken, moet u een Self-hosted Integration Runtime (ook bekend als Data Management Gateway) instellen en de `gatewayName` eigenschap configureren in de sink linked-service. Als u bijvoorbeeld wilt kopiëren van webtabel naar Azure Blob-opslag, configureert u de gekoppelde Azure Storage-service als volgt:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises Cassandra-gegevensarchief met behulp van verschillende hulpprogramma's/API's. 

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren. 
- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. 

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. 
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. 

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeeld: Gegevens uit de webtabel kopiëren naar azure blob-sectie](#json-example-copy-data-from-web-table-to-azure-blob) van dit artikel voor een voorbeeld van JSON met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een webtabel te kopiëren. 

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor een webtabel:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor webgekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **Web** |Ja |
| URL |URL naar de webbron |Ja |
| authenticationType |Anonieme. |Ja |

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **WebTable** heeft de volgende eigenschappen

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |type van de gegevensset. moet worden ingesteld op **WebTable** |Ja |
| path |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van gekoppelde service. |
| Index |De index van de tabel in de resource. Zie [Index van een tabel in een HTML-paginasectie opvragen](#get-index-of-a-table-in-an-html-page) voor stappen om index van een tabel in een HTML-pagina op te halen. |Ja |

**Voorbeeld:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Op dit moment, wanneer de bron in kopieeractiviteit van type **WebSource**is, worden er geen extra eigenschappen ondersteund.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-voorbeeld: gegevens uit webtabel kopiëren naar Azure Blob
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het [type Web](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [WebTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [WebSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens uit een webtabel naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

In het volgende voorbeeld ziet u hoe u gegevens uit een webtabel kopieert naar een Azure-blob. Gegevens kunnen echter rechtstreeks worden gekopieerd naar een van de sinks die in het artikel [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) zijn vermeld met behulp van de activiteit kopiëren in Azure Data Factory.

**Webgekoppelde service** In dit voorbeeld wordt de webgekoppelde service gebruikt met anonieme verificatie. Zie [webgekoppelde servicesectie](#linked-service-properties) voor verschillende typen verificatie die u gebruiken.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Gekoppelde Azure Storage-service**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**WebTable-invoergegevensset** Als **u extern** naar true **instelt,** wordt de service Data Factory geïnformeerd dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

> [!NOTE]
> Zie [Index van een tabel in een HTML-paginasectie opvragen](#get-index-of-a-table-in-an-html-page) voor stappen om index van een tabel in een HTML-pagina op te halen.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pijplijn met kopieeractiviteit**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **WebSource** en is **het gootsteentype** ingesteld op **BlobSink**.

Zie WebSource-teksteigenschappen voor de lijst met eigenschappen die door de WebSource worden ondersteund.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Index van een tabel in een HTML-pagina opdoen
1. Start **Excel 2016** en ga naar het tabblad **Gegevens.**  
2. Klik op **Nieuwe query** op de werkbalk, wijs Uit **andere bronnen** aan en klik op Van **web**.

    ![Power Query-menu](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Voer in het dialoogvenster **Van web** **URL** in die u zou https://en.wikipedia.org/wiki/) gebruiken in de gekoppelde service JSON (bijvoorbeeld: samen met het pad dat u voor de gegevensset opgeeft (bijvoorbeeld: AFI%27s_100_Years... 100_Movies) en klik op **OK**.

    ![Dialoogvenster Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL die in dit voorbeeld wordt gebruikt:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Als u het dialoogvenster **Access Web-inhoud** ziet, selecteert u de juiste **URL**, **verificatie**en klikt u op **Verbinding maken**.

   ![Dialoogvenster Webinhoud openen](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klik op een **tabelitem** in de structuurweergave om de inhoud van de tabel te bekijken en klik vervolgens op **Knop Bewerken** onderin.  

   ![Navigator-venster](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Klik in het venster **Queryeditor** op de knop **Geavanceerde editor** op de werkbalk.

    ![Knop Geavanceerde editor](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. In het dialoogvenster Geavanceerde editor is het getal naast 'Bron' de index.

    ![Geavanceerde editor - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Als u Excel 2013 gebruikt, gebruikt u [Microsoft Power Query voor Excel](https://www.microsoft.com/download/details.aspx?id=39379) om de index op te halen. Zie [Verbinding maken met een webpaginaartikel](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) voor meer informatie. De stappen zijn vergelijkbaar als u [Microsoft Power BI voor bureaublad gebruikt.](https://powerbi.microsoft.com/desktop/)

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
