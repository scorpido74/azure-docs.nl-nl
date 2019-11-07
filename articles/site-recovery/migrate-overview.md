---
title: Servers en virtuele machines migreren naar Azure met Azure Site Recovery
description: Hierin wordt beschreven hoe u on-premises en Azure IaaS Vm's naar Azure migreert met behulp van de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620617"
---
# <a name="about-migration"></a>Info over migratie

Lees dit artikel voor een beknopt overzicht van de manier waarop de [Azure site Recovery](site-recovery-overview.md) -service u helpt machines te migreren. 

U kunt als volgt migreren met Site Recovery:

- **Migreren van on-premises naar Azure**: Migreer on-premises virtuele Hyper-V-machines, virtuele VMware-machines en fysieke servers naar Azure. Na de migratie worden workloads die op de on-premises machines werden uitgevoerd, op Azure-VM's uitgevoerd. 
- **Migreren binnen Azure**: hierbij worden Azure-VM's tussen Azure-regio's gemigreerd. 
- **AWS migreren**: hierbij worden AWS Windows-exemplaren naar Azure IaaS-VM's gemigreerd. 

> [!NOTE]
> U kunt nu migreren van on-premises naar Azure met behulp van de Azure Migrate-service. [Meer informatie](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Wat wordt precies bedoeld met 'migreren’?

Naast het gebruik van Site Recovery voor herstel na nood gevallen van on-premises en virtuele Vm's van Azure, kunt u de Site Recovery-service gebruiken om ze te migreren. Wat is het verschil?

- Voor herstel na nood gevallen repliceert u machines regel matig naar Azure. Als er een storing optreedt, worden de machines van de primaire site naar de secundaire Azure-site geleid en krijgen ze daar toegang toe. Wanneer de primaire site weer beschikbaar is, wordt er een failback uitgevoerd van Azure.
- Voor migratie repliceert u on-premises machines naar Azure of Azure-Vm's naar een secundaire regio. Vervolgens voert u de virtuele machine van de primaire site naar de secundaire uit en voltooit u het migratie proces. Hierbij is geen failback betrokken.  


## <a name="migration-scenarios"></a>Migratie scenario's

**Scenario** | **Details**
--- | ---
**Migreren van on-premises naar Azure** | U kunt on-premises VMware-Vm's, virtuele Hyper-V-machines en fysieke servers naar Azure migreren. Hiervoor moet u bijna dezelfde stappen uitvoeren als voor een volledig herstel na nood gevallen. U kunt geen computers van Azure terugsturen naar de on-premises site.
**Migreren tussen Azure-regio's** | U kunt virtuele Azure-machines van de ene Azure-regio naar de andere migreren. Nadat de migratie is voltooid, kunt u herstel na nood gevallen configureren voor de virtuele Azure-machines in de secundaire regio waarnaar u hebt gemigreerd.
**AWS migreren naar Azure** | U kunt AWS-exemplaren naar Azure-VM's migreren. Site Recovery behandelt AWS-instanties als fysieke servers voor migratie doeleinden. 

## <a name="next-steps"></a>Volgende stappen

- [On-premises machines migreren naar Azure](migrate-tutorial-on-premises-azure.md)
- [VM's van de ene Azure-regio naar de andere migreren](azure-to-azure-tutorial-migrate.md)
- [AWS migreren naar Azure](migrate-tutorial-aws-azure.md)
