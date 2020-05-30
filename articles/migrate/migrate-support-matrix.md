---
title: Ondersteunings matrix Azure Migrate
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen voor de Azure Migrate-service.
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: raynew
ms.openlocfilehash: c1ed127834b826488d02b39304dd943866171441
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193577"
---
# <a name="azure-migrate-support-matrix"></a>Ondersteunings matrix Azure Migrate

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van algemene ondersteunings instellingen en beperkingen voor Azure Migrate scenario's en implementaties.

## <a name="supported-assessmentmigration-scenarios"></a>Ondersteunde scenario's voor evaluatie/migratie

De tabel bevat een overzicht van de ondersteunde detectie-, evaluatie-en migratie scenario's.

**Implementatie** | **Nadere** 
--- | --- 
**App-specifieke detectie** | U kunt apps, rollen en functies ontdekken die worden uitgevoerd op virtuele VMware-machines. Deze functie is momenteel beperkt tot detectie. De evaluatie bevindt zich momenteel op het niveau van de computer. We bieden nog geen app, rol of functie-specifieke evaluatie. 
**On-premises evaluatie** | Evalueer on-premises workloads en gegevens die worden uitgevoerd op virtuele VMware-machines, virtuele Hyper-V-machines en fysieke servers. Evalueer het gebruik van Azure Migrate server assessment en Microsoft Data Migration Assistant (DMA), evenals andere hulpprogram ma's en ISV-aanbiedingen.
**Migratie van on-premises naar Azure** | Migreer werk belastingen en gegevens die worden uitgevoerd op fysieke servers, virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers en virtuele machines in de Cloud naar Azure. Migreer met Azure Migrate server-evaluatie en-Azure Database Migration Service (DMS) en ook andere hulpprogram ma's en ISV-aanbiedingen.

> [!NOTE]
> Op dit moment kunnen ISV-hulpprogram ma's geen gegevens verzenden naar Azure Migrate in Azure Government. U kunt geïntegreerde micro soft-hulpprogram ma's gebruiken of partner hulpprogramma's onafhankelijk van.

## <a name="supported-tools"></a>Ondersteunde hulpprogramma 's

Specifieke ondersteuning van het hulp programma wordt in de tabel samenvatten.

