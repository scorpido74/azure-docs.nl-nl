---
title: Gegevens kopiëren naar/van Azure Blob-opslag | Microsoft Docs
description: 'Informatie over het kopiëren van blob-gegevens in Azure Data Factory. Gebruik ons voorbeeld: Het kopiëren van gegevens naar en vanuit Azure Blob Storage en Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 16d11a707851cdbb3e315c9a6d2fe592a97eca9a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839571"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Blob Storage met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-blob-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-blob-storage.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure Blob Storage-connector in V2](../connector-azure-blob-storage.md).


In dit artikel wordt uitgelegd hoe u de Kopieeractiviteit in Azure Data Factory gebruiken om gegevens te kopiëren naar en van Azure Blob Storage. Dit is gebaseerd op de [activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) artikel een algemeen overzicht van de verplaatsing van gegevens met de kopieeractiviteit geeft.

## <a name="overview"></a>Overzicht
U kunt gegevens kopiëren van een ondersteund brongegevensarchief naar Azure Blob Storage of Azure Blob Storage naar een ondersteunde sink-gegevensopslag. De volgende tabel geeft een lijst van gegevensarchieven die worden ondersteund als bron of PUT voor de kopieeractiviteit. U kunt bijvoorbeeld gegevens verplaatsen **van** een SQL Server-database of een Azure SQL database **naar** een Azure blob-opslag. En u kunt gegevens kopiëren **van** Azure blob-opslag **naar** een Azure SQL Data Warehouse of een Azure Cosmos DB-verzameling.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens kopiëren **uit Azure Blob Storage** opgeslagen in de volgende gegevens:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens kopiëren van de volgende gegevensarchieven **naar Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopieeractiviteit ondersteunt kopiëren van gegevens van/naar Azure Storage-accounts voor algemeen gebruik zowel warme/koude Blob-opslag. De activiteit ondersteunt **lezen vanaf blok, toevoegen of pagina-blobs**, maar ondersteunt **schrijven naar alleen blok-blobs**. Azure Premium Storage wordt niet ondersteund als een sink omdat deze wordt ondersteund door de pagina-blobs.
>
> Kopieeractiviteit worden gegevens niet verwijderd uit de bron nadat de gegevens is gekopieerd naar de bestemming. Als u verwijderen van gegevens na een geslaagde kopieerbewerking wilt, maakt u een [aangepaste activiteit](data-factory-use-custom-activities.md) verwijderen van de gegevens en gebruik van de activiteit in de pijplijn. Zie voor een voorbeeld: de [verwijderen blob of de map voorbeeld op GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Aan de slag
U kunt een pijplijn maken met een kopieeractiviteit die gegevens naar/van een Azure Blob-opslag wordt verplaatst met behulp van verschillende hulpprogramma's / API's.

De eenvoudigste manier om een pijplijn te maken is met de **Kopieerwizard**. In dit artikel bevat een [scenario](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) voor het maken van een pijplijn om gegevens te kopiëren vanaf de locatie van een Azure Blob Storage naar een andere locatie van de Azure Blob Storage. Zie voor een zelfstudie over het maken van een pijplijn om gegevens te kopiëren uit een Azure Blob Storage naar Azure SQL Database, [zelfstudie: Een pijplijn maken met de Wizard kopiëren](data-factory-copy-data-wizard-tutorial.md).

U kunt ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sjabloon**, **.NET API**, en **REST-API**. Zie [zelfstudie Kopieeractiviteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

Of u de hulpprogramma's of API's gebruikt, kunt u de volgende stappen uit voor het maken van een pijplijn die gegevens van een brongegevensarchief naar een sink-gegevensopslag verplaatst uitvoeren:

1. Maak een **gegevensfactory**. Een data factory kan één of meer pijplijnen bevatten.
2. Maak **gekoppelde services** opgeslagen om invoer- en gegevens te koppelen aan uw data factory. Bijvoorbeeld, als u gegevens van een Azure blob-opslag met een Azure SQL-database kopieert, u twee gekoppelde services om uw Azure storage-account en Azure SQL-database koppelen aan uw data factory. Zie voor de gekoppelde service-eigenschappen die specifiek voor Azure Blob Storage zijn, [gekoppelde service-eigenschappen](#linked-service-properties) sectie.
2. Maak **gegevenssets** te vertegenwoordigen invoer- en uitvoergegevens voor de kopieerbewerking. In het voorbeeld dat wordt vermeld in de vorige stap, maakt u een gegevensset om op te geven van de blob-container en map die de invoergegevens bevat. En u maakt een andere gegevensset om op te geven van de SQL-tabel in de Azure SQL-database waarin de gegevens die zijn gekopieerd uit de blob-opslag. Zie voor de gegevensseteigenschappen die specifiek voor Azure Blob Storage zijn, [gegevensseteigenschappen](#dataset-properties) sectie.
3. Maak een **pijplijn** met een kopieeractiviteit waarmee een gegevensset als invoer en een gegevensset als uitvoer. In het voorbeeld eerder vermeld, gebruikt u BlobSource als een bron en SqlSink als sink voor de kopieeractiviteit. Op dezelfde manier als u uit een Azure SQL Database naar Azure Blob Storage kopiëren wilt, gebruikt u SqlSource en BlobSink in de kopieeractiviteit. Zie voor kopiëren-activiteitseigenschappen die specifiek voor Azure Blob Storage zijn, [eigenschappen van de kopieeractiviteit](#copy-activity-properties) sectie. Klik op de koppeling in de vorige sectie voor de gegevensopslag voor meer informatie over het gebruik van een gegevensarchief als een bron of een sink.

Wanneer u de wizard gebruikt, worden de JSON-definities voor deze Data Factory-entiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u hulpprogramma's / API's (met uitzondering van de .NET API), kunt u deze Data Factory-entiteiten definiëren met behulp van de JSON-indeling.  Zie voor voorbeelden met JSON-definities voor Data Factory-entiteiten die worden gebruikt om gegevens te kopiëren naar/van Azure Blob Storage, [JSON voorbeelden](#json-examples-for-copying-data-to-and-from-blob-storage  ) sectie van dit artikel.

De volgende secties bevatten meer informatie over JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke naar Azure Blob Storage.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service
Er zijn twee soorten gekoppelde services, die kunt u een Azure Storage koppelen aan een Azure data factory. Dit zijn: **AzureStorage** gekoppelde service en **AzureStorageSas** gekoppelde service. De gekoppelde Azure Storage-service biedt de data factory met wereldwijde toegang tot Azure Storage. Terwijl de Azure Storage SAS (Shared Access Signature) gekoppelde biedt service de data factory met beperkte/tijdelijke toegang naar Azure Storage. Er zijn geen andere verschillen tussen de twee gekoppelde services. Kies de gekoppelde service die tegemoetkomt aan uw behoeften. De volgende secties vindt u meer informatie over deze twee gekoppelde services.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als een gegevensset die de invoer- of -gegevens in een Azure Blob-opslag opgeven, stelt u de eigenschap type van de gegevensset in: **AzureBlob**. Stel de **linkedServiceName** eigenschap van de gegevensset in de naam van de Azure Storage of Azure Storage SAS gekoppelde service.  De eigenschappen van het type van de gegevensset opgeven die de **blob-container** en de **map** in de blob-opslag.

Zie voor een volledige lijst van JSON-secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets maken](data-factory-create-datasets.md) artikel. Secties, zoals de structuur, beschikbaarheid en het beleid van een gegevensset JSON zijn vergelijkbaar voor alle typen van gegevensset (Azure SQL, Azure-blob, Azure-tabel, enz.).

Data factory ondersteunt de volgende CLS-conform .NET op basis van typewaarden voor het type informatie in "structuur" voor schema-on-read-gegevensbronnen, zoals Azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory wordt automatisch typeconversies voert bij het verplaatsen van gegevens van een brongegevensarchief naar een sink-gegevensopslag.

De **typeProperties** sectie verschilt voor elk type gegevensset en bevat informatie over de locatie, opmaken enz., van de gegevens in het gegevensarchief. De typeProperties sectie voor de gegevensset van het type **AzureBlob** gegevensset heeft de volgende eigenschappen:

| Eigenschap | Description | Verplicht |
| --- | --- | --- |
| folderPath |Pad naar de container en map in de blob-opslag. Voorbeeld: myblobcontainer\myblobfolder\ |Ja |
| fileName |Naam van de blob. Bestandsnaam is optioneel en is hoofdlettergevoelig.<br/><br/>Als u een filename opgeeft, wordt de activiteit (inclusief kopie) werkt op de specifieke Blob.<br/><br/>Als geen bestandsnaam is opgegeven, bevat kopiëren alle Blobs in de folderPath voor invoergegevensset.<br/><br/>Wanneer **fileName** is niet opgegeven voor een uitvoergegevensset en **preserveHierarchy** niet is opgegeven in de activiteit-sink, de naam van het gegenereerde bestand zou worden in de volgende notatie: `Data.<Guid>.txt` (voor Voorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy is een optionele eigenschap. U kunt deze gebruiken om op te geven van een dynamische folderPath en de bestandsnaam voor time series-gegevens. Bijvoorbeeld, folderPath kan worden als parameters gebruikt voor elk uur gegevens. Zie de [partitionedBy eigenschap sectie](#using-partitionedby-property) voor meer informatie en voorbeelden. |Nee |
| format | De volgende bestandsindelingen worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **type** eigenschap onder indeling op een van deze waarden. Zie voor meer informatie, [tekstindeling](data-factory-supported-file-and-compression-formats.md#text-format), [Json-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-indeling](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format), en [Parquet-indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) secties. <br><br> Als u wilt **bestanden als kopiëren-is** overslaan tussen op basis van bestanden (binaire kopie), het gedeelte indeling in beide definities van de gegevensset voor invoer en uitvoer. |Nee |
| compression | Geef het type en het niveau van compressie voor de gegevens. Ondersteunde typen zijn: **GZip**, **Deflate**, **BZip2**, en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimale** en **snelste**. Zie voor meer informatie, [bestands- en compressie indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nee |

### <a name="using-partitionedby-property"></a>Met behulp van de eigenschap partitionedBy
Zoals vermeld in de vorige sectie, kunt u een dynamische folderPath en de bestandsnaam voor time series-gegevens met de **partitionedBy** eigenschap [Data Factory-functies en de systeemvariabelen](data-factory-functions-variables.md).

Zie voor meer informatie over time series-gegevenssets, planning en segmenten [gegevenssets maken](data-factory-create-datasets.md) en [planning en uitvoering](data-factory-scheduling-and-execution.md) artikelen.

#### <a name="sample-1"></a>Voorbeeld 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In dit voorbeeld {segment} wordt vervangen door de opgegeven waarde van de Data Factory systeemvariabele slicestart-waarde in de notatie (YYYYMMDDHH). De slicestart-waarde verwijst naar de begintijd van het segment. FolderPath verschilt voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Voorbeeld 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Jaar, maand, dag en tijd van de slicestart-waarde in dit voorbeeld zijn uitgepakt naar afzonderlijke variabelen die worden gebruikt door de eigenschappen voor folderPath en de bestandsnaam.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie voor een volledige lijst van de secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen maken](data-factory-create-pipelines.md) artikel. Eigenschappen zoals naam, beschrijving, invoer en uitvoer gegevenssets en beleidsregels zijn beschikbaar voor alle soorten activiteiten. Dat eigenschappen die beschikbaar zijn in de **typeProperties** sectie van de activiteit variëren met elk activiteitstype. Ze verschillen voor de kopieeractiviteit, afhankelijk van de typen van bronnen en sinks. Als u gegevens van een Azure Blob-opslag verplaatst, u het brontype instellen in de kopieeractiviteit naar **BlobSource**. Als u gegevens naar een Azure Blob-opslag verplaatst, u stelt het sink-type in de kopieeractiviteit naar **BlobSink**. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door BlobSource en BlobSink.

**BlobSource** ondersteunt de volgende eigenschappen in de **typeProperties** sectie:

| Eigenschap | Description | Toegestane waarden | Verplicht |
| --- | --- | --- | --- |
| recursive |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. |True (standaardwaarde), False |Nee |

**BlobSink** ondersteunt de volgende eigenschappen **typeProperties** sectie:

| Eigenschap | Description | Toegestane waarden | Verplicht |
| --- | --- | --- | --- |
| copyBehavior |Definieert het gedrag kopiëren wanneer de bron BlobSource of bestandssysteem is. |<b>PreserveHierarchy</b>: behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand voor bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><br/><b>FlattenHierarchy</b>: alle bestanden uit de bronmap van het zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde naam. <br/><br/><b>MergeFiles</b>: alle bestanden uit de bronmap naar één bestand worden samengevoegd. Als de naam van de bestands-/ Blob is opgegeven, is de naam van het samengevoegde de opgegeven naam. anders zou worden automatisch gegenereerde naam. |Nee |

**BlobSource** biedt ook ondersteuning voor deze twee eigenschappen voor achterwaartse compatibiliteit.

* **treatEmptyAsNull**: Geeft aan of null of lege tekenreeks behandelen als null-waarde.
* **skipHeaderLineCount** -Hiermee geeft u het aantal regels moeten worden overgeslagen. Het is van toepassing alleen wanneer invoergegevensset gebruikmaakt TextFormat.

Op deze manier **BlobSink** ondersteunt de volgende eigenschap voor achterwaartse compatibiliteit.

* **blobWriterAddHeader**: Geeft aan of een koptekst van de kolomdefinities toevoegen tijdens het schrijven naar een uitvoergegevensset.

Gegevenssets bieden nu ondersteuning voor de volgende eigenschappen die dezelfde functionaliteit worden geïmplementeerd: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

De volgende tabel bevat richtlijnen over het gebruik van de eigenschappen van de nieuwe gegevensset in plaats van deze blob-bron/sink-eigenschappen.

| Een eigenschap van activiteit kopiëren | Eigenschap van de gegevensset |
|:--- |:--- |
| skipHeaderLineCount on BlobSource |skipLineCount als firstRowAsHeader. Regels eerst overgeslagen en wordt de eerste rij gelezen als een header. |
| treatEmptyAsNull op BlobSource |treatEmptyAsNull op invoergegevensset |
| blobWriterAddHeader op BlobSink |firstRowAsHeader op uitvoergegevensset |

Zie [op te geven TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) sectie voor gedetailleerde informatie over deze eigenschappen.

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden
Deze sectie beschrijft het resulterende gedrag van de kopieerbewerking voor de verschillende combinaties van recursieve en copyBehavior waarden.

| recursive | copyBehavior | Resulterende gedrag |
| --- | --- | --- |
| true |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 bestand2 + bestand3 + File4 + 5-bestand inhoud worden samengevoegd in één bestand met automatisch gegenereerde naam |
| false |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |
| false |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/><br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |
| false |mergeFiles |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>De doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + bestand2 inhoud worden samengevoegd in één bestand met automatisch gegenereerde naam. Automatisch gegenereerde naam voor File1<br/><br/>Subfolder1 bestand3 File4 en File5 worden niet doorgevoerd. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: Kopieerwizard gebruiken om gegevens naar/van Blob-opslag te kopiëren
Laten we kijken hoe u snel gegevens kopiëren van/naar een Azure blob-opslag. In dit scenario, bron- en doelserver gegevensopslag van het type: Azure Blob-opslag. De pijplijn in dit scenario kopieert gegevens uit een map naar een andere map in de dezelfde blob-container. In dit scenario is opzettelijk eenvoudig om weer te geven u eigenschappen van instellingen of bij het gebruik van Blob-opslag als een bron of de sink.

### <a name="prerequisites"></a>Vereisten
1. Maak een algemeen **Azure Storage-Account** als u er nog geen hebt. U de blob-opslag gebruiken als zowel **bron** en **bestemming** gegevens opslaan in dit scenario. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-quickstart-create-account.md) voor de stappen voor het maken van een account.
2. Maak een blobcontainer met de naam **adfblobconnector** in de storage-account.
4. Maak een map met de naam **invoer** in de **adfblobconnector** container.
5. Maak een bestand met de naam **emp.txt** met de volgende inhoud en upload het naar de **invoer** map met hulpprogramma's zoals [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>De data factory maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **een resource maken** Klik in de linkerbovenhoek op **Intelligence en analyse**, en klikt u op **Data Factory**.
3. In de **nieuwe data factory** deelvenster:  
    1. Voer **ADFBlobConnectorDF** voor de **naam**. De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de foutmelding: `*Data factory name “ADFBlobConnectorDF” is not available`, wijzigt u de naam van de data factory (bijvoorbeeld yournameADFBlobConnectorDF) en probeert u het opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    2. Selecteer uw Azure-**abonnement**.
    3. Selecteer voor de resourcegroep **Use existing** op een bestaande resourcegroep selecteren (of) selecteren **nieuw** een naam voor een resourcegroep op te geven.
    4. Selecteer een **locatie** voor de gegevensfactory.
    5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.
    6. Klik op **Create**.
3. Wanneer het aanmaken is voltooid, ziet u de blade **Gegevensfactory** zoals op de volgende afbeelding wordt weergegeven:  ![Data factory-startpagina](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>De wizard Kopiëren
1. Klik op de startpagina van Data Factory de **gegevens kopiëren** tegel om te starten **Kopieerwizard van Data** in een afzonderlijk tabblad.  
    
    > [!NOTE]
    > Als u ziet dat de webbrowser is vastgelopen bij "Autoriseren...", uitschakelen/uitschakelen **blokkeren van cookies van derden en sitegegevens** instellen (of) laat dit ingeschakeld en het maken van een uitzondering voor **login.microsoftonline.com**en probeer het vervolgens opnieuw starten van de wizard.
2. Op de pagina **Eigenschappen**:
    1. Voer **CopyPipeline** voor **taaknaam**. Naam van de taak is de naam van de pijplijn in uw gegevensfactory.
    2. Voer een **beschrijving** voor de taak (optioneel).
    3. Voor **uitgebracht van de taak of de planning van taak**, behouden de **regelmatig worden uitgevoerd volgens schema** optie. Als u deze taak slechts één keer in plaats van uitvoeren volgens een schema herhaaldelijk uitvoeren wilt, selecteert u **nu één keer uitvoeren**. Als u selecteert, **nu één keer uitvoeren** optie, een [eenmalige pijplijn](data-factory-create-pipelines.md#onetime-pipeline) wordt gemaakt.
    4. De instellingen voor **terugkerend patroon**. Deze taak wordt uitgevoerd dagelijks tussen de begin- en eindtijden die u in de volgende stap opgeeft.
    5. Wijziging de **datum en-tijd** naar **21-04-2017**.
    6. Wijzig de **einddatum en-tijd** naar **25-04-2017**. U kunt naar het type van de datum in plaats van bladeren op de agenda.
    8. Klik op **Volgende**.
        ![Hulpprogramma voor kopiëren - pagina eigenschappen](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Op de pagina **Brongegevensarchief** klikt u op de tegel **Azure Blob Storage**. U gebruikt deze pagina om het brongegevensarchief op te geven voor de kopieertaak. U kunt een bestaande gekoppelde service van een gegevensarchief gebruiken of een nieuw gegevensarchief opgeven. Voor het gebruik van een bestaande gekoppelde service, selecteert u **van bestaande gekoppelde SERVICES** en selecteer de juiste gekoppelde service.
    ![Hulpprogramma voor kopiëren - pagina van brongegevensarchief](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Op de pagina **Het Azure Blob Storage-account opgeven**:
    1. Behoud de automatisch gegenereerde naam voor **verbindingsnaam**. Naam van de verbinding is de naam van de gekoppelde service van het type: Azure Storage.
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw Azure-abonnement of houden **Alles selecteren** voor **Azure-abonnement**.
    4. Selecteer een **Azure-opslagaccount** uit de lijst met Azure-opslagaccounts die beschikbaar is voor het abonnement dat u hebt geselecteerd. U kunt er ook voor kiezen om in te voeren van de opslagaccountinstellingen handmatig door te selecteren **handmatig invoeren** optie voor de **Accountselectiemethode**.
    5. Klik op **Volgende**.  
        ![Hulpprogramma voor kopiëren - Azure Blob storage-account opgeven](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Op de pagina **Het invoerbestand of de invoermap kiezen**:
    1. Dubbelklik op **adfblobcontainer**.
    2. Selecteer **invoer**, en klikt u op **kiezen**. In dit scenario selecteert u de map invoer. U kunt ook selecteren het bestand Emp.txt te bekijken in de map in plaats daarvan.
        ![Hulpprogramma voor kopiëren - het invoerbestand of invoermap kiezen](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Op de **invoerbestand of invoermap kiezen** pagina:
    1. Bevestig dat de **bestand of map** is ingesteld op **adfblobconnector/input**. Als de bestanden in submappen, bijvoorbeeld 04-2017-01, 2017-04-02 en enzovoort, en geef adfblobconnector/input / {year} / {month} / {day} voor het bestand of map. Wanneer u op TAB buiten het tekstvak drukt, ziet u drie vervolgkeuzelijsten indelingen voor het jaar (jjjj), de maand (MM) en de dag (dd) selecteren.
    2. Stel geen **kopiëren bestand recursief**. Selecteer deze optie om te bladeren door mappen recursief voor bestanden die moeten worden gekopieerd naar de bestemming.
    3. Dit niet doet de **binaire kopie** optie. Selecteer deze optie om uit te voeren van een binaire kopie van het bronbestand naar de bestemming. Selecteer niet voor dit scenario zodat u meer opties in de volgende pagina's kunt zien.
    4. Bevestig dat de **compressietype** is ingesteld op **geen**. Selecteer een waarde voor deze optie als de bronbestanden in een van de ondersteunde indelingen zijn gecomprimeerd.
    5. Klik op **Volgende**.
    ![Hulpprogramma voor kopiëren - het invoerbestand of invoermap kiezen](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Op de pagina **Bestandsinstellingen** ziet u de scheidingstekens en het schema dat automatisch is gedetecteerd door de wizard tijdens het parseren van het bestand.
    1. Controleer of de volgende opties:  
        a. De **bestandsindeling** is ingesteld op **tekstindeling**. Hier ziet u alle ondersteunde indelingen in de vervolgkeuzelijst. Bijvoorbeeld: JSON, Avro, ORC, Parquet.
       b. De **kolomscheidingsteken** is ingesteld op `Comma (,)`. Hier ziet u de andere kolomscheidingstekens ondersteund door Data Factory in de vervolgkeuzelijst. U kunt ook een aangepast scheidingsteken opgeven.
       c. De **rijscheidingsteken** is ingesteld op `Carriage Return + Line feed (\r\n)`. Hier ziet u de andere rij-scheidingstekens ondersteund door Data Factory in de vervolgkeuzelijst. U kunt ook een aangepast scheidingsteken opgeven.
       d. De **aantal regels overslaan** is ingesteld op **0**. Als u wilt dat een paar regels aan de bovenkant van het bestand worden overgeslagen, voert u hier het nummer.
       e. De **eerste rij bevat de namen van kolommen** is niet ingesteld. Als de bronbestanden de namen van kolommen in de eerste rij bevat, selecteert u deze optie.
       f. De **behandelen van lege kolomwaarde als null** optie is ingesteld.
    2. Vouw **geavanceerde instellingen** om te zien van geavanceerde optie beschikbaar.
    3. Aan de onderkant van de pagina, Zie de **preview** van gegevens uit het bestand Emp.txt te bekijken.
    4. Klik op **SCHEMA** tabblad aan de onderkant om te zien van het schema dat de wizard kopiëren die zijn afgeleid door te kijken naar de gegevens in het bronbestand.
    5. Klik op **Volgende** nadat u de scheidingstekens hebt gecontroleerd en een voorbeeld van de gegevens hebt bekeken.
    ![Hulpprogramma voor kopiëren - bestandsindelingsinstellingen](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Op de **pagina doelgegevensarchief opslaan**, selecteer **Azure Blob Storage**, en klikt u op **volgende**. U de Azure Blob-opslag gebruiken als zowel de bron- en gegevensarchieven in dit scenario.  
    ![Hulpprogramma voor kopiëren - gegevensarchief van de doelserver selecteren](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Op **Azure Blob storage-account opgeven** pagina:  
    1. Voer **AzureStorageLinkedService** voor de **verbindingsnaam** veld.
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw Azure-**abonnement**.
    4. Selecteer uw Azure storage-account.
    5. Klik op **Volgende**.
10. Op de **uitvoerbestand of uitvoermap kiezen** pagina:  
    1. Geef **mappad** als **adfblobconnector/output / {year} / {month} / {day}** . Enter **TAB**.
    1. Voor de **jaar**, selecteer **jjjj**.
    1. Voor de **maand**, controleert u dat deze is ingesteld op **MM**.
    1. Voor de **dag**, controleert u dat deze is ingesteld op **dd**.
    1. Bevestig dat de **compressietype** is ingesteld op **geen**.
    1. Bevestig dat de **gedrag kopiëren** is ingesteld op **-bestanden samenvoegen**. Als het uitvoerbestand met dezelfde naam al bestaat, wordt de nieuwe inhoud toegevoegd aan hetzelfde bestand aan het einde.
    1. Klik op **Volgende**.
       ![Hulpprogramma voor kopiëren - uitvoerbestand of uitvoermap kiezen](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Op de **bestandsindelingsinstellingen** pagina, Controleer de instellingen en klik op **volgende**. Een van de aanvullende opties hier is het toevoegen van een koptekst naar het uitvoerbestand. Als u deze optie selecteert, wordt een rij met koppen toegevoegd door de namen van de kolommen van het schema van de bron. U kunt de standaardkolomnamen wijzigen bij het weergeven van het schema voor de bron. U kunt wijzigen de eerste kolom bijvoorbeeld de naam van de eerste en tweede kolom op achternaam. Vervolgens is het uitvoerbestand gegenereerd met een header met deze namen als kolomnamen.
    ![Hulpprogramma voor kopiëren - bestandsindelingsinstellingen voor doel](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Op de **prestatie-instellingen** pagina, controleert **eenheden in de cloud** en **parallelle exemplaren** zijn ingesteld op **automatisch**, en klik op volgende. Zie voor meer informatie over deze instellingen [en afstemmingshandleiding van activiteit kopiëren](data-factory-copy-activity-performance.md#parallel-copy).
    ![Hulpprogramma voor kopiëren - prestatie-instellingen](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Op de **samenvatting** pagina, Controleer de instellingen (taakeigenschappen, instellingen voor de bron en bestemming en instellingen) en klikt u op **volgende**.
    ![Hulpprogramma voor kopiëren - overzichtspagina](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Lees de informatie op de pagina **Samenvatting** en klik op **Voltooien**. De wizard maakt twee gekoppelde services, twee gegevenssets (invoer en uitvoer) en één pijplijn in de gegevensfactory (van waaruit u de wizard Kopiëren hebt gestart).
    ![Hulpprogramma voor kopiëren - pagina implementatie](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Bewaken van de pijplijn (kopieertaak)

1. Klik op de koppeling `Click here to monitor copy pipeline` op de **implementatie** pagina.
2. U ziet de **controleren en beheren van de toepassing** in een afzonderlijk tabblad.  ![Controleren en beheren van App](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Wijziging de **start** tijd aan de bovenkant op `04/19/2017` en **end** introductie op de `04/27/2017`, en klik vervolgens op **toepassen**.
4. U ziet vijf activiteitsvensters in de **ACTIVITEITSVENSTERS** lijst. De **WindowStart** tijden moeten betrekking hebben op alle dagen vanaf het begin van de pijplijn tot de eindtijd van een pijplijn.
5. Klik op **vernieuwen** knop voor de **ACTIVITEITSVENSTERS** lijst met een paar keer tot u de status van alle activiteitsvensters is ingesteld op gereed.
6. Controleer nu of dat de uitvoerbestanden worden gegenereerd in de map voor uitvoer van adfblobconnector container. Hier ziet u de volgende mapstructuur, in de map voor uitvoer:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Zie voor gedetailleerde informatie over het controleren en beheren van data factory's [bewaken en beheren van Data Factory-pijplijn](data-factory-monitor-manage-app.md) artikel.

### <a name="data-factory-entities"></a>Data Factory-entiteiten
Nu gaat u terug naar het tabblad met de Data Factory-startpagina. U ziet dat er twee gekoppelde services, twee gegevenssets en één pijplijn in uw data factory nu.

![Data Factory-startpagina met entiteiten](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klik op **auteur en implementeer** Data Factory-Editor gestart.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

U ziet de volgende Data Factory-entiteiten in uw data factory:

- Twee gekoppelde services. Een voor de bron en de andere is voor de bestemming. De gekoppelde services verwijzen naar hetzelfde Azure Storage-account in dit scenario.
- Twee gegevenssets. Een invoergegevensset en een uitvoergegevensset. In dit scenario, beide dezelfde blobcontainer gebruikt maar verwijzen naar verschillende mappen (invoer en uitvoer).
- Een pijplijn. De pijplijn bevat een kopieeractiviteit die gebruikmaakt van een blob-bron- en een blob om gegevens te kopiëren vanuit een Azure blob-locatie naar een andere Azure blob-locatie.

De volgende secties bevatten meer informatie over deze entiteiten.

#### <a name="linked-services"></a>Gekoppelde services
Hier ziet u twee gekoppelde services. Een voor de bron en de andere is voor de bestemming. In dit scenario uitzien zowel definities, met uitzondering van de namen. De **type** van de gekoppelde service is ingesteld op **AzureStorage**. Belangrijkste eigenschap van de definitie van de gekoppelde service is de **connectionString**, die wordt gebruikt door Data Factory verbinding maken met uw Azure Storage-account tijdens runtime. De eigenschap hubName in het definitie negeren.

##### <a name="source-blob-storage-linked-service"></a>Bron-blob storage-gekoppelde service
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Bestemming blob storage-gekoppelde service

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Zie voor meer informatie over Azure Storage gekoppelde service, [gekoppelde service-eigenschappen](#linked-service-properties) sectie.

#### <a name="datasets"></a>Gegevenssets
Er zijn twee gegevenssets: een invoergegevensset en een uitvoergegevensset. Het type van de gegevensset is ingesteld op **AzureBlob** voor beide.

De invoergegevensset verwijst naar de **invoer** map van de **adfblobconnector** blob-container. De **externe** eigenschap is ingesteld op **waar** voor deze dataset omdat de gegevens niet wordt geproduceerd door de pijplijn met de kopieeractiviteit die met deze gegevensset als invoer.

De uitvoergegevensset verwijst naar de **uitvoer** map van de dezelfde blobcontainer. De uitvoergegevensset gebruikt ook het jaar, maand en dag van de **SliceStart** systeemvariabele het pad naar het uitvoerbestand dynamisch te evalueren. Zie voor een lijst met functies en systeemvariabelen ondersteund door Data Factory, [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md). De **externe** eigenschap is ingesteld op **false** (standaardwaarde) omdat deze gegevensset wordt geproduceerd door de pijplijn.

Zie voor meer informatie over de eigenschappen die worden ondersteund door Azure Blob-gegevensset, [gegevensseteigenschappen](#dataset-properties) sectie.

##### <a name="input-dataset"></a>Invoergegevensset

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Uitvoergegevensset

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pijplijn
De pijplijn heeft slechts één activiteit. De **type** van de activiteit is ingesteld op **kopie**. In de type-eigenschappen voor de activiteit zijn er twee secties, één voor de bron en de andere is voor sink. Het brontype is ingesteld op **BlobSource** als de activiteit van gegevens van een blob-opslag kopiëren. Het sink-type is ingesteld op **BlobSink** als de activiteit gegevens kopiëren naar een blob-opslag. De kopieeractiviteit wordt InputDataset-z4y als invoer en OutputDataset-z4y als de uitvoer.

Zie voor meer informatie over de eigenschappen die worden ondersteund door BlobSource en BlobSink [eigenschappen van de Kopieeractiviteit](#copy-activity-properties) sectie.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-voorbeelden voor het kopiëren van gegevens naar en van Blob-opslag
De volgende voorbeelden geven een voorbeeld van JSON-definities die u gebruiken kunt voor het maken van een pijplijn met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe het kopiëren van gegevens naar en vanuit Azure Blob Storage en Azure SQL Database. Echter, de gegevens kunnen worden gekopieerd **rechtstreeks** uit een van de bronnen aan een van de vermelde sinks [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieeractiviteit in Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-voorbeeld: Gegevens kopiëren van Blob Storage naar SQL Database
Het volgende voorbeeld laat zien:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](#linked-service-properties).
3. Invoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
4. Uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die gebruikmaakt van [BlobSource](#copy-activity-properties) en [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

De voorbeeld-kopieën time series-gegevens van een Azure blob naar een Azure SQL tabel per uur. De JSON-eigenschappen die in deze voorbeelden worden beschreven in de secties na de voorbeelden.

**Azure SQL gekoppelde service:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Storage-service:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. U de verbindingsreeks met de accountsleutel opgeven voor de eerste versie, en voor de latere versie, geeft u de Shared Access Signature (SAS)-Uri. Zie [gekoppelde Services](#linked-service-properties) sectie voor meer informatie.

**Azure Blob-invoergegevensset:**

Gegevens wordt opgehaald uit een nieuwe blob elk uur (frequentie: uur en interval: 1). Pad en naam van de map voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Pad naar de map gebruikt jaar, maand en dag deel uit van de begintijd en de bestandsnaam wordt gebruikt voor het uur deel van de begintijd. "extern": "true" instelling Data Factory informeert dat de tabel bevindt zich buiten de data factory en niet door een activiteit in de data factory wordt geproduceerd.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL-uitvoergegevensset:**

De voorbeeldgegevens van de exemplaren in een tabel met de naam "MyTable" in een Azure SQL database. Als u verwacht de Blob-CSV-bestand dat bevat, moet u de tabel maken in uw Azure SQL-database met hetzelfde aantal kolommen. Nieuwe rijen zijn toegevoegd aan de tabel om het uur.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Een kopieeractiviteit in een pijplijn met Blob-bron en sink voor SQL:**

De pijplijn bevat een Kopieeractiviteit die is geconfigureerd voor het gebruik van de invoer- en uitvoergegevenssets en is gepland voor elk uur uitgevoerd. In de pijplijn-JSON-definitie heeft de **bron** type is ingesteld op **BlobSource** en **sink** type is ingesteld op **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-voorbeeld: Gegevens kopiëren van Azure SQL naar Azure Blob
Het volgende voorbeeld laat zien:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](#linked-service-properties).
3. Invoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met de kopieeractiviteit die gebruikmaakt van [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) en [BlobSink](#copy-activity-properties).

Het voorbeeld tijdreeksen worden gegevens gekopieerd van een Azure SQL-tabel naar een Azure-blob per uur. De JSON-eigenschappen die in deze voorbeelden worden beschreven in de secties na de voorbeelden.

**Azure SQL gekoppelde service:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Gekoppelde Azure Storage-service:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. U de verbindingsreeks met de accountsleutel opgeven voor de eerste versie, en voor de latere versie, geeft u de Shared Access Signature (SAS)-Uri. Zie [gekoppelde Services](#linked-service-properties) sectie voor meer informatie.

**Azure SQL-invoer gegevensset:**

Het voorbeeld wordt ervan uitgegaan dat u hebt een tabel 'MyTable' gemaakt in Azure SQL en bevat een kolom met de naam 'timestampcolumn' voor time series-gegevens.

Instellen van "extern": "true" informeert Data Factory-service dat de tabel bevindt zich buiten de data factory en niet door een activiteit in de data factory wordt geproduceerd.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob-uitvoergegevensset:**

Gegevens worden geschreven naar een nieuwe blob elk uur (frequentie: uur en interval: 1). Het pad naar de map voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Pad naar de map wordt gebruikt voor jaar, maand, dag en uur onderdelen van de begintijd.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Een kopieeractiviteit in een pijplijn met de SQL-bron en sink voor Blob:**

De pijplijn bevat een Kopieeractiviteit die is geconfigureerd voor het gebruik van de invoer- en uitvoergegevenssets en is gepland voor elk uur uitgevoerd. In de pijplijn-JSON-definitie heeft de **bron** type is ingesteld op **SqlSource** en **sink** type is ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **SqlReaderQuery** eigenschap selecteert u de gegevens in het afgelopen uur te kopiëren.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> Zie het toewijzen van kolommen in de brongegevensset op kolommen uit de sink-gegevensset [toewijzing van kolommen in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie [prestaties kopiëren en Afstemmingshandleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed prestaties van de verplaatsing van gegevens (Kopieeractiviteit) in Azure Data Factory en de verschillende manieren om te optimaliseren.
