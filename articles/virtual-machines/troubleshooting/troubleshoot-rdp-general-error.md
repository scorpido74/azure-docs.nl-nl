---
title: Een algemene RDP-fout oplossen voor een Windows-VM in Azure | Microsoft Documenten
description: Meer informatie over het oplossen van een algemene DP-fout op een Windows-vm in Azure | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058004"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Een algemene RDP-fout in Azure VM oplossen

In dit artikel wordt een algemene fout beschreven die u ondervinden wanneer u een RDP-verbinding (Remote Desktop Protocol) maakt met een Windows Virtual Machine (VM) in Azure.

## <a name="symptom"></a>Symptoom

Wanneer u een RDP-verbinding maakt met een Venster-VM in Azure, ontvangt u mogelijk het volgende algemene foutbericht:

**Extern bureaublad kan om een van de volgende redenen geen verbinding maken met de externe computer:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar in het netwerk**

**Controleer of de externe computer is ingeschakeld en verbonden is met het netwerk en dat externe toegang is ingeschakeld.**

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als gevolg van de volgende oorzaken:

### <a name="cause-1"></a>Oorzaak 1

De RDP-component is als volgt uitgeschakeld:

- Op componentniveau
- Op het niveau van de luisteraar
- Op de terminalserver
- In de rol Extern bureaublad-sessiehost

### <a name="cause-2"></a>Oorzaak 2

Extern bureaublad-services (TermService) wordt niet uitgevoerd.

### <a name="cause-3"></a>Oorzaak 3

De RDP-listener is verkeerd geconfigureerd.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, [maakt u een back-up van de schijf van het besturingssysteem](../windows/snapshot-copy-managed-disk.md)en [koppelt u de schijf van het besturingssysteem aan een reddings-vm](troubleshoot-recovery-disks-portal-windows.md)en volgt u de stappen.

### <a name="serial-console"></a>Seriële console

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Stap 1: CMD-instantie openen in seriële console

1. Toegang tot de [seriële console](serial-console-windows.md) door **ondersteuning & seriële** > **console (Preview)** te selecteren. Als de functie is ingeschakeld op de VM, u de vm met succes verbinden.

2. Maak een nieuw kanaal voor een CMD-instantie. Typ **CMD** om het kanaal te starten om de kanaalnaam te krijgen.

3. Schakel over naar het kanaal waarop de CMD-instantie wordt uitgevoerd, in dit geval moet het kanaal 1 zijn.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Stap 2: Controleer de waarden van RDP-registersleutels:

1. Controleer of de RDP is uitgeschakeld door de politie.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Als het domeinbeleid bestaat, wordt de instelling voor het lokale beleid overschreven.
      - Als in het domeinbeleid staat dat RDP is uitgeschakeld (1), werkt u het AD-beleid bij vanaf de domeincontroller.
      - Als in het domeinbeleid staat dat RDP is ingeschakeld (0), is er geen update nodig.
      - Als het domeinbeleid niet bestaat en het lokale beleid aangeeft dat RDP is uitgeschakeld (1), schakelt u RDP in met de volgende opdracht: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Controleer de huidige configuratie van de terminalserver.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Als de opdracht 0 retourneert, is de terminalserver uitgeschakeld. Schakel vervolgens de terminalserver als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. De Terminal Server-module is ingesteld op de afvoermodus als de server zich op een terminalserverfarm (RDS of Citrix) bevindt. Controleer de huidige modus van de Terminal Server-module.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Als de opdracht 1 retourneert, wordt de Terminal Server-module ingesteld op de afvoermodus. Stel vervolgens de module als volgt in op de werkmodus:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Controleer of u verbinding maken met de terminalserver.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Als de opdracht 1 retourneert, u geen verbinding maken met de terminalserver. Schakel vervolgens de verbinding als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Controleer de huidige configuratie van de RDP-listener.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Als de opdracht 0 retourneert, is de RDP-listener uitgeschakeld. Schakel vervolgens de luisteraar als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Controleer of u verbinding maken met de RDP-listener.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Als de opdracht 1 retourneert, u geen verbinding maken met de RDP-listener. Schakel vervolgens de verbinding als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Start de VM opnieuw.

8. Sluit de CMD-instantie `exit`af door te typen en druk twee keer op **Enter.**

9. Start de VM `restart`opnieuw op door te typen en maak vervolgens verbinding met de virtuele machine.

Als het probleem zich nog steeds voordoet, gaat u naar stap 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Stap 2: Extern bureaublad-services inschakelen

Zie [Extern bureaublad-services start niet op een Azure VM](troubleshoot-remote-desktop-services-issues.md)voor meer informatie.

#### <a name="step-3-reset-rdp-listener"></a>Stap 3: RDP-listener opnieuw instellen

Zie [Extern bureaublad wordt vaak losgekoppeld in Azure VM](troubleshoot-rdp-intermittent-connectivity.md)voor meer informatie.

### <a name="offline-repair"></a>Offline reparatie

#### <a name="step-1-turn-on-remote-desktop"></a>Stap 1: Extern bureaublad inschakelen

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een verbinding met Extern bureaublad met de herstel-vm.
3. Controleer of de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.
4. Start een verbinding met Extern bureaublad met de herstel-vm.
5. Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Uitvoeren als beheerder).** Voer de volgende scripts uit. In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze stationsletter met de juiste waarde voor uw VM.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Als de vm is samengevoegd, controleert u de volgende registersleutel om te zien of er een groepsbeleid is waarmee RDP wordt uitgeschakeld. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Als deze sleutelwaarde is ingesteld op 1, betekent dit dat RDP is uitgeschakeld door het beleid. Als u Extern bureaublad wilt inschakelen via het GPO-beleid, wijzigt u het volgende beleid van domeincontroller:

   
      **Computerconfiguratie\Beleid\Beheersjablonen:**

      Beleidsdefinities\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely using Remote Desktop Services
  
1. Maak de schijf los van de reddings-VM.
1. [Maak een nieuwe virtuele machine vanaf de schijf](../windows/create-vm-specialized.md).

Als het probleem zich nog steeds voordoet, gaat u naar stap 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Stap 2: Extern bureaublad-services inschakelen

Zie [Extern bureaublad-services start niet op een Azure VM](troubleshoot-remote-desktop-services-issues.md)voor meer informatie.

#### <a name="step-3-reset-rdp-listener"></a>Stap 3: RDP-listener opnieuw instellen

Zie [Extern bureaublad wordt vaak losgekoppeld in Azure VM](troubleshoot-rdp-intermittent-connectivity.md)voor meer informatie.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
