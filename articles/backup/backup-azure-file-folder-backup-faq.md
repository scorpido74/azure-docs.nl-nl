---
title: Back-ups maken van bestanden en mappen - veelgestelde vragen
description: Hiermee worden veelvoorkomende vragen over het maken van back-ups van bestanden en mappen met Azure Backup beantwoord.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 55819ce7ec5196812d935a21c096c132144d78af
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421310"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Veelgestelde vragen over het maken van back-ups van bestanden en mappen

In dit artikel worden veelgestelde vragen beantwoord met een back-up van bestanden en mappen met de Microsoft Azure Recovery Services (MARS)-agent in de [Azure Backup-service.](backup-overview.md)

## <a name="configure-backups"></a>Back-ups configureren

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Waar kan ik de nieuwste versie van de MARS-agent downloaden?

De nieuwste MARS-agent die wordt gebruikt bij het maken van een back-up van Windows Server-machines, System Center DPM en Microsoft Azure Backup-server is beschikbaar om [te downloaden.](https://aka.ms/azurebackup_agent)

### <a name="how-long-are-vault-credentials-valid"></a>Hoe lang zijn kluisreferenties geldig?

De kluisreferenties verlopen na 48 uur. Als het bestand met referenties verloopt, downloadt u het bestand opnieuw van de Azure-portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Van welke stations kan ik een back-up maken van bestanden en mappen?

U geen back-ups maken van de volgende typen schijven en volumes:

* Verwisselbare media: alle bronnen van back-upitems moeten als opgelost rapporteren.
* Alleen-lezen volumes: Het volume moet beschrijfbaar zijn om de volumeschaduwkopieerservice (VSS) te laten functioneren.
* Offline volumes: het volume moet online zijn om VSS te laten functioneren.
* Netwerkshares: het volume moet lokaal zijn voor de server om een back-up te maken met behulp van online back-ups.
* Door BitLocker beveiligde volumes: het volume moet worden ontgrendeld voordat de back-up kan optreden.
* Bestandssysteemidentificatie: NTFS is het enige bestandssysteem dat wordt ondersteund.

### <a name="what-file-and-folder-types-are-supported"></a>Welke bestands- en maptypen worden ondersteund?

[Meer informatie](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) over de typen bestanden en mappen die worden ondersteund voor back-up.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op een Azure VM?  

Ja. Azure Backup biedt back-up op VM-niveau voor Azure VM's met behulp van de VM-extensie voor de Azure VM-agent. Als u een back-up wilt maken van bestanden en mappen op het gastWindows-besturingssysteem op de VM, u de MARS-agent installeren om dat te doen.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan ik de MARS-agent gebruiken om een back-up te maken van bestanden en mappen op tijdelijke opslag voor de Azure VM?

Ja. Installeer de MARS-agent en maakt een back-up van bestanden en mappen op het gastWindows-besturingssysteem voor tijdelijke opslag.

* Back-uptaken mislukken wanneer tijdelijke opslaggegevens worden gewist.
* Als de tijdelijke opslaggegevens worden verwijderd, u alleen herstellen naar niet-vluchtige opslag.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hoe registreer ik een server in een andere regio?

Back-upgegevens worden verzonden naar het datacenter van de kluis waarin de server is geregistreerd. De eenvoudigste manier om het datacenter te wijzigen is door de agent te verwijderen en opnieuw te installeren en de machine vervolgens te registreren bij een nieuwe kluis in de regio die u nodig hebt.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Ondersteunt de MARS-agent de ontdubbeling van Windows Server 2012?

Ja. De MARS-agent converteert de gededupliceerde gegevens naar normale gegevens wanneer de back-upbewerking wordt voorbereid. Vervolgens optimaliseert het de gegevens voor back-up, versleutelt de gegevens en stuurt de versleutelde gegevens naar de kluis.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Heb ik beheerdersmachtigingen nodig om de MARS-agent te installeren en te configureren?

Ja, de installatie van de MARS-agent en de configuratie van back-ups met behulp van de MARS-console hebben de gebruiker nodig om een lokale beheerder op de beveiligde server te zijn.

## <a name="manage-backups"></a>Back-ups beheren

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Wat gebeurt er als ik de naam van een Windows-machine die is geconfigureerd voor back-up?

Wanneer u de naam van een Windows-machine wijzigt, worden alle momenteel geconfigureerde back-ups gestopt.

* U moet de nieuwe machinenaam registreren bij de kluis Back-up.
* Wanneer u de nieuwe naam registreert bij de kluis, is de eerste bewerking een *volledige* back-up.
* Als u gegevens wilt herstellen waarop een back-up van gegevens naar de kluis is gemaakt met de oude servernaam, gebruikt u de optie om te herstellen naar een alternatieve locatie in de wizard Gegevens herstellen. [Meer informatie](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Wat is de maximale bestandspadlengte voor back-up?

De MARS-agent is afhankelijk van NTFS en gebruikt de filepathlengtespecificatie die is beperkt door de [Windows API.](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths) Als de bestanden die u wilt beveiligen langer zijn dan de toegestane waarde, maakt u een back-up van de bovenliggende map of het schijfstation.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Welke tekens zijn toegestaan in bestandspaden?

De MARS-agent is afhankelijk van NTFS en maakt [ondersteunde tekens](/windows/desktop/FileIO/naming-a-file#naming-conventions) in bestandsnamen/paden mogelijk.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>De waarschuwing 'Azure Backups zijn niet geconfigureerd voor deze server' wordt weergegeven

Deze waarschuwing kan worden weergegeven, ook al hebt u een back-upbeleid geconfigureerd, wanneer de instellingen voor back-upplanning die zijn opgeslagen op de lokale server niet dezelfde zijn als de instellingen die zijn opgeslagen in de back-upkluis.

* Wanneer de server of de instellingen zijn hersteld naar een bekende goede staat, kunnen back-upschema's worden gesynchroniseerd.
* Als u deze waarschuwing ontvangt, [configureert u](backup-azure-manage-windows-server.md) het back-upbeleid opnieuw en voert u een on-demand back-up uit om de lokale server opnieuw te synchroniseren met Azure.

## <a name="manage-the-backup-cache-folder"></a>De map back-upcache beheren

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Wat is de vereiste minimumgrootte voor de cachemap?

De grootte van de cachemap bepaalt de hoeveelheid gegevens waarvan u een back-up maakt.

* De mapvolumes in de cache moeten vrije ruimte hebben die gelijk is aan ten minste 5-10% van de totale grootte van back-upgegevens.
* Als het volume minder dan 5% vrije ruimte heeft, verhoogt u de volumegrootte of verplaatst u de cachemap naar een volume met voldoende ruimte door [deze stappen](#how-do-i-change-the-cache-location-for-the-mars-agent)te volgen.
* Als u een back-up maakt van Windows System State, hebt u 30-35 GB extra vrije ruimte nodig in het volume dat de cachemap bevat.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Hoe controleer je of de krasmap geldig en toegankelijk is?

1. Standaard bevindt de krasmap zich op`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Zorg ervoor dat het pad van de locatie van uw krasmap overeenkomt met de waarden van de onderstaande registersleutelvermeldingen:

    | Registerpad | Registersleutel | Waarde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nieuwe locatie van de cachemap* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nieuwe locatie van de cachemap* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hoe wijzig ik de cachelocatie voor de MARS-agent?

1. Voer deze opdracht uit in een opdrachtprompt met verhoogde bevoegdheid om de back-upengine te stoppen:

    ```Net stop obengine```
2. Als u de back-up van de systeemstatus hebt geconfigureerd, opent u `"CBSSBVol_<ID>"`Schijfbeheer en monteert u de schijf(en) met namen in de indeling.
3. Standaard bevindt de krasmap zich op`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Kopieer de `\Scratch` hele map naar een ander station met voldoende ruimte. Zorg ervoor dat de inhoud wordt gekopieerd, niet verplaatst.
5. Werk de volgende registervermeldingen bij met het pad van de nieuw verplaatste krasmap.

    | Registerpad | Registersleutel | Waarde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nieuwe locatie van krasmap* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nieuwe locatie van krasmap* |

6. Start de back-upengine opnieuw op een opdrachtprompt met verhoogde bevoegdheid:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Voer een on-demand back-up uit. Nadat de back-up is voltooid met de nieuwe locatie, u de oorspronkelijke cachemap verwijderen.

### <a name="where-should-the-cache-folder-be-located"></a>Waar moet de cachemap zich bevinden?

De volgende locaties voor de cachemap worden niet aanbevolen:

* Netwerkshare/verwisselbare media: de cachemap moet lokaal zijn voor de server die een back-up moet maken met behulp van online back-ups. Netwerklocaties of verwisselbare media zoals USB-stations worden niet ondersteund.
* Offline volumes: de cachemap moet online zijn voor verwachte back-up met Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Zijn er kenmerken van de cachemap die niet worden ondersteund?

De volgende kenmerken of combinaties van deze kenmerken worden niet ondersteund voor de cachemap:

* Versleuteld
* Ontdubbeld
* Gecomprimeerd
* Sparse
* Reparsepunt

De cachemap en de metagegevens VHD hebben niet de benodigde kenmerken voor de Azure Backup-agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Is er een manier om de hoeveelheid bandbreedte die wordt gebruikt voor back-up aan te passen?

Ja, u de optie **Eigenschappen wijzigen** in de MARS-agent gebruiken om de bandbreedte en timing aan te passen. [Meer informatie](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Herstellen

### <a name="manage"></a>Beheren

**Kan ik herstellen als ik mijn wachtwoordzin ben vergeten?**
De Azure Backup-agent vereist een wachtwoordzin (die u tijdens de registratie hebt opgegeven) om de back-upgegevens tijdens het herstellen te decoderen. Bekijk de onderstaande scenario's om inzicht te krijgen in uw opties voor het afhandelen van een verloren wachtwoordzin:

| Originele machine <br> *(bronmachine waar back-ups zijn gemaakt)* | Passphrase | Beschikbare opties |
| --- | --- | --- |
| Beschikbaar |Verloren |Als uw oorspronkelijke machine (waar back-ups zijn genomen) beschikbaar is en nog steeds is geregistreerd bij dezelfde vault van Recovery Services, u de wachtwoordzin opnieuw genereren door deze [stappen](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)te volgen.  |
| Verloren |Verloren |Niet mogelijk om de gegevens of gegevens te herstellen is niet beschikbaar |

Houd rekening met de volgende voorwaarden:

* Als u de agent op dezelfde originele machine verwijdert en opnieuw registreert met
  * *Dezelfde wachtwoordzin,* dan zul je in staat zijn om uw back-up gegevens te herstellen.
  * *Andere wachtwoordzin,* dan zult u niet in staat zijn om uw back-up gegevens te herstellen.
* Als u de agent op een *andere machine installeert* met
  * *Dezelfde wachtwoordzin* (gebruikt in de originele machine), dan zul je in staat zijn om uw back-up gegevens te herstellen.
  * *Verschillende wachtwoordzin*, zult u niet in staat zijn om uw back-up gegevens te herstellen.
* Als uw oorspronkelijke machine beschadigd is (waardoor u de wachtwoordzin niet regenereren via de MARS-console), maar u de oorspronkelijke krasmap die door de MARS-agent wordt gebruikt, herstellen of openen, u mogelijk herstellen (als u het wachtwoord bent vergeten). Neem voor meer hulp contact op met customer support.

**Hoe herstel ik als ik mijn oorspronkelijke machine kwijt ben (waar back-ups zijn gemaakt)?**

Als u dezelfde wachtwoordzin (die u tijdens de registratie) van de oorspronkelijke machine hebt opgegeven, u de back-upgegevens herstellen naar een alternatieve machine. Bekijk de onderstaande scenario's om inzicht te krijgen in uw herstelopties.

| Originele machine | Passphrase | Beschikbare opties |
| --- | --- | --- |
| Verloren |Beschikbaar |U de MARS-agent installeren en registreren op een andere machine met dezelfde wachtwoordzin die u tijdens de registratie van de originele machine hebt opgegeven. Hersteloptie **kiezen Een** > **andere locatie** om uw herstel uit te voeren. Zie voor meer informatie dit [artikel.](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)
| Verloren |Verloren |Niet mogelijk om de gegevens of gegevens te herstellen is niet beschikbaar |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een doorlopende hersteltaak annuleer?

Als een doorlopende hersteltaak wordt geannuleerd, wordt het herstelproces gestopt. Alle bestanden die vóór de annulering zijn hersteld, blijven in geconfigureerde bestemming (oorspronkelijke of alternatieve locatie), zonder terugdraaien.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Maakt de MARS-agent een back-up en herstelt de ACL's op bestanden, mappen en volumes?

* De MARS-agent maakt een back-up van ACL's die zijn ingesteld op bestanden, mappen en volumes
* Voor hersteloptie Volumeherstel biedt de MARS-agent een optie om het herstellen van ACL-machtigingen voor het herstel van het bestand of de map die wordt hersteld, over te slaan
* Voor de hersteloptie voor afzonderlijke bestanden en mappen herstelt de MARS-agent met ACL-machtigingen (er is geen optie om ACL-herstel over te slaan).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](tutorial-backup-windows-server-to-azure.md) het maken van een back-up van een Windows-machine.
