---
title: Azure VM's verplaatsen naar een nieuw abonnement of brongroep
description: Gebruik Azure Resource Manager om virtuele machines te verplaatsen naar een nieuwe brongroep of -abonnement.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479759"
---
# <a name="move-guidance-for-virtual-machines"></a>Begeleiding verplaatsen voor virtuele machines

In dit artikel worden de scenario's beschreven die momenteel niet worden ondersteund en de stappen om virtuele machines met back-up te verplaatsen.

## <a name="scenarios-not-supported"></a>Scenario's die niet worden ondersteund

De volgende scenario's worden nog niet ondersteund:

* Beheerde schijven in beschikbaarheidszones kunnen niet worden verplaatst naar een ander abonnement.
* Virtuele machineschaalsets met standaard SKU Load Balancer of Standard SKU Public IP kunnen niet worden verplaatst.
* Virtuele machines die zijn gemaakt met Marketplace-bronnen met bijbehorende plannen, kunnen niet worden verplaatst tussen resourcegroepen of abonnementen. De-provisioner de virtuele machine in het huidige abonnement en opnieuw implementeren in het nieuwe abonnement.
* Virtuele machines in een bestaand virtueel netwerk kunnen niet worden verplaatst naar een nieuw abonnement wanneer u niet alle resources in het virtuele netwerk verplaatst.
* Virtuele machines met een lage prioriteit en virtuele machineschaalsets met lage prioriteit kunnen niet worden verplaatst tussen resourcegroepen of abonnementen.
* Virtuele machines in een beschikbaarheidsset kunnen niet afzonderlijk worden verplaatst.

## <a name="virtual-machines-with-azure-backup"></a>Virtuele machines met Azure Backup

Als u virtuele machines wilt verplaatsen die zijn geconfigureerd met Azure Backup, gebruikt u de volgende tijdelijke oplossing:

* Vind de locatie van uw virtuele machine.
* Zoek een resourcegroep met het `AzureBackupRG_<location of your VM>_1` volgende naamgevingspatroon: AzureBackupRG_westus2_1
* Als u in Azure-portal de controle 'Verborgen typen weergeven' inschakelt
* Als u in PowerShell bent, gebruikt u de `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Als u in CLI de`az resource list -g AzureBackupRG_<location of your VM>_1`
* De bron zoeken `Microsoft.Compute/restorePointCollections` met tekst met het naamgevingspatroon`AzureBackup_<name of your VM that you're trying to move>_###########`
* Verwijder deze bron. Met deze bewerking worden alleen de directe herstelpunten verwijderd, niet de back-upgegevens in de kluis.
* Nadat delete is voltooid, u de kluis en virtuele machine verplaatsen naar het doelabonnement. Na de verhuizing u back-ups voortzetten zonder verlies van gegevens.
* Zie Beperkingen van herstelservices voor informatie over het verplaatsen van vaults voor herstelservice voor [back-ups.](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](../move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.
