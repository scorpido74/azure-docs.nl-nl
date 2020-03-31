---
title: Problemen met de implementatie van virtuele machines oplossen als gevolg van losstaande schijven | Microsoft Documenten
description: Problemen met de implementatie van virtuele machines oplossen vanwege losstaande schijven
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486818"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Problemen met de implementatie van virtuele machines oplossen vanwege losstaande schijven

## <a name="symptom"></a>Symptoom

Wanneer u een virtuele machine probeert bij te werken waarvan de vorige gegevensschijf is mislukt, u deze foutcode tegenkomen.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een gegevensschijf opnieuw probeert te koppelen waarvan de laatste losmakende bewerking is mislukt. De beste manier om eruit te komen van deze staat is om de falende schijf los te maken.

## <a name="solution-1-powershell"></a>Oplossing 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Stap 1: De details van de virtuele machine en schijf

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Stap 2: Stel de vlag in voor het niet 'waar' van schijven.

Haal de arrayindex van de falende schijf en stel de **toBeDetached-vlag** in voor de falende schijf (waarvoor **attachdiskwhile-ontregelde** fout is opgetreden) op "true". Deze instelling houdt in dat de schijf wordt losgekoppeld van de virtuele machine. De foute schijfnaam is te vinden in de **errorMessage**.

> ! Opmerking: De API-versie die is opgegeven voor Aan- en Aanroepen voor worden gebeld, moet 2019-03-01 of hoger zijn.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

U deze schijf ook loskoppelen via de onderstaande opdracht, wat handig is voor gebruikers die API-versies vóór 01 maart 2019 gebruiken.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Stap 3: Update de virtuele machine

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Oplossing 2: RUST

### <a name="step-1-get-the-virtual-machine-payload"></a>Stap 1: Krijg de virtuele machine payload.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Stap 2: Stel de vlag in voor het niet 'waar' van schijven.

Stel de **toBeDetached-vlag** in voor het niet waar maken van de schijf in de payload die in stap 1 is geretourneerd. Let op: de API-versie die is `2019-03-01` opgegeven voor Get and Put-aanroepen moet groter zijn of groter zijn.

**Voorbeeldaanvraaginstantie**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Afwisselend u ook de falende gegevensschijf uit de bovenstaande payload verwijderen, wat handig is voor gebruikers die API-versies gebruiken vóór 01 maart 2019.

### <a name="step-3-update-the-virtual-machine"></a>Stap 3: Update de virtuele machine

Gebruik de payload van de aanvraaginstantie in stap 2 en werk de virtuele machine als volgt bij:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Voorbeeldreactie:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen [met RDP-verbindingen met een Azure VM](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw VM.

Zie Problemen met de connectiviteit van [toepassingen op een Windows-vm oplossen voor](troubleshoot-app-connection.md)problemen met de toegang tot toepassingen op uw vm.
