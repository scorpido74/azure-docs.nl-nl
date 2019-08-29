---
title: Een algemene RDP-fout in een Windows-VM in azure oplossen | Microsoft Docs
description: Meer informatie over het oplossen van een algemene RDP-fout naar een Windows-VM in azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: abff12e1a304c51cb0df394534c7da0a35518008
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089801"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Een algemene RDP-fout in een Azure-VM oplossen

In dit artikel wordt een algemene fout beschreven die kan optreden wanneer u een Remote Desktop Protocol (RDP)-verbinding maakt met een virtuele Windows-machine (VM) in Azure.

## <a name="symptom"></a>Symptoom

Wanneer u een RDP-verbinding maakt met een Windows-VM in azure, wordt mogelijk het volgende algemene fout bericht weer gegeven:

**Extern bureaublad kan geen verbinding maken met de externe computer voor een van de volgende redenen:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe Computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar op het netwerk**

**Zorg ervoor dat de externe computer is ingeschakeld en verbonden met het netwerk, en dat externe toegang is ingeschakeld.**

## <a name="cause"></a>Oorzaak

Dit probleem kan de volgende oorzaken hebben:

### <a name="cause-1"></a>Oorzaak 1

Het RDP-onderdeel is als volgt uitgeschakeld:

- Op onderdeel niveau
- Op het niveau van de listener
- Op de Terminal Server
- Op de functie sessiehost van Extern bureaublad

### <a name="cause-2"></a>Oorzaak 2

Extern bureaublad-services (term) wordt niet uitgevoerd.

### <a name="cause-3"></a>Oorzaak 3

De RDP-listener is onjuist geconfigureerd.

## <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door een [back-up te maken van de schijf van het besturings systeem](../windows/snapshot-copy-managed-disk.md)en [de besturingssysteem schijf te koppelen aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md)en de stappen te volgen.

### <a name="serial-console"></a>Seriële console

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Stap 1: CMD-exemplaar in Seriële console openen

1. Open de [seriële console](serial-console-windows.md) door ondersteuning te selecteren **& probleem oplossing** > **seriële console (preview)** . Als de functie is ingeschakeld op de virtuele machine, kunt u verbinding maken met de virtuele machine.

2. Een nieuw kanaal maken voor een CMD-exemplaar. Typ **cmd** om het kanaal te starten om de kanaal naam op te halen.

3. Schakel over naar het kanaal dat het CMD-exemplaar uitvoert. in dit geval moet het kanaal 1 zijn.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Stap 2: Controleer de waarden van RDP-register sleutels:

1. Controleer of RDP is uitgeschakeld door policies.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Als het domein beleid bestaat, wordt de installatie van het lokale beleid overschreven.
      - Als in het domein beleid wordt aangegeven dat RDP is uitgeschakeld (1), werkt u het AD-beleid bij van domein controller.
      - Als in het domein beleid wordt aangegeven dat RDP is ingeschakeld (0), is er geen update nodig.
      - Als het domein beleid niet bestaat en de status van het lokale beleid dat RDP is uitgeschakeld (1), schakelt u RDP in met behulp van de volgende opdracht: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Controleer de huidige configuratie van de Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Als de opdracht 0 retourneert, wordt de Terminal server uitgeschakeld. Schakel vervolgens de Terminal-Server als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. De module Terminal Server is ingesteld op de modus drain als de server zich op een Terminal server-farm (RDS of Citrix) bevindt. Controleer de huidige modus van de module Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Als de opdracht 1 retourneert, wordt de module Terminal Server ingesteld op de modus voor afvoer. Stel de module vervolgens als volgt in op Working-modus:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Controleer of u verbinding kunt maken met de Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Als de opdracht 1 retourneert, kunt u geen verbinding maken met de Terminal Server. Schakel vervolgens de verbinding als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Controleer de huidige configuratie van de RDP-listener.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Als de opdracht 0 retourneert, wordt de RDP-listener uitgeschakeld. Schakel de listener vervolgens als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Controleer of u verbinding kunt maken met de RDP-listener.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Als de opdracht 1 retourneert, kunt u geen verbinding maken met de RDP-listener. Schakel vervolgens de verbinding als volgt in:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Start de VM opnieuw.

8. Verlaat het cmd-exemplaar door te `exit`typen en vervolgens twee keer op **Enter** te drukken.

9. Start de VM opnieuw op `restart`door te typen en vervolgens verbinding te maken met de virtuele machine.

Als het probleem nog steeds optreedt, gaat u naar stap 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Stap 2: Extern bureau blad-Services inschakelen

Zie [extern bureaublad-services wordt niet gestart op een virtuele machine van Azure](troubleshoot-remote-desktop-services-issues.md)voor meer informatie.

#### <a name="step-3-reset-rdp-listener"></a>Stap 3: RDP-listener opnieuw instellen

Zie [extern bureaublad regel matig verbinding maken in azure VM](troubleshoot-rdp-intermittent-connectivity.md)voor meer informatie.

### <a name="offline-repair"></a>Offline reparatie

#### <a name="step-1-turn-on-remote-desktop"></a>Stap 1: Extern bureaublad inschakelen

1. [De besturingssysteemschijf koppelen aan een virtuele machine voor herstel](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een externe bureaubladverbinding met de virtuele machine voor herstel.
3. Zorg ervoor dat de schijf is gemarkeerd als **Online** in de Schijfbeheer-console. Houd er rekening mee de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf.
4. Start een externe bureaubladverbinding met de virtuele machine voor herstel.
5. Open een opdrachtprompt met verhoogde bevoegdheid-sessie (**als administrator uitvoeren**). Voer de volgende scripts uit. In dit script, we gaan ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf F. vervangen deze stationsletter door de juiste waarde voor uw virtuele machine.

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

6. Als de virtuele machine lid is van een domein, controleert u de volgende register sleutel om te zien of er een groeps beleid is waarmee RDP wordt uitgeschakeld. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Als deze sleutel waarde is ingesteld op 1, betekent RDP uitgeschakeld door het beleid. Als u Extern bureaublad via het GPO-beleid wilt inschakelen, wijzigt u het volgende beleid van domein controller:

   
      **Sjablonen voor Computerconfiguratie\beleid\beheersjablonen\windows-onderdelen\Extern:**

      Beleid definitions\Windows bureaublad-services\Extern bureau blad-sessie Host\Connections\Allow gebruikers om extern verbinding te maken met behulp van Extern bureaublad-services
  
1. Ontkoppel de schijf van de virtuele machine voor herstel.
1. [Maak een nieuwe virtuele machine op basis van de schijf](../windows/create-vm-specialized.md).

Als het probleem nog steeds optreedt, gaat u naar stap 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Stap 2: Extern bureau blad-Services inschakelen

Zie [extern bureaublad-services wordt niet gestart op een virtuele machine van Azure](troubleshoot-remote-desktop-services-issues.md)voor meer informatie.

#### <a name="step-3-reset-rdp-listener"></a>Stap 3: RDP-listener opnieuw instellen

Zie [extern bureaublad regel matig verbinding maken in azure VM](troubleshoot-rdp-intermittent-connectivity.md)voor meer informatie.

## <a name="need-help-contact-support"></a>Hulp nodig? Neem contact op met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
