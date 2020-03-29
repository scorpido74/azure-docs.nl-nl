---
title: Problemen in Azure Data Box Disk oplossen | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen in Azure Data Box Disk oplost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805713"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Logboeken gebruiken om validatieproblemen op te lossen in Azure Data Box Disk

Dit artikel is van toepassing op Microsoft Azure Data Box Disk. In het artikel wordt beschreven hoe u de logboeken gebruiken om de validatieproblemen op te lossen die u zien wanneer u deze oplossing implementeert.

## <a name="validation-tool-log-files"></a>Logboekbestanden voor validatiegereedschappen

Wanneer u de gegevens op de schijven valideert met het [validatiegereedschap,](data-box-disk-deploy-copy-data.md#validate-data)wordt een *error.xml* gegenereerd om eventuele fouten te registreren. Het logboekbestand bevindt `Drive:\DataBoxDiskImport\logs` zich in de map van uw station. Er wordt een koppeling naar het foutlogboek weergegeven wanneer u validatie uitvoert.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Als u meerdere sessies uitvoert voor validatie, wordt er per sessie één foutlogboek gegenereerd.

- Hier vindt u een voorbeeld van het foutlogboek wanneer de gegevens die in de `PageBlob` map zijn geladen, niet 512 bytes zijn uitgelijnd. Alle gegevens die naar PageBlob worden geüpload, moeten 512 bytes zijn uitgelijnd, bijvoorbeeld een VHD of VHDX. De fouten in dit `<Errors>` bestand zijn `<Warnings>`in de en waarschuwingen in .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Hier vindt u een voorbeeld van het foutlogboek wanneer de containernaam niet geldig is. De map die `BlockBlob`u `PageBlob`maakt `AzureFile` onder , of mappen op de schijf wordt een container in uw Azure Storage-account. De naam van de container moet de [Azure-naamgevingsconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)volgen.

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Fouten in validatiehulpprogramma's

De fouten in de *error.xml* met de bijbehorende aanbevolen acties worden samengevat in de volgende tabel.

| Foutcode| Beschrijving                       | Aanbevolen acties               |
|------------|--------------------------|-----------------------------------|
| `None` | De gegevens met succes gevalideerd. | Geen actie vereist. |
| `InvalidXmlCharsInPath` |Kan geen manifestbestand maken omdat het bestandspad tekens bevat die niet geldig zijn. | Verwijder deze tekens om verder te gaan.  |
| `OpenFileForReadFailed`| Kan het bestand niet verwerken. Dit kan te wijten zijn aan een toegangsprobleem of beschadiging van het bestandssysteem.|Kon het bestand niet lezen vanwege een fout. De foutgegevens staan in de uitzondering. |
| `Not512Aligned` | Dit bestand is niet geldig voor de map PageBlob.| Upload alleen gegevens die 512 `PageBlob` bytes zijn uitgelijnd op map. Verwijder het bestand uit de map PageBlob of verplaats het naar de map BlockBlob. Probeer de validatie opnieuw.|
| `InvalidBlobPath` | Bestandspad wordt niet toegewezen aan een geldig blobpad in de cloud volgens de Azure Blob-naamgevingsconventies.|Volg de azure-naamgevingsrichtlijnen om de naam van het bestandspad te wijzigen. |
| `EnumerationError` | Kan het bestand niet opsommen voor validatie. |Er kunnen meerdere redenen zijn voor deze fout. Een zeer waarschijnlijke reden is de toegang tot het bestand. |
| `ShareSizeExceeded` | Dit bestand heeft ervoor gezorgd dat de grootte van azure-bestandendelen de Azure-limiet van 5 TB heeft overschreden.|Verklein de grootte van de gegevens in het aandeel zodat deze voldoet aan de [azure-objectgroottelimieten.](data-box-disk-limits.md#azure-object-size-limits) Probeer de validatie opnieuw. |
| `AzureFileSizeExceeded` | De bestandsgrootte overschrijdt de limieten voor de grootte van Azure File.| Verklein de grootte van het bestand of de gegevens zodat het voldoet aan de [azure-objectgroottelimieten.](data-box-disk-limits.md#azure-object-size-limits) Probeer de validatie opnieuw.|
| `BlockBlobSizeExceeded` | De bestandsgrootte overschrijdt de groottelimieten van Azure Block Blob. | Verklein de grootte van het bestand of de gegevens zodat het voldoet aan de [azure-objectgroottelimieten.](data-box-disk-limits.md#azure-object-size-limits) Probeer de validatie opnieuw. |
| `ManagedDiskSizeExceeded` | De bestandsgrootte overschrijdt de groottelimieten voor Azure Managed Disk. | Verklein de grootte van het bestand of de gegevens zodat het voldoet aan de [azure-objectgroottelimieten.](data-box-disk-limits.md#azure-object-size-limits) Probeer de validatie opnieuw. |
| `PageBlobSizeExceeded` | De bestandsgrootte overschrijdt de groottelimieten voor Azure Managed Disk. | Verklein de grootte van het bestand of de gegevens zodat het voldoet aan de [azure-objectgroottelimieten.](data-box-disk-limits.md#azure-object-size-limits) Probeer de validatie opnieuw. |
| `InvalidShareContainerFormat`  |De directorynamen voldoen niet aan Azure-naamgevingsconventies voor containers of shares.         |De eerste map die is gemaakt onder de reeds bestaande mappen op de schijf wordt een container in uw opslagaccount. Deze share- of containernaam voldoet niet aan de Azure-naamgevingsconventies. Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevingsconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Probeer de validatie opnieuw.   |
| `InvalidBlobNameFormat` | Bestandspad wordt niet toegewezen aan een geldig blobpad in de cloud volgens de Azure Blob-naamgevingsconventies.|Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevingsconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Probeer de validatie opnieuw. |
| `InvalidFileNameFormat` | Bestandspad wordt niet toegewezen aan een geldig bestandspad in de cloud volgens de azure-bestandsnaamgevingsconventies. |Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevingsconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Probeer de validatie opnieuw. |
| `InvalidDiskNameFormat` | Bestandspad wordt niet toegewezen aan een geldige schijfnaam in de cloud volgens de naamgevingsconventies van Azure Managed Disk. |Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevingsconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Probeer de validatie opnieuw.       |
| `NotPartOfFileShare` | Het uploadpad voor bestanden is ongeldig. Upload de bestanden naar een map in Azure Files.   | Verwijder de bestanden ten onrechte en upload deze bestanden naar een vooraf gemaakte map. Probeer de validatie opnieuw. |
| `NonVhdFileNotSupportedForManagedDisk` | Een niet-VHD-bestand kan niet worden geüpload als een beheerde schijf. |Verwijder de niet-VHD-bestanden uit `ManagedDisk` de map omdat deze `PageBlob` niet worden ondersteund of verplaats deze bestanden naar een map. Probeer de validatie opnieuw. |


## <a name="next-steps"></a>Volgende stappen

- Problemen met [fouten bij het uploaden van gegevens](data-box-disk-troubleshoot-upload.md)oplossen .
