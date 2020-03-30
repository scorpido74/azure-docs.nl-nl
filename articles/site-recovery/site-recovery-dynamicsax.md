---
title: Herstel na noodgevallen van Dynamics AX met Azure-siteherstel
description: Meer informatie over het instellen van noodherstel voor Dynamics AX met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941593"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Noodherstel instellen voor een multitier Dynamics AX-toepassing   




 Dynamics AX is een van de meest populaire ERP-oplossingen die door ondernemingen worden gebruikt om processen op verschillende locaties te standaardiseren, resources te beheren en naleving te vereenvoudigen. Omdat de toepassing essentieel is voor een organisatie, moet de toepassing in geval van een ramp in minimale tijd operationeel zijn.

Vandaag biedt Dynamics AX geen out-of-the-box disaster recovery-mogelijkheden. Dynamics AX bestaat uit veel servercomponenten, zoals Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server en Reporting Services. Het beheren van de disaster recovery van elk van deze componenten handmatig is niet alleen duur, maar ook foutgevoelig.

In dit artikel wordt uitgelegd hoe u een oplossing voor noodherstel maken voor uw Dynamics AX-toepassing met Azure [Site Recovery.](site-recovery-overview.md) Het omvat ook geplande / ongeplande testfailovers met behulp van een herstelplan met één klik, ondersteunde configuraties en vereisten.



## <a name="prerequisites"></a>Vereisten

Het implementeren van disaster recovery voor Dynamics AX-toepassing met behulp van Site Recovery vereist de volgende vereisten:

• Stel een on-premises Dynamics AX-implementatie in.

• Maak een siteherstelkluis in een Azure-abonnement.

• Als Azure uw herstelsite is, voert u het hulpprogramma voor de beoordeling van gereedheid van Azure Virtual Machine uit op de VM's. Ze moeten compatibel zijn met de Azure Virtual Machines en Site Recovery-services.

## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor het maken van dit artikel gebruikten we virtuele VMware-machines met Dynamics AX 2012 R3 op Windows Server 2012 R2 Enterprise. Omdat replicatie van siteherstel toepassingsagnostisch is, verwachten we dat de aanbevelingen die hier worden gegeven, gelden voor de volgende scenario's.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**Vmware** | Ja | Ja
**Fysieke server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Noodherstel van de Dynamics AX-toepassing inschakelen met siteherstel
### <a name="protect-your-dynamics-ax-application"></a>Bescherm uw Dynamics AX-toepassing
Om de volledige toepassingsreplicatie en -herstel in te schakelen, moet elk onderdeel van Dynamics AX worden beveiligd.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Active Directory- en DNS-replicatie instellen

Active Directory is vereist op de rampherstelsite om de Dynamics AX-toepassing te laten functioneren. We raden de volgende twee keuzes aan op basis van de complexiteit van de on-premises omgeving van de klant.

**Optie 1**

