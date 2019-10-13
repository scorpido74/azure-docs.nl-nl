---
title: Azure Virtual Machines verplaatsen naar een nieuw abonnement of nieuwe resource groep | Microsoft Docs
description: Gebruik Azure Resource Manager om virtuele machines te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: 443d6f2bcbb61d9106b079a4e63c48bb433d19c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286735"
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

Als u virtuele machines wilt verplaatsen die met Azure Backup zijn geconfigureerd, gebruikt u de volgende tijdelijke oplossing:

* Zoek de locatie van de virtuele machine.
* Een resource groep zoeken met het volgende naamgevings patroon: `AzureBackupRG_<location of your VM>_1` bijvoorbeeld AzureBackupRG_westus2_1
* Als in Azure Portal, schakelt u ' verborgen typen weer geven ' in
* In Power shell gebruikt u de cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Gebruik in CLI het `az resource list -g AzureBackupRG_<location of your VM>_1`
* Zoek de resource met het type `Microsoft.Compute/restorePointCollections` met het naam patroon `AzureBackup_<name of your VM that you're trying to move>_###########`
* Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.
* Nadat het verwijderen is voltooid, kunt u de kluis en de virtuele machine verplaatsen naar het doel abonnement. Na de verplaatsing kunt u back-ups voortzetten zonder verlies van gegevens.
* Zie [Recovery Services beperkingen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)voor informatie over het verplaatsen van Recovery Services-kluizen voor back-ups.

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.
