---
title: Servers en virtuele machines migreren naar Azure met Azure Site Recovery
description: Hierin wordt beschreven hoe u on-premises en Azure IaaS Vm's naar Azure migreert met behulp van de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281307"
---
# <a name="about-migration"></a>Info over migratie

Gebruik de Azure Migrate-service om Vm's en servers te migreren naar Azure, in plaats van de Azure Site Recovery-service. Meer [informatie](../migrate/migrate-services-overview.md) over Azure Migrate.


## <a name="why-use-azure-migrate"></a>Waarom Azure Migrate gebruiken?

Het gebruik van Azure Migrate voor migratie biedt een aantal voor delen:
 
 
- Azure Migrate biedt een gecentraliseerde hub voor detectie, evaluatie en migratie naar Azure.
- Het gebruik van Azure Migrate biedt interoperabiliteit en toekomstige uitbrei ding met Azure Migrate-hulpprogram ma's, andere Azure-Services en hulpprogram ma's van derden.
- De Azure Migrate: hulp programma voor server migratie is bedoeld voor server migratie naar Azure. Deze is geoptimaliseerd voor migratie. U hoeft niet meer te weten te komen over concepten en scenario's die niet rechtstreeks relevant zijn voor migratie. 
- Er zijn geen kosten voor het gebruik van hulpprogram ma's voor de migratie van 180 dagen, vanaf het moment dat de replicatie voor een virtuele machine wordt gestart. Dit geeft u tijd om de migratie te volt ooien. U betaalt alleen voor de opslag-en netwerk resources die worden gebruikt tijdens de replicatie, en voor de reken kosten die tijdens de test migraties worden verbruikt.
- Voor virtuele VMware-machines biedt Azure Migrate een migratie zonder agent, naast migratie op basis van een agent.
- Er worden prioriteiten gegeven voor nieuwe migratie functies voor de Azure Migrate: alleen hulp programma voor server migratie.

## <a name="when-to-use-site-recovery"></a>Wanneer moet ik Site Recovery gebruiken?

Site Recovery moet worden gebruikt:

- Voor herstel na nood gevallen van on-premises machines naar Azure.
- Voor herstel na nood gevallen van Azure-Vm's tussen Azure-regio's.

We raden u aan Azure Migrate te gebruiken voor het migreren van on-premises servers naar Azure, als u uw migratie traject al hebt gestart met Site Recovery, kunt u deze blijven gebruiken om de migratie te volt ooien.  

## <a name="next-steps"></a>Volgende stappen

> [Bekijk veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.
