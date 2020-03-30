---
title: Disaster recovery instellen voor SQL Server met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u noodherstel voor SQL Server instelt met SQL Server en Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084744"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Disaster recovery instellen voor SQL Server

In dit artikel wordt beschreven hoe u de SQL Server back-end van een toepassing beschermen. U doet dit door gebruik te maken van een combinatie van BCDR-technologieën (Business Continuity and Disaster Recovery) van SQL Server en [Azure Site Recovery.](site-recovery-overview.md)

Controleer voordat u begint de sql server-noodherstelmogelijkheden. Deze mogelijkheden omvatten:

* Failoverclustering
* Beschikbaarheidsgroepen altijd in gebruik
* Databasespiegeling
* Back-upfunctie voor logboekbestanden
* Actieve geo-replicatie
* Automatische failover-groepen

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR-technologieën combineren met siteherstel

Uw keuze voor een BCDR-technologie om SQL Server-exemplaren te herstellen moet gebaseerd zijn op uw hersteltijddoelstelling (RTO) en de rpo-behoeften (recovery point objective), zoals beschreven in de volgende tabel. Combineer Site Recovery met de failover-bewerking van de door u gekozen technologie om het herstel van uw hele toepassing te orkestreren.

Implementatietype | BCDR-technologie | Verwachte RTO voor SQL Server | Verwachte RPO voor SQL Server |
--- | --- | --- | ---
SQL Server op een Azure-infrastructuur as a service (IaaS) virtuele machine (VM) of on-premises.| [AlwaysOn-beschikbaarheidsgroep](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | De tijd die nodig is om de secundaire replica als primaire te maken. | Omdat replicatie naar de secundaire replica asynchroon is, is er wat gegevensverlies.
SQL Server op een Azure IaaS VM of on-premises.| [Failoverclustering (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | De tijd die nodig is om te mislukken tussen de knooppunten. | Omdat Always On FCI gedeelde opslag gebruikt, is dezelfde weergave van de opslaginstantie beschikbaar bij failover.
SQL Server op een Azure IaaS VM of on-premises.| [Databasemirroring (high-performance modus)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | De tijd die nodig is om de service te forceren, die de mirror server gebruikt als een warme stand-by server. | Replicatie is asynchroon. De spiegeldatabase kan enigszins achterblijven bij de hoofddatabase. De vertraging is meestal klein. Maar het kan groot worden als het systeem van de hoofd- of spiegelserver onder zware belasting staat.<br/><br/>Logboekverzending kan een aanvulling zijn op databasemirroring. Het is een gunstig alternatief voor asynchrone database mirroring.
SQL as platform as a service (PaaS) op Azure.<br/><br/>Dit implementatietype bevat elastische pools en Azure SQL Database-servers. | Actieve geo-replicatie | 30 seconden nadat de failover is geactiveerd.<br/><br/>Wanneer failover wordt geactiveerd voor een van de secundaire databases, worden alle andere secondaries automatisch gekoppeld aan de nieuwe primaire. | RPO van vijf seconden.<br/><br/>Actieve georeplicatie maakt gebruik van de Always On-technologie van SQL Server. Hiermee worden vastgelegde transacties in de primaire database asynchroon gerepliceerd naar een secundaire database met behulp van momentopnameisolatie.<br/><br/>De secundaire gegevens zullen gegarandeerd nooit gedeeltelijke transacties hebben.
SQL zoals PaaS geconfigureerd met actieve georeplicatie op Azure.<br/><br/>Dit implementatietype bevat een SQL Database-beheerde instantie, elastische pools en SQL Database-servers. | Automatische failover-groepen | RTO van een uur. | RPO van vijf seconden.<br/><br/>Auto-failovergroepen bieden de groep semantiek bovenop actieve georeplicatie. Maar hetzelfde asynchrone replicatiemechanisme wordt gebruikt.
SQL Server op een Azure IaaS VM of on-premises.| Replicatie met Azure-siteherstel | RTO is meestal minder dan 15 minuten. Lees voor meer informatie de [RTO SLA van Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Een uur voor consistentie van de toepassing en vijf minuten voor crashconsistentie. Als u op zoek bent naar lagere RPO, gebruik dan andere BCDR-technologieën.

> [!NOTE]
> Een paar belangrijke overwegingen wanneer u SQL-workloads helpt beschermen met Site Recovery:
> * Site recovery is applicatie agnostisch. Siteherstel kan helpen bij het beschermen van elke versie van SQL Server die is geïmplementeerd op een ondersteund besturingssysteem. Zie voor meer informatie de [ondersteuningsmatrix voor herstel](vmware-physical-azure-support-matrix.md#replicated-machines) van gerepliceerde machines.
> * U ervoor kiezen om Site Recovery te gebruiken voor elke implementatie bij Azure, Hyper-V, VMware of fysieke infrastructuur. Volg de richtlijnen aan het einde van dit artikel over [hoe u een SQL Server-cluster beschermen](#how-to-help-protect-a-sql-server-cluster) met Site Recovery.
> * Controleer of de gegevenswijzigingssnelheid die op de machine wordt waargenomen binnen de [limieten voor siteherstel](vmware-physical-azure-support-matrix.md#churn-limits)valt. De wijzigingssnelheid wordt gemeten in schrijfbytes per seconde. Voor machines met Windows u deze wijzigingssnelheid weergeven door het tabblad **Prestaties** in Taakbeheer te selecteren. Let op de schrijfsnelheid voor elke schijf.
> * Siteherstel ondersteunt replicatie van failoverclusterexemplaren op Direct opgeslagen ruimten. Zie voor meer informatie hoe u [replicatie van Opslagruimten Direct inschakelt](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Herstel na noodgevallen van een toepassing

Site Recovery orkestreert de failover en de failover van uw hele toepassing met behulp van herstelplannen.

Er zijn enkele voorwaarden om ervoor te zorgen dat uw herstelplan volledig is aangepast aan uw behoefte. Elke SQL Server-implementatie heeft doorgaans een Active Directory-implementatie nodig. Het heeft ook connectiviteit nodig voor uw toepassingslaag.

### <a name="step-1-set-up-active-directory"></a>Stap 1: Active Directory instellen

Active Directory instellen in de secundaire herstelsite om SQL Server correct uit te voeren.

* **Kleine onderneming**: U hebt een klein aantal toepassingen en één domeincontroller voor de on-premises site. Als u de hele site wilt mislukken, gebruikt u replicatie siteherstel. Met deze service wordt de domeincontroller gerepliceerd naar het secundaire datacenter of naar Azure.
* **Middelgrote tot grote onderneming:** mogelijk moet u extra domeincontrollers instellen.
  - Als u een groot aantal toepassingen hebt, een Active Directory-forest hebt en wilt mislukken op toepassing of werkbelasting, stelt u een andere domeincontroller in in het secundaire datacenter of in Azure.
  -  Als u beschikbaarheidsgroepen Always On gebruikt om te herstellen naar een externe site, stelt u een andere domeincontroller in op de secundaire site of in Azure. Deze domeincontroller wordt gebruikt voor het herstelde SQL Server-exemplaar.

De instructies in dit artikel gaan ervan uit dat een domeincontroller beschikbaar is op de secundaire locatie. Zie voor meer informatie de procedures voor [het beveiligen van Active Directory met Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Stap 2: Zorgen voor connectiviteit met andere lagen

Nadat de databaselaag is uitgevoerd in het doelazure-gebied, moet u ervoor zorgen dat u verbinding hebt met de toepassing en weblagen. Neem vooraf de nodige stappen om de connectiviteit met testfailover te valideren.

Zie de volgende voorbeelden om te begrijpen hoe u toepassingen ontwerpen voor connectiviteitsoverwegingen:

* [Een toepassing ontwerpen voor herstel van cloudrampen](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategieën voor elastische pool Disaster Recovery](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Stap 3: Samenwerken met Always On, actieve georeplicatie en automatische failovergroepen

BCDR-technologieën Always On, actieve georeplicatie- en auto-failovergroepen hebben secundaire replica's van SQL Server die worden uitgevoerd in het doelazure-gebied. De eerste stap voor de failover van uw toepassing is het opgeven van deze replica als primair. In deze stap wordt ervan uitgegaan dat u al een domeincontroller in het secundaire hebt. De stap is mogelijk niet nodig als u ervoor kiest om een auto-failover te doen. Fail over uw web-en toepassingslagen pas nadat de database failover is voltooid.

> [!NOTE]
> Als u hebt geholpen om de SQL-machines te beschermen met Site Recovery, hoeft u alleen maar een herstelgroep van deze machines te maken en hun failover toe te voegen aan het herstelplan.

[Maak een herstelplan](site-recovery-create-recovery-plans.md) met virtuele machines op de website en op het webniveau. In de volgende stappen wordt uitgelegd hoe u failover van de databaselaag toevoegt:

1. Importeer de scripts om te mislukken boven SQL Availability Group in zowel een [Virtuele Resource Manager-machine](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) als een [klassieke virtuele machine.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) Importeer de scripts in uw Azure Automation-account.

    [![Afbeelding van een logo 'Implementeren naar Azure'](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg het ASR-SQL-FailoverAG-script toe als een vooractie van de eerste groep van het herstelplan.

1. Volg de instructies die beschikbaar zijn in het script om een automatiseringsvariabele te maken. Deze variabele geeft de naam van de beschikbaarheidsgroepen.

### <a name="step-4-conduct-a-test-failover"></a>Stap 4: Voer een test failover

Sommige BCDR-technologieën zoals SQL Always On ondersteunen niet native testfailover. Wij raden de volgende aanpak *alleen aan bij het gebruik van dergelijke technologieën.*

1. Azure [Backup](../backup/backup-azure-arm-vms.md) instellen op de VM waarmee de replica van de beschikbaarheidsgroep in Azure wordt host.

1. Voordat de testfailover van het herstelplan wordt geactiveerd, herstelt u de VM van de back-up die in de vorige stap is gemaakt.

    ![Schermafbeelding van venster voor het herstellen van een configuratie vanuit Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Forceer een quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) in de VM dat is hersteld vanuit back-up.

1. Werk het IP-adres van de listener bij om een adres te zijn dat beschikbaar is in het failovernetwerk van de test.

    ![Schermafbeelding van het dialoogvenster regelsvenster en IP-adreseigenschappen](./media/site-recovery-sql/update-listener-ip.png)

1. Breng de luisteraar online.

    ![Schermafbeelding van venster met label Content_AG met servernamen en -statussen](./media/site-recovery-sql/bring-listener-online.png)

1. Zorg ervoor dat de load balancer in het failovernetwerk één IP-adres heeft, van de front-end IP-adresgroep die overeenkomt met elke listener van de beschikbaarheidsgroep en met de SQL Server VM in de back-endgroep.

     ![Schermafbeelding van venster met de titel "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Schermafbeelding van venster met de titel "SQL-AlwaysOn-LB - Backend IP-pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Voeg in latere herstelgroepen failover van uw toepassingslaag toe, gevolgd door uw weblaag voor dit herstelplan.

1. Doe een testfailover van het herstelplan om end-to-end failover van uw toepassing te testen.

## <a name="steps-to-do-a-failover"></a>Stappen om een failover te doen

Nadat u het script in stap 3 hebt toegevoegd en gevalideerd in stap 4, u een failover uitvoeren van het herstelplan dat is gemaakt in stap 3.

De failoverstappen voor toepassings- en weblagen moeten hetzelfde zijn in zowel herstelplannen voor testfouten als failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Een SQL Server-cluster beveiligen

Voor een cluster met SQL Server Standard-editie of SQL Server 2008 R2 raden we u aan siteherstelreplicatie te gebruiken om SQL Server te beschermen.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure naar Azure en on-premises naar Azure

Siteherstel biedt geen ondersteuning voor gastclusterbij het repliceren naar een Azure-gebied. SQL Server Standard-editie biedt ook geen goedkope oplossing voor noodherstel. In dit scenario raden we u aan het SQL Server-cluster te beschermen tegen een zelfstandige SQL Server-instantie op de primaire locatie en het te herstellen in het secundaire.

1. Configureer een extra zelfstandige SQL Server-instantie op de primaire Azure-regio of op on-premises site.

1. Configureer de instantie om te dienen als een spiegel voor de databases die u wilt helpen beschermen. Mirroring configureren in de modus met hoge veiligheid.

1. Siteherstel configureren op de primaire site voor [Azure,](azure-to-azure-tutorial-enable-replication.md) [Hyper-V](site-recovery-hyper-v-site-to-azure.md)of [VMware VM's en fysieke servers](site-recovery-vmware-to-azure-classic.md).

1. Gebruik siteherstelreplicatie om de nieuwe SQL Server-instantie te repliceren naar de secundaire site. Omdat het een hoogbeveiligde spiegelkopie is, wordt het gesynchroniseerd met het primaire cluster, maar gerepliceerd met behulp van siteherstelreplicatie.

   ![Afbeelding van een standaardcluster dat de relatie en stroom tussen een primaire site, Siteherstel en Azure weergeeft](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Failback overwegingen

Voor SQL Server Standard-clusters vereist failback na een ongeplande failover een SQL Server-back-up en herstel. Deze bewerking wordt uitgevoerd van de spiegelinstantie tot het oorspronkelijke cluster met herstel van de spiegel.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hoe krijgt SQL Server een licentie bij gebruik met Site Recovery?

Site Recovery-replicatie voor SQL Server valt onder het voordeel voor noodherstel van Software Assurance. Deze dekking is van toepassing op alle scenario's voor siteherstel: on-premises azure disaster recovery en cross-region Azure IaaS disaster recovery. Zie de prijzen voor [Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) voor meer informatie.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Ondersteunt Site Recovery mijn SQL Server-versie?

Site recovery is applicatie agnostisch. Siteherstel kan helpen bij het beschermen van elke versie van SQL Server die is geïmplementeerd op een ondersteund besturingssysteem. Zie voor meer informatie de [ondersteuningsmatrix voor herstel](vmware-physical-azure-support-matrix.md#replicated-machines) van gerepliceerde machines.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [siteherstelarchitectuur](site-recovery-components.md).
* Voor SQL Server in Azure vindt u meer informatie over oplossingen voor [hoge beschikbaarheid](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) voor herstel in een secundair Azure-gebied.
* Voor SQL Database vindt u meer informatie over de opties voor [bedrijfscontinuïteit](../sql-database/sql-database-business-continuity.md) en [hoge beschikbaarheid](../sql-database/sql-database-high-availability.md) voor herstel in een secundairAzure-gebied.
* Voor SQL Server-machines op on-premises, vindt u meer informatie over de opties voor [hoge beschikbaarheid](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) voor herstel in Azure Virtual Machines.
