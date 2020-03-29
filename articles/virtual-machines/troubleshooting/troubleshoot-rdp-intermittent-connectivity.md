---
title: Extern bureaublad verbreekt vaak de verbinding in Azure VM| Microsoft Documenten
description: Meer informatie over het oplossen van frequente onderbrekingen van Extern bureaublad in Azure VM.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918220"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>De verbinding met Extern bureaublad wordt regelmatig verbroken in de Azure-VM

In dit artikel wordt uitgelegd hoe u frequente onderbrekingen van een virtuele Azure-machine (VM) oplossen via Extern bureaublad-protocol RDP).


## <a name="symptom"></a>Symptoom

U krijgt tijdens uw sessies te maken met intermitterende RDP-connectiviteitsproblemen. U in eerste instantie verbinding maken met de VM, maar dan wordt de verbinding verbroken.

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de RDP-listener verkeerd is geconfigureerd. Dit probleem treedt meestal op bij een vm die een aangepaste afbeelding gebruikt.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, [maakt u een momentopname van de OS-schijf](../windows/snapshot-copy-managed-disk.md) van de betreffende VM als back-up. 

Als u dit probleem wilt oplossen, gebruikt u Seriële besturingselement of [bevestigt u de VM offline](#repair-the-vm-offline) door de OS-schijf van de VM aan een herstelvm te koppelen.

### <a name="serial-control"></a>Seriële controle

1. Maak verbinding [met seriële console en open CMD-instantie](./serial-console-windows.md). Voer vervolgens de volgende opdrachten uit om de RDP-configuraties opnieuw in te stellen. Als de seriële console niet is ingeschakeld op uw vm, gaat u naar de volgende stap.
2. Verlaag de RDP-beveiligingslaag op 0. Bij deze instelling maakt communicatie tussen server en client gebruik van de native RDP-versleuteling.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Verlaag het versleutelingsniveau tot de minimale instelling om oudere RDP-clients verbinding te laten maken.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Rdp instellen om de gebruikersconfiguratie van de clientcomputer te laden.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Schakel de RDP Keep-Alive-besturingselement in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Stel het besturingselement RDP Reconnect in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Stel het besturingselement RDP-sessietijd in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Stel het besturingselement Rdp-ontkoppelingstijd in: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Stel het besturingselement RDP-verbindingstijd in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Stel het besturingselement Inactieve RDP-sessie in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Stel het besturingselement 'De maximale gelijktijdige verbindingen beperken' in:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Start de VM opnieuw en probeer er opnieuw verbinding mee te maken met RDP.

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Nadat de osschijf is gekoppeld aan de herstel-vm, moet u ervoor zorgen dat de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.
3. Navigeer op de besturingssysteemschijf die u hebt gekoppeld naar de map **\windows\system32\config.** Kopieer alle bestanden in deze map als back-up, voor het geval een terugdraaiing vereist is.
4. Start de Register-Editor (regedit.exe).
5. Selecteer de **HKEY_LOCAL_MACHINE** toets. Selecteer In het menu **File** > **Load Hive:**
6. Blader naar de map **\windows\system32\config\SYSTEM** op de besturingssysteemschijf die u hebt gekoppeld. Voer **BROKENSYSTEM**in voor de naam van de korf. De nieuwe registerkorf wordt weergegeven onder de **HKEY_LOCAL_MACHINE** sleutel. Laad vervolgens de software hive **\windows\system32\config\SOFTWARE** onder de **HKEY_LOCAL_MACHINE** toets. Voer BROKENSOFTWARE in voor de naam van de **hive-software.** 
7. Open een verhoogd opdrachtpromptvenster **(Uitvoeren als beheerder)** en voer opdrachten uit in de resterende stappen om de RDP-configuraties opnieuw in te stellen. 
8. Verlaag de RDP-beveiligingslaag op 0, zodat de communicatie tussen de server en de client de native RDP-versleuteling gebruikt:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Verlaag het versleutelingsniveau tot de minimale instelling om oudere RDP-clients verbinding te laten maken:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Stel RDP in om de gebruikersconfiguratie van de clientmachine te laden.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Schakel de RDP Keep-Alive-besturingselement in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Stel het besturingselement RDP Reconnect in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Stel het besturingselement RDP-sessietijd in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Stel het besturingselement Rdp-ontkoppelingstijd in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Stel het besturingselement RDP-verbindingstijd in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Het besturingselement Idle Time van de RDP-sessie instellen: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Stel het besturingselement 'De maximale gelijktijdige verbindingen beperken' in:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Start de VM opnieuw en probeer er opnieuw verbinding mee te maken met RDP.

## <a name="need-help"></a>Hebt u hulp nodig? 
Neem contact op met ondersteuning. Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.





