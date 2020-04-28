---
title: Uw Windows VM's migreren naar Azure Premium Storage met Azure Site Recovery
description: Migreer uw bestaande virtuele machines naar Azure Premium Storage met siteherstel. Premium Storage biedt krachtige schijfondersteuning met lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
author: luywang
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 071596557de00c9dfb2afaa5751d9331c21ada99
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866279"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migreren naar Premium-opslag met Azure Site Recovery

[Azure premium SSD's](disks-types.md) bieden krachtige schijfondersteuning met lage latentie voor virtuele machines (VM's) waarop I/O-intensieve workloads worden uitgevoerd. Met deze handleiding u uw VM-schijven migreren van een standaardopslagaccount naar een premium opslagaccount met behulp van [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site recovery is een Azure-service die bijdraagt aan uw strategie voor bedrijfscontinuïteit en disaster recovery door de replicatie van on-premises fysieke servers en VM's te orkestreren naar de cloud (Azure) of naar een secundair datacenter. Wanneer er storingen optreden op uw primaire locatie, slaagt u er niet in om toepassingen en workloads beschikbaar te houden. U keert niet terug naar uw primaire locatie wanneer deze weer normaal werkt. 

Site Recovery biedt testfailovers ter ondersteuning van noodhersteloefeningen zonder dat dit gevolgen heeft voor de productieomgevingen. U failovers uitvoeren met minimaal gegevensverlies (afhankelijk van de replicatiefrequentie) voor onverwachte rampen. In het scenario van migreren naar Premium Storage u de [failover in Site Recovery](../../site-recovery/site-recovery-failover.md) gebruiken om doelschijven te migreren naar een premium opslagaccount.

We raden u aan te migreren naar Premium Storage door siteherstel te gebruiken, omdat deze optie minimale downtime biedt. Deze optie voorkomt ook de handmatige uitvoering van het kopiëren van schijven en het maken van nieuwe VM's. Site Recovery zal systematisch kopiëren van uw schijven en maak nieuwe VM's tijdens failover. 

Site Recovery ondersteunt een aantal soorten failover met minimale of geen downtime. Zie de [typen failover in Site Recovery](../../site-recovery/site-recovery-failover.md)om uw downtime te plannen en gegevensverlies te schatten. Als u [zich voorbereidt op verbinding met Azure VM's na een failover,](../../site-recovery/vmware-walkthrough-overview.md)moet u verbinding kunnen maken met de Azure VM met behulp van RDP na failover.

![Diagram voor herstel na noodgevallen][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-onderdelen

Deze siteherstelcomponenten zijn relevant voor dit migratiescenario:

* **Configuratieserver** is een Azure VM die de communicatie coördineert en gegevensreplicatie- en herstelprocessen beheert. Op deze vm voert u één installatiebestand uit om de configuratieserver en een extra component, een zogenaamde processerver, als replicatiegateway te installeren. Lees meer over [vereisten voor configuratieserver](../../site-recovery/vmware-walkthrough-overview.md). U stelt de configuratieserver slechts één keer in en u deze gebruiken voor alle migraties naar dezelfde regio.

* **Processerver** is een replicatiegateway die: 

  1. Ontvangt replicatiegegevens van bron-VM's.
  2. Optimaliseert de gegevens met caching, compressie en versleuteling.
  3. Hiermee verzendt u de gegevens naar een opslagaccount. 

  Het behandelt ook push-installatie van de mobiliteitsservice om VM's te sourcen en voert automatische detectie van bron VM's uit. De standaardprocesserver is geïnstalleerd op de configuratieserver. U extra zelfstandige processervers implementeren om uw implementatie te schalen. Lees meer over [aanbevolen procedures voor het implementeren van processervers](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) en het implementeren van extra [processervers.](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers) U stelt de processerver slechts één keer in en u deze gebruiken voor alle migraties naar dezelfde regio.

* **Mobiliteitsservice** is een onderdeel dat wordt geïmplementeerd op elke standaard VM die u wilt repliceren. Het legt gegevensschrijft op de standaard VM en stuurt ze door naar de processerver. Lees meer over [de vereisten voor gerepliceerde machines.](../../site-recovery/vmware-walkthrough-overview.md)

In deze afbeelding ziet u hoe deze componenten op elkaar inwerken:

![Interactie van siteherstelcomponenten][15]

> [!NOTE]
> Siteherstel biedt geen ondersteuning voor de migratie van opslagruimtenschijven.

Zie [Scenarioarchitectuur](../../site-recovery/vmware-walkthrough-overview.md)voor extra componenten voor andere scenario's.

## <a name="azure-essentials"></a>Azure-benodigdheden

Dit zijn de Azure-vereisten voor dit migratiescenario:

* Een Azure-abonnement.
* Een Azure premium-opslagaccount om gerepliceerde gegevens op te slaan.
* Een virtueel Azure-netwerk waarmee VM's verbinding maken wanneer ze worden gemaakt bij failover. Het virtuele Azure-netwerk moet zich in dezelfde regio bevinden als het netwerk waarin siteherstel wordt uitgevoerd.
* Een Azure-standaardopslagaccount om replicatielogboeken op te slaan. Dit kan hetzelfde opslagaccount zijn voor de VM-schijven die worden gemigreerd.

## <a name="prerequisites"></a>Vereisten

* Inzicht in de relevante onderdelen van het migratiescenario in de vorige sectie.
* Plan uw downtime door meer te weten te komen over [failover in Site Recovery.](../../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Installatie- en migratiestappen

U Siteherstel gebruiken om Azure IaaS-VM's te migreren tussen regio's of binnen dezelfde regio. De volgende instructies zijn afgestemd op dit migratiescenario van het artikel [Vmv's of fysieke servers repliceren naar Azure.](../../site-recovery/vmware-walkthrough-overview.md) Volg de links voor gedetailleerde stappen in aanvulling op de instructies in dit artikel.

### <a name="step-1-create-a-recovery-services-vault"></a>Stap 1: Een kluis van Recovery Services maken

1. Open de [Azure Portal](https://portal.azure.com).
2. Selecteer Een > **bronbeheerback-up** > **en siteherstel (OMS)** **maken**. U ook **Browse** > **Recovery Services Vault** > **Add**selecteren.
   >[!NOTE]
   >Backup en Site Recovery maakten voorheen deel uit van de [OMS-suite.](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand)
1. Geef een gebied op waaraan VM's worden gerepliceerd. Selecteer voor migratie in dezelfde regio het gebied waar uw bron-VM's en bronopslagaccounts zich bevinden. 

### <a name="step-2-choose-your-protection-goals"></a>Stap 2: Kies uw beschermingsdoelen 

1. Open de [Azure-portal](https://portal.azure.com)op de VM waar u de configuratieserver wilt installeren.
2. Ga naar **Recovery Services vaults** > **Settings** > **Site Recovery** > **Stap 1: Voorbereiding Infrastructuur** > **bescherming doel**.

   ![Bladeren naar het doelvenster Beveiliging][2]

3. Selecteer **onder Doel beveiliging**in de eerste vervolgkeuzelijst De optie Om **Azure**. Selecteer in de tweede vervolgkeuzelijst **Niet gevirtualiseerd / Overig**en selecteer **vervolgens OK**.

   ![Doelvenster voor beveiliging met ingevulde vakken][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Stap 3: De bronomgeving instellen (configuratieserver)

1. Download **Azure Site Recovery Unified Setup** en de kluisregistratiesleutel door naar de **deelvensters** > Server voorbereiden**voorbereiden.** > **Add Server** 
 
   U hebt de kluisregistratiesleutel nodig om de uniforme installatie uit te voeren. De sleutel blijft vijf dagen na het genereren ervan geldig.

   ![Bladeren naar het deelvenster Server toevoegen][4]

2. Voeg in het deelvenster **Server toevoegen** een configuratieserver toe.

   ![Servervenster toevoegen met configuratieserver geselecteerd][5]

3. Voer op de VM die u als configuratieserver gebruikt, Unified Setup uit om de configuratieserver en de processerver te installeren. U [door de screenshots lopen](../../site-recovery/vmware-walkthrough-overview.md) om de installatie te voltooien. U verwijzen naar de volgende schermafbeeldingen voor stappen die zijn opgegeven voor dit migratiescenario.

   1. Selecteer **de configuratieserver en processerver installeren** **in Voordat u begint.**

      ![Voordat u begint,][6]

   2. Blader **in Registratie**en selecteer de registratiesleutel die u uit de kluis hebt gedownload.

      ![Registratiepagina][7]

   3. Selecteer bij **Details van de omgeving** of u virtuele VMware-machines wilt repliceren. Kies Voor dit migratiescenario **Nee**.

      ![Pagina Omgevingsdetails][8]

4. Ga als volgt te werk nadat de installatie is voltooid in het venster **Microsoft Azure Site Recovery Configuration Server:**
 
   1. Gebruik het tabblad **Accounts beheren** om het account te maken dat Site recovery kan gebruiken voor automatische detectie. (In het scenario over het beveiligen van fysieke machines is het instellen van het account niet relevant, maar hebt u ten minste één account nodig om een van de volgende stappen in te schakelen. In dit geval u het account en wachtwoord als een naam geven.) 
   2. Gebruik het tabblad **Vault Registration** om het kluisreferentiebestand te uploaden.

      ![Tabblad Vault Registratie][9]

### <a name="step-4-set-up-the-target-environment"></a>Stap 4: De doelomgeving instellen

Selecteer > **Infrastructuurdoel** **voorbereiden**en geef het implementatiemodel op dat u na een failover wilt gebruiken voor VM's. U **Klassiek** of **Resource Manager**kiezen, afhankelijk van uw scenario.

![Doelvenster][10]

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt. 

> [!NOTE]
> Als u een premium opslagaccount gebruikt voor gerepliceerde gegevens, moet u een extra standaardopslagaccount instellen om replicatielogboeken op te slaan.

### <a name="step-5-set-up-replication-settings"></a>Stap 5: Replicatie-instellingen instellen

Als u wilt controleren of uw configuratieserver is gekoppeld aan het replicatiebeleid dat u maakt, volgt u [Replicatieinstellingen instellen](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Stap 6: Capaciteit plannen

1. Gebruik de [capaciteitsplanner](../../site-recovery/site-recovery-capacity-planner.md) om de netwerkbandbreedte, -opslag en andere vereisten nauwkeurig in te schatten om aan uw replicatiebehoeften te voldoen. 
2. Als je klaar bent, selecteer **dan Ja, ik heb het gedaan** in Heb je de **capaciteitsplanning voltooid?**.

   ![Vak om te bevestigen dat u de capaciteitsplanning hebt voltooid][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Stap 7: Installeer de mobiliteitsservice en schakel replicatie in

1. U ervoor kiezen om [de installatie](../../site-recovery/vmware-walkthrough-overview.md) naar uw bron VM's te duwen of om [de mobiliteitsservice handmatig](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) te installeren op uw bron VM's. U vindt de eis van het duwen installatie en het pad van de handmatige installateur in de meegeleverde link. Als u een handmatige installatie doet, moet u mogelijk een intern IP-adres gebruiken om de configuratieserver te vinden.

   ![Pagina Details van configuratieserver][12]

   De mislukte VM heeft twee tijdelijke schijven: een van de primaire VM en de andere die zijn gemaakt tijdens de inrichting van de VM in het herstelgebied. Als u de tijdelijke schijf wilt uitsluiten voordat u de replicatie wilt repliceren, installeert u de mobiliteitsservice voordat u replicatie inschakelt. Zie [Schijven uitsluiten van replicatie](../../site-recovery/vmware-walkthrough-overview.md)voor meer informatie over het uitsluiten van de tijdelijke schijf.

2. Schakel als volgt replicatie in:
   1. Selecteer > **Toepassingsbron** **repliceren**. Nadat u replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+Repliceren** in de kluis om replicatie voor extra machines in te schakelen.
   2. Stel **bron** in stap 1 in als processerver.
   3. Geef in stap 2 het implementatiemodel na de failerfunctie op, een premium opslagaccount om naar te migreren, een standaardopslagaccount om logboeken op te slaan en een virtueel netwerk om niet te werken.
   4. Voeg in stap 3 beveiligde VM's toe op IP-adres. (Mogelijk hebt u een intern IP-adres nodig om ze te vinden.)
   5. Configureer in stap 4 de eigenschappen door de accounts te selecteren die u eerder op de processerver hebt ingesteld.
   6. Kies in stap 5 het replicatiebeleid dat u eerder hebt gemaakt in 'Stap 5: Replicatie-instellingen instellen'.
   7. Selecteer **OK**.

   > [!NOTE]
   > Wanneer een Azure VM is toegewezen en opnieuw wordt gestart, is er geen garantie dat deze hetzelfde IP-adres krijgt. Als het IP-adres van de configuratieserver/processerver of de beveiligde Azure VM's verandert, werkt de replicatie in dit scenario mogelijk niet correct.

   ![Replicatievenster inschakelen met Bron geselecteerd][13]

Wanneer u uw Azure Storage-omgeving ontwerpt, raden we u aan afzonderlijke opslagaccounts te gebruiken voor elke virtuele machine in een beschikbaarheidsset. We raden u aan de aanbevolen procedures in de opslaglaag te volgen om meerdere opslagaccounts te [gebruiken voor elke beschikbaarheidsset.](../linux/manage-availability.md) Het distribueren van VM-schijven naar meerdere opslagaccounts helpt de beschikbaarheid van opslag te verbeteren en distribueert de I/O over de Azure-opslaginfrastructuur.

Als uw VM's zich in een beschikbaarheidsset bevinden, raden we u ten zeerste aan meerdere vm's meerdere keren te migreren in plaats van schijven van alle VM's te repliceren naar één opslagaccount. Op die manier delen de VM's in dezelfde beschikbaarheidsset geen enkel opslagaccount. Gebruik het deelvenster **Replicatie inschakelen** om voor elke vm één voor één een een doelopslagaccount in te stellen.
 
U een post-failover implementatiemodel kiezen op basis van uw behoefte. Als u Azure Resource Manager kiest als uw post-failoverimplementatiemodel, u een VM (Resource Manager) niet overnemen van een VM (Resource Manager) of u een VM (klassiek) voor een VM (Resource Manager) niet gebruiken.

### <a name="step-8-run-a-test-failover"></a>Stap 8: Een testfailover uitvoeren

Als u wilt controleren of uw replicatie is voltooid, selecteert u de instantie Siteherstel en selecteert u **Vervolgens** > **Gerepliceerde items instellingen**. U ziet de status en het percentage van uw replicatieproces. 

Nadat de eerste replicatie is voltooid, voert u een testfailover uit om uw replicatiestrategie te valideren. Zie [Een testfailover uitvoeren in Site recovery](../../site-recovery/vmware-walkthrough-overview.md)voor gedetailleerde stappen van een testfailover. 

> [!NOTE]
> Voordat u een failover uitvoert, moet u ervoor zorgen dat uw VM's en replicatiestrategie aan de vereisten voldoen. Zie [Failovertesten naar Azure testen in Siteherstel](../../site-recovery/site-recovery-test-failover-to-azure.md)voor meer informatie over het uitvoeren van een testfailover.

U de status van uw testfailover zien in **Instellingen** > **taken** > *YOUR_FAILOVER_PLAN_NAME*. In het deelvenster ziet u een uitsplitsing van de stappen en resultaten van succes/mislukking. Als de testfailover bij elke stap mislukt, selecteert u de stap om het foutbericht te controleren. 

### <a name="step-9-run-a-failover"></a>Stap 9: Een failover uitvoeren

Nadat de testfailover is voltooid, voert u een failover uit om uw schijven te migreren naar Premium Storage en de VM-exemplaren te repliceren. Volg de gedetailleerde stappen in [Een failover uitvoeren](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Zorg ervoor dat u **VM's afsluiten selecteert en de nieuwste gegevens synchroniseert.** Met deze optie wordt opgegeven dat Site Recovery moet proberen de beveiligde VM's af te sluiten en de gegevens te synchroniseren, zodat de nieuwste versie van de gegevens niet meer wordt weergegeven. Als u deze optie niet selecteert of als de poging niet slaagt, is de failover van het laatst beschikbare herstelpunt voor de VM. 

Siteherstel maakt een VM-exemplaar waarvan het type hetzelfde is als of vergelijkbaar is met een VM met een Premium-opslag. U de prestaties en prijs van verschillende VM-exemplaren controleren door naar [Windows Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) of Linux Virtual Machines [Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)te gaan.

## <a name="post-migration-steps"></a>Stappen na de migratie

1. **Configureer gerepliceerde VM's naar de beschikbaarheidsset indien van toepassing**. Site recovery biedt geen ondersteuning voor het migreren van VM's, samen met de beschikbaarheidsset. Ga op een van de volgende handelingen naar een van de volgende handelingen, afhankelijk van de implementatie van uw gerepliceerde vm:
   * Voor een VM die is gemaakt via het klassieke implementatiemodel: Voeg de VM toe aan de beschikbaarheidsset in de Azure-portal. Ga voor gedetailleerde stappen naar [Een bestaande virtuele machine toevoegen aan een beschikbaarheidsset.](../linux/classic/configure-availability-classic.md)
   * Voor een VM die is gemaakt via het implementatiemodel ResourceManager: Sla uw configuratie van de vm op en verwijder en maak vervolgens de VM's in de beschikbaarheidsset opnieuw. Gebruik hiervoor het script bij [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Controleer voordat u dit script uitvoert de beperkingen en plant u uw downtime.

2. **Oude VM's en schijven verwijderen**. Zorg ervoor dat de Premium-schijven consistent zijn met bronschijven en dat de nieuwe VM's dezelfde functie vervullen als de bron-VM's. Verwijder de VM en verwijder de schijven uit uw bronopslagaccounts in de Azure-portal. Zie Fouten in het verwijderen van [opslagbronnen oplossen](storage-resource-deletion-errors.md)als er een probleem is waarbij de schijf niet wordt verwijderd, ook al hebt u de vm verwijderd.

3. **Maak de Azure Site Recovery-infrastructuur schoon**. Als siteherstel niet langer nodig is, u de infrastructuur schoonmaken. Verwijder gerepliceerde items, de configuratieserver en het herstelbeleid en verwijder vervolgens de kluis Azure Site Recovery.

## <a name="troubleshooting"></a>Problemen oplossen

* [Beveiliging voor virtuele machines en fysieke servers bewaken en oplossen](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor specifieke scenario's voor het migreren van virtuele machines:

* [Azure Virtual Machines migreren tussen opslagaccounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Een Windows Server-VHD maken en uploaden naar Azure](upload-generalized-managed.md)
* [Virtuele machines migreren van Amazon AWS naar Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zie ook de volgende bronnen voor meer informatie over Azure Storage en Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtuele machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
