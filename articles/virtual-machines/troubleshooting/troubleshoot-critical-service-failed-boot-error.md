---
title: KRITIEKE SERVICE IS MISLUKT bij het opstarten van een Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van de fout '0x0000005A-KRITIEKE SERVICE MISLUKT' die optreedt bij het opstarten | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921450"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows toont 'KRITIEKE SERVICE MISLUKT' op blauw scherm bij het opstarten van een Azure VM
In dit artikel wordt de fout 'KRITIEKE SERVICE MISLUKT' beschreven die u ondervinden bij het opstarten van een Windows Virtual Machine (VM) in Microsoft Azure. Het biedt stappen voor het oplossen van problemen om de problemen op te lossen. 


## <a name="symptom"></a>Symptoom 

Een Windows-vm wordt niet gestart. Wanneer u de opstartschermafbeeldingen in [Opstartdiagnose](./boot-diagnostics.md)controleert, ziet u een van de volgende foutmeldingen op een blauw scherm:

- "Uw pc liep in een probleem en moet opnieuw opstarten. Je opnieuw beginnen. Ga voor meer informatie over dit https://windows.com/stopcodeprobleem en mogelijke oplossingen naar . Als u een ondersteuningsmedewerker belt, geeft u deze informatie: Stopcode: CRITICAL SERVICE FAILED" 
- "Uw pc liep in een probleem en moet opnieuw opstarten. We verzamelen alleen wat foutinfo en dan starten we opnieuw voor je. Als u meer wilt weten, u later online zoeken naar deze fout: CRITICAL_SERVICE_FAILED

## <a name="cause"></a>Oorzaak

Er zijn verschillende oorzaken voor stopfouten. De meest voorkomende oorzaken zijn:
- Probleem met een bestuurder
- Beschadigd systeembestand of geheugen
- Toepassing toegang tot een verboden sector van het geheugen

## <a name="solution"></a>Oplossing 

Neem contact op met ondersteuning om dit probleem op te lossen [en dien een dumpbestand in,](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)waarmee we het probleem sneller kunnen diagnosticeren of probeer de volgende zelfhulpoplossing.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .
2. [Koppel de OS-schijf aan een herstelvm](./troubleshoot-recovery-disks-portal-windows.md). 
3. Een externe bureaubladverbinding met de herstel-vm tot stand brengen.

### <a name="enable-dump-logs-and-serial-console"></a>Dumplogboeken en seriële console inschakelen

Het dumplogboek en [de serial console](./serial-console-windows.md) helpen ons om verdere probleemoplossing te bieden.

Voer het volgende script uit om dumplogboeken en seriële console in te schakelen.

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid (als beheerder).
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. U moet deze vervangen door de juiste waarde voor uw vm.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>De niet-ondertekende stuurprogramma's vervangen

1. Voer op de herstelvm de volgende opdracht uit vanaf een opdrachtprompt met verhoogde bevoegdheid. Met deze opdracht wordt de betreffende os-schijf ingesteld om bij de volgende opstart in de veilige modus te starten:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Als de besturingssysteemschijf die u hebt gekoppeld bijvoorbeeld station F is, voert u de volgende opdracht uit:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Maak de osschijf los en koppel de osschijf opnieuw aan de betreffende VM.](troubleshoot-recovery-disks-portal-windows.md) De VM wordt opgestart in de veilige modus. Als u de fout nog steeds ondervindt, gaat u naar de optionele stap.
3. Open het vak **Uitvoeren** en voer **verificateur** uit om het gereedschap Stuurprogramma-verifiermanager te starten.
4. Selecteer **Automatisch niet-ondertekende stuurprogramma's selecteren**en klik op **Volgende**.
5. U krijgt de lijst met de stuurprogrammabestanden die niet zijn ondertekend. Onthoud de bestandsnamen.
6. Kopieer dezelfde versies van deze bestanden van een werkende virtuele machine en vervang deze niet-ondertekende bestanden. 

7. Verwijder de instellingen voor veilig opstarten:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Start de VM opnieuw. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Optioneel: De dumplogs analyseren in de dumpcrashmodus

Voer de volgende stappen uit om de dumplogs zelf te analyseren:

1. Koppel de besturingssysteemschijf aan een virtuele machine voor herstel.
2. Blader op de besturingssysteemschijf die u hebt gekoppeld naar **\windows\system32\config**. Kopieer alle bestanden als back-up voor het geval een terugdraaiing vereist is.
3. **Registereditor** starten (regedit.exe).
4. Selecteer de **HKEY_LOCAL_MACHINE** toets. Selecteer In het menu De knop **bestandladen** > **.**
5. Blader naar de map **\windows\system32\config\SYSTEM** op de besturingssysteemschijf die u hebt gekoppeld. Voer **BROKENSYSTEM**in voor de naam van de korf. De nieuwe registerkorf wordt weergegeven onder de **HKEY_LOCAL_MACHINE** sleutel.
6. Blader naar **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** en breng de volgende wijzigingen aan:

    Automatisch opnieuw opstarten = 0

    CrashDumpEnabled = 2
7.  Selecteer **BROKENSYSTEM**. Selecteer Hive**bij het uitladen van** **bestanden** > in het menu .
8.  Wijzig de BCD-installatie om op te starten in de foutopsporingsmodus. Voer de volgende opdrachten uit vanuit een opdrachtprompt met verhoogde bevoegdheid:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Maak de osschijf los en koppel de osschijf opnieuw aan de betreffende VM.](troubleshoot-recovery-disks-portal-windows.md)
10. Start de VM op om te zien of deze dumpanalyse toont. Zoek het bestand dat niet wordt geladen. U moet dit bestand vervangen door een bestand van de werkende vm. 

    Het volgende is een steekproef van dumpanalyse. U zien dat de **storing** is op filecrypt.sys: FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys.

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Zodra de VM normaal werkt en opstart, verwijdert u de instellingen voor dumpcrash:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
