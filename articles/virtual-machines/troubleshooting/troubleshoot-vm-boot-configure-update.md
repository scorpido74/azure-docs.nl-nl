---
title: VM opstarten zit vast op "Getting Windows ready. Schakel uw computer niet uit" in Azure | Microsoft Documenten
description: Introduceer de stappen om het probleem op te lossen waarbij het opstarten van VM vastzit op "Windows klaarmaken. Schakel de computer niet uit.'
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749637"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM opstarten zit vast op "Getting Windows ready. Uw computer niet uitschakelen" in Azure

In dit artikel worden de schermen 'Klaar maken' en 'Windows-klaar maken' beschreven die u tegenkomen wanneer u een Virtuele Windows-machine (VM) opstart in Microsoft Azure. Het biedt stappen om u te helpen gegevens te verzamelen voor een ondersteuningsticket.

 

## <a name="symptoms"></a>Symptomen

Een Windows-VM wordt niet opgestart. Wanneer u **Opstartdiagnose** gebruikt om de schermafbeelding van de vm te krijgen, ziet u mogelijk dat de virtuele machine het bericht 'Klaar maken' of 'Windows klaar maken' weergeeft.

![Voorbeeld van bericht voor Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Voorbeeld van bericht](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Oorzaak

Meestal treedt dit probleem op wanneer de server de laatste herstart doet nadat de configuratie is gewijzigd. De configuratiewijziging kan worden geïnitialiseerd door Windows-updates of door de wijzigingen in de rollen/functie van de server. Als de grootte van de updates groot is, heeft het besturingssysteem voor Windows Update meer tijd nodig om de wijzigingen opnieuw te configureren.

## <a name="collect-an-os-memory-dump"></a>Een geheugendump van het besturingssysteem verzamelen

Als het probleem niet is opgelost nadat u hebt gewacht tot de wijzigingen zijn verwerkt, moet u een geheugendumpbestand verzamelen en contact opnemen met ondersteuning. Voer de volgende stappen uit om het bestand Dump te verzamelen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .
2. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
3. Extern bureaublad naar de herstel-VM. 
4. Als de osschijf is versleuteld, moet u de versleuteling uitschakelen voordat u naar de volgende stap gaat. Zie [De versleutelde besturingssysteemschijf in de vm decoderen die niet kan worden opgestart.](troubleshoot-bitlocker-boot-error.md#solution)

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Dumpbestand zoeken en een ondersteuningsticket indienen

1. Ga op de herstel-vm naar de Windows-map in de bijgevoegde osschijf. Als de stuurprogrammaletter die is toegewezen aan de gekoppelde osschijf F is, moet u naar F:\Windows gaan.
2. Zoek het memory.dmp-bestand en [dien vervolgens een ondersteuningsticket in](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het dumpbestand. 

Als u het dumpbestand niet vinden, verplaatst u de volgende stap om dumplogboek en seriële console in te schakelen.

### <a name="enable-dump-log-and-serial-console"></a>Dumplog en Seriële console inschakelen

Voer het volgende script uit om dumplog en Seriële console in te schakelen.

1. Open de sessie Prompt met verhoogde opdracht (Uitvoeren als beheerder).
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is.  Vervang deze door de juiste waarde in uw VM.

    ```powershell
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
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Zorg ervoor dat er voldoende ruimte op de schijf is om evenveel geheugen toe te wijzen als het RAM-geheugen, dat afhankelijk is van de grootte die u voor deze virtuele machine selecteert.
    2. Als er niet genoeg ruimte is of als dit een VM(G-, GS- of E-reeks) is, u de locatie wijzigen waar dit bestand wordt gemaakt en dat doorverwijzen naar een andere gegevensschijf die aan de VM is gekoppeld. Hiervoor moet u de volgende toets wijzigen:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Maak de osschijf los en koppel de osschijf opnieuw aan de betreffende VM](../windows/troubleshoot-recovery-disks-portal.md).
4. Start de VM en krijg toegang tot de seriële console.
5. Selecteer **Niet-maskerbare interruptie(NMI) verzenden** om de geheugendump te activeren.
    ![de afbeelding over waar niet-maskerbaar onderbreken te verzenden](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Voeg de OS-schijf opnieuw toe aan een herstel-vm en verzamel het dumpbestand.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Nadat u het dumpbestand hebt verzameld, neemt u contact op met [microsoft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om de hoofdoorzaak te analyseren.