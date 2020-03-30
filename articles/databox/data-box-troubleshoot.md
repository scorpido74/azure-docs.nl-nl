---
title: Problemen met uw Azure-gegevensvak oplossen, Azure Data Box Heavy
description: Beschrijft hoe u problemen in Azure Data Box en Azure Data Box Heavy oplossen bij het kopiëren van gegevens naar deze apparaten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560062"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Problemen met Azure Data Box en Azure Data Box Heavy oplossen

In dit artikel vindt u informatie over het oplossen van problemen die u zien bij het gebruik van het Azure Data Box of Azure Data Box Heavy. Het artikel bevat de lijst met mogelijke fouten die worden gezien wanneer gegevens worden gekopieerd naar de gegevensvak of wanneer gegevens worden geüpload vanuit databox.

## <a name="error-classes"></a>Foutklassen

De fouten in Data Box en Data Box Heavy worden als volgt samengevat:

| Foutcategorie*        | Beschrijving        | Aanbevolen actie    |
|----------------------------------------------|---------|--------------------------------------|
| Namen container of delen | De container- of sharenamen volgen de azure-naamgevingsregels niet.  |Download de foutlijsten. <br> Wijzig de naam van de containers of aandelen. [Meer informatie](#container-or-share-name-errors).  |
| Limiet voor container- of deelgrootte | De totale gegevens in containers of aandelen overschrijden de Azure-limiet.   |Download de foutlijsten. <br> Verminder de algemene gegevens in de container of deel. [Meer informatie](#container-or-share-size-limit-errors).|
| Limiet voor object- of bestandsgrootte | Het object of de bestanden in containers of shares overschrijdt de Azure-limiet.|Download de foutlijsten. <br> Verklein de bestandsgrootte in de container of deel. [Meer informatie](#object-or-file-size-limit-errors). |    
| Gegevens of bestandstype | De gegevensindeling of het bestandstype wordt niet ondersteund. |Download de foutlijsten. <br> Voor paginablobs of beheerde schijven moet u ervoor zorgen dat de gegevens 512 bytes zijn uitgelijnd en gekopieerd naar de vooraf gemaakte mappen. [Meer informatie](#data-or-file-type-errors). |
| Niet-kritieke blob- of bestandsfouten  | De blob- of bestandsnamen volgen de Azure-naamgevingsregels niet of het bestandstype wordt niet ondersteund. | Deze blob of bestanden mogen niet worden gekopieerd of de namen kunnen worden gewijzigd. [Meer informatie over het oplossen van deze fouten](#non-critical-blob-or-file-errors). |

\*De eerste vier foutcategorieën zijn kritieke fouten en moeten worden opgelost voordat u zich voorbereiden op het verzenden.


## <a name="container-or-share-name-errors"></a>Fouten in container- of share-naam

Dit zijn fouten met betrekking tot container en delen namen.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Foutbeschrijving:** De container of de naam van het aandeel moet tussen 3 en 63 tekens liggen. 

**Voorgestelde resolutie:** De map onder de Gegevensbox of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslagaccount. 

- Download en controleer op de pagina **Verbinding en kopie** van de lokale webgebruikersinterface van het apparaat de foutbestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder het gegevensvak of het aandeel Zwaar gegevens om ervoor te zorgen dat:

    - De naam heeft tussen 3 en 63 tekens.
    - De namen kunnen alleen letters, cijfers en koppeltekens hebben.
    - De namen kunnen niet beginnen of eindigen met koppeltekens.
    - De namen mogen geen opeenvolgende koppeltekens hebben.
    - Voorbeelden van geldige `my-folder-1`namen: ,`my-really-extra-long-folder-111`
    - Voorbeelden van namen die niet `my-folder_1`geldig `my` `--myfolder`zijn: , , `myfolder--``myfolder!`

    Zie de Azure-naamgevingsconventies voor [containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [namen delen voor](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)meer informatie.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Foutbeschrijving:** De container of de naam van het aandeel mag alleen bestaan uit letters, cijfers of koppeltekens.

**Voorgestelde resolutie:** De map onder de Gegevensbox of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslagaccount. 

- Download en controleer op de pagina **Verbinding en kopie** van de lokale webgebruikersinterface van het apparaat de foutbestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder het gegevensvak of het aandeel Zwaar gegevens om ervoor te zorgen dat:

    - De naam heeft tussen 3 en 63 tekens.
    - De namen kunnen alleen letters, cijfers en koppeltekens hebben.
    - De namen kunnen niet beginnen of eindigen met koppeltekens.
    - De namen mogen geen opeenvolgende koppeltekens hebben.
    - Voorbeelden van geldige `my-folder-1`namen: ,`my-really-extra-long-folder-111`
    - Voorbeelden van namen die niet `my-folder_1`geldig `my` `--myfolder`zijn: , , `myfolder--``myfolder!`

    Zie de Azure-naamgevingsconventies voor [containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [namen delen voor](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)meer informatie.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Foutbeschrijving:** De containernamen en namen van delen kunnen niet beginnen of eindigen met koppeltekens en kunnen geen opeenvolgende koppeltekens hebben.

**Voorgestelde resolutie:** De map onder de Gegevensbox of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslagaccount. 

- Download en controleer op de pagina **Verbinding en kopie** van de lokale webgebruikersinterface van het apparaat de foutbestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder het gegevensvak of het aandeel Zwaar gegevens om ervoor te zorgen dat:

    - De naam heeft tussen 3 en 63 tekens.
    - De namen kunnen alleen letters, cijfers en koppeltekens hebben.
    - De namen kunnen niet beginnen of eindigen met koppeltekens.
    - De namen mogen geen opeenvolgende koppeltekens hebben.
    - Voorbeelden van geldige `my-folder-1`namen: ,`my-really-extra-long-folder-111`
    - Voorbeelden van namen die niet `my-folder_1`geldig `my` `--myfolder`zijn: , , `myfolder--``myfolder!`

    Zie de Azure-naamgevingsconventies voor [containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [namen delen voor](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)meer informatie.

## <a name="container-or-share-size-limit-errors"></a>Fouten in container- of sharesizelimiet

Dit zijn fouten die verband houden met gegevens die groter zijn dan de grootte van de gegevens die in een container of een aandeel zijn toegestaan.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Foutbeschrijving:** Azure-bestandsshare beperkt een aandeel tot 5 TB aan gegevens. Deze limiet is voor sommige aandelen overschreden.

**Voorgestelde resolutie:** Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.

Identificeer de mappen met dit probleem uit de foutlogboeken en zorg ervoor dat de bestanden in die map jonger zijn dan 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Fouten in object- of bestandsgroottelimiet

Dit zijn fouten die verband houden met gegevens die de maximale grootte van het object of het bestand dat is toegestaan in Azure overschrijden. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Foutbeschrijving:** De bestandsgrootte overschrijdt de maximale bestandsgrootte voor upload.

**Voorgestelde resolutie:** De blob of de bestandsgrootte overschrijdt de maximaal toegestane limiet voor het uploaden.

- Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
- Zorg ervoor dat de blob- en bestandsgrootten de limieten voor de grootte van azure-objecten niet overschrijden.

## <a name="data-or-file-type-errors"></a>Fouten in gegevens of bestandstype

Dit zijn fouten die verband houden met niet-ondersteund bestandstype of gegevenstype in de container of delen. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Foutbeschrijving:** De blob of het bestand is onjuist uitgelijnd.

**Voorgestelde resolutie:** De paginablobshare op Data Box of Data Box Heavy ondersteunt alleen bestanden die 512 bytes zijn uitgelijnd (bijvoorbeeld VHD/VHDX). Alle gegevens die naar het blobaandeel van de pagina worden gekopieerd, worden als paginablobs naar Azure geüpload.

Verwijder niet-VHD/VHDX-gegevens uit het delen van de paginablob. U shares gebruiken voor blokblob- of Azure-bestanden voor algemene gegevens.

Zie [Overzicht van paginablobs voor](../storage/blobs/storage-blob-pageblob-overview.md)meer informatie .

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Foutbeschrijving:** Er is een niet-ondersteund bestandstype aanwezig in een beheerde schijfshare. Alleen vaste VHD's zijn toegestaan.

**Voorgestelde resolutie:**

- Zorg ervoor dat u alleen de vaste VHD's uploadt om beheerde schijven te maken.
- VHDX-bestanden of **dynamische** en **differentiërende** VHD's worden niet ondersteund.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Foutbeschrijving:** Een map is niet toegestaan in een van de reeds bestaande mappen voor de beheerde schijven. Alleen vaste VHD's zijn toegestaan in deze mappen.

**Voorgestelde resolutie:** Voor beheerde schijven worden binnen elk aandeel de volgende drie mappen gemaakt die overeenkomen met containers in uw opslagaccount: Premium SSD, Standard HDD en Standard SSD. Deze mappen komen overeen met de prestatielaag voor de beheerde schijf.

- Zorg ervoor dat u uw paginablobgegevens (VHD's) kopieert naar een van deze bestaande mappen.
- Een map of map is niet toegestaan in deze bestaande mappen. Verwijder alle mappen die u hebt gemaakt in de reeds bestaande mappen.

Zie [Kopiëren naar beheerde schijven voor](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)meer informatie.

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Foutbeschrijving:** Symbolische links zijn niet toegestaan in Linux. 

**Voorgestelde resolutie:** De symbolische links zijn meestal links, pijpen, en andere dergelijke bestanden. Verwijder de koppelingen of los de koppelingen op en kopieer de gegevens.


## <a name="non-critical-blob-or-file-errors"></a>Niet-kritieke blob- of bestandsfouten

Alle niet-kritieke fouten met betrekking tot namen van blobs, bestanden of containers die worden gezien tijdens het kopiëren van gegevens, worden samengevat in de volgende sectie. Als deze fouten aanwezig zijn, worden de namen aangepast aan de Azure-naamgevingsconventies. De bijbehorende orderstatus voor het uploaden van gegevens wordt **voltooid met waarschuwingen**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Foutbeschrijving:** De blob- of bestandsnamen bevatten niet-ondersteunde besturingselementtekens.

**Voorgestelde resolutie:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
Verwijder of wijzig de naam van de bestanden om niet-ondersteunde tekens te verwijderen.

Zie de Azure-naamgevingsconventies voor [blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestandsnamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Foutbeschrijving:** De blob- of bestandsnamen bevatten illegale tekens.

**Voorgestelde resolutie:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
Verwijder of wijzig de naam van de bestanden om niet-ondersteunde tekens te verwijderen.

Zie de Azure-naamgevingsconventies voor [blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestandsnamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Foutbeschrijving:** De blob- of bestandsnamen eindigen met slechte tekens.

**Voorgestelde resolutie:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
Verwijder of wijzig de naam van de bestanden om niet-ondersteunde tekens te verwijderen.

Zie de Azure-naamgevingsconventies voor [blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestandsnamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Foutbeschrijving:** De blob- of bestandsnaam bevat te veel padsegmenten.

**Voorgestelde resolutie:** De blobs of de bestanden die u hebt gekopieerd, overschrijden het maximum aantal padsegmenten. Een padsegment is de tekenreeks tussen opeenvolgende tekens van de scheidingsteken, bijvoorbeeld de voorwaartse slash /.

- Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
- Zorg ervoor dat de [blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestandsnamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) voldoen aan Azure-naamgevingsconventies.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Foutbeschrijving:** De blob- of bestandsnaam is te lang.

**Voorgestelde resolutie:** De blob of de bestandsnamen overschrijden de maximale lengte.

- Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
- De naam van de blob mag niet groter zijn dan 1.024 tekens.
- Verwijder of wijzig de naam van de blob of bestanden zodat de namen niet meer dan 1024 tekens bevatten.

Zie de Azure-naamgevingsconventies voor blobnamen en bestandsnamen voor meer informatie.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Foutbeschrijving:** Een van de segmenten in de blob- of bestandsnaam is te lang.

**Voorgestelde resolutie:** Een van de padsegmenten in de blob- of bestandsnaam overschrijdt de maximale aantallen tekens. Een padsegment is de tekenreeks tussen opeenvolgende tekens van de scheidingsteken, bijvoorbeeld de voorwaartse slash /.

- Download en bekijk op de pagina **Verbinding en kopie** van de lokale web-gebruikersinterface de foutbestanden.
- Zorg ervoor dat de [blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestandsnamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) voldoen aan Azure-naamgevingsconventies.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Foutbeschrijving:** Onjuiste containernamen zijn opgegeven voor beheerde schijfshares.

**Voorgestelde resolutie:** Voor beheerde schijven worden binnen elk aandeel de volgende mappen gemaakt die overeenkomen met containers in uw opslagaccount: Premium SSD, Standard HDD en Standard SSD. Deze mappen komen overeen met de prestatielaag voor de beheerde schijf.

- Zorg ervoor dat u uw paginablobgegevens (VHD's) kopieert naar een van deze bestaande mappen. Alleen gegevens van deze bestaande containers worden geüpload naar Azure.
- Elke andere map die is gemaakt op hetzelfde niveau als Premium SSD, Standard HDD en Standard SSD komt niet overeen met een geldige prestatielaag en kan niet worden gebruikt.
- Verwijder bestanden of mappen die buiten de prestatielagen zijn gemaakt.

Zie [Kopiëren naar beheerde schijven voor](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de vereisten van het [Gegevensvak Blob-opslagsysteem](data-box-system-requirements-rest.md).
