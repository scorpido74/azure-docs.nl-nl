---
title: Windows reboot loop op een Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met windows reboot loop | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443583"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows reboot loop op een Azure VM
In dit artikel wordt de rebootlus beschreven die u ervaren op een Windows Virtual Machine (VM) in Microsoft Azure.

## <a name="symptom"></a>Symptoom

Wanneer u [Opstartdiagnostiek](./boot-diagnostics.md) gebruikt om de schermafbeeldingen van een VM te krijgen, merkt u dat de virtuele machine wordt opgestart, maar het opstartproces wordt onderbroken en het proces opnieuw begint.

![Startscherm 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Oorzaak

De rebootlus treedt op vanwege de volgende oorzaken:

### <a name="cause-1"></a>Oorzaak 1

Er is een service van derden die als kritiek wordt gemarkeerd en deze niet kan worden gestart. Hierdoor wordt het besturingssysteem opnieuw opgestart.

### <a name="cause-2"></a>Oorzaak 2

Er zijn enkele wijzigingen aangebracht in het besturingssysteem. Meestal zijn deze gerelateerd aan een update-installatie, installatie van toepassingen of een nieuw beleid. Mogelijk moet u de volgende logboeken controleren voor meer informatie:

- Gebeurtenislogboeken
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Oorzaak 3

Beschadiging van het bestandssysteem kan dit veroorzaken. Het is echter moeilijk om de verandering te diagnosticeren en te identificeren die de corruptie van het besturingssysteem veroorzaakt.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, [maakt u een back-up van de osschijf](../windows/snapshot-copy-managed-disk.md)en [koppelt u de schijf van het besturingssysteem aan een reddings-vm](../windows/troubleshoot-recovery-disks-portal.md)en volgt u de oplossingsopties dienovereenkomstig of probeert u de oplossingen één voor één.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Zodra de schijf van het besturingssysteem is gekoppeld aan een werkende vm, moet u ervoor zorgen dat de schijf is gemarkeerd als **online** in de schijfbeheerconsole en de stationsletter van de partitie met de **map \Windows** noteren.

2. Als de schijf is ingesteld op **Offline,** stelt u deze in op **Online**.

3. Maak een kopie van de map **\Windows\System32\config** voor het geval een terugdraaiing op de wijzigingen nodig is.

4. Open op de reddings-VM de Windows Registry Editor (regedit).

5. Selecteer de **HKEY_LOCAL_MACHINE-toets** en selecteer **Vervolgens Bijenkorf** > **bestand** in het menu.

6. Blader naar het systeembestand in de map **\Windows\System32\config.**

7. Selecteer **Open,** typ **BROKENSYSTEM** voor de naam, vouw de **HKEY_LOCAL_MACHINE** sleutel uit en dan ziet u een extra sleutel genaamd **BROKENSYSTEM**.

8. Controleer vanuit welke ControlSet de computer wordt opgestart. U ziet het sleutelnummer in de volgende registersleutel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Controleer welke de kritiek van de VM-agentservice is via de volgende registersleutel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Als de waarde van de registersleutel niet is ingesteld op **2,** gaat u naar de volgende beperking.

11. Als de waarde van de registersleutel is ingesteld op **2**, wijzigt u de waarde van **2** naar **1**.

12. Als een van de volgende toetsen bestaat en ze hebben waarde **2** of **3,** en verander deze waarden dan in **1** dienovereenkomstig:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecteer de **BROKENSYSTEM-toets** en **selecteer** > **Hive bestand uitladen** in het menu.

14. Maak de OS-schijf los van de VM voor het oplossen van problemen.

15. Verwijder de schijf van de vm voor het oplossen van problemen en wacht ongeveer 2 minuten tot Azure deze schijf vrijgeeft.

16. [Maak een nieuwe virtuele machine vanaf de osschijf](../windows/create-vm-specialized.md).

17. Als het probleem is opgelost, moet u mogelijk de [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe) opnieuw installeren.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

De VM terugzetten naar de laatst bekende goede configuratie, volgt de stappen in [Azure Windows VM starten met laatst bekende goede configuratie.](https://support.microsoft.com/help/4016731/)

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3
>[!NOTE]
>De volgende procedure mag alleen als laatste resource worden gebruikt. Hoewel het herstellen van regback de toegang tot de machine kan herstellen, wordt het besturingssysteem niet als stabiel beschouwd omdat er gegevens verloren gaan in het register tussen de tijdstempel van de korf en de huidige dag. U moet een nieuwe virtuele machine bouwen en plannen maken om gegevens te migreren.

1. Zodra de schijf is gekoppeld aan een VM voor het oplossen van problemen, moet u ervoor zorgen dat de schijf is gemarkeerd als **Online** in de schijfbeheerconsole.

2. Maak een kopie van de map **\Windows\System32\config** voor het geval een terugdraaiing op de wijzigingen nodig is.

3. Kopieer de bestanden in de map **\Windows\System32\config\regback** en vervang de bestanden in de map **\Windows\System32\config.**

4. Verwijder de schijf van de vm voor het oplossen van problemen en wacht ongeveer 2 minuten tot Azure deze schijf vrijgeeft.

5. [Maak een nieuwe virtuele machine vanaf de osschijf](../windows/create-vm-specialized.md).


