---
title: Ondersteuning voor VMware-beoordeling in Azure Migrate
description: Meer informatie over ondersteuning voor VMware VM-beoordeling met Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532201"
---
# <a name="support-matrix-for-vmware-assessment"></a>Ondersteuningsmatrix voor VMware-beoordeling 

In dit artikel worden de vereisten en ondersteuningsvereisten samengevat wanneer u VMware VM's beoordeelt op migratie naar Azure, met behulp van het hulpprogramma Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Als u VMware VM's wilt migreren naar Azure, controleert u de [matrix voor migratieondersteuning](migrate-support-matrix-vmware-migration.md).

Als u VMware VM's wilt beoordelen, maakt u een Azure Migrate-project en voegt u vervolgens het hulpprogramma voor serverbeoordeling toe aan het project. Nadat het hulpprogramma is toegevoegd, implementeert u het [Azure Migrate-toestel](migrate-appliance.md). Het toestel detecteert continu on-premises machines en stuurt machinemetadata en prestatiegegevens naar Azure. Nadat detectie is voltooid, verzamelt u gedetecteerde machines in groepen en voert u een beoordeling uit voor een groep.

## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Projectlimieten** | U meerdere projecten maken in een Azure-abonnement.<br/><br/> U tot 35.000 VMware VM's in één [project](migrate-support-matrix.md#azure-migrate-projects)ontdekken en beoordelen. Een project kan ook fysieke servers en Hyper-V VM's bevatten, tot de beoordelingslimieten voor elk project.
**Detectie** | Het Azure Migrate-toestel kan tot 10.000 VMware VM's detecteren op een vCenter-server.
**Beoordeling** | U maximaal 35.000 machines in één groep toevoegen.<br/><br/> U maximaal 35.000 VM's beoordelen in één beoordeling.

[Meer informatie](concepts-assessment-calculation.md) over beoordelingen.


## <a name="application-discovery"></a>Toepassingsdetectie

Server Assessment kan niet alleen machines ontdekken, maar ook apps, rollen en functies ontdekken die op machines worden uitgevoerd. Als u uw app-voorraad ontdekt, u een migratiepad identificeren en plannen dat is afgestemd op uw on-premises workloads. 

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde machines** | App-detectie wordt momenteel alleen ondersteund voor VMware VM's.
**Detectie** | App-detectie is agentloos. Het maakt gebruik van machine gast referenties, en op afstand toegang tot machines met behulp van WMI en SSH gesprekken.
**VM-ondersteuning** | App-discovery wordt ondersteund voor alle Windows- en Linux-versies.
**vCenter-referenties** | Voor app-detectie is een vCenter Server-account nodig met alleen-lezen toegang en bevoegdheden die zijn ingeschakeld voor virtuele machines > Gastbewerkingen.
**VM-referenties** | App-detectie ondersteunt momenteel het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers.<br/><br/> U maakt een gastgebruikersaccount voor Windows VM's en een regelmatig/normaal gebruikersaccount (niet-sudo-toegang) voor alle Linux-VM's.
**VMware-hulpprogramma's** | VMware-hulpprogramma's moeten worden geïnstalleerd en worden uitgevoerd op VM's die u wilt ontdekken. <br/> De versie van de VMware-tools moet later zijn dan 10.2.0.
**PowerShell** | VM's moeten PowerShell-versie 2.0 of hoger hebben geïnstalleerd.
**Poorttoegang** | Op ESXi-hosts waarop VM's worden uitgevoerd die u wilt ontdekken, moet het Azure Migrate-toestel verbinding kunnen maken met TCP-poort 443.
**Limieten** | Voor app-detectie u maximaal 10000 VM's ontdekken op elk Azure Migrate-toestel.



## <a name="vmware-requirements"></a>VMware-vereisten

