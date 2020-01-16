---
title: Er treedt een interne fout op wanneer u een RDP-verbinding maakt met Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het oplossen van de interne fouten RDP in Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: be0f61b1458fa8bd63d85669c7956a789892996a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981333"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Een interne fout treedt op wanneer u probeert verbinding maken met een Azure-VM via Extern bureaublad

Dit artikel wordt een fout die optreden kunnen wanneer u probeert verbinding maken met een virtuele machine (VM) in Microsoft Azure beschreven.
> [!NOTE]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../../azure-resource-manager/management/deployment-models.md). In dit artikel bevat informatie over het Resource Manager-implementatiemodel, dat wordt u aangeraden voor nieuwe implementaties in plaats van het klassieke implementatiemodel.

## <a name="symptoms"></a>Symptomen

U kan geen verbinding maken met een Azure VM met behulp van remote desktop protocol (RDP). De verbinding mislukt in de sectie 'Externe configureren' of ontvangt u de volgende strekking weergegeven:

- Interne fout van RDP
- Een interne fout is opgetreden
- Deze computer kan niet worden verbonden met de externe computer. Probeer opnieuw verbinding te maken. Als het probleem zich blijft voordoen, neem dan contact op met de eigenaar van de externe computer of de netwerkbeheerder


## <a name="cause"></a>Oorzaak

Dit probleem kan optreden voor de volgende redenen:

- De lokale versleutelingssleutels van RSA kunnen niet worden geopend.
- TLS-protocol is uitgeschakeld.
- Het certificaat is beschadigd of verlopen.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

Om dit probleem wilt oplossen, gebruikt u de seriële Console of [herstel de virtuele machine offline](#repair-the-vm-offline) door het koppelen van de besturingssysteemschijf van de virtuele machine aan een virtuele machine voor herstel.


### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

Verbinding maken met [seriële Console en open PowerShell exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële Console niet is ingeschakeld op de virtuele machine, gaat u naar de [herstel de virtuele machine offline](#repair-the-vm-offline) sectie.

#### <a name="step-1-check-the-rdp-port"></a>Stap: 1 Controleer de RDP-poort

1. In een PowerShell-sessie, gebruikt u de [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) om te controleren of poort 8080 wordt gebruikt door andere toepassingen:

        Netstat -anob |more
2. Als Termservice.exe 8080 poort wordt gebruikt, gaat u naar stap 2. Als een andere service of toepassing dan Termservice.exe 8080 poort wordt gebruikt, volg deze stappen:

    1. Stop de service voor de toepassing die van de 3389-service gebruikmaakt:

            Stop-Service -Name <ServiceName> -Force

    2. Start de terminal service:

            Start-Service -Name Termservice

2. Als de toepassing kan niet worden gestopt of als deze methode geldt niet voor u, wijzigt u de poort voor RDP:

    1. Wijzig de poort:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Stel de firewall voor de nieuwe poort:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Bijwerken van de netwerkbeveiligingsgroep voor de nieuwe poort](../../virtual-network/security-overview.md) in de Azure portal RDP-poort.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Stap 2: Juiste machtigingen instellen voor de RDP-zelf-ondertekend certificaat

1.  Voer de volgende opdrachten één voor één om de zelf-ondertekend RDP-certificaat te vernieuwen in een PowerShell-exemplaar:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Als u het certificaat niet vernieuwen met behulp van deze methode, probeert u het zelfondertekende certificaat van RDP op afstand vernieuwen:

    1. Vanaf een werkende virtuele machine die verbonden met de virtuele machine is er die problemen, type **mmc** in de **uitvoeren** vak om Microsoft Management Console te openen.
    2. Op de **bestand** in het menu **module toevoegen/verwijderen**, selecteer **certificaten**, en selecteer vervolgens **toevoegen**.
    3. Selecteer **computeraccounts**, selecteer **een andere Computer**, en voeg het IP-adres van de virtuele machine van het probleem.
    4. Ga naar de **externe Desktop\Certificates** map met de rechtermuisknop op het certificaat, en vervolgens en selecteer **verwijderen**.
    5. In een PowerShell-exemplaar van de seriële Console, de configuratie voor extern bureaublad-service opnieuw te starten:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Opnieuw instellen van de machtiging voor de map MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Start de VM opnieuw op en probeer het vervolgens Start een extern-bureaubladverbinding met de virtuele machine. Als de fout zich blijft voordoen, gaat u naar de volgende stap.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Stap 3: Alle ondersteunde TLS-versies inschakelen

De RDP-client maakt gebruik van TLS 1.0 als het standaard-protocol. Dit kan echter worden gewijzigd in TLS 1.1, waarvan de nieuwe standaard is geworden. Als TLS 1.1 op de virtuele machine is uitgeschakeld, mislukt de verbinding.
1.  In een CMD-instantie, schakelt u het TLS-protocol:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Om te voorkomen dat het beleid AD wijzigingen worden overschreven door het bijwerken van Groepsbeleid tijdelijk te stoppen:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Start de VM opnieuw zodat de wijzigingen van kracht. Als het probleem is opgelost, voert u de volgende opdracht uit om opnieuw het groepsbeleid:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Als de wijziging is hersteld, betekent dit dat er een Active Directory-beleid in het domein van uw bedrijf is. U hoeft te wijzigen dat beleid om te voorkomen dat dit probleem zich nogmaals voordoet.

### <a name="repair-the-vm-offline"></a>Herstel de virtuele machine Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteemschijf koppelen aan een virtuele machine voor herstel

1. [De besturingssysteemschijf koppelen aan een virtuele machine voor herstel](../windows/troubleshoot-recovery-disks-portal.md).
2. Nadat de besturingssysteemschijf is gekoppeld aan de virtuele machine voor herstel, zorg ervoor dat de schijf is gemarkeerd als **Online** in de Schijfbeheer-console. Houd er rekening mee de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf.
3. Start een externe bureaubladverbinding met de virtuele machine voor herstel.

#### <a name="enable-dump-log-and-serial-console"></a>Dump logboek- en seriële Console inschakelen

Om in te schakelen dump logboek- en seriële Console, voer het volgende script.

1. Open een opdrachtprompt met verhoogde bevoegdheid-sessie (**als administrator uitvoeren**).
2. Voer het volgende script uit:

    In dit script, we gaan ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf F. vervangen deze stationsletter door de juiste waarde voor uw virtuele machine.

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
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>De machtigingen voor map MachineKeys opnieuw instellen

1. Open een opdrachtprompt met verhoogde bevoegdheid-sessie (**als administrator uitvoeren**).
2. Voer het volgende script uit. In dit script, we gaan ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf F. vervangen deze stationsletter door de juiste waarde voor uw virtuele machine.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Inschakelen van alle ondersteunde TLS-versies

1.  Open een opdrachtprompt met verhoogde bevoegdheid-sessie (**als administrator uitvoeren**), en de voert de volgende opdrachten. Het volgende script wordt ervan uitgegaan dat de stationsletter is toegewezen aan de gekoppelde besturingssysteemschijf F. vervangen deze stationsletter door de juiste waarde is voor uw virtuele machine.
2.  Controleer welke TLS is ingeschakeld:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Als de sleutel bestaat niet of is de waarde ervan **0**, schakelt u het protocol door het uitvoeren van de volgende scripts:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  NLA inschakelen:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [De OS-schijf loskoppelen en opnieuw maken van de virtuele machine](../windows/troubleshoot-recovery-disks-portal.md), en controleer vervolgens of het probleem opgelost is.





