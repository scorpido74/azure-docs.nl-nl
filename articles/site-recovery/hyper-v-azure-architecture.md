---
title: Hyper-V-noodherstelarchitectuur in Azure Site Recovery
description: In dit artikel vindt u een overzicht van onderdelen en architectuur die worden gebruikt bij het implementeren van noodherstel voor on-premises Hyper-V VM's (zonder VMM) naar Azure met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082672"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architectuur van herstel na noodgevallen van Hyper-V naar Azure


In dit artikel worden de architectuur en processen beschreven die worden gebruikt wanneer u hyper-v-virtuele machines (VM's) nabootst, mislukt en herstelt tussen on-premises Hyper-V-hosts en Azure, met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)

Hyper-V-hosts kunnen optioneel worden beheerd in De Virtuele Machine Manager (VMM) van het Systeemcentrum.



## <a name="architectural-components---hyper-v-without-vmm"></a>Architecturale componenten - Hyper-V zonder VMM

De volgende tabel en afbeelding bieden een weergave op hoog niveau van de componenten die worden gebruikt voor Hyper-V-replicatie naar Azure, wanneer Hyper-V-hosts niet worden beheerd door VMM.

**Component** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, Azure-opslagaccount en Azure-netwerk. | Gerepliceerde gegevens van on-premises VM-workloads worden opgeslagen in het opslagaccount. Azure VM's worden gemaakt met de gerepliceerde werkbelastinggegevens wanneer failover van uw on-premises site plaatsvindt.<br/><br/> De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Hyper-V** | Tijdens de implementatie van Site Recovery verzamelt u Hyper-V-hosts en clusters in Hyper-V-sites. U installeert de azure site recovery provider en recovery services-agent op elke zelfstandige Hyper-V-host of op elk Hyper-V-clusterknooppunt. | De provider coördineert de replicatie met Site Recovery via internet. De Recovery Services-agent handelt de gegevensreplicatie af.<br/><br/> De communicatie vanuit zowel de Provider als de agent is beveiligd en versleuteld. De gerepliceerde gegevens in de Azure-opslag zijn eveneens versleuteld.
**Virtuele Hyper-V-machines** | Een of meer VM's draaien op Hyper-V. | Niets hoeft expliciet te worden geïnstalleerd op VM's.


**Hyper-V naar Azure-architectuur (zonder VMM)**

![Architectuur](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Architecturale componenten - Hyper-V met VMM

De volgende tabel en afbeelding bieden een weergave op hoog niveau van de componenten die worden gebruikt voor Hyper-V-replicatie naar Azure, wanneer Hyper-V-hosts worden beheerd in VMM-clouds.

**Component** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, Azure-opslagaccount en Azure-netwerk. | Gerepliceerde gegevens van on-premises VM-workloads worden opgeslagen in het opslagaccount. Azure VM's worden gemaakt met de gerepliceerde gegevens wanneer failover van uw on-premises site plaatsvindt.<br/><br/> De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**VMM-server** | De VMM-server heeft een of meer clouds met Hyper-V-hosts. | U installeert de Site Recovery Provider op de VMM-server, om replicatie te orkestreren met Site Recovery en registreert de server in de kluis Recovery Services.
**Hyper-V-host** | Een of meer Hyper-V-hosts/clusters worden beheerd door VMM. |  U installeert de agent Recovery Services op elke Hyper-V-host of clusterknooppunt.
**Virtuele Hyper-V-machines** | Er worden een of meer VM's uitgevoerd op een Hyper-V-hostserver. | U hoeft niets expliciet te installeren op virtuele machines.
**Networking** | Er zijn logische en VM-netwerken ingesteld op de VMM-server. Het VM-netwerk moet worden gekoppeld aan een logisch netwerk dat is gekoppeld aan de cloud. | VM-netwerken worden toegewezen aan virtuele Azure-netwerken. Wanneer Azure VM's worden gemaakt na een failover, worden ze toegevoegd aan het Azure-netwerk dat is toegewezen aan het VM-netwerk.

**Hyper-V naar Azure-architectuur (met VMM)**

![Onderdelen](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replicatieproces

![Hyper-V naar Azure-replicatie](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replicatie- en herstelproces**


### <a name="enable-protection"></a>Beveiliging inschakelen

1. Nadat u de beveiliging voor een Hyper-V-VM in Azure Portal of on-premises hebt ingeschakeld, start **Beveiliging inschakelen**.
2. Met deze taak wordt gecontroleerd of de machine voldoet aan de vereisten. Hierna wordt [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) aangeroepen om replicatie in te stellen op basis van de instellingen die u hebt geconfigureerd.
3. De taak start initiële replicatie door de methode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) aan te roepen om een volledige VM-replicatie te initialiseren en de virtuele schijven van de virtuele machine naar Azure te versturen.
4. U de taak controleren op het tabblad **Vacatures.**      ![Takenlijst](media/hyper-v-azure-architecture/image1.png) ![Beveiligingsoefening inschakelen](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Initiële gegevensreplicatie

1. Wanneer de eerste replicatie wordt geactiveerd, wordt een [Hyper-V VM-momentopname](https://technet.microsoft.com/library/dd560637.aspx) gemaakt.
2. Virtuele harde schijven op de VM worden één voor één gerepliceerd, totdat ze allemaal naar Azure worden gekopieerd. Dit kan even duren, afhankelijk van de VM-grootte en de netwerkbandbreedte. [Meer informatie over het](https://support.microsoft.com/kb/3056159) vergroten van de netwerkbandbreedte.
3. Als schijfwijzigingen optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen bij de Hyper-V Replica Replication Tracker bijhoudt als Hyper-V-replicatielogboeken (.hrl). Deze logboekbestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend .hrl-bestand dat naar secundaire opslag wordt verzonden. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Als de initiële replicatie is voltooid, wordt de VM-momentopname verwijderd.
5. Verschillen in het logboek worden gesynchroniseerd en samengevoegd op de bovenliggende schijf.


### <a name="finalize-protection-process"></a>Beveiligingsproces afronden

1. Nadat de eerste replicatie is voltooid, wordt de **beveiliging afronden op de taak van de virtuele machine** uitgevoerd. Het configureert netwerk- en andere instellingen voor postreplicatie, zodat de VM is beveiligd.
2. In dit stadium u de VM-instellingen controleren om te controleren of deze klaar is voor failover. U een noodherstelboor (testfailover) uitvoeren voor de VM, om te controleren of deze niet meer is zoals verwacht. 


## <a name="delta-replication"></a>Replicatie van verschillen

1. Na de eerste replicatie begint deltareplicatie, in overeenstemming met het replicatiebeleid.
2. De Hyper-V Replica Replication Tracker houdt wijzigingen in een virtuele harde schijf bij als .hrl-bestanden. Elke schijf die voor replicatie is geconfigureerd, heeft een bijbehorend .hrl-bestand.
3. Het logboek wordt verzonden naar het opslagaccount van de klant. Wanneer een logboek wordt doorgeleid naar Azure, worden de wijzigingen in de primaire schijf bijgehouden in een ander logboekbestand, in dezelfde map.
4. Tijdens de initiële en deltareplicatie u de VM in de Azure-portal controleren.

### <a name="resynchronization-process"></a>Opnieuw synchronisatieproces

1. Als de replicatie van verschillen mislukt en een volledige replicatie veel bandbreedte of tijd zou kosten, wordt de virtuele machine gemarkeerd voor hersynchronisatie.
    - Als de HRL-bestanden bijvoorbeeld 50% van de schijfgrootte in beslag nemen, wordt de virtuele machine gemarkeerd voor hersynchronisatie.
    -  Standaard wordt opnieuw synchronisatie automatisch buiten kantooruren uitgevoerd.
1.  Resynchronisatie verzendt alleen deltagegevens.
    - Het minimaliseert de hoeveelheid gegevens die wordt verzonden door computing checksums van de bron en doel VM's.
    - Het maakt gebruik van een vast blok chunking algoritme waar bron-en doelbestanden zijn verdeeld in vaste brokken.
    - Checksums voor elk stuk worden gegenereerd. Deze worden vergeleken om te bepalen welke blokken van de bron moeten worden toegepast op het doel.
2. Na hersynchronisatie moet de normale replicatie van verschillen worden hervat.
3. Als u niet wilt wachten op standaardsynchronisatie buiten de openingstijden, u een virtuele machine handmatig opnieuw synchroniseren. Bijvoorbeeld als er een storing optreedt. Selecteer hiervoor in de Azure-portal de VM-> **Opnieuw synchroniseren**.

    ![Handmatig opnieuw synchroniseren](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Proces opnieuw proberen

Als er een replicatiefout optreedt, wordt de replicatie automatisch opnieuw geprobeerd. Opnieuw proberen is ingedeeld zoals beschreven in de tabel.

**Categorie** | **Details**
--- | ---
**Niet-herstelbare fouten** | Er wordt geen nieuwe poging gedaan. De status van de virtuele machine is **Kritiek** en tussenkomst van de beheerder is vereist.<br/><br/> Voorbeelden van deze fouten zijn een kapotte VHD-keten, een ongeldige status voor de replica-VM, netwerkverificatiefouten, autorisatiefouten en vm-fouten (voor zelfstandige Hyper-V-servers.
**Herstelbare fouten** | Nieuwe pogingen worden elk replicatie-interval uitgevoerd met behulp van een exponentiële uitstelbewerking die het interval voor nieuwe pogingen vanaf het begin van de eerste poging met 1, 2, 4, 8 en 10 minuten verhoogt. Als een fout zich blijft voordoen, probeert u het om de 30 minuten opnieuw. Voorbeelden hiervan zijn netwerkfouten, fouten met lage schijfen en lage geheugenvoorwaarden.



## <a name="failover-and-failback-process"></a>Failover- en failbackproces

1. U kunt een geplande of niet-geplande failover uitvoeren van on-premises virtuele Hyper-V-machines naar Azure. Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen. Voer een ongeplande failover uit als uw primaire site niet toegankelijk is.
2. U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
3. Je loopt een failover. Nadat de eerste fase van failover is voltooid, moet u de gemaakte replica VM's in Azure kunnen zien. U kunt een openbaar IP-adres toewijzen aan de VM, indien nodig.
4. Vervolgens verbindt u de failover om toegang te krijgen tot de werkbelasting van de replica Azure VM.

Nadat uw on-premises infrastructuur weer operationeel is, u weer mislukken. Failback treedt op in drie fasen:

1. Start een geplande failover van Azure naar de on-premises site:
    - **Downtime minimaliseren:** als u deze optie gebruikt, synchroniseert Siteherstel gegevens voordat de failover mislukt. Het controleert op gewijzigde gegevensblokken en downloadt ze naar de on-premises site, terwijl de Azure VM blijft draaien, waardoor downtime wordt geminimaliseerd. Wanneer u handmatig opgeeft dat de failover moet worden voltooid, wordt de Azure VM afgesloten, worden eventuele definitieve deltawijzigingen gekopieerd en wordt de failover gestart.
    - **Volledige download**: Met deze optie worden gegevens gesynchroniseerd tijdens failover. Met deze optie wordt de hele schijf gedownload. Het is sneller omdat er geen checksums worden berekend, maar er is meer downtime. Gebruik deze optie als u de replica Azure VM's al enige tijd uitvoert of als de on-premises VM is verwijderd.
    - **VM maken:** u ervoor kiezen om niet terug te gaan naar dezelfde vm of naar een alternatieve vm. U opgeven dat Siteherstel de VM moet maken als deze nog niet bestaat.

2. Nadat de eerste synchronisatie is voltooid, selecteert u de failover te voltooien. Nadat deze is voltooid, u zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht. In de Azure-portal u zien dat de Azure VM's zijn gestopt.
3.  Vervolgens verbindt u de failover om te voltooien en krijgt u opnieuw toegang tot de werkbelasting van de on-premises VM.
4. Nadat workloads zijn mislukt, schakelt u reverse replication in, zodat de on-premises VM's opnieuw worden gerepliceerd naar Azure.



## <a name="next-steps"></a>Volgende stappen


Volg [deze zelfstudie](tutorial-prepare-azure.md) om aan de slag te gaan met Hyper-V naar Azure-replicatie.


