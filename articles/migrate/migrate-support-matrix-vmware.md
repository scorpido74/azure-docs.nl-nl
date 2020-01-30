---
title: VMware-evaluatie ondersteuning in Azure Migrate
description: Meer informatie over VMware-evaluatie ondersteuning in Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 8ed20ecd37eacdcb771db7c166ff8fc22b96cb89
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846173"
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
**Detectie** | Detectie is zonder agent, met behulp van de referenties van de computer gast en op afstand toegang tot computers met WMI-en SSH-aanroepen.
**Ondersteunde computers** | On-premises VMware-Vm's.
**Machine besturings systeem** | Alle Windows-en Linux-versies.
**vCenter-referenties** | Een vCenter Server account met alleen-lezen toegang en de bevoegdheden die zijn ingeschakeld voor Virtual Machines > gast bewerkingen.
**VM-referenties** | Biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
**VMware-hulpprogram ma's** | VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt detecteren.
**Poort toegang** | Op ESXi-hosts waarop Vm's worden uitgevoerd die u wilt detecteren, moet het Azure Migrate apparaat verbinding kunnen maken met TCP-poort 443.
**Limieten** | Voor app-Discovery kunt u Maxi maal 10000 per apparaat detecteren. 

## <a name="vmware-requirements"></a>VMware-vereisten

**VMware** | **Details**
--- | ---
**vCenter Server** | Machines die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server versie 5,5, 6,0, 6,5 of 6,7.
**Machtigingen (beoordeling)** | vCenter Server alleen-lezen-account.
**Machtigingen (app-detectie)** | vCenter Server account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **virtuele machines > gast bewerkingen**.
**Machtigingen (visualisatie van afhankelijkheden)** | Center Server-account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **virtuele machines** > - **gast bewerkingen**.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor VMware wordt geïmplementeerd met behulp van een eicellen-sjabloon, geïmporteerd in vCenter Server. 

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---vmware) voor VMware.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

**Apparaatconfiguratie** | **Verbinding**
--- | ---
Apparaat | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443 (HTTPS), 5671 en 5672 (AMQP) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
vCenter-server | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.
ESXi-hosts | **Alleen vereist voor de visualisatie van de [toepassings detectie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) en de [afhankelijkheid van agents](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization)** <br/><br/> Het apparaat maakt verbinding met ESXi-hosts op TCP-poort 443 voor het detecteren van toepassingen en het uitvoeren van een afhankelijkheids visualisatie zonder agents op de Vm's die op de hosts worden uitgevoerd.

## <a name="agent-based-dependency-visualization"></a>Visualisatie van afhankelijkheid op basis van een agent

Met de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md) kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. Voor visualisaties op basis van een agent worden vereisten en beperkingen in de volgende tabel samenvatten:


**Vereiste** | **Details**
--- | ---
**Implementatie** | Voordat u een afhankelijkheids visualisatie implementeert, moet er een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project. U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren.<br/><br/> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Serviceoverzicht** | Visualisatie op basis van een agent maakt gebruik van de [servicetoewijzing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) oplossing in [Azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Als u wilt implementeren, koppelt u een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project.
**Log Analytics-werkruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Belastingen** | Voor de Servicetoewijzing oplossing worden geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics aan het Azure Migrate project hebt gekoppeld).<br/><br/> Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden standaard Log Analytics kosten in rekening gebracht.<br/><br/> Als u het Azure Migrate project verwijdert, wordt de werk ruimte niet verwijderd. Nadat het project is verwijderd, is Servicetoewijzing niet gratis en worden alle knoop punten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte.
**Middelen** | Voor visualisatie op basis van een agent moet u twee agents installeren op elke computer die u wilt analyseren.<br/><br/> - [micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - - [afhankelijkheids agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Verbinding met internet** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.


## <a name="agentless-dependency-visualization"></a>Visualisatie van afhankelijkheid zonder agent

Deze optie is momenteel beschikbaar als preview-versie. [Meer informatie](how-to-create-group-machine-dependencies-agentless.md). In de volgende tabel vindt u een overzicht van de vereisten.

**Vereiste** | **Details**
--- | ---
**Implementatie** | Voordat u een afhankelijkheids visualisatie implementeert, moet er een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project. U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren.
**VM-ondersteuning** | Momenteel alleen ondersteund voor virtuele VMware-machines.
**Virtuele Windows-machines** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bits)
**Virtuele Linux-machines** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Windows-account** |  Voor visualisatie is een gebruikers account met gast toegang vereist.
**Linux-account** | Voor visualisatie is een gebruikers account met hoofd bevoegdheid vereist.<br/><br/> Het gebruikers account heeft ook de volgende machtigingen nodig voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.
**VM-agents** | Er is geen agent nodig op de Vm's.
**VMware-hulpprogram ma's** | VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt analyseren.
**vCenter-referenties** | Een vCenter Server account met alleen-lezen toegang en de bevoegdheden die zijn ingeschakeld voor Virtual Machines > gast bewerkingen.
**Poort toegang** | Op ESXi-hosts waarop Vm's worden uitgevoerd die u wilt analyseren, moet het Azure Migrate apparaat verbinding kunnen maken met TCP-poort 443.



## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties.
- [VMware](tutorial-prepare-vmware.md) -evaluatie voorbereiden.
