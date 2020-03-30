---
title: VMware disaster recovery met Azure Site Recovery
description: In dit artikel vindt u een overzicht van het herstel van VMware-vm's naar Azure met behulp van de Azure Site Recovery-service.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954395"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Over disaster recovery van VMware VM's naar Azure

In dit artikel vindt u een overzicht van noodherstel voor on-premises Vm's vMware naar Azure met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>Wat is BCDR?

Een BCDR-strategie (Business Continuity and Disaster Recovery) helpt uw bedrijf operationeel te houden. Tijdens geplande downtime en onverwachte uitval houdt BCDR gegevens veilig en beschikbaar en zorgt ervoor dat apps blijven draaien. Naast bcdr-functies voor platforms, zoals regionale koppeling en opslag met hoge beschikbaarheid, biedt Azure Herstelservices als integraal onderdeel van uw BCDR-oplossing. Hersteldiensten omvatten: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) maakt een back-up van uw on-premises en Azure VM-gegevens. U een back-up maken van een bestand en mappen, specifieke workloads of een hele virtuele machine. 
- [Azure Site Recovery](site-recovery-overview.md) biedt veerkracht en disaster recovery voor apps en workloads die worden uitgevoerd op on-premises machines of Azure IaaS VM's. Siteherstel orkestreert replicatie en verwerkt failover naar Azure wanneer er storingen optreden. Het behandelt ook het herstel van Azure naar uw primaire site. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hoe werkt Site Recovery bij herstel na noodgevallen?

1. Nadat u Azure en uw on-premises site hebt voorbereid, stelt u replicatie in en schakelt u replicatie in voor uw on-premises machines.
2. Site recovery orkestreert de eerste replicatie van de machine, in overeenstemming met uw beleidsinstellingen.
3. Na de eerste replicatie wordt deltawijzigingen in Azure gerepliceerd. 
4. Als alles volgens verwachting wordt herhaald, voert u een noodhersteloefening uit.
    - De boor helpt ervoor te zorgen dat failover zal werken zoals verwacht wanneer een echte behoefte zich voordoet.
    - De drill voert een testfailover uit zonder dat dit gevolgen heeft voor uw productieomgeving.
5. Als er een storing optreedt, voert u een volledige failover naar Azure uit. U falen boven één machine of u een herstelplan maken dat niet over meerdere machines tegelijk valt.
6. Bij failover worden Azure VM's gemaakt op basis van de VM-gegevens in beheerde schijven of opslagaccounts. Gebruikers kunnen toegang blijven krijgen tot apps en workloads via de Azure VM
7. Wanneer uw on-premises site weer beschikbaar is, mislukt u terug vanuit Azure.
8. Nadat u niet meer terugbent en vanaf uw primaire site werkt, begint u on-premises VM's opnieuw te repliceren naar Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hoe weet ik of mijn omgeving geschikt is voor noodherstel voor Azure?

Siteherstel kan elke werkbelasting repliceren die wordt uitgevoerd op een ondersteunde VMware VM of fysieke server. Hier zijn de dingen die je nodig hebt om te controleren in uw omgeving:

