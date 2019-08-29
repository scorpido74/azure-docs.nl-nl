---
title: Uitbrei ding van NVIDIA GPU-stuur programma-Azure Windows-Vm's | Microsoft Docs
description: Microsoft Azure-uitbrei ding voor het installeren van NVIDIA GPU-Stuur Programma's op virtuele machines met N-serie Compute die Windows uitvoeren.
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
ms.author: roiyz
ms.openlocfilehash: c44722403967f3563472692f97cb2a114e6294cd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084462"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Uitbrei ding voor NVIDIA GPU-stuur programma voor Windows

## <a name="overview"></a>Overzicht

Met deze extensie worden NVIDIA GPU-Stuur Programma's geïnstalleerd op Vm's uit de Windows N-serie. Afhankelijk van de VM-familie installeert de uitbrei ding CUDA of GRID-Stuur Programma's. Wanneer u NVIDIA-Stuur Programma's installeert met behulp van deze uitbrei ding, accepteert u de voor waarden van de [gebruiksrecht overeenkomst](https://go.microsoft.com/fwlink/?linkid=874330)van de NVIDIA en gaat u ermee akkoord. Tijdens het installatie proces kan de virtuele machine opnieuw worden opgestart om de installatie van het stuur programma te volt ooien.

Instructies voor de hand matige installatie van de Stuur Programma's en de huidige ondersteunde versies zijn [hier](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup)beschikbaar.
Er is ook een uitbrei ding beschikbaar om NVIDIA GPU-Stuur Programma's te installeren op Vm's uit de [Linux N-serie](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OSs:

| Distributie | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012R2 | Core |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De uitbrei ding van de Microsoft Azure voor NVIDIA GPU-Stuur Programma's vereist dat de doel-VM is verbonden met internet en toegang heeft.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de uitbrei ding weer gegeven.

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

### <a name="properties"></a>properties

| Name | Waarde / voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| Type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Implementatie

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon 

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor na de implementatie configuratie een vereiste is.

De JSON-configuratie voor een VM-extensie worden genest in de bron van de virtuele machine of geplaatst op de hoofdmap of het hoogste niveau van een Resource Manager JSON-sjabloon. De plaatsing van de JSON-configuratie is van invloed op de waarde van de resourcenaam en het type. Zie voor meer informatie, [naam en type voor de onderliggende resources instellen](../../azure-resource-manager/resource-manager-template-child-resource.md). 

In het volgende voor beeld wordt ervan uitgegaan dat de extensie is genest in de resource van de virtuele machine. Wanneer het nesten van de extensie-resource, de JSON wordt geplaatst in de `"resources": []` object van de virtuele machine.

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

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van Azure PowerShell en Azure CLI. Voer de volgende opdracht uit om de implementatie status van uitbrei dingen voor een bepaalde virtuele machine weer te geven.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Foutcodes

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is geslaagd |
| 1 | De bewerking is voltooid. Opnieuw opstarten is vereist. |
| 100 | De bewerking wordt niet ondersteund of kan niet worden voltooid. | Mogelijke oorzaken: De Power shell-versie wordt niet ondersteund, de VM-grootte is geen VM van de N-serie, fout bij het downloaden van gegevens. Controleer de logboek bestanden om de oorzaak van de fout te achterhalen. |
| 240, 840 | Time-out van bewerking. | Probeer het opnieuw. |
| -1 | Uitzonde ring opgetreden. | Controleer de logboek bestanden om de oorzaak van de uitzonde ring te bepalen. |
| -5x | De bewerking is onderbroken omdat opnieuw opstarten in behandeling is. | Start de VM opnieuw op. De installatie wordt voortgezet nadat de computer opnieuw is opgestart. Uninstall moet hand matig worden aangeroepen. |


### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/community/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Windows](features-windows.md)voor meer informatie over uitbrei dingen.

Zie grootten van [virtuele machines](../windows/sizes-gpu.md)die zijn geoptimaliseerd voor GPU voor meer informatie over vm's van de N-serie.
