---
title: VMware/fysiek herstel van rampen schalen met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel op Azure voor grote aantallen on-premises VMware VM's of fysieke servers met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409774"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Noodherstel op schaal instellen voor VMware VM's/fysieke servers

In dit artikel wordt beschreven hoe u disaster recovery instelt op Azure voor grote aantallen (> 1000) van on-premises VMware VM's of fysieke servers in uw productieomgeving, met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Definieer uw BCDR-strategie

Als onderdeel van uw BCDR-strategie (Business Continuity and Disaster Recovery) definieert u herstelpuntendoelstellingen (RTO's) en hersteltijddoelstellingen (RTO's) voor uw bedrijfsapps en workloads. RTO meet de duur van tijd en serviceniveau waarbinnen een bedrijfsapp of -proces moet worden hersteld en beschikbaar moet zijn, om continuïteitsproblemen te voorkomen.
- Site Recovery biedt continue replicatie voor VMware VM's en fysieke servers, en een [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) voor RTO.
- Terwijl u van plan bent voor grootschalige noodherstel voor VMware-VM's en de Azure-resources die u nodig hebt, te achterhalen, u een RTO-waarde opgeven die wordt gebruikt voor capaciteitsberekeningen.


## <a name="best-practices"></a>Aanbevolen procedures

Enkele algemene best practices voor grootschalige disaster recovery. Deze aanbevolen procedures worden in de volgende delen van het document nader besproken.

- **Doelvereisten identificeren:** schat de capaciteit en de resourcebehoeften in Azure voordat u herstel na noodgevallen instelt.
- **Plan voor siteherstelcomponenten:** zoek uit welke siteherstelcomponenten (configuratieserver, processervers) u nodig hebt om aan uw geschatte capaciteit te voldoen.
- **Een of meer scale-outprocesservers instellen:** gebruik niet de processerver die standaard op de configuratieserver wordt uitgevoerd. 
- **De nieuwste updates uitvoeren:** het Team Siteherstel brengt regelmatig nieuwe versies van Site Recovery-onderdelen uit en u moet ervoor zorgen dat u de nieuwste versies uitvoert. Om u daarbij te helpen, houdt u bij [wat er nieuw is](site-recovery-whats-new.md) voor updates en schakelt u updates in en [installeert](service-updates-how-to.md) deze tijdens de release.
- **Monitor proactief:** Als u noodherstel operationeel krijgt, moet u proactief de status en status van gerepliceerde machines en infrastructuurbronnen controleren.
- **Disaster recovery drills**: U moet regelmatig noodhersteloefeningen uitvoeren. Deze hebben geen invloed op uw productieomgeving, maar zorgen er wel voor dat failover naar Azure werkt zoals verwacht wanneer dat nodig is.



## <a name="gather-capacity-planning-information"></a>Informatie over capaciteitsplanning verzamelen

Verzamel informatie over uw on-premises omgeving om uw doelcapaciteitsbehoeften (Azure) te beoordelen en te schatten.
- Voer voor VMware de Implementatieplanner voor VMware VM's uit om dit te doen.
- Voor fysieke servers verzamelt u de informatie handmatig.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>De implementatieplanner voor VMware VM's uitvoeren

De Implementatieplanner helpt u om informatie te verzamelen over uw On-premises VMware-omgeving.