- Als u VMware VM's repliceert, voert u dan de juiste versies van VMware-virtualisatieservers uit? [Check hier](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Zijn de machines die u wilt repliceren met een ondersteund besturingssysteem? [Check hier](vmware-physical-azure-support-matrix.md#replicated-machines).
- Voor Linux ramp recovery, zijn machines met een ondersteund bestandssysteem / gast opslag? [Check hier](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Voldoen de machines die u wilt repliceren aan azure-vereisten? [Check hier](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Wordt uw netwerkconfiguratie ondersteund? [Check hier](vmware-physical-azure-support-matrix.md#network).
- Wordt uw opslagconfiguratie ondersteund? [Check hier](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Wat moet ik instellen in Azure voordat ik begin?

In Azure moet u het volgende voorbereiden:

1. Controleer of uw Azure-account machtigingen heeft om VM's te maken in Azure.
2. Maak een Azure-netwerk waar Azure VM's bij aansluiten wanneer ze zijn gemaakt met opslagaccounts of beheerde schijven na een failover.
3. Stel een Azure Recovery Services-kluis in voor siteherstel. De kluis bevindt zich in de Azure-portal en wordt gebruikt voor het implementeren, configureren, orkestreren, bewaken en oplossen van uw siteherstelimplementatie.

*Meer hulp nodig?*

Meer informatie over het instellen van Azure door [uw account te verifiëren,](tutorial-prepare-azure.md#verify-account-permissions)een [netwerk](tutorial-prepare-azure.md#set-up-an-azure-network)te maken en een kluis in [te stellen.](tutorial-prepare-azure.md#create-a-recovery-services-vault)



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Wat moet ik on-premises instellen voordat ik begin?

On-premises hier is wat je moet doen:

1. Je moet een paar accounts instellen:

    - Als u Vm's van VMware repliceert, is een account nodig om toegang te krijgen tot vCenter Server- of vSphere ESXi-hosts om vm's automatisch te detecteren.
    - Er is een account nodig om de serviceagent Site Recovery Mobility te installeren op elke fysieke machine of VM die u wilt repliceren.

2. U moet de compatibiliteit van uw VMware-infrastructuur controleren als u dat niet eerder hebt gedaan.
3. Controleer of u na een failover verbinding maken met Azure VM's. U stelt RDP in op on-premises Windows-machines of SSH op Linux-machines.

*Meer hulp nodig?*
- Maak accounts voor [automatische detectie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) en voor de installatie van [de Mobiliteitsservice.](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)
- [Controleer of](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) uw VMware-instellingen compatibel zijn.
- [Bereid u zo voor](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) dat u na een failover verbinding maakt in Azure.
- Als u meer diepgaande hulp wilt bij het instellen van IP-adressering voor Azure VM's na een failover, [leest u dit artikel](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Hoe stel ik herstel na noodgevallen in?

Nadat u uw Azure- en on-premises infrastructuur hebt geïnstalleerd, u herstel na noodgevallen instellen.

1. Als u wilt inzicht krijgen in de onderdelen die u moet implementeren, controleert u de [VMware naar Azure-architectuur](vmware-azure-architecture.md)en de [fysieke naar Azure-architectuur.](physical-azure-architecture.md) Er zijn een aantal componenten, dus het is belangrijk om te begrijpen hoe ze allemaal in elkaar passen.
2. **Bronomgeving:** Als eerste stap in implementatie stelt u uw replicatiebronomgeving in. U geeft op wat u wilt repliceren en waar u wilt repliceren.
3. **Configuratieserver:** u moet een configuratieserver instellen in uw on-premises bronomgeving:
    - De configuratieserver is één on-premises machine. Voor VMware-noodherstel raden we u aan deze te implementeren als een VMware-VM die kan worden geïmplementeerd vanuit een downloadbare OVF-sjabloon.
    - De configuratieserver coördineert de communicatie tussen on-premises en Azure
    - Een paar andere onderdelen draaien op de configuratieservermachine.
        - De processerver ontvangt, optimaliseert en verzendt replicatiegegevens naar cacheopslagaccount in Azure. Het verzorgt ook de automatische installatie van de Mobility-service op machines die u wilt repliceren en voert automatische detectie van VM's uit op VMware-servers.
        - Op de hoofddoelserver worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.
    - Het instellen omvat het registreren van de configuratieserver in de kluis, het downloaden van MySQL Server en VMware PowerCLI en het opgeven van de accounts die zijn gemaakt voor automatische detectie en de installatie van de Mobiliteitsservice.
4. **Doelomgeving:** u stelt uw doelAzure-omgeving in door uw Azure-abonnement en netwerkinstellingen op te geven.
5. **Replicatiebeleid:** u geeft op hoe replicatie moet plaatsvinden. Instellingen bevatten hoe vaak herstelpunten worden gemaakt en opgeslagen en of momentopnamen voor app-consistent moeten worden gemaakt.
6. **Replicatie inschakelen**. U schakelt replicatie in voor on-premises machines. Als u een account hebt aangemaakt om de Mobiliteitsservice te installeren, wordt deze geïnstalleerd wanneer u replicatie voor een machine inschakelt. 

*Meer hulp nodig?*

- Voor een snelle walkthrough van deze stappen, u proberen onze [VMware tutorial,](vmware-azure-tutorial.md)en [fysieke server walkthrough](physical-azure-disaster-recovery.md).
- [Meer informatie](vmware-azure-set-up-source.md) over het instellen van uw bronomgeving.
- [Meer informatie over](vmware-azure-deploy-configuration-server.md) configuratieserververeisten en het instellen van de configuratieserver met een OVF-sjabloon voor VMware-replicatie. Als u om de een of andere reden geen sjabloon gebruiken of als u fysieke servers repliceert, [gebruikt u deze instructies](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Meer informatie](vmware-azure-set-up-target.md) over doelinstellingen.
- [Meer informatie](vmware-azure-set-up-replication.md) over het instellen van een replicatiebeleid.
- [Meer informatie over](vmware-azure-enable-replication.md) het inschakelen van replicatie en [het uitsluiten](vmware-azure-exclude-disk.md) van replicatie door schijven.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Er is iets misgegaan, hoe los ik problemen op?

- Probeer als eerste stap [uw implementatie](site-recovery-monitor-and-troubleshoot.md) te controleren om de status van gerepliceerde items, taken en infrastructuurproblemen te verifiëren en eventuele fouten te identificeren.
- Als u de eerste replicatie niet voltooien of als de lopende replicatie niet werkt zoals verwacht, [controleert u dit artikel](vmware-azure-troubleshoot-replication.md) op veelvoorkomende fouten en tips voor het oplossen van problemen.
- Als u problemen ondervindt met de automatische installatie van de Mobiliteitsservice op machines die u wilt repliceren, controleert u veelvoorkomende fouten in [dit artikel.](vmware-azure-troubleshoot-push-install.md)
- Als failover niet werkt zoals verwacht, controleert u veelvoorkomende fouten in [dit artikel](site-recovery-failover-to-azure-troubleshoot.md).
- Als failback niet werkt, controleert u of uw probleem wordt weergegeven in [dit artikel.](vmware-azure-troubleshoot-failback-reprotect.md)



## <a name="next-steps"></a>Volgende stappen

Nu replicatie is uitgevoerd, moet u [een noodherstelboor uitvoeren](tutorial-dr-drill-azure.md) om ervoor te zorgen dat failover werkt zoals verwacht. 
