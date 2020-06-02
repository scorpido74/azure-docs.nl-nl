---
title: VMware-evaluatie ondersteuning in Azure Migrate
description: Meer informatie over ondersteuning voor VMware VM-evaluatie met Azure Migrate server-evaluatie.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 8e0b6f3babcc862e1a78effcdb1a61f430c01646
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267882"
---
# <a name="support-matrix-for-vmware-assessment"></a>Ondersteunings matrix voor VMware-evaluatie 

In dit artikel vindt u een overzicht van de vereisten voor en ondersteuning wanneer u virtuele VMware-machines beoordeelt voor migratie naar Azure, met behulp van het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool. Als u virtuele VMware-machines wilt migreren naar Azure, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-vmware-migration.md).

Als u virtuele VMware-machines wilt beoordelen, maakt u een Azure Migrate project en voegt u vervolgens het hulp programma voor Server evaluatie toe aan het project. Wanneer het hulp programma is toegevoegd, implementeert u het [Azure migrate apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises machines en verzendt meta gegevens en prestatie gegevens van de machine naar Azure. Nadat de detectie is voltooid, verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep.

## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Nadere**
--- | ---
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement.<br/><br/> U kunt Maxi maal 35.000 VMware-Vm's in één [project](migrate-support-matrix.md#azure-migrate-projects)detecteren en beoordelen. Een project kan ook fysieke servers en virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten voor elke.
**Detectie** | Het Azure Migrate-apparaat kan Maxi maal 10.000 VMware-Vm's detecteren op een vCenter Server.
**Onderzoek** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.


## <a name="application-discovery"></a>Toepassingsdetectie

Naast het detecteren van computers kunnen server analyses apps, rollen en functies vinden die op machines worden uitgevoerd. Als u uw app-inventaris detecteert, kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads. 

**Ondersteuning** | **Nadere**
--- | ---
**Ondersteunde computers** | App-detectie wordt momenteel alleen ondersteund voor virtuele VMware-machines.
**Detectie** | App-detectie is zonder agent. Hierbij worden de gast referenties van de machine gebruikt en kunnen computers op afstand worden benaderd met WMI-en SSH-aanroepen.
**VM-ondersteuning** | App-Discovery wordt ondersteund voor alle versies van Windows en Linux.
**vCenter-referenties** | App-detectie heeft een vCenter Server account met alleen-lezen toegang en bevoegdheden ingeschakeld voor Virtual Machines >-gast bewerkingen.
**VM-referenties** | App-detectie biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
**VMware-hulpprogram ma's** | VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt detecteren. <br/> De versie van de VMware-hulpprogram ma's moet later zijn dan 10.2.0.
**PowerShell** | Op Windows-Vm's moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Poort toegang** | Op ESXi-hosts waarop Vm's worden uitgevoerd die u wilt detecteren, moet het Azure Migrate apparaat verbinding kunnen maken met TCP-poort 443.
**Limieten** | Voor app-Discovery kunt u tot 10000 Vm's op elk Azure Migrate apparaat detecteren.



## <a name="vmware-requirements"></a>VMware-vereisten

**VMware** | **Nadere**
--- | ---
**Virtuele VMware-machines** | Alle besturings systemen kunnen worden geëvalueerd voor migratie. 
**vCenter Server** | Machines die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server versie 5,5, 6,0, 6,5 of 6,7.
**Machtigingen (beoordeling)** | vCenter Server alleen-lezen-account.
**Machtigingen (app-detectie)** | vCenter Server account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **virtuele machines > gast bewerkingen**.
**Machtigingen (visualisatie van afhankelijkheden)** | vCenter Server account met alleen-lezen toegang en de bevoegdheden die zijn **Virtual machines**ingeschakeld voor  >  **gast bewerkingen**voor virtuele machines.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. U kunt het apparaat als een virtuele VMWare-machine implementeren met behulp van een eicellen-sjabloon, geïmporteerd in vCenter Server of een [Power shell-script](deploy-appliance-script.md)gebruiken.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---vmware) voor VMware.
- Meer informatie over Url's die het apparaat nodig heeft voor toegang tot [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
- In Azure Government moet u het apparaat implementeren [met behulp van het script](deploy-appliance-script-government.md).


## <a name="port-access"></a>Poort toegang

