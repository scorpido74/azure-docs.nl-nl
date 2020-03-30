---
title: Ondersteuningsmatrix voor Azure Migreren
description: Biedt een overzicht van ondersteuningsinstellingen en beperkingen voor de Azure Migrate-service.
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: bf719f9179384ec3dca99d2429f569ef209b5daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127706"
---
# <a name="azure-migrate-support-matrix"></a>Ondersteuningsmatrix voor Azure Migreren

U de [Azure Migrate-service](migrate-overview.md) gebruiken om machines te beoordelen en te migreren naar de Microsoft Azure-cloud. In dit artikel worden algemene ondersteuningsinstellingen en -beperkingen voor Azure Migrate-scenario's en -implementaties samengevat.

## <a name="supported-assessmentmigration-scenarios"></a>Ondersteunde beoordelings-/migratiescenario's

De tabel bevat een overzicht van ondersteunde detectie-, beoordelings- en migratiescenario's.

**Implementatie** | **Details** 
--- | --- 
**App-specifieke detectie** | U apps, rollen en functies ontdekken die worden uitgevoerd op Vm's van VMware. Momenteel is deze functie beperkt tot detectie alleen. De beoordeling bevindt zich momenteel op machineniveau. We bieden nog geen app-, rol- of functiespecifieke beoordeling aan. 
**On-premises beoordeling** | On-premises workloads en gegevens die worden uitgevoerd op Vm's van VMware, Hyper-V VM's en fysieke servers beoordelen. Beoordelen met azure migrate server assessment en Microsoft Data Migration Assistant (DMA), evenals andere hulpprogramma's en ISV-aanbiedingen.
**On-premises migratie naar Azure** | Migreer workloads en gegevens die worden uitgevoerd op fysieke servers, VMware VM's, Hyper-V VM's, fysieke servers en vms in de cloud naar Azure. Migreer met Azure Migrate Server Assessment en Azure Database Migration Service (DMS) en andere hulpprogramma's en ISV-aanbiedingen.


## <a name="supported-tools"></a>Ondersteunde hulpprogramma 's

Specifieke toolondersteuning wordt samengevat in de tabel.

