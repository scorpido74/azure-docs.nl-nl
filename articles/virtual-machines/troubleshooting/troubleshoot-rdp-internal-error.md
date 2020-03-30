---
title: Er treedt een interne fout op wanneer u een RDP-verbinding met virtuele azure-machines maakt | Microsoft Documenten
description: Meer informatie over het oplossen van interne RDP-fouten in Microsoft Azure.| Microsoft Documenten
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
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266921"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Er treedt een interne fout op wanneer u via Extern bureaublad probeert te verbinden met een Azure-VM

In dit artikel wordt een fout beschreven die u ondervinden wanneer u verbinding probeert te maken met een virtuele machine (VM) in Microsoft Azure.


## <a name="symptoms"></a>Symptomen

U geen verbinding maken met een Azure VM met behulp van het Remote Desktop Protocol (RDP). De verbinding blijft steken in de sectie 'Extern configureren' of u ontvangt het volgende foutbericht:

- INTERNE RDP-fout
- Er is een interne fout opgetreden
- Deze computer kan niet worden aangesloten op de externe computer. Probeer opnieuw verbinding te maken. Als het probleem zich blijft aanhouden, neemt u contact op met de eigenaar van de externe computer of uw netwerkbeheerder


## <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen om de volgende redenen:

- De lokale RSA-coderingssleutels zijn niet toegankelijk.
- HET TLS-protocol is uitgeschakeld.
- Het certificaat is beschadigd of verlopen.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de OS-schijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u dit probleem wilt oplossen, gebruikt u de seriële console of [herstelt u de VM offline](#repair-the-vm-offline) door de OS-schijf van de VM aan een herstelvm te koppelen.


### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

Maak verbinding [met seriële console en open PowerShell-instantie](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële console niet is ingeschakeld op uw vm, gaat u naar [de sectie offline vm repareren.](#repair-the-vm-offline)

#### <a name="step-1-check-the-rdp-port"></a>Stap: 1 Controleer de RDP-poort

1. Gebruik in een PowerShell-instantie de [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) om te controleren of poort 8080 door andere toepassingen wordt gebruikt:

        Netstat -anob |more
2. Als Termservice.exe de 8080-poort gebruikt, gaat u naar stap 2. Als een andere service of toepassing dan Termservice.exe de 8080-poort gebruikt, voert u de volgende stappen uit:

    1. Stop de service voor de toepassing die de 3389-service gebruikt:

            Stop-Service -Name <ServiceName> -Force

    2. Start de terminalservice:

            Start-Service -Name Termservice

2. Als de toepassing niet kan worden gestopt of als deze methode niet op u van toepassing is, wijzigt u de poort voor RDP:

    1. De poort wijzigen:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Stel de firewall in voor de nieuwe poort:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Werk de netwerkbeveiligingsgroep voor de nieuwe poort](../../virtual-network/security-overview.md) in de RdP-poort van azure-portal bij.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Stap 2: Juiste machtigingen instellen voor het zelfondertekende RDP-certificaat

1.  Voer in een PowerShell-exemplaar één voor één de volgende opdrachten uit om het zelfondertekende CERTIFICAAT van RDP te vernieuwen:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Als u het certificaat niet verlengen met deze methode, probeert u het zelfondertekende certificaat van RDP op afstand te vernieuwen:

    1. Typ **mmc** in het vak **Uitvoeren** om Microsoft Management Console te openen, van een werkende VM met verbinding met de VM die problemen ondervindt.
    2. Selecteer in het menu **Bestand** de optie **Module toevoegen/verwijderen,** selecteer **Certificaten**en selecteer **Vervolgens Toevoegen**.
    3. Selecteer **Computeraccounts,** selecteer **Een andere computer**en voeg vervolgens het IP-adres van de probleem-VM toe.
    4. Ga naar de map **Extern bureaublad\Certificaten,** klik met de rechtermuisknop op het certificaat en selecteer **Verwijderen**.
    5. Start in een PowerShell-exemplaar vanaf de seriële console de service Extern bureaublad-configuratie opnieuw:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. De machtiging voor de map MachineKeys opnieuw instellen.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Start de VM opnieuw en probeer een verbinding met Extern bureaublad met de vm. Als de fout zich nog steeds voordoet, gaat u naar de volgende stap.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Stap 3: Alle ondersteunde TLS-versies inschakelen

De RDP-client gebruikt TLS 1.0 als standaardprotocol. Dit kan echter worden gewijzigd in TLS 1.1, wat de nieuwe standaard is geworden. Als TLS 1.1 is uitgeschakeld op de VM, mislukt de verbinding.
1.  Schakel in een CMD-instantie het TLS-protocol in:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Als u wilt voorkomen dat het AD-beleid de wijzigingen overschrijft, stopt u de update van het groepsbeleid tijdelijk:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Start de VM opnieuw op, zodat de wijzigingen van kracht worden. Als het probleem is opgelost, voert u de volgende opdracht uit om het groepsbeleid opnieuw in te schakelen:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Als de wijziging wordt teruggedraaid, betekent dit dat er een Active Directory-beleid in uw bedrijfsdomein is. U moet dat beleid wijzigen om te voorkomen dat dit probleem zich opnieuw voordoet.

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Nadat de osschijf is gekoppeld aan de herstel-vm, moet u ervoor zorgen dat de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.
3. Start een verbinding met Extern bureaublad met de herstel-vm.

#### <a name="enable-dump-log-and-serial-console"></a>Dumplog en Seriële console inschakelen

Voer het volgende script uit om dumplog en Seriële console in te schakelen.

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Uitvoeren als beheerder).**
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze stationsletter met de juiste waarde voor uw VM.

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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>De machtiging voor de map MachineKeys opnieuw instellen

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Uitvoeren als beheerder).**
2. Voer het volgende script uit. In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze stationsletter met de juiste waarde voor uw VM.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Alle ondersteunde TLS-versies inschakelen

1.  Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Voer uit als beheerder)** en voer de volgende opdrachten uit. In het volgende script wordt ervan uitgegaan dat de stuurprogrammaletter is toegewezen aan de gekoppelde OS-schijf F. Vervang deze stationsletter door de juiste waarde voor uw VM.
2.  Controleer welke TLS is ingeschakeld:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Als de sleutel niet bestaat of als de waarde **0**is, schakelt u het protocol in door de volgende scripts uit te voeren:

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
5.  [Maak de OS-schijf los en maak de VM opnieuw](../windows/troubleshoot-recovery-disks-portal.md)en controleer of het probleem is opgelost.





