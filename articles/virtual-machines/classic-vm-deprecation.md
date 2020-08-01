---
title: Op 1 maart 2023 worden klassieke Azure-Vm's buiten gebruik gesteld.
description: Artikel bevat een overzicht op hoog niveau van het buiten gebruik stellen van klassieke VM'S
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461201"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migreer uw IaaS-resources naar Azure Resource Manager op 1 maart 2023 

In 2014 heeft micro soft IaaS op Azure Resource Manager gestart en zijn de mogelijkheden van ooit verbeterd. Omdat [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu volledige IaaS-mogelijkheden en andere voor uitgangen heeft, is het beheer van IaaS-Vm's via Azure Service Manager op 28 februari 2020 afgeschaft en is deze functionaliteit volledig buiten gebruik gesteld op 1 maart 2023. 

Vandaag 90% van de virtuele machines van IaaS gebruikt Azure Resource Manager. Als u IaaS-resources gebruikt via Azure Service Manager (ASM), start u uw migratie nu en voltooit u deze op 1 maart 2023 om te profiteren van [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klassieke Vm's volgen het [moderne levenscyclus beleid](https://support.microsoft.com/help/30881/modern-lifecycle-policy) voor het buiten gebruik stellen.

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

- Meer [informatie](./windows/migration-classic-resource-manager-overview.md) over het migreren van uw klassieke [Linux](./linux/migration-classic-resource-manager-plan.md) -en [Windows](./windows/migration-classic-resource-manager-plan.md) -vm's naar Azure Resource Manager.

- Raadpleeg de [Veelgestelde vragen over klassieke migratie naar Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md) voor meer informatie.

- [Neem contact op met de ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})voor technische vragen, problemen en abonnementen toevoegen aan de acceptatie lijst.

- [Neem contact op met de migratie ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}) voor hulp tijdens de migratie

- Voor andere vragen die geen deel uitmaken van veelgestelde vragen en feedback, opmerking hieronder.
