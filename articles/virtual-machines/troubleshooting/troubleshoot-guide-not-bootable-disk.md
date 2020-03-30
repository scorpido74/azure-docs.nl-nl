---
title: Opstartfout – "dit is geen opstartbare schijf"
description: In dit artikel worden stappen gezet om problemen op te lossen waarbij de schijf niet kan worden opgestart in een Azure Virtual Machine
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300977"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Opstartfout : dit is geen opstartschijf

In dit artikel worden stappen gezet om problemen op te lossen waarbij de schijf niet kan worden opgestart in een Virtuele Machine (Azure).

## <a name="symptoms"></a>Symptomen

Wanneer u [Opstartdiagnose](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de vm weer te geven, ziet u dat de schermafbeelding een prompt weergeeft met het bericht 'Dit is geen opstartbare schijf. Plaats een opstartbare floppy en druk op een toets om het opnieuw te proberen...'.

   Afbeelding 1

   ![Figuur 1 toont het bericht *"Dit is geen opstartbare schijf. Plaats een opstartbare floppy en druk op een toets om het opnieuw te proberen..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Oorzaak

Dit foutbericht betekent dat het opstartproces van het besturingssysteem geen actieve systeempartitie kan vinden. Deze fout kan ook betekenen dat er een ontbrekende verwijzing in het BCD-archief (Boot Configuration Data) is, waardoor de Windows-partitie niet kan worden gevonden.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van proces

1. Een reparatievm maken en openen.
2. Partitiestatus instellen op Actief.
3. Repareer de schijfpartitie.
4. **Aanbevolen:** Voordat u de VM opnieuw opbouwt, schakelt u de verzameling seriële console en geheugendump in.
5. De oorspronkelijke vm opnieuw opbouwen.

   > [!NOTE]
   > Wanneer u deze opstartfout tegenkomt, is het besturingssysteem van de gast niet operationeel. U lost problemen op in de offlinemodus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een reparatievm maken en openen

1. Gebruik stap 1-3 van de [VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een reparatie-vm voor te bereiden.
2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

### <a name="set-partition-status-to-active"></a>Partitiestatus instellen op Actief

Generatie 1 VM's moeten eerst controleren of de OS-partitie, die het BCD-archief bevat, is gemarkeerd als *actief.* Als u een generatie 2-VM hebt, gaat u verder [naar Fix the Disk Partition](#fix-the-disk-partition), omdat de *statusvlag* in de latere generatie is afgeschaft.

1. Open een opdrachtprompt met verhoogde bevoegdheid *(cmd.exe).*
2. Voer *schijfonderdeel* in om het gereedschap DISKPART te starten.
3. Voer *lijstschijf* in om de schijven op het systeem weer te geven en het bijgevoegde BE VHD te identificeren.
4. Zodra het aangesloten BE VHD zich bevindt, voert u *seldisk #* in om de schijf te selecteren.  Zie figuur 2, waar Schijf 1 het aangesloten OS VHD is.

   Afbeelding 2

   ![Figuur 2 toont het venster *DISKPART*met de uitvoer van de opdracht lijstschijf, Schijf 0 en Schijf 1 die in de tabel worden weergegeven.  Toont ook de uitvoer van de opdracht selschijf 1, Schijf 1 is de geselecteerde schijf](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Zodra de schijf is geselecteerd, voert u *lijstpartitie* in om de partities van de geselecteerde schijf weer te geven.
6. Zodra de opstartpartitie is geïdentificeerd, voert u *selpartitie #* in om de partitie te selecteren.  Meestal is de opstartpartitie ongeveer 350 MB groot.  Zie figuur 3, waarbij partitie 1 de opstartpartitie is.

   Figuur 3

   ![Figuur 3 toont het venster *DISKPART*met de uitvoer van de opdracht *lijstpartitie*. Partitie 1 en Partitie 2 worden weergegeven in de tabel. Het toont ook de uitvoer van de *sel partitie 1* opdracht, wanneer Partitie 1 de geselecteerde schijf is.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Voer 'detailpartitie' in om de status van de partitie te controleren. Zie figuur 4, waar de partitie *actief is: Nee*of figuur 5, waarbij de partitie 'Actief: Ja' is.

   Figuur 4

   ![Figuur 4 toont het *DISKPART*-venster met de uitvoer van de opdracht *detailpartitie*, wanneer partitie 1 is ingesteld op *Actief: Nee*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figuur 5

   ![Figuur 5 toont het venster *DISKPART*met de uitvoer van de opdracht *detailpartitie*, wanneer partitie 1 is ingesteld op *Actief: Ja*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Als de partitie **niet actief**is, voert u *actief* in om de *actieve* vlag te wijzigen.
9. Controleer of de statuswijziging correct is uitgevoerd door *detailpartitie te*typen .

   Figuur 6

   ![Figuur 6 toont het venster van het schijfonderdeel met de uitvoer van *detailpartitie*, wanneer partitie 1 is ingesteld op *Actief: Ja*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Voer *de afsluiting in* om het gereedschap DISKPART te sluiten en uw configuratiewijzigingen op te slaan.

### <a name="fix-the-disk-partition"></a>De schijfpartitie herstellen

1. Open een opdrachtprompt met verhoogde bevoegdheid (cmd.exe).
2. Gebruik de volgende opdracht om *CHKDSK* op de schijf(en) uit te voeren en fouten op te lossen:

   `chkdsk <DRIVE LETTER>: /f`

   Als u de opdracht '/f' toevoegt, worden eventuele fouten op de schijf opgelost. Zorg ervoor <DRIVE LETTER> dat u de letter van het bijgevoegde OS VHD vervangt.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: Voordat u de VM opnieuw opbouwt, schakelt u de verzameling seriële console en geheugendump in

Voer het volgende script uit om geheugendumpverzameling en seriële console in te schakelen:

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid (Uitvoeren als beheerder).
2. Voer de volgende opdrachten uit:

   Seriële console inschakelen

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Controleer of de vrije ruimte op de OS-schijf evenveel is als de geheugengrootte (RAM) op de VM.

   Als er niet genoeg ruimte op de OS-schijf is, moet u de locatie wijzigen waar het geheugendumpbestand wordt gemaakt en verwijzen naar een gegevensschijf die is gekoppeld aan de VM die voldoende vrije ruimte heeft. Als u de locatie wilt wijzigen, vervangt u "%SystemRoot%" door de stationsletter (bijvoorbeeld 'F:') van de gegevensschijf in de onderstaande opdrachten.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Voorgestelde configuratie om OS Dump in te schakelen

**Kapotte OS-schijf laden:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Inschakelen op ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Inschakelen op ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Kapotte OS-schijf uitladen:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>De oorspronkelijke vm opnieuw opbouwen

Gebruik [stap 5 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de VM opnieuw in elkaar te zetten.
