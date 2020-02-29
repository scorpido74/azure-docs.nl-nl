---
title: Op 1 maart 2023 worden klassieke Azure-Vm's buiten gebruik gesteld.
description: Artikel bevat een overzicht op hoog niveau van het buiten gebruik stellen van klassieke VM'S
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 9f3fd59fa040ab46a5fc4ef8272a17cba4c631b8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925772"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migreer uw IaaS-resources naar Azure Resource Manager op 1 maart 2023 

In 2014 heeft micro soft IaaS op Azure Resource Manager gestart en zijn de mogelijkheden van ooit verbeterd. Omdat [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu volledige IaaS-mogelijkheden en andere voor uitgangen heeft, is het beheer van IaaS-Vm's via Azure Service Manager op 28 februari 2020 afgeschaft en is deze functionaliteit volledig buiten gebruik gesteld op 1 maart 2023. 

Vandaag 90% van de virtuele machines van IaaS gebruikt Azure Resource Manager. Als u IaaS-resources gebruikt via Azure Service Manager (ASM), start u uw migratie nu en voltooit u deze op 1 maart 2023 om te profiteren van [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

Klassieke Vm's volgen het [moderne levenscyclus beleid](https://support.microsoft.com/help/30881/modern-lifecycle-policy) voor afschaffing.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij? 

1) Met ingang van 28 februari 2020 kunnen klanten die IaaS Vm's via Azure Service Manager (ASM) niet gebruiken in de maand van februari 2020 geen klassieke Vm's meer maken. 
2) Op 1 maart 2023 kunnen klanten geen IaaS Vm's meer starten met behulp van Azure Service Manager en worden er nog steeds actieve of toegewezen gebruikers worden gestopt en wordt de toewijzing ervan ongedaan gemaakt. 
2) Op 1 maart 2023 worden abonnementen die niet zijn gemigreerd naar Azure Resource Manager, op de hoogte gesteld van tijd lijnen voor het verwijderen van alle resterende klassieke Vm's.  

De volgende Azure-Services en-functionaliteit worden **niet** beïnvloed door dit buiten gebruik stellen: 
- Cloud Services 
- Opslag accounts die **niet** worden gebruikt door klassieke vm's 
- Virtuele netwerken (VNets) die **niet** worden gebruikt door klassieke vm's. 
- Andere klassieke resources

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen? 

- Begin met het plannen van de migratie naar Azure Resource Manager, vandaag. 

- Meer [informatie](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) over het migreren van uw klassieke [Linux](./linux/migration-classic-resource-manager-plan.md) -en [Windows](./windows/migration-classic-resource-manager-plan.md) -vm's naar Azure Resource Manager.

- Raadpleeg de [Veelgestelde vragen over klassieke migratie naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq) voor meer informatie.

- [Neem contact op met de ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)voor technische vragen en problemen.

- Voor andere vragen die geen deel uitmaken van veelgestelde vragen en feedback, opmerking hieronder.
