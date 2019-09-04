---
title: Ondersteunings matrix Azure Migrate
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen voor de Azure Migrate-service.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 3fb36eb9b8507903d1aee00b82c375685a4dfa6c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279476"
---
# <a name="azure-migrate-support-matrix"></a>Ondersteunings matrix Azure Migrate

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van algemene ondersteunings instellingen en beperkingen voor Azure Migrate scenario's en implementaties.


## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: Met deze versie kunt u nieuwe Azure Migrate projecten maken, on-premises evalueren en analyses en migraties organiseren. [Meer informatie](whats-new.md#azure-migrate-new-version).
- **Vorige versie**: Voor klanten die de vorige versie van Azure Migrate gebruiken (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate projecten maken of nieuwe detecties uitvoeren.

## <a name="supported-migration-scenarios"></a>Ondersteunde migratie scenario's

De tabel bevat een overzicht van de ondersteunde migratie scenario's.

**Implementatie** | **Details** 
--- | --- 
**On-premises evaluatie** | Beoordeling van on-premises workloads en gegevens die worden uitgevoerd op virtuele VMware-machines en virtuele Hyper-V-machines. Evalueer het gebruik van Azure Migrate server-evaluatie en-Microsoft Data Migration Assistant (DMA) en hulpprogram ma's van derden die Cloudamize, cohuur tech en Turbonomic server bevatten.
**Migratie van on-premises naar Azure** | Migreer werk belastingen en gegevens die worden uitgevoerd op fysieke servers, virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers en virtuele machines in de Cloud naar Azure. Migreer met Azure Migrate server assessment en Azure Database Migration Service (DMS) en gebruik hulpprogram ma's van derden die Carbonite en CorentTech bevatten.

De ondersteuning van specifieke hulp middelen wordt als volgt samenvatten.

**Hulpprogramma** | **Beoordeling/migratie** | **Details**
--- | --- | ---
Evaluatie van Azure Migrate-server | Evaluatie | Probeer de server evaluatie voor [Hyper-V](tutorial-prepare-hyper-v.md) en [VMware](tutorial-prepare-vmware.md)uit.
Cloudamize | Evaluatie | [Meer informatie](https://www.cloudamize.com/platform#tab-0).
CorentTech | Evaluatie | [Meer informatie](https://www.corenttech.com/).
Turbonomic | Evaluatie | [Meer informatie](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migratie van Azure Migrate server | Migratie | Probeer de server migratie voor [Hyper-V](tutorial-migrate-hyper-v.md) en [VMware](tutorial-migrate-vmware.md)uit.
Carbonite | Migratie | [Meer informatie](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migratie | [Meer informatie](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Details**
--- | ---
Subscription | U kunt meerdere Azure Migrate-projecten in een abonnement hebben.
Azure-machtigingen | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
Virtuele VMware-machines  | Evalueer Maxi maal 35.000 VMware-Vm's in één project.
Virtuele Hyper-V-machines | Evalueer Maxi maal 35.000 Hyper-V-Vm's in één project.

Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.


## <a name="vmware-assessment-and-migration"></a>VMware-evaluatie en-migratie

[Bekijk](migrate-support-matrix-vmware.md) de ondersteunings matrix voor Azure migrate server evaluatie en server migratie voor VMware-vm's.

## <a name="hyper-v-assessment-and-migration"></a>Evaluatie en migratie van Hyper-V

[Bekijk](migrate-support-matrix-hyper-v.md) de ondersteunings matrix voor de evaluatie van Azure migrate server en de server migratie voor Hyper-V-vm's.


## <a name="next-steps"></a>Volgende stappen

- [Evalueer virtuele VMware-machines](tutorial-assess-vmware.md) voor migratie.
- [Evalueer virtuele Hyper-V-machines](tutorial-assess-hyper-v.md) voor migratie.

