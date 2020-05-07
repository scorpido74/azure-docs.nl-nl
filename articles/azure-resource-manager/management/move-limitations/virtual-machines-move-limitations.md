---
title: Virtuele Azure-machines verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om virtuele machines te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744639"
---
# <a name="move-guidance-for-virtual-machines"></a>Richt lijnen voor het verplaatsen van virtuele machines

In dit artikel worden de scenario's beschreven die momenteel niet worden ondersteund en de stappen voor het verplaatsen van virtuele machines met back-up.

## <a name="scenarios-not-supported"></a>Scenario's worden niet ondersteund

De volgende scenario's worden nog niet ondersteund:

* Managed Disks in Beschikbaarheidszones kan niet worden verplaatst naar een ander abonnement.
* Virtual Machine Scale Sets met een standaard-SKU Load Balancer of een open bare standaard-SKU kan niet worden verplaatst.
* Virtuele machines die zijn gemaakt op basis van Marketplace-resources met plannen die zijn gekoppeld, kunnen niet worden verplaatst over resource groepen of-abonnementen. De inrichting van de virtuele machine in het huidige abonnement ongedaan maken en de implementatie opnieuw uitvoeren in het nieuwe abonnement.
* Virtuele machines in een bestaand virtueel netwerk kunnen niet worden verplaatst naar een nieuw abonnement wanneer u niet alle resources in het virtuele netwerk verplaatst.
* Virtuele machines met lage prioriteit en virtuele-machine schaal sets met lage prioriteit kunnen niet worden verplaatst over resource groepen of-abonnementen.
* Virtuele machines in een beschikbaarheidsset kunnen niet afzonderlijk worden verplaatst.

## <a name="virtual-machines-with-azure-backup"></a>Virtuele machines met Azure Backup

Als u virtuele machines wilt verplaatsen die zijn geconfigureerd met Azure Backup, moet u de herstel punten verwijderen uit de kluis.

Als [voorlopig verwijderen](../../../backup/backup-azure-security-feature-cloud.md) is ingeschakeld voor uw virtuele machine, kunt u de virtuele machine niet verplaatsen terwijl de herstel punten worden bewaard. [Schakel zacht verwijderen uit](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) of wacht 14 dagen na het verwijderen van de herstel punten.

### <a name="portal"></a>Portal

1. De back-up tijdelijk stoppen en back-upgegevens behouden.
2. Voer de volgende stappen uit om virtuele machines te verplaatsen die zijn geconfigureerd met Azure Backup:

   1. Zoek de locatie van de virtuele machine.
   2. Een resource groep zoeken met het volgende naamgevings patroon `AzureBackupRG_<location of your VM>_1`:. Bijvoorbeeld *AzureBackupRG_westus2_1*
   3. Selecteer in het Azure Portal de optie **verborgen typen weer geven**.
   4. Zoek de resource met het type **micro soft. Compute/restorePointCollections** die het `AzureBackup_<name of your VM that you're trying to move>_###########`naamgevings patroon heeft.
   5. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.
   6. Nadat de bewerking delete is voltooid, kunt u de virtuele machine verplaatsen.

3. Verplaats de virtuele machine naar de doel resource groep.
4. Hervat de back-up.

### <a name="powershell"></a>PowerShell

* Zoek de locatie van de virtuele machine.
* Een resource groep zoeken met het volgende naamgevings patroon `AzureBackupRG_<location of your VM>_1` : bijvoorbeeld AzureBackupRG_westus2_1
* In Power shell gebruikt u de `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* De resource zoeken met een `Microsoft.Compute/restorePointCollections` type dat het naamgevings patroon heeft`AzureBackup_<name of your VM that you're trying to move>_###########`
* Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

### <a name="azure-cli"></a>Azure CLI

* Zoek de locatie van de virtuele machine.
* Een resource groep zoeken met het volgende naamgevings patroon `AzureBackupRG_<location of your VM>_1` : bijvoorbeeld AzureBackupRG_westus2_1
* Gebruik in CLI de`az resource list -g AzureBackupRG_<location of your VM>_1`
* De resource zoeken met een `Microsoft.Compute/restorePointCollections` type dat het naamgevings patroon heeft`AzureBackup_<name of your VM that you're trying to move>_###########`
* Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

## <a name="next-steps"></a>Volgende stappen

* Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

* Zie [Recovery Services beperkingen](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)voor informatie over het verplaatsen van Recovery Services-kluizen voor back-ups.
