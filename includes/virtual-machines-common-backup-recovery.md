---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176029"
---
## <a name="azure-backup"></a>Azure Backup

Gebruik Azure Backup voor het maken van back-ups van Azure VM's met productieworkloads. Azure Backup ondersteunt toepassingsconsistente back-ups voor zowel Windows- als Linux-vm's. Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of alleen specifieke bestanden herstellen. 

Zie de zelfstudie 'Back-up van Azure virtual machines' [voor](../articles/virtual-machines/linux/tutorial-backup-vms.md) Een eenvoudige, praktische inleiding tot Azure Backup voor Azure VM's. [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md)

Zie [Uw VM-back-upinfrastructuur plannen in Azure](../articles/backup/backup-azure-vms-introduction.md) voor meer informatie over hoe Azure Backup werkt


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery beschermt uw VM's tegen een groot rampscenario, wanneer een hele regio te maken krijgt met een storing als gevolg van grote natuurrampen of wijdverspreide serviceonderbrekingen. U Azure Site Recovery configureren voor uw VM's, zodat u uw toepassing binnen enkele minuten met één klik herstellen. U repliceren naar een Azure-gebied van uw keuze, het is niet beperkt tot gekoppelde regio's. 

U noodhersteloefeningen uitvoeren met on-demand testfailovers, zonder dat dit gevolgen heeft voor uw productieworkloads of doorlopende replicatie. Herstelplannen maken om failover en failback te orkestreren van de hele toepassing die op meerdere VM's wordt uitgevoerd. De functie herstelplan is geïntegreerd met Azure-automatiseringsrunbooks.

U aan de slag door [uw virtuele machines te repliceren.](https://aka.ms/a2a-getting-started) 

## <a name="managed-snapshots"></a>Beheerde momentopnamen 

In ontwikkel- en testomgevingen bieden momentopnamen een snelle en eenvoudige optie voor het maken van back-ups van VM's die beheerde schijven gebruiken. Een beheerde momentopname is een alleen-lezen volledige kopie van een beheerde schijf. Momentopnamen bestaan onafhankelijk van de bronschijf en kunnen worden gebruikt om nieuwe beheerde schijven te maken voor het opnieuw opbouwen van een VM. Ze worden gefactureerd op basis van het gebruikte gedeelte van de schijf. Als u bijvoorbeeld een momentopname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GB en de werkelijke gebruikte gegevensgrootte van 10 GB, wordt momentopname alleen gefactureerd voor de gebruikte gegevensgrootte van 10 GB.  

Zie voor meer informatie over het maken van momentopnamen:

* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Volgende stappen
U Azure Backup uitproberen door de zelfstudie 'Back-up van windows virtuele machines' voor [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) of Windows te [volgen.](../articles/virtual-machines/windows/tutorial-backup-vms.md)
