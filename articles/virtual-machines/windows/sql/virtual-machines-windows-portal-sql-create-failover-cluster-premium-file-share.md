---
title: SQL Server FCI met Premium-bestands share-Azure Virtual Machines
description: In dit artikel wordt uitgelegd hoe u een SQL Server failover-cluster exemplaar maakt met behulp van een Premium-bestands share op virtuele machines van Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 09dd4ea3cd039bcb91acc877e51fee7e40168ac3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612755"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Een SQL Server-failovercluster configureren met Premium-bestands share op Azure virtual machines

In dit artikel wordt uitgelegd hoe u een SQL Server failovercluster (FCI) maakt op virtuele machines van Azure met behulp van een [Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Premium-bestands shares zijn met een SSD-back-up, consistente bestands shares met lage latentie die volledig worden ondersteund voor gebruik met exemplaren van failoverclusters voor SQL Server 2012 of hoger op Windows Server 2012 of hoger. Premium-bestands shares bieden meer flexibiliteit, waardoor u een bestands share zonder uitval tijd kunt verg Roten of verkleinen en schalen.


## <a name="before-you-begin"></a>Voordat u begint

Er zijn enkele dingen die u moet weten voordat u begint.

U moet een operationeel memorandum hebben van deze technologieën:

- [Windows-cluster technologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [Failover-cluster exemplaren SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Als u zich bewust bent van een Azure IaaS VM-failovercluster, raden we u aan één NIC per server (cluster knooppunt) en één subnet te maken. Azure Networking heeft fysieke redundantie waardoor extra Nic's en subnetten onnodig worden uitgevoerd op een Azure IaaS VM-gast cluster. In het cluster validatie rapport wordt u gewaarschuwd dat de knoop punten alleen bereikbaar zijn op één netwerk. U kunt deze waarschuwing voor Azure IaaS VM-failoverclusters negeren.

U moet ook algemene informatie over deze technologieën hebben:

- [Azure Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-resourcegroepen](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Op dit moment worden SQL Server failover-cluster exemplaren op virtuele machines van Azure alleen ondersteund in de [Lightweight-beheer modus](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) van de [SQL Server IaaS agent-extensie](virtual-machines-windows-sql-server-agent-extension.md). Als u wilt overschakelen van de volledige extensie modus naar Lightweight, verwijdert u de resource van de **virtuele SQL-machine** voor de bijbehorende vm's en registreert u deze vervolgens bij de resource provider van de SQL-vm in de Lightweight-modus. Als u de bron van de **virtuele SQL-machine** verwijdert met behulp van de Azure Portal, **schakelt u het selectie vakje naast de juiste virtuele machine uit**. De volledige extensie ondersteunt functies zoals automatische back-ups, patches en Geavanceerd Portal beheer. Deze functies werken niet voor SQL-Vm's nadat de agent opnieuw is geïnstalleerd in de modus voor licht gewicht beheer.

Premium-bestands shares bieden IOPS en doorvoer capaciteit die voldoet aan de behoeften van veel werk belastingen. Voor i/o-intensieve workloads kunt u [SQL Server failover-cluster instanties met opslagruimten direct](virtual-machines-windows-portal-sql-create-failover-cluster.md), op basis van beheerde Premium-schijven of Ultra disks.  

Controleer de IOPS-activiteit van uw omgeving en controleer of Premium-bestands shares de IOPS bevatten die u nodig hebt voordat u een implementatie of migratie start. Gebruik schijf tellers van de Windows-prestatie meter om het totale aantal IOPS (schijf overdrachten/seconde) en de door Voer (schijf bytes/seconde) te bewaken die vereist zijn voor SQL Server gegevens, logboeken en tijdelijke DB-bestanden.

Veel werk belastingen hebben een bursting-IO, dus het is een goed idee om tijdens zware gebruiks perioden te controleren en zowel de maximale IOPS als de gemiddelde IOPS te noteren. Premium-bestands shares bieden IOPS op basis van de grootte van de share. Premium-bestands shares bieden ook extra bursting waarmee u uw IO kunt indelen om de basislijn hoeveelheid tot een uur te laten opdrien.

Zie [prestatie lagen voor bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)voor meer informatie over de prestaties van Premium-bestands shares.

### <a name="licensing-and-pricing"></a>Licentie verlening en prijzen

Op virtuele machines van Azure kunt u een licentie SQL Server met behulp van betalen per gebruik (PAYG) of uw BYOL-VM-installatie kopieën (your-own-License). Welk type installatie kopie u kiest, is van invloed op de manier waarop u in rekening wordt gebracht.

Met betalen per gebruik-licenties is een FCI (failover cluster instance) van SQL Server op virtuele machines van Azure de kosten voor alle knoop punten van de FCI, met inbegrip van de passieve knoop punten. Zie [SQL Server Enterprise virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)voor meer informatie.

Als u Enterprise Agreement met Software Assurance hebt, kunt u één gratis passieve FCI-knoop punt voor elk actief knoop punt gebruiken. Als u gebruik wilt maken van dit voor deel in azure, gebruikt u BYOL VM-installatie kopieën en gebruikt u dezelfde licentie op de actieve en passieve knoop punten van de FCI. Zie [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)voor meer informatie.

Zie aan de [slag met SQL vm's](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)om betalen per gebruik en BYOL-licenties te vergelijken voor SQL Server op virtuele machines van Azure.

Zie [prijzen](https://www.microsoft.com/sql-server/sql-server-2017-pricing)voor volledige informatie over licentie SQL Server.

### <a name="filestream"></a>Bestandsstroom

FileStream wordt niet ondersteund voor een failovercluster met een Premium-bestands share. Als u FileStream wilt gebruiken, implementeert u het cluster met behulp van [opslagruimten direct](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel hebt voltooid, hebt u het volgende nodig:

- Een Microsoft Azure-abonnement.
- Een Windows-domein op virtuele machines van Azure.
- Een domein gebruikers account dat machtigingen heeft om objecten te maken op virtuele machines van Azure en in Active Directory.
- Een domein gebruikers account om de SQL Server-service uit te voeren en dat u bij de virtuele machine kunt aanmelden bij het koppelen van de bestands share.  
- Een virtueel Azure-netwerk en subnet met voldoende IP-adres ruimte voor deze onderdelen:
   - Twee virtuele machines.
   - Het IP-adres van het failovercluster.
   - Een IP-adres voor elke FCI.
- DNS geconfigureerd op het Azure-netwerk, die verwijst naar de domein controllers.
- Een [Premium-bestands share](../../../storage/files/storage-how-to-create-premium-fileshare.md) die moet worden gebruikt als het geclusterde station, op basis van de opslag quota van uw Data Base voor uw gegevens bestanden.
- Als u Windows Server 2012 R2 en ouder hebt, moet u een andere bestands share gebruiken als bestandssharewitness, omdat Cloud-witnesss worden ondersteund voor Windows 2016 en nieuwer. U kunt een andere Azure-bestands share gebruiken of u kunt een bestands share gebruiken op een afzonderlijke virtuele machine. Als u een andere Azure-bestands share wilt gebruiken, kunt u deze koppelen aan hetzelfde proces als voor de Premium-bestands share die wordt gebruikt voor uw geclusterde station. 

Als aan deze vereisten is voldaan, kunt u beginnen met het bouwen van uw failovercluster. De eerste stap is het maken van de virtuele machines.

## <a name="step-1-create-the-virtual-machines"></a>Stap 1: de virtuele machines maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw abonnement.

1. [Maak een Azure-beschikbaarheidsset](../tutorial-availability-sets.md).

   Met de beschikbaarheidsset worden virtuele machines gegroepeerd op fout domeinen en update domeinen. Het zorgt ervoor dat uw toepassing niet wordt beïnvloed door afzonderlijke storings punten, zoals de netwerk switch of de energie-eenheid van een rek van servers.

   Als u de resource groep voor uw virtuele machines nog niet hebt gemaakt, doet u dit wanneer u een Azure-beschikbaarheidsset maakt. Voer de volgende stappen uit als u de Azure Portal voor het maken van de beschikbaarheidsset wilt gebruiken:

   1. Selecteer in de Azure Portal **een resource maken** om Azure Marketplace te openen. Zoeken naar **beschikbaarheidsset**.
   1. Selecteer een **beschikbaarheidsset**.
   1. Selecteer **Maken**.
   1. Geef onder **beschikbaarheidsset maken**de volgende waarden op:
      - **Naam**: een naam voor de beschikbaarheidsset.
      - **Abonnement**: uw Azure-abonnement.
      - **Resource groep**: als u een bestaande groep wilt gebruiken, klikt u op **bestaande selecteren** en selecteert u vervolgens de groep in de lijst. Anders selecteert u **nieuwe maken** en voert u een naam in voor de groep.
      - **Locatie**: Stel de locatie in waar u uw virtuele machines wilt maken.
      - **Fout domeinen**: gebruik de standaard waarde (**3**).
      - **Update domeinen**: gebruik de standaard waarde (**5**).
   1. Selecteer **maken** om de beschikbaarheidsset te maken.

1. Maak de virtuele machines in de beschikbaarheidsset.

   Voorziet in twee SQL Server virtuele machines in de beschikbaarheidsset van Azure. Zie [een SQL Server virtuele machine inrichten in de Azure Portal](virtual-machines-windows-portal-sql-server-provision.md)voor instructies.

   Plaats beide virtuele machines:

   - In dezelfde Azure-resource groep als uw beschikbaarheidsset.
   - Op hetzelfde netwerk als uw domein controller.
   - Op een subnet met voldoende IP-adres ruimte voor zowel virtuele machines als alle Failoverclusterinstanties die u uiteindelijk kunt gebruiken op het cluster.
   - In de beschikbaarheidsset van Azure.

      >[!IMPORTANT]
      >U kunt de beschikbaarheidsset niet instellen of wijzigen nadat u een virtuele machine hebt gemaakt.

   Kies een installatie kopie van Azure Marketplace. U kunt een Azure Marketplace-installatie kopie gebruiken die Windows Server en SQL Server bevat, of u moet er een gebruiken die alleen Windows Server bevat. Zie [overzicht van SQL Server op virtuele machines van Azure](virtual-machines-windows-sql-server-iaas-overview.md)voor meer informatie.

   De officiële SQL Server installatie kopieën in de Azure-galerie bevatten een geïnstalleerd SQL Server exemplaar, de SQL Server installatie software en de vereiste sleutel.

   >[!IMPORTANT]
   > Nadat u de virtuele machine hebt gemaakt, verwijdert u het vooraf geïnstalleerde zelfstandige SQL Server exemplaar. U gebruikt het vooraf geïnstalleerde SQL Server medium om de SQL Server FCI te maken nadat u het failovercluster en Premium file share hebt ingesteld als opslag.

   U kunt ook installatie kopieën van Azure Marketplace gebruiken die alleen het besturings systeem bevatten. Kies een installatie kopie van **Windows Server 2016 Data Center** en installeer de SQL Server FCI na het instellen van het failovercluster en Premium file share als opslag. Deze installatie kopie bevat geen SQL Server-installatie media. Plaats de SQL Server-installatie media op een locatie waar u deze voor elke server kunt uitvoeren.

1. Nadat Azure uw virtuele machines heeft gemaakt, maakt u deze met behulp van RDP.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met behulp van RDP, wordt u gevraagd of u wilt toestaan dat de PC kan worden gedetecteerd in het netwerk. Selecteer **Ja**.

1. Als u een van de op SQL Server gebaseerde installatie kopieën voor virtuele machines gebruikt, verwijdert u het SQL Server exemplaar.

   1. Klik in **Program ma's en onderdelen**met de rechter muisknop op **Microsoft SQL Server 201_ (64-bits)** en selecteer **verwijderen/wijzigen**.
   1. Selecteer **Verwijderen**.
   1. Selecteer het standaard exemplaar.
   1. Verwijder alle functies onder **Data Base Engine-Services**. Verwijder **gedeelde onderdelen**niet. U ziet iets als de volgende scherm afbeelding:

        ![Functies selecteren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecteer **volgende**en selecteer vervolgens **verwijderen**.

1. <span id="ports"> </span> Open de firewall poorten.  

   Open op elke virtuele machine deze poorten op het Windows Firewall:

   | Doel | TCP-poort | Opmerkingen
   | ------ | ------ | ------
   | SQL Server | 1433 | Normale poort voor standaard exemplaren van SQL Server. Als u een installatie kopie uit de galerie hebt gebruikt, wordt deze poort automatisch geopend.
   | Statustest | 59999 | Een open TCP-poort. Configureer in een latere stap de load balancer [Health probe](#probe) en het cluster om deze poort te gebruiken.
   | Bestandsshare | 445 | Poort die wordt gebruikt door de bestands share service.

1. [Voeg de virtuele machines toe aan uw vooraf bestaande domein](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Nadat u de virtuele machines hebt gemaakt en geconfigureerd, kunt u de Premium-bestands share configureren.

## <a name="step-2-mount-the-premium-file-share"></a>Stap 2: de Premium-bestands share koppelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en ga naar uw opslag account.
1. Ga naar **Bestands shares** onder **Bestands service** en selecteer de Premium-bestands share die u wilt gebruiken voor uw SQL-opslag.
1. Selecteer **verbinding** om de Connection String voor uw bestands share weer te geven.
1. Selecteer de stationsletter die u wilt gebruiken in de vervolg keuzelijst en Kopieer beide code blokken naar Klad blok.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Beide Power shell-opdrachten kopiëren van de bestands share Connect Portal":::

1. Gebruik RDP om verbinding te maken met de SQL Server-VM met het account dat door uw SQL Server FCI wordt gebruikt voor het service account.
1. Open een Power shell-opdracht console voor beheer.
1. Voer de opdrachten uit die u eerder hebt opgeslagen tijdens het werken in de portal.
1. Ga naar de share met behulp van de bestanden Verkenner of het dialoog venster **uitvoeren** (Windows-logo toets + r). Gebruik het netwerkpad `\\storageaccountname.file.core.windows.net\filesharename`. Bijvoorbeeld: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Maak ten minste één map op de zojuist verbonden bestands share om uw SQL-gegevens bestanden in te brengen.
1. Herhaal deze stappen op elke SQL Server virtuele machine die u aan het cluster wilt deel nemen.

  > [!IMPORTANT]
  > - Overweeg het gebruik van een afzonderlijke bestands share voor back-upbestanden om de IOPS-en ruimte capaciteit van deze share op te slaan voor gegevens en logboek bestanden. U kunt een Premium-of standaard bestands share gebruiken voor back-upbestanden.
  > - Als u Windows 2012 R2 en ouder gebruikt, voert u dezelfde stappen uit om uw bestands share te koppelen die u als de bestandssharewitness gaat gebruiken. 

## <a name="step-3-configure-the-failover-cluster"></a>Stap 3: het failovercluster configureren

De volgende stap is het configureren van het failovercluster. In deze stap gaat u de volgende substaps volt ooien:

1. Voeg de functie Windows Server Failover Clustering toe.
1. Valideer het cluster.
1. Maak het failovercluster.
1. Maak de cloudwitness (voor Windows Server 2016 en hoger) of de bestands share-Witness (voor Windows Server 2012 R2 en ouder).


### <a name="add-windows-server-failover-clustering"></a>Windows Server failover clustering toevoegen

1. Maak verbinding met de eerste virtuele machine met RDP met behulp van een domein account dat lid is van de lokale beheerders en dat gemachtigd is om objecten te maken in Active Directory. Gebruik dit account voor de rest van de configuratie.

1. [Voeg Failover Clustering toe aan elke virtuele machine](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Als u Failover Clustering wilt installeren vanuit de gebruikers interface, voert u de volgende stappen uit op beide virtuele machines:
   1. Selecteer in **Serverbeheer** **beheren**en selecteer vervolgens **functies en onderdelen toevoegen**.
   1. Selecteer in de **wizard functies en onderdelen toevoegen**de optie **volgende** totdat u **onderdelen selecteert**.
   1. Selecteer in **functies selecteren**de optie **failover clustering**. Neem alle vereiste functies en de beheer hulpprogramma's op. Selecteer **onderdelen toevoegen**.
   1. Selecteer **volgende**en selecteer vervolgens **volt ooien** om de functies te installeren.

   Als u Failover Clustering wilt installeren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Het cluster valideren

Valideer het cluster in de gebruikers interface of met behulp van Power shell.

Als u het cluster wilt valideren met behulp van de gebruikers interface, voert u de volgende stappen uit op een van de virtuele machines:

1. Klik onder **Serverbeheer**op **extra**en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer onder **Failoverclusterbeheer** **actie**en selecteer vervolgens **configuratie valideren**.
1. Selecteer **Volgende**.
1. Voer onder **servers of een cluster selecteren**de namen van beide virtuele machines in.
1. Onder **test opties**selecteert u **alleen geselecteerde tests uitvoeren**. Selecteer **Volgende**.
1. Selecteer onder **selectie testen**alle tests, met uitzonde ring van **opslag** en **opslagruimten direct**, zoals hier wordt weer gegeven:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Cluster validatie tests selecteren":::

1. Selecteer **Volgende**.
1. Klik onder **bevestiging**op **volgende**.

Met de **wizard een configuratie valideren** worden de validatie tests uitgevoerd.

Als u het cluster wilt valideren met behulp van Power shell, voert u het volgende script uit vanuit een Power shell-beheer sessie op een van de virtuele machines:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Nadat u het cluster hebt gevalideerd, maakt u het failovercluster.

### <a name="create-the-failover-cluster"></a>Het failovercluster maken

Als u het failovercluster wilt maken, hebt u het volgende nodig:
- De namen van de virtuele machines die worden de cluster knooppunten.
- Een naam voor het failovercluster
- Een IP-adres voor het failovercluster. U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de cluster knooppunten.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 tot en met Windows Server 2016

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2012 via Windows Server 2016. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Met het volgende Power shell-script maakt u een failovercluster voor Windows Server 2019. Zie voor meer informatie [failover cluster: Cluster Network object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Een cloudwitness maken (Win 2016 +)

Als u Windows Server 2016 en hoger gebruikt, moet u een Cloudwitness maken. Cloudwitness is een nieuw type cluster quorum-Witness dat is opgeslagen in een Azure Storage-blob. Hiermee verwijdert u de nood zaak van een afzonderlijke virtuele machine die als host fungeert voor een Witness-share of het gebruik van een afzonderlijke bestands share.

1. [Maak een cloudwitness voor het failovercluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Maak een BLOB-container.

1. Sla de toegangs sleutels en de container-URL op.

### <a name="configure-quorum"></a>Quorum configureren 

Voor Windows Server 2016 en hoger configureert u het cluster voor gebruik van de cloudwitness die u zojuist hebt gemaakt. Voer alle stappen uit om [de quorumwitness in de gebruikers interface te configureren](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Voor Windows Server 2012 R2 en ouder voert u dezelfde stappen uit in de [Quorumwitness configureren in de gebruikers interface](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) , maar op de pagina **quorumwitness selecteren** selecteert u de optie **een bestands share-Witness configureren** . Geef de bestands share op die u hebt toegewezen als de bestandssharewitness, of het een andere virtuele machine is die u hebt geconfigureerd of die is gekoppeld vanuit Azure. 


## <a name="step-4-test-cluster-failover"></a>Stap 4: failover van het cluster testen

Test de failover van het cluster. Klik in **Failoverclusterbeheer**met de rechter muisknop op uw cluster en selecteer **meer acties** > **basis cluster resource** > verplaatsen**knoop punt selecteren**en selecteer vervolgens het andere knoop punt van het cluster. Verplaats de kern cluster bron naar elk knoop punt van het cluster en verplaats deze vervolgens terug naar het primaire knoop punt. Als u het cluster kunt verplaatsen naar elk knoop punt, bent u klaar om SQL Server te installeren.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Cluster-Failover testen door de kern bron te verplaatsen naar de andere knoop punten":::

## <a name="step-5-create-the-sql-server-fci"></a>Stap 5: de SQL Server FCI maken

Nadat u het failovercluster hebt geconfigureerd, kunt u de SQL Server FCI maken.

1. Maak verbinding met de eerste virtuele machine met behulp van RDP.

1. Controleer in **Failoverclusterbeheer**of alle kern cluster resources zich op de eerste virtuele machine bevinden. Als dat het geval is, verplaatst u alle resources naar deze virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt `C:\SQLServer_<version number>_Full`de media zich op. Selecteer **Setup**.

1. Selecteer in het **SQL Server-installatie centrum**de optie **installatie**.

1. Selecteer **nieuwe SQL Server failover-cluster installatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

   De FCI-gegevens directory's moeten zich op de Premium-bestands share bestaan. Geef het volledige pad van de share op in dit formulier: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Er wordt een waarschuwing weer gegeven met de mede deling dat u een bestands server hebt opgegeven als de data directory. Deze waarschuwing wordt verwacht. Zorg ervoor dat het gebruikers account dat u RDP gebruikt voor de virtuele machine bij het persistent maken van de bestands share hetzelfde account is als de SQL Server-service om mogelijke storingen te voor komen.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Bestands share gebruiken als SQL-gegevens mappen":::

1. Nadat u de stappen in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met behulp van RDP.

1. Open het **SQL Server-installatie centrum**. Selecteer **installatie**.

1. Selecteer **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en de server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt met SQL Server, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u een van deze installatie kopieën niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Stap 6: de Azure load balancer maken

Op virtuele machines van Azure gebruiken clusters een load balancer voor het opslaan van een IP-adres dat op één cluster knooppunt tegelijk moet zijn. In deze oplossing bevat de load balancer het IP-adres voor de SQL Server FCI.

Zie [een Azure-Load Balancer maken en configureren](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)voor meer informatie.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Maak de load balancer in het Azure Portal

De load balancer maken:

1. Ga in het Azure Portal naar de resource groep die de virtuele machines bevat.

1. Selecteer **Toevoegen**. Zoek in de Azure Marketplace naar **Load Balancer**. Selecteer **Load Balancer**.

1. Selecteer **Maken**.

1. Stel de load balancer in met behulp van de volgende waarden:

   - **Abonnement**: uw Azure-abonnement.
   - **Resource groep**: de resource groep die uw virtuele machines bevat.
   - **Naam**: een naam die de Load Balancer aanduidt.
   - **Regio**: de Azure-locatie waar uw virtuele machines zich bevinden.
   - **Type**: ofwel openbaar of privé. Er kan vanuit het virtuele netwerk toegang worden verkregen tot een persoonlijke load balancer. De meeste Azure-toepassingen kunnen een privé-load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang moet hebben tot SQL Server, gebruik dan een open bare load balancer.
   - **SKU**: Standard.
   - **Virtueel netwerk**: hetzelfde netwerk als de virtuele machines.
   - **IP-adres toewijzing**: statisch. 
   - **Persoonlijk IP-adres**: het IP-adres dat u hebt toegewezen aan de resource van het SQL Server FCI-cluster netwerk.

   De volgende afbeelding toont de gebruikers interface **Create Load Balancer** :

   ![De load balancer instellen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>De load balancer back-end-groep configureren

1. Ga terug naar de Azure-resource groep die de virtuele machines bevat en zoek de nieuwe load balancer. Mogelijk moet u de weer gave van de resource groep vernieuwen. Selecteer de load balancer.

1. Selecteer **back-endservers**en selecteer vervolgens **toevoegen**.

1. Koppel de back-end-pool met de beschikbaarheidsset die de virtuele machines bevat.

1. Onder **IP-configuraties**voor het doelnet **werk selecteert u virtuele machine** en kiest u de virtuele machines die als cluster knooppunten zullen worden beschouwd. Zorg ervoor dat u alle virtuele machines opneemt die als host moeten fungeren voor de FCI.

1. Selecteer **OK** om de back-end-pool te maken.

### <a name="configure-a-load-balancer-health-probe"></a>Een load balancer-statustest configureren

1. Selecteer op de Blade load balancer **status controles**.

1. Selecteer **Toevoegen**.

1. <span id="probe"> </span> Stel op de Blade **status test toevoegen** de volgende Health probe para meters in.

   - **Naam**: een naam voor de status test.
   - **Protocol**: TCP.
   - **Poort**: de poort die u in [deze stap](#ports)in de firewall hebt gemaakt voor de status test. In dit artikel gebruikt het voor beeld TCP- `59999`poort.
   - **Interval**: 5 seconden.
   - **Drempel waarde voor onjuiste status**: 2 opeenvolgende fouten.

1. Selecteer **OK**.

### <a name="set-load-balancing-rules"></a>Taakverdelings regels instellen

1. Selecteer op de Blade load balancer de optie **taakverdelings regels**.

1. Selecteer **Toevoegen**.

1. De para meters voor de taakverdelings regel instellen:

   - **Naam**: een naam voor de taakverdelings regels.
   - **Frontend-IP-adres**: het IP-adres voor de SQL Server cluster netwerk bron van het FCI.
   - **Poort**: de SQL Server FCI TCP-poort. De standaard instantie poort is 1433.
   - **Back-endserver**: maakt gebruik van dezelfde poort als de **poort** waarde wanneer u **zwevende IP (direct server return)** inschakelt.
   - **Back-end-pool**: de naam van de back-endserver die u eerder hebt geconfigureerd.
   - **Health probe**: de status test die u eerder hebt geconfigureerd.
   - **Sessie persistentie**: geen.
   - **Time-out voor inactiviteit (minuten)**: 4.
   - **Zwevend IP (direct server return)**: ingeschakeld.

1. Selecteer **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Stap 7: het cluster voor de test configureren

Stel de para meter cluster probe Port in Power shell in.

Als u de para meter voor de cluster test poort wilt instellen, werkt u de variabelen in het volgende script bij met waarden uit uw omgeving. Verwijder de punt haken (`<` en `>`) van het script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

In de volgende lijst worden de waarden beschreven die u moet bijwerken:

   - `<Cluster Network Name>`: De naam van het Windows Server-failovercluster voor het netwerk. Klik in **Failoverclusterbeheer** > **netwerken**met de rechter muisknop op het netwerk en selecteer **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** .

   - `<SQL Server FCI IP Address Resource Name>`: De naam van de FCI-IP-adres bron van het SQL Server. Klik in **Failoverclusterbeheer** > **rollen**onder de rol SQL Server FCI onder **Server naam**met de rechter muisknop op de IP-adres bron en selecteer **Eigenschappen**. U vindt de juiste waarde onder **naam** op het tabblad **Algemeen** .

   - `<ILBIP>`: Het IP-adres van ILB. Dit adres wordt geconfigureerd in de Azure Portal als het front-end-adres van de ILB. Dit is ook het SQL Server IP-adres van FCI. U kunt deze in **Failoverclusterbeheer** vinden op dezelfde eigenschappen pagina waar u de `<SQL Server FCI IP Address Resource Name>`hebt opgeslagen.  

   - `<nnnnn>`: De test poort die u hebt geconfigureerd in de load balancer Health probe. Alle ongebruikte TCP-poort is geldig.

>[!IMPORTANT]
>Het subnetmasker voor de cluster parameter moet het TCP IP-broadcast adres zijn `255.255.255.255`:.

Nadat u de cluster test hebt ingesteld, kunt u alle cluster parameters in Power shell zien. Voer dit script uit:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Stap 8: FCI failover testen

Test de failover van de FCI om de functionaliteit van het cluster te valideren. Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server FCI-cluster knooppunten met behulp van RDP.

1. Open **Failoverclusterbeheer**. Selecteer **rollen**. U ziet welk knoop punt eigenaar is van de SQL Server rol FCI.

1. Klik met de rechter muisknop op de SQL Server FCI rol.

1. Selecteer **verplaatsen**en selecteer vervolgens **best mogelijke knoop punt**.

**Failoverclusterbeheer** toont de rol en de bijbehorende resources gaan offline. De resources worden vervolgens verplaatst en weer online in het andere knoop punt.

### <a name="test-connectivity"></a>Connectiviteit testen

Als u de verbinding wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de naam van de SQL Server FCI.

>[!NOTE]
>Als dat het geval is, kunt u [SQL Server Management Studio downloaden](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Beperkingen

Azure virtual machines ondersteunen micro soft Distributed Transaction Coordinator (MSDTC) op Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en een [standaard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

Op virtuele machines van Azure wordt MSDTC niet ondersteund in Windows Server 2016 of eerder omdat:

- De geclusterde MSDTC-bron kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u in Windows Server 2016 een MSDTC-bron maakt, wordt er geen gedeelde opslag weer gegeven die beschikbaar is voor gebruik, zelfs als de opslag ruimte beschikbaar is. Dit probleem is opgelost in Windows Server 2019.
- Met de basis load balancer worden geen RPC-poorten afgehandeld.

## <a name="see-also"></a>Zie ook

- [Windows-cluster technologieën](/windows-server/failover-clustering/failover-clustering-overview)
- [Failover-cluster exemplaren SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
