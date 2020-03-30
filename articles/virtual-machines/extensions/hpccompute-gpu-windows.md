---
title: NVIDIA GPU Driver Extension - Azure Windows VM's
description: Microsoft Azure-extensie voor het installeren van NVIDIA GPU-stuurprogramma's op gegevensvm's uit de N-serie met Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250541"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA GPU Driver Extension voor Windows

## <a name="overview"></a>Overzicht

Deze extensie installeert NVIDIA GPU-stuurprogramma's op Vm's uit de Windows N-serie. Afhankelijk van de VM-familie installeert de extensie CUDA- of GRID-stuurprogramma's. Wanneer u NVIDIA-stuurprogramma's installeert met behulp van deze extensie, accepteert en stemt u in met de voorwaarden van de [NVIDIA-licentieovereenkomst voor eindgebruikers.](https://go.microsoft.com/fwlink/?linkid=874330) Tijdens het installatieproces kan de VM opnieuw worden opgestart om de stuurprogramma-installatie te voltooien.

Instructies voor handmatige installatie van de stuurprogramma's en de huidige ondersteunde versies zijn [hier](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup)beschikbaar.
Een extensie is ook beschikbaar voor het installeren van NVIDIA GPU drivers op [Linux N-serie VM's.](hpccompute-gpu-linux.md)

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende bes:

| Distributie | Versie |
|---|---|
| Windows 10 | Kern |
| Windows Server 2016 | Kern |
| Windows Server 2012 R2 | Kern |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Microsoft Azure Extension for NVIDIA GPU Drivers vereist dat de doel-VM is verbonden met het internet en toegang heeft.

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de extensie.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Eigenschappen

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| uitgever | Microsoft.HpcCompute | tekenreeks |
| type | NvidiaGpuDriverWindows | tekenreeks |
| typeHandlerVersie | 1.2 | int |


## <a name="deployment"></a>Implementatie

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon 

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor een configuratie na implementatie vereist is.

De JSON-configuratie voor een virtuele machine-extensie kan worden genest in de bron van de virtuele machine of op het hoofd- of hoogste niveau van een JSON-sjabloon voor Resource Manager worden geplaatst. De plaatsing van de JSON-configuratie is van invloed op de waarde van de resourcenaam en -type. Zie [Naam en type voor onderliggende bronnen instellen voor](../../azure-resource-manager/resource-manager-template-child-resource.md)meer informatie . 

In het volgende voorbeeld wordt ervan uitgegaan dat de extensie is genest in de bron van de virtuele machine. Bij het nesten van de extensiebron wordt `"resources": []` de JSON in het object van de virtuele machine geplaatst.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure-CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met Azure PowerShell en Azure CLI. Voer de volgende opdracht uit om de implementatiestatus van extensies voor een bepaalde vm te bekijken.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Foutcodes

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking geslaagd |
| 1 | Operatie geslaagd. Opnieuw opstarten vereist. |
| 100 | Bewerking die niet wordt ondersteund of niet kan worden voltooid. | Mogelijke oorzaken: PowerShell-versie wordt niet ondersteund, VM-grootte is geen N-serie VM, Fout downloaden van gegevens. Controleer de logboekbestanden om de oorzaak van de fout te bepalen. |
| 240, 840 | Een time-out van de operatie. | Probeer de operatie opnieuw. |
| -1 | Uitzondering opgetreden. | Controleer de logboekbestanden om de oorzaak van de uitzondering te bepalen. |
| -5x | Bewerking onderbroken vanwege in behandeling zijnde herstart. | VM opnieuw opstarten. De installatie wordt voortgezet na het opnieuw opstarten. Verwijderen moet handmatig worden ingeroepen. |


### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/community/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen
Zie [Virtuele machine-extensies en -functies voor Windows voor](features-windows.md)meer informatie over extensies.

Zie [GPU-geoptimaliseerde virtuele machineformaten](../windows/sizes-gpu.md)voor meer informatie over VM's uit de N-serie.
