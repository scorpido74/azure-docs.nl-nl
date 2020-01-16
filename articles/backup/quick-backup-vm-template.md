---
title: 'Snelstartgids: VM-back-up van Resource Manager-sjabloon'
description: Meer informatie over het maken van een back-up van uw virtuele machines met Azure Resource Manager-sjabloon
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc
ms.openlocfilehash: 721213dcdd4751de936968b7e67a4b5d31b8d9ec
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980644"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Een back-up maken van een virtuele machine in azure met Resource Manager-sjabloon

[Azure backup](backup-overview.md) maakt back-ups van on-premises machines en apps en Azure-vm's. In dit artikel wordt beschreven hoe u een back-up van een virtuele machine van Azure maakt met Resource Manager-sjabloon en Azure PowerShell. In deze Snelstartgids wordt gekeken naar het proces van het implementeren van een resource manager-sjabloon om een herstel Services-kluis te maken. Zie de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.recoveryservices/allversions)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

U kunt ook een back-up van een virtuele machine maken met behulp van [Azure PowerShell](./quick-backup-vm-powershell.md), de [Azure CLI](quick-backup-vm-cli.md)of de [Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Een virtuele machine en Recovery Services kluis maken

Een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarmee back-upgegevens worden opgeslagen voor beveiligde bronnen, zoals virtuele Azure-machines. Wanneer een back-uptaak wordt uitgevoerd, wordt er een herstel punt in de Recovery Services kluis gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)start-sjablonen. Met deze sjabloon kunt u een eenvoudige Windows-VM en Recovery Services kluis implementeren die is geconfigureerd met de Defaultpolicy bij voor beveiliging.

Als u de sjabloon wilt implementeren, selecteert u **proberen** om de Azure Cloud shell te openen en plakt u het volgende Power shell-script in het shell-venster. Als u de code wilt plakken, klikt u met de rechter muisknop op het shell venster en selecteert u vervolgens **Plakken**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell wordt gebruikt voor het implementeren van de Resource Manager-sjabloon in deze Quick Start. De [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure cli](../azure-resource-manager/templates/deploy-cli.md)en [rest API](../azure-resource-manager/templates/deploy-rest.md) kunnen ook worden gebruikt voor het implementeren van sjablonen.

## <a name="start-a-backup-job"></a>Een back-uptaak starten

Met de sjabloon wordt een virtuele machine gemaakt en wordt de virtuele machine weer ingeschakeld. Nadat u de sjabloon hebt geïmplementeerd, moet u een back-uptaak starten. Zie [een back-uptaak starten](./quick-backup-vm-powershell.md#start-a-backup-job)voor meer informatie.

## <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

Zie [de back-uptaak bewaken](./quick-backup-vm-powershell.md#monitor-the-backup-job)om de back-uptaak te controleren.

## <a name="clean-up-the-deployment"></a>De implementatie opschonen

Als u een back-up van de virtuele machine niet meer nodig hebt, kunt u deze opschonen.

- Als u de virtuele machine opnieuw wilt proberen te herstellen, slaat u de opschoon bewerking over.
- Als u een bestaande virtuele machine hebt gebruikt, kunt u de laatste [Remove-AzResourceGroup-](/powershell/module/az.resources/remove-azresourcegroup) cmdlet overs Laan om de resource groep en de VM op locatie te laten staan.

Schakel de beveiliging uit, verwijder de herstel punten en de kluis. Verwijder vervolgens de resource groep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie over](tutorial-backup-vm-at-scale.md) het maken van een back-up van virtuele machines in de Azure Portal.
- [Meer informatie over hoe](tutorial-restore-disk.md) u een virtuele machine snel kunt herstellen
