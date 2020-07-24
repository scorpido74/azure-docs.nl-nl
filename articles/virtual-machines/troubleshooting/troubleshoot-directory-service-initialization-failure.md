---
title: Problemen oplossen met Windows stop error – initialisatie fout van Directory service
description: Los problemen op waarbij een virtuele machine (VM) van Active Directory domein controller in Azure is vastgelopen in een lus, waarna de computer opnieuw moet worden opgestart.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028359"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Problemen oplossen met Windows stop error – initialisatie fout van Directory service

Dit artikel bevat stappen voor het oplossen van problemen waarbij een Active Directory domein controller virtuele machine (VM) in Azure is vastgelopen in een lus en aangeeft dat de computer opnieuw moet worden opgestart.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u in de scherm afbeelding dat de virtuele machine opnieuw moet worden opgestart vanwege een fout, waarbij de stop code **0XC00002E1** in Windows Server 2008 R2 of **0xC00002E2** in Windows Server 2012 of hoger wordt weer gegeven.

![In het opstart scherm van Windows Server 2012 staat dat er een probleem is opgetreden in de computer en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens wordt de computer opnieuw opgestart. ".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Oorzaak

De fout code **0xC00002E2** vertegenwoordigt **STATUS_DS_INIT_FAILURE**en de fout code **0xC00002E1** vertegenwoordigt **STATUS_DS_CANT_START**. Beide fouten treden op wanneer er een probleem is met de Directory service.

Wanneer het besturings systeem wordt opgestart, wordt het vervolgens gedwongen om automatisch opnieuw op te starten door de lokale beveiligings verificatie server (**LSASS.exe**), waarmee gebruikers aanmeldingen worden geverifieerd. Verificatie kan niet plaatsvinden wanneer het besturings systeem op de virtuele machine een domein controller is die geen lees-/schrijftoegang heeft tot de lokale Active Directory-Data Base. Vanwege een gebrek aan toegang tot **Active Directory (AD)**, kan LSASS.exe niet worden geverifieerd en wordt het systeem geforceerd opnieuw opgestart.

Deze fout kan worden veroorzaakt door een van de volgende voor waarden:

- Er is geen toegang tot de schijf met de lokale AD-data base (**NTDS. DIT**).
- De schijf met de lokale AD-data base (NTDS. DIT) heeft geen beschik bare ruimte meer.
- De lokale AD-data base (NTDS. DIT) ontbreekt.
- De virtuele machine heeft meerdere schijven en het SAN-beleid (Storage Area Network) is onjuist geconfigureerd. Het SAN-beleid is niet ingesteld op **ONLINEALL**en de niet-besturingssysteem schijven worden aan de offline modus gekoppeld op schijf beheer.
- De lokale AD-data base (NTDS. DIT) is beschadigd.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces:

1. Een herstel-VM maken en openen.
1. Vrije ruimte op schijf.
1. Controleer of het station met de AD-data base is gekoppeld.
1. Schakel de modus Directory Services terugzetten in.
1. **Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.
1. Configureer het SAN-beleid opnieuw.

> [!NOTE]
> Als deze fout optreedt, is het gast besturingssysteem niet operationeel. U kunt problemen oplossen in de offline modus om het probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om een herstel-VM voor te bereiden.
1. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="free-up-space-on-disk"></a>Ruimte vrijmaken op schijf

Als de schijf nu is gekoppeld aan een herstel-VM, controleert u of de schijf die de interne data base van Active Directory heeft, voldoende ruimte heeft om correct te worden uitgevoerd.

1. Controleer of de schijf vol is door met de rechter muisknop op het station te klikken en **Eigenschappen**te selecteren.
1. Als de schijf minder dan 300 MB beschik bare ruimte heeft, kunt u [deze uitbreiden tot Maxi maal 1 TB met behulp van Power shell](../windows/expand-os-disk.md).
1. Als de schijf 1 TB aan gebruikte ruimte heeft bereikt, voert u een schijf opruiming uit.

   1. [De gegevens schijf loskoppelen](../windows/detach-disk.md#detach-a-data-disk-using-powershell) van de verbroken virtuele machine met Power shell.
   1. Wanneer u [de gegevens schijf](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm) hebt losgekoppeld van de verbroken virtuele machine, koppelt u deze aan een werkende VM.
   1. Gebruik het [hulp programma schijf opruiming](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) om meer ruimte vrij te maken.

1. **Optioneel** : als er meer ruimte nodig is, opent u een cmd-exemplaar en voert `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` u de opdracht voor het uitvoeren van een defragmentatie op het station uit:

  * Vervang in de opdracht door `<LETTER ASSIGNED TO THE OS DISK>` de stationsletter van de besturingssysteem schijf. Als de stationsletter bijvoorbeeld is `F:` , is de opdracht `defrag F: /u /x /g` .

  * Afhankelijk van het niveau van de fragmentatie kan de gefragmentering uren in beslag nemen.

Als er voldoende ruimte op de schijf is, gaat u verder met de volgende taak.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Controleer of het station met de Active Directory Data Base is gekoppeld

1. Open een met verhoogde bevoegdheden een CMD-exemplaar en voer de volgende opdrachten uit:

   1. Register bestand laden:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      De aanwijzing `f:` gaat ervan uit dat de schijf station is `F:` . Gebruik de stationsletter die hoort bij het station met de besturingssysteem schijf.

   1. Bepaal de stationsletter en map van **NTDS. DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Register bestand verwijderen:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Controleer met behulp van Azure Portal of het station met NTDS. DIT is ingesteld, wordt toegevoegd aan de virtuele machine.
1. Gebruik de schijf beheer-console vanuit het gast besturingssysteem om te controleren of de schijf met NTDS. DIT is online.
   1. U kunt het hulp programma schijf beheer vinden in **systeem beheer > computer beheer > opslag**of toegang tot de `diskmgmt.msc` opdracht in een cmd-exemplaar.
1. Als de schijf niet is gekoppeld aan de virtuele machine, koppelt u de gegevens schijf opnieuw om het probleem op te lossen.

   Als de schijf normaal is gekoppeld, gaat u door met de volgende taak.

### <a name="enable-directory-services-restore-mode"></a>Modus Directory Services herstellen inschakelen

Stel de virtuele machine zo in dat deze wordt opgestart in de modus **Directory Services Restore Mode (DSRM)** om te voor komen dat het bestaan van NTDS wordt gecontroleerd. DIT-bestand tijdens het opstarten.

1. Voordat u doorgaat, controleert u of u de vorige taken hebt voltooid om de schijf aan een herstel-VM te koppelen en hebt bepaald op welke schijf het NTDS. DIT-bestand bevindt zich in.
1. Geef in het geval van een met verhoogde bevoegdheden een lijst op met de opstart partitie gegevens in die Store om de id van de actieve partitie te vinden:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Vervang door `< Drive Letter >` de letter die in de vorige stappen is bepaald.

   ![De scherm afbeelding toont een verhoogde CMD-instantie na het invoeren van de opdracht ' bcdedit/Store <stationsletter>: \boot\bcd Enum ', waarmee Windows Boot Manager met de id wordt weer gegeven.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Schakel de `safeboot DsRepair` vlag in op de opstart partitie:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Vervang `< Drive Letter >` en `< Identifier >` door de waarden die u in de vorige stappen hebt bepaald.

1. Zoek opnieuw naar de opstart opties om ervoor te zorgen dat de wijziging correct is ingesteld.

   ![De scherm afbeelding toont een verhoogde CMD-instantie nadat de SafeBoot DsRepair-vlag is ingeschakeld.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in

Als u geheugen dump verzameling en seriële console wilt inschakelen, voert u het volgende script uit door een opdracht prompt sessie met verhoogde bevoegdheden te openen (als administrator uitvoeren) en voert u de volgende opdrachten uit.

1. De seriële console inschakelen:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Controleer of de beschik bare ruimte op de besturingssysteem schijf ten minste gelijk is aan de geheugen grootte (RAM) op de virtuele machine.

  1. Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst u naar alle gegevens schijven die zijn gekoppeld aan de VM met voldoende beschik bare ruimte.

     Als u de locatie wilt wijzigen, vervangt u door `%SystemRoot%` de stationsletter (zoals, `F:` ) van de gegevens schijf in de volgende opdrachten.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>De volgende configuratie wordt voorgesteld om OS dump in te scha kelen:

  **Beschadigde besturingssysteem schijf laden**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Inschakelen op ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Inschakelen op ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Beschadigde besturingssysteem schijf verwijderen**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

1. Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.

### <a name="reconfigure-the-storage-area-network-policy"></a>Het Storage Area Network-beleid opnieuw configureren

1. Bij het opstarten in de modus Active Directory terugzetten is de enige gebruiker die zich aanmeldt, de herstel beheerder die is gebruikt toen de virtuele machine werd gepromoveerd naar een domein controller. Voor alle andere gebruikers wordt een verificatie fout weer gegeven.

   1. Als er geen andere domein controller beschikbaar is, moet u zich lokaal aanmelden met `.\administrator` of `machinename\administrator` en het wacht woord voor DSRM.

1. Stel het SAN-beleid zo in dat alle schijven online zijn.

   1. Open een met verhoogde bevoegdheden een CMD-exemplaar en voer in `DISKPART` .
   1. Query voor de lijst met schijven.

      `DISKPART> list disk`

   1. Voer de volgende opdrachten in om de schijf te selecteren die online moet worden gebracht en het SAN-beleid te wijzigen:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Wanneer het probleem is opgelost, controleert u of de markering `DsRepair safeboot` is verwijderd:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Start de VM opnieuw op.

   > [!NOTE]
   > Als uw virtuele machine zojuist is gemigreerd van on-premises en u meer domein controllers van on-premises naar Azure wilt migreren, kunt u de volgende stappen volgen om te voor komen dat dit probleem zich voordoet in toekomstige migraties:
   >
   > [Bestaande on-premises Hyper-V-domein controllers uploaden naar Azure met behulp van Azure PowerShell](https://support.microsoft.com/help/2904015)
