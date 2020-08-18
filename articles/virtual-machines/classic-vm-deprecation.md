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
ms.openlocfilehash: c1807da76fe25d31581a07706489967d6e83f748
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505305"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migreer uw IaaS-resources naar Azure Resource Manager op 1 maart 2023 

In 2014 heeft micro soft IaaS op Azure Resource Manager gestart en zijn de mogelijkheden van ooit verbeterd. Omdat [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu volledige IaaS-mogelijkheden en andere voor uitgangen heeft, is het beheer van IaaS-Vm's via [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) op 28 februari 2020 afgeschaft en is deze functionaliteit volledig buiten gebruik gesteld op 1 maart 2023. 

Vandaag 90% van de virtuele machines van IaaS gebruikt Azure Resource Manager. Als u IaaS-resources gebruikt via Azure Service Manager (ASM), start u uw migratie nu en voltooit u deze op 1 maart 2023 om te profiteren van [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klassieke Vm's volgen het [moderne levenscyclus beleid](https://support.microsoft.com/help/30881/modern-lifecycle-policy) voor het buiten gebruik stellen.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij? 

- Met ingang van 28 februari 2020 kunnen klanten die IaaS Vm's via Azure Service Manager (ASM) niet gebruiken in de maand van februari 2020 geen klassieke Vm's meer maken. 
- Op 1 maart 2023 kunnen klanten geen IaaS Vm's meer starten met behulp van Azure Service Manager en worden er nog steeds actieve of toegewezen gebruikers worden gestopt en wordt de toewijzing ervan ongedaan gemaakt. 
- Op 1 maart 2023 worden abonnementen die niet naar Azure Resource Manager worden gemigreerd, op de hoogte gesteld van tijd lijnen voor het verwijderen van alle resterende klassieke Vm's.  

De volgende Azure-Services en-functionaliteit worden **niet** beïnvloed door dit buiten gebruik stellen: 
- Cloud Services 
- Opslag accounts die **niet** worden gebruikt door klassieke vm's 
- Virtuele netwerken (VNets) die **niet** worden gebruikt door klassieke vm's. 
- Andere klassieke resources

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen? 

- Begin met het plannen van de migratie naar Azure Resource Manager, vandaag. 

- Meer [informatie](./windows/migration-classic-resource-manager-overview.md) over het migreren van uw klassieke [Linux](./linux/migration-classic-resource-manager-plan.md) -en [Windows](./windows/migration-classic-resource-manager-plan.md) -vm's naar Azure Resource Manager.

- Raadpleeg de [Veelgestelde vragen over klassieke migratie naar Azure Resource Manager](./migration-classic-resource-manager-faq.md) voor meer informatie.

- [Neem contact op met de ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})voor technische vragen, problemen en abonnementen toevoegen aan de acceptatie lijst.

- Voor andere vragen die geen deel uitmaken van veelgestelde vragen en feedback, opmerking hieronder.

- Voltooi de migratie zo snel mogelijk om bedrijfs impact te voor komen en te profiteren van verbeterde prestaties, beveiliging & nieuwe functies van Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Welke resources worden er voor deze migratie aan mij aangeboden?

- [Micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): micro soft & Community-ondersteuning voor migratie

- [Azure-migratie ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): toegewijd ondersteunings team voor technische hulp tijdens de migratie

- [Micro soft Fast track](https://www.microsoft.com/fasttrack): micro soft Fast track-team kan technische hulp bieden tijdens de migratie naar in aanmerking komende klanten. 

- Als uw bedrijf/organisatie is gekoppeld aan micro soft en/of met micro soft representatief is, zoals (TAMs (Cloud Solution architect), Technical Account Manager (CSA)), kunt u het beste met hen samen werken voor aanvullende bronnen voor migratie. 

