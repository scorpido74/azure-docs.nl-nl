---
title: SQL Server FCI - Azure Virtual Machines | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u een SQL Server-failoverclusterinstantie maakt op virtuele Azure-machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249800"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Een SQL Server-failoverclusterinstantie configureren op virtuele Azure-machines

In dit artikel wordt uitgelegd hoe u een SQL Server failoverclusterinstantie (FCI) maakt op virtuele Azure-machines in het Azure Resource Manager-model. Deze oplossing gebruikt [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) als een softwaregebaseerdvirtueel SAN dat de opslag (gegevensschijven) synchroniseert tussen de knooppunten (Azure VM's) in een Windows-cluster. Storage Spaces Direct was nieuw in Windows Server 2016.

In het volgende diagram ziet u de volledige oplossing op virtuele Azure-machines:

![De complete oplossing](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Dit diagram toont:

- Twee Azure virtuele machines in een Windows Server Failover cluster. Wanneer een virtuele machine zich in een failovercluster bevindt, wordt deze ook een *clusterknooppunt* of *-knooppunt*genoemd.
- Elke virtuele machine heeft twee of meer gegevensschijven.
- Storage Spaces Direct synchroniseert de gegevens op de gegevensschijven en presenteert de gesynchroniseerde opslag als een opslaggroep.
- De opslaggroep presenteert een CLUSTER Gedeeld Volume (CSV) aan het failovercluster.
- De SQL Server FCI-clusterrol gebruikt de CSV voor de gegevensstations.
- Een Azure load balancer om het IP-adres voor sql server FCI vast te houden.
- Een Azure-beschikbaarheidsset bevat alle resources.

>[!NOTE]
>Alle Azure-resources in het diagram bevinden zich in dezelfde brongroep.

Zie [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)voor meer informatie over Storage Spaces Direct.

Storage Spaces Direct ondersteunt twee soorten architecturen: geconvergeerd en hypergeconvergeerd. De architectuur in dit document is hypergeconvergeerd. Een hypergeconvergeerde infrastructuur plaatst de opslag op dezelfde servers die de geclusterde toepassing hosten. In deze architectuur bevindt de opslag zich op elk SQL Server FCI-knooppunt.

## <a name="licensing-and-pricing"></a>Licenties en prijzen

Op Virtuele Azure-machines u SQL Server in licentie geven met pay-as-you-go (PAYG) of BYOL-vm-afbeeldingen (bring-your-own-license). Het type afbeelding dat u kiest, is van invloed op de manier waarop u wordt in rekening gebracht.

Met pay-as-you-go-licenties brengt een failoverclusterinstantie (FCI) van SQL Server op Azure virtuele machines kosten met zich mee voor alle knooppunten van de FCI, inclusief de passieve knooppunten. Zie [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)voor meer informatie.

Als u een Enterprise Agreement with Software Assurance hebt, u één gratis passief FCI-knooppunt gebruiken voor elk actief knooppunt. Als u wilt profiteren van dit voordeel in Azure, gebruikt u BYOL VM-afbeeldingen en gebruikt u dezelfde licentie op zowel de actieve als passieve knooppunten van de FCI. Zie [Ondernemingsovereenkomst](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)voor meer informatie .

Zie [Aan de slag met SQL VM's](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)voor het vergelijken van pay-as-you-go- en BYOL-licenties voor SQL Server op virtuele Azure-machines.

Zie [Prijzen](https://www.microsoft.com/sql-server/sql-server-2017-pricing)voor volledige informatie over het verlenen van licenties voor SQL Server.

### <a name="example-azure-template"></a>Voorbeeld azure-sjabloon

U deze hele oplossing in Azure maken op basis van een sjabloon. Een voorbeeld van een sjabloon is beschikbaar in de GitHub [Azure Quickstart-sjablonen.](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) Dit voorbeeld is niet ontworpen of getest voor een specifieke werkbelasting. U de sjabloon uitvoeren om een SQL Server FCI te maken met Storage Spaces Direct-opslag die is verbonden met uw domein. U de sjabloon evalueren en wijzigen voor uw doeleinden.

## <a name="before-you-begin"></a>Voordat u begint

Er zijn een paar dingen die je moet weten en hebben in plaats voordat je begint.

### <a name="what-to-know"></a>Wat te weten
U moet een operationeel inzicht hebben in deze technologieën:

- [Windows-clustertechnologieën](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failoverclusterexemplaren](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Een ding om bewust te zijn van is dat op een Azure IaaS VM guest failover cluster, raden we een enkele NIC per server (cluster knooppunt) en een enkel subnet. Azure-netwerken hebben fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn op een Azure IaaS VM-gastcluster. In het clustervalidatierapport wordt u gewaarschuwd dat de knooppunten slechts op één netwerk bereikbaar zijn. U deze waarschuwing negeren op Azure IaaS VM-failoverclusters voor gasten.

U moet ook een algemeen begrip van deze technologieën:

- [Hypergeconvergeerde oplossingen die Storage Spaces Direct gebruiken in Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure-brongroepen](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Op dit moment worden SQL Server failoverclusterexemplaren op virtuele Azure-machines alleen ondersteund met de [lichtgewicht beheermodus](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) van de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md) Als u wilt overstappen van de volledige extensiemodus naar lichtgewicht, verwijdert u de **SQL Virtual Machine-bron** voor de bijbehorende VM's en registreert u deze vervolgens bij de SQL VM-resourceprovider in de lichtgewicht modus. Wanneer u de **SQL Virtual Machine-bron** met de Azure-portal verwijderd, **schakelt u het selectievakje uit naast de juiste virtuele machine**. De volledige extensie ondersteunt functies zoals geautomatiseerde back-up, patching en geavanceerd portalbeheer. Deze functies werken niet voor SQL VM's nadat de agent opnieuw is geïnstalleerd in de lichtgewicht beheermodus.

### <a name="what-to-have"></a>Wat te hebben

Voordat u de stappen in dit artikel voltooit, moet u al het volgende hebben:

- Een Microsoft Azure-abonnement.
- Een Windows-domein op virtuele Azure-machines.
- Een account met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- Een virtueel Azure-netwerk en subnet met voldoende IP-adresruimte voor deze componenten:
   - Beide virtuele machines.
   - Het IP-adres van failovercluster.
   - Een IP-adres voor elke FCI.
- DNS geconfigureerd op het Azure-netwerk en wijst naar de domeincontrollers.

Met deze vereisten u beginnen met het bouwen van uw failovercluster. De eerste stap is het creëren van de virtuele machines.

## <a name="step-1-create-the-virtual-machines"></a>Stap 1: De virtuele machines maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw abonnement.

1. [Maak een Azure-beschikbaarheidsset](../tutorial-availability-sets.md).

   De beschikbaarheidsset groepeert virtuele machines tussen foutdomeinen en updatedomeinen. Het zorgt ervoor dat uw toepassing niet wordt beïnvloed door enkele storingspunten, zoals de netwerkswitch of de stroomeenheid van een rack van servers.

   Als u de resourcegroep voor uw virtuele machines niet hebt gemaakt, doet u dit wanneer u een Azure-beschikbaarheidsset maakt. Als u de Azure-portal gebruikt om de beschikbaarheidsset te maken, gaat u de volgende stappen uitvoeren:

   1. Selecteer in de Azure-portal **een resource maken** om de Azure Marketplace te openen. Zoeken naar **beschikbaarheidingesteld**.
   1. Selecteer **Beschikbaarheidsset**.
   1. Selecteer **Maken**.
   1. Geef onder **Beschikbaarheidsset maken**de volgende waarden op:
      - **Naam:** een naam voor de beschikbaarheidsset.
      - **Abonnement:** uw Azure-abonnement.
      - **Resourcegroep:** Als u een bestaande groep wilt gebruiken, klikt u op **Bestaand selecteren** en selecteert u de groep in de lijst. Selecteer anders **Nieuw maken** en voer een naam in voor de groep.
      - **Locatie:** stel de locatie in waar u van plan bent uw virtuele machines te maken.
      - **Foutdomeinen**: Gebruik de standaardwaarde (**3**).
      - **Domeinen bijwerken**: Gebruik de standaardinstelling (**5**).
   1. Selecteer **Maken** om de beschikbaarheidsset te maken.

1. Maak de virtuele machines in de beschikbaarheidsset.

   Inrichten van twee virtuele SQL Server-machines in de Azure-beschikbaarheidsset. Zie Een [virtuele SQL Server-machine inrichten in de Azure-portal](virtual-machines-windows-portal-sql-server-provision.md)voor instructies.

   Plaats beide virtuele machines:

   - In dezelfde Azure-brongroep als uw beschikbaarheidsset.
   - Op hetzelfde netwerk als uw domeincontroller.
   - Op een subnet dat voldoende IP-adresruimte heeft voor zowel virtuele machines als alle FCI's die u uiteindelijk op het cluster zou kunnen gebruiken.
   - In de azure-beschikbaarheidsset.

      >[!IMPORTANT]
      >U de beschikbaarheidsset niet instellen of wijzigen nadat u een virtuele machine hebt gemaakt.

   Kies een afbeelding uit Azure Marketplace. U een Azure Marketplace-afbeelding gebruiken die Windows Server en SQL Server bevat, of er een gebruiken die alleen Windows Server bevat. Zie Overzicht [van SQL Server op virtuele Azure-machines](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie.

   De officiële SQL Server-afbeeldingen in de Azure Gallery bevatten een geïnstalleerd SQL Server-exemplaar, de SQL Server-installatiesoftware en de vereiste sleutel.

   Kies de juiste afbeelding op basis van hoe u wilt betalen voor de SQL Server-licentie:

   - **Licenties voor betalen per gebruik**. De kosten per seconde van deze afbeeldingen omvatten de SQL Server-licenties:
      - **SQL Server 2016 Enterprise op Windows Server 2016-datacenter**
      - **SQL Server 2016-standaard op Windows Server 2016-datacenter**
      - **SQL Server 2016-ontwikkelaar op Windows Server 2016-datacenter**

   - **Breng-je-eigen-licentie (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise op Windows Server 2016-datacenter**
      - **(BYOL) SQL Server 2016-standaard op Windows Server 2016-datacenter**

   >[!IMPORTANT]
   >Nadat u de virtuele machine hebt gemaakt, verwijdert u de vooraf geïnstalleerde zelfstandige SQL Server-instantie. U gebruikt de vooraf geïnstalleerde SQL Server-media om de SQL Server FCI te maken nadat u het failovercluster en Storage Spaces Direct hebt ingesteld.

   U ook Azure Marketplace-afbeeldingen gebruiken die alleen het besturingssysteem bevatten. Kies een **Windows Server 2016-datacenterafbeelding** en installeer de SQL Server FCI nadat u het failovercluster en Storage Spaces Direct hebt ingesteld. Deze afbeelding bevat geen SQL Server-installatiemedia. Plaats de SQL Server-installatiemedia op een locatie waar u het voor elke server uitvoeren.

1. Nadat Azure uw virtuele machines hebt gemaakt, maakt u verbinding met elk van deze machines met RDP.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met rdp, wordt u gevraagd of u wilt toestaan dat de pc in het netwerk kan worden ontdekt. Selecteer **Ja**.

1. Als u een van de op SQL Server gebaseerde virtuele machineafbeeldingen gebruikt, verwijdert u de SQL Server-instantie.

   1. Klik in **Programma's en onderdelen**met de rechtermuisknop op **Microsoft SQL Server 2016 (64-bits)** en selecteer **Verwijderen/wijzigen**.
   1. Selecteer **Verwijderen**.
   1. Selecteer de standaardinstantie.
   1. Verwijder alle functies onder **Database Engine Services**. Verwijder gedeelde **functies**niet . Je ziet iets als de volgende screenshot:

      ![Functies selecteren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecteer **Volgende**en selecteer **Verwijderen**.

1. <a name="ports"></a>Open de firewallpoorten.

   Open deze poorten op elke virtuele machine op de Windows Firewall:

   | Doel | TCP-poort | Opmerkingen
   | ------ | ------ | ------
   | SQL Server | 1433 | Normale poort voor standaardexemplaren van SQL Server. Als u een afbeelding uit de galerie hebt gebruikt, wordt deze poort automatisch geopend.
   | Statustest | 59999 | Elke open TCP-poort. Configureer in een latere stap de [statussprobe](#probe) van de load balancer en het cluster om deze poort te gebruiken.  

1. Opslag toevoegen aan de virtuele machine. Zie [Opslag toevoegen voor](../disks-types.md)gedetailleerde informatie.

   Beide virtuele machines hebben ten minste twee gegevensschijven nodig.

   Voeg ruwe schijven toe, niet op NTFS-geformatteerde schijven.
      >[!NOTE]
      >Als u schijven met NTFS-formaat koppelt, u Opslagruimten Direct alleen inschakelen zonder dat de schijf in aanmerking komt.  

   Voeg minimaal twee premium SSD's toe aan elke VM. Wij raden ten minste P30 (1-TB) schijven.

   Hostcaching instellen op **alleen-lezen**.

   De opslagcapaciteit die u in productieomgevingen gebruikt, is afhankelijk van uw werkbelasting. De waarden die in dit artikel worden beschreven zijn voor demonstratie en testen.

1. [Voeg de virtuele machines toe aan uw reeds bestaande domein.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

Nadat u de virtuele machines hebt gemaakt en geconfigureerd, u het failovercluster instellen.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Stap 2: Het failovercluster van Windows Server configureren met Direct opslagruimten

De volgende stap is het configureren van het failovercluster met Storage Spaces Direct. In deze stap voltooit u de volgende stappen:

1. Voeg de functie Failoverclustering van Windows Server toe.
1. Valideer het cluster.
1. Maak het failovercluster.
1. Maak de wolkengetuige.
1. Opslag toevoegen.

### <a name="add-windows-server-failover-clustering"></a>Windows Server Failoverclustering toevoegen

1. Maak verbinding met de eerste virtuele machine met RDP met behulp van een domeinaccount dat lid is van de lokale beheerders en dat toestemming heeft om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Failoverclustering toevoegen aan elke virtuele machine](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Als u Failoverclustering vanuit de gebruikersinterface wilt installeren, neemt u de volgende stappen op beide virtuele machines:
   1. Selecteer in **Serverbeheer** **Beheren**en selecteer **Vervolgens Rollen en onderdelen toevoegen**.
   1. Selecteer **Volgende** in de **wizard Rollen en onderdelen toevoegen**totdat u Functies **selecteert.**
   1. Selecteer **failoverclustering**in **Onderdelen**selecteren . Neem alle vereiste functies en de beheertools op. Selecteer **Onderdelen toevoegen**.
   1. Selecteer **Volgende**en selecteer **Voltooien** om de functies te installeren.

   Als u Failoverclustering wilt installeren met PowerShell, voert u het volgende script uit vanuit een PowerShell-sessie van de beheerder op een van de virtuele machines:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Zie de instructies onder Stap 3 van [hypergeconvergeerde oplossing met Storage Spaces Direct in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)voor meer informatie over de volgende stappen.

### <a name="validate-the-cluster"></a>Het cluster valideren

Valideer het cluster in de gebruikersinterface of met PowerShell.

Als u het cluster wilt valideren met de gebruikersinterface, neemt u de volgende stappen op een van de virtuele machines:

1. Selecteer onder **Serverbeheer**De optie **Extra**en selecteer **Vervolgens Failoverclusterbeheer**.
1. Selecteer **onder Failoverclusterbeheer** **actie**en selecteer Vervolgens **Configuratie valideren**.
1. Selecteer **Volgende**.
1. Voer **onder Servers of een cluster selecteren**de namen van beide virtuele machines in.
1. Selecteer **Onder Testopties**de optie **Alleen tests uitvoeren die ik selecteer**. Selecteer **Volgende**.
1. Selecteer **onder Testselectie**alle tests behalve **Opslag,** zoals hier wordt weergegeven:

   ![Clustervalidatietests selecteren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Selecteer **Volgende**.
1. Selecteer **Volgende**onder **Bevestiging**.

Met de wizard Een configuratie valideren worden de validatietests uitgevoerd.

Als u het cluster wilt valideren met PowerShell, voert u het volgende script uit vanuit een PowerShell-sessie van een beheerder op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.

### <a name="create-the-failover-cluster"></a>Het failovercluster maken

Als u het failovercluster wilt maken, moet u het sein
- De namen van de virtuele machines die de clusterknooppunten worden.
- Een naam voor het failovercluster
- Een IP-adres voor het failovercluster. U een IP-adres gebruiken dat niet wordt gebruikt op hetzelfde virtuele Azure-netwerk en subnet als de clusterknooppunten.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 tot en met Windows Server 2016

Met het volgende PowerShell-script wordt een failovercluster voor Windows Server 2008 gemaakt via Windows Server 2016. Werk het script bij met de namen van de knooppunten (de namen van de virtuele machine) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Met het volgende PowerShell-script wordt een failovercluster voor Windows Server 2019 gemaakt. Zie [Failovercluster: Clusternetwerkobject voor](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)meer informatie. Werk het script bij met de namen van de knooppunten (de namen van de virtuele machine) en een beschikbaar IP-adres uit het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Een cloudgetuige maken

Cloud Witness is een nieuw type clusterquorumgetuige dat is opgeslagen in een Azure-opslagblob. Hierdoor is er geen aparte VM nodig die een getuigenaandeel host.

1. [Maak een cloudgetuige voor het failovercluster.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Maak een blobcontainer.

1. Sla de toegangssleutels en de URL van de container op.

1. Configureer de failoverclusterquorumgetuige. Zie [De quorumgetuige configureren in de gebruikersinterface](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Opslag toevoegen

De schijven voor Storage Spaces Direct moeten leeg zijn. Ze kunnen geen partities of andere gegevens bevatten. Volg de stappen [in deze handleiding](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)om de schijven schoon te maken.

1. [Winkelruimten direct inschakelen](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Met het volgende PowerShell-script storage spaces direct:  

   ```powershell
   Enable-ClusterS2D
   ```

   In **Failoverclusterbeheer**u nu de opslaggroep bekijken.

1. [Een volume maken](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Storage Spaces Direct maakt automatisch een opslaggroep wanneer u deze inschakelt. U bent nu klaar om een volume te maken. De PowerShell-cmdlet `New-Volume` automatiseert het volumecreatieproces. Dit proces omvat opmaak, het toevoegen van het volume aan het cluster en het maken van een clustergedeeld volume (CSV). In dit voorbeeld wordt een CSV (800 gigabyte) (GB) gemaakt:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Nadat deze opdracht is voltooid, wordt een volume van 800 GB als clusterbron gemonteerd. Het volume `C:\ClusterStorage\Volume1\`is op .

   In deze schermafbeelding wordt een gedeeld clustervolume met Direct opgeslagen ruimten weergegeven:

   ![Gedeeld clustervolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Stap 3: Failoverclusterfailover testen

Controleer in **Failoverclusterbeheer**of u de opslagbron naar het andere clusterknooppunt verplaatsen. Als u verbinding maken met het failovercluster met **Failoverclusterbeheer** en de opslag van het ene knooppunt naar het andere verplaatsen, u de FCI configureren.

## <a name="step-4-create-the-sql-server-fci"></a>Stap 4: De SQL Server FCI maken

Nadat u het failovercluster en alle clusteronderdelen hebt geconfigureerd, inclusief opslag, u de SQL Server FCI maken.

1. Maak verbinding met de eerste virtuele machine via RDP.

1. Controleer in **Failoverclusterbeheer**of alle kernclusterbronnen zich op de eerste virtuele machine bevinden. Verplaats indien nodig alle resources naar die virtuele machine.

1. Zoek de installatiemedia. Als de virtuele machine een van de Azure Marketplace-afbeeldingen gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full`. Selecteer **Setup**.

1. Selecteer **Installatie**in **SQL Server-installatiecentrum**.

1. Selecteer **Nieuwe SQL Server-failoverclusterinstallatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevensmappen moeten worden opgeslagen op geclusterde opslag. Met Storage Spaces Direct is het geen gedeelde schijf, maar een mount-punt naar een volume op elke server. Storage Spaces Direct synchroniseert het volume tussen beide knooppunten. Het volume wordt aan het cluster gepresenteerd als gedeeld clustervolume. Gebruik het CSV-bevestigingspunt voor de gegevensmappen.

   ![Datamappen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Nadat u de instructies in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knooppunt.

1. Nadat Setup de FCI op het eerste knooppunt hebt geïnstalleerd, maakt u verbinding met het tweede knooppunt met RDP.

1. Open het **SQL Server Installation Center**. Selecteer **Installatie**.

1. Selecteer **Knooppunt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en voeg de server toe aan de FCI.

   >[!NOTE]
   >Als u een Azure Marketplace-galerieafbeelding hebt gebruikt die SQL Server bevat, zijn SQL Server-hulpprogramma's bij de afbeelding opgenomen. Als u een van deze afbeeldingen niet hebt gebruikt, installeert u de SQL Server-hulpprogramma's afzonderlijk. Zie [SQL Server Management Studio (SSMS) downloaden.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="step-5-create-the-azure-load-balancer"></a>Stap 5: De Azure load balancer maken

Op virtuele Azure-machines gebruiken clusters een load balancer om een IP-adres vast te houden dat op één clusterknooppunt tegelijk moet staan. In deze oplossing houdt de load balancer het IP-adres voor de SQL Server FCI.

Zie [Een Azure load balancer maken en configureren](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)voor meer informatie.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>De load balancer maken in de Azure-portal

Ga als u de load balancer maken:

1. Ga in de Azure-portal naar de resourcegroep die de virtuele machines bevat.

1. Selecteer **Toevoegen**. Zoek in de Azure Marketplace voor **Load Balancer**. Selecteer **Load Balancer**.

1. Selecteer **Maken**.

1. Configureer de load balancer met:

   - **Abonnement:** uw Azure-abonnement.
   - **Resourcegroep:** de resourcegroep die uw virtuele machines bevat.
   - **Naam:** een naam die de load balancer identificeert.
   - **Regio**: De Azure-locatie die uw virtuele machines bevat.
   - **Type**: Openbaar of privé. Een private load balancer is toegankelijk vanuit het virtuele netwerk. De meeste Azure-toepassingen kunnen een private load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang tot SQL Server nodig heeft, gebruikt u een public load balancer.
   - **SKU**: Standaard.
   - **Virtueel netwerk**: Hetzelfde netwerk als de virtuele machines.
   - **IP-adrestoewijzing**: Statisch. 
   - **Privé-IP-adres:** het IP-adres dat u hebt toegewezen aan de SQL Server FCI-clusternetwerkbron.

 In de volgende schermafbeelding wordt de **gebruikersinterface van de load balancer** gemaakt:

   ![De load balancer instellen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>De backendpool load balancer configureren

1. Ga terug naar de Azure-brongroep die de virtuele machines bevat en zoek de nieuwe load balancer. Mogelijk moet u de weergave op de resourcegroep vernieuwen. Selecteer de load balancer.

1. Selecteer **Backend-groepen**en selecteer **Toevoegen**.

1. Koppel de backendpool aan de beschikbaarheidsset die de VM's bevat.

1. Selecteer **onder IP-configuraties van doelnetwerk**de optie **VIRTUELE MACHINE** en kies de virtuele machines die als clusterknooppunten deelnemen. Zorg ervoor dat u alle virtuele machines die de FCI zal hosten.

1. Selecteer **OK** om de backendpool te maken.

### <a name="configure-a-load-balancer-health-probe"></a>Een load balancer-statustest configureren

1. Selecteer op het lastenbalancerblad **De optie Sondes gezondheid**.

1. Selecteer **Toevoegen**.

1. Stel op het **sondeblad Gezondheid toevoegen** de parameters van de statussonde in. <a name="probe"> </a>

   - **Naam:** Een naam voor de statussonde.
   - **Protocol**: TCP.
   - **Poort:** in [deze stap](#ports)instellen op de poort die u in de firewall hebt gemaakt voor de statussonde . In dit artikel wordt in `59999`het voorbeeld de TCP-poort gebruikt.
   - **Interval:** 5 seconden.
   - **Ongezonde drempel :** 2 opeenvolgende fouten.

1. Selecteer **OK**.

### <a name="set-load-balancing-rules"></a>Regels voor taakverdeling instellen

1. Selecteer op het lastenbalancerblad **de taakverdelingsregels**.

1. Selecteer **Toevoegen**.

1. Stel de parameters voor de taakverdelingsregel in:

   - **Naam:** Een naam voor de regels voor het balanceren van de lasten.
   - **Frontend IP-adres:** het IP-adres voor de SQL Server FCI-clusternetwerkbron.
   - **Poort:** de SQL Server FCI TCP-poort. De standaardinstantiepoort is 1433.
   - **Backendpoort:** gebruikt dezelfde poort als de **poortwaarde** wanneer u **Zwevend IP (direct serverretour) inschakelt.**
   - **Backend pool**: de backend pool naam die u eerder hebt geconfigureerd.
   - **Statussonde:** de statussonde die u eerder hebt geconfigureerd.
   - **Sessie persistentie**: Geen.
   - **Idle time-out (minuten)**: 4.
   - **Zwevend IP (direct server retour)**: Ingeschakeld.

1. Selecteer **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Stap 6: Het cluster configureren voor de sonde

Stel de parameter clustersondepoort in PowerShell in.

Als u de parameter clustersondepoort wilt instellen, werkt u de variabelen in het volgende script bij met waarden uit uw omgeving. Verwijder de hoekhaakjes`<` `>`(en) uit het script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

In de volgende lijst worden de waarden beschreven die u moet bijwerken:

   - `<Cluster Network Name>`: De naam van het Failovercluster van Windows Server voor het netwerk. Klik in **Failoverclusterbeheernetwerken** > **Networks**met de rechtermuisknop op het netwerk en selecteer **Eigenschappen**. De juiste waarde staat onder **Naam** op het tabblad **Algemeen.**

   - `<SQL Server FCI IP Address Resource Name>`: De IP-adresnaam van SQL Server FCI. Klik in **Failoverclusterbeheerrollen** > **Roles**onder de ROL SQL Server FCI onder **Servernaam**met de rechtermuisknop op de IP-adresbron en selecteer **Eigenschappen**. De juiste waarde staat onder **Naam** op het tabblad **Algemeen.** 

   - `<ILBIP>`: Het IP-adres van ILB. Dit adres is geconfigureerd in de Azure-portal als het ILB-front-endadres. Dit is ook het SQL Server FCI IP-adres. U het vinden in **Failoverclusterbeheer** op dezelfde `<SQL Server FCI IP Address Resource Name>`eigenschappenpagina waar u de.  

   - `<nnnnn>`: De sondepoort die u hebt geconfigureerd in de statusssonde van de load balancer. Elke ongebruikte TCP-poort is geldig.

>[!IMPORTANT]
>Het subnetmasker voor de clusterparameter moet het `255.255.255.255`IP-adres van TCP zijn: .

Nadat u de clustersonde hebt ingesteld, u alle clusterparameters in PowerShell zien. Voer dit script uit:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Stap 7: Test FCI failover

Test failover van de FCI om clusterfunctionaliteit te valideren. Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server FCI-clusterknooppunten met RDP.

1. **Failoverclusterbeheer openen**. Selecteer **Rollen**. Merk op welk knooppunt eigenaar is van de SQL Server FCI-rol.

1. Klik met de rechtermuisknop op de SQL Server FCI-rol.

1. Selecteer **Verplaatsen**en selecteer vervolgens **Het best mogelijke knooppunt**.

**Failoverclusterbeheer** toont de rol en de bronnen gaan offline. De bronnen vervolgens bewegen en online komen op het andere knooppunt.

### <a name="test-connectivity"></a>Connectiviteit testen

Als u de connectiviteit wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de SQL Server FCI-naam.

>[!NOTE]
>Als dat nodig is, u [SQL Server Management Studio downloaden.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="limitations"></a>Beperkingen

Azure virtual machines support Microsoft Distributed Transaction Coordinator (MSDTC) on Windows Server 2019 with storage on Clustered Shared Volumes (CSV) and a [standard load balancer](../../../load-balancer/load-balancer-standard-overview.md).

Op virtuele Azure-machines wordt MSDTC niet ondersteund op Windows Server 2016 of eerder omdat:

- De geclusterde MSDTC-bron kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u op Windows Server 2016 een MSDTC-bron maakt, wordt geen gedeelde opslag weergegeven die beschikbaar is voor gebruik, zelfs niet als er opslag beschikbaar is. Dit probleem is opgelost in Windows Server 2019.
- De basisload balancer verwerkt geen RPC-poorten.

## <a name="see-also"></a>Zie ook

[Opslagruimten direct instellen met extern bureaublad (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-geconvergeerde oplossing met Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Overzicht van Opslagruimten Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server-ondersteuning voor Direct voor opslagruimten](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
