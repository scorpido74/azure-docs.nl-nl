---
title: Gegevens kopiëren naar/vanuit Azure Blob Storage
description: 'Meer informatie over het kopiëren van blobgegevens in Azure Data Factory. Gebruik ons voorbeeld: Gegevens kopiëren van en naar Azure Blob Storage en Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282131"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Gegevens kopiëren naar of vanuit Azure Blob Storage met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-blob-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-blob-storage.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure Blob Storage-connector in V2](../connector-azure-blob-storage.md)als u de huidige versie van de service Data Factory gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar Azure Blob Storage te kopiëren. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

## <a name="overview"></a>Overzicht
U gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Blob Storage of vanuit Azure Blob Storage naar een ondersteund sink-gegevensarchief. In de volgende tabel vindt u een lijst met gegevensarchieven die worden ondersteund als bronnen of putten door de kopieeractiviteit. U bijvoorbeeld gegevens **verplaatsen van** een SQL Server-database of een Azure SQL-database **naar** een Azure blob-opslag. En u gegevens **uit** Azure blob-opslag kopiëren **naar** een Azure SQL Data Warehouse of een Azure Cosmos DB-verzameling.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit Azure Blob Storage** kopiëren naar de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar Azure Blob Storage:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Copy Activity ondersteunt het kopiëren van gegevens van/naar zowel Azure Storage-accounts voor algemene doeleinden als Hot/Cool Blob-opslag. De activiteit ondersteunt **lezen vanaf blok-, app-toe- of paginablobs,** maar ondersteunt **het schrijven om alleen blobs te blokkeren.** Azure Premium Storage wordt niet ondersteund als een sink omdat deze wordt ondersteund door paginablobs.
>
> Als activiteit wordt gekopieerd, worden gegevens niet uit de bron verwijderd nadat de gegevens naar de bestemming zijn gekopieerd. Als u brongegevens na een geslaagde kopie moet verwijderen, maakt u een [aangepaste activiteit](data-factory-use-custom-activities.md) om de gegevens te verwijderen en de activiteit in de pijplijn te gebruiken. Zie bijvoorbeeld het voorbeeld van blob [of map verwijderen op GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een Azure Blob-opslag verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. In dit artikel wordt een [walkthrough](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) gemaakt voor het maken van een pijplijn om gegevens van een Azure Blob Storage-locatie naar een andere Azure Blob Storage-locatie te kopiëren. Zie [Zelfstudie: Een pijplijn maken met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een zelfstudie over het maken van een pijplijn om gegevens uit een Azure Blob Storage naar Azure SQL Database te kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Azure blob-opslag naar een Azure SQL-database, maakt u twee gekoppelde services om uw Azure-opslagaccount en Azure SQL-database te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde service-eigenschappen](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure Blob Storage.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de blobcontainer en -map op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de SQL-tabel op te geven in de Azure SQL-database met de gegevens die zijn gekopieerd uit de blob-opslag. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor Azure Blob Storage.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u BlobSource als bron en SqlSink als een sink voor de kopieeractiviteit. Als u van Azure SQL Database naar Azure Blob Storage kopieert, gebruikt u SqlSource en BlobSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor Azure Blob Storage. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeelden](#json-examples-for-copying-data-to-and-from-blob-storage  ) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in gegevensfabriek die worden gebruikt om gegevens naar/vanuit een Azure Blob-opslag te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Azure Blob Storage.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Er zijn twee typen gekoppelde services die u gebruiken om een Azure Storage te koppelen aan een Azure-gegevensfabriek. Dit zijn: **AzureStorage** linked service en **AzureStorageSas** linked service. De gekoppelde Azure Storage-service biedt de gegevensfabriek algemene toegang tot de Azure Storage. Terwijl de gekoppelde service Azure Storage SAS (Shared Access Signature) de gegevensfabriek beperkte/tijdgebonden toegang biedt tot de Azure Storage. Er zijn geen andere verschillen tussen deze twee gekoppelde diensten. Kies de gekoppelde service die bij uw behoeften past. De volgende secties geven meer details over deze twee gekoppelde diensten.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoer- of uitvoergegevens in een Azure Blob-opslag weer te geven, stelt u de eigenschap type van de gegevensset in op: **AzureBlob**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de SAS-gekoppelde service Azure Storage of Azure Storage.  De teksteigenschappen van de gegevensset geven de **blobcontainer** en de **map** in de blobopslag op.

Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met JSON-secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

Gegevensfabriek ondersteunt de volgende CLS-compatibele .NET-gebaseerde typewaarden voor het verstrekken van tekstinformatie in "structuur" voor schema-on-read-gegevensbronnen zoals Azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datumtijdcompensatie, Tijdspanne. Data Factory voert automatisch typeconversies uit bij het verplaatsen van gegevens van een brongegevensarchief naar een sink datastore.

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie, indeling enz., van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van het type **AzureBlob-gegevensset** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de container en map in de blobopslag. Voorbeeld: myblobcontainer\myblobfolder\ |Ja |
| fileName |Naam van de blob. fileName is optioneel en hoofdlettergevoelig.<br/><br/>Als u een bestandsnaam opgeeft, werkt de activiteit (inclusief Kopiëren) op de specifieke Blob.<br/><br/>Wanneer bestandsnaam niet is opgegeven, bevat Kopiëren alle Blobs in de mapPath voor invoergegevensset.<br/><br/>Wanneer **bestandsnaam** niet is opgegeven voor een uitvoergegevensset en **behoudenHiërarchie** niet is opgegeven in activiteitssink, is de naam van het gegenereerde bestand in de volgende indeling: `Data.<Guid>.txt` (bijvoorbeeld: :: Gegevens.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy is een optionele eigenschap. U deze gebruiken om een dynamische mapPath en bestandsnaam op te geven voor tijdreeksgegevens. MapPath kan bijvoorbeeld worden geparameteriseerd voor elk uur aan gegevens. Zie de [sectie PartitionedBy gebruiken](#using-partitionedby-property) voor meer informatie en voorbeelden. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

### <a name="using-partitionedby-property"></a>PartitionedBy-eigenschap gebruiken
Zoals in de vorige sectie is vermeld, u een dynamische mapPath en bestandsnaam opgeven voor tijdreeksgegevens met de eigenschap **partitionedBy,** [de functies Gegevensfabriek en de systeemvariabelen](data-factory-functions-variables.md).

Zie Gegevenssets maken en [plannen & uitvoering](data-factory-scheduling-and-execution.md) voor meer informatie over gegevenssets, planningen en segmenten van tijdreeksen. [Creating Datasets](data-factory-create-datasets.md)

#### <a name="sample-1"></a>Voorbeeld 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In dit voorbeeld wordt {Slice} vervangen door de waarde van Data Factory-systeemvariabele SliceStart in de opgegeven indeling (YYYYMMDDHH). De SliceStart verwijst naar de begintijd van het segment. De mapPath is verschillend voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104

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

In dit voorbeeld worden jaar, maand, dag en tijd van SliceStart geëxtraheerd in afzonderlijke variabelen die worden gebruikt door mapPath- en fileName-eigenschappen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoergegevenssets en beleidsregels zijn beschikbaar voor alle soorten activiteiten. Overwegende dat de eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten. Als u gegevens verplaatst vanuit een Azure Blob Storage, stelt u het brontype in de kopieeractiviteit in op **BlobSource**. Als u gegevens verplaatst naar een Azure Blob Storage, stelt u het gootsteentype in de kopieeractiviteit in op **BlobSink**. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door BlobSource en BlobSink.

**BlobSource** ondersteunt de volgende eigenschappen in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True (standaardwaarde), Onwaar |Nee |

**BlobSink** ondersteunt de volgende **eigenschappentypeEigenschappen** sectie:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee definieert u het kopieergedrag wanneer de bron BlobSource of FileSystem is. |<b>BehoudenHiërarchie:</b>behoudt de bestandshiërarchie in de doelmap. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><br/><b>FlattenHierarchy:</b>alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben een automatisch gegenereerde naam. <br/><br/><b>MergeFiles</b>: voegt alle bestanden uit de bronmap samen tot één bestand. Als de bestandsnaam/blob is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. anders zou worden automatisch gegenereerde bestandsnaam. |Nee |

**BlobSource** ondersteunt ook deze twee eigenschappen voor achterwaartse compatibiliteit.

* **treatEmptyAsNull**: Hiermee geeft u aan of null of lege tekenreeks als null-waarde moet worden behandeld.
* **skipHeaderLineCount** - Hiermee geeft u aan hoeveel regels moeten worden overgeslagen. Het is alleen van toepassing wanneer invoergegevensset TextFormat gebruikt.

Op dezelfde manier ondersteunt **BlobSink** de volgende eigenschap voor achterwaartse compatibiliteit.

* **blobWriterAddHeader:** hiermee geeft u op of u een kop met kolomdefinities wilt toevoegen tijdens het schrijven aan een uitvoergegevensset.

Gegevenssets ondersteunen nu de volgende eigenschappen die dezelfde functionaliteit implementeren: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

In de volgende tabel vindt u richtlijnen voor het gebruik van de nieuwe gegevensseteigenschappen in plaats van deze blobbron/sink-eigenschappen.

| Activiteit kopiëren, eigenschap | De eigenschap Dataset |
|:--- |:--- |
| skipHeaderLineCount op BlobSource |skipLineCount en firstRowAsHeader. Lijnen worden eerst overgeslagen en vervolgens wordt de eerste rij gelezen als een koptekst. |
| treatEmptyAsNull op BlobSource |treatEmptyAsNull op invoergegevensset |
| BlobWriterAddHeader op BlobSink |firstRowAsHeader op uitvoergegevensset |

Zie [Sectie TextFormat opgeven](data-factory-supported-file-and-compression-formats.md#text-format) voor gedetailleerde informatie over deze eigenschappen.

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden
In deze sectie wordt het resulterende gedrag van de bewerking Kopiëren beschreven voor verschillende combinaties van recursieve en copyBehavior-waarden.

| Recursieve | copyBehavior | Resulterend gedrag |
| --- | --- | --- |
| waar |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5. |
| waar |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam |
| false |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: Wizard kopiëren gebruiken om gegevens naar/vanuit Blob-opslag te kopiëren
Laten we eens kijken hoe u snel gegevens kopiëren naar/vanuit een Azure blob-opslag. In deze walkthrough worden zowel bron- als doelgegevensopslag van het type: Azure Blob Storage. De pijplijn in deze walkthrough kopieert gegevens van een map naar een andere map in dezelfde blobcontainer. Deze walkthrough is opzettelijk eenvoudig om u instellingen of eigenschappen te laten zien wanneer u Blob Storage als bron of gootsteen gebruikt.

### <a name="prerequisites"></a>Vereisten
1. Maak een **Azure Storage-account voor** algemene doeleinden als u er nog geen hebt. U gebruikt de blob-opslag als zowel **bron-** als **doelgegevensarchief** in deze walkthrough. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor de stappen voor het maken van een account.
2. Maak een blobcontainer met de naam **adfblobconnector** in het opslagaccount.
4. Maak een map met de naam **invoer** in de **adfblobconnectorcontainer.**
5. Een bestand met de naam **emp.txt** maken met de volgende inhoud en uploadhet naar de **invoermap** met behulp van hulpprogramma's zoals [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>De gegevensfabriek maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik **op Een resource maken** vanuit de linkerbovenhoek, klik op Intelligentie + **analytics**en klik op **Gegevensfabriek**.
3. In het fabrieksdeelvenster **Nieuwe gegevens:**  
    1. Voer **ADFBlobConnectorDF** in voor de **naam**. De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de `*Data factory name “ADFBlobConnectorDF” is not available`fout ontvangt: wijzigt u de naam van de gegevensfabriek (bijvoorbeeld uw naamADFBlobConnectorDF) en probeer opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    2. Selecteer uw **Azure-abonnement**.
    3. Selecteer voor Resourcegroep **Bestaand gebruiken** om een bestaande resourcegroep (of) **nieuw maken** te selecteren om een naam voor een resourcegroep in te voeren.
    4. Selecteer een **locatie** voor de gegevensfactory.
    5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.
    6. Klik **op Maken**.
3. Nadat het maken is voltooid, ziet u het blade ![van de **gegevensfabriek** zoals weergegeven in de volgende afbeelding: Startpagina gegevensfabriek](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>De wizard Kopiëren
1. Klik op de startpagina van Gegevensfabriek op de tegel **Gegevens kopiëren** om wizard **Gegevens kopiëren** op een apart tabblad te starten.  

    > [!NOTE]
    > Als u ziet dat de webbrowser vastzit bij 'Autoriseren...,, schakelt u **Cookies van derden blokkeren en sitegegevensinstelling** (of) in ingeschakeld uit en maakt u een uitzondering voor **login.microsoftonline.com** en probeert u de wizard opnieuw te starten.
2. Op de pagina **Eigenschappen**:
    1. Voer **CopyPipeline** voor **taaknaam**in . De taaknaam is de naam van de pijplijn in uw gegevensfabriek.
    2. Voer een **beschrijving** voor de taak in (optioneel).
    3. Houd de optie **Uitvoeren regelmatig op schema** voor **taakcadans of taakplanning.** Als u deze taak slechts één keer wilt uitvoeren in plaats van herhaaldelijk volgens een planning te worden uitgevoerd, selecteert u **Nu eenmaal uitvoeren**. Als u de optie **Eenmaal af uitvoeren** selecteert, wordt een [eenmalige pijplijn](data-factory-create-pipelines.md#onetime-pipeline) gemaakt.
    4. De instellingen voor **Terugkerend patroon behouden**. Deze taak wordt dagelijks uitgevoerd tussen de begin- en eindtijden die u in de volgende stap opgeeft.
    5. Wijzig de **begindatumtijd** naar **04/21/2017**.
    6. Wijzig de **einddatumtijd** naar **04/25/2017**. U de datum typen in plaats van door de agenda te bladeren.
    8. Klik op **Volgende**.
        ![Hulpprogramma voor kopiëren - pagina Eigenschappen](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Op de pagina **Brongegevensarchief** klikt u op de tegel **Azure Blob Storage**. U gebruikt deze pagina om het brongegevensarchief op te geven voor de kopieertaak. U kunt een bestaande gekoppelde service van een gegevensarchief gebruiken of een nieuw gegevensarchief opgeven. Als u een bestaande gekoppelde service wilt gebruiken, selecteert u **VAN BESTAANDE GEKOPPELDE SERVICES** en selecteert u de juiste gekoppelde service.
    ![Hulpprogramma voor kopiëren - pagina van brongegevensarchief](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Op de pagina **Het Azure Blob Storage-account opgeven**:
    1. Bewaar de automatisch gegenereerde naam voor **verbindingsnaam**. De verbindingsnaam is de naam van de gekoppelde service van het type: Azure Storage.
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw Azure-abonnement of houd **Alles** selecteren voor **Azure-abonnement**.
    4. Selecteer een **Azure-opslagaccount** uit de lijst met Azure-opslagaccounts die beschikbaar is voor het abonnement dat u hebt geselecteerd. U er ook voor kiezen om handmatig opslagaccountinstellingen in te voeren door handmatig de optie **Enter** in te selecteren voor de **selectiemethode Account**.
    5. Klik op **Volgende**.  
        ![Hulpprogramma voor kopiëren - Het Azure Blob Storage-account opgeven](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Op de pagina **Het invoerbestand of de invoermap kiezen**:
    1. Dubbelklik op **adfblobcontainer**.
    2. Selecteer **invoer**en klik op **Kiezen**. In deze walkthrough selecteert u de invoermap. U in plaats daarvan ook het emp.txt-bestand in de map selecteren.
        ![Hulpprogramma voor kopiëren - Het invoerbestand of de invoermap kiezen](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Ga als het gaat om de pagina **Het invoerbestand of de map** kiezen:
    1. Controleer of het **bestand of** de map is ingesteld op **adfblobconnector/invoer**. Als de bestanden zich in submappen bevinden, bijvoorbeeld 2017/04/01, 2017/04/02, enzovoort, voert u adfblobconnector/input/{year}/{month}/{day} in voor bestand of map. Wanneer u tab uit het tekstvak drukt, ziet u drie vervolgkeuzelijsten om indelingen voor jaar (yyyy), maand (MM) en dag (dd) te selecteren.
    2. Stel het kopieerbestand niet **recursief in.** Selecteer deze optie om opnieuw door mappen te bladerden om bestanden naar de bestemming te kopiëren.
    3. Niet de **binaire kopie** optie. Selecteer deze optie om een binaire kopie van het bronbestand naar de bestemming uit te voeren. Selecteer niet voor deze walkthrough, zodat u meer opties in de volgende pagina's zien.
    4. Controleer of het **type compressie** is ingesteld op **Geen**. Selecteer een waarde voor deze optie als uw bronbestanden worden gecomprimeerd in een van de ondersteunde indelingen.
    5. Klik op **Volgende**.
    ![Hulpprogramma voor kopiëren - Het invoerbestand of de invoermap kiezen](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Op de pagina **Bestandsinstellingen** ziet u de scheidingstekens en het schema dat automatisch is gedetecteerd door de wizard tijdens het parseren van het bestand.
    1. Bevestig de volgende opties:  
        a. De **bestandsindeling** is ingesteld op **Tekstnotatie**. U alle ondersteunde indelingen in de vervolgkeuzelijst bekijken. Bijvoorbeeld: JSON, Avro, ORC, Parket.
       b. De **kolomscheidingsteken** is `Comma (,)`ingesteld op . In de vervolgkeuzelijst ziet u de andere kolomscheidingstekens die door Data Factory worden ondersteund. U ook een aangepaste scheidingsteken opgeven.
       c. De **rijscheidingis** ingesteld `Carriage Return + Line feed (\r\n)`op . In de vervolgkeuzelijst ziet u de andere rijscheidingstekens die door Data Factory worden ondersteund. U ook een aangepaste scheidingsteken opgeven.
       d. Het **aantal overslaande regel** is ingesteld op **0**. Als u boven aan het bestand een paar regels wilt overlaten, voert u het nummer hier in.
       e. De **eerste rij met gegevens bevat kolomnamen** is niet ingesteld. Als de bronbestanden kolomnamen in de eerste rij bevatten, selecteert u deze optie.
       f. De **lege kolomwaarde als null-optie behandelen** is ingesteld.
    2. Geavanceerde **instellingen** uitvouwen om de beschikbare geavanceerde optie te zien.
    3. Zie onderaan de pagina de **preview** van gegevens uit het emp.txt-bestand.
    4. Klik op het tabblad **SCHEMA** onderaan om het schema te zien dat de wizard kopiëren heeft afgeleid door naar de gegevens in het bronbestand te kijken.
    5. Klik op **Volgende** nadat u de scheidingstekens hebt gecontroleerd en een voorbeeld van de gegevens hebt bekeken.
    ![Hulpprogramma voor kopiëren - Bestandsindelingsinstellingen](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Selecteer op de **pagina Doelgegevensarchief**de optie **Azure Blob Storage**en klik op **Volgende**. U gebruikt de Azure Blob Storage als zowel de bron- als doelgegevensopslag in deze walkthrough.  
    ![Gereedschap Kopiëren - doelgegevensarchief selecteren](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Geef op De pagina **Azure Blob-opslagaccount op:**  
    1. Voer **AzureStorageLinkedService** in voor het veld **Verbindingsnaam.**
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw **Azure-abonnement**.
    4. Selecteer uw Azure-opslagaccount.
    5. Klik op **Volgende**.
10. Ga als u op de pagina **Het uitvoerbestand of de map kiezen:**  
    1. **Geef Mappad** op als **adfblobconnector/output/{year}/{month}/{day}**. Voer **TAB**in .
    1. Voor het **jaar,** selecteer **yyyy**.
    1. Voor de **maand,** bevestigen dat het is ingesteld op **MM**.
    1. Voor de **dag,** bevestigen dat het is ingesteld op **dd**.
    1. Controleer of het **compressietype** is ingesteld op **Geen**.
    1. Controleer of het **kopieergedrag** is ingesteld op **Bestanden samenvoegen**. Als het uitvoerbestand met dezelfde naam al bestaat, wordt de nieuwe inhoud aan het einde aan hetzelfde bestand toegevoegd.
    1. Klik op **Volgende**.
       ![Gereedschap Kopiëren - Uitvoerbestand of -map kiezen](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Controleer op de pagina **Instellingen voor bestandsindeling** de instellingen en klik op **Volgende**. Een van de extra opties hier is om een header toe te voegen aan het uitvoerbestand. Als u die optie selecteert, wordt een koptekstrij toegevoegd met namen van de kolommen uit het schema van de bron. U de naam van de standaardkolomnamen wijzigen wanneer u het schema voor de bron weergeeft. U de eerste kolom bijvoorbeeld wijzigen in Voornaam en tweede kolom in Achternaam. Vervolgens wordt het uitvoerbestand gegenereerd met een koptekst met deze namen als kolomnamen.
    ![Gereedschap Kopiëren - Instellingen voor bestandsindeling voor bestemming](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Controleer op de pagina **Prestatie-instellingen** of **cloud-eenheden** en **parallelle kopieën** zijn ingesteld op **Automatisch**en klik op Volgende. Zie [Activiteitsprestaties en stemhandleiding kopiëren](data-factory-copy-activity-performance.md#parallel-copy)voor meer informatie over deze instellingen.
    ![Gereedschap Kopiëren - Prestatie-instellingen](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Controleer **op** de pagina Overzicht alle instellingen (taakeigenschappen, instellingen voor bron en bestemming en kopieerinstellingen) en klik op **Volgende**.
    ![Gereedschap Kopiëren - Overzichtspagina](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Lees de informatie op de pagina **Samenvatting** en klik op **Voltooien**. De wizard maakt twee gekoppelde services, twee gegevenssets (invoer en uitvoer) en één pijplijn in de gegevensfactory (van waaruit u de wizard Kopiëren hebt gestart).
    ![Gereedschap Kopiëren - pagina Implementatie](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>De pijplijn controleren (kopieertaak)

1. Klik op `Click here to monitor copy pipeline` de koppeling op **de pagina Implementatie.**
2. U ziet de **toepassing Controleren en beheren** op een apart tabblad.  ![App controleren en beheren](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Wijzig de **begintijd** bovenaan `04/19/2017` en **eindtijd** in `04/27/2017`, en klik op **Toepassen**.
4. U ziet vijf activiteitsvensters in de lijst **ACTIVITEITWINDOWS.** De **WindowStart-tijden** moeten betrekking hebben op alle dagen vanaf de begintijden van de pijplijn tot de eindtijden van de pijplijn.
5. Klik een paar keer op **Vernieuwen** voor de lijst **ACTIVITEIT WINDOWS** totdat u de status ziet van alle activiteitvensters is ingesteld op Gereed.
6. Controleer nu of de uitvoerbestanden zijn gegenereerd in de uitvoermap van de adfblobconnectorcontainer. U ziet de volgende mapstructuur in de uitvoermap:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Zie [Het pijplijnartikel van Data Factory controleren en beheren](data-factory-monitor-manage-app.md) voor gedetailleerde informatie over het monitoren en beheren van gegevensfabrieken.

### <a name="data-factory-entities"></a>Gegevensfabriekentiteiten
Ga nu terug naar het tabblad met de startpagina van Data Factory. Merk op dat er nu twee gekoppelde services, twee gegevenssets en één pijplijn in uw gegevensfabriek zijn.

![Startpagina van Gegevensfabriek met entiteiten](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klik **op Auteur en implementeren** om De Editor van gegevensfabriek te starten.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

U ziet de volgende gegevensfabrieksentiteiten in uw gegevensfabriek:

- Twee gekoppelde diensten. Een voor de bron en de andere voor de bestemming. Beide gekoppelde services verwijzen naar hetzelfde Azure Storage-account in deze walkthrough.
- Twee datasets. Een invoergegevensset en een uitvoergegevensset. In deze walkthrough gebruiken beide dezelfde blobcontainer, maar verwijzen ze naar verschillende mappen (invoer en uitvoer).
- Een pijpleiding. De pijplijn bevat een kopieeractiviteit die een blobbron en een blobsink gebruikt om gegevens van een Azure-bloblocatie naar een andere Azure-bloblocatie te kopiëren.

In de volgende secties vindt u meer informatie over deze entiteiten.

#### <a name="linked-services"></a>Gekoppelde services
U ziet twee gekoppelde services. Een voor de bron en de andere voor de bestemming. In deze walkthrough zien beide definities er hetzelfde uit, behalve de namen. Het **type** gekoppelde service is ingesteld op **AzureStorage.** De belangrijkste eigenschap van de gekoppelde servicedefinitie is de **verbindingstekenreeks**, die door Data Factory wordt gebruikt om verbinding te maken met uw Azure Storage-account tijdens runtime. Negeer de eigenschap hubName in de definitie.

##### <a name="source-blob-storage-linked-service"></a>Gekoppelde bronblobopslagservice
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

##### <a name="destination-blob-storage-linked-service"></a>Gekoppelde service voor doelblobopslag

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

Zie [Sectie Gekoppelde serviceeigenschappen](#linked-service-properties) voor meer informatie over gekoppelde azure-opslagservice.

#### <a name="datasets"></a>Gegevenssets
Er zijn twee gegevenssets: een invoergegevensset en een uitvoergegevensset. Het type gegevensset is ingesteld op **AzureBlob** voor beide.

De invoerset verwijst naar de **invoermap** van de **blobcontainer adfblobconnector.** De **externe** eigenschap is ingesteld op **true** voor deze gegevensset, omdat de gegevens niet worden geproduceerd door de pijplijn met de kopieeractiviteit die deze gegevensset als invoer neemt.

De uitvoerset verwijst naar de **uitvoermap** van dezelfde blobcontainer. De uitvoergegevensset gebruikt ook de jaar-, maand- en dagvan de variabele **SliceStart-systeem** om het pad voor het uitvoerbestand dynamisch te evalueren. Zie [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md)voor een lijst met functies en systeemvariabelen die door Data Factory worden ondersteund. De **externe** eigenschap is ingesteld op **false** (standaardwaarde) omdat deze gegevensset wordt geproduceerd door de pijplijn.

Zie Sectie [Gegevensseteigenschappen](#dataset-properties) voor meer informatie over eigenschappen die worden ondersteund door azure blob-gegevensset.

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
De pijplijn heeft slechts één activiteit. Het **type** activiteit is ingesteld op **Kopiëren**. In de typeeigenschappen voor de activiteit zijn er twee secties, een voor bron en een voor gootsteen. Het brontype is ingesteld op **BlobSource,** omdat de activiteit gegevens uit een blobopslag kopieert. Het gootsteentype is ingesteld op **BlobSink** als de activiteit die gegevens kopieert naar een blobopslag. De kopieeractiviteit neemt InputDataset-z4y als de invoer en OutputDataset-z4y als de uitvoer.

Zie Sectie [Activiteitseigenschappen kopiëren](#copy-activity-properties) voor meer informatie over eigenschappen die worden ondersteund door BlobSource en BlobSink.

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar Blob Storage
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van en naar Azure Blob Storage en Azure SQL Database kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-voorbeeld: gegevens kopiëren van Blob-opslag naar SQL-database
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [BlobSource](#copy-activity-properties) en [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens van een Azure-blob naar een Azure SQL-tabel per uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Azure SQL-gekoppelde service:**

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
Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. Voor de eerste geeft u de verbindingstekenreeks op die de accountsleutel bevat en voor de latere tekenreeks geeft u de SAS-uri (Shared Access Signature) op. Zie [sectie Gekoppelde services](#linked-service-properties) voor meer informatie.

**Azure Blob-invoergegevensset:**

Gegevens worden elk uur opgehaald uit een nieuwe blob (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd en bestandsnaam, het uurdeel van de begintijd. "extern": "true"-instelling informeert Data Factory dat de tabel buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

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

Het voorbeeld kopieert gegevens naar een tabel met de naam 'MyTable' in een Azure SQL-database. Maak de tabel in uw Azure SQL-database met hetzelfde aantal kolommen als u verwacht dat het Blob CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

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
**Een kopieeractiviteit in een pijplijn met Blob-bron en SQL-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-voorbeeld: gegevens uit Azure SQL kopiëren naar Azure Blob
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) en [BlobSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens uit een Azure SQL-tabel naar een Azure-blob per uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Azure SQL-gekoppelde service:**

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
Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. Voor de eerste geeft u de verbindingstekenreeks op die de accountsleutel bevat en voor de latere tekenreeks geeft u de SAS-uri (Shared Access Signature) op. Zie [sectie Gekoppelde services](#linked-service-properties) voor meer informatie.

**Azure SQL-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel 'MyTable' in Azure SQL hebt gemaakt en dat deze kolom 'tijdstempelkolom' bevat voor tijdreeksgegevens.

Als u "extern" instelt: "true" informeert de service Gegevensfabriek dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

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

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

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

**Een kopieeractiviteit in een pijplijn met SQL-bron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **SqlSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

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
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
