---
title: Een probleem met netvsc.sys oplossen wanneer u op afstand verbinding maakt met een Windows 10- of Windows Server 2016-vm in Azure | Microsoft Documenten
description: Meer informatie over het oplossen van een netsvc.sys-gerelateerd RDP-probleem wanneer u verbinding maakt met een Windows 10- of Windows Server 2016-vm in Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057992"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Kan niet op afstand verbinding maken met een Windows 10- of Windows Server 2016-vm in Azure vanwege netvsc.sys

In dit artikel wordt uitgelegd hoe u een probleem oplossen waarbij er geen netwerkverbinding is wanneer u verbinding maakt met een virtuele machine (VM) van Windows 10 of Windows Server 2016 Datacenter op een Hyper-V Server 2016-host.

## <a name="symptoms"></a>Symptomen

U geen verbinding maken met een Windows 10- of Windows Server 2016-vm met Behulp van Extern bureaublad-protocol (RDP). In [Boot diagnostics](boot-diagnostics.md)toont het scherm een rood kruis over de netwerkinterfacekaart (NIC). Dit geeft aan dat de VM geen connectiviteit heeft nadat het besturingssysteem volledig is geladen.

Dit probleem treedt meestal op in Windows [build 14393](https://support.microsoft.com/help/4093120/) en [build 15063](https://support.microsoft.com/help/4015583/). Als de versie van uw besturingssysteem later is dan deze versies, is dit artikel niet van toepassing op uw scenario. Als u de versie van het systeem wilt controleren, opent u een CMD-sessie in [de functie Serial Access Console](serial-console-windows.md)en voert u **Ver**uit.

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de versie van het geïnstalleerde netvsc.sys-systeembestand **10.0.14393.594** of **10.0.15063.0**is. Deze versies van netvsc.sys kunnen voorkomen dat het systeem interactie heeft met het Azure-platform.


## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, [maakt u een momentopname van de systeemschijf](../windows/snapshot-copy-managed-disk.md) van de betreffende VM als back-up. Als u dit probleem wilt oplossen, gebruikt u de seriële console of [herstelt u de VM offline](#repair-the-vm-offline) door de systeemschijf van de VM aan een herstelvm te koppelen.


### <a name="use-the-serial-console"></a>De seriële console gebruiken

Maak verbinding met [de seriële console, open een PowerShell-exemplaar](serial-console-windows.md)en volg deze stappen.

> [!NOTE]
> Als de seriële console niet is ingeschakeld op uw vm, gaat u naar [de sectie offline vm repareren.](#repair-the-vm-offline)

1. Voer de volgende opdracht uit in een PowerShell-instantie om de versie van het bestand te krijgen **(c:\windows\system32\drivers\netvsc.sys):**

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Download de juiste update naar een nieuwe of bestaande gegevensschijf die is gekoppeld aan een werkende vm uit dezelfde regio:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) of een latere update
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) of een latere update

3. Maak de utility disk los van de werkende VM en bevestig deze vervolgens aan de kapotte VM.

4. Voer de volgende opdracht uit om de update op de VM te installeren:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Start de VM opnieuw.

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

1. [Koppel de systeemschijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).

2. Start een verbinding met Extern bureaublad met de herstel-vm.

3. Controleer of de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde systeemschijf.

4. Maak een kopie van de map **\Windows\System32\config** voor het geval een terugdraaiing op de wijzigingen noodzakelijk is.

5. Start registereditor (regedit.exe) op de reddings-VM.

6. Selecteer de **HKEY_LOCAL_MACHINE** toets en selecteer **Vervolgens Bijenkorf** > **bestand** in het menu.

7. Zoek het systeembestand in de map **\Windows\System32\config.**

8. Selecteer **Open,** typ **BROKENSYSTEM** voor de naam, vouw de **HKEY_LOCAL_MACHINE** sleutel uit en zoek vervolgens de extra sleutel met de naam **BROKENSYSTEM**.

9. Ga naar de volgende locatie:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Controleer in elke subsleutel (zoals 0000) de **DriverDesc-waarde** die wordt weergegeven als **Microsoft HYPER-V-netwerkadapter.**

11. Controleer in de subsleutel de **driverversiewaarde** die de stuurprogrammaversie van de netwerkadapter van de vm is.

12. Download de juiste update:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) of een latere update
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) of een latere update

13. Bevestig de systeemschijf als gegevensschijf op een reddings-vm waarop u de update downloaden.

14. Voer de volgende opdracht uit om de update op de VM te installeren:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Voer de volgende opdracht uit om de bijenkasten los te maken:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Maak de systeemschijf los en maak de VM opnieuw.](../windows/troubleshoot-recovery-disks-portal.md)

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen.
