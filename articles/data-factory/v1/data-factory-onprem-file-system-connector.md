---
title: Gegevens kopiëren naar/van een bestandssysteem met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van en naar een on-premises bestandssysteem met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265933"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Gegevens van en naar een on-premises bestandssysteem kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-file-system-connector.md)
> * [Versie 2 (huidige versie)](../connector-file-system.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Bestandssysteemconnector in V2](../connector-file-system.md)als u de huidige versie van de service Data Factory gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar/vanuit een on-premises bestandssysteem te kopiëren. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit een on-premises bestandssysteem** kopiëren naar de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar een on-premises bestandssysteem:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Copy Activity verwijdert het bronbestand niet nadat het naar de bestemming is gekopieerd. Als u het bronbestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijplijn te gebruiken.

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Data Factory ondersteunt het verbinden van en naar een on-premises bestandssysteem via **Data Management Gateway.** U moet de Data Management Gateway installeren in uw on-premises omgeving voor de Data Factory-service om verbinding te maken met een ondersteund on-premises gegevensarchief, inclusief bestandssysteem. Zie [Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Afgezien van Data Management Gateway hoeven er geen andere binaire bestanden te worden geïnstalleerd om te communiceren van en naar een on-premises bestandssysteem. U moet de Data Management Gateway installeren en gebruiken, zelfs als het bestandssysteem zich in Azure IaaS VM bevindt. Zie [Data Management Gateway](data-factory-data-management-gateway.md)voor gedetailleerde informatie over de gateway.

Als u een Linux-bestandsshare wilt gebruiken, installeert u [Samba](https://www.samba.org/) op uw Linux-server en installeert u Data Management Gateway op een Windows-server. Het installeren van Data Management Gateway op een Linux-server wordt niet ondersteund.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een bestandssysteem verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Azure blob-opslag naar een on-premises bestandssysteem, maakt u twee gekoppelde services om uw on-premises bestandssysteem en Azure-opslagaccount te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde serviceeigenschappen](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor een on-premises bestandssysteem.
3. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de blobcontainer en -map op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de map en bestandsnaam (optioneel) in uw bestandssysteem op te geven. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor on-premises bestandssystemen.
4. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u BlobSource als bron en FileSystemSink als een sink voor de kopieeractiviteit. Als u ook van het on-premises bestandssysteem naar Azure Blob Storage kopieert, gebruikt u FileSystemSource en BlobSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor on-premises bestandssystemen. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeelden](#json-examples-for-copying-data-to-and-from-file-system) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in gegevensfabriek die worden gebruikt om gegevens naar/van een bestandssysteem te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor het bestandssysteem:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
U een on-premises bestandssysteem koppelen aan een Azure-gegevensfabriek met de **on-premises bestandsservergekoppelde** service. In de volgende tabel vindt u beschrijvingen voor JSON-elementen die specifiek zijn voor de gekoppelde service On-Premises Bestandsserver.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |Controleer of de eigenschap type is ingesteld op **OnPremisesFileServer**. |Ja |
| host |Hiermee geeft u het hoofdpad op van de map die u wilt kopiëren. Gebruik het escape character ' \ ' voor speciale tekens in de tekenreeks. Zie [Voorbeeld definities van gekoppelde service en gegevenssets](#sample-linked-service-and-dataset-definitions) voor voorbeelden. |Ja |
| userid |Geef de id op van de gebruiker die toegang heeft tot de server. |Nee (als u versleuteld referentie kiest) |
| wachtwoord |Geef het wachtwoord voor de gebruiker op (userid). |Nee (als u versleuteld referentiekiest |
| versleuteldCredential |Geef de versleutelde referenties op die u krijgen door de cmdlet Nieuw-AzDataFactoryEncryptValue uit te voeren. |Nee (als u ervoor kiest om userid en wachtwoord op te geven in platte tekst) |
| gatewayNaam |Hiermee geeft u de naam op van de gateway die Gegevensfabriek moet gebruiken om verbinding te maken met de on-premises bestandsserver. |Ja |


### <a name="sample-linked-service-and-dataset-definitions"></a>Voorbeeld van gekoppelde service- en gegevenssetdefinities
| Scenario | Host in gekoppelde servicedefinitie | folderPath in gegevenssetdefinitie |
| --- | --- | --- |
| Lokale map op de datamanagementgatewaymachine: <br/><br/>Voorbeelden: D:\\ \* of D:\map\submap\\\* |D:\\ \\ (voor Data Management Gateway 2.0 en latere versies) <br/><br/> localhost (voor eerdere versies dan Data Management Gateway 2.0) |. of\\\\mapsubmap (voor Gegevensbeheer Gateway 2.0 en latere versies) \\ \\ <br/><br/>D:\\ \\ of\\\\D: mapsubmap\\\\(voor gatewayversie onder 2.0) |
| Gedeelde map op afstand: <br/><br/>\\ \\Voorbeelden: submap\\\\ \* myserver\\share\\\\of \\ \\myserver share folder\\\* |\\\\\\\\myservershare\\\\ |. of mapsubmap\\\\ \\ \\ |

>[!NOTE]
>Wanneer u via de gebruikersinterface schrijft, hoeft u`\\`geen dubbele backslash ( ) in te voeren om te ontsnappen zoals u doet via JSON, enkele backslash opgeven.

### <a name="example-using-username-and-password-in-plain-text"></a>Voorbeeld: gebruikersnaam en wachtwoord gebruiken in platte tekst

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Voorbeeld: versleutelde referenties gebruiken

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie [Gegevenssets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen.

De sectie typeEigenschappen is verschillend voor elk type gegevensset. Het biedt informatie, zoals de locatie en het formaat van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **FileShare** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Hiermee geeft u het subpad naar de map op. Gebruik het escape\' character ' voor speciale tekens in de string. Wildcard-filter wordt niet ondersteund. Zie [Voorbeeld definities van gekoppelde service en gegevenssets](#sample-linked-service-and-dataset-definitions) voor voorbeelden.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer **bestandsnaam** niet is opgegeven voor een uitvoergegevensset en **behoudenHiërarchie** niet is opgegeven in activiteitssink, is de naam van het gegenereerde bestand in de volgende indeling: <br/><br/>`Data.<Guid>.txt`(Voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nee |
| fileFilter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de mapPath te selecteren in plaats van alle bestanden. <br/><br/>Toegestane waarden zijn: `*` (meerdere `?` tekens) en (enkel teken).<br/><br/>Voorbeeld 1: "fileFilter": "*.log"<br/>Voorbeeld 2: "fileFilter": 2014-1-?. txt"<br/><br/>Houd er rekening mee dat fileFilter van toepassing is op een invoerfileShare-gegevensset. |Nee |
| partitionedBy |U partitionedBy gebruiken om een dynamische mapPath/fileName op te geven voor tijdreeksgegevens. Een voorbeeld is folderPath parameterized voor elk uur van gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. zie [Bestands- en compressie-indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nee |

> [!NOTE]
> U fileName en fileFilter niet tegelijkertijd gebruiken.

### <a name="using-partitionedby-property"></a>PartitionedBy-eigenschap gebruiken
Zoals in de vorige sectie is vermeld, u een dynamische mapPath en bestandsnaam opgeven voor tijdreeksgegevens met de eigenschap **partitionedBy,** [de functies Gegevensfabriek en de systeemvariabelen](data-factory-functions-variables.md).

Zie [Gegevenssets maken,](data-factory-create-datasets.md) [Plannen en uitvoeren en](data-factory-scheduling-and-execution.md)pijplijnen maken voor meer informatie over gegevenssets voor tijdreeksen, planning en segmenten. [Creating pipelines](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Voorbeeld 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In dit voorbeeld wordt {Slice} vervangen door de waarde van de variabele SliceStart van het Data Factory-systeem in de indeling (YYYYMMDDHH). SliceStart verwijst naar de begintijd van het segment. De mapPath is verschillend voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Voorbeeld 2:

```JSON
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

In dit voorbeeld worden jaar, maand, dag en tijd van SliceStart geëxtraheerd in afzonderlijke variabelen die de eigenschappen folderPath en fileName gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoergegevenssets en beleidsregels zijn beschikbaar voor alle soorten activiteiten. Overwegende dat de eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit per activiteitstype verschillen.

Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten. Als u gegevens verplaatst vanuit een on-premises bestandssysteem, stelt u het brontype in de kopieeractiviteit in op **FileSystemSource.** Als u gegevens verplaatst naar een on-premises bestandssysteem, stelt u het gootsteentype in de kopieeractiviteit in op **FileSystemSink.** In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door FileSystemSource en FileSystemSink.

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

**FileSystemSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee definieert u het kopieergedrag wanneer de bron BlobSource of FileSystem is. |**Hiërarchie behouden:** Hiermee behoudt u de bestandshiërarchie in de doelmap. Dat wil zeggen dat het relatieve pad van het bronbestand naar de bronmap hetzelfde is als het relatieve pad van het doelbestand naar de doelmap.<br/><br/>**Afvlakkenhiërarchie:** Alle bestanden uit de bronmap worden gemaakt in het eerste niveau van de doelmap. De doelbestanden worden gemaakt met een automatisch gegenereerde naam.<br/><br/>**MergeFiles:** Hiermee worden alle bestanden uit de bronmap samengevoegd tot één bestand. Als de bestandsnaam/blobnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. |Nee |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden
In deze sectie wordt het resulterende gedrag van de bewerking Kopiëren beschreven voor verschillende combinaties van waarden voor de eigenschappen recursieve en copyBehavior.

| recursieve waarde | copyBehavior-waarde | Resulterend gedrag |
| --- | --- | --- |
| waar |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5 |
| waar |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5-inhoud wordt samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam. |
| false |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |
| false |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |
| false |mergeFiles |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met een automatisch gegenereerde bestandsnaam.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 wordt niet opgehaald. |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands- en compressie-indelingen in](data-factory-supported-file-and-compression-formats.md) het artikel van Azure Data Factory over details.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar het bestandssysteem
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van en naar een on-premises bestandssysteem en Azure Blob-opslag kopieert. U gegevens echter *rechtstreeks* uit een van de bronnen kopiëren naar een van de putten die worden vermeld in [ondersteunde bronnen en sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met kopieeractiviteit in Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Voorbeeld: gegevens van een on-premises bestandssysteem kopiëren naar Azure Blob-opslag
In dit voorbeeld ziet u hoe u gegevens van een on-premises bestandssysteem kopiëren naar Azure Blob-opslag. De steekproef heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van het type [OnPremisesFileServer](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [FileShare](#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [Bestandssysteembron](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

In het volgende voorbeeld worden elk uur tijdreeksgegevens van een on-premises bestandssysteem naar Azure Blob-opslag gekopieerd. De JSON-eigenschappen die in deze monsters worden gebruikt, worden in de secties na de monsters beschreven.

Stel als eerste stap Data Management Gateway in volgens de instructies in [Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway.](data-factory-move-data-between-onprem-and-cloud.md)

**On-Premises fileservergekoppelde service:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

We raden u aan de eigenschap **encryptedCredential** te gebruiken in plaats van de **eigenschappen userid** en **password.** Zie [Bestandsservergekoppelde service](#linked-service-properties) voor meer informatie over deze gekoppelde service.

**Gekoppelde Azure Storage-service:**

```JSON
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

**De invoergegevensset voor on-premises bestandssystemen:**

Elk uur worden gegevens opgehaald uit een nieuw bestand. De eigenschappen folderPath en fileName worden bepaald op basis van de begintijd van het segment.

Het `"external": "true"` instellen van gegevensfabriek informeert dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Gegevensset azure Blob-opslaguitvoer:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt de delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Een kopieeractiviteit in een pijplijn met bestandssysteembron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **FileSystemSource**en is **het sinktype** ingesteld op **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Voorbeeld: Gegevens uit Azure SQL-database kopiëren naar een on-premises bestandssysteem
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

* Een gekoppelde service van het type [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Een gekoppelde service van het type [OnPremisesFileServer](#linked-service-properties).
* Een invoergegevensset van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Een uitvoergegevensset van het type [FileShare](#dataset-properties).
* Een pijplijn met een kopieeractiviteit die [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) en [FileSystemSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur tijdreeksgegevens uit een Azure SQL-tabel naar een on-premises bestandssysteem. De JSON-eigenschappen die in deze monsters worden gebruikt, worden in secties na de monsters beschreven.

**Gekoppelde Azure SQL Database-service:**

```JSON
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

**On-Premises fileservergekoppelde service:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

We raden u aan de eigenschap **encryptedCredential** te gebruiken in plaats van de eigenschappen **userid** en **wachtwoord** te gebruiken. Zie [De gekoppelde service Bestandssysteem](#linked-service-properties) voor meer informatie over deze gekoppelde service.

**Azure SQL-invoergegevensset:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel 'MyTable' in Azure SQL hebt gemaakt en dat deze kolom 'tijdstempelkolom' bevat voor tijdreeksgegevens.

Het ``“external”: ”true”`` instellen van gegevensfabriek informeert dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
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

**Gegevensset voor on-premises bestandssysteemuitvoer:**

Gegevens worden elk uur naar een nieuw bestand gekopieerd. De mapPath en fileName voor de blob worden bepaald op basis van de begintijd van het segment.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Een kopieeractiviteit in een pijplijn met SQL-bron en bestandssysteemsink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **SqlSource**en wordt het **gootsteentype** ingesteld op **FileSystemSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

U kolommen van brongegevensset ook toewijzen aan kolommen uit de sink-gegevensset in de definitie van kopieeractiviteit. Zie Kolommen [met gegevenssetstoewijzen in Azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
 Zie de handleiding voor de [prestaties en afstemming](data-factory-copy-activity-performance.md)van activiteit kopiëren voor meer informatie over de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren.
