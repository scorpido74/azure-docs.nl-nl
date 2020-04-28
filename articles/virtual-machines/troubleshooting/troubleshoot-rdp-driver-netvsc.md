---
title: Problemen oplossen met een probleem met netvsc. sys wanneer u extern verbinding maakt met een virtuele machine met Windows 10 of Windows Server 2016 in azure | Microsoft Docs
description: Informatie over het oplossen van problemen met een Netsvc. sys-gerelateerd RDP-probleem wanneer u verbinding maakt met een virtuele machine met Windows 10 of Windows Server 2016 in Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71057992"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Kan niet extern verbinding maken met een virtuele machine met Windows 10 of Windows Server 2016 in azure vanwege netvsc. sys

In dit artikel wordt uitgelegd hoe u een probleem oplost waarbij geen netwerk verbinding wordt weer gegeven wanneer u verbinding maakt met een virtuele machine (VM) van Windows 10 of Windows 2016 Server Data Center op een Hyper-V Server 2016-host.

## <a name="symptoms"></a>Symptomen

U kunt geen verbinding maken met een virtuele machine van Azure Windows 10 of Windows Server 2016 met behulp van Remote Desktop Protocol (RDP). In [Diagnostische gegevens over opstarten](boot-diagnostics.md)toont het scherm een rood kruis over de netwerk interface kaart (NIC). Dit geeft aan dat de virtuele machine geen verbinding heeft nadat het besturings systeem volledig is geladen.

Dit probleem treedt doorgaans op in Windows [build 14393](https://support.microsoft.com/help/4093120/) en [Build 15063](https://support.microsoft.com/help/4015583/). Als de versie van uw besturings systeem later is dan deze versies, is dit artikel niet van toepassing op uw scenario. Als u de versie van het systeem wilt controleren, opent u een CMD-sessie in [de console van seriële toegang](serial-console-windows.md)en voert u **ver**uit.

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de versie van het geïnstalleerde netvsc. sys-systeem bestand **10.0.14393.594** of **10.0.15063.0**is. Deze versies van netvsc. sys kunnen verhinderen dat het systeem communiceert met het Azure-platform.


## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u [een moment opname maken van de systeem schijf](../windows/snapshot-copy-managed-disk.md) van de betrokken VM als back-up. U kunt dit probleem oplossen door de seriële console te gebruiken of [de virtuele machine offline te herstellen](#repair-the-vm-offline) door de systeem schijf van de virtuele machine aan een herstel-VM te koppelen.


### <a name="use-the-serial-console"></a>De seriële console gebruiken

Maak verbinding met [de seriële console, open een Power shell-exemplaar](serial-console-windows.md)en voer vervolgens de volgende stappen uit.

> [!NOTE]
> Als de seriële console niet is ingeschakeld op uw virtuele machine, gaat u naar de sectie [de virtuele machine offline herstellen](#repair-the-vm-offline) .

1. Voer de volgende opdracht uit in een Power shell-exemplaar om de versie van het bestand op te halen (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Down load de juiste update naar een nieuwe of bestaande gegevens schijf die is gekoppeld aan een werkende VM uit dezelfde regio:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) of een latere update
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) of een latere update

3. Ontkoppel de schijf van het hulp programma van de werkende VM en koppel deze vervolgens aan de beschadigde virtuele machine.

4. Voer de volgende opdracht uit om de update te installeren op de VM:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Start de VM opnieuw.

### <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

1. [Koppel de systeem schijf aan een herstel-VM](../windows/troubleshoot-recovery-disks-portal.md).

2. Start een Extern bureaublad verbinding met de virtuele machine voor herstel.

3. Zorg ervoor dat de schijf is gemarkeerd als **online** in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde systeem schijf.

4. Maak een kopie van de map **\Windows\System32\Config** als u de wijzigingen ongedaan wilt maken.

5. Start de REGI ster-editor (Regedit. exe) op de virtuele machine voor herstel.

6. Selecteer de sleutel **HKEY_LOCAL_MACHINE** en selecteer vervolgens component **bestand** > **laden** in het menu.

7. Zoek het systeem bestand in de map **\Windows\System32\Config** .

8. Selecteer **openen**, typ **BROKENSYSTEM** voor de naam, vouw de **HKEY_LOCAL_MACHINE** sleutel uit en zoek vervolgens de extra sleutel met de naam **BROKENSYSTEM**.

9. Ga naar de volgende locatie:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Controleer in elke subsleutel (zoals 0000) de **DriverDesc** -waarde die wordt weer gegeven als **micro soft Hyper-V-netwerk adapter**.

11. Controleer in de subsleutel de **DriverVersion** -waarde die de stuurprogrammaversie van de netwerk adapter van de virtuele machine is.

12. Down load de juiste update:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) of een latere update
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) of een latere update

13. Koppel de systeem schijf als een gegevens schijf op een herstel-VM waarop u de update kunt downloaden.

14. Voer de volgende opdracht uit om de update te installeren op de VM:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Voer de volgende opdracht uit om de componenten te ontkoppelen:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Ontkoppel de systeem schijf en maak de VM opnieuw](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
