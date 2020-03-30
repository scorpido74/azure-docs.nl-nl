---
title: NVIDIA GPU Driver Extension - Azure Linux VM's
description: Microsoft Azure-extensie voor het installeren van NVIDIA GPU-stuurprogramma's op Gegevens-VM's uit de N-serie met Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250567"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU Driver Extension voor Linux

## <a name="overview"></a>Overzicht

Deze extensie installeert NVIDIA GPU drivers op Linux N-serie VM's. Afhankelijk van de VM-familie installeert de extensie CUDA- of GRID-stuurprogramma's. Wanneer u NVIDIA-stuurprogramma's installeert met behulp van deze extensie, accepteert en stemt u in met de voorwaarden van de [NVIDIA-licentieovereenkomst voor eindgebruikers.](https://go.microsoft.com/fwlink/?linkid=874330) Tijdens het installatieproces kan de VM opnieuw worden opgestart om de stuurprogramma-installatie te voltooien.

Instructies voor handmatige installatie van de stuurprogramma's en de huidige ondersteunde versies zijn [hier](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)beschikbaar.
Er is ook een extensie beschikbaar voor het installeren van NVIDIA [GPU-stuurprogramma's op Vm's uit de Windows N-serie.](hpccompute-gpu-windows.md)

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OS-distro's, afhankelijk van stuurprogrammaondersteuning voor specifieke OS-versie.

| Distributie | Versie |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "type": "NvidiaGpuDriverLinux",
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
| type | NvidiaGpuDriverLinux | tekenreeks |
| typeHandlerVersie | 1.2 | int |

### <a name="settings"></a>Instellingen

Alle instellingen zijn optioneel. Het standaardgedrag is om de kernel niet bij te werken als deze niet vereist is voor de installatie van stuurprogramma's, het nieuwste ondersteunde stuurprogramma en de CUDA-toolkit (indien van toepassing) te installeren.

| Name | Beschrijving | Standaardwaarde | Geldige waarden | Gegevenstype |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | De kernel bijwerken, zelfs als deze niet vereist is voor stuurprogramma-installatie | false | de waarde True, false | booleaans |
| stuurprogrammaVersie | NV: GRID driver versie<br> NC/ND: CUDA toolkit versie. De nieuwste drivers voor de gekozen CUDA worden automatisch geïnstalleerd. | meest recente | GRID: "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | tekenreeks |
| installCUDA | Installeer CUDA toolkit. Alleen relevant voor VM's uit de NC/ND-serie. | waar | de waarde True, false | booleaans |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure-CLI

In het volgende voorbeeld worden de bovenstaande voorbeelden van Azure Resource Manager en PowerShell weergegeven en worden aangepaste instellingen toegevoegd als voorbeeld voor niet-standaardstuurprogramma-installatie. Specifiek, het werkt de OS kernel en installeert een specifieke CUDA toolkit versie stuurprogramma.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
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

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Uitgangscodes

| Afsluitcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking geslaagd |
| 1 | Onjuist gebruik van extensie | Uitvoeringsuitvoerlogboek controleren |
| 10 | Linux Integration Services voor Hyper-V en Azure niet beschikbaar of geïnstalleerd | Controleer de uitvoer van lspci |
| 11 | NVIDIA GPU niet te vinden op deze VM-grootte | Een [ondersteunde VM-grootte en -besturingssysteem gebruiken](../linux/n-series-driver-setup.md) |
| 12 | Afbeeldingsaanbieding niet ondersteund |
| 13 | VM-grootte wordt niet ondersteund | Een N-serie VM gebruiken om te implementeren |
| 14 | Bewerking mislukt | Uitvoeringsuitvoerlogboek controleren |


### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/community/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen
Zie [Virtuele machine-extensies en -functies voor Linux voor](features-linux.md)meer informatie over extensies.

Zie [GPU-geoptimaliseerde virtuele machineformaten](../linux/sizes-gpu.md)voor meer informatie over VM's uit de N-serie.
