---
title: Communitytools - Klassieke resources verplaatsen naar Azure Resource Manager
description: In dit artikel worden de hulpprogramma's gecatalogiseert die door de community zijn geleverd om IaaS-resources te migreren van klassiek naar het azure resource manager-implementatiemodel.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 9839f411458eeb4fd071177ec8208baa94dca3a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866158"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Communitytools om IaaS-resources te migreren van klassiek naar Azure Resource Manager

> [!IMPORTANT]
> Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Vanaf 28 februari 2020 zijn klassieke VM's afgeschaft en op 1 maart 2023 volledig met pensioen gegaan. [Meer informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe het u beïnvloedt.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

In dit artikel worden de hulpprogramma's gecatalogiseert die door de community zijn geleverd om te helpen bij de migratie van IaaS-resources van klassiek naar het azure resource manager-implementatiemodel.

> [!NOTE]
> Deze hulpprogramma's worden niet officieel ondersteund door Microsoft Support. Daarom zijn ze open source op GitHub en we zijn blij om PR's te accepteren voor fixes of extra scenario's. Als u een probleem wilt melden, gebruikt u de functie Problemen met GitHub.
> 
> Migreren met deze tools zal leiden tot downtime voor uw klassieke Virtuele Machine. Als u op zoek bent naar door het platform ondersteunde migratie, gaat u naar 
> 
>   * [Platform ondersteunde migratie van IaaS-resources van Classic naar Azure Resource Manager-stack](migration-classic-resource-manager-overview.md)
>   * [Technical Deep Dive on Platform ondersteunde migratie van Classic naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-resources migreren van Classic naar Azure Resource Manager met Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Dit is een verzameling helperhulpprogramma's die zijn gemaakt als onderdeel van bedrijfsmigraties van Azure Service Management naar Azure Resource Manager. Met deze tool u uw infrastructuur repliceren naar een ander abonnement dat kan worden gebruikt voor het testen van migratie en het oplossen van eventuele problemen voordat u de migratie uitvoert op uw productieabonnement.

[Koppeling naar de gereedschapsdocumentatie](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz is een extra optie om een complete set klassieke IaaS-resources te migreren naar Azure Resource Manager IaaS-bronnen. De migratie kan plaatsvinden binnen hetzelfde abonnement of tussen verschillende abonnementen en abonnementstypen (bijvoorbeeld CSP-abonnementen).

[Koppeling naar de gereedschapsdocumentatie](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

