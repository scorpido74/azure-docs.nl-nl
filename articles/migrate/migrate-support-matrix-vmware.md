---
title: VMware-evaluatie ondersteuning in Azure Migrate
description: Meer informatie over VMware-evaluatie ondersteuning in Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029015"
---
# <a name="support-matrix-for-vmware-assessment"></a>Ondersteunings matrix voor VMware-evaluatie 

In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen van virtuele VMware-machines met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-migration-tool). Als u op zoek bent naar informatie over het migreren van virtuele VMware-machines naar Azure, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Overzicht

Als u on-premises machines wilt evalueren voor migratie naar Azure met dit artikel, voegt u het hulp programma Azure Migrate: Server Assessment toe aan een Azure Migrate-project. U implementeert het [Azure migrate-apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises machines en verzendt configuratie-en prestatie gegevens naar Azure. Na detectie van machines verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep.


## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten**| Ontdek en evalueer Maxi maal 35.000 VMware-Vm's in één [project](migrate-support-matrix.md#azure-migrate-projects).
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Een project kan bestaan uit VMware-Vm's, virtuele Hyper-V-machines en fysieke servers, tot aan de evaluatie limieten.
**Detectie** | Het Azure Migrate-apparaat kan Maxi maal 10.000 VMware-Vm's detecteren op een vCenter Server.
**Evaluatie** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.


## <a name="application-discovery"></a>Toepassingsdetectie

Naast het detecteren van computers, Azure Migrate: Server assessment kan apps, rollen en functies vinden die op machines worden uitgevoerd. Als u uw app-inventaris detecteert, kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads. 

**Ondersteuning** | **Details**
--- | ---
Discovery | Detectie is zonder agent, met behulp van de referenties van de computer gast en op afstand toegang tot computers met WMI-en SSH-aanroepen.
Ondersteunde computers | On-premises VMware-Vm's.
Machine besturings systeem | Alle Windows-en Linux-versies
Referenties | Biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
Beperkingen | Voor app-Discovery kunt u Maxi maal 10000 per apparaat detecteren. 

## <a name="vmware-requirements"></a>VMware-vereisten

**VMware** | **Details**
--- | ---
**vCenter Server** | Machines die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server versie 5,5, 6,0, 6,5 of 6,7.
**Machtigingen (beoordeling)** | vCenter Server alleen-lezen-account.
**Machtigingen (app-detectie)** | vCenter Server account met alleen-lezen toegang en bevoegdheden ingeschakeld voor virtuele machines > gast bewerkingen.
**Machtigingen (visualisatie van afhankelijkheden)** | Center Server-account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **virtuele machines** > - **gast bewerkingen**.


## <a name="azure-migrate-appliance-requirements"></a>Vereisten van Azure Migrate apparaat

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor VMware wordt geïmplementeerd met behulp van een eicellen-sjabloon, geïmporteerd in vCenter Server. 

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---vmware) voor VMware.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

**Apparaat** | **Verbinding**
--- | ---
Apparaat | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
vCenter-server | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.

## <a name="dependency-visualization"></a>Visualisatie van afhankelijkheden

Met de visualisatie van afhankelijkheden kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. U gebruikt meestal afhankelijkheids toewijzing als u computers wilt beoordelen met een hoger vertrouwens niveau. Voor virtuele VMware-machines wordt de visualisatie van afhankelijkheden als volgt ondersteund:

- **Visualisatie van afhankelijkheid zonder agent**: deze optie is momenteel beschikbaar als preview-versie. U hoeft geen agents op computers te installeren.
    - Het werkt door de TCP-verbindings gegevens vast te leggen van de computers waarvoor deze is ingeschakeld. Nadat de detectie van afhankelijkheden is gestart, verzamelt het apparaat gegevens van machines met een polling-interval van vijf minuten.
    - De volgende gegevens worden verzameld:
        - TCP-verbindingen
        - Namen van processen die actieve verbindingen hebben
        - Namen van geïnstalleerde toepassingen die de bovenstaande processen uitvoeren
        - Nee. van verbindingen die zijn gedetecteerd bij elk polling-interval
- **Visualisatie van afhankelijkheid op basis van een agent**: voor het gebruik van op agents gebaseerde afhankelijkheids visualisatie moet u de volgende agents downloaden en installeren op elke on-premises computer die u wilt analyseren.
    - Installeer micro soft Monitoring Agent (MMA) op elke machine. Meer [informatie](how-to-create-group-machine-dependencies.md#install-the-mma) over het installeren van de MMA-agent.
    - Installeer de afhankelijkheids agent op elke computer. Meer [informatie](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) over het installeren van de afhankelijkheids agent.
    - En als u VM's zonder internetverbinding hebt, moet u op deze machines Log Analytics-gateway downloaden en installeren.


## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties.
- [VMware](tutorial-prepare-vmware.md) -evaluatie voorbereiden.
