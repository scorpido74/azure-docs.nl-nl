---
title: Snelstart - VM-back-up van resourcebeheersjabloon
description: Meer informatie over het maken van een back-up van uw virtuele machines met de sjabloon Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: c40dc7ef8fc55acade709b1ffbbd86ff306f7f0e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79459239"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Een back-up maken van een virtuele machine in Azure met resourcebeheersjabloon

[Azure Backup](backup-overview.md) maakt een back-up van on-premises machines en apps en Azure VM's. In dit artikel ziet u hoe u een back-up maakt van een Azure VM met resourcebeheersjabloon en Azure PowerShell. Deze quickstart richt zich op het proces van het implementeren van een Resource Manager-sjabloon om een kluis Van Herstelservices te maken. Zie [Resourcemanager-documentatie](/azure/azure-resource-manager/) en de [sjabloonverwijzing](/azure/templates/microsoft.recoveryservices/allversions)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U ook een back-up maken van een vm met [Azure PowerShell,](./quick-backup-vm-powershell.md)Azure CLI of in de [Azure-portal.](quick-backup-vm-portal.md) [Azure CLI](quick-backup-vm-cli.md)

## <a name="create-a-vm-and-recovery-services-vault"></a>Een kluis van VM en Recovery Services maken

Een [vault van Recovery Services](backup-azure-recovery-services-vault-overview.md) is een logische container die back-upgegevens opslaat voor beveiligde bronnen, zoals Azure VM's. Wanneer een back-uptaak wordt uitgevoerd, wordt een herstelpunt in de kluis van Herstelservices gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/) Met deze sjabloon u eenvoudige Windows VM- en Recovery Services Vault implementeren die is geconfigureerd met het standaardbeleid voor beveiliging.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

De resources die in de sjabloon zijn gedefinieerd, zijn:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/kluizen**](/azure/templates/microsoft.recoveryservices/vaults)
- [**Microsoft.RecoveryServices/kluizen/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>De sjabloon implementeren

Als u de sjabloon wilt implementeren, selecteert **u Probeer deze** om de Azure Cloud Shell te openen en plakt u het volgende PowerShell-script in het shell-venster. Als u de code wilt plakken, klikt u met de rechtermuisknop op het shell-venster en selecteert u **Plakken**.

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

Azure PowerShell wordt gebruikt om de resourcemanagersjabloon in deze snelstart te implementeren. De [Azure-portal,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)en Rest [API](../azure-resource-manager/templates/deploy-rest.md) kunnen ook worden gebruikt om sjablonen te implementeren.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="start-a-backup-job"></a>Een back-uptaak starten

De sjabloon maakt een VM en maakt de VM weer in. Nadat u de sjabloon hebt geïmplementeerd, moet u een back-uptaak starten. Zie [Een back-uptaak starten voor](./quick-backup-vm-powershell.md#start-a-backup-job)meer informatie .

### <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

Zie [De back-uptaak controleren](./quick-backup-vm-powershell.md#monitor-the-backup-job)als u de back-uptaak wilt controleren.

## <a name="clean-up-the-deployment"></a>De implementatie opschonen

Als u geen back-up meer van de VM hoeft te maken, u deze opschonen.

- Als u het herstellen van de VM wilt uitproberen, slaat u de opschoning over.
- Als u een bestaande vm hebt gebruikt, u de laatste cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) overslaan om de resourcegroep en VM op hun plaats te laten.

Schakel de beveiliging uit, verwijder de herstelpunten en de kluis. Verwijder vervolgens de brongroep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie over het](tutorial-backup-vm-at-scale.md) maken van back-ups van VM's in de Azure-portal.
- [Meer informatie over het](tutorial-restore-disk.md) snel herstellen van een virtuele machine
- [Meer informatie over het](../azure-resource-manager/templates/template-tutorial-create-first-template.md) maken van Resource Manager-sjablonen.
