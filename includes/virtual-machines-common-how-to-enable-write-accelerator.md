---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74566247"
---
Write Accelerator is een schijfmogelijkheid voor Virtuele Machines uit de M-serie (VM's) op Premium Storage met Azure Managed Disks. Zoals de naam al aangeeft, is het doel van de functionaliteit om de I/O-latentie van schrijften te verbeteren ten opzichte van Azure Premium Storage. Write Accelerator is bij uitstek geschikt wanneer logboekbestandsupdates nodig zijn om op een zeer performante manier op de schijf te blijven werken voor moderne databases.

Write Accelerator is algemeen beschikbaar voor Vm's uit de M-serie in de public cloud.

## <a name="planning-for-using-write-accelerator"></a>Planning voor het gebruik van Write Accelerator

Schrijfaccelerator moet worden gebruikt voor de volumes die het transactielogboek of de redo-logboeken van een DBMS bevatten. Het wordt afgeraden om Write Accelerator te gebruiken voor de gegevensvolumes van een DBMS, omdat de functie is geoptimaliseerd om te worden gebruikt tegen logschijven.

Write Accelerator werkt alleen in combinatie met [door Azure beheerde schijven.](https://azure.microsoft.com/services/managed-disks/)

> [!IMPORTANT]
> Als u Schrijfversneller inschakelt voor de schijf van het besturingssysteem van de VM, wordt de VM opnieuw opgestart.
>
> Schrijfaccelerator inschakelen op een bestaande Azure-schijf die GEEN deel uitmaakt van een volumeopbouw van meerdere schijven met Windows-schijf- of volumemanagers, Windows-opslagruimten, Windows Scale-out-bestandsserver (SOFS), Linux LVM of MDADM, de werkbelasting die toegang heeft tot de Azure-schijf moet worden stilgelegd. Databasetoepassingen met de Azure-schijf moeten worden afgesloten.
>
> Als u Schrijfaccelerator wilt in- of uitschakelen voor een bestaand volume dat is opgebouwd uit meerdere Azure Premium-opslagschijven en gestreept met Windows-schijf- of volumemanagers, Windows-opslagruimten, Windows Scale-out-bestandsserver (SOFS), Linux LVM of MDADM, allemaal schijven die het volume bouwen, moeten in afzonderlijke stappen zijn ingeschakeld of uitgeschakeld voor Write Accelerator. **Voordat u Schrijfaccelerator in een dergelijke configuratie in- of uitschakelt, schakelt u de Azure VM uit.**

Het inschakelen van Schrijfversneller voor OS-schijven is niet nodig voor SAP-gerelateerde VM-configuraties.

### <a name="restrictions-when-using-write-accelerator"></a>Beperkingen bij het gebruik van Write Accelerator

Wanneer u Schrijfversneller voor een Azure-schijf/VHD gebruikt, zijn deze beperkingen van toepassing:

- De Premium-schijfcache moet worden ingesteld op 'Geen' of 'Alleen lezen'. Alle andere cachingmodi worden niet ondersteund.
- Momentopname wordt momenteel niet ondersteund voor schijven met schrijfaccelerator. Tijdens de back-up wordt de Azure Backup-service automatisch uitgesloten van schrijfacceleratorschijven die aan de VM zijn gekoppeld.
- Alleen kleinere I/O-maten (<=512 KiB) nemen het versnelde pad. In werkbelastingsituaties waarin gegevens bulkworden geladen of waar de transactielogboekbuffers van de verschillende DBMS in grotere mate worden gevuld voordat ze aande opslag worden vastgehouden, is de kans groot dat de I/O die naar de schijf is geschreven, niet het versnelde pad neemt.

Er zijn limieten voor Azure Premium Storage VHD's per VM die kunnen worden ondersteund door Write Accelerator. De huidige limieten zijn:

| VM-SKU | Aantal schrijfversnellerschijven | Accelerator Disk IOPS per VM schrijven |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20.000 |
| M208ms_v2 M208s_v2.| 8 | 10.000 |
| M128ms, M128's | 16 | 20.000 |
| M64ms, M64ls, M64s | 8 | 10.000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

De IOPS-limieten zijn per VM en *niet* per schijf. Alle Schrijfversnellerschijven delen dezelfde IOPS-limiet per VM.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Write Accelerator inschakelen op een bepaalde schijf

In de volgende secties wordt beschreven hoe Write Accelerator kan worden ingeschakeld op Azure Premium Storage VHDs.

### <a name="prerequisites"></a>Vereisten

De volgende voorwaarden zijn van toepassing op het gebruik van Write Accelerator op dit moment:

- De schijven waarop u Azure Write Accelerator wilt toepassen, moeten [Azure-beheerde schijven](https://azure.microsoft.com/services/managed-disks/) op Premium Storage zijn.
- U moet een VM uit de M-serie gebruiken

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure Write Accelerator inschakelen met behulp van Azure PowerShell

De Azure Power Shell-module uit versie 5.5.0 bevat de wijzigingen in de relevante cmdlets om Schrijfversneller in te schakelen of uit te schakelen voor specifieke Azure Premium Storage-schijven.
Om schijven die door Write Accelerator worden ondersteund, in te schakelen of te implementeren, zijn de volgende Power Shell-opdrachten gewijzigd en uitgebreid om een parameter voor Write Accelerator te accepteren.

Een nieuwe switchparameter, **-WriteAccelerator** is toegevoegd aan de volgende cmdlets:

- [Set-azvmosdisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-azVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Als u de parameter niet geeft, wordt de eigenschap op false ingesteld en worden schijven geïmplementeerd die geen ondersteuning hebben voor Write Accelerator.

Een nieuwe switchparameter, **-OsDiskWriteAccelerator** is toegevoegd aan de volgende cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Als u de parameter niet opgeeft, wordt de eigenschap standaard ingesteld op false, waardoor schijven worden geretourneerd die geen gebruik maken van Write Accelerator.

Een nieuwe optionele Booleaanse (niet-nullable) parameter, **-OsDiskWriteAccelerator** is toegevoegd aan de volgende cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Geef $true of $false op om de ondersteuning van Azure Write Accelerator met de schijven te beheren.

Voorbeelden van opdrachten kunnen er uitzien als:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Twee belangrijke scenario's kunnen worden gescript zoals weergegeven in de volgende secties.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Een nieuwe schijf toevoegen die wordt ondersteund door Write Accelerator met PowerShell

U dit script gebruiken om een nieuwe schijf aan uw virtuele machine toe te voegen. De schijf die met dit script is gemaakt, maakt gebruik van Write Accelerator.

Vervang `myVM` `myWAVMs`, `log001`, grootte van de schijf en LunID van de schijf door waarden die geschikt zijn voor uw specifieke implementatie.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Schrijfversneller inschakelen op een bestaande Azure-schijf met PowerShell

U dit script gebruiken om Schrijfaccelerator in te schakelen op een bestaande schijf. Vervang `myVM` `myWAVMs`, `test-log001` en door waarden die geschikt zijn voor uw specifieke implementatie. Het script voegt Schrijfversneller toe aan een bestaande schijf waar de waarde voor **$newstatus** is ingesteld op '$true'. Als u de waarde '$false' gebruikt, schakelt u Write Accelerator op een bepaalde schijf uit.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Als u het bovenstaande script uitvoert, wordt de opgegeven schijf losgekoppeld, wordt Schrijfversneller tegen de schijf ingeschakeld en wordt de schijf opnieuw bevestigd

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Write Accelerator inschakelen met behulp van de Azure-portal

U Write Accelerator inschakelen via de portal waar u de instellingen voor schijfcache opgeeft:

![Accelerator schrijven op de Azure-portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Write Accelerator inschakelen met behulp van Azure CLI

U de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) gebruiken om Schrijfaccelerator in te schakelen.

Als u Schrijfversneller op een bestaande schijf wilt inschakelen, gebruikt u [de AZ VM-update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), u de volgende voorbeelden gebruiken als u de diskName, VMName en ResourceGroup vervangt door uw eigen waarden:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Als u een schijf wilt koppelen met Write Accelerator ingeschakeld gebruik [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), u het volgende voorbeeld gebruiken als u in uw eigen waarden vervangt:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Als u Schrijfversneller wilt uitschakelen, gebruikt u [de AZ VM-update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), waarbij de eigenschappen op false worden ingesteld:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Schrijfversneller inschakelen met rest-API's

Als u wilt implementeren via Azure Rest API, moet u de Azure-client installeren.

### <a name="install-armclient"></a>Armclient installeren

Om armclient te runnen, moet je het installeren via Chocolatey. U het installeren via cmd.exe of powershell. Gebruik verhoogde rechten voor deze opdrachten ('Uitvoeren als administrator').

Voer de volgende opdracht uit met cmd.exe:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Voer de volgende opdracht uit met Power Shell:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nu u de armclient installeren met behulp van de volgende opdracht in cmd.exe of PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Uw huidige VM-configuratie verkrijgen

Als u de kenmerken van uw schijfconfiguratie wilt wijzigen, moet u eerst de huidige configuratie in een JSON-bestand opvragen. U de huidige configuratie krijgen door de volgende opdracht uit te voeren:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Vervang de termen in '<<   >>' door uw gegevens, inclusief de bestandsnaam die het JSON-bestand zou moeten hebben.

De uitvoer kan eruit zien als:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Werk vervolgens het JSON-bestand bij en schakel Write Accelerator in op de schijf genaamd 'log1'. Dit kan worden bereikt door dit kenmerk toe te voegen aan het JSON-bestand na de cache-invoer van de schijf.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Werk vervolgens de bestaande implementatie bij met deze opdracht:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

De output moet lijken op de onderstaande. U zien dat Write Accelerator ingeschakeld voor een schijf.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Zodra u deze wijziging hebt aangebracht, moet het station worden ondersteund door Write Accelerator.
