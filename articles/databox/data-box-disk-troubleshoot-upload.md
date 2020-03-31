---
title: Problemen met het uploaden van gegevens oplossen met behulp van logboeken
titleSuffix: Azure Data Box Disk
description: Beschrijft hoe u de logboeken gebruiken en problemen oplossen bij het uploaden van gegevens naar Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260135"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Logboeken begrijpen om problemen met het uploaden van gegevens op te lossen in Azure Data Box Disk

Dit artikel is van toepassing op Microsoft Azure Data Box Disk en beschrijft de problemen die u ziet wanneer u gegevens uploadt naar Azure.

## <a name="about-upload-logs"></a>Logboeken uploaden

Wanneer de gegevens worden geüpload `_error.xml` naar `_verbose.xml` Azure in het datacenter en bestanden worden gegenereerd voor elk opslagaccount. Deze logboeken worden geüpload naar hetzelfde opslagaccount dat is gebruikt om gegevens te uploaden. 

Beide logboeken bevinden zich in dezelfde indeling en bevatten XML-beschrijvingen van de gebeurtenissen die zijn opgetreden tijdens het kopiëren van de gegevens van de schijf naar het Azure Storage-account.

Het verboselogboek bevat volledige informatie over de status van de kopieerbewerking voor elke blob of bestand, terwijl het foutlogboek alleen de informatie bevat voor blobs of bestanden die fouten hebben ondervonden tijdens het uploaden.

Het foutlogboek heeft dezelfde structuur als de breedboselog, maar filtert succesvolle bewerkingen uit.

## <a name="download-logs"></a>Logboeken downloaden

Volg de volgende stappen om de uploadlogboeken te vinden.

1. Als er fouten zijn bij het uploaden van de gegevens naar Azure, geeft de portal een pad weer naar de map waar de diagnostische logboeken zich bevinden.

    ![Koppeling naar logboeken in de portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Ga naar **waies.**

    ![fout- en verboselogboeken](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

In elk geval ziet u de foutlogboeken en de verboselogboeken. Selecteer elk logboek en download een lokaal exemplaar.

## <a name="sample-upload-logs"></a>Voorbeeld van uploadlogboeken

Een voorbeeld `_verbose.xml` van de hieronder wordt weergegeven. In dit geval is de bestelling zonder fouten voltooid.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Voor dezelfde volgorde wordt hieronder `_error.xml` een voorbeeld van de volgorde weergegeven.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Een voorbeeld `_error.xml` van de wordt hieronder weergegeven waar de volgorde met fouten is voltooid. 

Het foutbestand in dit `Summary` geval heeft een sectie en een andere sectie die alle fouten op bestandsniveau bevat. 

Het `Summary` bevat `ValidationErrors` de `CopyErrors`en de . In dit geval zijn er 8 bestanden of mappen geüpload naar Azure en waren er geen validatiefouten. Toen de gegevens naar het Azure Storage-account zijn gekopieerd, zijn 5 bestanden of mappen geüpload. De overige 3 bestanden of mappen zijn hernoemd volgens de Azure-containernaamgevingsconventies en vervolgens geüpload naar Azure.

De status van `BlobStatus` het bestandsniveau beschrijft alle acties die zijn ondernomen om de blobs te uploaden. In dit geval worden drie containers hernoemd omdat de mappen waarnaar de gegevens zijn gekopieerd, niet in overeenstemming zijn met de Azure-naamgevingsconventies voor containers. Voor de blobs die in die containers zijn geüpload, zijn de nieuwe containernaam, het pad van de blob in Azure, het oorspronkelijke ongeldige bestandspad en de blobgrootte opgenomen.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Fouten bij het uploaden van gegevens

De fouten die zijn gegenereerd bij het uploaden van de gegevens naar Azure worden samengevat in de volgende tabel.

| Foutcode | Beschrijving                   |
|-------------|------------------------------|
|`None` |  Voltooid.           |
|`Renamed` | De blob is opnieuw benoemd.   |
|`CompletedWithErrors` | Upload voltooid met fouten. De details van de bestanden in fout zijn opgenomen in het logbestand.  |
|`Corrupted`|CRC berekend tijdens de opname van gegevens komt niet overeen met de CRC berekend tijdens het uploaden.  |  
|`StorageRequestFailed` | Azure-opslagaanvraag is mislukt.   |     
|`LeasePresent` | Dit object wordt geleased en wordt gebruikt door een andere gebruiker. |
|`StorageRequestForbidden` |Kon niet uploaden vanwege verificatieproblemen. |
|`ManagedDiskCreationTerminalFailure` | Kon niet uploaden als beheerde schijven. De bestanden zijn beschikbaar in het opslagaccount met tijdelijke bestanden als paginablobs. U paginablobs handmatig converteren naar beheerde schijven.  |
|`DiskConversionNotStartedTierInfoMissing` | Aangezien het VHD-bestand buiten de vooraf gemaakte laagmappen is gekopieerd, is er geen beheerde schijf gemaakt. Het bestand wordt geüpload als paginablob naar het opslagaccount voor tijdelijke bestanden zoals opgegeven tijdens het maken van de bestelling. U deze handmatig converteren naar een beheerde schijf.|
|`InvalidWorkitem` | Kan de gegevens niet uploaden omdat deze niet voldoen aan de naamgeving van Azure en conventies worden beperkt.|
|`InvalidPageBlobUploadAsBlockBlob` | Geüpload als blokblobs in `databoxdisk-invalid-pb-`een container met voorvoegsel .|
|`InvalidAzureFileUploadAsBlockBlob` | Geüpload als blokblobs in `databoxdisk-invalid-af`een container met voorvoegsel -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Geüpload als blokblobs in `databoxdisk-invalid-md`een container met voorvoegsel -.|
|`InvalidManagedDiskUploadAsPageBlob` |Geüpload als paginablobs in `databoxdisk-invalid-md-`een container met voorvoegsel . |
|`MovedToOverflowShare` |Geüploade bestanden naar een nieuw aandeel omdat de oorspronkelijke grootte van het aandeel de maximale azure-groottelimiet heeft overschreden. De naam van het nieuwe bestandsaandeel `-2`heeft de oorspronkelijke naam achtergeschreven met .   |
|`MovedToDefaultAzureShare` |Geüploade bestanden die geen deel uitmaakten van een map naar een standaardshare. De naam van `databox-`het aandeel begint met . |
|`ContainerRenamed` |De container voor deze bestanden voldoet niet aan Azure-naamgevingsconventies en wordt hernoemd. De nieuwe naam `databox-` begint met en is achtervoegsel met de SHA1 hash van de oorspronkelijke naam |
|`ShareRenamed` |Het aandeel voor deze bestanden voldoet niet aan azure-naamgevingsconventies en wordt hernoemd. De nieuwe naam `databox-` begint met en is achtervoegsel met de SHA1 hash van de oorspronkelijke naam. |
|`BlobRenamed` |Deze bestanden voldoen niet aan azure-naamgevingsconventies en werden hernoemd. Controleer `BlobPath` het veld voor de nieuwe naam. |
|`FileRenamed` |Deze bestanden voldoen niet aan azure-naamgevingsconventies en werden hernoemd. Controleer `FileStoragePath` het veld voor de nieuwe naam. |
|`DiskRenamed` |Deze bestanden voldoen niet aan azure-naamgevingsconventies en werden hernoemd. Controleer `BlobPath` het veld voor de nieuwe naam. |


## <a name="next-steps"></a>Volgende stappen

- [Open een ondersteuningsticket voor problemen met gegevensboxschijven](data-box-disk-contact-microsoft-support.md).
