---
title: Overzicht van back-upopties voor Linux-Vm's
description: Overzicht van back-upopties voor virtuele Linux Azure-machines.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: bb1abb5b94653893e131c09af6de1625b0bc7998
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554799"
---
# <a name="backup-and-restore-options-for-linux-virtual-machines-in-azure"></a>Opties voor back-up en herstel voor virtuele Linux-machines in azure

U kunt uw gegevens beschermen door regelmatig back-ups te maken. Er zijn verschillende back-upopties beschikbaar voor virtuele machines, afhankelijk van uw gebruiks aanvraag.

## <a name="azure-backup"></a>Azure Backup

Gebruik Azure Backup voor het maken van back-ups van virtuele Azure-machines waarop productie workloads worden uitgevoerd. Azure Backup ondersteunt toepassings consistente back-ups voor virtuele Windows-en Linux-machines. Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of alleen specifieke bestanden herstellen. 

Zie de zelf studie ' back-up maken van virtuele Azure-machines ' voor [Linux](./linux/tutorial-backup-vms.md) of [Windows](./windows/tutorial-backup-vms.md)voor een eenvoudige, praktische Inleiding tot Azure backup voor Azure-vm's.

Zie [uw VM-back-upinfrastructuur plannen in azure](../backup/backup-azure-vms-introduction.md) voor meer informatie over de werking van Azure backup.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery beschermt uw Vm's vanuit een belang rijke nood situatie, wanneer een hele regio een storing veroorzaakt door een grote natuur ramp of een uitgebreide service onderbreking. U kunt Azure Site Recovery voor uw virtuele machines configureren, zodat u uw toepassing met één klik in een paar minuten kunt herstellen. U kunt naar een Azure-regio naar keuze repliceren, maar dit is niet beperkt tot gekoppelde regio's. 

U kunt nood herstel oefeningen uitvoeren met testfailover op aanvraag, zonder dat dit van invloed is op uw productie workloads of voortdurende replicatie. Herstel plannen maken om de failover en failback te organiseren van de volledige toepassing die op meerdere Vm's wordt uitgevoerd. De functie herstel plan is geïntegreerd met Azure Automation-runbooks.

U kunt aan de slag door [uw virtuele machines te repliceren](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Beheerde moment opnamen 

In ontwikkel-en test omgevingen bieden moment opnamen een snelle en eenvoudige optie voor het maken van back-ups van virtuele machines die gebruikmaken van Managed Disks. Een beheerde moment opname is een alleen-lezen volledige kopie van een beheerde schijf. Moment opnamen bestaan onafhankelijk van de bron schijf en kunnen worden gebruikt om nieuwe beheerde schijven te maken voor het opnieuw opbouwen van een virtuele machine. Ze worden gefactureerd op basis van het gebruikte gedeelte van de schijf. Als u bijvoorbeeld een moment opname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GB en de daad werkelijke gebruikte gegevens grootte van 10 GB, wordt de moment opname alleen gefactureerd voor de gebruikte gegevens grootte van 10 GB.  

Zie voor meer informatie over het maken van moment opnamen:

* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Windows](./windows/snapshot-copy-managed-disk.md)
* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Linux](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Volgende stappen
U kunt Azure Backup uitproberen door de zelf studie ' back-up maken van virtuele Windows-machines ' te volgen voor [Linux](./linux/tutorial-backup-vms.md) of [Windows](./windows/tutorial-backup-vms.md).