**Vmware** | **Details**
--- | ---
**Virtuele VMware-machines** | Beoordeling wordt ondersteund voor alle Windows- en Linux-besturingssystemen.
**vCenter Server** | Machines die u wilt detecteren en beoordelen, moeten worden beheerd door vCenter Server-versie 5.5, 6.0, 6.5 of 6.7.
**Machtigingen (beoordeling)** | vCenter Server alleen-lezen account.
**Machtigingen (app-detectie)** | vCenter Server-account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **virtuele machines > Gastbewerkingen**.
**Machtigingen (afhankelijkheidsvisualisatie)** | Center Server-account met alleen-lezen toegang en bevoegdheden ingeschakeld voor **Virtuele machines** > **Gastbewerkingen**.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure Migrate-toestel](migrate-appliance.md) voor detectie en beoordeling. U het toestel implementeren als VMWare VM Met behulp van een OVA-sjabloon, geïmporteerd in vCenter Server of met behulp van een [PowerShell-script.](deploy-appliance-script.md)

- Meer informatie over [toestelvereisten](migrate-appliance.md#appliance---vmware) voor VMware.
- Meer informatie over URL's die het toestel moet openen in [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
- In Azure Government moet u het toestel implementeren met behulp van het script.


## <a name="port-access"></a>Poorttoegang

**Apparaat** | **Verbinding**
--- | ---
Toestel | Binnenkomende verbindingen op TCP-poort 3389 om externe desktopverbindingen met het toestel mogelijk te maken.<br/><br/> Binnenkomende verbindingen op poort 44368 om op afstand toegang te krijgen tot de app voor toestelbeheer met behulp van de URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443 (HTTPS) om detectie- en prestatiemetagegevens naar Azure Migrate te verzenden.
vCenter-server | Binnenkomende verbindingen op TCP-poort 443 zodat het toestel configuratie- en prestatiemetagegevens kan verzamelen voor beoordelingen. <br/><br/> Het toestel maakt standaard verbinding met vCenter op poort 443. Als de vCenter-server op een andere poort luistert, u de poort wijzigen wanneer u detectie instelt.
ESXi-hosts (analyse van app-detectie/agentless afhankelijkheid) | Als u [app-detectie](how-to-discover-applications.md) of [agentless afhankelijkheidsanalyse](concepts-dependency-visualization.md#agentless-analysis)wilt uitvoeren, maakt het toestel verbinding met ESXi-hosts op TCP-poort 443, om toepassingen te ontdekken, om agentless afhankelijkheidsvisualisatie op VM's te gebruiken en uit te voeren.

## <a name="agentless-dependency-analysis-requirements"></a>Agentless afhankelijkheidsanalysevereisten

[Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u om afhankelijkheden te identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel geeft een overzicht van de vereisten voor het instellen van agentless afhankelijkheidsanalyse. 

**Vereiste** | **Details**
--- | --- 
**Vóór implementatie** | U moet een Azure Migrate-project hebben geïnstalleerd, met het hulpprogramma Serverbeoordeling dat aan het project is toegevoegd.<br/><br/>  U implementeert afhankelijkheidsvisualisatie nadat u een Azure Migrate-toestel hebt ingesteld om uw on-premises VMWare-machines te ontdekken.<br/><br/> [Meer informatie over het](create-manage-projects.md) maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een beoordelingstool aan een bestaand project.<br/> [Meer informatie over het](how-to-set-up-appliance-vmware.md) instellen van het Azure Migrate-toestel voor beoordeling van VMware VM's.
**VM-ondersteuning** | Momenteel alleen ondersteund voor VMware VM's.
**Virtuele Windows-machines** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bits).
**Windows-account** |  Voor afhankelijkheidsanalyse heeft het Azure Migrate-toestel een lokaal of een domeinbeheerdersaccount nodig om toegang te krijgen tot Windows VM's.
**Virtuele Linux-machines** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7.<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux-account** | Voor afhankelijkheidsanalyse heeft het Azure Migrate-toestel op Linux-machines een gebruikersaccount met rootbevoegdheid nodig.<br/><br/> Afwisselend heeft het gebruikersaccount deze machtigingen nodig op /bin/netstat- en /bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.
**Vereiste agenten** | Geen agent nodig op machines die u wilt analyseren.
**VMware-hulpprogramma's** | VMware Tools (later dan 10.2) moeten worden geïnstalleerd en worden uitgevoerd op elke vm die u wilt analyseren.
**vCenter Server-referenties** | Afhankelijkheidsvisualisatie heeft een vCenter Server-account nodig met alleen-lezen toegang en bevoegdheden die zijn ingeschakeld voor virtuele machines > Gastbewerkingen. 
**PowerShell** | VM's moeten PowerShell-versie 2.0 of hoger hebben geïnstalleerd.
**Poorttoegang** | Op ESXi-hosts waarop VM's worden uitgevoerd die u wilt analyseren, moet het Azure Migrate-toestel verbinding kunnen maken met TCP-poort 443.


## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheidsanalyse op basis van agent

[Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u om afhankelijkheden te identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel geeft een overzicht van de vereisten voor het instellen van afhankelijkheidsanalyse op basis van agent. 

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | U moet een Azure Migrate-project hebben, waarbij het azure migrate: serverassessment-hulpprogramma aan het project is toegevoegd.<br/><br/>  U implementeert afhankelijkheidsvisualisatie na het instellen van een Azure Migrate-toestel om uw on-premises machines te ontdekken<br/><br/> [Meer informatie over het](create-manage-projects.md) maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een beoordelingstool aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-toestel voor beoordeling van [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)of fysieke servers.
**Azure Government** | Afhankelijkheidsvisualisatie is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [Service Map-oplossing](../operations-management-suite/operations-management-suite-service-map.md) in [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) voor afhankelijkheidsvisualisatie.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werkruimte aan een Azure Migrate-project. De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werkruimte moet zich in de regio's Oost-VS, Zuidoost-Azië of West-Europa bevinden. Werkruimten in andere regio's kunnen niet aan een project worden gekoppeld.<br/><br/> De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics wordt de werkruimte die is gekoppeld aan Azure Migrate getagd met de toets Migratieproject en de projectnaam.
**Vereiste agenten** | Installeer op elke machine die u wilt analyseren de volgende agents:<br/><br/> De [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> De [afhankelijkheidsagent.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Als on-premises machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [afhankelijkheidsagent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-werkruimte** | De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werkruimten die zich bevinden in de regio's Oost-VS, Zuidoost-Azië en West-Europa.<br/><br/>  De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De oplossing Servicekaart brengt geen kosten met zich mee voor de eerste 180 dagen (vanaf de dag dat u de werkruimte Log Analytics koppelt aan het Azure Migrate-project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Als u een andere oplossing dan Service Map in de bijbehorende Werkruimte Log Analytics gebruikt, worden [standaardkosten](https://azure.microsoft.com/pricing/details/log-analytics/) voor Log Analytics in rekening gebracht.<br/><br/> Wanneer het Azure Migrate-project wordt verwijderd, wordt de werkruimte niet samen met het project verwijderd. Na het verwijderen van het project is het gebruik van servicekaarten niet gratis en wordt elk knooppunt in rekening gebracht volgens de betaalde laag loganalytics-werkruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt voordat Azure Algemene beschikbaarheid migreert (GA- 28 februari 2018), hebt u mogelijk extra servicekosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen wordt betaald, raden we u aan een nieuw project te maken, omdat bestaande werkruimten vóór GA nog steeds in rekening worden gebracht.
**Beheer** | Wanneer u agents registreert in de werkruimte, gebruikt u de id en de sleutel die worden geleverd door het Azure Migrate-project.<br/><br/> U de werkruimte Log Analytics buiten Azure Migreren gebruiken.<br/><br/> Als u het bijbehorende Azure Migrate-project verwijdert, wordt de werkruimte niet automatisch verwijderd. [Verwijder het handmatig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werkruimte die is gemaakt door Azure Migrate niet, tenzij u het Azure Migrate-project verwijdert. Als u dit doet, werkt de functionaliteit voor afhankelijkheidsvisualisatie niet zoals verwacht.
**Verbinding met internet** | Als machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop installeren.
**Azure Government** | Afhankelijkheidsanalyse op basis van agenten wordt niet ondersteund.


## <a name="next-steps"></a>Volgende stappen

- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van beoordelingen.
- [Bereid je voor op VMware-beoordeling.](tutorial-prepare-vmware.md)
