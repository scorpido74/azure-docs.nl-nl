---
title: De virtuele machine van Azure reageert niet tijdens het Toep assen van beleid
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het laad scherm vastloopt wanneer een beleid wordt toegepast tijdens het opstarten van een Azure-VM.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749275"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>De VM reageert niet meer tijdens het Toep assen van ' groepsbeleid lokale gebruikers & groeps beleid

In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het laad scherm vastloopt wanneer u een beleid toepast tijdens het opstarten van een Azure-VM.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om een scherm opname van de virtuele machine weer te geven, wordt het scherm vastgeladen met het bericht: '*Toep assen Groepsbeleid lokale gebruikers en groeps beleid*'.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Scherm opname van het Toep assen van groepsbeleid voor het laden van lokale gebruikers en groepen (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Scherm opname van het Toep assen van groepsbeleid voor het laden van lokale gebruikers en groepen (Windows Server 2012).":::

## <a name="cause"></a>Oorzaak

Er zijn conflicterende vergren delingen wanneer het beleid probeert oude gebruikers profielen op te schonen.

> [!NOTE]
> Dit geldt alleen voor Windows Server 2012 en Windows Server 2012 R2.

Dit is het problematische beleid:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

1. [Een herstel-VM maken en openen](#step-1-create-and-access-a-repair-vm)
2. [Het beleid uitschakelen](#step-2-disable-the-policy)
3. [Seriële console-en geheugen dump verzameling inschakelen](#step-3-enable-serial-console-and-memory-dump-collection)
4. [De virtuele machine opnieuw bouwen](#step-4-rebuild-the-vm)

> [!NOTE]
> Als deze opstart fout optreedt, is het gast besturingssysteem niet operationeel. U moet problemen oplossen in de offline modus.

### <a name="step-1-create-and-access-a-repair-vm"></a>Stap 1: een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="step-2-disable-the-policy"></a>Stap 2: het beleid uitschakelen

1. Open de REGI ster-editor op de virtuele machine herstellen.
2. Zoek de sleutel **HKEY_LOCAL_MACHINE** en selecteer **File**  >  **component bestand laden...** in het menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Scherm opname toont de gemarkeerde HKEY_LOCAL_MACHINE en het menu met de Component Load.":::

    - Met het onderdeel laden kunt u register sleutels laden vanuit een offline systeem, in dit geval de defecte schijf die is gekoppeld aan de reparatie-VM.
    - Instellingen voor het hele systeem worden opgeslagen op `HKEY_LOCAL_MACHINE` en kunnen worden afgekort als HKLM.
3. Ga op de gekoppelde schijf naar het `\windows\system32\config\SOFTWARE` bestand en open het.

    1. U wordt gevraagd een naam op te vragen. Voer BROKENSOFTWARE in.<br/>
    2. Als u wilt controleren of BROKENSOFTWARE is geladen, vouwt u **HKEY_LOCAL_MACHINE** uit en zoekt u naar de toegevoegde BROKENSOFTWARE-sleutel.
4. Navigeer naar BROKENSOFTWARE en controleer of de sleutel CleanupProfile bestaat in de geladen component.

    1. Als de sleutel bestaat, wordt het CleanupProfile-beleid ingesteld. de waarde vertegenwoordigt het Bewaar beleid in dagen. Ga door met het verwijderen van de sleutel.<br/>
    2. Als de sleutel niet bestaat, wordt het CleanupProfile-beleid niet ingesteld. [Dien een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in, met inbegrip van het Memory. dmp-bestand dat zich bevindt in de Windows-map van de gekoppelde besturingssysteem schijf.

5. Verwijder de CleanupProfiles-sleutel met behulp van deze opdracht:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Verwijder de BROKENSOFTWARE-component met de volgende opdracht:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Stap 3: seriële console-en geheugen dump verzameling inschakelen

Voer dit script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer deze opdrachten uit:

    **Seriële console inschakelen**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Controleer of de beschik bare ruimte op de besturingssysteem schijf ten minste gelijk is aan de geheugen grootte (RAM) van de virtuele machine.

    Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie van de geheugen dump en verwijst u deze naar een gekoppelde gegevens schijf met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u '% System root% ' door de stationsletter (bijvoorbeeld ' F: ') van de gegevens schijf in de onderstaande opdrachten.

    **Voorgestelde configuratie voor het inschakelen**van de OS-dump:

    Beschadigde besturingssysteem schijf laden:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Inschakelen op ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Inschakelen op ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Beschadigde besturingssysteem schijf verwijderen:
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Stap 4: de virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de virtuele machine opnieuw samen te stellen.

Als het probleem is opgelost, is het beleid lokaal uitgeschakeld. Gebruik voor een permanente oplossing geen CleanupProfiles-beleid op Vm's. Gebruik een andere methode om profiel opruimingen uit te voeren.

Dit beleid niet gebruiken:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het Toep assen van Windows Update, raadpleegt u de [virtuele machine reageert niet met de fout ' C01A001D ' bij het Toep assen van Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).