- Voer de implementatieplanner uit gedurende een periode die typische churn voor uw VM's vertegenwoordigt. Dit zal leiden tot nauwkeurigere schattingen en aanbevelingen.
- We raden u aan de Implementatieplanner op de configuratieservermachine uit te voeren, omdat de Planner de doorvoer berekent vanaf de server waarop deze wordt uitgevoerd. [Meer informatie](site-recovery-vmware-deployment-planner-run.md#get-throughput) over het meten van doorvoer.
- Als u nog geen configuratieserver hebt ingesteld:
    - [Krijg een overzicht](vmware-physical-azure-config-process-server-overview.md) van siteherstelcomponenten.
    - [Stel een configuratieserver in](vmware-azure-deploy-configuration-server.md)om de implementatieplanner erop uit te voeren.

Voer vervolgens de Planner als volgt uit:

1. [Meer informatie over](site-recovery-deployment-planner.md) de implementatieplanner. U de nieuwste versie downloaden van de portal, of [direct downloaden.](https://aka.ms/asr-deployment-planner)
2. Bekijk de [vereisten](site-recovery-deployment-planner.md#prerequisites) en [de nieuwste updates](site-recovery-deployment-planner-history.md) voor de Implementatieplanner en download en [haal](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) de tool.
3. [Voer de implementatieplanner](site-recovery-vmware-deployment-planner-run.md) uit op de configuratieserver.
4. [Een rapport genereren](site-recovery-vmware-deployment-planner-run.md#generate-report) om schattingen en aanbevelingen samen te vatten.
5. Analyseer de [rapportaanbevelingen](site-recovery-vmware-deployment-planner-analyze-report.md) en [kostenschattingen](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Standaard is het hulpprogramma geconfigureerd om te profileren en genereert het rapport voor maximaal 1000 VM's. U deze limiet wijzigen door de maximale waarde van MaxVMsSupported in het asrDeploymentPlanner.exe.config-bestand te verhogen.

## <a name="plan-target-azure-requirements-and-capacity"></a>Azure-vereisten en -capaciteit plannen

Met behulp van uw verzamelde schattingen en aanbevelingen u plannen voor doelresources en -capaciteit. Als u de Implementatieplanner voor VMware VM's hebt uitgevoerd, u een aantal [aanbevelingen](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) voor het rapport gebruiken om u te helpen.

- **Compatibele VM's:** gebruik dit nummer om het aantal VM's te identificeren dat klaar is voor noodherstel in Azure. Aanbevelingen over netwerkbandbreedte en Azure-cores zijn gebaseerd op dit nummer.
- **Vereiste netwerkbandbreedte:** let op de bandbreedte die u nodig hebt voor deltareplicatie van compatibele VM's. 
    - Wanneer u de Planner uitvoert, geeft u binnen enkele minuten de gewenste RPO op. De aanbevelingen tonen u de bandbreedte die nodig is om te voldoen aan die RPO 100% en 90% van de tijd. 
    - De aanbevelingen voor netwerkbandbreedte houden rekening met de bandbreedte die nodig is voor het totale aantal configuratieservers en processervers dat in de Planner wordt aanbevolen.
- **Vereiste Azure-cores:** noteer het aantal cores dat u nodig hebt in de doelregio Azure, op basis van het aantal compatibele VM's. Als u niet genoeg cores hebt, kan bij failover Site Recovery niet de vereiste Azure VM's maken.
- **Aanbevolen VM-batchgrootte**: De aanbevolen batchgrootte is gebaseerd op de mogelijkheid om de eerste replicatie voor de batch standaard binnen 72 uur te voltooien, terwijl u voldoet aan een RPO van 100%. De uurwaarde kan worden gewijzigd.

U deze aanbevelingen gebruiken om te plannen voor Azure-resources, netwerkbandbreedte en VM-batching.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure-abonnementen en -quota plannen

We willen ervoor zorgen dat de beschikbare quota in het doelabonnement voldoende zijn om failover te verwerken.

**Taak** | **Details** | **Actie**
--- | --- | ---
**Bekijk de kernen** | Als kernen in het beschikbare quotum niet gelijk zijn aan of hoger zijn dan het totale aantal doelwaarden op het moment van failover, mislukken mislukken. | Controleer voor VMware VM's of u voldoende cores in het doelabonnement hebt om te voldoen aan de core-aanbeveling van Deployment Planner.<br/><br/> Controleer bij fysieke servers of Azure-kernen voldoen aan uw handmatige schattingen.<br/><br/> Als u quota wilt controleren, klikt u in de Azure-portal **>-abonnement**op **Gebruik + quota**.<br/><br/> [Meer informatie](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) over het verhogen van quota.
**Failoverlimieten controleren** | Het aantal failovers mag niet hoger zijn dan de failoverlimieten voor siteherstel. |  Als failovers de limieten overschrijden, u abonnementen toevoegen en niet overgaan naar meerdere abonnementen of het quotum voor een abonnement verhogen. 


### <a name="failover-limits"></a>Failoverlimieten

De limieten geven het aantal failovers aan dat binnen een uur door Site Recovery wordt ondersteund, uitgaande van drie schijven per machine.

Wat betekent voldoen? Als u een Azure VM wilt starten, moet voor Azure een aantal stuurprogramma's worden gestart en moeten services zoals DHCP automatisch worden gestart.
- Machines die hieraan voldoen, hebben deze instellingen al.
- Voor machines met Windows u proactief controleren of u voldoet en deze waar nodig compliant maken. [Meer informatie](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux machines worden pas in overeenstemming gebracht op het moment van failover.

**Voldoet de machine aan Azure?** | **Azure VM-limieten (failover beheerde schijf)**
--- | --- 
Ja | 2000
Nee | 1000

- Limieten gaan ervan uit dat er minimale andere taken worden uitgevoerd in de doelregio voor het abonnement.
- Sommige Azure-regio's zijn kleiner en hebben mogelijk iets lagere limieten.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Infrastructuur en VM-connectiviteit plannen

Na een fail-over naar Azure hebt u uw workloads nodig om te werken zoals ze on-premises deden en om gebruikers toegang te geven tot workloads die worden uitgevoerd op de Azure VM's.

- [Meer informatie](site-recovery-active-directory.md#test-failover-considerations) over het niet slagen via uw Active Directory- of DNS-on-premises infrastructuur voor Azure.
- [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) over het voorbereiden van verbinding met Azure VM's na een failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Plannen voor broncapaciteit en -vereisten

Het is belangrijk dat u over voldoende configuratieservers en scale-out processervers beschikt om aan de capaciteitsvereisten te voldoen. Als u begint met uw grootschalige implementatie, begint u met één configuratieserver en één scale-outprocesserver. Als u de voorgeschreven limieten bereikt, voegt u extra servers toe.

>[!NOTE]
> Voor VMware VM's doet de Deployment Planner enkele aanbevelingen over de configuratie- en processervers die u nodig hebt. We raden u aan de tabellen in de volgende procedures te gebruiken in plaats van de aanbeveling implementatieplanner te volgen. 


## <a name="set-up-a-configuration-server"></a>Een configuratieserver instellen
 
De capaciteit van de configuratieserver wordt beïnvloed door het aantal machines dat wordt gerepliceerd en niet door de gegevensverloopsnelheid. Gebruik deze gedefinieerde VM-limieten om erachter te komen of u extra configuratieservers nodig hebt.

**Cpu** | **Geheugen** | **Cacheschijf** | **Gerepliceerde machinelimiet**
 --- | --- | --- | ---
8 vCPU's<br> 2 sockets * 4 cores @ 2,5 Ghz | 16 GB | 600 GB | Maximaal 550 machines<br> Gaat ervan uit dat elke machine drie schijven van elk 100 GB heeft.

- Deze limieten zijn gebaseerd op een configuratieserver die is ingesteld met behulp van een OVF-sjabloon.
- De limieten gaan ervan uit dat u de processerver die standaard op de configuratieserver wordt uitgevoerd, niet gebruikt.

Als u een nieuwe configuratieserver wilt toevoegen, volgt u de volgende instructies:

- [Stel een configuratieserver in](vmware-azure-deploy-configuration-server.md) voor VMware VM-noodherstel met behulp van een OVF-sjabloon.
- [Stel handmatig een configuratieserver in](physical-azure-set-up-source.md) voor fysieke servers of voor VMware-implementaties die geen OVF-sjabloon kunnen gebruiken.

Houd er rekening mee dat u bij het instellen van een configuratieserver het als:

- Wanneer u een configuratieserver instelt, is het belangrijk om rekening te houden met het abonnement en de kluis waarbinnen deze zich bevindt, omdat deze niet na de installatie moeten worden gewijzigd. Als u de kluis moet wijzigen, moet u de configuratieserver loskoppelen van de kluis en deze opnieuw registreren. Hiermee wordt de replicatie van VM's in de kluis gestopt.
- Als u een configuratieserver met meerdere netwerkadapters wilt instellen, moet u dit doen tijdens het instellen. U dit niet doen nadat u de configuratieserver in de kluis hebt geregistreerd.

## <a name="set-up-a-process-server"></a>Een processerver instellen

De capaciteit van de processerver wordt beïnvloed door de gegevensverloopsnelheden en niet door het aantal machines dat is ingeschakeld voor replicatie.

- Voor grote implementaties moet u altijd ten minste één scale-out processerver hebben.
- Gebruik de volgende tabel om erachter te komen of u extra servers nodig hebt.
- We raden u aan een server met de hoogste specificaties toe te voegen. 


**Cpu** | **Geheugen** | **Cacheschijf** | **Churn rate**
 --- | --- | --- | --- 
12 vCPU's<br> 2 sockets*6 cores @ 2,5 Ghz | 24 GB | 1 GB | Tot 2 TB per dag

Stel de processerver als volgt in:

1. Bekijk de [voorwaarden](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installeer de server in de [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)of vanaf de [opdrachtregel](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configureer gerepliceerde machines om de nieuwe server te gebruiken. Als u al machines hebt die repliceren:
    - U een volledige processerverworkload [verplaatsen](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) naar de nieuwe processerver.
    - U ook specifieke VM's naar de nieuwe processerver [verplaatsen.](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)



## <a name="enable-large-scale-replication"></a>Grootschalige replicatie inschakelen

Na het plannen van capaciteit en het implementeren van de vereiste componenten en infrastructuur, u replicatie inschakelen voor grote aantallen VM's.

1. Sorteer machines in batches. U schakelt replicatie voor VM's binnen een batch in en gaat vervolgens door naar de volgende batch.

    - Voor VMware VM's u de [aanbevolen VM-batchgrootte](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) gebruiken in het rapport Deployment Planner.
    - Voor fysieke machines raden we u aan batches te identificeren op basis van machines met een vergelijkbare grootte en hoeveelheid gegevens en op de beschikbare netwerkdoorvoer. Het doel is om batch machines die waarschijnlijk hun eerste replicatie te voltooien in ongeveer dezelfde hoeveelheid tijd.
    
2. Als het schijfverloop voor een machine hoog is of de limieten overschrijdt in Deployment thePlanner, u niet-kritieke bestanden die u niet hoeft te repliceren (zoals logboekdumps of tijdelijke bestanden) van de machine verplaatsen. Voor VMware VM's u deze bestanden naar een afzonderlijke schijf verplaatsen en [deze schijf](vmware-azure-exclude-disk.md) vervolgens uitsluiten van replicatie.
3. Controleer voordat u replicatie inschakelt of machines voldoen aan [de replicatievereisten](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Een replicatiebeleid configureren voor Vm's of [fysieke servers](physical-azure-disaster-recovery.md#create-a-replication-policy)van [VMware.](vmware-azure-set-up-replication.md#create-a-policy)
5. Replicatie inschakelen voor [VMware VM's](vmware-azure-enable-replication.md) of [fysieke servers](physical-azure-disaster-recovery.md#enable-replication). Hiermee wordt de eerste replicatie voor de geselecteerde machines afgetrapt.

## <a name="monitor-your-deployment"></a>Uw implementatie bewaken

Nadat u de replicatie voor de eerste batch VM's hebt gestart, begint u als volgt uw implementatie te controleren:  

1. Wijs een beheerder voor noodherstel toe om de status van gerepliceerde machines te controleren.
2. [Monitor gebeurtenissen](site-recovery-monitor-and-troubleshoot.md) voor gerepliceerde items en de infrastructuur.
3. [Controleer de status](vmware-physical-azure-monitor-process-server.md) van uw scale-out processervers.
4. Meld u aan om [e-mailmeldingen](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) voor gebeurtenissen te ontvangen, voor eenvoudigere monitoring.
5. Voer regelmatig [noodhersteloefeningen](site-recovery-test-failover-to-azure.md)uit om ervoor te zorgen dat alles werkt zoals verwacht.


## <a name="plan-for-large-scale-failovers"></a>Plan voor grootschalige failovers

In geval van een calamiteit moet u mogelijk een groot aantal machines/workloads naar Azure laten mislukken. Bereid je als volgt voor op dit soort gebeurtenissen.

U zich van tevoren als volgt voorbereiden op failover:

- [Bereid uw infrastructuur en VM's](#plan-infrastructure-and-vm-connectivity) zo voor dat uw workloads beschikbaar zijn na failover, zodat gebruikers toegang hebben tot de Azure VM's.
- Let op de [failoverlimieten](#failover-limits) eerder in dit document. Zorg ervoor dat uw failovers binnen deze grenzen vallen.
- Voer regelmatig [noodhersteloefeningen uit.](site-recovery-test-failover-to-azure.md) Boren helpen bij:
    - Zoek hiaten in uw implementatie voordat u failover.
    - Schat end-to-end RTO voor uw apps.
    - Schat end-to-end RPO in voor uw workloads.
    - Identificeer conflicten in het IP-adresbereik.
    - Terwijl u oefeningen uitvoert, raden we u aan geen productienetwerken te gebruiken voor oefeningen, te voorkomen dat u dezelfde subnetnamen gebruikt in productie- en testnetwerken en na elke oefening testfailovers op te ruimen.

Als u een grootschalige failover wilt uitvoeren, raden we het volgende aan:

1. Herstelplannen maken voor failover van werkbelasting.
    - Elk herstelplan kan leiden tot failover van maximaal 100 machines.
    - [Meer informatie](recovery-plan-overview.md) over herstelplannen.
2. Voeg Azure Automation runbook-scripts toe aan herstelplannen om handmatige taken op Azure te automatiseren. Typische taken zijn het configureren van load balancers, het updaten van DNS etc. [Meer informatie](site-recovery-runbook-automation.md)
2. Bereid Windows-machines voor de fail-over zodat ze voldoen aan de Azure-omgeving. [Failoverlimieten](#plan-azure-subscriptions-and-quotas) zijn hoger voor machines die voldoen. [Meer informatie](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) over runbooks.
4.  Trigger failover met de [Cmdlet Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell, samen met een herstelplan.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Site Recovery bewaken](site-recovery-monitor-and-troubleshoot.md)