**Hulpprogramma** | **Beoordelen** | **Migreren** 
--- | --- | ---
Azure-beoordeling van de server migreren | Beoordeel [VMware VM's,](tutorial-prepare-vmware.md) [Hyper-V VM's](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md). |  Niet beschikbaar (NA)
Azure Migrate-servermigratie | N.v.t. | VMware [VM's,](tutorial-migrate-vmware.md) [Hyper-V VM's](tutorial-migrate-hyper-v.md)en [fysieke servers migreren](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N.v.t. | Migreer VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Beoordeel VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads. | N.v.t.
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads beoordelen en migreren. |  Migreer VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads.
[Apparaat 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Beoordeel VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads.| N.v.t.
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | On-premises SQL Server-databases beoordelen. | N.v.t.
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | N.v.t. | Migreer SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Virtuele desktopinfrastructuur (VDI) beoordelen | N.v.t.
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare VMM's, Hyper-V VM's, Xen VM's, fysieke machines, werkstations (inclusief VDI), public cloud workloads beoordelen | N.v.t.
[RackWare (RackWare)](https://go.microsoft.com/fwlink/?linkid=2102735) | N.v.t. | VMWare VMMs, Hyper-V VM's, Xen VM's, KVM's, fysieke machines, public cloudworkloads migreren 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Beoordeel VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads. | N.v.t.
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Beoordeel VMware VM's, Hyper-V VM's, fysieke servers, public cloud workloads en SQL Server databases. | N.v.t.
[Webapp-migratieassistent](https://appmigration.microsoft.com/) | Web-apps beoordelen | Web-apps migreren.


## <a name="azure-migrate-projects"></a>Azure-migratieprojecten

**Ondersteuning** | **Details**
--- | ---
Abonnement | U meerdere Azure Migrate-projecten in een abonnement hebben.
Azure-machtigingen | U hebt inzender- of eigenaarmachtigingen in het abonnement machtigingen nodig om een Azure Migrate-project te maken.
Virtuele VMware-machines  | Beoordeel tot 35.000 VMware VM's in één project.
Virtuele Hyper-V-machines    | Beoordeel tot 35.000 Hyper-V VM's in één project.

Een project kan zowel VMware VM's als Hyper-V VM's bevatten, tot aan de beoordelingslimieten.

## <a name="azure-permissions"></a>Azure-machtigingen

Voor Azure Migreren om met Azure te werken, hebt u deze machtigingen nodig voordat u machines gaat beoordelen en migreren.

**Taak** | **Machtigingen** | **Details**
--- | --- | ---
Een Azure-migratieproject maken | Uw Azure-account heeft machtigingen nodig om een project te maken. | Instellen voor [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)of [fysieke servers](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Het Azure Migrate-toestel registreren| Azure Migrate gebruikt een lichtgewicht [Azure Migrate-toestel](migrate-appliance.md) om machines te beoordelen met Azure Migrate Server Assessment en om [agentless migratie](server-migrate-overview.md) van VMware VM's met Azure Migrate Server Migration uit te voeren. Dit toestel detecteert machines en stuurt metagegevens en prestatiegegevens naar Azure Migrate.<br/><br/> Tijdens de registratie worden registerproviders (Microsoft.OffAzure, Microsoft.Migrate en Microsoft.KeyVault) geregistreerd bij het abonnement dat in het toestel is gekozen, zodat het abonnement werkt met de resourceprovider. Als u zich wilt registreren, hebt u toegang tot inzender of eigenaar nodig voor het abonnement.<br/><br/> **VMware**-Tijdens het instappen maakt Azure Migrate twee Azure Active Directory -apps (Azure AD). De eerste app communiceert tussen de toestelagents en de Azure Migrate-service. De app heeft geen machtigingen om Azure resource management calls te voeren of RBAC toegang te hebben voor resources. De tweede app heeft toegang tot een Azure Key Vault die is gemaakt in het gebruikersabonnement voor alleen agentloze VMware-migratie. Bij agentless migratie maakt Azure Migrate een Key Vault om toegangssleutels te beheren tot het replicatieopslagaccount in uw abonnement. Het heeft RBAC-toegang op de Azure Key Vault (in de clienttenant) wanneer detectie wordt gestart vanuit het toestel.<br/><br/> **Hyper-V**-Tijdens het instappen. Azure Migrate maakt één Azure AD-app. De app communiceert tussen de toestelagents en de Azure Migrate-service. De app heeft geen machtigingen om Azure resource management calls te voeren of RBAC toegang te hebben voor resources. | Instellen voor [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)of [fysieke servers](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Een sleutelkluis maken voor VMware-agentloze migratie | Als u VMware VM's wilt migreren met agentless Azure Migrate Server Migration, maakt Azure Migrate een Key Vault om toegangssleutels te beheren tot het replicatieopslagaccount in uw abonnement. Als u de kluis wilt maken, stelt u machtigingen (eigenaar of inzender- en gebruikerstoegangsbeheerder) in op de brongroep waarin het Azure Migrate-project zich bevindt. | [Machtigingen instellen.](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)

## <a name="supported-geographies"></a>Ondersteunde regio's

U een Azure Migrate-project maken in een aantal regio's. Hoewel u alleen projecten in deze regio's maken, u machines voor andere doellocaties beoordelen of migreren. De projectgeografie wordt alleen gebruikt om de gedetecteerde metagegevens op te slaan.

**Geografie** | **Opslaglocatie met ametagegevens**
--- | ---
Azure Government | VS (overheid) - Virginia
Azië en Stille Oceaan | Oost-Azië of Zuidoost-Azië
Australië | Australië Oost of Australië Zuidoost
Brazilië | Brazilië - zuid
Canada | Canada Centraal of Canada Oost
Europa | Europa - noord of Europa - west
Frankrijk | Frankrijk - centraal
India | Centraal-India of Zuid-India
Japan |  Japan Oost of Japan West
Korea | Korea Centraal of Korea Zuid
Verenigd Koninkrijk | Verenigd Koninkrijk Zuid of UK West
Verenigde Staten | Centraal VS of West US 2


 > [!NOTE]
 > Ondersteuning voor Azure Government is momenteel alleen beschikbaar voor de [oudere versie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>VMware-beoordeling en migratie

[Controleer](migrate-support-matrix-vmware.md) de ondersteuningsmatrix azure migrateserverbeoordeling en servermigratie voor Vm's van VMware.

## <a name="hyper-v-assessment-and-migration"></a>Evaluatie en migratie van Hyper-V

[Controleer](migrate-support-matrix-hyper-v.md) de ondersteuningsmatrix azure migrateserverbeoordeling en servermigratie voor Hyper V VM's.



## <a name="azure-migrate-versions"></a>Azure-migratieversies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: Met deze versie u nieuwe Azure Migrate-projecten maken, on-premises beoordelingen ontdekken en beoordelingen en migraties orkestreren. [Meer informatie](whats-new.md).
- **Vorige versie**: Voor klanten die de vorige versie van Azure Migrate gebruiken (alleen beoordeling van on-premises VMware VM's werd ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie u geen nieuwe Azure Migrate-projecten maken of nieuwe ontdekkingen uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- [Beoordeel VMware VM's](tutorial-assess-vmware.md) op migratie.
- [Beoordeel Hyper-V VM's](tutorial-assess-hyper-v.md) op migratie.

