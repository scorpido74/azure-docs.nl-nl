---
title: Ondersteunings matrix Azure Migrate
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen voor de Azure Migrate-service.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: raynew
ms.openlocfilehash: fa6ea1ec1992c94d44531cda9802290edf8db301
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669158"
---
# <a name="azure-migrate-support-matrix"></a>Ondersteunings matrix Azure Migrate

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van algemene ondersteunings instellingen en beperkingen voor Azure Migrate scenario's en implementaties.


## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: met deze versie kunt u nieuwe Azure migrate projecten maken, on-premises evalueren en analyses en migraties organiseren. [Meer informatie](whats-new.md#release-version-july-2019).
- **Vorige versie**: voor klant die de vorige versie van Azure migrate gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate projecten maken of nieuwe detecties uitvoeren.

## <a name="supported-assessmentmigration-scenarios"></a>Ondersteunde scenario's voor evaluatie/migratie

De tabel bevat een overzicht van de ondersteunde detectie-, evaluatie-en migratie scenario's.

**Implementatie** | **Details** 
--- | --- 
**App-specifieke detectie** | U kunt apps, rollen en functies ontdekken die worden uitgevoerd op virtuele VMware-machines. Deze functie is momenteel beperkt tot detectie. De evaluatie bevindt zich momenteel op het niveau van de computer. We bieden nog geen app, rol of functie-specifieke evaluatie. 
**On-premises evaluatie** | Evalueer on-premises workloads en gegevens die worden uitgevoerd op virtuele VMware-machines, virtuele Hyper-V-machines en fysieke servers. Evalueer het gebruik van Azure Migrate server assessment en Microsoft Data Migration Assistant (DMA), evenals andere hulpprogram ma's en ISV-aanbiedingen.
**Migratie van on-premises naar Azure** | Migreer werk belastingen en gegevens die worden uitgevoerd op fysieke servers, virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers en virtuele machines in de Cloud naar Azure. Migreer met Azure Migrate server-evaluatie en-Azure Database Migration Service (DMS) en ook andere hulpprogram ma's en ISV-aanbiedingen.


## <a name="supported-tools"></a>Ondersteunde hulpprogram ma's

Specifieke ondersteuning van het hulp programma wordt in de tabel samenvatten.

**Hulpprogramma** | **Vast** | **Migreren** 
--- | --- | ---
Evaluatie van Azure Migrate-server | Evalueer [virtuele VMware-machines](tutorial-prepare-vmware.md), [virtuele Hyper-V-machines](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md). |  Niet beschikbaar (NA)
Migratie van Azure Migrate server | N.V.T. | Migreer [VMware-vm's](tutorial-migrate-vmware.md), [virtuele Hyper-V-machines](tutorial-migrate-hyper-v.md)en [fysieke servers](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N.V.T. | Migreer VMware-Vm's, virtuele Hyper-V-machines, fysieke servers, open bare Cloud werkbelastingen. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud. | N.V.T.
[Cohuur technologie](https://go.microsoft.com/fwlink/?linkid=2084928) | Evalueer en migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers, open bare Cloud werkbelastingen. |  Migreer VMware-Vm's, virtuele Hyper-V-machines, fysieke servers, open bare Cloud werkbelastingen.
[Apparaat 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud.| N.V.T.
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | On-premises SQL Server data bases evalueren. | N.V.T.
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | N.V.T. | Migrate SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Virtual Desktop Infrastructure (VDI) evalueren | N.V.T.
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Bewaak virtuele VMWare-machines, Hyper-V-Vm's, xen-Vm's, fysieke machines, werk stations (inclusief VDI), open bare Cloud werkbelastingen | N.V.T.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N.V.T. | Virtuele VMWare-machines migreren, virtuele Hyper-V-machines, xen-Vm's, KVM-Vm's, fysieke machines, open bare Cloud werkbelastingen 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud. | N.V.T.
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Evalueer virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, open bare Cloud werkbelastingen en SQL Server data bases. | N.V.T.
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Web-apps evalueren | Web-apps migreren.


## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Details**
--- | ---
Abonnement | U kunt meerdere Azure Migrate-projecten in een abonnement hebben.
Azure-machtigingen | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
Virtuele VMware-machines  | Evalueer Maxi maal 35.000 VMware-Vm's in één project.
Virtuele Hyper-V-machines | Evalueer Maxi maal 35.000 Hyper-V-Vm's in één project.

Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.

## <a name="supported-geographies"></a>Ondersteunde geografs

U kunt een Azure Migrate project maken in een aantal geographs. Hoewel u alleen projecten in deze geografi kunt maken, kunt u machines voor andere doel locaties evalueren of migreren. De Geografie van het project wordt alleen gebruikt om de gedetecteerde meta gegevens op te slaan.

**Geografie** | **Opslag locatie van meta gegevens**
--- | ---
Azure Government | US Gov - Virginia
Azië-Pacific | Azië Azië-oost of Zuidoost
Australië | Australië-oost of Australië-zuidoost
Brazilië | Brazilië - Zuid
Canada | Canada-centraal of Canada-oost
Europa | Europa - noord of Europa - west
Frankrijk | Frankrijk - centraal
India | Centraal-India of India-zuid
Japan |  Japan-Oost of Japan-West
Korea | Korea-centraal of Korea-zuid
Verenigd Koninkrijk | UK-zuid of UK-west
Verenigde Staten | VS-midden, VS-West 2


 > [!NOTE]
 > Ondersteuning voor Azure Government is momenteel alleen beschikbaar voor de [oudere versie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>VMware-evaluatie en-migratie

[Bekijk](migrate-support-matrix-vmware.md) de ondersteunings matrix voor Azure migrate server evaluatie en server migratie voor VMware-vm's.

## <a name="hyper-v-assessment-and-migration"></a>Evaluatie en migratie van Hyper-V

[Bekijk](migrate-support-matrix-hyper-v.md) de ondersteunings matrix voor de evaluatie van Azure migrate server en de server migratie voor Hyper-V-vm's.


## <a name="next-steps"></a>Volgende stappen

- [Evalueer virtuele VMware-machines](tutorial-assess-vmware.md) voor migratie.
- [Evalueer virtuele Hyper-V-machines](tutorial-assess-hyper-v.md) voor migratie.

