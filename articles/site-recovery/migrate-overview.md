---
title: Servers en VM's migreren naar Azure met Azure Site Recovery
description: Beschrijft hoe u on-premises en Azure IaaS VM's naar Azure migreert met behulp van de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388896"
---
# <a name="about-migration"></a>Info over migratie

Lees dit artikel voor een snel overzicht van hoe de [Azure Site Recovery-service](site-recovery-overview.md) u helpt om machines te migreren. 

> [!TIP]
> U moet nu de Azure Migrate-service gebruiken om VM's en servers te migreren naar Azure, in plaats van de Azure Site Recovery-service. [Meer informatie](../migrate/migrate-services-overview.md).


Dit kun je migreren met Site recovery:

- **Migreren van on-premises naar Azure:** migreer on-premises Hyper-V VM's, VMware VM's en fysieke servers naar Azure. Na de migratie worden workloads die op de on-premises machines werden uitgevoerd, op Azure-VM's uitgevoerd. 
- **Migreren binnen Azure**: hierbij worden Azure-VM's tussen Azure-regio's gemigreerd. 
- **AWS migreren**: hierbij worden AWS Windows-exemplaren naar Azure IaaS-VM's gemigreerd. 

> [!NOTE]
> U nu migreren van on-premises naar Azure met behulp van de Azure Migrate-service. [Meer informatie](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Wat wordt precies bedoeld met 'migreren’?

Naast het gebruik van Site Recovery voor noodherstel van on-premises en Azure VM's, u de siteherstelservice gebruiken om ze te migreren. Wat is het verschil?

- Voor herstel na noodgevallen repliceert u machines regelmatig naar Azure. Wanneer er een storing optreedt, mislukt u de machines van de primaire site naar de secundaire Azure-site en hebt u er vanaf daar toegang toe. Wanneer de primaire site weer beschikbaar is, mislukt u terug vanuit Azure.
- Voor migratie repliceert u on-premises machines naar Azure of Azure VM's naar een secundaire regio. Vervolgens mislukt u de VM-over van de primaire site naar het secundaire en voltooit u het migratieproces. Hierbij is geen failback betrokken.  


## <a name="migration-scenarios"></a> Migratiescenario's

**Scenario** | **Details**
--- | ---
**Migreren vanaf on-premises machines naar Azure** | U on-premises Vm's vmware, Hyper-V VM's en fysieke servers migreren naar Azure. Om dit te doen, voltooit u bijna dezelfde stappen als u zou doen voor volledig herstel na noodgevallen. U hoeft machines eenvoudigweg niet terug te laten van Azure naar de on-premises site.
**Migreren tussen Azure-regio's** | U Azure VM's migreren van de ene Azure-regio naar de andere. Nadat de migratie is voltooid, u disaster recovery configureren voor de Azure VM's nu in het secundaire gebied waarnaar u migreerde.
**AWS migreren naar Azure** | U kunt AWS-exemplaren naar Azure-VM's migreren. Site Recovery behandelt AWS-exemplaren als fysieke servers voor migratiedoeleinden. 

## <a name="next-steps"></a>Volgende stappen

- [On-premises machines migreren naar Azure](migrate-tutorial-on-premises-azure.md)
- [VM's van de ene Azure-regio naar de andere migreren](azure-to-azure-tutorial-migrate.md)
- [AWS migreren naar Azure](migrate-tutorial-aws-azure.md)
