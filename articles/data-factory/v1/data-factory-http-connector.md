---
title: Gegevens verplaatsen van een HTTP-bron - Azure
description: Meer informatie over het verplaatsen van gegevens uit een on-premises of cloud HTTP-bron met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 36592151385a08d75b9b34e85bfa9d62342fc8cd
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991566"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Gegevens uit een HTTP-bron verplaatsen met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-http-connector.md)
> * [Versie 2 (huidige versie)](../connector-http.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [HTTP-connector in V2](../connector-http.md)als u de huidige versie van de Azure Data Factory-service gebruikt.


In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van een on-premises of cloud HTTP-eindpunt naar een ondersteund sink-gegevensarchief te verplaatsen. Dit artikel bouwt voort op [Move-gegevens met behulp van Kopieeractiviteit](data-factory-data-movement-activities.md), die een algemeen overzicht van gegevensverkeer weergeeft met behulp van Kopieeractiviteit. Het artikel bevat ook de gegevensarchieven die Kopieeractiviteit ondersteunt als bronnen en putten.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een HTTP-bron naar andere gegevensopslag. Het ondersteunt geen verplaatsing van gegevens van andere gegevensarchieven naar een HTTP-bestemming.

## <a name="supported-scenarios-and-authentication-types"></a>Ondersteunde scenario's en verificatietypen

U deze HTTP-connector gebruiken om gegevens uit *zowel een cloud als een on-premises HTTP/S-eindpunt* op te halen met behulp van de HTTP **GET-** of **POST-methoden.** De volgende verificatietypen worden ondersteund: **Anoniem,** **Basic**, **Digest,** **Windows**en **ClientCertificate**. Let op het verschil tussen deze connector en de [webtafelconnector](data-factory-web-table-connector.md). Met de connector voor de webtabel wordt tabelinhoud uit een HTML-webpagina geëxtraheerd.

Wanneer u gegevens kopieert van een on-premises HTTP-eindpunt, moet u Data Management Gateway installeren in de on-premises omgeving of in een Azure VM. Zie [Gegevens verplaatsen tussen on-premises locaties en de cloud](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over de gateway en stapsgewijze instructies voor het instellen van de gateway.

## <a name="get-started"></a>Aan de slag

U een pijplijn maken met een kopieeractiviteit om gegevens uit een HTTP-bron te verplaatsen met behulp van verschillende hulpprogramma's of API's:

- De eenvoudigste manier om een pijplijn te maken, is door de wizard Gegevens kopiëren te gebruiken. Zie [Zelfstudie: Een pijplijn maken met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle doorloop van het maken van een pijplijn met de wizard Gegevens kopiëren .

- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: de **Visual Studio,** **Azure PowerShell,** een **Azure Resource Manager-sjabloon,** de **.NET-API**of de **REST-API**. Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit. Zie [JSON-voorbeelden](#json-examples)voor JSON-voorbeelden die gegevens uit een HTTP-bron naar Azure Blob-opslag kopiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

In de volgende tabel worden JSON-elementen beschreven die specifiek zijn voor de gekoppelde HTTP-service:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type | De eigenschap **type** moet zijn ingesteld op **Http**. | Ja |
| url | De basis-URL naar de webserver. | Ja |
| authenticationType | Hiermee geeft u het verificatietype op. Toegestane waarden zijn **Anoniem,** **Basis,** **Samenvatting,** **Windows**en **ClientCertificate**. <br><br> Raadpleeg latere secties in dit artikel voor meer eigenschappen en JSON-voorbeelden voor deze verificatietypen. | Ja |
| enableServerCertificateValidation | Hiermee geeft u op of serverTLS/SSL-certificaatvalidatie moet worden ingeschakeld als de bron een HTTPS-webserver is. Wanneer uw HTTPS-server een zelfondertekend certificaat gebruikt, stelt u dit in op **false**. | Nee<br /> (de standaardinstelling is **waar)** |
| gatewayNaam | De naam van de instantie Data Management Gateway die moet worden gebruikt om verbinding te maken met een on-premises HTTP-bron. | Ja, als u gegevens kopieert van een on-premises HTTP-bron |
| versleuteldCredential | De versleutelde referenties voor toegang tot het HTTP-eindpunt. De waarde wordt automatisch gegenereerd wanneer u de verificatiegegevens configureert in de wizard Kopiëren of met het dialoogvenster **ClickOnce.** | Nee<br /> (alleen van toepassing wanneer u gegevens kopieert van een on-premises HTTP-server) |

Zie [Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over het instellen van referenties voor een on-premises HTTP-connectorgegevensbron.

### <a name="using-basic-digest-or-windows-authentication"></a>Basis-, Digest- of Windows-verificatie gebruiken

**Verificatietype** instellen op **Basic,** **Digest**of **Windows**. Stel naast de algemene HTTP-connectoreigenschappen die in de voorgaande secties zijn beschreven, de volgende eigenschappen in:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| userName | De gebruikersnaam die u wilt gebruiken om toegang te krijgen tot het HTTP-eindpunt. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker **(gebruikersnaam).** | Ja |

**Voorbeeld: Basis-, Digest- of Windows-verificatie gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate-verificatie gebruiken

Als u basisverificatie wilt gebruiken, stelt u **verificatieType** in **op ClientCertificate**. Stel naast de algemene HTTP-connectoreigenschappen die in de voorgaande secties zijn beschreven, de volgende eigenschappen in:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| embeddedCertData | De Base64-gecodeerde inhoud van binaire gegevens van het PFX-bestand. | Ingesloten **CertData** of **certThumbprint opgeven** |
| certThumbprint | De duimafdruk van het certificaat dat is geïnstalleerd in de cert-winkel van uw gatewaymachine. Pas alleen toe wanneer u gegevens kopieert van een on-premises HTTP-bron. | Ingesloten **CertData** of **certThumbprint opgeven** |
| wachtwoord | Het wachtwoord dat aan het certificaat is gekoppeld. | Nee |

Als u **certThumbprint** gebruikt voor verificatie en het certificaat is geïnstalleerd in de persoonlijke winkel van de lokale computer, verleent u leesmachtigingen aan de gatewayservice:

1. Open de Microsoft Management Console (MMC). Voeg de module **Certificaten** toe die is gericht op **lokale computer**.
2. **Certificaten** > **persoonlijk**uitvouwen en selecteer **certificaten**.
3. Klik met de rechtermuisknop op het certificaat in de persoonlijke winkel en selecteer **Alle taken** >**Privésleutels beheren**.
3. Voeg op het tabblad **Beveiliging** het gebruikersaccount toe waaronder de Data Management Gateway Host Service wordt uitgevoerd, met leestoegang tot het certificaat.  

**Voorbeeld: een clientcertificaat gebruiken**

Deze gekoppelde service koppelt uw gegevensfabriek aan een on-premises HTTP-webserver. Het maakt gebruik van een clientcertificaat dat is geïnstalleerd op de machine waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Voorbeeld: een clientcertificaat gebruiken in een bestand**

Deze gekoppelde service koppelt uw gegevensfabriek aan een on-premises HTTP-webserver. Het maakt gebruik van een clientcertificaatbestand op de machine waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Sommige secties van een JSON-bestand met gegevensset, zoals structuur, beschikbaarheid en beleid, zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL Database, Azure Blob-opslag, Azure Table-opslag).

Zie [Gegevenssets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset. De sectie **typeEigenschappen** geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor een gegevensset van het **http-type** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het **type** gegevensset moet worden ingesteld op **Http**. | Ja |
| relativenurl | Een relatieve URL naar de bron die de gegevens bevat. Wanneer het pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de gekoppelde servicedefinitie. <br><br> Als u een dynamische URL wilt samenstellen, u [de functies Data Factory en systeemvariabelen](data-factory-functions-variables.md)gebruiken. Voorbeeld: **relativeurl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | Nee |
| requestMethode | De HTTP-methode. Toegestane waarden zijn **GET** en **POST**. | Nee <br />(standaard is **GET)** |
| extra Kopteksten | Aanvullende HTTP-aanvraagkoppen. | Nee |
| requestBody | De instantie voor het HTTP-verzoek. | Nee |
| formaat | Als u *de gegevens wilt ophalen uit een HTTP-eindpunt zonder* deze te ontleed, slaat u de **indelingsinstelling** over. <br><br> Als u de HTTP-antwoordinhoud tijdens de kopie wilt ontleden, worden de volgende opmaaktypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Zie [Tekstindeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-formaat](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formaat](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-formaat](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parketformaat](data-factory-supported-file-and-compression-formats.md#parquet-format)voor meer informatie . |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus: **optimaal** en **snelst.** Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

**Voorbeeld: de methode GET (standaard) gebruiken**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Voorbeeld: De methode POST gebruiken**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

Zie [Pijplijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit, verschillen per activiteitstype. Voor een kopieeractiviteit zijn de eigenschappen afhankelijk van de typen bronnen en putten.

Op dit moment worden de volgende eigenschappen ondersteund wanneer de bron in Kopieeractiviteit van het **type HttpSource** is:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| httpRequestTime-out | De time-out (de **TimeSpan-waarde)** voor het HTTP-verzoek om een antwoord te krijgen. Het is de time-out om een antwoord te krijgen, niet de time-out om reactiegegevens te lezen. | Nee<br />(standaardwaarde: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie

Zie [Bestands- en compressie-indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md) voor meer informatie.

## <a name="json-examples"></a>VOORBEELDEN VAN JSON

In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) In de voorbeelden wordt uitgelegd hoe u gegevens van een HTTP-bron kopiëren naar Azure Blob-opslag. Gegevens kunnen echter *rechtstreeks* worden gekopieerd van een van de bronnen naar een van de sinks [die worden ondersteund](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met kopieeractiviteit in Azure Data Factory.

**Voorbeeld: Gegevens uit een HTTP-bron kopiëren naar Azure Blob-opslag**

De oplossing Gegevensfabriek voor dit voorbeeld bevat de volgende entiteiten in gegevensfabrieken:

*   Een gekoppelde service van het type [HTTP](#linked-service-properties).
*   Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Een [invoergegevensset](data-factory-create-datasets.md) van het type [Http](#dataset-properties).
*   Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [httpsource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens uit een HTTP-bron naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

### <a name="http-linked-service"></a>HTTP-gekoppelde service

In dit voorbeeld wordt de http-gekoppelde service gebruikt met anonieme verificatie. Zie [HTTP-gekoppelde service](#linked-service-properties) voor verschillende soorten verificatie die u gebruiken.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Gekoppelde Azure-opslagservice

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP-invoergegevensset

Als **u extern** naar true **instelt,** wordt de service Gegevensfabriek geïnformeerd dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe blob geschreven **(frequentie**: **uur**, **interval**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pijplijn die een kopieeractiviteit gebruikt

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken. De kopieeractiviteit wordt elk uur uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **HttpSource** en wordt het **gootsteentype** ingesteld op **BlobSink**.

Zie [HttpSource](#copy-activity-properties)voor de lijst met eigenschappen die **HttpSource** ondersteunt.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen van een brongegevensset wilt toewijzen aan kolommen uit een sink-set.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

Zie de [handleiding activiteits- en afstemmingshandleiding kopiëren](data-factory-copy-activity-performance.md)voor meer informatie over de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren.
