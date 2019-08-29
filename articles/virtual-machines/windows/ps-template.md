---
title: Een Windows-VM maken op basis van een sjabloon in azure | Microsoft Docs
description: Gebruik een resource manager-sjabloon en Power shell om eenvoudig een nieuwe Windows-VM te maken.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7e1f50753f155d1583de3a1e8426975e1b0d6aee
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102521"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Een virtuele Windows-machine maken op basis van een resource manager-sjabloon

Meer informatie over het maken van een virtuele Windows-machine met behulp van een Azure Resource Manager sjabloon en Azure PowerShell van de Azure Cloud shell. De sjabloon die in dit artikel wordt gebruikt, implementeert één virtuele machine met Windows Server in een nieuw virtueel netwerk met één subnet. Zie [een virtuele Linux-machine maken met Azure Resource Manager sjablonen](../linux/create-ssh-secured-vm-from-template.md)voor het maken van een virtuele Linux-machine.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Het maken van een virtuele machine in azure omvat meestal twee stappen:

- Maak een resourcegroep. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt.
- Hiermee maakt u een virtuele machine.

In het volgende voor beeld wordt een VM gemaakt op basis van een [Azure Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)start-sjabloon. Hier volgt een kopie van de sjabloon:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Als u het Power shell-script wilt uitvoeren, selecteert u **proberen** om de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Als u ervoor kiest om de Power shell lokaal te installeren en te gebruiken in plaats van vanuit de Azure Cloud shell, is voor deze zelf studie de module Azure PowerShell vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

In het vorige voor beeld hebt u een sjabloon opgegeven die is opgeslagen in GitHub. U kunt ook een sjabloon downloaden of maken en het lokale pad opgeven met de `--template-file` para meter.

Hier volgen enkele aanvullende bronnen:

- Zie [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [Naslag informatie over Azure-sjablonen](/azure/templates/microsoft.compute/allversions)voor een overzicht van de Azure virtual machine-schema's.
- Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)start-sjablonen voor meer voor beelden van virtuele-machine sjablonen.

## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

In de laatste Power shell-opdracht uit het vorige script ziet u de naam van de virtuele machine. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](./connect-logon.md)om verbinding te maken met de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

- Als er problemen zijn met de implementatie, kunt u een kijkje nemen bij het [oplossen van veelvoorkomende problemen met Azure-implementaties met Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Meer informatie over het maken en beheren van een virtuele machine in [Windows-Vm's maken en beheren met de module Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Voor meer informatie over het maken van sjablonen bekijkt u de JSON-syntaxis en de eigenschappen voor de typen resources die u hebt geïmplementeerd:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
