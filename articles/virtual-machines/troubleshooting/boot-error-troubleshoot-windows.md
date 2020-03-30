---
title: Azure Virtual Machines shutdown zit vast bij Het opnieuw opstarten, afsluiten of stoppen van services | Microsoft Documenten
description: Met dit artikel u servicefouten in Azure Windows Virtual Machines oplossen.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371357"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM shutdown zit vast bij 'Opnieuw opstarten', 'Afsluiten' of 'Services stoppen'

In dit artikel vindt u stappen om de problemen op te lossen van berichten 'Opnieuw opstarten', 'Afsluiten' of 'Services stoppen' die u tegenkomen wanneer u een virtuele Windows-machine (VM) opnieuw opstart in Microsoft Azure.

## <a name="symptoms"></a>Symptomen

Wanneer u [Opstartdiagnose](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de virtuele machine weer te geven, ziet u mogelijk dat op de schermafbeelding het bericht 'Opnieuw opstarten', 'Afsluiten' of 'Services stoppen' wordt weergegeven.

![Servicesschermen opnieuw opstarten, afsluiten en stoppen](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Oorzaak

Windows gebruikt het afsluitproces om systeemonderhoudsbewerkingen uit te voeren en proceswijzigingen zoals updates, rollen en functies. Het wordt afgeraden om dit kritieke proces te onderbreken totdat het is voltooid. Afhankelijk van het aantal updates/wijzigingen en de VM-grootte kan het proces lang duren. Als het proces wordt gestopt, is het mogelijk dat het besturingssysteem beschadigd raakt. Onderbreek het proces alleen als het te lang duurt.

## <a name="solution"></a>Oplossing

### <a name="collect-a-process-memory-dump"></a>Een procesgeheugendump verzamelen

1. Download [het procdump-hulpprogramma](http://download.sysinternals.com/files/Procdump.zip) in een nieuwe of bestaande gegevensschijf, die is gekoppeld aan een werkende VM uit dezelfde regio.

2. Maak de schijf los met de bestanden die nodig zijn van de werkende vm en voeg de schijf aan uw kapotte vm. We noemen deze schijf de **Utility disk**.

Gebruik [Seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) om de volgende stappen uit te voeren:

1. Open een administratieve Powershell en controleer de service die is opgehangen bij het stoppen.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Op een administratieve CMD, krijg de PID van de opgehangen dienst.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Krijg een memory dump monster <STOPPING SERVICE>van het opgehangen proces.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Dood nu het opgehangen proces om het shutdown proces te ontgrendelen.

   ``
   taskkill /PID <PID> /t /f
   ``

Zodra het OS opnieuw begint, als het normaal start, zorg er dan gewoon voor dat de consistentie van het besturingssysteem ok is. Als er melding wordt gemaakt van corruptie, voert u de volgende opdracht uit totdat de schijf zonder corruptie is:

``
dism /online /cleanup-image /restorehealth
``

Als u niet in staat bent om een proces geheugen dump te verzamelen, of dit probleem is recursieve en u moet een root cause analyse, ga verder met het verzamelen van een OS memory dump hieronder, de opbrengst om een ondersteuningsverzoek te openen.

### <a name="collect-an-os-memory-dump"></a>Een geheugendump van het besturingssysteem verzamelen

Als het probleem niet is opgelost nadat u hebt gewacht tot de wijzigingen zijn verwerkt, moet u een geheugendumpbestand verzamelen en contact opnemen met ondersteuning. Voer de volgende stappen uit om het bestand Dump te verzamelen:

**De OS-schijf koppelen aan een herstel-vm**

1. Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)meer informatie .

2. [Koppel de OS-schijf aan een herstelvm](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Extern bureaublad naar de herstel-VM.

4. Als de osschijf is versleuteld, moet u de versleuteling uitschakelen voordat u naar de volgende stap gaat. Zie [De versleutelde besturingssysteemschijf in de vm decoderen die niet kan worden opgestart.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)

**Dumpbestand zoeken en een ondersteuningsticket indienen**

1. Ga op de herstel-vm naar de Windows-map in de bijgevoegde osschijf. Als de stuurprogrammaletter die is toegewezen aan de gekoppelde osschijf F is, moet u naar F:\Windows gaan.

2. Zoek het memory.dmp-bestand en [dien vervolgens een ondersteuningsticket in](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het dumpbestand.

Als u het dumpbestand niet vinden, verplaatst u de volgende stap om dumplogboek en seriële console in te schakelen.

**Dumplog en Seriële console inschakelen**

Voer het volgende script uit om dumplog en Seriële console in te schakelen.

1. Open de sessie Prompt met verhoogde opdracht (Uitvoeren als beheerder).

2. Voer het volgende script uit:

   In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze door de juiste waarde in uw VM.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Controleer of er voldoende ruimte op de schijf is om evenveel geheugen toe te wijzen als het RAM-geheugen, afhankelijk van de grootte die u voor deze virtuele machine selecteert.

4. Als er niet genoeg ruimte is of als de VM groot is (G-, GS- of E-reeks), u de locatie wijzigen waar dit bestand wordt gemaakt en dat doorverwijzen naar een andere gegevensschijf, die is gekoppeld aan de VM. Als u de locatie wilt wijzigen, moet u de volgende sleutel wijzigen:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Maak de osschijf los en koppel de osschijf opnieuw aan de betreffende VM.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

6. Start de VM en krijg toegang tot de seriële console.

7. Selecteer Niet-maskerbare interrupt (NMI) verzenden om de geheugendump te activeren.

   ![Niet-maskerbare onderbreking verzenden](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Voeg de OS-schijf opnieuw toe aan een herstel-vm en verzamel het dumpbestand.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Nadat u het dumpbestand hebt verzameld, neemt u contact op met microsoft-ondersteuning om de hoofdoorzaak vast te stellen.
