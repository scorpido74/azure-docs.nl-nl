---
title: Diagnostische gegevens voor VM's starten in Azure | Microsoft-document
description: Overzicht van de twee foutopsporingsfuncties voor virtuele machines in Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: fe2427d008b49daa6222ca981994f0dc2fbea355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476583"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Opstartdiagnoses gebruiken om virtuele machines in Azure op te lossen

Er kunnen vele redenen zijn dat een virtuele machine een niet-opstartbare status invoert. Als u problemen met uw virtuele machines wilt oplossen die zijn gemaakt met behulp van het implementatiemodel voor Resource Manager, u de volgende foutopsporingsfuncties gebruiken: ondersteuning voor console-uitvoer en schermafbeelding voor virtuele Azure-machines. 

Voor virtuele Linux-machines u de uitvoer van uw consolelogboek bekijken via de Portal. Voor zowel Virtuele Machines van Windows als Linux u met Azure een schermafbeelding van de VM van de hypervisor zien. Beide functies worden ondersteund voor virtuele Azure-machines in alle regio's. Opmerking, screenshots en uitvoer kunnen tot 10 minuten duren voordat ze in uw opslagaccount worden weergegeven.

U de optie **Diagnostische gegevens opstarten** selecteren om het logboek en de schermafbeelding weer te geven.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Veelvoorkomende opstartfouten

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Er is geen besturingssysteem gevonden](https://support.microsoft.com/help/4010142)
- [Opstartfout of OPSTARTAPPARAAT_NIET_TOEGANKELIJK](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Diagnostische gegevens inschakelen op een virtuele machine die is gemaakt met de Azure Portal

De volgende procedure is voor een virtuele machine die is gemaakt met behulp van het implementatiemodel Resource Manager.

Controleer op het tabblad **Beheer** in **de** sectie Controle of Diagnostische **gegevens opstart** zijn ingeschakeld. Selecteer in de vervolgkeuzelijst **Diagnostische opslagaccount** een opslagaccount waarin u de diagnostische bestanden wilt plaatsen.
 
![VM maken](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> De functie Diagnostische gegevens opstarten biedt geen ondersteuning voor een premium opslagaccount. Als u het premium opslagaccount gebruikt voor opstartdiagnoses, ontvangt u mogelijk de fout StorageAccountTypeNotSupported wanneer u de vm start.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Implementeren vanuit een Azure Resource Manager-sjabloon

Als u implementeert vanuit een Azure Resource Manager-sjabloon, navigeert u naar uw virtuele machinebron en plaatst u de sectie diagnostisch profiel toe. Stel de API-versiekop in op '2015-06-15' of hoger. De nieuwste versie is "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Met het diagnostische profiel kunt u het opslagaccount selecteren waarin u deze logboeken wilt opslaan.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Zie [Snelstart: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)voor meer informatie over het implementeren van resources met behulp van sjablonen.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Opstartdiagnose inschakelen op bestaande virtuele machine 

Voer de volgende stappen uit om opstartdiagnoses op een bestaande virtuele machine in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en selecteer vervolgens de virtuele machine.
2. Selecteer in de sectie **Ondersteuning + probleemoplossing** de optie **Diagnostische gegevens opstarten**en selecteer vervolgens het tabblad **Instellingen.**
3. Wijzig in **De instellingen voor diagnostische gegevens** de status in **Aan**en selecteer in de vervolgkeuzelijst **Opslagaccount** een opslagaccount. 
4. Sla de wijziging op.

    ![Bestaande VM bijwerken](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

U moet de virtuele machine opnieuw opstarten om de wijziging van kracht te laten worden.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Diagnostische gegevens over opstarten inschakelen met behulp van Azure CLI

U de Azure CLI gebruiken om opstartdiagnoses in te schakelen op een bestaande virtuele Azure-machine. Zie voor meer informatie [de az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