**Hulpprogramma** | **Vast** | **Migreren** 
--- | --- | ---
Evaluatie van Azure Migrate-server | Evalueer [virtuele VMware-machines](tutorial-prepare-vmware.md), [virtuele Hyper-V-machines](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md). |  Niet beschikbaar (NA)
Azure Migrate-servermigratie | NA | Migreer [VMware-vm's](tutorial-migrate-vmware.md), [virtuele Hyper-V-machines](tutorial-migrate-hyper-v.md)en [fysieke servers](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | Migreer VMware-Vm's, virtuele Hyper-V-machines, fysieke servers, open bare Cloud werkbelastingen. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud. | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Evalueer en migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers, open bare Cloud werkbelastingen. |  Migreer VMware-Vm's, virtuele Hyper-V-machines, fysieke servers, open bare Cloud werkbelastingen.
[Apparaat 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud.| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Beoordeel SQL Server data bases. | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | Migrate SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Virtual Desktop Infrastructure (VDI) evalueren | NA
[Movere](https://www.movere.io/) | Bewaak virtuele VMWare-machines, Hyper-V-Vm's, xen-Vm's, fysieke machines, werk stations (inclusief VDI), open bare Cloud werkbelastingen | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | Virtuele VMWare-machines migreren, virtuele Hyper-V-machines, xen-Vm's, KVM-Vm's, fysieke machines, open bare Cloud werkbelastingen 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Evalueer virtuele VMware-machines, virtuele Hyper-V-machines, fysieke servers, werk belastingen voor de open bare Cloud. | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Evalueer virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, open bare Cloud werkbelastingen en SQL Server data bases. | NA
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Web-apps evalueren | Web-apps migreren.


## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Nadere**
--- | ---
Abonnement | U kunt meerdere Azure Migrate-projecten in een abonnement hebben.
Azure-machtigingen | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
Virtuele VMware-machines  | Evalueer Maxi maal 35.000 VMware-Vm's in één project.
Virtuele Hyper-V-machines    | Evalueer Maxi maal 35.000 Hyper-V-Vm's in één project.

Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.

## <a name="azure-permissions"></a>Azure-machtigingen

Azure Migrate werkt alleen met Azure als u deze machtigingen hebt voordat u computers gaat beoordelen en migreren.

**Taak** | **Machtigingen** | **Nadere**
--- | --- | ---
Een Azure Migrate-project maken | Uw Azure-account heeft machtigingen nodig om een project te maken. | Ingesteld voor [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project)-, [Hyper-V-](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)of [fysieke servers](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Het Azure Migrate apparaat registreren| Azure Migrate maakt gebruik van een licht gewicht [Azure migrate apparaat](migrate-appliance.md) om computers te beoordelen met de evaluatie van Azure migrate server, en voor het uitvoeren van [agentloze migratie](server-migrate-overview.md) van virtuele VMware-machines met Azure migrate server migratie. Met dit apparaat worden machines gedetecteerd en worden meta gegevens en prestaties naar Azure Migrate verzonden.<br/><br/> Tijdens de registratie worden providers (micro soft. OffAzure, micro soft. migrate en micro soft. de sleutel kluis) geregistreerd bij het abonnement dat is geselecteerd in het apparaat, zodat het abonnement samenwerkt met de resource provider. Als u zich wilt registreren, moet u de toegang tot mede werkers of eigenaar hebben voor het abonnement.<br/><br/> **VMware**: tijdens het voorbereiden van Azure migrate worden twee Azure Active Directory-apps (Azure AD) gemaakt. De eerste app communiceert tussen de apparaat agents en de Azure Migrate service. De app beschikt niet over machtigingen om Azure resource management-aanroepen te maken of om RBAC-toegang voor resources te hebben. De tweede app heeft alleen toegang tot een Azure Key Vault die is gemaakt in het gebruikers abonnement voor VMware-migratie zonder agent. Bij migratie zonder agent maakt Azure Migrate een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement. Het heeft RBAC-toegang op de Azure Key Vault (in de Tenant van de klant) wanneer detectie vanaf het apparaat wordt gestart.<br/><br/> **Hyper-V**-tijdens onboarding. Azure Migrate maakt een Azure AD-app. De app communiceert tussen de apparaat agents en de Azure Migrate service. De app beschikt niet over machtigingen om Azure resource management-aanroepen te maken of om RBAC-toegang voor resources te hebben. | Ingesteld voor [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)-, [Hyper-V-](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)of [fysieke servers](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Een sleutel kluis maken voor VMware-agentloze migratie | Als u virtuele VMware-machines wilt migreren met agentloze Azure Migrate server migratie, maakt Azure Migrate een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement. Als u de kluis wilt maken, stelt u machtigingen (eigenaar of Inzender en beheerder van gebruikers toegang) in voor de resource groep waarin het Azure Migrate project zich bevindt. | [Stel](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) machtigingen in.

## <a name="supported-geographies-public-cloud"></a>Ondersteunde geographs (open bare Cloud)

U kunt een Azure Migrate-project maken in een aantal geographs in de open bare Cloud. Hoewel u alleen projecten in deze geografi kunt maken, kunt u machines voor andere doel locaties evalueren of migreren. De Geografie van het project wordt alleen gebruikt om de gedetecteerde meta gegevens op te slaan.

**Geografie** | **Opslag locatie van meta gegevens**
--- | ---
Azië en Stille Oceaan | Azië Azië-oost of Zuidoost
Australië | Australië-oost of Australië-zuidoost
Brazilië | Brazilië - zuid
Canada | Canada-centraal of Canada-oost
Europa | Europa - noord of Europa - west
Frankrijk | Frankrijk - centraal
India | Centraal-India of India-zuid
Japan |  Japan-Oost of Japan-West
Korea | Korea-centraal of Korea-zuid
Verenigd Koninkrijk | UK-zuid of UK-west
Verenigde Staten | VS-midden, VS-West 2


## <a name="supported-geographies-azure-government"></a>Ondersteunde geographs (Azure Government)

**Taak** | **Geografie** | **Nadere**
--- | --- | ---
Project maken | Verenigde Staten | Meta gegevens worden opgeslagen in US Gov-Arizona, US Gov-Virginia
Doel evaluatie | Verenigde Staten | Doel regio's: US Gov-Arizona, US Gov-Virginia, US Gov-Texas
Doel replicatie | Verenigde Staten | Doel regio's: US DoD-centraal, US DoD-oost, US Gov-Arizona, US Gov-Iowa, US Gov-Texas, US Gov-Virginia


## <a name="vmware-assessment-and-migration"></a>VMware-evaluatie en-migratie

[Bekijk](migrate-support-matrix-vmware.md) de ondersteunings matrix voor Azure migrate server evaluatie en server migratie voor VMware-vm's.

## <a name="hyper-v-assessment-and-migration"></a>Evaluatie en migratie van Hyper-V

[Bekijk](migrate-support-matrix-hyper-v.md) de ondersteunings matrix voor de evaluatie van Azure migrate server en de server migratie voor Hyper-V-vm's.



## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: met deze versie kunt u nieuwe Azure migrate projecten maken, on-premises evalueren en analyses en migraties organiseren. [Meer informatie](whats-new.md).
- **Vorige versie**: voor klant die de vorige versie van Azure migrate gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate projecten maken of nieuwe detecties uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- [Evalueer virtuele VMware-machines](tutorial-assess-vmware.md) voor migratie.
- [Evalueer virtuele Hyper-V-machines](tutorial-assess-hyper-v.md) voor migratie.

