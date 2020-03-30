---
title: Een Windows-vm maken op basis van een sjabloon in Azure
description: Gebruik een Resource Manager-sjabloon en PowerShell om eenvoudig een nieuwe Windows-vm te maken.
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
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065555"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Een virtuele Windows-machine maken op basis van een resourcemanagersjabloon

Meer informatie over het maken van een virtuele Windows-machine met behulp van een Azure Resource Manager-sjabloon en Azure PowerShell vanuit de Azure Cloud-shell. De sjabloon die in dit artikel wordt gebruikt, implementeert één virtuele machine met Windows Server in een nieuw virtueel netwerk met één subnet. Zie [Een virtuele Linux-machine maken met Azure Resource Manager-sjablonen](../linux/create-ssh-secured-vm-from-template.md)voor het maken van een virtuele Linux-machine.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Het maken van een virtuele Azure-machine bevat meestal twee stappen:

- Maak een resourcegroep. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt.
- Hiermee maakt u een virtuele machine.

In het volgende voorbeeld wordt een VM gemaakt op basis van een [Azure Quickstart-sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Hier is een kopie van de sjabloon:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Als u het PowerShell-script wilt uitvoeren, selecteert **u Proberen** om de Azure Cloud-shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

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

Als u ervoor kiest om de PowerShell lokaal te installeren en te gebruiken in plaats van vanuit de Azure Cloud-shell, vereist deze zelfstudie de Azure PowerShell-module. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

In het vorige voorbeeld hebt u een sjabloon opgegeven die is opgeslagen in GitHub. U ook een sjabloon downloaden of maken `--template-file` en het lokale pad met de parameter opgeven.

Hier volgen enkele aanvullende bronnen:

- Zie [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [Azure-sjabloonverwijzing](/azure/templates/microsoft.compute/allversions)voor de programma's voor virtuele machines van Azure.
- Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)voor meer voorbeelden van virtuele machines.

## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

De laatste PowerShell-opdracht uit het vorige script toont de naam van de virtuele machine. Zie Verbinding maken en [aanmelden bij een virtuele Azure-machine met Windows](./connect-logon.md)als u verbinding wilt maken met de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

- Als er problemen zijn met de implementatie, u de [algemene Azure-implementatiefouten oplossen met Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Meer informatie over het maken en beheren van een virtuele machine in [Windows VM's maken en beheren met de Azure PowerShell-module.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Voor meer informatie over het maken van sjablonen u de syntaxis en eigenschappen van JSON weergeven voor de resourcestypen die u hebt geïmplementeerd:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
