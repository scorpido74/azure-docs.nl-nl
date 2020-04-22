---
title: Azure Data Box, Azure Data Box Zware gebeurtenissen bijhouden en registreren| Microsoft Documenten
description: Beschrijft hoe u gebeurtenissen bijhouden en registreren in de verschillende fasen van uw Azure Data Box en Azure Data Box Heavy-order.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676414"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Tracking- en gebeurtenislogboekregistratie voor uw Azure Data Box en Azure Data Box Heavy

Een gegevensvak of gegevensvak zware order gaat door de volgende stappen: order, set-up, data copy, return, upload naar Azure en verifiëren, en gegevens wissen. Als u overeenkomt met elke stap in de volgorde, u meerdere acties uitvoeren om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volgorde bij te houden en de verschillende logboeken die worden gegenereerd te interpreteren.

In de volgende tabel vindt u een overzicht van de bestelstappen Gegevensvak of Gegevensvak Zwaar en de tools die beschikbaar zijn om de order tijdens elke stap bij te houden en te controleren.

| Volgorde van de gegevensbox       | Tool om bij te houden en te controleren                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Order maken               | [Toegangscontrole instellen voor de bestelling via RBAC](#set-up-access-control-on-the-order)                                                    |
| Bestelling verwerkt            | [Volg de bestelling](#track-the-order) via <ul><li> Azure Portal </li><li> Website van de rederij </li><li>E-mailmeldingen</ul> |
| Apparaat instellen              | Toegang tot apparaatreferenties in [logboeken van activiteit](#query-activity-logs-during-setup)                                              |
| Gegevens kopiëren naar apparaat        | [ *Error.xml-bestanden* weergeven](#view-error-log-during-data-copy) voor gegevenskopiëren                                                             |
| Voorbereiding voor verzending            | [De BOM-bestanden](#inspect-bom-during-prepare-to-ship) of de manifestbestanden op het apparaat inspecteren                                      |
| Gegevens uploaden naar Azure       | [Kopieerlogboeken](#review-copy-log-during-upload-to-azure) controleren op fouten tijdens het uploaden van gegevens in Azure-datacenter                         |
| Gegevenswissen van apparaat   | [Logboeken van bewaringsverzamelingen weergeven,](#get-chain-of-custody-logs-after-data-erasure) inclusief controlelogboeken en ordergeschiedenis                |

In dit artikel worden de verschillende mechanismen of tools beschreven die beschikbaar zijn om databox of databox zware bestellingbij te houden en te controleren. De informatie in dit artikel is van toepassing op zowel Data Box en Data Box Heavy. In de volgende secties zijn verwijzingen naar Data Box ook van toepassing op Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Toegangsbeheer instellen voor de bestelling

U bepalen wie toegang heeft tot uw bestelling wanneer de bestelling voor het eerst wordt gemaakt. RBAC-rollen (Role-based Access Control) instellen op verschillende scopes om de toegang tot de gegevensboxvolgorde te beheren. Een RBAC-rol bepaalt het type toegang – lezen-schrijven, alleen-lezen, lezen schrijven naar een subset van bewerkingen.

De twee rollen die kunnen worden gedefinieerd voor de Azure Data Box-service zijn:

- **Data Box Reader** - hebben alleen-lezen toegang tot een bestelling (s) zoals gedefinieerd door het bereik. Ze kunnen alleen details van een bestelling bekijken. Ze hebben geen toegang tot andere gegevens met betrekking tot opslagaccounts of bewerken de ordergegevens, zoals adres enzovoort.
- **Data Box Contributor** - kan alleen een order maken om gegevens over te dragen naar een bepaald opslagaccount *als ze al schrijftoegang hebben tot een opslagaccount*. Als ze geen toegang hebben tot een opslagaccount, kunnen ze niet eens een databox-order maken om gegevens naar het account te kopiëren. Deze rol definieert geen machtigingen voor opslagaccounts en verleent geen toegang tot opslagaccounts.  

Als u de toegang tot een bestelling wilt beperken, u het nieuwe dialoogvenster scan

- Een rol toewijzen op orderniveau. De gebruiker heeft alleen die machtigingen zoals gedefinieerd door de rollen om alleen te communiceren met die specifieke databoxvolgorde en niets anders.
- Wijs een rol toe op het niveau van de resourcegroep, de gebruiker heeft toegang tot alle databoxorders binnen een resourcegroep.

Zie Aanbevolen procedures voor Azure [RBAC voor](../role-based-access-control/best-practices.md)meer informatie over voorgesteld RBAC-gebruik.

## <a name="track-the-order"></a>De bestelling volgen

U uw bestelling volgen via de Azure-portal en via de website van de vervoerder. De volgende mechanismen zijn aanwezig om de databoxorder te volgen op elk gewenst moment:

- Als u de volgorde wilt bijhouden wanneer het apparaat zich in azure-datacenter of uw locatie bevindt, gaat u naar uw **gegevensboxorder > Overzicht** in Azure-portal.

    ![Bestelstatus weergeven en nee bijhouden](media/data-box-logs/overview-view-status-1.png)

- Als u de bestelling wilt bijhouden terwijl het apparaat onderweg is, gaat u naar de website van de regionale provider, bijvoorbeeld de UPS-website in de VS. Geef het volgnummer op dat aan uw bestelling is gekoppeld.
- Data Box stuurt ook e-mailmeldingen wanneer de orderstatus verandert op basis van de e-mails die zijn verstrekt toen de bestelling werd gemaakt. Zie De status van de [volgorde weergeven voor](data-box-portal-admin.md#view-order-status)een lijst met alle statussen van de gegevensboxvolgorde. Zie [Meldingsgegevens](data-box-portal-admin.md#edit-notification-details)bewerken als u de aan de order gekoppelde meldingsinstellingen wilt wijzigen.

## <a name="query-activity-logs-during-setup"></a>Queryactiviteitslogboeken tijdens de installatie

- Uw gegevensbox arriveert in een vergrendelde staat op uw locatie. U de apparaatreferenties gebruiken die beschikbaar zijn in de Azure-portal voor uw bestelling.  

    Wanneer een gegevensvak is ingesteld, moet u mogelijk weten wie alle referenties van het apparaat heeft geopend. Als u wilt weten wie toegang heeft tot het blade van de referenties van het **apparaat,** u de activiteitslogboeken opvragen.  Elke actie waarbij toegang wordt gegeven tot **apparaatgegevens >** `ListCredentials` het blad Credentials wordt aangemeld bij de activiteitslogboeken als actie.

    ![Logboeken met queryactiviteit](media/data-box-logs/query-activity-log-1.png)

- Elk teken in de gegevensbox wordt realtime geregistreerd. Deze informatie is echter alleen beschikbaar in de [controlelogboeken](#audit-logs) nadat de order is voltooid.

## <a name="view-error-log-during-data-copy"></a>Foutlogboek weergeven tijdens gegevenskopie

Tijdens de gegevenskopie naar Data Box of Data Box Heavy wordt een foutbestand gegenereerd als er problemen zijn met de gegevens die worden gekopieerd.

### <a name="errorxml-file"></a>Bestand Error.xml

Zorg ervoor dat de kopie taken zijn voltooid met geen fouten. Als er fouten zijn tijdens het kopieerproces, downloadt u de logboeken van de pagina **Verbinding maken en kopiëren.**

- Als u een bestand hebt gekopieerd dat niet 512 bytes is uitgelijnd op een beheerde schijfmap in uw gegevensvak, wordt het bestand niet geüpload als paginablob naar uw opslagaccount voor tijdelijke bestanden. U ziet een fout in de logboeken. Verwijder het bestand en kopieer een bestand dat 512 bytes is uitgelijnd.
- Als u een VHDX hebt gekopieerd, of een dynamische VHD, of een differentiërende VHD (deze bestanden worden niet ondersteund), ziet u een fout in de logboeken.

Hier is een voorbeeld van de *error.xml* voor verschillende fouten bij het kopiëren naar beheerde schijven.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Hier is een voorbeeld van de *error.xml* voor verschillende fouten bij het kopiëren naar paginablobs.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Hier is een voorbeeld van de *error.xml* voor verschillende fouten bij het kopiëren naar blobs te blokkeren.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Hier vindt u een voorbeeld van de *error.xml* voor verschillende fouten bij het kopiëren naar Azure-bestanden.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Los in elk van de bovenstaande gevallen de fouten op voordat u doorgaat naar de volgende stap. Ga voor meer informatie over de fouten die tijdens het kopiëren van gegevens naar Gegevensbox via SMB- of NFS-protocollen worden ontvangen, naar [Problemen met gegevensbox en gegevensbox zwaar](data-box-troubleshoot.md)oplossen. Ga voor informatie over fouten die tijdens het kopiëren van gegevens naar Gegevensvak via REST zijn ontvangen, naar [Problemen met de opslagproblemen van Data Box Blob](data-box-troubleshoot-rest.md)oplossen.

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecteer BOM tijdens de voorbereiding op het schip

Tijdens de voorbereiding om te verzenden, wordt een lijst gemaakt met bestanden die bekend staan als de Bill of Materials (BOM) of manifestbestand.

- Gebruik dit bestand om te controleren op basis van de werkelijke namen en het aantal bestanden dat naar het gegevensvak is gekopieerd.
- Gebruik dit bestand om te controleren op basis van de werkelijke grootte van de bestanden.
- Controleer of de *crc64* overeenkomt met een tekenreeks zonder nul. <!--A null value for crc64 indicates that there was a reparse point error)-->

Ga voor meer informatie over de fouten die tijdens de voorbereiding op het schip zijn ontvangen, naar [Problemen met de gegevensbox en gegevensbox zwaar oplossen.](data-box-troubleshoot.md)

### <a name="bom-or-manifest-file"></a>BOM of manifestbestand

Het bom- of manifestbestand bevat de lijst met alle bestanden die naar het gegevensvakapparaat zijn gekopieerd. Het BOM-bestand heeft bestandsnamen en de bijbehorende afmetingen en de checksum. Er wordt een apart BOM-bestand gemaakt voor de blokblobs, paginablobs, Azure-bestanden, voor kopiëren via de REST-API's en voor de kopie naar beheerde schijven in het gegevensvak. U de BOM-bestanden downloaden van de lokale webgebruikersinterface van het apparaat tijdens de voorbereiding op het verzenden.

Deze bestanden bevinden zich ook op het Data Box-apparaat en worden geüpload naar het bijbehorende opslagaccount in het Azure-datacenter.

### <a name="bom-file-format"></a>BOM-bestandsindeling

BOM of manifestbestand heeft de volgende algemene indeling:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Hier is een voorbeeld van een manifest dat is gegenereerd wanneer de gegevens zijn gekopieerd naar het blokblobaandeel in het gegevensvak.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

De BOM- of manifestbestanden worden ook gekopieerd naar het Azure-opslagaccount. U de bom- of manifestbestanden gebruiken om te controleren of bestanden die naar Azure zijn geüpload, overeenkomen met de gegevens die naar het gegevensvak zijn gekopieerd.

## <a name="review-copy-log-during-upload-to-azure"></a>Kopieerlogboek bekijken tijdens het uploaden naar Azure

Tijdens het uploaden van gegevens naar Azure wordt een kopieerlogboek gemaakt.

### <a name="copy-log"></a>Logboek kopiëren

Voor elke order die wordt verwerkt, maakt de Data Box-service een copy-logboek in het bijbehorende opslagaccount. Het kopieerlogboek bevat het totale aantal bestanden dat is geüpload en het aantal bestanden dat is uitgeschakeld tijdens de gegevenskopie van Gegevensvak naar uw Azure-opslagaccount.

Een Cyclic Redundancy Check (CRC) berekening wordt gedaan tijdens het uploaden naar Azure. De CRC's van de gegevenskopie en na het uploaden van de gegevens worden vergeleken. Een CRC-mismatch geeft aan dat de bijbehorende bestanden niet zijn geüpload.

Logopen worden standaard naar `copylog`een container met de naam geschreven. De logboeken worden opgeslagen met de volgende naamgevingsconventie:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Het logboekpad voor kopiëren wordt ook weergegeven op het **overzichtsblad** voor de portal.

![Pad om het inlogblad overzichtsblad te kopiëren wanneer voltooid](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Upload voltooid 

In het volgende voorbeeld wordt de algemene indeling van een kopielogboek voor een upload van een gegevensvak beschreven dat is voltooid:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Upload voltooid met fouten 

Uploaden naar Azure kan ook compleet zijn met fouten.

![Pad om het inlogblad overzichtsblad te kopiëren wanneer voltooid met fouten](media/data-box-logs/copy-log-path-2.png)

Hier is een voorbeeld van een kopie log waar de upload voltooid met fouten:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Upload voltooid met waarschuwingen

Upload naar Azure wordt compleet met waarschuwingen als uw gegevens container-/blob-/bestandsnamen bevatten die niet voldeden aan Azure-naamgevingsconventies en de namen zijn gewijzigd om de gegevens naar Azure te uploaden.

![Pad om het inlogblad overzichtsblad te kopiëren wanneer voltooid met waarschuwingen](media/data-box-logs/copy-log-path-3.png)

Hier is een voorbeeld van een kopielogboek waarbij de containers die niet voldeden aan Azure-naamgevingsconventies tijdens het uploaden van gegevens naar Azure zijn hernoemd.

De nieuwe unieke namen voor `DataBox-GUID` containers zijn in de indeling en de gegevens voor de container worden in de nieuwe omgedoopte container geplaatst. Het kopieerlogboek geeft de oude en de nieuwe containernaam voor container op.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Hier is een voorbeeld van een kopielogboek waarbij de blobs of bestanden die niet voldeden aan Azure-naamgevingsconventies, werden hernoemd tijdens het uploaden van gegevens naar Azure. De nieuwe blob- of bestandsnamen worden geconverteerd naar SHA256 digest van relatieve pad naar container en worden geüpload naar pad op basis van doeltype. De bestemming kan blokblobs, paginablobs of Azure-bestanden zijn.

Hiermee `copylog` wordt de oude en de nieuwe blob- of bestandsnaam en het pad in Azure opgegeven.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Krijg chain of custody logs na het wissen van gegevens

Nadat de gegevens zijn gewist uit de Data Box-schijven volgens de NIST SP 800-88 Revision 1-richtlijnen, zijn de chain of custody-logboeken beschikbaar. Deze logboeken bevatten de controlelogboeken en de ordergeschiedenis. De BOM- of manifestbestanden worden ook gekopieerd met de controlelogboeken.

### <a name="audit-logs"></a>Auditlogboeken

Controlelogboeken bevatten informatie over het inschakelen en openen van shares in de Data Box of Data Box Heavy wanneer deze zich buiten azure datacenter bevinden. Deze logboeken bevinden zich op:`storage-account/azuredatabox-chainofcustodylogs`

Hier is een voorbeeld van het controlelogboek uit een gegevensvak:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Ordergeschiedenis downloaden

De bestelgeschiedenis is beschikbaar in de Azure-portal. Als de bestelling is voltooid en het apparaat wordt opgeschoond (gegevenswissen van de schijven) is voltooid, gaat u naar uw apparaatvolgorde en navigeert u naar **Details van bestellen.** De optie **Ordergeschiedenis downloaden** is beschikbaar. Zie [Bestelgeschiedenis downloaden](data-box-portal-admin.md#download-order-history)voor meer informatie.

Als u door de bestelgeschiedenis bladert, ziet u het:

- Gegevens over het bijhouden van vervoerders voor uw apparaat.
- Gebeurtenissen met *SecureErase-activiteit.* Deze gebeurtenissen komen overeen met het wissen van de gegevens op de schijf.
- Gegevensboxlogboekkoppelingen. De paden voor de *controlelogboeken,* *kopieerlogboeken*en *BOM-bestanden* worden weergegeven.

Hier vindt u een voorbeeld van het ordergeschiedenislogboek van azure-portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen in uw gegevensvak en gegevensvak zwaar.](data-box-troubleshoot.md)