**Apparaat** | **Verbinding**
--- | ---
Apparaat | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443 (HTTPS) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
vCenter-server | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.
ESXi-hosts (app-detectie/agentloze afhankelijkheids analyse) | Als u [app-detectie](how-to-discover-applications.md) of een [afhankelijkheids analyse zonder agent](concepts-dependency-visualization.md#agentless-analysis)wilt uitvoeren, maakt het apparaat verbinding met ESXI-hosts op TCP-poort 443, om toepassingen te detecteren, en voor het uitvoeren van zonder agents afhankelijke afhankelijkheids visualisatie op vm's.

## <a name="application-discovery"></a>Toepassingsdetectie

Naast het detecteren van computers kunnen server analyses apps, rollen en functies vinden die op machines worden uitgevoerd. Als u uw app-inventaris detecteert, kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads. 

**Ondersteuning** | **Nadere**
--- | ---
**Ondersteunde computers** | App-detectie wordt momenteel alleen ondersteund voor virtuele VMware-machines.
**Detectie** | App-detectie is zonder agent. Hierbij worden de gast referenties van de machine gebruikt en kunnen computers op afstand worden benaderd met WMI-en SSH-aanroepen.
**VM-ondersteuning** | App-Discovery wordt ondersteund voor alle versies van Windows en Linux.
**vCenter-referenties** | App-detectie heeft een vCenter Server account met alleen-lezen toegang en bevoegdheden ingeschakeld voor Virtual Machines >-gast bewerkingen.
**VM-referenties** | App-detectie biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
**VMware-hulpprogram ma's** | VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt detecteren. <br/> De versie van de VMware-hulpprogram ma's moet later zijn dan 10.2.0.
**PowerShell** | Op Vm's moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Poort toegang** | Op ESXi-hosts waarop Vm's worden uitgevoerd die u wilt detecteren, moet het Azure Migrate apparaat verbinding kunnen maken met TCP-poort 443.
**Limieten** | Voor app-Discovery kunt u tot 10000 Vm's op elk Azure Migrate apparaat detecteren.


## <a name="agentless-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheids analyse zonder agent

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van een afhankelijkheids analyse zonder agent. 

**Vereiste** | **Nadere**
--- | --- 
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn met het hulp programma voor Server evaluatie dat is toegevoegd aan het project.<br/><br/>  U kunt afhankelijkheids visualisatie implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises VMWare-machines te detecteren.<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/> [Meer informatie over](how-to-set-up-appliance-vmware.md) het instellen van het Azure migrate-apparaat voor de evaluatie van virtuele VMware-machines.
**VM-ondersteuning** | Momenteel alleen ondersteund voor virtuele VMware-machines.
**Virtuele Windows-machines** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bits).
**Windows-account** |  Voor de afhankelijkheids analyse heeft het Azure Migrate-apparaat een domein beheerders account of een lokaal beheerders account nodig om toegang te krijgen tot Windows-Vm's.
**Virtuele Linux-machines** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-account** | Voor afhankelijkheids analyse, op Linux-machines, heeft het Azure Migrate apparaat een gebruikers account nodig met de bevoegdheid root.<br/><br/> Het gebruikers account heeft ook de volgende machtigingen nodig voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.
**Vereiste agents** | Er is geen agent vereist voor computers die u wilt analyseren.
**VMware-Hulpprogram Ma's** | VMware-Hulpprogram Ma's (hoger dan 10,2) moeten zijn geïnstalleerd en worden uitgevoerd op elke virtuele machine die u wilt analyseren.
**vCenter Server referenties** | Voor afhankelijkheids visualisatie moet een vCenter Server account met alleen-lezen toegang en bevoegdheden zijn ingeschakeld voor Virtual Machines >-gast bewerkingen. 
**PowerShell** | Op Windows-Vm's moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.
**Poort toegang** | Op ESXi-hosts waarop Vm's worden uitgevoerd die u wilt analyseren, moet het Azure Migrate apparaat verbinding kunnen maken met TCP-poort 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheids analyse op basis van een agent

[Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel bevat een overzicht van de vereisten voor het instellen van afhankelijkheids analyse op basis van een agent. 

**Vereiste** | **Nadere** 
--- | --- 
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project.<br/><br/>  U kunt een afhankelijkheids visualisatie implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises computers te detecteren<br/><br/> [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Azure Government** | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../operations-management-suite/operations-management-suite-service-map.md) oplossing in [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Vereiste agents** | Installeer de volgende agents op elke computer die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Als on-premises computers niet zijn verbonden met internet, moet u Log Analytics gateway op deze machines downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-werkruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics koppelt aan het Azure Migrate project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.
**Internetconnectiviteit** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.
**Azure Government** | Afhankelijkheids analyse op basis van een agent wordt niet ondersteund.


## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties.
- [VMware](tutorial-prepare-vmware.md) -evaluatie voorbereiden.
