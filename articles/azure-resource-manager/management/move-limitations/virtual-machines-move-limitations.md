---
title: Virtuele Azure-machines verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om virtuele machines te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230956"
---
# <a name="move-guidance-for-virtual-machines"></a>Richt lijnen voor het verplaatsen van virtuele machines

In dit artikel worden de scenario's beschreven die momenteel niet worden ondersteund en de stappen voor het verplaatsen van virtuele machines met back-up.

## <a name="scenarios-not-supported"></a>Scenario's worden niet ondersteund

De volgende scenario's worden nog niet ondersteund:

* Virtual Machine Scale Sets met een standaard-SKU Load Balancer of een open bare standaard-SKU kan niet worden verplaatst.
* Virtuele machines die zijn gemaakt op basis van Marketplace-resources waarvoor plannen zijn gekoppeld, kunnen niet worden verplaatst naar abonnementen. De inrichting van de virtuele machine in het huidige abonnement ongedaan maken en de implementatie opnieuw uitvoeren in het nieuwe abonnement.
* Virtuele machines in een bestaand virtueel netwerk kunnen niet worden verplaatst naar een nieuw abonnement wanneer u niet alle resources in het virtuele netwerk verplaatst.
* Virtuele machines met lage prioriteit en virtuele-machine schaal sets met lage prioriteit kunnen niet worden verplaatst over resource groepen of-abonnementen.
* Virtuele machines in een beschikbaarheidsset kunnen niet afzonderlijk worden verplaatst.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

U kunt een virtuele machine die is geïntegreerd met een sleutel kluis, niet verplaatsen om Azure Disk Encryption te implementeren [voor Linux vm's](../../../virtual-machines/linux/disk-encryption-overview.md) of [Azure Disk Encryption voor Windows-vm's](../../../virtual-machines/windows/disk-encryption-overview.md). Als u de virtuele machine wilt verplaatsen, moet u versleuteling uitschakelen.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Virtuele machines met Azure Backup

Als u virtuele machines wilt verplaatsen die zijn geconfigureerd met Azure Backup, moet u de herstel punten verwijderen uit de kluis.

Als [voorlopig verwijderen](../../../backup/backup-azure-security-feature-cloud.md) is ingeschakeld voor uw virtuele machine, kunt u de virtuele machine niet verplaatsen terwijl de herstel punten worden bewaard. [Schakel zacht verwijderen uit](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) of wacht 14 dagen na het verwijderen van de herstel punten.

### <a name="portal"></a>Portal

1. De back-up tijdelijk stoppen en back-upgegevens behouden.
2. Voer de volgende stappen uit om virtuele machines te verplaatsen die zijn geconfigureerd met Azure Backup:

   1. Zoek de locatie van de virtuele machine.
   2. Een resource groep zoeken met het volgende naamgevings patroon: `AzureBackupRG_<VM location>_1` . De naam heeft bijvoorbeeld de indeling van *AzureBackupRG_westus2_1*.
   3. Selecteer in het Azure Portal de optie **verborgen typen weer geven**.
   4. Zoek de resource met het type **micro soft. Compute/restorePointCollections** die het naamgevings patroon heeft `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.
   6. Nadat de bewerking delete is voltooid, kunt u de virtuele machine verplaatsen.

3. Verplaats de virtuele machine naar de doel resource groep.
4. Hervat de back-up.

### <a name="powershell"></a>PowerShell

1. Zoek de locatie van de virtuele machine.

1. Een resource groep zoeken met het naamgevings patroon- `AzureBackupRG_<VM location>_1` . De naam kan bijvoorbeeld zijn `AzureBackupRG_westus2_1` .

1. Gebruik de volgende opdracht om de herstel punt verzameling op te halen.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. Zoek de locatie van de virtuele machine.

1. Een resource groep zoeken met het naamgevings patroon- `AzureBackupRG_<VM location>_1` . De naam kan bijvoorbeeld zijn `AzureBackupRG_westus2_1` .

1. Gebruik de volgende opdracht om de verzameling herstel punten op te halen.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. De resource-ID voor de resource met het naamgevings patroon zoeken `AzureBackup_<VM name>_###########`

1. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

* Zie [Recovery Services beperkingen](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)voor informatie over het verplaatsen van Recovery Services-kluizen voor back-ups.