De klant heeft een klein aantal toepassingen en één domeincontroller voor de gehele on-premises site en is van plan om samen over de hele site te falen. We raden u aan siteherstelreplicatie te gebruiken om de domeincontrollermachine te repliceren naar een secundaire site (van toepassing op zowel site-to-site- als site-to-Azure-scenario's).

**Optie 2**

De klant heeft een groot aantal toepassingen en draait een Active Directory-forest en is van plan om een paar toepassingen tegelijk te mislukken. We raden u aan een extra domeincontroller in te stellen op de rampherstelsite (een secundaire site of in Azure).

 Zie [Een domeincontroller beschikbaar maken op een locatie voor noodherstel](site-recovery-active-directory.md)voor meer informatie. Voor de rest van dit document gaan we ervan uit dat er een domeincontroller beschikbaar is op de site voor noodherstel.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server-replicatie instellen
Zie Toepassingen repliceren met SQL Server en Azure [Site Recovery](site-recovery-sql.md)voor technische richtlijnen voor de aanbevolen optie voor het beveiligen van de SQL-laag.

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Beveiliging inschakelen voor de Dynamics AX-client- en toepassingsobjectservervm's
Voer relevante siteherstelconfiguratie uit op basis van de vraag of de VM's worden geïmplementeerd op [Hyper-V](site-recovery-hyper-v-site-to-azure.md) of [VMware.](site-recovery-vmware-to-azure.md)

> [!TIP]
> We raden u aan de crashconsistente frequentie te configureren tot 15 minuten.
>

De volgende momentopname toont de beveiligingsstatus van Dynamics-component VM's in een VMware-beveiligingsscenario voor site-to-Azure.The following snapshot shows the protection status of Dynamics-component VM's in a VMware site-to-Azure protection scenario.

![Beveiligde objecten](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Netwerken configureren
**Vm-compute- en netwerkinstellingen configureren**

Configureer netwerkinstellingen in Site Recovery voor de Dynamics AX-client- en toepassingsobjectserver, zodat de VM-netwerken na een failover worden gekoppeld aan het juiste disaster recovery-netwerk. Controleer of het disaster recovery-netwerk voor deze lagen routetabel is voor de SQL-laag.

U de VM in de gerepliceerde items selecteren om de netwerkinstellingen te configureren, zoals in de volgende momentopname wordt weergegeven:

* Selecteer voor Application Object Server-servers de juiste beschikbaarheidsset.

* Als u een statisch IP-adres gebruikt, geeft u het IP op dat de VM moet gebruiken in het **doel-IP-tekstvak.**

    ![Netwerkinstellingen](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Maak een herstelplan

U een herstelplan maken in Site recovery om het failoverproces te automatiseren. Voeg een app-laag en een weblaag toe in het herstelplan. Bestel ze in verschillende groepen, zodat de front-end wordt afgesloten vóór de app-laag.

1. Selecteer de kluis Siteherstel in uw abonnement en selecteer de tegel **Herstelplannen.**

2. Selecteer **+ Herstelplan**en geef een naam op.

3. Selecteer de **bron** en **het doel**. Het doel kan Azure of een secundaire site zijn. Als u Azure kiest, moet u het implementatiemodel opgeven.

    ![Herstelplan maken](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecteer de toepassingsobjectserver en de client-VM's voor het herstelplan en selecteer de ✓.

    ![Objecten selecteren](./media/site-recovery-dynamics-ax/selectvms.png)

    Voorbeeld van herstelplan:

    ![Details herstelplan](./media/site-recovery-dynamics-ax/recoveryplan.png)

U het herstelplan voor de Dynamics AX-toepassing aanpassen door de volgende stappen toe te voegen. De vorige momentopname toont het volledige herstelplan nadat u alle stappen hebt toegevoegd.


* **SQL Server-failoverstappen:** zie [Replicatietoepassingen met SQL Server en Azure Site Recovery](site-recovery-sql.md)voor informatie over herstelstappen die specifiek zijn voor SQL-server.

* **Failovergroep 1:** Fail over de Vmen van de toepassingsobjectserver.
Zorg ervoor dat het geselecteerde herstelpunt zo dicht mogelijk bij de database PIT ligt, maar niet vooruit.

* **Script**: Load balancer toevoegen (alleen E-A).
Voeg een script toe (via Azure Automation) nadat de VM-groep Application Object Server wordt weergegeven om er een load balancer aan toe te voegen. U een script gebruiken om deze taak uit te voeren. Zie [Een load balancer toevoegen voor multitier application disaster recovery voor](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)meer informatie.

* **Failover groep 2:** Fail over de Dynamics AX client VM's. Fail over de web tier VM's als onderdeel van het herstelplan.


### <a name="perform-a-test-failover"></a>Een testfailover uitvoeren

Zie de begeleidende handleiding 'Active Directory disaster recovery solution' voor meer informatie over Active Directory tijdens de failovervan de test.

Zie [Toepassingen repliceren met SQL Server en Azure Site Recovery](site-recovery-sql.md)voor meer informatie over SQL-server tijdens de failovervan test.

1. Ga naar de Azure-portal en selecteer de kluis Siteherstel.

2. Selecteer het herstelplan dat is gemaakt voor Dynamics AX.

3. Selecteer **Failover testen**.

4. Selecteer het virtuele netwerk om het failoverproces te starten.

5. Nadat de secundaire omgeving is ingesteld, u uw validaties uitvoeren.

6. Nadat de validaties zijn voltooid, selecteert u **Validaties voltooid** en wordt de failoveromgeving van de test gereinigd.

Zie [Failovertesten naar Azure testen in Siterecovery](site-recovery-test-failover-to-azure.md)voor meer informatie over het uitvoeren van een testfailover.

### <a name="perform-a-failover"></a>Een failover uitvoeren

1. Ga naar de Azure-portal en selecteer de kluis Siteherstel.

2. Selecteer het herstelplan dat is gemaakt voor Dynamics AX.

3. Selecteer **Failover**en selecteer **Failover**.

4. Selecteer het doelnetwerk en selecteer **✓** om het failoverproces te starten.

Zie Failover in Site Recovery voor meer informatie over het doen van een [failover.](site-recovery-failover.md)

### <a name="perform-a-failback"></a>Failback uitvoeren

Zie [Toepassingen repliceren met SQL Server en Azure Site Recovery](site-recovery-sql.md)voor overwegingen die specifiek zijn voor SQL Server tijdens failback.

1. Ga naar de Azure-portal en selecteer de kluis Siteherstel.

2. Selecteer het herstelplan dat is gemaakt voor Dynamics AX.

3. Selecteer **Failover**en selecteer **Failover**.

4. Selecteer **Richting wijzigen**.

5. Selecteer de juiste opties: gegevenssynchronisatie en het maken van vm's.

6. Selecteer **✓** om het failbackproces te starten.


Zie [Failback VMware VM's van Azure naar on-premises](site-recovery-failback-azure-to-vmware.md)voor meer informatie over het doen van een failback.

## <a name="summary"></a>Samenvatting
Met Site Recovery u een volledig geautomatiseerd noodherstelplan maken voor uw Dynamics AX-toepassing. In het geval van een onderbreking u de failover binnen enkele seconden vanaf elke locatie starten en de toepassing binnen enkele minuten operationeel krijgen.

## <a name="next-steps"></a>Volgende stappen
Zie [Welke workloads kan ik beveiligen](site-recovery-workload.md)voor meer informatie over het beveiligen van bedrijfsworkloads met Site Recovery? .
