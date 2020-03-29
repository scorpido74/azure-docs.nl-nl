---
title: Problemen met het oplossen van problemen met schijfontgrendeling van Azure Data Box Disk | Microsoft Documenten
description: Hierin wordt beschreven hoe u problemen in Azure Data Box Disk oplost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148281"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Problemen met schijfontgrendeling oplossen in Azure Data Box Disk

Dit artikel is van toepassing op Microsoft Azure Data Box Disk en beschrijft de werkstromen die worden gebruikt om eventuele problemen op te lossen bij het gebruik van het ontgrendelentool. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Fouten met het ontgrendelingsprogramma voor Data Box Disk


| Fout bericht/gedrag hulpprogramma      | Aanbevelingen                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Het huidige .NET Framework wordt niet ondersteund. De ondersteunde versies zijn 4.5 of hoger.<br><br>Hulpprogramma wordt beëindigd met een bericht.  | .NET 4.5 is niet geïnstalleerd. Installeer .NET 4.5 of hoger op de hostcomputer waarop het ontgrendelingsprogramma voor Data Box Disk wordt uitgevoerd.                                                                            |
| Kan geen volumes ontgrendelen of verifiëren. Neem contact op met Microsoft Ondersteuning.  <br><br>Vergrendelde stations kunnen niet worden ontgrendeld of geverifieerd door het hulpprogramma. | De vergrendelde stations kunnen niet worden ontgrendeld door het hulpprogramma met de verstrekte wachtwoordsleutel. Neem contact op met Microsoft Ondersteuning voor volgende stappen.                                                |
| De volgende volumes zijn ontgrendeld en geverifieerd. <br>Volumestationsletters: E:<br>Er konden geen volumes worden ontgrendeld met de volgende wachtwoordsleutels: werwerqomnf, qwerwerqwdfda <br><br>Door het hulpprogramma worden sommige stations ontgrendeld en worden de letters van stations waarbij het is mislukt weergegeven.| Gedeeltelijk geslaagd. Sommige van de stations kunnen niet worden ontgrendeld met de verstrekte wachtwoordsleutel. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| De vergrendelde volumes zijn niet gevonden. Controleer of de schijf die is ontvangen van Microsoft op de juiste manier is aangesloten en is vergrendeld.          | Er zijn geen vergrendelde stations gevonden door het hulpprogramma. De stations zijn al ontgrendeld of niet gedetecteerd. Zorg ervoor dat de stations zijn verbonden en zijn vergrendeld.                                                           |
| Onherstelbare fout: ongeldige parameter<br>Parameternaam: invalid_arg<br>GEBRUIK:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Voorbeeld: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Voorbeeld: DataBoxDiskUnlock /SystemCheck<br>Voorbeeld: DataBoxDiskUnlock/Help<br><br>/PassKeys:       Haal deze wachtwoordsleutel op vanuit de Azure DataBox Disk-order. De wachtwoordsleutel ontgrendelt uw schijven.<br>/ Help:           Deze optie biedt Help bij het gebruik van cmdlets en voorbeelden.<br>/ SystemCheck:     met deze optie wordt gecontroleerd of uw systeem voldoet aan de vereisten voor het uitvoeren van het hulpprogramma.<br><br>Druk op een willekeurige toets om af te sluiten. | Ongeldige parameter ingevoerd. De alleen toegestane parameters zijn /SystemCheck, /PassKey en /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Problemen voor schijven ontgrendelen bij het gebruik van een Windows-client

In dit gedeelte worden enkele van de belangrijkste problemen beschreven die tijdens de implementatie van Gegevensboxschijf worden ondervonden bij het gebruik van een Windows-client voor gegevenskopiëren.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probleem: Kon schijf niet ontgrendelen vanuit BitLocker
 
**Oorzaak** 

U hebt het wachtwoord gebruikt in het dialoogvenster BitLocker en probeert de schijf te ontgrendelen via het dialoogvenster Voor ontgrendelstations van BitLocker. Dit zou niet werken.

**Afsluiting**

Als u de gegevensboxschijven wilt ontgrendelen, moet u het hulpprogramma Voor schijfontgrendeling in gegevensboxen gebruiken en het wachtwoord opgeven vanuit de Azure-portal. Ga voor meer informatie naar [Zelfstudie: Azure Data Box Disk uitpakken, verbinden en ontgrendelen.](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probleem: kan sommige volumes niet ontgrendelen of verifiëren. Neem contact op met Microsoft Ondersteuning.
 
**Oorzaak**

Mogelijk ziet u de volgende fout in het foutlogboek en u bepaalde volumes niet ontgrendelen of verifiëren.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Dit geeft aan dat u waarschijnlijk de juiste versie van Windows PowerShell mist op uw Windows-client.

**Afsluiting**

U [Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) installeren en de bewerking opnieuw proberen.
 
Als u de volumes nog steeds niet ontgrendelen, kopieert u de logboeken uit de map met het hulpprogramma Voor schijfontgrendeling in gegevensbox en [neemt u contact op met Microsoft Support.](data-box-disk-contact-microsoft-support.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van validatieproblemen](data-box-disk-troubleshoot.md).
