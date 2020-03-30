---
title: Problemen met het oplossen van problemen met gegevenskopie problemen in Azure Data Box Disk| Microsoft Documenten
description: Beschrijft hoe u problemen oplossen die worden gezien tijdens het kopiëren van gegevens in Azure Data Box Disk met behulp van logboeken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148346"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Problemen met gegevenskopiëren oplossen in Azure Data Box Disk

In dit artikel wordt beschreven hoe u eventuele problemen oplost die u ziet bij het kopiëren van de gegevens naar schijven. Het artikel heeft ook betrekking op problemen bij het gebruik van de split copy tool.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemen met gegevenskopiëren bij het gebruik van een Linux-systeem

In dit gedeelte worden enkele van de belangrijkste problemen beschreven waarmee u te maken hebt met het gebruik van een Linux-client om gegevens naar schijven te kopiëren.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probleem: Drive wordt gemonteerd als alleen-lezen
 
**Oorzaak** 

Dit kan te wijten zijn aan een onrein bestandssysteem.

Het opnieuw monteren van een station als lezen-schrijven werkt niet met Data Box Disks. Dit scenario wordt niet ondersteund met schijven gedecodeerd door ontsleuteld. Mogelijk hebt u het apparaat opnieuw gemonteerd met de volgende opdracht:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Hoewel de montage succesvol was, zullen de gegevens niet blijven bestaan.

**Afsluiting**

Volg de volgende stappen op uw Linux-systeem:

1. Installeer `ntfsprogs` het pakket voor het ntfsfix-hulpprogramma.
2. Maak de bevestigingspunten voor het station los door het ontgrendelgereedschap. Het aantal bevestigingspunten varieert voor schijven.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Ren `ntfsfix` op het bijbehorende pad. Het gemarkeerde getal moet hetzelfde zijn als stap 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Voer de volgende opdracht uit om de metagegevens in de sluimerstand te verwijderen die het probleem met de montage kunnen veroorzaken.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Doe een schone unmount.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Doe een schone ontgrendelen en monteren.
7. Test het bevestigingspunt door een bestand te schrijven.
8. Het loskoppelen en opnieuw monteren om de bestandspersistentie te valideren.
9. Ga verder met de gegevenskopie.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probleem: Fout met gegevens die niet blijven bestaan na kopie
 
**Oorzaak** 

Als u ziet dat uw schijf geen gegevens heeft nadat deze is ontkoppeld (hoewel gegevens naar het is gekopieerd), is het mogelijk dat u een station opnieuw als read-write nadat het station werd gemonteerd als alleen-lezen.

**Afsluiting**
 
Als dat het geval is, zie de resolutie voor [stations krijgen gemonteerd als alleen-lezen](#issue-drive-getting-mounted-as-read-only).

Als dat niet het geval was, kopieert u de logboeken uit de map met het hulpprogramma Schijfontgrendelen in gegevensbox en [neemt u contact op met Microsoft Support.](data-box-disk-contact-microsoft-support.md)


## <a name="data-box-disk-split-copy-tool-errors"></a>Fouten met het splits- en kopieerprogramma voor Data Box Disk

De problemen die worden waargenomen bij het gebruik van een gereedschap Gesplitst kopiëren om de gegevens over meerdere schijven te splitsen, worden samengevat in de volgende tabel.

|Foutbericht/waarschuwingen |Aanbevelingen |
|---------|---------|
|[Info] BitLocker-wachtwoord ophalen voor volume: m <br>[Fout] Uitzondering gevangen tijdens het ophalen van BitLocker-toets voor volume m:<br> Reeks bevat geen elementen.|Deze fout wordt gegenereerd als de doel-Data Box Disk offline is. <br> Gebruik hulpprogramma `diskmgmt.msc` voor online schijven.|
|[Fout] Uitzondering opgetreden: WMI-bewerking is mislukt:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=De indeling van het verstrekte herstelwachtwoord is ongeldig. <br>BitLocker-herstelwachtwoorden bestaan uit 48 cijfers. <br>Controleer of het herstelwachtwoord de juiste indeling heeft en probeer het opnieuw.|Gebruik eerst het ontgrendelingsprogramma van Data Box Disk om de schijven te ontgrendelen en voer dan de opdracht opnieuw uit. Voor meer informatie gaat u naar <li> [Data Box Disk ontgrendelen voor Windows-clients](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Data Box Disk ontgrendelen voor Linux-clients](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fout] Uitzondering opgetreden: er bestaat een bestand DriveManifest.xml in het doelstation. <br> Dit betekent dat het doelstation mogelijk is voorbereid met een ander logboekbestand. <br>Als u meer gegevens aan hetzelfde station wilt toevoegen, gebruik dan het vorige logboekbestand. Als u bestaande gegevens wilt verwijderen en doelstation opnieuw wilt gebruiken voor een nieuwe importtaak, verwijdert u de *DriveManifest.xml* op het station. Voer deze opdracht opnieuw uit met een nieuw logboekbestand.| Deze fout wordt gegenereerd wanneer u dezelfde reeks stations probeert te gebruiken voor meerdere importsessies. <br> Gebruik één set schijven maar voor één splits- en kopieersessie.|
|[Fout] Uitzondering opgetreden: CopySessionId importdata-sept-test-1 verwijst naar een eerdere kopieersessie en kan niet worden hergebruikt voor een nieuwe kopieersessie.|Deze fout wordt gerapporteerd wanneer wordt geprobeerd om dezelfde taaknaam voor een nieuwe taak te gebruiken als een eerder met succes voltooide taak.<br> Geef een unieke naam op voor de nieuwe taak.|
|[Info] De naam van het doelbestand of de doelmap overschrijdt de lengtelimiet van NTFS. |Dit bericht wordt gegenereerd als de naam van het doelbestand is gewijzigd vanwege een lang pad.<br> Wijzig de verwijderingsoptie in bestand `config.json` om dit gedrag te regelen.|
|[Fout] Uitzondering opgetreden: ongeldige JSON-escape-volgorde. |Dit bericht wordt gegenereerd als de config.json een ongeldige opmaak heeft. <br> Valideer uw `config.json` met behulp van [JSONlint](https://jsonlint.com/) voordat u het bestand opslaat.|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met validatiehulpprogramma's](data-box-disk-troubleshoot.md).
