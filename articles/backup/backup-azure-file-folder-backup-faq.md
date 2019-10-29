---
title: Back-ups maken van bestanden en mappen met Azure Backup-Veelgestelde vragen
description: Behandelt Veelgestelde vragen over het maken van back-ups van bestanden en mappen met Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: c30b918be5e4185d6fb4fdd2fcfc47f8dd4d25ef
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969170"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Veelgestelde vragen over het maken van back-ups van bestanden en mappen

In dit artikel vindt u antwoorden op veelgestelde vragen Abound back-ups maken van bestanden en mappen met de Microsoft Azure Recovery Services-agent (MARS) in de [Azure backup](backup-overview.md) -service.

## <a name="general"></a>Algemeen

## <a name="configure-backups"></a>Back-ups configureren

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Waar kan ik de nieuwste versie van de MARS-agent downloaden?

De meest recente MARS-agent die wordt gebruikt bij het maken van een back-up van Windows Server-computers, System Center DPM en Microsoft Azure Backup Server, kan worden [gedownload](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Hoe lang zijn de kluis referenties geldig?

De kluisreferenties verlopen na 48 uur. Als het referenties bestand is verlopen, downloadt u het bestand opnieuw vanuit de Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Vanaf welke stations kan ik back-ups maken van bestanden en mappen?

U kunt geen back-up maken van de volgende typen stations en volumes:

* Verwissel bare media: alle bronnen van back-upitems moeten worden gerapporteerd als vast.
* Alleen-lezen volumes: het volume moet schrijfbaar zijn voor het functioneren van de Volume Shadow Copy service (VSS).
* Offline volumes: het volume moet online zijn om te kunnen werken met VSS.
* Netwerk shares: het volume moet lokaal op de server zijn om een back-up te maken met online back-ups.
* Met BitLocker beveiligde volumes: het volume moet worden ontgrendeld voordat de back-up kan worden uitgevoerd.
* Bestandssysteemidentificatie: NTFS is het enige bestandssysteem dat wordt ondersteund.

### <a name="what-file-and-folder-types-are-supported"></a>Welke typen bestanden en mappen worden ondersteund?

Meer [informatie](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) over de typen bestanden en mappen die worden ondersteund voor back-ups.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op een virtuele Azure-machine?  

Ja. Azure Backup biedt back-up op VM-niveau voor virtuele Azure-machines met behulp van de VM-extensie voor de Azure VM-agent. Als u een back-up wilt maken van bestanden en mappen op het gast-Windows-besturings systeem op de virtuele machine, kunt u de MARS-agent installeren.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op de tijdelijke opslag voor de virtuele Azure-machine?

Ja. Installeer de MARS-agent en maak een back-up van bestanden en mappen op het gast-Windows-besturings systeem naar de tijdelijke opslag.

* Back-uptaken mislukken wanneer tijdelijke opslag gegevens worden gewist.
* Als de tijdelijke opslag gegevens worden verwijderd, kunt u alleen herstellen naar niet-vluchtige opslag.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hoe kan ik een server registreren bij een andere regio?

Back-upgegevens worden verzonden naar het Data Center van de kluis waarin de server is geregistreerd. De eenvoudigste manier om het Data Center te wijzigen is door de agent te verwijderen en opnieuw te installeren en de machine vervolgens te registreren bij een nieuwe kluis in de gewenste regio.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Ondersteunt de MARS-agent Windows Server 2012-ontdubbeling?

Ja. De MARS-agent converteert de ontdubbelde gegevens naar normale gegevens wanneer de back-upbewerking wordt voor bereid. Vervolgens worden de gegevens voor het maken van de back-up geoptimaliseerd, worden de gegevens versleuteld en worden de versleutelde gegevens vervolgens naar de kluis verzonden.

## <a name="manage-backups"></a>Back-ups beheren

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Wat gebeurt er als ik de naam Wijzig van een Windows-computer die is geconfigureerd voor back-up?

Wanneer u de naam van een Windows-computer wijzigt, worden alle momenteel geconfigureerde back-ups gestopt.

* U moet de nieuwe machine naam registreren bij de back-upkluis.
* Wanneer u de nieuwe naam bij de kluis registreert, is de eerste bewerking een *volledige* back-up.
* Als u gegevens moet herstellen waarvan een back-up naar de kluis is gemaakt met de oude server naam, gebruikt u de optie om terug te zetten naar een andere locatie in de wizard gegevens herstellen. [Meer informatie](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Wat is de maximale lengte voor het bestandspad voor back-up?

De MARS-agent is afhankelijk van NTFS en maakt gebruik van de lengte specificatie filepath, beperkt door de [Windows-API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Als de bestanden die u wilt beveiligen, langer zijn dan de toegestane waarde, maakt u een back-up van de bovenliggende map of van het schijf station.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Welke tekens zijn toegestaan in bestands paden?

De MARS-agent is afhankelijk van NTFS en ondersteunt [tekens](/windows/desktop/FileIO/naming-a-file#naming-conventions) in bestands namen/paden.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>De waarschuwing ' Azure-back-ups zijn niet geconfigureerd voor deze server ' wordt weer gegeven.

Deze waarschuwing kan worden weer gegeven ondanks dat u een back-upbeleid hebt geconfigureerd, wanneer de instellingen voor het back-upschema die zijn opgeslagen op de lokale server niet hetzelfde zijn als de instellingen die zijn opgeslagen in de back-upkluis.

* Wanneer de server of de instellingen zijn hersteld naar een bekende goede staat, kunnen back-upscheman niet meer worden gesynchroniseerd.
* Als deze waarschuwing wordt weer gegeven, [configureert](backup-azure-manage-windows-server.md) u het back-upbeleid opnieuw en voert u een back-up op aanvraag uit om de lokale server opnieuw te synchroniseren met Azure.

## <a name="manage-the-backup-cache-folder"></a>De map voor de back-upcache beheren

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Wat is de vereiste minimumgrootte voor de cachemap?

De grootte van de cachemap bepaalt de hoeveelheid gegevens waarvan u een back-up maakt.

* De volumes in de cache-map moeten vrije ruimte hebben die gelijk is aan ten minste 5-10% van de totale grootte van de back-upgegevens.
* Als het volume minder dan 5% beschik bare ruimte heeft, verg root u de volume grootte of verplaatst u de cachemap naar een volume met voldoende ruimte.
* Als u een back-up maakt van de systeem status van Windows, hebt u een extra beschik bare schijf ruimte van 30-35 GB nodig in het volume met de cachemap.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Controleren of de Scratch-map geldig is en toegankelijk is?

1. Standaard bevindt de map Scratch zich op `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Zorg ervoor dat het pad naar de locatie van de map Scratch overeenkomt met de waarden van de register sleutel vermeldingen die hieronder worden weer gegeven:

  | Registerpad | Registersleutel | Waarde |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nieuwe locatie van de cachemap* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nieuwe locatie van de cachemap* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hoe kan ik de cache locatie van de MARS-agent wijzigen?

1. Voer deze opdracht uit in een opdracht prompt met verhoogde bevoegdheid om de back-upengine te stoppen:

    ```PS C:\> Net stop obengine```

2. Als u de systeem status back-up hebt geconfigureerd, opent u schijf beheer en ontkoppelt u de schijven met namen in de indeling `"CBSSBVol_<ID>"`.
3. Verplaats de bestanden niet. Kopieer in plaats daarvan de map met de cache ruimte naar een ander station dat voldoende ruimte heeft.
4. Werk de volgende Register vermeldingen bij met het pad van de nieuwe cachemap.<br/>

    | Registerpad | Registersleutel | Waarde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nieuwe locatie van de cachemap* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nieuwe locatie van de cachemap* |

5. Start de backup-engine opnieuw op bij een opdracht prompt met verhoogde bevoegdheid:

    ```PS C:\> Net stop obengine```

    ```PS C:\> Net start obengine```

6. Voer een ad-hoc back-up uit. Nadat de back-up is voltooid met behulp van de nieuwe locatie, kunt u de oorspronkelijke cachemap verwijderen.

### <a name="where-should-the-cache-folder-be-located"></a>Waar moet de cachemap zich bevinden?

De volgende locaties worden niet aanbevolen voor de cachemap:

* Netwerk share/Verwissel bare media: de cachemap moet lokaal zijn voor de server waarvan een back-up moet worden gemaakt met online back-ups. Netwerk locaties of verwissel bare media, zoals USB-stations, worden niet ondersteund
* Offline volumes: de cachemap moet online zijn voor de verwachte back-up met Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Zijn er kenmerken van de cachemap die niet worden ondersteund?

De volgende kenmerken of combinaties van deze kenmerken worden niet ondersteund voor de cachemap:

* Versleuteld
* Ontdubbeld
* Gecomprimeerd
* Sparse
* Reparsepunt

De cachemap en de metagegevens-VHD hebben beide niet de benodigde kenmerken voor de Azure Backup-agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Is er een manier om de hoeveelheid band breedte die voor de back-up wordt gebruikt, aan te passen?

Ja, u kunt de optie **Eigenschappen wijzigen** in de Mars-agent gebruiken om de band breedte en de timing aan te passen. [Meer informatie](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Herstellen

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een actieve herstel taak Annuleer?

Als een actieve herstel taak wordt geannuleerd, wordt het herstel proces gestopt. Alle bestanden die vóór de annulering worden teruggezet, blijven geconfigureerd op de ingestelde bestemming (oorspronkelijke of alternatieve locatie), zonder terugdraai bewerkingen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](tutorial-backup-windows-server-to-azure.md) over het maken van een back-up van een Windows-computer.
