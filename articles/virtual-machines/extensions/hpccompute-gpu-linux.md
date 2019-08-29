---
title: Uitbrei ding van NVIDIA GPU-stuur programma-Azure Linux-Vm's | Microsoft Docs
description: Microsoft Azure-extensie voor het installeren van NVIDIA GPU-Stuur Programma's op virtuele machines met een N-serie en Linux.
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
ms.author: roiyz
ms.openlocfilehash: 167780971ec59efd1ca197958798564d1ef2d596
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092318"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Uitbrei ding van NVIDIA GPU-stuur programma voor Linux

## <a name="overview"></a>Overzicht

Met deze uitbrei ding worden NVIDIA GPU-Stuur Programma's geïnstalleerd op Vm's uit de Linux N-serie. Afhankelijk van de VM-familie installeert de uitbrei ding CUDA of GRID-Stuur Programma's. Wanneer u NVIDIA-Stuur Programma's installeert met behulp van deze uitbrei ding, accepteert u de voor waarden van de [gebruiksrecht overeenkomst](https://go.microsoft.com/fwlink/?linkid=874330)van de NVIDIA en gaat u ermee akkoord. Tijdens het installatie proces kan de virtuele machine opnieuw worden opgestart om de installatie van het stuur programma te volt ooien.

Instructies voor de hand matige installatie van de Stuur Programma's en de huidige ondersteunde [versies](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)zijn hier beschikbaar.
Er is ook een uitbrei ding beschikbaar om NVIDIA GPU-Stuur Programma's te installeren op Vm's uit de [Windows N-serie](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OS distributies, afhankelijk van de ondersteuning van Stuur Programma's voor een specifieke versie van het besturings systeem.

| Distributie | Version |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6 |

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
    "type": "NvidiaGpuDriverLinux",
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
| Type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Instellingen

Alle instellingen zijn optioneel. Standaard wordt de kernel niet bijgewerkt als deze niet is vereist voor de installatie van het stuur programma, installeert u het meest recente ondersteunde stuur programma en de CUDA Toolkit (indien van toepassing).

| Name | Description | Default Value | Geldige waarden | Gegevenstype |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | De kernel bijwerken, zelfs als deze niet vereist is voor installatie van Stuur Programma's | false | true, false | boolean |
| driverVersion | NV Versie van GRID-stuur programma<br> NC/ND: CUDA Toolkit-versie. De meest recente Stuur Programma's voor de gekozen CUDA worden automatisch geïnstalleerd. | latest | YRASTER ' 430,30 ', ' 418,70 ', ' 410,92 ', ' 410,71 ', ' 390,75 ', ' 390,57 ', ' 390,42 '<br> CUDA "10.0.130", "9.2.88", "9.1.85" | string |
| installCUDA | Installeer de CUDA Toolkit. Alleen relevant voor virtuele machines van de NC/ND-serie. | true | true, false | boolean |


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

In het volgende voor beeld worden de bovenstaande Azure Resource Manager-en Power shell-voor beelden Spie gels en worden ook aangepaste instellingen toegevoegd als voor beeld voor een niet-standaard installatie van een stuur programma. Met name wordt de kernel van het besturings systeem bijgewerkt en wordt een specifiek stuur programma van de CUDA Toolkit geïnstalleerd.

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

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van Azure PowerShell en Azure CLI. Voer de volgende opdracht uit om de implementatie status van uitbrei dingen voor een bepaalde virtuele machine weer te geven.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Extensie uitvoering uitvoer wordt vastgelegd in het volgende bestand:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Afsluit codes

| Afsluitcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is geslaagd |
| 1 | Onjuist gebruik van uitbrei ding | Uitvoer logboek van uitvoering controleren |
| 10 | Linux-integratie Services voor Hyper-V en Azure zijn niet beschikbaar of geïnstalleerd | Controleer de uitvoer van lspci |
| 11 | NVIDIA GPU niet gevonden op deze VM-grootte | Een [ondersteunde VM-grootte en besturings systeem](../linux/n-series-driver-setup.md) gebruiken |
| 12 | Aanbieding met installatie kopieën wordt niet ondersteund |
| 13 | VM-grootte wordt niet ondersteund | Een N-serie-VM gebruiken om te implementeren |
| 14 | De bewerking is mislukt | Uitvoer logboek van uitvoering controleren |


### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/community/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Linux](features-linux.md)voor meer informatie over uitbrei dingen.

Zie grootten van [virtuele machines](../linux/sizes-gpu.md)die zijn geoptimaliseerd voor GPU voor meer informatie over vm's van de N-serie.
