---
title: De Azure VM-agent installeren in offlinemodus | Microsoft Documenten
description: Meer informatie over het installeren van de Azure VM Agent in offlinemodus.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920855"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>De Azure Virtual Machine Agent installeren in offlinemodus 

De Azure Virtual Machine Agent (VM Agent) biedt handige functies, zoals het opnieuw instellen van het wachtwoord van de lokale beheerder en het pushen van scripts. In dit artikel ziet u hoe u de VM-agent voor een virtuele Windows-virtuele machine (VM) installeert. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Wanneer de VM-agent in de offlinemodus gebruiken

Installeer de VM-agent in de offlinemodus in de volgende scenario's:

- De geïmplementeerde Azure VM heeft de VM-agent niet geïnstalleerd of de agent werkt niet.
- U bent het beheerderswachtwoord voor de vm vergeten of u hebt geen toegang tot de vm.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>De VM-agent installeren in de offlinemodus

Gebruik de volgende stappen om de VM-agent in de offlinemodus te installeren.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Stap 1: De OS-schijf van de VM als gegevensschijf aan een andere vm koppelen

1. Maak een momentopname voor de OS-schijf van de betreffende VM, maak een schijf van de momentopname en voeg de schijf toe aan een probleemoplossingsvm. Zie [Problemen met een Windows-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met behulp van de Azure-portal](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie. Verwijder voor de klassieke VM de VM en bewaar de osschijf en voeg de OS-schijf toe aan de vm voor problemen oplossen.

2.  Maak verbinding met de probleemoplosser-vm. **Schijfbeheer** > **openen**. Controleer of de schijf van het besturingssysteem online is en dat er stationsletters zijn toegewezen aan de schijfpartities.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Stap 2: De osschijf wijzigen om de Azure VM-agent te installeren

1.  Maak een externe bureaubladverbinding met de probleemoplosser-vm.

2.  Blader in de probleemoplosser-VM naar de besturingssysteemschijf die u hebt gekoppeld en open de map \windows\system32\config. Kopieer alle bestanden in deze map als back-up, voor het geval een terugdraaiing vereist is.

3.  Start de **registereditor** (regedit.exe).

4.  Selecteer de **HKEY_LOCAL_MACHINE** toets. Selecteer In het menu **File** > **Load Hive:**

    ![Laad de korf](./media/install-vm-agent-offline/load-hive.png)

5.  Blader naar de map \windows\system32\config\SYSTEM op de besturingssysteemschijf die u hebt gekoppeld. Voer **BROKENSYSTEM**in voor de naam van de korf. De nieuwe registerkorf wordt weergegeven onder de **HKEY_LOCAL_MACHINE** sleutel.

6.  Blader naar de map \windows\system32\config\SOFTWARE op de besturingssysteemschijf die u hebt gekoppeld. Voer BROKENSOFTWARE in voor de naam van de **hive-software.**

7. Als de gekoppelde besturingssysteemschijf de VM-agent heeft geïnstalleerd, voert u een back-up van de huidige configuratie uit. Als de VM-agent niet is geïnstalleerd, gaat u naar de volgende stap.
      
    1. Wijzig de naam van de map \windowsazure in \windowsazure.old.

    2. Exporteer de volgende registers:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Gebruik de bestaande bestanden op de probleemoplosser VM als opslagplaats voor de VM Agent-installatie. Voltooi de volgende stappen:

    1. Exporteer vanuit de probleemoplosser VM de volgende subsleutels in registerformaat (.reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![De registersubsleutels exporteren](./media/install-vm-agent-offline/backup-reg.png)

    2. Bewerk de registerbestanden. Wijzig in elk bestand het **invoerwaardesysteem** in **BROKENSYSTEM** (zoals in de volgende afbeeldingen wordt weergegeven) en sla het bestand op. Denk aan het **ImagePath** van de huidige VM-agent. We moeten de bijbehorende map kopiëren naar de bijgevoegde OS-schijf. 

        ![De subsleutelwaarden van het register wijzigen](./media/install-vm-agent-offline/change-reg.png)

    3. Importeer de registerbestanden in de opslagplaats door op elk registerbestand te dubbelklikken.

    4. Controleer of de volgende drie subsleutels met succes zijn geïmporteerd in de **brokensystem-korf:**
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent (RdAgent)

    5. Kopieer de installatiemap van de huidige VM-agent naar de bijgevoegde osschijf: 

        1.  Maak op de besturingssysteemschijf die u hebt gekoppeld een map met de naam WindowsAzure in het hoofdpad.

        2.  Ga naar C:\WindowsAzure op de probleemoplosser VM, zoek naar een map met de naam C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Kopieer de Map GuestAgent met het nieuwste versienummer van C:\WindowsAzure naar de WindowsAzure-map in de bijgevoegde osschijf. Als u niet zeker weet welke map moet worden gekopieerd, kopieert u alle GuestAgent-mappen. In de volgende afbeelding ziet u een voorbeeld van de map GuestAgent die naar de bijgevoegde osschijf is gekopieerd.

             ![Map GuestAgent kopiëren](./media/install-vm-agent-offline/copy-files.png)

9.  Selecteer **BROKENSYSTEM**. Selecteer Hive**bij het uitladen van** **bestanden** > in het menu .

10.  Selecteer **BROKENSOFTWARE**. Selecteer Hive**bij het uitladen van** **bestanden** > in het menu .

11.  Maak de osschijf los en [wijzig vervolgens de osschijf voor de betreffende VM.](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm) Maak voor de klassieke VM een nieuwe VM met behulp van de gerepareerde OS-schijf.

12.  Toegang tot de VM. Merk op dat de RdAgent wordt uitgevoerd en de logboeken worden gegenereerd.

Als u de VM hebt gemaakt met behulp van het implementatiemodel ResourceManager, bent u klaar.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>De eigenschap ProvisionGuestAgent gebruiken voor klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Als u de VM hebt gemaakt met behulp van het klassieke model, gebruikt u de Azure PowerShell-module om de eigenschap **ProvisionGuestAgent** bij te werken. De eigenschap informeert Azure dat de VM de VM-agent heeft geïnstalleerd.

Voer de volgende opdrachten uit in Azure PowerShell als u de eigenschap **ProvisionGuestAgent** wilt instellen:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Voer vervolgens `Get-AzureVM` de opdracht uit. De eigenschap **GuestAgentStatus** is nu gevuld met gegevens:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Virtual Machine Agent](../extensions/agent-windows.md)
- [Virtuele machine-extensies en -functies voor Windows](../extensions/features-windows.md)
