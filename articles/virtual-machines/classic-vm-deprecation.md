---
title: We gaan per 1 maart 2023 met Azure Classic VM's
description: Artikel biedt een overzicht op hoog niveau van Classic VM pensioen
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127331"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Uw IaaS-resources vmigreren naar Azure Resource Manager vtegen 1 maart 2023 

In 2014 lanceerden we IaaS op Azure Resource Manager en sindsdien verbeteren we de mogelijkheden. Omdat [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu volledige IaaS-mogelijkheden en andere verbeteringen heeft, hebben we het beheer van IaaS VM's via Azure Service Manager op 28 februari 2020 afgeschaft en wordt deze functionaliteit op 1 maart 2023 volledig buiten gebruik gesteld. 

Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's Azure Resource Manager. Als u IaaS-resources gebruikt via Azure Service Manager (ASM), begint u nu met het plannen van uw migratie en voltooit u deze v:12 uur om te profiteren van [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Klassieke VM's volgen het [moderne levenscyclusbeleid](https://support.microsoft.com/help/30881/modern-lifecycle-policy) voor pensionering.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij? 

1) Vanaf 28 februari 2020 kunnen klanten die in de maand februari 2020 geen IaaS VM's hebben gebruikt via Azure Service Manager (ASM) geen klassieke VM's meer maken. 
2) Op 1 maart 2023 kunnen klanten iaas-vm's niet meer starten met Azure Service Manager en worden alle klanten die nog worden uitgevoerd of toegewezen, gestopt en dealen. 
2) Op 1 maart 2023 worden abonnementen die niet zijn gemigreerd naar Azure Resource Manager op de hoogte gebracht van tijdlijnen voor het verwijderen van resterende Klassieke VM's.  

De volgende Azure-services en -functionaliteit worden **NIET** beïnvloed door dit pensioen: 
- Cloudservices 
- Opslagaccounts **die niet** worden gebruikt door klassieke VM's 
- Virtuele netwerken (VNets) **worden niet** gebruikt door klassieke VM's. 
- Andere klassieke bronnen

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen? 

- Begin vandaag nog met het plannen van uw migratie naar Azure Resource Manager. 

- [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) over het migreren van uw klassieke [Linux-](./linux/migration-classic-resource-manager-plan.md) en Windows-VM's naar Azure Resource Manager. [Windows](./windows/migration-classic-resource-manager-plan.md)

- Raadpleeg voor meer informatie de [veelgestelde vragen over de migratie naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Voor technische vragen en problemen [u contact opnemen met ondersteuning.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Voor andere vragen die geen deel uitmaken van faq en feedback, commentaar hieronder.
